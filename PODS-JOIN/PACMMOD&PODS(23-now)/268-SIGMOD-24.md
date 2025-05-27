## Reservoir Sampling over Joins

## 连接操作上的蓄水池采样

BINYANG DAI ${}^{ * }$ , Hong Kong University of Science and Technology, China

戴彬阳 ${}^{ * }$，香港科技大学，中国

XIAO HU*, University of Waterloo, Canada

胡晓*，滑铁卢大学，加拿大

KE YI, Hong Kong University of Science and Technology, China

易珂，香港科技大学，中国

Sampling over joins is a fundamental task in large-scale data analytics. Instead of computing the full join results, which could be massive, a uniform sample of the join results would suffice for many purposes, such as answering analytical queries or training machine learning models. In this paper, we study the problem of how to maintain a random sample over joins while the tuples are streaming in. Without the join, this problem can be solved by some simple and classical reservoir sampling algorithms. However, the join operator makes the problem significantly harder, as the join size can be polynomially larger than the input. We present a new algorithm for this problem that achieves a near-linear complexity. The key technical components are a generalized reservoir sampling algorithm that supports a predicate, and a dynamic index for sampling over joins. We also conduct extensive experiments on both graph and relational data over various join queries, and the experimental results demonstrate significant performance improvement over the state of the art.

在连接操作上进行采样是大规模数据分析中的基础任务。相较于计算可能极其庞大的完整连接结果，获取连接结果的均匀样本足以满足许多需求，例如回答分析查询或训练机器学习模型。本文研究了在元组持续流入时如何维护连接操作上的随机样本。若不涉及连接操作，该问题可通过一些简单经典的蓄水池采样算法解决。然而连接运算符使问题显著复杂化，因为连接结果规模可能呈多项式级大于输入数据。我们提出了一种新算法，能以近线性复杂度解决该问题。关键技术组件包括支持谓词的广义蓄水池采样算法，以及用于连接采样的动态索引。我们在图数据和关系数据上针对多种连接查询进行了大量实验，结果表明该算法较现有技术有显著性能提升。

CCS Concepts: $\cdot$ Theory of computation $\rightarrow$ Sketching and sampling; $\cdot$ Information systems $\rightarrow$ Join algorithms.

CCS概念: $\cdot$计算理论 $\rightarrow$草图与采样; $\cdot$信息系统 $\rightarrow$连接算法

Additional Key Words and Phrases: Data Stream, Acyclic Join, Uniform Sample

附加关键词: 数据流、无环连接、均匀采样

## ACM Reference Format:

## ACM参考文献格式:

Binyang Dai, Xiao Hu, and Ke Yi. 2024. Reservoir Sampling over Joins. Proc. ACM Manag. Data 2, 3 (SIGMOD), Article 118 (June 2024), 26 pages. https://doi.org/10.1145/3654921

戴彬阳、胡晓、易珂. 2024. 连接操作上的蓄水池采样. ACM数据管理会刊 2卷3期(SIGMOD), 118号论文(2024年6月), 26页. https://doi.org/10.1145/3654921

## 1 INTRODUCTION

## 1 引言

In large-scale data analytics, people often need to compute complicated functions on top of the query results over the underlying relational database. However, the join operator presents a major challenge, since the join size can be polynomially larger than the original database. Computing and storing the join results is very costly, especially as the data size keeps increasing. Sampling the join results is thus a common approach used in many complicated analytical tasks while providing provable statistical guarantees. One naive method is to first materialize the join results in a table and then randomly access the table, but this loses the performance benefit of sampling. In as early as 1999, two prominent papers [6, 13] asked the intriguing question, whether a sample can be obtained without computing the full join. As observed in by [13], the main barrier is that the sampling operator cannot be pushed down,i.e.,sample $\left( {R \boxtimes  S}\right)  \neq  \operatorname{sample}\left( R\right)  \boxtimes  \operatorname{sample}\left( S\right)$ . To overcome this barrier, the idea is to design some index structures to guide the sampling process. Notably, an index was proposed for acyclic joins (formally defined in Section 4) that can be built in

在大规模数据分析中，人们常需在关系数据库的查询结果上计算复杂函数。然而连接运算符带来重大挑战，因为连接结果规模可能呈多项式级大于原数据库。计算和存储连接结果成本极高，尤其在数据持续增长时。对连接结果进行采样因而成为许多复杂分析任务的常用方法，同时能提供可证明的统计保证。一种简单方法是先将连接结果物化为表再随机访问，但这丧失了采样性能优势。早在1999年，两篇重要论文[6,13]就提出关键问题：能否不计算完整连接而获得样本？如文献[13]所述，主要障碍在于采样运算符无法下推，即采样$\left( {R \boxtimes  S}\right)  \neq  \operatorname{sample}\left( R\right)  \boxtimes  \operatorname{sample}\left( S\right)$。为突破此障碍，解决方案是设计指导采样过程的索引结构。值得注意的是，针对无环连接（第4节正规定义）提出了一种可在

Permission to make digital or hard copies of all or part of this work for personal or classroom use is granted without fee provided that copies are not made or distributed for profit or commercial advantage and that copies bear this notice and the full citation on the first page. Copyrights for components of this work owned by others than the author(s) must be honored. Abstracting with credit is permitted. To copy otherwise, or republish, to post on servers or to redistribute to lists, requires prior specific permission and/or a fee. Request permissions from permissions@acm.org.

本作品允许为个人或课堂教学目的制作全部或部分数字或硬拷贝，前提是复制品不用于盈利或商业利益，且首页须保留本声明及完整引用。非作者持有的组件版权须受尊重。允许标注来源的摘要。其他复制、再版、服务器发布或列表分发行为需获得特别许可和/或付费。请向permissions@acm.org申请许可。

© 2024 Copyright held by the owner/author(s). Publication rights licensed to ACM.

© 2024 版权归作者所有。出版权由ACM许可。

ACM 2836-6573/2024/6-ART118

ACM 2836-6573/2024/6-ART118

https://doi.org/10.1145/3654921 $O\left( N\right)$ time,where $N$ is the number of tuples in the database,which can then be used to draw a sample of the join results in $O\left( 1\right)$ time [12,30]. Please see Section 2.2 for a more comprehensive review on the sampling complexity for different join queries.

https://doi.org/10.1145/3654921 $O\left( N\right)$时间内构建的索引（其中$N$表示数据库元组数量），该索引随后可用于在$O\left( 1\right)$时间内获取连接结果样本[12,30]。不同连接查询的采样复杂度详见第2.2节。

---

<!-- Footnote -->

*Both authors contributed equally to this research.

*两位作者对本研究贡献均等。

Authors' addresses: Binyang Dai, bdaiab@connect.ust.hk, Hong Kong University of Science and Technology, Clear Water Bay, Hong Kong, China; Xiao Hu, xiaohu@uwaterloo.ca, University of Waterloo, 200 University Ave W, Waterloo, Ontario, Canada, N2L 3G1; Ke Yi, yike@ust.hk, Hong Kong University of Science and Technology, Clear Water Bay, Hong Kong, China.

作者地址：戴彬阳，bdaiab@connect.ust.hk，香港科技大学，中国香港清水湾；胡晓，xiaohu@uwaterloo.ca，滑铁卢大学，200 University Ave W，加拿大安大略省滑铁卢市，N2L 3G1；易珂，yike@ust.hk，香港科技大学，中国香港清水湾。

<!-- Footnote -->

---

This problem becomes more challenging in the streaming setting, where input tuples arrive at a high velocity. How to efficiently and continuously maintain a uniform sample of the join results produced by tuples seen so far? One naive solution would be to re-build the index and re-draw the samples after each tuple has arrived,but this results in a total running time of $O\left( {N}^{2}\right)$ to process a stream with $N$ tuples. Recently,Zhao et al. [31] applied the reservoir sampling algorithms [24,27] to this problem to update the sample incrementally. However, their index also suffers from a high maintenance cost that still leads to a total running time of $O\left( {N}^{2}\right)$ in the worst case.

在流式数据场景下，该问题变得更具挑战性——输入元组以高速率持续到达。如何高效且持续地维护当前所见元组生成连接结果的均匀样本？朴素解法是在每个元组到达后重建索引并重新抽样，但这将导致处理含$N$个元组的数据流时总运行时间高达$O\left( {N}^{2}\right)$。近期，赵等人[31]将蓄水池抽样算法[24,27]应用于该问题以实现增量更新样本。然而其索引仍存在较高维护成本，最坏情况下总运行时间仍为$O\left( {N}^{2}\right)$。

This paper presents a new reservoir sampling algorithm for maintaining a sample over joins with a near-linear running time of $O\left( {N\log N + k\log N\log \frac{N}{k}}\right)$ ,where $k$ is the given sample size. Our algorithm does not need the knowledge of $N$ ; equivalently speaking,it works over an unbounded stream,and the total running time over the first $N$ tuples,for every $N \in  {\mathbb{Z}}^{ + }$ ,satisfies the aforementioned bound. This result is built upon the following two key technical ingredients, both of which are of independent interest.

本文提出新型蓄水池抽样算法，可在$O\left( {N\log N + k\log N\log \frac{N}{k}}\right)$近线性时间内维护连接操作的样本，其中$k$为给定样本量。本算法无需预知$N$，即适用于无界数据流，且对任意$N \in  {\mathbb{Z}}^{ + }$，前$N$个元组的总运行时间均满足上述边界。该成果基于以下两项关键技术要素，二者均具独立研究价值。

Reservoir sampling with a predicate. The classical reservoir sampling algorithm, attributed to Waterman by Knuth [22],maintains a sample of size $k$ in $O\left( N\right)$ time over a stream of $N$ items, which is already optimal. Assuming there is a skip(i) operation that can skip the next $i$ items and jump directly to the next $\left( {i + 1}\right)$ -th item in $O\left( 1\right)$ time,the complexity can be further reduced to $O\left( {k\log \frac{N}{k}}\right)$ ,and there are several algorithms achieving this $\left\lbrack  {{24},{27}}\right\rbrack$ . In this paper,we design a more general reservoir sampling algorithm that,for a given predicate $\theta$ ,maintains a sample of size $k$ only over the items on which $\theta$ evaluates to true. The complexity of our algorithm is $O\left( {\mathop{\sum }\limits_{{i = 1}}^{N}\min \left( {1,\frac{k}{{r}_{i} + 1}}\right) }\right)$ ,where ${r}_{i}$ the number of items in the first $i - 1$ items that pass the predicate. Note that when $\theta \left( \cdot \right)  \equiv$ true,we have ${r}_{i} = i - 1$ and the bound simplifies to $O\left( {k\log \frac{N}{k}}\right)$ ,matching the classical result. Meanwhile, the complexity degrades gracefully as the stream becomes sparser, i.e., less items pass the predicate. Intuitively, sparse streams are more difficult, as it is not safe to skip items. In the extreme case where only one item passes the predicate, then the algorithm is required to return that item as the sample, and we have to check every item in order not to miss it.

谓词约束的蓄水池抽样。Knuth[22]归功于Waterman的经典蓄水池抽样算法，在$N$个项目流上以$O\left( N\right)$时间维护$k$大小的样本，已达最优。若存在skip(i)操作能在$O\left( 1\right)$时间内跳过后续$i$个项目直达第$\left( {i + 1}\right)$项，则复杂度可进一步降至$O\left( {k\log \frac{N}{k}}\right)$，现有多种算法可实现此$\left\lbrack  {{24},{27}}\right\rbrack$。本文设计更通用的蓄水池抽样算法，对于给定谓词$\theta$，仅对满足谓词的项目维护$k$大小样本。算法复杂度为$O\left( {\mathop{\sum }\limits_{{i = 1}}^{N}\min \left( {1,\frac{k}{{r}_{i} + 1}}\right) }\right)$，其中${r}_{i}$表示前$i - 1$个项目中通过谓词检验的数量。当$\theta \left( \cdot \right)  \equiv$恒真时，${r}_{i} = i - 1$成立，边界简化为$O\left( {k\log \frac{N}{k}}\right)$，与经典结果一致。当数据流稀疏（通过谓词项目减少）时，复杂度平缓退化。极端情况下仅一个项目满足谓词时，算法必须返回该项目作为样本，此时需检查每个项目以防遗漏。

However,the assumption that skip(i) takes $O\left( 1\right)$ time is usually not true: One has to at least use a counter to count how many items have been skipped,which already takes $O\left( i\right)$ time. Interestingly, the reservoir sampling over joins problem provides a nice scenario where this assumption is true, except that it has an $O\left( {\log N}\right)$ cost. It is known [7] that the join results of $N$ tuples can be as many as ${N}^{{\rho }^{ * }}$ ,where ${\rho }^{ * }$ is the fractional edge cover number of the join (details provided in Section 5). Thus, the stream of input tuples implicitly defines a polynomially longer (conceptual) stream of join results, which we want to sample from. As there is good structure in the latter, there is no need to materialize this simulated join result stream; and moreover it is possible to skip its items without counting them one by one.

然而，认为skip(i)操作耗时$O\left( 1\right)$的假设通常不成立：至少需要使用计数器统计跳过的条目数量，这本身就需要$O\left( i\right)$时间。有趣的是，连接操作上的蓄水池抽样问题提供了一个理想场景使得该假设成立，只不过需承担$O\left( {\log N}\right)$的成本。已知[7]$N$元组的连接结果可能多达${N}^{{\rho }^{ * }}$，其中${\rho }^{ * }$表示连接的分式边覆盖数（详见第5节）。因此输入元组流隐式定义了一个多项式级增长的（概念性）连接结果流，我们需要从中抽样。由于后者具有良好的结构特性，无需物化这个模拟的连接结果流；更重要的是可以无需逐条计数就能跳过其中条目。

Dynamic sampling from joins. Let $Q$ be an acyclic join query, $\mathcal{R}$ a database instance of size $N$ , and $\mathcal{Q}\left( \mathcal{R}\right)$ the join results of $\mathcal{Q}$ on $\mathcal{R}$ . The second technical ingredient is an index structure that supports the following operations:

动态连接抽样。设$Q$为无环连接查询，$\mathcal{R}$为规模$N$的数据库实例，$\mathcal{Q}\left( \mathcal{R}\right)$表示$\mathcal{Q}$在$\mathcal{R}$上的连接结果。第二个技术要素是支持下列操作的索引结构：

(1) After a tuple is added to $\mathcal{R}$ ,the index structure can be updated in $O\left( {\log N}\right)$ time amortized.

(1) 当元组被添加至$\mathcal{R}$后，索引结构可在$O\left( {\log N}\right)$摊还时间内完成更新。

(2) The index implicitly defines an array $J$ that contains $\mathcal{Q}\left( \mathcal{R}\right)$ plus some dummy tuples,but it is guaranteed that $\left| J\right|  = O\left( \left| {\mathcal{Q}\left( \mathcal{R}\right) }\right| \right)$ ,i.e.,the dummy tuples are no more than a constant fraction. For any given $j \in  \left\lbrack  \left| J\right| \right\rbrack$ ,the index can return $J\left\lbrack  j\right\rbrack$ in $O\left( {\log N}\right)$ time. It can also return $\left| J\right|$ in $O\left( 1\right)$ time. (3) The above is also supported for the delta query $\Delta \mathcal{Q}\left( {\mathcal{R},t}\right)  \mathrel{\text{:=}} \mathcal{Q}\left( {\mathcal{R}\cup \{ t\} }\right)  - \mathcal{Q}\left( \mathcal{R}\right)$ for any tuple $t \notin  \mathcal{R}$ .

(2) 该索引隐式定义了一个数组$J$，其中包含$\mathcal{Q}\left( \mathcal{R}\right)$及部分虚拟元组，但保证$\left| J\right|  = O\left( \left| {\mathcal{Q}\left( \mathcal{R}\right) }\right| \right)$（即虚拟元组不超过恒定比例）。对于任意给定$j \in  \left\lbrack  \left| J\right| \right\rbrack$，索引可在$O\left( {\log N}\right)$时间内返回$J\left\lbrack  j\right\rbrack$，也可在$O\left( 1\right)$时间内返回$\left| J\right|$。(3) 上述功能同样适用于针对任意元组$t \notin  \mathcal{R}$的增量查询$\Delta \mathcal{Q}\left( {\mathcal{R},t}\right)  \mathrel{\text{:=}} \mathcal{Q}\left( {\mathcal{R}\cup \{ t\} }\right)  - \mathcal{Q}\left( \mathcal{R}\right)$。

Note that operation (2) above directly solves the join sampling problem: We simply generate a random $j \in  \left\lbrack  \left| J\right| \right\rbrack$ and find $J\left\lbrack  j\right\rbrack$ ,and repeat if it is dummy. Since $\left| J\right|  = O\left( \left| {\mathcal{Q}\left( \mathcal{R}\right) }\right| \right)$ ,this process will terminate after $O\left( 1\right)$ trials in expectation,so the time to draw a sample is $O\left( {\log N}\right)$ expected. This is only slightly slower than the previous index structures [12, 30], which are inherently static. Furthermore, operations (1) and (2) together also provide a solution for the reservoir sampling over join problem: For each tuple,we first update the index in $O\left( {\log N}\right)$ time and then re-draw $k$ samples in $O\left( {k\log N}\right)$ time. This leads to a total time of $O\left( {{Nk}\log N}\right)$ ,already better than [31],but still not near-linear.

注意操作(2)直接解决了连接抽样问题：只需生成随机$j \in  \left\lbrack  \left| J\right| \right\rbrack$并定位$J\left\lbrack  j\right\rbrack$，若为虚拟元组则重试。由于$\left| J\right|  = O\left( \left| {\mathcal{Q}\left( \mathcal{R}\right) }\right| \right)$，该过程预期在$O\left( 1\right)$次尝试后终止，因此抽样预期耗时$O\left( {\log N}\right)$。这仅比固有静态的现有索引结构[12,30]稍慢。此外，操作(1)与(2)共同提供了连接蓄水池抽样的解决方案：对每个元组，先以$O\left( {\log N}\right)$时间更新索引，再用$O\left( {k\log N}\right)$时间重抽$k$个样本。总耗时$O\left( {{Nk}\log N}\right)$已优于[31]，但仍未达到近线性。

To achieve near-linear time, we use operation (3) in conjunction with our reservoir sampling algorithm. The observation is that each incoming tuple $t$ adds a batch of join results,which are defined by the delta query ${\Delta Q}\left( {\mathcal{R},t}\right)$ . If we can access any tuple in ${\Delta Q}\left( {\mathcal{R},t}\right)$ by position,then we can implement a skip easily. Our index can almost provide this functionality, except that it does so over ${\Delta J}$ ,which is a superset of ${\Delta Q}\left( {\mathcal{R},t}\right)$ that contains some dummy tuples. This is exactly the reason why we need a reservoir sampling algorithm that supports a predicate. We will run it over the stream of batches,where each batch is the ${\Delta J}$ of the corresponding delta query. The predicate evaluates to true for the real tuples while false for the dummies. Finally, since each batch is dense (at least a constant fraction is real), our reservoir sampling algorithm will have good performance.

为实现近线性时间，我们将操作(3)与蓄水池抽样算法结合使用。关键观察在于：每个输入元组$t$都会新增一批连接结果，这些结果由增量查询${\Delta Q}\left( {\mathcal{R},t}\right)$定义。若能通过位置访问${\Delta Q}\left( {\mathcal{R},t}\right)$中的任意元组，则可轻松实现跳过操作。我们的索引几乎能提供此功能，只不过其作用对象是${\Delta J}$——这是包含部分虚拟元组的${\Delta Q}\left( {\mathcal{R},t}\right)$超集。这正是我们需要支持谓词的蓄水池抽样算法的原因。该算法将在批处理流上运行，每批数据对应增量查询的${\Delta J}$。谓词对真实元组返回真值，对虚拟元组返回假值。最后由于每批数据都是密集的（至少恒定比例是真实元组），我们的蓄水池抽样算法将具有优异性能。

The contributions of this paper are thus summarized as follows:

本文贡献可总结如下：

- (Section 3) We formulate the problem of reservoir sampling with a predicate. Assuming skip takes $O\left( 1\right)$ time,we design an algorithm that can maintain a sample (without replacement) of size $k$ in time $O\left( {\mathop{\sum }\limits_{{i = 1}}^{N}\min \left( {1,\frac{k}{{r}_{i} + 1}}\right) }\right)$ ,which we also show is instance-optimal.

-（第3节）我们形式化定义了带谓词的蓄水池抽样问题。假设跳过操作耗时$O\left( 1\right)$，我们设计的算法能在$O\left( {\mathop{\sum }\limits_{{i = 1}}^{N}\min \left( {1,\frac{k}{{r}_{i} + 1}}\right) }\right)$时间内维护容量为$k$的无放回样本，并证明该算法具有实例最优性。

- (Section 4) We present a dynamic index for acyclic joins that can be updated in $O\left( {\log N}\right)$ amortized time,and return a sample from either the full query or the delta query in $O\left( {\log N}\right)$ time. Combined with our reservoir sampling with predicate algorithm, we show how the reservoir sampling over join problem can be solved in time $O\left( {N\log N + k\log N\log \frac{N}{k}}\right)$ . We show how our algorithm can be optimized when key constraints are present.

-（第4节）我们提出适用于无环连接的动态索引，其摊销更新时间为$O\left( {\log N}\right)$，并能以$O\left( {\log N}\right)$时间返回完整查询或增量查询的样本。结合带谓词的蓄水池抽样算法，我们展示了如何在$O\left( {N\log N + k\log N\log \frac{N}{k}}\right)$时间内解决连接操作的蓄水池抽样问题，并说明存在键约束时的优化方法。

- (Section 5) We extend the algorithm to cyclic joins using the generalized hypertree decomposition technique. In this case,the running time becomes $O\left( {{N}^{\mathrm{w}}\log N + k\log N\log \frac{N}{k}}\right)$ ,where $\mathrm{w}$ is the fractional hypertree width of the query.

-（第5节）利用广义超树分解技术，我们将算法扩展至循环连接场景。此时运行时间变为$O\left( {{N}^{\mathrm{w}}\log N + k\log N\log \frac{N}{k}}\right)$，其中$\mathrm{w}$表示查询的分数超树宽度。

- (Section 6) We implement our algorithm and evaluate it over both graph and relational data. The experimental results show that our algorithm significantly outperforms the state-of-the-art solution [31].

-（第6节）我们实现了该算法并在图数据与关系数据上进行评估。实验结果表明本算法显著优于现有最优方案[31]。

## 2 PRELIMINARIES

## 2 预备知识

### 2.1 Problem Definition

### 2.1 问题定义

We first recap some standard notation in relational algebra [5]. A multi-way (natural) join query can be defined as a hypergraph $\mathcal{Q} = \left( {\mathcal{V},\mathcal{E}}\right)$ ,where $\mathcal{V}$ is the set of attributes,and $\mathcal{E} \subseteq  {2}^{\mathcal{V}}$ is the set of relations. Let $\operatorname{dom}\left( x\right)$ be the domain of attribute $x \in  \mathcal{V}$ . A database instance $\mathcal{R}$ consists of a relation instance ${R}_{e}$ for each $e \in  \mathcal{E}$ ,which is a set of tuples and each tuple $t \in  {R}_{e}$ specifies a value in $\operatorname{dom}\left( v\right)$ for each attribute $v \in  e$ . For a tuple $t$ ,we use $\operatorname{supp}\left( t\right)$ to denote the support of $t$ ,i.e.,the set of attributes on which $t$ is defined. For attribute(s) $x$ and tuple $t$ with $x \subseteq  \operatorname{supp}\left( t\right)$ , the projection ${\pi }_{x}t$ is the value of tuple $t$ on attribute(s) $x$ . The join results of $Q$ over instance $\mathcal{R}$ , denoted by $Q\left( \mathcal{R}\right)$ ,is the set of all combinations of tuples,one from each ${R}_{e}$ ,that share common values for their common attributes, i.e.,

我们首先回顾关系代数[5]中的一些标准符号。多路(自然)连接查询可定义为超图$\mathcal{Q} = \left( {\mathcal{V},\mathcal{E}}\right)$，其中$\mathcal{V}$是属性集，$\mathcal{E} \subseteq  {2}^{\mathcal{V}}$是关系集。设$\operatorname{dom}\left( x\right)$为属性$x \in  \mathcal{V}$的域。数据库实例$\mathcal{R}$由每个$e \in  \mathcal{E}$对应的关系实例${R}_{e}$组成，该实例是元组集合且每个元组$t \in  {R}_{e}$为每个属性$v \in  e$指定$\operatorname{dom}\left( v\right)$中的值。对于元组$t$，用$\operatorname{supp}\left( t\right)$表示其支持集，即定义$t$的属性集。对于属性集$x$和满足$x \subseteq  \operatorname{supp}\left( t\right)$的元组$t$，投影${\pi }_{x}t$是元组$t$在属性$x$上的取值。实例$\mathcal{R}$上$Q$的连接结果记作$Q\left( \mathcal{R}\right)$，是所有元组组合的集合，每个组合包含来自各${R}_{e}$的一个元组，这些元组在其共有属性上具有相同值。

$$
\mathcal{Q}\left( \mathcal{R}\right)  = \left\{  {t \in  \mathop{\prod }\limits_{{x \in  \mathcal{V}}}\operatorname{dom}\left( x\right) \mid \forall e \in  \mathcal{E},\exists {t}_{e} \in  {R}_{e},{\pi }_{e}t = {t}_{e}}\right\}  . \tag{1}
$$

For relation ${R}_{e}$ and tuple $t$ ,the semi-join ${R}_{e} \ltimes  t$ returns the set of tuples from ${R}_{e}$ which have the same value(s) on attribute(s) $e \cap  \operatorname{supp}\left( t\right)$ with $t$ . For a pair of relations ${R}_{e},{R}_{{e}^{\prime }}$ ,the semi-join ${R}_{e} \ltimes  {R}_{{e}^{\prime }}$ is the set of tuples from ${R}_{e}$ which has the same value(s) on attribute(s) $e \cap  {e}^{\prime }$ with at least one tuple from ${R}_{{e}^{\prime }}$ . Note that for a join query $\mathcal{Q}$ ,the delta query $\Delta \mathcal{Q}\left( {\mathcal{R},t}\right)$ is equal to $\mathcal{Q}\left( {\mathcal{R}\cup \{ t\} }\right)  \ltimes  t$ .

对于关系${R}_{e}$和元组$t$，半连接${R}_{e} \ltimes  t$返回${R}_{e}$中在属性$e \cap  \operatorname{supp}\left( t\right)$上与$t$取值相同的元组集。对于关系对${R}_{e},{R}_{{e}^{\prime }}$，半连接${R}_{e} \ltimes  {R}_{{e}^{\prime }}$是${R}_{e}$中在属性$e \cap  {e}^{\prime }$上与${R}_{{e}^{\prime }}$至少一个元组取值相同的元组集。注意对于连接查询$\mathcal{Q}$，增量查询$\Delta \mathcal{Q}\left( {\mathcal{R},t}\right)$等于$\mathcal{Q}\left( {\mathcal{R}\cup \{ t\} }\right)  \ltimes  t$。

In the streaming setting,we model each tuple as a triple $u = \left( {t,i,{R}_{e}}\right)$ for $i \in  {\mathbb{Z}}^{ + }$ ,indicating that tuple $t$ is inserted into relation ${R}_{e}$ at time $i$ . Let $D$ be the stream of input tuples,ordered by their timestamp. Let ${\mathcal{R}}^{i}$ be the database defined by the first $i$ tuples of the stream,and set ${\mathcal{R}}^{0} = \varnothing$ . We use $N$ to denote the length of the stream,which is only used in the analysis. The algorithms will not need the knowledge of $N$ ,so they work over an unbounded stream.

在流处理场景中，我们将每个元组建模为三元组$u = \left( {t,i,{R}_{e}}\right)$，表示元组$t$在时刻$i$被插入关系${R}_{e}$。设$D$为按时间戳排序的输入元组流，${\mathcal{R}}^{i}$由流中前$i$个元组定义的数据库，且设${\mathcal{R}}^{0} = \varnothing$。用$N$表示流长度（仅用于分析），算法无需知晓$N$的具体值，因此可处理无界流。

