# Continual Observation of Joins under Differential Privacy

差分隐私下的连接操作持续观测研究

WEI DONG, Carnegie Mellon University, USA

魏东，美国卡内基梅隆大学

ZIJUN CHEN and QIYAO LUO, Hong Kong University of Science and Technology, China

陈子君与罗淇耀，中国香港科技大学

ELAINE SHI, Carnegie Mellon University, USA

石 Elaine，美国卡内基梅隆大学

KE YI, Hong Kong University of Science and Technology, China

易柯，中国香港科技大学

The problem of continual observation under differential privacy has been studied extensively in the literature. However,all existing works,with the exception of $\left\lbrack  {{28},{50}}\right\rbrack$ ,have only studied the simple counting query and its derivatives. Join queries, which are arguably the most important class of queries in relational databases, have only been considered in $\left\lbrack  {{28},{50}}\right\rbrack$ ,but the solutions offered there have two limitations: First,they only support a few specific graph pattern queries, which are special cases of joins. Second, they require hard degree/frequency constraints on the graph/database instance, and the privatized query answers have errors proportional to these constraints.

差分隐私下的持续观测问题在学界已有广泛研究。然而除$\left\lbrack  {{28},{50}}\right\rbrack$外，现有研究仅聚焦于简单计数查询及其衍生类型。作为关系型数据库中最重要的查询类别，连接查询仅在$\left\lbrack  {{28},{50}}\right\rbrack$中被探讨，但该方案存在双重局限：其一，仅支持特定图模式查询这类连接查询的特例；其二，要求图结构/数据库实例预先设定严格的度数/频率约束，且隐私化查询结果的误差与这些约束成正比。

In this paper, we propose a new differentially private mechanism for continual observation of joins that overcomes these two limitations. Our mechanism supports arbitrary joins and predicates, and do not require any constraints to be given in advance, even over an infinite stream. More importantly, it yields an error that is proportional to the actual maximum degree/frequencies in the graph/database instance at the current time of observation. Such an instance-specific utility guarantee is much preferred for the continual observation problem, where the database size and the query answer may change significantly over time.

本文提出新型差分隐私机制，突破上述局限实现连接操作的持续观测。该机制支持任意连接条件与谓词，无需预先设定约束条件，即便面对无限数据流仍可适用。更重要的是，其生成误差仅与观测时刻图结构/数据库实例的实际最大度数/频率成正比。这种基于实例特性的效用保障特别适用于持续观测场景——数据库规模与查询结果可能随时间显著变化。

## CCS Concepts: $\cdot$ Information systems $\rightarrow$ Database query processing; $\cdot$ Security and privacy $\rightarrow$ Database and storage security; - Theory of computation $\rightarrow$ Theory of database privacy and security.

## CCS概念体系：$\cdot$信息系统$\rightarrow$数据库查询处理；$\cdot$安全与隐私$\rightarrow$数据库及存储安全；-计算理论$\rightarrow$数据库隐私与安全理论

Additional Key Words and Phrases: Differential privacy; Join query; Continual observation

附加关键词：差分隐私；连接查询；持续观测

## ACM Reference Format:

## ACM文献格式：

Wei Dong, Zijun Chen, Qiyao Luo, Elaine Shi, and Ke Yi. 2024. Continual Observation of Joins under Differential Privacy. Proc. ACM Manag. Data 2, 3 (SIGMOD), Article 128 (June 2024), 27 pages. https://doi.org/10.1145/ 3654931

魏东、陈子君、罗淇耀、石Elaine、易柯。2024。差分隐私下的连接操作持续观测研究。ACM数据管理会刊2卷3期(SIGMOD)，论文128号（2024年6月），27页。https://doi.org/10.1145/3654931

## 1 INTRODUCTION

## 1 引言

Following the influential paper of Dwork et al. [23], a series of works [5, 8-10, 13-15, 17, 24, 28, 30, ${31},{41},{47},{49},{53},{55}\rbrack$ have extensively studied the problem of continual observation under differential privacy. In this problem,we are given a query $Q$ and a possibly infinite stream $\mathbf{I} = \left( {{e}^{\left( 1\right) },{e}^{\left( 2\right) },\ldots }\right)$ of tuples arriving over time $\left( {e}^{\left( t\right) }\right.$ is set to the dummy tuple $\bot$ if no tuple arrives at time $\left. t\right)$ . The goal is to release the query answer $Q\left( {\mathbf{I}}^{\left( t\right) }\right)$ at each time step $t \in  {\mathbb{Z}}^{ + }$ under differential privacy(DP),where ${\mathbf{I}}^{\left( t\right) } = \left\{  {{e}^{\left( 1\right) },\ldots ,{e}^{\left( t\right) }}\right\}$ is the database instance consisting of all tuples (not including dummy tuples) that have arrived up until time $t$ . Little motivation is needed for this problem,which naturally arises whenever one wishes to monitor private data that evolves over time.

自Dwork等人开创性论文[23]以来，系列研究[5,8-10,13-15,17,24,28,30,${31},{41},{47},{49},{53},{55}\rbrack$深入探讨了差分隐私下的持续观测问题。该场景给定查询$Q$与可能无限的数据流$\mathbf{I} = \left( {{e}^{\left( 1\right) },{e}^{\left( 2\right) },\ldots }\right)$——若时刻$\left. t\right)$无数据到达，则$\left( {e}^{\left( t\right) }\right.$设为虚拟元组$\bot$。目标是在差分隐私(DP)条件下，每个时间步$t \in  {\mathbb{Z}}^{ + }$发布查询结果$Q\left( {\mathbf{I}}^{\left( t\right) }\right)$，其中${\mathbf{I}}^{\left( t\right) } = \left\{  {{e}^{\left( 1\right) },\ldots ,{e}^{\left( t\right) }}\right\}$为由截至$t$时刻所有到达元组（不含虚拟元组）构成的数据库实例。该问题在需要监测随时间演变的隐私数据时自然显现其重要性。

This work is licensed under a Creative Commons Attribution International 4.0 License.

本作品采用知识共享署名国际4.0许可协议

---

<!-- Footnote -->

Authors' addresses: Wei Dong, wdong2@cs.cmu.edu, Carnegie Mellon University, Pittsburgh, USA; Zijun Chen, zchendg@ cse.ust.hk; Qiyao Luo, qluoak@cse.ust.hk, Hong Kong University of Science and Technology, Hong Kong, China; Elaine Shi, runting@cs.cmu.edu, Carnegie Mellon University, Pittsburgh, USA; Ke Yi, yike@cse.ust.hk, Hong Kong University of Science and Technology, Hong Kong, China.

作者联系方式：魏东 wdong2@cs.cmu.edu，美国匹兹堡卡内基梅隆大学；陈子君 zchendg@cse.ust.hk；罗淇耀 qluoak@cse.ust.hk，中国香港科技大学；石Elaine runting@cs.cmu.edu，美国匹兹堡卡内基梅隆大学；易柯 yike@cse.ust.hk，中国香港科技大学

<!-- Footnote -->

---

<!-- Media -->

<table><tr><td colspan="2">Mechanism</td><td>Ours</td><td>[28, 50]</td><td>Composition</td></tr><tr><td rowspan="3">Error level at time $t$</td><td>General join counting queries</td><td>poly $\left( {\operatorname{mf}\left( {\mathbf{I}}^{\left( t\right) }\right) }\right)$</td><td>Not supported</td><td>$\Omega \left( {T}^{\frac{n}{2}}\right)$</td></tr><tr><td>Triangle counting queries</td><td>$\widetilde{O}\left( \widehat{\mathrm{{mf}}}\right)$</td><td>$\Omega \left( {T}^{1.5}\right)$</td><td>$\widetilde{O}\left( {\left( \operatorname{mf}\left( {\mathbf{I}}^{\left( t\right) }\right) \right) }^{n - 1}\right)$</td></tr><tr><td>$n$ -star counting queries</td><td>$\widetilde{O}\left( {\left( \operatorname{mf}\left( {\mathbf{I}}^{\left( t\right) }\right) \right) }^{n - 1}\right)$</td><td>$\widetilde{O}\left( {\widehat{\mathrm{{mf}}}}^{n - 1}\right)$</td><td>$\Omega \left( {T}^{\frac{n}{2}}\right)$</td></tr></table>

<table><tbody><tr><td colspan="2">机制</td><td>我们的方法</td><td>[28, 50]</td><td>组成</td></tr><tr><td rowspan="3">时间$t$的误差水平</td><td>通用连接计数查询</td><td>多项式$\left( {\operatorname{mf}\left( {\mathbf{I}}^{\left( t\right) }\right) }\right)$</td><td>不支持</td><td>$\Omega \left( {T}^{\frac{n}{2}}\right)$</td></tr><tr><td>三角形计数查询</td><td>$\widetilde{O}\left( \widehat{\mathrm{{mf}}}\right)$</td><td>$\Omega \left( {T}^{1.5}\right)$</td><td>$\widetilde{O}\left( {\left( \operatorname{mf}\left( {\mathbf{I}}^{\left( t\right) }\right) \right) }^{n - 1}\right)$</td></tr><tr><td>$n$星形计数查询</td><td>$\widetilde{O}\left( {\left( \operatorname{mf}\left( {\mathbf{I}}^{\left( t\right) }\right) \right) }^{n - 1}\right)$</td><td>$\widetilde{O}\left( {\widehat{\mathrm{{mf}}}}^{n - 1}\right)$</td><td>$\Omega \left( {T}^{\frac{n}{2}}\right)$</td></tr></tbody></table>

Table 1. Comparison between our work with prior works. Composition refers to the naïve baseline of using advanced composition on top of known static join-counting schemes $\left\lbrack  {{19},{20}}\right\rbrack  .T$ is the time domain size, $n$ is the number of relations in $Q.\operatorname{mf}\left( {\mathrm{I}}^{\left( t\right) }\right)$ denotes the maximum frequency of any single attribute in instance $\mathrm{I}$ at time $t$ and $\widehat{\mathrm{{mf}}}$ is an a-priori upper bound of that. Note that for graph pattern counting queries, $\widehat{\mathrm{{mf}}} = D$ , while $\operatorname{mf}\left( {\mathbf{I}}^{\left( t\right) }\right)$ is the actual maximum degree of the graph at time $t$ .

表1. 本研究与先前工作的对比。"Composition"指在已知静态连接计数方案基础上采用高级组合的朴素基线方法。$\left\lbrack  {{19},{20}}\right\rbrack  .T$表示时域大小，$n$代表$Q.\operatorname{mf}\left( {\mathrm{I}}^{\left( t\right) }\right)$中关系的数量，$\mathrm{I}$表示实例在时间$t$时任一属性的最大频率，而$\widehat{\mathrm{{mf}}}$是其先验上界。需注意对于图模式计数查询，$\widehat{\mathrm{{mf}}} = D$，而$\operatorname{mf}\left( {\mathbf{I}}^{\left( t\right) }\right)$是时间$t$时图的实际最大度数。

<!-- Media -->

However, all past works in the continual setting, with the exception of the recent two [28, 50], have only considered the simple counting query (i.e., $Q\left( \mathbf{I}\right)  \mathrel{\text{:=}} \left| \mathbf{I}\right| )\left\lbrack  {{10},{23}}\right\rbrack$ ,and its derivatives such as histograms [8, 9, 53], sum queries [5, 15, 33, 47, 55, 56], and linear queries [14, 49]. Most importantly, all these queries have a bounded global sensitivity ${\mathrm{{GS}}}_{Q}$ ,i.e.,the query answer (at any particular time) changes by at most ${\mathrm{{GS}}}_{Q}$ if the stream contains one more tuple,e.g., ${\mathrm{{GS}}}_{Q} = 1$ for simple counting. On the other hand, join (counting) queries, which are clearly highly useful, have not been thoroughly studied. The main technical challenge is that joins have unbounded global sensitivity. For example,consider a simple two-way join query of the form $Q = \left| {{R}_{1}\left( A\right)  \boxtimes  {R}_{2}\left( {A,B}\right) }\right|$ ,i.e.,we want to join the tables ${R}_{1}$ and ${R}_{2}$ based on the attribute $A$ ,and count how many tuples are in the joined table. Each tuple in the input stream is either of the form ${R}_{1} : \left( a\right)$ or ${R}_{2} : \left( {a,b}\right)$ where the markers ${R}_{1}$ and ${R}_{2}$ denote which table the corresponding tuple belongs to. Suppose initially, the input stream is $\mathbf{I} = \left( {{R}_{2} : \left( {{a}_{1},{b}_{1}}\right) ,{R}_{2} : \left( {{a}_{1},{b}_{2}}\right) ,\ldots ,{R}_{2} : \left( {{a}_{1},{b}_{T}}\right) }\right)$ ,where all tuples belong to ${R}_{2}$ . At this moment,there is no join result,i.e., $Q\left( \mathbf{I}\right)  = 0$ . If now a new tuple ${R}_{1} : \left( {a}_{1}\right)$ arrives,then $Q\left( \mathbf{I}\right)$ suddenly becomes $T$ . The amount of change is $T$ ,which can be unbounded as $T$ goes to infinity.

然而，除近期两项研究[28,50]外，所有持续环境下的既往工作仅考虑了简单计数查询（即$Q\left( \mathbf{I}\right)  \mathrel{\text{:=}} \left| \mathbf{I}\right| )\left\lbrack  {{10},{23}}\right\rbrack$及其衍生形式，如直方图[8,9,53]、求和查询[5,15,33,47,55,56]和线性查询[14,49]）。最关键的是，这些查询都具有有界全局敏感度${\mathrm{{GS}}}_{Q}$，即若数据流新增一个元组，查询结果（在任何特定时间）的变化量至多为${\mathrm{{GS}}}_{Q}$（例如简单计数时为${\mathrm{{GS}}}_{Q} = 1$）。另一方面，极具实用价值的连接（计数）查询尚未得到深入研究，其核心技术难点在于连接操作具有无界全局敏感度。例如考察形如$Q = \left| {{R}_{1}\left( A\right)  \boxtimes  {R}_{2}\left( {A,B}\right) }\right|$的简单双向连接查询：基于属性$A$连接表${R}_{1}$与${R}_{2}$，并统计连接表中的元组数量。输入流中的每个元组形式为${R}_{1} : \left( a\right)$或${R}_{2} : \left( {a,b}\right)$，其中标记${R}_{1}$和${R}_{2}$指明所属表。假设初始输入流为$\mathbf{I} = \left( {{R}_{2} : \left( {{a}_{1},{b}_{1}}\right) ,{R}_{2} : \left( {{a}_{1},{b}_{2}}\right) ,\ldots ,{R}_{2} : \left( {{a}_{1},{b}_{T}}\right) }\right)$（所有元组均属${R}_{2}$），此时连接结果$Q\left( \mathbf{I}\right)  = 0$为空。若新增元组${R}_{1} : \left( {a}_{1}\right)$，则$Q\left( \mathbf{I}\right)$骤增至$T$，该变化量$T$可随$T$趋近无穷而无界增长。

Fichtenberger et al. [28] and Song et al. [50] have studied the continual observation of joins under differential privacy, among some other graph problems. However, their solution for joins has two limitations: First, their mechanism relies on a query-specific method for calculating the global sensitivity for a given query under the continual setting (see Section 3.4 for more details). They only give the method for triangles and $n$ -stars,which do not work for general graph pattern counting queries. Second,and,and more importantly,they solve the ${\mathrm{{GS}}}_{Q} = \infty$ issue by introducing a hard,a priori degree constraint $D$ on the graph,thus making the global sensitivity bounded. For example, ${\mathrm{{GS}}}_{Q} = D$ for triangle counting,and ${\mathrm{{GS}}}_{Q} = {D}^{n - 1}$ for $n$ -star counting. Consequently,their mechanism has an error proportional to ${\mathrm{{GS}}}_{Q}$ for every time step. Additionally,another limitation in their mechanism is that it only supports a finite time domain of a predefined length $T$ . The reason is that they partition the time domain with a tree structure, where the height of the tree must be predetermined so as to allocate the privacy budget to each level. On the other hand, our mechanism supports an infinite time domain by adopting a growing tree structure, and we allocate the privacy budget using a telescoping strategy [10] (see Section 3.4 for more details).

Fichtenberger等人[28]和Song等人[50]研究了差分隐私下连接操作的持续观测问题以及其他若干图论问题。然而，他们的连接查询解决方案存在两个局限：首先，其机制依赖于针对特定查询的全局敏感度计算方法（详见第3.4节），但仅给出了三角形和$n$-星形结构的计算方案，无法适用于通用图模式计数查询。其次且更重要的是，他们通过引入硬性的先验度约束$D$来解决${\mathrm{{GS}}}_{Q} = \infty$问题，从而限定全局敏感度范围——例如三角形计数采用${\mathrm{{GS}}}_{Q} = D$约束，$n$-星形计数采用${\mathrm{{GS}}}_{Q} = {D}^{n - 1}$约束。这导致其机制在每个时间步的误差与${\mathrm{{GS}}}_{Q}$成正比。此外，该机制仅支持预定义长度$T$的有限时间域，因其采用树结构划分时间域时需预先确定树高以分配隐私预算。相比之下，我们通过动态生长的树结构支持无限时间域，并采用伸缩策略[10]分配隐私预算（详见第3.4节）。

We can generalize their approach to the relational model, where the degree constraints become frequency constraints. ${\mathrm{{GS}}}_{Q}$ is thus a function of the these frequency constraints. For example,let $\overset{⏜}{mf}$ denote an a-priori upper bound on the maximum frequency of any single attribute in any table. For simplicity,we will use a single $\overset{⏜}{mf}$ for all tables here,but our technical sections later will give more refined bounds when $\overset{⏜}{mf}$ differs across the tables. Then,for the $n$ -line path join query of the form $Q = \left| {{R}_{1}\left( {{A}_{1},{A}_{2}}\right)  \bowtie  {R}_{2}\left( {{A}_{2},{A}_{3}}\right)  \bowtie  \ldots  \bowtie  {R}_{n}\left( {{A}_{n},{A}_{n + 1}}\right) }\right|$ ,GS ${}_{Q}$ can be as large as ${\widehat{\mathrm{{mf}}}}^{n - 1}$ . For a general $n$ -way join query,the global sensitivity depends on the polymatroid bound [2] of the boundary queries of the $Q$ . When $n = O\left( 1\right) ,{\mathrm{{GS}}}_{Q}$ is a polynomial function (denoted poly(mf)in the frequency constraint $\overset{⏜}{mf}$ .

我们可将其方法推广至关系模型，此时度约束转化为频次约束。${\mathrm{{GS}}}_{Q}$遂成为这些频次约束的函数。例如设$\overset{⏜}{mf}$表示任意表中单属性最大频次的先验上界（为简化起见，此处所有表共用同一$\overset{⏜}{mf}$值，后续技术章节将给出各表$\overset{⏜}{mf}$不同时的精细化边界）。对于$n$线路径连接查询$Q = \left| {{R}_{1}\left( {{A}_{1},{A}_{2}}\right)  \bowtie  {R}_{2}\left( {{A}_{2},{A}_{3}}\right)  \bowtie  \ldots  \bowtie  {R}_{n}\left( {{A}_{n},{A}_{n + 1}}\right) }\right|$，全局敏感度GS${}_{Q}$最高可达${\widehat{\mathrm{{mf}}}}^{n - 1}$。对于通用$n$路连接查询，全局敏感度取决于$Q$边界查询的多拟阵界[2]。当$n = O\left( 1\right) ,{\mathrm{{GS}}}_{Q}$为多项式函数时（在频次约束$\overset{⏜}{mf}$中记作poly(mf)）。

The main drawback of using these hard, a priori frequency constraints is that it is difficult to predict the parameters in practice, especially when the stream can be unbounded. If set too conservatively, a large error will be incurred; if set too small, the constraint can be easily violated. Interestingly, we give an example (see Example 5.1) showing that naïvely clipping the database using the given frequency constraints not only hurts accuracy but also violates DP.

采用硬性先验频次约束的主要缺陷在于：实践中难以预测参数值（特别是面对无界数据流时）。约束值设置过保守会导致较大误差，过小则易违反约束。有趣的是，我们通过示例5.1证明：若直接依据给定频次约束裁剪数据库，不仅会损害精度，还会违反差分隐私(DP)。

Another line of work studies DP single-shot join counting queries for a static database. Although these works $\left\lbrack  {{19},{20},{32}}\right\rbrack$ can remove the dependency on a-priori frequency constraints,their techniques do not easily generalize to the continual setting. Specifically, running a single-shot mechanism on each time step and naïvely applying the advanced composition theorem [26] results in an error as large as $\Omega \left( {T}^{n/2}\right)$ ,where $n$ is the number of relations involved in the query $Q$ .

另一研究方向针对静态数据库的DP单次连接计数查询。虽然这些工作$\left\lbrack  {{19},{20},{32}}\right\rbrack$能消除对先验频次约束的依赖，但其技术难以迁移到持续观测场景。具体而言，若在每个时间步运行单次机制并简单套用高级组合定理[26]，将产生高达$\Omega \left( {T}^{n/2}\right)$的误差（其中$n$是查询$Q$涉及的关系数）。

Our contributions. This paper proposes the first DP algorithm for general join counting queries under continual observation with infinite time domain. Our algorithm supports arbitrary joins (including self-joins) and predicates, which cover all graph pattern counting queries as special cases, and does not require any constraints on the input stream. More importantly, our algorithm achieves a time-dependent instance-specific error: the error at any time step $t$ depends only on the actual maximum frequency of any attribute at time step $t$ ,henceforth denoted $\operatorname{mf}\left( {\mathbf{I}}^{\left( t\right) }\right)$ . For example,for the $n$ -line path join query,the error at time $t$ is $\widetilde{O}\left( {\left( \operatorname{mf}\left( {\mathbf{I}}^{\left( t\right) }\right) \right) }^{n - 1}\right)$ ,where the $\widetilde{O}$ notation hides $\varepsilon$ and polylogarithmic factors. For more general queries where $n = O\left( 1\right)$ ,we achieve $\operatorname{poly}\left( {\operatorname{mf}\left( {\mathbf{I}}^{\left( t\right) }\right) }\right)$ error at time $t$ . Later in our technical sections,we will give a more refined bound on the error when the actual mf differs across the tables. For specific queries such as the 2-line path counting and $n$ -star counting query, it can be further demonstrated that such an error is optimal [16]. The comparison between our mechanism and prior works are shown in Table 1.

我们的贡献。本文首次提出了一种针对无限时间域下持续观测的通用连接计数查询的差分隐私(DP)算法。该算法支持任意连接（包括自连接）和谓词操作，涵盖所有图模式计数查询作为特例，且无需对输入流施加任何约束。更重要的是，本算法实现了随时间变化的实例特定误差：在任意时间步$t$的误差仅取决于该时间步$t$的实际最大属性频率（此后记为$\operatorname{mf}\left( {\mathbf{I}}^{\left( t\right) }\right)$）。例如，对于$n$-线路径连接查询，时间$t$的误差为$\widetilde{O}\left( {\left( \operatorname{mf}\left( {\mathbf{I}}^{\left( t\right) }\right) \right) }^{n - 1}\right)$，其中$\widetilde{O}$符号隐藏了$\varepsilon$及多对数因子。对于$n = O\left( 1\right)$的更通用查询，我们在时间$t$实现$\operatorname{poly}\left( {\operatorname{mf}\left( {\mathbf{I}}^{\left( t\right) }\right) }\right)$误差。在后文技术章节中，当不同表的实际最大频率存在差异时，我们将给出更精确的误差边界。对于2-线路径计数和$n$-星形计数等特定查询，可进一步证明该误差是最优的[16]。表1展示了本机制与现有工作的对比。

We evaluate the performance of our algorithms using seven real-world graph datasets and the TPC-H benchmark. Our experiments show that for both graph queries and general join queries, achieving such time-dependent instance-specific error significantly improves the accuracy of the algorithm in comparison with previous approaches $\left\lbrack  {{28},{50}}\right\rbrack$ that make use of a priori frequency constraints,even if they are set to be much smaller than $T$ . For example,for 4-star counting query on a graph dataset with $T \approx  {10}^{7}$ tuples,we improve the the error by ${10}^{14} \times$ ,in comparison with prior approaches $\left\lbrack  {{28},{50}}\right\rbrack$ where we set the a-priori frequency constraint to $T/{1000}$ .

我们使用七个真实图数据集和TPC-H基准测试评估算法性能。实验表明，对于图查询和通用连接查询，相比采用先验频率约束的现有方法$\left\lbrack  {{28},{50}}\right\rbrack$（即使将其设置为远小于$T$），实现这种随时间变化的实例特定误差能显著提升算法精度。例如，在包含$T \approx  {10}^{7}$元组的图数据集上进行4-星计数查询时，相较于将先验频率约束设为$T/{1000}$的现有方法$\left\lbrack  {{28},{50}}\right\rbrack$，我们将误差降低了${10}^{14} \times$。

Technical highlight. In achieving this goal, we have resolved two technical difficulties. First, we design a mechanism to estimate the actual maximum frequency adaptively and in a privacy-preserving fashion as the database grows. In doing so, we can get rid of all a priori constraints, even over an infinite stream. Second, since our estimated maximum frequencies can be under-approximating, using them directly to determine how much noise to add may violate the privacy requirement. Therefore, we devise a new clipping mechanism so that the clipped database instance always meets the constraints while preserving differential privacy, which allows for the further application of the mechanism with frequency constraints. Given that our estimated maximum frequencies change over time, a new clipping is invoked at each change. To handle an infinite stream, we allocate the privacy budgets across these clippings in a telescoping manner. We have built such a system prototype to support a wide range of SQL queries and graph pattern counting queries. ${}^{1}$

技术亮点。为实现该目标，我们攻克了两大技术难点：首先设计了一种自适应估计实际最大频率的隐私保护机制，随着数据库增长动态调整，从而摆脱了对无限数据流的所有先验约束；其次由于估计的最大频率可能存在低估，直接用于确定噪声添加量可能违反隐私要求，因此我们开发了新的截断机制，在保证差分隐私的前提下使截断后的数据库实例始终满足约束条件，从而支持带频率约束机制的扩展应用。鉴于估计的最大频率会随时间变化，每次变化时都会触发新的截断操作。针对无限流处理，我们采用望远镜式分配法分配隐私预算。目前已构建支持多种SQL查询和图模式计数查询的系统原型。${}^{1}$

---

<!-- Footnote -->

${}^{1}$ Code is available at https://github.com/hkustDB/Dynamic-Join.

${}^{1}$代码开源地址：https://github.com/hkustDB/Dynamic-Join

<!-- Footnote -->

---

Paper organization. The rest of the paper is organized as follows. Section 2 and Section 3 review related work and the necessary preliminaries. In Section 4, we show how to generalize the solution of $\left\lbrack  {{28},{50}}\right\rbrack$ to arbitrary joins under some given frequency constraints. Section 5 presents our main result: a new clipping mechanism and how to choose the clipping thresholds adaptively so as to achieve an instance-specific utility guarantee. Section 6 and 7 discuss how to handle self-joins, predicates, as well as some implementation details. Experimental results are presented in Section 8 before concluding the paper with some open problems.

论文结构。本文后续安排如下：第2-3节回顾相关工作与基础知识；第4节展示如何在给定频率约束下将$\left\lbrack  {{28},{50}}\right\rbrack$的解决方案推广至任意连接；第5节提出核心成果——新型截断机制及自适应阈值选择方法以实现实例特定的效用保障；第6-7节讨论自连接、谓词处理及实现细节；第8节展示实验结果；最后提出开放性问题作为总结。

## 2 RELATED WORK

## 2 相关工作

Answering queries in a relational database under DP has been extensively studied in the static setting $\left\lbrack  {3,6,{16},{18} - {20},{22},{27},{32},{36},{42},{44},{46},{48},{51},{52}}\right\rbrack$ . There are two common DP policies in the relational model: tuple-DP, which protects the privacy of individual tuples, and user-DP, safeguarding users who may possess multiple tuples. Under tuple-DP, the problem can be trivially solved by the Laplace mechanism adding only constant noise if the query does not contain joins. Therefore, significant efforts have been devoted to joins. Most existing works [3, 42, 44, 46, 48] can only handle restricted types of joins. [32] proposed the first mechanism to support arbitrary joins, which are further improved by [19, 20]. Recently, query answering under user-DP has also been studied, where we need additional noise to ensure a higher level privacy protection [6, 16, 18, ${22},{27},{36},{52}\rbrack$ . As an important special case of join queries,graph pattern counting queries under DP $\left\lbrack  {4,{12},{34},{35},{45},{57}}\right\rbrack$ has also been studied. Note that when the edges of the graph are considered as a relation, tuple-DP and user-DP degenerate into edge-DP [4, 34, 45, 57] and node-DP [4, 12, 35], respectively. For more details of answering relational queries under DP in the static setting, see the survey [21].

在静态场景下，关系型数据库中满足差分隐私(DP)的查询应答已得到广泛研究$\left\lbrack  {3,6,{16},{18} - {20},{22},{27},{32},{36},{42},{44},{46},{48},{51},{52}}\right\rbrack$。关系模型中有两种常见DP策略：元组差分隐私(tuple-DP)——保护单个元组的隐私，以及用户差分隐私(user-DP)——保护可能拥有多个元组的用户隐私。对于不含连接操作的查询，元组差分隐私下仅需通过添加恒定噪声的拉普拉斯机制即可简单求解。因此现有研究主要聚焦连接查询，大多数成果[3,42,44,46,48]仅能处理受限连接类型。[32]首次提出支持任意连接操作的机制，后续被[19,20]改进。近期用户差分隐私下的查询应答也得到研究，需要额外噪声确保更高阶隐私保护[6,16,18]${22},{27},{36},{52}\rbrack$。作为连接查询的重要特例，差分隐私下的图模式计数查询$\left\lbrack  {4,{12},{34},{35},{45},{57}}\right\rbrack$亦有研究。需注意当图的边被视为关系时，元组差分隐私与用户差分隐私分别退化为边差分隐私[4,34,45,57]和节点差分隐私[4,12,35]。静态场景下关系型查询的差分隐私应答详见综述[21]。

Under continual observation, tuple-DP corresponds to event-DP, which protects the privacy of the tuple at each time step. Research so far has mostly focused on event-DP and queries without joins. Dwork et al. [23] and Chan et al. [10] initialized the studies by proposing the binary mechanism to answer counting queries and $\left\lbrack  {{13},{15},{24},{30},{31},{41}}\right\rbrack$ have proposed techniques to reduce the error, either by examining specific instances or by relaxing the privacy definition to $\left( {\varepsilon ,\delta }\right)$ -DP. Besides the simple counting query,other queries such as sum [5, 15, 33, 47, 55, 56], histogram [8, 9, 53], and linear queries $\left\lbrack  {{14},{49}}\right\rbrack$ have also been explored. For joins,as mentioned earlier,the only existing work is $\left\lbrack  {{28},{50}}\right\rbrack$ ,which has studied some specific graph pattern counting queries. They generalize the binary mechanism by assuming some a priori degree constraints. A recent work [17] initializes the study of continual observation under user-DP, but it does not consider joins.

在持续观测场景中，元组差分隐私对应事件差分隐私(event-DP)，保护每个时间步的元组隐私。现有研究主要集中于不含连接的事件差分隐私查询。Dwork等人[23]与Chan等人[10]开创性提出应答计数查询的二进制机制$\left\lbrack  {{13},{15},{24},{30},{31},{41}}\right\rbrack$，后续通过分析特定实例或放宽隐私定义至$\left( {\varepsilon ,\delta }\right)$-DP来降低误差。除简单计数查询外，求和[5,15,33,47,55,56]、直方图[8,9,53]及线性查询$\left\lbrack  {{14},{49}}\right\rbrack$等也被探索。对于连接查询，如前所述，仅$\left\lbrack  {{28},{50}}\right\rbrack$研究了特定图模式计数查询，通过预设度数约束推广了二进制机制。近期工作[17]开创了用户差分隐私下的持续观测研究，但未涉及连接操作。

## 3 PRELIMINARIES

## 3 基础知识

### 3.1 Notation

### 3.1 符号体系

We first introduce the notation in the static setting. Let $\left\lbrack  n\right\rbrack   \mathrel{\text{:=}} \{ 1,\ldots ,n\}$ ,and $\left\lbrack  {i,j}\right\rbrack   \mathrel{\text{:=}} \{ i,\ldots ,j\}$ . Denote $\mathbf{R}$ to be the database schema. In this paper,we primarily focus on multi-way join counting queries in the form of

首先介绍静态场景的符号体系。设$\left\lbrack  n\right\rbrack   \mathrel{\text{:=}} \{ 1,\ldots ,n\}$，及$\left\lbrack  {i,j}\right\rbrack   \mathrel{\text{:=}} \{ i,\ldots ,j\}$。定义$\mathbf{R}$为数据库模式。本文主要研究形如下的多路连接计数查询：

$$
Q \mathrel{\text{:=}} \left| {{R}_{1}\left( {\mathbf{x}}_{1}\right)  \bowtie  \cdots  \bowtie  {R}_{n}\left( {\mathbf{x}}_{n}\right) }\right| ,
$$

where each ${R}_{i},i \in  \left\lbrack  n\right\rbrack$ is a relation in $\mathbf{R}$ ,and ${\mathbf{x}}_{i}$ denotes the set of variables/attributes of ${R}_{i}$ . For any single variable $x$ ,we use $\operatorname{dom}\left( x\right)$ to denote the domain of $x$ . For a set of variables $\mathbf{x} = \left( {{x}_{1},\ldots ,{x}_{k}}\right)$ , define $\operatorname{dom}\left( \mathbf{x}\right)  = \operatorname{dom}\left( {x}_{1}\right)  \times  \cdots  \times  \operatorname{dom}\left( {x}_{k}\right)$ .

其中每个${R}_{i},i \in  \left\lbrack  n\right\rbrack$是$\mathbf{R}$中的关系，${\mathbf{x}}_{i}$表示${R}_{i}$的变量/属性集合。对任意单一变量$x$，用$\operatorname{dom}\left( x\right)$表示其定义域。对于变量集合$\mathbf{x} = \left( {{x}_{1},\ldots ,{x}_{k}}\right)$，定义$\operatorname{dom}\left( \mathbf{x}\right)  = \operatorname{dom}\left( {x}_{1}\right)  \times  \cdots  \times  \operatorname{dom}\left( {x}_{k}\right)$。

Let $\mathbf{I}$ be a database instance of $\mathbf{R}$ ,and $Q\left( \mathbf{I}\right)$ the result of evaluating $Q$ on $\mathbf{I}$ . For each relation ${R}_{i} \in  \mathbf{R}$ ,the instance of ${R}_{i}$ in $\mathbf{I}$ is denoted by ${R}_{i}\left( \mathbf{I}\right)$ . For brevity,we use ${I}_{i}$ as a shorthand for ${R}_{i}\left( \mathbf{I}\right)$ . Let $N = \mathop{\sum }\limits_{i}\left| {I}_{i}\right|$ ,which is the total instance size. In this paper,we follow the convention of data complexity [1],i.e.,the complexity is measured in the instance size $N$ while the query size (i.e., Static setting:

设$\mathbf{I}$为$\mathbf{R}$的数据库实例，$Q\left( \mathbf{I}\right)$表示在$\mathbf{I}$上评估$Q$的结果。对于每个关系${R}_{i} \in  \mathbf{R}$，$\mathbf{I}$中${R}_{i}$的实例记作${R}_{i}\left( \mathbf{I}\right)$。为简洁起见，我们用${I}_{i}$作为${R}_{i}\left( \mathbf{I}\right)$的简写。令$N = \mathop{\sum }\limits_{i}\left| {I}_{i}\right|$表示总实例大小。本文遵循数据复杂性[1]的惯例，即复杂度以实例规模$N$度量，而查询规模（即静态设定：

<!-- Media -->

<!-- figureText: ${I}_{1},{N}_{1} = 6$ ${I}_{2},{N}_{2} = 6$ ${I}_{3},{N}_{3} = 6$ $\operatorname{freq}\left( {{I}_{2},{b}_{1}}\right)  = 2$ ${R}_{3}\left( {C,A}\right)$ $\operatorname{freq}\left( {{I}_{3},{c}_{1}}\right)  = 3$ $\operatorname{freq}\left( {{I}_{2},{b}_{2}}\right)  = 2$ $\operatorname{freq}\left( {{I}_{3},{c}_{2}}\right)  = 3$ $\operatorname{freq}\left( {{I}_{2},{b}_{3}}\right)  = 2$ ${a}_{1}$ $\operatorname{mf}\left( {{I}_{3},C}\right)  = 3$ $\operatorname{mf}\left( {{I}_{2},B}\right)  = 2$ ${c}_{1}$ ${a}_{2}$ ${c}_{1}$ ${a}_{3}$ $\operatorname{freq}\left( {{I}_{2},{c}_{1}}\right)  = 3$ ${c}_{2}$ ${a}_{1}$ $\operatorname{freq}\left( {{I}_{3},{a}_{1}}\right)  = 2$ $\operatorname{freq}\left( {{I}_{2},{c}_{2}}\right)  = 3$ $\operatorname{freq}\left( {{I}_{3},{a}_{2}}\right)  = 2$ $\operatorname{mf}\left( {{I}_{2},C}\right)  = 3$ ${c}_{2}$ ${a}_{2}$ $\operatorname{freq}\left( {{I}_{3},{a}_{3}}\right)  = 2$ ${c}_{2}$ ${a}_{3}$ $\operatorname{mf}\left( {{I}_{3},A}\right)  = 2$ ${R}_{1}\left( {A,B}\right)$ $\operatorname{freq}\left( {{I}_{1},{a}_{1}}\right)  = 3$ ${R}_{2}\left( {B,C}\right)$ $\operatorname{freq}\left( {{I}_{1},{a}_{2}}\right)  = 2$ ${a}_{1}$ ${b}_{1}$ $\operatorname{freq}\left( {{I}_{1},{a}_{3}}\right)  = 1$ ${b}_{1}$ ${c}_{1}$ ${a}_{1}$ ${b}_{2}$ $\operatorname{mf}\left( {{I}_{1},A}\right)  = 3$ ${b}_{1}$ ${c}_{2}$ ${a}_{1}$ ${b}_{3}$ ${b}_{2}$ ${c}_{1}$ ${a}_{2}$ $\operatorname{freq}\left( {{I}_{1},{b}_{1}}\right)  = 2$ ${b}_{2}$ ${c}_{2}$ $\operatorname{freq}\left( {{I}_{1},{b}_{2}}\right)  = 2$ ${a}_{2}$ ${b}_{2}$ $\operatorname{freq}\left( {{I}_{1},{b}_{3}}\right)  = 2$ ${b}_{3}$ ${c}_{1}$ ${a}_{3}$ ${b}_{3}$ $\operatorname{mf}\left( {{I}_{1},B}\right)  = 2$ ${b}_{3}$ ${c}_{2}$ ${Q}_{\Delta }\left( \mathbf{I}\right)  = \left| {{I}_{1} \bowtie  {I}_{2} \bowtie  {I}_{3}}\right|  = {12}$ -->

<img src="https://cdn.noedgeai.com/0196599c-190a-7a73-a499-f2871a0182b1_4.jpg?x=233&y=349&w=1202&h=386&r=0"/>

<!-- Media -->

Dynamic setting: the number of relations and number of variables) is taken as a constant. For any $\mathbf{I}$ and ${\mathbf{I}}^{\prime }$ ,we write $\mathbf{I} \subseteq  {\mathbf{I}}^{\prime }$ if ${I}_{i} \subseteq  {I}_{i}^{\prime }$ for every $i \in  \left\lbrack  n\right\rbrack$ .

动态设定：关系数量与变量数量）视为常数。对于任意$\mathbf{I}$和${\mathbf{I}}^{\prime }$，若对每个$i \in  \left\lbrack  n\right\rbrack$都有${I}_{i} \subseteq  {I}_{i}^{\prime }$，则记作$\mathbf{I} \subseteq  {\mathbf{I}}^{\prime }$。

<!-- Media -->

<table><tr><td>Time 1</td><td>Time 2</td><td>Time 3</td><td>Time 4</td><td>Time 5</td><td>Time 6</td><td>Time 7</td><td>Time 8</td><td>Time 9</td><td>Time 10</td><td>...</td></tr><tr><td>$\left( {{a}_{1},{b}_{1}}\right)$</td><td>$\left( {{b}_{1},{c}_{1}}\right)$</td><td>$\left( {{c}_{1},{a}_{1}}\right)$</td><td>⊥</td><td>$\left( {{a}_{1},{b}_{2}}\right)$</td><td>$\left( {{b}_{1},{c}_{2}}\right)$</td><td>$\left( {{c}_{1},{a}_{2}}\right)$</td><td>$\left( {{a}_{1},{b}_{3}}\right)$</td><td>$\left( {{b}_{2},{c}_{1}}\right)$</td><td>$\left( {{c}_{1},{a}_{3}}\right)$</td><td>...</td></tr><tr><td/><td/><td/><td/><td/><td/><td/><td/><td/><td/><td/></tr><tr><td/><td>${\mathbf{I}}^{\left( 9\right) }$ :</td><td/><td/><td>${I}_{1}^{\left( 9\right) },{N}_{1}^{\left( 9\right) } = 3$</td><td/><td/><td>${I}_{2}^{\left( 9\right) },{N}_{2}^{\left( 9\right) } = 3$</td><td/><td colspan="2">${I}_{3}^{\left( 9\right) },{N}_{3}^{\left( 9\right) } = 2$</td></tr><tr><td/><td>${Q}_{\Delta }\left( {\mathbf{I}}^{\left( 9\right) }\right)  = 2$</td><td/><td/><td>${R}_{1}\left( {A,B}\right)$</td><td/><td/><td>${R}_{2}\left( {B,C}\right)$</td><td/><td>${R}_{3}\left( {C,A}\right)$</td><td/></tr><tr><td/><td/><td/><td>${a}_{1}$</td><td>${b}_{1}$</td><td/><td>${b}_{1}$</td><td>${c}_{1}$</td><td/><td>${c}_{1}$</td><td>${a}_{1}$</td></tr><tr><td/><td/><td/><td>${a}_{1}$ ${a}_{1}$</td><td>${b}_{2}$ ${b}_{3}$</td><td/><td>${b}_{1}$ ${b}_{2}$</td><td>${c}_{2}$ ${c}_{1}$</td><td/><td>${c}_{1}$</td><td>${a}_{2}$</td></tr></table>

<table><tbody><tr><td>时间1</td><td>时间2</td><td>时间3</td><td>时间4</td><td>时间5</td><td>时间6</td><td>时间7</td><td>时间8</td><td>时间9</td><td>时间10</td><td>...</td></tr><tr><td>$\left( {{a}_{1},{b}_{1}}\right)$</td><td>$\left( {{b}_{1},{c}_{1}}\right)$</td><td>$\left( {{c}_{1},{a}_{1}}\right)$</td><td>⊥</td><td>$\left( {{a}_{1},{b}_{2}}\right)$</td><td>$\left( {{b}_{1},{c}_{2}}\right)$</td><td>$\left( {{c}_{1},{a}_{2}}\right)$</td><td>$\left( {{a}_{1},{b}_{3}}\right)$</td><td>$\left( {{b}_{2},{c}_{1}}\right)$</td><td>$\left( {{c}_{1},{a}_{3}}\right)$</td><td>...</td></tr><tr><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td></tr><tr><td></td><td>${\mathbf{I}}^{\left( 9\right) }$ :</td><td></td><td></td><td>${I}_{1}^{\left( 9\right) },{N}_{1}^{\left( 9\right) } = 3$</td><td></td><td></td><td>${I}_{2}^{\left( 9\right) },{N}_{2}^{\left( 9\right) } = 3$</td><td></td><td colspan="2">${I}_{3}^{\left( 9\right) },{N}_{3}^{\left( 9\right) } = 2$</td></tr><tr><td></td><td>${Q}_{\Delta }\left( {\mathbf{I}}^{\left( 9\right) }\right)  = 2$</td><td></td><td></td><td>${R}_{1}\left( {A,B}\right)$</td><td></td><td></td><td>${R}_{2}\left( {B,C}\right)$</td><td></td><td>${R}_{3}\left( {C,A}\right)$</td><td></td></tr><tr><td></td><td></td><td></td><td>${a}_{1}$</td><td>${b}_{1}$</td><td></td><td>${b}_{1}$</td><td>${c}_{1}$</td><td></td><td>${c}_{1}$</td><td>${a}_{1}$</td></tr><tr><td></td><td></td><td></td><td>${a}_{1}$ ${a}_{1}$</td><td>${b}_{2}$ ${b}_{3}$</td><td></td><td>${b}_{1}$ ${b}_{2}$</td><td>${c}_{2}$ ${c}_{1}$</td><td></td><td>${c}_{1}$</td><td>${a}_{2}$</td></tr></tbody></table>

Fig. 1. Example: triangle counting as a joint-counting query ${Q}_{\nabla }\left( \mathbf{I}\right)  = \left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right)  \bowtie  {R}_{3}\left( {C,A}\right) }\right|$ .

图1. 示例：将三角形计数作为联合计数查询${Q}_{\nabla }\left( \mathbf{I}\right)  = \left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right)  \bowtie  {R}_{3}\left( {C,A}\right) }\right|$。

