## DPconv: Super-Polynomially Faster Join Ordering

## DPconv：超多项式加速的连接顺序优化

MIHAIL STOIAN, UTN, Germany

米哈伊尔·斯托扬，德国UTN

ANDREAS KIPF, UTN, Germany

安德烈亚斯·基普夫，德国UTN

We revisit the join ordering problem in query optimization. The standard exact algorithm, DPccp, has a worst-case running time of $O\left( {3}^{n}\right)$ . This is prohibitively expensive for large queries,which are not that uncommon anymore. We develop a new algorithmic framework based on subset convolution. DPconv achieves a super-polynomial speedup over DPccp,breaking the $O\left( {3}^{n}\right)$ time-barrier for the first time. We show that the framework instantiation for the ${C}_{\max }$ cost function is up to ${30}\mathrm{x}$ faster than DPccp for large clique queries.

我们重新审视查询优化中的连接顺序问题。标准精确算法DPccp的最坏情况时间复杂度为$O\left( {3}^{n}\right)$，这对于如今并不罕见的大型查询而言代价过高。我们开发了基于子集卷积的新算法框架DPconv，首次突破$O\left( {3}^{n}\right)$时间壁垒，实现了对DPccp的超多项式加速。实验表明，在${C}_{\max }$代价函数下的框架实例化，对于大型团查询可比DPccp快达${30}\mathrm{x}$倍。

CCS Concepts: - Information systems $\rightarrow$ Query optimization.

CCS概念：- 信息系统 $\rightarrow$ 查询优化

Additional Key Words and Phrases: join ordering, dynamic programming, fast subset convolution, exponential-time approximation algorithm

附加关键词：连接顺序、动态规划、快速子集卷积、指数时间近似算法

## ACM Reference Format:

## ACM参考文献格式：

Mihail Stoian and Andreas Kipf. 2024. DPconv: Super-Polynomially Faster Join Ordering. Proc. ACM Manag. Data 2, 6 (SIGMOD), Article 234 (December 2024), 26 pages. https://doi.org/10.1145/3698809

米哈伊尔·斯托扬与安德烈亚斯·基普夫。2024。DPconv：超多项式加速的连接顺序优化。《ACM数据管理汇刊》2卷6期(SIGMOD)，第234篇文章（2024年12月），26页。https://doi.org/10.1145/3698809

## 1 INTRODUCTION

## 1 引言

The query optimizer is the heart of any relational database system. One of the fundamental tasks of the query optimizer is join ordering. The problem is to reorder the joins, so that the query execution time is minimized. To this end, one introduces a cost model that acts as proxy for the actual execution time. Since the costs are directly reflected in the query execution time, optimal or near-optimal join orders are indispensable for the overall performance. However, the problem is inherently $\mathrm{{NP}}$ -hard [20]. This means that,unless $\mathrm{P} = \mathrm{{NP}}$ ,one has to resort to the exponential (exact) algorithm for small queries and to greedy strategies otherwise.

查询优化器是关系数据库系统的核心组件，其基础任务之一是确定连接顺序。该问题旨在通过重排连接操作使查询执行时间最小化，通常借助作为实际执行时间代理的代价模型实现。由于代价直接影响查询执行效率，最优或近似最优的连接顺序对整体性能至关重要。但该问题本质上是$\mathrm{{NP}}$难问题[20]，这意味着除非$\mathrm{P} = \mathrm{{NP}}$成立，否则对小查询需采用指数级精确算法，对大查询则需依赖贪心策略。

Motivation & Research Question. In a seminal work, Selinger introduces the first dynamic program to (exactly) optimize the ordering problem [44]. The key observation is that the optimal solution ${S}^{ * }$ for a set of relations $P$ ,called the problem,satisfies Bellman’s optimality principle [1], namely that ${S}^{ * }$ is computed from two disjoint subproblems ${P}_{1}$ and ${P}_{2}$ ,with optimal solutions ${S}_{1}^{ * }$ and ${S}_{2}^{ * }$ ,respectively. The naive algorithm,DPsize,runs in $O\left( {4}^{n}\right)$ -time,which can be reduced to $O\left( {3}^{n}\right)$ by a careful traversal of the subsets of a given set,algorithm known as DPsub [48,49].

研究动机与问题。Selinger在开创性工作中首次提出动态规划算法来精确求解排序问题[44]，其核心思想是：对于关系集合$P$（称为问题）的最优解${S}^{ * }$满足贝尔曼最优性原理[1]，即${S}^{ * }$由两个互斥子问题${P}_{1}$和${P}_{2}$的最优解${S}_{1}^{ * }$与${S}_{2}^{ * }$组合而成。朴素算法DPsize的时间复杂度为$O\left( {4}^{n}\right)$，而通过精心遍历给定集合的子集（DPsub算法[48,49]）可优化至$O\left( {3}^{n}\right)$。

Later, Moerkotte and Neumann [30] showed that one can obtain an improved algorithm if one disallows cross-products, namely by considering the connectivity structure of the underlying query graph (the algorithm was later extended to hypergraphs [31]). Their algorithm, DPccp, achieves the lower-bound on the number of connected complement pairs which any dynamic program needs to traverse, as shown by Ono and Lohman [36]. Recently, Haffner and Dittrich [18] proved that join ordering reduces to computing shortest paths in an exponential-size graph in which the vertices are relation subsets. Their reduction enables the use of well-known speedups via heuristic search,

后续Moerkotte和Neumann[30]证明，通过禁止笛卡尔积并考虑查询图的连通性结构（该算法后扩展至超图[31]），可获得更优算法DPccp。如Ono和Lohman[36]所示，该算法达到了动态规划必须遍历的连通互补对数量下界。近期Haffner与Dittrich[18]证实，连接顺序问题可转化为在顶点为关系子集的指数级图中计算最短路径，这种归约使得启发式搜索加速技术得以应用。

Permission to make digital or hard copies of all or part of this work for personal or classroom use is granted without fee provided that copies are not made or distributed for profit or commercial advantage and that copies bear this notice and the full citation on the first page. Copyrights for components of this work owned by others than the author(s) must be honored. Abstracting with credit is permitted. To copy otherwise, or republish, to post on servers or to redistribute to lists, requires prior specific permission and/or a fee. Request permissions from permissions@acm.org.

本作品允许为个人或课堂教学目的制作数字或硬拷贝，但不得为商业利益制作或分发副本，且副本须包含首页完整版权声明。非作者持有的部分作品版权需受尊重。允许标注来源的摘要，其他复制、再版、服务器发布或重新分发需获得特别许可和/或费用。申请权限请联系permissions@acm.org。

© 2024 Copyright held by the owner/author(s). Publication rights licensed to ACM.

© 2024 版权归作者所有。出版权由ACM授权。

ACM 2836-6573/2024/12-ART234

ACM 2836-6573/2024/12-ART234

https://doi.org/10.1145/3698809 as known from the ${A}^{ * }$ algorithm [19]. However,while their average-case running time beats that of DPccp,the worst-case running time still remains $O\left( {3}^{n}\right)$ . The $O\left( {3}^{n}\right)$ -time bottleneck leads us to our main question:

如${A}^{ * }$算法[19]所示，https://doi.org/10.1145/3698809。然而，虽然其平均运行时间优于DPccp，但最坏情况下的运行时间仍保持$O\left( {3}^{n}\right)$。$O\left( {3}^{n}\right)$时间瓶颈引出了我们的核心问题：

---

<!-- Footnote -->

Authors' addresses: Mihail Stoian, UTN, Ulmenstraße 52i, Nuremberg, Germany, 90443, mihail.stoian@utn.de; Andreas Kipf, UTN, Ulmenstraße 52i, Nuremberg, Germany, 90443, andreas.kipf@utn.de.

作者联系方式：米海尔·斯托扬，德国纽伦堡乌尔门大街52i，90443，UTN，mihail.stoian@utn.de；安德烈亚斯·基普夫，德国纽伦堡乌尔门大街52i，90443，UTN，andreas.kipf@utn.de。

<!-- Footnote -->

---

<!-- Media -->

<!-- figureText: $V \mathrel{\text{:=}} \left( {{R}_{1}{R}_{2}{R}_{3}{R}_{4}}\right) \mathop{\min }\limits_{{\varnothing  \subset  S \subset  V}}\left( {\mathrm{{DP}}\left\lbrack  S\right\rbrack   + \mathrm{{DP}}\left\lbrack  {V \smallsetminus  S}\right\rbrack  }\right)$ Subset Convolution -->

<img src="https://cdn.noedgeai.com/019659a0-2ea1-7884-92fc-eb39bae80dc5_1.jpg?x=425&y=260&w=717&h=420&r=0"/>

Fig. 1. How join ordering dynamic programming algorithms, e.g., DPsub, are implicitly using subset convolution. However, they are computing it naively. DPconv instead uses a highly-tuned implementation of fast subset convolution [2].

图1. 连接排序动态规划算法（如DPsub）如何隐式使用子集卷积。但它们采用原始计算方式，而DPconv则采用高度优化的快速子集卷积实现[2]。

<!-- Media -->

Is there a way to break the seemingly unyielding $O\left( {3}^{n}\right)$ -time barrier?

是否存在方法突破看似坚不可摧的$O\left( {3}^{n}\right)$时间壁垒？

Surprisingly, there is. To this end, consider Fig. 1, in which we show how the standard join ordering dynamic programming algorithms DPsub [48, 49] and DPccp [30] optimize the full set of relations $V = \left\{  {{R}_{1},{R}_{2},{R}_{3},{R}_{4}}\right\}$ . Simply put,the algorithm iterates over all possible ways to split the original set $V$ into two subsets. This is exactly a subset convolution. However,all current join ordering algorithms, DPsize, DPsub, and DPccp, perform it naively, i.e., the expression is evaluated as is. Fortunately for our community, research in algorithm design has led to a fast subset convolution [2]. Intuitively, fast subset convolution no longer naively enumerates subsets, but instead uses an FFT-inspired strategy that avoids redundant computational steps of the naive evaluation. To this end, we develop a new exact algorithmic framework based on fast subset convolution that has super-polynomial speedup over DPccp / DPsub. This breaks the long-standing $O\left( {3}^{n}\right)$ time-barrier for the first time.

令人惊讶的是，答案是肯定的。如图1所示，标准连接排序动态规划算法DPsub[48,49]和DPccp[30]通过迭代所有可能的分割方式来优化关系全集$V = \left\{  {{R}_{1},{R}_{2},{R}_{3},{R}_{4}}\right\}$——这正是子集卷积的运算。然而当前所有连接排序算法（DPsize、DPsub、DPccp）都采用原始计算方式。幸运的是，算法设计领域已发展出快速子集卷积技术[2]，其通过类FFT策略避免冗余计算。基于此，我们开发了突破性算法框架，首次打破了长期存在的$O\left( {3}^{n}\right)$时间壁垒。

We instantiate the framework for two well-studied cost functions, ${C}_{\text{out }}$ and ${C}_{\max }$ ,which guarantee time- and space-optimality of query execution, respectively. Namely, the latter minimizes the sum of the intermediate join sizes, while the former minimizes the largest intermediate one. This results in an $O\left( {{2}^{n}{n}^{2}{Wn}\log {Wn}}\right)$ -time algorithm for ${C}_{\text{out }}$ ,which is $\widetilde{O}\left( {2}^{n}\right)$ when the largest join cardinality $W$ is polynomial in $n,{}^{1}$ and an $O\left( {{2}^{n}{n}^{3}}\right)$ -time algorithm for ${C}_{\max }$ ; the latter running time is independent of $W$ . At a practical level,we show that the instantiation for ${C}_{\max }$ is up to ${30}\mathrm{x}$ faster than the classic algorithm for clique queries of 17 or more relations.

我们针对两种经典成本函数${C}_{\text{out }}$和${C}_{\max }$实例化该框架：前者最小化最大中间连接规模，后者最小化中间连接规模总和。对于${C}_{\text{out }}$，当最大连接基数$W$是$n,{}^{1}$的多项式时，获得$\widetilde{O}\left( {2}^{n}\right)$时间复杂度的$O\left( {{2}^{n}{n}^{2}{Wn}\log {Wn}}\right)$算法；对于${C}_{\max }$则获得与$W$无关的$O\left( {{2}^{n}{n}^{3}}\right)$时间复杂度。实际测试表明，在17个及以上关系的团查询中，${C}_{\max }$实例比经典算法快${30}\mathrm{x}$倍。

We further reduce the optimization time for ${C}_{\text{out }}$ to $\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\varepsilon }}\right)$ -time using an $\left( {1 + \varepsilon }\right)$ -approximation algorithm. Unlike our exact algorithm,the running time of this algorithm is independent of $W$ .

通过$\left( {1 + \varepsilon }\right)$近似算法，我们将${C}_{\text{out }}$的优化时间进一步降至$\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\varepsilon }}\right)$，且该算法运行时间与$W$无关。

In addition,we devise a new cost function which combines the benefits of ${C}_{\text{out }}$ and ${C}_{\max }$ and provide an implementation which first computes the optimal ${C}_{\max }$ value and then runs a pruned ${C}_{\text{out }}$ optimization. We show that this optimization is faster than that of the "vanilla" ${C}_{\text{out }}$ when using our new framework.

此外，我们设计了融合${C}_{\text{out }}$与${C}_{\max }$优势的新型成本函数，其实现先计算最优${C}_{\max }$值再执行剪枝${C}_{\text{out }}$优化。测试表明，采用新框架时该优化速度优于传统${C}_{\text{out }}$方法。

---

<!-- Footnote -->

${}^{1}$ The notation $\widetilde{O}$ hides poly-logarithmic factors; in this particular case, ${n}^{O\left( 1\right) }$ .

${}^{1}$ 符号$\widetilde{O}$表示隐藏多对数因子，本例中为${n}^{O\left( 1\right) }$。

<!-- Footnote -->

---

## Contribution. We summarize our contributions below:

## 贡献。我们将主要贡献总结如下：

(1) We introduce a new exact algorithmic framework based on subset convolution which breaks the long-standing time-barrier of $O\left( {3}^{n}\right)$ for the first time.

(1) 我们提出了一种基于子集卷积的全新精确算法框架，首次突破了$O\left( {3}^{n}\right)$问题的长期时间壁垒。

(2) We provide a practical instantiation of the framework for ${C}_{\max }$ ,achieving an $O\left( {{2}^{n}{n}^{3}}\right)$ -time algorithm.

(2) 我们为该框架在${C}_{\max }$场景中提供了具体实现，获得了$O\left( {{2}^{n}{n}^{3}}\right)$时间复杂度算法。

(3) We introduce an $\left( {1 + \varepsilon }\right)$ -approximation algorithm for the join ordering problem under ${C}_{\text{out }}$ in $\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\varepsilon }}\right)$ -time.

(3) 我们针对${C}_{\text{out }}$约束下的连接顺序问题，提出了$\left( {1 + \varepsilon }\right)$近似算法，其时间复杂度为$\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\varepsilon }}\right)$。

(4) We initiate the joint study of ${C}_{\text{out }}$ and ${C}_{\max }$ : Minimize the sum of the intermediate join sizes so that the largest one is equal to the optimal ${C}_{\max }$ value.

(4) 我们首次将${C}_{\text{out }}$与${C}_{\max }$联合研究：在保证最大中间连接规模等于最优${C}_{\max }$值的前提下，最小化中间连接规模总和。

Running Times. Let us first relate the running times for ${C}_{\text{out }}$ and ${C}_{\max }$ ,which seem quite disparate at first glance. They both rely on our highly-tuned implementation of fast subset convolution for dynamic programming (Sec. 5),which runs in $O\left( {{2}^{n}{n}^{2}}\right)$ -time. Thus,we can observe a common $O\left( {{2}^{n}{n}^{2}}\right)$ -time factor in both running times. The difference lies in the implementation of the individual cost functions: Optimizing for ${C}_{\text{out }}$ introduces an additional $O\left( {{Wn}\log {Wn}}\right)$ -time factor resulting from the application of FFT to sequences of length ${Wn}$ (Sec. 3.3),while ${C}_{\max }$ incurs only an $O\left( n\right)$ -time overhead (Sec. 6).

运行时间分析。首先比较${C}_{\text{out }}$与${C}_{\max }$的运行时间，二者看似差异显著。它们都依赖于我们高度优化的快速子集卷积动态规划实现（第5节），其时间复杂度为$O\left( {{2}^{n}{n}^{2}}\right)$。因此可见两者运行时间都包含$O\left( {{2}^{n}{n}^{2}}\right)$这个共同因子。差异在于各自成本函数的实现：优化${C}_{\text{out }}$时由于需要对长度为${Wn}$的序列应用快速傅里叶变换（第3.3节），会引入额外的$O\left( {{Wn}\log {Wn}}\right)$时间因子；而${C}_{\max }$仅产生$O\left( n\right)$的时间开销（第6节）。

Search Space. DPconv imposes no restrictions on the shape of the query graph or join tree. Our framework optimizes arbitrary query graphs-both acyclic and cyclic queries-including cliques, which are considered the worst case of join ordering [33], and bushy join trees, as do other join ordering algorithms such as DPsub and DPccp. This includes optimizing for cross-products in the same running time as for arbitrary query graphs. We discuss this aspect as part of Sec. 2. Note that our framework also optimizes query hypergraphs, representing non-inner joins [31] (we discuss this aspect in Sec. 3.1).

搜索空间。DPconv不限制查询图或连接树的形态。我们的框架可优化任意查询图——包括无环查询、环形查询以及被视为连接顺序最坏情况的团结构[33]，也能处理灌木状连接树，这与DPsub、DPccp等其他连接顺序算法相同。该框架还能在相同时间复杂度下优化查询图中的笛卡尔积，我们将在第2节讨论此特性。值得注意的是，我们的框架还能优化表示非内连接的查询超图[31]（详见第3.1节）。

Other Cost Functions. The literature on join ordering also addresses various cost functions beyond ${C}_{\text{out }}$ and ${C}_{\max }$ ,depending on how a join is executed,e.g.,by sort-merge join,hash-join,or nested-loop join [29]. We demonstrate that our framework can accommodate the cost function associated with the sort-merge join because it satisfies an additive separability property (see Sec. 3.5). However, the cost functions associated with hash-joins and nested-loop joins do not enjoy this property and thus cannot be mapped to our framework.

其他成本函数。关于连接顺序的文献还涉及除${C}_{\text{out }}$和${C}_{\max }$外的多种成本函数，具体取决于排序合并连接、哈希连接或嵌套循环连接等执行方式[29]。我们证明该框架可适配排序合并连接的成本函数，因其满足加法可分离性（见第3.5节）。但哈希连接和嵌套循环连接的成本函数不具备此特性，因此无法映射到我们的框架中。

Organization. The rest of the paper is organized as follows: First, in Sec. 2, we formalize the problem of join ordering and that of fast subset convolution. Then, in Sec. 3, we introduce DPconv along with the novel connection between join ordering and subset convolution. We describe the machinery behind fast subset convolution in Sec. 4, and in Sec. 5 we show how to shave a linear factor from the running time of any dynamic programming recursion based on subset convolution (including that of DPconv). Based on this,we provide in Sec. 6 a practical algorithm for ${C}_{\max }$ . Then, we outline the approximation algorithm in Sec. 7. We propose ${C}_{\text{cap }}$ in Sec. 8,which we start for the first time the joint study of ${C}_{\text{out }}$ and ${C}_{\text{max }}$ with. We outline related work in Sec. 10,provide a discussion in Sec. 11, and finally conclude in Sec. 12.

论文结构。本文后续章节安排如下：第2节形式化定义连接顺序与快速子集卷积问题；第3节提出DPconv框架，揭示连接顺序与子集卷积的新颖关联；第4节阐述快速子集卷积的底层机制；第5节展示如何从基于子集卷积的动态规划递归（包括DPconv）中消除线性时间因子；第6节据此给出${C}_{\max }$的实用算法；第7节概述近似算法；第8节首次提出${C}_{\text{cap }}$，启动${C}_{\text{out }}$与${C}_{\text{max }}$的联合研究；第10节综述相关工作；第11节展开讨论；第12节总结全文。

## 2 BACKGROUND

## 2 背景

In this section, we formalize both problems, namely join ordering and subset convolution.

本节将形式化定义连接顺序和子集卷积两个问题。

### 2.1 Query Graph

### 2.1 查询图

Let $\mathcal{D} = \left\{  {{R}_{1},\ldots ,{R}_{n}}\right\}$ be a database that contains $n$ relations. A select-project-join query $\mathcal{Q}$ is defined as

设$\mathcal{D} = \left\{  {{R}_{1},\ldots ,{R}_{n}}\right\}$为包含$n$个关系的数据库。选择-投影-连接查询$\mathcal{Q}$定义为

$$
Q = {\Pi }_{A}\left( {{\sigma }_{P}\left( {{R}_{1} \times  \ldots  \times  {R}_{n}}\right) }\right) , \tag{1}
$$

where $P$ is the conjunction of predicates that can be both join predicates,i.e., ${R}_{i}.a = {R}_{j}.b$ ,and selection predicates,i.e., ${R}_{i}a =$ const,and $A$ is the list of attributes required to appear in the output. The operators $\Pi ,\sigma$ ,and $\times$ are the projection,selection and cross-product operators,respectively,as defined in relational algebra [7].

其中$P$是可同时作为连接谓词（即${R}_{i}.a = {R}_{j}.b$）和选择谓词（即${R}_{i}a =$常量）的谓词合取式，$A$是输出结果中需要出现的属性列表。运算符$\Pi ,\sigma$、$\times$分别表示关系代数[7]中定义的投影、选择和笛卡尔积运算。

We can model a query as a query graph $Q = \left( {V,E}\right)$ ,where the vertex set $V$ corresponds to the set of relations ${\left\{  {R}_{i}\right\}  }_{i \in  \left\lbrack  n\right\rbrack  }$ of the query and the edge set $E = \left\{  {\left\{  {{R}_{u},{R}_{v}}\right\}   \mid  {R}_{u},{R}_{v} \in  V}\right\}$ corresponds to the join predicates (called join edges in the sequel). Intuitively, a query can be evaluated by repeatedly joining two relations and replacing one of them with their join. Another prominent way of executing joins by worst-case optimal joins, which are not necessarily binary joins anymore [35]. In this work, we only concentrate on query optimization of binary joins. In this case, the order in which the joins are performed can be represented by a (binary) join tree, where the leaf nodes are the relations and the inner nodes are the corresponding joins.

我们可以将查询建模为查询图$Q = \left( {V,E}\right)$，其顶点集$V$对应查询的关系集${\left\{  {R}_{i}\right\}  }_{i \in  \left\lbrack  n\right\rbrack  }$，边集$E = \left\{  {\left\{  {{R}_{u},{R}_{v}}\right\}   \mid  {R}_{u},{R}_{v} \in  V}\right\}$对应连接谓词（后续称为连接边）。直观上，查询可通过反复连接两个关系并用其连接结果替换其中之一来求值。另一种重要连接执行方式是最坏情况最优连接，其不再限定于二元连接[35]。本文仅聚焦二元连接的查询优化，此时连接顺序可用（二元）连接树表示，其中叶节点为关系，内部节点为对应连接操作。