There are two versions of the join sampling problem: The first is the sampling over join problem as studied in $\left\lbrack  {6,{12} - {15},{21},{30}}\right\rbrack$ . This is an indexing (data structure) problem where we wish to have an index that supports drawing a sample from $\mathcal{Q}\left( {\mathcal{R}}^{i}\right)$ . For this problem,we care about the sampling time ${t}_{s}$ and the update time ${t}_{u}$ . For a static index,we care about the index construction time and the sampling time. The other is the reservoir sampling problem, as studied in [24, 27, 31], where we wish to maintain $k$ random samples from $Q\left( {\mathcal{R}}^{i}\right)$ without replacement for every $Q\left( {\mathcal{R}}^{i}\right) ,i \in  {\mathbb{Z}}^{ + }$ . For this problem, we just care about the total running time. Note that any solution for the former yields a solution for the latter with total time $O\left( {{t}_{u} \cdot  N + {t}_{s} \cdot  {Nk}}\right)$ ,but this may not be optimal. For both versions of the problem,all algorithms,including ours,use $O\left( N\right)$ space. Note that the classical reservoir sampling only uses $O\left( k\right)$ space,but sub-linear space is not possible when $Q$ has joins. Just consider a two-table join $Q \mathrel{\text{:=}} {R}_{1}\left( {X,Y}\right)  \ltimes  {R}_{2}\left( {Y,Z}\right)$ . Suppose the first $N$ tuples in the stream are all in ${R}_{1}$ . The algorithm must keep all of them in memory,otherwise it will miss the first join result,which must be sampled,when some tuple in ${R}_{2}$ arrives.

连接采样问题存在两种形式：第一种是如$\left\lbrack  {6,{12} - {15},{21},{30}}\right\rbrack$中研究的连接采样问题，这属于索引（数据结构）问题，旨在构建支持从$\mathcal{Q}\left( {\mathcal{R}}^{i}\right)$抽取样本的索引结构。该问题关注采样时间${t}_{s}$与更新耗时${t}_{u}$。对于静态索引，则需权衡索引构建时间与采样效率。第二种是如[24,27,31]所述的水塘抽样问题，要求针对每个$Q\left( {\mathcal{R}}^{i}\right) ,i \in  {\mathbb{Z}}^{ + }$从$Q\left( {\mathcal{R}}^{i}\right)$无放回地持续维护$k$个随机样本。此情形下仅需考量总运行时间。需注意，前者的解决方案可通过$O\left( {{t}_{u} \cdot  N + {t}_{s} \cdot  {Nk}}\right)$总时间转化为后者方案，但未必最优。两种问题的所有算法（含本方案）均占用$O\left( N\right)$空间。经典水塘抽样仅需$O\left( k\right)$空间，但涉及$Q$连接时亚线性空间不可行。以两表连接$Q \mathrel{\text{:=}} {R}_{1}\left( {X,Y}\right)  \ltimes  {R}_{2}\left( {Y,Z}\right)$为例：若数据流前$N$个元组均属${R}_{1}$，算法必须全量驻留内存，否则当${R}_{2}$出现元组时将遗漏首个必须采样的连接结果。

We follow the convention of data complexity [5] and analyze the running time in terms of the input size $N$ and sample size $k$ ,while taking the size of $Q$ (i.e., $\left| \mathcal{V}\right|$ and $\left| \mathcal{E}\right|$ ) as a constant. We follow the set semantics, so inserting a tuple into a relation that already has it has no effect. Thus we assume that duplicates have been removed from the input stream.

我们遵循数据复杂性[5]的惯例，以输入规模$N$和样本量$k$为基准分析运行时间，同时将$Q$（即$\left| \mathcal{V}\right|$与$\left| \mathcal{E}\right|$）的尺寸视为常量。采用集合语义意味着向已有元组的关系重复插入无效，故假定输入流已去重。

### 2.2 Previous Results

### 2.2 已有研究成果

Sampling over joins. Chaudhuri et al. [13] showed,for the basic two-table join ${R}_{1}\left( {{x}_{1},{x}_{2}}\right)  \land$ ${R}_{2}\left( {{x}_{2},{x}_{3}}\right)$ ,how to construct an index structure in $O\left( N\right)$ time,such that a sample can be drawn in $O\left( 1\right)$ time. Acharya et al. [6] achieved the same complexity result for multi-way joins but all joins are restricted to foreign-key joins. These results have been later extended to all acyclic joins $\left\lbrack  {{12},{30}}\right\rbrack$ . All these sampling indexes on acyclic joins are static. For cyclic joins,there is an index that can be built in $O\left( N\right)$ time,while a sample can be drawn in $O\left( \frac{{N}^{{\rho }^{ * } + 1}}{\left| Q\left( \mathcal{R}\right) \right| }\right)$ time [14],which has recently been improved to $O\left( \frac{{N}^{{\rho }^{ * }}}{\left| Q\left( \mathcal{R}\right) \right| }\right) \left\lbrack  {{15},{21}}\right\rbrack$ ,who also make the index dynamic,but note that the sampling time for cyclic joins is significantly higher than that for acyclic joins.

连接采样。Chaudhuri等人[13]针对基本两表连接${R}_{1}\left( {{x}_{1},{x}_{2}}\right)  \land$${R}_{2}\left( {{x}_{2},{x}_{3}}\right)$，提出了$O\left( N\right)$时间构建索引结构的方法，实现$O\left( 1\right)$时间采样。Acharya等[6]在多表外键连接中达成相同复杂度，后续研究延伸至所有无环连接$\left\lbrack  {{12},{30}}\right\rbrack$。这些无环连接采样索引均为静态结构。对于环状连接，存在$O\left( N\right)$时间构建的索引，其采样时间为$O\left( \frac{{N}^{{\rho }^{ * } + 1}}{\left| Q\left( \mathcal{R}\right) \right| }\right)$[14]，近期改进至$O\left( \frac{{N}^{{\rho }^{ * }}}{\left| Q\left( \mathcal{R}\right) \right| }\right) \left\lbrack  {{15},{21}}\right\rbrack$并实现动态索引，但需注意环状连接的采样耗时显著高于无环连接。

Reservoir sampling over joins. When $Q$ has no joins,the classic reservoir sampling algorithm [22] solves the problem in $O\left( N\right)$ time. Assuming that skip takes $O\left( 1\right)$ time,this can be reduced to $O\left( {k\log \frac{N}{k}}\right) \left\lbrack  {{24},{27}}\right\rbrack$ . Zhao et al. [31] investigated the problem over acyclic joins,and proposed some efficient heuristics. But their solution takes $O\left( {N}^{2}\right)$ time in the worst case,which is the same as the naive solution that rebuilds the static join sampling index $\left\lbrack  {{12},{30}}\right\rbrack$ at each time step.

连接操作上的蓄水池抽样。当$Q$不涉及连接时，经典蓄水池抽样算法[22]可在$O\left( N\right)$时间内解决问题。假设跳过操作耗时$O\left( 1\right)$，该时间可进一步缩短至$O\left( {k\log \frac{N}{k}}\right) \left\lbrack  {{24},{27}}\right\rbrack$。Zhao等人[31]研究了无环连接下的该问题，并提出若干高效启发式方法。但其解决方案在最坏情况下仍需$O\left( {N}^{2}\right)$时间，与每步重建静态连接抽样索引$\left\lbrack  {{12},{30}}\right\rbrack$的原始方案耗时相同。

Hardness results. In this paper we will focus on acyclic joins over an insertion-only stream. Both restrictions turn out to be necessary for achieving a near-linear running time, following some existing hardness results. The observation is that sampling a query is at least as hard as the corresponding Boolean query (i.e.,determining whether $Q\left( \mathcal{R}\right)  = \varnothing$ ): We can just return true for the Boolean query if there is any sample returned from the sampling algorithm, and false otherwise. It is known that it requires $\Omega \left( {N}^{\mathrm{w}}\right)$ time to compute a Boolean cyclic query,for some width parameter $w > 1$ of the query [8]. So for a cyclic query,there is no hope for taking a sample of the join in near-linear time, even over a static database. Meanwhile, for a general (more precisely, a non-hierarchical) acyclic query,it is known that the update time must be $\Omega \left( \sqrt{N}\right)$ just to maintain the Boolean answer, when both insertions and deletions are allowed [10]. This means that the reservoir sampling problem requires at least $\Omega \left( {N}^{1.5}\right)$ time over a fully-dynamic stream.

困难性结论。本文我们将聚焦仅含插入操作的流式无环连接。现有困难性结果表明，要实现近线性时间复杂度，这两个限制条件缺一不可。关键观察在于：查询抽样至少与其对应的布尔查询（即判定$Q\left( \mathcal{R}\right)  = \varnothing$是否成立）同等困难——只需当抽样算法返回样本时判定布尔查询为真，反之则为假。已知计算布尔循环查询需要$\Omega \left( {N}^{\mathrm{w}}\right)$时间（取决于查询的宽度参数$w > 1$[8]）。因此对于循环查询，即使在静态数据库上，也不可能在近线性时间内完成连接抽样。此外对于一般性（更准确说是非层次化）无环查询，当允许插入和删除操作时，仅维护布尔答案就需要$\Omega \left( \sqrt{N}\right)$更新时间[10]。这意味着在全动态流上，蓄水池抽样问题至少需要$\Omega \left( {N}^{1.5}\right)$时间。

## 3 RESERVOIR SAMPLING WITH PREDICATE

## 3 带谓词的蓄水池抽样

### 3.1 Reservoir Sampling Revisited

### 3.1 蓄水池抽样再探

Reservoir sampling $\left\lbrack  {{24},{27}}\right\rbrack$ is a family of algorithms for maintaining a random sample,without replacement,of $k$ items from a possibly infinite stream. The classical version,as described in [22], works as follows. (Step 1) It initializes an array $S$ (called the reservoir) of size $k$ ,which contains the first $k$ items of the input. (Step 2) For each new input ${x}_{i}$ ,it generates a random number $j$ uniformly in $\left\lbrack  {1,i}\right\rbrack$ . If $j \leq  k$ ,then it replaces $S\left\lbrack  j\right\rbrack$ with ${x}_{i}$ . Otherwise,it simply discards ${x}_{i}$ . At any time, $S$ is a uniform sample without replacement of $k$ items of all items processed so far. Clearly,this algorithm takes $O\left( N\right)$ time to process a stream of $N$ items. Also,the algorithm does not need the knowledge of $N$ ,so it works over an unbounded stream.

蓄水池抽样$\left\lbrack  {{24},{27}}\right\rbrack$是一类从不限长度的流中维护$k$个项目无放回随机抽样的算法族。[22]描述的经典版本工作流程如下：（步骤1）初始化大小为$k$的数组$S$（称为蓄水池），存入输入流前$k$项；（步骤2）对每个新输入项${x}_{i}$，生成$\left\lbrack  {1,i}\right\rbrack$范围内的均匀随机数$j$。若$j \leq  k$，则用${x}_{i}$替换$S\left\lbrack  j\right\rbrack$，否则直接丢弃${x}_{i}$。任意时刻$S$都是已处理项目的均匀无放回抽样。显然该算法处理$N$个项目耗时$O\left( N\right)$，且无需预知$N$总量，故适用于无限流。

Assuming a skip(i) operation that can skip the next $i$ items in $O\left( 1\right)$ time,more efficient versions are known. In particular, we will make use of the one from [24]. It is based on the fact that, in a set of $N$ independent random numbers drawn the uniform distribution $\operatorname{Uni}\left( {0,1}\right)$ ,the indices of the smallest $k$ random numbers are a sample without replacement from the index set $\{ 1,2,\cdots ,N\}$ . The algorithm works as follows. (Step 1) It initializes $S$ as before,and set $w = {u}^{1/k}$ for $u \sim  \operatorname{Uni}\left( {0,1}\right)$ . (Step 2) It draws a random number $q$ from the geometric distribution $\operatorname{Geo}\left( w\right)$ ,and skip the next $q$ items. It then replaces a random item from $S$ with ${x}_{i}$ ,and updates $w$ to $w \cdot  {u}^{1/k}$ for $u \sim  \operatorname{Uni}\left( {0,1}\right)$ . It can be shown [24] that at any time, $S$ is a sample without replacement of $k$ items of all items processed so far,and this algorithm runs in $O\left( {k \cdot  \log \frac{N}{k}}\right)$ expected time,which is optimal.

假设存在一个skip(i)操作，能在$O\left( 1\right)$时间内跳过后续$i$个项目，已知存在更高效的改进版本。特别地，我们将采用文献[24]提出的方法。其原理基于：从均匀分布$\operatorname{Uni}\left( {0,1}\right)$中抽取$N$个独立随机数时，其中最小$k$个随机数的索引构成从索引集$\{ 1,2,\cdots ,N\}$中无放回抽取的样本。算法流程如下：（步骤1）如常初始化$S$，并为$u \sim  \operatorname{Uni}\left( {0,1}\right)$设置$w = {u}^{1/k}$；（步骤2）从几何分布$\operatorname{Geo}\left( w\right)$抽取随机数$q$，跳过后续$q$个项目，然后从$S$中随机替换一个项目为${x}_{i}$，并为$u \sim  \operatorname{Uni}\left( {0,1}\right)$将$w$更新至$w \cdot  {u}^{1/k}$。文献[24]证明：在任何时刻，$S$都是已处理项目中无放回抽取的$k$样本，且该算法具有$O\left( {k \cdot  \log \frac{N}{k}}\right)$的期望时间复杂度，这是最优的。

### 3.2 Reservoir Sampling with Predicate

### 3.2 带谓词的水塘抽样

The problem of reservoir sampling with predicate is defined as follows. Given an input stream of items,a predicate $\theta$ and an integer $k > 0$ ,it asks to maintain a sample of size $k$ of all items on which $\theta$ evaluates to true (these items are also called real items,while the others dummy). We assume that $\theta$ can be evaluated in $O\left( 1\right)$ time. Note that the $O\left( N\right)$ -time algorithm easily supports a predicate: We just evaluate $\theta$ on each item and feed the real items to the algorithm. It is more nontrivial to adapt the $O\left( {k\log \frac{N}{k}}\right)$ algorithm,since the skip operation skips an unknown number of real items.

带谓词的水塘抽样问题定义如下：给定物品输入流、谓词$\theta$及整数$k > 0$，要求维护对$\theta$评估为真（这类物品称为实物品，其余为虚物品）的所有物品中大小为$k$的样本。假设$\theta$可在$O\left( 1\right)$时间内完成评估。注意$O\left( N\right)$时间算法天然支持谓词：只需对每个物品评估$\theta$并将实物品输入算法。而适配$O\left( {k\log \frac{N}{k}}\right)$算法更具挑战性，因为跳跃操作会跳过未知数量的实物品。

We adapt the reservoir sampling algorithm [24] to Algorithm 1. In the description, we use the following two primitives:

我们将水塘抽样算法[24]适配为算法1。描述中使用以下两个原语：

- next(   ) returns the next item if it exists, and null otherwise;

- next(   )返回下一物品（若存在），否则返回空值；

- skip(i) skips the next $i$ items and returns the $\left( {i + 1}\right)$ -th item if it exists,and null otherwise.

- skip(i)跳过后续$i$个物品，并返回第$\left( {i + 1}\right)$个物品（若存在），否则返回空值。

Compared with [24], we have made two changes: (line 2-5) when the reservoir is not full, we only add real items to it; (line 11 - 14) we only update the reservoir and the parameter $w$ when the algorithm stops at a real item. The correctness proof of Algorithm 1, given in the full version [2], is quite technical. We only provide some intuition here. For each item $x$ in the stream,Algorithm 1 generates a random variable from $\operatorname{Uni}\left( {0,1}\right)$ ,say ${u}_{x}$ ,and executes line 11-14 for $x$ if ${u}_{x} < w$ ,since the next random variable generated from $\operatorname{Uni}\left( {0,1}\right)$ being smaller than $w$ follows the geometric distribution parameterized by $w$ . We can further exchange the two if-conditions $(w < u$ and $x$ is real) since these two events are independent. Then, one can show that Algorithm 1 is equivalent to feeding only the real items to the non-predicate version of the algorithm in [24].