<!-- Media -->

For any $i \in  \left\lbrack  n\right\rbrack$ ,any ${\mathbf{x}}^{\prime } \subseteq  {\mathbf{x}}_{i}$ ,and any $a \in  \operatorname{dom}\left( {\mathbf{x}}^{\prime }\right)$ ,let $\operatorname{freq}\left( {{I}_{i},a}\right)$ be the number of times $a$ appears in attributes ${\mathbf{x}}^{\prime }$ in ${I}_{i}$ ,i.e.,

对于任意$i \in  \left\lbrack  n\right\rbrack$、任意${\mathbf{x}}^{\prime } \subseteq  {\mathbf{x}}_{i}$和任意$a \in  \operatorname{dom}\left( {\mathbf{x}}^{\prime }\right)$，令$\operatorname{freq}\left( {{I}_{i},a}\right)$表示$a$在${I}_{i}$的属性${\mathbf{x}}^{\prime }$中出现的次数，即

$$
\operatorname{freq}\left( {{I}_{i},a}\right)  = \left| \left\{  {e \in  {I}_{i} \mid  {\pi }_{{\mathbf{x}}^{\prime }}e = a}\right\}  \right| ,
$$

and the maximum frequency in attributes ${\mathbf{x}}^{\prime }$ in ${I}_{i}$ is

而属性${\mathbf{x}}^{\prime }$在${I}_{i}$中的最大频数为

$$
\operatorname{mf}\left( {{I}_{i},{\mathbf{x}}^{\prime }}\right)  = \mathop{\max }\limits_{{a \in  \operatorname{dom}\left( {\mathbf{x}}^{\prime }\right) }}\operatorname{freq}\left( {{I}_{i},a}\right) .
$$

Example 3.1. The following is the classical triangle query:

示例3.1. 以下是经典的三角形查询：

$$
{Q}_{\nabla } \mathrel{\text{:=}} \left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right)  \bowtie  {R}_{3}\left( {C,A}\right) }\right| , \tag{1}
$$

which involves three distinct relations and attributes. Figure 1 shows a particular instance and the values of freq and mf on this instance.

该查询涉及三个不同的关系和属性。图1展示了一个具体实例及该实例上的freq和mf值。

For counting the number of triangles in a graph, we can model all the edges as tuples in a single relation Edge(src, dst), and rewrite the query using self-joins and variable renaming:

为计算图中的三角形数量，可将所有边建模为单个关系Edge(src, dst)中的元组，并通过自连接和变量重写查询：

$$
\left| {\operatorname{Edge}\left( {A,B}\right)  \bowtie  \operatorname{Edge}\left( {B,C}\right)  \bowtie  \operatorname{Edge}\left( {C,A}\right) }\right| .
$$

Note that for counting some other patterns, predicates are needed to exclude degenerate cases, e.g., length- 2 paths should be counted by the query

注意在计算某些其他模式时，需要谓词来排除退化情况，例如应由查询计算的长度为2的路径

$$
\left| {{\sigma }_{A \neq  C}\left( {\operatorname{Edge}\left( {A,B}\right)  \bowtie  \operatorname{Edge}\left( {B,C}\right) }\right) }\right| .
$$

For most parts of the paper, we focus on queries without self-joins and predicates, which will be discussed in Section 6.

本文大部分内容聚焦于无自连接和谓词的查询，相关讨论将在第6节展开。

Under continual observation, which we also call the dynamic setting, the database instance becomes a possibly infinite stream of tuples $\mathbf{I} = \left( {{R}^{\left( 1\right) } : {e}^{\left( 1\right) },{R}^{\left( 2\right) } : {e}^{\left( 2\right) },\ldots }\right)$ ,where tuple ${e}^{\left( t\right) }$ is added to relation ${R}^{\left( t\right) }$ at $t$ . We set ${R}^{\left( t\right) }$ to be NULL and ${x}^{\left( t\right) }$ to the dummy tuple $\bot$ if no tuple arrives at time $t$ .

在持续观察（亦称动态设置）下，数据库实例变为可能无限的元组流$\mathbf{I} = \left( {{R}^{\left( 1\right) } : {e}^{\left( 1\right) },{R}^{\left( 2\right) } : {e}^{\left( 2\right) },\ldots }\right)$，其中元组${e}^{\left( t\right) }$在$t$时刻被添加到关系${R}^{\left( t\right) }$。若$t$时刻无元组到达，则将${R}^{\left( t\right) }$设为NULL，${x}^{\left( t\right) }$设为虚拟元组$\bot$。

For any ${t}_{1} \leq  {t}_{2} \in  {\mathbb{Z}}^{ + }$ ,the database instance within the time interval $\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack$ is denoted as ${\mathbf{I}}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  } \mathrel{\text{:=}} \left( {{I}_{1}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  },\ldots ,{I}_{n}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }}\right)$ ,where

对于任意${t}_{1} \leq  {t}_{2} \in  {\mathbb{Z}}^{ + }$，时间区间$\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack$内的数据库实例记为${\mathbf{I}}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  } \mathrel{\text{:=}} \left( {{I}_{1}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  },\ldots ,{I}_{n}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }}\right)$，其中

$$
{I}_{i}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  } \mathrel{\text{:=}} \left\{  {{e}^{\left( t\right) } : {t}_{1} \leq  t \leq  {t}_{2},{R}^{\left( t\right) } = {R}_{i}}\right\}  .
$$

When ${t}_{1} = 1$ ,we simplify the notation $\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack$ as $\left( {t}_{2}\right)$ like ${\mathbf{I}}^{\left( t\right) } = {\mathbf{I}}^{\left\lbrack  1,t\right\rbrack  },{I}_{i}^{\left( t\right) } = {I}_{i}^{\left\lbrack  1,t\right\rbrack  }$ ,etc. Specially, define ${I}_{i}^{\left( 0\right) } = \varnothing$ for each $i \in  \left\lbrack  n\right\rbrack$ .

当${t}_{1} = 1$时，我们将$\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack$简记为$\left( {t}_{2}\right)$如${\mathbf{I}}^{\left( t\right) } = {\mathbf{I}}^{\left\lbrack  1,t\right\rbrack  },{I}_{i}^{\left( t\right) } = {I}_{i}^{\left\lbrack  1,t\right\rbrack  }$等。特别地，为每个$i \in  \left\lbrack  n\right\rbrack$定义${I}_{i}^{\left( 0\right) } = \varnothing$。

In the dynamic setting, we wish to continually monitor the query answers, i.e., the query output also becomes a stream $Q\left( I\right)  \mathrel{\text{:=}} \left( {Q\left( {I}^{\left( 1\right) }\right) ,Q\left( {I}^{\left( 2\right) }\right) ,\ldots }\right)$ ,and it is required that $Q\left( {I}^{\left( t\right) }\right)$ should be outputted at time $t$ . Figure 1 also shows an example in the dynamic setting.

在动态环境中，我们希望持续监控查询结果，即查询输出也成为一个数据流$Q\left( I\right)  \mathrel{\text{:=}} \left( {Q\left( {I}^{\left( 1\right) }\right) ,Q\left( {I}^{\left( 2\right) }\right) ,\ldots }\right)$，并且要求$Q\left( {I}^{\left( t\right) }\right)$应在时间$t$输出。图1也展示了动态环境下的一个示例。

### 3.2 Differential Privacy

### 3.2 差分隐私

Definition 3.2 (Differential privacy). For any $\varepsilon  > 0$ ,a mechanism $\mathcal{M} : \mathcal{I} \rightarrow  \mathcal{Y}$ is $\varepsilon$ -differentially private (DP) if for any neighboring instances $\mathbf{I} \sim  {\mathbf{I}}^{\prime } \in  \mathcal{I}$ and any subset of outputs $Y \subseteq  \mathcal{Y}$ ,

定义3.2（差分隐私）。对于任意$\varepsilon  > 0$，机制$\mathcal{M} : \mathcal{I} \rightarrow  \mathcal{Y}$若满足$\varepsilon$-差分隐私（DP），则对于任何相邻实例$\mathbf{I} \sim  {\mathbf{I}}^{\prime } \in  \mathcal{I}$和输出子集$Y \subseteq  \mathcal{Y}$，

$$
\Pr \left\lbrack  {\mathcal{M}\left( \mathbf{I}\right)  \in  Y}\right\rbrack   \leq  {e}^{\varepsilon } \cdot  \Pr \left\lbrack  {\mathcal{M}\left( {\mathbf{I}}^{\prime }\right)  \in  Y}\right\rbrack  
$$

The exact definition of the neighboring relationship $\sim$ depends on what information is to be protected. In relational databases, two definitions have been adopted in the literature: tuple-DP $\left\lbrack  {3,{19},{20},{32},{42},{44},{46},{48}}\right\rbrack$ and user-DP $\left\lbrack  {6,{16},{18},{27},{36},{52}}\right\rbrack$ ,which respectively generalize edge-DP and node-DP for graph data. In this paper, we adopt the former, while leaving the latter as an interesting open problem. Under tuple-DP,two instances $\mathbf{I}$ and ${\mathbf{I}}^{\prime }$ are neighbors if one can be obtained from the other by inserting/deleting one tuple from some relation. More formally, define the distance between two relation instances ${I}_{i}$ and ${I}_{i}^{\prime }$ as $d\left( {{I}_{i},{I}_{i}^{\prime }}\right)  \mathrel{\text{:=}} \left| {{I}_{i} - {I}_{i}^{\prime }}\right|  + \left| {{I}_{i}^{\prime } - {I}_{i}}\right|$ ,and for two database instances $\mathbf{I}$ and ${\mathbf{I}}^{\prime },d\left( {\mathbf{I},{\mathbf{I}}^{\prime }}\right)  \mathrel{\text{:=}} \mathop{\sum }\limits_{{i \in  \left\lbrack  n\right\rbrack  }}d\left( {{I}_{i},{I}_{i}^{\prime }}\right)$ . In the dynamic setting,we have $d\left( {\mathbf{I},{\mathbf{I}}^{\prime }}\right)  \mathrel{\text{:=}} \mathop{\sum }\limits_{t}\left( {\left| {\left\{  {e}^{\left( t\right) }\right\}   - \left\{  {e}^{\prime \left( t\right) }\right\}  }\right|  + \left| {\left\{  {e}^{\prime \left( t\right) }\right\}   - \left\{  {e}^{\left( t\right) }\right\}  }\right| }\right)$ (set $\{  \bot  \}  = \varnothing$ ),i.e.,it is the minimum number of insertions/deletions needed to convert one stream of tuples to the other. For both scenarios, I and ${\mathbf{I}}^{\prime }$ are neighboring instances if $d\left( {\mathbf{I},{\mathbf{I}}^{\prime }}\right)  = 1$ .

相邻关系$\sim$的精确定义取决于需要保护的信息类型。在关系数据库中，文献采用了两种定义：元组差分隐私（tuple-DP）$\left\lbrack  {3,{19},{20},{32},{42},{44},{46},{48}}\right\rbrack$和用户差分隐私（user-DP）$\left\lbrack  {6,{16},{18},{27},{36},{52}}\right\rbrack$，它们分别推广了图数据中的边差分隐私（edge-DP）和节点差分隐私（node-DP）。本文采用前者，而将后者留作一个有趣的开放问题。在元组差分隐私下，若两个实例$\mathbf{I}$和${\mathbf{I}}^{\prime }$可通过在某个关系中插入/删除一个元组相互转换，则它们是相邻的。更正式地，定义两个关系实例${I}_{i}$和${I}_{i}^{\prime }$之间的距离为$d\left( {{I}_{i},{I}_{i}^{\prime }}\right)  \mathrel{\text{:=}} \left| {{I}_{i} - {I}_{i}^{\prime }}\right|  + \left| {{I}_{i}^{\prime } - {I}_{i}}\right|$，对于两个数据库实例$\mathbf{I}$和${\mathbf{I}}^{\prime },d\left( {\mathbf{I},{\mathbf{I}}^{\prime }}\right)  \mathrel{\text{:=}} \mathop{\sum }\limits_{{i \in  \left\lbrack  n\right\rbrack  }}d\left( {{I}_{i},{I}_{i}^{\prime }}\right)$。在动态环境中，我们有$d\left( {\mathbf{I},{\mathbf{I}}^{\prime }}\right)  \mathrel{\text{:=}} \mathop{\sum }\limits_{t}\left( {\left| {\left\{  {e}^{\left( t\right) }\right\}   - \left\{  {e}^{\prime \left( t\right) }\right\}  }\right|  + \left| {\left\{  {e}^{\prime \left( t\right) }\right\}   - \left\{  {e}^{\left( t\right) }\right\}  }\right| }\right)$（设$\{  \bot  \}  = \varnothing$），即它是将一个元组流转换为另一个所需的最小插入/删除次数。对于这两种情况，若$d\left( {\mathbf{I},{\mathbf{I}}^{\prime }}\right)  = 1$，则I和${\mathbf{I}}^{\prime }$是相邻实例。

Note that,in the dynamic setting,the output of $\mathcal{M}$ consists of all the (possibly infinitely many) privatized query answers $\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)$ ,and they must jointly satisfy Definition 3.2.

注意，在动态环境中，$\mathcal{M}$的输出包括所有（可能无限多的）私有化查询结果$\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)$，它们必须共同满足定义3.2。

The following essential properties of DP will be useful:

差分隐私的以下基本性质将非常有用：

Lemma 3.3 (Post Processing [26]). If $\mathcal{M} : \mathcal{I} \rightarrow  \mathcal{Y}$ satisfies $\varepsilon$ -DP and ${\mathcal{M}}^{\prime } : \mathcal{Y} \rightarrow  \mathcal{Z}$ is any randomized mechanism,then ${\mathcal{M}}^{\prime }\left( {\mathcal{M}\left( \mathrm{I}\right) }\right)$ satisfies $\varepsilon$ -DP.

引理3.3（后处理[26]）。若$\mathcal{M} : \mathcal{I} \rightarrow  \mathcal{Y}$满足$\varepsilon$-差分隐私且${\mathcal{M}}^{\prime } : \mathcal{Y} \rightarrow  \mathcal{Z}$为任意随机化机制，则${\mathcal{M}}^{\prime }\left( {\mathcal{M}\left( \mathrm{I}\right) }\right)$满足$\varepsilon$-差分隐私。

LEMMA 3.4 (BASIC COMPOSITION THEOREM [26]). If $\mathcal{M}$ is an adaptive composition of differentially private mechanisms ${\mathcal{M}}_{1},\ldots ,{\mathcal{M}}_{k}$ ,where each ${\mathcal{M}}_{i}$ satisfies $\varepsilon$ -DP,then $\mathcal{M}$ satisfies $\left( {k\varepsilon }\right)$ -DP.

引理3.4（基本组合定理[26]）。若$\mathcal{M}$是差分隐私机制${\mathcal{M}}_{1},\ldots ,{\mathcal{M}}_{k}$的自适应组合，其中每个${\mathcal{M}}_{i}$满足$\varepsilon$-差分隐私，则$\mathcal{M}$满足$\left( {k\varepsilon }\right)$-差分隐私。

<!-- Media -->

Algorithm 1: SVT.

算法1：稀疏向量技术(SVT)。

---

Input: $\eta ,\varepsilon ,{f}_{1}\left( \mathbf{I}\right) ,{f}_{2}\left( \mathbf{I}\right) ,\ldots$

输入：$\eta ,\varepsilon ,{f}_{1}\left( \mathbf{I}\right) ,{f}_{2}\left( \mathbf{I}\right) ,\ldots$

$\widetilde{\eta } \leftarrow  \eta  + \operatorname{Lap}\left( {2/\varepsilon }\right)$ ;

for $k \leftarrow  1,2,\ldots$ do

循环：$k \leftarrow  1,2,\ldots$

	${\widetilde{f}}_{k}\left( \mathrm{I}\right)  \leftarrow  {f}_{k}\left( \mathrm{I}\right)  + \operatorname{Lap}\left( {4/\varepsilon }\right) ;$

	if ${\widetilde{f}}_{k}\left( \mathrm{I}\right)  > \widetilde{\eta }$ then

	若${\widetilde{f}}_{k}\left( \mathrm{I}\right)  > \widetilde{\eta }$成立

		Break;

		终止循环

	end

	结束

end

结束

return $k$ ;

返回$k$

---

<!-- Media -->

LEMMA 3.5 (GROUP PRIVACY [26]). If $\mathcal{M}$ is an $\varepsilon$ -DP mechanism when neighboring instances are those with distance 1,then $\mathcal{M}$ satisfies $\left( {\lambda \varepsilon }\right)$ -DP when neighboring instances are defined as those with distance less than or equal to $\lambda$ .