### 2.2 Cost Function

### 2.2 代价函数

To optimize the join order,one introduces a cost function $\mathcal{C}$ which best models the query execution time. The goal is to minimize the cost function among all possible join trees. Due to the binary structure of a join tree, the cost function can be represented as a recursive function along a join tree $\mathcal{T}$ ,as follows:

为优化连接顺序，需引入最能模拟查询执行时间的代价函数$\mathcal{C}$。目标是在所有可能的连接树中最小化该函数。由于连接树的二元结构，代价函数可沿连接树$\mathcal{T}$递归表示为：

$$
\mathcal{C}\left( \mathcal{T}\right)  = \left\{  \begin{array}{ll} 0, & \text{ if }\mathcal{T}\text{ is a single }1 \\  c\left( T\right)  \otimes  \mathcal{C}\left( {\mathcal{T}}_{1}\right)  \otimes  \mathcal{C}\left( {\mathcal{T}}_{2}\right) , & \text{ if }\mathcal{T} = {\mathcal{T}}_{1} \boxtimes  {\mathcal{T}}_{2}, \end{array}\right.  \tag{2}
$$

where $c$ is the join cardinality function defined on sets of relations, $T$ is the set of relations spanned by the join tree $\mathcal{T}{,}^{2}$ and ${\mathcal{T}}_{1}$ and ${\mathcal{T}}_{2}$ are the left and right join subtrees of $\mathcal{T}$ ,respectively.

其中$c$是定义在关系集上的连接基数函数，$T$是连接树$\mathcal{T}{,}^{2}$覆盖的关系集，${\mathcal{T}}_{1}$和${\mathcal{T}}_{2}$分别是$\mathcal{T}$的左右连接子树。

Let us instantiate Eq. (2) for two cost functions, ${C}_{\text{out }}$ and ${C}_{\max }$ ,which guarantee time-optimality and space-optimality of the query execution, respectively:

现将公式(2)实例化为两个分别保证查询执行时间最优性和空间最优性的代价函数${C}_{\text{out }}$和${C}_{\max }$：

$$
{C}_{\text{out }}\left( T\right)  = c\left( T\right)  + {C}_{\text{out }}\left( {\mathcal{T}}_{1}\right)  + {C}_{\text{out }}\left( {\mathcal{T}}_{2}\right) , \tag{3}
$$

$$
{C}_{\max }\left( T\right)  = \max \left\{  {c\left( T\right) ,{C}_{\max }\left( {\mathcal{T}}_{1}\right) ,{C}_{\max }\left( {\mathcal{T}}_{2}\right) }\right\}  . \tag{4}
$$

We can observe that the " $\otimes$ " operator has been substituted by "+" and "max",respectively. We discuss the applicability of our framework to other cost functions in the literature in Sec. 3.5.

可见"$\otimes$"运算符分别被替换为"+"和"max"运算符。我们将在3.5节讨论本框架对其他文献中代价函数的适用性。

### 2.3 Join Ordering and Dynamic Programming

### 2.3 连接排序与动态规划

By Bellman’s optimality principle [1],the problem of finding the optimal join tree ${\mathcal{T}}^{ * }$ amounts to finding the optimal split of a set of relations $S$ into two disjoint sets ${S}_{1}$ and ${S}_{2}$ ,i.e., ${S}_{1} \cap  {S}_{2} = \varnothing$ and $S = {S}_{1} \cup  {S}_{2}$ . Consequently,given a cost function $C$ ,the problem can be optimized by the following dynamic programming (DP) recursion,which closely follows the definition of $C$ :

根据贝尔曼最优性原理[1]，寻找最优连接树${\mathcal{T}}^{ * }$等价于将关系集$S$最优分割为两个互斥子集${S}_{1}$和${S}_{2}$，即${S}_{1} \cap  {S}_{2} = \varnothing$且$S = {S}_{1} \cup  {S}_{2}$。因此给定代价函数$C$，该问题可通过以下严格遵循$C$定义的动态规划(DP)递归来优化：

$$
\mathrm{{DP}}\left( S\right)  = \left\{  \begin{array}{ll} 0, & \text{ if }\left| S\right|  = 1 \\  c\left( S\right)  \otimes  \mathop{\min }\limits_{{\varnothing  \subset  T \subset  S}}\left( {\mathrm{{DP}}\left( T\right)  \otimes  \mathrm{{DP}}\left( {S \smallsetminus  T}\right) }\right) , & \text{ otherwise. } \end{array}\right.  \tag{5}
$$

Indeed, this is the idea explored by Selinger and the subsequent work [30, 44, 48, 49]. In particular, DPccp [30] optimizes the recursion by considering only sets of relations that induce a connected subgraph; for clique queries, DPsub and DPccp are both exactly the recursion above. As motivated in Fig. 1, Eq. (5) is a subset convolution. All previous algorithms evaluate it in the naive way, which takes $O\left( {3}^{n}\right)$ -time. DPconv speeds up its computation by employing fast subset convolution [2].

这正是Selinger及后续研究[30,44,48,49]的核心思想。特别地，DPccp[30]通过仅考虑生成连通子图的关系集来优化递归；对于团查询，DPsub和DPccp均严格对应上述递归。如图1所示，公式(5)实为子集卷积。现有算法均采用耗时$O\left( {3}^{n}\right)$的朴素计算方式，而DPconv通过快速子集卷积[2]加速该计算。

---

<!-- Footnote -->

${}^{2}$ Having a separate notation for the join tree and the set of relations it spans will prove useful in the following sections.

${}^{2}$为连接树及其覆盖的关系集设立独立符号表示，将在后续章节中体现其价值。

<!-- Footnote -->

---

### 2.4 Subset Convolution

### 2.4 子集卷积

Subset convolution is one of the important tools in the field of exact algorithms [9, 17]. Its fast counterpart, called Fast Subset Convolution (FSC) [2], represented a breakthrough in the field by reducing the running time from the straightforward $O\left( {3}^{n}\right)$ to a non-trivial $O\left( {{2}^{n}{n}^{2}}\right)$ .

子集卷积是精确算法领域的重要工具之一[9,17]。其快速版本——快速子集卷积(FSC)[2]通过将运行时间从原始的$O\left( {3}^{n}\right)$降低到非平凡的$O\left( {{2}^{n}{n}^{2}}\right)$，代表了该领域的重大突破。

Dynamic Programming Speedups. The main application of FSC is the speedup of several dynamic programming recursions of well-known NP-hard problems, such as the Steiner tree problem [11] and min-cost $k$ -coloring [9]. While these problems may seem foreign to our research area,there is a striking similarity between the dynamic programming recursion of these problems and that of the join ordering problem. Indeed, they all use an implicit subset convolution. Through our work, join ordering is now becoming part of this family of problems $\left\lbrack  {3,9,{11},{37},{43}}\right\rbrack$ .

动态规划加速。FSC主要应用于加速若干著名NP难问题的动态规划递归计算，如斯坦纳树问题[11]和最小成本$k$着色问题[9]。虽然这些问题看似与我们的研究领域无关，但其动态规划递归与连接顺序问题存在惊人的相似性——它们都使用了隐式子集卷积。通过本研究，连接顺序问题现已成为这类问题家族的新成员$\left\lbrack  {3,9,{11},{37},{43}}\right\rbrack$。

While our main result mostly uses FSC in a black-box manner, we present the full machinery behind it in Sec. 4. Note that for an efficient implementation of the dynamic programs, we will revisit the computation of FSC in Sec. 5, and shave a linear factor for generic FSC-based dynamic programs, as well as several constant factors hidden behind the running time's big-O notation.

虽然我们的主要成果以黑盒方式使用FSC，但第4章节将完整揭示其背后的机制。值得注意的是，为实现动态规划算法的高效实施，我们将在第5章节重新审视FSC的计算过程，为基于FSC的通用动态规划程序削减一个线性因子，并优化隐藏在大O表示法中的若干常数因子。

Key Idea. Let us first gain an intuition about how subset convolution works at a high level. First, note that the DP-table is by definition a set function: It maps subsets of relations to their corresponding costs. The usual way to refer to a subset structure is by a subset lattice, in our case of order $n$ ,since we have $n$ relations. This leads to the following setting: Let $f$ and $g$ be two set functions on the subset lattice of order $n$ ,their subset convolution in the $\left( {+, \cdot  }\right)$ ring is defined for all $S \subseteq  \left\lbrack  n\right\rbrack   \mathrel{\text{:=}} \{ 1,\ldots ,n\}$ by

核心思想。首先从宏观层面理解子集卷积的运作原理：动态规划表本质上是一个集合函数——它将关系子集映射到相应成本值。通常通过$n$阶子集格（因涉及$n$个关系）来表述子集结构。具体而言：设$f$和$g$为$n$阶子集格上的两个集合函数，其在$\left( {+, \cdot  }\right)$环上的子集卷积对所有$S \subseteq  \left\lbrack  n\right\rbrack   \mathrel{\text{:=}} \{ 1,\ldots ,n\}$定义为

$$
h\left( S\right)  = \left( {f * g}\right) \left( S\right)  = \mathop{\sum }\limits_{{T \subseteq  S}}f\left( T\right) g\left( {S \smallsetminus  T}\right) .
$$

Let us first make a few observations: First,the above kind of subset convolution,in the $\left( {+, \cdot  }\right)$ ring, is not yet what we exactly need in DPconv. In the next paragraphs, we gradually introduce the toolset to support the subset convolution appearing in Eq. (5). Second, naively evaluating the above equation for all subsets $S$ takes $O\left( {3}^{n}\right)$ -time. This follows from the fact that for each $S$ we have to iterate over all its $\left( \begin{matrix} n \\  \left| S\right|  \end{matrix}\right)$ -many subsets $T{.}^{3}$

初步观察：首先，$\left( {+, \cdot  }\right)$环上的这类子集卷积并非DPconv所需的精确形式，后续段落将逐步引入支持公式(5)中子集卷积的工具集。其次，对所有子集$S$直接计算上述方程需要$O\left( {3}^{n}\right)$时间——这是因为每个$S$都需要遍历其$\left( \begin{matrix} n \\  \left| S\right|  \end{matrix}\right)$个子集$T{.}^{3}$。

Where The Speedup Comes From. The faster computation in Björklund et al. [2] has its roots in a simple observation: One can do a calculation similar to the FFT algorithm [8]. The reason: FFT was specifically designed to speed up sequence convolutions,bringing down the $O\left( {n}^{2}\right)$ -time of the naive algorithm to a (still unbeatable) $O\left( {n\log n}\right)$ -time. Its key insight was to (a) map the original sequence into a Fourier space, (b) perform the convolution in that space as a point-wise multiplication - which takes linear time instead - and (c) bring the result from the Fourier space back into the original one. The authors take a similar path,resulting in a running time of $O\left( {{2}^{n}{n}^{2}}\right)$ . The perhaps only difference to the original FFT algorithm is how the subset functions are mapped to a similar Fourier space where the convolution can be transformed into a point-wise multiplication.

加速原理。Björklund等人[2]的快速算法源于一个简单发现：可采用类似快速傅里叶变换(FFT)[8]的计算策略。FFT通过(a)将序列映射到傅里叶空间，(b)在该空间将卷积转化为线性时间的逐点乘法，(c)将结果逆映射回原空间，将朴素算法的$O\left( {n}^{2}\right)$时间复杂度降至（至今未被超越的）$O\left( {n\log n}\right)$。该研究遵循相似路径，最终实现$O\left( {{2}^{n}{n}^{2}}\right)$运行时间，与原始FFT算法的主要区别在于如何将子集函数映射到可进行逐点乘法的类傅里叶空间。

The Real Deal: Semi-Rings. Dynamic programs defined on sets often require the computation to be worked out in semi-rings. This is also the case in join ordering for ${C}_{\text{out }}$ and ${C}_{\max }$ : The well-known ${C}_{\text{out }}$ works in the (min,+) semi-ring,while ${C}_{\max }$ works instead in the (min,max) semi-ring. The (min,+) subset convolution of two set functions $f$ and $g,h = f \circ  g$ ,is defined for all $S \subseteq  \left\lbrack  n\right\rbrack$ as

核心概念：半环结构。定义在集合上的动态程序常需在半环中进行运算，连接排序中的${C}_{\text{out }}$和${C}_{\max }$亦是如此：著名的${C}_{\text{out }}$基于(min,+)半环运算，而${C}_{\max }$则采用(min,max)半环。对于两个集合函数$f$和$g,h = f \circ  g$，(min,+)子集卷积定义为对所有$S \subseteq  \left\lbrack  n\right\rbrack$满足

$$
h\left( S\right)  = \left( {f \circ  g}\right) \left( S\right)  = \mathop{\min }\limits_{{T \subseteq  S}}\left( {f\left( T\right)  + g\left( {S \smallsetminus  T}\right) }\right) . \tag{6}
$$

Unlike the previous kind of subset convolution,computing in the $\left( {\min , + }\right)$ semi-ring results in a different time-complexity landscape. Surprisingly enough,in the general setting,the naive $O\left( {3}^{n}\right)$ - time algorithm which we saw before has the best running time so far. However, in the case where the values of the set functions are bounded integers, one can leverage the previous fast subset convolution for the $\left( {+, \cdot  }\right)$ ring. We will come to this in the next section.

与前述子集卷积不同，在$\left( {\min , + }\right)$半环中的计算会形成不同的时间复杂度分布。值得注意的是，在通用场景下，此前提到的$O\left( {3}^{n}\right)$时间复杂度朴素算法仍是目前最优解。但当集合函数值为有界整数时，可借助$\left( {+, \cdot  }\right)$环的快速子集卷积算法，详见下节。

---

<!-- Footnote -->

${}^{3}$ Formally, $\mathop{\sum }\limits_{{k = 0}}^{n}\left( \begin{array}{l} n \\  k \end{array}\right) {2}^{k} = {\left( 1 + 2\right) }^{n} = {3}^{n}$ .

${}^{3}$ 形式化定义为 $\mathop{\sum }\limits_{{k = 0}}^{n}\left( \begin{array}{l} n \\  k \end{array}\right) {2}^{k} = {\left( 1 + 2\right) }^{n} = {3}^{n}$ 。

<!-- Footnote -->

---

<!-- Media -->

Algorithm 1: DPconv: Using fast subset convolution (FSC) to gradually optimize the dynamic programming table.

算法1：DPconv：利用快速子集卷积(FSC)逐步优化动态规划表

---

Input: Query graph $Q = \left( {V,E}\right)$ ,cardinality function $c$

输入：查询图$Q = \left( {V,E}\right)$，基数函数$c$

Output: Optimal cost value w.r.t. $C$

输出：关于$C$的最优成本值

$\mathrm{{DP}}\left\lbrack  \varnothing \right\rbrack   \leftarrow   + \infty$

$\operatorname{DP}\left\lbrack  \left\{  {R}_{i}\right\}  \right\rbrack   \leftarrow  0,\forall {R}_{i} \in  V$

for each $k$ in $2,\ldots ,\left| V\right|$ do

对$2,\ldots ,\left| V\right|$中的每个$k$执行

	${\mathrm{{DP}}}^{\prime } \leftarrow  {\mathrm{{FSC}}}_{\left( \min , \otimes  \right) }\left( {\mathrm{{DP}},\mathrm{{DP}}}\right)$

	$\mathrm{{DP}}\left\lbrack  S\right\rbrack   \leftarrow  {\mathrm{{DP}}}^{\prime }\left\lbrack  S\right\rbrack   \otimes  c\left( S\right) ,\forall S$ s.t. $\left| S\right|  = k$

	$\mathrm{{DP}}\left\lbrack  S\right\rbrack   \leftarrow  {\mathrm{{DP}}}^{\prime }\left\lbrack  S\right\rbrack   \otimes  c\left( S\right) ,\forall S$ 满足 $\left| S\right|  = k$

end for

结束循环

return $\operatorname{DP}\left\lbrack  V\right\rbrack$

返回$\operatorname{DP}\left\lbrack  V\right\rbrack$

---

<!-- Media -->

### 2.5 Rings & Semi-Rings

### 2.5 环与半环

We have mentioned rings and semi-rings several times so far. We now want to introduce them in the context of dynamic programming and, more specifically, join ordering. We will focus in particular on the $\left( {+, \cdot  }\right)$ ring and the $\left( {\min , + }\right)$ and $\left( {\min ,\max }\right)$ semi-rings. Note that we are only aiming for an intuitive understanding of why supporting the latter is much more complex than the simple ring setting,where fast subset convolution can work directly in $O\left( {{2}^{n}{n}^{2}}\right)$ -time,as shown earlier in Sec. 2.4.

我们已多次提及环与半环，现将在动态规划特别是连接排序的语境中展开说明。重点讨论$\left( {+, \cdot  }\right)$环及$\left( {\min , + }\right)$、$\left( {\min ,\max }\right)$半环。需注意的是，我们仅直观阐释为何后者支持比简单环复杂得多——如2.4节所示，快速子集卷积在环结构中可直接实现$O\left( {{2}^{n}{n}^{2}}\right)$时间复杂度。

Intuition. Within the pair of operators of a (semi-)ring, the first one is decisive. In our case, while the $\left( {+, \cdot  }\right)$ ring has "+" as its first operator,both $\left( {\min , + }\right)$ and $\left( {\max ,\max }\right)$ have "min". To understand the contrast between these,consider the following illustrative example: If we calculate $2 + 3 + 5 = {10}$ and want to remove one of the first terms, e.g., 2 , we can recover the sum of the other terms by using the inverse of 2,i.e., ${10} + \left( {-2}\right)  = 8$ . Things are not so clear in the case of "min": If we have $\min \{ 2,3,5\}  = 2$ and want to remove 2,we cannot simply recover the minimum of the remaining elements, $\min \{ 3,5\}$ . The underlying problem is that has 2 no inverse. This example may seem artificial at first, but this very problem occurs when applying the inverse map to come back from the Fourier space - essentially step (c) above. How can this be alleviated? What Björklund et al. [2] propose is a standard trick in algorithms: embed the $\left( {\min , + }\right)$ semi-ring in the $\left( {+, \cdot  }\right)$ ring. We explain and exemplify this technique in Sec. 3.2.

直觉。（半）环中的运算符对中，第一个运算符起决定性作用。在本例中，虽然$\left( {+, \cdot  }\right)$环以"+"作为其首个运算符，但$\left( {\min , + }\right)$和$\left( {\max ,\max }\right)$都采用"min"。为理解二者差异，请看示例：若计算$2 + 3 + 5 = {10}$时需移除首项（如2），可通过2的逆元${10} + \left( {-2}\right)  = 8$恢复其他项之和。但"min"运算则不然：给定$\min \{ 2,3,5\}  = 2$时若移除2，无法简单推导剩余元素$\min \{ 3,5\}$的最小值——核心问题在于2没有逆元。此例看似刻意，却正是傅里叶逆变换（即前文步骤c）时遭遇的难题。如何解决？Björklund等人[2]提出算法中的标准技巧：将$\left( {\min , + }\right)$半环嵌入$\left( {+, \cdot  }\right)$环。第3.2节将详解此技术。

In the following, we relate the join ordering problem to fast subset convolution for the first time, and provide a unified framework that can be instantiated for several cost functions.

下文首次将连接顺序问题与快速子集卷积关联，并提出可适配多种成本函数的统一框架。

## 3 OUR FRAMEWORK

## 3 我们的框架

Let us consider the optimization of an arbitrary cost function $C$ in its associated (min, $\otimes$ ) semi-ring under a generic framework. We will then instantiate the framework for ${C}_{\text{out }}$ and ${C}_{\max }$ ,respectively.

考虑在通用框架下优化任意成本函数$C$，该函数关联于(min,$\otimes$)半环。随后将分别针对${C}_{\text{out }}$和${C}_{\max }$实例化该框架。

### 3.1 Join Ordering Meets Subset Convolution

### 3.1 连接顺序与子集卷积的交汇

The key observation behind our results is the (now trivial) observation that the definition of DP-recursion, Eq. (5), is similar to that of subset convolution, Eq. (6). In particular, we show that join ordering falls into the category of dynamic programs which fast subset convolution has already been applied to. In our specific context, there are a few (minor) issues that need to be addressed for FSC to be applicable, issues that have also been considered by Björklund et al. [2] for other problems, namely:

本研究的关键发现（如今看来显而易见）在于：DP递归定义式(5)与子集卷积式(6)具有相似性。特别地，我们证明连接顺序属于动态规划范畴，而快速子集卷积已在该领域得到应用。具体实施时需解决若干（次要）问题，Björklund等人[2]处理其他问题时亦考虑过类似情况，包括：

<!-- Media -->

Algorithm 2: BuildJoinTree: Recursively extracting the optimal bushy join tree from the DP-table

算法2：BuildJoinTree：从DP表递归提取最优浓密连接树

---

Input: Subset of relations $S$ ,DP-table

输入：关系子集$S$，DP表

Output: The optimal bushy join tree

输出：最优浓密连接树

if $\left| S\right|  = 1$ return $S$ end if

若$\left| S\right|  = 1$则返回$S$

for each $\varnothing  \subset  T \subset  S$ do

遍历$\varnothing  \subset  T \subset  S$执行

	if $c\left( S\right)  \otimes  \mathrm{{DP}}\left( T\right)  \otimes  \mathrm{{DP}}\left( {S \smallsetminus  T}\right)  = \mathrm{{DP}}\left( S\right)$ then

	若$c\left( S\right)  \otimes  \mathrm{{DP}}\left( T\right)  \otimes  \mathrm{{DP}}\left( {S \smallsetminus  T}\right)  = \mathrm{{DP}}\left( S\right)$成立

		return (BuildJoinTree(T),BuildJoinTree $\left( {S \smallsetminus  T}\right)$ )

		返回(BuildJoinTree(T),BuildJoinTree$\left( {S \smallsetminus  T}\right)$)

	end if

	结束条件

end for

结束遍历

---

<!-- Media -->

(i) The dynamic program DP, Eq. (5), is defined recursively.

(i) 动态规划DP式(5)采用递归定义

(ii) The subset $T$ of $S$ in the same Eq. (5) must not take $\varnothing$ nor $S$ as value.

(ii) 同一方程(5)中$T$的子集$S$不得取$\varnothing$或$S$作为值。

Overview. Both issues are resolved by a simple technique: We apply FSC layer-wise, i.e., we optimize sets of size 2 first, then those of size 3, and so on - this is what we call a layer. Specifically, at each layer $k$ ,since the DP-table has been computed for layers ${k}^{\prime } < k$ ,we can directly optimize $\mathrm{{DP}}\left\lbrack  S\right\rbrack$ for all $S$ with $\left| S\right|  = k$ by a call to FSC. To alleviate issue (ii),we set $\mathrm{{DP}}\left\lbrack  \varnothing \right\rbrack$ ,i.e.,the $\mathrm{{DP}}$ -cell representing the empty set of relations,to $+ \infty {.}^{4}$ Since FSC is called $n$ times,the total optimization time adds up to $O\left( {{2}^{n}{n}^{3}{\tau }_{C}}\right)$ ,where the function ${\tau }_{C}$ is tailored to the specific cost function $\mathcal{C}$ and accounts for the time overhead of semi-ring operations. We will cover the exact expressions of ${\tau }_{C}$ for individual cost functions in the following sections (see Sec. 3.3 for ${C}_{\text{out }}$ and Sec. 3.4 for ${C}_{\max }$ ).

概述。通过一种简单技术可同时解决这两个问题：我们逐层应用FSC（分步集覆盖），即先优化大小为2的集合，然后是大小为3的集合，依此类推——这被称为层级。具体而言，在每一层$k$，由于动态规划表已为${k}^{\prime } < k$层完成计算，可通过调用FSC直接优化所有满足$\left| S\right|  = k$条件的$S$的$\mathrm{{DP}}\left\lbrack  S\right\rbrack$。为缓解问题(ii)，我们将表示空关系集的$\mathrm{{DP}}$单元$\mathrm{{DP}}\left\lbrack  \varnothing \right\rbrack$设为$+ \infty {.}^{4}$。由于FSC被调用$n$次，总优化时间累计为$O\left( {{2}^{n}{n}^{3}{\tau }_{C}}\right)$，其中函数${\tau }_{C}$专为特定成本函数$\mathcal{C}$定制，并计入半环运算的时间开销。后续章节将详述各成本函数下${\tau }_{C}$的具体表达式（${C}_{\text{out }}$见第3.3节，${C}_{\max }$见第3.4节）。

Note that we will shave a factor of $O\left( n\right)$ from the running time of generic FSC-based dynamic programs,including ours,in Sec. 5,thus reducing the total running time to $O\left( {{2}^{n}{n}^{2}{\tau }_{C}}\right)$ -time for a cost function $C$ .

注意我们将在第5节从通用FSC动态规划（包括本方案）的运行时间中削减$O\left( n\right)$因子，从而将总运行时间降至$O\left( {{2}^{n}{n}^{2}{\tau }_{C}}\right)$（针对成本函数$C$）。

Pseudocode. In Alg. 1, we outline the pseudocode behind our framework DPconv. It takes the query graph $Q$ and the join cardinality function $c$ as input and outputs the optimal cost value w.r.t. the specific cost function $\mathcal{C}$ to which the semi-ring $\left( {\min , \otimes  }\right)$ corresponds. It first optimizes the base cases, namely for the empty set of relations and for all sets containing only one relation. The former are initialized with $+ \infty$ ,as argued above,and the latter with 0,cf. Eq. (5). Then,at each layer $k$ ,we optimize the subsets of size $k$ by calling FSC on the current state of the DP-table (line 6) and then update the values with the join cardinalities of the subsets (line 7). To this end, note that Alg. 1 can optimize for cross-products out of the box: We simply need to also use the cardinalities of all cross-products in $c$ . The running time remains naturally the same. Finally,we return the optimal cost represented by $\mathrm{{DP}}\left\lbrack  V\right\rbrack$ .

伪代码。算法1概述了DPconv框架的伪代码实现。该算法以查询图$Q$和连接基数函数$c$为输入，输出与半环$\left( {\min , \otimes  }\right)$对应的特定成本函数$\mathcal{C}$的最优成本值。它首先优化基础案例：空关系集和单关系集。前者按前述初始化为$+ \infty$，后者根据方程(5)设为0。接着在每层$k$，通过在当前DP表状态调用FSC优化大小为$k$的子集（第6行），并用子集连接基数更新值（第7行）。值得注意的是，算法1可直接优化笛卡尔积：只需在$c$中同时使用所有笛卡尔积的基数，运行时间保持不变。最终返回$\mathrm{{DP}}\left\lbrack  V\right\rbrack$表示的最优成本。

Join Tree Extraction. Note that unlike previous algorithms, Alg. 1 does not maintain an OPT-table that stores the optimal split for each subset $S$ . This is because FSC itself does not keep track of this information during its execution. In contrast, after the DP-table is fully-optimized, we can extract the optimal join tree from the DP-table itself,as outlined in Alg. 2. Specifically,for each set $S$ ,we find the subset $T$ that was intrinsically used in FSC to optimize $S$ ,i.e., $\operatorname{DP}\left\lbrack  S\right\rbrack   = \operatorname{DP}\left\lbrack  T\right\rbrack   + \operatorname{DP}\left\lbrack  {S \smallsetminus  T}\right\rbrack$ . Since there are at most $n$ levels of recursion,the worst-case running time for Alg. 2 reads $O\left( {{2}^{n}n}\right)$ .

连接树提取。与先前算法不同，算法1不维护记录各子集$S$最优分割的OPT表，因为FSC执行过程本身不追踪该信息。相反，在DP表完全优化后，可按算法2所述从DP表提取最优连接树。具体而言，对每个集合$S$，找出FSC内部用于优化该集的子集$T$，即$\operatorname{DP}\left\lbrack  S\right\rbrack   = \operatorname{DP}\left\lbrack  T\right\rbrack   + \operatorname{DP}\left\lbrack  {S \smallsetminus  T}\right\rbrack$。由于递归最多有$n$层，算法2最坏情况运行时间为$O\left( {{2}^{n}n}\right)$。

---

<!-- Footnote -->

${}^{4}$ This is not mathematically rigorous. One has to define what $+ \infty$ in the specific semi-ring is.

${}^{4}$ 这在数学上并不严谨，需明确定义特定半环中的$+ \infty$。

<!-- Footnote -->

---

Cross-Products. While allowing cross-products can lead to better overall costs [36, 38], the search space increases exponentially [36]. A prominent way to deal with cross-products is to heuristically insert them when they are guaranteed to be beneficial [36]; this tends to be the case when the estimated cardinality of the input is small enough [26, 38].

交叉乘积。虽然允许交叉乘积能带来更优的总体成本[36,38]，但搜索空间会呈指数级增长[36]。处理交叉乘积的典型方法是启发式地插入那些确定有益的情况[36]——当输入关系的预估基数足够小时往往符合这种条件[26,38]。

A natural question is whether DPconv could also support the optimization of cross-products, and whether this particular optimization would take more time than previously specified. Similar to DPsub [48,49],we can use the cardinalities of the cross-products directly in $c$ (line 7,Alg. 1). This means that DPconv can optimize for cross-products for both cost functions without any overhead.

一个自然的问题是DPconv是否也能支持交叉乘积优化，以及该优化是否会耗时超过既定值。类似于DPsub[48,49]，我们可直接在$c$中使用交叉乘积的基数（算法1第7行）。这意味着DPconv能为两种成本函数优化交叉乘积且无需额外开销。

Query Hypergraphs. A standard way to model arbitrary non-inner joins in the query graph is to introduce corresponding binary join hyperedges. A binary join hyperedge $h = \left( {A,B}\right)$ connects two sets of relations $A$ and $B$ [31]. This is a generalization of the regular join edge which connects only two relations. In the query hypergraph setting, whenever we want to join two sets of relations connected by a hyperedge, we have to check that both sides are themselves connected and there is a hyperedge connecting them. Fortunately, since Eq. (5) enforces that the join cardinalities are taken into account only after the DP-layer has been optimized (lines 6-7, Alg. 1), we can directly specify which subgraphs are connected (using Ref. [31]); this is independent of whether the subgraph contains hyperedges or not. Extending our framework to optimize group-by operators optimally, as in Eich et al. [12], is an interesting future work.

查询超图。在查询图中建模任意非内连接的标准方法是引入对应的二元连接超边。二元连接超边$h = \left( {A,B}\right)$连接两个关系集$A$和$B$[31]，这是仅连接两个关系的常规连接边的泛化。在查询超图设定中，当需要连接由超边关联的两个关系集时，必须确认双方自身连通且存在连接它们的超边。幸运的是，由于公式(5)强制要求连接基数仅在DP层优化后才被纳入考量（算法1第6-7行），我们可以直接指定哪些子图是连通的（引用[31]），这与子图是否包含超边无关。如Eich等人[12]所述，将我们的框架扩展至最优分组操作符优化是个值得探索的未来方向。

We now come to the embedding technique we motivated and mentioned in Sec. 2.4 that helps us leverage the running time of the fast subset convolution to our employed semi-rings.

现在介绍我们在2.4节提及的嵌入技术，该技术能帮助我们利用快速子集卷积的运行时间来适配所采用的半环。

### 3.2 Embedding Technique

### 3.2 嵌入技术

Recall the motivating example in the section on rings and semi-rings (Sec. 2.5). To enable the existence of the inverse element, Björklund et al. [2] propose to embed the semi-ring into a ring,

回顾关于环与半环的动机示例（2.5节）。为实现逆元存在性，Björklund等人[2]提出将半环嵌入环结构：

Polynomials to the Rescue. The embedding technique maps the values of the set functions to monomials and then runs the fast subset convolution algorithm in the $\left( {+, \cdot  }\right)$ ring. The convolution values can then be read from the resulting polynomials. To see why this works, consider the functions $\left\lbrack  {2,1,3,4}\right\rbrack$ and $\left\lbrack  {5,0,1,2}\right\rbrack$ . When we embed these set functions to monomials,we obtain $\left\lbrack  {{x}^{2},{x}^{1},{x}^{3},{x}^{4}}\right\rbrack$ and $\left\lbrack  {{x}^{5},{x}^{0},{x}^{1},{x}^{2}}\right\rbrack$ ,respectively. Thus,by running their subset convolution $\left\lbrack  {{x}^{2},{x}^{1},{x}^{3},{x}^{4}}\right\rbrack   * \left\lbrack  {{x}^{5},{x}^{0},{x}^{1},{x}^{2}}\right\rbrack$ ,we can retrieve the final values as follows: Consider the value at 001,which is ${x}^{2 + 0} + {x}^{1 + 5}$ . Note that multiplication between monomials is simply an addition at the exponent level,while the minimum-in our case, $\min \{ 2 + 0,1 + 5\}  -$ is represented by the smallest exponent in the resulting polynomial.

多项式解决方案。嵌入技术将集合函数值映射为单项式，然后在$\left( {+, \cdot  }\right)$环中运行快速子集卷积算法，最终可从生成的多项式中读取卷积值。其原理如下：考虑函数$\left\lbrack  {2,1,3,4}\right\rbrack$和$\left\lbrack  {5,0,1,2}\right\rbrack$，当将其嵌入为单项式时，分别得到$\left\lbrack  {{x}^{2},{x}^{1},{x}^{3},{x}^{4}}\right\rbrack$和$\left\lbrack  {{x}^{5},{x}^{0},{x}^{1},{x}^{2}}\right\rbrack$。因此通过计算它们的子集卷积$\left\lbrack  {{x}^{2},{x}^{1},{x}^{3},{x}^{4}}\right\rbrack   * \left\lbrack  {{x}^{5},{x}^{0},{x}^{1},{x}^{2}}\right\rbrack$，可如下获取最终值：观察001处的值${x}^{2 + 0} + {x}^{1 + 5}$，注意单项式乘法在指数层面表现为加法，而最小值（本例中的$\min \{ 2 + 0,1 + 5\}  -$）则由结果多项式中的最小指数表示。

Representation. To allow for a seamless instantiation of our framework for other cost functions, we represent the polynomials in coefficient form, i.e., pairs of exponents and their associated coefficients. For instance,we represent ${2x} + 3{x}^{4}$ as $\{ \left( {1,2}\right) ,\left( {4,3}\right) \}$ .

表示方法。为实现本框架对其他成本函数的无缝实例化，我们采用系数形式表示多项式，即指数与其对应系数的组合。例如，将${2x} + 3{x}^{4}$表示为$\{ \left( {1,2}\right) ,\left( {4,3}\right) \}$。

Limitation. The core limitation of the embedding technique is that the size of the coefficient forms exactly corresponds to the largest input value. The reason is that value will be the largest exponent in the entire embedding of the corresponding set function.

局限性。该嵌入技术的核心局限在于系数形式的大小严格取决于输入最大值。这是因为该值将成为对应集合函数嵌入过程中的最大指数。

In the following,we instantiate the framework for ${C}_{\text{out }}$ and ${C}_{\max }$ ,respectively. In Sec. 6,we show a simpler algorithm to optimize for ${C}_{\max }$ that bypasses the need for the embedding technique.

下文将分别针对${C}_{\text{out }}$和${C}_{\max }$实例化本框架。在第6节中，我们将展示一种更简单的${C}_{\max }$优化算法，无需依赖嵌入技术。

### 3.3 Instantiating ${C}_{\text{out }}$

### 3.3 ${C}_{\text{out }}$实例化

In the case of ${C}_{\text{out }}$ ,we are working in the $\left( {\min , + }\right)$ semi-ring. To implement the embedding,we simply need to specify how the " + " operator should work - in the most general form, the " $\otimes$ " operator; compare Eq. (2). This corresponds to polynomial multiplication in the coefficient form. Let ${P}_{1}$ and ${P}_{2}$ be two polynomials in coefficient form. Then ${P}_{1} \otimes  {P}_{2}$ for an exponent $e$ is defined as

对于${C}_{\text{out }}$的情况，我们工作在$\left( {\min , + }\right)$半环中。实现嵌入只需定义"+"运算符的运作方式——最广义形式下即"$\otimes$"运算符（参见公式(2)）。这对应于系数形式下的多项式乘法。设${P}_{1}$和${P}_{2}$为两个系数形式多项式，则指数$e$对应的${P}_{1} \otimes  {P}_{2}$定义为

$$
\left( {{P}_{1} \otimes  {P}_{2}}\right) \left( e\right)  = \mathop{\sum }\limits_{\substack{{\left( {{e}_{1},{c}_{1}}\right)  \in  {P}_{1}} \\  {\left( {{e}_{2},{c}_{2}}\right)  \in  {P}_{2}} \\  {{e}_{1} + {e}_{2} = e} }}{c}_{1}{c}_{2}. \tag{7}
$$

Since the maximum value of the ${C}_{\text{out }}$ cost function could be ${Wn}$ (recall that $W$ is the largest join cardinality) and assuming a FFT-based implementation of the convolution in Eq. (7),the factor ${\tau }_{\text{out }}$ for supporting ${C}_{\text{out }}$ is $O\left( {{Wn}\log {Wn}}\right)$ .

鉴于${C}_{\text{out }}$成本函数的最大值可能达到${Wn}$（注意$W$为最大连接基数），并假设基于FFT实现公式(7)中的卷积运算，支持${C}_{\text{out }}$的系数${\tau }_{\text{out }}$应为$O\left( {{Wn}\log {Wn}}\right)$。

### 3.4 Instantiating ${C}_{\max }$

### 3.4 ${C}_{\max }$实例化

We now specify the embedding for the (min,max) semi-ring. Unlike ${C}_{\text{out }}$ ,we need to specify how the "max" operator should work. Namely,the coefficient of exponent $e$ of two polynomials ${P}_{1}$ and ${P}_{2}$ in coefficient form reads:

现在定义(min,max)半环的嵌入方式。与${C}_{\text{out }}$不同，此处需规定"max"运算符的行为。具体而言，两个系数形式多项式${P}_{1}$和${P}_{2}$中指数$e$的系数表示为：

$$
\left( {{P}_{1} \otimes  {P}_{2}}\right) \left( e\right)  = \mathop{\sum }\limits_{\substack{{\left( {{e}_{1},{c}_{1}}\right)  \in  {P}_{1}} \\  {\left( {{e}_{2},{c}_{2}}\right)  \in  {P}_{2}} \\  {\max \left( {{e}_{1},{e}_{2}}\right)  = e} }}{c}_{1}{c}_{2}. \tag{8}
$$

The intuition is that all exponents below $e$ contribute to its final coefficient. If used as in Eq. (8),the size of the coefficient form will still be $W$ ,as in the case of ${C}_{\text{out }}$ ; this is prohibitively expensive. While there is indeed a way to mitigate this and obtain a running time of $O\left( {{2}^{n}{n}^{4}}\right)$ ,which is independent of $W$ ,we discovered a much simpler algorithm with an even better running time of $O\left( {{2}^{n}{n}^{3}}\right)$ ,which does not require the embedding technique. This is understandable due to the fact that, in the (min,max) semi-ring,we are not creating new values,as is the case in ${C}_{\text{out }}$ . To not burden the reader with the technicalities of the first approach, we will present directly the simpler algorithm; we continue its presentation in Sec. 6.

其原理是：所有低于$e$的指数都会影响最终系数。若按公式(8)使用，系数形式的大小仍为$W$（与${C}_{\text{out }}$情况相同），这在实际应用中代价过高。虽然存在方法可缓解此问题并获得$O\left( {{2}^{n}{n}^{4}}\right)$的时间复杂度（与$W$无关），但我们发现了一个更简洁的算法，其时间复杂度更优（$O\left( {{2}^{n}{n}^{3}}\right)$），且无需嵌入技术。这可以理解，因为在(min,max)半环中不会像${C}_{\text{out }}$那样产生新值。为避免技术细节干扰读者，我们将直接介绍该简化算法，具体内容在第6节展开。

### 3.5 Beyond ${C}_{\text{out }}$ and ${C}_{\max }$

### 3.5 超越${C}_{\text{out }}$与${C}_{\max }$

Beside ${C}_{\text{out }}$ and ${C}_{\max }$ ,literature on join ordering also considers other cost functions: Moerkotte [29] mentions cost functions related to (a) nested-loop, (b) hash, and (c) sort-merge joins. These cost functions have been mainly designed for left-deep join trees. However, it is an easy exercise to remodel them to work on bushy joins trees. We show that DPconv can be extended to the cost function associated to the sort-merge join.

除${C}_{\text{out }}$和${C}_{\max }$外，连接排序文献还涉及其他成本函数：Moerkotte[29]提及与(a)嵌套循环、(b)哈希及(c)排序归并连接相关的成本函数。这些函数主要针对左深连接树设计，但改造为适用于浓密连接树并不困难。我们将证明DPconv可扩展至排序归并连接相关的成本函数。

The problem is that these cost functions require that $c\left( T\right)$ be rewritten in terms of ${\mathcal{T}}_{1}$ and ${\mathcal{T}}_{2}$ . That is,instead of $c\left( T\right)$ ,we would need to write $c\left( {{T}_{1},{T}_{2}}\right)$ ; see the below example. The key idea to solve this is to first check whether $c\left( {{T}_{1},{T}_{2}}\right)$ can be separated into two independent terms depending only on ${T}_{1}$ and ${T}_{2}$ ,respectively.

问题在于这些成本函数要求将$c\left( T\right)$改写为基于${\mathcal{T}}_{1}$和${\mathcal{T}}_{2}$的表达式。也就是说，我们需要用$c\left( {{T}_{1},{T}_{2}}\right)$替代$c\left( T\right)$；参见下方示例。解决此问题的核心思路是首先检查$c\left( {{T}_{1},{T}_{2}}\right)$能否被分解为两个独立项，分别仅依赖于${T}_{1}$和${T}_{2}$。

When It Works. We take as running example the sort-merge join cost. Adapting the definition by Moerkotte [29, Sec. 3.1.3], we have:

适用场景。我们以排序归并连接成本作为运行示例。根据Moerkotte[29,第3.1.3节]的定义调整后，可得：

$$
{C}_{\mathrm{{smj}}}\left( \mathcal{T}\right)  = \left\{  \begin{array}{ll} 0, & \text{ if }\mathcal{T}\text{ is a single relation } \\  c\left( {T}_{1}\right) \log c\left( {T}_{1}\right) & \\   + c\left( {T}_{2}\right) \log c\left( {T}_{2}\right) & \\   + {C}_{\mathrm{{smj}}}\left( {\mathcal{T}}_{1}\right)  + {C}_{\mathrm{{smj}}}\left( {\mathcal{T}}_{2}\right) , & \text{ if }\mathcal{T} = {\mathcal{T}}_{1} \bowtie  {\mathcal{T}}_{2}, \end{array}\right.  \tag{9}
$$

where ${T}_{1}$ and ${T}_{2}$ are the set of relations corresponding to ${\mathcal{T}}_{1}$ and ${\mathcal{T}}_{2}$ ,respectively,and $c\left( {T}_{1}\right)$ and $c\left( {T}_{2}\right)$ are the corresponding join cardinalities. Note the change to our original $c\left( T\right)$ in Eq. (2): The split $\mathcal{T} = {\mathcal{T}}_{1} \bowtie  {\mathcal{T}}_{2}$ now plays a role. For our framework,this means that we can no longer simply optimize the subset convolution part separately (see line 6 of Alg. 1). We also need to account for the actual sort-merge join cost at the current join, $c\left( {T}_{1}\right) \log c\left( {T}_{1}\right)  + c\left( {T}_{2}\right) \log c\left( {T}_{2}\right)$ . However,there is a simple solution to fix this: We can separate the sort-merge join cost and integrate that into the corresponding side,i.e.,either ${\mathcal{T}}_{1}$ or ${\mathcal{T}}_{2}$ . Concretely,we need to modify line 6 in Alg. 1 as follows:

其中${T}_{1}$和${T}_{2}$分别是与${\mathcal{T}}_{1}$和${\mathcal{T}}_{2}$对应的关系集合，$c\left( {T}_{1}\right)$和$c\left( {T}_{2}\right)$为相应的连接基数。注意公式(2)中对我们原始$c\left( T\right)$的修改：分割项$\mathcal{T} = {\mathcal{T}}_{1} \bowtie  {\mathcal{T}}_{2}$现在发挥作用。这意味着在我们的框架中，无法再简单地单独优化子集卷积部分（参见算法1第6行），还需考虑当前连接$c\left( {T}_{1}\right) \log c\left( {T}_{1}\right)  + c\left( {T}_{2}\right) \log c\left( {T}_{2}\right)$处的实际排序归并连接成本。不过有个简单解决方案：可将排序归并连接成本分离并整合到对应侧（即${\mathcal{T}}_{1}$或${\mathcal{T}}_{2}$）。具体需将算法1第6行修改为：

$$
{\mathrm{{FSC}}}_{\left( \min , + \right) }\left( {\mathrm{{DP}} + c\log c}\right) \text{,}
$$

where the inner function, $\mathrm{{DP}} + c\log c$ ,is applied point-wise to each set $S \subseteq  \left\lbrack  n\right\rbrack$ . Put simple,we also add to each DP-entry the sort-merge join cost corresponding to each side. This does not incur a large overhead in the optimization time, as the addition can be performed when pre-processing the zeta transforms of the DP-layers (see Sec. 5.1).

其中内层函数$\mathrm{{DP}} + c\log c$会逐点应用于每个集合$S \subseteq  \left\lbrack  n\right\rbrack$。简言之，我们还在每个动态规划条目中增加了对应侧的排序归并连接成本。这不会显著增加优化时间开销，因为加法运算可在预处理动态规划层的zeta变换时完成（见第5.1节）。

When It Does Not Work. Note that this adaptation to the sort-merge join worked because we could split the initial $c\left( {{T}_{1},{T}_{2}}\right)$ into two separate cost factors that could be "sinked" in the entries of the DP table. To support other cost functions, they need to have a similar additive separation property. For instance,the nested-loop join cost, $c\left( {T}_{1}\right) c\left( {T}_{2}\right)$ ,does not enjoy this property. This means that our subset convolution based framework, DPconv, cannot be extended to this cost function. A similar situation holds for the hash-join cost, at least in the (classic) setting we are considering: $c\left( {{T}_{1},{T}_{2}}\right)  = {1.2}\max \left\{  {c\left( {T}_{1}\right) ,c\left( {T}_{2}\right) }\right\}$ . This extension from Moerkotte’s definition mainly designed for left-deep join trees [29, Sec. 3.1.3] takes into account that the hash-table is built on the smaller side,known as the build side. The issue is that "max" in $c\left( {{T}_{1},{T}_{2}}\right)$ destroys its additive separability into two cost functions depending only on ${T}_{1}$ and ${T}_{2}$ . Therefore,the hash-join cost function cannot also be supported in our framework.

非适用场景。之所以能适配排序归并连接，是因为初始成本$c\left( {{T}_{1},{T}_{2}}\right)$可拆分为两个能"下沉"到动态规划表条目中的独立成本因子。要支持其他成本函数，它们需具备类似的加法可分性。例如嵌套循环连接成本$c\left( {T}_{1}\right) c\left( {T}_{2}\right)$就不满足此特性，这意味着我们基于子集卷积的DPconv框架无法扩展至该成本函数。哈希连接成本在（经典）设定下同样如此：$c\left( {{T}_{1},{T}_{2}}\right)  = {1.2}\max \left\{  {c\left( {T}_{1}\right) ,c\left( {T}_{2}\right) }\right\}$。这个源自Moerkotte定义的扩展主要针对左深连接树[29,第3.1.3节]，考虑在较小侧（构建侧）建立哈希表。问题在于$c\left( {{T}_{1},{T}_{2}}\right)$中的"max"运算破坏了其对于${T}_{1}$和${T}_{2}$的加法可分性，因此我们的框架也不支持哈希连接成本函数。

## 4 FAST SUBSET CONVOLUTION

## 4 快速子集卷积

We next describe Fast Subset Convolution (FSC). We take a closer look at FSC from a practical perspective,so that in Sec. 5 we can shave the promised $O\left( n\right)$ -time factor from the running time of DPconv. In the following, we adopt the notation from the Parameterized Algorithms book [9], since it has established itself in the literature compared to that of Björklund et al. [2].

接下来我们将阐述快速子集卷积(FSC)算法。从实践角度深入剖析FSC，以便在第5节中能从DPconv运行时间中削减承诺的$O\left( n\right)$倍时间因子。下文采用《参数化算法》专著[9]的符号体系，因其相比Björklund等人[2]的表述已在学术界形成共识。

### 4.1 Zeta Transform

### 4.1 泽塔变换

A fundamental operation in FSC is the zeta transform, defined as

FSC的核心运算是泽塔变换，其定义为

$$
\left( {\zeta f}\right) \left( S\right)  = \mathop{\sum }\limits_{{T \subseteq  S}}f\left( T\right)  \tag{10}
$$

for any $S \subseteq  \left\lbrack  n\right\rbrack$ . That is,the zeta transform sums $f$ at all subsets of $S$ . Naively,this can be computed in $O\left( {3}^{n}\right)$ -time for all $S \subseteq  \left\lbrack  n\right\rbrack$ . However,we can compute it in $O\left( {{2}^{n}n}\right)$ -time by observing that we can reuse the computation done for subsets. We detail this in Sec. 4.4.

对于任意$S \subseteq  \left\lbrack  n\right\rbrack$。该变换将$f$在$S$的所有子集上求和。朴素实现需要$O\left( {3}^{n}\right)$时间复杂度，但通过复用子集计算结果可优化至$O\left( {{2}^{n}n}\right)$时间复杂度，详见第4.4节。

### 4.2 Ranked Convolution

### 4.2 分级卷积

Given ${\zeta f}$ and ${\zeta g}$ ,the zeta transform of the actual convolution $h = f * g$ ,i.e., ${\zeta h}$ ,can now be computed point-wise. To this end, Björklund et al. [2] employ a ranked convolution. Formally,

给定${\zeta f}$和${\zeta g}$后，实际卷积$h = f * g$(即${\zeta h}$)的泽塔变换可逐点计算。为此Björklund等人[2]采用分级卷积，其形式化定义为

$$
\left( {\zeta h}\right) \left( {S,r}\right)  = \mathop{\sum }\limits_{{d = 0}}^{r}\left( {\zeta f}\right) \left( {S,d}\right) \left( {\zeta g}\right) \left( {S,r - d}\right) , \tag{11}
$$

for any $S \subseteq  \left\lbrack  n\right\rbrack$ ,where $\left| S\right|  = r$ . Thus,we have to apply a zeta transform for each rank,i.e.,for each cardinality in $\{ 0,\ldots ,n\}$ . The ranked convolution can then be computed naively in $O\left( {{2}^{n}{n}^{2}}\right)$ ,as for each rank $r$ we need to iterate over all $d \leq  r$ .

对于任意$S \subseteq  \left\lbrack  n\right\rbrack$，其中$\left| S\right|  = r$。因此需要对每个秩(即$\{ 0,\ldots ,n\}$中的每个基数)单独应用泽塔变换。分级卷积的朴素实现复杂度为$O\left( {{2}^{n}{n}^{2}}\right)$，因为每个秩$r$都需要遍历所有$d \leq  r$。

<!-- Media -->

<!-- figureText: Ranked ${\zeta f}$ Ranked ${\zeta g}$ Ranked ${\zeta h}$ Ranked $h$ $t = f * g$ 000 001 010 011 100 101 110 111 000 001 010 011 100 101 110 111 0: Convolution $\left\lbrack  \begin{matrix} 0 & 0 & 2 & 0 & 1 & 0 & 0 & 0 \end{matrix}\right\rbrack$ $\begin{matrix} 0 & 0 & 0 & 4 & 2 & 3 & 4 & 0 \end{matrix}$ 111 000 001 010 011 100 101 110 111 000 001 010 011 100 101 110 111 $\begin{array}{llllllll} 0 & 0 & 2 & 2 & 1 & 1 & 3 & 3 \end{array}$ ① + ② $\begin{array}{llllllll} 0 & 0 & 0 & 3 & 0 & 0 & 1 & 4 \end{array}$ $\begin{matrix} 0 & 0 & 0 & 0 & 0 & 1 & 0 & 1 \end{matrix}$ -->

<img src="https://cdn.noedgeai.com/019659a0-2ea1-7884-92fc-eb39bae80dc5_10.jpg?x=140&y=260&w=1282&h=218&r=0"/>

Fig. 2. Visualizing the fast subset convolution (FSC),outlined in Lst. 3: ① We rank the set functions $f$ and $g$ and ② apply the zeta transform to obtain ${\zeta f}$ and ${\zeta g}$ ,respectively. ③ We perform the ranked convolution between ${\zeta f}$ and ${\zeta g}$ . ④ We apply the Möbius transform to obtain the ranked $h$ . ⑤ Finally,we reconstitute $h = f * g$ ,the actual subset convolution. We highlight in color the steps needed to compute the second rank "slice" of ${\zeta h}$ ,namely $\left( {\zeta h}\right) \left( { : ,2}\right)$ ,during ranked convolution (as in Sec. 4.2). Intuitively,we need to sum up the dot products between the corresponding slices,i.e., $\left( {\zeta f}\right) \left( { : ,0}\right)$ with $\left( {\zeta g}\right) \left( { : ,2}\right) ,\left( {\zeta f}\right) \left( { : ,1}\right)$ with $\left( {\zeta g}\right) \left( { : ,1}\right)$ ,and $\left( {\zeta f}\right) \left( { : ,2}\right)$ with $\left( {\zeta g}\right) \left( { : ,0}\right)$ .

图2. 快速子集卷积(FSC)流程示意图(基于算法清单3)：①对集合函数$f$和$g$分级处理；②分别应用泽塔变换得到${\zeta f}$和${\zeta g}$；③执行${\zeta f}$与${\zeta g}$的分级卷积；④应用莫比乌斯变换获得分级结果$h$；⑤最终合成实际子集卷积$h = f * g$。彩色标注部分展示计算第二秩"切片"$\left( {\zeta h}\right) \left( { : ,2}\right)$时所需步骤(如第4.2节所述)，本质上是将对应切片(即$\left( {\zeta f}\right) \left( { : ,0}\right)$与$\left( {\zeta g}\right) \left( { : ,2}\right) ,\left( {\zeta f}\right) \left( { : ,1}\right)$、$\left( {\zeta g}\right) \left( { : ,1}\right)$与$\left( {\zeta f}\right) \left( { : ,2}\right)$)的点积求和。

<!-- Media -->

### 4.3 Möbius Transform

### 4.3 莫比乌斯变换

To obtain the actual convolution, one applies the Möbius transform rank-wise. The Möbius transform is indeed the inverse of the zeta transform,i.e., ${\zeta \mu } = {\mu \zeta } = \mathrm{{id}}$ ,and is defined for any $S \subseteq  \left\lbrack  n\right\rbrack$ as

为获得实际卷积结果，需按秩施加莫比乌斯变换。该变换实质上是泽塔变换的逆运算(${\zeta \mu } = {\mu \zeta } = \mathrm{{id}}$)，其定义为：对任意$S \subseteq  \left\lbrack  n\right\rbrack$

$$
\left( {\mu f}\right) \left( S\right)  = \mathop{\sum }\limits_{{T \subseteq  S}}{\left( -1\right) }^{\left| T\right| }f\left( T\right) . \tag{12}
$$

A full-fledged example of FSC is shown in Sec. 4.5 and its associated Fig. 2.

完整FSC示例见第4.5节及图2所示。

### 4.4 Implementation

### 4.4 实现方案

4.4.1 Zeta Transform. A naive evaluation of Eq. (10) leads to an $O\left( {3}^{n}\right)$ -time algorithm,as for each subset we are to sum up along all its subsets. However, there is a faster way computing it, commonly referred to as Yates’ algorithm [52]. Define ${\widehat{f}}_{0}\left( S\right)  = f\left( S\right)$ for all $S \subseteq  \left\lbrack  n\right\rbrack$ ,and then iterate for all $j = 1,2,\ldots ,n$ and $S \subseteq  \left\lbrack  n\right\rbrack$ as follows [2]:

4.4.1 Zeta变换。直接计算式(10)会导致$O\left( {3}^{n}\right)$时间复杂度的算法，因为每个子集都需要遍历其所有子集求和。但存在更快的计算方式，通常称为Yates算法[52]。首先对所有$S \subseteq  \left\lbrack  n\right\rbrack$定义${\widehat{f}}_{0}\left( S\right)  = f\left( S\right)$，然后按以下方式迭代所有$j = 1,2,\ldots ,n$和$S \subseteq  \left\lbrack  n\right\rbrack$[2]：

$$
{\widehat{f}}_{j}\left( S\right)  = \left\{  \begin{array}{ll} {\widehat{f}}_{j - 1}\left( S\right) & \text{ if }j \notin  S, \\  {\widehat{f}}_{j - 1}\left( {S\smallsetminus \{ j\} }\right)  + {\widehat{f}}_{j - 1}\left( S\right) & \text{ if }j \in  S. \end{array}\right.  \tag{13}
$$

By induction,one can show that ${\widehat{f}}_{n}\left( S\right)  = \left( {\zeta f}\right) \left( S\right)$ for all $S \subseteq  \left\lbrack  n\right\rbrack$ . The computation of Eq. (13) takes $O\left( {{2}^{n}n}\right)$ operations,as for each subset $S$ we need to iterate over its elements. Lst. 1 shows an implementation of Eq. (13).

通过归纳法可以证明，对所有$S \subseteq  \left\lbrack  n\right\rbrack$都有${\widehat{f}}_{n}\left( S\right)  = \left( {\zeta f}\right) \left( S\right)$。计算式(13)需要$O\left( {{2}^{n}n}\right)$次操作，因为每个子集$S$都需要遍历其元素。清单1展示了式(13)的实现。

---

	zeta(f):

	zeta(f):

		for $\left( {\mathrm{d} = 0;\mathrm{d}! = \mathrm{n}; +  + \mathrm{d}}\right)$ :

		for $\left( {\mathrm{d} = 0;\mathrm{d}! = \mathrm{n}; +  + \mathrm{d}}\right)$ :

			for $\left( {\mathrm{S} = 0;\mathrm{S}! = 2 *  * \mathrm{n}; +  + \mathrm{S}}\right)$ :

			for $\left( {\mathrm{S} = 0;\mathrm{S}! = 2 *  * \mathrm{n}; +  + \mathrm{S}}\right)$ :

				if S & 2**d:

				if S & 2**d:

5 f[S] += f[S ^ 2**d]

5 f[S] += f[S ^ 2**d]

															Listing 1. Zeta transform

															清单1. Zeta变换

---

4.4.2 Möbius Transform. The Möbius transform can be computed in a similar way. Define ${\check{f}}_{0}\left( S\right)  =$ $f\left( S\right)$ for all $S \subseteq  \left\lbrack  n\right\rbrack$ ,and then evaluate the following recursion [2]:

4.4.2 莫比乌斯变换。莫比乌斯变换可采用类似方法计算。首先对所有$S \subseteq  \left\lbrack  n\right\rbrack$定义${\check{f}}_{0}\left( S\right)  =$$f\left( S\right)$，然后计算以下递推式[2]：

$$
{\check{f}}_{j}\left( S\right)  = \left\{  \begin{array}{ll} {\check{f}}_{j - 1}\left( S\right) & \text{ if }j \notin  S, \\   - {\check{f}}_{j - 1}\left( {S\smallsetminus \{ j\} }\right)  + {\check{f}}_{j - 1}\left( S\right) & \text{ if }j \in  S. \end{array}\right.  \tag{14}
$$

Then one can show that ${\check{f}}_{n}\left( S\right)  = \left( {\mu f}\right) \left( S\right)$ for all $S \subseteq  \left\lbrack  n\right\rbrack$ and the computation happens in $O\left( {{2}^{n}n}\right)$ operations as well.

由此可证明对所有$S \subseteq  \left\lbrack  n\right\rbrack$都有${\check{f}}_{n}\left( S\right)  = \left( {\mu f}\right) \left( S\right)$，且计算过程同样具有$O\left( {{2}^{n}n}\right)$的时间复杂度。

<!-- Media -->

---

	$\operatorname{FSC}\left( {f,g}\right)$ :

		for ( r in [0, n] ) :

		for ( r in [0, n] ) :

			// Rank f and g

			// 对f和g进行秩排序

			for (S with $\left| S\right|  = r$ ): ①

			for (S with $\left| S\right|  = r$ ): ①

5 f[S, r] = f[S]

5 f[S, r] = f[S]

6 g[S, r] = g[S]

6 g[S, r] = g[S]

7

			// Zeta transform ②

			// Zeta变换 ②

			zf[S, r] = zeta(f[:, r])

			zf[S, r] = zeta(f[:, r])

			zg[S, r] = zeta(g[:, r])

			zg[S, r] = zeta(g[:, r])

			// Ranked convolution ③

			// 秩卷积 ③

			for (d in [0, r])

			for (d in [0, r])

					zh[:, r] += zf[:, d] * zg[:, r - d]

					zh[:, r] += zf[:, d] * zg[:, r - d]

																												④

			// Moebius transform

			// 莫比乌斯变换

			h[:, r] = mu(zh[:, r])

			h[:, r] = mu(zh[:, r])

																												⑤

		// Reconstitute h

		// 重构h

		for ( S in $\left\lbrack  {0,2 *  * n}\right\rbrack$ ) :

		for ( S in $\left\lbrack  {0,2 *  * n}\right\rbrack$ ) :

			h[S] = h[S, |S|]

			h[S] = h[S, |S|]

					Fig. 3. Fast subset convolution, visualized in Fig. 2.

					图3. 快速子集卷积，如图2所示。

---

<!-- Media -->

A sketch of the entire FSC algorithm is shown in Lst. 3. We use the already-established Python's slicing notation ":" to denote an entire axis of the array, in our case, indexed by bitsets corresponding to the actual sets of relations. We next show a working example.

清单3展示了FSC算法的整体框架。我们采用Python既定的切片符号":"表示数组的整个轴，在本例中该轴由关系集对应的位集索引。接下来将通过实例演示其运作过程。

### 4.5 Example

### 4.5 示例

To facilitate the understanding of how fast subset convolution works, we provide a working example in Fig. 2. In particular, we visualize the steps of Lst. 3. Our example considers two set functions, $f$ and $g$ ,of size 8,i.e.,a subset lattice of size 3 . Note that we have combined steps ① and ② in Fig. 2 into a single step.

为帮助理解快速子集卷积原理，图2给出了具体实例。该示例特别演示了清单3的步骤流程，考察了两个基数为8的集合函数$f$和$g$（即规模为3的子集格）。需注意图2中将步骤①与②合并呈现。

① Rank. In the first step, we create as many rank "slices" as there are set cardinalities; in our case, there are 4 rank slices in total. Initially, they all contain only the values corresponding to the positions of the same cardinality. For instance, the slice corresponding to rank 2 is initially comprised of the values at positions 011, 101, and 110. Accordingly, these positions and values are displayed in the same color.

① 秩划分。第一步创建与集合基数数量相等的秩"切片"（本例共4个）。初始时各切片仅包含相同基数位置的值，例如秩2切片初始由011、101和110位置的值构成，这些位置与数值均以同色标示。

② Applying Zeta. Once we created the rank slices, we can now apply the zeta transform to them. Recall its definition in Eq. (10): For each set $S$ ,we sum all the values of $f$ (and analogously for $g$ ) of at the indices of $S$ ’s subsets. To show this,consider the same rank slice 2 of ${\zeta f}$ : The value at 111- which is $4 -$ is the sum of $3 + 1$ . Similar in the rank slice 1 of ${\zeta g}$ : The value at $\left( {\zeta g}\right) \left( {{111},1}\right)$ is made up of the non-zero values $g\left( {010}\right)$ and $g\left( {100}\right)$ .

② 应用Zeta变换。完成秩切片后，可对其执行zeta变换。回顾公式(10)定义：对每个集合$S$，累加其所有子集索引处$f$的值（$g$同理）。以${\zeta f}$的秩2切片为例，111处的值$4 -$是$3 + 1$之和；类似地，${\zeta g}$秩1切片中，$\left( {\zeta g}\right) \left( {{111},1}\right)$处的值由非零值$g\left( {010}\right)$与$g\left( {100}\right)$组成。

③ Ranked Convolution. Once both ranked ${\zeta f}$ and ${\zeta g}$ have been computed,we can run the (ranked) convolution between them, as described in Eq. 11. We visualize the steps for computing the rank slice 2 of ${\zeta h}$ in Fig. 2,namely: The colored arrows connecting $\left( {\zeta f}\right) \left( { : ,0}\right)$ with $\left( {\zeta g}\right) \left( { : ,2}\right)$ , $\left( {\zeta f}\right) \left( { : ,1}\right)$ with $\left( {\zeta g}\right) \left( { : ,1}\right)$ ,and $\left( {\zeta f}\right) \left( { : ,2}\right)$ with $\left( {\zeta g}\right) \left( { : ,0}\right)$ show that we need to multiply these rank slices to obtain $\left( {\zeta h}\right) \left( { : ,2}\right)$ . As pointed out in Eq. (11),we simply perform a dot product between these and sum up the results. For instance,to obtain $\left( {\zeta h}\right) \left( {{111},2}\right)$ ,we need to perform the following calculation: $1 \cdot  1 + 4 \cdot  3 + 4 \cdot  0 = {13}$ .

③排序卷积。当排序后的${\zeta f}$和${\zeta g}$都计算完成后，我们可以按照公式11进行(排序)卷积运算。图2展示了计算${\zeta h}$的秩切片2的具体步骤：连接$\left( {\zeta f}\right) \left( { : ,0}\right)$与$\left( {\zeta g}\right) \left( { : ,2}\right)$、$\left( {\zeta f}\right) \left( { : ,1}\right)$与$\left( {\zeta g}\right) \left( { : ,1}\right)$、$\left( {\zeta f}\right) \left( { : ,2}\right)$与$\left( {\zeta g}\right) \left( { : ,0}\right)$的彩色箭头表明，我们需要将这些秩切片相乘以获得$\left( {\zeta h}\right) \left( { : ,2}\right)$。如公式(11)所述，我们只需对这些切片执行点积并求和。例如，要得到$\left( {\zeta h}\right) \left( {{111},2}\right)$，需进行如下计算：$1 \cdot  1 + 4 \cdot  3 + 4 \cdot  0 = {13}$。

④ Applying Möbius. To obtain the actual "ranked" $h$ ,we have to apply the Möbius transform onto ranked ${\zeta h}$ . As explained in Sec. 4.3,the Möbius transform is the inverse of the zeta transform. Once this is done, the next paragraph explains how to obtain the final subset convolution result, $h$ . The Möbius transform is applied as in Eq. (12),namely we consider all the subsets of a set $S$ and subtract the values where the subset cardinality is odd, and add those for even cardinality. For instance, $h\left( {{111},2}\right)$ is computed as follows: The values ${\zeta h}\left( {{100},2}\right) ,{\zeta h}\left( {{111},2}\right)$ are at odd cardinalities, so we subtract their values,while ${\zeta h}\left( {{100},2}\right) ,{\zeta h}\left( {{101},2}\right)$ ,and ${\zeta h}\left( {{110},2}\right)$ are at even cardinalities,so we add them. In total,these results in $- 4 - {13} + 2 + 5 + 6 = 0$ ,which is exactly $h\left( {{111},2}\right)$ .

④应用莫比乌斯变换。为获得实际的"排序"$h$，需对排序后的${\zeta h}$施加莫比乌斯变换。如第4.3节所述，莫比乌斯变换是zeta变换的逆运算。完成此步骤后，下一段将阐述如何获取最终的子集卷积结果$h$。莫比乌斯变换按公式(12)实施：考虑集合$S$的所有子集，对基数奇数项做减法运算，偶数项做加法运算。例如，$h\left( {{111},2}\right)$的计算过程为：${\zeta h}\left( {{100},2}\right) ,{\zeta h}\left( {{111},2}\right)$属于奇数基数故相减，而${\zeta h}\left( {{100},2}\right) ,{\zeta h}\left( {{101},2}\right)$和${\zeta h}\left( {{110},2}\right)$属于偶数基数故相加，最终得到$- 4 - {13} + 2 + 5 + 6 = 0$，即精确等于$h\left( {{111},2}\right)$。

⑤ Gather. Finally,once the ranked $h$ has been fully computed by applying the Möbius transform to ${\zeta h}$ ,we can obtain the final $h$ by taking a reverse process to step ① : Instead of scattering the set functions to rank slices, we now gather the rank slices into one set function. This is done by simply taking the positions from the corresponding rank slice and putting these into $h$ ; this is also highlighted by the corresponding colors. For instance, to collect the positions 011, 101, and 110, we take them from the rank slice 2 , since all these subsets have cardinality 2 .

⑤聚合。最终，当通过对${\zeta h}$施加莫比乌斯变换完整计算出排序后的$h$后，可通过逆向执行步骤①来获得最终$h$：不再将集合函数分散到秩切片，而是将秩切片聚合成单一集合函数。具体操作是从对应秩切片提取位置信息填入$h$，该过程通过颜色对应关系予以强调。例如，要收集011、101和110三个位置，需从秩切片2获取，因为这些子集的基数均为2。

### 4.6 Running Time

### 4.6 运行时间

Let us calculate the total running time of FSC: The $n$ zeta and Möbius transforms take in total $O\left( {{2}^{n}{n}^{2}}\right)$ -time,while the rank convolution itself takes $O\left( {{2}^{n}{n}^{2}}\right)$ -time.

计算FSC总运行时间：$n$次zeta变换和莫比乌斯变换共耗时$O\left( {{2}^{n}{n}^{2}}\right)$，而秩卷积本身耗时$O\left( {{2}^{n}{n}^{2}}\right)$。

When used as is in dynamic programming recursions, the running time of FSC is multiplied by a factor $O\left( n\right)$ . To this end,we show in the next section how to improve the running time from $O\left( {{2}^{n}{n}^{3}}\right)$ to $O\left( {{2}^{n}{n}^{2}}\right)$ . To motivate this,note that a slowdown of ${20}\mathrm{x}$ for $n = {20}$ in the context of join ordering can make the difference between a practical and an impractical algorithm.

当直接应用于动态规划递归时，FSC的运行时间会乘以系数$O\left( n\right)$。为此，我们将在下节展示如何将运行时间从$O\left( {{2}^{n}{n}^{3}}\right)$优化至$O\left( {{2}^{n}{n}^{2}}\right)$。值得注意的是，在连接排序场景中，$n = {20}$操作出现${20}\mathrm{x}$倍减速可能导致算法从实用变为不实用。

## 5 LAYERED DYNAMIC PROGRAMMING

## 5 分层动态规划

Subset convolution is usually employed in definitions of dynamic programs (as our own) where it is called to optimize the $k$ th layer of the DP-table (in our case,line 6 in Alg. 1). As previously argued, this call contains a lot of redundancy.

子集卷积通常用于动态规划的定义（如本方案），其作用是优化DP表的$k$层（本例中即算法1第6行）。如前所述，该调用存在大量冗余计算。

A first observation, $\left( \star \right)$ ,is that,even though we call FSC on the entire DP-table (Alg. 1,lines 6-7), we will only update the table for subsets $S$ of size exactly $k$ . Taking a look at the internals of FSC explained in Sec. 4,we observe that the ranked convolution,Eq. (11),actually computes $\widehat{h}\left( { : ,r}\right)$ for each $r$ in each call. This is detrimental,as we will use only the $k$ th layer $\widehat{h}\left( { : ,k}\right)$ in the $k$ th call. A second observation, $\left( {\star  \star  }\right)$ ,is that the DP-table itself does not change for subsets of size less than $k$ .

首要发现$\left( \star \right)$是：虽然我们对整个DP表调用FSC（算法1第6-7行），但仅会更新基数恰好为$k$的子集$S$。观察第4节所述FSC内部机制可知，秩卷积（公式11）每次调用实际计算的是每个$r$的$\widehat{h}\left( { : ,r}\right)$。这存在严重浪费，因为第$k$次调用仅需使用第$k$层$\widehat{h}\left( { : ,k}\right)$。次要发现$\left( {\star  \star  }\right)$是：对于基数小于$k$的子集，DP表本身不会变化。

In the following, we explain how one can improve the computation of the transforms and that of the ranked convolution given these two observations to reduce the time-complexity of FSC-based DPs from $O\left( {{2}^{n}{n}^{3}}\right)$ to $O\left( {{2}^{n}{n}^{2}}\right)$ ,hence shaving a $O\left( n\right)$ factor.

下文将阐述如何基于这两个发现改进变换计算和秩卷积，从而将基于FSC的DP时间复杂度从$O\left( {{2}^{n}{n}^{3}}\right)$降至$O\left( {{2}^{n}{n}^{2}}\right)$，实现$O\left( n\right)$倍的优化。

### 5.1 Layer-Wise Zeta Transform

### 5.1 分层Zeta变换

With this setting in mind,we can adapt the zeta transforms ${}^{5}$ intrinsically used in Alg. 1 to run faster. Namely,at layer $k > 1$ ,we do not need to recompute the zeta transforms $\left( {\zeta f}\right) \left( { : ,j}\right)$ ,with $j < k$ ,since due to observation $\left( {\star  \star  }\right)$ ,these do not change once computed and can,hence,be cached and reused during the entire computation. Consequently,at the $k$ th call to FSC,we only need to compute $\left( {\zeta f}\right) \left( { : ,k - 1}\right)$ .

基于此设定，我们可以优化算法1内生的zeta变换${}^{5}$。具体而言，在第$k > 1$层时，由于发现$\left( {\star  \star  }\right)$表明这些变换一旦计算便不再改变，因此无需重新计算$j < k$对应的$\left( {\zeta f}\right) \left( { : ,j}\right)$，可缓存并复用。故第$k$次调用FSC时仅需计算$\left( {\zeta f}\right) \left( { : ,k - 1}\right)$。

---

<!-- Footnote -->

${}^{5}$ The plural is intended.

${}^{5}$ 此处使用复数形式具有特定含义。

<!-- Footnote -->

---

### 5.2 Layer-Wise Ranked Convolution

### 5.2 分层秩卷积

In the same manner,by observation $\left( \star \right)$ ,we may skip the outer for-loop (line 2,Lst. 3) and directly compute $\left( {\zeta h}\right) \left( { : ,k}\right)$ once $\left( {\zeta f}\right) \left( { : ,k - 1}\right)$ has been computed as previously argued. Notably,due to symmetry - recall that we actually call FSC with the DP-table - we may only iterate $d$ until $\left\lfloor  \frac{i}{2}\right\rfloor$ and multiply $f\left( { : ,d}\right) g\left( { : ,i - d}\right)$ by 2 (apart from the case when $d$ is indeed equal to $i - d$ ). Finally,we apply the Möbius transform on $\left( {\zeta h}\right) \left( { : ,k}\right)$ to obtain the $k$ th layer of the DP-table (this is symbolically denoted by ${\mathrm{{DP}}}^{\prime }$ in Alg. 1).

同理，根据发现$\left( \star \right)$，我们可跳过外层循环（清单3第2行），在$\left( {\zeta f}\right) \left( { : ,k - 1}\right)$计算完成后直接求取$\left( {\zeta h}\right) \left( { : ,k}\right)$。值得注意的是，由于对称性（考虑到实际使用DP表调用FSC），仅需迭代$d$至$\left\lfloor  \frac{i}{2}\right\rfloor$并将$f\left( { : ,d}\right) g\left( { : ,i - d}\right)$乘以2（当$d$等于$i - d$时除外）。最后对$\left( {\zeta h}\right) \left( { : ,k}\right)$应用Möbius变换，即可获得DP表的第$k$层（算法1中用${\mathrm{{DP}}}^{\prime }$符号表示）。

Alone these two optimizations shave an $O\left( n\right)$ -overhead from the running time. We,however, present an additional optimization which, albeit does not reduce the asymptotic time complexity, it does indeed save another constant factor.

仅这两项优化就将运行时间缩短了$O\left( n\right)$个开销单位。但我们还提出了另一项优化，虽然不会降低渐近时间复杂度，却能进一步节省一个常数因子。

### 5.3 Avoiding Useless Multiplications

### 5.3 避免无效乘法运算

Recall that our algorithms will work with coefficient forms of polynomials, as described in Sec. 3.2. Hence, the multiplication operator in the ranked convolution (Eq. (11) and Lst. 3, line 13), is rather expensive since this corresponds to the " $\otimes$ " operator. We show how to reduce the number of multiplications. This optimization also holds for the simpler algorithm for ${C}_{\max }$ in Sec. 6.

回顾我们的算法将处理多项式系数形式（如第3.2节所述），因此秩卷积中的乘法运算符（公式(11)和列表3第13行）代价高昂，因为这对应着"$\otimes$"运算符。我们将展示如何减少乘法次数。该优化同样适用于第6节中更简单的${C}_{\max }$算法。

The multiplications take place between ranked zeta transforms. Thus,we have $\left( {\zeta f}\right) \left( {S,r}\right)  = 0$ for $\left| S\right|  < r$ ,for any rank $r$ . This is because when we apply the zeta transform,we first fill the $r$ th layer of the subset lattice with the values of $f\left( S\right)$ with $\left| S\right|  = r$ ,and then,by construction,only supersets will be iterated. Hence,for a rank $r$ ,sets $S$ with $\left| S\right|  < r$ will never be touched in the computation of $\left( {\zeta f}\right) \left( { : ,r}\right)$ .

乘法运算发生在秩zeta变换之间。因此对于任意秩$r$，我们有$\left( {\zeta f}\right) \left( {S,r}\right)  = 0$对应$\left| S\right|  < r$。这是因为应用zeta变换时，我们先用$\left| S\right|  = r$条件下的$f\left( S\right)$值填充子集格的第$r$层，根据构造，后续只会迭代超集。故在计算$\left( {\zeta f}\right) \left( { : ,r}\right)$时，永远不会触及满足$\left| S\right|  < r$条件的集合$S$。

With this observation,we can prune the range of sets $S$ that we need to consider in line 13 (Lst. 3) even further. In the following,let $f = g$ ,as in the context of DPconv. We have:

基于此观察，可进一步缩减列表3第13行中需要考虑的集合$S$范围。下文令$f = g$与DPconv语境一致，可得：

$$
\forall S.\left| S\right|  < d \Rightarrow  \left( {\zeta f}\right) \left( {S,d}\right)  = 0,
$$

$$
\forall S.\left| S\right|  < r - d \Rightarrow  \left( {\zeta f}\right) \left( {S,r - d}\right)  = 0.
$$

Consequently,we can simply skip those sets $S$ with $\left| S\right|  < \max \left( {d,r - d}\right)$ . Another further optimization is to restrict ourselves to sets $S$ with $\left| S\right|  \leq  k$ . This is because since we only require the $k$ th layer, the Möbius transform only needs to consider sets of maximum cardinality $k$ .

因此可直接跳过满足$\left| S\right|  < \max \left( {d,r - d}\right)$的集合$S$。另一优化是限定集合$S$满足$\left| S\right|  \leq  k$条件。由于只需第$k$层，Möbius变换仅需考虑最大基数$k$的集合。

## 6 A SIMPLE ALGORITHM FOR ${C}_{\max }$

## 6 ${C}_{\max }$的简化算法

While our framework can support ${C}_{\max }$ (see Sec. 3.4),we found another a much simpler algorithm that does not require the (rather intricate) implementation of the (min, max) semi-ring.

虽然我们的框架支持${C}_{\max }$（见第3.4节），但我们发现无需实现复杂的(min,max)半环结构就能实现的更简单算法。

Key Idea. The key insight is the following: Since we are applying only "min" and "max" operations, the optimal solution will take its value in the set of join cardinalities. Hence, we can binary search the optimal value OPT. To check whether a given value $\gamma$ qualifies to be an optimal solution,we apply a technique used by Kosaraju for exact (min, max) sequence convolution [22], which we will use on the DP-table itself. The strategy is to first put the DP-entries l.e.q. $\gamma$ on 1 and those greater than $\gamma$ on 0,and then run FSC,in the $\left( {+, \cdot  }\right)$ ring,on this modified DP-table. In particular,this refers to one of the layers of the DP-table. We also use our improved layered dynamic programming described in Sec. 5.

核心思路。关键在于：由于仅进行"min"和"max"运算，最优解必然取自连接基数集合。因此可通过二分查找确定最优值OPT。为验证给定值$\gamma$是否合格，我们采用Kosaraju用于精确(min,max)序列卷积的技术[22]，将其应用于DP表本身。具体策略是：先将DP表中≤$\gamma$的项标记为1，大于项标记为0，然后在$\left( {+, \cdot  }\right)$环中对修改后的DP表执行FSC。这里特指DP表的某一层级，同时采用第5节所述的改进分层动态规划。

Pseudocode. We outline the pseudocode of the algorithm in Alg. 3. It first sorts the join cardinalities in descending order and then performs a binary search on them, searching for the one which separates feasible $\gamma$ ’s from infeasible ones (note that the maximum join cardinality is always feasible, but may not be the optimum). To this end, we employ Iverson's bracket notation: Given a property $P,\left\lbrack  P\right\rbrack$ returns 1 is the property is true,0 otherwise. In our case, $\left\lbrack  {c \leq  \gamma }\right\rbrack$ is the following:

伪代码。算法3概述了该过程：首先将连接基数降序排列，然后进行二分查找以区分可行解$\gamma$与不可行解（注意最大连接基数总是可行但不一定最优）。此处采用Iverson括号标记法：给定属性$P,\left\lbrack  P\right\rbrack$，若属性为真则返回1，否则返回0。本案例中$\left\lbrack  {c \leq  \gamma }\right\rbrack$定义如下：

$$
S \mapsto  \left\{  \begin{array}{ll} 1, & \text{ if }c\left( S\right)  \leq  \gamma , \\  0, & \text{ otherwise } \end{array}\right. 
$$

<!-- Media -->

Algorithm 3: Simpler DPconv[max]: Optimal cost w.r.t. ${C}_{\max }$ in $O\left( {{2}^{n}{n}^{3}}\right)$ -time

算法3：简化版DPconv[max]：在$O\left( {{2}^{n}{n}^{3}}\right)$时间内求解${C}_{\max }$相关最优成本

---

Input: Query graph $Q = \left( {V,E}\right)$

输入：查询图$Q = \left( {V,E}\right)$

Output: Optimal cost value w.r.t. ${C}_{\max }$

输出：${C}_{\max }$相关的最优成本值

${cs} \leftarrow  \operatorname{sort}\left( {\left\lbrack  {c\left( S\right)  \mid  S \subseteq  \left\lbrack  \left| V\right| \right\rbrack  }\right\rbrack  \text{,decreasing=True}}\right)$

$p$ ,step $\leftarrow  0,{2}^{\left| V\right|  - 1}$

$p$ ，步骤 $\leftarrow  0,{2}^{\left| V\right|  - 1}$

while step $> 0$ do

当步骤 $> 0$ 执行时

	$\gamma  \leftarrow  \operatorname{cs}\left\lbrack  {p + \text{ step }}\right\rbrack$

	$\mathrm{{DP}} \leftarrow  \operatorname{LayerEDDP}\left( \left\lbrack  {c \leq  \gamma }\right\rbrack  \right)$ (Sec. 5)

	$\mathrm{{DP}} \leftarrow  \operatorname{LayerEDDP}\left( \left\lbrack  {c \leq  \gamma }\right\rbrack  \right)$ （第5节）

	if $\mathrm{{DP}}\left( V\right)  > 0$ then

	如果 $\mathrm{{DP}}\left( V\right)  > 0$ 成立

		$p \leftarrow  p +$ step

		执行 $p \leftarrow  p +$ 步骤

	end if

	结束条件

	step $\leftarrow$ step $/2$

	步骤 $\leftarrow$ 步骤 $/2$

end while

结束循环

return $\operatorname{cs}\left\lbrack  p\right\rbrack$

返回 $\operatorname{cs}\left\lbrack  p\right\rbrack$

---

<!-- Media -->

Once the DP-table has been computed, the algorithm checks whether this value was feasible, i.e., whether $V$ has a positive value in the DP-table. If that is the case,we search for smaller $\gamma$ ’s. The algorithm concludes by returning the smallest $\gamma$ for which $\operatorname{DP}\left( V\right)$ is still positive. In the same manner as for the standard DPconv, we can build the join tree once we found the optimal value (see Alg. 2). We visualize Alg. 3 in Fig. 4.

当动态规划表(DP-table)计算完成后，算法会校验该值是否可行，即检查DP表中$V$是否为正值。若满足条件，则继续搜索更小的$\gamma$。算法最终返回使$\operatorname{DP}\left( V\right)$仍保持正值的最小$\gamma$。与标准DPconv算法类似，在确定最优值后即可构建连接树（参见算法2）。图4直观展示了算法3的流程。

<!-- Media -->

<!-- figureText: $> \gamma$ $\leq  \gamma$ LayeredDP 日 $\operatorname{DP}\left\lbrack  V\right\rbrack   = 0$ $\gamma  \uparrow$ B S $\operatorname{DP}\left\lbrack  V\right\rbrack   > 0$ 九 $\downarrow  \gamma$ 4 R $Y$ -->

<img src="https://cdn.noedgeai.com/019659a0-2ea1-7884-92fc-eb39bae80dc5_14.jpg?x=525&y=1286&w=516&h=488&r=0"/>

Fig. 4. Visualizing Alg. 3.

图4. 算法3可视化

<!-- Media -->

Running Time. Given our improved implementation of layered dynamic programming (Sec. 5), our new Alg. 3 runs in time $O\left( {{2}^{n}\log {2}^{n} + {2}^{n}{n}^{2}\log {2}^{n}}\right)  = O\left( {{2}^{n}{n}^{3}}\right)$ . The additional factor $\log {2}^{n}$ in the second term comes from the running time of the binary search on the ${2}^{n}$ -sized sorted list of join cardinalities.

时间复杂度。基于分层动态规划的优化实现（第5节），新算法3的时间复杂度为$O\left( {{2}^{n}\log {2}^{n} + {2}^{n}{n}^{2}\log {2}^{n}}\right)  = O\left( {{2}^{n}{n}^{3}}\right)$。第二项中的附加因子$\log {2}^{n}$源于对${2}^{n}$规模连接基数有序列表进行二分查找的时间消耗。

Constant-Factor Optimizations. While this is sufficient to outperform the standard exact algorithm, there is still an optimization that can be done that only reduces the constant factor hidden in the running time. Namely, for the first layers of the DP-table, we directly hardcode the dynamic programming solution for subsets of cardinality l.e.q. 6. This removes the overhead of subset convolution for these small layers. Note that we still need to compute the zeta transforms of these layers, since they will be used in later layers (as in Lst. 3, line 13).

常数因子优化。虽然这已足以超越标准精确算法，但仍可通过仅降低时间复杂度中的隐藏常数因子进一步优化。具体而言，对于DP表的前几层，我们直接硬编码处理基数小于等于6的子集动态规划解，消除了这些小规模层的子集卷积开销。需要注意的是，仍需计算这些层的zeta变换，因为它们将用于后续层级（如清单3第13行所示）。

## 7 APPROXIMATION ALGORITHM

## 7 近似算法

Motivation. A prominent result on approximation algorithms for the join ordering problem is due to Chatterji et al. [5], who show that in the case of linear join trees, the problem of approximating the optimal cost $K$ within a factor of ${2}^{\Theta \left( {{\log }^{1 - \delta }K}\right) }$ is NP-hard,for any $\delta  > 0$ . We approach the problem from the other end:

研究动机。Chatterji等人[5]关于连接顺序近似算法的著名研究表明：对于线性连接树情形，在${2}^{\Theta \left( {{\log }^{1 - \delta }K}\right) }$因子内逼近最优成本$K$的问题是NP难解的（对任意$\delta  > 0$）。我们则从相反角度切入该问题：

How fast can we approximate the optimal ${C}_{\text{out }}$ value within a factor of $\left( {1 + \varepsilon }\right)$ ?

能以多快速度在$\left( {1 + \varepsilon }\right)$因子内逼近最优值${C}_{\text{out }}$？

Indeed, a fast approximation algorithm can enable a faster evaluation of the optimal plan, while incurring a small overhead,specified by the precision parameter $\varepsilon$ .

实际上，快速近似算法能在精度参数$\varepsilon$限定的较小开销下，加速最优计划的评估过程。

Our Approximation Algorithm. To show the benefit of reducing the problem of join ordering to subset convolution,we now show how to obtain an $\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\varepsilon }}\right)$ -time approximation algorithm that optimizes ${C}_{\text{out }}$ within a multiplicative factor of $\left( {1 + \varepsilon }\right)$ . In particular,note that our algorithm is still exponential. However,it shows that the $O\left( {3}^{n}\right)$ -time barrier can be overcome when asking about $\left( {1 + \varepsilon }\right)$ -approximation algorithms. This is the first result of this kind,which we state in Thm. 7.2.

我们的近似算法。为展示将连接顺序问题归约为子集卷积的优势，现提出一种$\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\varepsilon }}\right)$时间复杂度的近似算法，该算法能在$\left( {1 + \varepsilon }\right)$乘积因子内优化${C}_{\text{out }}$。特别指出，虽然该算法仍属指数级，但证明了在$\left( {1 + \varepsilon }\right)$近似算法研究中可突破$O\left( {3}^{n}\right)$时间壁垒。这是此类研究中的首个成果，我们将其表述为定理7.2。