相较于[24]，我们做了两处修改：（第2-5行）当蓄水池未满时，仅添加实物品；（第11-14行）仅在算法停止于实物品时更新蓄水池及参数$w$。完整版[2]给出的算法1正确性证明技术性较强，此处仅提供直观解释：对于流中每个物品$x$，算法1从$\operatorname{Uni}\left( {0,1}\right)$生成随机变量${u}_{x}$，若${u}_{x} < w$则对$x$执行第11-14行——因为从$\operatorname{Uni}\left( {0,1}\right)$生成的下个随机变量小于$w$的概率服从以$w$为参数的几何分布。由于事件$(w < u$与$x$为实物品相互独立，可交换这两个if条件。由此可证算法1等价于仅将实物品输入[24]中无谓词版本的算法。

<!-- Media -->

Algorithm 1: Reservoir $\left( {D,k,\theta }\right)$

算法1：水塘$\left( {D,k,\theta }\right)$

---

Input : An input stream $D$ of items,an integer $k > 0$ ,and a predicate $\theta$ ;

输入：物品输入流$D$，整数$k > 0$，谓词$\theta$；

Output: A set $S$ maintaining $k$ random samples without replacement of items on which $\theta$

输出：集合$S$，维护对$\theta$评估为真的物品中$k$个无放回随机样本；

					evaluates to true;

					

$S \leftarrow  \varnothing$ ;

while $\left| S\right|  < k$ do

当$\left| S\right|  < k$时执行

		$x \leftarrow  D$ .next(   );

		$x \leftarrow  D$ .next(   );

		if $x =$ null then break;

		if $x =$ null then break;

		if $\theta \left( x\right)$ then $S \leftarrow  S \cup  \{ x\}$ ;

		if $\theta \left( x\right)$ then $S \leftarrow  S \cup  \{ x\}$ ;

$w \leftarrow  \operatorname{rand}{\left( \right) }^{1/k}$ ;

$q \leftarrow  \lfloor \left( {\ln \left( {\operatorname{rand}\left( \right) /\ln \left( {1 - w}\right) }\right) }\right) \rbrack$

while true do

while true do

		$x \leftarrow  D$ .skip(q);

		$x \leftarrow  D$ .skip(q);

		if $x =$ null then break;

		if $x =$ null then break;

		if $\theta \left( x\right)$ then

		if $\theta \left( x\right)$ then

				$y \leftarrow$ a randomly chosen item from $S$ ;

				$y \leftarrow$ 从$S$中随机选取的项;

				$S \leftarrow  S - \{ y\}  + \{ x\}$

				$w \leftarrow  w \cdot  \operatorname{rand}{\left( \right) }^{1/k}$

		$q \leftarrow  \lfloor \left( {\ln \left( {\operatorname{rand}\left( \right) /\ln \left( {1 - w}\right) }\right) }\right) \rfloor$ ; (note that $q \sim  \operatorname{Geo}\left( w\right)$ )

		$q \leftarrow  \lfloor \left( {\ln \left( {\operatorname{rand}\left( \right) /\ln \left( {1 - w}\right) }\right) }\right) \rfloor$ ; (注意$q \sim  \operatorname{Geo}\left( w\right)$)

---

<!-- Media -->

The time complexity of Algorithm 1 depends on how the real and dummy items are distributed in the stream, as more precisely characterized by the following theorem:

算法1的时间复杂度取决于真实项与虚拟项在数据流中的分布情况，下述定理给出了更精确的特征描述：

THEOREM 3.1. Algorithm 1 runs in $O\left( {\alpha  \cdot  \left( {p - 1}\right)  + \gamma  \cdot  \mathop{\sum }\limits_{{i = p}}^{N}\frac{k}{{r}_{i} + 1}}\right)$ expected time over a stream of $N$ items,where ${r}_{i}$ is the number of real items in the first $i - 1$ items, $p$ is the smallest $i$ such that ${r}_{i} = k$ (set $p = N + 1$ if no such $p$ exists),and $\alpha$ and $\gamma$ are the time complexities of next $\left( \cdot \right)$ and skip $\left( \cdot \right)$ , respectively.

定理3.1. 算法1处理$N$项数据流的期望时间复杂度为$O\left( {\alpha  \cdot  \left( {p - 1}\right)  + \gamma  \cdot  \mathop{\sum }\limits_{{i = p}}^{N}\frac{k}{{r}_{i} + 1}}\right)$，其中${r}_{i}$表示前$i - 1$项中真实项的数量，$p$是满足${r}_{i} = k$条件的最小$i$值（若不存在这样的$p$则设$p = N + 1$），$\alpha$和$\gamma$分别为next$\left( \cdot \right)$和skip$\left( \cdot \right)$操作的时间复杂度。

Proof. For the ease of analysis, we add one additional real item as a sentinel object to the end of the stream. We will ignore the cost for this real item later. Let ${S}^{\prime }$ denote the set of items with index larger than or equal to $p$ . We analyze the number of invocations of skip(.) by Algorithm 1. Each time Algorithm 1 calls skip(.) and returns some item $x$ ,we say it stops at $x$ . Note that Algorithm 1 only stops at items that are from ${S}^{\prime }$ . We consider an equivalent version for the ease of analysis: for each item,we generate a random variable from $\operatorname{Uni}\left( {0,1}\right)$ ,say $u$ ,and execute line ${11} - {14}$ only for successful trials (i.e. $u < w$ ). The number of stops is the same as the number of successful trials. If ${w}_{i}$ is the value of $w$ when processing item ${x}_{i}$ ,then the probability that Algorithm 1 stops at ${x}_{i}$ is exactly ${w}_{i}$ for $i \geq  p$ . Let $\mathbf{w} = \left\langle  {{w}_{1},{w}_{2},\cdots ,{w}_{N + 1}}\right\rangle$ be the state of Algorithm 1. For any $w$ , we observe that (1) ${w}_{i} \in  \left\lbrack  {0,1}\right\rbrack$ if $i \in  \left\lbrack  {1\ldots N + 1}\right\rbrack$ ; (2) ${w}_{i} \geq  {w}_{j}$ if $i < j$ ; (3) ${w}_{i} = 1$ if $i < p$ ; (4) ${w}_{i} = {w}_{j}$ if the $j$ -th item is real and the $i$ -th item to the(j - 1)-th item are all dummy. For (4),we use function $\pi \left( i\right)$ to denote the smallest index $j$ where $j \geq  i$ such that ${x}_{j}$ is real. Note that ${w}_{i} = {w}_{\pi \left( i\right) }$ for $i \in  \left\lbrack  {p\ldots N + 1}\right\rbrack$ . Let $W$ be the set of all possible states of Algorithm 1. The expected number of stops in ${S}^{\prime }$ is

证明。为便于分析，我们在数据流末端添加一个真实项目作为哨兵对象。后续将忽略该真实项目的成本。令${S}^{\prime }$表示索引大于等于$p$的项目集合。我们分析算法1调用skip(.)的次数：每当算法1调用skip(.)并返回项目$x$时，称为在$x$处停止。注意算法1仅会停止于${S}^{\prime }$中的项目。为简化分析，考虑等价版本：为每个项目生成$\operatorname{Uni}\left( {0,1}\right)$的随机变量$u$，仅当试验成功时（即$u < w$）执行第${11} - {14}$行。停止次数等于成功试验次数。若处理项目${x}_{i}$时$w$的值为${w}_{i}$，则算法1在${x}_{i}$处停止的概率恰好为${w}_{i}$（当$i \geq  p$时）。设$\mathbf{w} = \left\langle  {{w}_{1},{w}_{2},\cdots ,{w}_{N + 1}}\right\rangle$为算法1的状态，对于任意$w$，可观察到：(1) 若$i \in  \left\lbrack  {1\ldots N + 1}\right\rbrack$则${w}_{i} \in  \left\lbrack  {0,1}\right\rbrack$；(2) 若$i < j$则${w}_{i} \geq  {w}_{j}$；(3) 若$i < p$则${w}_{i} = 1$；(4) 若第$j$个项目为真实且第$i$至(j-1)个项目均为虚拟时${w}_{i} = {w}_{j}$。对于(4)，用函数$\pi \left( i\right)$表示满足$j \geq  i$的最小索引$j$使得${x}_{j}$为真实项目。注意当$i \in  \left\lbrack  {p\ldots N + 1}\right\rbrack$时${w}_{i} = {w}_{\pi \left( i\right) }$。令$W$为算法1所有可能状态的集合，${S}^{\prime }$中期望停止次数为

$$
\mathbb{E}\left\lbrack  {\# \text{ stops in }{S}^{\prime }}\right\rbrack   = \mathop{\sum }\limits_{{\mathbf{w} \in  W}}\Pr \left( \mathbf{w}\right)  \cdot  \mathbb{E}\left\lbrack  {\# \text{ stops in }{S}^{\prime } \mid  \mathbf{w}}\right\rbrack  
$$

$$
 = \mathop{\sum }\limits_{{\mathbf{w} \in  W}}\Pr \left( \mathbf{w}\right)  \cdot  \mathop{\sum }\limits_{{i = p}}^{N}{w}_{\pi \left( i\right) } = \mathop{\sum }\limits_{{i = p}}^{N}\mathop{\sum }\limits_{{\mathbf{w} \in  W}}\Pr \left( \mathbf{w}\right)  \cdot  {w}_{\pi \left( i\right) } = \mathop{\sum }\limits_{{i = p}}^{N}\frac{k}{{r}_{i} + 1}
$$

where the rationale behind the last equality is that $\mathop{\sum }\limits_{{\mathbf{w} \in  W}}\Pr \left( \mathbf{w}\right)  \cdot  {w}_{\pi \left( i\right) }$ is exactly the probability that ${x}_{\pi \left( i\right) }$ enters the reservoir ever. Next,we move to the next(   ) primitive. For while loop in line 2, we keep adding real items into the reservoir until it becomes full. So, the number of invocations of the next(   ) is exactly $p - 1$ . Putting everything together,we complete the proof.

末项等式背后的原理是：$\mathop{\sum }\limits_{{\mathbf{w} \in  W}}\Pr \left( \mathbf{w}\right)  \cdot  {w}_{\pi \left( i\right) }$恰好是${x}_{\pi \left( i\right) }$进入采样池的概率。接下来分析next()原语：对于第2行的while循环，我们持续添加真实项目直至采样池填满，因此next()的调用次数恰好为$p - 1$。综合上述分析，完成证明。

Note that in the degenerate case where all items are real,we have ${r}_{i} = i - 1$ and the running time of Algorithm 1 becomes $O\left( {k\log \frac{N}{k}}\right)$ (when taking $\alpha ,\gamma$ as $O\left( 1\right)$ ),matching the optimal reservoir sampling running time [24,27]. In the other extreme case,all items are dummy,so $p = N + 1$ and ${r}_{i} = 0$ ,and the running time becomes $O\left( N\right)$ ,i.e.,no item is skipped. Indeed in this case,it is not safe to skip anything; otherwise, the algorithm may miss the first real item if one shows up, which must be sampled. Below, we formalize this intuition and prove that Algorithm 1 is not just optimal in these two degenerate cases, but in all cases, namely, it is instance-optimal.

注意在退化情况下（所有项目均为真实时），有${r}_{i} = i - 1$且算法1运行时间为$O\left( {k\log \frac{N}{k}}\right)$（当取$\alpha ,\gamma$为$O\left( 1\right)$时），与最优蓄水池抽样运行时间相符[24,27]。另一极端情况是所有项目均为虚拟，此时$p = N + 1$且${r}_{i} = 0$，运行时间变为$O\left( N\right)$（即无项目被跳过）。实际上在此情况下跳过任何项目都不安全——否则算法可能错过必须采样的首个真实项目。下文将形式化这一直觉，证明算法1不仅在两种退化情况下最优，而且在所有情况下均具有实例最优性。

THEOREM 3.2. For any input stream $S$ of $N$ elements,any algorithm that can maintain a uniform sample of size $k$ over all real elements must run in $\Omega \left( {\mathop{\sum }\limits_{{i = 1}}^{N}\min \left\{  {1,\frac{k}{{r}_{i} + 1}}\right\}  }\right)$ expected time.

定理3.2. 对于任意由$N$个元素组成的输入流$S$，任何能在所有真实元素上维持规模为$k$的均匀采样的算法，其期望运行时间必须为$\Omega \left( {\mathop{\sum }\limits_{{i = 1}}^{N}\min \left\{  {1,\frac{k}{{r}_{i} + 1}}\right\}  }\right)$。

Proof. Consider an arbitrary input stream $S$ and an arbitrary $i \in  \left\lbrack  N\right\rbrack$ . Any correct algorithm for maintaining a uniform sample over real elements at timestamp $i$ must stop at ${x}_{i}$ with probability at least $\min \left\{  {1,\frac{k}{{r}_{i} + 1}}\right\}$ . Recall that any algorithm cannot distinguish whether an element is real or dummy until it stops (and checks). Suppose ${x}_{i}$ is real. If the probability is smaller than $\min \left\{  {1,\frac{k}{{r}_{i} + 1}}\right\}$ , then the probability that ${x}_{i}$ enters into the reservoir must be smaller than $\min \left\{  {1,\frac{k}{{r}_{i} + 1}}\right\}$ ,which contradicts the fact that this algorithm can return a uniform sample at timestamp $i$ . A correct algorithm for maintaining a uniform sample of size $k$ over all real elements in the stream must be correct at timestamp $i$ for $1 \leq  i \leq  N$ . Hence,any correct algorithm must stops at $\mathop{\sum }\limits_{{i = 1}}^{N}\min \left\{  {1,\frac{k}{{r}_{i} + 1}}\right\}$ expected numbers.

证明. 考虑任意输入流$S$和任意$i \in  \left\lbrack  N\right\rbrack$。任何在时间戳$i$处维持真实元素均匀采样的正确算法，必须以至少$\min \left\{  {1,\frac{k}{{r}_{i} + 1}}\right\}$的概率在${x}_{i}$处停止。需注意，任何算法在停止（并检查）前都无法区分元素是真实还是虚拟的。假设${x}_{i}$为真实元素，若该概率小于$\min \left\{  {1,\frac{k}{{r}_{i} + 1}}\right\}$，则${x}_{i}$进入采样池的概率必然小于$\min \left\{  {1,\frac{k}{{r}_{i} + 1}}\right\}$，这与该算法能在时间戳$i$返回均匀采样的事实矛盾。对于流中所有真实元素维持规模$k$均匀采样的正确算法，在时间戳$i$对$1 \leq  i \leq  N$必须保持正确性。因此，任何正确算法的期望停止次数必须为$\mathop{\sum }\limits_{{i = 1}}^{N}\min \left\{  {1,\frac{k}{{r}_{i} + 1}}\right\}$。

Although the running time of Algorithm 1 can vary significantly from $O\left( {k\log \frac{N}{k}}\right)$ to $O\left( N\right)$ ,but it is closer to the former as long as the stream is dense enough.

虽然算法1的运行时间可能在$O\left( {k\log \frac{N}{k}}\right)$到$O\left( N\right)$间大幅波动，但只要数据流足够稠密，其更接近前者。

Definition 3.3 (Dense stream). Given a stream $S = \left\langle  {{x}_{1},{x}_{2},\cdots ,{x}_{n}}\right\rangle  ,S$ is $\phi$ -dense for $0 < \phi  \leq  1$ ,if ${r}_{i} \geq  \phi  \cdot  \left( {i - 1}\right)$ for all $i$ .

定义3.3（稠密流）. 给定流$S = \left\langle  {{x}_{1},{x}_{2},\cdots ,{x}_{n}}\right\rangle  ,S$对$0 < \phi  \leq  1$是$\phi$-稠密的，若对所有$i$满足${r}_{i} \geq  \phi  \cdot  \left( {i - 1}\right)$。

Combining Theorem 3.1 and Definition 3.3 we obtain:

结合定理3.1与定义3.3可得：

COROLLARY 3.4. For any $\phi$ -dense stream where $\phi$ is a constant,Algorithm 2 runs in $O(\alpha  \cdot  k + \gamma  \cdot$ $\left. {k\log \frac{N}{k}}\right)$ expected time.

推论3.4. 对于任何$\phi$为常数的$\phi$-稠密流，算法2的期望运行时间为$O(\alpha  \cdot  k + \gamma  \cdot$$\left. {k\log \frac{N}{k}}\right)$。

We also mention three important properties for dense streams, which will be used later for joins. Lemma 3.5 implies that straightforwardly concatenating two streams still preserves their minimum density of real items. If one stream only consists of dummy items, it is possible to get a better bound on the density of real items in the whole stream, which is essentially captured by Lemma 3.7. The more dummy items padded, the sparser the stream becomes. Lemma 3.6 implies that mixing two streams as their Cartesian product preserves a density that is at least half of their density product. For the ease of notation,we denote ${q}_{i} = {r}_{i + 1}$ (i.e.,the number of real items in the first $i$ items) in the following proofs of Lemma 3.5,Lemma 3.6,and Lemma 3.7. It is easy to see that a stream $S$ is $\phi$ -dense if ${q}_{i} \geq  \phi  \cdot  i$ for all $i$ .

我们还指出稠密流的三个重要性质（将用于后续连接操作）。引理3.5表明直接连接两个流仍能保持其真实项的最小密度。若某流仅含虚拟项，则可能获得整个流中真实项密度的更优边界（由引理3.7本质刻画）。填充的虚拟项越多，流越稀疏。引理3.6说明以笛卡尔积形式混合两流时，其密度至少保持两流密度乘积的一半。为表述简便，在引理3.5、3.6、3.7的证明中，我们记${q}_{i} = {r}_{i + 1}$（即前$i$项中真实项的数量）。易见当对所有$i$满足${q}_{i} \geq  \phi  \cdot  i$时，流$S$是$\phi$-稠密的。

LEMMA 3.5. Given two streams ${S}_{1} = \left\langle  {{x}_{1},{x}_{2},\cdots {x}_{m}}\right\rangle$ and ${S}_{2} = \left\langle  {{y}_{1},{y}_{2},\cdots ,{y}_{n}}\right\rangle$ ,if ${S}_{1}$ is ${\phi }_{1}$ -dense and ${S}_{2}$ is ${\phi }_{2}$ -dense,their concatenation ${S}_{1} \circ  {S}_{2} \mathrel{\text{:=}} \left\langle  {{x}_{1},{x}_{2},\cdots ,{x}_{m},{y}_{1},{y}_{2},\cdots ,{y}_{n}}\right\rangle$ is $\min \left\{  {{\phi }_{1},{\phi }_{2}}\right\}$ -dense.

引理3.5. 给定两个流${S}_{1} = \left\langle  {{x}_{1},{x}_{2},\cdots {x}_{m}}\right\rangle$和${S}_{2} = \left\langle  {{y}_{1},{y}_{2},\cdots ,{y}_{n}}\right\rangle$，若${S}_{1}$是${\phi }_{1}$-稠密的且${S}_{2}$是${\phi }_{2}$-稠密的，则它们的串联${S}_{1} \circ  {S}_{2} \mathrel{\text{:=}} \left\langle  {{x}_{1},{x}_{2},\cdots ,{x}_{m},{y}_{1},{y}_{2},\cdots ,{y}_{n}}\right\rangle$是$\min \left\{  {{\phi }_{1},{\phi }_{2}}\right\}$-稠密的。

Proof. Consider the stream ${S}_{1} \circ  {S}_{2}$ . As ${S}_{1}$ is ${\phi }_{1}$ -dense,we have ${q}_{i} \geq  {\phi }_{1} \cdot  i$ for every $i \in  \left\lbrack  {1\ldots m}\right\rbrack$ . Moreover, ${q}_{m} \geq  {\phi }_{1} \cdot  m$ . As ${S}_{2}$ is ${\phi }_{2}$ -dense,we have ${q}_{j} - {q}_{m} \geq  {\phi }_{2} \cdot  \left( {j - m}\right)$ for every $m \leq  j \leq  m + n$ . Hence, we obtain

证明. 考虑流${S}_{1} \circ  {S}_{2}$。由于${S}_{1}$是${\phi }_{1}$-稠密的，对于每个$i \in  \left\lbrack  {1\ldots m}\right\rbrack$都有${q}_{i} \geq  {\phi }_{1} \cdot  i$。此外，${q}_{m} \geq  {\phi }_{1} \cdot  m$。由于${S}_{2}$是${\phi }_{2}$-稠密的，对于每个$m \leq  j \leq  m + n$都有${q}_{j} - {q}_{m} \geq  {\phi }_{2} \cdot  \left( {j - m}\right)$。因此可得

$$
{q}_{j} \geq  {\phi }_{2} \cdot  \left( {j - m}\right)  + {q}_{m} \geq  {\phi }_{2} \cdot  \left( {j - m}\right)  + {\phi }_{1} \cdot  m \geq  \min \left\{  {{\phi }_{1},{\phi }_{2}}\right\}   \cdot  j
$$

for every $m \leq  j \leq  m + n$ . Together with the fact ${q}_{j} \geq  {\phi }_{1} \cdot  j \geq  \min \left\{  {{\phi }_{1},{\phi }_{2}}\right\}   \cdot  j$ for every $j \in  \left\lbrack  {1\ldots m}\right\rbrack$ , we have proved that ${q}_{j} \geq  \min \left\{  {{\phi }_{1},{\phi }_{2}}\right\}   \cdot  j$ for every $j \in  \left\lbrack  {1\ldots m + n}\right\rbrack$ .

对于每个$m \leq  j \leq  m + n$成立。结合每个$j \in  \left\lbrack  {1\ldots m}\right\rbrack$都满足${q}_{j} \geq  {\phi }_{1} \cdot  j \geq  \min \left\{  {{\phi }_{1},{\phi }_{2}}\right\}   \cdot  j$的事实，我们已证明对于每个$j \in  \left\lbrack  {1\ldots m + n}\right\rbrack$都有${q}_{j} \geq  \min \left\{  {{\phi }_{1},{\phi }_{2}}\right\}   \cdot  j$。

LEMMA 3.6. Given two streams ${S}_{1} = \left\langle  {{x}_{1},{x}_{2},\ldots ,{x}_{m}}\right\rangle$ and ${S}_{2} = \left\langle  {{y}_{1},{y}_{2},\ldots ,{y}_{n}}\right\rangle$ ,if ${S}_{1}$ is ${\phi }_{1}$ -dense and ${S}_{2}$ is ${\phi }_{2}$ -dense,their Cartesian product ${S}_{1} \times  {S}_{2} \mathrel{\text{:=}} \left\langle  {\left( {{x}_{1},{y}_{1}}\right) ,\cdots ,\left( {{x}_{1},{y}_{n}}\right) ,\left( {{x}_{2},{y}_{1}}\right) ,\cdots ,\left( {{x}_{2},{y}_{n}}\right) }\right.$ , $\left. {\left( {{x}_{m},{y}_{1}}\right) ,\cdots \left( {{x}_{m},{y}_{n}}\right) }\right\rangle$ is $\left( \frac{{\phi }_{1}{\phi }_{2}}{2}\right)$ -dense,where $\left( {{x}_{i},{x}_{j}}\right)$ is real if and only if both ${x}_{i}$ and ${x}_{j}$ are real.

引理3.6. 给定两个流${S}_{1} = \left\langle  {{x}_{1},{x}_{2},\ldots ,{x}_{m}}\right\rangle$和${S}_{2} = \left\langle  {{y}_{1},{y}_{2},\ldots ,{y}_{n}}\right\rangle$，若${S}_{1}$是${\phi }_{1}$-稠密的且${S}_{2}$是${\phi }_{2}$-稠密的，则它们的笛卡尔积${S}_{1} \times  {S}_{2} \mathrel{\text{:=}} \left\langle  {\left( {{x}_{1},{y}_{1}}\right) ,\cdots ,\left( {{x}_{1},{y}_{n}}\right) ,\left( {{x}_{2},{y}_{1}}\right) ,\cdots ,\left( {{x}_{2},{y}_{n}}\right) }\right.$,$\left. {\left( {{x}_{m},{y}_{1}}\right) ,\cdots \left( {{x}_{m},{y}_{n}}\right) }\right\rangle$是$\left( \frac{{\phi }_{1}{\phi }_{2}}{2}\right)$-稠密的，其中$\left( {{x}_{i},{x}_{j}}\right)$为实数当且仅当${x}_{i}$和${x}_{j}$均为实数。

Proof. Consider the stream ${S}_{1} \times  {S}_{2} = \left\langle  {{z}_{1},{z}_{2},\ldots {z}_{mn}}\right\rangle$ and an arbitrary $1 \leq  i \leq  {mn}$ . Assume that $m > 0$ and $n > 0$ . Let ${i}_{1} = \left\lfloor  \frac{i}{n}\right\rfloor$ and ${i}_{2} = i - {i}_{1} \cdot  n$ . Let $\left\langle  {{z}_{{jn} + 1},{z}_{{jn} + 2},\ldots {z}_{{jn} + n}}\right\rangle$ be the ${\operatorname{row}}_{j}$ , where $j = 0,1,\ldots {i}_{1} - 1$ . Then ${\operatorname{row}}_{j}$ contains at least ${\phi }_{2} \cdot  n$ real items if ${x}_{j + 1}$ in ${S}_{1}$ is real. Otherwise, all the $n$ items in ${ro}{w}_{j}$ are dummy. Let ${q}_{i}$ be the number of items that are real in the first $i$ items of the resulted stream. We distinguish the following 2 cases:

证明。考虑流${S}_{1} \times  {S}_{2} = \left\langle  {{z}_{1},{z}_{2},\ldots {z}_{mn}}\right\rangle$及任意$1 \leq  i \leq  {mn}$。假设$m > 0$且$n > 0$。令${i}_{1} = \left\lfloor  \frac{i}{n}\right\rfloor$和${i}_{2} = i - {i}_{1} \cdot  n$。设$\left\langle  {{z}_{{jn} + 1},{z}_{{jn} + 2},\ldots {z}_{{jn} + n}}\right\rangle$为${\operatorname{row}}_{j}$，其中$j = 0,1,\ldots {i}_{1} - 1$。则当${S}_{1}$中的${x}_{j + 1}$为真实项时，${\operatorname{row}}_{j}$至少包含${\phi }_{2} \cdot  n$个真实项；否则${ro}{w}_{j}$中所有$n$项均为虚拟项。令${q}_{i}$表示结果流前$i$项中真实项的数量。我们区分以下两种情况：

- ${i}_{1} = 0$ : Since ${S}_{1}$ is ${\phi }_{1}$ -dense,the first item of ${S}_{1}$ must pass predicate $\theta$ . Then in this case, ${q}_{i} \geq  {\phi }_{2} \cdot  i$ as ${S}_{2}$ is ${\phi }_{2}$ -dense.

- ${i}_{1} = 0$：由于${S}_{1}$具有${\phi }_{1}$密度，其首项必通过谓词$\theta$。此时因${S}_{2}$具有${\phi }_{2}$密度，故${q}_{i} \geq  {\phi }_{2} \cdot  i$。

- ${i}_{1} > 0$ : The first $i$ items of the resulted stream can be represented as ${i}_{1}$ rows followed by ${i}_{2}$ items. In the ${i}_{1}$ rows,there are at least ${\phi }_{1} \cdot  {i}_{1}$ rows each contains at least ${\phi }_{2} \cdot  n$ items that are real as ${S}_{1}$ is ${\phi }_{1}$ -dense. In total,there are at least ${\phi }_{1} \cdot  {\phi }_{2} \cdot  n \cdot  {i}_{1}$ item that are real. As $i \leq  \left( {{i}_{1} + 1}\right)  \cdot  n$ ,we have

- ${i}_{1} > 0$：结果流的前$i$项可表示为${i}_{1}$行后接${i}_{2}$项。在${i}_{1}$行中，因${S}_{1}$具有${\phi }_{1}$密度，至少有${\phi }_{1} \cdot  {i}_{1}$行每行包含至少${\phi }_{2} \cdot  n$个真实项。总计至少有${\phi }_{1} \cdot  {\phi }_{2} \cdot  n \cdot  {i}_{1}$个真实项。由$i \leq  \left( {{i}_{1} + 1}\right)  \cdot  n$可得

$$
{q}_{i} \geq  \frac{{\phi }_{1} \cdot  {\phi }_{2} \cdot  n \cdot  {i}_{1}}{i} \cdot  i \geq  \frac{{\phi }_{1} \cdot  {\phi }_{2} \cdot  n \cdot  {i}_{1}}{\left( {{i}_{1} + 1}\right)  \cdot  n} \cdot  i \geq  {\phi }_{1} \cdot  {\phi }_{2} \cdot  \frac{{i}_{1}}{{i}_{1} + 1} \cdot  i \geq  \frac{{\phi }_{1} \cdot  {\phi }_{2}}{2} \cdot  i
$$

As $0 < {\phi }_{1} \leq  1,{q}_{i} \geq  {\phi }_{2} \cdot  i \geq  \frac{{\phi }_{1} \cdot  {\phi }_{2}}{2} \cdot  i$ for the case ${i}_{1} = 0$ . Putting all together,we have ${q}_{i} \geq  \frac{{\phi }_{1} \cdot  {\phi }_{2}}{2} \cdot  i$ .

对于情形${i}_{1} = 0$，因$0 < {\phi }_{1} \leq  1,{q}_{i} \geq  {\phi }_{2} \cdot  i \geq  \frac{{\phi }_{1} \cdot  {\phi }_{2}}{2} \cdot  i$。综上可得${q}_{i} \geq  \frac{{\phi }_{1} \cdot  {\phi }_{2}}{2} \cdot  i$。

LEMMA 3.7. Given a $\phi$ -dense stream of size $m$ ,padding $n$ dummy items at the end yields $a\left( {\frac{m}{m + n} \cdot  \phi }\right)$ - dense stream.

引理3.7。给定规模为$m$的$\phi$密度流，在末尾填充$n$个虚拟项可得到$a\left( {\frac{m}{m + n} \cdot  \phi }\right)$密度流。

Proof. Let $S,{S}^{\prime }$ be the original and resulted stream respectively. Let $\left| S\right|  = m$ and $\left| {S}^{\prime }\right|  = m + n$ ,i.e., padding $n$ dummy items at the end of $S$ . It suffices to prove ${q}_{i} \geq  \phi  \cdot  \frac{m}{m + n} \cdot  i$ for any $m + 1 \leq  i \leq  m + n$ . As $S$ is $\phi$ -dense,we note that ${q}_{m} \geq  \phi  \cdot  m$ . We have

证明。设$S,{S}^{\prime }$分别为原始流与结果流。令$\left| S\right|  = m$且$\left| {S}^{\prime }\right|  = m + n$，即在$S$末尾填充$n$个虚拟项。需证对任意$m + 1 \leq  i \leq  m + n$有${q}_{i} \geq  \phi  \cdot  \frac{m}{m + n} \cdot  i$。因$S$具有$\phi$密度，注意到${q}_{m} \geq  \phi  \cdot  m$。可得

$$
{q}_{i} = {q}_{m} \geq  \phi  \cdot  m \geq  \phi  \cdot  \frac{m}{m + n} \cdot  \left( {m + n}\right)  \geq  \phi  \cdot  \frac{m}{m + n} \cdot  i
$$

### 3.3 Batched Reservoir Sampling with Predicate

### 3.3 带谓词的批量蓄水池采样

As described in Section 1,each arriving tuple $t$ generates a batch of new join results $\Delta \mathcal{Q}\left( {\mathcal{R},t}\right)$ . To apply our reservoir sampling algorithm over joins, we first adapt Algorithm 1 into a batched version. Formally,given an input stream of item-disjoint batches $\left\langle  {{B}_{1},{B}_{2},\cdots ,{B}_{m}}\right\rangle$ ,and a predicate $\theta$ ,the goal is to maintain $k$ uniform samples without replacement from ${B}_{1}^{\theta } \cup  {B}_{2}^{\theta } \cup  \cdots  \cup  {B}_{i}^{\theta }$ for every $i$ ,where ${B}_{i}^{\theta } \subseteq  {B}_{i}$ is the set of real items in batch ${B}_{i}$ .

如第1节所述，每个到达的元组$t$会生成一批新的连接结果$\Delta \mathcal{Q}\left( {\mathcal{R},t}\right)$。为了在连接操作上应用我们的蓄水池采样算法，首先将算法1改造成批处理版本。具体而言，给定一个由物品不相交批次$\left\langle  {{B}_{1},{B}_{2},\cdots ,{B}_{m}}\right\rangle$组成的输入流，以及谓词$\theta$，目标是为每个$i$从${B}_{1}^{\theta } \cup  {B}_{2}^{\theta } \cup  \cdots  \cup  {B}_{i}^{\theta }$中维护$k$个无放回均匀样本，其中${B}_{i}^{\theta } \subseteq  {B}_{i}$是批次${B}_{i}$中的实际物品集合。

The framework of our batched reservoir sampling is described in Algorithm 2, which calls BATCHUPDATE (Algorithm 3) for every batch. BATCHUPDATE essentially runs Algorithm 1 on the given batch $B$ ,but it must guard against the case where a skip(q) may skip out of the batch. For this purpose, it needs another primitive:

我们的批量蓄水池采样框架如算法2所述，该框架为每个批次调用BATCHUPDATE（算法3）。BATCHUPDATE本质上是在给定批次$B$上运行算法1，但必须防范skip(q)可能跳过整个批次的情况。为此，它需要另一个原语：

<!-- Media -->

Algorithm 2: BATCHRESERVOIR $\left( {D,k,\theta }\right)$

算法2：批量蓄水池$\left( {D,k,\theta }\right)$

---

Input : An input stream $D$ of item-disjoint batches,an integer $k > 0$ ,and a predicate $\theta$ ;

输入：由物品不相交批次组成的输入流$D$，整数$k > 0$，以及谓词$\theta$；

Output: A set $S$ maintaining $k$ random samples without replacement of items on which $\theta$

输出：集合$S$维护着对$\theta$评估为真的物品的$k$个无放回随机样本

		evaluates to true;

		评估结果为真；

$S \leftarrow  \varnothing ,w \leftarrow   + \infty ,q \leftarrow  0;$

foreach batch $B \in  D$ do

对每个批次$B \in  D$执行

	$\left( {S,w,q}\right)  \leftarrow  \operatorname{BATCHUPDATE}\left( {S,k,B,q,w,\theta }\right)$ ;

---

Algorithm 3: BATCHUPDATE $\left( {S,k,B,q,w,\theta }\right)$

算法3：批量更新 $\left( {S,k,B,q,w,\theta }\right)$

---

Input : A set $S$ of random samples,an integer $k > 0$ ,a new batch $B$ with the first $q$ items to

输入：随机样本集合 $S$，整数 $k > 0$，新批次 $B$（前 $q$ 项需跳过），参数 $S$ 及谓词 $k > 0$；

						be skipped,parameter $w$ and a predicate $\theta$ ;

						

Output: Updated $S,w$ and $q$ ;

输出：更新后的 $S,w$ 和 $q$；

while $\left| S\right|  < k$ and $B$ .remain $\left( \right)  > 0$ do

当 $\left| S\right|  < k$ 与 $B$ 剩余 $\left( \right)  > 0$ 时循环

			$x \leftarrow  B$ .next(   );

			$x \leftarrow  B$ .next(   );

		if $\theta \left( x\right)$ then $S \leftarrow  S \cup  \{ x\}$ ;

		若 $\theta \left( x\right)$ 则 $S \leftarrow  S \cup  \{ x\}$；

if $\left| S\right|  < k$ then return $S,w,q$ ;

若 $\left| S\right|  < k$ 则返回 $S,w,q$；

if $w > 1$ then

若 $w > 1$ 则

			$w \leftarrow  \operatorname{rand}{\left( \right) }^{1/k}$ ;

$q \leftarrow  \lfloor \left( {\ln \left( {\operatorname{rand}\left( \right) }\right) /\ln \left( {1 - w}\right) }\right) \rfloor$ ; (note that $q \sim  \operatorname{Geo}\left( w\right)$ )

$q \leftarrow  \lfloor \left( {\ln \left( {\operatorname{rand}\left( \right) }\right) /\ln \left( {1 - w}\right) }\right) \rfloor$；（注意 $q \sim  \operatorname{Geo}\left( w\right)$）

while $B$ .remain $\left( \right)  > q$ do

当 $B$ 剩余 $\left( \right)  > q$ 时循环

			$x \leftarrow  B$ .skip(q);

			$x \leftarrow  B$ .skip(q);

			if $\theta \left( x\right)$ then

			若 $\theta \left( x\right)$ 则

					$y \leftarrow$ a randomly chosen item from $S$ ;

					从 $S$ 随机选取一项 $y \leftarrow$；

					$S \leftarrow  S - \{ y\}  + \{ x\}$

					$w \leftarrow  w \cdot  \operatorname{rand}{\left( \right) }^{1/k}$

		$q \leftarrow  \lfloor \left( {\ln \left( {\operatorname{rand}\left( \right) /\ln \left( {1 - w}\right) }\right) }\right) \rfloor$ ; (note that $q \sim  \operatorname{Geo}\left( w\right)$ )

		$q \leftarrow  \lfloor \left( {\ln \left( {\operatorname{rand}\left( \right) /\ln \left( {1 - w}\right) }\right) }\right) \rfloor$；（注意 $q \sim  \operatorname{Geo}\left( w\right)$）

return $S,w,q - B$ .remain(   );

返回 $S,w,q - B$ .remain(   );

---

<!-- Media -->

## remain(   ) returns the number of remaining items in a batch.

## remain(   )返回批次中剩余项目的数量。

More precisely,when $B$ .remain $\left( \right)  \leq  q$ ,we skip all the remaining items in the current batch,and pass $q - B$ .remain(   ) as another parameter to the next batch so that the first $q - B$ .remain(   ) items in the next batch will be skipped. The details are given in Algorithm 3. Moreover, we note that parameters $w,q$ are only initialized once (as line 6-7 in Algorithm 1),i.e.,the first time when the reservoir $S$ is filled with $k$ items. To ensure this in the batched version,we set $w$ with $+ \infty$ at the beginning (line 1 of Algorithm 2),so that $w,q$ will be initialized the first time when the reservoir $S$ is filled with $k$ items,and will never be initialized again no matter how many times Algorithm 3 is invoked,since the value of $w$ is always no larger than 1 after initialization.

更准确地说，当$B$.remain$\left( \right)  \leq  q$时，我们会跳过当前批次中的所有剩余项目，并将$q - B$.remain(   )作为另一个参数传递给下一个批次，以便跳过下一个批次中的前$q - B$.remain(   )个项目。具体细节如算法3所示。此外，我们注意到参数$w,q$仅初始化一次（如算法1的第6-7行），即当蓄水池$S$首次填满$k$个项目时。为了在批次版本中确保这一点，我们在开始时（算法2的第1行）将$w$设置为$+ \infty$，这样$w,q$将在蓄水池$S$首次填满$k$个项目时初始化，并且无论算法3被调用多少次，都不会再次初始化，因为初始化后$w$的值始终不大于1。

The samples maintained by Algorithm 2 are exactly the same as that maintained by Algorithm 1 over items in batches, so correctness follows immediately. Below we analyze its running time.

算法2维护的样本与算法1在批次项目上维护的样本完全相同，因此正确性立即得到保证。下面我们分析其运行时间。

<!-- Media -->

Algorithm 4: ReservoirJoin(Q,D,k)

算法4：ReservoirJoin(Q,D,k)

---

Input : A join query $Q$ ,an input stream $D$ of tuples,and the target number of samples $k$ ;

输入：一个连接查询$Q$，一个元组输入流$D$，以及目标样本数量$k$；

Output: A set $S$ maintaining $k$ random samples without replacement for the join results of

输出：一个集合$S$，维护$k$个无放回的随机样本，用于连接查询$S$在当前所见元组上的结果。

			$Q$ over tuples seen as far;

			$Q$在当前所见元组上的结果；

Initialize index $\mathcal{L},S \leftarrow  \varnothing ,w \leftarrow   + \infty ,q \leftarrow  0,\theta  \leftarrow$ isReal(-);

初始化索引$\mathcal{L},S \leftarrow  \varnothing ,w \leftarrow   + \infty ,q \leftarrow  0,\theta  \leftarrow$为isReal(-)；

while true do

while true do

	$t \leftarrow  D$ .next(   );

	$t \leftarrow  D$.next(   );

	if $t =$ null then break;

	if $t =$为null则break；

	$\mathcal{L} \leftarrow  \operatorname{INDEXUPDATE}\left( {\mathcal{L},t}\right)$ ;

	$B \leftarrow$ BATCHGENERATE(Q,L,t);

	$B \leftarrow$BATCHGENERATE(Q,L,t);

	$\left( {S,w,q}\right)  \leftarrow  \operatorname{BATCHUPDATE}\left( {S,k,w,q,B,\theta }\right) ;$

---

<!-- figureText: Input stream ・・・・・ input tuple real join result dummy join result $\Delta {J}_{5}$ Fig. 1. An illustration of reservoir sampling over join. $\begin{array}{lllll} {t}_{1} & {t}_{2} & {t}_{3} & {t}_{4} & {t}_{5} \end{array}$ reservoir sampling Zhao's work $\Delta \mathcal{Q}\left( {{\mathcal{R}}^{0},{t}_{1}}\right) \;\Delta \mathcal{Q}\left( {{\mathcal{R}}^{1},{t}_{2}}\right) \;\Delta \mathcal{Q}\left( {{\mathcal{R}}^{2},{t}_{3}}\right) \;\Delta \mathcal{Q}\left( {{\mathcal{R}}^{3},{t}_{4}}\right) \;\Delta \mathcal{Q}\left( {{\mathcal{R}}^{4},{t}_{5}}\right)$ reservoir sampling with predicate Our work $\Delta {J}_{1}$ $\Delta {J}_{2}$ -->

<img src="https://cdn.noedgeai.com/0196599d-1a1e-711f-9862-e3c27b652aff_9.jpg?x=137&y=805&w=1296&h=426&r=0"/>

<!-- Media -->

THEOREM 3.8. Given an input stream of batches each of which is $\phi$ -dense for some constant $\phi$ , Algorithm 2 runs in $O\left( {\left( {\alpha  + \beta }\right)  \cdot  k + \left( {\beta  + \gamma }\right)  \cdot  k\log \frac{N}{k} + m}\right)$ expected time over a stream of $m$ item-disjoint batches,where $N$ is the total size of items in all batches,and $\alpha ,\beta ,\gamma$ are the time complexities of next $\left( \cdot \right)$ , remain $\left( \cdot \right)$ ,skip $\left( \cdot \right)$ respectively.

定理3.8。给定一个批次输入流，每个批次对于某个常数$\phi$是$\phi$-密集的，算法2在$m$个项目不相交的批次流上的期望运行时间为$O\left( {\left( {\alpha  + \beta }\right)  \cdot  k + \left( {\beta  + \gamma }\right)  \cdot  k\log \frac{N}{k} + m}\right)$，其中$N$是所有批次中项目的总大小，$\alpha ,\beta ,\gamma$分别是next$\left( \cdot \right)$、remain$\left( \cdot \right)$和skip$\left( \cdot \right)$的时间复杂度。

Proof. Running Algorithm 2 on a stream of batches containing $\phi$ -dense streams is essentially the same as running Algorithm 1 on the concatenation of the $\phi$ -dense streams. The numbers of invocation to next $\left( \cdot \right)$ and skip $\left( \cdot \right)$ in Algorithm 2 are the same as in Algorithm 1. Note that each call to remain $\left( \cdot \right)$ is immediately followed by a call to next $\left( \cdot \right)$ or skip $\left( \cdot \right)$ except for line 15 . In line 15,the return value of remain(.) must be the same as the last call to remain(.) in line 8 . Hence, we can store it in a variable and eliminate the invocation in line 15 . The last $O\left( m\right)$ term comes from the fact that Algorithm 2 makes $m$ calls to BATCHUPDATE in total. Then,it follows Corollary 3.4.

证明。在包含$\phi$-密集流的批次流上运行算法2，本质上与在$\phi$-密集流的连接上运行算法1相同。算法2中next$\left( \cdot \right)$和skip$\left( \cdot \right)$的调用次数与算法1中相同。需要注意的是，除了第15行外，每次调用remain$\left( \cdot \right)$后都会立即调用next$\left( \cdot \right)$或skip$\left( \cdot \right)$。在第15行，remain(.)的返回值必须与第8行中最后一次调用remain(.)相同。因此，我们可以将其存储在变量中并消除第15行的调用。最后的$O\left( m\right)$项来自于算法2总共调用了$m$次BATCHUPDATE。然后，根据推论3.4。

### 3.4 Reservoir Sampling Over Joins

### 3.4 连接上的蓄水池抽样

The framework for reservoir sampling over joins is described in Algorithm 4. For each tuple $t$ in the input stream,we invoke procedure BATCHGENERATE to conceptually generate a batch ${\Delta Q}\left( {\mathcal{R},t}\right)$ and feed it into the batched reservoir sampling algorithm. However, we cannot afford to materialize each batch,whose total size could be as large as $O\left( {N}^{{\rho }^{ * }}\right)$ ,where ${\rho }^{ * }$ is the fractional edge cover number of join (details provided in Section 5). Instead,we will maintain a linear-size index $\mathcal{L}$ , which support a retrieve operation that returns the item at position $z$ in ${\Delta Q}\left( {\mathcal{R},t}\right)$ for any given $z$ . The index should also be able to return ${N}_{B}$ ,the size of the batch. We further maintain a variable pos to indicate the position of the current item retrieved. Then the three primitives required by the batched reservoir sampling algorithm can be implemented as follows:

连接操作的蓄水池抽样框架如算法4所述。对于输入流中的每个元组$t$，我们调用BATCHGENERATE过程在概念上生成批次${\Delta Q}\left( {\mathcal{R},t}\right)$，并将其输入批量蓄水池抽样算法。然而，我们无法承担物化每个批次的开销——其总大小可能高达$O\left( {N}^{{\rho }^{ * }}\right)$（其中${\rho }^{ * }$为连接的分式边覆盖数，详见第5节）。取而代之的是，我们将维护一个线性规模的索引$\mathcal{L}$，该索引支持检索操作：对于任意给定的$z$，返回${\Delta Q}\left( {\mathcal{R},t}\right)$中位于位置$z$的项。该索引还应能返回批次大小${N}_{B}$。我们另维护变量pos表示当前检索项的位置。如此，批量蓄水池抽样算法所需的三个基本操作可如下实现：

- remain(   ) returns ${N}_{B} -$ pos,where ${N}_{B}$ is the size of batch;

- remain()返回${N}_{B} -$ pos，其中${N}_{B}$为批次大小；

- skip(i) increases pos by $i + 1$ and returns the item at pos (i.e.,skips the next $i$ items and jumps directly to the $\left( {i + 1}\right)$ -th item);

- skip(i)将pos增加$i + 1$并返回pos处的项（即跳过后续$i$项直接跳转到第$\left( {i + 1}\right)$项）；

- next(   ) simply returns skip(0);

- next()直接返回skip(0)；

Thus,to apply batched reservoir sampling on any join query $Q$ ,it suffices to show how to maintain a linear-size index $\mathcal{L}$ that can efficiently support the retrieve operation for each ${\Delta Q}\left( {\mathcal{R},t}\right)$ , as well as $\left| {{\Delta Q}\left( {\mathcal{R},t}\right) }\right|$ . This is still hard. To get around this difficulty,in Section 4 we devise an approximate solution. Our index $\mathcal{L}$ will implicitly define a ${\Delta J}$ that contains all the join results in ${\Delta Q}\left( {\mathcal{R},t}\right)$ ,plus some dummy results. However,we should not sample from these dummy join results, and this is exactly the reason why we must use a reservoir sampling algorithm that supports predicate. We set the predicate $\theta$ to isReal(.),which filters out the dummy results. We conceptually add some dummy tuples to base relations as well as some dummy partial join results. In this way, a join result is real if and only if all participated tuples are real, and dummy otherwise (i.e., at least one participated tuple or partial join result is dummy). The details of these dummy join results will be clear in Section 4.2 and Section 4.3. Finally,we will also guarantee that each ${\Delta J}$ is dense so as to apply Theorem 3.8.

因此，要在任意连接查询$Q$上应用批量蓄水池抽样，只需展示如何维护线性规模索引$\mathcal{L}$，该索引能高效支持对每个${\Delta Q}\left( {\mathcal{R},t}\right)$的检索操作及$\left| {{\Delta Q}\left( {\mathcal{R},t}\right) }\right|$的获取。这仍具挑战性。为突破此难点，第4节我们将设计近似解决方案。我们的索引$\mathcal{L}$将隐式定义包含${\Delta Q}\left( {\mathcal{R},t}\right)$中所有连接结果及部分虚拟结果的${\Delta J}$。但不应从这些虚拟连接结果中抽样，这正是必须采用支持谓词的蓄水池抽样算法的原因。我们将谓词$\theta$设为isReal(.)以过滤虚拟结果。我们在概念上向基关系及部分中间连接结果添加虚拟元组。如此，当且仅当所有参与元组为真实时连接结果才为真实，否则为虚拟（即至少存在一个参与元组或中间结果为虚拟）。第4.2与4.3节将详述这些虚拟连接结果。最后，我们还将保证每个${\Delta J}$是稠密的以应用定理3.8。

Comparison with [31]. The algorithm in Zhao et al. [31] in fact follows the same framework, but they simply used the classical reservoir sampling algorithm without predicate. As such, they must use an index $\mathcal{L}$ that supports the retrieve operation and the size information directly on $\Delta \mathcal{Q}\left( {\mathcal{R},t}\right)$ ; please see Figure 1 for an illustration. Such an index takes $O\left( N\right)$ time to update,although they used some heuristics to improve its practical performance. On the other hand, our predicate-enabled reservoir sampling algorithm allows us to use an $\mathcal{L}$ filled with dummy join results,which can be updated in $O\left( {\log N}\right)$ time as shown in the next section.

与文献[31]的对比。Zhao等[31]的算法实际遵循相同框架，但仅使用经典的无谓词蓄水池抽样算法。因此他们必须使用直接在$\Delta \mathcal{Q}\left( {\mathcal{R},t}\right)$上支持检索操作和大小信息的索引$\mathcal{L}$（见图1示意）。该索引更新耗时$O\left( N\right)$，尽管他们采用启发式方法提升实际性能。而我们的支持谓词的蓄水池抽样算法允许使用填充虚拟连接结果的$\mathcal{L}$，该索引可按下一节所述在$O\left( {\log N}\right)$时间内更新。

## 4 SAMPLING OVER ACYCLIC JOINS

## 4 无环连接上的抽样

We first recall the definition of an acyclic join:

首先回顾无环连接的定义：

Definition 4.1 (Acyclic Join [9]). A (natural) join $Q = \left( {\mathcal{V},\mathcal{E}}\right)$ is $\alpha$ -acyclic if there exists a tree $\mathcal{T}$ (called the join tree) such that (1) there is a one-to-one correspondence between the relations in $\mathcal{E}$ and nodes in $\mathcal{T}$ ; and (2) for every attribute $X \in  \mathcal{V}$ ,the set of nodes in $\mathcal{T}$ containing $X$ are connected in $\mathcal{T}$ .

定义4.1（无环连接[9]）。若存在树结构$\mathcal{T}$（称为连接树）使得：(1) 关系集合$\mathcal{E}$与树节点间存在一一对应关系；(2) 对于每个属性$X \in  \mathcal{V}$，包含$X$的节点在树$\mathcal{T}$中形成连通子图，则称（自然）连接$Q = \left( {\mathcal{V},\mathcal{E}}\right)$是$\alpha$-无环的。

In this section, we present a dynamic index that can achieve the following guarantees:

本节提出一种动态索引，其性能保证如下：

THEOREM 4.2. Given any acyclic join $Q$ ,an initially empty database $\mathcal{R}$ ,and a stream of $N$ tuples, we can maintain an index $\mathcal{L}$ on $\mathcal{R}$ using $O\left( N\right)$ space while supporting the following operations:

定理4.2。给定任意无环连接$Q$、初始空数据库$\mathcal{R}$及$N$元组流，可在$O\left( N\right)$空间复杂度下维护数据库$\mathcal{R}$上的索引$\mathcal{L}$，并支持以下操作：

(1) After a tuple $t$ is added to $\mathcal{R},\mathcal{L}$ can be updated in $O\left( {\log N}\right)$ time amortized.

(1) 元组$t$插入$\mathcal{R},\mathcal{L}$后，索引可在$O\left( {\log N}\right)$摊还时间内完成更新

(2) The index implicitly defines an array $J \supseteq  \mathcal{Q}\left( \mathcal{R}\right)$ where the tuples in $\mathcal{Q}\left( \mathcal{R}\right)$ are the real tuples and the others are dummy. The index can return $\left| J\right|$ in $O\left( 1\right)$ time. For any given $j \in  \left\lbrack  \left| J\right| \right\rbrack$ ,it can return $J\left\lbrack  j\right\rbrack$ in $O\left( {\log N}\right)$ time. Furthermore, $J$ is guaranteed to be $\phi$ -dense for some constant $0 < \phi  \leq  1$ .

(2) 该索引隐式定义数组$J \supseteq  \mathcal{Q}\left( \mathcal{R}\right)$，其中$\mathcal{Q}\left( \mathcal{R}\right)$内元组为真实元组，其余为虚拟元组。索引可在$O\left( 1\right)$时间内返回$\left| J\right|$。对于任意给定$j \in  \left\lbrack  \left| J\right| \right\rbrack$，可在$O\left( {\log N}\right)$时间内返回$J\left\lbrack  j\right\rbrack$。此外，$J$保证具有常数$0 < \phi  \leq  1$的$\phi$-稠密性

(3) The above is also supported for the delta query ${\Delta Q}\left( {\mathcal{R},t}\right)$ for any $t \notin  \mathcal{R}$ .

(3) 上述功能同样适用于任意$t \notin  \mathcal{R}$的增量查询${\Delta Q}\left( {\mathcal{R},t}\right)$

This index (using operation (1) and (2) above) immediately solves the dynamic sampling over join problem with an update time of $O\left( {\log N}\right)$ and sampling time $O\left( {\log N}\right)$ . Thanks to operation (3) and the density guarantee, it also solves the reservoir sampling over join problem by plugging into Theorem 3.8 with $\alpha  = \gamma  = O\left( {\log n}\right)$ and $\beta  = O\left( 1\right)$ . The number of batches is $m = N$ ,while the $N$ in Theorem 3.8,which corresponds to the stream of join results now,becomes ${N}^{{\rho }^{ * }}$ . Note that ${\rho }^{ * }$ only depends on the query and not the input size,so it is taken as a constant.

该索引（通过操作(1)(2)）可立即解决连接查询动态采样问题，更新时间与采样时间均为$O\left( {\log N}\right)$。结合操作(3)与稠密性保证，通过代入定理3.8中$\alpha  = \gamma  = O\left( {\log n}\right)$和$\beta  = O\left( 1\right)$参数，还可解决连接查询蓄水池采样问题。批处理次数为$m = N$，而定理3.8中对应连接结果流的$N$参数变为${N}^{{\rho }^{ * }}$。注意${\rho }^{ * }$仅取决于查询而非输入规模，故视为常数

Corollary 4.3. Given any acyclic join $Q$ ,an initially empty database $\mathcal{R}$ ,a sample size $k$ ,and a stream of $N$ tuples,Algorithm 4 maintains $k$ uniform samples without replacement for each $Q\left( {\mathcal{R}}^{i}\right)$ , and runs in $O\left( {N\log N + k\log N\log \frac{N}{k}}\right)$ expected time.

推论4.3。给定任意无环连接$Q$、初始空数据库$\mathcal{R}$、采样规模$k$及$N$元组流，算法4为每个$Q\left( {\mathcal{R}}^{i}\right)$维护$k$个无放回均匀样本，期望运行时间为$O\left( {N\log N + k\log N\log \frac{N}{k}}\right)$

In fact,operation (2) can be reduced to operation (3): The $J$ for $Q\left( \mathcal{R}\right)$ is just the concatenation of all the ${\Delta J}$ ’s of the delta queries. The size of $J$ is the sum of all the $\left| {\Delta J}\right|$ ’s,which can be easily maintained in $O\left( 1\right)$ time. The concatenated $J$ is still dense as long as each ${\Delta J}$ is dense,due to Lemma 3.5. Henceforth we will only focus on operation (1) and (3).

事实上，操作(2)可归约为操作(3)：$J$对于$Q\left( \mathcal{R}\right)$而言，仅是所有增量查询${\Delta J}$的串联结果。$J$的大小为所有$\left| {\Delta J}\right|$之和，这可在$O\left( 1\right)$时间内轻松维护。根据引理3.5，只要每个${\Delta J}$是稠密的，串联后的$J$仍保持稠密性。因此后续我们将仅聚焦于操作(1)和(3)。

### 4.1 Two-table Join

### 4.1 双表连接

We start by considering the simple two-table join ${R}_{1}\left( {X,Y}\right)  \boxtimes  {R}_{2}\left( {Y,Z}\right)$ . For this query,the index simply consists of two arrays ${R}_{1} \ltimes  b$ and ${R}_{2} \ltimes  b$ ,as well as their sizes,for every $b \in  \operatorname{dom}\left( Y\right)$ . The size of two arrays ${R}_{1} \ltimes  b$ and ${R}_{2} \ltimes  b$ is $\left| {{R}_{1} \ltimes  b}\right|$ and $\left| {{R}_{2} \ltimes  b}\right|$ respectively. Summing over all $b \in  \operatorname{dom}\left( Y\right)$ , the whole index uses $O\left( N\right)$ space. Then operation (1) can be easily supported in $O\left( 1\right)$ time: We just add the tuple $t$ to ${R}_{1} \ltimes  t.Y$ if $t \in  {R}_{1}$ ,or ${R}_{2} \ltimes  t.Y$ if $t \in  {R}_{2}$ . For operation (3),suppose $t \in  {R}_{1}$ . We set ${\Delta J} = {R}_{2} \ltimes  t$ . Clearly, $J$ is 1-dense as there are no dummy tuples,and any $J\left\lbrack  j\right\rbrack$ can be retrieved in $O\left( 1\right)$ time.

我们首先考虑简单的双表连接${R}_{1}\left( {X,Y}\right)  \boxtimes  {R}_{2}\left( {Y,Z}\right)$。对于该查询，索引仅由两个数组${R}_{1} \ltimes  b$和${R}_{2} \ltimes  b$及其大小构成，针对每个$b \in  \operatorname{dom}\left( Y\right)$。两个数组${R}_{1} \ltimes  b$和${R}_{2} \ltimes  b$的大小分别为$\left| {{R}_{1} \ltimes  b}\right|$和$\left| {{R}_{2} \ltimes  b}\right|$。对所有$b \in  \operatorname{dom}\left( Y\right)$求和后，整个索引占用$O\left( N\right)$空间。那么操作(1)可在$O\left( 1\right)$时间内轻松实现：若$t \in  {R}_{1}$，我们只需将元组$t$添加至${R}_{1} \ltimes  t.Y$；若$t \in  {R}_{2}$，则添加至${R}_{2} \ltimes  t.Y$。对于操作(3)，假设$t \in  {R}_{1}$。我们设定${\Delta J} = {R}_{2} \ltimes  t$。显然，$J$是1-稠密的（因无虚拟元组），且任何$J\left\lbrack  j\right\rbrack$都可在$O\left( 1\right)$时间内检索。

### 4.2 Line-3 Join

### 4.2 三线连接

When we move to the line-3 join ${R}_{1}\left( {X,Y}\right)  \boxtimes  {R}_{2}\left( {Y,Z}\right)  \boxtimes  {R}_{3}\left( {Z,W}\right)$ ,the situation becomes more complicated. However, even maintaining an index for just finding the delta query sizes is difficult: It is still an open problem if there is a better algorithm than computing each delta query size from scratch,which takes $O\left( N\right)$ time. This is where we need to introduce dummy join results.

当转向三线连接${R}_{1}\left( {X,Y}\right)  \boxtimes  {R}_{2}\left( {Y,Z}\right)  \boxtimes  {R}_{3}\left( {Z,W}\right)$时，情况变得复杂。然而，即便是仅维护用于计算增量查询大小的索引也颇具挑战：是否存在比从头计算每个增量查询大小（耗时$O\left( N\right)$）更优的算法，目前仍是未解难题。这正是我们需要引入虚拟连接结果的原因。

Index Structure. For each $b \in  {\pi }_{Y}{R}_{1}$ ,we maintain the degree of $b$ in ${R}_{1}$ ,i.e., $\operatorname{cnt}\left( b\right)  = \left| {{R}_{1} \ltimes  b}\right|$ and its approximation $\operatorname{cnt}\left( b\right)  = {2}^{\left\lceil  {\log }_{2}\operatorname{cnt}\left( b\right) \right\rceil  }$ by rounding $\operatorname{cnt}\left( b\right)$ up to the nearest power of 2 . Similarly, we maintain cnt $\left( c\right)  = \left| {{R}_{3} \ltimes  c}\right|$ and $\widetilde{\operatorname{cnt}}\left( c\right)  = {2}^{\left\lceil  {\log }_{2}\operatorname{cnt}\left( c\right) \right\rceil  }$ for each $c \in  {\pi }_{Z}{R}_{3}$ . Note that $\widetilde{\operatorname{cnt}}\left( \cdot \right)$ changes at most $O\left( {\log N}\right)$ times.

索引结构。对于每个$b \in  {\pi }_{Y}{R}_{1}$，我们维护$b$在${R}_{1}$中的度数，即$\operatorname{cnt}\left( b\right)  = \left| {{R}_{1} \ltimes  b}\right|$及其近似值$\operatorname{cnt}\left( b\right)  = {2}^{\left\lceil  {\log }_{2}\operatorname{cnt}\left( b\right) \right\rceil  }$（通过将$\operatorname{cnt}\left( b\right)$上取至最近的2的幂获得）。同理，我们为每个$c \in  {\pi }_{Z}{R}_{3}$维护cnt$\left( c\right)  = \left| {{R}_{3} \ltimes  c}\right|$和$\widetilde{\operatorname{cnt}}\left( c\right)  = {2}^{\left\lceil  {\log }_{2}\operatorname{cnt}\left( c\right) \right\rceil  }$。注意$\widetilde{\operatorname{cnt}}\left( \cdot \right)$最多变化$O\left( {\log N}\right)$次。

For each value $b \in  {\pi }_{Y}{R}_{2}$ ,we organize the tuples ${R}_{2} \ltimes  b$ into at most $\log N$ buckets according to the approximate degree of $c$ ,where the $i$ -th bucket is

对于每个值$b \in  {\pi }_{Y}{R}_{2}$，我们根据$c$的近似度将元组${R}_{2} \ltimes  b$组织成最多$\log N$个桶，其中第$i$个桶为

$$
{\Phi }_{i}\left( b\right)  = \left\{  {\left( {b,c}\right)  \in  {R}_{2} : \operatorname{cnt}\left( c\right)  = {2}^{i}}\right\}  .
$$

Let ${\mathcal{L}}_{b}$ be the list of non-empty buckets. Define ${\varphi }_{i}\left( b\right)  = {2}^{i} \cdot  \left| {{\Phi }_{i}\left( b\right) }\right|$ . We also maintain ${N}_{b} =$ $\mathop{\sum }\limits_{{i \in  \left\lbrack  {\log N}\right\rbrack  }}{\varphi }_{i}\left( b\right)$ for each value $b \in  {\pi }_{Y}{R}_{2}$ ,which is an upper bound on the number of new join results if some tuple(a,b)is added to ${R}_{1}$ . Symmetrically,for each $c \in  {\pi }_{Z}{R}_{2}$ ,we maintain such a list ${\mathcal{L}}_{c}$ , and ${N}_{c} = \mathop{\sum }\limits_{{i \in  \left\lbrack  {\log N}\right\rbrack  }}{\varphi }_{i}\left( c\right)$ . Please see Figure 2 for an example.

设${\mathcal{L}}_{b}$为非空桶列表。定义${\varphi }_{i}\left( b\right)  = {2}^{i} \cdot  \left| {{\Phi }_{i}\left( b\right) }\right|$。我们还为每个值$b \in  {\pi }_{Y}{R}_{2}$维护${N}_{b} =$$\mathop{\sum }\limits_{{i \in  \left\lbrack  {\log N}\right\rbrack  }}{\varphi }_{i}\left( b\right)$，这是当元组(a,b)被添加到${R}_{1}$时新连接结果数量的上界。对称地，对于每个$c \in  {\pi }_{Z}{R}_{2}$，我们维护这样的列表${\mathcal{L}}_{c}$和${N}_{c} = \mathop{\sum }\limits_{{i \in  \left\lbrack  {\log N}\right\rbrack  }}{\varphi }_{i}\left( c\right)$。示例见图2。

Space Usage. As there are $O\left( N\right)$ values in ${\pi }_{Y}{R}_{1}$ ,we need to maintain $O\left( N\right)$ degrees and their approximations in total. For each $b \in  {\pi }_{Y}{R}_{2}$ ,it needs to organize the tuples ${R}_{2} \ltimes  b$ into buckets and maintain a value ${N}_{b}$ . The size of non-empty buckets maintained for $b$ is essentially $\left| {{R}_{2} \ltimes  b}\right|$ . Summing over all values $b \in  {\pi }_{Y}{R}_{2}$ ,the total size is $O\left( N\right)$ . Similar argument applies to ${\pi }_{Z}{R}_{2}$ .

空间占用。由于${\pi }_{Y}{R}_{1}$中存在$O\left( N\right)$个值，我们总共需要维护$O\left( N\right)$个度数及其近似值。对于每个$b \in  {\pi }_{Y}{R}_{2}$，需要将元组${R}_{2} \ltimes  b$组织到桶中并维护值${N}_{b}$。为$b$维护的非空桶大小本质上是$\left| {{R}_{2} \ltimes  b}\right|$。对所有$b \in  {\pi }_{Y}{R}_{2}$值求和后，总大小为$O\left( N\right)$。${\pi }_{Z}{R}_{2}$同理。

Index Update. After a tuple $t$ has arrived,we update our data structure as follows. If $t \in  {R}_{2}$ ,say $t = \left( {b,c}\right)$ ,we add(b,c)to ${\Phi }_{i}\left( b\right)$ for $i = {\log }_{2}$ cnt(c). This just takes $O\left( 1\right)$ time.

索引更新。元组$t$到达后，按以下方式更新数据结构。若$t \in  {R}_{2}$（假设为$t = \left( {b,c}\right)$），我们将(b,c)添加到${\Phi }_{i}\left( b\right)$中对应$i = {\log }_{2}$的cnt(c)。此操作仅需$O\left( 1\right)$时间。

If $t = \left( {a,b}\right)  \in  {R}_{1}$ (the $t \in  {R}_{3}$ case is similar),we increase $\operatorname{cnt}\left( b\right)$ by 1,and update $\operatorname{cnt}\left( b\right)$ if needed. If $\widetilde{\operatorname{cnt}}\left( b\right)$ has changed,for each $c \in  {\pi }_{Z}\left( {{R}_{2} \ltimes  b}\right)$ ,we remove(b,c)from ${\Phi }_{i - 1}\left( c\right)$ and add(b,c)to ${\Phi }_{i}\left( c\right)$ ,where $i = {\log }_{2}\operatorname{cnt}\left( b\right)$ . This may take $O\left( N\right)$ time,but this update is only triggered when cnt(b)doubles,which happens at most $O\left( {\log N}\right)$ times. Thus,the total update cost is

若$t = \left( {a,b}\right)  \in  {R}_{1}$（$t \in  {R}_{3}$情况类似），我们将$\operatorname{cnt}\left( b\right)$增加1，并在必要时更新$\operatorname{cnt}\left( b\right)$。若$\widetilde{\operatorname{cnt}}\left( b\right)$发生变化，则对每个$c \in  {\pi }_{Z}\left( {{R}_{2} \ltimes  b}\right)$，我们从${\Phi }_{i - 1}\left( c\right)$移除(b,c)并将其添加到${\Phi }_{i}\left( c\right)$，其中$i = {\log }_{2}\operatorname{cnt}\left( b\right)$。此操作可能耗时$O\left( N\right)$，但仅当cnt(b)翻倍时触发（最多发生$O\left( {\log N}\right)$次）。因此总更新成本为

$$
\mathop{\sum }\limits_{b}\left\lceil  {\log \operatorname{cnt}\left( b\right) }\right\rceil   \cdot  \left| {{\pi }_{Z}\left( {{R}_{2} \ltimes  b}\right) }\right|  \leq  \log N \cdot  \mathop{\sum }\limits_{b}\left| {{\pi }_{Z}\left( {{R}_{2} \ltimes  b}\right) }\right|  \leq  N\log N,
$$

namely,the amortized update cost is $O\left( {\log N}\right)$ . Finally,whenever some ${\Phi }_{i}\left( b\right)$ or ${\Phi }_{i}\left( c\right)$ changes,we update ${N}_{b}$ and ${N}_{c}$ accordingly. The time for this update is the same as that for ${\Phi }_{i}\left( b\right)$ and ${\Phi }_{i}\left( c\right)$ .

即，摊还更新成本为$O\left( {\log N}\right)$。最后，每当某些${\Phi }_{i}\left( b\right)$或${\Phi }_{i}\left( c\right)$发生变化时，我们会相应更新${N}_{b}$和${N}_{c}$。此更新所需时间与${\Phi }_{i}\left( b\right)$和${\Phi }_{i}\left( c\right)$的更新时间相同。

<!-- Media -->

<!-- figureText: $b$ ${\mathcal{L}}_{b}$ ${\psi }_{i}\left( b\right)$ ${N}_{b}$ $c$ ${\mathcal{L}}_{c}$ ${\psi }_{i}\left( c\right)$ ${N}_{c}$ 1 $i = 0$ (1,1) 1 2 (1,2) 1 pos item 5 $i = 2$ (6,5) 4 0 (2,1,1,1) (i) Initialization of data structures built for relation ${R}_{2}$ 1 (2,1,4,4) ${\psi }_{i}\left( b\right)$ ${N}_{b}$ ${\mathcal{L}}_{b}$ ${\psi }_{i}\left( b\right)$ 2 (2,1,4,5) 1 $i = 0$ (1,1) 3 (2,1,2,3) 2 1 (1,4) 4 (2,1,2,4) 4 (1,2) 5 (2,1,2,5) 8 $j = 0,\ell  = 1$ 6 上 (iv) Retrieve at position 4 (v) (Conceptual) batch from the batch of $\left( {2,1}\right)  \in  I$ generated for $\left( {2,1}\right)  \in  {R}_{1}$ 2 3 $i = 0$ (1,1) 1 $i = 1$ $\left( {1,2}\right) \left( {1,4}\right)$ 5 $i = 2$ (2,3) 6 7 $Y$ $W$ Database of line-3 join $b$ ${\mathcal{L}}_{b}$ ${\psi }_{i}\left( b\right)$ $b$ ${\mathcal{L}}_{b}$ $i = 0$ (1,1) $i = 0$ (1,1) 1 $\left( {1,2}\right) \left( {1,4}\right)$ (1,4) (2,2) (1,2) 2 $i = 2$ (2,3) $i = 2$ $\left( {2,2}\right) \left( {2,3}\right)$ (ii) After inserting (2,2) into ${R}_{2}$ (iii) After inserting(2,5)into ${R}_{3}$ -->

<img src="https://cdn.noedgeai.com/0196599d-1a1e-711f-9862-e3c27b652aff_12.jpg?x=133&y=262&w=1279&h=606&r=0"/>

Fig. 2. An illustration of index structure built for line-3 join ${R}_{1}\left( {X,Y}\right)  \boxtimes  {R}_{2}\left( {Y,Z}\right)  \boxtimes  {R}_{3}\left( {Z,W}\right)$ .

图2. 为线3连接${R}_{1}\left( {X,Y}\right)  \boxtimes  {R}_{2}\left( {Y,Z}\right)  \boxtimes  {R}_{3}\left( {Z,W}\right)$构建的索引结构示意图。

<!-- Media -->

Batch Generate. The delta query ${\Delta Q}\left( {\mathcal{R},t}\right)$ on the line-3 join falls into the following 3 cases:

批量生成。线3连接上的增量查询${\Delta Q}\left( {\mathcal{R},t}\right)$可分为以下3种情况：

$$
\Delta \mathcal{Q}\left( {\mathcal{R},t}\right)  = \left\{  \begin{array}{ll} \{ t\}  \times  \left( {{R}_{3} \ltimes  \left( {{R}_{2} \ltimes  b}\right) }\right) & \text{ if }t = \left( {a,b}\right)  \in  {R}_{1} \\  \left( {{R}_{1} \ltimes  b}\right)  \times  \{ t\}  \ltimes  \left( {{R}_{3} \ltimes  c}\right) & \text{ if }t = \left( {b,c}\right)  \in  {R}_{2} \\  \left( {{R}_{1} \ltimes  \left( {{R}_{2} \ltimes  c}\right) }\right)  \times  \{ t\} & \text{ if }t = \left( {c,d}\right)  \in  {R}_{3} \end{array}\right. 
$$

The batch ${\Delta J} \supseteq  {\Delta Q}\left( {\mathcal{R},t}\right)$ for any $t$ is defined as follows. If $t \in  {R}_{2}$ ,say $t = \left( {b,c}\right)$ ,then ${\Delta J} \mathrel{\text{:=}}$ $\left( {{R}_{1} \ltimes  b}\right)  \times  \left( {{R}_{3} \ltimes  c}\right)$ . This batch is 1-dense and $\left| {\Delta J}\right|  = \operatorname{cnt}\left( b\right)  \cdot  \operatorname{cnt}\left( c\right)$ .

任意$t$的批次${\Delta J} \supseteq  {\Delta Q}\left( {\mathcal{R},t}\right)$定义如下：若$t \in  {R}_{2}$（假设为$t = \left( {b,c}\right)$），则${\Delta J} \mathrel{\text{:=}}$$\left( {{R}_{1} \ltimes  b}\right)  \times  \left( {{R}_{3} \ltimes  c}\right)$。该批次是1-稠密的且$\left| {\Delta J}\right|  = \operatorname{cnt}\left( b\right)  \cdot  \operatorname{cnt}\left( c\right)$。

Next,consider the case $t = \left( {a,b}\right)  \in  {R}_{1}$ . Consider a bucket $\left\langle  {i,{\Phi }_{i}\left( b\right) }\right\rangle   \in  {\mathcal{L}}_{b}$ . For each $\left( {b,c}\right)  \in  {\Phi }_{i}\left( b\right)$ , define a mini-batch that consists of all tuples in ${R}_{3} \ltimes  c$ ,followed by $\operatorname{cnt}\left( c\right)  - \operatorname{cnt}\left( c\right)$ dummy tuples. We concatenate these mini-batches to form the batch for the bucket, and concatenate all the buckets to form ${\Delta J}$ . This ${\Delta J}$ is $\frac{1}{2}$ -dense,since each mini-batch is $\frac{1}{2}$ -dense and then we invoke Lemma 3.5. Moreover, $\left| {\Delta J}\right|  = {N}_{b}$ and can be returned in $O\left( 1\right)$ time. The case $t \in  {R}_{3}$ is similar.

接下来考虑$t = \left( {a,b}\right)  \in  {R}_{1}$的情况。设一个桶$\left\langle  {i,{\Phi }_{i}\left( b\right) }\right\rangle   \in  {\mathcal{L}}_{b}$，对于每个$\left( {b,c}\right)  \in  {\Phi }_{i}\left( b\right)$，定义由${R}_{3} \ltimes  c$中所有元组组成的微批次，后接$\operatorname{cnt}\left( c\right)  - \operatorname{cnt}\left( c\right)$个虚拟元组。将这些微批次连接形成桶的批次，再连接所有桶构成${\Delta J}$。由于每个微批次都是$\frac{1}{2}$-稠密的，根据引理3.5可知该${\Delta J}$是$\frac{1}{2}$-稠密的。此外，$\left| {\Delta J}\right|  = {N}_{b}$且可在$O\left( 1\right)$时间内返回。$t \in  {R}_{3}$的情况类似。

Retrieve. We next show how to retrieve a specific element from the ${\Delta J}$ defined above. We consider the two cases $t \in  {R}_{2}$ and $t \in  {R}_{1}$ ( $t \in  {R}_{3}$ is similar),respectively.

检索。接下来展示如何从上述定义的${\Delta J}$中检索特定元素。我们分别考虑$t \in  {R}_{2}$和$t \in  {R}_{1}$两种情况（$t \in  {R}_{3}$的情况类似）。

If $t = \left( {b,c}\right)  \in  {R}_{2},{\Delta J}$ is the Cartesian product of ${R}_{2} \ltimes  b$ and ${R}_{3} \ltimes  c$ . Given a position $z \in  \left\lbrack  \left| {\Delta J}\right| \right\rbrack$ , we first find the unique pair $\left( {{z}_{1},{z}_{2}}\right)  \in  \left\lbrack  \left| {{R}_{2} \ltimes  b}\right| \right\rbrack   \times  \left\lbrack  \left| {{R}_{3} \ltimes  c}\right| \right\rbrack$ such that $z = {z}_{1} \cdot  \left| {{R}_{3} \ltimes  c}\right|  + {z}_{2}$ . Then, we just return the combination of the tuple at position ${z}_{1}$ in ${R}_{2} \ltimes  b$ and the tuple at position ${z}_{2}$ in ${R}_{3} \ltimes  c$ . The retrieve operation in this case takes $O\left( 1\right)$ time.

若$t = \left( {b,c}\right)  \in  {R}_{2},{\Delta J}$是${R}_{2} \ltimes  b$与${R}_{3} \ltimes  c$的笛卡尔积。给定位置$z \in  \left\lbrack  \left| {\Delta J}\right| \right\rbrack$时，首先找到唯一数对$\left( {{z}_{1},{z}_{2}}\right)  \in  \left\lbrack  \left| {{R}_{2} \ltimes  b}\right| \right\rbrack   \times  \left\lbrack  \left| {{R}_{3} \ltimes  c}\right| \right\rbrack$使得$z = {z}_{1} \cdot  \left| {{R}_{3} \ltimes  c}\right|  + {z}_{2}$成立。随后直接返回${R}_{2} \ltimes  b$中位置${z}_{1}$的元组与${R}_{3} \ltimes  c$中位置${z}_{2}$的元组组合。此情况下的检索操作耗时$O\left( 1\right)$。

If $t = \left( {a,b}\right)  \in  {R}_{1}$ ,we retrieve the tuple at position $z$ as follows:

若$t = \left( {a,b}\right)  \in  {R}_{1}$，按以下方式获取位置$z$的元组：

- Let $i \in  \left\lbrack  {0,\log N}\right\rbrack$ be the unique integer such that

- 设$i \in  \left\lbrack  {0,\log N}\right\rbrack$为满足条件的唯一整数

$$
\mathop{\sum }\limits_{{{i}^{\prime } \leq  i - 1 : {\Phi }_{{i}^{\prime }}\left( b\right)  \neq  \varnothing }}{\varphi }_{{i}^{\prime }}\left( b\right)  < z + 1 \leq  \mathop{\sum }\limits_{{{i}^{\prime } \leq  i : {\Phi }_{{i}^{\prime }}\left( b\right)  \neq  \varnothing }}{\varphi }_{{i}^{\prime }}\left( b\right) .
$$

$$
\text{- Set}j = \left\lfloor  {\left( {z - \mathop{\sum }\limits_{{{i}^{\prime } \leq  i - 1 : {\Phi }_{{i}^{\prime }}\left( b\right)  \neq  \varnothing }}{\varphi }_{{i}^{\prime }}\left( b\right) }\right) /{2}^{i}}\right\rfloor  \text{.}
$$

- Set $\ell  = z - \mathop{\sum }\limits_{{{i}^{\prime } \leq  i - 1 : {\Phi }_{{i}^{\prime }}\left( b\right)  \neq  \varnothing }}{\varphi }_{{i}^{\prime }}\left( b\right)  - {2}^{i} \cdot  j$ .

- 设定$\ell  = z - \mathop{\sum }\limits_{{{i}^{\prime } \leq  i - 1 : {\Phi }_{{i}^{\prime }}\left( b\right)  \neq  \varnothing }}{\varphi }_{{i}^{\prime }}\left( b\right)  - {2}^{i} \cdot  j$

Let ${t}^{\prime }$ be the tuple at position $j$ in ${\Phi }_{i}\left( b\right)$ . Then we return the tuple at position $\ell$ in ${R}_{3} \ltimes  {t}^{\prime }$ if $\ell  < \left| {{R}_{3} \ltimes  {t}^{\prime }}\right|$ ,and a dummy tuple otherwise. As there are at most $O\left( {\log N}\right)$ distinct $i$ ’s with ${\Phi }_{i}\left( b\right)  \neq  \varnothing$ ,the value of $i,j,\ell$ can be computed in $O\left( {\log N}\right)$ time. So the retrieve operation takes $O\left( {\log N}\right)$ time in this case.

令${t}^{\prime }$表示${\Phi }_{i}\left( b\right)$中位置$j$的元组。若$\ell  < \left| {{R}_{3} \ltimes  {t}^{\prime }}\right|$成立则返回${R}_{3} \ltimes  {t}^{\prime }$中位置$\ell$的元组，否则返回虚拟元组。由于最多存在$O\left( {\log N}\right)$个不同$i$满足${\Phi }_{i}\left( b\right)  \neq  \varnothing$，$i,j,\ell$的值可在$O\left( {\log N}\right)$时间内计算得出。因此该情况下的检索操作耗时为$O\left( {\log N}\right)$。

### 4.3 Acyclic Joins

### 4.3 无环连接

Finally,we generalize the line-3 algorithm to an arbitrary acyclic join $Q = \left( {\mathcal{V},\mathcal{E}}\right)$ . Let $\mathcal{T}$ be any join tree of $\mathcal{Q}$ . Recall that there is a one-to-one correspondence between nodes in $\mathcal{T}$ and relations in $\mathcal{E}$ . Moreover,for every attribute $X \in  \mathcal{V}$ ,all nodes containing $X$ are connected in $\mathcal{T}$ . By definition, $\mathcal{T}$ is an unrooted tree,but we can root it by specifying any node as the root $r$ . We will consider all the rooted trees where $r$ ranges over all nodes,and the one with root $r$ will be responsible for generating the batch ${\Delta J} \supseteq  {\Delta Q}\left( {\mathcal{R},t}\right)$ for any $t \in  {R}_{r}$ . For example,the line-3 join has one unrooted join tree ${R}_{1} - {R}_{2} - {R}_{3}$ but 3 rooted trees. The line-3 algorithm can be conceptually considered as 3 algorithms, each using one rooted tree. Some data structures among them can be shared, but below we will just focus on one rooted tree for conceptual simplicity.

最后我们将line-3算法推广至任意无环连接$Q = \left( {\mathcal{V},\mathcal{E}}\right)$。设$\mathcal{T}$为$\mathcal{Q}$的任意连接树。注意$\mathcal{T}$中节点与$\mathcal{E}$中的关系存在一一对应。此外，对于每个属性$X \in  \mathcal{V}$，包含$X$的所有节点在$\mathcal{T}$中相互连通。根据定义，$\mathcal{T}$是无根树，但可通过指定任意节点为根节点$r$来确立树根。我们将考察$r$遍历所有节点时的所有有根树，其中以$r$为根的树负责为任意$t \in  {R}_{r}$生成批次${\Delta J} \supseteq  {\Delta Q}\left( {\mathcal{R},t}\right)$。例如line-3连接存在一棵无根连接树${R}_{1} - {R}_{2} - {R}_{3}$但对应3棵有根树。line-3算法在概念上可视为3个算法，各自使用一棵有根树。它们之间可共享部分数据结构，但为概念简洁起见下文仅聚焦于单棵有根树。

Consider a $\mathcal{T}$ rooted at $r$ . We use ${p}_{e}$ to denote the parent of $e$ . For the root $r$ ,set ${p}_{r} = \varnothing$ . Let $\operatorname{key}\left( e\right)  = e \cap  {p}_{e}$ be the common attributes shared between $e$ and its parent ${p}_{e}$ . Let ${C}_{e}$ be the child nodes of node $e$ . For a leaf node $e,{C}_{e} = \varnothing$ . Let ${\mathcal{T}}_{e}$ be the sub-tree below $e$ . With a slight abuse of notation,we also use ${\mathcal{T}}_{e}$ to denote the set of relations whose corresponding nodes are in ${\mathcal{T}}_{e}$ .

考虑一棵以$r$为根的$\mathcal{T}$树。我们用${p}_{e}$表示$e$的父节点。对于根节点$r$，设定${p}_{r} = \varnothing$。令$\operatorname{key}\left( e\right)  = e \cap  {p}_{e}$为$e$与其父节点${p}_{e}$共有的属性集合。设${C}_{e}$是节点$e$的子节点集合。对于叶节点$e,{C}_{e} = \varnothing$，令${\mathcal{T}}_{e}$表示$e$下方的子树。在不严格区分符号的情况下，我们也用${\mathcal{T}}_{e}$表示其对应节点位于该子树内的关系集合。

Index structure. We store input tuples in a hash table,so that for any $e \in  \mathcal{E}$ ,a subset of attributes $x \subseteq  e$ and a tuple $t \in  \operatorname{dom}\left( x\right)$ ,we can get the list of tuples ${R}_{e} \ltimes  t$ in $O\left( 1\right)$ time. For each node $e \in  \mathcal{T}$ and tuple $t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$ ,we maintain a upper bound cnt $\left\lbrack  {\mathcal{T},e,t}\right\rbrack$ on the degree of $t$ in ${\mathcal{T}}_{e}$ ,i.e.,the number of join results over relations in ${\mathcal{T}}_{e}$ whose projection onto attributes key(e)matches $t$ :

索引结构。我们将输入元组存储在哈希表中，使得对于任意$e \in  \mathcal{E}$、属性子集$x \subseteq  e$和元组$t \in  \operatorname{dom}\left( x\right)$，可在$O\left( 1\right)$时间内获取元组列表${R}_{e} \ltimes  t$。对于每个节点$e \in  \mathcal{T}$和元组$t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$，我们维护一个关于$t$在${\mathcal{T}}_{e}$中度的上界cnt$\left\lbrack  {\mathcal{T},e,t}\right\rbrack$，即${\mathcal{T}}_{e}$内关系连接结果中属性key(e)匹配$t$的数量：

$$
\operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   = \left\{  \begin{array}{ll} \left| {{R}_{e} \ltimes  t}\right| & \text{ if }e\text{ is a leaf } \\  \mathop{\sum }\limits_{{{t}^{\prime } \in  {R}_{e} \ltimes  t}}\mathop{\prod }\limits_{{{e}^{\prime } \in  {C}_{e}}}\operatorname{cnt}\left\lbrack  {\mathcal{T},{e}^{\prime },{\pi }_{\operatorname{key}\left( {e}^{\prime }\right) }{t}^{\prime }}\right\rbrack  & \text{ otherwise } \end{array}\right. 
$$

Note that this definition depends on $\widetilde{\operatorname{cnt}}\left( \cdot \right)$ ,which is recursively defined as $\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   =$ ${2}^{\left\lceil  {\log }_{2}\operatorname{cnt}\left\lbrack  \mathcal{T},e,t\right\rbrack  \right\rceil  }$ by rounding cnt $\left\lceil  {\mathcal{T},e,t}\right\rceil$ up to the nearest power of 2 . We point out an important property of $\operatorname{cnt}\left( \cdot \right)$ in Lemma 4.4,which indicates that $\operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$ is a constant-approximation of the degree of $t$ in ${\mathcal{T}}_{e}$ .

注意该定义依赖于$\widetilde{\operatorname{cnt}}\left( \cdot \right)$，其递归定义为将cnt$\left\lceil  {\mathcal{T},e,t}\right\rceil$向上取整至最近的2的幂次方$\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   =$${2}^{\left\lceil  {\log }_{2}\operatorname{cnt}\left\lbrack  \mathcal{T},e,t\right\rbrack  \right\rceil  }$。我们在引理4.4中指出$\operatorname{cnt}\left( \cdot \right)$的重要性质：$\operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$是$t$在${\mathcal{T}}_{e}$中度的常数近似值。

LEMMA 4.4. For a join tree $\mathcal{T}$ ,node $e$ and tuple $t \in  {\pi }_{{key}\left( e\right) }{R}_{e}$ , $\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   \leq  {2}^{\left| {\mathcal{T}}_{e}\right| } \cdot  \left| {\left( {{ \boxtimes  }_{{e}^{\prime } \in  {\mathcal{T}}_{e}}{R}_{{e}^{\prime }}}\right)  \ltimes  t}\right|$ .

引理4.4。对于连接树$\mathcal{T}$、节点$e$和元组$t \in  {\pi }_{{key}\left( e\right) }{R}_{e}$，$\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   \leq  {2}^{\left| {\mathcal{T}}_{e}\right| } \cdot  \left| {\left( {{ \boxtimes  }_{{e}^{\prime } \in  {\mathcal{T}}_{e}}{R}_{{e}^{\prime }}}\right)  \ltimes  t}\right|$。

Proof. We prove it by induction. If $e$ is a leaf node, $\operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   = \left| {{R}_{e} \ltimes  t}\right|$ . As $\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   \leq$ $2\operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$ ,we have $\operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   \leq  2 \cdot  \left| {{R}_{e} \ltimes  t}\right|$ . If $e$ is an internal node,we assume the lemma holds for every child node ${e}^{\prime } \in  {\mathcal{C}}_{e}$ and tuple ${t}^{\prime } \in  {\pi }_{\operatorname{key}\left( {e}^{\prime }\right) }{R}_{{e}^{\prime }}$ . For an arbitrary tuple $t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$ ,we can bound $\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$ as

证明。我们采用归纳法进行证明。若$e$为叶节点，$\operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   = \left| {{R}_{e} \ltimes  t}\right|$。由于$\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   \leq$$2\operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$，可得$\operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   \leq  2 \cdot  \left| {{R}_{e} \ltimes  t}\right|$。若$e$为内部节点，我们假设该引理对所有子节点${e}^{\prime } \in  {\mathcal{C}}_{e}$及元组${t}^{\prime } \in  {\pi }_{\operatorname{key}\left( {e}^{\prime }\right) }{R}_{{e}^{\prime }}$成立。对于任意元组$t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$，可将$\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$界定为

$$
\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   \leq  2 \cdot  \operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   = 2 \cdot  \mathop{\sum }\limits_{{{t}^{\prime } \in  {R}_{e} \ltimes  t}}\mathop{\prod }\limits_{{{e}^{\prime } \in  {C}_{e}}}\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},{e}^{\prime },{\pi }_{\operatorname{key}\left( {e}^{\prime }\right) }{t}^{\prime }}\right\rbrack  
$$

$$
 \leq  2 \cdot  \mathop{\sum }\limits_{{{t}^{\prime } \in  {R}_{e} \ltimes  t}}\mathop{\prod }\limits_{{{e}^{\prime } \in  {C}_{e}}}{2}^{\left| {\mathcal{T}}_{{e}^{\prime }}\right| } \cdot  \left| {\left( {{ \bowtie  }_{{e}^{\prime \prime } \in  {\mathcal{T}}_{{e}^{\prime }}}{R}_{{e}^{\prime \prime }}}\right)  \ltimes  \left( {{\pi }_{\operatorname{key}\left( {e}^{\prime }\right) }{t}^{\prime }}\right) }\right| 
$$

$$
 = 2 \cdot  {2}^{\left| {\mathcal{T}}_{e}\right|  - 1} \cdot  \left| {\left( {{ \bowtie  }_{{e}^{\prime } \in  {\mathcal{T}}_{e}}{R}_{{e}^{\prime }}}\right)  \ltimes  t}\right| 
$$

where the last equality follows the intersections property of $\mathcal{T}$ .

此处最后一个等式遵循$\mathcal{T}$的交集性质。

<!-- Media -->

Algorithm 5: INDEXUPDATE(T,e,t,old)

算法5：索引更新(T,e,t,old)

---

	Input : A join tree $\mathcal{T}$ for $Q$ ,a node $e$ and tuple $t \in  {R}_{e}$ ,an approximate degree old of $t$ in ${\mathcal{T}}_{e}$

	输入：连接树$\mathcal{T}$（用于$Q$）、节点$e$与元组$t \in  {R}_{e}$、$t$在${\mathcal{T}}_{e}$中的近似度旧值

						before update;

						更新前；

	Output:Updated cnt $\left( \cdot \right)$ and $\widetilde{\mathrm{{cnt}}}\left( \cdot \right)$ ;

	输出：更新后的计数$\left( \cdot \right)$及$\widetilde{\mathrm{{cnt}}}\left( \cdot \right)$；

	${t}_{e} \leftarrow  {\pi }_{\operatorname{key}\left( e\right) }t$

	new $\leftarrow  \mathop{\prod }\limits_{{{e}^{\prime } \in  {\mathcal{C}}_{e}}}\operatorname{cnt}\left\lbrack  {\mathcal{T},{e}^{\prime },{\pi }_{\operatorname{key}\left( {e}^{\prime }\right) }t}\right\rbrack$ ;

	新$\leftarrow  \mathop{\prod }\limits_{{{e}^{\prime } \in  {\mathcal{C}}_{e}}}\operatorname{cnt}\left\lbrack  {\mathcal{T},{e}^{\prime },{\pi }_{\operatorname{key}\left( {e}^{\prime }\right) }t}\right\rbrack$；

	${i}^{\prime } \leftarrow  {\log }_{2}$ old and $i \leftarrow  {\log }_{2}$ new;

	${i}^{\prime } \leftarrow  {\log }_{2}$旧值与$i \leftarrow  {\log }_{2}$新值；

if ${i}^{\prime } > 0$ then ${\Phi }_{{i}^{\prime },e}\left( {t}_{e}\right)  \leftarrow  {\Phi }_{{i}^{\prime },e}\left( {t}_{e}\right)  - \{ t\}$ ;

若${i}^{\prime } > 0$则${\Phi }_{{i}^{\prime },e}\left( {t}_{e}\right)  \leftarrow  {\Phi }_{{i}^{\prime },e}\left( {t}_{e}\right)  - \{ t\}$；

${\Phi }_{i,e}\left( {t}_{e}\right)  \leftarrow  {\Phi }_{i,e}\left( {t}_{e}\right)  \cup  \{ t\}$

$j \leftarrow  {2}^{\left\lceil  \log \operatorname{cnt}\left\lbrack  \mathcal{T},e,{t}_{e}\right\rbrack  \right\rceil  }$

$\operatorname{cnt}\left\lbrack  {\mathcal{T},e,{t}_{e}}\right\rbrack   \leftarrow  \operatorname{cnt}\left\lbrack  {\mathcal{T},e,{t}_{e}}\right\rbrack   +$ new-old;

$\operatorname{cnt}\left\lbrack  {\mathcal{T},e,{t}_{e}}\right\rbrack   \leftarrow  \operatorname{cnt}\left\lbrack  {\mathcal{T},e,{t}_{e}}\right\rbrack   +$新旧差值；

if $\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},e,{t}_{e}}\right\rbrack$ changes and ${p}_{e}$ is not the root then