引理3.5（群体隐私[26]）。当相邻实例的距离为1时，若$\mathcal{M}$是$\varepsilon$-差分隐私机制，则当相邻实例定义为距离不超过$\lambda$时，$\mathcal{M}$满足$\left( {\lambda \varepsilon }\right)$-差分隐私。

### 3.3 DP Mechanisms in the Static Setting

### 3.3 静态场景下的差分隐私机制

In the static setting, the most commonly used DP mechanism is the Laplace Mechanism. We describe its $d$ -dimensional version here. Given any query $\mathbf{Q} : \mathcal{I} \rightarrow  {\mathbb{R}}^{d}$ ,its global sensitivity is defined as ${\mathrm{{GS}}}_{\mathrm{Q}} = \mathop{\max }\limits_{{\mathrm{I} \sim  {\mathrm{I}}^{\prime }}}{\begin{Vmatrix}\mathrm{Q}\left( \mathrm{I}\right)  - \mathrm{Q}\left( {\mathrm{I}}^{\prime }\right) \end{Vmatrix}}_{1}.$

静态场景中最常用的差分隐私机制是拉普拉斯机制。此处描述其$d$维版本：给定任意查询$\mathbf{Q} : \mathcal{I} \rightarrow  {\mathbb{R}}^{d}$，其全局敏感度定义为${\mathrm{{GS}}}_{\mathrm{Q}} = \mathop{\max }\limits_{{\mathrm{I} \sim  {\mathrm{I}}^{\prime }}}{\begin{Vmatrix}\mathrm{Q}\left( \mathrm{I}\right)  - \mathrm{Q}\left( {\mathrm{I}}^{\prime }\right) \end{Vmatrix}}_{1}.$

LEMMA 3.6 (LAPLACE MECHANISM). Given $\mathrm{Q} : \mathcal{I} \rightarrow  {\mathbb{R}}^{d}$ with global sensitivity ${\mathrm{{GS}}}_{\mathrm{Q}}$ ,the mechanism $\mathcal{M}\left( \mathrm{I}\right)  = \mathrm{Q}\left( \mathrm{I}\right)  + \gamma$ preserves $\varepsilon$ -DP,where $\gamma$ is a $d$ -dimensional vector where each entry is independently drawn from the Laplace distribution $\operatorname{Lap}\left( {{\mathrm{{GS}}}_{\mathrm{Q}}/\varepsilon }\right)$ .

引理3.6（拉普拉斯机制）。给定全局敏感度为${\mathrm{{GS}}}_{\mathrm{Q}}$的$\mathrm{Q} : \mathcal{I} \rightarrow  {\mathbb{R}}^{d}$，机制$\mathcal{M}\left( \mathrm{I}\right)  = \mathrm{Q}\left( \mathrm{I}\right)  + \gamma$可保持$\varepsilon$-差分隐私，其中$\gamma$是$d$维向量，每个元素独立取自拉普拉斯分布$\operatorname{Lap}\left( {{\mathrm{{GS}}}_{\mathrm{Q}}/\varepsilon }\right)$。

The utility analysis of the Laplace mechanism makes use of the following concentration property of the Laplace distribution:

拉普拉斯机制的效用分析利用了下述拉普拉斯分布的集中特性：

LEMMA 3.7 ([10]). Suppose ${\gamma }_{1},{\gamma }_{2},\ldots ,{\gamma }_{k}$ are independent random variables,where each ${\gamma }_{i} \sim  \operatorname{Lap}\left( {b}_{i}\right)$ .

引理3.7（[10]）。设${\gamma }_{1},{\gamma }_{2},\ldots ,{\gamma }_{k}$为独立随机变量，其中每个${\gamma }_{i} \sim  \operatorname{Lap}\left( {b}_{i}\right)$。

Then for any $\beta  > 0$ ,

那么对于任意$\beta  > 0$，

$$
\Pr \left\lbrack  {\left| {\mathop{\sum }\limits_{i}{\gamma }_{i}}\right|  \geq  \sqrt{8\mathop{\sum }\limits_{i}{b}_{i}^{2}} \cdot  \log \frac{2}{\beta }}\right\rbrack   \leq  \beta .
$$

Another useful tool is the Sparse Vector Technique (SVT) [25]. Given a threshold $\eta$ and a (possibly infinite) sequence of 1-dimensional queries, ${f}_{1},{f}_{2},\ldots$ ,where each has global sensitivity 1,SVT (described in Algorithm 1) returns the first $k$ such that ${f}_{k}\left( \mathrm{I}\right)  \geq  \eta$ . Due to the noise,SVT cannot return such an $k$ exactly,but somewhere not too faraway. The formal utility guarantee of SVT is as follows.

另一个实用工具是稀疏向量技术(Sparse Vector Technique, SVT)[25]。给定阈值$\eta$和一个(可能无限的)一维查询序列${f}_{1},{f}_{2},\ldots$（每个查询的全局敏感度为1），SVT（如算法1所述）会返回首个满足${f}_{k}\left( \mathrm{I}\right)  \geq  \eta$的$k$。由于噪声干扰，SVT无法精确返回这样的$k$，但会在不远的位置返回结果。SVT的正式效用保证如下。

LEMMA 3.8 ([17]). Given any $\varepsilon  > 0$ ,SVT satisfies $\varepsilon$ -DP and returns a $k$ such that with probability at least $1 - \beta ,{f}_{k}\left( \mathbf{I}\right)  > \eta  - \frac{6}{\varepsilon }\log \left( {2/\beta }\right)  - \frac{8}{\varepsilon }\log \left( {k + 1}\right)$ and ${f}_{{k}^{\prime }}\left( \mathbf{I}\right)  < \eta  + \frac{8}{\varepsilon }\log \left( {2/\beta }\right)  + \frac{6}{\varepsilon }\log \left( {{k}^{\prime } + 1}\right)$ for all ${k}^{\prime } < k$ .

引理3.8([17])。给定任意$\varepsilon  > 0$，SVT满足$\varepsilon$-差分隐私(DP)，并以至少$1 - \beta ,{f}_{k}\left( \mathbf{I}\right)  > \eta  - \frac{6}{\varepsilon }\log \left( {2/\beta }\right)  - \frac{8}{\varepsilon }\log \left( {k + 1}\right)$的概率返回$k$，且对所有${k}^{\prime } < k$满足${f}_{{k}^{\prime }}\left( \mathbf{I}\right)  < \eta  + \frac{8}{\varepsilon }\log \left( {2/\beta }\right)  + \frac{6}{\varepsilon }\log \left( {{k}^{\prime } + 1}\right)$。

### 3.4 Binary Mechanism

### 3.4 二进制机制

A basic DP mechanism in the dynamic setting is the Binary Mechanism (BM) [10, 23]. Assume for now that the stream has a finite length $T$ ,which is a power of 2 . The idea is to build a binary hierarchical decomposition of the time domain $\left\lbrack  {1,T}\right\rbrack$ that consists of $\log T + 1$ levels. On level $\ell  = 0,1,\ldots ,\log T$ ,the time domain is partitioned into $T/{2}^{\ell }$ canonical intervals,each of length ${2}^{\ell }$ : $\left\lbrack  {1,{2}^{\ell }}\right\rbrack  ,\left\lbrack  {{2}^{\ell } + 1,2 \cdot  {2}^{\ell }}\right\rbrack  ,\ldots ,\left\lbrack  {T - {2}^{\ell } + 1,T}\right\rbrack$ . Let $\mathcal{T}$ be the set of all such canonical intervals. It is clear that $\left| \mathcal{T}\right|  = {2T} - 1$ and any interval $\left\lbrack  {1,t}\right\rbrack$ is the disjoint union of $\log t$ canonical intervals. We use BinaryForm $\left( \left\lbrack  {1,t}\right\rbrack  \right)$ to denote this set of canonical intervals that make up $\left\lbrack  {1,t}\right\rbrack$ . Thus,any query on ${\mathbf{I}}^{\left( t\right) }$ can be answered by just adding up the query results on these canonical intervals.

动态设置中的基础差分隐私机制是二进制机制(Binary Mechanism, BM)[10,23]。假设当前数据流有限长度为$T$（2的幂次）。该机制通过构建时间域$\left\lbrack  {1,T}\right\rbrack$的二进制层次分解结构，包含$\log T + 1$个层级。在第$\ell  = 0,1,\ldots ,\log T$层级，时间域被划分为$T/{2}^{\ell }$个规范区间，每个区间长度为${2}^{\ell }$：$\left\lbrack  {1,{2}^{\ell }}\right\rbrack  ,\left\lbrack  {{2}^{\ell } + 1,2 \cdot  {2}^{\ell }}\right\rbrack  ,\ldots ,\left\lbrack  {T - {2}^{\ell } + 1,T}\right\rbrack$。设$\mathcal{T}$为所有此类规范区间的集合。显然$\left| \mathcal{T}\right|  = {2T} - 1$，且任意区间$\left\lbrack  {1,t}\right\rbrack$都是$\log t$个规范区间的不相交并集。使用BinaryForm$\left( \left\lbrack  {1,t}\right\rbrack  \right)$表示构成$\left\lbrack  {1,t}\right\rbrack$的规范区间集合。因此，对${\mathbf{I}}^{\left( t\right) }$的任何查询只需累加这些规范区间的查询结果即可回答。

<!-- Media -->

<!-- figureText: Level 3: ${\varepsilon }_{3} = \frac{\theta \varepsilon }{{5}^{1 + \theta }}$ $\Delta {Q}_{\Delta }^{\left\lbrack  1,8\right\rbrack  }\left( 1\right)$ $\Delta {Q}_{\Delta }^{\left\lbrack  5,8\right\rbrack  }\left( 1\right)$ $\underline{\Delta }{Q}_{\Delta }^{\left\lbrack  7,8\right\rbrack  }\left( \mathbf{I}\right)$ $\Delta {Q}_{\Delta }^{\left\lbrack  9,{10}\right\rbrack  }\left( \mathbf{I}\right)$ ${\mathrm{R}}_{2} : \left( {{b}_{1},{c}_{2}}\right)$ ${\mathrm{R}}_{3} : \left( {{c}_{1},{a}_{2}}\right)$ ${\mathrm{R}}_{1} : \left( {{a}_{1},{b}_{3}}\right)$ ${\mathrm{R}}_{2} : \left( {{b}_{2},{c}_{1}}\right)$ ${\mathrm{R}}_{3} : \left( {{c}_{1},{a}_{3}}\right)$ Time 6 Time 7 Time 8 Time 9 Time 10 $\Delta {Q}_{\Delta }^{\left\lbrack  9,{10}\right\rbrack  }\left( \mathbf{I}\right)  = {Q}_{\Delta }\left( {\mathbf{I}}^{\left( {10}\right) }\right)  - {Q}_{\Delta }\left( {\mathbf{I}}^{\left( 8\right) }\right)  = 1$ ${\widetilde{\Delta Q}}_{\Delta }^{\left\lbrack  9,{10}\right\rbrack  }\left( \mathbf{I}\right)  = \Delta {Q}_{\Delta }^{\left\lbrack  9,{10}\right\rbrack  }\left( \mathbf{I}\right)  + \operatorname{Lap}\left( {{3}^{1 + \theta } \cdot  {\mathrm{{GS}}}_{\Delta \mathbf{Q}}/\left( {\varepsilon \theta }\right) }\right)$ $\Delta {Q}_{\Delta }^{\left\lbrack  1,4\right\rbrack  }\left( \mathbf{I}\right)$ Level 2: ${\varepsilon }_{2} = \frac{\theta \varepsilon }{{4}^{1 + \theta }}$ $\Delta {Q}_{\Delta }^{\left\lbrack  1,2\right\rbrack  }\left( \mathbf{I}\right)$ $\Delta {Q}_{\Delta }^{\left\lbrack  3,4\right\rbrack  }\left( \mathbf{I}\right)$ $\Delta {Q}_{\Delta }^{\left\lbrack  5,6\right\rbrack  }\left( \mathbf{I}\right)$ Level 1: ${\varepsilon }_{1} = \frac{\theta \varepsilon }{{3}^{1 + \theta }}$ Level 0: ${\varepsilon }_{0} = \frac{\theta \varepsilon }{{2}^{1 + \theta }}$ ${\mathrm{R}}_{1} : \left( {{a}_{1},{b}_{1}}\right)$ ${\mathrm{R}}_{2} : \left( {{b}_{1},{c}_{1}}\right)$ ${\mathrm{R}}_{3} : \left( {{c}_{1},{a}_{1}}\right)$ NULL: $\bot$ ${\mathrm{R}}_{1} : \left( {{a}_{1},{b}_{2}}\right.$ Time 1 Time 2 Time 3 Time 4 Time 5 ${\widetilde{Q}}_{\Delta }\left( {\mathbf{I}}^{\left( {14}\right) }\right)  = {\widetilde{\Delta Q}}_{\Delta }^{\left\lbrack  1,8\right\rbrack  }\left( \mathbf{I}\right)  + {\widetilde{\Delta Q}}_{\Delta }^{\left\lbrack  9,{10}\right\rbrack  }\left( \mathbf{I}\right)$ $\Delta {Q}_{\Delta }^{\left\lbrack  1,8\right\rbrack  }\left( \mathbf{I}\right)  = {Q}_{\Delta }\left( {\mathbf{I}}^{\left( 8\right) }\right)  - {Q}_{\Delta }\left( {\mathbf{I}}^{\left( 0\right) }\right)  = 1$ ${\widetilde{\Delta Q}}_{\Delta }^{\left\lbrack  1,8\right\rbrack  }\left( \mathbf{I}\right)  = \Delta {Q}_{\Delta }^{\left\lbrack  1,8\right\rbrack  }\left( \mathbf{I}\right)  + \operatorname{Lap}\left( {{5}^{1 + \theta } \cdot  {\mathrm{{GS}}}_{\Delta \mathbf{Q}}/\left( {\varepsilon \theta }\right) }\right)$ -->

<img src="https://cdn.noedgeai.com/0196599c-190a-7a73-a499-f2871a0182b1_7.jpg?x=144&y=268&w=1273&h=455&r=0"/>

Fig. 2. A running example of the binary mechanism on ${Q}_{\nabla }\left( \mathbf{I}\right)  = \left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right)  \bowtie  {R}_{3}\left( {C,A}\right) }\right|$ .

图2. 二进制机制在${Q}_{\nabla }\left( \mathbf{I}\right)  = \left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right)  \bowtie  {R}_{3}\left( {C,A}\right) }\right|$上的运行示例。

Algorithm 2: The Binary Mechanism (BM).

算法2：二进制机制(BM)。

---

Input: $\mathbf{I} = \left( {{R}^{\left( 1\right) } : {e}^{\left( 1\right) },{R}^{\left( 2\right) } : {e}^{\left( 2\right) },\ldots }\right) ,\varepsilon ,{\mathrm{{GS}}}_{\Delta \mathbf{Q}},\theta$

输入：$\mathbf{I} = \left( {{R}^{\left( 1\right) } : {e}^{\left( 1\right) },{R}^{\left( 2\right) } : {e}^{\left( 2\right) },\ldots }\right) ,\varepsilon ,{\mathrm{{GS}}}_{\Delta \mathbf{Q}},\theta$

for $t \leftarrow  1,2,\ldots$ do

循环开始 $t \leftarrow  1,2,\ldots$ 执行

	foreach canonical interval $\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack$ such that ${t}_{2} = t$ do

	对于每个规范区间$\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack$，当满足${t}_{2} = t$时执行

		${\widetilde{\Delta Q}}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \mathbf{I}\right)  \leftarrow  \Delta {Q}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \mathbf{I}\right)$

		$+ \operatorname{Lap}\left( {{\left( \log \left( {t}_{2} - {t}_{1} + 1\right)  + 2\right) }^{1 + \theta } \cdot  {\mathrm{{GS}}}_{\Delta \mathrm{Q}}/\left( {\varepsilon \theta }\right) }\right)$ ;

	end

	结束

	$\mathcal{S} \leftarrow$ BinaryForm $\left( \left\lbrack  {1,t}\right\rbrack  \right)$ ;

	$\mathcal{S} \leftarrow$的二进制形式$\left( \left\lbrack  {1,t}\right\rbrack  \right)$；

	$\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)  \leftarrow  \mathop{\sum }\limits_{{\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack   \in  \mathcal{S}}}{\widetilde{\Delta Q}}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \mathbf{I}\right) ;$

end

结束

---

<!-- Media -->

Next,we release the privatized results of $Q\left( {\mathbf{I}}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\right)$ for all the canonical intervals $\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack   \in  \mathcal{T}$ . The observation is that the queries for the canonical intervals on the same level can be regarded as a high-dimensional query. Specifically,the query on level $\ell$ is

接下来，我们发布所有规范区间$\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack   \in  \mathcal{T}$上$Q\left( {\mathbf{I}}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\right)$的私有化结果。观察到同一层级上的规范区间查询可视为高维查询，具体而言，层级$\ell$的查询式为

$$
{Q}^{\left( \ell \right) }\left( \mathbf{I}\right)  \mathrel{\text{:=}} \left( {Q\left( {\mathbf{I}}^{\left\lbrack  1,{2}^{\ell }\right\rbrack  }\right) ,Q\left( {\mathbf{I}}^{\left\lbrack  {2}^{\ell } + 1,2 \cdot  {2}^{\ell }\right\rbrack  }\right) ,\ldots ,Q\left( {\mathbf{I}}^{\left\lbrack  T - {2}^{\ell } + 1,T\right\rbrack  }\right) }\right) . \tag{2}
$$

For the simple counting query, ${\mathrm{Q}}^{\left( \ell \right) }$ has global sensitivity 1,since adding a tuple at any time step adds 1 to only one canonical interval on each level. Therefore, we can divide the privacy budget by $\log T + 1$ via basic composition and then apply the Laplace mechanism on each level,i.e.,masking each $Q\left( {\mathbf{I}}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\right)$ for $\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack   \in  \mathcal{T}$ with Laplace noise of scale $\left( {\log T + 1}\right) /\varepsilon$ . Then by Lemma 3.7,the total noise for answering any $Q\left( {\mathbf{I}}^{\left( t\right) }\right)$ is $O\left( {{\log }^{1.5}T/\varepsilon }\right)$ with constant probability.

对于简单计数查询，${\mathrm{Q}}^{\left( \ell \right) }$的全局敏感度为1，因为在任何时间步添加元组仅会使每个层级的单个规范区间计数值增加1。因此，可通过基本组合将隐私预算分配$\log T + 1$份，再对每个层级应用拉普拉斯机制——即用尺度参数为$\left( {\log T + 1}\right) /\varepsilon$的拉普拉斯噪声掩盖$\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack   \in  \mathcal{T}$对应的$Q\left( {\mathbf{I}}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\right)$。根据引理3.7，回答任意查询$Q\left( {\mathbf{I}}^{\left( t\right) }\right)$的总噪声量$O\left( {{\log }^{1.5}T/\varepsilon }\right)$在恒定概率下成立。

The aforementioned idea works for any decomposable query $Q$ ,i.e., $Q\left( {\mathbf{I}}^{\left\lbrack  {t}_{1},{t}_{3}\right\rbrack  }\right)  = Q\left( {\mathbf{I}}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\right)  +$ $Q\left( {\mathbf{I}}^{\left\lbrack  {t}_{2} + 1,{t}_{3}\right\rbrack  }\right)$ for any ${t}_{1} \leq  {t}_{2} < {t}_{3}$ . However,join queries are not decomposable. To support such queries,the idea to consider delta queries [28]: $\Delta {Q}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \mathrm{I}\right)  \mathrel{\text{:=}} Q\left( {\mathrm{I}}^{\left( {t}_{2}\right) }\right)  - Q\left( {\mathrm{I}}^{\left( {t}_{1} - 1\right) }\right)$ . This way,any $Q\left( {\mathbf{I}}^{\left( t\right) }\right)$ is still the sum of $O\left( {\log t}\right)$ delta queries and the binary mechanism still works,except that the high-dimensional query on each level are now formed by the delta queries on this level, i.e., (2)

上述方案适用于任何可分解查询$Q$，即对任意${t}_{1} \leq  {t}_{2} < {t}_{3}$满足$Q\left( {\mathbf{I}}^{\left\lbrack  {t}_{1},{t}_{3}\right\rbrack  }\right)  = Q\left( {\mathbf{I}}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\right)  +$$Q\left( {\mathbf{I}}^{\left\lbrack  {t}_{2} + 1,{t}_{3}\right\rbrack  }\right)$。然而连接查询不可分解。为支持此类查询，考虑采用增量查询[28]的方案：$\Delta {Q}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \mathrm{I}\right)  \mathrel{\text{:=}} Q\left( {\mathrm{I}}^{\left( {t}_{2}\right) }\right)  - Q\left( {\mathrm{I}}^{\left( {t}_{1} - 1\right) }\right)$。此时，任何$Q\left( {\mathbf{I}}^{\left( t\right) }\right)$仍是$O\left( {\log t}\right)$个增量查询之和，二进制机制依然适用，只是每层级的高维查询现由该层级的增量查询构成，即式(2)

becomes

变为

$$
\Delta {\mathbf{Q}}^{\left( \ell \right) }\left( \mathbf{I}\right)  \mathrel{\text{:=}} \left( {\Delta {Q}^{\left\lbrack  1,{2}^{\ell }\right\rbrack  }\left( \mathbf{I}\right) ,\Delta {Q}^{\left\lbrack  {2}^{\ell } + 1,2 \cdot  {2}^{\ell }\right\rbrack  }\left( \mathbf{I}\right) ,\ldots ,\Delta {Q}^{\left\lbrack  T - {2}^{\ell } + 1,T\right\rbrack  }\left( \mathbf{I}\right) }\right) . \tag{3}
$$

Since $\Delta {\mathrm{Q}}^{\left( \ell \right) }\left( \mathrm{I}\right)$ exhibits the highest sensitivity at level 0,the only remaining issue is to bound ${\mathrm{{GS}}}_{\Delta {\mathbf{Q}}^{\left( 0\right) }}$ ,the global sensitivity of $\Delta {\mathbf{Q}}^{\left( 0\right) }\left( \mathbf{I}\right)$ . For notational simplicity,we just write it as ${\mathrm{{GS}}}_{\Delta \mathbf{Q}}$ . Note that for the simple counting query, $Q\left( {\mathbf{I}}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\right)$ and $\Delta {Q}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \mathbf{I}\right)$ are identical,so ${\mathrm{{GS}}}_{\Delta \mathrm{Q}} = 1$ . However, this might not be the case for other queries. [28] has derived ${\mathrm{{GS}}}_{\Delta \mathrm{Q}}$ for some specific graph pattern counting queries; later, we will prove a more general result that holds for all monotonic and supermodular queries, which include all join and graph pattern counting queries as special cases.

由于$\Delta {\mathrm{Q}}^{\left( \ell \right) }\left( \mathrm{I}\right)$在0级表现出最高灵敏度，唯一剩下的问题就是界定${\mathrm{{GS}}}_{\Delta {\mathbf{Q}}^{\left( 0\right) }}$——即$\Delta {\mathbf{Q}}^{\left( 0\right) }\left( \mathbf{I}\right)$的全局灵敏度。为简化表示，我们将其记为${\mathrm{{GS}}}_{\Delta \mathbf{Q}}$。需注意，对于简单计数查询而言，$Q\left( {\mathbf{I}}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\right)$与$\Delta {Q}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \mathbf{I}\right)$是等同的，因此${\mathrm{{GS}}}_{\Delta \mathrm{Q}} = 1$。但其他查询可能并非如此。[28]已推导出特定图模式计数查询的${\mathrm{{GS}}}_{\Delta \mathrm{Q}}$；后文我们将证明一个更普适的结论，该结论适用于所有单调且超模的查询（包括所有连接查询和图模式计数查询作为特例）。

To accommodate an infinite stream,we build $\mathcal{T}$ incrementally,i.e.,it only includes all canonical intervals $\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack$ for ${t}_{2} \leq  t$ ,where $t$ is the current time. Then instead of allocating the privacy budget equally to all levels, a telescoping strategy [10] is adopted, where the privacy budget for the $i$ -th level is ${\varepsilon }_{i} \mathrel{\text{:=}} \frac{\varepsilon \theta }{{\left( i + 2\right) }^{1 + \theta }}$ ,where $\theta  > 0$ is any small constant. The detailed algorithm is shown in Algorithm 2 and a running example is given in Figure 2.

为适应无限流，我们采用增量方式构建$\mathcal{T}$，即仅包含当前时间$t$之前所有${t}_{2} \leq  t$的规范区间$\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack$。随后采用伸缩策略[10]替代均分隐私预算的方案：第$i$层级的隐私预算为${\varepsilon }_{i} \mathrel{\text{:=}} \frac{\varepsilon \theta }{{\left( i + 2\right) }^{1 + \theta }}$，其中$\theta  > 0$为任意小常数。具体算法如算法2所示，图2给出了运行实例。

BM has the following utility guarantee:

BM具有如下效用保证：

LEMMA 3.9 ([10]). Given any $\varepsilon ,\theta  > 0$ ,BM satisfies $\varepsilon$ -DP,and for any $\beta  > 0$ and any $t \in  \mathbb{N}$ ,with probability at least $1 - \beta$ ,it returns a $\widetilde{Q}\left( {\mathbf{I}}_{t}\right)$ such that

引理3.9（[10]）。给定任意$\varepsilon ,\theta  > 0$，BM满足$\varepsilon$-差分隐私，且对于任意$\beta  > 0$与任意$t \in  \mathbb{N}$，以至少$1 - \beta$的概率返回满足以下条件的$\widetilde{Q}\left( {\mathbf{I}}_{t}\right)$：

$$
\left| {\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)  - Q\left( {\mathbf{I}}^{\left( t\right) }\right) }\right|  = O\left( {\frac{1}{\varepsilon \theta } \cdot  {\mathrm{{GS}}}_{\Delta \mathrm{Q}} \cdot  {\log }^{{1.5} + \theta }t \cdot  \log \left( {1/\beta }\right) }\right) .
$$

It is worth mentioning that [10] has proposed another technique to handle an infinite stream without incurring the extra $\theta$ term in utility. However,that solution only works for decomposable queries.

值得一提的是，[10]提出了另一种处理无限流的技术，可避免在效用项中引入额外的$\theta$。但该方案仅适用于可分解查询。

<!-- Media -->

<table><tr><td>Notation</td><td>Meaning</td></tr><tr><td>R</td><td>Database schema</td></tr><tr><td>${R}_{1},\ldots ,{R}_{n}$</td><td>Relation names</td></tr><tr><td>$\mathbf{I},{\mathbf{I}}^{\prime }$</td><td>Database instances</td></tr><tr><td>${e}^{\left( t\right) }$</td><td>Tuple coming at time $t$</td></tr><tr><td>${I}_{1},\ldots ,{I}_{n}$</td><td>Relation instances</td></tr><tr><td>${\mathbf{I}}^{\left( t\right) }$</td><td>Prefix database instance I of time $t$</td></tr><tr><td>${I}_{i}^{\left( t\right) }$</td><td>Prefix relation instance ${I}_{i}$ of time $t$</td></tr><tr><td>$\operatorname{mf}\left( {{I}_{i},{\mathbf{x}}^{\prime }}\right)$</td><td>Max-frequency of ${\mathbf{x}}^{\prime }$ in instance ${I}_{i}$</td></tr><tr><td>$\operatorname{mf}\left( {{R}_{i},{\mathbf{x}}^{\prime }}\right)$</td><td>An predefined upper bound of $\operatorname{mf}\left( {{I}_{i},{\mathbf{x}}^{\prime }}\right)$</td></tr><tr><td>${\mathcal{B}}_{Q,i}$</td><td>Set of boundaries of ${R}_{i}$</td></tr><tr><td>${m}_{i}$</td><td>${m}_{i} = \left| {\mathcal{B}}_{Q,i}\right|$</td></tr><tr><td>${m}_{\max }$</td><td>${m}_{\max } = \mathop{\max }\limits_{i}{m}_{i}$</td></tr><tr><td>${\mathcal{B}}_{O}$</td><td>${\mathcal{B}}_{Q} = \left\{  {\left( {i,\mathbf{x}}\right)  : \mathbf{x} \in  {\mathcal{B}}_{Q,i}}\right\}$</td></tr><tr><td>$\Delta {Q}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \mathbf{I}\right)$</td><td>Change of $Q$ between time interval $\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack$</td></tr><tr><td>${\mathrm{{GS}}}_{Q}$</td><td>Global sensitivity of $Q$</td></tr><tr><td>${\mathrm{{GS}}}_{\Delta \mathrm{Q}}$</td><td>GS of $\left\{  {\Delta {Q}^{\left\lbrack  1,1\right\rbrack  }\left( \mathbf{I}\right) ,\Delta {Q}^{\left\lbrack  2,2\right\rbrack  }\left( \mathbf{I}\right) ,\ldots }\right\}$</td></tr></table>

<table><tbody><tr><td>符号表示</td><td>含义</td></tr><tr><td>R</td><td>数据库模式</td></tr><tr><td>${R}_{1},\ldots ,{R}_{n}$</td><td>关系名称</td></tr><tr><td>$\mathbf{I},{\mathbf{I}}^{\prime }$</td><td>数据库实例</td></tr><tr><td>${e}^{\left( t\right) }$</td><td>在时间$t$到达的元组</td></tr><tr><td>${I}_{1},\ldots ,{I}_{n}$</td><td>关系实例</td></tr><tr><td>${\mathbf{I}}^{\left( t\right) }$</td><td>时间$t$的前缀数据库实例I</td></tr><tr><td>${I}_{i}^{\left( t\right) }$</td><td>时间$t$的前缀关系实例${I}_{i}$</td></tr><tr><td>$\operatorname{mf}\left( {{I}_{i},{\mathbf{x}}^{\prime }}\right)$</td><td>实例${I}_{i}$中${\mathbf{x}}^{\prime }$的最大频率</td></tr><tr><td>$\operatorname{mf}\left( {{R}_{i},{\mathbf{x}}^{\prime }}\right)$</td><td>$\operatorname{mf}\left( {{I}_{i},{\mathbf{x}}^{\prime }}\right)$的预定义上界</td></tr><tr><td>${\mathcal{B}}_{Q,i}$</td><td>${R}_{i}$的边界集合</td></tr><tr><td>${m}_{i}$</td><td>${m}_{i} = \left| {\mathcal{B}}_{Q,i}\right|$</td></tr><tr><td>${m}_{\max }$</td><td>${m}_{\max } = \mathop{\max }\limits_{i}{m}_{i}$</td></tr><tr><td>${\mathcal{B}}_{O}$</td><td>${\mathcal{B}}_{Q} = \left\{  {\left( {i,\mathbf{x}}\right)  : \mathbf{x} \in  {\mathcal{B}}_{Q,i}}\right\}$</td></tr><tr><td>$\Delta {Q}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \mathbf{I}\right)$</td><td>时间区间$\left\lbrack  {{t}_{1},{t}_{2}}\right\rbrack$内$Q$的变化量</td></tr><tr><td>${\mathrm{{GS}}}_{Q}$</td><td>$Q$的全局敏感度</td></tr><tr><td>${\mathrm{{GS}}}_{\Delta \mathrm{Q}}$</td><td>$\left\{  {\Delta {Q}^{\left\lbrack  1,1\right\rbrack  }\left( \mathbf{I}\right) ,\Delta {Q}^{\left\lbrack  2,2\right\rbrack  }\left( \mathbf{I}\right) ,\ldots }\right\}$的GS值</td></tr></tbody></table>