### 7.1 Approximate Min-Sum Subset Convolution

### 7.1 近似最小和子集卷积

Following a recent result by Bringmann et al. [4] and the so far unexplored connection between min-plus sequence convolution and min-sum subset convolution, where the latter is the one we reduced join ordering to,Stoian [46] has shown that min-sum subset convolution can be $\left( {1 + \varepsilon }\right)$ - approximated in $\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\varepsilon }}\right)$ -time; here, $\widetilde{O}$ hides poly-logarithmic factors in the input size and $\varepsilon$ (note that the input size also consists of the join cardinality function, which is represented as a vector of size ${2}^{n}$ ). For completeness,this is their main theorem:

基于Bringmann等人[4]的最新成果，以及最小加序列卷积与最小和子集卷积（后者是我们归约连接顺序问题的核心）之间尚未被探索的关联，Stoian[46]证实最小和子集卷积可在$\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\varepsilon }}\right)$时间内实现$\left( {1 + \varepsilon }\right)$近似——其中$\widetilde{O}$包含输入规模与$\varepsilon$的多对数因子（需注意输入规模还包括用${2}^{n}$维向量表示的连接基数函数）。为完整起见，以下是其核心定理：

THEOREM 7.1 ([46,THM. 3]). $\left( {1 + \varepsilon }\right)$ -Approximate min-sum subset convolution can be solved in $\widetilde{O}\left( {{2}^{\frac{3n}{2}}/\sqrt{\varepsilon }}\right)$ -time.