若$\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},e,{t}_{e}}\right\rbrack$发生变化且${p}_{e}$非根节点时

			foreach ${t}^{\prime } \in  {R}_{{p}_{e}} \ltimes  {t}_{e}$ do

			遍历${t}^{\prime } \in  {R}_{{p}_{e}} \ltimes  {t}_{e}$执行

					${\text{old}}^{\prime } \leftarrow  j \cdot  \prod \;\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},{e}^{\prime },{\pi }_{\operatorname{key}\left( {e}^{\prime }\right) }{t}^{\prime }}\right\rbrack$ ;

												${e}^{\prime } \in  {\mathcal{C}}_{pe} - \{ e\}$

					INDEXUPDATE $\left( {\mathcal{T},{p}_{e},{t}^{\prime },{\mathrm{{old}}}^{\prime }}\right)$ ;

					索引更新$\left( {\mathcal{T},{p}_{e},{t}^{\prime },{\mathrm{{old}}}^{\prime }}\right)$；

---

<!-- Media -->

Together with the fact that $\left| {\left( {{ \bowtie  }_{{e}^{\prime } \in  {\mathcal{T}}_{e}}{R}_{{e}^{\prime }}}\right)  \ltimes  t}\right|  \leq  \left| {{ \bowtie  }_{{e}^{\prime } \in  {\mathcal{T}}_{e}}{R}_{{e}^{\prime }}}\right|  \leq  {N}^{\left| {\mathcal{T}}_{e}\right| }$ ,we obtain $\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   \leq$ ${\left( 2N\right) }^{\left| {\mathcal{T}}_{e}\right| }$ ,which implies that $\widetilde{\operatorname{crt}}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$ can only be doubled by at most $O\left( {\log N}\right)$ times.