Table 2. Notation used in the paper.

表2. 本文使用的符号说明。

<!-- Media -->

## 4 THE GLOBAL SENSITIVITY OF JOINS

## 4 连接操作的全局敏感度

As seen above,to use the $\mathrm{{BM}}$ on any query $Q$ ,it boils down to bounding ${\mathrm{{GS}}}_{\Delta Q}$ ,which can be further shown to depend on ${\mathrm{{GS}}}_{Q}$ ,the global sensitivity of $Q$ in the static setting. Below,we first review and clarify existing work in the static setting, and then extend these results to the dynamic setting.

如前所述，要在任意查询$Q$上应用$\mathrm{{BM}}$，关键在于界定${\mathrm{{GS}}}_{\Delta Q}$的上限，这又可进一步转化为静态场景下$Q$的全局敏感度${\mathrm{{GS}}}_{Q}$的问题。下文将首先回顾并厘清静态场景下的现有研究成果，继而将这些结论推广至动态场景。

### 4.1 The Static Setting

### 4.1 静态场景

As mentioned, the global sensitivity of joins is unbounded. Thus, all prior work has restricted the allowable instances with frequency constraints. Specifically,for every $i \in  \left\lbrack  n\right\rbrack$ and every subset of variables $\mathbf{x} \subseteq  {\mathbf{x}}_{i}$ ,a frequency upper bound $\widehat{\operatorname{mf}}\left( {{R}_{i},\mathbf{x}}\right)$ should be set a priori,and all allowable instances $\mathbf{I} = {\left\{  {I}_{i}\right\}  }_{i \in  \left\lbrack  n\right\rbrack  }$ must satisfy $\operatorname{mf}\left( {{I}_{i},\mathbf{x}}\right)  \leq  \widehat{\operatorname{mf}}\left( {{R}_{i},\mathbf{x}}\right)$ for all $i,\mathbf{x}$ . Then, ${\mathrm{{GS}}}_{Q}$ can be bounded in terms of these frequency constraints. For example, $\left\lbrack  {{32},{36}}\right\rbrack$ have derived the following bound on ${\mathrm{{GS}}}_{{Q}_{\nabla }}$ for the triangle query:

如前所述，连接操作的全局敏感度无界。因此所有先前研究都通过频率约束来限定允许的实例。具体而言，对于每个$i \in  \left\lbrack  n\right\rbrack$和变量子集$\mathbf{x} \subseteq  {\mathbf{x}}_{i}$，需预先设定频率上限$\widehat{\operatorname{mf}}\left( {{R}_{i},\mathbf{x}}\right)$，且所有允许的实例$\mathbf{I} = {\left\{  {I}_{i}\right\}  }_{i \in  \left\lbrack  n\right\rbrack  }$必须满足$\operatorname{mf}\left( {{I}_{i},\mathbf{x}}\right)  \leq  \widehat{\operatorname{mf}}\left( {{R}_{i},\mathbf{x}}\right)$对所有$i,\mathbf{x}$成立。此时${\mathrm{{GS}}}_{Q}$便可根据这些频率约束确定上限。例如$\left\lbrack  {{32},{36}}\right\rbrack$针对三角形查询推导出如下${\mathrm{{GS}}}_{{Q}_{\nabla }}$的上限：

$$
{\mathrm{{GS}}}_{{Q}_{\nabla }} \leq  \max \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{2},B}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{3},A}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{1},B}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{3},C}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{2},C}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{1},A}\right) }\right) . \tag{4}
$$

We observe that this bound is far from tight, and can improve it by borrowing two ideas from the literature. First,[20] shows that ${\mathrm{{GS}}}_{Q}$ can be computed from the join size upper bounds of several sub-queries. Consider again the triangle query. Since ${\mathrm{{GS}}}_{Q}$ is the maximum amount of change in $Q$ if one tuple is added/removed from ${R}_{1},{R}_{2}$ ,or ${R}_{3}$ . These changes are precisely captured by the following sub-queries:

我们发现该上限远非紧约束，可通过借鉴文献中的两个思路进行改进。首先，文献[20]证明${\mathrm{{GS}}}_{Q}$可通过若干子查询的规模上限计算得出。仍以三角形查询为例，由于${\mathrm{{GS}}}_{Q}$表示在${R}_{1},{R}_{2}$或${R}_{3}$中增删元组时$Q$的最大变化量，这些变化恰由以下子查询刻画：

$$
{Q}_{\nabla ,1} \mathrel{\text{:=}} \left| {\left( {a,b}\right)  \bowtie  {R}_{2}\left( {b,C}\right)  \bowtie  {R}_{3}\left( {C,a}\right) }\right| ,
$$

$$
{Q}_{\nabla ,2} \mathrel{\text{:=}} \left| {{R}_{1}\left( {A,b}\right)  \bowtie  \left( {b,c}\right)  \bowtie  {R}_{3}\left( {c,A}\right) }\right| ,
$$

$$
{Q}_{\nabla ,3} \mathrel{\text{:=}} \left| {{R}_{1}\left( {a,B}\right)  \bowtie  {R}_{2}\left( {B,c}\right)  \bowtie  {R}_{3}\left( {c,a}\right) }\right| .
$$

Subsequently,we have ${\mathrm{{GS}}}_{{Q}_{\nabla }} \leq  \mathop{\max }\limits_{\mathbf{I}}\max \left\{  {{Q}_{\nabla ,1}\left( \mathbf{I}\right) ,{Q}_{\nabla ,2}\left( \mathbf{I}\right) ,{Q}_{\nabla ,3}\left( \mathbf{I}\right) }\right\}$ .

由此可得${\mathrm{{GS}}}_{{Q}_{\nabla }} \leq  \mathop{\max }\limits_{\mathbf{I}}\max \left\{  {{Q}_{\nabla ,1}\left( \mathbf{I}\right) ,{Q}_{\nabla ,2}\left( \mathbf{I}\right) ,{Q}_{\nabla ,3}\left( \mathbf{I}\right) }\right\}$。

More generally,given a query $Q$ ,for each relation ${R}_{i}$ we define ${\mathcal{B}}_{Q,i}$ as the boundary of ${R}_{i}$ ,which comprises of the variables that ${R}_{i}$ shares with another relation ${R}_{j}$ for $j \in  \left\lbrack  n\right\rbrack$ ,i.e.,

更一般地，给定查询$Q$时，对每个关系${R}_{i}$我们定义${\mathcal{B}}_{Q,i}$作为${R}_{i}$的边界变量集，该集合由${R}_{i}$与其它关系${R}_{j}$（其中$j \in  \left\lbrack  n\right\rbrack$）的共享变量构成，即：

$$
{\mathcal{B}}_{Q,i} \mathrel{\text{:=}} \left\{  {{\mathbf{x}}_{i} \cap  {\mathbf{x}}_{j} : j \in  \left\lbrack  n\right\rbrack  ,j \neq  i,{\mathbf{x}}_{i} \cap  {\mathbf{x}}_{j} \neq  \varnothing }\right\}  .
$$

Let ${m}_{i} = \left| {\mathcal{B}}_{Q,i}\right| ,m = \mathop{\sum }\limits_{i}{m}_{i}$ ,and ${m}_{\max } = \mathop{\max }\limits_{i}{m}_{i}$ . Furthermore,define

设${m}_{i} = \left| {\mathcal{B}}_{Q,i}\right| ,m = \mathop{\sum }\limits_{i}{m}_{i}$，且${m}_{\max } = \mathop{\max }\limits_{i}{m}_{i}$。进一步定义

$$
{\mathcal{B}}_{Q} \mathrel{\text{:=}} \left\{  {\left( {i,\mathbf{x}}\right)  : i \in  \left\lbrack  n\right\rbrack  ,\mathbf{x} \in  {\mathcal{B}}_{Q,i}}\right\}  .
$$

For example,on the triangle query,we have ${\mathcal{B}}_{Q,1} = \{ \{ A\} ,\{ B\} \} ,{\mathcal{B}}_{Q,2} = \{ \{ B\} ,\{ C\} \} ,{\mathcal{B}}_{Q,3} =$ $\{ \{ C\} ,\{ A\} \} ,{\mathcal{B}}_{Q} = \{ \left( {1,\{ A\} }\right) ,\left( {1,\{ B\} }\right) ,\left( {2,\{ B\} }\right) ,\left( {2,\{ C\} }\right) ,\left( {3,\{ C\} }\right) ,\left( {3,\{ A\} }\right) \}$ ,and ${m}_{1} = {m}_{2} = {m}_{3} =$ $2,m = 6,{m}_{\max } = 2$ .

例如，在三角形查询中，我们有${\mathcal{B}}_{Q,1} = \{ \{ A\} ,\{ B\} \} ,{\mathcal{B}}_{Q,2} = \{ \{ B\} ,\{ C\} \} ,{\mathcal{B}}_{Q,3} =$$\{ \{ C\} ,\{ A\} \} ,{\mathcal{B}}_{Q} = \{ \left( {1,\{ A\} }\right) ,\left( {1,\{ B\} }\right) ,\left( {2,\{ B\} }\right) ,\left( {2,\{ C\} }\right) ,\left( {3,\{ C\} }\right) ,\left( {3,\{ A\} }\right) \}$，以及${m}_{1} = {m}_{2} = {m}_{3} =$$2,m = 6,{m}_{\max } = 2$。

Let ${Q}_{i}$ be the sub-query of $Q$ where all variables in ${\mathcal{B}}_{Q,i}$ are set to constants (like ${Q}_{\nabla ,1},{Q}_{\nabla ,2}$ ,and ${Q}_{\nabla ,3}$ shown above). Then we can bound ${\mathrm{{GS}}}_{Q}$ as

设${Q}_{i}$为$Q$的子查询，其中${\mathcal{B}}_{Q,i}$的所有变量均设为常量（如上述的${Q}_{\nabla ,1},{Q}_{\nabla ,2}$和${Q}_{\nabla ,3}$）。则可将${\mathrm{{GS}}}_{Q}$界定为

$$
{\mathrm{{GS}}}_{Q} \leq  \mathop{\max }\limits_{{i \in  \left\lbrack  n\right\rbrack  }}\mathop{\max }\limits_{\mathbf{I}}{Q}_{i}\left( \mathbf{I}\right) . \tag{5}
$$

It now remains to bound each $\mathop{\max }\limits_{\mathbf{I}}{Q}_{i}\left( \mathbf{I}\right)$ under the given frequency constraints ${\left\{  \widehat{\operatorname{mf}}\left( {R}_{i},\mathbf{x}\right) \right\}  }_{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}$ . We observe that this is precisely the problem studied in [2, 29], for which the best efficiently computable join size upper bound is the polymatroid bound. Plugging the polymatroid bound into (5) then yields a bound on ${\mathrm{{GS}}}_{Q}$ ,which is a function of ${\left\{  \widehat{\operatorname{mf}}\left( {R}_{i},\mathbf{x}\right) \right\}  }_{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}$ . The polymatroid bound in its full generality is complicated; instead,we have derived ${\mathrm{{GS}}}_{Q}\left( {\left\{  \overline{\mathrm{{mf}}\left( {{R}_{i},\mathbf{x}}\right) }\right\}  }_{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}\right)$ for several common queries in Table 3. These bounds are much tighter than the previous bounds [32, 36]. For example, for the triangle query and suppose all the frequency constraints are equal, then the bound in Table 3 is quadratically smaller than the previous bound in (4). In the sequel, we often omit the subscript $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$ for notational simplicity.

现在需要在给定频率约束${\left\{  \widehat{\operatorname{mf}}\left( {R}_{i},\mathbf{x}\right) \right\}  }_{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}$下界定每个$\mathop{\max }\limits_{\mathbf{I}}{Q}_{i}\left( \mathbf{I}\right)$。我们注意到这正是文献[2,29]研究的问题，其最优高效可计算连接大小上界为多拟阵界。将多拟阵界代入(5)式即可得到${\mathrm{{GS}}}_{Q}$的界——该界是${\left\{  \widehat{\operatorname{mf}}\left( {R}_{i},\mathbf{x}\right) \right\}  }_{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}$的函数。完整的多拟阵界较为复杂；为此我们推导出表3中几种常见查询的${\mathrm{{GS}}}_{Q}\left( {\left\{  \overline{\mathrm{{mf}}\left( {{R}_{i},\mathbf{x}}\right) }\right\}  }_{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}\right)$。这些界限较先前[32,36]的界限更为严格。例如对于三角形查询，假设所有频率约束相等，则表3中的界限较(4)式旧界缩小了二次方量级。下文为表述简洁常省略下标$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$。

### 4.2 The Dynamic Setting

### 4.2 动态场景

Moving forward to the dynamic setting, we need to bound the global sensitivity of the delta queries $\Delta \mathbf{Q}$ . Earlier work [28] has considered this problem for several specific graph pattern counting

转向动态场景时，需界定增量查询$\Delta \mathbf{Q}$的全局敏感度。先前研究[28]已针对几种特定图模式计数问题探讨过此问题

<!-- Media -->

<table><tr><td>Query</td><td>$\frac{{\operatorname{GS}}_{Q}\left( {\left\{  \widehat{\operatorname{mf}}\left( {R}_{i},\mathbf{x}\right) \right\}  }_{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}\right) }{\max \left( {\widehat{\operatorname{mf}}\left( {{R}_{1},B}\right) ,\widehat{\operatorname{mf}}\left( {{R}_{2},B}\right) }\right) }$</td></tr><tr><td>$\left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right) }\right|$</td><td/></tr><tr><td>$\left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right)  \bowtie  {R}_{3}\left( {C,D}\right) }\right|$</td><td>$\max \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{2},B}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{3},C}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{1},B}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{3},C}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{1},B}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{2},C}\right) }\right)$</td></tr><tr><td>$\left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right)  \bowtie  {R}_{3}\left( {C,A}\right) }\right|$</td><td>$\max \left( {\min \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{2},B}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{3},A}\right) }\right) ,\min \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{1},B}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{3},C}\right) }\right) }\right)$ , $\min \left( {\overset{⏜}{\mathrm{{mf}}\left( {{R}_{2},C}\right) },\overset{⏜}{\mathrm{{mf}}\left( {{R}_{1},A}\right) }}\right)$</td></tr><tr><td>${R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right)$ $\bowtie  {R}_{3}\left( {C,D}\right)  \bowtie  {R}_{4}\left( {D,A}\right)$</td><td>$\max \left( {\min \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{2},B}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{3},C}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{3},D}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{4},A}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{2},B}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{4},A}\right) }\right) }\right) ,$ $\min \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{3},C}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{4},D}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{1},B}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{4},A}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{3},C}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{1},B}\right) }\right) ,$ $\min \left( {\mathrm{{mf}}\left( {{R}_{4},D}\right)  \cdot  \mathrm{{mf}}\left( {{R}_{1},A}\right) ,\mathrm{{mf}}\left( {{R}_{2},C}\right)  \cdot  \mathrm{{mf}}\left( {{R}_{1},B}\right) ,\mathrm{{mf}}\left( {{R}_{4},D}\right)  \cdot  \mathrm{{mf}}\left( {{R}_{2},C}\right) }\right) ,$ $\min \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{1},A}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{2},C}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{3},D}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{2},C}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{3},D}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{1},A}\right) }\right)$</td></tr><tr><td>$\left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {A,C}\right)  \bowtie  {R}_{3}\left( {A,D}\right) }\right|$</td><td>$\max \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{2},A}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{3},A}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{1},A}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{3},A}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{1},A}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{2},A}\right) }\right)$</td></tr></table>

<table><tbody><tr><td>查询</td><td>$\frac{{\operatorname{GS}}_{Q}\left( {\left\{  \widehat{\operatorname{mf}}\left( {R}_{i},\mathbf{x}\right) \right\}  }_{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}\right) }{\max \left( {\widehat{\operatorname{mf}}\left( {{R}_{1},B}\right) ,\widehat{\operatorname{mf}}\left( {{R}_{2},B}\right) }\right) }$</td></tr><tr><td>$\left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right) }\right|$</td><td></td></tr><tr><td>$\left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right)  \bowtie  {R}_{3}\left( {C,D}\right) }\right|$</td><td>$\max \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{2},B}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{3},C}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{1},B}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{3},C}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{1},B}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{2},C}\right) }\right)$</td></tr><tr><td>$\left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right)  \bowtie  {R}_{3}\left( {C,A}\right) }\right|$</td><td>$\max \left( {\min \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{2},B}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{3},A}\right) }\right) ,\min \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{1},B}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{3},C}\right) }\right) }\right)$ , $\min \left( {\overset{⏜}{\mathrm{{mf}}\left( {{R}_{2},C}\right) },\overset{⏜}{\mathrm{{mf}}\left( {{R}_{1},A}\right) }}\right)$</td></tr><tr><td>${R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right)$ $\bowtie  {R}_{3}\left( {C,D}\right)  \bowtie  {R}_{4}\left( {D,A}\right)$</td><td>$\max \left( {\min \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{2},B}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{3},C}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{3},D}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{4},A}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{2},B}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{4},A}\right) }\right) }\right) ,$ $\min \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{3},C}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{4},D}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{1},B}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{4},A}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{3},C}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{1},B}\right) }\right) ,$ $\min \left( {\mathrm{{mf}}\left( {{R}_{4},D}\right)  \cdot  \mathrm{{mf}}\left( {{R}_{1},A}\right) ,\mathrm{{mf}}\left( {{R}_{2},C}\right)  \cdot  \mathrm{{mf}}\left( {{R}_{1},B}\right) ,\mathrm{{mf}}\left( {{R}_{4},D}\right)  \cdot  \mathrm{{mf}}\left( {{R}_{2},C}\right) }\right) ,$ $\min \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{1},A}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{2},C}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{3},D}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{2},C}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{3},D}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{1},A}\right) }\right)$</td></tr><tr><td>$\left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {A,C}\right)  \bowtie  {R}_{3}\left( {A,D}\right) }\right|$</td><td>$\max \left( {\widehat{\mathrm{{mf}}}\left( {{R}_{2},A}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{3},A}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{1},A}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{3},A}\right) ,\widehat{\mathrm{{mf}}}\left( {{R}_{1},A}\right)  \cdot  \widehat{\mathrm{{mf}}}\left( {{R}_{2},A}\right) }\right)$</td></tr></tbody></table>

Table 3. Formulating ${\operatorname{GS}}_{Q}\left( {\left\{  \widehat{\operatorname{mf}}\left( {R}_{i},\mathbf{x}\right) \right\}  }_{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}\right)$ with polymatroid bound for common join counting queries.

表3. 常见连接计数查询中采用多拟阵界构建${\operatorname{GS}}_{Q}\left( {\left\{  \widehat{\operatorname{mf}}\left( {R}_{i},\mathbf{x}\right) \right\}  }_{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}\right)$的公式化表示。

<!-- Media -->

queries. Below we prove a more general result that ${\mathrm{{GS}}}_{\Delta \mathrm{Q}} \leq  {\mathrm{{GS}}}_{Q}$ as long as $Q$ is monotonic and supermodular,i.e.,for any ${\mathbf{I}}_{1} \subseteq  {\mathbf{I}}_{2}$ ,and any ${\mathbf{I}}_{3}$ ,

查询。下文我们将证明一个更普适的结论：只要$Q$具有单调性和超模性（即对任意${\mathbf{I}}_{1} \subseteq  {\mathbf{I}}_{2}$及任意${\mathbf{I}}_{3}$），则${\mathrm{{GS}}}_{\Delta \mathrm{Q}} \leq  {\mathrm{{GS}}}_{Q}$成立。

$$
Q\left( {\mathbf{I}}_{1}\right)  \leq  Q\left( {{\mathbf{I}}_{1} \cup  {\mathbf{I}}_{3}}\right)  \tag{Monotonic}
$$

$$
Q\left( {{\mathbf{I}}_{1} \cup  {\mathbf{I}}_{3}}\right)  - Q\left( {\mathbf{I}}_{1}\right)  \leq  Q\left( {{\mathbf{I}}_{2} \cup  {\mathbf{I}}_{3}}\right)  - Q\left( {\mathbf{I}}_{2}\right) .
$$

(Supermodular)

LEMMA 4.1. For any monotonic and supermodular $Q$ ,we have ${\mathrm{{GS}}}_{\Delta \mathrm{Q}} \leq  {\mathrm{{GS}}}_{Q}$ .

引理4.1. 对于任意单调且超模的$Q$，我们有${\mathrm{{GS}}}_{\Delta \mathrm{Q}} \leq  {\mathrm{{GS}}}_{Q}$。

Proof. Recall that ${\mathrm{{GS}}}_{\Delta \mathrm{Q}}$ is the global sensitivity of the following $T$ -dimensional query at level 0 ( $T$ can go to infinite):

证明. 需注意${\mathrm{{GS}}}_{\Delta \mathrm{Q}}$是下列$T$维查询在0层级的全局敏感度（$T$可趋于无限）：

$$
\Delta {\mathbf{Q}}^{\left( 0\right) }\left( \mathbf{I}\right)  \mathrel{\text{:=}} \left( {\Delta {Q}^{\left\lbrack  1,1\right\rbrack  }\left( \mathbf{I}\right) ,\Delta {Q}^{\left\lbrack  2,2\right\rbrack  }\left( \mathbf{I}\right) ,\ldots ,\Delta {Q}^{\left\lbrack  T,T\right\rbrack  }\left( \mathbf{I}\right) }\right) .
$$

Let $\mathbf{I} \sim  {\mathbf{I}}^{\prime }$ be any two neighboring input streams. Without loss of generality,assume that only difference between I and I’ happens at time ${t}^{\prime }$ where ${e}^{\left( {t}^{\prime }\right) } \neq   \bot$ while ${e}^{\prime \left( {t}^{\prime }\right) } =  \bot$ .

设$\mathbf{I} \sim  {\mathbf{I}}^{\prime }$为任意两个相邻输入流。不失一般性，假设I与I'的唯一差异发生在时刻${t}^{\prime }$，其中${e}^{\left( {t}^{\prime }\right) } \neq   \bot$而${e}^{\prime \left( {t}^{\prime }\right) } =  \bot$。

Initially,both instances $\mathbf{I}$ and ${\mathbf{I}}^{\prime }$ are empty,thus

初始状态下，实例$\mathbf{I}$和${\mathbf{I}}^{\prime }$均为空，故有

$$
Q\left( {\mathbf{I}}^{\left( 0\right) }\right)  = Q\left( {\mathbf{I}}^{\prime }\right) . \tag{6}
$$

By the definition ${\mathrm{{GS}}}_{Q}$ ,we have

根据${\mathrm{{GS}}}_{Q}$定义，可得

$$
\left| {Q\left( {\mathbf{I}}^{\left( T\right) }\right)  - Q\left( {\mathbf{I}}^{\prime \left( T\right) }\right) }\right|  \leq  {\mathrm{{GS}}}_{Q}. \tag{7}
$$

Recalling $Q\left( \mathbf{I}\right)  = Q\left( {\mathbf{I}}^{\left( 0\right) }\right)  + \mathop{\sum }\limits_{t}\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \mathbf{I}\right)$ and $Q\left( {\mathbf{I}}^{\prime }\right)  = Q\left( {\mathbf{I}}^{\prime \left( 0\right) }\right)  + \mathop{\sum }\limits_{t}\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( {\mathbf{I}}^{\prime }\right)$ and integrating these with (6) and (7), we have

结合$Q\left( \mathbf{I}\right)  = Q\left( {\mathbf{I}}^{\left( 0\right) }\right)  + \mathop{\sum }\limits_{t}\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \mathbf{I}\right)$与$Q\left( {\mathbf{I}}^{\prime }\right)  = Q\left( {\mathbf{I}}^{\prime \left( 0\right) }\right)  + \mathop{\sum }\limits_{t}\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( {\mathbf{I}}^{\prime }\right)$的定义，并将其代入式(6)和(7)，可得

$$
\left| {\mathop{\sum }\limits_{t}\left( {\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \mathbf{I}\right)  - \Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( {\mathbf{I}}^{\prime }\right) }\right) }\right|  \leq  {\mathrm{{GS}}}_{Q}. \tag{8}
$$

For any $t < {t}^{\prime }$ ,it is clear that $\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( {\mathbf{I}}^{\prime }\right)  = \Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \mathbf{I}\right)$ and by invoking the monotonic property of $Q$ ,we have

对于任意$t < {t}^{\prime }$，显然$\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( {\mathbf{I}}^{\prime }\right)  = \Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \mathbf{I}\right)$成立。根据$Q$的单调性性质，可得

$$
\Delta {Q}^{\left\lbrack  {t}^{\prime },{t}^{\prime }\right\rbrack  }\left( \mathbf{I}\right)  \geq  0 = \Delta {Q}^{\left\lbrack  {t}^{\prime },{t}^{\prime }\right\rbrack  }\left( {\mathbf{I}}^{\prime }\right) .
$$

Moreover,for any $t > {t}^{\prime }$ ,the condition ${\mathbf{I}}^{\prime \left( {t - 1}\right) } \subseteq  {\mathbf{I}}^{\left( t - 1\right) }$ holds true and ${e}^{\left( t\right) } = {e}^{\prime \left( t\right) }$ leading to

此外，对任意$t > {t}^{\prime }$，条件${\mathbf{I}}^{\prime \left( {t - 1}\right) } \subseteq  {\mathbf{I}}^{\left( t - 1\right) }$成立且${e}^{\left( t\right) } = {e}^{\prime \left( t\right) }$，从而导出

$$
\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \mathbf{I}\right)  = Q\left( {{\mathbf{I}}^{\left( t - 1\right) } \cup  \left\{  {e}^{\left( t\right) }\right\}  }\right)  - Q\left( {\mathbf{I}}^{\left( t - 1\right) }\right) 
$$

$$
 = Q\left( {{\mathbf{I}}^{\left( t - 1\right) } \cup  \left\{  {e}^{\prime \left( t\right) }\right\}  }\right)  - Q\left( {\mathbf{I}}^{\left( t - 1\right) }\right) 
$$

$$
 \geq  Q\left( {{\mathbf{I}}^{\prime \left( {t - 1}\right) } \cup  \left\{  {e}^{\prime \left( t\right) }\right\}  }\right)  - Q\left( {\mathbf{I}}^{\prime \left( {t - 1}\right) }\right) 
$$

$$
 = \Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( {\mathbf{I}}^{\prime }\right) , \tag{9}
$$

where the inequality in the third line is by the supermodular property of $Q$ .

其中第三行不等式源于$Q$的超模性性质。

Combining (8) and (9), we have

综合式(8)与(9)，可得

$$
\mathop{\sum }\limits_{t}\left| {\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \mathbf{I}\right)  - \Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( {\mathbf{I}}^{\prime }\right) }\right|  \leq  {\mathrm{{GS}}}_{Q},
$$

as desired.

证毕。

