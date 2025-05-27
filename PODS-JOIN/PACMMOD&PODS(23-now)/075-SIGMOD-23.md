# Better than Composition: How to Answer Multiple Relational Queries under Differential Privacy

超越组合机制：差分隐私下如何回答多重关系查询

WEI DONG, DAJUN SUN, and KE YI, Hong Kong University of Science and Technology, China

魏东、孙大军和易珂，香港科技大学，中国

Answering relational queries under differential privacy has attracted a lot of attention in recent years due to growing concerns on personal privacy, and instance-optimal mechanisms have been developed for a single query. However, most real-world data analytical tasks require multiple queries to be answered under a total privacy budget. The standard solution to extend the single-query mechanism to multiple queries is via privacy composition. However,we observe that this may yield an error bound that could be a $\sqrt{d}$ -factor worse from the optimal,where $d$ is the number of queries. In this paper,we present a different,more holistic approach that closes this gap. In addition to theoretical optimality, our new mechanism also significantly outperforms privacy composition in practice,especially on more skewed data and large $d$ .

近年来，随着对个人隐私保护的日益关注，差分隐私下的关系查询应答受到广泛研究，针对单一查询的实例最优机制已趋成熟。然而实际数据分析任务通常需要在总隐私预算下应答多个查询。传统解决方案通过隐私组合将单查询机制扩展至多查询场景，但我们发现这可能导致误差边界比最优解恶化$\sqrt{d}$倍（$d$为查询数量）。本文提出一种全新的整体性方法以消除该差距，新机制不仅在理论上达到最优，在实际数据（尤其是偏态分布和大规模$d$场景）中的表现也显著优于隐私组合方法。

CCS Concepts: - Information systems $\rightarrow$ Database query processing; - Security and privacy $\rightarrow$ Database and storage security; - Theory of computation $\rightarrow$ Theory of database privacy and security.

CCS概念体系：- 信息系统$\rightarrow$数据库查询处理；- 安全与隐私$\rightarrow$数据库及存储安全；- 计算理论$\rightarrow$数据库隐私与安全理论

Additional Key Words and Phrases: Differential privacy; SJA query; Multiple queries

附加关键词：差分隐私；SJA查询；多重查询

## ACM Reference Format:

## ACM参考文献格式：

Wei Dong, Dajun Sun, and Ke Yi. 2023. Better than Composition: How to Answer Multiple Relational Queries under Differential Privacy. Proc. ACM Manag. Data 1, 2, Article 123 (June 2023), 26 pages. https://doi.org/10.1145/3589268

魏东、孙大军、易珂. 2023. 超越组合机制：差分隐私下多重关系查询应答方法. ACM数据管理汇刊1卷2期123号(2023年6月)，26页. https://doi.org/10.1145/3589268

## 1 INTRODUCTION

## 1 引言

Query answering under differential privacy (DP) has been studied extensively in the last 20 years. To date, most problems over a flat table have been relatively well solved. A single (counting or linear) query can be easily answered by the classical Laplace mechanism or the Gaussian mechanism [17], so most efforts have been devoted to the problem of answering a set of $d$ queries. There are two general approaches to this multi-query problem. The first is privacy composition, i.e., we divide the privacy budget to the $d$ queries and answer each query with the single-query mechanism. Using advanced composition [18],the utility suffers an $\widetilde{O}\left( \sqrt{d}\right)$ -factor degradation ${}^{1}$ ,which is the best we can if the queries are arbitrary linear queries,unless $d$ is larger than the size of the database [19]. The other approach is to exploit some special structures of the queries. For instance, if all queries are ranges queries,then the $\widetilde{O}\left( \sqrt{d}\right)$ -factor degradation can be reduced to $\widetilde{O}\left( 1\right) \left\lbrack  {{15},{29},{41}}\right\rbrack$ .

差分隐私(DP)下的查询应答研究已有二十年历史，目前单表查询问题已基本解决。经典拉普拉斯机制或高斯机制[17]可轻松处理单次(计数或线性)查询，因此研究重点转向$d$个查询集的应答问题。现有两种主流方法：其一是隐私组合——将隐私预算分配给$d$个查询后采用单查询机制处理，使用高级组合[18]时会产生$\widetilde{O}\left( \sqrt{d}\right)$倍效用损失${}^{1}$，这对任意线性查询已是最优解（除非$d$超过数据库规模[19]）；其二是利用查询特殊结构，例如范围查询可将$\widetilde{O}\left( \sqrt{d}\right)$倍损失降至$\widetilde{O}\left( 1\right) \left\lbrack  {{15},{29},{41}}\right\rbrack$。

### 1.1 DP in Relational Databases

### 1.1 关系数据库中的差分隐私

The situation becomes more complicated in a relational database with multiple relations (tables), mostly due to two challenges.

多关系(多表)数据库的隐私保护更为复杂，主要面临两大挑战。

${}^{1}$ The $\widetilde{O}$ notation suppresses the dependency on $\varepsilon$ and polylogarithmic factors.

${}^{1}$符号$\widetilde{O}$用于忽略$\varepsilon$相关项及多对数因子

Permission to make digital or hard copies of all or part of this work for personal or classroom use is granted without fee provided that copies are not made or distributed for profit or commercial advantage and that copies bear this notice and the full citation on the first page. Copyrights for components of this work owned by others than the author(s) must be honored. Abstracting with credit is permitted. To copy otherwise, or republish, to post on servers or to redistribute to lists, requires prior specific permission and/or a fee. Request permissions from permissions@acm.org.

允许为个人或课堂教学目的无偿复制本作品全部或部分内容，前提是复制件不用于盈利或商业用途，且须在首页保留完整版权声明。非作者持有的部分版权内容须受尊重。允许规范引用。其他形式的复制、再版、服务器发布或列表分发需获得特别授权和/或付费。申请权限请联系permissions@acm.org

© 2023 Copyright held by the owner/author(s). Publication rights licensed to ACM.

©2023版权归所有者/作者所有，出版权由ACM许可

2836-6573/2023/6-ART123 \$15.00

2836-6573/2023/6-第123号作品 15.00美元

https://doi.org/10.1145/3589268

---

<!-- Footnote -->

Authors' address: Wei Dong, wdongac@cse.ust.hk; Dajun Sun, dsunad@cse.ust.hk; Ke Yi, yike@cse.ust.hk, Hong Kong University of Science and Technology, Hong Kong, China.

作者联系方式：魏东 wdongac@cse.ust.hk；孙大军 dsunad@cse.ust.hk；易珂 yike@cse.ust.hk，香港科技大学，中国香港

<!-- Footnote -->

---

Challenge 1: Unbounded global sensitivity. First, unlike a flat table, a relational schema models complicated relationships among different types of entities, not all of which are equally positioned in terms of privacy protection. Consider the TPC-H schema. We usually take Customer or Supplier (or both) as the private entities, often called "users", while Nation and Region are public. Furthermore, any tuple that has a foreign key (FK) reference (directly or indirectly) to a user, such as a lineitem in an order placed by a customer, is considered as data belonging to the user. This results in what is known as user-level ${DP}$ ,as opposed to tuple-level ${DP}$ used over a flat table. The formal user-level DP definition in relational databases is deferred to Section 3.1; intuitively, this creates a difficulty to the Laplace/Gaussian mechanism, since two neighboring instances that differ by one user (e.g., a customer or a supplier) may differ by arbitrarily many tuples (e.g., lineitems), thus making the global sensitivity of the query, ${\mathrm{{GS}}}_{Q}$ ,unbounded.

挑战一：无界的全局敏感度。首先，与平面表不同，关系模式建模了不同类型实体间的复杂关联，并非所有实体在隐私保护层面都具有同等地位。以TPC-H模式为例，通常将Customer（客户）或Supplier（供应商）视为隐私实体（统称"用户"），而Nation（国家）和Region（地区）则为公开信息。此外，任何通过外键（直接或间接）关联到用户的元组（如客户订单中的lineitem条目）均被视为该用户的数据。这形成了所谓的用户级${DP}$（区别于平面表中使用的元组级${DP}$）。关系数据库中用户级差分隐私（DP）的形式化定义将在3.1节阐述；直观而言，这给拉普拉斯/高斯机制带来了困难——因为相差一个用户（如客户或供应商）的两个相邻实例可能包含任意数量元组（如订单条目）的差异，从而导致查询的全局敏感度${\mathrm{{GS}}}_{Q}$无界。

When ${\mathrm{{GS}}}_{Q}$ is unbounded,a common approach is to consider the local sensitivity [40],or more appropriately, the downward sensitivity [10]. Informally (formal definition given in Section 3.3), for a given query $Q$ ,the downward sensitivity of a user $u$ ,denoted ${\mathrm{{DS}}}_{Q}\left( u\right)$ ,is the contribution to $Q$ from all the data belonging to $u$ . The downward sensitivity of a database instance $\mathbf{I}$ ,denoted ${\mathrm{{DS}}}_{Q}\left( \mathrm{I}\right)$ ,is the maximum ${\mathrm{{DS}}}_{Q}\left( u\right)$ over all $u \in  \mathrm{I}$ . Then,one may use the truncation mechanism [27]: For a truncation threshold $r$ ,delete all users $u$ and their data where ${\mathrm{{DS}}}_{Q}\left( u\right)  > r$ ,and then apply the Laplace/Gaussian mechanism with sensitivity $r$ . The optimal value of $r$ is ${\mathrm{{DS}}}_{Q}\left( \mathbf{I}\right)$ ,which results in an instance-optimal error of $O\left( {{\mathrm{{DS}}}_{Q}\left( \mathbf{I}\right) }\right)$ . However,but this optimal truncation threshold cannot be used directly as it is sensitive to I. Thus,the main challenge is to find a near-optimal $r$ in a differentially private manner.

当${\mathrm{{GS}}}_{Q}$无界时，通常考虑局部敏感度[40]或更准确的向下敏感度[10]（非正式定义见3.3节）。对于给定查询$Q$，用户$u$的向下敏感度（记作${\mathrm{{DS}}}_{Q}\left( u\right)$）指该用户所有数据对$Q$的贡献值。数据库实例$\mathbf{I}$的向下敏感度（记作${\mathrm{{DS}}}_{Q}\left( \mathrm{I}\right)$）则是所有$u \in  \mathrm{I}$中${\mathrm{{DS}}}_{Q}\left( u\right)$的最大值。此时可采用截断机制[27]：设定截断阈值$r$后，删除所有满足${\mathrm{{DS}}}_{Q}\left( u\right)  > r$的用户$u$及其数据，再应用敏感度为$r$的拉普拉斯/高斯机制。最优阈值$r$应为${\mathrm{{DS}}}_{Q}\left( \mathbf{I}\right)$，此时可实现实例最优误差$O\left( {{\mathrm{{DS}}}_{Q}\left( \mathbf{I}\right) }\right)$。但由于该最优阈值对实例I敏感而无法直接使用，核心挑战在于如何以差分隐私方式寻找近似最优的$r$。

Challenge 2: Self-joins. When the query $Q$ has no self-joins,the truncation mechanism would work (after finding a good $r$ ). However,self-joins create another challenge,as they introduce correlations among the users: Truncating one user's data may change the downward sensitivities of other users, which results in the truncation mechanism violating privacy. This issue has been identified in [10], who then propose a linear-program-based solution that fixes the problem while achieving the optimal error $\widetilde{O}\left( {{\mathrm{{DS}}}_{Q}\left( \mathrm{I}\right) }\right)$ . Furthermore,as explained in Section 3.2,the issue with self-joins is more prominent under DP: many queries without explicit self-joins actually induce implicit self-joins due to the privacy requirement. In particular, self-joins are always introduced when the a join result references more than one user, which is a common scenario in relational databases.

挑战二：自连接。当查询$Q$不含自连接时，截断机制（在找到合适$r$后）可生效。但自连接会引发新难题——它建立了用户间的关联：截断某用户数据可能改变其他用户的向下敏感度，导致截断机制破坏隐私性。[10]文献已指出该问题并提出基于线性规划的解决方案，在实现最优误差$\widetilde{O}\left( {{\mathrm{{DS}}}_{Q}\left( \mathrm{I}\right) }\right)$的同时修复此缺陷。如3.2节所述，自连接问题在DP环境下更显著：许多无显式自连接的查询因隐私要求实际隐含自连接。特别当连接结果涉及多个用户时（关系数据库中的常见场景），必然产生自连接。

### 1.2 From a Single Query to Multiple Queries

### 1.2 从单查询到多查询

Answering a single query is not very useful in practice. Thus, as with the flat table case, it is natural to consider the multi-query problem in relational databases, which includes group-by queries as an important special case (i.e.,each group corresponds to one query). Let $\mathbf{Q} = \left( {{Q}_{1},\ldots ,{Q}_{d}}\right)$ be the $d$ queries we wish to answer privately. We use the standard metric of root-mean-square error (RMSE) to measure the utility:

实践中仅回答单个查询价值有限。与平面表情形类似，自然需考虑关系数据库中的多查询问题（其中分组查询是重要特例，即每组对应一个查询）。设$\mathbf{Q} = \left( {{Q}_{1},\ldots ,{Q}_{d}}\right)$为需要隐私回答的$d$个查询，我们采用均方根误差（RMSE）作为标准效用度量指标：

$$
\parallel \widetilde{\mathbf{Q}}\left( \mathbf{I}\right)  - \mathbf{Q}\left( \mathbf{I}\right) \parallel  = \sqrt{\mathop{\sum }\limits_{{k = 1}}^{d}{\left( {\widetilde{Q}}_{k}\left( \mathbf{I}\right)  - {Q}_{k}\left( \mathbf{I}\right) \right) }^{2}},
$$

or equivalently,the ${\ell }_{2}$ distance between the privatized query answers $\widetilde{\mathbf{Q}}\left( \mathbf{I}\right)$ and the true answers $\mathrm{Q}\left( \mathrm{I}\right)$ ,both taken as $d$ -dimensional vectors. The notation $\parallel  \cdot  \parallel$ refers to the ${\ell }_{2}$ norm of a vector throughout the paper.

或者说，私有化查询答案$\widetilde{\mathbf{Q}}\left( \mathbf{I}\right)$与真实答案$\mathrm{Q}\left( \mathrm{I}\right)$之间的${\ell }_{2}$距离（两者均视为$d$维向量）。本文中符号$\parallel  \cdot  \parallel$始终表示向量的${\ell }_{2}$范数。

In this paper, we will allow the queries to contain arbitrary joins and selection predicates. As we do not assume any restricted form of the joins and predicates, the only known solution is to use privacy composition. By advanced composition,we can allocate a privacy budget of $\widetilde{O}\left( {1/\sqrt{d}}\right)$ to each query and invoke the single-query mechanism in [10]. This leads to an error of $\widetilde{O}\left( {\sqrt{d} \cdot  {\mathrm{{DS}}}_{{Q}_{k}}\left( \mathrm{I}\right) }\right)$ for ${Q}_{k}$ ,hence an RMSE of

本文允许查询包含任意连接和选择谓词。由于不限制连接和谓词的形式，现有解决方案只能采用隐私组合。通过高级组合，可为每个查询分配$\widetilde{O}\left( {1/\sqrt{d}}\right)$隐私预算并调用文献[10]的单查询机制，这将导致${Q}_{k}$的误差为$\widetilde{O}\left( {\sqrt{d} \cdot  {\mathrm{{DS}}}_{{Q}_{k}}\left( \mathrm{I}\right) }\right)$，从而得到均方根误差

$$
\widetilde{O}\left( {\sqrt{d} \cdot  \sqrt{\mathop{\sum }\limits_{{k = 1}}^{d}{\mathrm{{DS}}}_{{Q}_{k}}{\left( \mathrm{\;I}\right) }^{2}}}\right) . \tag{1}
$$