结合$\left| {\left( {{ \bowtie  }_{{e}^{\prime } \in  {\mathcal{T}}_{e}}{R}_{{e}^{\prime }}}\right)  \ltimes  t}\right|  \leq  \left| {{ \bowtie  }_{{e}^{\prime } \in  {\mathcal{T}}_{e}}{R}_{{e}^{\prime }}}\right|  \leq  {N}^{\left| {\mathcal{T}}_{e}\right| }$这一事实，我们得出$\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   \leq$${\left( 2N\right) }^{\left| {\mathcal{T}}_{e}\right| }$，这意味着$\widetilde{\operatorname{crt}}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$最多只能翻倍$O\left( {\log N}\right)$次。

Consider any non-root node $e$ . For each tuple $t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$ ,we organize the tuples ${t}^{\prime } \in  {R}_{e} \ltimes  t$ into at most $\left| {\mathcal{T}}_{e}\right|  \cdot  \log {2N}$ buckets according to the approximate degree of ${t}^{\prime }$ in ${\mathcal{T}}_{e}$ ,where the $i$ -th bucket is

考虑任意非根节点$e$。对于每个元组$t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$，我们根据${t}^{\prime }$在${\mathcal{T}}_{e}$中的近似度，将元组${t}^{\prime } \in  {R}_{e} \ltimes  t$组织成最多$\left| {\mathcal{T}}_{e}\right|  \cdot  \log {2N}$个桶，其中第$i$个桶为

$$
{\Phi }_{i,e}\left( t\right)  = \left\{  {{t}^{\prime } \in  {R}_{e} \ltimes  t : \mathop{\prod }\limits_{{{e}^{\prime } \in  {C}_{e}}}\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},{e}^{\prime },{\pi }_{\operatorname{key}\left( {e}^{\prime }\right) }{t}^{\prime }}\right\rbrack   = {2}^{i}}\right\}  .
$$

Let ${\mathcal{L}}_{e,t}$ be the list of non-empty buckets. For simplicity,we denote ${\varphi }_{i,e}\left( t\right)  = {2}^{i} \cdot  \left| {{\Phi }_{i,e}\left( t\right) }\right|$ for each $i \in  \left\lbrack  {\left| {\mathcal{T}}_{e}\right|  \cdot  \log {2N}}\right\rbrack$ . We also maintain ${N}_{t} = \mathop{\sum }\limits_{{i \in  \left\lbrack  {\left| {\mathcal{T}}_{e}\right|  \cdot  \log {2N}}\right\rbrack  }}{\varphi }_{i,e}\left( t\right)$ for each $t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$ .