Note that any multi-way join query is both monotonic and supermodular. Monotonicity is trivial. To see supermodularity,consider adding a tuple $e$ to $\mathbf{I}$ . The join size will increase by $\left| {e \bowtie  \mathbf{I}}\right|$ . When $\mathbf{I}$ has more tuples, $\left| {e \bowtie  \mathrm{I}}\right|$ cannot be less. Thus,the BM and Lemma 3.9 immediately generalize to any multi-way join query,with an error proportional to ${\operatorname{GS}}_{Q}\left( \left\{  {\widehat{\operatorname{mf}}\left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right)$ . We note that [28] has also derived ${\mathrm{{GS}}}_{\Delta \mathrm{Q}}$ for triangle counting query and $n$ -star counting queries. Our general polymatroid bound ${\operatorname{GS}}_{Q}\left( \left\{  {\widehat{\operatorname{mf}}\left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right)$ degenerates into their bounds on these two specific queries.

注意，任何多路连接查询既是单调的又是超模的。单调性显而易见。要理解超模性，考虑向$\mathbf{I}$添加元组$e$时，连接规模将增加$\left| {e \bowtie  \mathbf{I}}\right|$。当$\mathbf{I}$包含更多元组时，$\left| {e \bowtie  \mathrm{I}}\right|$不可能减少。因此，BM和引理3.9可直接推广至任意多路连接查询，其误差与${\operatorname{GS}}_{Q}\left( \left\{  {\widehat{\operatorname{mf}}\left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right)$成正比。我们注意到文献[28]同样推导出了三角形计数查询的${\mathrm{{GS}}}_{\Delta \mathrm{Q}}$和$n$-星形计数查询的边界。我们提出的通用多拟阵边界${\operatorname{GS}}_{Q}\left( \left\{  {\widehat{\operatorname{mf}}\left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right)$在这两种特定查询中退化为他们的边界。

## 5 DYNAMIC CLIPPING MECHANISM

## 5 动态截断机制

Although Lemma 4.1 has enabled the binary mechanism to work for any multi-way join query, it is far from satisfactory. First,it requires the frequency constraints $\left\{  {\widehat{\operatorname{mf}}\left( {{R}_{i},\mathbf{x}}\right) }\right\}$ to be set a priori. This is problematic, especially for an unbounded stream. In practice, it is also not easy to give a reasonable constraint for every $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$ : The triangle query already requires 6 constraints,other queries may require more. Second and more importantly, its error at every time step is proportional to ${\mathrm{{GS}}}_{Q}\left( \left\{  {\widehat{\operatorname{mf}}\left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right)$ ,even when the current database is small and nowhere near the constraints. Ideally,the error should be proportional to ${\mathrm{{GS}}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)$ ,i.e.,it only depends on the actual maximum frequencies of the database at the current time $t$ . In this section,we present the main result of this paper, a dynamic clipping mechanism that achieves this instance-specific error without requiring any frequency constraints given in advance.

尽管引理4.1使得二元机制能适用于任意多路连接查询，但仍远未臻完善。首先，它要求频率约束$\left\{  {\widehat{\operatorname{mf}}\left( {{R}_{i},\mathbf{x}}\right) }\right\}$必须预先设定。这对无界流尤其成问题。实践中，为每个$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$设定合理约束也非易事：仅三角形查询就需要6个约束，其他查询可能需求更多。其次且更重要的是，其在每个时间步的误差与${\mathrm{{GS}}}_{Q}\left( \left\{  {\widehat{\operatorname{mf}}\left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right)$成正比，即使当前数据库规模很小且远未触及约束边界。理想情况下，误差应与${\mathrm{{GS}}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)$成正比，即仅取决于当前时刻$t$数据库的实际最大频率。本节将展示本文主要成果——一种无需预先给定频率约束即可实现此实例特定误差的动态截断机制。

In the static setting, a prevalent technique for achieving an instance-specific error is the clipping (or truncation) mechanism [16,32,36]. For a clipping threshold $\tau$ ,all tuples with influence more than $\tau$ are clipped. Then,the global sensitivity of the query is limited by $\tau$ (or some function of $\tau$ ) and the Laplace mechanism is then invoked.

静态环境下，实现实例特定误差的常用技术是截断机制[16,32,36]。给定截断阈值$\tau$，所有影响力超过$\tau$的元组将被截断。此时查询的全局敏感度受限于$\tau$（或$\tau$的某个函数），随后调用拉普拉斯机制。

However, a straightforward extension of the clipping mechanism to the dynamic setting will not work. Let $\overline{\mathbf{I}}$ denote the clipped instance,and ${\overline{\mathbf{I}}}^{\left( t\right) }$ and ${\bar{I}}_{i}^{\left( t\right) }$ are prefix database instance and prefix relation instance of time $t$ . Suppose we use a clipping threshold $\tau \left( {{R}_{i},\mathbf{x}}\right)$ for each $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$ , and clip an incoming tuple ${e}^{\left( t\right) }$ if $\operatorname{freq}\left( {{\bar{I}}_{i}^{\left( t - 1\right) },{\pi }_{\mathbf{x}}{e}^{\left( t\right) }}\right)  = \tau \left( {{R}_{i},\mathbf{x}}\right)$ for some $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$ . The clipped database instance will thus satisfy the frequency constraints

然而，将剪裁机制直接扩展至动态场景并不可行。设$\overline{\mathbf{I}}$表示剪裁后的实例，${\overline{\mathbf{I}}}^{\left( t\right) }$和${\bar{I}}_{i}^{\left( t\right) }$分别是时间$t$的前缀数据库实例与前缀关系实例。若对每个$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$采用剪裁阈值$\tau \left( {{R}_{i},\mathbf{x}}\right)$，当某些$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$满足$\operatorname{freq}\left( {{\bar{I}}_{i}^{\left( t - 1\right) },{\pi }_{\mathbf{x}}{e}^{\left( t\right) }}\right)  = \tau \left( {{R}_{i},\mathbf{x}}\right)$时剪裁输入元组${e}^{\left( t\right) }$，则剪裁后的数据库实例将满足频次约束

$\left\{  {\operatorname{freq}\left( {{\bar{I}}_{i}^{\left( t\right) },\mathbf{x}}\right)  \leq  \tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$ for any times $t \in  {\mathbb{Z}}^{ + }$ ,and it may appear that we can then use the BM with ${\mathrm{{GS}}}_{\Delta \mathrm{Q}} = {\mathrm{{GS}}}_{Q}\left( \left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right)$ . However,the following example shows that this breaks privacy.

$\left\{  {\operatorname{freq}\left( {{\bar{I}}_{i}^{\left( t\right) },\mathbf{x}}\right)  \leq  \tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$对于任意时间$t \in  {\mathbb{Z}}^{ + }$成立，表面上看似乎可以结合${\mathrm{{GS}}}_{\Delta \mathrm{Q}} = {\mathrm{{GS}}}_{Q}\left( \left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right)$使用BM机制。但下例表明这会破坏隐私性。

<!-- Media -->

<!-- figureText: Kept tuples Clipped tuples ... Time 7 Time 12 Time 11 ${c}_{2}$ Time 6 Time 9 ${a}_{2}$ Time 10 Time 7 Time 12 Time 11 ${c}_{2}$ Time 4 Time 3 Time 8 ${\mathbf{I}}^{\prime }$ Time 2 Time 5 Time 4 Time 3 Time 8 ${c}_{1}$ I -->

<img src="https://cdn.noedgeai.com/0196599c-190a-7a73-a499-f2871a0182b1_12.jpg?x=408&y=259&w=747&h=534&r=0"/>

Fig. 3. An example showing that the naïvely clipping mechanism breaks privacy.

图3. 展示朴素剪裁机制破坏隐私性的示例。

<!-- Media -->

Example 5.1. Consider the triangle query ${Q}_{\nabla } = \left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right)  \bowtie  {R}_{3}\left( {C,A}\right) }\right|$ ,where $\operatorname{dom}\left( A\right)  = \left\{  {{a}_{1},{a}_{2},\ldots ,}\right\}  ,\operatorname{dom}\left( B\right)  = \left\{  {{b}_{0},{b}_{1},{b}_{2},\ldots ,}\right\}  ,\operatorname{dom}\left( C\right)  = \left\{  {{c}_{1},{c}_{2},\ldots }\right\}$ . The two neighboring instances $\mathrm{I}$ and ${\mathrm{I}}^{\prime }$ are shown in Figure 3,where $\mathrm{I}$ has one less tuple at time $1 : {e}^{\left( 1\right) } =  \bot$ . Suppose $\tau \left( {{R}_{i},\mathbf{x}}\right)  = 1$ for all $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$ ,i.e.,all the in-degrees and out-degrees are constrained to 1 . Then we see that on the clipped instances,at every time $t \in  {\mathbb{Z}}^{ + }$ ,we have ${Q}_{\nabla }\left( {\overline{\mathbf{I}}}^{\left( t\right) }\right)  = \lfloor t/4\rfloor$ while ${Q}_{\nabla }\left( {\overline{\mathbf{I}}}^{\left( t\right) }\right)  = 0$ . On the other hand, ${\mathrm{{GS}}}_{Q}\left( \left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right)  = 1}\right\}  \right)  = 1$ ,but adding a noise of scale 1 cannot mask this difference.

示例5.1. 考虑三角查询${Q}_{\nabla } = \left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right)  \bowtie  {R}_{3}\left( {C,A}\right) }\right|$，其中$\operatorname{dom}\left( A\right)  = \left\{  {{a}_{1},{a}_{2},\ldots ,}\right\}  ,\operatorname{dom}\left( B\right)  = \left\{  {{b}_{0},{b}_{1},{b}_{2},\ldots ,}\right\}  ,\operatorname{dom}\left( C\right)  = \left\{  {{c}_{1},{c}_{2},\ldots }\right\}$。图3所示的两个相邻实例$\mathrm{I}$与${\mathrm{I}}^{\prime }$中，$\mathrm{I}$在时间$1 : {e}^{\left( 1\right) } =  \bot$少一个元组。假设所有$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$满足$\tau \left( {{R}_{i},\mathbf{x}}\right)  = 1$（即入度与出度均约束为1）。可见在剪裁实例上，每个时刻$t \in  {\mathbb{Z}}^{ + }$都存在${Q}_{\nabla }\left( {\overline{\mathbf{I}}}^{\left( t\right) }\right)  = \lfloor t/4\rfloor$而${Q}_{\nabla }\left( {\overline{\mathbf{I}}}^{\left( t\right) }\right)  = 0$。另一方面${\mathrm{{GS}}}_{Q}\left( \left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right)  = 1}\right\}  \right)  = 1$，但添加尺度为1的噪声无法掩盖此差异。

Fundamentally,the key condition needed by the BM is that ${\mathrm{{GS}}}_{Q}\left( \left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right)$ is the maximum difference in the query result between any two neighboring instances both satisfying the frequency constraints. However, while the clipped instance satisfies the frequency constraints after clipping, two neighboring instances (before clipping) may not be neighbors anymore after the clipping, as illustrated in the example above.

本质上，BM所需的关键条件是：${\mathrm{{GS}}}_{Q}\left( \left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right)$是满足频率约束的任意两个相邻实例间查询结果的最大差异。然而，虽然裁剪后的实例满足频率约束，但两个相邻实例（裁剪前）在裁剪后可能不再相邻，如上例所示。

In the dynamic setting, another challenge is that we must dynamically select a clipping threshold as the instance grows over time in order to achieve an error that depends on the current instance, as opposed to the static setting where the threshold is only computed once and for all. Note that the growing clipping threshold must be continuously selected in a differentially private fashion. In the next two subsections, we show how to overcome these two challenges.

在动态场景中，另一个挑战是必须随着实例随时间增长动态选择裁剪阈值，以实现与当前实例相关的误差，这与静态场景中仅需一次性计算阈值不同。需要注意的是，增长的裁剪阈值必须以差分隐私方式持续选择。接下来两小节将展示如何克服这两个挑战。

### 5.1 A Private Clipping Mechanism

### 5.1 私有裁剪机制

In this subsection, we show how to clip a stream of tuples under a set of fixed clipping threshold $\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$ ,so that two neighboring streams are still $k$ -neighbors after the clipping with $k$ equal to some constant. Then it would be safe to feed the clipped streams to the BM while satisfying DP.

本小节展示如何在一组固定裁剪阈值$\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$下裁剪元组流，使得两个相邻流在裁剪后仍保持$k$-相邻性（$k$为某常数）。这样就能安全地将裁剪后的流输入BM，同时满足差分隐私(DP)。

The idea is, in addition to the clipped database instance, we also maintain the unclipped database. When deciding if a tuple ${e}^{\left( t\right) }$ should be clipped,we check its frequencies in the unclipped database: If $\operatorname{freq}\left( {{I}_{i}^{\left( t - 1\right) },{\pi }_{\mathbf{x}}{e}^{\left( t\right) }}\right)  \geq  \tau \left( {{R}_{i},\mathbf{x}}\right)$ for some $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$ in the unclipped database, $e$ will be clipped. Note that the condition uses $\geq$ instead of $=$ as in the naïvely clipping mechanism,since the frequencies may exceed the clipping threshold in the unclipped database. Applying our new clipping mechanism on Example 5.1, the clipped streams are shown in Figure 4, which differ in 2 time steps (time 1 and 2), i.e., they are still distance-2 neighbors. This means that they can still be fed to BM, except with a privacy budget of $\varepsilon /2$ by the group privacy property of DP (Lemma 3.5).

核心思路是：除维护裁剪后的数据库实例外，还保留原始数据库。当判定是否裁剪元组${e}^{\left( t\right) }$时，检查其在原始库中的频率：若原始库中某个$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$的$\operatorname{freq}\left( {{I}_{i}^{\left( t - 1\right) },{\pi }_{\mathbf{x}}{e}^{\left( t\right) }}\right)  \geq  \tau \left( {{R}_{i},\mathbf{x}}\right)$成立，则执行$e$裁剪。注意这里使用$\geq$而非朴素裁剪机制中的$=$作为条件，因为原始库中频率可能超过裁剪阈值。将该机制应用于例5.1后，裁剪流如图4所示，两者在2个时间步（时刻1和2）存在差异，即仍保持距离-2相邻性。这意味着仍可输入BM，仅需根据DP的群组隐私性质（引理3.5）调整隐私预算为$\varepsilon /2$。

<!-- Media -->

<!-- figureText: Kept tuples Clipped tuples ... Time 7 Time 12 Time 11 ${c}_{2}$ Time 6 Time 9 ${a}_{2}$ Time 10 Time 7 Time 12 Time 11 ${c}_{2}$ Time 4 Time 3 Time 8 ${\mathbf{I}}^{\prime }$ Time 2 Time 5 Time 4 Time 3 Time 8 ${c}_{1}$ $\mathbf{I}$ -->

<img src="https://cdn.noedgeai.com/0196599c-190a-7a73-a499-f2871a0182b1_13.jpg?x=410&y=262&w=745&h=532&r=0"/>

Fig. 4. An example of the new clipping mechanism.

图4. 新裁剪机制示例

<!-- Media -->

It turns out that the general guarantee we can prove below is just slightly worse than this example (recall that ${m}_{\max } = 2$ for the triangle query). Here,we use $\operatorname{Clip}\left( {\mathbf{I},\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)$ to denote the stream clipped with thresholds $\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$ . More formally, $\operatorname{Clip}\left( {\mathbf{I},\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)$ $= \left( {{\bar{R}}^{\left( 1\right) } : {\bar{e}}^{\left( 1\right) },{\bar{R}}^{\left( 2\right) } : {\bar{e}}^{\left( 2\right) },\ldots }\right)$ ,where for each $t \in  {\mathbb{Z}}^{ + }$ ,let ${R}^{\left( t\right) } = {R}_{{i}^{\prime }}$ ,then

事实证明，下文可证明的通用保证仅略逊于此例（回忆三角形查询的${m}_{\max } = 2$）。此处用$\operatorname{Clip}\left( {\mathbf{I},\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)$表示以阈值$\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$裁剪的流。更形式化地，$\operatorname{Clip}\left( {\mathbf{I},\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)$$= \left( {{\bar{R}}^{\left( 1\right) } : {\bar{e}}^{\left( 1\right) },{\bar{R}}^{\left( 2\right) } : {\bar{e}}^{\left( 2\right) },\ldots }\right)$，其中对每个$t \in  {\mathbb{Z}}^{ + }$，令${R}^{\left( t\right) } = {R}_{{i}^{\prime }}$，则有：

$$
{\bar{R}}^{\left( t\right) },{\bar{e}}^{\left( t\right) } = \left\{  \begin{array}{ll} {R}^{\left( t\right) },{e}^{\left( t\right) } & \forall \mathbf{x} \in  {\mathcal{B}}_{Q,{i}^{\prime }},\operatorname{freq}\left( {{I}_{{i}^{\prime }}^{\left( t - 1\right) },{\pi }_{\mathbf{x}}{e}^{\left( t - 1\right) }}\right)  < \tau \left( {{R}_{{i}^{\prime }},\mathbf{x}}\right) \\  \text{ NULL,}\bot & \text{ Otherwise. } \end{array}\right. 
$$

LEMMA 5.2. Given any $\mathbf{I} \sim  {\mathbf{I}}^{\prime }$ and any $\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$ ,

引理5.2. 给定任意$\mathbf{I} \sim  {\mathbf{I}}^{\prime }$与任意$\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$，

$$
d\left( {\operatorname{Clip}\left( {\mathbf{I},\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right) ,\operatorname{Clip}\left( {{\mathbf{I}}^{\prime },\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right) }\right)  \leq  {m}_{\max } + 1.
$$

Proof. Let us consider two instances $\mathbf{I}$ and ${\mathbf{I}}^{\prime }$ such that ${\mathbf{I}}^{\prime } \subseteq  \mathbf{I}$ ,with $\mathbf{I}$ and ${\mathbf{I}}^{\prime }$ differing by ${e}^{\left( {t}^{\prime }\right) }$ with ${R}^{\left( {t}^{\prime }\right) } = {R}_{{i}^{\prime }}$ ,i.e., ${e}^{\left( {t}^{\prime }\right) } \in  {I}_{{i}^{\prime }}^{\left( {t}^{\prime }\right) },{e}^{\prime \left( {t}^{\prime }\right) } =  \bot$ .

证明。考虑两个实例$\mathbf{I}$与${\mathbf{I}}^{\prime }$满足${\mathbf{I}}^{\prime } \subseteq  \mathbf{I}$，其中$\mathbf{I}$和${\mathbf{I}}^{\prime }$在${R}^{\left( {t}^{\prime }\right) } = {R}_{{i}^{\prime }}$条件下相差${e}^{\left( {t}^{\prime }\right) }$，即${e}^{\left( {t}^{\prime }\right) } \in  {I}_{{i}^{\prime }}^{\left( {t}^{\prime }\right) },{e}^{\prime \left( {t}^{\prime }\right) } =  \bot$。

Given that ${\mathbf{I}}^{\prime } \subseteq  \mathbf{I}$ ,it follows that for any time $t \in  {\mathbb{Z}}^{ + }$ ,any $i \in  \left\lbrack  n\right\rbrack$ ,any $\mathbf{x} \subseteq  {\mathbf{x}}_{i}$ ,and any $a \in  \operatorname{dom}\left( \mathbf{x}\right)$ , we always have $\operatorname{freq}\left( {{I}^{\prime }{}_{i}^{\left( t\right) },a}\right)  \leq  \operatorname{freq}\left( {{I}_{i}^{\left( t\right) },a}\right)$ . As a consequence,excluding the tuple ${e}^{\left( {t}^{\prime }\right) }$ ,the clipped instance of $\mathbf{I}$ will not contain any additional tuples compared to the clipped instance of ${\mathbf{I}}^{\prime }$ ,i.e.,

给定${\mathbf{I}}^{\prime } \subseteq  \mathbf{I}$，可知对任意时间$t \in  {\mathbb{Z}}^{ + }$、任意$i \in  \left\lbrack  n\right\rbrack$、任意$\mathbf{x} \subseteq  {\mathbf{x}}_{i}$及任意$a \in  \operatorname{dom}\left( \mathbf{x}\right)$，始终有$\operatorname{freq}\left( {{I}^{\prime }{}_{i}^{\left( t\right) },a}\right)  \leq  \operatorname{freq}\left( {{I}_{i}^{\left( t\right) },a}\right)$。因此，剔除元组${e}^{\left( {t}^{\prime }\right) }$后，$\mathbf{I}$的裁剪实例相比${\mathbf{I}}^{\prime }$的裁剪实例不会包含额外元组，即

$$
\left| {\operatorname{Clip}\left( {\mathbf{I},\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)  - \operatorname{Clip}\left( {{\mathbf{I}}^{\prime },\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right) }\right|  \leq  1. \tag{10}
$$

Furthermore,at any given time $t \in  {\mathbb{Z}}^{ + }$ ,the tuple ${e}^{\left( {t}^{\prime }\right) }$ will only affect freq $\left( {{I}_{{i}^{\prime }}^{\left( t\right) },{\pi }_{\mathbf{x}}{e}^{\left( {t}^{\prime }\right) }}\right)$ for $\mathbf{x} \in  {\mathcal{B}}_{Q,{i}^{\prime }}$ ,each of which can at most result in one additional tuple being clipped in I. Recalling the definition that ${m}_{i} = \left| {\mathcal{B}}_{Q,i}\right|$ ,we have

此外，在任意时刻$t \in  {\mathbb{Z}}^{ + }$，元组${e}^{\left( {t}^{\prime }\right) }$仅会影响$\mathbf{x} \in  {\mathcal{B}}_{Q,{i}^{\prime }}$对应的频率$\left( {{I}_{{i}^{\prime }}^{\left( t\right) },{\pi }_{\mathbf{x}}{e}^{\left( {t}^{\prime }\right) }}\right)$，每个频率至多导致实例I中被裁剪的元组增加一个。根据${m}_{i} = \left| {\mathcal{B}}_{Q,i}\right|$的定义可得

$$
\left| {\operatorname{Clip}\left( {{\mathbf{I}}^{\prime },\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)  - \operatorname{Clip}\left( {\mathbf{I},\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right) }\right|  \leq  {m}_{i}. \tag{11}
$$

Combining (10) and (11), we are able to deduce the conclusion.

综合(10)和(11)式，可推导出结论。

<!-- Media -->

Algorithm 3: ClipDP.

算法3：ClipDP（差分隐私裁剪算法）。

---

	Input: $\mathrm{I} = \left( {{R}^{\left( 1\right) } : {e}^{\left( 1\right) },{R}^{\left( 2\right) } : {e}^{\left( 2\right) },\ldots }\right) ,\varepsilon ,\theta ,\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$

	输入：$\mathrm{I} = \left( {{R}^{\left( 1\right) } : {e}^{\left( 1\right) },{R}^{\left( 2\right) } : {e}^{\left( 2\right) },\ldots }\right) ,\varepsilon ,\theta ,\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$

	Compute ${\mathrm{{GS}}}_{Q}\left( {\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  \text{as Section 4.1;}}\right)$

	计算${\mathrm{{GS}}}_{Q}\left( {\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  \text{as Section 4.1;}}\right)$

2 Initialize $\operatorname{BM}\left( {\operatorname{Clip}\left( {\mathbf{I},\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right) ,\frac{\varepsilon }{{m}_{\max  + 1}},{\operatorname{GS}}_{Q}\left( \left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right) }\right)$ with $\operatorname{Clip}\left( {\mathbf{I},\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)$ updated time to

2 初始化$\operatorname{BM}\left( {\operatorname{Clip}\left( {\mathbf{I},\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right) ,\frac{\varepsilon }{{m}_{\max  + 1}},{\operatorname{GS}}_{Q}\left( \left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right) }\right)$，将$\operatorname{Clip}\left( {\mathbf{I},\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)$更新时间设为

	time;

	时间；

	for $t \leftarrow  1,2,\ldots$ do

	对于 $t \leftarrow  1,2,\ldots$ 执行

			Update $\operatorname{Clip}\left( {\mathbf{I},\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)$ used in $\mathrm{{BM}}$ ;

			更新 $\operatorname{Clip}\left( {\mathbf{I},\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)$ 用于 $\mathrm{{BM}}$ ;

			Answer $\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)$ with BM;

			用BM应答 $\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)$ ;

	end

	结束

---

<!-- Media -->

The immediate consequence of this lemma is that,to satisfy $\varepsilon$ -DP,it is sufficient to feed the clipped stream to BM with a privacy budget of $\varepsilon /\left( {{m}_{\max } + 1}\right)$ . We denote this mechanism as ClipDP and the details are shown in Algorithm 3. Next, we analyze the utility. The error of ClipDP consists of two parts: the noise introduced by BM and the bias due the clipping. Feeding the facts that ${m}_{\max }$ is some small constant,and the clipped stream satisfies the frequency constraints $\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$ ,into Lemma 3.9,we can get the noise term bounded by $O\left( {\frac{1}{\varepsilon \theta } \cdot  {\operatorname{GS}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)  \cdot  {\log }^{{1.5} + \theta }t \cdot  \log \left( {1/\beta }\right) }\right)$ . We bound the bias in terms of the number of tuples clipped:

该引理的直接推论是：为满足$\varepsilon$-差分隐私(DP)，只需将裁剪后的数据流输入BM机制并分配$\varepsilon /\left( {{m}_{\max } + 1}\right)$的隐私预算。我们将此机制称为ClipDP，其具体实现如算法3所示。接着分析效用性，ClipDP的误差由两部分构成：BM引入的噪声和裁剪导致的偏差。鉴于${m}_{\max }$为较小常数，且裁剪流满足频率约束$\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$，代入引理3.9可得噪声项上限为$O\left( {\frac{1}{\varepsilon \theta } \cdot  {\operatorname{GS}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)  \cdot  {\log }^{{1.5} + \theta }t \cdot  \log \left( {1/\beta }\right) }\right)$。我们通过裁剪元组数量界定偏差：

$$
\operatorname{ClipNum}\left( {{\mathbf{I}}^{\left( t\right) },\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)  = \left| {{\mathbf{I}}^{\left( t\right) } - \operatorname{Clip}\left( {{\mathbf{I}}^{\left( t\right) },\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right) }\right| .
$$

Since each clipped tuple can contribute at most ${\mathrm{{GS}}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)$ number of join results,we obtain the following utility guarantee:

由于每个被裁剪元组最多贡献${\mathrm{{GS}}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)$个连接结果，得到如下效用保证：

THEOREM 5.3. Given any $\varepsilon  > 0,\theta  > 0$ ,and any clipping thresholds $\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$ ,the mechanism $\operatorname{ClipDP}\left( {\mathbf{I},\varepsilon ,\theta ,\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)$ satisfies $\varepsilon$ -DP. For any $t \in  {\mathbb{Z}}^{ + }$ ,with probability at least $1 - \beta$ ,it returns a $\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)$ such that

定理5.3. 给定任意$\varepsilon  > 0,\theta  > 0$和裁剪阈值$\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$，机制$\operatorname{ClipDP}\left( {\mathbf{I},\varepsilon ,\theta ,\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)$满足$\varepsilon$-差分隐私(DP)。对于任意$t \in  {\mathbb{Z}}^{ + }$，以至少$1 - \beta$的概率返回满足以下条件的$\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)$：

$$
\left| {\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)  - Q\left( {\mathbf{I}}^{\left( t\right) }\right) }\right|  \leq  O\left( {\frac{1}{\varepsilon \theta } \cdot  {\operatorname{GS}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)  \cdot  {\log }^{{1.5} + \theta }t \cdot  \log \left( {1/\beta }\right) }\right) 
$$

$$
 + {\operatorname{GS}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)  \cdot  \operatorname{ClipNum}\left( {{\mathbf{I}}^{\left( t\right) },\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right) \text{.}
$$

### 5.2 Adaptive Clipping Thresholds

### 5.2 自适应裁剪阈值

Both the error terms in Theorem 5.3 crucially depend on the clipping thresholds $\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$ . Larger values of $\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$ will reduce the bias but increase the noise. The optimal choice of thresholds is $\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right)  = \operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}$ for each time $t$ ,which will make the noise term $\widetilde{O}\left( {{\operatorname{GS}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right) }\right)$ , as desired, and the second term 0 . However, using these thresholds directly violates DP as they depend on the actual instance.

定理5.3中的误差项关键取决于裁剪阈值$\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$。增大$\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$会减小偏差但增加噪声。最优阈值选择是每个时刻$t$取$\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right)  = \operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}$，这样可使噪声项如期望变为$\widetilde{O}\left( {{\operatorname{GS}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right) }\right)$，第二项归零。但直接使用这些阈值会违反差分隐私，因其依赖具体数据实例。

To find privacy-preserving and near-optimal clipping thresholds, the idea is to start with a small threshold $\tau \left( {{R}_{i},\mathbf{x}}\right)  = 2$ at the beginning. After excessive tuples have been clipped,we double the value of $\tau \left( {{R}_{i},\mathbf{x}}\right)$ and (conceptually) rerun ClipDP with the new thresholds. To determine the right moment to double $\tau \left( {{R}_{i},\mathbf{x}}\right)$ in a privacy-preserving manner,it is crucial first to establish a method for quantifying the number of tuples clipped by the current $\tau \left( {{R}_{i},\mathbf{x}}\right)$ . For any $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$ ,let

为寻找隐私保护且接近最优的裁剪阈值，其思路是从一个较小的初始阈值$\tau \left( {{R}_{i},\mathbf{x}}\right)  = 2$开始。当过量元组被裁剪后，我们将$\tau \left( {{R}_{i},\mathbf{x}}\right)$的值翻倍，并（概念上）用新阈值重新运行ClipDP。要以隐私保护的方式确定翻倍$\tau \left( {{R}_{i},\mathbf{x}}\right)$的恰当时机，关键在于首先建立量化当前$\tau \left( {{R}_{i},\mathbf{x}}\right)$所裁剪元组数量的方法。对于任意$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$，设

$$
\operatorname{ClipNum}\left( {{I}_{i}^{\left( t\right) },\tau \left( {{R}_{i},\mathbf{x}}\right) }\right)  = \left| \left\{  {e \in  {I}_{i}^{\left( t\right) } : \operatorname{freq}\left( {{I}_{i}^{\left( t\right) },{\pi }_{\mathbf{x}}e}\right)  > \tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right| 
$$

<!-- Media -->

Algorithm 4: DynamicClipDP.

算法4：动态裁剪差分隐私(DynamicClipDP)。

Input: $\mathbf{I} = \left( {{R}^{\left( 1\right) } : {e}^{\left( 1\right) },{R}^{\left( 2\right) } : {e}^{\left( 2\right) },\ldots }\right) ,\varepsilon ,\beta ,\theta$

输入：$\mathbf{I} = \left( {{R}^{\left( 1\right) } : {e}^{\left( 1\right) },{R}^{\left( 2\right) } : {e}^{\left( 2\right) },\ldots }\right) ,\varepsilon ,\beta ,\theta$

---

${k}_{\mathrm{C}} \leftarrow  1$ ; // Initialize the parameters for the first ClipDP

${k}_{\mathrm{C}} \leftarrow  1$； // 初始化首个ClipDP的参数

${\varepsilon }_{\mathrm{C}} \leftarrow  {\varepsilon \theta }/{2}^{2 + \theta };$

Let $\tau \left( {{R}_{i},\mathbf{x}}\right)  \leftarrow  2$ for each $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$ ;

对每个$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$设$\tau \left( {{R}_{i},\mathbf{x}}\right)  \leftarrow  2$；

Start $\operatorname{ClipDP}\left( {\mathbf{I},{\varepsilon }_{\mathrm{C}},\theta ,\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)$ ;

启动$\operatorname{ClipDP}\left( {\mathbf{I},{\varepsilon }_{\mathrm{C}},\theta ,\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)$；

for $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$ do

循环开始$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$：

			${k}_{{\mathrm{S}}_{\left( i,\mathrm{x}\right) }} \leftarrow  1;//$ Initialize the parameters for the first SVT associated with $\tau \left( {{R}_{i},\mathbf{x}}\right)$

			${k}_{{\mathrm{S}}_{\left( i,\mathrm{x}\right) }} \leftarrow  1;//$ 初始化与$\tau \left( {{R}_{i},\mathbf{x}}\right)$关联的首个稀疏向量测试(SVT)参数

			${\varepsilon }_{{\mathrm{S}}_{\left( i,\mathbf{x}\right) }} \leftarrow  {\varepsilon \theta }/\left( {{m}_{\max } \cdot  {2}^{2 + \theta }}\right) ,{\beta }_{{\mathrm{S}}_{\left( i,\mathbf{x}\right) }} \leftarrow  \beta /\left( {m \cdot  {2}^{3}}\right) ;$

			Initialize ${\mathrm{{SVT}}}_{\left( i,\mathbf{x}\right) } \leftarrow  \operatorname{SVT}\left( {0,{\varepsilon }_{{\mathrm{{SVT}}}_{\left( i,\mathbf{x}\right) }},{f}_{1}\left( \mathbf{I}\right) ,{f}_{2}\left( \mathbf{I}\right) ,\ldots }\right)$ with ${f}_{t}\left( \mathbf{I}\right)$ ’s inputted later;

			用后续输入的${f}_{t}\left( \mathbf{I}\right)$初始化${\mathrm{{SVT}}}_{\left( i,\mathbf{x}\right) } \leftarrow  \operatorname{SVT}\left( {0,{\varepsilon }_{{\mathrm{{SVT}}}_{\left( i,\mathbf{x}\right) }},{f}_{1}\left( \mathbf{I}\right) ,{f}_{2}\left( \mathbf{I}\right) ,\ldots }\right)$；

end

结束

for $t \leftarrow  1,2,\ldots$ do

循环开始$t \leftarrow  1,2,\ldots$：

			do

			执行

						SVTStop $\leftarrow$ False; // Check whether any $\tau \left( {{R}_{i},\mathbf{x}}\right)$ needs to be doubled

											稀疏向量测试停止标志$\leftarrow$设为假； // 检查是否有$\tau \left( {{R}_{i},\mathbf{x}}\right)$需要翻倍

						for $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{O}$ do

											循环开始$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{O}$：

									Feed ${f}_{t}\left( \mathbf{I}\right)  \leftarrow  \operatorname{ClipNum}\left( {{I}_{i}^{\left( t\right) },\tau \left( {{R}_{i},\mathbf{x}}\right) }\right)  - \frac{8}{{\varepsilon }_{{\mathrm{S}}_{\left( i,\mathbf{x}\right) }}}\log \left( {2/{\beta }_{{\mathrm{S}}_{\left( i,\mathbf{x}\right) }}}\right)  - \frac{6}{{\varepsilon }_{{\mathrm{S}}_{\left( i,\mathbf{x}\right) }}}\log \left( {t + 1}\right)$ into

																	将${f}_{t}\left( \mathbf{I}\right)  \leftarrow  \operatorname{ClipNum}\left( {{I}_{i}^{\left( t\right) },\tau \left( {{R}_{i},\mathbf{x}}\right) }\right)  - \frac{8}{{\varepsilon }_{{\mathrm{S}}_{\left( i,\mathbf{x}\right) }}}\log \left( {2/{\beta }_{{\mathrm{S}}_{\left( i,\mathbf{x}\right) }}}\right)  - \frac{6}{{\varepsilon }_{{\mathrm{S}}_{\left( i,\mathbf{x}\right) }}}\log \left( {t + 1}\right)$输入

										${\mathrm{{SVT}}}_{\left( i,\mathbf{x}\right) }$ ;

									if ${\mathrm{{SVT}}}_{\left( i,\mathbf{x}\right) }$ stops at $t$ then

																	若${\mathrm{{SVT}}}_{\left( i,\mathbf{x}\right) }$在$t$处停止则

												$\tau \left( {{R}_{i},\mathbf{x}}\right)  \leftarrow  \tau \left( {{R}_{i},\mathbf{x}}\right)  \cdot  2;//$ Double the $\tau \left( {{R}_{i},\mathbf{x}}\right)$ and re-run the ${k}_{{\mathrm{S}}_{\left( i,\mathbf{x}\right) }} + 1$ th SVT

												$\tau \left( {{R}_{i},\mathbf{x}}\right)  \leftarrow  \tau \left( {{R}_{i},\mathbf{x}}\right)  \cdot  2;//$ 将$\tau \left( {{R}_{i},\mathbf{x}}\right)$加倍并重新运行第${k}_{{\mathrm{S}}_{\left( i,\mathbf{x}\right) }} + 1$次SVT

													associated with $\tau \left( {{R}_{i},\mathbf{x}}\right)$

													关联到$\tau \left( {{R}_{i},\mathbf{x}}\right)$

												${k}_{{\mathrm{S}}_{\left( i,\mathbf{x}\right) }} \leftarrow  {k}_{{\mathrm{S}}_{\left( i,\mathbf{x}\right) }} + 1$

												${\varepsilon }_{{\mathrm{S}}_{\left( i,\mathrm{x}\right) }} \leftarrow  {\varepsilon \theta }/\left( {2{m}_{\max } \cdot  {\left( {k}_{{\mathrm{S}}_{\left( i,\mathrm{x}\right) }} + 1\right) }^{1 + \theta }}\right) ,{\beta }_{{\mathrm{S}}_{\left( i,\mathrm{x}\right) }} \leftarrow  \beta /\left( {{2m} \cdot  {\left( {k}_{{\mathrm{S}}_{\left( i,\mathrm{x}\right) }} + 1\right) }^{2}}\right) ;$

												Re-run ${\mathrm{{SVT}}}_{\left( i,\mathbf{x}\right) } \leftarrow  \mathrm{{SVT}}\left( {0,{\varepsilon }_{{\mathrm{S}}_{\left( i,\mathrm{x}\right) }},{f}_{t}\left( \mathbf{I}\right) ,{f}_{t + 1}\left( \mathbf{I}\right) ,\ldots }\right)$ ;

												重新运行${\mathrm{{SVT}}}_{\left( i,\mathbf{x}\right) } \leftarrow  \mathrm{{SVT}}\left( {0,{\varepsilon }_{{\mathrm{S}}_{\left( i,\mathrm{x}\right) }},{f}_{t}\left( \mathbf{I}\right) ,{f}_{t + 1}\left( \mathbf{I}\right) ,\ldots }\right)$；

												SVTStop $\leftarrow$ True;

												SVT停止$\leftarrow$ 真值；

												break;

												中断；

						end

						结束

						if SVTStop = True then

						若SVT停止为真则

									${k}_{\mathrm{C}} \leftarrow  {k}_{\mathrm{C}} + 1$ ; // Re-run the ClipDP with updated ${\left\{  \tau \left( {R}_{i},\mathbf{x}\right) \right\}  }_{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{O}}$

									${k}_{\mathrm{C}} \leftarrow  {k}_{\mathrm{C}} + 1$； // 用更新后的${\left\{  \tau \left( {R}_{i},\mathbf{x}\right) \right\}  }_{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{O}}$重新运行ClipDP

									${\varepsilon }_{\mathrm{C}} \leftarrow  {\varepsilon \theta }/\left( {2 \cdot  {\left( {k}_{\mathrm{C}} + 1\right) }^{1 + \theta }}\right) ;$

									Start $\operatorname{ClipDP}\left( {\mathbf{I},{\varepsilon }_{\mathrm{C}},\theta ,\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)$ ;

																	启动$\operatorname{ClipDP}\left( {\mathbf{I},{\varepsilon }_{\mathrm{C}},\theta ,\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)$；

			while SVTStop = True;

			当SVT停止为真时；

			Use ClipDP to answer $\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)$ ;

			使用ClipDP回答$\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)$；

end

结束

---

<!-- Media -->

be the number of tuples in ${I}_{i}^{\left( t\right) }$ that have been clipped due to $\tau \left( {{R}_{i},\mathbf{x}}\right)$ . Because a tuple may exceed multiple clipping thresholds, the total number of clipped tuples is bounded by their sum:

作为${I}_{i}^{\left( t\right) }$中因$\tau \left( {{R}_{i},\mathbf{x}}\right)$被裁剪的元组数量。由于单个元组可能超出多个裁剪阈值，被裁元组总数受其总和限制：

$$
\operatorname{ClipNum}\left( {{\mathbf{I}}^{\left( t\right) },\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)  \leq  \mathop{\sum }\limits_{{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}}\operatorname{ClipNum}\left( {{I}_{i}^{\left( t\right) },\tau \left( {{R}_{i},\mathbf{x}}\right) }\right) . \tag{12}
$$

The reason we look at $\operatorname{ClipNum}\left( {{I}_{i}^{\left( t\right) },\tau \left( {{R}_{i},\mathbf{x}}\right) }\right)$ instead of the total number of clipped tuples is that the former is entirely decided locally by ${I}_{i}^{\left( t\right) }$ . In particular,it has low sensitivity:

我们考察$\operatorname{ClipNum}\left( {{I}_{i}^{\left( t\right) },\tau \left( {{R}_{i},\mathbf{x}}\right) }\right)$而非被裁元组总数的原因在于，前者完全由${I}_{i}^{\left( t\right) }$本地决定。特别地，其具有低敏感度：

LEMMA 5.4. Fixed any $\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$ and consider any $\mathbf{I} \sim  {\mathbf{I}}^{\prime }$ where the only difference happens in ${R}_{{i}^{\prime }}$ . For any $t \in  {\mathbb{Z}}^{ + }$ ,any $i \neq  {i}^{\prime }$ ,and any $\mathbf{x} \in  {\mathcal{B}}_{Q,i}$ we have

引理5.4. 固定任意$\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$并考虑仅在${R}_{{i}^{\prime }}$处存在差异的$\mathbf{I} \sim  {\mathbf{I}}^{\prime }$。对于任意$t \in  {\mathbb{Z}}^{ + }$、任意$i \neq  {i}^{\prime }$及任意$\mathbf{x} \in  {\mathcal{B}}_{Q,i}$，我们有

$$
\operatorname{ClipNum}\left( {{I}_{i}^{\left( t\right) },\tau \left( {{R}_{i},\mathbf{x}}\right) }\right)  = \operatorname{ClipNum}\left( {{I}_{i}^{\prime \left( t\right) },\tau \left( {{R}_{i},\mathbf{x}}\right) }\right) .
$$

For any $\mathbf{x} \subseteq  {\mathcal{B}}_{Q,{i}^{\prime }}$ ,we have

对于任意$\mathbf{x} \subseteq  {\mathcal{B}}_{Q,{i}^{\prime }}$，我们有

$$
\left| {\operatorname{ClipNum}\left( {{I}_{{i}^{\prime }}^{\left( t\right) },\tau \left( {{R}_{{i}^{\prime }},\mathbf{x}}\right) }\right)  - \operatorname{ClipNum}\left( {{{I}_{{i}^{\prime }}^{\prime }}^{\left( t\right) },\tau \left( {{R}_{{i}^{\prime }},\mathbf{x}}\right) }\right) }\right|  \leq  1.
$$

Given the preceding discussions,for each $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$ ,we can employ SVT to pinpoint the time to double $\tau \left( {{R}_{i},\mathbf{x}}\right)$ ,using the following sensitivity-1 queries

基于前述讨论，对于每个$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$，我们可以采用SVT（稀疏向量技术）来精确测定倍增时间$\tau \left( {{R}_{i},\mathbf{x}}\right)$，具体通过以下敏感度-1查询实现

$$
{f}_{t}\left( \mathbf{I}\right)  = \operatorname{ClipNum}\left( {{I}_{i}^{\left( t\right) },\tau \left( {{R}_{i},\mathbf{x}}\right) }\right)  - \left( {\frac{8}{\varepsilon }\log \left( {2/\beta }\right)  + \frac{8}{\varepsilon }\log \left( {t + 1}\right) }\right) 
$$

for successive $t$ with the stopping threshold $\eta  = 0$ . The negative term in ${f}_{t}\left( \mathbf{I}\right)$ is from Lemma 3.8, which ensures that when the SVT stops,we have $\operatorname{ClipNum}\left( {{I}_{i}^{\left( t\right) },\tau \left( {{R}_{i},\mathbf{x}}\right) }\right)  > 0$ with probability $1 - \beta$ . This means that the current $\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right)$ has exceeded $\tau \left( {{R}_{i},\mathbf{x}}\right)$ so it is time to double $\tau \left( {{R}_{i},\mathbf{x}}\right)$ .

对于连续$t$，当达到停止阈值$\eta  = 0$时。引理3.8中的负项${f}_{t}\left( \mathbf{I}\right)$确保SVT停止时，我们以$1 - \beta$的概率获得$\operatorname{ClipNum}\left( {{I}_{i}^{\left( t\right) },\tau \left( {{R}_{i},\mathbf{x}}\right) }\right)  > 0$。这意味着当前$\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right)$已超过$\tau \left( {{R}_{i},\mathbf{x}}\right)$，因此需要将$\tau \left( {{R}_{i},\mathbf{x}}\right)$倍增。

As we will invoke both ClipDP and SVT multiple times, the privacy budget must be allocated properly. First, we equally split the total privacy budget between them. For ClipDP, it is restarted after each doubling of some $\tau \left( {{R}_{i},\mathbf{x}}\right)$ . For an unbounded stream,this may happen for an unbounded number of times,so we allocate the privacy budget using a telescoping series: ${\varepsilon \theta }/\left( {2{\left( k + 1\right) }^{1 + \theta }}\right)$ is assigned for the $k$ th ClipDP. This ensures that all invocations of ClipDP satisfies $\varepsilon /2$ -DP. For SVT, by Lemma 5.4, the difference between two neighboring streams can only affect one relation, so each relation will get a privacy budget of $\varepsilon /2$ . Each relation has at most ${m}_{\max }$ constraints to monitor using SVT, and there can be an unlimited number of SVTs for each constraint, so we assign a privacy budget of ${\varepsilon \theta }/\left( {2{m}_{\max }{\left( k + 1\right) }^{1 + \theta }}\right)$ to its $k$ th SVT. The detailed algorithm is shown in Algorithm 4, which we denote by DynamicClipDP.

由于需要多次调用ClipDP（裁剪差分隐私）和SVT，必须合理分配隐私预算。首先将总预算均分给两者。对于ClipDP，每次$\tau \left( {{R}_{i},\mathbf{x}}\right)$倍增后重启。对于无限数据流，这种情况可能无限次发生，因此采用望远镜级数分配隐私预算：第$k$次ClipDP分配${\varepsilon \theta }/\left( {2{\left( k + 1\right) }^{1 + \theta }}\right)$，确保所有ClipDP调用满足$\varepsilon /2$-差分隐私。对于SVT，根据引理5.4，相邻数据流差异仅影响一个关系，故每个关系分配$\varepsilon /2$隐私预算。每个关系最多需要监控${m}_{\max }$个约束条件，且每个约束可能进行无限次SVT检测，因此第$k$次SVT分配${\varepsilon \theta }/\left( {2{m}_{\max }{\left( k + 1\right) }^{1 + \theta }}\right)$预算。具体算法见算法4，称为DynamicClipDP。

The privacy and utility guarantees of DynamicClipDP are analyzed in the following theorem.

DynamicClipDP的隐私保障与效用保证将在下文中进行定理分析。

THEOREM 5.5. For any $\varepsilon  > 0$ ,DynamicClipDP preserves $\varepsilon$ -DP. For any $\beta  > 0,\theta  > 0$ ,any $\mathbf{I}$ ,and any $t \in  {\mathbb{Z}}^{ + }$ ,with probability at least $1 - \beta$ ,it returns a $\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)$ such that

定理5.5. 对任意$\varepsilon  > 0$，DynamicClipDP保持$\varepsilon$-差分隐私。对任意$\beta  > 0,\theta  > 0$、任意$\mathbf{I}$及任意$t \in  {\mathbb{Z}}^{ + }$，以至少$1 - \beta$概率返回满足条件的$\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)$：

$$
\left| {\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)  - Q\left( {\mathbf{I}}^{\left( t\right) }\right) }\right|  \leq  O\left( {\frac{1}{\varepsilon \theta } \cdot  {\operatorname{GS}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)  \cdot  {\log }^{{1.5} + \theta }t}\right. 
$$

$$
\left. {\mathop{\sum }\limits_{{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}}\left( {{\log }^{1 + \theta }\left( {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right)  \cdot  \log \left( {\log \left( {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right) /\beta }\right) }\right) }\right) \text{.}
$$

Proof. The privacy guarantee follows from the preceding discussion. Below we analyze the utility.

证明. 隐私保障已由前述讨论得出，以下分析效用性。

First,by Lemma 3.8,for any $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$ and any $k \in  {\mathbb{Z}}^{ + },k$ th ${\operatorname{SVT}}_{\left( i,\mathbf{x}\right) }$ stops at ${t}^{\prime }$ such that with probability $1 - \beta /\left( {{2m} \cdot  {\left( k + 1\right) }^{2}}\right)$ ,

首先根据引理3.8，对任意$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$和第$k \in  {\mathbb{Z}}^{ + },k$次${\operatorname{SVT}}_{\left( i,\mathbf{x}\right) }$，当停止于${t}^{\prime }$时，以$1 - \beta /\left( {{2m} \cdot  {\left( k + 1\right) }^{2}}\right)$概率满足：

$$
{f}_{{t}^{\prime }}\left( G\right)  >  - \frac{{m}_{\max }{\left( k + 1\right) }^{1 + \theta }}{\varepsilon \theta }\left( {{16}\log \left( {{4m}{\left( k + 1\right) }^{2}/\beta }\right)  + {12}\log \left( {t + 1}\right) }\right) ,
$$

$$
{f}_{{t}^{\prime } - 1}\left( G\right)  < \frac{{m}_{\max }{\left( k + 1\right) }^{1 + \theta }}{\varepsilon \theta }\left( {{16}\log \left( {{4m}{\left( k + 1\right) }^{2}/\beta }\right)  + {12}\log \left( {t + 1}\right) }\right) ,
$$

which imply

由此可得

$$
\operatorname{ClipNum}\left( {{I}_{i}^{\left( {t}^{\prime }\right) },{2}^{k}}\right)  > 0\text{,} \tag{13}
$$

$$
\operatorname{ClipNum}\left( {{I}_{i}^{\left( {t}^{\prime } - 1\right) },{2}^{k}}\right)  = O\left( {\frac{{k}^{1 + \theta }}{\varepsilon \theta }\left( {\log \left( {{mk}/\beta }\right)  + \log \left( t\right) }\right) }\right)  \tag{14}
$$

Combining the probabilities across all values of $k$ and all $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$ ,we have,with probability at least $1 - \beta /2$ ,(13) and (14) hold for all instances of SVT.

综合所有$k$取值和$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$的对应概率，当概率不低于$1 - \beta /2$时，(13)和(14)式对所有SVT实例成立。

(13) means that for any time $t \in  {\mathbf{Z}}^{ + }$ ,and any $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$ ,the $\tau \left( {{R}_{i},\mathbf{x}}\right)$ used in $\mathrm{{BM}}$ at time $t$ ,must have

(13)式表明，对于任意时刻$t \in  {\mathbf{Z}}^{ + }$及任意$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$，在时刻$t$使用的$\mathrm{{BM}}$中所采用的$\tau \left( {{R}_{i},\mathbf{x}}\right)$必须满足

$$
\tau \left( {{R}_{i},\mathbf{x}}\right)  \leq  2 \cdot  \operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) , \tag{15}
$$

which implies,it corresponds to $k$ th instance of ${\mathrm{{SVT}}}_{\left( i,\mathbf{x}\right) }$ such that

这意味着，它对应于满足以下条件的${\mathrm{{SVT}}}_{\left( i,\mathbf{x}\right) }$的第$k$个实例：

$$
k \leq  \log \left( {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right)  + 1 \tag{16}
$$

Now, let us analyze the error. For the bias, incorporating (12), (14), and (16), we have, for each time $t \in  {\mathbb{Z}}^{ + }$ ,

现在分析误差项。对于偏差项，结合(12)、(14)和(16)式可得，每个时刻$t \in  {\mathbb{Z}}^{ + }$满足

$$
\operatorname{ClipNum}\left( {{\mathbf{I}}^{\left( t\right) },\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}  }\right)  = O\left( {\frac{1}{\varepsilon \theta }\mathop{\sum }\limits_{{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}}\left( {{\log }^{1 + \theta }\left( {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right) \left( {\log \left( {\log \left( {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right) /\beta }\right)  + \log t}\right) }\right) }\right) 
$$

Further integrating this with the fact that each tuple in ${\mathbf{I}}^{\left( t\right) }$ contributes at most ${\operatorname{GS}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)$ to $Q\left( {\mathbf{I}}^{\left( t\right) }\right)$ ,we get the desired bias.

进一步结合${\mathbf{I}}^{\left( t\right) }$中每个元组对$Q\left( {\mathbf{I}}^{\left( t\right) }\right)$的贡献最多为${\operatorname{GS}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)$这一事实，即可得到所需偏差。

For the noise component,we draw from equation (16) that for any $t \in  {\mathbb{Z}}^{ + }$ ,the $k$ th ClipDP is applied under the condition

对于噪声分量，由方程(16)可知，对任意$t \in  {\mathbb{Z}}^{ + }$，第$k$次ClipDP的应用条件为

$$
k \leq  \mathop{\sum }\limits_{{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}}\log \left( {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right)  + m. \tag{17}
$$

By combining (15) and (17), we have the noise bounded by

联立(15)和(17)式可得噪声上界为

$$
O\left( {\frac{1}{\varepsilon \theta } \cdot  {\operatorname{GS}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)  \cdot  {\log }^{{1.5} + \theta }t\mathop{\sum }\limits_{{\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}}}{\log }^{1 + \theta }\left( {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right)  \cdot  \log \left( {1/\beta }\right) }\right) .
$$

Optimality. The algorithm DynamicClipDP attains an error of $\widetilde{O}\left( {{\mathrm{{GS}}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right) }\right)$ for each time $t \in  {\mathbb{Z}}^{ + }$ even over an unbounded stream. This matches (up to polylogarithmic factors) the best-known result utilizing the maximum frequency information to calibrate the noise [32, 36] in the static setting,which is equivalent to a finite stream of length $t$ and the query result is released only once at the end.

最优性。DynamicClipDP算法在无限数据流中仍能对每个时刻$t \in  {\mathbb{Z}}^{ + }$保持$\widetilde{O}\left( {{\mathrm{{GS}}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right) }\right)$的误差界。这与静态场景下利用最大频率信息校准噪声[32,36]的最佳结果（相差多对数因子）相匹配——该场景等价于长度为$t$的有限流且仅在最终发布一次查询结果。

In the static setting,an $\Omega \left( {{\mathrm{{GS}}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right) }\right)$ lower bound has been established for certain queries $\left\lbrack  {{16},{19},{20}}\right\rbrack$ . For example,for the line-2 query $\left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right) }\right|$ ,it has been shown that no DP mechanism can achieve an error lower than

静态场景中，特定查询$\left\lbrack  {{16},{19},{20}}\right\rbrack$存在$\Omega \left( {{\mathrm{{GS}}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right) }\right)$的下界。例如对于线二查询$\left| {{R}_{1}\left( {A,B}\right)  \bowtie  {R}_{2}\left( {B,C}\right) }\right|$，已有研究表明任何差分隐私机制都无法实现低于下式的误差：

$$
\Omega \left( {\max \left( {\operatorname{mf}\left( {{I}_{1},B}\right) ,\operatorname{mf}\left( {{I}_{2},B}\right) }\right) }\right) ,
$$

and for the $n$ -star query $\left| {{R}_{1}\left( {A,{B}_{1}}\right)  \bowtie  {R}_{2}\left( {A,{B}_{2}}\right)  \bowtie  \cdots  \bowtie  {R}_{n}\left( {A,{B}_{n}}\right) }\right|$ ,there is a lower bound of

对于$n$星型查询$\left| {{R}_{1}\left( {A,{B}_{1}}\right)  \bowtie  {R}_{2}\left( {A,{B}_{2}}\right)  \bowtie  \cdots  \bowtie  {R}_{n}\left( {A,{B}_{n}}\right) }\right|$，存在一个下界为

$$
\Omega \left( {\mathop{\prod }\limits_{i}\operatorname{mf}\left( {{I}_{i},A}\right)  \mid  \mathop{\min }\limits_{i}\operatorname{mf}\left( {{I}_{i},A}\right) }\right) .
$$

Note that ${\operatorname{GS}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)$ is exactly equal to these two expressions in these two cases. However, $\left\lbrack  {{19},{20}}\right\rbrack$ show for other queries like $n$ -line path counting queries with $n \geq  3$ ,a better error can be achieved in the static setting. How to achieve those errors in the dynamic setting is still an open problem.

注意在以下两种情况下，${\operatorname{GS}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)$完全等同于这两个表达式。然而$\left\lbrack  {{19},{20}}\right\rbrack$表明，对于其他查询（如具有$n \geq  3$的$n$线性路径计数查询），在静态设置中可实现更优误差。如何在动态设置中达成这些误差仍是未解难题。

<!-- Media -->

<!-- figureText: Input Estimating clipping thresholds ClipDP Clipping thresholds SVT with ${\epsilon \theta }/\left( {2 \cdot  {m}_{\max } \cdot  {4}^{1 + \theta }}\right)$ Clipping SVT with ${\mathrm{{GS}}}_{Q}$ ${\epsilon \theta }/\left( {2 \cdot  {m}_{\mathrm{{max}}} \cdot  {3}^{1 + \theta }}\right)$ Clipped instance: $\overline{\mathbf{I}}$ SVT with Binary ${\epsilon \theta }/\left( {2 \cdot  {m}_{\max } \cdot  {3}^{1 + \theta }}\right)$ Mechanism SOL Updating clipping $\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \overline{\mathbf{I}}\right)$ ClipDP with ClipDP with $\Delta {Q}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \overline{\mathbf{I}}\right)$ ${\epsilon \theta }/\left( {2 \cdot  {5}^{1 + \theta }}\right)$ ${\epsilon \theta }/\left( {2 \cdot  {6}^{1 + \theta }}\right)$ $\widetilde{Q}\left( {\mathbf{I}}^{\left( 8\right) }\right)$ $\widetilde{Q}\left( {\mathbf{I}}^{\left( 9\right) }\right)$ $\widetilde{Q}\left( {\mathbf{I}}^{\left( {10}\right) }\right)$ $\widetilde{Q}\left( {\mathbf{I}}^{\left( {11}\right) }\right)$ $\widetilde{Q}\left( {\mathbf{I}}^{\left( {12}\right) }\right)$ $\widetilde{Q}\left( {\mathbf{I}}^{\left( t\right) }\right)$ Timeline SVT with SVT with $Q$ $\left. {{\epsilon \theta }/\left( {2 \cdot  {m}_{\mathrm{{max}}} \cdot  {2}^{1 + \theta }}\right) }\right.$ ${\epsilon \theta }/\left( {2 \cdot  {m}_{\max } \cdot  {3}^{1 + \theta }}\right)$ SVT with ${\epsilon \theta }/\left( {2 \cdot  {m}_{\mathrm{{max}}} \cdot  {2}^{1 + \theta }}\right)$ $\beta$ SVT with ${\epsilon \theta }/\left( {2 \cdot  {m}_{\mathrm{{max}}} \cdot  {2}^{1 + \theta }}\right)$ ClipDP with ClipDP with ClipDP with ${\epsilon \theta }/\left( {2 \cdot  {2}^{1 + \theta }}\right)$ ${\epsilon \theta }/\left( {2 \cdot  {3}^{1 + \theta }}\right)$ ${\epsilon \theta }/\left( {2 \cdot  {4}^{1 + \theta }}\right)$ $\mathbf{I}$ $\widetilde{Q}\left( {\mathbf{I}}^{\left( 1\right) }\right)$ $\widetilde{Q}\left( {\mathbf{I}}^{\left( 2\right) }\right)$ $\widetilde{Q}\left( {\mathbf{I}}^{\left( 3\right) }\right)$ $\widetilde{Q}\left( {\mathbf{I}}^{\left( 4\right) }\right)$ $\widetilde{Q}\left( {\mathbf{I}}^{\left( 5\right) }\right)$ $\widetilde{Q}\left( {\mathbf{I}}^{\left( 6\right) }\right)$ $\widetilde{Q}\left( {\mathbf{I}}^{\left( 7\right) }\right)$ -->

<img src="https://cdn.noedgeai.com/0196599c-190a-7a73-a499-f2871a0182b1_18.jpg?x=141&y=260&w=1274&h=484&r=0"/>

Fig. 5. Implementation of our algorithm

图5. 算法实现方案

<!-- Media -->

## 6 EXTENSIONS

## 6 扩展应用

Self-joins. For a query with self-joins, we can treat the multiple occurrences of a relation in the query as copies of the same relation and then invoke our algorithm. Accordingly, every incoming tuple will be treated as an insertion to each of the copies. Note that this has an impact on privacy, since two neighboring instances now have a distance of $\ell$ ,where $\ell$ is the maximum number of occurrences of any relation in the query. Therefore, we need to run our mechanism with a privacy budget of $\varepsilon /\ell$ . The error will then also grow by a factor of $\ell$ accordingly.

自连接处理。对于含自连接的查询，可将关系中多次出现的表视为同一关系的副本并调用算法。因此每个新增元组都将被视为对所有副本的插入操作。需注意这会改变隐私距离——相邻实例的距离现为$\ell$（即查询中任一关系的最大出现次数），故需使用$\varepsilon /\ell$隐私预算运行机制，误差也将相应增长$\ell$倍。

Example 6.1. Consider counting the number of triangles in a directed graph, which can be written as a self-join:

示例6.1. 考虑有向图中三角形数量的计数查询，该查询可表示为自连接形式：

$$
{Q}_{\nabla } = \left| {\operatorname{Edge}\left( {A,B}\right)  \bowtie  \operatorname{Edge}\left( {B,C}\right)  \bowtie  \operatorname{Edge}\left( {C,A}\right) }\right| . \tag{18}
$$

We first rewrite the query into the standard triangle query in (1) by instituting three distinct relations ${R}_{1},{R}_{2}$ ,and ${R}_{3}$ ,all of which are copies of Edge. For each incoming tuple $e$ ,we will insert $e$ into each of ${R}_{1},{R}_{2},{R}_{3}$ . We have $\ell  = 3$ for this query.

首先通过创建三个Edge关系的副本${R}_{1},{R}_{2}$、${R}_{3}$，将查询重写为标准三角形查询(1)。每个输入元组$e$将被同时插入${R}_{1},{R}_{2},{R}_{3}$。本查询中$\ell  = 3$。

Predicates. As shown in prior work $\left\lbrack  {{32},{36}}\right\rbrack$ ,the presence of predicates does not increase the sensitivity of the query. Therefore,we can compute ${\mathrm{{GS}}}_{Q}$ of the query as before,while ignoring the predicates. In addition, it is clear that the predicates do not affect the monotonicity and supermodularity of the query. The only change is that, in line 3 of BM (Algorithm 2) when we evaluate the true delta query $\Delta {Q}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \mathbf{I}\right)$ ,we need to apply the predicates.

谓词处理。如先前研究$\left\lbrack  {{32},{36}}\right\rbrack$所示，谓词存在不会增加查询敏感度。因此可沿用原有方式计算${\mathrm{{GS}}}_{Q}$，忽略谓词影响。需注意谓词不影响查询的单调性与超模性，唯一变化在于：执行BM算法（算法2）第3行计算真实增量查询$\Delta {Q}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \mathbf{I}\right)$时，需应用谓词条件。

## 7 IMPLEMENTATION

## 7 系统实现

Our algorithm is versatile and can be implemented on top of any SQL query engine, with architecture illustrated in Figure 5. The algorithm consists of two main components. The first component is to dynamically estimate the clipping thresholds $\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$ . During this phase, $\left| {\mathcal{B}}_{Q}\right|$ instances of SVT are executed concurrently where each instance is designated to detect the time step to double the value for a unique $\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$ . Subsequent to each doubling,the SVT is re-initialized with a diminished privacy budget.

本算法具有通用性，可在任何SQL查询引擎上实现（架构见图5）。系统包含两大核心组件：动态估计截断阈值$\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$的组件（该阶段并行执行$\left| {\mathcal{B}}_{Q}\right|$个SVT实例，每个实例负责检测特定$\left( {i,\mathbf{x}}\right)  \in  {\mathcal{B}}_{Q}$值的翻倍时机），以及每次阈值翻倍后使用递减隐私预算重新初始化SVT的组件。

The second part of the algorithm is designed to execute ClipDP utilizing the dynamically estimated clipping thresholds $\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$ to answer the query at each time step. In this phase,we initially clip the instance $\mathrm{I}$ to $\overline{\mathrm{I}}$ and compute ${\mathrm{{GS}}}_{Q}$ using these thresholds $\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$ . Subsequently, both $\overline{\mathrm{I}}$ and ${\mathrm{{GS}}}_{Q}$ are inputted into the binary mechanism. Within the binary mechanism,at each time $t,\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \overline{\mathrm{I}}\right)$ is formulated as a multi-way join counting query. Assuming the tuple ${e}^{\left( t\right) }$ is from the relation ${R}_{i}$ ,then,

算法的第二部分旨在利用动态估计的裁剪阈值$\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$执行ClipDP，以响应每个时间步长的查询。在此阶段，我们首先将实例$\mathrm{I}$裁剪至$\overline{\mathrm{I}}$，并使用这些阈值$\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$计算${\mathrm{{GS}}}_{Q}$。随后，将$\overline{\mathrm{I}}$和${\mathrm{{GS}}}_{Q}$同时输入二进制机制。在该机制中，每个时刻$t,\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \overline{\mathrm{I}}\right)$都被构建为多路连接计数查询。假设元组${e}^{\left( t\right) }$来自关系${R}_{i}$，则

$$
\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \overline{\mathbf{I}}\right)  = \left| {{e}^{\left( t\right) } \bowtie  \left( {{ \bowtie  }_{j \neq  i}{I}_{j}^{\left( t - 1\right) }}\right) }\right| .
$$

In our implementation, we frame the above query as a SQL query. It is worth mentioning that several techniques, as cited [7, 11, 54], can evaluate the above query in more efficient way. Following this,we can conveniently obtain $\Delta {Q}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \overline{\mathrm{I}}\right)$ using these instances of $\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \overline{\mathrm{I}}\right)$ . Furthermore,as argued in [10],we only need to maintain at most $\log \left( t\right)$ number of $\Delta {Q}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \overline{\mathrm{I}}\right)$ at each time $t$ . Importantly,every time there is an update in $\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$ ,a restart is required for this process.

在实现中，我们将上述查询构造成SQL查询。值得一提的是，文献[7,11,54]中引用的多项技术能以更高效的方式评估该查询。据此，我们可以便捷地利用这些$\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \overline{\mathrm{I}}\right)$实例获取$\Delta {Q}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \overline{\mathrm{I}}\right)$。此外，如文献[10]所述，每个时刻$t$最多只需维护$\log \left( t\right)$个$\Delta {Q}^{\left\lbrack  {t}_{1},{t}_{2}\right\rbrack  }\left( \overline{\mathrm{I}}\right)$。值得注意的是，每当$\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$发生更新时，该流程都需要重启。

Optimization. For conceptual simplicity,after doubling a $\tau \left( {{R}_{i},\mathbf{x}}\right)$ at time $t$ ,we restart both the SVT associated with $\tau \left( {{R}_{i},\mathbf{x}}\right)$ and the ClipDP mechanism from the beginning of the stream. However, in the actual implementation, this can be avoided by utilizing the information gathered in preceding steps. First, the new instance of SVT needs to track the number of tuples that ought to be clipped at the current time, with the updated clipping threshold. Instead of rewinding the entire instance, a more efficient strategy is to ascertain whether the tuples, previously clipped with the old threshold, will be maintained under the new threshold. Second, during the re-instantiation of the binary tree,we can compress the entire time frame $\left\lbrack  {1,t}\right\rbrack$ into a single time step,given that there is no requirement to answer queries prior to time $t$ .

优化方案。为简化概念，当在时刻$t$对$\tau \left( {{R}_{i},\mathbf{x}}\right)$进行倍增后，我们会从头重启与$\tau \left( {{R}_{i},\mathbf{x}}\right)$关联的SVT及ClipDP机制。但在实际实施中，可通过利用前期步骤收集的信息避免此操作。首先，更新后的SVT实例需追踪当前时刻需裁剪的元组数量（采用新裁剪阈值）。相较于回滚整个实例，更高效的策略是确认先前按旧阈值裁剪的元组是否会被新阈值保留。其次，在二叉树重新实例化时，若无需响应时刻$t$之前的查询，可将整个时间范围$\left\lbrack  {1,t}\right\rbrack$压缩为单一时间步长。

Computational Complexity and Space Usage. By maintaining all $\operatorname{freq}\left( {{I}_{i}^{\left( t\right) },a}\right)  > 0$ for $\left( {i,\mathbf{x}}\right)  \in  \mathcal{B}$ and $a \in  \mathbf{{dom}}\left( \mathbf{x}\right)$ ,which requires at most linear space cost,we only require constant running time to execute SVT's and do the clipping at each time step. Furthermore, as corroborated in the proof of Theorem 5.5,for any time $t \in  {\mathbb{Z}}^{ + }$ ,we only need to store at most $\widetilde{O}\left( 1\right)$ clipped tuples,implying a logarithmic running time for restarting an SVT. Given that there are at most $O\left( {\log \left( t\right) }\right)$ restarts of SVT before reaching time $t$ ,the amortized cost of these restarts becomes $o\left( 1\right)$ . In the binary mechanism,aside from computing $\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \overline{\mathrm{I}}\right)$ ,only a constant running time and $\log \left( t\right)$ memory are used at any given time $t$ . Every re-building process requires the computation of the query update, influenced by some previously clipped tuples. Since each tuple is inserted into the database only once,the amortized cost for any time $t$ aligns with $\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( I\right)$ ,equivalent to the non-private setting. Above all,in addition to computing $\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \overline{\mathbf{I}}\right)$ ,which is needed even for non-private continual observation, our system incurs a constant amortized computational overhead at each time step with linear space usage.

计算复杂度与空间占用。通过维护所有$\operatorname{freq}\left( {{I}_{i}^{\left( t\right) },a}\right)  > 0$用于$\left( {i,\mathbf{x}}\right)  \in  \mathcal{B}$和$a \in  \mathbf{{dom}}\left( \mathbf{x}\right)$（最多仅需线性空间成本），我们仅需恒定运行时间即可执行SVT（稀疏向量技术）并在每个时间步进行剪裁。此外，如定理5.5证明所示，对于任意时刻$t \in  {\mathbb{Z}}^{ + }$，我们最多只需存储$\widetilde{O}\left( 1\right)$个剪裁后的元组，这意味着重启SVT具有对数级时间复杂度。考虑到在到达时刻$t$前最多发生$O\left( {\log \left( t\right) }\right)$次SVT重启，这些重启的摊还成本降至$o\left( 1\right)$。在二元机制中，除计算$\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \overline{\mathrm{I}}\right)$外，任意时刻$t$仅需恒定运行时间和$\log \left( t\right)$内存。每次重建过程需计算受先前剪裁元组影响的查询更新。由于每个元组仅被插入数据库一次，任意时刻$t$的摊还成本与$\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( I\right)$一致，等同于非隐私保护场景。最重要的是，除计算$\Delta {Q}^{\left\lbrack  t,t\right\rbrack  }\left( \overline{\mathbf{I}}\right)$（非隐私持续观察也需此步骤）外，我们的系统在每个时间步仅产生恒定摊余计算开销，同时保持线性空间占用。

## 8 EXPERIMENTS

## 8 实验

In this section, we compared our mechanism in Section 5 with the following three baselines on both graph pattern counting queries and general multi-way join counting queries in the dynamic setting.

本节将第五章提出的机制与以下三种基线方法进行比较，评估场景涵盖动态环境下的图模式计数查询和通用多路连接计数查询。

Composition: We employed advanced composition [26] to allocate the privacy budget ${\varepsilon }^{\prime }$ ,where $\varepsilon  =$ $\sqrt{{2T}\ln \left( {1/\delta }\right) }{\varepsilon }^{\prime } + T{\varepsilon }^{\prime }\left( {{e}^{{\varepsilon }^{\prime }} - 1}\right)$ to each ${\mathbf{I}}^{\left( t\right) }$ ,on which we use the residual sensitivity (RS) mechanism [19, 20], the state-of-the-art algorithm for static multi-way join counting queries under DP.

组合策略：采用高级组合定理[26]分配隐私预算${\varepsilon }^{\prime }$（其中$\varepsilon  =$$\sqrt{{2T}\ln \left( {1/\delta }\right) }{\varepsilon }^{\prime } + T{\varepsilon }^{\prime }\left( {{e}^{{\varepsilon }^{\prime }} - 1}\right)$分配给每个${\mathbf{I}}^{\left( t\right) }$），并基于残差敏感度(RS)机制[19,20]——该机制是差分隐私(DP)下静态多路连接计数查询的最先进算法。

<!-- Media -->

<table><tr><td rowspan="2">Dataset</td><td colspan="3">Simulated-temporal networks.</td><td colspan="3">Real-temporal networks.</td></tr><tr><td>RoadnetUS</td><td>DuWiki</td><td>CaWiki</td><td>Dblp</td><td>Flickr</td><td>StackOverflow</td></tr><tr><td>Number of edges</td><td>${4.08} \times  {10}^{7}$</td><td>${1.08} \times  {10}^{7}$</td><td>${1.14} \times  {10}^{7}$</td><td>${9.37} \times  {10}^{6}$</td><td>${4.27} \times  {10}^{6}$</td><td>${9.22} \times  {10}^{6}$</td></tr><tr><td>Maximum degree</td><td>16</td><td>167</td><td>442</td><td>122</td><td>76</td><td>158</td></tr></table>

<table><tbody><tr><td rowspan="2">数据集</td><td colspan="3">模拟时序网络</td><td colspan="3">真实时序网络</td></tr><tr><td>美国道路网(RoadnetUS)</td><td>杜维基(DuWiki)</td><td>加州维基(CaWiki)</td><td>学术文献网(Dblp)</td><td>图片分享平台(Flickr)</td><td>技术问答社区(StackOverflow)</td></tr><tr><td>边数量</td><td>${4.08} \times  {10}^{7}$</td><td>${1.08} \times  {10}^{7}$</td><td>${1.14} \times  {10}^{7}$</td><td>${9.37} \times  {10}^{6}$</td><td>${4.27} \times  {10}^{6}$</td><td>${9.22} \times  {10}^{6}$</td></tr><tr><td>最大度数</td><td>16</td><td>167</td><td>442</td><td>122</td><td>76</td><td>158</td></tr></tbody></table>

Table 4. Network datasets used in the experiments.

表4. 实验所用的网络数据集

<!-- figureText: Edge(A,B) Edge(A,B) Edge(A,D) F ① $\operatorname{Edge}\left( {A,F}\right)$ Edge(A,B) Edge (A, C) Edge (A, D) ⑥ Edge (A, C) ⑩ ${Q}_{3 * }$ ${Q}_{4 * }$ Graph Pattern Counting Queries S(SK) PS (SK, PK) $\mathrm{L}\left( {\mathrm{{SK}},\mathrm{{PK}},\mathrm{{OK}}}\right)$ O(OK) ${Q}_{9}$ TPC-H Queries Edge(A,B) Edge (A, B) Edge(C,D) Edge(B,C) Edge (B, C) Edge (C, A Edge (B, C) ${Q}_{2 - }$ ${Q}_{3 - }$ ${Q}_{ \bigtriangleup  }$ C(CK) O(CK, OK) $\mathrm{L}\left( {\mathrm{{CK}},\mathrm{{SK}}}\right)$ S(SK) ${Q}_{7}$ -->

<img src="https://cdn.noedgeai.com/0196599c-190a-7a73-a499-f2871a0182b1_20.jpg?x=194&y=513&w=1172&h=329&r=0"/>

Fig. 6. The query structures.

图6. 查询结构示意图

<!-- Media -->

Binary mechanism. This is the mechanism described in Section 4, which requires the frequency constraints $\left\{  {\overset{⏜}{mf}\left( {{R}_{i},\mathbf{x}}\right) }\right\}$ . In our experiments,all datasets have a temporal domain ranging between 2 million and 50 million with a maximum frequency of up to ${2}^{10}$ ,so we set each $\widehat{mf}\left( {{R}_{i},\mathbf{x}}\right)$ to ${2}^{15}$ .

二进制机制。这是第4节描述的机制，需要满足频率约束$\left\{  {\overset{⏜}{mf}\left( {{R}_{i},\mathbf{x}}\right) }\right\}$。实验中所有数据集的时间域范围在200万至5000万之间，最高频率可达${2}^{10}$，因此我们将每个$\widehat{mf}\left( {{R}_{i},\mathbf{x}}\right)$设为${2}^{15}$。

Clipping mechanism. This is the clipping mechanism discussed in Section 5.1 but with fixed clipping thresholds $\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$ . As there is no method prior to this work on how to determine the clipping threshold,we randomly selected a value from $\left\{  {2,4,8,\ldots ,{2}^{15}}\right\}$ and set all $\tau \left( {{R}_{i},\mathbf{x}}\right)$ to that value.

截断机制。这是5.1节讨论的固定阈值截断机制$\left\{  {\tau \left( {{R}_{i},\mathbf{x}}\right) }\right\}$。由于此前没有确定截断阈值的方法，我们从$\left\{  {2,4,8,\ldots ,{2}^{15}}\right\}$中随机选取数值，并将所有$\tau \left( {{R}_{i},\mathbf{x}}\right)$设为该值。

### 8.1 Setup

### 8.1 实验设置

Query. For graph pattern counting queries, we used five queries: length-2 path counting query ${Q}_{2 - }$ ,length-3 path counting query ${Q}_{3 - }$ ,triangle counting query ${Q}_{\nabla },3$ -star counting query ${Q}_{3 * }$ , and 4-star counting query ${Q}_{4 * }$ . Note that for all graph pattern counting queries,we use predicates to avoid duplicate-counting. For example,we equip ${Q}_{\nabla }$ defined in (18) with $\mathrm{A} < \mathrm{B}$ and $\mathrm{A} < \mathrm{B}$ . For multi-way join counting queries,we used two queries from the TPC-H benchmark, ${Q}_{7}$ and ${Q}_{9}$ , omitting the projection and group-by clauses. In addition, to avoid exceedingly small join results, we excluded the predicates for ${Q}_{9}$ . For ${Q}_{7}$ ,we maintain the predicate on shipdate within a four-year time range. Furthermore, we also utilized the primary key information in the TPC-H schemas by setting $\overset{⏜}{mf}\left( {C,{CK}}\right) ,\overset{⏜}{mf}\left( {S,{SK}}\right)$ ,and $\overset{⏜}{mf}\left( {O,{OK}}\right)$ to be 1 .

查询。针对图模式计数查询，我们使用五种查询：长度-2路径计数${Q}_{2 - }$、长度-3路径计数${Q}_{3 - }$、三角形计数${Q}_{\nabla },3$、星形结构计数${Q}_{3 * }$及4-星计数${Q}_{4 * }$。所有图模式查询均采用谓词避免重复计数，例如为式(18)定义的${Q}_{\nabla }$配置$\mathrm{A} < \mathrm{B}$和$\mathrm{A} < \mathrm{B}$。多路连接计数查询采用TPC-H基准中的${Q}_{7}$和${Q}_{9}$，省略投影与分组子句。为避免连接结果过小，移除了${Q}_{9}$的谓词。对${Q}_{7}$保留四年时间范围的shipdate谓词。同时利用TPC-H模式中的主键信息，将$\overset{⏜}{mf}\left( {C,{CK}}\right) ,\overset{⏜}{mf}\left( {S,{SK}}\right)$和$\overset{⏜}{mf}\left( {O,{OK}}\right)$设为1。

Dataset. For graph pattern counting queries, we used 6 real world networks datasets categorized into two classes based on the presence of timestamps on each edge, referred to as real-temporal networks and simulated-temporal networks. For simulated-temporal networks, edges were allocated a random order. We used three datasets. RoadnetUS corresponds to the road network of the USA and was used in the 9th DIMACS Implementation Challenge [37]. CaWiki and DuWiki characterize the hyperlink networks between Wikipedia articles written in the Catalan and Dutch languages respectively [37]. For real-temporal network, we selected three datasets, retaining their intrinsic temporal sequence to organize the edges. Dblp stands for the collaboration graph of authors contributing to the DBLP computer science bibliography [40]. Flickr is the social network of Flickr users [43]. StackOverflow is the interaction network of users from the Stack Exchange website, Stack Overflow [38]. All the aforementioned networks are undirected. Among them, StackOverflow was collected from SNAP [39] while the remaining were from KONECT [37]. To avoid too much error, we have deleted the top 5% nodes with the highest degrees. Detailed information regarding these datasets can be found in Table 4. For TPC-H data, we used the dataset of scale 10 , which encompasses about 75 million tuples. Given the absence of timestamps on these tuples, we also arranged them with a random order with tuples in come before.

数据集。图模式计数查询使用6个真实网络数据集，根据边时间戳分为实时序网络和模拟时序网络两类。模拟时序网络采用随机边序，包含三个数据集：RoadnetUS对应美国道路网络（第9届DIMACS挑战赛[37]）、CaWiki和DuWiki分别表示加泰罗尼亚语和荷兰语维基百科超链接网络[37]。实时序网络选用DBLP（计算机科学文献合作图[40]）、Flickr（用户社交网络[43]）和StackOverflow（Stack Exchange用户交互网络[38]），均保留原始时序。所有网络均为无向图，其中StackOverflow来自SNAP[39]，其余源自KONECT[37]。为避免误差过大，已删除度值最高的前5%节点。数据集详情见表4。TPC-H数据采用规模10的约7500万条元组数据集，由于缺乏时间戳，同样采用随机顺序排列。

<!-- Media -->

<table><tr><td colspan="2" rowspan="3">Dataset</td><td colspan="6">Simulated-temporal data</td><td colspan="6">Real-temporal data</td></tr><tr><td colspan="2">RoadnetUS</td><td colspan="2">$\mathbf{{DuWiki}}$</td><td colspan="2">$\mathbf{{CaWiki}}$</td><td colspan="2">$\mathbf{{Dblp}}$</td><td colspan="2">Flickr</td><td colspan="2">StackOverflow</td></tr><tr><td>RE(%)</td><td>RT(s)</td><td>RE(%)</td><td>RT(s)</td><td>RE(%)</td><td>RT(s)</td><td>RE(%)</td><td>RT(s)</td><td>RE(%)</td><td>RT(s)</td><td>RE(%)</td><td>RT(s)</td></tr><tr><td rowspan="4">${Q}_{2 - }$</td><td rowspan="4">Ours BM CM RS</td><td>0.0065</td><td>6.79</td><td>0.172</td><td>2.55</td><td>0.254</td><td>2.98</td><td>0.119</td><td>3.11</td><td>0.34</td><td>1.17</td><td>0.19</td><td>1.91</td></tr><tr><td>3.08</td><td>8.54</td><td>2.09</td><td>3.84</td><td>0.882</td><td>2.42</td><td>3.13</td><td>3.22</td><td>15.4</td><td>1.41</td><td>15.1</td><td>2.78</td></tr><tr><td>0.107</td><td>7.03</td><td>3.54</td><td>3.47</td><td>14.8</td><td>3.49</td><td>1.95</td><td>3.34</td><td>2.21</td><td>1.38</td><td>2.89</td><td>3.17</td></tr><tr><td>${4.25} \times  {10}^{4}$</td><td>42.2</td><td>${9.32} \times  {10}^{3}$</td><td>14.4</td><td>${4.84} \times  {10}^{3}$</td><td>12.3</td><td>${1.19} \times  {10}^{4}$</td><td>13.3</td><td>${3.79} \times  {10}^{4}$</td><td>9.38</td><td>${1.2} \times  {10}^{4}$</td><td>13.5</td></tr><tr><td rowspan="4">${Q}_{3 - }$</td><td rowspan="4">Ours BM CM RS</td><td>0.145</td><td>17.3</td><td>3.7</td><td>6.82</td><td>7.6</td><td>5.55</td><td>3.61</td><td>11.2</td><td>29.7</td><td>6.6</td><td>3.74</td><td>12.2</td></tr><tr><td>${6.06} \times  {10}^{4}$</td><td>14.8</td><td>${6.03} \times  {10}^{3}$</td><td>10.9</td><td>${1.06} \times  {10}^{3}$</td><td>5.39</td><td>${9.95} \times  {10}^{3}$</td><td>11.7</td><td>${1.6} \times  {10}^{5}$</td><td>6.8</td><td>${1.03} \times  {10}^{4}$</td><td>10.8</td></tr><tr><td>212</td><td>18.7</td><td>54.5</td><td>10.7</td><td>52.4</td><td>5.74</td><td>69.5</td><td>12.3</td><td>${1.37} \times  {10}^{3}$</td><td>6.91</td><td>63.2</td><td>10.9</td></tr><tr><td>${9.42} \times  {10}^{9}$</td><td>160</td><td>${1.49} \times  {10}^{8}$</td><td>109</td><td>${2.73} \times  {10}^{7}$</td><td>186</td><td>${1.63} \times  {10}^{8}$</td><td>82.4</td><td>${1.81} \times  {10}^{9}$</td><td>38.2</td><td>${1.75} \times  {10}^{8}$</td><td>80.2</td></tr><tr><td rowspan="4">${Q}_{\nabla }$</td><td rowspan="4">Ours BM CM RS</td><td>1.37</td><td>12.4</td><td>4.41</td><td>6.92</td><td>7.63</td><td>11.4</td><td>1.14</td><td>9.01</td><td>30.1</td><td>6.78</td><td>0.318</td><td>12.2</td></tr><tr><td>272</td><td>13.1</td><td>12.2</td><td>10.3</td><td>4.6</td><td>9.51</td><td>5.04</td><td>12.5</td><td>375</td><td>6.35</td><td>1.21</td><td>11</td></tr><tr><td>10.7</td><td>14.3</td><td>11.3</td><td>13.7</td><td>23.9</td><td>7.46</td><td>3.71</td><td>13.3</td><td>32</td><td>7.17</td><td>5.18</td><td>12.2</td></tr><tr><td>${1.37} \times  {10}^{12}$</td><td>114</td><td>${6.37} \times  {10}^{9}$</td><td>113</td><td>${3.19} \times  {10}^{9}$</td><td>200</td><td>${3.14} \times  {10}^{9}$</td><td>78.4</td><td>${9.03} \times  {10}^{10}$</td><td>42.2</td><td>${8.57} \times  {10}^{8}$</td><td>76</td></tr><tr><td rowspan="4">${Q}_{3 * }$</td><td rowspan="4">Ours BM CM RS</td><td>0.00302</td><td>8.23</td><td>0.377</td><td>2.07</td><td>0.554</td><td>1.15</td><td>0.263</td><td>1.69</td><td>2.16</td><td>1.75</td><td>0.337</td><td>1.9</td></tr><tr><td>${2.61} \times  {10}^{3}$</td><td>9.19</td><td>${1.21} \times  {10}^{4}$</td><td>1.54</td><td>210</td><td>1.31</td><td>${1.45} \times  {10}^{3}$</td><td>1.82</td><td>${5.43} \times  {10}^{4}$</td><td>2.38</td><td>${1.38} \times  {10}^{3}$</td><td>2.22</td></tr><tr><td>11.6</td><td>8.21</td><td>33.1</td><td>1.51</td><td>34.3</td><td>1.22</td><td>33</td><td>1.95</td><td>149</td><td>2.01</td><td>34.8</td><td>1.94</td></tr><tr><td>${3.63} \times  {10}^{8}$</td><td>72.3</td><td>${2.34} \times  {10}^{7}$</td><td>34.3</td><td>${4.85} \times  {10}^{6}$</td><td>33</td><td>${3.4} \times  {10}^{7}$</td><td>30.9</td><td>${3.64} \times  {10}^{8}$</td><td>20.6</td><td>${3.34} \times  {10}^{7}$</td><td>30.3</td></tr><tr><td rowspan="4">${Q}_{4 * }$</td><td rowspan="4">Ours BM CM RS</td><td>0.0262</td><td>12</td><td>1.89</td><td>3.02</td><td>2.83</td><td>2.1</td><td>1.15</td><td>2.45</td><td>12.6</td><td>2.57</td><td>1.36</td><td>3</td></tr><tr><td>${4.12} \times  {10}^{7}$</td><td>12.3</td><td>${1.4} \times  {10}^{6}$</td><td>2.25</td><td>${1.32} \times  {10}^{5}$</td><td>2.05</td><td>${3.55} \times  {10}^{6}$</td><td>2.74</td><td>${2.11} \times  {10}^{8}$</td><td>2.36</td><td>${2.16} \times  {10}^{6}$</td><td>2.93</td></tr><tr><td>${2.46} \times  {10}^{4}$</td><td>9.77</td><td>${1.07} \times  {10}^{3}$</td><td>2.28</td><td>104</td><td>1.62</td><td>${2.56} \times  {10}^{3}$</td><td>2.58</td><td>${9.43} \times  {10}^{4}$</td><td>2.79</td><td>${2.31} \times  {10}^{3}$</td><td>2.82</td></tr><tr><td>${1.03} \times  {10}^{14}$</td><td>253</td><td>${3.56} \times  {10}^{11}$</td><td>133</td><td>${2.03} \times  {10}^{10}$</td><td>133</td><td>${7.31} \times  {10}^{11}$</td><td>122</td><td>${9.25} \times  {10}^{12}$</td><td>83.1</td><td>${3.92} \times  {10}^{11}$</td><td>119</td></tr></table>

<table><tbody><tr><td colspan="2" rowspan="3">数据集</td><td colspan="6">模拟时序数据</td><td colspan="6">真实时序数据</td></tr><tr><td colspan="2">美国路网(RoadnetUS)</td><td colspan="2">$\mathbf{{DuWiki}}$</td><td colspan="2">$\mathbf{{CaWiki}}$</td><td colspan="2">$\mathbf{{Dblp}}$</td><td colspan="2">Flickr</td><td colspan="2">StackOverflow</td></tr><tr><td>相对误差(RE%)</td><td>响应时间(RT秒)</td><td>相对误差(RE%)</td><td>响应时间(RT秒)</td><td>相对误差(RE%)</td><td>响应时间(RT秒)</td><td>相对误差(RE%)</td><td>响应时间(RT秒)</td><td>相对误差(RE%)</td><td>响应时间(RT秒)</td><td>相对误差(RE%)</td><td>响应时间(RT秒)</td></tr><tr><td rowspan="4">${Q}_{2 - }$</td><td rowspan="4">本方法 BM CM RS</td><td>0.0065</td><td>6.79</td><td>0.172</td><td>2.55</td><td>0.254</td><td>2.98</td><td>0.119</td><td>3.11</td><td>0.34</td><td>1.17</td><td>0.19</td><td>1.91</td></tr><tr><td>3.08</td><td>8.54</td><td>2.09</td><td>3.84</td><td>0.882</td><td>2.42</td><td>3.13</td><td>3.22</td><td>15.4</td><td>1.41</td><td>15.1</td><td>2.78</td></tr><tr><td>0.107</td><td>7.03</td><td>3.54</td><td>3.47</td><td>14.8</td><td>3.49</td><td>1.95</td><td>3.34</td><td>2.21</td><td>1.38</td><td>2.89</td><td>3.17</td></tr><tr><td>${4.25} \times  {10}^{4}$</td><td>42.2</td><td>${9.32} \times  {10}^{3}$</td><td>14.4</td><td>${4.84} \times  {10}^{3}$</td><td>12.3</td><td>${1.19} \times  {10}^{4}$</td><td>13.3</td><td>${3.79} \times  {10}^{4}$</td><td>9.38</td><td>${1.2} \times  {10}^{4}$</td><td>13.5</td></tr><tr><td rowspan="4">${Q}_{3 - }$</td><td rowspan="4">本方法 BM CM RS</td><td>0.145</td><td>17.3</td><td>3.7</td><td>6.82</td><td>7.6</td><td>5.55</td><td>3.61</td><td>11.2</td><td>29.7</td><td>6.6</td><td>3.74</td><td>12.2</td></tr><tr><td>${6.06} \times  {10}^{4}$</td><td>14.8</td><td>${6.03} \times  {10}^{3}$</td><td>10.9</td><td>${1.06} \times  {10}^{3}$</td><td>5.39</td><td>${9.95} \times  {10}^{3}$</td><td>11.7</td><td>${1.6} \times  {10}^{5}$</td><td>6.8</td><td>${1.03} \times  {10}^{4}$</td><td>10.8</td></tr><tr><td>212</td><td>18.7</td><td>54.5</td><td>10.7</td><td>52.4</td><td>5.74</td><td>69.5</td><td>12.3</td><td>${1.37} \times  {10}^{3}$</td><td>6.91</td><td>63.2</td><td>10.9</td></tr><tr><td>${9.42} \times  {10}^{9}$</td><td>160</td><td>${1.49} \times  {10}^{8}$</td><td>109</td><td>${2.73} \times  {10}^{7}$</td><td>186</td><td>${1.63} \times  {10}^{8}$</td><td>82.4</td><td>${1.81} \times  {10}^{9}$</td><td>38.2</td><td>${1.75} \times  {10}^{8}$</td><td>80.2</td></tr><tr><td rowspan="4">${Q}_{\nabla }$</td><td rowspan="4">本方法 BM CM RS</td><td>1.37</td><td>12.4</td><td>4.41</td><td>6.92</td><td>7.63</td><td>11.4</td><td>1.14</td><td>9.01</td><td>30.1</td><td>6.78</td><td>0.318</td><td>12.2</td></tr><tr><td>272</td><td>13.1</td><td>12.2</td><td>10.3</td><td>4.6</td><td>9.51</td><td>5.04</td><td>12.5</td><td>375</td><td>6.35</td><td>1.21</td><td>11</td></tr><tr><td>10.7</td><td>14.3</td><td>11.3</td><td>13.7</td><td>23.9</td><td>7.46</td><td>3.71</td><td>13.3</td><td>32</td><td>7.17</td><td>5.18</td><td>12.2</td></tr><tr><td>${1.37} \times  {10}^{12}$</td><td>114</td><td>${6.37} \times  {10}^{9}$</td><td>113</td><td>${3.19} \times  {10}^{9}$</td><td>200</td><td>${3.14} \times  {10}^{9}$</td><td>78.4</td><td>${9.03} \times  {10}^{10}$</td><td>42.2</td><td>${8.57} \times  {10}^{8}$</td><td>76</td></tr><tr><td rowspan="4">${Q}_{3 * }$</td><td rowspan="4">本方法 BM CM RS</td><td>0.00302</td><td>8.23</td><td>0.377</td><td>2.07</td><td>0.554</td><td>1.15</td><td>0.263</td><td>1.69</td><td>2.16</td><td>1.75</td><td>0.337</td><td>1.9</td></tr><tr><td>${2.61} \times  {10}^{3}$</td><td>9.19</td><td>${1.21} \times  {10}^{4}$</td><td>1.54</td><td>210</td><td>1.31</td><td>${1.45} \times  {10}^{3}$</td><td>1.82</td><td>${5.43} \times  {10}^{4}$</td><td>2.38</td><td>${1.38} \times  {10}^{3}$</td><td>2.22</td></tr><tr><td>11.6</td><td>8.21</td><td>33.1</td><td>1.51</td><td>34.3</td><td>1.22</td><td>33</td><td>1.95</td><td>149</td><td>2.01</td><td>34.8</td><td>1.94</td></tr><tr><td>${3.63} \times  {10}^{8}$</td><td>72.3</td><td>${2.34} \times  {10}^{7}$</td><td>34.3</td><td>${4.85} \times  {10}^{6}$</td><td>33</td><td>${3.4} \times  {10}^{7}$</td><td>30.9</td><td>${3.64} \times  {10}^{8}$</td><td>20.6</td><td>${3.34} \times  {10}^{7}$</td><td>30.3</td></tr><tr><td rowspan="4">${Q}_{4 * }$</td><td rowspan="4">本方法 BM CM RS</td><td>0.0262</td><td>12</td><td>1.89</td><td>3.02</td><td>2.83</td><td>2.1</td><td>1.15</td><td>2.45</td><td>12.6</td><td>2.57</td><td>1.36</td><td>3</td></tr><tr><td>${4.12} \times  {10}^{7}$</td><td>12.3</td><td>${1.4} \times  {10}^{6}$</td><td>2.25</td><td>${1.32} \times  {10}^{5}$</td><td>2.05</td><td>${3.55} \times  {10}^{6}$</td><td>2.74</td><td>${2.11} \times  {10}^{8}$</td><td>2.36</td><td>${2.16} \times  {10}^{6}$</td><td>2.93</td></tr><tr><td>${2.46} \times  {10}^{4}$</td><td>9.77</td><td>${1.07} \times  {10}^{3}$</td><td>2.28</td><td>104</td><td>1.62</td><td>${2.56} \times  {10}^{3}$</td><td>2.58</td><td>${9.43} \times  {10}^{4}$</td><td>2.79</td><td>${2.31} \times  {10}^{3}$</td><td>2.82</td></tr><tr><td>${1.03} \times  {10}^{14}$</td><td>253</td><td>${3.56} \times  {10}^{11}$</td><td>133</td><td>${2.03} \times  {10}^{10}$</td><td>133</td><td>${7.31} \times  {10}^{11}$</td><td>122</td><td>${9.25} \times  {10}^{12}$</td><td>83.1</td><td>${3.92} \times  {10}^{11}$</td><td>119</td></tr></tbody></table>

Table 5. Comparison among our mechanism, residual sensitivity with the advanced composition (RS), binary mechanism (BM),and clipping mechanism (CM) on graph pattern counting queries $\left( {\varepsilon  = 4}\right)$ . RE and RT denote relative error and running time and we report median of RE over all selected timestamps.

表5. 本机制与基于高级组合的残差敏感度(RS)、二分机制(BM)及截断机制(CM)在图模式计数查询上的对比$\left( {\varepsilon  = 4}\right)$。RE与RT分别表示相对误差和运行时间，我们报告所有选定时间戳上RE的中位数。

<!-- Media -->

Experimental parameters. All experiments were conducted on a Linux server equipped with a 24-core 48-thread 2.2GHz Intel Xeon CPU and 256GB of memory. We used the the absolute difference between the actual result and the DP result as the error metric. Each experiment was repeated 20 times, ${}^{2}$ with error being recorded every $5 \times  {10}^{5}$ time steps. For each selected time step,we excluded the ${20}\%$ largest and ${20}\%$ smallest errors and reported the average error for the remaining runs. We set the privacy budget at $\varepsilon  = 1,4,{16}$ ,with the default value being 4 . RS has an input as $\delta$ ,which is set to ${10}^{-{10}}$ . Additionally,the failure probability $\beta  = {0.1}$ and $\theta  = 1$ . Furthermore, since RS requires knowledge of the length of the time domain in advance, for the sake of fairness, we employed the finite domain version of the binary mechanism. This version was also used to construct both our mechanism and the clipping mechanism.

实验参数。所有实验均在配备24核48线程2.2GHz英特尔至强处理器和256GB内存的Linux服务器上进行。我们采用实际结果与差分隐私(DP)结果的绝对差值作为误差指标。每组实验重复20次${}^{2}$，每$5 \times  {10}^{5}$个时间步记录一次误差。对于每个选定时间步，我们剔除${20}\%$个最大和${20}\%$个最小误差后计算剩余运行的平均误差。隐私预算设为$\varepsilon  = 1,4,{16}$，默认值为4。RS的输入参数$\delta$设置为${10}^{-{10}}$，失效概率为$\beta  = {0.1}$和$\theta  = 1$。由于RS需要预先知晓时间域长度，为保证公平性，我们采用有限域版本的二分机制，该版本也用于构建本机制和截断机制。

### 8.2 Graph Pattern Counting Queries

### 8.2 图模式计数查询

Utility and efficiency. The errors and running times of all mechanisms pertaining to graph pattern counting queries are shown in Table 5. For each selected timestamp, we collected the relative error and report their median. The results indicate a clear superiority of our mechanism in terms of the utility: our mechanism consistently exhibits high utility, with a median relative error of under 10% in all experiments except three queries over Flickr dataset, where the median of relative error is at most 30%. That is because Flickr dataset is sparser thus has a smaller graph pattern count, amplifying the relative error. In a comparative perspective, RS loses utility across all experiments, aligning with our analysis that its error bears a polynomial dependency on the time domain size $T$ . BM and CM always have much higher error level than our mechanism: our improvement in error over $\mathrm{{BM}}$ and $\mathrm{{CM}}$ can be as large as ${10}^{6}$ and ${10}^{10}$ respectively. One exception happens for the ${Q}_{\nabla }$ over $\mathbf{{CaWiki}}$ dataset,where the ratio between ${\mathrm{{GS}}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)$ and ${\mathrm{{GS}}}_{Q}\left( \left\{  {\widehat{\operatorname{mf}}\left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right)$ is only a large constant in most time steps. Importantly, as query complexity increase, their performance gap from our mechanism also increases, confirming our theoretical analysis. Additionally, in most cases, especially when queries are complex or datasets are sparse, CM outperforms BM due to it removes the error dependency on prior frequency constraints and can utilizes a smaller clipping thresholds to reduce the noise. However,for simple query like ${Q}_{2 - }$ over dense datasets,the clipping could lead to a significant bias. That also demonstrate the importance to estimate the actual maximum frequency adaptvely to balance the bias and noise. In terms of the running time, BM, CM, and our mechanism have similar running times, all of which are much smaller than RS, matching our analysis that BM, CM, and our mechanism have a similar running time as non-private mechanism.

效用与效率。表5展示了各机制在图模式计数查询中的误差与运行时间。针对每个选定时间戳，我们采集相对误差并报告其中位数。结果表明本机制在效用方面具有显著优势：除Flickr数据集上三个查询的中位相对误差最高达30%外，本机制在所有实验中均保持高效用（中位相对误差低于10%）。这是因为Flickr数据集更稀疏导致图模式计数较小，从而放大了相对误差。对比来看，RS在所有实验中效用均不佳，印证了我们关于其误差与时间域尺寸呈多项式依赖的分析$T$。BM和CM的误差水平始终远高于本机制：本机制相较$\mathrm{{BM}}$和$\mathrm{{CM}}$的误差改进幅度分别可达${10}^{6}$和${10}^{10}$。唯一例外发生在$\mathbf{{CaWiki}}$数据集的${Q}_{\nabla }$查询，其中${\mathrm{{GS}}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)$与${\mathrm{{GS}}}_{Q}\left( \left\{  {\widehat{\operatorname{mf}}\left( {{R}_{i},\mathbf{x}}\right) }\right\}  \right)$的比值在多数时间步仅为较大常数。值得注意的是，随着查询复杂度提升，它们与本机制的性能差距也相应增大，验证了我们的理论分析。此外在多数情况下（特别是复杂查询或稀疏数据集时），CM因消除了对先验频率约束的误差依赖并能采用更小截断阈值降噪，其表现优于BM。但对于${Q}_{2 - }$这类稠密数据集上的简单查询，截断操作可能导致显著偏差，这印证了自适应估计实际最大频率以平衡偏差与噪声的重要性。运行时间方面，BM、CM与本机制耗时相近，均远低于RS，这与我们关于三者运行时间接近非隐私机制的分析一致。

---

<!-- Footnote -->

${}^{2}$ For the clipping mechanism,the number of repetitions was increased to 100 since it has more randomness.

${}^{2}$ 由于截断机制具有更高随机性，其重复实验次数增至100次。

<!-- Footnote -->

---

<!-- Media -->

<!-- figureText: ${10}^{17}$ ${10}^{13}$ ${10}^{11}$ ${10}^{9}$ ${10}^{7}$ 5N 10M 2M 5M 7M 10M Timestamp Timestamp ${10}^{19}$ ${10}^{16}$ ${10}^{13}$ ${10}^{10}$ 71 Timestamp Timestamp ${10}^{15}$ - Query result Error Level — Ours ${10}^{13}$ RS ${10}^{12}$ ${10}^{11}$ ${10}^{10}$ ${10}^{8}$ ${10}^{7}$ ${10}^{6}$ 1M 3M 5M 10M 2M Timestamp (a) ${Q}_{3 - }$ on CaWiki. ${10}^{20}$ Error Level ${10}^{20}$ ${10}^{17}$ ${10}^{17}$ ${10}^{14}$ ${10}^{11}$ ${10}^{8}$ 1M Timestamp (b) ${Q}_{4 * }$ on DuWiki. -->

<img src="https://cdn.noedgeai.com/0196599c-190a-7a73-a499-f2871a0182b1_22.jpg?x=142&y=270&w=1286&h=574&r=0"/>

Fig. 7. Error levels vs time of various mechanisms with $\varepsilon  = 1,4,{16}$ . The actual query result is also piloted to help see whether the mechanisms have utility.

图7. 各机制在$\varepsilon  = 1,4,{16}$时的误差水平随时间变化。实际查询结果也被绘制以辅助判断机制效用。

<!-- Media -->

Error with Time. We also conducted experiments to evaluate how the error changes with the time for various mechanisms with different $\varepsilon  = 1,4,{16}$ . Here,we plot the results for ${Q}_{3 - }$ on the CaWiki dataset and ${Q}_{4 * }$ on the DuWiki dataset in Figure 7. The results show that our mechanism, consistently has a high utility except on the initial time steps, where the query result is very small. Moreover, benefiting from the adaptive estimation of the actual maximum frequency, our mechanism has the time-specific error. Contrarily, the errors of the other three mechanisms do not have a strong correlation with the time. It matches our theoretical guarantee that we attain the error proportional to ${\mathrm{{GS}}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)$ at each time while their errors depend on $T$ or some predefined frequency constraints.

时间误差分析。我们还通过实验评估了不同机制下误差随时间的变化情况，这些机制具有不同的$\varepsilon  = 1,4,{16}$参数。图7展示了CaWiki数据集上${Q}_{3 - }$和DuWiki数据集上${Q}_{4 * }$的结果曲线。结果显示，除初始时间步长（此时查询结果量极小）外，我们的机制始终保持着较高的效用值。得益于对实际最大频率的自适应估计，我们的机制能产生随时间变化的特异性误差。相比之下，其他三种机制的误差与时间无明显相关性。这与我们的理论保证一致：我们在每个时间点获得的误差与${\mathrm{{GS}}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)$成正比，而其他机制的误差则取决于$T$或某些预定义的频率约束条件。

Comparison with binary mechanism under different frequency constraints. In the next set of experiments, we compare our mechanism against the binary mechanism under different predefined frequency constraints. We tested the query ${Q}_{3 * }$ on dataset StackOverflow with a frequency constraint ranging from ${2}^{10}$ to ${2}^{15}$ . As mentioned in Section 8.1, ${2}^{10}$ closely approximates the actual maximum frequency of the dataset. The results are shown in Figure 8. The findings firstly demonstrate that the error of binary mechanism highly depends on the setting of frequency constraints. Moreover, our mechanism consistently outperforms the binary mechanism, even when the frequency constraint is nearly equivalent to the actual maximum frequency. That is because our mechanism achieves an time-specific error,i.e.,an error proportional to ${\operatorname{GS}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)$ at at each time $t$ . In contrast, the binary mechanism maintains an error proportional to ${\mathrm{{GS}}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( T\right) },\mathbf{x}}\right) }\right\}  \right)$ across all time steps. Although both mechanisms eventually reach a similar error level, our method performs better at intermediate time steps. Given that we report the average error over all time steps, our mechanism attains a significantly lower error. This outcome also reveals the advantage of adaptively estimating maximum frequencies.

不同频率约束下与二元机制的对比。在后续实验中，我们将所提机制与二元机制在不同预定义频率约束下进行对比测试。在StackOverflow数据集上，我们对查询${Q}_{3 * }$进行了频率约束范围从${2}^{10}$到${2}^{15}$的测试。如8.1节所述，${2}^{10}$非常接近该数据集的实际最大频率。图8显示的结果表明：首先，二元机制的误差高度依赖频率约束的设置；更重要的是，即使当频率约束接近实际最大频率时，我们的机制仍持续优于二元机制。这是因为我们的机制实现了时间特异性误差，即在每个时间点$t$获得与${\operatorname{GS}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( t\right) },\mathbf{x}}\right) }\right\}  \right)$成正比的误差，而二元机制在所有时间步长都保持着与${\mathrm{{GS}}}_{Q}\left( \left\{  {\operatorname{mf}\left( {{I}_{i}^{\left( T\right) },\mathbf{x}}\right) }\right\}  \right)$成正比的误差。虽然两种机制最终达到相近的误差水平，但我们的方法在中间时间步长表现更优。由于我们报告的是所有时间步长的平均误差，因此所提机制获得了显著更低的误差值，这也揭示了自适应估计最大频率的优势。

<!-- Media -->

<!-- figureText: Relative Error Level ordiours ${10}^{1}$ ${10}^{c}$ ${10}^{-1}$ ${10}^{-2}$ ${2}^{1}$ Ours ${2}^{10}$ ${2}^{14}$ ${2}^{15}$ Maximum Frequency Maximum Frequency ${10}^{1}$ CORM ${10}^{0}$ ${10}^{0}$ ${10}^{-1}$ ${10}^{-1}$ ${10}^{-2}$ Ours ${2}^{12}$ ${2}^{13}$ ${2}^{14}$ ${2}^{15}$ Ours 210 ${2}^{1}$ Maximum Frequency -->

<img src="https://cdn.noedgeai.com/0196599c-190a-7a73-a499-f2871a0182b1_23.jpg?x=144&y=271&w=1281&h=247&r=0"/>

Fig. 8. Error levels of our mechanism and binary mechanism (BM) under different maximum frequency ranging from ${2}^{10}$ to ${2}^{15}$ on ${Q}_{3 * }$ over StackOverflow with $\varepsilon  = 1,4,{16}$ .

图8. 在StackOverflow的${Q}_{3 * }$上，当最大频率从${2}^{10}$变化至${2}^{15}$时，我们的机制与二元机制(BM)在$\varepsilon  = 1,4,{16}$条件下的误差水平对比。

<table><tr><td colspan="2" rowspan="2">Mechanism</td><td colspan="2">Ours</td><td colspan="2">BM</td><td colspan="2">CM</td><td colspan="2">RS</td></tr><tr><td>RE(%)</td><td>RT(s)</td><td>RE(%)</td><td>RT(s)</td><td>RE(%)</td><td>RT(s)</td><td>RE(%)</td><td>RT(s)</td></tr><tr><td rowspan="2">Result</td><td>${Q}_{7}$</td><td>21.2</td><td>3.19</td><td>${1.18} \times  {10}^{5}$</td><td>2.92</td><td>192</td><td>2.27</td><td>${1.68} \times  {10}^{17}$</td><td>392</td></tr><tr><td>${Q}_{9}$</td><td>18.1</td><td>1.32</td><td>${1.43} \times  {10}^{5}$</td><td>3.19</td><td>142</td><td>1.49</td><td>${1.98} \times  {10}^{17}$</td><td>848</td></tr></table>

<table><tbody><tr><td colspan="2" rowspan="2">机制</td><td colspan="2">本方案</td><td colspan="2">基准模型(BM)</td><td colspan="2">对比模型(CM)</td><td colspan="2">资源状态(RS)</td></tr><tr><td>相对误差(RE%)</td><td>响应时间(RT秒)</td><td>相对误差(RE%)</td><td>响应时间(RT秒)</td><td>相对误差(RE%)</td><td>响应时间(RT秒)</td><td>相对误差(RE%)</td><td>响应时间(RT秒)</td></tr><tr><td rowspan="2">结果</td><td>${Q}_{7}$</td><td>21.2</td><td>3.19</td><td>${1.18} \times  {10}^{5}$</td><td>2.92</td><td>192</td><td>2.27</td><td>${1.68} \times  {10}^{17}$</td><td>392</td></tr><tr><td>${Q}_{9}$</td><td>18.1</td><td>1.32</td><td>${1.43} \times  {10}^{5}$</td><td>3.19</td><td>142</td><td>1.49</td><td>${1.98} \times  {10}^{17}$</td><td>848</td></tr></tbody></table>

Table 6. Comparison among our mechanism, residual sensitivity with the advanced composition (RS), binary mechanism (BM),and clipping mechanism (CM) on TPC-H queries $\left( {\varepsilon  = 4}\right)$ . RE and RT denote relative error and running time.

表6. 我们的机制与残差敏感度高级组合(RS)、二元机制(BM)及截断机制(CM)在TPC-H查询上的对比$\left( {\varepsilon  = 4}\right)$。RE与RT分别表示相对误差与运行时间。

<!-- Media -->

### 8.3 Multi-way Join Counting Queries

### 8.3 多路连接计数查询

We also evaluated all mechanisms using two selected TPC-H queries, with the results presented in Table 6. Similar to the graph pattern counting queries, our mechanism consistently outperforms the others. However, compared with graph pattern counting queries, our improvement over BM and CM is more modest. This is because as mentioned in Section 8.1, the primary key information in the TPC-H schema will be used. Primary key constraints can be regarded as some strong prior knowledge of frequency constraints thus BM and CM can benefit a lot. On the other hand, our mechanism will not be affected since it automatically adapts to the actual maximum frequencies. Above all, the errors of BM and CM will be reduced after considering primary key constraints but our errors remain unchanged. As a result, our improvement over BM and CM decreases.

我们同样使用两个精选的TPC-H查询评估了所有机制，结果呈现在表6中。与图模式计数查询类似，我们的机制持续优于其他方案。不过相较于图模式计数查询，我们对BM和CM的改进幅度较为有限。这是因为如第8.1节所述，TPC-H模式中的主键信息将被利用——主键约束可视为频次约束的强先验知识，这使得BM和CM能显著受益。而我们的机制因能自动适配实际最大频次而不受影响。关键在于，考虑主键约束后BM和CM的误差会降低，但我们的误差保持不变，因此相较BM和CM的改进幅度有所减小。

## ACKNOWLEDGMENTS

## 致谢

This work has been supported by HKRGC under grant numbers 16205420, 16205422, and 16204223, National Science Foundation under grant numbers 2128519 and 2044679, a grant from ONR, a grant from the DARPA SIEVE program under a subcontract from SRI, a gift from Cisco, and a Packard Fellowship. We would also like to thank the anonymous reviewers who have made valuable suggestions on improving the presentation of the paper.

本研究获香港研资局资助(项目编号16205420/16205422/16204223)、美国国家科学基金会资助(项目编号2128519/2044679)、海军研究办公室资助、DARPA SIEVE项目通过SRI转包的资助、思科公司捐赠以及Packard Fellowship支持。同时感谢匿名评审人对论文改进提出的宝贵建议。

## REFERENCES

## 参考文献

[1] Serge Abiteboul, Richard Hull, and Victor Vianu. 1995. Foundations of databases. Vol. 8. Addison-Wesley Reading.

[2] Mahmoud Abo Khamis, Hung Q Ngo, and Dan Suciu. 2017. What do Shannon-type inequalities, submodular width, and disjunctive datalog have to do with one another?. In Proceedings of the 36th ACM SIGMOD-SIGACT-SIGAI Symposium on Principles of Database Systems. 429-444.

[3] Myrto Arapinis, Diego Figueira, and Marco Gaboardi. 2016. Sensitivity of Counting Queries. In International Colloquium on Automata, Languages, and Programming (ICALP).

[4] Jeremiah Blocki, Avrim Blum, Anupam Datta, and Or Sheffet. 2013. Differentially private data analysis of social networks via restricted sensitivity. In Proceedings of the 4th conference on Innovations in Theoretical Computer Science. 87-96.

[5] Jean Bolot, Nadia Fawaz, Shanmugavelayutham Muthukrishnan, Aleksandar Nikolov, and Nina Taft. 2013. Private decayed predicate sums on streams. In Proceedings of the 16th International Conference on Database Theory. 284-295.

[6] Kuntai Cai, Xiaokui Xiao, and Graham Cormode. 2023. Privlava: synthesizing relational data with foreign keys under differential privacy. Proceedings of the ACM on Management of Data 1, 2 (2023), 1-25.

[7] Paris Carbone, Asterios Katsifodimos, Stephan Ewen, Volker Markl, Seif Haridi, and Kostas Tzoumas. 2015. Apache flink: Stream and batch processing in a single engine. The Bulletin of the Technical Committee on Data Engineering 38,4 (2015).

[8] Adrian Rivera Cardoso and Ryan Rogers. 2022. Differentially private histograms under continual observation: Streaming selection into the unknown. In International Conference on Artificial Intelligence and Statistics. PMLR, 2397-2419.

[9] T-H Hubert Chan, Mingfei Li, Elaine Shi, and Wenchang Xu. 2012. Differentially private continual monitoring of heavy hitters from distributed streams. In International Symposium on Privacy Enhancing Technologies Symposium. Springer, 140-159.

[10] T.-H. Hubert Chan, Elaine Shi, and Dawn Song. 2011. Private and Continual Release of Statistics. ACM Transactions on Information and System Security (2011).

[11] Badrish Chandramouli, Jonathan Goldstein, Mike Barnett, Robert DeLine, Danyel Fisher, John C Platt, James F Terwilliger, and John Wernsing. 2014. Trill: A high-performance incremental query processor for diverse analytics. Proceedings of the VLDB Endowment 8, 4 (2014), 401-412.

[12] Shixi Chen and Shuigeng Zhou. 2013. Recursive mechanism: towards node differential privacy and unrestricted joint In Proceedings of the 2013 ACM SIGMOD International Conference on Management of Data. 653-664.

[13] Yan Chen, Ashwin Machanavajjhala, Michael Hay, and Gerome Miklau. 2017. Pegasus: Data-adaptive differentially private stream processing. In Proceedings of the 2017 ACM SIGSAC Conference on Computer and Communications Security. 1375-1388.

[14] Rachel Cummings, Sara Krehbiel, Kevin A Lai, and Uthaipon Tantipongpipat. 2018. Differential privacy for growing databases. Advances in Neural Information Processing Systems 31 (2018).

[15] Sergey Denisov, Brendan McMahan, Keith Rush, Adam Smith, and Abhradeep Thakurta. 2022. Improved differential privacy for sgd via optimal private linear operators on adaptive streams. In NeurIPS.

[16] Wei Dong, Juanru Fang, Ke Yi, Yuchao Tao, and Ashwin Machanavajjhala. 2022. R2T: Instance-optimal Truncation for Differentially Private Query Evaluation with Foreign Keys. In Proc. ACM SIGMOD International Conference on Management of Data.

[17] Wei Dong, Qiyao Luo, and Ke Yi. 2023. Continual Observation under User-level Differential Privacy. In 2023 IEEE Symposium on Security and Privacy (SP). IEEE Computer Society, 2190-2207.

[18] Wei Dong, Dajun Sun, and Ke Yi. 2023. Better than Composition: How to Answer Multiple Relational Queries under Differential Privacy. Proceedings of the ACM on Management of Data 1, 2 (2023), 1-26.

[19] Wei Dong and Ke Yi. 2021. Residual Sensitivity for Differentially Private Multi-Way Joins. In Proc. ACM SIGMOD International Conference on Management of Data.

[20] Wei Dong and Ke Yi. 2022. A Nearly Instance-optimal Differentially Private Mechanism for Conjunctive Queries. In Proc. ACM Symposium on Principles of Database Systems.

[21] Wei Dong and Ke Yi. 2023. Query Evaluation under Differential Privacy. ACM SIGMOD Record 52, 3 (2023), 6-17.

[22] Wei Dong and Ke Yi. 2023. Universal private estimators. In Proceedings of the 42nd ACM SIGMOD-SIGACT-SIGAI Symposium on Principles of Database Systems. 195-206.

[23] Cynthia Dwork, Moni Naor, Toniann Pitassi, and Guy N Rothblum. 2010. Differential privacy under continual observation. In Proceedings of the forty-second ACM symposium on Theory of computing. 715-724.

[24] Cynthia Dwork, Moni Naor, Omer Reingold, and Guy N Rothblum. 2015. Pure differential privacy for rectangle queries via private partitions. In International Conference on the Theory and Application of Cryptology and Information Security. Springer, 735-751.

[25] Cynthia Dwork, Moni Naor, Omer Reingold, Guy N Rothblum, and Salil Vadhan. 2009. On the complexity of differentially private data release: efficient algorithms and hardness results. In Proceedings of the forty-first annual ACM symposium

on Theory of computing. 381-390.

[26] Cynthia Dwork and Aaron Roth. 2014. The algorithmic foundations of differential privacy. Foundations and Trends® in Theoretical Computer Science 9, 3-4 (2014), 211-407.

[27] Juanru Fang, Wei Dong, and Ke Yi. 2022. Shifted Inverse: A General Mechanism for Monotonic Functions under User Differential Privacy. (2022).

[28] Hendrik Fichtenberger, Monika Henzinger, and Wolfgang Ost. 2021. Differentially Private Algorithms for Graphs Under Continual Observation. In 29th Annual European Symposium on Algorithms (ESA 2021). Schloss Dagstuhl-Leibniz-Zentrum für Informatik.

[29] Georg Gottlob, Stephanie Tien Lee, Gregory Valiant, and Paul Valiant. 2012. Size and treewidth bounds for conjunctive queries. Journal of the ACM (JACM) 59, 3 (2012), 1-35.

[30] Monika Henzinger and Jalaj Upadhyay. 2022. Constant matters: Fine-grained Complexity of Differentially Private Continual Observation Using Completely Bounded Norms. arXiv preprint arXiv:2202.11205 (2022).

[31] Monika Henzinger, Jalaj Upadhyay, and Sarvagya Upadhyay. 2022. Almost tight error bounds on differentially private continual counting. arXiv preprint arXiv:2211.05006 (2022).

[32] Noah Johnson, Joseph P Near, and Dawn Song. 2018. Towards practical differential privacy for SQL queries. Proceedings of the VLDB Endowment 11, 5 (2018), 526-539.

[33] Peter Kairouz, Brendan McMahan, Shuang Song, Om Thakkar, Abhradeep Thakurta, and Zheng Xu. 2021. Practical and private (deep) learning without sampling or shuffling. In International Conference on Machine Learning. PMLR, ${5213} - {5225}$ .

[34] Vishesh Karwa, Sofya Raskhodnikova, Adam Smith, and Grigory Yaroslavtsev. 2011. Private analysis of graph structure. Proceedings of the VLDB Endowment 4, 11 (2011), 1146-1157.

[35] Shiva Prasad Kasiviswanathan, Kobbi Nissim, Sofya Raskhodnikova, and Adam Smith. 2013. Analyzing graphs with node differential privacy. In Theory of Cryptography Conference. Springer, 457-476.

[36] Ios Kotsogiannis, Yuchao Tao, Xi He, Maryam Fanaeepour, Ashwin Machanavajjhala, Michael Hay, and Gerome Miklau. 2019. PrivateSQL: a differentially private SQL query engine. Proceedings of the VLDB Endowment 12, 11 (2019), 1371-1384.

[37] Jérôme Kunegis. 2013. Konect: the koblenz network collection. In Proceedings of the 22nd international conference on world wide web. 1343-1350.

[38] Jure Leskovec and Andrej Krevl. 2014. SNAP: Stanford network analysis project.

[39] Jure Leskovec and Andrej Krev1. 2016. SNAP datasets: Stanford large network dataset collection (2014). URL http://snap.stanford.edu/data (2016), 49.

[40] Michael Ley. 2002. The DBLP computer science bibliography: Evolution, research issues, perspectives. In International symposium on string processing and information retrieval. Springer, 1-10.

[41] Chao Li, Gerome Miklau, Michael Hay, Andrew McGregor, and Vibhor Rastogi. 2015. The matrix mechanism: optimizing linear counting queries under differential privacy. The VLDB journal 24, 6 (2015), 757-781.

[42] Frank D McSherry. 2009. Privacy integrated queries: an extensible platform for privacy-preserving data analysis. In Proceedings of the 2009 ACM SIGMOD International Conference on Management of data. 19-30.

[43] Alan Mislove, Hema Swetha Koppula, Krishna P Gummadi, Peter Druschel, and Bobby Bhattacharjee. 2008. Growth of the flickr social network. In Proceedings of the first workshop on Online social networks. 25-30.

[44] Arjun Narayan and Andreas Haeberlen. 2012. DJoin: Differentially private join queries over distributed databases. In USENIX Symposium on Operating Systems Design and Implementation. 149-162.

[45] Kobbi Nissim, Sofya Raskhodnikova, and Adam Smith. 2007. Smooth sensitivity and sampling in private data analysis. In Proceedings of the thirty-ninth annual ACM symposium on Theory of computing. 75-84.

[46] Catuscia Palamidessi and Marco Stronati. 2012. Differential Privacy for Relational Algebra: Improving the Sensitivity Bounds via Constraint Systems. In ${QAPL}$ .

[47] Victor Perrier, Hassan Jameel Asghar, and Dali Kaafar. 2019. Private continual release of real-valued data streams. In 26th Annual Network and Distributed System Security Symposium, NDSS 2016. Internet Society, 1-13.

[48] Davide Proserpio, Sharon Goldberg, and Frank McSherry. 2014. Calibrating Data to Sensitivity in Private Data Analysis. Proceedings of the VLDB Endowment 7, 8 (2014).

[49] Yuan Qiu and Ke Yi. 2022. Differential Privacy on Dynamic Data. arXiv preprint arXiv:2209.01387 (2022).

[50] Shuang Song, Susan Little, Sanjay Mehta, Staal Vinterbo, and Kamalika Chaudhuri. 2018. Differentially private continual release of graph statistics. arXiv preprint arXiv:1809.02575 (2018).

[51] Dajun Sun, Wei Dong, and Ke Yi. 2023. Confidence Intervals for Private Query Processing. Proceedings of the VLDB Endowment 17, 3 (2023), 373-385.

[52] Yuchao Tao, Xi He, Ashwin Machanavajjhala, and Sudeepa Roy. 2020. Computing Local Sensitivities of Counting Queries with Joins. In Proceedings of the 2020 ACM SIGMOD International Conference on Management of Data. 479-494.

[53] Jalaj Upadhyay. 2019. Sublinear space private algorithms under the sliding window model. In International Conference on Machine Learning. PMLR, 6363-6372.

[54] Qichen Wang, Xiao Hu, Binyang Dai, and Ke Yi. 2023. Change Propagation Without Joins. Proceedings of the VLDB Endowment 16, 5 (2023), 1046-1058.

[55] Tianhao Wang, Joann Qiongna Chen, Zhikun Zhang, Dong Su, Yueqiang Cheng, Zhou Li, Ninghui Li, and Somesh Jha. 2021. Continuous release of data streams under both centralized and local differential privacy. In Proceedings of the 2021 ACM SIGSAC Conference on Computer and Communications Security. 1237-1253.

[56] Bing Zhang, Vadym Doroshenko, Peter Kairouz, Thomas Steinke, Abhradeep Thakurta, Ziyin Ma, Himani Apte, and Jodi Spacek. 2023. Differentially Private Stream Processing at Scale. arXiv preprint arXiv:2303.18086 (2023).

[57] Jun Zhang, Graham Cormode, Cecilia M Procopiuc, Divesh Srivastava, and Xiaokui Xiao. 2015. Private release of graph statistics using ladder functions. In Proceedings of the 2015 ACM SIGMOD international conference on management of data. 731-745.