Challenge/opportunity 3: Better than composition. We make the crucial observation that the error bound in (1) is not optimal. In Section 4, we show that the lower bound for the multi-query problem is $\widetilde{\Omega }\left( {\sqrt{d} \cdot  {\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }\right)$ ,where ${\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$ ,informally speaking,is the largest contribution of any user in $\mathrm{I}$ to the $d$ query results measured in ${\ell }_{2}$ norm. Note that we have the following relationship:

挑战/机遇三：突破组合限制。我们关键性地发现(1)式误差界并非最优。第4章将证明多查询问题的下界是$\widetilde{\Omega }\left( {\sqrt{d} \cdot  {\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }\right)$，其中${\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$（通俗地说）是任何用户对$\mathrm{I}$中$d$个查询结果在${\ell }_{2}$范数下的最大贡献。注意存在以下关系：

$$
{\mathrm{{DS}}}_{\mathbb{Q}}\left( \mathrm{I}\right)  \leq  \sqrt{\mathop{\sum }\limits_{{k = 1}}^{d}{\mathrm{{DS}}}_{{Q}_{k}}{\left( \mathrm{I}\right) }^{2}} \leq  \sqrt{d} \cdot  {\mathrm{{DS}}}_{\mathbb{Q}}\left( \mathrm{I}\right) .
$$

Both inequalities are tight: The first inequality becomes an equality if the user with the maximum contribution to $\mathbf{Q}$ happens to be the maximum-contribution user to every ${Q}_{k} \in  \mathbf{Q}$ ,and the second inequality becomes an equality if each user contributes to only one query in Q. For typical database instances and queries (especially a group-by query), the situation will be more towards the latter, i.e., each user contributes to a small number of queries (groups), in which case the error bound of (1) can be a $\sqrt{d}$ -factor away from optimal. This creates a third challenge,or rather,an opportunity for the multi-query problem, i.e., how to do better than privacy composition.

两个不等式都是紧致的：当对$\mathbf{Q}$贡献最大的用户恰好也是对每个${Q}_{k} \in  \mathbf{Q}$贡献最大的用户时，第一个不等式取等；当每个用户仅参与Q中一个查询时，第二个不等式取等。典型数据库实例和查询（特别是分组查询）往往更接近后者，即用户仅贡献少量查询（分组），此时(1)式误差界可能偏离最优值$\sqrt{d}$倍。这构成了多查询问题的第三个挑战，或者说机遇——如何突破隐私组合的限制。

### 1.3 Our Results

### 1.3 研究成果

Our key insight is that answering all $d$ queries as a whole can yield a much better result. We start by considering multiple self-join-free queries. We observe that $d$ such queries are equivalent to the sum (mean) estimation problem in $d$ dimensions,a problem that has been extensively studied in the machine learning literature $\left\lbrack  {5,{21},{23}}\right\rbrack$ . Restated in our terminology,their algorithms achieve the optimal error of $\widetilde{O}\left( {\sqrt{d} \cdot  {\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }\right)$ ,modulo polylogarithmic factors. However,they are all restricted to instances $\mathrm{I}$ in which no user has contribution more than ${\mathrm{{GS}}}_{\mathrm{Q}}$ for some predefined ${\mathrm{{GS}}}_{\mathrm{Q}}$ ,and the hidden logarithmic factors depend on ${\mathrm{{GS}}}_{\mathrm{Q}}$ . More precisely,the best error obtained so far [21] is

我们的核心洞见是：整体处理所有$d$个查询能获得更优结果。首先考虑无自连接的多查询，观察到$d$个此类查询等价于$d$维空间中的求和（均值）估计问题——机器学习领域广泛研究的问题$\left\lbrack  {5,{21},{23}}\right\rbrack$。用我们的术语表述，其算法在忽略多对数因子时能达到$\widetilde{O}\left( {\sqrt{d} \cdot  {\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }\right)$的最优误差，但都限制在预定义${\mathrm{{GS}}}_{\mathrm{Q}}$下用户贡献不超过${\mathrm{{GS}}}_{\mathrm{Q}}$的实例$\mathrm{I}$，且隐含对数因子依赖${\mathrm{{GS}}}_{\mathrm{Q}}$。文献[21]目前最佳误差为

$$
O\left( {{\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)  \cdot  \left( {\sqrt{d} + \sqrt{\log \left( {\mathrm{{GS}}}_{\mathrm{Q}}\right) \log \log \left( {\mathrm{{GS}}}_{\mathrm{Q}}\right) }}\right)  \cdot  \sqrt{\log \left( {1/\delta }\right) }/\varepsilon }\right) , \tag{2}
$$

where $\varepsilon ,\delta$ are the privacy parameters (see Section 3.3). Thus,they do not satisfactorily solve challenge 1. Our first result in this paper is the complete removal of the dependency on ${\mathrm{{GS}}}_{\mathrm{Q}}$ ,i.e.,we do not impose any restrictions on the database instance $\mathrm{I}$ ,effectively allowing ${\mathrm{{GS}}}_{\mathrm{Q}} = \infty$ . Specifically, in Section 4 we design an algorithm that achieves an error of

其中$\varepsilon ,\delta$为隐私参数（见3.3节）。这未能完全解决挑战1。本文首个成果是彻底消除对${\mathrm{{GS}}}_{\mathrm{Q}}$的依赖，即不限制数据库实例$\mathrm{I}$，允许${\mathrm{{GS}}}_{\mathrm{Q}} = \infty$。具体而言，第4章设计的算法可实现如下误差：

$$
O\left( {{\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)  \cdot  \left( {\sqrt{d\log \left( {1/\delta }\right) } + \log \log \left( {{\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }\right) }\right) /\varepsilon }\right) . \tag{3}
$$

Note that even assuming a finite ${\mathrm{{GS}}}_{\mathrm{Q}}$ ,the error bound of (3) is better than (2) since ${\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)  < {\mathrm{{GS}}}_{\mathrm{Q}}$ by definition. The key to obtaining this result is to find a near-optimal truncation threshold $r$ under an unbounded ${\mathrm{{GS}}}_{\mathrm{Q}}$ ,and then the standard truncation mechanism can be applied.

需要注意的是，即使假设${\mathrm{{GS}}}_{\mathrm{Q}}$有限，由于${\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)  < {\mathrm{{GS}}}_{\mathrm{Q}}$的定义，(3)式的误差界仍优于(2)式。获得该结果的关键在于无界${\mathrm{{GS}}}_{\mathrm{Q}}$条件下寻找接近最优的截断阈值$r$，随后即可应用标准截断机制。

Our main technical innovation is how to deal with self-joins. Self-joins are difficult to handle, since they make the truncation mechanism fail. To tackle self-joins, R2T [10] uses a series of linear programs (LPs), which can be considered as the more general forms of the LPs used in private graph analysis [25]. However, as we explain in Section 5.1, these LPs do not work for multiple queries due to fundamental reasons. Thereafter, we take a different approach to the multi-query problem, with the first version of the algorithm running in exponential time, which is subsequently reduced to polynomial using quadratically constrained quadratic programming (QCQP). We show that this algorithm achieves an error of

我们的核心技术突破在于处理自连接(self-joins)的方法。自连接会致使截断机制失效，因而难以处理。针对自连接，R2T[10]采用了一系列线性规划(LPs)，可视为私有图分析[25]所用线性规划的广义形式。但如第5.1节所述，由于根本性原因，这些线性规划无法适用于多查询场景。为此，我们采用不同方法解决多查询问题：算法初版运行时间为指数级，后通过二次约束二次规划(QCQP)降至多项式级。该算法实现的误差

$$
O\left( {\sqrt{d} \cdot  {\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)  \cdot  \sqrt{\log \left( {{e}^{\varepsilon }/\delta }\right) } \cdot  \left( {\log \log \left( {{\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }\right)  + \log \left( {{e}^{\varepsilon }/\delta }\right) }\right) /{\varepsilon }^{2}}\right) ,
$$

matching the lower bound up to polylogarithmic factors.

与对数多项式因子下的下界相匹配。

Finally, we built a system prototype that can accept a set of SJA queries consisting of arbitrary joins, selection predicates, followed by aggregation. It can also automatically rewrites a group-by query into such a set of SJA queries and answer them with our query-answering mechanism. Experimental results demonstrate that our mechanism can significantly outperform privacy composition combined with the state-of-the-art single-query mechanism [10], especially on more skewed data and large $d$ .

最终，我们构建的系统原型可接收包含任意连接、选择谓词及聚合操作的SJA查询集，还能自动将分组查询重写为SJA查询集，并通过我们的查询应答机制处理。实验结果表明，相较于结合最新单查询机制[10]的隐私组合方法，我们的机制在偏斜数据和大规模$d$场景下表现尤为优异。

## 2 RELATED WORK

## 2 相关工作

In the past several years, query answering under differential privacy [14] has attracted a lot of attention $\left\lbrack  {3,{10},{11},{13},{22},{27},{30},{32},{35},{36},{40}}\right\rbrack$ . Early works did not consider FK constraints,or equivalently, they adopt a DP model that only protects the tuples, which is called tuple-DP. Starting from [27], people began to consider user-DP modeled by FK constraints. Under user-DP, self-join-free queries $\left\lbrack  {{27},{40}}\right\rbrack$ are actually equivalent to the sum (mean) estimation problem $\left\lbrack  {2,{12},{21}}\right\rbrack$ . Very recently, R2T [10] is proposed to deal with queries with self-joins while achieving instance optimality.

过去数年，差分隐私[14]下的查询应答备受关注$\left\lbrack  {3,{10},{11},{13},{22},{27},{30},{32},{35},{36},{40}}\right\rbrack$。早期研究未考虑外键约束，或等价地采用仅保护元组的DP模型（称为元组差分隐私）。自[27]始，学者开始研究外键约束建模的用户差分隐私(user-DP)。在该模型下，无自连接查询$\left\lbrack  {{27},{40}}\right\rbrack$实际等价于求和（均值）估计问题$\left\lbrack  {2,{12},{21}}\right\rbrack$。最近提出的R2T[10]在实现实例最优性的同时，解决了含自连接的查询问题。

There are also a number works studying graph pattern counting queries under differential privacy $\left\lbrack  {7,9,{24},{26},{34},{44}}\right\rbrack$ ,which is an important special case of SJA queries. For graph data,there are two DP policies: edge-DP [7, 24, 34, 44] and node-DP [7, 9, 26]. They correspond to tuple-DP and user-DP applied to the special schema $R = \{ \operatorname{Node}\left( {ID}\right) ,\operatorname{Edge}\left( {\operatorname{src},\operatorname{dst}}\right) \}$ ,respectively.

另有大量研究专注于差分隐私$\left\lbrack  {7,9,{24},{26},{34},{44}}\right\rbrack$下的图模式计数查询——这是SJA查询的重要特例。针对图数据存在两种DP策略：边差分隐私[7,24,34,44]和节点差分隐私[7,9,26]，分别对应特定模式$R = \{ \operatorname{Node}\left( {ID}\right) ,\operatorname{Edge}\left( {\operatorname{src},\operatorname{dst}}\right) \}$上应用的元组差分隐私和用户差分隐私。

All the aforementioned works answer a single query at a time. The multi-query problem has been studied extensively on a flat table under tuple-DP [4, 6, 15, 19, 20, 29, 31, 33, 37-39, 41-43, 45]. For a set of $d$ arbitrary linear queries,advanced composition or the $d$ -dimensional Gaussian mechanism achieves $\widetilde{O}\left( \sqrt{d}\right)$ error for each query,which is the best we can achieve for $d < n$ ,where $n$ is the size of the table. For $d > n$ ,the optimal error of each query is $\widetilde{O}\left( \sqrt{n}\right)$ [19]. For a set of queries with special structures, the error can be further reduced [15, 31, 41]. Furthermore, [6, 29, 33] design mechanisms that are optimal for any given query set.

上述研究均针对单次查询应答。在元组差分隐私条件下，平面表上的多查询问题已被广泛研究[4,6,15,19,20,29,31,33,37-39,41-43,45]。对于$d$个任意线性查询集，高级组合或$d$维高斯机制可使每个查询达到$\widetilde{O}\left( \sqrt{d}\right)$误差——这是$d < n$条件下可实现的最佳效果，其中$n$表示表规模。当$d > n$时，各查询的最优误差为$\widetilde{O}\left( \sqrt{n}\right)$[19]。对于特殊结构的查询集，误差可进一步降低[15,31,41]。此外，[6,29,33]设计了适用于任意查询集的最优机制。

## 3 PRELIMINARIES

## 3 基础知识

### 3.1 Database with FK Constraints

### 3.1 含外键约束的数据库

We first review the DP definition in databases with FKs [27]. Let $\mathrm{R}$ be the database schema. When foreign-key constraints exist, $\mathbf{R}$ can be formalized as a directed acyclic graph,where each node corresponds to a relation $R \in  \mathbf{R}$ and a directed edge from ${R}^{\prime }$ to $R$ indicates an FK reference from an attribute of ${R}^{\prime }$ to the PK of $R$ . One relation is designated as the primary private relations ${R}_{P}$ (this is without loss of generality; see example below), while a relation having a direct or indirect FK referencing ${R}_{P}$ is called a secondary private relation; relations having no FK references to ${R}_{P}$ are public.

我们首先回顾带外键(FK)的数据库中的差分隐私(DP)定义[27]。设$\mathrm{R}$为数据库模式。当存在外键约束时，$\mathbf{R}$可形式化为有向无环图，其中每个节点对应一个关系$R \in  \mathbf{R}$，从${R}^{\prime }$指向$R$的有向边表示${R}^{\prime }$某属性对$R$主键(PK)的外键引用。指定一个关系作为主隐私关系${R}_{P}$(这不会丧失一般性；参见下例)，而通过直接或间接外键引用${R}_{P}$的关系称为次隐私关系；不包含指向${R}_{P}$外键引用的关系为公开关系。

Example 3.1. Consider the TPC-H schema in Figure 1 : Suppose we want to protect the privacy of both the customers and the suppliers. Then we can add a virtual relation User(ID), which includes all the PKs in Customer and Supplier, while adding FK constraints from the PKs of Customer, Supplier to ID. Then, User becomes the only primary private relation, while Customer, Supplier, Lineitem, Order, Partsupp are secondary private relations, and Part, Nation, Region are public.

示例3.1. 考虑图1中的TPC-H模式：假设需要同时保护客户与供应商的隐私。可添加虚拟关系User(ID)，包含Customer和Supplier的所有主键，并建立从Customer、Supplier主键到ID的外键约束。此时User成为唯一的主隐私关系，Customer、Supplier、Lineitem、Order、Partsupp为次隐私关系，Part、Nation、Region为公开关系。

<!-- Media -->

<!-- figureText: Partsupp(PtK, SK) Lineitem(OK, SK, PtK) Orders(OK, CK) Part(PtK)Supplier(SK,NK)Customer(CK,NK) Nation(NK, RK) Region(RK) -->

<img src="https://cdn.noedgeai.com/01965992-99d2-7937-99a4-2c1821ef6ce9_4.jpg?x=471&y=270&w=619&h=419&r=0"/>

Fig. 1. The TPC-H schema and its FK constraints. The underlined attributes are the PKs. Not all attributes are shown.

图1. TPC-H模式及其外键约束。带下划线属性为主键。未展示全部属性。

<!-- Media -->

Let $\mathbf{I}$ be a database instance over $\mathbf{R}$ . For any $R \in  \mathbf{R}$ ,let $\mathbf{I}\left( R\right)$ be the relation instance of $R$ in $\mathbf{I}$ . The reference relationships over tuples are defined naturally as follows: For $t \in  \mathbf{I}\left( R\right)$ and ${t}^{\prime } \in  \mathbf{I}\left( {R}^{\prime }\right)$ ,we say ${t}^{\prime }$ references $t$ if (1) ${R}^{\prime }$ references $R$ and the FK of ${t}^{\prime }$ is same as the PK of $t$ ; or (2) there exists another ${t}^{\prime \prime }$ such that ${t}^{\prime }$ references ${t}^{\prime \prime }$ and ${t}^{\prime \prime }$ references $t$ . Let $N = \left| {\mathbf{I}\left( {R}_{P}\right) }\right|$ be the number of private users. For $i \in  \left\lbrack  N\right\rbrack$ ,let ${t}_{i}\left( \mathrm{I}\right)$ be the $i$ th user in $\mathbf{I}\left( {R}_{P}\right)$ . We write ${\mathbf{I}}^{\prime } \subseteq  \mathbf{I}$ if ${\mathbf{I}}^{\prime }\left( {R}_{P}\right)  \subseteq  \mathbf{I}\left( {R}_{P}\right)$ ,and for each tuple $t$ in a secondary relation,it is included in ${\mathrm{I}}^{\prime }$ iff all tuples in $\mathrm{I}\left( {R}_{P}\right)$ that are referenced by $t$ are included in ${\mathbf{I}}^{\prime }\left( {R}_{P}\right)$ . This way,it generalizes the notion of an "induced subgraph" and we can call ${\mathbf{I}}^{\prime }$ an induced sub-instance of $\mathbf{I}$ . Note that an induced sub-instance ${\mathbf{I}}^{\prime }$ is completely specified by ${\mathbf{I}}^{\prime }\left( {R}_{P}\right)$ . Besides,let $\left\lbrack  n\right\rbrack   = \{ 1,2,\ldots ,n\}$ .

设$\mathbf{I}$为$\mathbf{R}$上的数据库实例。对于任意$R \in  \mathbf{R}$，令$\mathbf{I}\left( R\right)$表示$\mathbf{I}$中$R$的关系实例。元组间的引用关系自然定义如下：对于$t \in  \mathbf{I}\left( R\right)$和${t}^{\prime } \in  \mathbf{I}\left( {R}^{\prime }\right)$，当(1)${R}^{\prime }$引用$R$且${t}^{\prime }$的外键等于$t$的主键；或(2)存在${t}^{\prime \prime }$使得${t}^{\prime }$引用${t}^{\prime \prime }$且${t}^{\prime \prime }$引用$t$时，称${t}^{\prime }$引用$t$。设$N = \left| {\mathbf{I}\left( {R}_{P}\right) }\right|$为隐私用户数量。对于$i \in  \left\lbrack  N\right\rbrack$，令${t}_{i}\left( \mathrm{I}\right)$表示$\mathbf{I}\left( {R}_{P}\right)$中第$i$个用户。当${\mathbf{I}}^{\prime }\left( {R}_{P}\right)  \subseteq  \mathbf{I}\left( {R}_{P}\right)$时记作${\mathbf{I}}^{\prime } \subseteq  \mathbf{I}$，且次关系中的每个元组$t$当且仅当被$t$引用的$\mathrm{I}\left( {R}_{P}\right)$中所有元组都包含在${\mathbf{I}}^{\prime }\left( {R}_{P}\right)$中时，该元组才包含于${\mathrm{I}}^{\prime }$。这推广了"诱导子图"概念，可称${\mathbf{I}}^{\prime }$为$\mathbf{I}$的诱导子实例。注意诱导子实例${\mathbf{I}}^{\prime }$完全由${\mathbf{I}}^{\prime }\left( {R}_{P}\right)$确定。此外，令$\left\lbrack  n\right\rbrack   = \{ 1,2,\ldots ,n\}$。

### 3.2 SJA Queries

### 3.2 SJA查询

We now define the class of queries considered. We start with a multi-way (natural) join:

现在定义所考虑的查询类别。从多路(自然)连接开始：

$$
J \mathrel{\text{:=}} {R}_{1}\left( {\mathbf{x}}_{1}\right)  \bowtie  \cdots  \bowtie  {R}_{n}\left( {\mathbf{x}}_{n}\right) , \tag{4}
$$

where ${R}_{1},\ldots ,{R}_{n}$ are relation names in $\mathbf{R}$ and each ${\mathbf{x}}_{i}$ is a set of arity $\left( {R}_{i}\right)$ variables. Let $\mathbf{x} \mathrel{\text{:=}}$ ${\mathbf{x}}_{1} \cup  \cdots  \cup  {\mathbf{x}}_{n}$ . When self-joins are present,there can be repeats among ${R}_{1},\ldots ,{R}_{n}$ ,i.e., ${R}_{i} = {R}_{j}$ (then we must have ${\mathbf{x}}_{i} \neq  {\mathbf{x}}_{j}$ ). The join is required to be complete [27],i.e.,if any ${R}_{i}$ in $J$ references another ${R}^{\prime }$ ,then ${R}^{\prime }$ must also be included in $J$ ,with its PK given a variable that is the same as the variable given to the corresponding FK of ${R}_{i}$ .

其中${R}_{1},\ldots ,{R}_{n}$是$\mathbf{R}$中的关系名，每个${\mathbf{x}}_{i}$是一组元数为$\left( {R}_{i}\right)$的变量。设$\mathbf{x} \mathrel{\text{:=}}$${\mathbf{x}}_{1} \cup  \cdots  \cup  {\mathbf{x}}_{n}$。当存在自连接时，${R}_{1},\ldots ,{R}_{n}$中可能出现重复项，即${R}_{i} = {R}_{j}$（此时必须满足${\mathbf{x}}_{i} \neq  {\mathbf{x}}_{j}$）。该连接要求是完备的[27]，即若$J$中任何${R}_{i}$引用了另一个${R}^{\prime }$，则${R}^{\prime }$也必须包含在$J$中，且其主键变量必须与${R}_{i}$对应外键的变量相同。

Let $M = \left| {J\left( \mathbf{I}\right) }\right|$ be the join size,let for $j \in  \left\lbrack  M\right\rbrack  ,{q}_{j}\left( \mathbf{I}\right)$ denotes the $j$ th join result. We say ${q}_{j}\left( \mathbf{I}\right)$ references ${t}_{i}\left( \mathrm{I}\right)$ iff ${q}_{j}\left( \mathrm{I}\right)  \bowtie  {t}_{i}\left( \mathrm{I}\right)  \neq  \varnothing$ . To better describe the referencing relationships between tuples and join results,we introduce the following sets of indices ${C}_{i}\left( \mathbf{I}\right)$ and ${D}_{j}\left( \mathbf{I}\right)$ for each $i \in  \left\lbrack  N\right\rbrack  ,j \in  \left\lbrack  M\right\rbrack$ :

设$M = \left| {J\left( \mathbf{I}\right) }\right|$为连接结果大小，对于$j \in  \left\lbrack  M\right\rbrack  ,{q}_{j}\left( \mathbf{I}\right)$，$j$表示第$j$个连接结果。当且仅当${q}_{j}\left( \mathrm{I}\right)  \bowtie  {t}_{i}\left( \mathrm{I}\right)  \neq  \varnothing$时，称${q}_{j}\left( \mathbf{I}\right)$引用${t}_{i}\left( \mathrm{I}\right)$。为更好描述元组与连接结果间的引用关系，我们为每个$i \in  \left\lbrack  N\right\rbrack  ,j \in  \left\lbrack  M\right\rbrack$引入以下索引集合${C}_{i}\left( \mathbf{I}\right)$和${D}_{j}\left( \mathbf{I}\right)$：

$$
{C}_{i}\left( \mathbf{I}\right)  \mathrel{\text{:=}} \left\{  {j : {q}_{j}\left( \mathbf{I}\right) \text{ references }{t}_{i}\left( \mathbf{I}\right) }\right\}  , \tag{5}
$$

$$
{D}_{j}\left( \mathbf{I}\right)  \mathrel{\text{:=}} \left\{  {i : {q}_{j}\left( \mathbf{I}\right) \text{ references }{t}_{i}\left( \mathbf{I}\right) }\right\}  . \tag{6}
$$

A JA or SJA query $Q$ aggregates over the join results $J\left( \mathrm{I}\right)$ . They can both be captured by introducing a function $\psi  : \operatorname{dom}\left( \mathbf{x}\right)  \rightarrow  \mathbb{N}$ and defining the query result on $\mathbf{I}$ as

JA或SJA查询$Q$对连接结果$J\left( \mathrm{I}\right)$进行聚合运算。通过引入函数$\psi  : \operatorname{dom}\left( \mathbf{x}\right)  \rightarrow  \mathbb{N}$并定义$\mathbf{I}$上的查询结果，可统一表示为：

$$
Q\left( \mathbf{I}\right)  \mathrel{\text{:=}} \mathop{\sum }\limits_{{j \in  \left\lbrack  M\right\rbrack  }}\psi \left( {{q}_{j}\left( \mathbf{I}\right) }\right) . \tag{7}
$$

Note that if there is a selection predicate,we can simply set $\psi \left( q\right)  = 0$ for any $q$ that does not satisfy the predicate. We also use the shorthand ${\psi }_{j}\left( \mathbf{I}\right)  \mathrel{\text{:=}} \psi \left( {{q}_{j}\left( \mathbf{I}\right) }\right)$ .

注意若存在选择谓词，可直接将不满足谓词的$q$对应$\psi \left( q\right)  = 0$设为零。另采用简记法${\psi }_{j}\left( \mathbf{I}\right)  \mathrel{\text{:=}} \psi \left( {{q}_{j}\left( \mathbf{I}\right) }\right)$。

Example 3.2. Continuing with Example 3.1, suppose we ask the following query: SELECT count(*) FROM Lineitem WHERE Lineitem.QTY > 10 Although this query does not have any explicit joins, we must make it complete, resulting in the following join:

例3.2 续例3.1，假设查询为：SELECT count(*) FROM Lineitem WHERE Lineitem.QTY > 10。虽然该查询无显式连接，但需使其完备化，最终生成如下连接：

$$
J = \text{Orders}\left( {\mathrm{{OK}},\mathrm{{CK}}}\right)  \bowtie  \text{Lineitem}\left( {\mathrm{{OK}},\mathrm{{SK}},\mathrm{{PtK}},\mathrm{{QTY}}}\right)  \bowtie  \text{Customer}\left( {\mathrm{{CK}},\mathrm{{NK}}}\right) 
$$

$$
 \bowtie  \operatorname{User}\left( \mathrm{{CK}}\right)  \bowtie  \operatorname{PartSupp}\left( {\mathrm{{PtK}},\mathrm{{SK}}}\right)  \bowtie  \text{Supplier}\left( {\mathrm{{SK}},\mathrm{{NK}}}\right)  \bowtie  \operatorname{User}\left( \mathrm{{SK}}\right) 
$$

Note how it introduces a self-join on User. Generally speaking, self-joins will be introduced as long as a join result references more than one private user. In fact, the join with the two copies of User has no effect on the join results; their purpose is to just mark CK and SK as variables representing the private users. To finish the query,we define $\psi \left( q\right)  = 1$ for each lineitem $q$ in the join result whose QTY is larger than 10,otherwise 0 . Then ${C}_{i}\left( \mathbf{I}\right)$ consists of all the lineitems belonging to the $i$ -th user (a customer or a supplier),and ${D}_{j}\left( \mathbf{I}\right)$ consists of all the users that the $j$ -th lineitem references (exactly one customer and one supplier in this case).

注意其对User表引入了自连接。一般而言，当连接结果引用多个私有用户时就会产生自连接。实际上，与两个User副本的连接不影响结果，其作用仅是标记CK和SK作为私有用户的变量。为完成查询，对QTY大于10的连接结果中的每个lineitem$q$定义$\psi \left( q\right)  = 1$，否则为0。${C}_{i}\left( \mathbf{I}\right)$包含属于第$i$个用户（客户或供应商）的所有lineitem，${D}_{j}\left( \mathbf{I}\right)$包含第$j$个lineitem引用的所有用户（本例中恰好一个客户和一个供应商）。

Example 3.3. Many graph pattern matching queries under node-DP [7, 9, 26] can be written as SJA queries on the schema $\mathbf{R} = \{ \operatorname{Node}\left( {I\underline{D}}\right) ,\operatorname{Edge}\left( {\operatorname{src},\operatorname{dst}}\right) \}$ ,where $\operatorname{src}$ ,dst are both FKs referencing ID. For example, the triangle counting query uses the join

例3.3 在节点差分隐私[7,9,26]下的许多图模式匹配查询可表示为模式$\mathbf{R} = \{ \operatorname{Node}\left( {I\underline{D}}\right) ,\operatorname{Edge}\left( {\operatorname{src},\operatorname{dst}}\right) \}$上的SJA查询，其中$\operatorname{src}$和dst均为引用ID的外键。例如三角计数查询使用连接

$$
\operatorname{Edge}\left( {A,B}\right)  \bowtie  \operatorname{Edge}\left( {B,C}\right)  \bowtie  \operatorname{Edge}\left( {C,A}\right)  \bowtie  \operatorname{Node}\left( A\right)  \bowtie  \operatorname{Node}\left( B\right)  \bowtie  \operatorname{Node}\left( C\right) 
$$

with $\psi \left( q\right)  = 1$ for all $q \in  J\left( \mathrm{I}\right)$ . Note that the 3 copies of Node are not really needed,but they are included (virtually) to signify that $\mathrm{A},\mathrm{B},\mathrm{C}$ all represent private users. For this query, ${C}_{i}\left( \mathrm{I}\right)$ includes all the triangles incident to the $i$ -th node,and ${D}_{j}\left( \mathbf{I}\right)$ is the 3 nodes forming the $j$ -th triangle.

与$\psi \left( q\right)  = 1$进行全量$q \in  J\left( \mathrm{I}\right)$匹配。需注意Node的三个副本并非必需，但(虚拟)包含它们是为了表明$\mathrm{A},\mathrm{B},\mathrm{C}$均代表隐私用户。对此查询，${C}_{i}\left( \mathrm{I}\right)$包含与第$i$个节点关联的所有三角形，${D}_{j}\left( \mathbf{I}\right)$构成第$j$个三角形的三个节点。

In this paper,we consider answering $d$ such queries $\mathbf{Q} = \left( {{Q}_{1},\ldots ,{Q}_{d}}\right)$ . We subscript them by $k$ , and generalize the notation above as ${N}_{k},{M}_{k},{J}_{k}\left( \mathbf{I}\right) ,{q}_{k,j}\left( \mathbf{I}\right) ,{\psi }_{k,j}\left( \mathbf{I}\right)$ ,etc. An important special case is group-by queries. Continuing with Example 3.2, suppose we add a GROUP BY OrderDate clause. Then $d =  \mid$ dom $\left( \text{OrderDate}\right)  \mid  ;{N}_{k},{M}_{k},{J}_{k}\left( \mathbf{I}\right) ,{q}_{k,j}\left( \mathbf{I}\right)$ are the same for all $k$ ,while ${\psi }_{k,j}\left( \mathbf{I}\right)$ has a different predicate OrderDate $= x$ ,where $x$ ranges over all the dates in $\operatorname{dom}$ (OrderDate). Nevertheless,all developments below will assume the general case where the $d$ queries can be completely different.

本文研究如何应答$d$个查询$\mathbf{Q} = \left( {{Q}_{1},\ldots ,{Q}_{d}}\right)$。我们通过$k$进行下标标注，并将上述符号泛化为${N}_{k},{M}_{k},{J}_{k}\left( \mathbf{I}\right) ,{q}_{k,j}\left( \mathbf{I}\right) ,{\psi }_{k,j}\left( \mathbf{I}\right)$等。分组查询是重要特例。延续例3.2，假设添加GROUP BY OrderDate子句，则$d =  \mid$定义域$\left( \text{OrderDate}\right)  \mid  ;{N}_{k},{M}_{k},{J}_{k}\left( \mathbf{I}\right) ,{q}_{k,j}\left( \mathbf{I}\right)$对所有$k$相同，而${\psi }_{k,j}\left( \mathbf{I}\right)$包含不同谓词OrderDate$= x$，其中$x$遍历$\operatorname{dom}$(OrderDate)中所有日期。下文所有推导均基于$d$个查询可完全不同的通用情形。

### 3.3 Differential Privacy

### 3.3 差分隐私

Definition 3.4 (Differential privacy). For $\varepsilon ,\delta  > 0$ ,an algorithm $\mathcal{M} : \mathcal{I} \rightarrow  \mathcal{Y}$ is $\left( {\varepsilon ,\delta }\right)$ -differentially private if for any neighboring instances $\mathbf{I} \sim  {\mathbf{I}}^{\prime } \in  \mathcal{I}$ and any subset of outputs $Y \subseteq  \mathcal{Y}$ ,

定义3.4(差分隐私)。对于$\varepsilon ,\delta  > 0$，算法$\mathcal{M} : \mathcal{I} \rightarrow  \mathcal{Y}$满足$\left( {\varepsilon ,\delta }\right)$-差分隐私，当且仅当对任意相邻实例$\mathbf{I} \sim  {\mathbf{I}}^{\prime } \in  \mathcal{I}$及输出子集$Y \subseteq  \mathcal{Y}$，

$$
\Pr \left\lbrack  {\mathcal{M}\left( \mathbf{I}\right)  \in  Y}\right\rbrack   \leq  {e}^{\varepsilon } \cdot  \Pr \left\lbrack  {\mathcal{M}\left( {\mathbf{I}}^{\prime }\right)  \in  Y}\right\rbrack   + \delta .
$$

The privacy parameter $\varepsilon$ is usually a constant from 0.1 to 10,while $\delta  \ll  1/N$ . To apply the DP definition to a concrete problem, we need to define the neighboring relationship. We adopt the user-DP policy in [27] for relational database,which defines two instances $\mathbf{I}$ and ${\mathbf{I}}^{\prime }$ as neighbors if ${\mathbf{I}}^{\prime }$ can be obtained from $\mathbf{I}$ by deleting some ${t}_{P} \in  \mathbf{I}\left( {R}_{P}\right)$ and all tuples referencing ${t}_{P}$ .

隐私参数$\varepsilon$通常取0.1至10之间的常数，而$\delta  \ll  1/N$。将DP定义应用于具体问题时需定义相邻关系。我们采用文献[27]的关系数据库用户-DP策略，若${\mathbf{I}}^{\prime }$可通过从$\mathbf{I}$删除某些${t}_{P} \in  \mathbf{I}\left( {R}_{P}\right)$及所有引用${t}_{P}$的元组获得，则视二者为相邻实例。

LEMMA 3.5 (LAPLACE MECHANISM [14]). Given a single query $Q : \mathcal{I} \rightarrow  \mathbb{R}$ ,let ${\mathrm{{GS}}}_{Q} \mathrel{\text{:=}} \mathop{\max }\limits_{{\mathbf{I} \sim  {\mathbf{I}}^{\prime }}}\left| {Q\left( \mathbf{I}\right)  - }\right|$ $Q\left( {\mathbf{I}}^{\prime }\right)  \mid$ . The mechanism

引理3.5(拉普拉斯机制[14])。给定单个查询$Q : \mathcal{I} \rightarrow  \mathbb{R}$，令${\mathrm{{GS}}}_{Q} \mathrel{\text{:=}} \mathop{\max }\limits_{{\mathbf{I} \sim  {\mathbf{I}}^{\prime }}}\left| {Q\left( \mathbf{I}\right)  - }\right|$$Q\left( {\mathbf{I}}^{\prime }\right)  \mid$。该机制

$$
\mathcal{M}\left( \mathrm{I}\right)  = Q\left( \mathrm{I}\right)  + \frac{{\mathrm{{GS}}}_{Q}}{\varepsilon } \cdot  Y,
$$

where $Y \sim  \operatorname{Lap}\left( 1\right)$ ,preserves $\left( {\varepsilon ,0}\right)$ -DP.

其中$Y \sim  \operatorname{Lap}\left( 1\right)$，能保持$\left( {\varepsilon ,0}\right)$-差分隐私（DP）。

For a vectored-valued query, the Gaussian mechanism is more commonly used:

对于向量值查询，高斯机制更为常用：

LEMMA 3.6 (GAUSSIAN MECHANISM [8,14]). Given a d-dimensional query $\mathrm{Q} : I \rightarrow  {\mathbb{R}}^{d}$ ,let ${\mathrm{{GS}}}_{\mathrm{Q}} \mathrel{\text{:=}} \mathop{\max }\limits_{{\mathbf{I} \sim  {\mathbf{I}}^{\prime }}}\begin{Vmatrix}{\mathbf{Q}\left( \mathbf{I}\right)  - \mathbf{Q}\left( {\mathbf{I}}^{\prime }\right) }\end{Vmatrix}$ . Then the mechanism

引理3.6（高斯机制[8,14]）。给定一个d维查询$\mathrm{Q} : I \rightarrow  {\mathbb{R}}^{d}$，设${\mathrm{{GS}}}_{\mathrm{Q}} \mathrel{\text{:=}} \mathop{\max }\limits_{{\mathbf{I} \sim  {\mathbf{I}}^{\prime }}}\begin{Vmatrix}{\mathbf{Q}\left( \mathbf{I}\right)  - \mathbf{Q}\left( {\mathbf{I}}^{\prime }\right) }\end{Vmatrix}$。则该机制

$$
\mathcal{M}\left( \mathrm{I}\right)  = \mathrm{Q}\left( \mathrm{I}\right)  + \sigma  \cdot  \mathrm{Y},
$$

where $\mathbf{Y} \sim  \mathcal{N}\left( {0,{\mathbf{I}}_{d \times  d}}\right)$ ,preserves $\left( {\frac{{\mathrm{{GS}}}_{\mathbb{Q}}^{2}}{2{\sigma }^{2}} + \frac{{\mathrm{{GS}}}_{\mathbb{Q}}}{\sigma }\sqrt{2\log \left( {1/\delta }\right) },\delta }\right)$ -DP for any $\delta  > 0$ .

其中$\mathbf{Y} \sim  \mathcal{N}\left( {0,{\mathbf{I}}_{d \times  d}}\right)$，能保持$\left( {\frac{{\mathrm{{GS}}}_{\mathbb{Q}}^{2}}{2{\sigma }^{2}} + \frac{{\mathrm{{GS}}}_{\mathbb{Q}}}{\sigma }\sqrt{2\log \left( {1/\delta }\right) },\delta }\right)$-差分隐私（DP）对于任意$\delta  > 0$。

Thus,given $\varepsilon ,\delta$ ,one can set $\frac{{\mathrm{{GS}}}_{\mathrm{Q}}^{2}}{2{\sigma }^{2}} + \frac{{\mathrm{{GS}}}_{\mathrm{Q}}}{\sigma }\sqrt{2\log \left( {1/\delta }\right) } = \varepsilon$ and solve for $\sigma$ ,and we denote the solution as $\sigma \left( {\varepsilon ,\delta }\right)$ . For $\varepsilon  = O\left( 1\right) ,\sigma \left( {\varepsilon ,\delta }\right)  = O\left( {\sqrt{\log \left( {1/\delta }\right) }/\varepsilon  \cdot  {\mathrm{{GS}}}_{\mathrm{Q}}}\right)$ ,so the Gaussian mechanism achieves an ${\ell }_{2}$ error of $\widetilde{O}\left( {\sqrt{d} \cdot  {\mathrm{{GS}}}_{Q}}\right)$ .

因此，给定$\varepsilon ,\delta$，可以设置$\frac{{\mathrm{{GS}}}_{\mathrm{Q}}^{2}}{2{\sigma }^{2}} + \frac{{\mathrm{{GS}}}_{\mathrm{Q}}}{\sigma }\sqrt{2\log \left( {1/\delta }\right) } = \varepsilon$并求解$\sigma$，我们将解记为$\sigma \left( {\varepsilon ,\delta }\right)$。对于$\varepsilon  = O\left( 1\right) ,\sigma \left( {\varepsilon ,\delta }\right)  = O\left( {\sqrt{\log \left( {1/\delta }\right) }/\varepsilon  \cdot  {\mathrm{{GS}}}_{\mathrm{Q}}}\right)$，高斯机制实现的${\ell }_{2}$误差为$\widetilde{O}\left( {\sqrt{d} \cdot  {\mathrm{{GS}}}_{Q}}\right)$。

Both the Laplace and the Gaussian mechanism require a small GS. If it is large or unbounded, one may consider the local sensitivity ${\mathrm{{LS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)  = \mathop{\max }\limits_{{{\mathrm{I}}^{\prime } : {\mathrm{I}}^{\prime } \sim  \mathrm{I}}}\begin{Vmatrix}{\mathrm{Q}\left( \mathrm{I}\right)  - \mathrm{Q}\left( {\mathrm{I}}^{\prime }\right) }\end{Vmatrix}$ ,and try to obtain a DP upper bound of ${\mathrm{{LS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$ before applying the mechanism. Karwa et al. [25] show how this can be done for the Laplace mechanism. Here we obtain a similar result for the Gaussian mechanism:

拉普拉斯机制和高斯机制都需要较小的全局敏感度（GS）。若敏感度较大或无界，可考虑局部敏感度${\mathrm{{LS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)  = \mathop{\max }\limits_{{{\mathrm{I}}^{\prime } : {\mathrm{I}}^{\prime } \sim  \mathrm{I}}}\begin{Vmatrix}{\mathrm{Q}\left( \mathrm{I}\right)  - \mathrm{Q}\left( {\mathrm{I}}^{\prime }\right) }\end{Vmatrix}$，并在应用机制前尝试获得${\mathrm{{LS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$的差分隐私上界。Karwa等人[25]展示了如何在拉普拉斯机制中实现这一点。本文针对高斯机制得出类似结论：

LEMMA 3.7 (SECOND-ORDER GAUSSIAN MECHANISM). Given a $d$ -dimensional query $\mathbf{Q} : I \rightarrow  {\mathbb{R}}^{d}$ , suppose there is an $\left( {{\varepsilon }_{1},{\delta }_{1}}\right)$ -DP mechanism that outputs an ${\widehat{\mathrm{{LS}}}}_{\mathrm{Q}}\left( \mathrm{I}\right)  \geq  {\mathrm{{LS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$ with probability at least $1 - {\delta }_{2}$ ,then the mechanism

引理3.7（二阶高斯机制）。给定一个$d$维查询$\mathbf{Q} : I \rightarrow  {\mathbb{R}}^{d}$，假设存在一个$\left( {{\varepsilon }_{1},{\delta }_{1}}\right)$-差分隐私机制能以至少$1 - {\delta }_{2}$概率输出${\widehat{\mathrm{{LS}}}}_{\mathrm{Q}}\left( \mathrm{I}\right)  \geq  {\mathrm{{LS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$，则该机制

$$
\mathcal{M}\left( \mathbf{I}\right)  = \mathbf{Q}\left( \mathbf{I}\right)  + {\widehat{\mathrm{{LS}}}}_{\mathbf{Q}}\left( \mathbf{I}\right)  \cdot  \sigma \left( {{\varepsilon }_{2},{\delta }_{3}}\right)  \cdot  \mathbf{Y},
$$

where $\mathbf{Y} \sim  \mathcal{N}\left( {0,{\mathbf{I}}_{d \times  d}}\right)$ ,preserves $\left( {{\varepsilon }_{1} + {\varepsilon }_{2},{\delta }_{1} + {e}^{{\varepsilon }_{1}}{\delta }_{2} + {e}^{{\varepsilon }_{1}}{\delta }_{3}}\right)$ -DP.

其中$\mathbf{Y} \sim  \mathcal{N}\left( {0,{\mathbf{I}}_{d \times  d}}\right)$保留了$\left( {{\varepsilon }_{1} + {\varepsilon }_{2},{\delta }_{1} + {e}^{{\varepsilon }_{1}}{\delta }_{2} + {e}^{{\varepsilon }_{1}}{\delta }_{3}}\right)$-差分隐私(DP)。

Proof. Similar to the proof of Lemma 4.4 in [25].

证明。类似于文献[25]中引理4.4的证明。

However,in a relational database, ${\mathrm{{LS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$ is also unbounded,since one can construct an ${\mathrm{I}}^{\prime }$ by adding to $\mathbf{I}$ a user ${t}_{P}$ with arbitrarily many tuples all referencing ${t}_{P}$ . Thus,Dong et al. [10] propose to consider the downward sensitivity:

然而在关系型数据库中，${\mathrm{{LS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$同样无界——因为可以通过向$\mathbf{I}$添加包含任意多元组（均引用${t}_{P}$）的用户${t}_{P}$来构造${\mathrm{I}}^{\prime }$。因此，Dong等人[10]提出考虑向下敏感性：

$$
{\mathrm{{DS}}}_{\mathbf{Q}}\left( \mathbf{I}\right)  = \mathop{\max }\limits_{{{\mathbf{I}}^{\prime } \subseteq  \mathbf{I},{\mathbf{I}}^{\prime } \sim  \mathbf{I}}}\begin{Vmatrix}{\mathbf{Q}\left( {\mathbf{I}}^{\prime }\right)  - \mathbf{Q}\left( \mathbf{I}\right) }\end{Vmatrix}.
$$

Note by adding the restriction ${\mathrm{I}}^{\prime } \subseteq  \mathrm{I},{\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$ corresponds to the maximum contribution of any user in $\mathbf{I}$ ,which is always finite. Specifically,for each user ${t}_{i}\left( \mathbf{I}\right)  \in  \mathbf{I}\left( {R}_{P}\right)$ ,let

注意通过添加限制条件${\mathrm{I}}^{\prime } \subseteq  \mathrm{I},{\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$，对应$\mathbf{I}$中任何用户的最大贡献值（该值始终有限）。具体而言，对每个用户${t}_{i}\left( \mathbf{I}\right)  \in  \mathbf{I}\left( {R}_{P}\right)$，令

$$
{S}_{k,i}\left( \mathbf{I}\right)  = \mathop{\sum }\limits_{{j \in  {C}_{k,i}\left( \mathbf{I}\right) }}{\psi }_{k,j}\left( \mathbf{I}\right)  \tag{8}
$$

be the contribution of ${t}_{i}\left( \mathrm{I}\right)$ to ${Q}_{k}$ . The contributions of ${t}_{i}\left( \mathrm{I}\right)$ to all queries are thus a $d$ -dimensional vector ${\mathbf{S}}_{i}\left( \mathbf{I}\right)  = \left( {{S}_{1,i}\left( \mathbf{I}\right) ,\ldots ,{S}_{d,i}\left( \mathbf{I}\right) }\right)$ . Then we have ${\operatorname{DS}}_{\mathbf{Q}}\left( \mathbf{I}\right)  = \mathop{\max }\limits_{{i \in  \left\lbrack  N\right\rbrack  }}\begin{Vmatrix}{{\mathbf{S}}_{i}\left( \mathbf{I}\right) }\end{Vmatrix}$ .

表示${t}_{i}\left( \mathrm{I}\right)$对${Q}_{k}$的贡献度。因此${t}_{i}\left( \mathrm{I}\right)$对所有查询的贡献构成$d$维向量${\mathbf{S}}_{i}\left( \mathbf{I}\right)  = \left( {{S}_{1,i}\left( \mathbf{I}\right) ,\ldots ,{S}_{d,i}\left( \mathbf{I}\right) }\right)$，此时有${\operatorname{DS}}_{\mathbf{Q}}\left( \mathbf{I}\right)  = \mathop{\max }\limits_{{i \in  \left\lbrack  N\right\rbrack  }}\begin{Vmatrix}{{\mathbf{S}}_{i}\left( \mathbf{I}\right) }\end{Vmatrix}$。

### 3.4 Sparse Vector Technique

### 3.4 稀疏向量技术

The Sparse Vector Technique (SVT) [16] has as input a (possibly infinite) sequence of 1-dimensional queries, ${f}_{1}\left( \mathrm{I}\right) ,{f}_{2}\left( \mathrm{I}\right) ,\ldots$ ,where each has global sensitivity 1,and a threshold $T$ . It targets to find the first query whose answer is above $T$ . SVT has been shown to satisfy $\varepsilon$ -DP with the following utility guarantee.

稀疏向量技术(SVT)[16]的输入包含（可能无限的）一维查询序列${f}_{1}\left( \mathrm{I}\right) ,{f}_{2}\left( \mathrm{I}\right) ,\ldots$（各查询全局敏感度为1）及阈值$T$，旨在找出首个结果超过$T$的查询。SVT已被证明满足$\varepsilon$-差分隐私(DP)，并具有如下效用保证。

LEMMA 3.8 ([12,16]). If there exists a $k$ such that ${f}_{k}\left( D\right)  \geq  T + \frac{6}{\varepsilon }\log \left( {2/\beta }\right)$ ,then with probability at least $1 - \beta$ ,SVT returns an $\ell  \leq  k$ such that ${f}_{\ell }\left( D\right)  \geq  T - \frac{6}{\varepsilon }\log \left( {{2k}/\beta }\right)$ .

引理3.8([12,16])。若存在$k$使得${f}_{k}\left( D\right)  \geq  T + \frac{6}{\varepsilon }\log \left( {2/\beta }\right)$，则以至少$1 - \beta$概率，SVT将返回满足${f}_{\ell }\left( D\right)  \geq  T - \frac{6}{\varepsilon }\log \left( {{2k}/\beta }\right)$的$\ell  \leq  k$。

## 4 MULTIPLE SELF-JOIN-FREE QUERIES

## 4 多重无自连接查询

We start with the simpler case of $d$ self-join-free queries. We observe that it is equivalent to the sum estimation problem in $d$ dimensions: Given $N$ vectors in $d$ dimensions ${\mathbf{x}}_{1},\ldots ,{\mathbf{x}}_{N}$ ,we wish to estimate $\mathop{\sum }\limits_{{i \in  \left\lbrack  N\right\rbrack  }}{\mathbf{x}}_{i}$ under DP where neighboring instances differ by one vector. For the forward direction,we just set ${\mathbf{x}}_{i} \mathrel{\text{:=}} {\mathbf{S}}_{i}\left( \mathbf{I}\right)$ . Since there is no self-join,each join result only references one user, then adding/removing one user in I is the same as adding/removing vector. For the backward direction,we simply construct a single table with $d$ columns storing these vectors,and the $k$ -th query asks for the sum on the $k$ -th column.

我们从无自连接查询($d$ self-join-free queries)这一更简单的情形入手。观察到该问题等价于$d$维空间中的求和估计问题：给定$d$维空间中的$N$个向量${\mathbf{x}}_{1},\ldots ,{\mathbf{x}}_{N}$，需在差分隐私(DP)条件下估计$\mathop{\sum }\limits_{{i \in  \left\lbrack  N\right\rbrack  }}{\mathbf{x}}_{i}$，其中相邻实例仅相差一个向量。正向推导时，只需设定${\mathbf{x}}_{i} \mathrel{\text{:=}} {\mathbf{S}}_{i}\left( \mathbf{I}\right)$。由于无自连接特性，每个连接结果仅关联单个用户，因此在I中增减用户等同于增减向量。逆向推导时，只需构建包含$d$个列的单表存储这些向量，第$k$个查询即要求第$k$列的和。

This equivalence has two immediate consequences. First, the lower bound on the sum estimation problem is also a lower bound for the multi-query problem, which of course also holds for the more difficult case of self-joins:

该等价关系直接衍生两个推论：首先，求和估计问题的下界也是多查询问题的下界，这一结论自然适用于更复杂的自连接情形：

THEOREM 4.1 ([21, 23]). For the multi-query problem, no DP mechanism can achieve an error smaller than $\widetilde{\Omega }\left( {\sqrt{d} \cdot  {\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }\right)$ for all $\mathrm{I}$ .

定理4.1([21,23])。对于多查询问题，任何差分隐私(DP)机制都无法为所有$\mathrm{I}$实现小于$\widetilde{\Omega }\left( {\sqrt{d} \cdot  {\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }\right)$的误差。

Secondly, any sum estimation mechanism can also be used for self-join-free queries. However, all existing mechanisms assume that $\begin{Vmatrix}{{\mathrm{S}}_{i}\left( \mathrm{I}\right) }\end{Vmatrix} \leq  {\mathrm{{GS}}}_{\mathrm{Q}}$ for a predefined ${\mathrm{{GS}}}_{\mathrm{Q}}$ . Under this assumption, the best result is ${\left\lbrack  {21}\right\rbrack  }^{2}$

其次，任何求和估计机制均可用于无自连接查询。但现有机制均假设$\begin{Vmatrix}{{\mathrm{S}}_{i}\left( \mathrm{I}\right) }\end{Vmatrix} \leq  {\mathrm{{GS}}}_{\mathrm{Q}}$相对于预定义${\mathrm{{GS}}}_{\mathrm{Q}}$成立。在此假设下，最佳结果为${\left\lbrack  {21}\right\rbrack  }^{2}$

$$
O\left( {{\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)  \cdot  \left( {\sqrt{d} + \sqrt{\log \left( {\mathrm{{GS}}}_{\mathrm{Q}}\right) \log \log \left( {\mathrm{{GS}}}_{\mathrm{Q}}\right) }}\right)  \cdot  \sqrt{\log \left( {1/\delta }\right) }/\varepsilon }\right) .
$$

Below,we show how to remove this assumption,i.e.,we allow ${\mathrm{{GS}}}_{\mathrm{Q}} = \infty$ . Meanwhile,we also improve the error bound to

下文将展示如何消除该假设，即允许${\mathrm{{GS}}}_{\mathrm{Q}} = \infty$。同时将误差界提升至

$$
O\left( {{\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)  \cdot  \left( {\sqrt{d\log \left( {1/\delta }\right) } + \log \log \left( {{\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }\right) }\right) /\varepsilon }\right) .
$$

Our idea is to extend the 1-dimensional mechanism in [12] to $d$ dimensions. Given a truncation threshold $r \geq  0$ ,the truncated query result $\mathrm{Q}\left( {\mathrm{I},r}\right)$ is defined as

我们的核心思想是将文献[12]中的一维机制扩展至$d$维空间。给定截断阈值$r \geq  0$，截断查询结果$\mathrm{Q}\left( {\mathrm{I},r}\right)$定义为

$$
\mathrm{Q}\left( {\mathbf{I},r}\right)  \mathrel{\text{:=}} \mathop{\sum }\limits_{{i \in  \left\lbrack  N\right\rbrack  }}\left( {\min \left( {1,\frac{r}{\begin{Vmatrix}{\mathrm{\;S}}_{i}\left( \mathbf{I}\right) \end{Vmatrix}}}\right)  \cdot  {\mathrm{S}}_{i}\left( \mathbf{I}\right) }\right) .
$$

It is easy to see that $Q\left( {I,r}\right)$ has the global sensitivity $r$ ,so the Gaussian mechanism can be applied. Note that by using $r = {\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$ ,no data is truncated and the Gaussian mechanism achieves the optimal error $\widetilde{O}\left( {\sqrt{d} \cdot  {\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }\right)$ . However,since ${\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$ depends on $\mathrm{I}$ ,using it directly will breach privacy. Then the idea is to find a privatized $r$ that is as close to ${\mathrm{{DS}}}_{\mathbf{Q}}\left( \mathbf{I}\right)  = \mathop{\max }\limits_{{i \in  \left\lbrack  N\right\rbrack  }}\begin{Vmatrix}{{\mathbf{S}}_{i}\left( \mathbf{I}\right) }\end{Vmatrix}$ as possible.

易证$Q\left( {I,r}\right)$具有全局敏感度$r$，故可采用高斯机制。需注意使用$r = {\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$时未截断任何数据，此时高斯机制可达成最优误差$\widetilde{O}\left( {\sqrt{d} \cdot  {\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }\right)$。但由于${\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$依赖于$\mathrm{I}$，直接使用会破坏隐私性。因此关键在于寻找尽可能接近${\mathrm{{DS}}}_{\mathbf{Q}}\left( \mathbf{I}\right)  = \mathop{\max }\limits_{{i \in  \left\lbrack  N\right\rbrack  }}\begin{Vmatrix}{{\mathbf{S}}_{i}\left( \mathbf{I}\right) }\end{Vmatrix}$的隐私化$r$。

For any $r \geq  0$ ,define

对于任意$r \geq  0$，定义

$$
\operatorname{Count}\left( {\mathbf{I},r}\right)  = \left| \left\{  {i : \begin{Vmatrix}{{\mathbf{S}}_{i}\left( \mathbf{I}\right) }\end{Vmatrix} \leq  r}\right\}  \right| .
$$

Then consider the sequence of queries $\operatorname{Count}\left( {\mathrm{I},r}\right)  - N$ for $r = 1,2,4,\ldots$ . It is clear that each such query has global sensitivity 1 . We use SVT with privacy budget $\varepsilon /{10}$ to find the first $\widetilde{r}$ such that Count $\left( {\mathbf{I},\widetilde{r}}\right)  - N >  - \frac{60}{\varepsilon }\log \frac{4}{\beta }$ ,where $\beta$ will be failure probability. After finding such an $\widetilde{r}$ ,we invoke the Gaussian mechanism with global sensitivity $\widetilde{r}$ and privacy budget ${9\varepsilon }/{10.}^{3}$ The detailed algorithm is shown in Algorithm 1.

考虑查询序列$\operatorname{Count}\left( {\mathrm{I},r}\right)  - N$对$r = 1,2,4,\ldots$的情况。显然每个此类查询的全局敏感度为1。我们使用隐私预算$\varepsilon /{10}$的稀疏向量技术(SVT)来找到首个满足Count$\left( {\mathbf{I},\widetilde{r}}\right)  - N >  - \frac{60}{\varepsilon }\log \frac{4}{\beta }$的$\widetilde{r}$，其中$\beta$表示失败概率。找到该$\widetilde{r}$后，我们调用全局敏感度为$\widetilde{r}$、隐私预算为${9\varepsilon }/{10.}^{3}$的高斯机制。详细算法如算法1所示。

<!-- Media -->

Algorithm 1: Multiple self-join-free queries.

算法1：无自连接的多重查询

---

Input: $\mathrm{I},\varepsilon ,\beta$

输入：$\mathrm{I},\varepsilon ,\beta$

	$\widetilde{i} \leftarrow  \operatorname{SVT}\left( {-\frac{60}{\varepsilon }\log \frac{4}{\beta },\frac{\varepsilon }{10},\operatorname{Count}\left( {\mathbf{I},{2}^{0}}\right)  - N,\operatorname{Count}\left( {\mathbf{I},{2}^{1}}\right)  - N,\ldots }\right) ;$

$\widetilde{r} \leftarrow  {2}^{\widetilde{i} - 1}$

$\widetilde{\mathrm{Q}}\left( \mathbf{I}\right)  = \mathrm{Q}\left( {\mathbf{I},\widetilde{r}}\right)  + \widetilde{r} \cdot  \sigma \left( {\frac{9\varepsilon }{10},\delta }\right)  \cdot  \mathbf{Y},\mathbf{Y} \sim  \mathcal{N}\left( {0,{\mathbf{I}}_{d \times  d}}\right) ;$

	return $\widetilde{\mathrm{Q}}\left( \mathrm{I}\right)$ ;

	返回$\widetilde{\mathrm{Q}}\left( \mathrm{I}\right)$;

---

<!-- Media -->

---

<!-- Footnote -->

${}^{2}$ [21] states the result under zCDP [8]; here we translate their result to $\left( {\varepsilon ,\delta }\right)$ -DP.

${}^{2}$[21]给出了zCDP[8]框架下的结果；此处我们将其转化为$\left( {\varepsilon ,\delta }\right)$-差分隐私(DP)。

${}^{3}$ The $\varepsilon$ is split into $\varepsilon /{10}$ and ${9\varepsilon }/{10}$ since the error is dominated by the noise instead of bias and we want to use more privacy budget when adding the noise.

${}^{3}$将$\varepsilon$拆分为$\varepsilon /{10}$和${9\varepsilon }/{10}$，因为误差主要由噪声而非偏差主导，故我们希望在添加噪声时分配更多隐私预算。

<!-- Footnote -->

---

THEOREM 4.2. Algorithm 1 satisfies $\left( {\varepsilon ,\delta }\right)$ -DP,and with probability at least $1 - \beta$ ,

定理4.2. 算法1满足$\left( {\varepsilon ,\delta }\right)$-差分隐私，且概率不低于$1 - \beta$时：

$$
\parallel \widetilde{\mathrm{Q}}\left( \mathrm{I}\right)  - \mathrm{Q}\left( \mathrm{I}\right) \parallel  = O\left( {\frac{{\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }{\varepsilon } \cdot  \left( {\log \frac{\log \left( {\mathrm{{DS}}}_{\mathrm{Q}}\right) }{\beta } + \sqrt{d\log \frac{1}{\delta }\log \frac{1}{\beta }}}\right) }\right) .
$$

Proof. The privacy guarantee follows directly from basic composition. For the utility, by Lemma 3.8,with probability at least $1 - \frac{\beta }{2}$ ,we have (1) Count $\left( {\mathrm{I},\widetilde{r}}\right)  \geq  N - O\left( {\frac{1}{\varepsilon } \cdot  \log \frac{\log \left( {\mathrm{{DS}}}_{\mathrm{O}}\right) }{\beta }}\right)$ ; and (2) $\widetilde{r} \leq  {\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$ . The first result implies the introduced bias is $O\left( {\frac{{\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }{\varepsilon } \cdot  \log \frac{\log \left( {\mathrm{{DS}}}_{\mathrm{Q}}\right) }{\beta }}\right)$ . By combining the second result and the tail bound of Gaussian distribution, with probability at least $1 - \frac{\beta }{2}$ ,the added noise is $O\left( {\frac{{\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }{\varepsilon } \cdot  \sqrt{d\log \left( {1/\delta }\right) \log \left( {1/\beta }\right) }}\right)$ .

证明. 隐私保障直接源于基本组合定理。对于效用，根据引理3.8，在概率不低于$1 - \frac{\beta }{2}$时：(1)计数结果$\left( {\mathrm{I},\widetilde{r}}\right)  \geq  N - O\left( {\frac{1}{\varepsilon } \cdot  \log \frac{\log \left( {\mathrm{{DS}}}_{\mathrm{O}}\right) }{\beta }}\right)$；(2)$\widetilde{r} \leq  {\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$。第一项结果意味着引入的偏差为$O\left( {\frac{{\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }{\varepsilon } \cdot  \log \frac{\log \left( {\mathrm{{DS}}}_{\mathrm{Q}}\right) }{\beta }}\right)$。结合第二项结果与高斯分布的尾界，在概率不低于$1 - \frac{\beta }{2}$时，所添加噪声为$O\left( {\frac{{\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) }{\varepsilon } \cdot  \sqrt{d\log \left( {1/\delta }\right) \log \left( {1/\beta }\right) }}\right)$。

## 5 MULTIPLE QUERIES WITH SELF-JOINS

## 5 含自连接的多重查询

### 5.1 Why Self-joins are Hard

### 5.1 自连接的难点

When self-joins are present, or more fundamentally, when a join result references more than one user, a number of difficulties arise. First, since the contributions of the users overlap, the problem is no longer equivalent to sum estimation. Second, as pointed out in [10], the truncation mechanism fails: The query on all users $i$ with ${S}_{i}\left( \mathbf{I}\right)  \leq  r$ still has unbounded global sensitivity. To address this issue, [10] replaces this "hard" truncation with a "soft" truncation [26] for the case of a single query. The idea is that each join result may contribute a part of its full ${\psi }_{j}\left( \mathrm{I}\right)$ ,so that the total contribution from any user is bounded by $r$ . This can be formulated as an LP,where the variable ${z}_{j}$ denotes the partial contribution from the $j$ -th join result:

当存在自连接时，或更本质地说，当连接结果涉及多个用户时，会产生若干难题。首先，由于用户贡献存在重叠，问题不再等同于求和估计。其次，如文献[10]指出，截断机制会失效：对所有用户$i$且${S}_{i}\left( \mathbf{I}\right)  \leq  r$的查询仍具有无界全局敏感度。为解决此问题，文献[10]针对单查询情况用"软"截断[26]替代"硬"截断。其核心思想是每个连接结果可贡献其${\psi }_{j}\left( \mathrm{I}\right)$值的部分比例，使得任何用户的总贡献不超过$r$。这可通过线性规划表述，其中变量${z}_{j}$表示第$j$个连接结果的部分贡献：

$$
\max Q\left( {\mathbf{I},r}\right)  = \mathop{\sum }\limits_{{j \in  \left\lbrack  M\right\rbrack  }}{z}_{j}
$$

$$
\text{s.t.}\mathop{\sum }\limits_{{j \in  {C}_{i}\left( \mathrm{I}\right) }}{z}_{j} \leq  r,\;i \in  \left\lbrack  N\right\rbrack  ,
$$

$$
0 \leq  {z}_{j} \leq  {\psi }_{j}\left( \mathbf{I}\right) ,\;j \in  \left\lbrack  M\right\rbrack  .
$$

The truncated query answer,denoted $Q\left( {\mathrm{I},r}\right)$ ,is set to be the optimal solution of this LP,which can be shown to have global sensitivity $r$ ,so the Laplace mechanism can be applied. Then,[10] further proposes a mechanism to privately select an $r$ to achieve the optimal error $\widetilde{O}\left( {{\mathrm{{DS}}}_{Q}\left( \mathrm{I}\right) }\right)$ .

截断查询答案（记为$Q\left( {\mathrm{I},r}\right)$）被设定为该线性规划的最优解，可证明其具有全局敏感度$r$，因此可应用拉普拉斯机制。随后，文献[10]进一步提出一种隐私选择$r$的机制，以实现最优误差$\widetilde{O}\left( {{\mathrm{{DS}}}_{Q}\left( \mathrm{I}\right) }\right)$。

This LP can be naturally extended to multiple queries: ${z}_{j}$ and ${\psi }_{j}\left( \mathrm{I}\right)$ both become $d$ -dimensional vectors,the first constraint imposes a bound $r$ on the ${\ell }_{2}$ norm of $\sum {z}_{j}$ ,and the second constraint becomes an element-wise inequality. Meanwhile, $\mathbf{Q}\left( {\mathbf{I},r}\right)  = \sum {z}_{j}$ also becomes a vector,and we may try to maximize its norm. This turns the LP in a quadratic program (QP), which is still efficiently solvable. But the critical issue is that $\mathrm{Q}\left( {\mathrm{I},r}\right)$ has high local sensitivity,as illustrated in the following example.

该线性规划可自然扩展至多查询场景：${z}_{j}$和${\psi }_{j}\left( \mathrm{I}\right)$均变为$d$维向量，第一约束条件对$\sum {z}_{j}$的${\ell }_{2}$范数施加$r$限制，第二约束条件转为逐元素不等式。同时，$\mathbf{Q}\left( {\mathbf{I},r}\right)  = \sum {z}_{j}$也变为向量，我们可尝试最大化其范数。这将线性规划转化为二次规划(QP)，仍可高效求解。但关键问题在于$\mathrm{Q}\left( {\mathrm{I},r}\right)$具有高局部敏感度，如下例所示。

Example 5.1. Consider the query in Example 3.2 with GROUP BY OrderDate. Suppose there are only two different dates on OrderDate,so we have just $d = 2$ queries. Consider an instance $\mathbf{I}$ that has $N/2$ suppliers ${s}_{1},{s}_{2},\cdots {s}_{N/2}$ and $N/2$ customers ${c}_{1},{c}_{2},\cdots {c}_{N/2}$ . Each lineitem is purchased by one customer and supplied by a supplier, and the detailed construction is shown in Figure 2, where solid lines denote the lineitems on day 1 and dashed lines for day 2 . Next,we construct ${\mathbf{I}}^{\prime }$ by deleting ${s}_{1}$ (and the associated lineitem) and construct ${\mathbf{I}}^{\prime \prime }$ by further deleting ${c}_{N/2}$ . Note that we have $\mathbf{I} \sim  {\mathbf{I}}^{\prime } \sim  {\mathbf{I}}^{\prime \prime }$ .

示例5.1. 考虑示例3.2中按OrderDate分组的查询。假设OrderDate仅有两个不同日期，因此仅有$d = 2$个查询。考虑实例$\mathbf{I}$包含$N/2$个供应商${s}_{1},{s}_{2},\cdots {s}_{N/2}$和$N/2$个客户${c}_{1},{c}_{2},\cdots {c}_{N/2}$。每个订单明细由一位客户购买并通过一个供应商供货，具体构造如图2所示，其中实线表示第1天的订单明细，虚线表示第2天。接着，通过删除${s}_{1}$（及关联订单明细）构建${\mathbf{I}}^{\prime }$，再通过进一步删除${c}_{N/2}$构建${\mathbf{I}}^{\prime \prime }$。注意此时满足$\mathbf{I} \sim  {\mathbf{I}}^{\prime } \sim  {\mathbf{I}}^{\prime \prime }$。

Suppose we set $r = 1$ . We see that $\parallel \mathbf{Q}\left( {\mathbf{I},1}\right) \parallel$ is maximized by keeping all lineitems on day 1, yielding $\mathbf{Q}\left( {\mathbf{I},1}\right)  = \left( {N/2,0}\right)$ ,while $\begin{Vmatrix}{\mathbf{Q}\left( {{\mathbf{I}}^{\prime \prime },1}\right) }\end{Vmatrix}$ is maximized by keeping all lineitems on day 2,yielding $\mathbf{Q}\left( {{\mathbf{I}}^{\prime \prime },1}\right)  = \left( {0,N/2 - 1}\right)$ . We see that although $\parallel \mathbf{Q}\left( {\mathbf{I},1}\right) \parallel  - \parallel \mathbf{Q}\left( {{\mathbf{I}}^{\prime \prime },1}\right) \parallel$ is small, $\parallel \mathbf{Q}\left( {\mathbf{I},1}\right)  - \mathbf{Q}\left( {{\mathbf{I}}^{\prime \prime },1}\right) \parallel$ is large,which means that one of $\parallel \mathrm{Q}\left( {\mathrm{I},1}\right)  - \mathrm{Q}\left( {{\mathrm{I}}^{\prime },1}\right) \parallel$ and $\parallel \mathrm{Q}\left( {{\mathrm{I}}^{\prime },1}\right)  - \mathrm{Q}\left( {{\mathrm{I}}^{\prime \prime },1}\right) \parallel$ must be large. Fundamentally, the reason is that although the LP (or QP) has low sensitivity in its optimal value $\parallel \mathbf{Q}\left( {\mathbf{I},r}\right) \parallel$ ,it does not necessarily imply a low sensitivity on the optimal vector solution $\mathbf{Q}\left( {\mathbf{I},r}\right)$ , except in one dimension. However,the Gaussian mechanism needs a low sensitivity on $\mathrm{Q}\left( {\mathbf{I},r}\right)$ ,not $\parallel \mathbf{Q}\left( {\mathbf{I},r}\right) \parallel$ .

假设我们设定$r = 1$。可以看出，若将所有行项目保留在第1天，可使$\parallel \mathbf{Q}\left( {\mathbf{I},1}\right) \parallel$最大化，得到$\mathbf{Q}\left( {\mathbf{I},1}\right)  = \left( {N/2,0}\right)$；而将所有行项目保留在第2天则会使$\begin{Vmatrix}{\mathbf{Q}\left( {{\mathbf{I}}^{\prime \prime },1}\right) }\end{Vmatrix}$最大化，得到$\mathbf{Q}\left( {{\mathbf{I}}^{\prime \prime },1}\right)  = \left( {0,N/2 - 1}\right)$。虽然$\parallel \mathbf{Q}\left( {\mathbf{I},1}\right) \parallel  - \parallel \mathbf{Q}\left( {{\mathbf{I}}^{\prime \prime },1}\right) \parallel$数值较小，但$\parallel \mathbf{Q}\left( {\mathbf{I},1}\right)  - \mathbf{Q}\left( {{\mathbf{I}}^{\prime \prime },1}\right) \parallel$数值较大，这意味着$\parallel \mathrm{Q}\left( {\mathrm{I},1}\right)  - \mathrm{Q}\left( {{\mathrm{I}}^{\prime },1}\right) \parallel$和$\parallel \mathrm{Q}\left( {{\mathrm{I}}^{\prime },1}\right)  - \mathrm{Q}\left( {{\mathrm{I}}^{\prime \prime },1}\right) \parallel$中必有一个较大值。本质上，这是因为线性规划（或二次规划）的最优值$\parallel \mathbf{Q}\left( {\mathbf{I},r}\right) \parallel$灵敏度较低时，并不能保证最优向量解$\mathbf{Q}\left( {\mathbf{I},r}\right)$的灵敏度也低（一维情况除外）。然而高斯机制要求的是对$\mathrm{Q}\left( {\mathbf{I},r}\right)$而非$\parallel \mathbf{Q}\left( {\mathbf{I},r}\right) \parallel$具有低灵敏度。

<!-- Media -->

<!-- figureText: supplier customer lineitems on day 1 lineitems on day 2 ${s}_{N/2 - 2}$ ${s}_{N/2 - 1}$ ${s}_{N/2}$ ${C}_{2}$ ${c}_{3}$ ${c}_{N/2 - 2}$ ${c}_{N/2 - 1}$ ${s}_{N/2 - 2}$ ${s}_{N/2 - 1}$ ${s}_{N/2}$ ${C}_{2}$ ${c}_{3}$ ${c}_{N/2 - 2}$ ${c}_{N/2 - 1}$ ${c}_{N/2}$ -->

<img src="https://cdn.noedgeai.com/01965992-99d2-7937-99a4-2c1821ef6ce9_9.jpg?x=218&y=264&w=1117&h=345&r=0"/>

Fig. 2. An example showing $Q\left( {I,r}\right)$ has large sensitivity.

图2. 示例展示$Q\left( {I,r}\right)$具有高敏感度

<!-- Media -->

### 5.2 An Exponential-time Algorithm

### 5.2 指数时间算法

To address the issue above,we take a different approach to defining $Q\left( {I,r}\right)$ so that it has bounded sensitivity. First, define

为解决上述问题，我们采用不同方法定义$Q\left( {I,r}\right)$以限制其敏感度。首先定义

$$
E\left( {\mathbf{I},r}\right)  \mathrel{\text{:=}} \mathop{\max }\limits_{{{\mathbf{I}}^{\prime \prime } \subseteq  \mathbf{I},{\operatorname{DS}}_{Q}\left( {\mathbf{I}}^{\prime \prime }\right)  \leq  r}}\left| {{\mathbf{I}}^{\prime \prime }\left( {R}_{P}\right) }\right| ,
$$

i.e., the maximum number of users in any induced sub-instance of I such that no user's contribution is more than $r$ in ${\ell }_{2}$ norm. Note that for self-join-free queries, $E\left( {\mathrm{I},r}\right)  = \operatorname{Count}\left( {\mathrm{I},r}\right)$ . When there are self-joins,we have $E\left( {\mathbf{I},r}\right)  \geq  \operatorname{Count}\left( {\mathbf{I},r}\right)$ ,since removing one user may also reduce the contributions of other users. Exactly due to this reason, $\operatorname{Count}\left( {\mathrm{I},r}\right)$ has unbounded sensitivity for self-joins, which is why our self-join-free algorithm no longer works. On the other hand, we will show that $E\left( {\mathbf{I},r}\right)$ has sensitivity 1,as desired. However,computing $E\left( {\mathbf{I},r}\right)$ can take exponential time: Even for the simple query $J = \operatorname{Edge}\left( {\mathrm{A},\mathrm{B}}\right)  \bowtie  \operatorname{Node}\left( \mathrm{A}\right)  \bowtie  \operatorname{Node}\left( \mathrm{B}\right)$ with $\psi \left( q\right)  = 1$ for all $q \in  J\left( \mathrm{I}\right)$ (i.e., counting the number of edges in a graph under node-DP), $E\left( {\mathbf{I},r}\right)$ is exactly the size of the maximum induced subgraph with degree constraint $r$ ,which is a classical NP-hard problem. We will leave the computational issue to Section 5.3, while focusing on privacy for now.

即在实例I的任何诱导子实例中，满足${\ell }_{2}$范数下用户贡献不超过$r$的最大用户数。需注意，对于无自连接查询，$E\left( {\mathrm{I},r}\right)  = \operatorname{Count}\left( {\mathrm{I},r}\right)$。存在自连接时，$E\left( {\mathbf{I},r}\right)  \geq  \operatorname{Count}\left( {\mathbf{I},r}\right)$，因为移除一个用户可能同时减少其他用户的贡献。正是这个原因导致$\operatorname{Count}\left( {\mathrm{I},r}\right)$对自连接具有无界敏感度，使得我们的无自连接算法失效。但我们将证明$E\left( {\mathbf{I},r}\right)$的敏感度为1。然而计算$E\left( {\mathbf{I},r}\right)$可能需要指数时间：即使对于简单查询$J = \operatorname{Edge}\left( {\mathrm{A},\mathrm{B}}\right)  \bowtie  \operatorname{Node}\left( \mathrm{A}\right)  \bowtie  \operatorname{Node}\left( \mathrm{B}\right)$（所有$q \in  J\left( \mathrm{I}\right)$条件下$\psi \left( q\right)  = 1$，即节点差分隐私下的图边计数），$E\left( {\mathbf{I},r}\right)$等同于满足度约束$r$的最大诱导子图规模，这是个经典NP难问题。计算问题留待5.3节讨论，本节聚焦隐私性。

Next, define

接下来定义

$$
F\left( {\mathbf{I},r}\right)  \mathrel{\text{:=}} E\left( {\mathbf{I},r}\right)  - \left| {\mathbf{I}\left( {R}_{P}\right) }\right| ,
$$

i.e., $- F\left( {\mathrm{I},r}\right)$ is the minimum number of users that need to be removed so that the contribution from any user is at most $r$ . The following lemma is obvious:

即$- F\left( {\mathrm{I},r}\right)$是为使任何用户贡献不超过$r$需移除的最小用户数。显然有：

LEMMA 5.2. For any $r$ and any $\mathbf{I},F\left( {\mathbf{I},r}\right)  \leq  0$ . If $r \geq  {\mathrm{{DS}}}_{\mathbf{Q}}\left( \mathbf{I}\right)$ ,then $F\left( {\mathbf{I},r}\right)  = 0$ .

引理5.2. 对任意$r$和$\mathbf{I},F\left( {\mathbf{I},r}\right)  \leq  0$，若$r \geq  {\mathrm{{DS}}}_{\mathbf{Q}}\left( \mathbf{I}\right)$，则$F\left( {\mathbf{I},r}\right)  = 0$。

More importantly,we show that $F\left( {\mathbf{I},r}\right)$ has sensitivity 1 :

更重要的是，我们证明$F\left( {\mathbf{I},r}\right)$具有敏感度1：

LEMMA 5.3. For any $r$ and any $\mathbf{I} \sim  {\mathbf{I}}^{\prime },{\mathbf{I}}^{\prime } \subseteq  \mathbf{I}$ ,we have

引理5.3. 对任意$r$和$\mathbf{I} \sim  {\mathbf{I}}^{\prime },{\mathbf{I}}^{\prime } \subseteq  \mathbf{I}$，有

$$
F\left( {{\mathbf{I}}^{\prime },r}\right)  - 1 \leq  F\left( {\mathbf{I},r}\right)  \leq  F\left( {{\mathbf{I}}^{\prime },r}\right) .
$$

Proof. Let ${N}^{\prime } = \left| {{\mathbf{I}}^{\prime }\left( {R}_{P}\right) }\right|$ and $\mathbf{I}\left( {R}_{P}\right)  = {\mathbf{I}}^{\prime }\left( {R}_{P}\right)  \cup  {t}_{N}\left( \mathbf{I}\right)$ . On one hand,it is trivial to see for any $r \geq  0,E\left( {\mathbf{I},r}\right)  \geq  E\left( {{\mathbf{I}}^{\prime },r}\right)$ since any ${\mathbf{I}}^{\prime \prime } \subseteq  {\mathbf{I}}^{\prime }$ also has ${\mathbf{I}}^{\prime \prime } \subseteq  \mathbf{I}$ .

证明. 设${N}^{\prime } = \left| {{\mathbf{I}}^{\prime }\left( {R}_{P}\right) }\right|$和$\mathbf{I}\left( {R}_{P}\right)  = {\mathbf{I}}^{\prime }\left( {R}_{P}\right)  \cup  {t}_{N}\left( \mathbf{I}\right)$。一方面显然对任意$r \geq  0,E\left( {\mathbf{I},r}\right)  \geq  E\left( {{\mathbf{I}}^{\prime },r}\right)$成立，因任何${\mathbf{I}}^{\prime \prime } \subseteq  {\mathbf{I}}^{\prime }$也满足${\mathbf{I}}^{\prime \prime } \subseteq  \mathbf{I}$。

On the other hand,given any $r \geq  0$ ,let

另一方面，给定任意$r \geq  0$，设

$$
{\mathbf{I}}^{ * } = \underset{{\mathbf{I}}^{\prime \prime } \subseteq  \mathbf{I},{\operatorname{DS}}_{\mathrm{Q}}\left( {\mathbf{I}}^{\prime \prime }\right)  \leq  r}{\arg \max }\left| {\mathbf{I}}^{\prime \prime }\right| .
$$

Proc. ACM Manag. Data, Vol. 1, No. 2, Article 123. Publication date: June 2023. Then,we can construct a ${\mathbf{I}}^{*\prime }$ from ${\mathbf{I}}^{ * }$ by deleting all tuples referencing ${t}_{N}\left( \mathbf{I}\right)$ . Then, ${\mathbf{I}}^{*\prime } \subseteq  {\mathbf{I}}^{\prime }$ , ${\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathbf{I}}^{*\prime }\right)  \leq  {\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathbf{I}}^{ * }\right)  \leq  r$ and $\left| {{\mathbf{I}}^{*\prime }\left( {R}_{P}\right) }\right|  = \left| {{\mathbf{I}}^{ * }\left( {R}_{P}\right) }\right|  - 1$ . And this further means, $E\left( {{\mathbf{I}}^{\prime },r}\right)  \geq  E\left( {\mathbf{I},r}\right)  - 1$ . Finally,combining $N = {N}^{\prime } + 1$ ,the lemma follows.

《ACM数据管理汇刊》第1卷第2期，文章编号123，2023年6月出版。随后，我们可以通过删除所有引用${t}_{N}\left( \mathbf{I}\right)$的元组，从${\mathbf{I}}^{ * }$构建一个${\mathbf{I}}^{*\prime }$。继而，${\mathbf{I}}^{*\prime } \subseteq  {\mathbf{I}}^{\prime }$、${\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathbf{I}}^{*\prime }\right)  \leq  {\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathbf{I}}^{ * }\right)  \leq  r$与$\left| {{\mathbf{I}}^{*\prime }\left( {R}_{P}\right) }\right|  = \left| {{\mathbf{I}}^{ * }\left( {R}_{P}\right) }\right|  - 1$成立。这意味着$E\left( {{\mathbf{I}}^{\prime },r}\right)  \geq  E\left( {\mathbf{I},r}\right)  - 1$。最终结合$N = {N}^{\prime } + 1$，该引理得证。

Because $F\left( {\mathbf{I},r}\right)$ has sensitivity 1,we can feed $F\left( {\mathbf{I},1}\right) ,F\left( {\mathbf{I},2}\right) ,F\left( {\mathbf{I},4}\right) ,\ldots$ into SVT with threshold $- O\left( {\log \left( {1/\beta }\right) /\varepsilon }\right)$ . Using a similar argument as for our self-join-free algorithm,we can show that this will return an $\widetilde{r}$ that is close to ${\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$ .

由于$F\left( {\mathbf{I},r}\right)$的敏感度为1，可将$F\left( {\mathbf{I},1}\right) ,F\left( {\mathbf{I},2}\right) ,F\left( {\mathbf{I},4}\right) ,\ldots$输入至阈值为$- O\left( {\log \left( {1/\beta }\right) /\varepsilon }\right)$的SVT（稀疏向量技术）中。采用类似无自连接算法的论证方法，可证明该操作将返回一个接近${\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$的$\widetilde{r}$。

It turns out that $E\left( {\mathbf{I},r}\right)$ is not only useful for finding the truncation threshold $\widetilde{r}$ ,it also yields a new definition of the truncated query answer $\mathbf{Q}\left( {\mathbf{I},r}\right)$ with bounded sensitivity. For any $r \geq  0$ , define

事实证明，$E\left( {\mathbf{I},r}\right)$不仅可用于确定截断阈值$\widetilde{r}$，还能为具有有限敏感度的截断查询结果$\mathbf{Q}\left( {\mathbf{I},r}\right)$提供新的定义。对于任意$r \geq  0$，定义如下：

$$
\mathrm{Q}\left( {\mathbf{I},r}\right)  \mathrel{\text{:=}} \mathrm{Q}\left( {{\mathbf{I}}^{ * }\left( r\right) }\right) \text{,where }{\mathbf{I}}^{ * }\left( r\right)  = \underset{{\mathbf{I}}^{\prime \prime } \subseteq  \mathbf{I},{\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathbf{I}}^{\prime \prime }\right)  \leq  r}{\arg \max }\left| {{\mathbf{I}}^{\prime \prime }\left( {R}_{P}\right) }\right| \text{. } \tag{9}
$$

Note that ${\mathbf{I}}^{ * }\left( r\right)$ may not be unique. In this case,we use an arbitrary tie-breaker (e.g.,the ${\mathbf{I}}^{\prime \prime }$ with the lexicographically smallest user IDs) to fix an ${\mathbf{I}}^{ * }\left( r\right)$ .

需注意${\mathbf{I}}^{ * }\left( r\right)$可能不唯一。此时我们采用任意决胜规则（例如选择用户ID字典序最小的${\mathbf{I}}^{\prime \prime }$）来确定${\mathbf{I}}^{ * }\left( r\right)$。

Example 5.4. Following the Example 5.1 and assuming $N = {6c}$ for some $c \in  \mathbb{N}$ ,for I,when $r = 1$ ,we have $E\left( {\mathbf{I},1}\right)  = {2N}/3,{\mathbf{I}}^{ * }\left( 1\right)$ is obtained by deleting ${s}_{3 * i - 1}$ ’s and ${c}_{3 * i}$ ’s for all $i \in  \left\lbrack  {N/6}\right\rbrack$ , and $\mathbf{Q}\left( {\mathbf{I},1}\right)  = \left( {N/6,N/6}\right)$ . When $r \geq  2,E\left( {\mathbf{I},r}\right)  = N,{\mathbf{I}}^{ * }\left( r\right)  = \mathbf{I}$ ,and $\mathbf{Q}\left( {\mathbf{I},r}\right)  = \left( {N/2,N/2 - 1}\right)$ . Meanwhile,for ${\mathbf{I}}^{\prime \prime }$ ,when $r = 1$ ,we have $E\left( {{\mathbf{I}}^{\prime \prime },1}\right)  = {2N}/3 - 1,{\mathbf{I}}^{*\prime }\left( 1\right)$ is obtained by deleting ${c}_{3 * i - 1}$ ’s for all $i \in  \left\lbrack  {N/6}\right\rbrack$ and ${s}_{3 * j + 1}$ ’s for all $j \in  \left\lbrack  {N/6 - 1}\right\rbrack$ ,and $\mathbf{Q}\left( {{\mathbf{I}}^{\prime \prime },1}\right)  = \left( {N/6 - 1,N/6}\right)$ . When $r \geq  2$ , $E\left( {{\mathbf{I}}^{\prime \prime },r}\right)  = N - 2,{\mathbf{I}}^{*\prime }\left( r\right)  = {\mathbf{I}}^{\prime \prime }$ ,and $\mathrm{Q}\left( {{\mathbf{I}}^{\prime \prime },r}\right)  = \left( {N/2 - 2,N/2 - 1}\right) .$

例5.4. 延续例5.1的设定，假设$N = {6c}$对于某些$c \in  \mathbb{N}$，当$r = 1$时，我们通过删除所有$i \in  \left\lbrack  {N/6}\right\rbrack$对应的${s}_{3 * i - 1}$和${c}_{3 * i}$得到$E\left( {\mathbf{I},1}\right)  = {2N}/3,{\mathbf{I}}^{ * }\left( 1\right)$，且$\mathbf{Q}\left( {\mathbf{I},1}\right)  = \left( {N/6,N/6}\right)$。当$r \geq  2,E\left( {\mathbf{I},r}\right)  = N,{\mathbf{I}}^{ * }\left( r\right)  = \mathbf{I}$时，$\mathbf{Q}\left( {\mathbf{I},r}\right)  = \left( {N/2,N/2 - 1}\right)$。同时，对于${\mathbf{I}}^{\prime \prime }$，当$r = 1$时，我们通过删除所有$i \in  \left\lbrack  {N/6}\right\rbrack$对应的${c}_{3 * i - 1}$和所有$j \in  \left\lbrack  {N/6 - 1}\right\rbrack$对应的${s}_{3 * j + 1}$得到$E\left( {{\mathbf{I}}^{\prime \prime },1}\right)  = {2N}/3 - 1,{\mathbf{I}}^{*\prime }\left( 1\right)$，且$\mathbf{Q}\left( {{\mathbf{I}}^{\prime \prime },1}\right)  = \left( {N/6 - 1,N/6}\right)$。当$r \geq  2$时，$E\left( {{\mathbf{I}}^{\prime \prime },r}\right)  = N - 2,{\mathbf{I}}^{*\prime }\left( r\right)  = {\mathbf{I}}^{\prime \prime }$，且$\mathrm{Q}\left( {{\mathbf{I}}^{\prime \prime },r}\right)  = \left( {N/2 - 2,N/2 - 1}\right) .$

We bound the local sensitivity of $\mathrm{Q}\left( {\mathrm{I},r}\right)$ as follows.

我们对$\mathrm{Q}\left( {\mathrm{I},r}\right)$的局部敏感度进行如下界定。

LEMMA 5.5. Given $r \geq  0$ ,for any $\mathbf{I} \sim  {\mathbf{I}}^{\prime }$ ,

引理5.5. 给定$r \geq  0$，对于任意$\mathbf{I} \sim  {\mathbf{I}}^{\prime }$，

$$
\begin{Vmatrix}{\mathbf{Q}\left( {\mathbf{I},r}\right)  - \mathbf{Q}\left( {{\mathbf{I}}^{\prime },r}\right) }\end{Vmatrix} \leq  \left( {-{2F}\left( {\mathbf{I},r}\right)  + 2}\right)  \cdot  r.
$$

Proof. Here,we first consider the case $\mathbf{I}\left( {R}_{P}\right)  = {\mathbf{I}}^{\prime }\left( {R}_{P}\right)  \cup  \left\{  {{t}_{N}\left( \mathbf{I}\right) }\right\}$ .

证明. 此处我们首先考虑$\mathbf{I}\left( {R}_{P}\right)  = {\mathbf{I}}^{\prime }\left( {R}_{P}\right)  \cup  \left\{  {{t}_{N}\left( \mathbf{I}\right) }\right\}$的情况。

For convenience, let

为方便起见，设

$$
{\mathbf{I}}^{ * } = \underset{{\mathbf{I}}^{\prime \prime } \subseteq  \mathbf{I},{\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathbf{I}}^{\prime \prime }\right)  \leq  r}{\arg \max }\begin{Vmatrix}{{\mathbf{I}}^{\prime \prime }\left( {R}_{P}\right) }\end{Vmatrix},
$$

$$
{\mathbf{I}}^{{ * }^{\prime }} = \underset{{\mathbf{I}}^{\prime \prime } \subseteq  {\mathbf{I}}^{\prime },{\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathbf{I}}^{\prime \prime }\right)  \leq  r}{\arg \max }\begin{Vmatrix}{{\mathbf{I}}^{\prime \prime }\left( {R}_{P}\right) }\end{Vmatrix}.
$$

And define

并定义

$$
{\mathbf{I}}_{ \cap  }^{ * } = {\mathbf{I}}^{ * } \cap  {\mathbf{I}}^{ * }.
$$

First, by definition,

首先根据定义，

$$
\left\{  {\begin{array}{l} \left| {\mathbf{I}\left( {R}_{P}\right)  \smallsetminus  {\mathbf{I}}^{\prime }\left( {R}_{P}\right) }\right|  \leq  1 \\  \left| {{\mathbf{I}}^{\prime }\left( {R}_{P}\right)  \smallsetminus  {\mathbf{I}}^{{ * }^{\prime }}\left( {R}_{P}\right) }\right|  \leq   - F\left( {{\mathbf{I}}^{\prime },r}\right)  \end{array} \Rightarrow  \left| {\mathbf{I}\left( {R}_{P}\right)  \smallsetminus  {\mathbf{I}}^{{ * }^{\prime }}\left( {R}_{P}\right) }\right|  \leq   - F\left( {{\mathbf{I}}^{\prime },r}\right)  + 1.}\right. 
$$

Further recall ${\mathbf{I}}^{ * } \subseteq  \mathbf{I}$ ,we have

进一步回顾${\mathbf{I}}^{ * } \subseteq  \mathbf{I}$，可得

$$
\left| {{\mathbf{I}}^{ * }\left( {R}_{P}\right)  \smallsetminus  {\mathbf{I}}^{{ * }^{\prime }}\left( {R}_{P}\right) }\right|  \leq   - F\left( {{\mathbf{I}}^{\prime },r}\right)  + 1,
$$

which means

这意味着

$$
\left| {{\mathbf{I}}^{ * }\left( {R}_{P}\right)  \smallsetminus  {\mathbf{I}}_{ \cap  }^{ * }\left( {R}_{P}\right) }\right|  \leq   - F\left( {{\mathbf{I}}^{\prime },r}\right)  + 1.
$$

Recall ${\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathrm{I}}^{ * }\right)  \leq  r$ ,

回顾${\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathrm{I}}^{ * }\right)  \leq  r$，

$$
\begin{Vmatrix}{\mathbf{Q}\left( {\mathbf{I}}^{ * }\right)  - \mathbf{Q}\left( {\mathbf{I}}_{ \cap  }^{ * }\right) }\end{Vmatrix} \leq  \left( {-F\left( {{\mathbf{I}}^{\prime },r}\right)  + 1}\right)  \cdot  r. \tag{10}
$$

Symmetrically, we have

对称地，我们有

$$
\begin{Vmatrix}{\mathbf{Q}\left( {\mathbf{I}}^{{ * }^{\prime }}\right)  - \mathbf{Q}\left( {\mathbf{I}}_{ \cap  }^{ * }\right) }\end{Vmatrix} \leq   - F\left( {\mathbf{I},r}\right)  \cdot  r. \tag{11}
$$

Combining Lemma 5.3, (10), (11), the claim follows.

结合引理5.3、(10)式和(11)式，结论得证。

<!-- Media -->

Algorithm 2: Exponential-time mechanism for multiple queries with self-joins.

算法2：带自连接的多重查询指数时间机制。

---

Input: $\varepsilon ,\delta ,\beta$

输入：$\varepsilon ,\delta ,\beta$

	$\widetilde{i} \leftarrow  \operatorname{SVT}\left( {-\frac{30}{\varepsilon }\log \left( {6/\beta }\right) ,\frac{\varepsilon }{5},F\left( {\mathbf{I},{2}^{0}}\right) ,F\left( {\mathbf{I},{2}^{1}}\right) ,\ldots }\right) ;$

	$\widetilde{r} \leftarrow  {2}^{\widetilde{i} - 1}$

$T \leftarrow   - {2F}\left( {\mathbf{I},\widetilde{r}}\right)  + 2$

		$\widehat{T} \leftarrow  T + \operatorname{Lap}\left( \frac{5}{\varepsilon }\right)  + \frac{5}{\varepsilon }\log \left( {{e}^{{3\varepsilon }/5}/\delta }\right) ;$

		$\widetilde{\mathbb{Q}}\left( \mathbf{I}\right)  \leftarrow  \mathbb{Q}\left( {\mathbf{I},\widetilde{r}}\right)  + \widehat{T} \cdot  \sigma \left( {\frac{2\varepsilon }{5},\frac{\delta }{2{e}^{{3\varepsilon }/5}}}\right)  \cdot  \widetilde{r} \cdot  \mathbf{Y},\mathbf{Y} \sim  \mathcal{N}\left( {0,{\mathbf{I}}_{d \times  d}}\right) ;$

		return $\widetilde{Q}\left( I\right)$ ;

		返回$\widetilde{Q}\left( I\right)$；

---

<!-- Media -->

Similarly,for the other case,where ${\mathbf{I}}^{\prime }\left( {R}_{P}\right)  = \mathbf{I}\left( {R}_{P}\right)  \cup  \left\{  {{t}_{N + 1}\left( {\mathbf{I}}^{\prime }\right) }\right\}$ ,we have,

类似地，对于另一种情况，当${\mathbf{I}}^{\prime }\left( {R}_{P}\right)  = \mathbf{I}\left( {R}_{P}\right)  \cup  \left\{  {{t}_{N + 1}\left( {\mathbf{I}}^{\prime }\right) }\right\}$时，我们有

$$
\begin{Vmatrix}{\mathbf{Q}\left( {\mathbf{I}}^{ * }\right)  - \mathbf{Q}\left( {\mathbf{I}}_{ \cap  }^{ * }\right) }\end{Vmatrix} \leq   - F\left( {{\mathbf{I}}^{\prime },r}\right)  \cdot  r,
$$

$$
\begin{Vmatrix}{\mathbf{Q}\left( {\mathbf{I}}^{{ * }^{\prime }}\right)  - \mathbf{Q}\left( {\mathbf{I}}_{ \cap  }^{ * }\right) }\end{Vmatrix} \leq  \left( {-F\left( {\mathbf{I},r}\right)  + 1}\right)  \cdot  r.
$$

With a similar argument, the claim also follows.

通过类似论证，该结论同样成立。

Note that Lemma 5.5 does not imply a small sensitivity for all $r$ . In particular,if $r$ is very small, $- F\left( {\mathbf{I},r}\right)$ can be as large as $N$ ,resulting in the same issue as in Example 5.1. However,the crucial difference here is that we will only use an $\widetilde{r}$ returned by the SVT,which has $- F\left( {\mathbf{I},\widetilde{r}}\right)  = \widetilde{O}\left( 1\right)$ with high probability. We can further add a Laplace noise to it so that it becomes a high-probability DP upper bound on the local sensitivity, and then apply the second-order Gaussian mechanism. The detailed algorithm is shown in Algorithm 2.

注意引理5.5并不保证所有$r$都具有低敏感度。特别是当$r$极小时，$- F\left( {\mathbf{I},r}\right)$可能高达$N$，导致与示例5.1相同的问题。但关键区别在于，我们将仅使用SVT返回的$\widetilde{r}$——该值以高概率满足$- F\left( {\mathbf{I},\widetilde{r}}\right)  = \widetilde{O}\left( 1\right)$。我们可进一步添加拉普拉斯噪声，使其成为局部敏感度的高概率差分隐私上界，继而应用二阶高斯机制。完整算法如算法2所示。

THEOREM 5.6. For any $\varepsilon ,\delta ,\beta  > 0$ ,and any $\mathbf{I}$ ,Algorithm 2 satisfies $\left( {\varepsilon ,\delta }\right)$ -DP and returns a $\widetilde{\mathbf{Q}}\left( \mathbf{I}\right)$ such that with probability at least $1 - \beta$ ,

定理5.6. 对于任意$\varepsilon ,\delta ,\beta  > 0$和任意$\mathbf{I}$，算法2满足$\left( {\varepsilon ,\delta }\right)$-差分隐私，并以至少$1 - \beta$概率返回满足条件的$\widetilde{\mathbf{Q}}\left( \mathbf{I}\right)$：

$$
\parallel \widetilde{\mathrm{Q}}\left( \mathrm{I}\right)  - \mathrm{Q}\left( \mathrm{I}\right) \parallel 
$$

$$
 = O\left( {\frac{\sqrt{d\log \left( {1/\beta }\right) \log \left( {{e}^{\varepsilon }/\delta }\right) }}{{\varepsilon }^{2}} \cdot  \mathrm{{DS}}\left( \mathrm{I}\right)  \cdot  \left( {\log \frac{\log \left( {\mathrm{{DS}}\left( \mathrm{I}\right) }\right) }{\beta } + \log \left( {{e}^{\varepsilon }/\delta }\right) }\right) }\right) .
$$

Proof. Privacy: By Lemma 5.3,each $F\left( {\mathrm{I}, \cdot  }\right)$ has the sensitivity 1 thus line 1 and line 4 consume $\frac{\varepsilon }{5}$ and $\frac{2\varepsilon }{5}$ privacy budget respectively. By composition theory, $\widehat{T}$ preserves $\frac{3\varepsilon }{5}$ -DP and by the tail bound of Laplace distribution,with probability at least $1 - \frac{\delta }{2{e}^{\varepsilon /5}},\widehat{T} \geq  T$ . By Lemma 3.7, $\widetilde{Q}\left( \mathbf{I}\right)$ preserves $\left( {\varepsilon ,\delta }\right)$ -DP.

证明. 隐私性：根据引理5.3，每个$F\left( {\mathrm{I}, \cdot  }\right)$的敏感度为1，故步骤1和步骤4分别消耗$\frac{\varepsilon }{5}$与$\frac{2\varepsilon }{5}$隐私预算。根据组合定理，$\widehat{T}$保持$\frac{3\varepsilon }{5}$-差分隐私，且由拉普拉斯分布的尾界可知，概率至少为$1 - \frac{\delta }{2{e}^{\varepsilon /5}},\widehat{T} \geq  T$。根据引理3.7，$\widetilde{Q}\left( \mathbf{I}\right)$保持$\left( {\varepsilon ,\delta }\right)$-差分隐私。

Below we prove the utility bound. First,by Lemma 3.8,with probability at least $1 - \frac{\beta }{3}$ ,

下面证明效用界。首先由引理3.8，以至少$1 - \frac{\beta }{3}$概率满足：

$$
 - F\left( {\mathbf{I},\widetilde{r}}\right)  = O\left( {\frac{1}{\varepsilon }\log \frac{\log \left( {\mathrm{{DS}}\left( \mathbf{I}\right) }\right) }{\beta }}\right) , \tag{12}
$$

and

且

$$
\widetilde{r} \leq  2 \cdot  \operatorname{DS}\left( \mathrm{I}\right) \text{.} \tag{13}
$$

Second, by (12) and the definitions of DS(I) and $Q\left( {I,\widetilde{r}}\right)$ ,

其次，根据式(12)及DS(I)与$Q\left( {I,\widetilde{r}}\right)$的定义：

$$
\parallel \mathbf{Q}\left( \mathbf{I}\right)  - \mathbf{Q}\left( {\mathbf{I},\widetilde{r}}\right) \parallel  = O\left( {\frac{\mathrm{{DS}}\left( \mathbf{I}\right) }{\varepsilon }\log \frac{\log \left( {\mathrm{{DS}}\left( \mathbf{I}\right) }\right) }{\beta }}\right) . \tag{14}
$$

Third,by (12) and the tail bound of Laplace distribution,with probability at least $1 - \frac{\beta }{3}$ ,

第三，由式(12)及拉普拉斯分布尾界，以至少$1 - \frac{\beta }{3}$概率满足：

$$
\widehat{T} = O\left( {\frac{1}{\varepsilon }\log \frac{\log \left( {\mathrm{{DS}}\left( \mathbf{I}\right) }\right) }{\beta } + \frac{1}{\varepsilon }\log \left( {{e}^{\varepsilon }/\delta }\right) }\right) . \tag{15}
$$

Then, combine (13), (15) and the tail bound of Gaussian distribution, we have with probability at least $1 - \frac{\beta }{3}$ ,

结合式(13)、(15)及高斯分布尾界，以至少$1 - \frac{\beta }{3}$概率可得：

$$
\begin{Vmatrix}{\widetilde{Q}\left( \mathrm{I}\right)  - Q\left( {\mathrm{I}}^{ * }\right) }\end{Vmatrix}
$$

$$
 = O\left( {\frac{\sqrt{d\log \left( {1/\beta }\right) \log \left( {{e}^{\varepsilon }/\delta }\right) }}{{\varepsilon }^{2}} \cdot  \mathrm{{DS}}\left( \mathrm{I}\right)  \cdot  \left( {\log \frac{\log \left( \mathrm{{DS}}\right) }{\beta } + \log \left( {{e}^{\varepsilon }/\delta }\right) }\right) }\right) . \tag{16}
$$

Finally, the theorem follows by combining (14) and (16).

最终，联立式(14)与(16)即得定理。

### 5.3 A Polynomial-time Algorithm

### 5.3 多项式时间算法

In this section, we show how to reduce the running time of Algorithm 2 to polynomial without affecting its utility bound. The computational bottleneck is $E\left( {\mathrm{I},r}\right)$ . We borrow a popular technique from approximation algorithms: formulate $E\left( {\mathbf{I},r}\right)$ as an integer program,and solve its relaxed version.

本节展示如何在保持效用界的前提下，将算法2的运行时间降至多项式级别。计算瓶颈在于$E\left( {\mathrm{I},r}\right)$。我们采用近似算法的常用技术：将$E\left( {\mathbf{I},r}\right)$表述为整数规划，并求解其松弛版本。

Observe that any ${\mathbf{I}}^{\prime \prime } \subseteq  \mathbf{I}$ is specified by the users in ${\mathbf{I}}^{\prime \prime }\left( {R}_{P}\right)$ . We introduce a variable ${y}_{i} \in  \{ 0,1\}$ to indicate whether the $i$ -th user is included in ${\mathbf{I}}^{\prime \prime }\left( {R}_{P}\right)$ . The objective is thus to maximize $\mathop{\sum }\limits_{i}{y}_{i}$ . To link the ${y}_{i}$ ’s with the join results,we introduce a variable ${z}_{k,j} \in  \{ 0,1\}$ to indicate whether the join result ${q}_{k,j}\left( \mathbf{I}\right)  \in  {J}_{k}\left( {\mathbf{I}}^{\prime \prime }\right)$ ,for $k \in  \left\lbrack  d\right\rbrack  ,j \in  \left\lbrack  {{M}_{k}\left( \mathbf{I}\right) }\right\rbrack$ . Recall ${q}_{k,j}\left( \mathbf{I}\right)  \in  {J}_{k}\left( {\mathbf{I}}^{\prime \prime }\right)$ if and only if for any $i \in  {D}_{k,j}\left( \mathbf{I}\right) ,{t}_{i}\left( \mathbf{I}\right)  \in  {\mathbf{I}}^{\prime \prime }\left( {R}_{P}\right)$ . To capture this requirement,we add the following constraint:

注意到任何${\mathbf{I}}^{\prime \prime } \subseteq  \mathbf{I}$均由用户在${\mathbf{I}}^{\prime \prime }\left( {R}_{P}\right)$中指定。我们引入变量${y}_{i} \in  \{ 0,1\}$来指示第$i$个用户是否包含在${\mathbf{I}}^{\prime \prime }\left( {R}_{P}\right)$中。因此目标函数是最大化$\mathop{\sum }\limits_{i}{y}_{i}$。为了将${y}_{i}$与连接结果关联，我们引入变量${z}_{k,j} \in  \{ 0,1\}$来指示连接结果${q}_{k,j}\left( \mathbf{I}\right)  \in  {J}_{k}\left( {\mathbf{I}}^{\prime \prime }\right)$（对于$k \in  \left\lbrack  d\right\rbrack  ,j \in  \left\lbrack  {{M}_{k}\left( \mathbf{I}\right) }\right\rbrack$）的状态。当且仅当对于任意$i \in  {D}_{k,j}\left( \mathbf{I}\right) ,{t}_{i}\left( \mathbf{I}\right)  \in  {\mathbf{I}}^{\prime \prime }\left( {R}_{P}\right)$时，${q}_{k,j}\left( \mathbf{I}\right)  \in  {J}_{k}\left( {\mathbf{I}}^{\prime \prime }\right)$才成立。为满足该条件，我们添加如下约束：

$$
{z}_{k,j} \geq  \mathop{\sum }\limits_{{i \in  {D}_{k,j}\left( \mathbf{I}\right) }}{y}_{i} - \left| {{D}_{k,j}\left( \mathbf{I}\right) }\right|  + 1,k \in  \left\lbrack  d\right\rbrack  ,j \in  \left\lbrack  {M}_{k}\right\rbrack  .
$$

Note that the RHS is equal to 1 if ${y}_{i} = 1$ for all $i \in  {D}_{k,j}\left( \mathrm{I}\right)$ and 0 otherwise.

注意当所有$i \in  {D}_{k,j}\left( \mathrm{I}\right)$满足${y}_{i} = 1$时，等式右边值为1，否则为0。

Next,we need to express the constraint ${\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathbf{I}}^{\prime \prime }\right)  \leq  r$ . Recall ${\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathbf{I}}^{\prime \prime }\right)  = \mathop{\max }\limits_{i}\begin{Vmatrix}{{\mathbf{S}}_{i}\left( {\mathbf{I}}^{\prime \prime }\right) }\end{Vmatrix}$ ,where ${\mathbf{S}}_{i}\left( {\mathbf{I}}^{\prime \prime }\right)  = \left( {{S}_{1,i}\left( {\mathbf{I}}^{\prime \prime }\right) ,\ldots ,{S}_{d,i}\left( {\mathbf{I}}^{\prime \prime }\right) }\right)$ . Plugging (8) into the constraint ${\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathbf{I}}^{\prime \prime }\right)  \leq  r$ turns it into

接下来需要表示约束条件${\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathbf{I}}^{\prime \prime }\right)  \leq  r$。回顾${\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathbf{I}}^{\prime \prime }\right)  = \mathop{\max }\limits_{i}\begin{Vmatrix}{{\mathbf{S}}_{i}\left( {\mathbf{I}}^{\prime \prime }\right) }\end{Vmatrix}$（其中${\mathbf{S}}_{i}\left( {\mathbf{I}}^{\prime \prime }\right)  = \left( {{S}_{1,i}\left( {\mathbf{I}}^{\prime \prime }\right) ,\ldots ,{S}_{d,i}\left( {\mathbf{I}}^{\prime \prime }\right) }\right)$），将(8)式代入约束${\mathrm{{DS}}}_{\mathrm{Q}}\left( {\mathbf{I}}^{\prime \prime }\right)  \leq  r$后可转化为

$$
\mathop{\sum }\limits_{{k \in  \left\lbrack  d\right\rbrack  }}{\left( \mathop{\sum }\limits_{{j \in  {C}_{k,i}\left( \mathrm{\;I}\right) }}\left( {\psi }_{k,j}\left( \mathrm{I}\right)  \cdot  {z}_{k,j}\right) \right) }^{2} \leq  {r}^{2},i \in  \left\lbrack  N\right\rbrack  .
$$

Therefore, $E\left( {\mathbf{I},r}\right)$ is the optimal solution of the following integer program:

因此，$E\left( {\mathbf{I},r}\right)$是下列整数规划的最优解：

$$
\max \mathop{\sum }\limits_{{i \in  \left\lbrack  N\right\rbrack  }}{y}_{i}
$$

$$
\text{s.t.}{z}_{k,j} \geq  \mathop{\sum }\limits_{{i \in  {D}_{k,j}\left( \mathbf{I}\right) }}{y}_{i} - \left| {{D}_{k,j}\left( \mathbf{I}\right) }\right|  + 1,\;k \in  \left\lbrack  d\right\rbrack  ,j \in  \left\lbrack  {M}_{k}\right\rbrack  
$$

$$
\mathop{\sum }\limits_{{k \in  \left\lbrack  d\right\rbrack  }}{\left( \mathop{\sum }\limits_{{j \in  {C}_{k,i}\left( \mathrm{\;I}\right) }}\left( {\psi }_{k,j}\left( \mathrm{I}\right)  \cdot  {z}_{k,j}\right) \right) }^{2} \leq  {r}^{2},\;i \in  \left\lbrack  N\right\rbrack  ,
$$

$$
{y}_{i} \in  \{ 0,1\} \;i \in  \left\lbrack  N\right\rbrack  ,
$$

$$
{z}_{k,j} \in  \{ 0,1\} \;k \in  \left\lbrack  d\right\rbrack  ,j \in  \left\lbrack  {M}_{k}\right\rbrack  .
$$

By relaxing the integral constraint to ${y}_{i} \in  \left\lbrack  {0,1}\right\rbrack  ,{z}_{k,j} \in  \left\lbrack  {0,1}\right\rbrack$ ,this program turns into a QCQP. Note that only convex QCQPs can be solved efficiently, which is indeed the case for our QCQP, by observing that the quadratic constraint is positive semi-definite.

通过将整数约束松弛为${y}_{i} \in  \left\lbrack  {0,1}\right\rbrack  ,{z}_{k,j} \in  \left\lbrack  {0,1}\right\rbrack$，该规划问题转化为QCQP（二次约束二次规划）。需注意只有凸QCQP能高效求解，而通过观察可知我们的QCQP中二次约束是半正定的，故满足该条件。

Let ${\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$ be the optimal fractional solution of this QCQP,and let $\widehat{E}\left( {\mathbf{I},r}\right)  = \mathop{\sum }\limits_{i}{y}_{i}^{ * }$ . In approximation algorithms,one would then try to round ${\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$ into integers,and show that the rounded solution is not too far away from $\widehat{E}\left( {\mathbf{I},r}\right)$ . However,for the private query answering problem, there is no need to do the rounding as we will not return the join results anyway. Instead, we only need to return the privatized aggregated query answer. On the other hand, we must show that $\widehat{E}\left( {\mathbf{I},r}\right)$ preserves the three important sensitivity properties of $E\left( {\mathbf{I},r}\right)$ ,namely,Lemma 5.2-5.5.

设${\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$为该QCQP问题的最优分数解，且令$\widehat{E}\left( {\mathbf{I},r}\right)  = \mathop{\sum }\limits_{i}{y}_{i}^{ * }$。在近似算法中，通常会将${\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$舍入为整数解，并证明舍入后的解与$\widehat{E}\left( {\mathbf{I},r}\right)$的偏差可控。但对于私有查询应答问题，由于最终不返回连接结果，无需进行舍入操作，仅需返回经过隐私化处理的聚合查询答案。另一方面，必须证明$\widehat{E}\left( {\mathbf{I},r}\right)$保留了$E\left( {\mathbf{I},r}\right)$的三个关键敏感度特性，即引理5.2-5.5所述性质。

Letting $\widehat{F}\left( {\mathbf{I},r}\right)  = \widehat{E}\left( {\mathbf{I},r}\right)  - \left| {\mathbf{I}\left( {R}_{P}\right) }\right|$ . The first property is trivial:

令$\widehat{F}\left( {\mathbf{I},r}\right)  = \widehat{E}\left( {\mathbf{I},r}\right)  - \left| {\mathbf{I}\left( {R}_{P}\right) }\right|$。第一个性质是显然的：

LEMMA 5.7. For any $r$ and any $\mathbf{I},\widehat{F}\left( {\mathbf{I},r}\right)  \leq  0$ . If $r \geq  {\mathrm{{DS}}}_{\mathbf{Q}}\left( \mathbf{I}\right)$ ,then $\widehat{F}\left( {\mathbf{I},r}\right)  = 0$ .

引理5.7. 对于任意$r$和任意$\mathbf{I},\widehat{F}\left( {\mathbf{I},r}\right)  \leq  0$，若$r \geq  {\mathrm{{DS}}}_{\mathbf{Q}}\left( \mathbf{I}\right)$，则$\widehat{F}\left( {\mathbf{I},r}\right)  = 0$。

Now we prove that $\widehat{F}\left( {\mathbf{I},r}\right)$ also has sensitivity 1 .

现证明$\widehat{F}\left( {\mathbf{I},r}\right)$同样具有敏感度1。

LEMMA 5.8. For any $r$ and any $\mathbf{I} \sim  {\mathbf{I}}^{\prime },{\mathbf{I}}^{\prime } \subseteq  \mathbf{I}$ ,we have

引理5.8. 对于任意$r$和任意$\mathbf{I} \sim  {\mathbf{I}}^{\prime },{\mathbf{I}}^{\prime } \subseteq  \mathbf{I}$，均有

$$
\widehat{F}\left( {{\mathbf{I}}^{\prime },r}\right)  - 1 \leq  \widehat{F}\left( {\mathbf{I},r}\right)  \leq  \widehat{F}\left( {{\mathbf{I}}^{\prime },r}\right) .
$$

Proof. Let $\mathbf{I}\left( {R}_{P}\right)  = {\mathbf{I}}^{\prime }\left( {R}_{P}\right)  \cup  {t}_{N}\left( \mathbf{I}\right)$ . Assume that the join results referencing ${t}_{N}\left( \mathbf{I}\right)$ are put at the end of $J\left( \mathbf{I}\right)$ ,i.e.,for every $k \in  \left\lbrack  d\right\rbrack  ,{C}_{k,i}\left( \mathbf{I}\right)  = \left\{  {{M}_{k} - \left| {{C}_{k,i}\left( \mathbf{I}\right) }\right|  + 1,\ldots ,{M}_{k} - 1,{M}_{k}}\right\}$ .

证明. 设$\mathbf{I}\left( {R}_{P}\right)  = {\mathbf{I}}^{\prime }\left( {R}_{P}\right)  \cup  {t}_{N}\left( \mathbf{I}\right)$。假定引用${t}_{N}\left( \mathbf{I}\right)$的连接结果置于$J\left( \mathbf{I}\right)$末尾，即对每个$k \in  \left\lbrack  d\right\rbrack  ,{C}_{k,i}\left( \mathbf{I}\right)  = \left\{  {{M}_{k} - \left| {{C}_{k,i}\left( \mathbf{I}\right) }\right|  + 1,\ldots ,{M}_{k} - 1,{M}_{k}}\right\}$成立。

Let ${\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$ and ${\left\{  {y}_{i}^{{ * }^{\prime }}\right\}  }_{i},{\left\{  {z}_{k,j}^{{ * }^{\prime }}\right\}  }_{k,j}$ be the optimal fractional solutions of the QCQP on $\mathbf{I}$ and ${\mathrm{I}}^{\prime }$ ,respectively. On one hand,from ${\left\{  {y}_{i}^{{ * }^{\prime }}\right\}  }_{i},{\left\{  {z}_{k,j}^{{ * }^{\prime }}\right\}  }_{k,j}$ ,we can construct a valid solution of the QCQP for I by setting ${y}_{N}^{{ * }^{\prime }} = 0,{z}_{k,j}^{{ * }^{\prime }} = 0$ for any $k \in  \left\lbrack  d\right\rbrack  ,j \in  {C}_{k,N}\left( \mathbf{I}\right)$ . Thus,the optimal QCQP solution on $\mathbf{I}$ can only be higher.

设${\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$和${\left\{  {y}_{i}^{{ * }^{\prime }}\right\}  }_{i},{\left\{  {z}_{k,j}^{{ * }^{\prime }}\right\}  }_{k,j}$分别为在$\mathbf{I}$和${\mathrm{I}}^{\prime }$上的QCQP最优分数解。一方面，根据${\left\{  {y}_{i}^{{ * }^{\prime }}\right\}  }_{i},{\left\{  {z}_{k,j}^{{ * }^{\prime }}\right\}  }_{k,j}$，我们可以通过为任意$k \in  \left\lbrack  d\right\rbrack  ,j \in  {C}_{k,N}\left( \mathbf{I}\right)$设定${y}_{N}^{{ * }^{\prime }} = 0,{z}_{k,j}^{{ * }^{\prime }} = 0$来构造问题I的有效QCQP解。因此，$\mathbf{I}$上的最优QCQP解只会更高。

On the other hand,by removing ${y}_{N}^{ * },{z}_{k,j}^{ * }$ for any $k \in  \left\lbrack  k\right\rbrack  ,j \in  {C}_{k,N}\left( \mathbf{I}\right)$ for all $k \in  \left\lbrack  k\right\rbrack$ ,from ${\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$ ,we can obtain a valid solution of the QCQP on ${\mathbf{I}}^{\prime }$ . On this solution,we have $\mathop{\sum }\limits_{{i \in  \left\lbrack  {N - 1}\right\rbrack  \rbrack }}{y}_{i}^{ * } = E\left( {\mathbf{I},r}\right)  - {y}_{N}^{ * } \geq  E\left( {\mathbf{I},r}\right)  - 1$ ,which implies that $\widehat{E}\left( {{\mathbf{I}}^{\prime },r}\right)  \geq  \widehat{E}\left( {\mathbf{I},r}\right)  - 1$ .

另一方面，通过移除所有$k \in  \left\lbrack  k\right\rbrack$中任意$k \in  \left\lbrack  k\right\rbrack  ,j \in  {C}_{k,N}\left( \mathbf{I}\right)$对应的${y}_{N}^{ * },{z}_{k,j}^{ * }$，从${\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$出发，我们可以获得${\mathbf{I}}^{\prime }$上的有效QCQP解。在该解上，我们有$\mathop{\sum }\limits_{{i \in  \left\lbrack  {N - 1}\right\rbrack  \rbrack }}{y}_{i}^{ * } = E\left( {\mathbf{I},r}\right)  - {y}_{N}^{ * } \geq  E\left( {\mathbf{I},r}\right)  - 1$，这意味着$\widehat{E}\left( {{\mathbf{I}}^{\prime },r}\right)  \geq  \widehat{E}\left( {\mathbf{I},r}\right)  - 1$。

Finally,combining with $N = {N}^{\prime } + 1$ ,the lemma follows.

最终，结合$N = {N}^{\prime } + 1$，引理得证。

Lastly,we show how the optimal fractional solution ${\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$ also leads to a $\widehat{\mathrm{Q}}\left( {\mathbf{I},r}\right)$ with bounded local sensitivity as in Lemma 5.5. First, it is easy to see that there must exist an optimal solution in which the constraint on each ${z}_{k,j}^{ * }$ is tight,i.e.,

最后，我们说明最优分数解${\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$如何如引理5.5所述，导出一个具有有限局部敏感度的$\widehat{\mathrm{Q}}\left( {\mathbf{I},r}\right)$。首先容易看出，必然存在一个最优解使得每个${z}_{k,j}^{ * }$的约束条件是紧致的，即

$$
{z}_{k,j}^{ * } = \max \left( {0,\mathop{\sum }\limits_{{i \in  {D}_{k,j}\left( \mathrm{I}\right) }}{y}_{i}^{ * } - \left| {{D}_{k,j}\left( \mathrm{I}\right) }\right|  + 1}\right) . \tag{17}
$$

If not,we could lower ${z}_{k,j}^{ * }$ to make it tight without violating the quadratic constraint or changing the objective. If there are still multiple optimal fractional solutions, we pick one using an arbitrary tie-breaker. Then,we define $\widehat{\mathrm{Q}}\left( {\mathrm{I},r}\right)$ as the query answers using the optimal fractional solution,i.e.,

若非如此，我们可以降低${z}_{k,j}^{ * }$使其紧致，同时不违反二次约束或改变目标值。若仍存在多个最优分数解，则采用任意决胜规则选取其一。随后，我们将$\widehat{\mathrm{Q}}\left( {\mathrm{I},r}\right)$定义为采用最优分数解得到的查询答案，即

$$
\widehat{\mathbf{Q}}\left( {\mathbf{I},r}\right)  = \mathbf{Q}\left( {\mathbf{I}}^{ * }\right)  = \left( {{Q}_{1}\left( {\mathbf{I}}^{ * }\right) ,{Q}_{2}\left( {\mathbf{I}}^{ * }\right) ,\ldots ,{Q}_{d}\left( {\mathbf{I}}^{ * }\right) }\right) ,
$$

$$
\text{where}{Q}_{k}\left( {\mathbf{I}}^{ * }\right)  = \mathop{\sum }\limits_{{j \in  \left\lbrack  {M}_{k}\right\rbrack  }}\left( {{z}_{k,j}^{ * } \cdot  {\psi }_{k,j}\left( \mathbf{I}\right) }\right) ,k \in  \left\lbrack  d\right\rbrack  \text{.} \tag{18}
$$

Example 5.9. Following the Example 5.4,when $r = 1$ ,for both $\mathbf{I}$ and ${\mathbf{I}}^{\prime }$ ,we have all ${z}_{k,j} = \sqrt{2}/2$ , and all ${y}_{i} = \sqrt{2}/4 + 1/2$ ,thus $\widehat{E}\left( {\mathbf{I},1}\right)  = \sqrt{2}N/4 + N/2,\widehat{Q}\left( {\mathbf{I},1}\right)  = \left( {\sqrt{2}N/4,\sqrt{2}N/4 - \sqrt{2}/2}\right) ,\widehat{E}\left( {{\mathbf{I}}^{\prime \prime },1}\right)  =$ $\sqrt{2}N/4 + N/2$ ,and $\widehat{Q}\left( {{\mathbf{I}}^{\prime \prime },1}\right)  = \left( {\sqrt{2}N/4 - \sqrt{2},\sqrt{2}N/4 - \sqrt{2}/2}\right)$ . When $r \geq  2$ ,for both $\mathbf{I}$ and ${\mathbf{I}}^{\prime }$ ,we have all ${z}_{k,j} = 1$ ,and all ${y}_{i} = 1$ ,thus $\widehat{E}\left( {\mathbf{I},r}\right)  = N,\widehat{Q}\left( {\mathbf{I},1}\right)  = \left( {N/2,N/2 - 1}\right) ,\widehat{E}\left( {{\mathbf{I}}^{\prime \prime },1}\right)  = N - 2$ ,and $\widehat{Q}\left( {{\mathbf{I}}^{\prime \prime },1}\right)  = \left( {N/2 - 2,N/2 - 1}\right) .$

例5.9 承接例5.4，当$r = 1$时，对于$\mathbf{I}$和${\mathbf{I}}^{\prime }$，我们得到所有${z}_{k,j} = \sqrt{2}/2$及所有${y}_{i} = \sqrt{2}/4 + 1/2$，因此$\widehat{E}\left( {\mathbf{I},1}\right)  = \sqrt{2}N/4 + N/2,\widehat{Q}\left( {\mathbf{I},1}\right)  = \left( {\sqrt{2}N/4,\sqrt{2}N/4 - \sqrt{2}/2}\right) ,\widehat{E}\left( {{\mathbf{I}}^{\prime \prime },1}\right)  =$$\sqrt{2}N/4 + N/2$，且$\widehat{Q}\left( {{\mathbf{I}}^{\prime \prime },1}\right)  = \left( {\sqrt{2}N/4 - \sqrt{2},\sqrt{2}N/4 - \sqrt{2}/2}\right)$。当$r \geq  2$时，对于$\mathbf{I}$和${\mathbf{I}}^{\prime }$，我们得到所有${z}_{k,j} = 1$及所有${y}_{i} = 1$，故$\widehat{E}\left( {\mathbf{I},r}\right)  = N,\widehat{Q}\left( {\mathbf{I},1}\right)  = \left( {N/2,N/2 - 1}\right) ,\widehat{E}\left( {{\mathbf{I}}^{\prime \prime },1}\right)  = N - 2$且$\widehat{Q}\left( {{\mathbf{I}}^{\prime \prime },1}\right)  = \left( {N/2 - 2,N/2 - 1}\right) .$

We now bound the local sensitivity of $\widehat{\mathrm{Q}}\left( {\mathrm{I},r}\right)$ :

现界定$\widehat{\mathrm{Q}}\left( {\mathrm{I},r}\right)$的局部敏感度：

LEMMA 5.10. Given any $r \geq  0$ ,for any $\mathbf{I} \sim  {\mathbf{I}}^{\prime }$ ,

引理5.10 给定任意$r \geq  0$，对于任何$\mathbf{I} \sim  {\mathbf{I}}^{\prime }$，

$$
\begin{Vmatrix}{\widehat{\mathbf{Q}}\left( {\mathbf{I},r}\right)  - \widehat{\mathbf{Q}}\left( {{\mathbf{I}}^{\prime },r}\right) }\end{Vmatrix} \leq  \left( {-2\widehat{F}\left( {\mathbf{I},r}\right)  + 2}\right)  \cdot  r.
$$

Proof. We consider the case $\mathbf{I}\left( {R}_{P}\right)  = {\mathbf{I}}^{\prime }\left( {R}_{P}\right)  \cup  \left\{  {{t}_{N}\left( \mathbf{I}\right) }\right\}$ and the other case can been shown similarly as the proof of Lemma 5.5. And similarly as the proof of Lemma 5.8, we assume the join results corresponding the ${t}_{N}\left( \mathbf{I}\right)$ are put at the end.

证明。我们考虑$\mathbf{I}\left( {R}_{P}\right)  = {\mathbf{I}}^{\prime }\left( {R}_{P}\right)  \cup  \left\{  {{t}_{N}\left( \mathbf{I}\right) }\right\}$的情形，另一情形可参照引理5.5的证明同理得证。类似引理5.8的证明，我们假定与${t}_{N}\left( \mathbf{I}\right)$对应的连接结果置于末尾。

For $\mathbf{I}$ ,we have ${\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$ ,and $\mathbf{Q}\left( {\mathbf{I}}^{ * }\right)$ constructed as $\widehat{E}\left( {\mathbf{I},r}\right) ,\left( {17}\right)$ ,and(18). For ${\mathbf{I}}^{\prime }$ ,we have ${\left\{  {y}_{i}^{{ * }^{\prime }}\right\}  }_{i},{\left\{  {z}_{k,j}^{{ * }^{\prime }}\right\}  }_{k,j}$ ,and $\mathrm{Q}\left( {\mathrm{I}}^{{ * }^{\prime }}\right)$ . To unify the size of ${\left\{  {y}_{i}^{ * }\right\}  }_{i}$ and ${\left\{  {y}_{i}^{{ * }^{\prime }}\right\}  }_{i}$ ,we append one zero at the end of ${\left\{  {y}_{i}^{{ * }^{\prime }}\right\}  }_{i}$ . And we process similarly for ${\left\{  {z}_{k,j}^{{ * }^{\prime }}\right\}  }_{k,j}$ .

对于$\mathbf{I}$，我们有${\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$，且$\mathbf{Q}\left( {\mathbf{I}}^{ * }\right)$构建为$\widehat{E}\left( {\mathbf{I},r}\right) ,\left( {17}\right)$，以及(18)。对于${\mathbf{I}}^{\prime }$，我们有${\left\{  {y}_{i}^{{ * }^{\prime }}\right\}  }_{i},{\left\{  {z}_{k,j}^{{ * }^{\prime }}\right\}  }_{k,j}$和$\mathrm{Q}\left( {\mathrm{I}}^{{ * }^{\prime }}\right)$。为统一${\left\{  {y}_{i}^{ * }\right\}  }_{i}$与${\left\{  {y}_{i}^{{ * }^{\prime }}\right\}  }_{i}$的尺寸，我们在${\left\{  {y}_{i}^{{ * }^{\prime }}\right\}  }_{i}$末尾补零。对${\left\{  {z}_{k,j}^{{ * }^{\prime }}\right\}  }_{k,j}$也进行类似处理。

By definition of $\widehat{F}\left( {\mathbf{I},r}\right)$ and ${\left\{  {y}_{i}^{ * }\right\}  }_{i}$ ,we have

根据$\widehat{F}\left( {\mathbf{I},r}\right)$和${\left\{  {y}_{i}^{ * }\right\}  }_{i}$的定义，可得

$$
N - \mathop{\sum }\limits_{{i \in  \left\lbrack  N\right\rbrack  }}{y}_{i}^{ * } =  - \widehat{F}\left( {\mathbf{I},r}\right) . \tag{19}
$$

And similarly, we have

同理可得

$$
N - \mathop{\sum }\limits_{{i \in  \left\lbrack  N\right\rbrack  }}{y}_{i}^{{ * }^{\prime }} =  - \widehat{F}\left( {{\mathbf{I}}^{\prime },r}\right)  + 1 \leq   - \widehat{F}\left( {\mathbf{I},r}\right)  + 2, \tag{20}
$$

where the inequality is by Lemma 5.8.

其中不等式由引理5.8得出

By this setting, we have

在此设定下，我们得到

$$
\begin{Vmatrix}{\mathrm{Q}\left( {\mathrm{I}}^{ * }\right)  - \mathrm{Q}\left( {\mathrm{I}}^{{ * }^{\prime }}\right) }\end{Vmatrix}
$$

$$
 = \sqrt{\mathop{\sum }\limits_{{k \in  \left\lbrack  d\right\rbrack  }}{\left( \mathop{\sum }\limits_{{j \in  \left\lbrack  {M}_{k}\right\rbrack  }}\left( {z}_{k,j}^{ * } - {z}_{k,j}^{ * }\right)  \cdot  {\psi }_{k,j}\left( \mathbf{I}\right) \right) }^{2}}
$$

$$
 \leq  \sqrt{\mathop{\sum }\limits_{{k \in  \left\lbrack  d\right\rbrack  }}{\left( \mathop{\sum }\limits_{{j \in  \left\lbrack  {M}_{k}\right\rbrack  }}\left( {z}_{k,j}^{{ * }^{\prime }} - {z}_{k,j}^{ * }\right)  \cdot  {\psi }_{k,j}\left( \mathbf{I}\right)  \cdot  \mathbb{I}\left( {z}_{k,j}^{ * } < {z}_{k,j}^{{ * }^{\prime }}\right) \right) }^{2}}
$$

$$
 + \sqrt{\mathop{\sum }\limits_{{k \in  \left\lbrack  d\right\rbrack  }}{\left( \mathop{\sum }\limits_{{j \in  \left\lbrack  {M}_{k}\right\rbrack  }}\left( {z}_{k,j}^{ * } - {z}_{k,j}^{{ * }^{\prime }}\right)  \cdot  {\psi }_{k,j}\left( \mathrm{I}\right)  \cdot  \mathbb{I}\left( {z}_{k,j}^{ * } > {z}_{k,j}^{{ * }^{\prime }}\right) \right) }^{2}}. \tag{21}
$$

For any $k \in  \left\lbrack  d\right\rbrack  ,j \in  \left\lbrack  {M}_{k}\right\rbrack$ such that ${z}_{k,j}^{ * } < {z}_{k,j}^{{ * }^{\prime }}$ ,we have

对于任意满足${z}_{k,j}^{ * } < {z}_{k,j}^{{ * }^{\prime }}$的$k \in  \left\lbrack  d\right\rbrack  ,j \in  \left\lbrack  {M}_{k}\right\rbrack$，有

$$
{z}_{k,j}^{{ * }^{\prime }} - {z}_{k,j}^{ * } \leq  {z}_{k,j}^{{ * }^{\prime }} - {z}_{k,j}^{ * } \cdot  {z}_{k,j}^{{ * }^{\prime }}
$$

$$
 = \left( {1 - {z}_{k,j}^{ * }}\right)  \cdot  {z}_{k,j}^{{ * }^{\prime }}
$$

$$
 \leq  \mathop{\sum }\limits_{{i \in  {D}_{k,j}\left( \mathrm{I}\right) }}\left( {1 - {y}_{i}^{ * }}\right)  \cdot  {z}_{k,j}^{{ * }^{\prime }}. \tag{22}
$$

The first line is by ${z}_{k,j}^{{ * }^{\prime }} \leq  1$ . The last line is by (17).

首行依据${z}_{k,j}^{{ * }^{\prime }} \leq  1$。末行依据(17)

Then, we have

继而可得

$$
\sqrt{\mathop{\sum }\limits_{{k \in  \left\lbrack  d\right\rbrack  }}{\left( \mathop{\sum }\limits_{{j \in  \left\lbrack  {M}_{k}\right\rbrack  }}\left( {z}_{k,j}^{{ * }^{\prime }} - {z}_{k,j}^{ * }\right)  \cdot  {\psi }_{k,j}\left( \mathrm{I}\right)  \cdot  \mathbb{I}\left( {z}_{k,j}^{ * } < {z}_{k,j}^{{ * }^{\prime }}\right) \right) }^{2}}
$$

$$
 \leq  \sqrt{\mathop{\sum }\limits_{{k \in  \left\lbrack  d\right\rbrack  }}{\left( \mathop{\sum }\limits_{{j \in  \left\lbrack  {M}_{k}\right\rbrack  }}\mathop{\sum }\limits_{{i \in  {D}_{k,j}\left( \mathrm{I}\right) }}\left( 1 - {y}_{i}^{ * }\right)  \cdot  {z}_{k,j}^{{ * }^{\prime }} \cdot  {\psi }_{k,j}\left( \mathrm{I}\right)  \cdot  \mathbb{I}\left( {z}_{k,j}^{ * } < {z}_{k,j}^{{ * }^{\prime }}\right) \right) }^{2}}
$$

$$
 \leq  \sqrt{\mathop{\sum }\limits_{{k \in  \left\lbrack  d\right\rbrack  }}{\left( \mathop{\sum }\limits_{{j \in  \left\lbrack  {M}_{k}\right\rbrack  }}\mathop{\sum }\limits_{{i \in  {D}_{k,j}\left( \mathrm{I}\right) }}\left( 1 - {y}_{i}^{ * }\right)  \cdot  {z}_{k,j}^{ * } \cdot  {\psi }_{k,j}\left( \mathrm{I}\right) \right) }^{2}}
$$

$$
 \leq  \mathop{\sum }\limits_{{i \in  \left\lbrack  N\right\rbrack  }}\left( {\left( {1 - {y}_{i}^{ * }}\right)  \cdot  \sqrt{\mathop{\sum }\limits_{{k \in  \left\lbrack  d\right\rbrack  }}{\left( \mathop{\sum }\limits_{{j \in  {C}_{k,i}\left( \mathbf{I}\right) }}{z}_{k,j}^{{ * }^{\prime }} \cdot  {\psi }_{k,j}\left( \mathbf{I}\right) \right) }^{2}}}\right) 
$$

$$
 \leq  \mathop{\sum }\limits_{{i \in  \left\lbrack  N\right\rbrack  }}\left( {1 - {y}_{i}^{ * }}\right)  \cdot  r
$$

$$
 \leq   - \widehat{F}\left( {\mathrm{I},r}\right)  \cdot  r \tag{23}
$$

The second line is by (22). The fourth line is by the triangle inequality under ${\ell }_{2}$ distance metric. The last line is by (19).

第二行依据(22)。第四行应用${\ell }_{2}$距离度量下的三角不等式。末行依据(19)

Similarly, with (20), we can show,

类似地，结合(20)可证

$$
\sqrt{\mathop{\sum }\limits_{{k \in  \left\lbrack  d\right\rbrack  }}{\left( \mathop{\sum }\limits_{{j \in  \left\lbrack  {M}_{k}\right\rbrack  }}\left( {z}_{k,j}^{ * } - {z}_{k,j}^{{ * }^{\prime }}\right)  \cdot  {\psi }_{k,j}\left( \mathrm{I}\right)  \cdot  \mathbb{I}\left( {z}_{k,j}^{ * } > {z}_{k,j}^{{ * }^{\prime }}\right) \right) }^{2}}
$$

$$
 \leq  \left( {-\widehat{F}\left( {\mathbf{I},r}\right)  + 2}\right)  \cdot  r \tag{24}
$$

Finally, combining (21), (23), and (24), the lemma follows.

最终联立(21)、(23)和(24)，引理得证

Our polynomial-time algorithm is thus the same as Algorithm 2,except that $F\left( {\mathbf{I},r}\right)$ and $\mathrm{Q}\left( {\mathbf{I},r}\right)$ are replaced by $\widehat{F}\left( {\mathbf{I},r}\right)$ and $\widehat{\mathrm{Q}}\left( {\mathbf{I},r}\right)$ ,respectively. Below we show that this replacement does not affect its privacy or utility:

因此我们的多项式时间算法与算法2相同，仅将$F\left( {\mathbf{I},r}\right)$和$\mathrm{Q}\left( {\mathbf{I},r}\right)$分别替换为$\widehat{F}\left( {\mathbf{I},r}\right)$和$\widehat{\mathrm{Q}}\left( {\mathbf{I},r}\right)$。下文证明该替换不影响隐私性与效用性：

THEOREM 5.11. For any $\varepsilon ,\delta ,\beta  > 0$ and any $\mathbf{I}$ ,the polynomial-time version of Algorithm 2 preserves $\left( {\varepsilon ,\delta }\right)$ -DP,and returns a $\widetilde{\mathrm{Q}}\left( \mathrm{I}\right)$ such that with probability at least $1 - \beta$ ,

定理5.11。对于任意$\varepsilon ,\delta ,\beta  > 0$和任意$\mathbf{I}$，算法2的多项式时间版本保持$\left( {\varepsilon ,\delta }\right)$-差分隐私，并以至少$1 - \beta$概率返回满足条件的$\widetilde{\mathrm{Q}}\left( \mathrm{I}\right)$

$$
\begin{Vmatrix}{\widetilde{Q}\left( \mathrm{I}\right)  - Q\left( \mathrm{I}\right) }\end{Vmatrix}
$$

$$
 = O\left( {\frac{\sqrt{d\log \left( {1/\beta }\right) \log \left( {{e}^{\varepsilon }/\delta }\right) }}{{\varepsilon }^{2}} \cdot  \mathrm{{DS}}\left( \mathrm{I}\right)  \cdot  \left( {\log \frac{\log \left( \mathrm{{DS}}\right) }{\beta } + \log \left( {{e}^{\varepsilon }/\delta }\right) }\right) }\right) .
$$

Proof. The privacy analysis remains the same as in the proof of Theorem 5.6,since $\widehat{F}\left( {\mathbf{I},r}\right)$ and $\widehat{\mathbb{Q}}\left( {\mathrm{I},r}\right)$ have the same sensitivity properties as $F\left( {\mathrm{I},r}\right)$ and $\mathbb{Q}\left( {\mathrm{I},r}\right)$ . Below we analyze the utility.

证明。隐私分析同定理5.6证明，因$\widehat{F}\left( {\mathbf{I},r}\right)$和$\widehat{\mathbb{Q}}\left( {\mathrm{I},r}\right)$与$F\left( {\mathrm{I},r}\right)$和$\mathbb{Q}\left( {\mathrm{I},r}\right)$具有相同敏感度特性。以下分析效用性

First,by Lemma 3.8 and 5.2,with probability at least $1 - \frac{\beta }{3}$ ,

首先，根据引理3.8和5.2，以至少$1 - \frac{\beta }{3}$概率满足

$$
 - \widehat{F}\left( {\mathbf{I},\widetilde{r}}\right)  = O\left( {\frac{1}{\varepsilon }\log \frac{\log \left( {\mathrm{{DS}}\left( \mathbf{I}\right) }\right) }{\beta }}\right) , \tag{25}
$$

and $\widetilde{r} \leq  2 \cdot  \operatorname{DS}\left( \mathrm{I}\right)$ .

和$\widetilde{r} \leq  2 \cdot  \operatorname{DS}\left( \mathrm{I}\right)$。

By the difference between ExpPrivMultiSJA and PolyPrivMultiSJA, we only need to bound the bias

通过ExpPrivMultiSJA与PolyPrivMultiSJA的差异，我们只需界定偏差

$$
\parallel \mathrm{Q}\left( \mathrm{I}\right)  - \widehat{\mathrm{Q}}\left( {\mathrm{I},\widetilde{r}}\right) \parallel  = O\left( {\frac{\mathrm{{DS}}\left( \mathrm{I}\right) }{\varepsilon }\log \frac{\log \left( {\mathrm{{DS}}\left( \mathrm{I}\right) }\right) }{\beta }}\right) . \tag{26}
$$

Let ${\left\{  {y}_{i}^{ * }\right\}  }_{i} = \operatorname{Sol}\left( {\widehat{E}\left( {\mathrm{I},\widetilde{r}}\right) }\right)$ . And we construct ${\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$ and $\mathrm{Q}\left( {\mathrm{I}}^{ * }\right)$ as (17) and (18). By (25),

设${\left\{  {y}_{i}^{ * }\right\}  }_{i} = \operatorname{Sol}\left( {\widehat{E}\left( {\mathrm{I},\widetilde{r}}\right) }\right)$。我们按(17)和(18)式构建${\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$与$\mathrm{Q}\left( {\mathrm{I}}^{ * }\right)$。根据(25)式，

$$
N - \mathop{\sum }\limits_{i}{y}_{i}^{ * } = O\left( {\frac{1}{\varepsilon }\log \frac{\log \left( {\mathrm{{DS}}\left( \mathbf{I}\right) }\right) }{\beta }}\right) . \tag{27}
$$

Then,we increment ${\left\{  {y}_{i}^{ * }\right\}  }_{i}$ to one vector,i.e. all elements equal to 1,and update the corresponding ${\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j},\mathrm{Q}\left( {\mathrm{I}}^{ * }\right)$ iteratively. For convenience,we use the subscript to denote the iteration and let ${\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$ and, $\mathbf{Q}\left( {\mathbf{I}}^{ * }\right)$ be the ones for iteration 0,i.e, ${\left\{  {y}_{i}^{*\left( 0\right) }\right\}  }_{i} = {\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{*\left( 0\right) }\right\}  }_{k,j} = {\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$ ,and

随后，将${\left\{  {y}_{i}^{ * }\right\}  }_{i}$递增为全1向量，并迭代更新对应的${\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j},\mathrm{Q}\left( {\mathrm{I}}^{ * }\right)$。为简化表述，用下标表示迭代次数，令${\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$和$\mathbf{Q}\left( {\mathbf{I}}^{ * }\right)$对应第0次迭代，即${\left\{  {y}_{i}^{*\left( 0\right) }\right\}  }_{i} = {\left\{  {y}_{i}^{ * }\right\}  }_{i},{\left\{  {z}_{k,j}^{*\left( 0\right) }\right\}  }_{k,j} = {\left\{  {z}_{k,j}^{ * }\right\}  }_{k,j}$，且

$$
\mathrm{Q}\left( {\mathrm{I}}^{*\left( 0\right) }\right)  = \mathrm{Q}\left( {\mathrm{I}}^{ * }\right) . \tag{28}
$$

At iteration $i \in  \left\lbrack  N\right\rbrack$ ,we increment ${y}_{i}^{*\left( i\right) }$ from ${y}_{i}^{ * }$ to 1 . Then,we set ${\left\{  {z}_{k,j}^{*\left( i\right) }\right\}  }_{k,j}$ and $\mathbf{Q}\left( {\mathbf{I}}^{*\left( i\right) }\right)$ as(17) and (18). With setting, we have

在第$i \in  \left\lbrack  N\right\rbrack$次迭代时，将${y}_{i}^{*\left( i\right) }$从${y}_{i}^{ * }$增至1。接着按(17)和(18)式设定${\left\{  {z}_{k,j}^{*\left( i\right) }\right\}  }_{k,j}$与$\mathbf{Q}\left( {\mathbf{I}}^{*\left( i\right) }\right)$。此设定下可得

$$
\begin{Vmatrix}{\mathbf{Q}\left( {\mathbf{I}}^{*\left( i\right) }\right)  - \mathbf{Q}\left( {\mathbf{I}}^{*\left( {i - 1}\right) }\right) }\end{Vmatrix}
$$

$$
 = \sqrt{\mathop{\sum }\limits_{{k = 1}}^{d}{\left( \mathop{\sum }\limits_{{j = 1}}^{{M}_{k}}\left( {z}_{k,j}^{*\left( i\right) } - {z}_{k,j}^{*\left( {i - 1}\right) }\right)  \cdot  {\psi }_{k,j}\left( \mathrm{I}\right) \right) }^{2}}
$$

$$
 = \sqrt{\mathop{\sum }\limits_{{k = 1}}^{d}{\left( \mathop{\sum }\limits_{{j \in  {C}_{k,i}\left( \mathbf{I}\right) }}\left( {z}_{k,j}^{*\left( i\right) } - {z}_{k,j}^{*\left( {i - 1}\right) }\right)  \cdot  {\psi }_{k,j}\left( \mathbf{I}\right) \right) }^{2}}
$$

$$
 \leq  \sqrt{\mathop{\sum }\limits_{{k = 1}}^{d}{\left( \mathop{\sum }\limits_{{j \in  {C}_{k,i}\left( \mathbf{I}\right) }}\left( 1 - {y}_{i}^{ * }\right)  \cdot  {\psi }_{k,j}\left( \mathbf{I}\right) \right) }^{2}}
$$

$$
 = \left( {1 - {y}_{i}^{ * }}\right)  \cdot  \sqrt{\mathop{\sum }\limits_{{k = 1}}^{d}{\left( \mathop{\sum }\limits_{{j \in  {C}_{k,i}\left( \mathrm{I}\right) }}{\psi }_{k,j}\left( \mathrm{I}\right) \right) }^{2}}
$$

$$
 \leq  \left( {1 - {y}_{i}^{ * }}\right)  \cdot  {\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right) \text{.} \tag{29}
$$

The third line is because,at $i$ th iteration,for any $k \in  \left\lbrack  d\right\rbrack  ,{z}_{k,j}^{*\left( i\right) }$ will not be updated if $j \notin  {C}_{k,i}\left( \mathbf{I}\right)$ . The fourth line is by (17). The last line is by the definition of ${\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$ .

第三行成立是因为：在第$i$次迭代中，若$j \notin  {C}_{k,i}\left( \mathbf{I}\right)$，则任意$k \in  \left\lbrack  d\right\rbrack  ,{z}_{k,j}^{*\left( i\right) }$都不会更新。第四行依据(17)式。末行由${\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$的定义得出。

After all iterations,we have all ${y}_{i}^{*\left( N\right) } = 1$ thus

全部迭代完成后，所有${y}_{i}^{*\left( N\right) } = 1$满足，因此

$$
\mathbf{Q}\left( {\mathbf{I}}^{*\left( N\right) }\right)  = \mathbf{Q}\left( \mathbf{I}\right) . \tag{30}
$$

Summing up (29) for all $i$ and combining (28) and (30),we get (26). Finally,with a similar process as the proof of Theorem 5.6, the claim follows.

对所有$i$求和(29)式，结合(28)和(30)式，即得(26)式。最终通过类似定理5.6证明的过程，可得结论成立。

<!-- Media -->

Algorithm 3: SVT with jump start

算法3：带跳跃启动的SVT

---

Input: $T,\varepsilon ,k$ ,and a sequence of sensitivity-1 queries ${f}_{1}\left( \mathbf{I}\right) ,{f}_{2}\left( \mathbf{I}\right) ,\ldots$

输入：$T,\varepsilon ,k$，及一组敏感度为1的查询${f}_{1}\left( \mathbf{I}\right) ,{f}_{2}\left( \mathbf{I}\right) ,\ldots$

$\widetilde{T} \leftarrow  T + \operatorname{Lap}\left( {2/\varepsilon }\right)$ ;

${\widehat{f}}^{\prime } = \infty$

for $\ell  \leftarrow  k,k - 1,\ldots ,1$ do

循环$\ell  \leftarrow  k,k - 1,\ldots ,1$执行

	${v}_{\ell } \leftarrow  \operatorname{Lap}\left( {4/\varepsilon }\right)$ ;

	if ${f}^{\prime } + {v}_{\ell } < \widetilde{T}$ then

	若${f}^{\prime } + {v}_{\ell } < \widetilde{T}$成立

		${f}_{\ell }\left( \mathrm{I}\right)  \leftarrow  {f}^{\prime }$ ;

	else

	否则

		Compute ${f}_{\ell }\left( \mathbf{I}\right)$ ;

		计算${f}_{\ell }\left( \mathbf{I}\right)$；

		${f}^{\prime } \leftarrow  {f}_{\ell }\left( \mathrm{I}\right)$ ;

	end

	结束

end

结束

for $\ell  \leftarrow  1,2,\ldots$ do

对于 $\ell  \leftarrow  1,2,\ldots$ 执行

	if $\ell  > k$ then

	如果 $\ell  > k$ 则

		${v}_{\ell } \leftarrow  \operatorname{Lap}\left( {4/\varepsilon }\right)$ ;

		Compute ${f}_{\ell }\left( \mathbf{I}\right)$ ;

		计算 ${f}_{\ell }\left( \mathbf{I}\right)$ ;

	end

	结束

	if ${f}_{\ell }\left( \mathbf{I}\right)  + {v}_{\ell } \geq  \widetilde{T}$ then

	如果 ${f}_{\ell }\left( \mathbf{I}\right)  + {v}_{\ell } \geq  \widetilde{T}$ 成立

		return $\ell$ ;

		返回 $\ell$ ;

	end

	结束

end

结束

---

<!-- Media -->

## 6 SYSTEM IMPLEMENTATION

## 6 系统实现

Our algorithm can be implemented on top of any SQL engine and a QCQP solver. For our system prototype, we use PostgreSQL and MOSEK.

我们的算法可在任何SQL引擎和QCQP求解器上实现。系统原型采用PostgreSQL和MOSEK。

The first step is to extract ${\left\{  {C}_{k,i}\right\}  }_{k,i},{\left\{  {D}_{k,j}\right\}  }_{k,j}$ from the join results. Note that the original query does not output this information, so the first step is to rewrite the query so that it also includes the PKs of the private entities, as illustrated in the following example.

第一步是从连接结果中提取${\left\{  {C}_{k,i}\right\}  }_{k,i},{\left\{  {D}_{k,j}\right\}  }_{k,j}$。需注意原查询并不输出该信息，因此需重写查询以包含私有实体的主键，如下例所示。

<!-- Media -->

Example 6.1. Consider Q5 of TPC-H benchmark:

示例6.1 以TPC-H基准测试的Q5为例：

---

							SELECT nation_name, SUM(price * (1 - discount))

							选择 国家名称, 合计(价格*(1-折扣))

							FROM Supplier, Lineitem, Orders, Customer, Nation

							来自 供应商, 订单项, 订单, 客户, 国家

							WHERE ... GROUP BY nation_name

							条件... 按国家名称分组

We rewrite it as

将其重写为

								SELECT nation_name, Supplier.SK, Customer.CK,

								选择 国家名称, 供应商.SK, 客户.CK,

								price * (1 - discount)

								价格*(1-折扣)

							FROM Supplier, Lineitem, Orders, Customer, Nation

							从供应商、订单明细、订单、客户、国家表

							WHERE ...

---

<!-- Media -->

From the results of the rewritten query, we then construct a series of QCQPs and feed them into the SVT. Note that these QCQPs only differ in the value of $r$ . The SVT returns an $\widetilde{r},\widehat{F}\left( {\mathrm{I},\widetilde{r}}\right)$ ,and the corresponding optimal fractional solution,from which we construct $\widehat{\mathbf{Q}}\left( {\mathbf{I},\widetilde{r}}\right)$ . Finally,we add Gaussian noise to $\widehat{\mathbf{Q}}\left( {\mathbf{I},\widetilde{r}}\right)$ .

根据重写查询的结果，我们构建一系列二次约束二次规划问题(QCQP)并输入到稀疏向量技术(SVT)中。注意这些QCQP仅$r$的取值不同。SVT返回$\widetilde{r},\widehat{F}\left( {\mathrm{I},\widetilde{r}}\right)$及对应的最优分数解，据此我们构建$\widehat{\mathbf{Q}}\left( {\mathbf{I},\widetilde{r}}\right)$，最后对$\widehat{\mathbf{Q}}\left( {\mathbf{I},\widetilde{r}}\right)$添加高斯噪声。

Optimizations. We observe that the computational bottleneck is to solve the series of QCQPs. To make them more efficient, we use the following two techniques. First, for each QCQP, we rewrite it into a conic programming, which is then solved by MOSEK with a homogeneous primal-dual algorithm. It turns out that solving the QCQP this way is much more efficient in practice.

优化措施。我们发现计算瓶颈在于求解系列QCQP问题。为提高效率采用两项技术：首先将每个QCQP重写为锥规划问题，通过MOSEK软件采用原始对偶同构算法求解。实践证明这种求解方式显著提升了效率。

The second technique reduces the number of QCQPs we have to solve. We observe that as we increase $r$ ,the optimal solutions of the QCQPs,hence the $\widehat{F}\left( {\mathbf{I},r}\right)$ ’s,are monotonically increasing. Recall that SVT returns the first $r = {2}^{\ell }$ such that $\widehat{F}\left( {\mathrm{I},{2}^{\ell }}\right)  + {v}_{\ell } \geq  \widetilde{T}$ ,where ${v}_{\ell }$ is a Laplace noise. Since the first few $\widehat{F}\left( {\mathrm{I},{2}^{\ell }}\right)$ ’s are unlikely to go above the threshold,our idea is to give a "jump start" to the SVT by skipping those QCQPs. Let ${2}^{k}$ be the smallest power of 2 no less than DS(I). By our analysis in the proof of Theorem 5.6,the SVT is likely to stop around $r = {2}^{k}$ . Our idea is then to first generate ${v}_{\ell }$ for all $\ell  = 1,\ldots ,k$ in advance,but only compute an $\widehat{F}\left( {\mathrm{I},{2}^{\ell }}\right)$ if it has a chance to be above $\widetilde{T}$ after adding ${v}_{\ell }$ . By going backward from ${2}^{k}$ to 1 and exploiting the monotonicity of $\widehat{F}\left( {\mathrm{I},{2}^{\ell }}\right)$ , this can eliminate many of them from having to be computed. The detailed algorithm is shown in Algorithm 3. Note that Algorithm 3 is nothing but a more efficient execution of the original SVT, so its privacy and utility guarantees remain the same. Furthermore, this technique can be applied to any SVT instantiation with a sequence of monotonic queries ${f}_{1}\left( \mathrm{I}\right) ,{f}_{2}\left( \mathrm{I}\right) ,\ldots$ ,as long as there is a good guess $k$ on the likely stopping position (note that $k$ can depend on private information),so we present Algorithm 3 in a more generic form.

第二项技术减少了需求解的QCQP数量。我们观察到随着$r$增大，QCQP的最优解（即$\widehat{F}\left( {\mathbf{I},r}\right)$）呈单调递增。由于SVT返回首个满足$\widehat{F}\left( {\mathrm{I},{2}^{\ell }}\right)  + {v}_{\ell } \geq  \widetilde{T}$的$r = {2}^{\ell }$（其中${v}_{\ell }$为拉普拉斯噪声），且前几个$\widehat{F}\left( {\mathrm{I},{2}^{\ell }}\right)$大概率不会超过阈值，我们通过跳过这些QCQP实现"SVT加速启动"。设${2}^{k}$为不小于DS(I)的最小2的幂次。根据定理5.6证明中的分析，SVT很可能在$r = {2}^{k}$附近终止。因此我们预先为所有$\ell  = 1,\ldots ,k$生成${v}_{\ell }$，但仅当$\widehat{F}\left( {\mathrm{I},{2}^{\ell }}\right)$加上${v}_{\ell }$可能超过$\widetilde{T}$时才计算其值。通过从${2}^{k}$倒序至1并利用$\widehat{F}\left( {\mathrm{I},{2}^{\ell }}\right)$的单调性，可避免大量计算。具体算法见算法3。需注意算法3仅是原始SVT的高效实现，其隐私保障与效用保持不变。该技术可应用于任何具有单调查询序列${f}_{1}\left( \mathrm{I}\right) ,{f}_{2}\left( \mathrm{I}\right) ,\ldots$的SVT实例，只要对可能终止位置有合理估计$k$（注：$k$可依赖私有信息），因此算法3以通用形式呈现。

## 7 EXPERIMENTS

## 7 实验评估

In this section, we report our experimental results comparing our algorithm (denoted PMSJA) with state-of-the-art algorithms for answering SJA queries with group-by over both benchmark and real-world datasets. For self-join-free queries, we compare with OptMean [21]; for queries with (implicit) self-joins, we compare with R2T [10] combined with advanced composition [18].

本节报告我们在基准数据集和真实数据集上，将所提算法(记为PMSJA)与最先进的带分组聚合自连接(SJA)查询应答算法的对比实验结果。对于无自连接查询，与OptMean[21]对比；对于含(隐式)自连接的查询，与结合高级组合[18]的R2T[10]对比。

### 7.1 Setup

### 7.1 实验设置

Datasets. We use two types of datasets: TPC-H and Stack Overflow network dataset. The TPC-H schema has been discussed before and is shown in Figure 1. We use datasets with scale ${0.125},{0.25},\ldots ,8$ . The default scale is 2,where there are about 15 million tuples.

数据集。采用两类数据集：TPC-H和Stack Overflow网络数据集。TPC-H模式前文已讨论（见图1），使用规模为${0.125},{0.25},\ldots ,8$的数据集，默认规模2包含约1500万条元组。

The Stack Overflow network dataset is from SNAP [28] and records users' interactions on the Stack Overflow website. Here, each node represents one user and the interactions are stored as edges with a timestamp. We use two graphs, corresponding to answer-to-question (a2q) and comment-to-answer (c2a), respectively. They contain 2,464,606 nodes, 17,823,525 edges, and 1,646,338 nodes, 25,405,374 edges, respectively. We have deleted the top 10% nodes with the highest degrees, as protecting their privacy would introduce too much error. The number of edges of stackoverflow - a2q and stackoverflow $- \mathrm{c}2\mathrm{a}$ are then reduced to1,468,092and1,425,352.

Stack Overflow网络数据集源自SNAP[28]，记录了用户在Stack Overflow网站上的互动行为。其中每个节点代表一个用户，互动以带时间戳的边形式存储。我们使用两个图结构，分别对应问题回答(a2q)和答案评论(c2a)场景，包含2,464,606个节点与17,823,525条边、1,646,338个节点与25,405,374条边。为保护隐私而删除度数最高的前10%节点后，stackoverflow-a2q和stackoverflow$- \mathrm{c}2\mathrm{a}$的边数分别降至1,468,092和1,425,352条。

Queries. We use 8 queries over TPC-H schema. The first two are self-join-free queries while the others are self-join queries. The query structures are shown in Figure 3. Some of the queries are taken directly from TPC-H benchmark while others are designed to test the algorithms under various settings, in particular different combinations of primary private relations and counting queries are used. Furthermore, for the same TPC-H query, we may use different group-by attributes. That is also to test algorithm under more settings: Different group-by attributes lead to different group sizes $d$ and different data distributions in groups. Another reason is that TPC-H queries have too many or too few groups,i.e.,for TPC-H query ${Q7}$ ,if adding predicates,there are only 8 groups while removing the predicates will lead to thousands of groups. Therefore, we removed the predicates and used a subset of group-by attributes to get a good $d$ . More precisely,both ${\mathbf{Q}}_{1},{\mathbf{Q}}_{2}$ correspond to TPC-H query ${Q10}$ where Customer is assigned as the primary private relation but use different group-by attributes. ${\mathrm{Q}}_{3}$ corresponds to TPC-H query ${Q5}.{\mathrm{Q}}_{4}$ and ${\mathrm{Q}}_{5}$ correspond to TPC-H query ${Q7}$ but with different group-by attributes. All these three queries have Customer and Supplier as the primary private relations. Finally, ${\mathbf{Q}}_{6},{\mathbf{Q}}_{7}$ ,and ${\mathbf{Q}}_{8}$ provide more primary private relation combinations: \{PartSupp, Customer\} and \{PartSupp, Orders\}.

查询语句。我们在TPC-H架构上使用8组查询，前两组为无自连接查询，其余为自连接查询。查询结构如图3所示：部分直接采用TPC-H基准测试查询，另一些专为测试算法在不同设置下的表现而设计，特别是主隐私关系与计数查询的各种组合。对于相同TPC-H查询，我们会采用不同分组属性——这既能测试算法在分组规模$d$和组内数据分布差异下的表现，也解决了TPC-H查询分组数量极端化问题（如${Q7}$查询添加谓词后仅剩8组，去除谓词则产生数千组）。因此我们移除谓词并选用部分分组属性以获得理想$d$。具体而言，${\mathbf{Q}}_{1},{\mathbf{Q}}_{2}$对应将Customer设为主隐私关系的TPC-H${Q10}$查询但采用不同分组属性；${\mathrm{Q}}_{3}$对应TPC-H${Q5}.{\mathrm{Q}}_{4}$查询；${\mathrm{Q}}_{5}$对应TPC-H${Q7}$查询但调整了分组属性——这三组查询均以Customer和Supplier为主隐私关系。最后${\mathbf{Q}}_{6},{\mathbf{Q}}_{7}$和${\mathbf{Q}}_{8}$提供了更多主隐私关系组合：{PartSupp, Customer}与{PartSupp, Orders}。

<!-- Media -->

<!-- figureText: Primary private relation Select attribute $\mathrm{L}\left( {\mathrm{{SK}},\mathrm{{OK}},\overline{\mathrm{{EP}}},\overline{\mathrm{{DC}}}}\right)$ $\mathrm{S}\left( {\underline{\mathrm{{SK}}},\mathrm{{NK}}}\right)$ $\mathrm{N}\left( {\mathrm{{NK}},\mathrm{{RK}},\mathrm{{QNN}}}\right)$ PS (SK, PK) $\mathrm{L}\left( {\mathrm{{SK}},\mathrm{{PK}},\mathrm{{OK}}}\right)$ $\mathrm{R}\left( \mathrm{{RK}}\right)$ $\mathrm{C}\left( {\underline{\mathrm{{CK}}},\mathrm{{NK}}}\right)$ $- 0\left( {\underline{OK},\underline{CK}}\right)$ ${\mathrm{Q}}_{6}$ $\mathrm{N}\left( {\mathrm{{NK}},\left( \mathrm{{NN}}\right) }\right)$ PS (SK,PK) $\mathrm{L}(\mathrm{{SK}},\mathrm{{PK}},\mathrm{{OK}}$ $C\left( {\underline{\mathrm{{CK}}},\mathrm{{NK}}}\right)$ $C\left( {{CK},{NK}}\right)$ $D\left( {\underline{OK},\underline{CK}}\right)$ ${\mathrm{Q}}_{7}$ 0 (OK , $\mathrm{C}\left( \mathrm{{CK}}\right)$ PS (SK, PK) L(SK, P) O(OK) ${\mathrm{Q}}_{8}$ Edge(B,C,T) Edge (A, B, T) Edge(B,C,T) Edge (C, D, T) ${\mathbf{Q}}_{3 - }$ Graph Pattern Counting Queries Aggregation attribute Group by attribute $D\left( {\underline{OK},\underline{CK}}\right)$ $\mathrm{C}\left( {\underline{\mathrm{{CK}}},\mathrm{{NK}}}\right)$ ${\mathrm{Q}}_{3}$ C(CK) $D\left( {\underline{OK},{CK}}\right)$ S(SK) $O\left( {{OK},{CK},{OD}}\right)$ ${\mathrm{Q}}_{1}$ O(CK) $\mathrm{C}\left( {\mathrm{{CK}},\mathrm{{NK}}}\right)$ $\mathrm{N}\left( {\underline{\mathrm{{NK}}},\left( {\underline{\mathrm{N}}\dot{\mathrm{N}}}\right) }\right)$ S(SK) ${\mathbf{Q}}_{2}$ ${\mathrm{Q}}_{5}$ TPC-H Queries Edge (A, B, T) Edge (A, B, T) ${\mathbf{Q}}_{1 - }$ ${\mathbf{Q}}_{2 - }$ -->

<img src="https://cdn.noedgeai.com/01965992-99d2-7937-99a4-2c1821ef6ce9_19.jpg?x=141&y=260&w=1290&h=647&r=0"/>

Fig. 3. The structure of queries.

图3. 查询语句结构

<!-- Media -->

For the group-by attributes, we use date attribute or/and nation attribute. There are three cases. In the first case, we use the nation attribute and it has 25 groups. In the second case, we use the date, where we select 100 dates with each one corresponding to one group. We also conduct experiments with various grouping, which will be discussed later. Furthermore, we also use their combinations to make the group: we group the query results by nation and month. To avoid the heavy computations brought by the large group size, we only select two months and the group size here is 50 . The group size for each query is shown in the head of Table 1.

分组属性采用日期属性或/和国家属性，分三种情况：第一种使用含25组的国家属性；第二种选用100个日期各成一组；第三种结合国家与月份分组（为避免大数据量计算仅选两个月，形成50组规模）。各查询的具体组规模见表1首行。

In another dimension, ${\mathrm{Q}}_{2},{\mathrm{Q}}_{6},{\mathrm{Q}}_{7}$ are counting queries while the others do the sum aggregation over extendedprice and discount attributes in Lineitem relation. The numbers shown are in thousands.

另一维度上，${\mathrm{Q}}_{2},{\mathrm{Q}}_{6},{\mathrm{Q}}_{7}$为计数查询，其余则在Lineitem关系的extendedprice和discount属性上做求和聚合。所示数值单位为千。

For graph pattern counting queries,we use edge counting query ${Q}_{1 - }$ ,length-2 path counting query ${\mathrm{Q}}_{2 - }$ ,and length-3 path counting query ${\mathrm{Q}}_{3 - }$ . We take the groups on the time attribute on the first edge. Here,we have 10 groups and each group has several dates. Each group in ${\mathbf{Q}}_{1 - },{\mathbf{Q}}_{2 - }$ ,and ${\mathrm{Q}}_{3 - }$ has40,80,220dates respectively.

针对图模式计数查询，我们采用边计数查询${Q}_{1 - }$、长度-2路径计数${\mathrm{Q}}_{2 - }$和长度-3路径计数${\mathrm{Q}}_{3 - }$。以第一条边的时间属性分组，共10组且各组日期数量不同：${\mathbf{Q}}_{1 - },{\mathbf{Q}}_{2 - }$、${\mathrm{Q}}_{3 - }$中每组分别含40/80/220个日期。

Experimental parameters. We conduct all experiments on a Linux server with a 24-core 48-thread 2.2GHz Intel Xeon CPU and 256GB memory. Each program is allowed to use at most 24 threads. We use ${\ell }_{2}$ metric in the report of query result and the error and we call one mechanism has utility and high utility if the relative error is below ${50}\%$ and ${30}\%$ respectively. Each experiment is repeated 20 times and we remove 4 largest errors and 4 smallest errors and report the average error for the rest 12 runs. For the privacy budgets,we use $\varepsilon  = 2,4,8$ and the default value is set to 4 . Compared with the work of answering single query $\left\lbrack  {{10},{11},{13},{22},{40}}\right\rbrack$ ,we use larger $\varepsilon$ . That is because answering multiple queries is much more complex and we need larger $\varepsilon$ to guarantee the utility [1]. We set $\delta$ to 1e-7 and the failure probability $\beta$ to 0.1 . Both OptMean and R2T require an ${\mathrm{{GS}}}_{\mathrm{Q}}$ as the input

实验参数。所有实验均在配备24核48线程2.2GHz英特尔至强处理器和256GB内存的Linux服务器上进行。每个程序最多允许使用24个线程。我们在查询结果报告中采用${\ell }_{2}$指标衡量误差，当相对误差分别低于${50}\%$和${30}\%$时，我们称该机制具有效用性和高效用性。每组实验重复20次，剔除4个最大误差和4个最小误差后，取剩余12次运行的平均误差值。隐私预算方面，我们采用$\varepsilon  = 2,4,8$，默认值设为4。与处理单查询的$\left\lbrack  {{10},{11},{13},{22},{40}}\right\rbrack$研究相比，我们使用更大的$\varepsilon$参数，这是因为多查询处理更为复杂，需要更大的$\varepsilon$来保证效用性[1]。设定$\delta$为1e-7，失败概率$\beta$为0.1。OptMean和R2T算法均需输入${\mathrm{{GS}}}_{\mathrm{Q}}$参数。

<!-- Media -->

parameter. For TPC-H queries,we set ${\mathrm{{GS}}}_{\mathrm{Q}}$ to 1e6. For graph pattern counting queries,we set a degree upper bound of $D = 1,{000},{000}$ and set ${\mathrm{{GS}}}_{\mathrm{Q}}$ as the maximum number of graph patterns containing any node,i.e., ${\mathrm{{GS}}}_{{\mathrm{Q}}_{1 - }} = D,{\mathrm{{GS}}}_{{\mathrm{Q}}_{2 - }} = {D}^{2}$ ,and ${\mathrm{{GS}}}_{{\mathrm{Q}}_{3 - }} = {D}^{3}$ .

参数设置。针对TPC-H查询，设定${\mathrm{{GS}}}_{\mathrm{Q}}$为1e6。对于图模式计数查询，设置$D = 1,{000},{000}$作为度上限，并将${\mathrm{{GS}}}_{\mathrm{Q}}$定义为包含任意节点的图模式最大数量，即${\mathrm{{GS}}}_{{\mathrm{Q}}_{1 - }} = D,{\mathrm{{GS}}}_{{\mathrm{Q}}_{2 - }} = {D}^{2}$和${\mathrm{{GS}}}_{{\mathrm{Q}}_{3 - }} = {D}^{3}$。

<table><tr><td colspan="2">Query type</td><td colspan="2">Self-join-free queries</td><td colspan="6">Self-join queries</td></tr><tr><td colspan="2">Query</td><td>${\mathrm{Q}}_{1}$</td><td>${\mathrm{Q}}_{2}$</td><td>${\mathrm{Q}}_{3}$</td><td>${\mathrm{Q}}_{4}$</td><td>${\mathrm{Q}}_{5}$</td><td>${\mathrm{Q}}_{6}$</td><td>${\mathrm{Q}}_{7}$</td><td>${\mathrm{Q}}_{8}$</td></tr><tr><td colspan="2">Group size $d$</td><td>100</td><td>25</td><td>25</td><td>50</td><td>100</td><td>25</td><td>50</td><td>100</td></tr><tr><td rowspan="2">Query result</td><td>${\ell }_{2}$ norm</td><td>1,820,000</td><td>2,400,000</td><td>3,480,000</td><td>1,830,000</td><td>1,820,000</td><td>59,000</td><td>43,800</td><td>1,820,000</td></tr><tr><td>Time(s)</td><td>1.33</td><td>4.58</td><td>3.85</td><td>2.62</td><td>1.66</td><td>2.12</td><td>2.21</td><td>3.56</td></tr><tr><td rowspan="2">PMSJA</td><td>Error(%)</td><td>0.504</td><td>0.0644</td><td>24.6</td><td>18.5</td><td>20.0</td><td>12.4</td><td>21.9</td><td>10.2</td></tr><tr><td>Time(s)</td><td>12.5</td><td>75.4</td><td>562</td><td>4683.8</td><td>3056.8</td><td>36.3</td><td>35.8</td><td>68.5</td></tr><tr><td rowspan="2">R2T/OptMean</td><td>Error(%)</td><td>1.2</td><td>0.138</td><td>99.6</td><td>83.7</td><td>87.6</td><td>56.1</td><td>85.7</td><td>82.5</td></tr><tr><td>Time(s)</td><td>6</td><td>65.2</td><td>20.7</td><td>30.1</td><td>24.1</td><td>12.0</td><td>15.3</td><td>26.1</td></tr></table>

<table><tbody><tr><td colspan="2">查询类型</td><td colspan="2">无自连接查询</td><td colspan="6">自连接查询</td></tr><tr><td colspan="2">查询</td><td>${\mathrm{Q}}_{1}$</td><td>${\mathrm{Q}}_{2}$</td><td>${\mathrm{Q}}_{3}$</td><td>${\mathrm{Q}}_{4}$</td><td>${\mathrm{Q}}_{5}$</td><td>${\mathrm{Q}}_{6}$</td><td>${\mathrm{Q}}_{7}$</td><td>${\mathrm{Q}}_{8}$</td></tr><tr><td colspan="2">分组大小 $d$</td><td>100</td><td>25</td><td>25</td><td>50</td><td>100</td><td>25</td><td>50</td><td>100</td></tr><tr><td rowspan="2">查询结果</td><td>${\ell }_{2}$ 范数</td><td>1,820,000</td><td>2,400,000</td><td>3,480,000</td><td>1,830,000</td><td>1,820,000</td><td>59,000</td><td>43,800</td><td>1,820,000</td></tr><tr><td>时间(秒)</td><td>1.33</td><td>4.58</td><td>3.85</td><td>2.62</td><td>1.66</td><td>2.12</td><td>2.21</td><td>3.56</td></tr><tr><td rowspan="2">PMSJA算法</td><td>误差率(%)</td><td>0.504</td><td>0.0644</td><td>24.6</td><td>18.5</td><td>20.0</td><td>12.4</td><td>21.9</td><td>10.2</td></tr><tr><td>时间(秒)</td><td>12.5</td><td>75.4</td><td>562</td><td>4683.8</td><td>3056.8</td><td>36.3</td><td>35.8</td><td>68.5</td></tr><tr><td rowspan="2">R2T/最优均值</td><td>误差率(%)</td><td>1.2</td><td>0.138</td><td>99.6</td><td>83.7</td><td>87.6</td><td>56.1</td><td>85.7</td><td>82.5</td></tr><tr><td>时间(秒)</td><td>6</td><td>65.2</td><td>20.7</td><td>30.1</td><td>24.1</td><td>12.0</td><td>15.3</td><td>26.1</td></tr></tbody></table>

Table 1. Comparison between PMSJA and state-of-the-art algorithms (OptMean [21] for self-join-free queries and R2T [10] for self-join queries) on TPC-H queries with group-by operator. We use data scale equal to 2, $\varepsilon  = 4$ and report the relative error.

表1. PMSJA与最先进算法（无自连接查询采用OptMean[21]，自连接查询采用R2T[10]）在含分组操作的TPC-H查询中的对比。我们使用数据规模为2，$\varepsilon  = 4$并报告相对误差。

<table><tr><td>Dataset</td><td colspan="6">stackoverflow - a2q</td><td colspan="6">stackoverflow - c2a</td></tr><tr><td rowspan="2">Query</td><td colspan="2">${\mathrm{Q}}_{1 - }$</td><td colspan="2">${\mathrm{Q}}_{2 - }$</td><td colspan="2">${\mathrm{Q}}_{3 - }$</td><td colspan="2">${\mathrm{Q}}_{1 - }$</td><td colspan="2">${\mathrm{Q}}_{2 - }$</td><td colspan="2">${\mathrm{Q}}_{3 - }$</td></tr><tr><td>Error(%)</td><td>Time(s)</td><td>Error(%)</td><td>Time(s)</td><td>Error(%)</td><td>Time(s)</td><td>Error(%)</td><td>Time(s)</td><td>Error(%)</td><td>Time(s)</td><td>Error(%)</td><td>Time(s)</td></tr><tr><td>Query Result</td><td>48,000</td><td>0.735</td><td>41,400</td><td>1.3</td><td>49,500</td><td>1.78</td><td>34,900</td><td>0.61</td><td>50,100</td><td>1.11</td><td>106,000</td><td>1.57</td></tr><tr><td>PMSJA</td><td>5.56</td><td>27.3</td><td>23</td><td>41.4</td><td>35.5</td><td>81.3</td><td>11.7</td><td>16</td><td>24.8</td><td>60.8</td><td>36.7</td><td>1,943</td></tr><tr><td>R2T</td><td>13.9</td><td>10.7</td><td>58.5</td><td>10.6</td><td>81</td><td>12.8</td><td>22.3</td><td>8.67</td><td>60.6</td><td>11</td><td>77.5</td><td>19.5</td></tr></table>

<table><tbody><tr><td>数据集</td><td colspan="6">stackoverflow - a2q</td><td colspan="6">stackoverflow - c2a</td></tr><tr><td rowspan="2">查询</td><td colspan="2">${\mathrm{Q}}_{1 - }$</td><td colspan="2">${\mathrm{Q}}_{2 - }$</td><td colspan="2">${\mathrm{Q}}_{3 - }$</td><td colspan="2">${\mathrm{Q}}_{1 - }$</td><td colspan="2">${\mathrm{Q}}_{2 - }$</td><td colspan="2">${\mathrm{Q}}_{3 - }$</td></tr><tr><td>错误率(%)</td><td>耗时(秒)</td><td>错误率(%)</td><td>耗时(秒)</td><td>错误率(%)</td><td>耗时(秒)</td><td>错误率(%)</td><td>耗时(秒)</td><td>错误率(%)</td><td>耗时(秒)</td><td>错误率(%)</td><td>耗时(秒)</td></tr><tr><td>查询结果</td><td>48,000</td><td>0.735</td><td>41,400</td><td>1.3</td><td>49,500</td><td>1.78</td><td>34,900</td><td>0.61</td><td>50,100</td><td>1.11</td><td>106,000</td><td>1.57</td></tr><tr><td>PMSJA</td><td>5.56</td><td>27.3</td><td>23</td><td>41.4</td><td>35.5</td><td>81.3</td><td>11.7</td><td>16</td><td>24.8</td><td>60.8</td><td>36.7</td><td>1,943</td></tr><tr><td>R2T</td><td>13.9</td><td>10.7</td><td>58.5</td><td>10.6</td><td>81</td><td>12.8</td><td>22.3</td><td>8.67</td><td>60.6</td><td>11</td><td>77.5</td><td>19.5</td></tr></tbody></table>

Table 2. Comparison between PMSJA and R2T [10] on graph pattern counting queries with $d = {10}$ on different networks. We use $\varepsilon  = 4$ and report relative error.

表2. PMSJA与R2T[10]在不同网络上进行$d = {10}$图模式计数查询的对比。我们采用$\varepsilon  = 4$并报告相对误差。

<!-- Media -->

### 7.2 Experimental Results

### 7.2 实验结果

Compare with state-of-the-art algorithms. We conduct the experiments on the TPC-H dataset and graph data to compare PMSJA with OptMean and R2T and the results are shown in Table 1 and Table 2 where we report both error level (relative error) and running time. For self-join-free cases, OptMean already has very high utility while PMSJA further reduces this error by more than 50%. This matches our theoretical analyses: PMSJA improves OptMean by a log factor and both of them match the lower bound up to log factors. For efficiency, PMSJA uses a bit more time than OptMean but they are nearly at the same level. That is because, for self-join-free queries, they have very similar processes: extracting the relationship between users and join results first and then finding some threshold to do the clipping with sub-linear time.

与最先进算法对比。我们在TPC-H数据集和图数据上开展实验，将PMSJA与OptMean和R2T进行比较，结果如表1和表2所示，其中同时报告了误差水平（相对误差）和运行时间。对于无自连接查询，OptMean已具备较高效用，而PMSJA进一步将误差降低50%以上。这与理论分析一致：PMSJA以对数因子改进OptMean，且两者均达到对数因子下的理论下界。效率方面，PMSJA耗时略多于OptMean但基本持平，这是因为对于无自连接查询，两者处理流程相似：先提取用户与连接结果的关系，再通过亚线性时间寻找截断阈值。

Then, we talk about the results of 12 self-join queries and make a comparison between PMSJA with R2T. For the utility, PMSJA has high utility (relative error below 30%) in all 12 queries except the two ${\mathrm{Q}}_{3 - }$ ,in which PMSJA still has the utility (relative error below 50%). Meanwhile,R2T only has utility in two ${\mathrm{Q}}_{1 - }$ queries,where the group size is small,i.e., $d = {10}$ . More precisely,for TPC-H queries,the error level of R2T is ${3.91} \times   \sim  {8.1} \times$ of PMSJA and a larger $d$ is more likely to lead to a larger gap. For the graph pattern counting queries where $d = {10}$ ,that ratio is reduced to ${1.91} \times   \sim  {2.53} \times$ . That confirms our theoretical analysis that PMSJA has $\sqrt{d}$ improvement over R2T and matches the lower bound up to log factors. For efficiency, as mentioned before, PMSJA solves several convex QCQP's and needs much more running time than R2T, which only solves a number of LPs. However, as the experiments show, in more than half of the cases (8/12), the running time of PMSJA is not much larger than R2T (less than $8 \times$ ),although it can be much longer in the worst case.

接下来讨论12个自连接查询的结果，对比PMSJA与R2T。在效用方面，除两个${\mathrm{Q}}_{3 - }$查询外（其相对误差仍低于50%），PMSJA在所有查询中均保持高效用（相对误差低于30%）。而R2T仅在两个${\mathrm{Q}}_{1 - }$查询（组规模较小，即$d = {10}$时）具备效用。具体而言，在TPC-H查询中，R2T的误差水平是PMSJA的${3.91} \times   \sim  {8.1} \times$倍，且$d$越大差距越显著。对于$d = {10}$的图模式计数查询，该比率降至${1.91} \times   \sim  {2.53} \times$倍，验证了PMSJA相较R2T具有$\sqrt{d}$倍改进且达到对数因子下界的理论分析。效率方面，如前所述，PMSJA需求解多个凸QCQP问题，耗时远超过仅需求解线性规划的R2T。但实验显示，在超半数案例（8/12）中，PMSJA运行时间与R2T差异不大（小于$8 \times$倍），尽管最坏情况下可能显著延长。

<!-- Media -->

<!-- figureText: PMSJA R2T Query result $\varepsilon  = 2$ Running Time(s) ${10}^{2}$ ${10}^{1}$ ${10}^{0}$ $\varepsilon  = 4$ Running Time(s) ${10}^{2}$ ${10}^{1}$ ${10}^{0}$ $\varepsilon  = 8$ Running Time(s) ${10}^{2}$ ${10}^{1}$ ${10}^{0}$ 12 25 50 100 200 400 d Error Level ${10}^{6}$ ${10}^{5}$ Error Level ${10}^{6}$ ${10}^{5}$ Error Level ${10}^{6}$ ${10}^{5}$ 12 25 50 100 200 400 d -->

<img src="https://cdn.noedgeai.com/01965992-99d2-7937-99a4-2c1821ef6ce9_21.jpg?x=236&y=271&w=1097&h=842&r=0"/>

Fig. 4. Running times and error levels of PMSJA and R2T for ${\mathrm{Q}}_{3}$ with different $d$ and $\varepsilon$ .

图4. PMSJA与R2T在不同$d$和$\varepsilon$条件下处理${\mathrm{Q}}_{3}$的运行时间及误差水平。

<!-- Media -->

Number of queries. To further examine the effects of the change of number of queries $d$ ,we use ${\mathrm{Q}}_{3}$ but more/fewer dates so we can have different $d$ ’s. More precisely,we run it with $d =$ ${12},{25},{50},\ldots ,{400}$ on the TPC-H dataset with scale 2 and $\varepsilon$ is set to 2,4,8. We plot both error levels and running times in Figure 4,where we also plot the ${\ell }_{2}$ norm of the query result and its running time. For the error level, first, it is not surprising to see, PMSJA always has an error lower than R2T. As $d$ increases,the error of PMSJA decreases at the same rate as that of the query result: both of them increase with $\sqrt{d}$ . Meanwhile,for R2T,its error level increases linearly with $d$ before it catches up with the query result (see the figure with $\varepsilon  = 8$ ). One interesting finding is,when ${R2T}$ ’s catches up with the query result, it increases at the same rate as the query result and will not surpass that (see the figures with $\varepsilon  = 2$ and $\varepsilon  = 4$ ). That is because R2T will always return a noised value between 0 and the real query result thus its error is at most the query result. However, this does not really have any benefit since we have already lost all utility when the error level reaches the query result.

查询数量分析。为探究查询数量$d$变化的影响，我们固定${\mathrm{Q}}_{3}$但调整日期数量以生成不同$d$值。具体而言，在规模为2的TPC-H数据集上运行$d =$${12},{25},{50},\ldots ,{400}$次实验，设定$\varepsilon$为2/4/8。图4展示了误差水平与运行时间，同时绘制了查询结果的${\ell }_{2}$范数及其耗时。误差方面，PMSJA始终优于R2T。随着$d$增加，PMSJA误差与查询结果同步下降，两者均按$\sqrt{d}$比率增长。而R2T误差在达到查询结果前（参见$\varepsilon  = 8$图示）与$d$呈线性增长。有趣的是，当${R2T}$追上查询结果后，其增长速率与查询结果同步且不会超越（参见$\varepsilon  = 2$和$\varepsilon  = 4$图示），这是因为R2T始终返回0到真实结果间的噪声值，故误差上限为查询结果本身。但该特性并无实际益处，因误差达到查询结果时已完全丧失效用。

On the other hand,we see the running time of real query sub-linearly increases with $d$ . By contrast, R2T and PMSJA have linear and sup-linear speeds respectively. That is because R2T runs each query independently and for every single query,increasing $d$ will only affect the assigned $\varepsilon$ , which just brings a minor effect on the running time thus the running time has a linear dependency on $d$ . Meanwhile,for PMSJA,increasing $d$ will complicate the convex QCQP’s it solves thus leading to a super-linear effect on the running time.

另一方面，我们观察到实际查询的运行时间随$d$呈次线性增长。相比之下，R2T和PMSJA分别呈现线性和超线性速度。这是因为R2T独立执行每个查询，对于单个查询而言，增加$d$仅影响分配的$\varepsilon$，这对运行时间影响较小，因此运行时间与$d$呈线性关系。而对于PMSJA，增加$d$会使求解的凸QCQP问题复杂化，从而导致运行时间呈现超线性效应。

Scalability. Lastly, we conduct the experiments to see the effects of data scale changes. We use TPC-H datasets with scale ${0.125},{0.2},\ldots ,8$ and run ${\mathbf{Q}}_{3}$ and ${\mathbf{Q}}_{7}$ with $\varepsilon  = 2,4,8$ . The results are shown in Figure 5a and 5b.

可扩展性。最后我们通过实验观察数据规模变化的影响。采用规模为${0.125},{0.2},\ldots ,8$的TPC-H数据集，在$\varepsilon  = 2,4,8$条件下运行${\mathbf{Q}}_{3}$和${\mathbf{Q}}_{7}$。结果如图5a和5b所示。

<!-- Media -->

<!-- figureText: PMSJA ... R2T Query result $\varepsilon  = 2$ Running Time(s) ${10}^{3}$ ${10}^{1}$ Running Time(s) ${10}^{3}$ ${10}^{1}$ $\varepsilon  = 8$ Running Time(s) ${10}^{3}$ ${10}^{1}$ 0.125 0.25 Scale $\varepsilon  = 2$ Running Time(s) ${10}^{2}$ 10 ${10}^{0}$ $\varepsilon  = 4$ Running Time(s) ${10}^{2}$ ${10}^{1}$ ${10}^{0}$ $\varepsilon  = 8$ Running Time(s) ${10}^{2}$ ${10}^{1}$ ${10}^{(}$ 0.125 0.25 0.5 8 Scale ${10}^{7}$ Error Level ${10}^{6}$ ${10}^{7}$ Error Level ${10}^{6}$ ${10}^{7}$ Error Level ${10}^{6}$ 0.125 0.25 2 Scale (a) ${\mathrm{Q}}_{3}$ ${10}^{5}$ Error Level ${10}^{4}$ ${10}^{5}$ Error Level ${10}^{4}$ ${10}^{5}$ Error Level ${10}^{4}$ 0.125 0.25 0.5 2 8 Scale (b) ${\mathrm{Q}}_{7}$ -->

<img src="https://cdn.noedgeai.com/01965992-99d2-7937-99a4-2c1821ef6ce9_22.jpg?x=238&y=259&w=1095&h=1688&r=0"/>

Fig. 5. Running times and error levels of PMSJA and R2T with different queries,data scales and $\varepsilon$ .

图5. PMSJA和R2T在不同查询、数据规模及$\varepsilon$条件下的运行时间与误差水平。

<!-- Media -->

First, the error level of DPSJA barely changes with the data scale. That is because theoretically, it only depends on ${\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$ ,which does not change much by the scale of TPC-H data. On the other hand,the error level of ${R2T}$ first increases with query result but will then stay at some level. That is because its error guarantee also depends on ${\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$ and as mentioned before,its error is also bounded by the query result.

首先，DPSJA的误差水平基本不随数据规模变化。理论上这是因为其仅取决于${\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$，而该参数受TPC-H数据规模影响较小。另一方面，${R2T}$的误差水平会随查询结果先上升后趋于稳定，因其误差保证同样依赖于${\mathrm{{DS}}}_{\mathrm{Q}}\left( \mathrm{I}\right)$，且如前所述，其误差也受查询结果限制。

For efficiency, both query and R2T have running time that increases linearly with the data scale while the running time of PMSJA has a super-linear dependency on the data scale.

效率方面，查询和R2T的运行时间均与数据规模呈线性增长，而PMSJA的运行时间与数据规模呈超线性关系。

## ACKNOWLEDGMENTS

## 致谢

This work has been supported by HKRGC under grants 16201819, 16205420, and 16205422. We would also like to thank the anonymous reviewers who have made valuable suggestions on improving the presentation of the paper.

本研究获香港研资局资助（项目编号：16201819、16205420及16205422）。同时感谢匿名评审专家对论文改进提出的宝贵建议。

## REFERENCES

## 参考文献

[1] Martin Abadi, Andy Chu, Ian Goodfellow, H Brendan McMahan, Ilya Mironov, Kunal Talwar, and Li Zhang. 2016. Deep learning with differential privacy. In Proceedings of the 2016 ACM SIGSAC conference on computer and communications security. 308-318.

[2] Kareem Amin, Alex Kulesza, Andres Munoz, and Sergei Vassilvtiskii. 2019. Bounding user contributions: A bias-variance trade-off in differential privacy. In International Conference on Machine Learning. PMLR, 263-271.

[3] Myrto Arapinis, Diego Figueira, and Marco Gaboardi. 2016. Sensitivity of Counting Queries. In International Colloquium on Automata, Languages, and Programming (ICALP).

[4] Boaz Barak, Kamalika Chaudhuri, Cynthia Dwork, Satyen Kale, Frank McSherry, and Kunal Talwar. 2007. Privacy, accuracy, and consistency too: a holistic solution to contingency table release. In Proceedings of the twenty-sixth ACM SIGMOD-SIGACT-SIGART symposium on Principles of database systems. 273-282.

[5] Sourav Biswas, Yihe Dong, Gautam Kamath, and Jonathan Ullman. 2020. CoinPress: Practical Private Mean and Covariance Estimation. Advances in Neural Information Processing Systems 33 (2020).

[6] Jaroslaw Blasiok, Mark Bun, Aleksandar Nikolov, and Thomas Steinke. 2019. Towards instance-optimal private query release. In Proceedings of the Thirtieth Annual ACM-SIAM Symposium on Discrete Algorithms. SIAM, 2480-2497.

[7] Jeremiah Blocki, Avrim Blum, Anupam Datta, and Or Sheffet. 2013. Differentially private data analysis of social networks via restricted sensitivity. In Proceedings of the 4th conference on Innovations in Theoretical Computer Science. 87-96.

[8] Mark Bun and Thomas Steinke. 2016. Concentrated differential privacy: Simplifications, extensions, and lower bounds. In Theory of Cryptography Conference. Springer, 635-658.

[9] Shixi Chen and Shuigeng Zhou. 2013. Recursive mechanism: towards node differential privacy and unrestricted join In Proceedings of the 2013 ACM SIGMOD International Conference on Management of Data. 653-664.

[10] Wei Dong, Juanru Fang, Ke Yi, Yuchao Tao, and Ashwin Machanavajjhala. 2022. R2T: Instance-optimal Truncation for Differentially PrivateQuery Evaluation with Foreign Keys. In Proc. ACM SIGMOD International Conference on Management of Data.

[11] Wei Dong and Ke Yi. 2021. Residual Sensitivity for Deferentially Private Multi-Way Joins. In Proc. ACM SIGMOD International Conference on Management of Data.

[12] Wei Dong and Ke Yi. 2021. Universal Private Estimators. arXiv preprint arXiv:2111.02598 (2021).

[13] Wei Dong and Ke Yi. 2022. A Nearly Instance-optimal Differentially Private Mechanism for Conjunctive Queries. In Proc. ACM Symposium on Principles of Database Systems.

[14] Cynthia Dwork, Frank McSherry, Kobbi Nissim, and Adam Smith. 2006. Calibrating noise to sensitivity in private data analysis. In Theory of cryptography conference. Springer, 265-284.

[15] Cynthia Dwork, Moni Naor, Toniann Pitassi, and Guy N Rothblum. 2010. Differential privacy under continual observation. In Proceedings of the forty-second ACM symposium on Theory of computing. 715-724.

[16] Cynthia Dwork, Moni Naor, Omer Reingold, Guy N Rothblum, and Salil Vadhan. 2009. On the complexity of differentially private data release: efficient algorithms and hardness results. In Proceedings of the forty-first annual ACM symposium on Theory of computing. 381-390.

[17] Cynthia Dwork and Aaron Roth. 2014. The algorithmic foundations of differential privacy. Foundations and Trends® in Theoretical Computer Science 9, 3-4 (2014), 211-407.

[18] Cynthia Dwork, Guy N Rothblum, and Salil Vadhan. 2010. Boosting and differential privacy. In 2010 IEEE 51st Annual Symposium on Foundations of Computer Science. IEEE, 51-60.

[19] Moritz Hardt, Katrina Ligett, and Frank McSherry. 2012. A simple and practical algorithm for differentially private data release. In Advances in Neural Information Processing Systems. 2339-2347.

[20] Michael Hay, Vibhor Rastogi, Gerome Miklau, and Dan Suciu. 2010. Boosting the Accuracy of Differentially Private Histograms Through Consistency. Proceedings of the VLDB Endowment 3, 1 (2010).

[21] Ziyue Huang, Yuting Liang, and Ke Yi. 2021. Instance-optimal Mean Estimation Under Differential Privacy. Advances in Neural Information Processing Systems (2021).

[22] Noah Johnson, Joseph P Near, and Dawn Song. 2018. Towards practical differential privacy for SQL queries. Proceedings of the VLDB Endowment 11, 5 (2018), 526-539.

[23] Gautam Kamath, Jerry Li, Vikrant Singhal, and Jonathan Ullman. 2019. Privately Learning High-Dimensional Distributions. In Proceedings of the 32nd Annual Conference on Learning Theory (COLT '19). 1853-1902.

[24] Vishesh Karwa, Sofya Raskhodnikova, Adam Smith, and Grigory Yaroslavtsev. 2011. Private analysis of graph structure. Proceedings of the VLDB Endowment 4, 11 (2011), 1146-1157.

[25] Vishesh Karwa, Sofya Raskhodnikova, Adam Smith, and Grigory Yaroslavtsev. 2014. Private analysis of graph structure. ACM Transactions on Database Systems (TODS) 39, 3 (2014), 1-33.

[26] Shiva Prasad Kasiviswanathan, Kobbi Nissim, Sofya Raskhodnikova, and Adam Smith. 2013. Analyzing graphs with node differential privacy. In Theory of Cryptography Conference. Springer, 457-476.

[27] Ios Kotsogiannis, Yuchao Tao, Xi He, Maryam Fanaeepour, Ashwin Machanavajjhala, Michael Hay, and Gerome Miklau. 2019. PrivateSQL: a differentially private SQL query engine. Proceedings of the VLDB Endowment 12, 11 (2019), 1371-1384.

[28] Jure Leskovec and Andrej Krev. 2016. SNAP datasets: Stanford large network dataset collection (2014). URL http://snap.stanford.edu/data (2016), 49.

[29] Chao Li, Gerome Miklau, Michael Hay, Andrew McGregor, and Vibhor Rastogi. 2015. The matrix mechanism: optimizing linear counting queries under differential privacy. The VLDB journal 24, 6 (2015), 757-781.

[30] Frank D McSherry. 2009. Privacy integrated queries: an extensible platform for privacy-preserving data analysis. In Proceedings of the 2009 ACM SIGMOD International Conference on Management of data. 19-30.

[31] Shanmugavelayutham Muthukrishnan and Aleksandar Nikolov. 2012. Optimal private halfspace counting via discrep ancy. In Proceedings of the forty-fourth annual ACM symposium on Theory of computing. 1285-1292.

[32] Arjun Narayan and Andreas Haeberlen. 2012. DJoin: Differentially private join queries over distributed databases. In USENIX Symposium on Operating Systems Design and Implementation. 149-162.

[33] Aleksandar Nikolov, Kunal Talwar, and Li Zhang. 2013. The geometry of differential privacy: the sparse and approximate cases. In Proceedings of the forty-fifth annual ACM symposium on Theory of computing. 351-360.

[34] Kobbi Nissim, Sofya Raskhodnikova, and Adam Smith. 2007. Smooth sensitivity and sampling in private data analysis. In Proceedings of the thirty-ninth annual ACM symposium on Theory of computing. 75-84.

[35] Catuscia Palamidessi and Marco Stronati. 2012. Differential Privacy for Relational Algebra: Improving the Sensitivity Bounds via Constraint Systems. In ${QAPL}$ .

[36] Davide Proserpio, Sharon Goldberg, and Frank McSherry. 2014. Calibrating Data to Sensitivity in Private Data Analysis. Proceedings of the VLDB Endowment 7, 8 (2014).

[37] Wahbeh Qardaji, Weining Yang, and Ninghui Li. 2013. Understanding hierarchical methods for differentially private histograms. Proceedings of the VLDB Endowment 6, 14 (2013), 1954-1965.

[38] Wahbeh Qardaji, Weining Yang, and Ninghui Li. 2014. Priview: practical differentially private release of marginal contingency tables. In Proceedings of the 2014 ACM SIGMOD international conference on Management of data. 1435-1446.

[39] Yuan Qiu, Wei Dong, Ke Yi, Bin Wu, and Feifei Li. 2022. Releasing Private Data for Numerical Queries. In Proceedings of the 28th ACM SIGKDD Conference on Knowledge Discovery and Data Mining. 1410-1419.

[40] Yuchao Tao, Xi He, Ashwin Machanavajjhala, and Sudeepa Roy. 2020. Computing Local Sensitivities of Counting Queries with Joins. In Proceedings of the 2020 ACM SIGMOD International Conference on Management of Data. 479-494.

[41] Xiaokui Xiao, Guozhang Wang, and Johannes Gehrke. 2010. Differential privacy via wavelet transforms. IEEE Transactions on knowledge and data engineering 23, 8 (2010), 1200-1214.

[42] Ganzhao Yuan, Yin Yang, Zhenjie Zhang, and Zhifeng Hao. 2016. Convex optimization for linear query processing under approximate differential privacy. In Proceedings of the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining. 2005-2014.

[43] Ganzhao Yuan, Zhenjie Zhang, Marianne Winslett, Xiaokui Xiao, Yin Yang, and Zhifeng Hao. 2015. Optimizing batch linear queries under exact and approximate differential privacy. ACM Transactions on Database Systems (TODS) 40, 2 (2015), 1-47.

[44] Jun Zhang, Graham Cormode, Cecilia M Procopiuc, Divesh Srivastava, and Xiaokui Xiao. 2015. Private release of graph statistics using ladder functions. In Proceedings of the 2015 ACM SIGMOD international conference on management of data. 731-745.

[45] Xiaojian Zhang, Rui Chen, Jianliang Xu, Xiaofeng Meng, and Yingtao Xie. 2014. Towards accurate histogram publication under differential privacy. In Proceedings of the 2014 SIAM international conference on data mining. SIAM, 587-595.