定理7.1（[46,定理3]）$\left( {1 + \varepsilon }\right)$-近似最小和子集卷积可在$\widetilde{O}\left( {{2}^{\frac{3n}{2}}/\sqrt{\varepsilon }}\right)$时间内求解。

This result implied approximation algorithms for several problems that reduce to subset convolution, e.g., the prize-collecting Steiner tree problem [39]. Thus, by our reduction of the join ordering problem to min-sum subset convolution in Sec. 3,we can obtain an $\left( {1 + \varepsilon }\right)$ -approximation algorithm for the join ordering problem as well.

该结果暗示了若干可归约为子集卷积问题的近似算法，例如奖励收集斯坦纳树问题[39]。因此，通过我们在第3节中将连接排序问题归约为最小和子集卷积的方法，同样可以获得连接排序问题的$\left( {1 + \varepsilon }\right)$-近似算法。

### 7.2 Approximate Join Ordering

### 7.2 近似连接排序

The approximation algorithm follows their simple scheme:

该近似算法遵循其简单方案：

THEOREM 7.2. If $\left( {1 + \varepsilon }\right)$ -approximate min-sum subset convolution runs in $T\left( {n,\varepsilon }\right)$ -time,then $\left( {1 + \varepsilon }\right)$ - approximate join ordering can be solved in $O\left( {T\left( {n,\frac{\varepsilon }{n - 1}}\right) }\right)$ -time.