设${\mathcal{L}}_{e,t}$为非空桶列表。为简化表示，我们用${\varphi }_{i,e}\left( t\right)  = {2}^{i} \cdot  \left| {{\Phi }_{i,e}\left( t\right) }\right|$指代每个$i \in  \left\lbrack  {\left| {\mathcal{T}}_{e}\right|  \cdot  \log {2N}}\right\rbrack$。同时为每个$t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$维护${N}_{t} = \mathop{\sum }\limits_{{i \in  \left\lbrack  {\left| {\mathcal{T}}_{e}\right|  \cdot  \log {2N}}\right\rbrack  }}{\varphi }_{i,e}\left( t\right)$。

Space Usage. We consider an arbitrary $e \in  \mathcal{E}$ in an arbitrary join tree maintained. Let ${\mathcal{C}}_{e}$ be the children of ${R}_{e}$ . We build an index on ${R}_{e}$ with $\operatorname{key}\left( {e}_{i}\right)$ as the key for each ${e}_{i} \in  {\mathcal{C}}_{e}$ in order to perform the look up in line 9 of Algorithm 5. There are $\left| {\mathcal{C}}_{e}\right|$ such indices of size $O\left( N\right)$ in total. Moreover, for any non-root node $e$ and each tuple $t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$ ,we organize tuples ${R}_{e} \ltimes  t$ into buckets and maintain ${N}_{t}$ . All these buckets are disjoint and the total size is $O\left( N\right)$ . As there are $O\left( 1\right)$ join trees and each join tree contains $O\left( 1\right)$ nodes,the whole index uses $O\left( N\right)$ space.

空间占用分析。考察维护的任意连接树中任意节点$e \in  \mathcal{E}$。令${\mathcal{C}}_{e}$表示${R}_{e}$的子节点。我们以$\operatorname{key}\left( {e}_{i}\right)$为键在${R}_{e}$上建立索引，以便执行算法5第9行的查询操作。此类索引总共有$\left| {\mathcal{C}}_{e}\right|$个，每个大小为$O\left( N\right)$。此外，对于任意非根节点$e$和每个元组$t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$，我们将元组${R}_{e} \ltimes  t$分桶存储并维护${N}_{t}$。这些桶互不相交，总容量为$O\left( N\right)$。由于存在$O\left( 1\right)$棵连接树且每棵树包含$O\left( 1\right)$个节点，整个索引共占用$O\left( N\right)$空间。

Index Update. We define a generalized procedure for updating our index. As described in Algorithm 5,the procedure INDEXUPDATE takes as input the join tree $\mathcal{T}$ ,a node $e$ ,tuple $t \in  {R}_{e}$ and an integer old $\geq  0$ (indicating the approximated degree of $t$ in ${\mathcal{T}}_{e}$ before update). The update proceeds in a recursive way. We first compute the approximated degree of $t$ in ${\mathcal{T}}_{e}$ after update,denoted as new. For simplicity,denote ${t}_{e} = {\pi }_{\operatorname{key}\left( e\right) }$ . We then remove $t$ from the old bucket if it exists (line 4) and insert $t$ into the new bucket (line 5). We increase $\operatorname{cnt}\left\lbrack  {\mathcal{T},e,{t}_{e}}\right\rbrack$ by new - old (line 7),and update cnt $\left\lbrack  {\mathcal{T},e,{t}_{e}}\right\rbrack$ if needed. If cnt $\left\lbrack  {\mathcal{T},e,{t}_{e}}\right\rbrack$ has changed,we might need to propagate the updates upward (line 8-11). Furthermore,if ${p}_{e}$ is not the root,for each tuple ${t}^{\prime } \in  {R}_{{p}_{e}} \ltimes  {t}_{e}$ ,we compute the approximated degree of ${t}^{\prime }$ in ${\mathcal{T}}_{{p}_{e}}$ before update (line 10),denoted as old ${}^{\prime }$ and invoke this whole procedure recursively (line 11).

索引更新。我们定义了一个通用的索引更新流程。如算法5所述，INDEXUPDATE过程以连接树$\mathcal{T}$、节点$e$、元组$t \in  {R}_{e}$和整数old$\geq  0$（表示更新前$t$在${\mathcal{T}}_{e}$中的近似度）作为输入。该更新以递归方式进行。我们首先计算更新后$t$在${\mathcal{T}}_{e}$中的近似度，记为new。为简化表示，记作${t}_{e} = {\pi }_{\operatorname{key}\left( e\right) }$。随后若存在旧桶，则从中移除$t$（第4行），并将$t$插入新桶（第5行）。我们将$\operatorname{cnt}\left\lbrack  {\mathcal{T},e,{t}_{e}}\right\rbrack$增加new与old的差值（第7行），并在需要时更新cnt$\left\lbrack  {\mathcal{T},e,{t}_{e}}\right\rbrack$。若cnt$\left\lbrack  {\mathcal{T},e,{t}_{e}}\right\rbrack$发生变化，可能需要向上传播更新（第8-11行）。此外，若${p}_{e}$非根节点，则对每个元组${t}^{\prime } \in  {R}_{{p}_{e}} \ltimes  {t}_{e}$计算更新前${t}^{\prime }$在${\mathcal{T}}_{{p}_{e}}$中的近似度（第10行），记为old${}^{\prime }$，并递归调用此完整流程（第11行）。

<!-- Media -->

Algorithm 6: BATCHGENERATE(T,e,t)

算法6：批量生成(T,e,t)

---

Input : A join tree $\mathcal{T}$ for $Q$ ,a node $e$ and a tuple $t \in  {R}_{e}$ or $t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$ ;

输入：连接树$\mathcal{T}$（用于$Q$）、节点$e$及元组$t \in  {R}_{e}$或$t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$；

Output: ${AO}\left( 1\right)$ -dense batch ${\Delta J} \supseteq  \Delta \mathcal{Q}\left( {\mathcal{R},t}\right)$ ;

输出：${AO}\left( 1\right)$密集批次${\Delta J} \supseteq  \Delta \mathcal{Q}\left( {\mathcal{R},t}\right)$；

$x \leftarrow  \operatorname{supp}\left( t\right)$ ;

if $e$ is a leaf node and $x = e$ then return $t$ ;

若$e$是叶节点且$x = e$，则返回$t$；

if $e$ is an internal node and $x = e$ then

若$e$是内部节点且$x = e$，则

	foreach ${e}_{i} \in  {C}_{e}$ do

	对每个${e}_{i} \in  {C}_{e}$执行

			${B}_{i} \leftarrow$ BatchGenerate $\left( {\mathcal{T},{e}_{i},{\pi }_{\operatorname{key}\left( {e}_{i}\right) }t}\right)$ ;

			${B}_{i} \leftarrow$批量生成$\left( {\mathcal{T},{e}_{i},{\pi }_{\operatorname{key}\left( {e}_{i}\right) }t}\right)$；

	return $\{ t\}  \times  \left( {{ \times  }_{{e}_{i} \in  {C}_{e}}{B}_{i}}\right)$ ;

	返回$\{ t\}  \times  \left( {{ \times  }_{{e}_{i} \in  {C}_{e}}{B}_{i}}\right)$；

for ${t}^{\prime } \in  {R}_{e} \ltimes  t$ do ${B}_{{t}^{\prime }} \leftarrow$ BatchGenerate $\left( {\mathcal{T},e,{t}^{\prime }}\right)$ ;

对${t}^{\prime } \in  {R}_{e} \ltimes  t$执行${B}_{{t}^{\prime }} \leftarrow$批量生成$\left( {\mathcal{T},e,{t}^{\prime }}\right)$；

$\leftarrow$ cit $\left\lbrack  {\mathcal{T},e,t}\right\rbrack   - \operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$ dummy elements;

$\leftarrow$引用$\left\lbrack  {\mathcal{T},e,t}\right\rbrack   - \operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$个虚拟元素；

return concatenation of ${B}_{{t}^{\prime }}$ for ${t}^{\prime } \in  {R}_{e} \ltimes  t$ ,followed by $L$ ;

返回${t}^{\prime } \in  {R}_{e} \ltimes  t$的${B}_{{t}^{\prime }}$连接结果，后接$L$；

---

<!-- Media -->

When a tuple $t$ is inserted into ${R}_{e}$ ,we just invoke INDExUPDATE(T,e,t,0)for every join tree $\mathcal{T}$ used in our index. This may take $O\left( N\right)$ time,but the observation is that this update is only triggered when $\operatorname{cnt}\left\lbrack  {\mathcal{T},e,{t}_{e}}\right\rbrack$ changes,which happens at most $O\left( {\log N}\right)$ times. Thus,the total update cost is:

当元组$t$插入${R}_{e}$时，我们仅为索引中使用的每个连接树$\mathcal{T}$调用INDExUPDATE(T,e,t,0)。此操作可能耗时$O\left( N\right)$，但需注意该更新仅在$\operatorname{cnt}\left\lbrack  {\mathcal{T},e,{t}_{e}}\right\rbrack$变更时触发，最多发生$O\left( {\log N}\right)$次。因此总更新成本为：

$$
\mathop{\sum }\limits_{{e \in  \mathcal{T}}}\mathop{\sum }\limits_{{t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}}}\log N \cdot  \left| {{R}_{{p}_{e}} \ltimes  t}\right|  \leq  \log N \cdot  \mathop{\sum }\limits_{{e \in  \mathcal{T}}}\mathop{\sum }\limits_{{t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}}}\left| {{R}_{{p}_{e}} \ltimes  t}\right| 
$$

$$
 \leq  \log N \cdot  \mathop{\sum }\limits_{{{e}^{\prime } \in  \mathcal{T} : {e}^{\prime }\text{ is an internal node }}}\left| {R}_{{e}^{\prime }}\right|  \cdot  \left| {\mathcal{C}}_{{e}^{\prime }}\right|  = O\left( {N\log N}\right) .
$$

Whenever some ${\Phi }_{i,e}\left( t\right)$ changes,we update ${N}_{t}$ accordingly. The time for this update is the same as that for updating ${\Phi }_{i,e}\left( t\right)$ . Finally,summing over all join trees used,each having a distinct relation as its root,the overall update cost is $O\left( {N\log N}\right)$ ,namely,the amortized update cost is $O\left( {\log N}\right)$ .

每当${\Phi }_{i,e}\left( t\right)$变更时，我们相应更新${N}_{t}$。此更新耗时与更新${\Phi }_{i,e}\left( t\right)$相同。最终对所有使用的连接树（各以不同关系为根节点）求和，总更新成本为$O\left( {N\log N}\right)$，即摊还更新成本为$O\left( {\log N}\right)$。

Batch Generate. We define a generalized procedure for generating an $\Omega \left( 1\right)$ -dense batch ${\Delta J} \supseteq$ ${\Delta Q}\left( {\mathcal{R},t}\right)$ for any tuple $t \in  {R}_{e}$ or $t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$ ,as described in Algorithm 6 . The density will depend on the query size,which is taken as a constant,but not on the data size. If tuple $t$ is inserted into ${R}_{e}$ , the fist call is BATCHGENERATE(T,e,t),where $\mathcal{T}$ is the join tree rooted at node $e$ . In each recursive call, Algorithm 6 distinguishes three cases:

批量生成。我们定义了一个通用流程，用于为任意元组$t \in  {R}_{e}$或$t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$生成$\Omega \left( 1\right)$密集型批次${\Delta J} \supseteq$${\Delta Q}\left( {\mathcal{R},t}\right)$，如算法6所述。密度取决于被视为常量的查询规模，而非数据规模。若元组$t$插入${R}_{e}$，首次调用为BATCHGENERATE(T,e,t)，其中$\mathcal{T}$是以节点$e$为根的连接树。递归调用时，算法6区分三种情况：

- Case 1: $e$ is a leaf node and $t \in  {R}_{e}$ . We simply return $t$ as ${\Delta J}$ . This batch is 1-dense and $\left| {\Delta J}\right|  = 1$ .

- 情况1：$e$是叶节点且$t \in  {R}_{e}$。我们直接返回$t$作为${\Delta J}$。此批次为1密集型且$\left| {\Delta J}\right|  = 1$。

- Case 2: $e$ is an internal node and $t \in  {R}_{e}$ . In this case, ${\Delta Q}\left( {\mathcal{R},t}\right)$ can be decomposed into the Cartesian product of $\Delta \mathcal{Q}\left( {\mathcal{R},{\pi }_{\operatorname{key}\left( {e}_{i}\right) }t}\right)$ for each child ${e}_{i} \in  {\mathcal{C}}_{e}$ . The batch ${\Delta J}$ also follows the same way. We recursively generate a batch for ${\pi }_{\operatorname{key}\left( {e}_{i}\right) }t$ in ${\mathcal{T}}_{{e}_{i}}$ for each ${e}_{i} \in  {\mathcal{C}}_{e}$ ,and return their cross product as ${\Delta J}$ .

- 情况2：$e$为内部节点且$t \in  {R}_{e}$。此时，${\Delta Q}\left( {\mathcal{R},t}\right)$可分解为每个子节点${e}_{i} \in  {\mathcal{C}}_{e}$的$\Delta \mathcal{Q}\left( {\mathcal{R},{\pi }_{\operatorname{key}\left( {e}_{i}\right) }t}\right)$笛卡尔积。批次${\Delta J}$也遵循相同方式。我们递归地为每个${e}_{i} \in  {\mathcal{C}}_{e}$在${\mathcal{T}}_{{e}_{i}}$中生成${\pi }_{\operatorname{key}\left( {e}_{i}\right) }t$的批次，并返回它们的笛卡尔积作为${\Delta J}$。

- Case 3: $t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$ . We recursively generate a batch for every tuple ${t}^{\prime } \in  {R}_{e} \ltimes  t$ in ${\mathcal{T}}_{e}$ and concatenate these batches with $\operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   - \operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$ dummy elements at the end as ${\Delta J}$ .

- 情况3：$t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$。我们递归地为${\mathcal{T}}_{e}$中的每个元组${t}^{\prime } \in  {R}_{e} \ltimes  t$生成批次，并在末尾用$\operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   - \operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$个虚拟元素连接这些批次作为${\Delta J}$。

It can be easily shown by induction that

通过归纳法可轻易证明

$$
\left| {\Delta J}\right|  = \left\{  \begin{array}{ll} \mathop{\prod }\limits_{{{e}_{i} \in  {C}_{e}}}\operatorname{cnt}\left\lbrack  {\mathcal{T},{e}_{i},{\pi }_{\operatorname{key}\left( {e}_{i}\right) }t}\right\rbrack  & \text{ if }t \in  {R}_{e} \\  \operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack  & \text{ if }t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e} \end{array}\right. 
$$

where the second case follows the definition of cnt(.). Hence, $\left| {\Delta J}\right|$ can be returned in $O\left( 1\right)$ time. We next prove by induction that

其中第二种情况遵循cnt(.)的定义。因此，$\left| {\Delta J}\right|$可在$O\left( 1\right)$时间内返回。接下来我们用归纳法证明

$$
{\Delta J}\text{ is }\phi \text{-dense,where }\phi  = \left\{  \begin{array}{ll} {\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{e}\right|  - 1} & \text{ if }t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e} \\  {\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{e}\right|  - 2} & \text{ if }t \in  {R}_{e} \end{array}\right. 
$$

<!-- Media -->

Algorithm 7: RETRIEVE(T,e,t,z)

算法7：检索(T,e,t,z)

---

Input : A join tree $\mathcal{T}$ for $Q$ ,a node $e$ and a tuple $t \in  {R}_{e}$ or $t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$ ,an integer $z \geq  0$ ;

输入：连接树$\mathcal{T}$（针对$Q$）、节点$e$、元组$t \in  {R}_{e}$或$t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$、整数$z \geq  0$；

Output:The element at position $z$ in the batch generated for $t$ by BATCHGENERATE(T,e,t);

输出：BATCHGENERATE(T,e,t)为$t$生成的批次中位置$z$处的元素；

$x \leftarrow  \operatorname{supp}\left( t\right)$ ;

if $e$ is a leaf node then

若$e$为叶节点则

	if $z \geq  \operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$ then return $\bot$ ;

	若$z \geq  \operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$则返回$\bot$；

	else return the element at position $z$ in ${R}_{e} \ltimes  t$ ;

	否则返回${R}_{e} \ltimes  t$中位置$z$处的元素；

if $e = x$ then

若$e = x$则

	${C}_{e} \leftarrow  \left\{  {{e}_{1},{e}_{2},\cdots ,{e}_{m}}\right\}$

	foreach $i \in  \left\lbrack  {1\ldots m}\right\rbrack$ do ${t}_{i} \leftarrow  {\pi }_{\operatorname{key}\left( {e}_{i}\right) }t$ ;

	对每个$i \in  \left\lbrack  {1\ldots m}\right\rbrack$执行${t}_{i} \leftarrow  {\pi }_{\operatorname{key}\left( {e}_{i}\right) }t$；

	Find $\left( {{z}_{1},{z}_{2},\cdots ,{z}_{m}}\right)  \in  { \times  }_{i = 1}^{m}$ c $\widetilde{\operatorname{cn}}\left\lbrack  {\mathcal{T},{e}_{i},{t}_{i}}\right\rbrack$ such that $z = \mathop{\sum }\limits_{{i \in  \left\lbrack  {1\ldots m}\right\rbrack  }}\left( {{z}_{i} \cdot  \mathop{\prod }\limits_{{j > i}}\widetilde{\operatorname{cn}}\left\lbrack  {\mathcal{T},{e}_{j},{t}_{j}}\right\rbrack  }\right)$ ;

	找到满足$z = \mathop{\sum }\limits_{{i \in  \left\lbrack  {1\ldots m}\right\rbrack  }}\left( {{z}_{i} \cdot  \mathop{\prod }\limits_{{j > i}}\widetilde{\operatorname{cn}}\left\lbrack  {\mathcal{T},{e}_{j},{t}_{j}}\right\rbrack  }\right)$的$\left( {{z}_{1},{z}_{2},\cdots ,{z}_{m}}\right)  \in  { \times  }_{i = 1}^{m}$ c $\widetilde{\operatorname{cn}}\left\lbrack  {\mathcal{T},{e}_{i},{t}_{i}}\right\rbrack$；

	foreach $i \in  \left\lbrack  {1\ldots m}\right\rbrack$ do

	对每个$i \in  \left\lbrack  {1\ldots m}\right\rbrack$执行

		${t}_{i}^{\prime } \leftarrow  \operatorname{RETRIEVE}\left( {\mathcal{T},{e}_{i},{t}_{i},{z}_{i}}\right) ;$

		if ${t}_{i}^{\prime } =  \bot$ then return $\bot$ ;

		若${t}_{i}^{\prime } =  \bot$则返回$\bot$；

	return $\left( {{t}_{1}^{\prime },{t}_{2}^{\prime },\cdots ,{t}_{m}^{\prime }}\right)$ ;

	返回$\left( {{t}_{1}^{\prime },{t}_{2}^{\prime },\cdots ,{t}_{m}^{\prime }}\right)$；

else

否则

	if $z \geq  \operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$ then return $\bot$ ;

	如果$z \geq  \operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$则返回$\bot$；

	Find $i$ such that $\mathop{\sum }\limits_{{{i}^{\prime } \leq  i - 1}}{\varphi }_{{i}^{\prime },e}\left( t\right)  < z + 1 \leq  \mathop{\sum }\limits_{{{i}^{\prime } \leq  i}}{\varphi }_{{i}^{\prime },e}\left( t\right)$ ;

	寻找满足$\mathop{\sum }\limits_{{{i}^{\prime } \leq  i - 1}}{\varphi }_{{i}^{\prime },e}\left( t\right)  < z + 1 \leq  \mathop{\sum }\limits_{{{i}^{\prime } \leq  i}}{\varphi }_{{i}^{\prime },e}\left( t\right)$的$i$；

	$j \leftarrow  \left\lfloor  {\left( {z - \mathop{\sum }\limits_{{{i}^{\prime } \leq  i - 1}}{\varphi }_{{i}^{\prime },e}\left( t\right) }\right) /{2}^{i}}\right\rfloor  ;$

	$\ell  \leftarrow  z - \mathop{\sum }\limits_{{{i}^{\prime } \leq  i - 1}}{\varphi }_{{i}^{\prime },e}\left( t\right)  - {2}^{i} \cdot  j$

	${t}^{\prime } \leftarrow$ the element at position $j$ in ${\Phi }_{i,e}\left( t\right)$ ;

	在${\Phi }_{i,e}\left( t\right)$中获取位置$j$处的元素${t}^{\prime } \leftarrow$；

	return $\operatorname{RETRIEVE}\left( {\mathcal{T},e,{t}^{\prime },\ell }\right)$ ;

	返回$\operatorname{RETRIEVE}\left( {\mathcal{T},e,{t}^{\prime },\ell }\right)$；

---

<!-- Media -->

This holds trivially for Case 1. For Case 2,we assume that the batch generated for tuple ${\pi }_{\operatorname{key}\left( {e}_{i}\right) }t$ is ${\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{{e}_{i}}\right|  - 1}$ -dense. Implied by Lemma 3.6,this ${\Delta J}$ is ${\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{e}\right|  - 2}$ -dense,since

情况1显然成立。对于情况2，我们假设为元组${\pi }_{\operatorname{key}\left( {e}_{i}\right) }t$生成的批次是${\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{{e}_{i}}\right|  - 1}$-稠密的。根据引理3.6，该${\Delta J}$具有${\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{e}\right|  - 2}$-稠密性，因为

$$
{\left( \frac{1}{2}\right) }^{\left| {\mathcal{C}}_{e}\right|  - 1} \cdot  \mathop{\prod }\limits_{{{e}_{i} \in  {\mathcal{C}}_{e}}}{\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{{e}_{i}}\right|  - 1} = {\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{e}\right|  - 3} \geq  {\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{e}\right|  - 2}
$$

For Case 3,we assume the batch generated for each tuple ${t}^{\prime } \in  {R}_{e} \ltimes  t$ is ${\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{e}\right|  - 2}$ -dense. Their concatenation is also ${\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{e}\right|  - 2}$ -dense,implied by Lemma 3.5. The ${\Delta J}$ is ${\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{e}\right|  - 1}$ -dense,since $\operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   \geq  \frac{1}{2} \cdot  \operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$ and then we invoke Lemma 3.7.

对于情况3，我们假设为每个元组${t}^{\prime } \in  {R}_{e} \ltimes  t$生成的批次都是${\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{e}\right|  - 2}$-稠密的。根据引理3.5，它们的级联结果同样具有${\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{e}\right|  - 2}$-稠密性。该${\Delta J}$具有${\left( \frac{1}{2}\right) }^{2 \cdot  \left| {\mathcal{T}}_{e}\right|  - 1}$-稠密性，因为$\operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack   \geq  \frac{1}{2} \cdot  \operatorname{cnt}\left\lbrack  {\mathcal{T},e,t}\right\rbrack$，此时我们调用引理3.7。

Retrieve. Finally,we describe how to retrieve the join result at position $z$ in the batch generated by BATCHGENERATE. As described in Algorithm 7, RETRIEVE follows the same recursive structure as that of BATCHGENERATE. If tuple $t$ is inserted into ${R}_{e}$ ,the first call is RETRIEVE(T,e,t,z),where $\mathcal{T}$ is join tree rooted at node $e$ . In each recursive call,we also distinguish three cases:

检索。最后描述如何从BATCHGENERATE生成批次中获取位置$z$的连接结果。如算法7所述，RETRIEVE与BATCHGENERATE采用相同的递归结构。若将元组$t$插入${R}_{e}$，首次调用为RETRIEVE(T,e,t,z)，其中$\mathcal{T}$是以节点$e$为根的连接树。每次递归调用需区分三种情况：

- Case 1: $e$ is a leaf node. We simply return the element at position $z$ in the batch accordingly. This takes $O\left( 1\right)$ time.

- 情况1：$e$是叶节点。直接返回批次中位置$z$对应的元素，耗时$O\left( 1\right)$。

- Case 2: $e$ is an internal node and $t \in  {R}_{e}$ . In this case,we decompose the index $z$ into a $m$ -coordinate $\left( {{z}_{1},{z}_{2},\cdots ,{z}_{m}}\right)$ as defined in line 8,then retrieve the element at position ${z}_{i}$ in the batch generated for tuple ${\pi }_{\operatorname{key}\left( {e}_{i}\right) }t$ for each ${e}_{i} \in  {\mathcal{C}}_{e}$ recursively (line 9),and return their combinations as the final result (line 12). The value of ${z}_{1},{z}_{2},\cdots ,{z}_{m}$ can be computed in $O\left( 1\right)$ time.

- 情况2：$e$是内部节点且$t \in  {R}_{e}$。此时将索引$z$分解为第8行定义的$m$坐标$\left( {{z}_{1},{z}_{2},\cdots ,{z}_{m}}\right)$，递归获取每个${e}_{i} \in  {\mathcal{C}}_{e}$对应元组${\pi }_{\operatorname{key}\left( {e}_{i}\right) }t$生成批次中位置${z}_{i}$的元素（第9行），最终返回组合结果（第12行）。${z}_{1},{z}_{2},\cdots ,{z}_{m}$的值可在$O\left( 1\right)$时间内计算得出。

- Case 3: $e$ is an internal node and $t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$ . In this case,we first locate the bucket into which the element at position $z$ falls,say $i$ . We then locate the index of the tuple whose batch contains the element at position $z$ ,say $j$ ,and find the specific tuple ${t}^{\prime }$ . We also need to compute the index of the target element in the batch generated for ${t}^{\prime }$ ,say $\ell$ . Finally,the element at position $z$ in the batch generated for $t$ can be found by RETRIEVE $\left( {\mathcal{T},e,{t}^{\prime },\ell }\right)$ . The value of $i,j,\ell$ can be computed in $O\left( {\log N}\right)$ time.

- 情况3：$e$为内部节点且$t \in  {\pi }_{\operatorname{key}\left( e\right) }{R}_{e}$。此时，我们首先定位包含位置$z$元素的桶，记为$i$。接着找到批次包含该元素的元组索引，记为$j$，并定位具体元组${t}^{\prime }$。还需计算该元素在为${t}^{\prime }$生成的批次中的索引，记为$\ell$。最终，通过RETRIEVE $\left( {\mathcal{T},e,{t}^{\prime },\ell }\right)$可获取为$t$生成的批次中位置$z$的元素。$i,j,\ell$的值可在$O\left( {\log N}\right)$时间内计算得出。

It is not hard to see that the retrieve operation takes $O\left( {\log N}\right)$ time,by summing the time cost for each recursive invocation.

显然，通过累加每次递归调用的耗时，检索操作的时间复杂度为$O\left( {\log N}\right)$。

### 4.4 Optimizations

### 4.4 优化策略

We next discuss some optimization techniques for our algorithm. Although they do not improve the complexity results, they significantly reduce the constant factor, as verified in Section 6. More details are given in the full version [2].

接下来我们将探讨算法的若干优化技术。虽然这些优化不会改变理论复杂度，但如第6节验证所示，它们能显著降低常数因子。完整版本[2]提供了更多细节。

Grouping. In a join tree $\mathcal{T}$ ,for an non-root internal node ${R}_{e}$ with its children nodes $\left\{  {{e}_{1},{e}_{2},\cdots ,{e}_{m}}\right\}$ , let $\bar{e} = \operatorname{key}\left( e\right)  \cup  \operatorname{key}\left( {e}_{1}\right)  \cup  \cdots  \cup  \operatorname{key}\left( {e}_{m}\right)$ denote the join attributes. We can group tuples in ${R}_{e}$ by attributes $\bar{e}$ ,if $e - \bar{e} \neq  \varnothing$ . Let ${R}_{\bar{e}} = {\pi }_{\bar{e}}{R}_{e}$ . We replace $e$ with $\bar{e}$ in $\mathcal{T}$ . For each tuple $t \in  {R}_{\bar{e}}$ ,we maintain feq $\left\lbrack  {\mathcal{T},\bar{e},t}\right\rbrack   = \left| {{R}_{e} \ltimes  t}\right|$ and $\operatorname{feq}\left\lbrack  {\mathcal{T},\bar{e},t}\right\rbrack   = {2}^{\left\lceil  {\log }_{2}\operatorname{feq}\left\lbrack  \mathcal{T},\bar{e},t\right\rbrack  \right\rceil  }$ . Then,cnt $\left\lbrack  {\mathcal{T},\bar{e},t}\right\rbrack$ is defined as:

分组策略。在连接树$\mathcal{T}$中，对于非根内部节点${R}_{e}$及其子节点$\left\{  {{e}_{1},{e}_{2},\cdots ,{e}_{m}}\right\}$，设$\bar{e} = \operatorname{key}\left( e\right)  \cup  \operatorname{key}\left( {e}_{1}\right)  \cup  \cdots  \cup  \operatorname{key}\left( {e}_{m}\right)$表示连接属性。当$e - \bar{e} \neq  \varnothing$时，可按属性$\bar{e}$对${R}_{e}$中的元组进行分组。令${R}_{\bar{e}} = {\pi }_{\bar{e}}{R}_{e}$。我们在$\mathcal{T}$中用$\bar{e}$替换$e$。对于每个元组$t \in  {R}_{\bar{e}}$，维护feq $\left\lbrack  {\mathcal{T},\bar{e},t}\right\rbrack   = \left| {{R}_{e} \ltimes  t}\right|$和$\operatorname{feq}\left\lbrack  {\mathcal{T},\bar{e},t}\right\rbrack   = {2}^{\left\lceil  {\log }_{2}\operatorname{feq}\left\lbrack  \mathcal{T},\bar{e},t\right\rbrack  \right\rceil  }$。此时cnt $\left\lbrack  {\mathcal{T},\bar{e},t}\right\rbrack$定义为：

$$
\operatorname{cnt}\left\lbrack  {\mathcal{T},\bar{e},t}\right\rbrack   = \mathop{\sum }\limits_{{{t}^{\prime } \in  {R}_{\bar{e}} \ltimes  t}}\widetilde{\operatorname{feq}}\left\lbrack  {\mathcal{T},\bar{e},{t}^{\prime }}\right\rbrack   \cdot  \mathop{\prod }\limits_{{{e}_{i} \in  {C}_{\bar{e}}}}\widetilde{\operatorname{cnt}}\left\lbrack  {\mathcal{T},{e}_{i},{\pi }_{\operatorname{key}\left( {e}_{i}\right) }{t}^{\prime }}\right\rbrack  
$$

Grouping can bring much benefit in index update. More specifically, in line 9-11 of Algorithm 5, instead of propagating update for every tuple ${t}^{\prime } \in  {R}_{{p}_{e}} \ltimes  {t}_{e}$ ,we now propagate update only for every tuple $t \in  {R}_{\overline{{p}_{e}}} \ltimes  {t}_{e}$ ,where ${R}_{\overline{{p}_{e}}}$ is the projection of relation ${R}_{{p}_{e}}$ onto join attributes in ${p}_{e}$ . Hence, we can see a significant reduction in the number of propagated updates.

分组能极大优化索引更新。具体而言，在算法5的第9-11行中，原本需要对每个元组${t}^{\prime } \in  {R}_{{p}_{e}} \ltimes  {t}_{e}$传播更新，现在只需为每个$t \in  {R}_{\overline{{p}_{e}}} \ltimes  {t}_{e}$元组（即关系${R}_{{p}_{e}}$在${p}_{e}$连接属性上的投影）传播更新，从而显著减少传播更新次数。

Foreign-keys. When foreign-key join exists, similar to [31], we simply combine the corresponding sub-join as a whole relation. More specifically,for ${R}_{i}{ \boxtimes  }_{X}{R}_{j}$ ,where $X$ is the primary key of ${R}_{j}$ ,we combine ${R}_{i},{R}_{j}$ together as a new relation ${R}_{ij} = {R}_{i} \boxtimes  {R}_{j}$ . This combination can be recursively done until no more foreign-key join exists. When a tuple ${t}_{i}$ is inserted into ${R}_{i}$ ,we check if there exists a matching tuple ${t}_{j} \in  {R}_{j}$ with the value ${\pi }_{X}{t}_{i}$ . If ${t}_{j}$ exists,we insert ${t}_{ij} = {t}_{i} \boxtimes  {t}_{j}$ into ${R}_{ij}$ . However, when a tuple ${t}_{j}$ is inserted into ${R}_{j}$ ,we need to identify all tuples in ${R}_{i}$ that can joined with ${t}_{j}$ ,and insert ${t}_{ij} = {t}_{i} \boxtimes  {t}_{j}$ into ${R}_{ij}$ .

外键。当存在外键连接时，类似于[31]的做法，我们只需将对应的子连接组合为一个完整关系。具体而言，对于${R}_{i}{ \boxtimes  }_{X}{R}_{j}$，其中$X$是${R}_{j}$的主键，我们将${R}_{i},{R}_{j}$合并为一个新关系${R}_{ij} = {R}_{i} \boxtimes  {R}_{j}$。这种组合可递归执行直至不存在外键连接。当元组${t}_{i}$被插入${R}_{i}$时，我们检查是否存在具有值${\pi }_{X}{t}_{i}$的匹配元组${t}_{j} \in  {R}_{j}$。若${t}_{j}$存在，则将${t}_{ij} = {t}_{i} \boxtimes  {t}_{j}$插入${R}_{ij}$。但当元组${t}_{j}$被插入${R}_{j}$时，需识别${R}_{i}$中所有可与之连接的元组，并将${t}_{ij} = {t}_{i} \boxtimes  {t}_{j}$插入${R}_{ij}$。

## 5 EXTENSION TO CYCLIC JOINS

## 5 循环连接的扩展

We next show how to handle cyclic joins using our algorithm in Section 4, by resorting to the classic GHD decomposition framework [17]. It has been shown [7] that for a join query $\mathcal{Q} = \left( {\mathcal{V},\mathcal{E}}\right)$ and any instance of input size $N$ ,the maximum join size is $\Theta \left( {N}^{{\rho }^{ * }\left( \mathcal{Q}\right) }\right)$ ,where ${\rho }^{ * }\left( \mathcal{Q}\right)$ is the fractional edge covering number of $Q$ . Please see an example in Figure 3.

接下来我们将展示如何通过经典GHD分解框架[17]，运用第4节的算法处理循环连接。研究[7]表明，对于连接查询$\mathcal{Q} = \left( {\mathcal{V},\mathcal{E}}\right)$和任意输入规模$N$的实例，最大连接规模为$\Theta \left( {N}^{{\rho }^{ * }\left( \mathcal{Q}\right) }\right)$，其中${\rho }^{ * }\left( \mathcal{Q}\right)$是$Q$的分数边覆盖数。示例见图3。

Definition 5.1 (Fractional Edge Covering Number). Given a join query $\mathcal{Q} = \left( {\mathcal{V},\mathcal{E}}\right)$ ,a fractional edge covering is a function $W : \mathcal{E} \rightarrow  \left\lbrack  {0,1}\right\rbrack$ such that $\mathop{\sum }\limits_{{e \in  \mathcal{E} : x \in  e}}W\left( e\right)  \geq  1$ for every attribute $x \in  \mathcal{V}$ . The fractional edge covering number $\rho \left( \mathbf{Q}\right)$ is defined as minimum value of $\mathop{\sum }\limits_{{e \in  \mathcal{E}}}W\left( e\right)$ over all possible fractional edge coverings $W$ .

定义5.1（分数边覆盖数）。给定连接查询$\mathcal{Q} = \left( {\mathcal{V},\mathcal{E}}\right)$，分数边覆盖是指满足对每个属性$x \in  \mathcal{V}$都有$\mathop{\sum }\limits_{{e \in  \mathcal{E} : x \in  e}}W\left( e\right)  \geq  1$的函数$W : \mathcal{E} \rightarrow  \left\lbrack  {0,1}\right\rbrack$。分数边覆盖数$\rho \left( \mathbf{Q}\right)$定义为所有可能分数边覆盖$W$中$\mathop{\sum }\limits_{{e \in  \mathcal{E}}}W\left( e\right)$的最小值。

Definition 5.2 (Generalized Hypertree Decomposition). Given a join query $Q = \left( {\mathcal{V},\mathcal{E}}\right)$ ,a GHD of $\mathcal{Q}$ is a pair $\left( {\mathcal{T},\lambda }\right)$ ,where $\mathcal{T}$ is a tree as an ordered set of nodes and $\lambda  : \mathcal{T} \rightarrow  {2}^{\mathcal{V}}$ is a labeling

定义5.2（广义超树分解）。给定连接查询$Q = \left( {\mathcal{V},\mathcal{E}}\right)$，$\mathcal{Q}$的GHD是一个二元组$\left( {\mathcal{T},\lambda }\right)$，其中$\mathcal{T}$是作为有序节点集的树，$\lambda  : \mathcal{T} \rightarrow  {2}^{\mathcal{V}}$是标注函数

<!-- Media -->

<!-- figureText: $\bullet  {x}_{1}$ ${x}_{5} \bullet$ ${R}_{5}$ ${R}_{7}$ $\bullet  {x}_{4}$ ${R}_{4}$ ${R}_{6}$ ${x}_{6} \oplus$ ${R}_{2}$ ${R}_{3}$ $\bullet  {x}_{2}$ -->

<img src="https://cdn.noedgeai.com/0196599d-1a1e-711f-9862-e3c27b652aff_18.jpg?x=557&y=262&w=446&h=195&r=0"/>

Fig. 3. The dumbbell join $Q = {R}_{1}\left( {{x}_{1},{x}_{2}}\right)  \boxtimes  {R}_{2}\left( {{x}_{1},{x}_{3}}\right)  \boxtimes  {R}_{3}\left( {{x}_{2},{x}_{3}}\right)  \boxtimes  {R}_{4}\left( {{x}_{5},{x}_{6}}\right)  \boxtimes  {R}_{5}\left( {{x}_{4},{x}_{5}}\right)  \boxtimes  {R}_{6}\left( {{x}_{4},{x}_{6}}\right)  \boxtimes$ ${R}_{7}\left( {{x}_{3},{x}_{4}}\right)$ with GHD illustrated as the red circle. It has fractional hypertree width $\mathrm{w}\left( Q\right)  = {1.5}$ since the triangle join ${R}_{1}\left( {{x}_{1},{x}_{2}}\right)  \boxtimes  {R}_{2}\left( {{x}_{1},{x}_{3}}\right)  \boxtimes  {R}_{3}\left( {{x}_{2},{x}_{3}}\right)$ and ${R}_{5}\left( {{x}_{4},{x}_{5}}\right)  \boxtimes  {R}_{6}\left( {{x}_{4},{x}_{6}}\right)  \boxtimes  {R}_{7}\left( {{x}_{3},{x}_{4}}\right)$ have the fractional edge covering number ${\rho }^{ * } = {1.5}$ .

图3. 哑铃连接$Q = {R}_{1}\left( {{x}_{1},{x}_{2}}\right)  \boxtimes  {R}_{2}\left( {{x}_{1},{x}_{3}}\right)  \boxtimes  {R}_{3}\left( {{x}_{2},{x}_{3}}\right)  \boxtimes  {R}_{4}\left( {{x}_{5},{x}_{6}}\right)  \boxtimes  {R}_{5}\left( {{x}_{4},{x}_{5}}\right)  \boxtimes  {R}_{6}\left( {{x}_{4},{x}_{6}}\right)  \boxtimes$${R}_{7}\left( {{x}_{3},{x}_{4}}\right)$，其中广义超树分解(GHD)以红色圆圈表示。由于三角形连接${R}_{1}\left( {{x}_{1},{x}_{2}}\right)  \boxtimes  {R}_{2}\left( {{x}_{1},{x}_{3}}\right)  \boxtimes  {R}_{3}\left( {{x}_{2},{x}_{3}}\right)$和${R}_{5}\left( {{x}_{4},{x}_{5}}\right)  \boxtimes  {R}_{6}\left( {{x}_{4},{x}_{6}}\right)  \boxtimes  {R}_{7}\left( {{x}_{3},{x}_{4}}\right)$的分数边覆盖数为${\rho }^{ * } = {1.5}$，该结构具有分数超树宽度$\mathrm{w}\left( Q\right)  = {1.5}$。

<!-- Media -->

function,which associates to each vertex $u \in  \mathcal{T}$ a subset of attributes in $\mathcal{V}$ ,such that the following conditions are satisfied:

函数，为每个顶点$u \in  \mathcal{T}$关联$\mathcal{V}$中的属性子集，需满足以下条件：

- For each $e \in  \mathcal{E}$ ,there is a node $u \in  \mathcal{T}$ such that $e \subseteq  \lambda \left( u\right)$ ;

- 对于每个$e \in  \mathcal{E}$，存在节点$u \in  \mathcal{T}$使得$e \subseteq  \lambda \left( u\right)$；

- For each $x \in  \mathcal{V}$ ,the set of nodes $\{ u \in  \mathcal{T} : x \in  \lambda \left( u\right) \}$ forms a connected subtree of $\mathcal{T}$ .

- 对于每个$x \in  \mathcal{V}$，节点集$\{ u \in  \mathcal{T} : x \in  \lambda \left( u\right) \}$构成$\mathcal{T}$的连通子树。

Given a join query $\mathcal{Q} = \left( {\mathcal{V},\mathcal{E}}\right)$ ,a GHD $\left( {\mathcal{T},\lambda }\right)$ and a node $u \in  \mathcal{T}$ ,the width of $\mathcal{T}$ is defined as the optimal fractional edge covering number of its derived subquery ${\mathcal{Q}}_{u} = \left( {{\lambda }_{u},{\mathcal{E}}_{u}}\right)$ ,where ${\mathcal{E}}_{u} = \left\{  {e \cap  {\lambda }_{u} : e \in  \mathcal{E}}\right\}$ . Given a join query and a GHD $\left( {\mathcal{T},\lambda }\right)$ ,the width of $\left( {\mathcal{T},\lambda }\right)$ is defined as the maximum width over all nodes in $\mathcal{T}$ . Then,the fractional hypertree width of a join query follows:

给定连接查询$\mathcal{Q} = \left( {\mathcal{V},\mathcal{E}}\right)$、广义超树分解(GHD)$\left( {\mathcal{T},\lambda }\right)$和节点$u \in  \mathcal{T}$，其宽度定义为派生子查询${\mathcal{Q}}_{u} = \left( {{\lambda }_{u},{\mathcal{E}}_{u}}\right)$的最优分数边覆盖数（其中${\mathcal{E}}_{u} = \left\{  {e \cap  {\lambda }_{u} : e \in  \mathcal{E}}\right\}$）。对于给定连接查询和GHD$\left( {\mathcal{T},\lambda }\right)$，其宽度取$\mathcal{T}$中所有节点宽度的最大值。因此，连接查询的分数超树宽度定义如下：

Definition 5.3 (Fractional Hypertree Width [17]). . The fractional hypertree width of a join query $\mathcal{Q}$ ,denoted as $\mathrm{w}\left( \mathcal{Q}\right)$ ,is $\mathrm{w}\left( \mathcal{Q}\right)  = \mathop{\min }\limits_{\left( \mathcal{T},\lambda \right) }\mathop{\max }\limits_{{u \in  \mathcal{T}}}{\rho }^{ * }\left( {\mathcal{Q}}_{u}\right)$ ,i.e.,the minimum width over all GHDs.

定义5.3（分数超树宽度[17]）。连接查询$\mathcal{Q}$的分数超树宽度记作$\mathrm{w}\left( \mathcal{Q}\right)$，其值为$\mathrm{w}\left( \mathcal{Q}\right)  = \mathop{\min }\limits_{\left( \mathcal{T},\lambda \right) }\mathop{\max }\limits_{{u \in  \mathcal{T}}}{\rho }^{ * }\left( {\mathcal{Q}}_{u}\right)$，即所有GHD中的最小宽度。

Our algorithm builds upon a GHD $\left( {\mathcal{T},\lambda }\right)$ for the input join query $\mathcal{Q}$ ,and considers every version of $\mathcal{T}$ by rooting $\mathcal{T}$ at one distinct node. Given an instance $\mathcal{R}$ ,we define the sub-instance for each node $u \in  \mathcal{T}$ as ${\mathcal{R}}_{u} = \left\{  {{\pi }_{e \cap  {\lambda }_{u}}{R}_{e} : e \in  \mathcal{E},e \cap  {\lambda }_{u} \neq  \varnothing }\right\}$ . Suppose a tuple $t$ is inserted into relation ${R}_{e}$ . For each node $u \in  \mathcal{T}$ with $e \cap  {\lambda }_{u} \neq  \varnothing$ ,we add tuple ${\pi }_{e \cap  {\lambda }_{u}}t$ to relation ${\pi }_{e \cap  {\lambda }_{u}}{R}_{e}$ in instance ${\mathcal{R}}_{u}$ and update the results of ${\mathcal{Q}}_{u}$ . We pick an arbitrary node $u \in  \mathcal{T}$ with $e \subseteq  {\lambda }_{u}$ . Let ${\Delta }_{u} = {\mathcal{Q}}_{u}\left( {\mathcal{R}}_{u}\right)  \ltimes  t$ be the delta join results of $t$ . For every tuple ${t}^{\prime } \in  {\Delta }_{u}$ ,we just execute line 5-7 in Algorithm 4.

我们的算法基于输入连接查询$\mathcal{Q}$的广义超树分解(GHD)$\left( {\mathcal{T},\lambda }\right)$，通过将$\mathcal{T}$的每个版本以不同节点为根进行考虑。给定实例$\mathcal{R}$，我们将每个节点$u \in  \mathcal{T}$的子实例定义为${\mathcal{R}}_{u} = \left\{  {{\pi }_{e \cap  {\lambda }_{u}}{R}_{e} : e \in  \mathcal{E},e \cap  {\lambda }_{u} \neq  \varnothing }\right\}$。假设元组$t$被插入到关系${R}_{e}$中。对于每个满足$e \cap  {\lambda }_{u} \neq  \varnothing$的节点$u \in  \mathcal{T}$，我们将元组${\pi }_{e \cap  {\lambda }_{u}}t$添加到实例${\mathcal{R}}_{u}$的关系${\pi }_{e \cap  {\lambda }_{u}}{R}_{e}$中，并更新${\mathcal{Q}}_{u}$的结果。我们任意选取一个满足$e \subseteq  {\lambda }_{u}$的节点$u \in  \mathcal{T}$。令${\Delta }_{u} = {\mathcal{Q}}_{u}\left( {\mathcal{R}}_{u}\right)  \ltimes  t$表示$t$的增量连接结果。对于每个元组${t}^{\prime } \in  {\Delta }_{u}$，我们只需执行算法4中的第5-7行。

The correctness follows the fact that $Q\left( \mathcal{R}\right)  \ltimes  t = { \uplus  }_{{t}^{\prime } \in  {\Delta }_{u}}Q\left( \mathcal{R}\right)  \ltimes  {t}^{\prime }$ ,where $\uplus$ is the disjoint union operator.

正确性源于$Q\left( \mathcal{R}\right)  \ltimes  t = { \uplus  }_{{t}^{\prime } \in  {\Delta }_{u}}Q\left( \mathcal{R}\right)  \ltimes  {t}^{\prime }$这一事实，其中$\uplus$表示不相交并集运算符。

Time Complexity. We next analyze the time complexity. For each tuple $t$ inserted into ${R}_{e}$ ,the join result of ${Q}_{u}$ for each node $u$ can be updated in $\operatorname{AGM}\left( {{Q}_{u},{\mathcal{R}}_{u} \ltimes  t}\right)$ time. Summing over all inserted tuples and nodes in $\mathcal{T}$ ,the time complexity is

时间复杂度。接下来我们分析时间复杂度。对于每个插入${R}_{e}$的元组$t$，每个节点$u$的连接结果${Q}_{u}$可在$\operatorname{AGM}\left( {{Q}_{u},{\mathcal{R}}_{u} \ltimes  t}\right)$时间内更新。综合$\mathcal{T}$中所有插入的元组和节点，时间复杂度为

$$
\mathop{\sum }\limits_{{u \in  \mathcal{T}}}\mathop{\sum }\limits_{{e \in  \mathcal{E}}}\mathop{\sum }\limits_{{t \in  {R}_{e}}}\operatorname{AGM}\left( {{\mathcal{Q}}_{u},{\mathcal{R}}_{u} \ltimes  t}\right)  \leq  \operatorname{AGM}\left( {{\mathcal{Q}}_{u},{\mathcal{R}}_{u}}\right)  = O\left( {N}^{\mathrm{w}\left( \mathcal{Q}\right) }\right) .
$$

Moreover,we can also bound the size of ${\Delta }_{u}\left( t\right)$ by $\operatorname{AGM}\left( {{Q}_{u},{\mathcal{R}}_{u} \ltimes  t}\right)$ . Hence,the size of the simulated input stream over the GHD $\left( {\mathcal{T},\lambda }\right)$ is bounded by $\mathop{\sum }\limits_{{e \in  \mathcal{E}}}\mathop{\sum }\limits_{{t \in  {R}_{e}}}\operatorname{AGM}\left( {{\mathcal{Q}}_{u},{\mathcal{R}}_{u} \ltimes  t}\right)  = O\left( {N}^{\mathrm{w}\left( \mathcal{Q}\right) }\right)$ .

此外，我们还可以用$\operatorname{AGM}\left( {{Q}_{u},{\mathcal{R}}_{u} \ltimes  t}\right)$来界定${\Delta }_{u}\left( t\right)$的大小。因此，在GHD$\left( {\mathcal{T},\lambda }\right)$上模拟的输入流大小上限为$\mathop{\sum }\limits_{{e \in  \mathcal{E}}}\mathop{\sum }\limits_{{t \in  {R}_{e}}}\operatorname{AGM}\left( {{\mathcal{Q}}_{u},{\mathcal{R}}_{u} \ltimes  t}\right)  = O\left( {N}^{\mathrm{w}\left( \mathcal{Q}\right) }\right)$。

Space Usage. Our index for cyclic joins builds upon a GHD $\left( {\mathcal{T},\lambda }\right)$ for $Q$ . Note that the total number of input tuples inserted into each node of $\mathcal{T}$ is $O\left( {N}^{\mathrm{w}}\right)$ ,where $\mathrm{w}$ is the fractional hypertree width of $\mathcal{Q}$ . Following the same analysis of acyclic joins in Section 4,the space used by our index is proportional to the total number of tuples in each node of $\mathcal{T}$ ,i.e., $O\left( {N}^{\mathrm{w}}\right)$ for cyclic joins.

空间占用。我们针对循环连接的索引基于$Q$的广义超树分解(GHD)$\left( {\mathcal{T},\lambda }\right)$。注意插入到$\mathcal{T}$每个节点的输入元组总数为$O\left( {N}^{\mathrm{w}}\right)$，其中$\mathrm{w}$表示$\mathcal{Q}$的分数超树宽度。沿用第4节中对无环连接的分析方法，本索引占用的空间与$\mathcal{T}$各节点中的元组总数成正比，即循环连接时为$O\left( {N}^{\mathrm{w}}\right)$。

Putting everything together, we obtain:

综合以上分析，我们得到：

THEOREM 5.4. Given any join $Q$ ,an initially empty database $\mathcal{R}$ ,a sample size $k$ ,and a stream of $N$ tuples,Algorithm 4 maintains $k$ uniform samples without replacement for each $Q\left( {\mathcal{R}}^{i}\right)$ ,uses $O\left( {N}^{w}\right)$ space and runs in $O\left( {{N}^{w} \cdot  \log N + k \cdot  \log N \cdot  \log \frac{N}{k}}\right)$ expected time,where $w$ is the fractional hypertree width of $Q$ .

定理5.4 给定任意连接$Q$、初始空数据库$\mathcal{R}$、采样大小$k$以及$N$元组流，算法4能为每个$Q\left( {\mathcal{R}}^{i}\right)$维护$k$无放回均匀样本，使用$O\left( {N}^{w}\right)$存储空间并以$O\left( {{N}^{w} \cdot  \log N + k \cdot  \log N \cdot  \log \frac{N}{k}}\right)$期望时间复杂度运行，其中$w$表示$Q$的分数超树宽度。

<!-- Media -->

<!-- figureText: ${10}^{5}$ 222 RSJoin _____ RSJoin_opt Running time (sec) ${10}^{4}$ ESS Sjoin RXS Sjoin_opt ${10}^{3}$ ${10}^{2}$ ${10}^{0}$ -->

<img src="https://cdn.noedgeai.com/0196599d-1a1e-711f-9862-e3c27b652aff_19.jpg?x=196&y=265&w=1167&h=290&r=0"/>

Fig. 4. Running time over different join queries

图4. 不同连接查询的运行时间

<!-- Media -->

## 6 EXPERIMENTS

## 6 实验

### 6.1 Setup

### 6.1 实验设置

Implementation. We compare our algorithm (denoted as RSJoin) as well as the optimized version when foreign-key join exists (denoted as RSJoin_opt), with the algorithm in [31] (denoted as SJoin) and its optimized version when foreign-key join exists (denoted as SJoin_opt), which is also the state-of-the-art method for supporting random sampling over joins under updates. We mentioned that the symmetric hash join algorithm [3] was proposed for computing the (delta) join results for the basic two-table join over data streams. In [31], symmetric join was combined with reservoir sampling for supporting maintaining uniform samples over joins and also tested as a baseline solution, but its performance is overall dominated by [31], hence we do not include it in our experiments. We implement our algorithms in C++, and conduct experiments on a machine equipped with two Intel Xeon ${2.1}\mathrm{{GHz}}$ processors with 24 cores and ${251}\mathrm{{GB}}$ of memory,running CentOS 7. We repeat each experiment 10 times (with timeout as 12 hours) and report the average running time. All code is available at [1].

实现方案。我们将本算法（记为RSJoin）及其外键连接优化版本（记为RSJoin_opt），与文献[31]算法（记为SJoin）及其外键连接优化版本（记为SJoin_opt）进行对比——后者是目前支持更新条件下连接随机采样的最先进方法。需要说明的是，对称哈希连接算法[3]最初是为数据流中双表基础连接的（增量）结果计算而提出。文献[31]将对称连接与蓄水池采样结合来维护连接上的均匀样本，并作为基线方案测试，但其性能整体弱于[31]，故本实验未予纳入。我们使用C++实现算法，实验环境为配备两颗Intel Xeon${2.1}\mathrm{{GHz}}$处理器（24核）和${251}\mathrm{{GB}}$内存的CentOS 7服务器。每组实验重复10次（超时设为12小时），报告平均运行时间。完整代码见[1]。

Datasets and Queries. We evaluate algorithms on graph and relational datasets/queries. All queries in SQL can be found in the version [2].

数据集与查询。我们在图数据和关系数据/查询上评估算法。所有SQL查询语句详见版本[2]。

We use the Epinions dataset that contains 508,837 edges from SNAP (Stanford Network Analysis Project) [23] as the graph dataset. Each relation contains all edges. We randomly shuffle all edges for each relation to simulate the input stream. On Epinions,we evaluate line- $k$ joins (which find paths in the graph of length $k$ ),star- $k$ joins (which find all combinations of $k$ edges sharing a common vertex), and dumbbell join (which finds all pairs of triangle that is connected by an edge). There is no foreign-key join in graph queries.

图数据集采用斯坦福网络分析项目SNAP[23]的Epinions数据集（含508,837条边）。每个关系包含所有边。我们对各关系边随机重排以模拟输入流。在Epinions上评估：线型$k$连接（查找长度为$k$的路径）、星型$k$连接（查找共享顶点的$k$边组合）以及哑铃型连接（查找通过边相连的三角形对）。图查询中不存在外键连接。