定理7.2. 若$\left( {1 + \varepsilon }\right)$-近似最小和子集卷积能在$T\left( {n,\varepsilon }\right)$时间内运行，则$\left( {1 + \varepsilon }\right)$-近似连接排序问题可在$O\left( {T\left( {n,\frac{\varepsilon }{n - 1}}\right) }\right)$时间内求解。

Proof. Consider the evaluation of the min-sum subset convolution between the DP-table and itself at each of the $n - 1$ optimization layers; see Alg. 1,line 6. Fixing ${\varepsilon }^{\prime } > 0$ for each convolution call,we obtain a cumulative relative error bounded by ${\left( 1 + {\varepsilon }^{\prime }\right) }^{n - 1}$ . By setting ${\varepsilon }^{\prime } = \Theta \left( \frac{\varepsilon }{n - 1}\right)$ ,we obtain a relative error of at most $\varepsilon$ .

证明. 考虑在每个$n - 1$优化层对DP表自身进行最小和子集卷积求值（参见算法1第6行）。固定每次卷积调用的${\varepsilon }^{\prime } > 0$参数，我们得到累计相对误差不超过${\left( 1 + {\varepsilon }^{\prime }\right) }^{n - 1}$。设${\varepsilon }^{\prime } = \Theta \left( \frac{\varepsilon }{n - 1}\right)$时，最大相对误差不超过$\varepsilon$。

COROLLARY 7.3. $\left( {1 + \varepsilon }\right)$ -Approximate join ordering can be solved in $\widetilde{O}\left( {{2}^{\frac{3n}{2}}/\sqrt{\varepsilon }}\right)$ -time.

推论7.3. $\left( {1 + \varepsilon }\right)$-近似连接排序问题可在$\widetilde{O}\left( {{2}^{\frac{3n}{2}}/\sqrt{\varepsilon }}\right)$时间内求解。

<!-- Media -->

<!-- figureText: $\times  {10}^{20}$ $O\left( {3}^{n}\right)$ $\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\epsilon }}\right)$ ${10}^{-2}$ ${10}^{-1}$ ${10}^{0}$ $\varepsilon$ #Operations 1.0 0.5 0.0 ${10}^{-4}$ ${10}^{-3}$ -->

<img src="https://cdn.noedgeai.com/019659a0-2ea1-7884-92fc-eb39bae80dc5_16.jpg?x=537&y=276&w=496&h=328&r=0"/>

Fig. 5. Theoretical number of operations of the exact $O\left( {3}^{n}\right)$ -time algorithm and the $\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\varepsilon }}\right)$ -time $\left( {1 + \varepsilon }\right)$ - approximation algorithm for $n = {40}$ and varying $\varepsilon$ ’s.

图5. 精确$O\left( {3}^{n}\right)$时间复杂度算法与$\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\varepsilon }}\right)$时间$\left( {1 + \varepsilon }\right)$-近似算法在$n = {40}$及不同$\varepsilon$取值下的理论操作次数对比。

<!-- Media -->

In particular,we aim to optimize ${C}_{\text{out }}$ (since our ${C}_{\max }$ algorithm in Sec. 6 already achieves a better running time). This is particularly interesting since the running time of the approximation algorithm does not depend on $W$ ,the largest join cardinality. To get an intuition for the running time of the approximation algorithm, we plot in Fig. 5 the theoretical number of operations of the exact $O\left( {3}^{n}\right)$ -time algorithm and the $\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\varepsilon }}\right)$ -time $\left( {1 + \varepsilon }\right)$ -approximation algorithm for $n = {40}$ and varying $\varepsilon$ ’s. For $\varepsilon  = {10}^{-2}$ ,i.e.,the optimal value is approximated by a multiplicative factor of $\left( {1 + {10}^{-2}}\right)$ ,the runtime of the approximation algorithm outperforms that of the exact algorithm.

我们特别关注优化${C}_{\text{out }}$参数（因第6节的${C}_{\max }$算法已实现更优时间复杂度）。该优化意义重大，因为近似算法的运行时间不依赖于最大连接基数$W$。为直观理解近似算法的时间效率，图5展示了精确$O\left( {3}^{n}\right)$时间算法与$\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\varepsilon }}\right)$时间$\left( {1 + \varepsilon }\right)$-近似算法在$n = {40}$及不同$\varepsilon$取值下的理论操作次数。当$\varepsilon  = {10}^{-2}$时（即最优值被乘性因子$\left( {1 + {10}^{-2}}\right)$近似），近似算法的运行时间优于精确算法。

Note that the intricate details in the approximation framework by Bringmann et al. [4] make it hard to have an immediate practical algorithm out of the above theoretical result. We discuss this in Sec. 11.

需要注意的是，Bringmann等人[4]提出的近似框架中复杂的理论细节，使得我们难以直接从上述理论结果中得出实用的算法方案。我们将在第11节讨论这个问题。

## 8 FUSING ${C}_{\text{out }}$ AND ${C}_{\max }$

## 8 ${C}_{\text{out }}$与${C}_{\max }$的融合

We can indeed regard the faster optimization of ${C}_{\max }$ from another perspective: What if we could optimize the optimal ${C}_{\text{out }}$ -value under the constraint that the intermediate size is not too large? To show the motivation behind this problem,consider the optimization of ${C}_{\text{out }}$ in the case of Q19d in JOB [25]. When using the true cardinalities, the max. intermediate join size of the optimal plan w.r.t. ${C}_{\text{out }}$ is 3,036,719 tuples. In contrast,directly optimizing the largest join size via ${C}_{\text{max }}$ only results in an intermediate size of 1,760,645 tuples; this reduces the largest intermediate size by 1.72x. The same can be observed in the recently introduced CEB benchmark: There is a query, ${}^{6}$ whose optimal ${C}_{\text{out }}$ plan has the same behavior. Namely,the largest intermediate join is consists of 11,637,593tuples,yet if we directly optimized under ${C}_{\max }$ ,we obtain a largest intermediate join of 9,805,312 tuples.

我们确实可以从另一个角度审视${C}_{\max }$的快速优化：如果在中间结果规模不过大的约束条件下，能否优化出最佳的${C}_{\text{out }}$值？以JOB[25]中Q19d案例的${C}_{\text{out }}$优化为例说明该问题的动机。使用真实基数时，最优${C}_{\text{out }}$方案的最大中间连接规模达3,036,719个元组；而通过${C}_{\text{max }}$直接优化最大连接规模时，中间结果仅1,760,645个元组，降幅达1.72倍。新近推出的CEB基准测试中同样存在此现象：查询${}^{6}$的最优${C}_{\text{out }}$方案也呈现相同特征——最大中间连接含11,637,593个元组，但采用${C}_{\max }$优化后，最大中间连接降至9,805,312个元组。

### 8.1 Capping ${C}_{\text{out }}$

### 8.1 限制${C}_{\text{out }}$机制

Having optimized for ${C}_{\max }$ does not represent any impediment in further refining the plan w.r.t. ${C}_{\text{out }}$ . Indeed,we propose a novel cost function to be optimized for,namely the ${C}_{\text{cap }}$ ,motivated by the previous findings. Namely,we propose to jointly optimize ${C}_{\text{out }}$ and ${C}_{\max }$ ,i.e.,minimize the sum of the intermediate join sizes while enforcing that the largest one is equal to the optimal ${C}_{\max }$ value. This ensures that we both have a bounded intermediate size (space-optimality) and the best time-optimal plan under this constraint.

针对${C}_{\max }$的优化并不妨碍后续对${C}_{\text{out }}$的改进。基于前期发现，我们提出创新成本函数${C}_{\text{cap }}$进行联合优化：在确保最大中间连接规模等于最优${C}_{\max }$值的前提下，同步优化${C}_{\text{out }}$与${C}_{\max }$，即最小化中间连接规模总和。这种方法既保证了空间最优性（有界中间规模），又能在约束条件下获得时间最优方案。

The drawback is naturally that this joint optimization now needs two optimizer passes: (i) Find the optimal ${C}_{\max }$ value,and (ii) optimize ${C}_{\text{out }}$ so that all intermediate join sizes are bounded above by that value. To reduce the optimization time of the second pass, we can reduce the search space of the optimization problem, by observing that in DPccp (and DPsub) we can directly prune the intermediate solutions the size of which exceed the optimal ${C}_{\max }$ value.

这种联合优化需要两次优化器处理：(i)确定最优${C}_{\max }$值；(ii)在确保所有中间连接规模不超过该值的前提下优化${C}_{\text{out }}$。为缩短第二阶段耗时，可通过DPccp（及DPsub）直接剪除超过最优${C}_{\max }$值的中间解，从而缩减搜索空间。

---

<!-- Footnote -->

${}^{6}$ Specifically,11a/5ec72a84a33f3b3b1f4e53b734731ab0bbecebba.sql

${}^{6}$ 具体而言，11a/5ec72a84a33f3b3b1f4e53b734731ab0bbecebba.sql

<!-- Footnote -->

---

<!-- Media -->