We use two relational datasets. One is TPC-DS dataset [4], which models several generally applicable aspects of a decision support system. We evaluate the same QX, QY, and QZ queries as [31] on TPC-DS, which include the foreign-key joins, and follow the same setup as [31], such that small dimension tables (such as date_dim and household_demographics are pre-loaded, while the rest of the tables are loaded in a streaming fashion. The other is LDBC Social Network Benchmark (LDBC-SNB) [26], which focuses on join-heavy complex queries with updates. We tested Q10 query from the Business Intelligence (BI) workload 10. Similar as before, the static tables (such as tag and city) are pre-loaded, and the dynamic tables are loaded in a streaming fashion.

关系数据集采用两种：TPC-DS数据集[4]（建模决策支持系统的通用场景）和LDBC社交网络基准(LDBC-SNB)[26]（侧重含更新的复杂多连接查询）。TPC-DS上沿用文献[31]的QX、QY、QZ查询（含外键连接），实验设置同[31]——小维度表（如date_dim、household_demographics）预加载，其余表流式加载。LDBC-SNB测试其商业智能(BI)工作负载中的Q10查询，静态表（如tag、city）预加载，动态表流式加载。

### 6.2 Experiment Results

### 6.2 实验结果

Running time. Figure 4 shows the running time of all algorithms on tested queries. For graph queries (i.e.,line- $k$ ,star- $k$ ,and dumbbell),the sample size is 100,000 . For relational queries (i.e., QX, QY, QZ, and Q10), the sample size is 1,000,000. For the TPC-DS dataset, we use a scale factor of 10 , while for the LDBC-SNB dataset, we use a scale factor of 1. Firstly, RSJoin and RSJoin_opt can finish all queries within 12-hour time limit while SJoin cannot finish on the line-5 join and the QZ join. For the dumbbell join, the result is missing for SJoin since it does not support cyclic queries. Secondly, RSJoin is always the fastest over all join queries. Based on existing results, RSJoin achieves a speedup ranging from 4.6x to 147.6x over SJoin, not mention the case when SJoin cannot finish in time. When foreign-key join exists (i.e., QX, QY, QZ, and Q10), RSJoin_opt achieves an improvement of 2.2x to 4.7x over SJoin_opt. Furthermore, for QX, QY, QZ, and Q10, RSJoin does not heavily rely on foreign-key optimizations as SJoin. As long as data satisfies foreign-key constraints, RSJoin finishes the execution within a reasonable amount of time, but this is not the case for SJoin.

运行时间。图4展示了所有算法在测试查询上的运行时间。对于图查询（即线型-$k$、星型-$k$和哑铃型连接），采样规模为100,000；对于关系型查询（即QX、QY、QZ和Q10），采样规模为1,000,000。TPC-DS数据集采用10的比例因子，而LDBC-SNB数据集采用1的比例因子。首先，RSJoin和RSJoin_opt能在12小时时限内完成所有查询，而SJoin无法完成line-5连接和QZ连接。对于哑铃型连接，由于SJoin不支持循环查询，其结果为缺失状态。其次，在所有连接查询中RSJoin始终是最快的。现有结果表明，RSJoin相比SJoin实现了4.6倍至147.6倍的加速，更不用说SJoin无法按时完成的情况。当存在外键连接时（即QX、QY、QZ和Q10），RSJoin_opt比SJoin_opt有2.2倍至4.7倍的性能提升。此外，对于QX、QY、QZ和Q10查询，RSJoin不像SJoin那样严重依赖外键优化。只要数据满足外键约束，RSJoin就能在合理时间内完成执行，但SJoin则无法做到。

<!-- Media -->

<!-- figureText: 2222 RSJoin 60 5e9 RSJoin Running time (sec) 50 Sjoin 4e9 Join size 3e9 30 2e9 20 le9 10% 20% 30% 40% 50% 60% 70% 80% 100% Progress Fig. 6. Running time v.s. input size and join size 100000 CON Sjoin 80000 60000 40000 20000 ${10}^{3}$ ${10}^{4}$ ${10}^{5}$ ${10}^{6}$ ${10}^{7}$ ${10}^{8}$ ${10}^{9}$ Update time (ns) Fig. 5. Update time distribution -->

<img src="https://cdn.noedgeai.com/0196599d-1a1e-711f-9862-e3c27b652aff_20.jpg?x=156&y=264&w=1228&h=349&r=0"/>

<!-- figureText: 200 20 50 100 200 500 Sample size (1e4) RSJoin Running time (sec) 150 SJoin Input size 100 50 0 1 10 -->

<img src="https://cdn.noedgeai.com/0196599d-1a1e-711f-9862-e3c27b652aff_20.jpg?x=160&y=667&w=589&h=298&r=0"/>

Fig. 7. Running time v.s. sample size

图7. 运行时间与采样规模关系

<table><tr><td>Optimizations</td><td>#Execution</td><td>Run-time (sec)</td></tr><tr><td>N/A</td><td>172010370</td><td>678.864</td></tr><tr><td>Foreign-key</td><td>132175648</td><td>204.614</td></tr><tr><td>Foreign-key + Grouping</td><td>597557</td><td>68.047</td></tr></table>

<table><tbody><tr><td>优化</td><td>#执行</td><td>运行时间（秒）</td></tr><tr><td>不适用</td><td>172010370</td><td>678.864</td></tr><tr><td>外键</td><td>132175648</td><td>204.614</td></tr><tr><td>外键 + 分组</td><td>597557</td><td>68.047</td></tr></tbody></table>

Fig. 8. Optimizations on QZ over TPC-DS dataset

图8. QZ算法在TPC-DS数据集上的优化效果

<!-- Media -->

Update time. To compare the update time, we disable the sampling part of both algorithms and measure the update time required for each input tuple. Figure 5 shows the result on line-4 join. Most of the update time required is roughly ${10\mu }\mathrm{s}$ ,with an average of ${13\mu }\mathrm{s}$ . Some tuple may incur much larger update time( ${51}\mathrm{\;{ms}}$ in this case),but the overall update time remains small,which aligns with our theoretical analysis of $O\left( {\log N}\right)$ amortized update time. In contrast,there is no guarantee on the update time for SJoin,and its update time ranges from ${0.5\mu }\mathrm{s}$ to ${165}\mathrm{\;{ms}}$ ,with an average of ${1.4}\mathrm{{ms}}$ .

更新时间对比。为比较更新时间，我们禁用两种算法的采样模块，测量每个输入元组所需的更新时间。图5展示了line-4连接的结果。大部分更新时间约为${10\mu }\mathrm{s}$，平均值为${13\mu }\mathrm{s}$。个别元组可能产生较大更新时间(本例中达${51}\mathrm{\;{ms}}$)，但总体更新时间仍保持较低水平，这与我们$O\left( {\log N}\right)$摊还更新时间的理论分析一致。相比之下，SJoin的更新时间无保障，其波动范围从${0.5\mu }\mathrm{s}$至${165}\mathrm{\;{ms}}$，平均值为${1.4}\mathrm{{ms}}$。

Input size and Join size. We next investigate how the input size $N$ as well as the join size (i.e., the number of join results) affect the total execution time of all methods. We fix the sample size $k$ to be 10,000 and record the total execution after every 10% of input data is processed for line-3 join. Figure 6 shows the progress of total number of join results generated and the total execution time. We can see that the total number of join results grows exponentially with the input size, while the total execution time of RSJoin scales almost linearly proportional to the input size, instead of the join size. This is expected as the time complexity of RSJoin is $O\left( {N \cdot  \log N + k \cdot  \log N \cdot  \log \frac{N}{k}}\right)$ , where the term $N\log N$ almost dominates the total execution time in this case. In contrast,the total execution time of SJoin shows a clear increase trend together with the increase in the join size, which is much larger than the input size.

输入规模与连接规模。我们进一步研究输入规模$N$及连接结果量如何影响各方法的总执行时间。固定采样量$k$为10,000，在line-3连接中每处理10%输入数据后记录总耗时。图6展示了连接结果总数与总执行时间的增长过程。可见连接结果数随输入规模呈指数增长，而RSJoin的总执行时间与输入规模几乎呈线性关系，而非连接规模。这符合预期，因RSJoin的时间复杂度为$O\left( {N \cdot  \log N + k \cdot  \log N \cdot  \log \frac{N}{k}}\right)$，其中$N\log N$项在本案例中主导了总执行时间。反观SJoin，其总执行时间随连接规模的扩大呈现明显增长趋势，且远超输入规模。

<!-- Media -->

2000

RSJoin

RSJoin算法

Running time (sec) 1500 RSJoin_opt Sjoin_opt 1000 500

运行时间(秒) 1500 RSJoin优化版 Sjoin优化版 1000 500

0

10 30

Scale factor Fig. 9. Running time v.s. scale factor

比例因子 图9. 运行时间与比例因子关系

<!-- figureText: 50% 60% 70% 80% 90% 100% Progress 10000 RSJoin Line-3 Memory usage (MB) SJoin Line-3 8000 RSJoin_opt Q10 SJoin_opt Q10 6000 4000 2000 10% 20% 30% 40% -->

<img src="https://cdn.noedgeai.com/0196599d-1a1e-711f-9862-e3c27b652aff_21.jpg?x=804&y=267&w=582&h=299&r=0"/>

Fig. 10. Memory usage v.s. input size

图10. 内存使用量与输入规模关系

<!-- figureText: 350 Progress RSWP Running time (sec) RS 250 200 100 50 -->

<img src="https://cdn.noedgeai.com/0196599d-1a1e-711f-9862-e3c27b652aff_21.jpg?x=158&y=647&w=574&h=280&r=0"/>

Fig. 11. Running time v.s. input size

图11. 运行时间与输入规模关系

<!-- figureText: 400 RSWP Density Running time (sec) 300 200 -->

<img src="https://cdn.noedgeai.com/0196599d-1a1e-711f-9862-e3c27b652aff_21.jpg?x=786&y=645&w=561&h=286&r=0"/>

Fig. 12. Running time v.s. density

图12. 运行时间与密度关系

<!-- Media -->

Sample size. We next study how the sample size $k$ affect the total execution time of both algorithms. Figure 7 shows the running time on line-3 join,when $k$ varies from 10,000 to 5,000,000 . The dashed line indicates the input size $N = {508},{837}$ and the number of join results is3,721,042,797. When the sample size is smaller than the input size,i.e., $k \leq  N$ ,the total execution time of RSJoin grows very slow. More specially,when $k$ increases from 1 to 50,the total execution time of RSJoin only increases by a factor of 2. However,when the sample time overrides the input size,i.e., $k > N$ ,the total execution time of RSJoin starts to increases rapidly. This is also expected again as the theoretical complexity of RSJoin is $O\left( {N \cdot  \log N + k \cdot  \log N \cdot  \log \frac{N}{k}}\right)$ . When $k \leq  N$ ,the term $O\left( {N \cdot  \log N}\right)$ dominates the overall execution time, hence increasing the sample size within this regime does not change the total execution time significantly. When $k > N$ ,the term $O\left( {k \cdot  \log N \cdot  \log \frac{N}{k}}\right)$ dominates the overall execution time instead, hence increasing the sample size results in rapid increase in the total execution time. SJoin follows a similar trend. Moreover, when the sample size reaches $k = {10},{000}$ ,the running time required by SJoin is even more than that required by RSJoin for the case when sample size is as large as $k = 5,{000},{000}$ .

采样规模分析。我们接着研究采样量$k$对两种算法总执行时间的影响。图7展示了line-3连接中，当$k$从10,000变化到5,000,000时的运行时间。虚线表示输入规模$N = {508},{837}$，此时连接结果数为3,721,042,797。当采样量小于输入规模(即$k \leq  N$)时，RSJoin总执行时间增长极缓。具体而言，当$k$从1增至50时，RSJoin总耗时仅增加2倍。但当采样量超过输入规模(即$k > N$)后，RSJoin总耗时开始快速上升。这同样符合理论预期，因RSJoin的理论复杂度为$O\left( {N \cdot  \log N + k \cdot  \log N \cdot  \log \frac{N}{k}}\right)$。当$k \leq  N$时，$O\left( {N \cdot  \log N}\right)$项主导总体执行时间，故在此区间增加采样量不会显著改变总耗时；而当$k > N$时，$O\left( {k \cdot  \log N \cdot  \log \frac{N}{k}}\right)$项转而主导总耗时，导致采样量增加会引发执行时间激增。SJoin呈现相似趋势。值得注意的是，当采样量达到$k = {10},{000}$时，SJoin所需运行时间甚至超过了RSJoin在采样量高达$k = 5,{000},{000}$时的耗时。

Scalability. To examine the scalability of both methods, we evaluate the QZ query on TPC-DS dataset with scale factors of 1,3,10,and 30. The results are shown in Figure 9. The input size of QZ is approximately ${226}\mathrm{{MB}}$ when the scale factor is 1,while the input size reaches around ${6.6}\mathrm{{GB}}$ when the scale factor reaches 30 . We do not include the results of SJoin here since it takes more than 4 hours to finish the execution even with scale factor as 1 . We observe that even without applying foreign-key optimization, RSJoin achieves linear growth in the running time as the scale factor increases, which indicates that RSJoin is scalable and practical even when dealing with significantly huge input size.

可扩展性。为检验两种方法的扩展能力，我们在TPC-DS数据集上对QZ查询进行规模因子为1、3、10和30的评估。结果如图9所示：当规模因子为1时，QZ的输入规模约为${226}\mathrm{{MB}}$；当规模因子达到30时，输入规模增至约${6.6}\mathrm{{GB}}$。由于SJoin即使在规模因子为1时也需超过4小时完成执行，其数据未予展示。实验表明，即使未应用外键优化，RSJoin的运行时间仍随规模因子增长呈线性上升，这证明RSJoin在处理极大输入规模时仍具备可扩展性和实用性。

Memory usage. In addition, we explore the memory usage of all methods. Figure 10 shows the memory usage by RSJoin and SJoin on line-3 join and RSJoin_opt and SJoin_opt on Q10 query. The input size is roughly ${21}\mathrm{{MB}}$ for line-3 join and ${505}\mathrm{{MB}}$ for Q10 query. After processing every ${10}\%$ of the input data, we record the memory usage as shown in Figure 10. The memory usage of Q10 grows much faster than line-3 join as it is much more complex with more dedicated index built. The memory usage required by all algorithms is linear to the input size. On line-3 join, RSJoin requires only ${60}\%$ of the memory by SJoin,and on Q10,RSJoin_opt needs only 31% of the memory by SJoin_opt. This demonstrates a nice property of our algorithm: the amount of memory used by RSJoin and RSJoin_opt during execution scales linearly with the input size even when the join size grows exponentially, which also enables our algorithm to handle much more complex queries over large input datasets with limited memory resources.

内存消耗。我们还对比了各方法的内存使用情况。图10展示了RSJoin和SJoin在线性3连接（line-3 join）上的内存消耗，以及RSJoin_opt和SJoin_opt在Q10查询中的表现。线性3连接的输入规模约为${21}\mathrm{{MB}}$，Q10查询约为${505}\mathrm{{MB}}$。每处理${10}\%$输入数据后记录内存使用量（图10）。由于Q10需构建更复杂的专用索引，其内存增速远快于线性3连接。所有算法的内存消耗均与输入规模呈线性关系：在线性3连接中，RSJoin仅需SJoin${60}\%$的内存；在Q10查询中，RSJoin_opt仅消耗SJoin_opt的31%。这印证了我们算法的优势——即使连接规模指数级增长，RSJoin系列算法执行时的内存消耗仍与输入规模保持线性关系，使其能在有限内存资源下处理更复杂的大规模数据集查询。

Optimizations. We evaluate the effectiveness of our optimizations by counting the number of loop execution lines 9-11 in Algorithm 5. Table 8 records the count as well as the total running time of our method for QZ query over the TPC-DS dataset (scale factor 10 and sample size 1,000,000). It is clear to see that when applying foreign-key optimizations, the number of propagation decreases as well as the total execution time. If applying the grouping optimizations, we can further decrease the total execution time,achieving roughly ${10}\mathrm{x}$ speedup over the RSJoin without optimization.

优化效果。通过统计算法5中第9-11行循环执行次数来评估优化效果。表8记录了TPC-DS数据集（规模因子10，样本量1,000,000）上QZ查询的总运行时间及该计数项。可见应用外键优化后，传播次数与总执行时间同步减少；若叠加分组优化，可进一步缩短总执行时间，较未优化的RSJoin实现约${10}\mathrm{x}$加速比。

### 6.3 Reservoir Sampling with Predicate

### 6.3 带谓词的水库采样

At last, we compare our new reservoir sampling algorithm with predicate (denoted as RSWP) with the classic reservoir sampling algorithm (denoted as RS) on data streams. We generate a data stream as follows. We fix a random string of 1024 characters, referred as the query string. Each item in the input stream is a random string, within edit distance ranging from 0 to 64 from the base string. The predicate selects all strings in the data stream whose edit distance from the query string is less than or equal to 16 .

最后，我们在数据流上对比新型带谓词水库采样算法（RSWP）与经典水库采样算法（RS）。数据流生成方式如下：固定一个1024字符的随机字符串作为查询串，输入流中每个项目为与基准串编辑距离0至64之间的随机字符串。谓词筛选出数据流中与查询串编辑距离≤16的所有字符串。

In Figure 11,we take a $\frac{1}{10}$ -dense stream of 100,000 strings with sample size $k = 1,{000}$ . We record the execution time after processing every ${10}\%$ of the input stream. As RS needs to process every item (i.e., compute the edit distance from the query string), the running time of RS is linear to the number of items in the stream processed so far. The time required by RSWP for processing the first ${10}\%$ of the input stream is the same as RS,since both of them need to process every one in the first 10,000 items (approximately) until it fills the reservoir. After that, the running time of RSWP grows slower and slower,which is consistent with our theoretical result that it takes $O\left( \frac{k}{{r}_{i} + 1}\right)$ expected time to process the $i$ -th item.

图11展示了包含100,000个字符串的$\frac{1}{10}$密集流（样本量$k = 1,{000}$）测试结果。每处理${10}\%$输入流后记录执行时间。由于RS需处理每个项目（即计算与查询串的编辑距离），其运行时间与已处理流项目数呈线性关系。RSWP处理前${10}\%$输入流的时间与RS相同（约前10,000项需全处理以填充水库），此后RSWP耗时增速逐渐放缓，这与理论预期一致——处理第$i$个项目预计耗时$O\left( \frac{k}{{r}_{i} + 1}\right)$。

In figure 12, we measure the running time of both RSWP and RS over 11 streams of same input size but different densities. As RS needs to process every item in the stream, its running time only depends on the input size, instead of the density of input stream. In contrast, the running time of RSWP depends on the density of stream. In an extreme case, when no item passes the predicate (i.e., the density is 0 ), RSWP cannot skip any item and hence requires the same time as RS. However, as density increases, the running time of RSWP decreases significantly. In another extreme case, when every item passes the predicate (i.e., the density is 1.0), RSWP exhibits a speed advantage of 17.7x over RS.

在图12中，我们测量了RSWP和RS算法在11组输入规模相同但密度不同的数据流上的运行时间。由于RS需要处理流中的每个数据项，其运行时间仅取决于输入规模，而非输入流的密度。相比之下，RSWP的运行时间与数据流密度密切相关。极端情况下，当没有数据项满足谓词条件（即密度为0）时，RSWP无法跳过任何项，因此耗时与RS相同。但随着密度增加，RSWP的运行时间显著下降。另一个极端情况是当所有数据项都满足谓词（密度为1.0）时，RSWP相比RS展现出17.7倍的速度优势。

## 7 RELATED WORK

## 7 相关工作

In addition to the directly related work mentioned in Section 2.2, the following is also relevant to our work:

除第2.2节提到的直接相关研究外，以下工作也与本研究相关：

Streaming Subgraphs Sampling. The problem of sampling subgraph patterns from a graph whose edges come as an input stream has also been considered (where the space usage is important). For example,Paven et. al [25] designed an algorithm that uses $O\left( \frac{{N}^{3/2}}{OUT}\right)$ space,where OUT is the number of triangles in the graph. In the field of property testing (where sub-linear number of query accesses to the graph is important), Eden et al [16] studied the problem of almost uniform sampling of edges, and Biswas et. al [11] studied the problem of sampling subgraphs.

流式子图采样。从边以流式输入形式到达的图中采样子图模式的问题也受到关注（其中空间利用率是关键指标）。例如Paven等人[25]设计了空间复杂度为$O\left( \frac{{N}^{3/2}}{OUT}\right)$的算法（OUT表示图中三角形数量）。在属性测试领域（关注对图的次线性查询访问），Eden等人[16]研究了边近似均匀采样问题，Biswas等人[11]则研究了子图采样问题。

Maintaining Conjunctive Queries under Updates. It has been shown $\left\lbrack  {{10},{18}}\right\rbrack$ that a very restrictive class of queries,known as q-hierarchical query can admit an index with $O\left( 1\right)$ update time. However,any non-q-hierarchical query,a lower bound of $\Omega \left( {N}^{\frac{1}{2} - \epsilon }\right)$ has also been proved on the update time,for any small constant $\epsilon  > 0$ . This result is rather negative,since q-hierarchical queries are a very restricted class; for example, the line-3 join. Meanwhile, [18] showed an index for acyclic joins that can be updated in $O\left( N\right)$ time. Later,Kara et al. [19] designed optimal data structures that can be updated in $O\left( \sqrt{N}\right)$ time while supporting $O\left( 1\right)$ -delay enumeration for line-3 join,triangle join, length-4 cycle join, etc. Moreover, Kara et al. [20] also investigated the tradeoff between update time and delay for hierarchical queries. Wang et al. $\left\lbrack  {{28},{29}}\right\rbrack$ worked on instance-dependent complexity by relating the update time to the enclosureness of update sequences.

更新操作下的合取查询维护。研究表明$\left\lbrack  {{10},{18}}\right\rbrack$，只有极其受限的q-层次化查询类才能实现$O\left( 1\right)$更新时间的索引。而对于任何非q-层次化查询，任何小常数$\epsilon  > 0$都存在$\Omega \left( {N}^{\frac{1}{2} - \epsilon }\right)$更新时间下界。这个结论相当消极，因为q-层次化查询类限制性极强（例如三路连线连接）。同时文献[18]提出了无环连接查询的$O\left( N\right)$时间可更新索引。随后Kara等人[19]设计了最优数据结构，在支持三路连线连接、三角形连接、四元环连接等$O\left( 1\right)$延迟枚举的同时，可实现$O\left( \sqrt{N}\right)$时间更新。此外Kara等人[20]还研究了层次化查询中更新时间与延迟的权衡关系。Wang等人$\left\lbrack  {{28},{29}}\right\rbrack$通过将更新时间与更新序列的封闭性关联，探索了实例依赖的复杂度。

## 8 CONCLUSION

## 8 结论

In this paper, we propose a general reservoir sampling algorithm that supports a predicate. We design a dynamic data structure that supports efficient updates and direct access of the join results. By combining these two key techniques, we present our reservoir sampling over joins algorithm which runs in near-linear time. There are several interesting questions left as open, such as uniform sampling over join-project queries over data streams.

本文提出了一种支持谓词的通用蓄水池采样算法，设计了支持高效更新和直接访问连接结果的动态数据结构。通过结合这两项关键技术，我们实现了近线性时间运行的连接操作蓄水池采样算法。研究仍遗留若干开放性问题，例如数据流上连接-投影查询的均匀采样问题。

## ACKNOWLEDGMENTS

## 致谢

This work has been supported by HKRGC under grants 16205420, 16205422, and 16204223.

本研究获香港研资局资助（项目编号：16205420、16205422和16204223）。

## REFERENCES

## 参考文献

[1] Code. https://github.com/hkustDB/Reservoir-Sampling-over-Joins

[2] Reservoir Sampling over Joins. https://arxiv.org/pdf/2404.03194.pdf

[3] Symmetric hash join. https://en.wikipedia.org/wiki/Symmetric_hash_join

[4] TPC-DS dataset. https://www.tpc.org/tpcds/

[5] Serge Abiteboul, Richard Hull, and Victor Vianu. 1995. Foundations of databases. Vol. 8. Addison-Wesley Reading.

[6] Swarup Acharya, Phillip B Gibbons, Viswanath Poosala, and Sridhar Ramaswamy. 1999. Join synopses for approximate query answering. In Proceedings of the 1999 ACM SIGMOD international conference on Management of data. 275-286.

[7] Albert Atserias, Martin Grohe, and Dániel Marx. 2008. Size bounds and query plans for relational joins. In 2008 49th Annual IEEE Symposium on Foundations of Computer Science. IEEE, 739-748.

[8] Guillaume Bagan, Arnaud Durand, and Etienne Grandjean. 2007. On acyclic conjunctive queries and constant delay enumeration. In International Workshop on Computer Science Logic. Springer, 208-222.

[9] C. Beeri, R. Fagin, D. Maier, and M. Yannakakis. 1983. On the desirability of acyclic database schemes. JACM 30, 3 (1983), 479-513.

[10] Christoph Berkholz, Jens Keppeler, and Nicole Schweikardt. 2017. Answering conjunctive queries under updates. In proceedings of the 36th ACM SIGMOD-SIGACT-SIGAI symposium on Principles of database systems. 303-318.

[11] AS Biswas, T Eden, and R Rubinfeld. 2021. Towards a Decomposition-Optimal Algorithm for Counting and Sampling Arbitrary Motifs in Sublinear Time. RANDOM 2021 (2021).

[12] Nofar Carmeli, Shai Zeevi, Christoph Berkholz, Benny Kimelfeld, and Nicole Schweikardt. 2020. Answering (unions of) conjunctive queries using random access and random-order enumeration. In Proceedings of the 39th ACM SIGMOD-SIGACT-SIGAI Symposium on Principles of Database Systems. 393-409.

[13] Surajit Chaudhuri, Rajeev Motwani, and Vivek Narasayya. 1999. On random sampling over joins. ACM SIGMOD Record 28, 2 (1999), 263-274.

[14] Yu Chen and Ke Yi. 2020. Random Sampling and Size Estimation Over Cyclic Joins. In 23rd International Conference on Database Theory.

[15] Shiyuan Deng, Shangqi Lu, and Yufei Tao. 2023. On Join Sampling and Hardness of Combinatorial Output-Sensitive Join Algorithms. In Proceedings of the 42nd ACM SIGMOD-SIGACT-SIGAI Symposium on Principles of Database Systems (PODS 2023).

[16] Talya Eden and Will Rosenbaum. 2018. On Sampling Edges Almost Uniformly. In 1st Symposium on Simplicity in Algorithms (SOSA 2018).

[17] Georg Gottlob, Gianluigi Greco, Francesco Scarcello, et al. 2014. Treewidth and hypertree width. Tractability: Practical Approaches to Hard Problems 1 (2014), 20.

[18] Muhammad Idris, Martín Ugarte, and Stijn Vansummeren. 2017. The dynamic yannakakis algorithm: Compact and efficient query processing under updates. In Proceedings of the 2017 ACM International Conference on Management of Data. 1259-1274.

[19] Ahmet Kara, Hung Q Ngo, Milos Nikolic, Dan Olteanu, and Haozhe Zhang. 2019. Counting Triangles under Updates in Worst-Case Optimal Time. In 22nd International Conference on Database Theory. Schloss Dagstuhl-Leibniz-Zentrum fuer Informatik.

[20] Ahmet Kara, Milos Nikolic, Dan Olteanu, and Haozhe Zhang. 2020. Trade-offs in static and dynamic evaluation of hierarchical queries. In Proceedings of the 39th ACM SIGMOD-SIGACT-SIGAI Symposium on Principles of Database Systems. 375-392.

[21] Kyoungmin Kim, Jaehyun Ha, George Fletcher, and Wook-Shin Han. 2023. Guaranteeing the Ö(AGM/OUT) Runtime for Uniform Sampling and Size Estimation over Joins. In Proceedings of the 42nd ACM SIGMOD-SIGACT-SIGAI Symposium on Principles of Database Systems. 113-125.

[22] Donald Ervin Knuth. 1997. The art of computer programming. Vol. 3. Pearson Education.

[23] Jure Leskovec and Andrej Krevl. 2014. SNAP Datasets: Stanford Large Network Dataset Collection. http://snap.stanford.edu/data.

[24] Kim-Hung Li. 1994. Reservoir-sampling algorithms of time complexity o (n (1+ log (n/n)). ACM Transactions on Mathematical Software (TOMS) 20, 4 (1994), 481-493.

[25] Aduri Pavan, Kanat Tangwongsan, Srikanta Tirthapura, and Kun-Lung Wu. 2013. Counting and sampling triangles from a graph stream. Proceedings of the VLDB Endowment 6, 14 (2013), 1870-1881.

[26] Gábor Szárnyas, Jack Waudby, Benjamin A. Steer, Dávid Szakállas, Altan Birler, Mingxi Wu, Yuchen Zhang, and Peter Boncz. 2022. The LDBC Social Network Benchmark: Business Intelligence Workload. Proc. VLDB Endow. 16, 4 (dec 2022), 877-890. https://doi.org/10.14778/3574245.3574270

[27] Jeffrey S Vitter. 1985. Random sampling with a reservoir. ACM Transactions on Mathematical Software (TOMS) 11,1 (1985), 37-57.

[28] Qichen Wang, Xiao Hu, Binyang Dai, and Ke Yi. 2023. Change Propagation Without Joins. Proceedings of the VLDB Endowment 16, 5 (2023), 1046-1058.

[29] Qichen Wang and Ke Yi. 2020. Maintaining Acyclic Foreign-Key Joins under Updates. In Proceedings of the 2020 ACM SIGMOD International Conference on Management of Data. 1225-1239.

[30] Zhuoyue Zhao, Robert Christensen, Feifei Li, Xiao Hu, and Ke Yi. 2018. Random sampling over joins revisited. In Proceedings of the 2018 International Conference on Management of Data. 1525-1539.

[31] Zhuoyue Zhao, Feifei Li, and Yuxi Liu. 2020. Efficient join synopsis maintenance for data warehouse. In Proceedings of the 2020 ACM SIGMOD International Conference on Management of Data. 2027-2042.