<!-- figureText: Optimization time [ms JOB CEB 0.03 0.02 0.00 12 14 Number of relations(n) 30 DPccp: ${C}_{\text{out }}$ 20 DPccp: ${C}_{\text{cap }}$ 10 12 14 17 Number of relations(n) -->

<img src="https://cdn.noedgeai.com/019659a0-2ea1-7884-92fc-eb39bae80dc5_17.jpg?x=389&y=273&w=759&h=304&r=0"/>

Fig. 6. Overhead in optimization time for ${C}_{\text{cap }}$ on JOB [25] and CEB [32],i.e.,optimizing ${C}_{\text{out }}$ under the constraint that the largest intermediate size is the same as when optimizing with ${C}_{\max }$ (two optimization phases).

图6. JOB[25]和CEB[32]上${C}_{\text{cap }}$的优化时间开销，即在最大中间规模与${C}_{\max }$优化结果相同的约束下进行${C}_{\text{out }}$优化（两阶段优化）。

<!-- Media -->

We visualize this preliminary overhead in Fig. 6. Note that reducing this overhead is the motivation behind our novel framework, which achieves strongly-polynomial speed-up over standard join ordering algorithm, DPccp [30]. In Fig. 6, we show the price we have to pay for this joint optimization. We optimize the queries of the JOB [25] and CEB [32] benchmarks, respectively, via DPccp as follows: For ${C}_{\text{out }}$ ,this is the classic scenario. For ${C}_{\text{cap }}$ ,we first optimize ${C}_{\max }$ via DPccp and then run DPccp again,optimizing ${C}_{\text{out }}$ under the constraint that any intermediate join size is l.e.q. the previously computed ${C}_{\max }$ value. In the case of JOB,for the largest join queries of 17 relations,the overhead is of ${10}\mathrm{\;{ms}}$ . This is still negligible,but as we will show in Sec. 9.2,for larger join clique queries the overhead tends to be over 22%.

图6展示了这种预处理开销。值得注意的是，消除该开销正是我们新型框架的研发动机——该框架较标准连接排序算法DPccp[30]实现了强多项式级加速。图中显示了联合优化的代价：对于JOB[25]和CEB[32]的查询，分别通过DPccp进行优化。传统${C}_{\text{out }}$场景下，17个关系的最大连接查询开销为${10}\mathrm{\;{ms}}$，虽仍可忽略；但如第9.2节所示，更大规模团查询的开销通常超过22%。

### 8.2 Reducing Optimization Time

### 8.2 优化时间压缩

The optimization of ${C}_{\text{cap }}$ has in itself,first,the optimization of ${C}_{\max }$ ,and then a pruned ${C}_{\text{out }}$ optimization. If using the standard exact join ordering algorithm, DPccp, the running time of the ${C}_{\max }$ optimization is still $O\left( {3}^{n}\right)$ . As discussed in Sec. 6,we can reduce this running time to $O\left( {{2}^{n}{n}^{3}}\right)$ . Therefore, we can simply use DPconv[max] and reduce the optimization time. The second pass, that of optimizing ${C}_{\text{out }}$ under the constraint that the largest intermediate size does not exceed this value, remains as before. The advantage is that, since both the first pass is sped up via DPconv[max] and the second pass has a pruned search space, we show that we are even faster than a "vanilla" ${C}_{\text{out }}$ optimization. We show the corresponding experiments in Sec. 9.2.

对${C}_{\text{cap }}$的优化本质上包含两个阶段：首先是${C}_{\max }$的优化，其次是剪枝后的${C}_{\text{out }}$优化。若采用标准精确连接顺序算法DPccp，${C}_{\max }$优化的运行时间仍为$O\left( {3}^{n}\right)$。如第6节所述，我们可将运行时间缩短至$O\left( {{2}^{n}{n}^{3}}\right)$。因此可直接采用DPconv[max]来减少优化时间。第二阶段在最大中间结果规模不超过该值的约束下对${C}_{\text{out }}$进行优化，其流程保持不变。优势在于：由于第一阶段通过DPconv[max]加速，且第二阶段的搜索空间经过剪枝，实验证明本方案比传统${C}_{\text{out }}$优化更为高效。相关实验数据见第9.2节。

## 9 EVALUATION

## 9 性能评估

We show by means of experiments that DPconv achieves a significant speedup over the standard $O\left( {3}^{n}\right)$ -time join ordering algorithm.

实验数据表明，DPconv相较标准$O\left( {3}^{n}\right)$时间复杂度的连接顺序算法实现了显著加速。

Experimental Setup. We perform our experiments on a c5.xlarge EC2 instance which has an Intel Xeon Platinum 8275CL processor with $4\mathrm{{vCPUs}}$ and $8\mathrm{{GB}}$ of memory. All join ordering algorithms are implemented in $\mathrm{C} +  +$ .

实验环境。我们在配备Intel至强铂金8275CL处理器（$4\mathrm{{vCPUs}}$核心/$8\mathrm{{GB}}$内存）的c5.xlarge型EC2实例上开展实验。所有连接顺序算法均通过$\mathrm{C} +  +$实现。

Benchmark Sets. We use the setup from the CEB benchmark [32], which already provides the true cardinalities for IMDb for their 13,644 queries and the 113 queries of JOB [25] (note that this setup has already been used for Fig. 6). For clique queries,we generate random join cardinalities $\leq$ ${100}\mathrm{M}$ ,with the constraint that $c\left( S\right)  \leq  c\left( {S}_{1}\right) c\left( {S}_{2}\right) ,\forall {S}_{1},{S}_{2}, \varsubsetneq  S,{S}_{1} \cap  {S}_{2} = \varnothing ,{S}_{1} \cup  {S}_{2} = S$ ,i.e.,we do not exceed the cardinality of the cross-product of any possible combination of subset pairs. Note that since we directly optimize on clique queries, the running times can also be considered as that of optimizing for cross-products, as discussed in Sec. 3.1. Moreover, since subset convolution does not (yet) exploit sparse set functions-in our case, corresponding to unconnected query subgraphs-the running time is thus independent of the cyclicity of the query graph; we provide a discussion of this in Sec. 11.

基准测试集。采用CEB基准测试[32]的设置，该数据集已提供IMDb 13,644条查询及JOB[25] 113条查询的真实基数（该设置已用于图6）。针对团查询，我们生成随机连接基数$\leq$${100}\mathrm{M}$，并约束$c\left( S\right)  \leq  c\left( {S}_{1}\right) c\left( {S}_{2}\right) ,\forall {S}_{1},{S}_{2}, \varsubsetneq  S,{S}_{1} \cap  {S}_{2} = \varnothing ,{S}_{1} \cup  {S}_{2} = S$，即不超出任何子集对组合的笛卡尔积基数。由于直接对团查询进行优化，其运行时间也可视为对笛卡尔积的优化耗时（如第3.1节所述）。此外，由于子集卷积尚未利用稀疏集函数（对应未连接的查询子图），故运行时间与查询图的环状结构无关，第11节将对此展开讨论。

<!-- Media -->

<!-- figureText: 800 DPsub [max]: $O\left( {3}^{n}\right)$ Optimizing clique queries with ${C}_{\max }$ 15 20 25 Number of relations(n) Optimization time [s] DPconv $\left\lbrack  \max \right\rbrack   : O\left( {{2}^{n}{n}^{3}}\right)$ 600 400 200 0 5 10 -->

<img src="https://cdn.noedgeai.com/019659a0-2ea1-7884-92fc-eb39bae80dc5_18.jpg?x=392&y=277&w=778&h=459&r=0"/>

Fig. 7. Clique queries optimization: Both DPsub[max] and DPconv[max] optimize for ${C}_{\max }$ .

图7. 团查询优化：DPsub[max]与DPconv[max]均针对${C}_{\max }$进行优化

<!-- Media -->

Whenever we compare to the ${\mathrm{A}}^{ * }$ -based algorithm by Haffner and Dittrich [18],we use their benchmark set. We use the same evaluation scripts, ${}^{7}$ i.e.,we use their generated cliques and cardinalities. We show the optimization times for cliques of up to 18 relations in Fig. ${8.}^{8}$

与Haffner和Dittrich[18]提出的${\mathrm{A}}^{ * }$基算法对比时，我们采用其基准测试集。使用相同评估脚本${}^{7}$，即沿用其生成的团结构和基数。图${8.}^{8}$展示了最多18个关系的团查询优化耗时。

Competitors. The standard exact algorithms, DPccp and DPsub, follow the implementation in the reproducibility experiment of Neumann and Radke [34]. ${}^{9}$ We also implement the bitsets as 64-bit integers, which we wrap with helper functions to provide iterators of subsets. DPconv uses all the optimizations described in Sec. 5 for layered dynamic programs. The optimization time includes the time for extracting the join tree from the layered dynamic programming.

对比方案。标准精确算法DPccp和DPsub参照Neumann与Radke[34]可复现实验中的实现。${}^{9}$我们同样采用64位整数实现位集，并通过辅助函数封装以提供子集迭代器。DPconv应用了第5节所述的分层动态规划全部优化技术，优化时间包含从分层动态规划中提取连接树的时间。

### 9.1 Super-Polynomial Speedup

### 9.1 超多项式加速

Within our framework,DPconv,we have shown that join ordering can be done faster than $O\left( {3}^{n}\right)$ . Specifically,we provided an $O\left( {{2}^{n}{n}^{2}{Wn}\log {Wn}}\right)$ -time algorithm for optimizing ${C}_{\text{out }}$ ,which is $\widetilde{O}\left( {2}^{n}\right)$ when the largest join cardinality $W$ is polynomial in $n$ ,and an $O\left( {{2}^{n}{n}^{3}}\right)$ -time algorithm for optimizing ${C}_{\max }$ ; this is the first super-polynomial speedup for the join ordering problem. While the algorithm for ${C}_{\text{out }}$ is not a practical one,we devised in Sec. 6 a simple and practical algorithm for ${C}_{\max }$ .

在我们的框架DPconv中，我们证明了连接排序可以比$O\left( {3}^{n}\right)$更快完成。具体而言，我们提出了一个$O\left( {{2}^{n}{n}^{2}{Wn}\log {Wn}}\right)$时间复杂度的算法来优化${C}_{\text{out }}$，当最大连接基数$W$在$n$中呈多项式时，该算法复杂度为$\widetilde{O}\left( {2}^{n}\right)$；同时提出了$O\left( {{2}^{n}{n}^{3}}\right)$时间复杂度的算法优化${C}_{\max }$——这是连接排序问题首次实现超多项式级加速。虽然${C}_{\text{out }}$的算法不具备实用性，但我们在第6节为${C}_{\max }$设计了一个简单实用的算法。

DPconv vs. DPsub. We benchmark on clique queries, as these are the hardest queries to optimize for [33]. In particular, DPsub excels at this type of queries since DPccp has the overhead of exploring the graph itself (note that this is also the case in the experiments of the original paper [30]). We show the optimization times for cliques of up to 24 relations in Fig. 7. The optimization time is averaged for each $n \in  \{ 3,\ldots ,{24}\}$ across 5 randomly generated instances.

DPconv与DPsub对比。我们在团查询上进行基准测试，因为这类查询是优化难度最高的[33]。特别值得注意的是，DPsub在此类查询中表现优异，因为DPccp需要承担探索图结构本身的开销（原论文[30]的实验也证实了这点）。图7展示了最多24个关系的团查询优化时间，每个$n \in  \{ 3,\ldots ,{24}\}$的优化时间是5次随机生成实例的平均值。

The first observation is that our new algorithm is indeed practical: It starts being faster than DPsub after 17 relations,and for a large join query of 24 relations,it has a speedup of ${29}\mathrm{x}$ . Note that $n = {17}$ is still in the regime of the JOB benchmark. However,JOB has sparse query graphs, hence DPccp is enough for such queries, as already shown in Fig. 6.

首要发现是我们的新算法确实实用：在处理17个以上关系时开始超越DPsub，对于24个关系的大型连接查询，加速比达到${29}\mathrm{x}$。需说明$n = {17}$仍处于JOB基准测试范围内，但JOB的查询图结构稀疏，如图6所示，DPccp已足以处理此类查询。

---

<!-- Footnote -->

${}^{7}$ Their reproducibility experiment is available at: https://gitlab.cs.uni-saarland.de/bigdata/mutable/evaluation

${}^{7}$ 其可复现性实验详见：https://gitlab.cs.uni-saarland.de/bigdata/mutable/evaluation

${}^{8}$ In the current version,the evaluation script starts to timeout after 19 relations; we increased the default timeout to 800 s, yet this did not solve the issue.

${}^{8}$ 当前版本中，评估脚本在19个关系后开始超时；我们将默认超时延长至800秒，但问题仍未解决。

${}^{9}$ https://db.in.tum.de/~radke/papers/hugejoins-reproducibility.pdf

${}^{9}$ https://db.in.tum.de/~radke/papers/hugejoins-reproducibility.pdf

<!-- Footnote -->

---

<!-- Media -->

<!-- figureText: Optimization time [s] 15 ${A}_{ \uparrow  }^{ * } + {h}_{\text{zero }}$ : Optimizes ${C}_{\text{out }}$ Optimizing clique queries: DPconv vs. A* [18 DPconv $\left\lbrack  \max \right\rbrack   +$ DPsub $\left\lbrack  \text{out}\right\rbrack$ : Optimizes $($ 10 12 14 16 18 Number of relations(n) DPconv $\left\lbrack  \max \right\rbrack$ : Optimizes ${C}_{\max }$ 10 5 0 4 6 8 -->

<img src="https://cdn.noedgeai.com/019659a0-2ea1-7884-92fc-eb39bae80dc5_19.jpg?x=393&y=280&w=781&h=462&r=0"/>

Fig. 8. Clique queries optimization (setup as in Ref. [18]): The A*-based optimizer by Haffner and Dittrich [18] optimizes for ${C}_{\text{out }}$ ,while DPconv $\left\lbrack  \max \right\rbrack$ optimizes ${C}_{\max }$ in $O\left( {{2}^{n}{n}^{3}}\right)$ -time,and the joint combination between DPconv[max] and the pruned DPsub[out] optimizes for ${C}_{\text{cap }}$ (Sec. 8).

图8. 团查询优化（参照文献[18]实验设置）：Haffner与Dittrich提出的基于A*的优化器[18]针对${C}_{\text{out }}$进行优化，而DPconv$\left\lbrack  \max \right\rbrack$以$O\left( {{2}^{n}{n}^{3}}\right)$时间复杂度优化${C}_{\max }$，DPconv[max]与剪枝版DPsub[out]的联合组合则优化${C}_{\text{cap }}$（第8节）。

<!-- figureText: 7.0 Optimizing clique queries with ${C}_{\text{cap }}$ DPsub [max] + DPsub [out] DPconv [max] + DPsub [out] 20 21 22 23 24 Number of relations(n) Slowdown over vanilla ${C}_{\text{out }}$ 6.0 5.0 4.0 3.0 2.0 1.0 0.5 16 18 19 -->

<img src="https://cdn.noedgeai.com/019659a0-2ea1-7884-92fc-eb39bae80dc5_19.jpg?x=385&y=946&w=795&h=462&r=0"/>

Fig. 9. The slowdown of optimizing ${C}_{\text{cap }}$ for large clique queries over a "vanilla" ${C}_{\text{out }}$ optimization. The baseline is DPsub[out]. While a naive optimization is (naturally) slower, using DPconv[max], the instantiation of our novel framework for ${C}_{\max }$ ,in the first optimization pass,and followed by the pruned ${C}_{\text{out }}$ optimization,we obtain an optimization time even faster than that of a "vanilla" ${C}_{\text{out }}$ .

图9. 大型团查询优化${C}_{\text{cap }}$相较于"标准版"${C}_{\text{out }}$优化的减速情况。基线为DPsub[out]。虽然原始优化（自然）较慢，但通过在第一轮优化采用DPconv[max]（我们针对${C}_{\max }$设计的新框架实例），再结合剪枝版${C}_{\text{out }}$优化，最终获得的优化时间甚至快于"标准版"${C}_{\text{out }}$。

<!-- Media -->

DPconv vs. A*. Haffner and Dittrich [18], as part of their mutable database system, have recently shown that,indeed,using an ${A}^{ * }$ -based optimizer,one can reduce the number of ccp to be explored. Note that this indeed leads to an optimal solution. In particular, unlike DPconv[max], the practical instantiation of DPconv for ${C}_{\max }$ ,their algorithm optimizes for ${C}_{\text{out }}$ . Thus,the following experiment solely serves to compare the running times, as the cost functions to be optimized are different.

DPconv与A*对比。Haffner和Dittrich[18]在其可变数据库系统中近期证实，采用${A}^{ * }$为基础的优化器确实能减少需要探索的ccp数量。需注意该方法能得到最优解。特别地，与针对${C}_{\max }$的DPconv实用实例DPconv[max]不同，他们的算法优化目标是${C}_{\text{out }}$。因此后续实验仅用于对比运行时间，因为待优化的代价函数不同。

### 9.2 Optimizing ${C}_{\text{cap }}$

### 9.2 ${C}_{\text{cap }}$优化

We show that the optimization time of ${C}_{\text{cap }}$ can be made practical using our novel DPconv framework. Recall that optimizing ${C}_{\text{cap }}$ requires two optimization passes. We will focus on the first pass,in which we optimize for ${C}_{\max }$ . The reason is that we can use DPconv $\left\lbrack  \max \right\rbrack$ ,the instantiation of our novel framework DPconv for the ${C}_{\max }$ cost function. This reduces the running time of this pass from $O\left( {3}^{n}\right)$ -time to $O\left( {{2}^{n}{n}^{3}}\right)$ -time. This is particularly significant for large join queries.

我们证明，通过采用创新的DPconv框架，可使${C}_{\text{cap }}$的优化时间达到实用水平。需注意，优化${C}_{\text{cap }}$需要两轮优化过程。我们将聚焦首轮优化，该阶段针对${C}_{\max }$进行优化，这是因为可运用DPconv$\left\lbrack  \max \right\rbrack$——这是我们新框架DPconv在${C}_{\max }$成本函数中的具体实现。此举将该轮运行时间从$O\left( {3}^{n}\right)$缩短至$O\left( {{2}^{n}{n}^{3}}\right)$，这对大型连接查询尤为关键。

To this end,in Fig. 9,we show the slowdown of optimizing ${C}_{\text{cap }}$ compared to a "vanilla" ${C}_{\text{out }}$ optimization. We benchmark on clique queries, as previously argued. To not clutter the plot, we only keep DPsub as the baseline for clique queries. Thus,the baseline is the optimization of ${C}_{\text{cap }}$ via DPsub,namely we first optimize ${C}_{\max }$ and then run a pruned ${C}_{\text{out }}$ optimization,i.e.,we then skip the subsets whose intermediate size is larger than this latter value. Our proposed algorithm replaces DPsub[max] with DPconv[max] in the first pass. We first observe that,naturally,the naïve ${C}_{\text{cap }}$ optimization is slower than the "vanilla" ${C}_{\text{out }}$ optimization (slow-down is over 22%). In contrast, having both a super-polynomial speedup for the first optimization due to DPconv and a pruned search space for the second pass,we are even faster than the "vanilla" ${C}_{\text{out }}$ optimization after 21 relations. Compared to the ${\mathrm{A}}^{ * }$ -based algorithm,the optimization of ${C}_{\text{cap }}$ outperforms that of ${C}_{\text{out }}$ after 14 relations as well.

为此，图9展示了优化${C}_{\text{cap }}$相较于"基础版"${C}_{\text{out }}$优化的速度差异。如先前所述，我们采用团查询作为基准测试。为避免图表杂乱，仅保留DPsub作为团查询的基线方案。该基线通过DPsub优化${C}_{\text{cap }}$，即先优化${C}_{\max }$再执行剪枝后的${C}_{\text{out }}$优化（跳过中间结果规模超过后续值的子集）。我们提出的算法在首轮用DPconv[max]替代DPsub[max]。首先可见，原始${C}_{\text{cap }}$优化自然慢于"基础版"${C}_{\text{out }}$优化（减速超22%）。而得益于DPconv带来的首轮超多项式加速及次轮剪枝搜索空间，在21个关系后甚至快于"基础版"${C}_{\text{out }}$优化。相较${\mathrm{A}}^{ * }$基准算法，${C}_{\text{cap }}$优化在14个关系后也超越${C}_{\text{out }}$。

Analyzing ${C}_{\text{cap }}$ on CEB. Out of the 13,644 queries of the CEB [32] benchmark,there are 2,873 queries for which the largest intermediate size in the optimal ${C}_{\text{out }}$ plan is ${6.8}\%$ larger than the optimal ${C}_{\max }$ intermediate size. For these queries, ${C}_{\max }$ looses ${22.8}\%$ in the optimal ${C}_{\text{out }}$ value,while ${C}_{\text{cap }}$ naturally reduces this to only ${9.5}\%$ .

CEB基准测试分析。在13,644个查询中，有2,873个查询的最优${C}_{\text{out }}$方案里最大中间结果规模比最优${C}_{\max }$方案大${6.8}\%$。这些查询中，${C}_{\max }$在最优${C}_{\text{out }}$值上损失${22.8}\%$，而${C}_{\text{cap }}$自然将其降至仅${9.5}\%$。

## 10 RELATED WORK

## 10 相关工作

The literature on join ordering is extensive. This is partly because of the effect that a bad join order can have on the query performance and hence the natural desire to avoid such cases. As a result, there are a few exact algorithms, a small number of polynomial-time algorithms for restrictive cases, several greedy (non-optimal) algorithms, and a handful of optimizers based on general-purpose solvers. Our work falls into the category of exact algorithms. In particular, no previous work has observed the link to subset convolution, neither did it achieve a running time as we propose. We are the first to break the $O\left( {3}^{n}\right)$ time-barrier for the join ordering problem on generic query graphs (and bushy solutions). We divide the related work into exact, approximation, and best-effort algorithms. The latter are either polynomial-time algorithms for special instances or greedy algorithms without any approximation guarantee.

连接顺序优化研究文献浩如烟海，部分源于错误连接顺序对查询性能的重大影响及人们规避此类情况的天然诉求。现有成果包括若干精确算法、少量限制条件下的多项式时间算法、多种贪婪（非最优）算法，以及基于通用求解器的优化器。我们的工作属于精确算法范畴，且首次揭示了与子集卷积的关联性，并突破了既有时间复杂度限制。我们率先在通用查询图（及灌木式方案）的连接顺序问题上打破$O\left( {3}^{n}\right)$时间壁垒。将相关工作分为精确算法、近似算法和尽力而为算法三类，后者包含针对特殊实例的多项式时间算法或无近似保证的贪婪算法。

### 10.1 Exact Algorithms

### 10.1 精确算法

The history of the join ordering problem starts at Selinger,proposing an $O\left( {4}^{n}\right)$ -time algorithm, commonly referred to as DPsize [44]. To some extent, this algorithm does subset convolution in the naive way,i.e.,it iterates all subsets $T$ of a given set $S$ of relations,but does not do that in time ${2}^{\left| S\right| }$ , but rather in time ${2}^{n}$ . Vance and Maier [49] observed this limitation and fixed it within the DPsub algorithm,which takes time $O\left( {3}^{n}\right)$ . Since $O\left( {3}^{n}\right)$ seemed rather rigid,not being adaptive to the graph topology, Ono and Lohman [36] analyzed the minimum number of subplan pairs that have to be iterated in any dynamic program. To this end, Moerkotte and Neumann [30] designed DPccp, which emulates to the graph topology and obtains as time-bound exactly the number of connected complement pairs (#ccp’s). However,the running time $O\left( {3}^{n}\right)$ still persisted. In their recent work, Haffner and Dittrich [18] showed that using the ${\mathrm{A}}^{ * }$ algorithm,one can obtain an algorithm which still outputs the optimal plan without having to explore all #ccp's. This is indeed a promising result, as it shows that the lower-bound of #ccp can in some cases be by-passed. However, in the worst case,the running time is still the unyielding $O\left( {3}^{n}\right)$ . In our work,we obtain for the first time an $\widetilde{O}\left( {{2}^{n}W}\right)$ -time algorithm,completely breaking the $O\left( {3}^{n}\right)$ time-barrier when $W$ is polynomial in $n$ . In the case of ${C}_{\max }$ ,i.e.,minimizing the maximum intermediate join cardinality,we obtain an $O\left( {{2}^{n}{n}^{3}}\right)$ -time algorithm,which is also practical.

连接顺序问题的历史始于Selinger提出的$O\left( {4}^{n}\right)$时间复杂度算法，该算法通常被称为DPsize[44]。在某种程度上，该算法以朴素方式实现子集卷积，即遍历给定关系集$S$的所有子集$T$，但时间复杂度并非${2}^{\left| S\right| }$，而是${2}^{n}$。Vance和Maier[49]发现此局限后，在DPsub算法中予以改进，使其时间复杂度降至$O\left( {3}^{n}\right)$。由于$O\left( {3}^{n}\right)$显得过于僵化，无法适应图拓扑结构，Ono和Lohman[36]分析了动态规划中必须遍历的最小子规划对数。为此，Moerkotte和Neumann[30]设计了DPccp算法，该算法模拟图拓扑结构，并将时间上限定为连通补对数(#ccp's)。然而$O\left( {3}^{n}\right)$的时间复杂度依然存在。Haffner和Dittrich[18]在近期工作中证明，使用${\mathrm{A}}^{ * }$算法可在不遍历所有#ccp的情况下仍获得最优解。这确实是突破性进展，表明#ccp的下界在某些情况下可被绕过。但最坏情况下，时间复杂度仍顽固保持在$O\left( {3}^{n}\right)$。本研究中，我们首次提出$\widetilde{O}\left( {{2}^{n}W}\right)$时间复杂度算法，当$W$是$n$的多项式时，彻底突破$O\left( {3}^{n}\right)$时间壁垒。针对${C}_{\max }$情形（即最小化最大中间连接基数），我们开发了$O\left( {{2}^{n}{n}^{3}}\right)$时间复杂度算法，该算法亦具备实用性。

Bottom-Up vs. Top-Down. It is well known that dynamic programs have two implementations, bottom-up and top-down, each with its advantages and disadvantages. One of the most compelling advantages of top-down enumeration is the possibility of easily integrating cost-bounds so that the search space may be easily pruned [16]. Hence, Chaudhuri et al. [6] explore the possiblity of implementing join ordering as a top-down procedure, only considering linear solutions. Building on this work, DeHaan and Tompa [10] extend the top-down method to bushy join trees, disallowing cross products. Fender and Moerkotte $\left\lbrack  {{14},{15}}\right\rbrack$ improve the running time of these algorithms and get rid of the connectedness check, i.e., only outputting the ccp's.

自底向上 vs. 自顶向下。众所周知，动态规划有两种实现方式：自底向上与自顶向下，各有利弊。自顶向下枚举最显著的优势在于可轻松集成成本边界以剪枝搜索空间[16]。因此，Chaudhuri等人[6]探索了将连接顺序作为自顶向下过程实现的可能性，仅考虑线性解。基于此，DeHaan和Tompa[10]将自顶向下方法扩展至禁止叉积的灌木状连接树。Fender和Moerkotte$\left\lbrack  {{14},{15}}\right\rbrack$提升了这些算法的运行效率，并取消了连通性检查，即仅输出ccp's。

### 10.2 Approximation Algorithms

### 10.2 近似算法

Exact algorithms are rather expensive. To this end, Chatterji et al. [5] analyzed whether there are instances that can be solved by approximation algorithms in polynomial time. Unless $\mathrm{P} = \mathrm{{NP}}$ ,the answer remains negative. Specifically,they showed that,for any $\delta  > 0$ ,the problem of approximating the optimal cost $K$ within a factor of ${2}^{\Theta \left( {{\log }^{1 - \delta }K}\right) }$ is NP-hard. (Note that our $\left( {1 + \varepsilon }\right)$ -approximation algorithm from Sec. 7 runs in exponential time.)

精确算法代价高昂。为此，Chatterji等人[5]分析了是否存在可通过近似算法在多项式时间内求解的实例。除非$\mathrm{P} = \mathrm{{NP}}$，否则答案是否定的。具体而言，他们证明对于任意$\delta  > 0$，在${2}^{\Theta \left( {{\log }^{1 - \delta }K}\right) }$因子内近似最优成本$K$的问题是NP难解的（注意第7节中$\left( {1 + \varepsilon }\right)$近似算法具有指数时间复杂度）。

### 10.3 Best-Effort Algorithms

### 10.3 尽力而为算法

The NP-hardness of a fundamental problem is a bitter truth. Hence, research has focused on finding polynomial-time algorithms for special instances or greedy algorithms for arbitrary query graphs.

基础问题的NP难解性是残酷现实。因此，研究聚焦于寻找特殊实例的多项式时间算法或适用于任意查询图的贪心算法。

Polynomial-Time Algorithms. Exponential-time algorithms fail to optimize larger queries in a reasonable time. To this end, it is interesting to ask which instances admit polynomial-time algorithms. The most notable one is the cubic-time algorithm for chain queries. Another class is that of tree queries, for which the IKKBZ algorithms returns the optimal left-deep join tree [20, 24]. Neumann and Radke [34] observed that one can use IKKBZ as a sub-routine: They linearize the query graph via IKKBZ (since a left-deep solution is inherently a linear ordering of the underlying graph) and then run the cubic-time dynamic program on top to build a near-optimal solution. This strategy yields excellent costs for tree queries.

多项式时间算法。指数时间算法无法在合理时间内优化大型查询。因此，研究哪些实例适用多项式时间算法具有重要意义。最著名的案例是链式查询的三次时间算法。另一类树状查询则采用IKKBZ算法生成最优左深连接树[20,24]。Neumann和Radke[34]提出将IKKBZ作为子程序：先通过IKKBZ线性化查询图（因左深解本质上是基础图的线性排序），再在其上运行三次时间动态规划构建近似最优解。该策略为树状查询提供了卓越的成本效益。

Greedy Algorithms. Research has also focused on greedy algorithms which can at least avoid the bad plans. The most representative is the Greedy Operator Ordering (G00) [13] that chooses the cheapest sub-plan at each step. This runs in $O\left( {n\log n}\right)$ -time,yet it does not come with any optimality guarantee on the output join order. This gap between exponential-time exact algorithms and purely greedy ones has remained unexplored until Kossman and Stocker [23] introduced Iterative Dynamic Programming (IDP) which refines the greedy join orders of large queries. The key insight is to iteratively run exact DP on join subtrees of size $k$ .

贪心算法。研究也聚焦于至少能规避劣质方案的贪心算法，最具代表性的是逐步选择最廉价子计划的Greedy Operator Ordering(G00)[13]。该算法时间复杂度为$O\left( {n\log n}\right)$，但无法保证输出连接顺序的最优性。Kossman和Stocker[23]提出迭代动态规划(IDP)前，指数级精确算法与纯贪心算法间的空白领域始终未被探索。IDP通过迭代执行规模为$k$的连接子树精确动态规划，优化大型查询的贪心连接顺序。

General-Purpose Solvers. Join ordering has also been approached by several general-purpose solvers, such as genetic algorithms [45], mixed-integer linear programming [47], and simulated annealing [45]. Note that these works only approximate the optimal solution (without any approximation guarantee). The problem can also be optimized on quantum hardware via quantum annealing [42, 51]. However, this does not lower the classical time-complexity of exact join ordering. Motivated by the promise of workload-aware query optimization, research also has focused on learned alternatives: Marcus and Papaemmanouil [28] suggest using Reinforcement Learning and introduce an agent that outputs the join order and is penalized based on the corresponding join cost. Motivated by the repetitiveness of the queries in cloud workloads [41], a further promising direction is query super-optimization [27].

通用求解器。连接排序问题也见于遗传算法[45]、混合整数线性规划[47]和模拟退火[45]等通用求解器。需注意这些方法仅能近似最优解（无任何近似保证）。通过量子退火[42,51]可在量子硬件上优化该问题，但并未降低精确连接排序的经典时间复杂度。基于工作负载感知查询优化的前景，Marcus和Papaemmanouil[28]提出使用强化学习训练智能体输出连接顺序，并根据连接成本实施惩罚。针对云工作负载中查询的重复性[41]，查询超级优化[27]成为另一前景广阔的方向。

## 11 DISCUSSION

## 11 讨论

Resource-Aware Query Optimization. The trend nowadays is to execute queries in multi-tenant cloud machines. Recently, Viswanathan et al. [50] made the case for resource-aware query optimization. The ${C}_{\max }$ cost function can serve as a proxy for the maximum memory consumption of a given query. Minimizing ${C}_{\max }$ of concurrently running queries can help reduce memory spikes.

资源感知查询优化。当前趋势是在多租户云机器上执行查询。Viswanathan等[50]近期论证了资源感知查询优化的必要性。${C}_{\max }$成本函数可作为给定查询最大内存占用的代理指标。最小化并发查询的${C}_{\max }$值有助于降低内存峰值。

Co-Optimizing ${C}_{\text{out }}$ and ${C}_{\max }$ . The optimization of ${C}_{\text{out }}$ and ${C}_{\max }$ can go beyond our proposed cost function ${C}_{\text{cap }}$ . With ${C}_{\text{cap }}$ ,we first compute the optimal value of ${C}_{\max }$ and then do a pruned ${C}_{\text{out }}$ optimization. Instead of taking the optimal ${C}_{\max }$ value,capping ${C}_{\text{out }}$ at the 90th percentile of the largest intermediate size allows for more flexibility. So one can effectively trade off between query runtime and memory consumption. This is particularly interesting in cloud scenarios.

${C}_{\text{out }}$与${C}_{\max }$协同优化。二者的优化可超越我们提出的${C}_{\text{cap }}$成本函数。使用${C}_{\text{cap }}$时，我们首先计算${C}_{\max }$最优值，再进行剪枝式${C}_{\text{out }}$优化。将${C}_{\text{out }}$限制在最大中间规模90%分位数（而非直接采用${C}_{\max }$最优值）能获得更大灵活性，从而在查询运行时与内存消耗间实现有效权衡，这在云场景中尤为重要。

The cloud data warehouse Amazon Redshift uses predicted query memory to make scheduling decisions [40]. Instead, one could follow a proactive approach in which a query's runtime and memory consumption is co-optimized with query scheduling. For example, when there is a high (concurrent) memory load on the system, one would want to minimize the peak memory consumption of newly arriving queries, while when there is low memory load, one can afford a higher memory consumption. Likewise, if there are long-running queries with a low memory footprint in the system, one might want to produce a high memory but fast-running query.

亚马逊云数据仓库Redshift采用预测查询内存进行调度决策[40]。另一种主动方案是将查询运行时、内存消耗与调度协同优化。例如系统存在高并发内存负载时，需最小化新查询的峰值内存占用；内存负载较低时则可容忍更高内存消耗。同理，若系统存在低内存占用的长时查询，可考虑生成高内存占用但快速执行的查询。

Practical Implementations. While we break the $O\left( {3}^{n}\right)$ time-barrier in the theoretical sense and indeed also provide a practical implementation for ${C}_{\max }$ running in $O\left( {{2}^{n}{n}^{3}}\right)$ -time,it is interesting to further explore practical implementations for ${C}_{\text{out }}$ ,both for the exact (Sec. 3.3) and the approximation algorithm (Sec. 7). In particular, the details of the framework by Bringmann et al. [4], upon which the approximate min-sum subset convolution algorithm is based on, span several pages.

实际应用。虽然我们在理论层面突破了$O\left( {3}^{n}\right)$时间障碍，并确实提供了${C}_{\max }$在$O\left( {{2}^{n}{n}^{3}}\right)$时间内运行的实施方案，但进一步探索${C}_{\text{out }}$的实际应用仍然很有意义——包括精确算法（第3.3节）和近似算法（第7节）。特别是Bringmann等人[4]提出的框架细节（近似最小和子集卷积算法的基础）横跨了数十页篇幅。

Sparse Subset Convolution. Subset convolution does not (yet) have a sparse counterpart, as is the case for sequence convolution (we refer the reader to Jin and Xu [21] for the latest results on sparse sequence convolution). This would be particularly useful for sparse query graphs of the JOB [25] and CEB [32] benchmarks. These queries do not benefit from the speedup obtained by DPconv due to the fact they only touch at most 17 relations. An algorithmic advance in subset convolution for the sparse setting can be directly transferred to the join ordering problem.

稀疏子集卷积。与序列卷积不同（关于稀疏序列卷积的最新进展请参阅Jin和Xu[21]），子集卷积目前尚未发展出稀疏版本。这对于JOB[25]和CEB[32]基准测试中的稀疏查询图将特别有用。由于这些查询最多仅涉及17个关系，它们无法受益于DPconv的加速效果。稀疏环境下子集卷积的算法突破可直接迁移到连接顺序优化问题。

## 12 CONCLUSION

## 12 结论

Join ordering, or finding the optimal order of the joins in a query, is an indispensable task in a database management system. The problem has its roots in the seminal work of Selinger [44], culminating with the graph-theoretic exact algorithm by Moerkotte and Neumann [30]. Despite recent research [18],the worst-case running time still remains $O\left( {3}^{n}\right)$ .

连接顺序优化（即确定查询中连接操作的最佳顺序）是数据库管理系统的核心任务。该问题可追溯至Selinger[44]的开创性研究，最终由Moerkotte和Neumann[30]提出基于图论的精确算法。尽管近期研究[18]有所进展，但最坏情况时间复杂度仍保持$O\left( {3}^{n}\right)$。

In this work, we provided the first super-polynomial speedup over the standard dynamic programming solution. Our framework optimizes (i) ${C}_{\text{out }}$ in $\widetilde{O}\left( {2}^{n}\right)$ -time,when the largest join cardinality $W$ is polynomial in $n$ ,and (ii) ${C}_{\max }$ in $O\left( {{2}^{n}{n}^{3}}\right)$ -time. DPconv is based on subset convolution,a fundamental tool in parameterized algorithms [9], and uses the fact that join ordering is implicitly a dynamic programming recursion using subset convolution similar to other classic problems in the literature (see Björklund et al. [2]). The reduction to subset convolution also implies an $\left( {1 + \varepsilon }\right)$ -approximation algorithm for optimizing ${C}_{\text{out }}$ in $\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\varepsilon }}\right)$ -time.

本研究首次实现了对标准动态规划解决方案的超多项式加速。我们的框架实现了：(i)当最大连接基数$W$是$n$的多项式时，在$\widetilde{O}\left( {2}^{n}\right)$时间内优化${C}_{\text{out }}$；(ii)在$O\left( {{2}^{n}{n}^{3}}\right)$时间内优化${C}_{\max }$。DPconv基于参数化算法[9]的核心工具——子集卷积，利用连接顺序本质上是使用子集卷积的动态规划递归这一特性（类似文献中的其他经典问题，参见Björklund等人[2]）。该归约方法还衍生出$\widetilde{O}\left( {{2}^{{3n}/2}/\sqrt{\varepsilon }}\right)$时间内优化${C}_{\text{out }}$的$\left( {1 + \varepsilon }\right)$近似算法。

Beyond the theoretical results, we have made DPconv practical for database systems. In particular, our algorithm for optimizing ${C}_{\max }$ outperforms the standard exact algorithm for cliques with 17 relations and more. In addition,we showed that joint optimization of ${C}_{\text{out }}$ and ${C}_{\max }$ results in faster optimization times than a "vanilla" ${C}_{\text{out }}$ after 21 relations,while only increasing ${C}_{\text{out }}$ by 9.5%.

除理论成果外，我们使DPconv具备了数据库系统的实用价值。具体而言，针对17个及以上关系的团查询，我们的${C}_{\max }$优化算法性能超越标准精确算法。此外，实验表明对${C}_{\text{out }}$和${C}_{\max }$的联合优化在超过21个关系时，其优化速度优于"原生"${C}_{\text{out }}$方案，而${C}_{\text{out }}$仅增加9.5%。

We expect future work on sparse subset convolution to further speed up our framework for query graphs with few connected subgraphs.

我们预期未来关于稀疏子集卷积的研究，将进一步提升本框架在连接子图较少的查询图上的处理速度。

REFERENCES

[1] R. Bellman, R.E. Bellman, and Rand Corporation. 1957. Dynamic Programming. Princeton University Press. https: //books.google.ro/books?id=rZW4ugAACAAJ

[2] Andreas Björklund, Thore Husfeldt, Petteri Kaski, and Mikko Koivisto. 2007. Fourier meets möbius: fast subset convolution. In Proceedings of the 39th Annual ACM Symposium on Theory of Computing, San Diego, California, USA, June 11-13, 2007, David S. Johnson and Uriel Feige (Eds.). ACM, 67-74. https://doi.org/10.1145/1250790.1250801

[3] Andreas Björklund, Thore Husfeldt, and Mikko Koivisto. 2009. Set Partitioning via Inclusion-Exclusion. SIAM J. Comput. 39, 2 (2009), 546-563. https://doi.org/10.1137/070683933

[4] Karl Bringmann, Marvin Künnemann, and Karol Wegrzycki. 2019. Approximating APSP without scaling: equivalence of approximate min-plus and exact min-max. In Proceedings of the 51st Annual ACM SIGACT Symposium on Theory of Computing, STOC 2019, Phoenix, AZ, USA, June 23-26, 2019, Moses Charikar and Edith Cohen (Eds.). ACM, 943-954. https://doi.org/10.1145/3313276.3316373

[5] Sourav Chatterji, Sai Surya Kiran Evani, Sumit Ganguly, and Mahesh Datt Yemmanuru. 2002. On the Complexity of Approximate Query Optimization. In Proceedings of the Twenty-first ACM SIGACT-SIGMOD-SIGART Symposium on Principles of Database Systems, June 3-5, Madison, Wisconsin, USA, Lucian Popa, Serge Abiteboul, and Phokion G. Kolaitis (Eds.). ACM, 282-292. https://doi.org/10.1145/543613.543650

[6] Surajit Chaudhuri, Ravi Krishnamurthy, Spyros Potamianos, and Kyuseok Shim. 1995. Optimizing Queries with Materialized Views. In Proceedings of the Eleventh International Conference on Data Engineering, March 6-10, 1995, Taipei, Taiwan, Philip S. Yu and Arbee L. P. Chen (Eds.). IEEE Computer Society, 190-200. https://doi.org/10.1109/ ICDE. 1995.380392

[7] E. F. Codd. 1970. A Relational Model of Data for Large Shared Data Banks. Commun. ACM 13, 6 (1970), 377-387. https://doi.org/10.1145/362384.362685

[8] James W Cooley and John W Tukey. 1965. An algorithm for the machine calculation of complex Fourier series. Mathematics of computation 19, 90 (1965), 297-301.

[9] Marek Cygan, Fedor V. Fomin, Łukasz Kowalik, Daniel Lokshtanov, Dániel Marx, Marcin Pilipczuk, Michal Pilipczuk, and Saket Saurabh. 2015. Algebraic techniques: sieves, convolutions, and polynomials. Springer International Publishing, Cham, 321-355. https://doi.org/10.1007/978-3-319-21275-3_10

[10] David DeHaan and Frank Wm. Tompa. 2007. Optimal top-down join enumeration. In Proceedings of the ACM SIGMOD International Conference on Management of Data, Beijing, China, June 12-14, 2007, Chee Yong Chan, Beng Chin Ooi, and Aoying Zhou (Eds.). ACM, 785-796. https://doi.org/10.1145/1247480.1247567

[11] Stuart E. Dreyfus and Robert A. Wagner. 1971. The steiner problem in graphs. Networks 1, 3 (1971), 195-207. https://doi.org/10.1002/NET.3230010302

[12] Marius Eich, Pit Fender, and Guido Moerkotte. 2018. Efficient generation of query plans containing group-by, join, and groupjoin. VLDB J. 27, 5 (2018), 617-641. https://doi.org/10.1007/S00778-017-0476-3

[13] Leonidas Fegaras. 1998. A New Heuristic for Optimizing Large Queries. In Database and Expert Systems Applications, 9th International Conference, DEXA '98, Vienna, Austria, August 24-28, 1998, Proceedings (Lecture Notes in Computer Science, Vol. 1460), Gerald Quirchmayr, Erich Schweighofer, and Trevor J. M. Bench-Capon (Eds.). Springer, 726-735. https://doi.org/10.1007/BFB0054528

[14] Pit Fender and Guido Moerkotte. 2011. A new, highly efficient, and easy to implement top-down join enumeration algorithm. In Proceedings of the 27th International Conference on Data Engineering, ICDE 2011, April 11-16, 2011, Hannover, Germany, Serge Abiteboul, Klemens Böhm, Christoph Koch, and Kian-Lee Tan (Eds.). IEEE Computer Society, 864-875. https://doi.org/10.1109/ICDE.2011.5767901

[15] Pit Fender and Guido Moerkotte. 2012. Reassessing Top-Down Join Enumeration. IEEE Trans. Knowl. Data Eng. 24, 10 (2012), 1803-1818. https://doi.org/10.1109/TKDE.2011.235

[16] Pit Fender, Guido Moerkotte, Thomas Neumann, and Viktor Leis. 2012. Effective and Robust Pruning for Top-Down Join Enumeration Algorithms. In IEEE 28th International Conference on Data Engineering (ICDE 2012), Washington, DC, USA (Arlington, Virginia), 1-5 April, 2012, Anastasios Kementsietsidis and Marcos Antonio Vaz Salles (Eds.). IEEE Computer Society, 414-425. https://doi.org/10.1109/ICDE.2012.27

[17] Fedor V. Fomin and Dieter Kratsch. 2010. Exact Exponential Algorithms (1st ed.). Springer-Verlag, Berlin, Heidelberg.

[18] Immanuel Haffner and Jens Dittrich. 2023. Efficiently Computing Join Orders with Heuristic Search. Proc. ACM Manag. Data 1, 1 (2023), 73:1-73:26. https://doi.org/10.1145/3588927

[19] Peter E. Hart, Nils J. Nilsson, and Bertram Raphael. 1968. A Formal Basis for the Heuristic Determination of Minimum Cost Paths. IEEE Trans. Syst. Sci. Cybern. 4, 2 (1968), 100-107. https://doi.org/10.1109/TSSC.1968.300136

[20] Toshihide Ibaraki and Tiko Kameda. 1984. On the Optimal Nesting Order for Computing N-Relational Joins. ACM Trans. Database Syst. 9, 3 (1984), 482-502. https://doi.org/10.1145/1270.1498

[21] Ce Jin and Yinzhan Xu. 2024. Shaving Logs via Large Sieve Inequality: Faster Algorithms for Sparse Convolution and More. In Proceedings of the 56th Annual ACM Symposium on Theory of Computing, STOC 2024, Vancouver, BC, Canada,

June 24-28, 2024, Bojan Mohar, Igor Shinkar, and Ryan O'Donnell (Eds.). ACM, 1573-1584. https://doi.org/10.1145/ 3618260.3649605

[22] S.R. Kosaraju. 1989. Efficient tree pattern matching. In 30th Annual Symposium on Foundations of Computer Science. 178-183. https://doi.org/10.1109/SFCS.1989.63475

[23] Donald Kossmann and Konrad Stocker. 2000. Iterative dynamic programming: a new class of query optimization algorithms. ACM Trans. Database Syst. 25, 1 (2000), 43-82. https://doi.org/10.1145/352958.352982

[24] Ravi Krishnamurthy, Haran Boral, and Carlo Zaniolo. 1986. Optimization of Nonrecursive Queries. In VLDB'86 Twelfth International Conference on Very Large Data Bases, August 25-28, 1986, Kyoto, Japan, Proceedings, Wesley W. Chu, Georges Gardarin, Setsuo Ohsuga, and Yahiko Kambayashi (Eds.). Morgan Kaufmann, 128-137. http://www.vldb.org/ conf/1986/P128.PDF

[25] Viktor Leis, Andrey Gubichev, Atanas Mirchev, Peter A. Boncz, Alfons Kemper, and Thomas Neumann. 2015. How Good Are Query Optimizers, Really? Proc. VLDB Endow. 9, 3 (2015), 204-215. https://doi.org/10.14778/2850583.2850594

[26] Guy M. Lohman. 1988. Grammar-like Functional Rules for Representing Query Optimization Alternatives. In Proceedings of the 1988 ACM SIGMOD International Conference on Management of Data, Chicago, Illinois, USA, June 1-3, 1988, Haran Boral and Per-Åke Larson (Eds.). ACM Press, 18-27. https://doi.org/10.1145/50202.50204

[27] Ryan Marcus. 2023. Learned Query Superoptimization. In Joint Proceedings of Workshops at the 49th International Conference on Very Large Data Bases (VLDB 2023), Vancouver, Canada, August 28 - September 1, 2023 (CEUR Workshop Proceedings, Vol. 3462), Rajesh Bordawekar, Cinzia Cappiello, Vasilis Efthymiou, Lisa Ehrlinger, Vijay Gadepally, Sainyam Galhotra, Sandra Geisler, Sven Groppe, Le Gruenwald, Alon Y. Halevy, Hazar Harmouch, Oktie Hassanzadeh, Ihab F. Ilyas, Ernesto Jiménez-Ruiz, Sanjay Krishnan, Tirthankar Lahiri, Guoliang Li, Jiaheng Lu, Wolfgang Mauerer, Umar Farooq Minhas, Felix Naumann, M. Tamer Özsu, El Kindi Rezig, Kavitha Srinivas, Michael Stonebraker, Satya-narayana R. Valluri, Maria-Esther Vidal, Haixun Wang, Jiannan Wang, Yingjun Wu, Xun Xue, Mohamed Zaït, and Kai Zeng (Eds.). CEUR-WS.org. https://ceur-ws.org/Vol-3462/AIDB5.pdf

[28] Ryan Marcus and Olga Papaemmanouil. 2018. Deep Reinforcement Learning for Join Order Enumeration. In Proceedings of the First International Workshop on Exploiting Artificial Intelligence Techniques for Data Management, aiDM@SIGMOD 2018, Houston, TX, USA, June 10, 2018, Rajesh Bordawekar and Oded Shmueli (Eds.). ACM, 3:1-3:4. https://doi.org/10.1145/3211954.3211957

[29] Guido Moerkotte. 2023. Building Query Compilers (Draft / Under Construction). https://pi3.informatik.uni-mannheim.de/%7Emoer/querycompiler.pdf

[30] Guido Moerkotte and Thomas Neumann. 2006. Analysis of Two Existing and One New Dynamic Programming Algorithm for the Generation of Optimal Bushy Join Trees without Cross Products. In Proceedings of the 32nd International Conference on Very Large Data Bases, Seoul, Korea, September 12-15, 2006, Umeshwar Dayal, Kyu-Young Whang, David B. Lomet, Gustavo Alonso, Guy M. Lohman, Martin L. Kersten, Sang Kyun Cha, and Young-Kuk Kim (Eds.). ACM, 930-941. http://dl.acm.org/citation.cfm?id=1164207

[31] Guido Moerkotte and Thomas Neumann. 2008. Dynamic programming strikes back. In Proceedings of the ACM SIGMOD International Conference on Management of Data, SIGMOD 2008, Vancouver, BC, Canada, June 10-12, 2008, Jason Tsong-Li Wang (Ed.). ACM, 539-552. https://doi.org/10.1145/1376616.1376672

[32] Parimarjan Negi, Ryan Marcus, Andreas Kipf, Hongzi Mao, Nesime Tatbul, Tim Kraska, and Mohammad Alizadeh. 2021. Flow-Loss: Learning Cardinality Estimates That Matter. Proc. VLDB Endow. 14, 11 (2021), 2019-2032. https: //doi.org/10.14778/3476249.3476259

[33] Thomas Neumann. 2009. Query simplification: graceful degradation for join-order optimization. In Proceedings of the ACM SIGMOD International Conference on Management of Data, SIGMOD 2009, Providence, Rhode Island, USA, June 29 - July 2, 2009, Ugur Çetintemel, Stanley B. Zdonik, Donald Kossmann, and Nesime Tatbul (Eds.). ACM, 403-414. https://doi.org/10.1145/1559845.1559889

[34] Thomas Neumann and Bernhard Radke. 2018. Adaptive Optimization of Very Large Join Queries. In Proceedings of the 2018 International Conference on Management of Data, SIGMOD Conference 2018, Houston, TX, USA, June 10-15, 2018, Gautam Das, Christopher M. Jermaine, and Philip A. Bernstein (Eds.). ACM, 677-692. https://doi.org/10.1145/3183713.3183733

[35] Hung Q. Ngo, Ely Porat, Christopher Ré, and Atri Rudra. 2012. Worst-case optimal join algorithms: [extended abstract]. In Proceedings of the 31st ACM SIGMOD-SIGACT-SIGART Symposium on Principles of Database Systems, PODS 2012, Scottsdale, AZ, USA, May 20-24, 2012, Michael Benedikt, Markus Krötzsch, and Maurizio Lenzerini (Eds.). ACM, 37-48. https://doi.org/10.1145/2213556.2213565

[36] Kiyoshi Ono and Guy M. Lohman. 1990. Measuring the Complexity of Join Enumeration in Query Optimization. In 16th International Conference on Very Large Data Bases, August 13-16, 1990, Brisbane, Queensland, Australia, Proceedings, Dennis McLeod, Ron Sacks-Davis, and Hans-Jörg Schek (Eds.). Morgan Kaufmann, 314-325. http://www.vldb.org/ conf/1990/P314.PDF

[37] Oriana Ponta, Falk Hüffner, and Rolf Niedermeier. 2008. Speeding up Dynamic Programming for Some NP-Hard Graph Recoloring Problems. In Theory and Applications of Models of Computation, 5th International Conference, TAMC 2008, Xi'an, China, April 25-29, 2008. Proceedings (Lecture Notes in Computer Science, Vol. 4978), Manindra Agrawal, Ding-Zhu Du, Zhenhua Duan, and Angsheng Li (Eds.). Springer, 490-501. https://doi.org/10.1007/978-3-540-79228-4_43

[38] Bernhard Radke and Thomas Neumann. 2019. LinDP++: Generalizing Linearized DP to Crossproducts and Non-Inner Joins. In Datenbanksysteme für Business, Technologie und Web (BTW 2019), 18. Fachtagung des Gl-Fachbereichs "Datenbanken und Informationssysteme" (DBIS), 4.-8. März 2019, Rostock, Germany, Proceedings (LNI, Vol. P-289), Torsten Grust, Felix Naumann, Alexander Böhm, Wolfgang Lehner, Theo Härder, Erhard Rahm, Andreas Heuer, Meike Klettke, and Holger Meyer (Eds.). Gesellschaft für Informatik, Bonn, 57-76. https://doi.org/10.18420/BTW2019-05

[39] Daniel Rehfeldt and Thorsten Koch. 2022. On the Exact Solution of Prize-Collecting Steiner Tree Problems. INFORMS J. Comput. 34, 2 (2022), 872-889. https://doi.org/10.1287/IJOC.2021.1087

[40] Gaurav Saxena, Mohammad Rahman, Naresh Chainani, Chunbin Lin, George Caragea, Fahim Chowdhury, Ryan Marcus, Tim Kraska, Ippokratis Pandis, and Balakrishnan (Murali) Narayanaswamy. 2023. Auto-WLM: Machine Learning Enhanced Workload Management in Amazon Redshift. In Companion of the 2023 International Conference on Management of Data, SIGMOD/PODS 2023, Seattle, WA, USA, June 18-23, 2023, Sudipto Das, Ippokratis Pandis, K. Selçuk Candan, and Sihem Amer-Yahia (Eds.). ACM, 225-237. https://doi.org/10.1145/3555041.3589677

[41] Tobias Schmidt, Andreas Kipf, Dominik Horn, Gaurav Saxena, and Tim Kraska. 2024. Predicate Caching: Query-Driven Secondary Indexing for Cloud Data Warehouses. In Companion of the 2024 International Conference on Management of Data, SIGMOD/PODS 2024, Santiago AA, Chile, June 9-15, 2024, Pablo Barceló, Nayat Sánchez-Pi, Alexandra Meliou, and S. Sudarshan (Eds.). ACM, 347-359. https://doi.org/10.1145/3626246.3653395

[42] Manuel Schönberger, Stefanie Scherzinger, and Wolfgang Mauerer. 2023. Ready to Leap (by Co-Design)? Join Order Optimisation on Quantum Hardware. Proc. ACM Manag. Data 1, 1 (2023), 92:1-92:27. https://doi.org/10.1145/3588946

[43] Jacob Scott, Trey Ideker, Richard M. Karp, and Roded Sharan. 2005. Efficient Algorithms for Detecting Signaling Pathways in Protein Interaction Networks. In Research in Computational Molecular Biology, 9th Annual International Conference, RECOMB 2005, Cambridge, MA, USA, May 14-18, 2005, Proceedings (Lecture Notes in Computer Science, Vol. 3500), Satoru Miyano, Jill P. Mesirov, Simon Kasif, Sorin Istrail, Pavel A. Pevzner, and Michael S. Waterman (Eds.). Springer, 1-13. https://doi.org/10.1007/11415770_1

[44] Patricia G. Selinger, Morton M. Astrahan, Donald D. Chamberlin, Raymond A. Lorie, and Thomas G. Price. 1979. Access Path Selection in a Relational Database Management System. In Proceedings of the 1979 ACM SIGMOD International Conference on Management of Data, Boston, Massachusetts, USA, May 30 - June 1, Philip A. Bernstein (Ed.). ACM, 23-34. https://doi.org/10.1145/582095.582099

[45] Michael Steinbrunn, Guido Moerkotte, and Alfons Kemper. 1997. Heuristic and Randomized Optimization for the Join Ordering Problem. VLDB J. 6, 3 (1997), 191-208. https://doi.org/10.1007/S007780050040

[46] Mihail Stoian. 2024. Sinking an Algorithmic Isthmus: (1 + epsilon)-Approximate Min-Sum Subset Convolution. arXiv:2404.11364 [cs.DS]

[47] Immanuel Trummer and Christoph Koch. 2017. Solving the Join Ordering Problem via Mixed Integer Linear Programming. In Proceedings of the 2017 ACM International Conference on Management of Data, SIGMOD Conference 2017, Chicago, IL, USA, May 14-19, 2017, Semih Salihoglu, Wenchao Zhou, Rada Chirkova, Jun Yang, and Dan Suciu (Eds.). ACM, 1025-1040. https://doi.org/10.1145/3035918.3064039

[48] Bennet Vance. 1998. Join-order Optimization with Cartesian Products. Ph. D. Dissertation. Oregon Graduate Institute of Science and Technology.

[49] Bennet Vance and David Maier. 1996. Rapid Bushy Join-order Optimization with Cartesian Products. In Proceedings of the 1996 ACM SIGMOD International Conference on Management of Data, Montreal, Quebec, Canada, June 4-6, 1996, H. V. Jagadish and Inderpal Singh Mumick (Eds.). ACM Press, 35-46. https://doi.org/10.1145/233269.233317

[50] Lalitha Viswanathan, Alekh Jindal, and Konstantinos Karanasos. 2018. Query and Resource Optimization: Bridging the Gap. In 34th IEEE International Conference on Data Engineering, ICDE 2018, Paris, France, April 16-19, 2018. IEEE Computer Society, 1384-1387. https://doi.org/10.1109/ICDE.2018.00156

[51] Tobias Winker, Umut Çalikyilmaz, Le Gruenwald, and Sven Groppe. 2023. Quantum Machine Learning for Join Order Optimization using Variational Quantum Circuits. In Proceedings of the International Workshop on Big Data in Emergent Distributed Environments, BiDEDE 2023, Seattle, WA, USA, 18 June 2023, Sven Groppe, Le Gruenwald, and Ching-Hsien Hsu (Eds.). ACM, 5:1-5:7. https://doi.org/10.1145/3579142.3594299

[52] Frank Yates. 1937. The Design and Analysis of Factorial Experiments. Imperial Bureau of Soil Science (1937).