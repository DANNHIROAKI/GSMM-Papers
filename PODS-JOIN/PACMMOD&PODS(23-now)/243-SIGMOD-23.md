# DP-starJ: A Differential Private Scheme towards Analytical Star-Join Queries

DP-starJ：面向星型连接分析查询的差分隐私方案

CONGCONG FU, Xidian University, China

付聪聪，中国西安电子科技大学

HUI LI, Xidian University, China and Yunxi Technology Co., Ltd, China

李辉，中国西安电子科技大学与中国云栖科技有限公司

JIAN LOU, Zhejiang University, China

娄健，中国浙江大学

HUIZHEN LI, Xidian University, China

李慧珍，中国西安电子科技大学

JIANGTAO CUI, Xidian University, China

崔江涛，中国西安电子科技大学

Star-join query is the fundamental task in data warehouse and has wide applications in On-line Analytical Processing (OLAP) scenarios. Due to the large number of foreign key constraints and the asymmetric effect in the neighboring instance between the fact and dimension tables,even those latest DP efforts specifically designed for join, if directly applied to star-join query, will suffer from extremely large estimation errors and expensive computational cost.

星型连接查询是数据仓库的基础任务，在联机分析处理（OLAP）场景中应用广泛。由于外键约束数量庞大以及事实表与维度表在相邻实例中的不对称影响，即使那些专为连接操作设计的最新差分隐私方案，若直接应用于星型连接查询，也会面临极大估计误差和过高计算成本的问题。

In this paper, we are thus motivated to propose DP-starJ, a novel Differentially Private framework for star-Join queries. DP-starJ consists of a series of strategies tailored to specific features of star-join, including 1) we unveil the different effects of fact and dimension tables on the neighboring database instances, and accordingly revisit the definitions tailored to different cases of star-join; 2) we propose Predicate Mechanism (PM), which utilizes predicate perturbation to inject noise into the join procedure instead of the results; 3) to further boost the robust performance, we propose a DP-compliant star-join algorithm for various types of star-join tasks based on PM. We provide both theoretical analysis and empirical study, which demonstrate the superiority of the proposed methods over the state-of-the-art solutions in terms of accuracy, efficiency, and scalability.

本文由此提出DP-starJ——专为星型连接查询设计的新型差分隐私框架。该框架包含针对星型连接特性的系列策略：1）揭示事实表与维度表对相邻数据库实例的不同影响，据此重构适应不同星型连接场景的定义；2）提出谓词扰动机制（PM），通过在连接过程中而非结果上注入噪声；3）基于PM开发符合差分隐私的星型连接算法，适配多种查询类型。理论与实验证明，该方法在准确性、效率与可扩展性上均优于现有最佳方案。

CCS Concepts: - Security and privacy $\rightarrow$ Security services; Data anonymization and sanitization; - Information systems $\rightarrow$ Data warehouses; Relational database model.

CCS概念：- 安全与隐私 $\rightarrow$ 安全服务；数据匿名与脱敏 - 信息系统 $\rightarrow$ 数据仓库；关系数据库模型

Additional Key Words and Phrases: star-join, data warehouse, differential privacy, local sensitivity

附加关键词：星型连接、数据仓库、差分隐私、局部敏感度

## ACM Reference Format:

## ACM参考文献格式：

Congcong Fu, Hui Li, Jian Lou, Huizhen Li, and Jiangtao Cui. 2023. DP-starJ: A Differential Private Scheme towards Analytical Star-Join Queries. Proc. ACM Manag. Data 1, 4 (SIGMOD), Article 238 (December 2023), 24 pages. https://doi.org/10.1145/3626725

付聪聪、李辉、娄健、李慧珍、崔江涛。2023。DP-starJ：面向星型连接分析查询的差分隐私方案。ACM数据管理会刊1卷4期（SIGMOD），文章238（2023年12月），24页。https://doi.org/10.1145/3626725

## 1 INTRODUCTION

## 1 引言

Star-join query is a common type of query in data warehouse applications, especially on star schema warehouse, where a fact table is joined with one or more dimension tables. It usually performs some filtering on dimension tables, joins the dimension tables with the fact table, and executes some optional aggregation on that. The following provides the formal definition of star-join query.

星型连接查询是数据仓库中的常见操作，特别适用于星型模式仓库，其中事实表与一个或多个维度表进行连接。通常先对维度表执行筛选，再与事实表连接，最后可选地进行聚合操作。下文给出星型连接查询的形式化定义。

Permission to make digital or hard copies of all or part of this work for personal or classroom use is granted without fee provided that copies are not made or distributed for profit or commercial advantage and that copies bear this notice and the full citation on the first page. Copyrights for components of this work owned by others than the author(s) must be honored. Abstracting with credit is permitted. To copy otherwise, or republish, to post on servers or to redistribute to lists, requires prior specific permission and/or a fee. Request permissions from permissions@acm.org.

本作品允许为个人或课堂教学目的制作数字或硬拷贝，但不得为商业利益制作或分发副本，且副本须包含首页完整版权声明。非作者持有的作品组成部分版权须受尊重。允许标注来源的摘要引用。其他复制、重发布、服务器张贴或列表分发行为需获得特别许可和/或费用。许可申请请致信permissions@acm.org。

© 2023 Copyright held by the owner/author(s). Publication rights licensed to ACM.

© 2023 版权归作者所有。出版权由ACM授权许可。

2836-6573/2023/12-ART238 \$15.00

2836-6573/2023/12-ART238 \$15.00

https://doi.org/10.1145/3626725

---

<!-- Footnote -->

Authors' addresses: Congcong Fu, Xidian University, Xi'an, China, ccfu@stu.xidian.edu.cn; Hui Li, Xidian University, Xi'an, China and Yunxi Technology Co., Ltd, Shanghai, China, hli@xidian.edu.cn; Jian Lou, Zhejiang University, Hangzhou, China, jian.lou@zju.edu.cn; Huizhen Li, Xidian University, Xi'an, China, 22151214331@stu.xidian.edu.cn; Jiangtao Cui, Xidian University, Xi'an, China, cuijt@xidian.edu.cn.

作者联系方式：付聪聪，西安电子科技大学，中国西安，ccfu@stu.xidian.edu.cn；李辉，西安电子科技大学，中国西安，及云曦科技有限公司，中国上海，hli@xidian.edu.cn；娄健，浙江大学，中国杭州，jian.lou@zju.edu.cn；李慧珍，西安电子科技大学，中国西安，22151214331@stu.xidian.edu.cn；崔江涛，西安电子科技大学，中国西安，cuijt@xidian.edu.cn。

<!-- Footnote -->

---

<!-- Media -->

<!-- figureText: Customer Part PartKey Lineorder Category CustKey PartKey SuppKey orderdate Date Quantity DateKey Date CustKey Address Supplier SuppKey Address -->

<img src="https://cdn.noedgeai.com/01965995-a2c5-7ee2-a7da-5837c049adeb_1.jpg?x=457&y=262&w=653&h=396&r=0"/>

Fig. 1. An example of star schema with 4 dimension tables

图1. 包含4个维度表的星型模式示例

<!-- Media -->

Definition 1.1 (Star-Join). Let $\mathbf{R}$ be a database schema containing $n + 1$ tables,namely ${R}_{0},\ldots ,{R}_{n}$ . We start with a star-way join :

定义1.1（星型连接）。设$\mathbf{R}$为包含$n + 1$张表的数据库模式，即${R}_{0},\ldots ,{R}_{n}$。我们以星型连接开始：

$$
J \mathrel{\text{:=}} {R}_{0}\left( {\mathbf{x}}_{0}\right)  \boxtimes  {R}_{1}\left( {\mathbf{x}}_{1}\right)  \boxtimes  \ldots  \boxtimes  {R}_{n}\left( {\mathbf{x}}_{n}\right) , \tag{1}
$$

where ${R}_{1},\ldots ,{R}_{n}$ are dimension tables and ${R}_{0}$ is a fact table. We use $\left\lbrack  n\right\rbrack$ to denote $\{ 1,\ldots ,n\}$ and each ${\mathbf{x}}_{i}\left( {i \in  \left\lbrack  n\right\rbrack  }\right)$ of dimension table ${R}_{i}\left( {i \in  \left\lbrack  n\right\rbrack  }\right)$ consist of a join key ${k}_{i}$ and attribute ${a}_{i},{\mathbf{x}}_{i} = \left\{  {{k}_{i},{a}_{i}}\right\}$ . Yet ${\mathbf{x}}_{0}$ consist of all join keys ${k}_{i}$ and a measure attribute ${a}_{0},{\mathbf{x}}_{0} = \left\{  {{k}_{1},\ldots ,{k}_{n},{a}_{0}}\right\}$ . Let $\operatorname{var}\left( J\right)  \mathrel{\text{:=}} {a}_{0} \cup  \ldots  \cup  {a}_{n}$ is a set of variables in the join result $J$ . Each attribute ${a}_{i}$ has a finite domain $\operatorname{dom}\left( {a}_{i}\right)$ with size $\left| {\operatorname{dom}\left( {a}_{i}\right) }\right|  = {m}_{i}$ ,the full domain of $\mathbf{R}$ is $\operatorname{dom}\left( \mathbf{R}\right)  = \operatorname{dom}\left( {a}_{0}\right)  \times  \ldots  \times  \operatorname{dom}\left( {a}_{n}\right)$ and has size $m = \mathop{\prod }\limits_{i}{m}_{i}$ .

其中${R}_{1},\ldots ,{R}_{n}$为维度表，${R}_{0}$为事实表。$\left\lbrack  n\right\rbrack$表示$\{ 1,\ldots ,n\}$，每个维度表${R}_{i}\left( {i \in  \left\lbrack  n\right\rbrack  }\right)$的${\mathbf{x}}_{i}\left( {i \in  \left\lbrack  n\right\rbrack  }\right)$包含连接键${k}_{i}$和属性${a}_{i},{\mathbf{x}}_{i} = \left\{  {{k}_{i},{a}_{i}}\right\}$。而${\mathbf{x}}_{0}$包含所有连接键${k}_{i}$及度量属性${a}_{0},{\mathbf{x}}_{0} = \left\{  {{k}_{1},\ldots ,{k}_{n},{a}_{0}}\right\}$。设$\operatorname{var}\left( J\right)  \mathrel{\text{:=}} {a}_{0} \cup  \ldots  \cup  {a}_{n}$是连接结果$J$中的变量集合。每个属性${a}_{i}$具有有限域$\operatorname{dom}\left( {a}_{i}\right)$（大小为$\left| {\operatorname{dom}\left( {a}_{i}\right) }\right|  = {m}_{i}$），$\mathbf{R}$的完整域为$\operatorname{dom}\left( \mathbf{R}\right)  = \operatorname{dom}\left( {a}_{0}\right)  \times  \ldots  \times  \operatorname{dom}\left( {a}_{n}\right)$（大小为$m = \mathop{\prod }\limits_{i}{m}_{i}$）。

<!-- Media -->

Example 1.2. Suppose a data analyst is interested in the total number of items sold in the first half of this year in a given region, s/he would execute the following query (assuming the query is performed on the Star Schema Benchmark (SSB) [28]):

示例1.2。假设数据分析师需要统计今年上半年某指定区域的商品销售总量，将执行以下查询（假设查询基于星型模式基准测试(SSB)[28]）：

---

SELECT count (*)

SELECT count (*)

FROM Date, Customer, Supplier, Part, Lineorder

FROM 日期表, 客户表, 供应商表, 零件表, 订单表

WHERE Lineroder.CK = Customer.CK

WHERE 订单表.CK = 客户表.CK

	AND Lineroder. SK = Supplier. SK

	AND 订单表.SK = 供应商表.SK

	AND Lineroder. PK = Part. PK

	AND 订单表.PK = 零件表.PK

	AND Lineorder.orderdate = Date.DK

	AND 订单表.订单日期 = 日期表.DK

	AND Customer.region = '[REGION]'

	AND 客户表.区域 = '[区域]'

	AND Supplier.region = '[REGION]'

	AND 供应商表.区域 = '[区域]'

	AND Date.month < 7 ;

	AND 日期表.月份 < 7 ;

---

<!-- Media -->

Figure 1 shows an example of standard star schema where the query in Definition 1.1 can be applied and Example 1.2 shows an example of a star-join query. Such queries involving star-joins are ubiquitous within analytical tasks and act as a core query category in the data warehouse. Due to such a pivotal role in data analytics, star-join has been extensively studied in the literature and widely applied in On-line Analytical Processing (OLAP) practice. Meanwhile, unlike the other types of joint queries that all tables can be connected to each other, all dimension tables in star-join will be directly linked to the fact table through the foreign-key constraints. In the above example, the relations touched by the query contain private information,e.g.,customer ${c}_{1}$ has placed a particular order ${o}_{2}$ ,suppliers ${s}_{1},\ldots ,{s}_{m}$ provide an item ${i}_{1}$ ,of which the privacy must be protected in practical scenarios.

图1展示了一个标准星型模式的实例，定义1.1中的查询可应用于此，而示例1.2则演示了星型连接查询。这类涉及星型连接的查询在分析任务中无处不在，是数据仓库的核心查询类别。由于在数据分析中的关键作用，星型连接在文献中已被广泛研究，并普遍应用于联机分析处理（OLAP）实践中。与其他类型的连接查询不同（所有表可相互连接），星型连接中所有维度表都通过外键约束直接关联到事实表。上例中，查询涉及的关系包含隐私信息，例如客户${c}_{1}$下过特定订单${o}_{2}$、供应商${s}_{1},\ldots ,{s}_{m}$提供商品${i}_{1}$，这些信息在实际场景中必须受到保护。

At present, differential privacy (DP) has become a popular solution in privacy-preserving data analytics as it provides a statistically rigorous privacy guarantee. Since its introduction [10, 11], DP has attracted ever-growing interest in academia, government agencies, and industry. The standard DP mechanism (e.g., Laplace Mechanism) first finds the global sensitivity of the query, then it adds a carefully calibrated random noise tailored to the query result. High sensitivity can introduce large noise, which results in a distorted query result offering poor utility. In particular, the global sensitivity of the query refers to how much the query result may change in two neighboring instances of databases. Consequently, a proper definition of neighboring instances is of great importance in DP, which not only determines whether the DP mechanism built on it offers sound and practical privacy protection, but also affects the sensitivity and eventually the utility of the DP mechanism. Since such noise for the privacy-preserving purpose will unavoidably cause utility degradation for the query result,a central problem in $\mathrm{{DP}}$ is how to achieve a satisfactory tradeoff between privacy and utility. Existing works [17, 22, 31, 35, 37, 40, 44] have proven that DP mechanism usually achieves a better privacy-utility trade-off when its design is tailored to the specific data analysis task under consideration. In this regard, this paper, for the first time, proposes a solution towards answering star-join queries under differential privacy.

当前，差分隐私（DP）因其提供统计严谨的隐私保障，已成为隐私保护数据分析的主流解决方案。自[10,11]提出以来，DP持续吸引着学术界、政府机构和产业界日益增长的兴趣。标准DP机制（如拉普拉斯机制）首先计算查询的全局敏感度，然后向查询结果添加精心校准的随机噪声。高敏感度会导致强噪声干扰，使查询结果失真而丧失实用性。特别地，查询的全局敏感度指该查询在两个相邻数据库实例中结果的最大变化量。因此，准确定义相邻实例对DP至关重要——这不仅决定基于该定义的DP机制能否提供可靠且实用的隐私保护，还直接影响敏感度及最终机制效用。由于这种隐私保护噪声必然导致查询结果效用下降，$\mathrm{{DP}}$的核心问题在于如何实现隐私与效用的最佳平衡。现有研究[17,22,31,35,37,40,44]表明，当DP机制针对特定数据分析任务定制设计时，通常能获得更好的隐私-效用权衡。鉴于此，本文首次提出了差分隐私下星型连接查询的解决方案。

Roughly,the efforts of recent works in DP query processing [8, 19, 36] focus on three aspects to reduce the high global sensitivity: reduce query sensitivity by utilizing the upper bound of the local sensitivity, design an algorithm that effectively computes tight local sensitivity, and transform the database instance by deleting some tuples that are highly sensitive. However, different from other types of queries that all tables can be linked through join operations, the star-join query has a non-trivial number of foreign key constraints that a single fact table references a series of dimension ones. Due to that, answering a star-join query in a DP manner is more challenging because high global sensitivity results from the large number of foreign key constraints in star-join query, the DP mechanism of high global sensitivity fails to work, as the output of a join may contain duplicate sensitive rows. This duplication is difficult to bound as it depends on the join type, join condition, and the underlying data. Therefore, the global sensitivity becomes unbounded when joins are present because a single tuple may affect many join results. Therefore, the existing DP-compliant query strategies with a trusted server may not be able to provide satisfactory utility and efficiency, which motivates us to present the solutions in this work.

概言之，近期DP查询处理研究[8,19,36]主要从三方面降低高全局敏感度：利用局部敏感度上界降低查询敏感度、设计高效计算紧致局部敏感度的算法、通过删除高敏感元组来转换数据库实例。然而与其他可通过连接操作关联所有表的查询类型不同，星型连接查询存在大量外键约束——单个事实表关联多个维度表。这导致以DP方式应答星型连接查询更具挑战性：外键约束数量庞大引发高全局敏感度，而高敏感度的DP机制会失效，因为连接结果可能包含重复的敏感行。这种重复难以界定，因其取决于连接类型、连接条件和底层数据。因此存在连接操作时全局敏感度无界，单个元组可能影响多个连接结果。现有基于可信服务器的DP查询策略难以提供令人满意的效用和效率，这促使我们提出本研究的解决方案。

Example 1.3. The following is a simplest star-join query:

示例1.3. 以下是最简单的星型连接查询：

$$
q \mathrel{\text{:=}} \text{Customer}\left( {\underline{CK},\text{ Address,... }}\right)  \boxtimes  \text{Lineorder}\left( {{CK}\text{,orderdate,... }}\right) \text{.}
$$

Here, Customer may store customer information and Lineorder contains the orders the customers have placed. Then this query simply returns the total numbers of orders. Suppose the identities for the entities in Customer are private information we aim to protect. Unfortunately, the global sensitivity of this query is $\infty$ under existing DP solutions [11]. The reason is as follows,a customer could have an unbounded number of orders, and adding such a customer to the database can cause an unbounded change in the query result theoretically. To address this issue, some works [8, 26] suggest adding data-dependent noise to the query result. For instance, [26] proposes to use the local sensitivity, i.e., the sensitivity of the join query on the given database instance, which is usually much lower than global sensitivity. However, if applied in star-join query, it still leads to high sensitivity and further results in a low utility. The key challenge is how to decrease the global sensitivity of the star-join queries when designing the DP schemes.

本例中，Customer表存储客户信息，Lineorder表记录客户订单。该查询仅返回订单总数。假设Customer表中实体身份属待保护隐私信息。遗憾的是，在现有DP方案[11]下该查询的全局敏感度为$\infty$。原因在于：理论上客户可能拥有无限量订单，添加此类客户会导致查询结果无界变化。为解决该问题，部分研究[8,26]建议向查询结果添加数据依赖性噪声。例如[26]提出使用局部敏感度（即给定数据库实例上连接查询的敏感度），其通常远低于全局敏感度。但若应用于星型连接查询，仍会导致高敏感度及低效用。关键挑战在于设计DP方案时如何降低星型连接查询的全局敏感度。

Meanwhile, within star-join the tuples from the fact and the dimension tables shall affect the query result differently. For this reason, there also exist several different cases for neighboring database instances depending on whether the fact or the dimension tables are private. Therefore, before presenting a well-designed DP-compliant star-join solution, it is necessary to revisit the definition of neighboring database instances due to the asymmetry between the fact and dimension table. Accordingly, the DP-compliant star-join solution should take into account the fact that the definition of neighboring database instances may vary between scenarios.

在星型连接中，事实表与维度表的元组对查询结果的影响存在差异。因此，根据事实表或维度表是否属于隐私数据，邻近数据库实例会呈现多种不同情况。在提出符合差分隐私的星型连接解决方案前，必须重新审视邻近数据库实例的定义，以应对事实表与维度表的非对称特性。相应地，该方案需考虑邻近数据库实例定义可能随场景变化的特点。

In this paper, we systematically investigate the differential privacy star-join query problem. Our study first reveals that the existing approaches of the traditional DP-compliant join schemes [8, 16, 36], which work by adding subtly noise to the join result, fail to achieve a satisfactory utility and efficiency in star-join queries. Thus, we are further motivated to propose an advanced approach called DP-starJ, a Differentially Private framework for star-Join queries. To achieve that, we first investigate and unveil the asymmetry between the fact and dimension tables in the effect on neighboring instances of star-join. Driven by that unique nature, instead of considering a uniform definition and simplified case for neighboring database instance as existing DP schemes [7, 8], we propose a fine-grained definition for neighboring database instance tailored to the asymmetry characteristics of star-join task. On the other hand, as discussed above, truncating some highly sensitivity tuples or adding data-dependent noise towards the result fails to achieve a satisfactory utility and efficiency due to the large number of foreign keys, we are also motivated to propose a new perturbation mechanism to achieve superior utility and efficiency by adding the data-independent noise with bounded global sensitivity, namely Predicate Mechanism. Using the proposed mechanism as a building block, we present an DP-compliant star-join algorithm for various types of star-join tasks (i.e., aggregate query, "group_by" operation, and workload queries). Further, our theoretical study shows that the proposed methods obtain asymptotically optimal error bound on star-join. Empirical study over several real-world datasets justifies the superiority of our solution in the aspect of both utility and efficiency across various star-join tasks.

本文系统研究了差分隐私星型连接查询问题。研究发现，传统差分隐私连接方案[8,16,36]通过向连接结果添加精妙噪声的方法，在星型连接查询中无法实现理想的效用与效率。为此，我们提出名为DP-starJ的先进框架——面向星型连接查询的差分隐私解决方案。研究首先揭示了事实表与维度表对星型连接邻近实例影响的非对称性。基于此特性，我们摒弃现有差分隐私方案[7,8]采用的统一简化定义，提出了适配星型连接非对称特性的细粒度邻近数据库实例定义。另一方面，针对外键数量庞大导致截断高敏感度元组或添加数据依赖型噪声难以实现理想效果的问题，我们创新性地提出通过添加具有全局敏感度约束的数据独立型噪声（即谓词机制）来实现更优性能。以此为基础，我们开发了适用于聚合查询、"分组"操作及工作负载查询等各类星型连接任务的差分隐私算法。理论分析表明该方法在星型连接上具有渐进最优误差界，多组真实数据集实验验证了方案在效用与效率上的优越性。

The contributions of this paper are summarized as follows:

本文贡献可概括如下：

- We unveil the asymmetry between the fact and dimension tables in affecting the neighboring database instances and revisit the accordingly definitions to tailor to different cases of star-join.

- 揭示事实表与维度表影响邻近数据库实例的非对称性，重构适配不同星型连接场景的定义

- We propose a Predicate Mechanism under DP-starJ, which designs a new perturbation strategy to inject noise towards the star-join procedure instead of purely the results. Meanwhile, we further propose an DP-compliant star-join algorithm for various types of star-join tasks.

- 在DP-starJ框架下提出谓词机制，设计面向星型连接过程（而非仅结果）的新型噪声注入策略，并开发支持各类星型连接任务的差分隐私算法

- We prove theoretically that the proposed method obtains asymptotically optimal error bound on star-join queries and experimental study justifies the superiority of our solution in the aspects of both utility and efficiency.

- 理论证明所提方法在星型连接查询中具有渐进最优误差界，实验验证方案在效用与效率上的优越性

## 2 RELATED WORK

## 2 相关工作

Early works mostly focus on answering a given arbitrary SQL query under DP, which is acknowledged as the holy grail of private query processing. There have been several works on answering various types of queries under DP $\left\lbrack  {2,5,{18},{24},{42}}\right\rbrack$ but not star-join,which has always been the core and basis for the majority of OLAP applications [14]. At present, there is no work that is specifically designed to answer the star-join query in a privacy-preserving manner under trusted server settings. Since the elegant work by Dwork [10], there are plenty of works [4, 8, 9, 36] proposed to limit the sensitivity of join queries and extensions for optimizing multi-join queries. In addition, DP-compliant SQL query processing has also been extensively applied in industrial systems, for instance, Uber implements Flex [16] that answers SQL queries with DP.

早期研究主要聚焦差分隐私下的任意SQL查询应答——这一隐私查询处理领域的终极目标。现有工作虽涉及多种差分隐私查询类型$\left\lbrack  {2,5,{18},{24},{42}}\right\rbrack$，但作为OLAP应用核心的星型连接始终未被专门研究[14]。目前尚无以可信服务器环境为前提的隐私保护型星型连接查询方案。自Dwork开创性研究[10]以来，众多工作[4,8,9,36]致力于限制连接查询敏感度及优化多连接查询。差分隐私SQL查询处理技术已在工业系统广泛应用，如Uber实现的Flex系统[16]可提供符合差分隐私的SQL查询服务。

Many technologies have been proposed to answer set counting queries over a single relation with different predicates $\left\lbrack  {2,3,6,{15},{25},{31},{32},{39},{43}}\right\rbrack$ . Most existing work on join queries can only support restricted types of joins,such as joins with primary keys $\left\lbrack  {1,{23},{24},{29},{30}}\right\rbrack$ and joins with a fixed join attribute [38]. One approach is to reduce the high sensitivity of join queries by truncation. For instance, PrivateSQL [19] uses naive truncation to truncate the tuples with high degrees. Tao et al. [36] use naive truncation to truncate the tuples with high sensitivity for some queries without self-join and they propose a mechanism to select the truncation threshold. Dong et al. [36] proposed a mechanism Race-to-the-Top (R2T), which can be used to adaptively choose the truncation threshold. However, if applied in star-join, the truncation-based solution will cause a significant biased result due to the foreign key constraints between the large number of dimension tables and fact table. Another approach is adding data-dependent noise calibrated by other types of sensitivity rather than global sensitivity. Smooth sensitivity [26] is a popular approach for dealing with multi-way joins. Elastic sensitivity [16] and residual sensitivity [8], both of which are efficiently computable versions of smooth sensitivity, can handle join queries efficiently. However, smooth sensitivity (including any efficiently computable version) cannot support foreign key constraints, which are important to model the relationship between an individual and all his/her associated records in a relational database. Similarly, these methods cannot balance utility and efficiency in the star-join query under DP.

已有多种技术被提出用于回答单关系下不同谓词的集合计数查询$\left\lbrack  {2,3,6,{15},{25},{31},{32},{39},{43}}\right\rbrack$。现有关于连接查询的研究大多仅支持受限的连接类型，例如主键连接$\left\lbrack  {1,{23},{24},{29},{30}}\right\rbrack$和固定连接属性的查询[38]。一种解决方案是通过截断来降低连接查询的高敏感性。例如PrivateSQL[19]采用简单截断法处理高度数元组；Tao等人[36]针对无自连接的查询使用简单截断法处理高敏感性元组，并提出截断阈值选择机制；Dong等人[36]提出的Race-to-the-Top(R2T)机制可自适应选择截断阈值。但若应用于星型连接，基于截断的方案会因大量维度表与事实表间的外键约束导致显著偏差结果。另一种方案是添加基于非全局敏感性的数据依赖性噪声。平滑敏感性[26]是处理多路连接的常用方法，其高效计算版本——弹性敏感性[16]和残差敏感性[8]能有效处理连接查询。然而这些方法（包括所有高效计算版本）均不支持外键约束，而该约束对建模关系数据库中个体与其关联记录的关系至关重要。同样，这些方法无法在差分隐私下平衡星型连接查询的效用与效率。

In comparison, the star-join queries have a non-trivial number of foreign key constraints in multiway joins scenarios and the goal is to effectively get accurate query answers even when the star-join query contains a large number of dimension tables. The existing DP-compliant query strategies with trusted servers do not satisfy the practical requirements, which motivates us to present the solutions in this work.

相较而言，星型连接查询在多路连接场景中存在大量外键约束，其目标是在包含众多维度表时仍能获取精确查询结果。现有基于可信服务器的差分隐私查询策略无法满足实际需求，这促使我们提出本研究的解决方案。

## 3 PRELIMINARY AND PROBLEM DEFINITION

## 3 基础理论与问题定义

### 3.1 Preliminaries

### 3.1 理论基础

Star-join Query. Many relational data warehouse designs today follow a so-called dimensional modeling approach that has been made popular by Galindo et al. [14]. Dimensional modeling relies on the distinction of dimension tables with relatively static information in contrast to fact tables that store transactional statistical information. For instance, according to the TPC-H benchmark schema, dimensional table hold master data representing entitles such as part, customers, suppliers, and date. In comparison, the fact table in turn stores transactional data, e.g., lineorder contains statistics about sales or orders. Dimension tables and fact tables are correlated with each other by foreign key constraints. Usually, fact tables are several orders of magnitude larger than the dimension ones. Dimensional modeling leads to the well-known so-called star schema and snowflake schema design for data warehousing. A star schema consists of a fact table in the center of the star, and it is very popular for modeling data warehouses and data marts. The fact table contains foreign keys, which are pointing to the dimension tables, and the dimension tables contain a key used to join with the fact table and additional attributes.

星型连接查询。现代关系型数据仓库设计多遵循Galindo等人[14]推广的维度建模方法，该方法区分存储静态信息的维度表与存储事务统计信息的事实表。以TPC-H基准模式为例，维度表保存表示实体（如零件、客户、供应商和日期）的主数据，事实表则存储如包含销售/订单统计的lineorder等事务数据。维度表与事实表通过外键约束关联，通常事实表规模比维度表大数个数量级。这种建模形成了数据仓库中著名的星型模式和雪花模式设计。星型模式以事实表为中心，通过外键指向维度表，维度表则包含连接键及附加属性。

Star-join queries are queries on a database instance that the fact table is joined with one or more dimension tables, it selects several measures of interest from the fact table, joins the fact rows with one or several dimensions with respect to the keys, places filter predicates on the business columns of the dimension tables, performs grouping if required, and finally aggregates the measures retrieved from the fact table. As the star-join query in OLAP task places filter predicates on the attributes of the dimension tables, and finally aggregates the measure attribute from the fact table, the star-join query can be converted into a predicate query. Predicate queries are a versatile class, consisting of queries that satisfy any logical predicate. A predicate corresponds to a condition in the WHERE clause of an SQL statement, and a star-join query is a SQL query with aggregation on measure attributes of the fact table and predicates with equality and range constraints on some dimension tables. The following showcase the template for star-join queries in the form of a standard predicated SELECT SQL statement:

星型连接查询指事实表与一个或多个维度表连接的数据库实例查询：从事实表选取目标度量，按键连接事实行与维度表，在维度表的业务列上设置过滤谓词，按需分组后聚合事实表度量。由于OLAP任务中的星型连接查询在维度表属性上设置过滤谓词并最终聚合事实表度量属性，可转换为谓词查询。谓词查询是满足任意逻辑条件的通用查询类，对应SQL语句WHERE子句中的条件。星型连接查询即对事实表度量属性聚合，并对某些维度表施加等值/范围约束的SQL查询。以下展示标准谓词SELECT语句形式的星型连接查询模板：

$$
\text{SELECT Aggr(*) FROM R WHERE}\Phi \text{;}
$$

Aggr(*) refers to an aggregate function (e.g.,COUNT,AVG,SUM) over the fact table. $\Phi$ means conjunctions of filter conditions that consists of arbitrary predicates $\phi$ on attributes over the dimension tables. When a star-join query refers only to an attribute ${a}_{i}\left( {i \in  \left\lbrack  n\right\rbrack  }\right)$ in dimension table ${R}_{i}$ we may say that it is defined with respect to ${a}_{i}$ and annotate it as $\Phi  \mathrel{\text{:=}} {\phi }_{{a}_{i}},\left( {{\phi }_{{a}_{i}} : \operatorname{dom}\left( {a}_{i}\right)  \rightarrow  \{ 0,1\} }\right)$ . Similarly,if ${\phi }_{{a}_{i}}$ and ${\phi }_{{a}_{j}}$ are predicates on dimension tables ${R}_{i}$ and ${R}_{j}$ in a star-join query,then $\Phi$ is the conjunctions of predicates $\Phi  \mathrel{\text{:=}} {\phi }_{{a}_{i}} \land  {\phi }_{{a}_{j}},\left( {{\phi }_{{a}_{i}} \land  {\phi }_{{a}_{j}} : \operatorname{dom}\left( {{a}_{i} \cup  {a}_{j}}\right)  \rightarrow  \{ 0,1\} }\right)$ .

Aggr(*)表示事实表上的聚合函数（如COUNT计数、AVG平均值、SUM求和）。$\Phi$代表由维度表属性上任意谓词$\phi$组成的过滤条件合取式。当星型连接查询仅涉及维度表${R}_{i}$中的属性${a}_{i}\left( {i \in  \left\lbrack  n\right\rbrack  }\right)$时，可称其基于${a}_{i}$定义并标注为$\Phi  \mathrel{\text{:=}} {\phi }_{{a}_{i}},\left( {{\phi }_{{a}_{i}} : \operatorname{dom}\left( {a}_{i}\right)  \rightarrow  \{ 0,1\} }\right)$。类似地，若${\phi }_{{a}_{i}}$和${\phi }_{{a}_{j}}$是星型连接查询中维度表${R}_{i}$与${R}_{j}$上的谓词，则$\Phi$即为谓词$\Phi  \mathrel{\text{:=}} {\phi }_{{a}_{i}} \land  {\phi }_{{a}_{j}},\left( {{\phi }_{{a}_{i}} \land  {\phi }_{{a}_{j}} : \operatorname{dom}\left( {{a}_{i} \cup  {a}_{j}}\right)  \rightarrow  \{ 0,1\} }\right)$的合取式。

Let ${\mathbf{D}}_{s}$ be a database instance over star schema and a star-join query $Q$ aggregates over the join result $J\left( {\mathrm{D}}_{s}\right)$ . More abstractly,let $\Phi  : \operatorname{dom}\left( {\operatorname{var}\left( J\right) }\right)  \rightarrow  \{ 0,1\}$ be an indicator function and the join result satisfy the filter predicate,and $\mathbf{w}\left( t\right)$ assigns a non-negative integer weight to the join results only depending on the tuple $t$ . Given the above,we denote the query result of $Q$ on ${\mathbf{D}}_{s}$ as $Q\left( {\mathbf{D}}_{s}\right)$ , which can be formally represented as follows.

设${\mathbf{D}}_{s}$为星型模式上的数据库实例，星型连接查询$Q$对连接结果$J\left( {\mathrm{D}}_{s}\right)$执行聚合运算。抽象而言，令$\Phi  : \operatorname{dom}\left( {\operatorname{var}\left( J\right) }\right)  \rightarrow  \{ 0,1\}$为指示函数，连接结果满足过滤谓词时，$\mathbf{w}\left( t\right)$仅根据元组$t$为连接结果分配非负整数权重。基于上述定义，我们将$Q$在${\mathbf{D}}_{s}$上的查询结果记为$Q\left( {\mathbf{D}}_{s}\right)$，其形式化表示如下。

$$
Q\left( {\mathrm{D}}_{s}\right)  = \mathop{\sum }\limits_{{t \in  J\left( {\mathrm{\;D}}_{s}\right) }}\Phi \left( t\right)  \cdot  \mathbf{w}\left( t\right)  \tag{2}
$$

Note that the function $\Phi$ only depends on the star-join query and $t$ is the tuple in join result $J\left( {\mathbf{D}}_{s}\right)$ . In addition,a star-join query with arbitrary predicate over $\operatorname{var}\left( J\right)$ can be easily incorporated into this formulation (boolean function): If some $t \in  J\left( {\mathrm{D}}_{s}\right)$ does not satisfy the predicate,we simply set $\Phi \left( t\right)  = 0$ . For a counting query, $\operatorname{Aggr}\left( *\right)$ will appear in the form of a COUNT function, $\mathbf{w}\left( \cdot \right)  = 1$ ; for other aggregation query,e.g.,SUM $\left( {a}_{0}\right)$ ,Aggr $\left( *\right)$ refers to a SUM function, $\mathbf{w}\left( t\right)$ is the value of attribute ${a}_{0}$ for $t$ .

注意函数$\Phi$仅取决于星型连接查询，$t$是连接结果$J\left( {\mathbf{D}}_{s}\right)$中的元组。此外，包含$\operatorname{var}\left( J\right)$上任意谓词的星型连接查询均可纳入此布尔函数表述：若$t \in  J\left( {\mathrm{D}}_{s}\right)$不满足谓词，则直接设$\Phi \left( t\right)  = 0$。对于计数查询，$\operatorname{Aggr}\left( *\right)$将体现为COUNT函数形式$\mathbf{w}\left( \cdot \right)  = 1$；其他聚合查询如SUM求和$\left( {a}_{0}\right)$时，Aggr$\left( *\right)$指代SUM函数，$\mathbf{w}\left( t\right)$表示$t$对应属性${a}_{0}$的取值。

Consider the star-join query towards a database instance in Example 1.2. The query consists of a set of single-table predicates as follows: in the Date table,define predicate ${\phi }_{\text{Date }} = \left\{  {\mathbb{I}\left\lbrack  {{t}_{\text{month }} < 7}\right\rbrack  }\right\}$ , ${\phi }_{\text{Supp }} = \left\{  {\mathbb{I}\left\lbrack  {{t}_{\text{region }} = \operatorname{REGION}}\right\rbrack  }\right\}$ and ${\phi }_{\text{Cust }} = \left\{  {\mathbb{I}\left\lbrack  {{t}_{\text{region }} = \operatorname{REGION}}\right\rbrack  }\right\}$ in Supplier and Customer tables. The composite predicate for the query can be expressed as the product $\Phi  : {\phi }_{\text{Date }} \land  {\phi }_{\text{Cust }} \land  {\phi }_{\text{Supp }}$ . Differential Privacy in Join Query. Differential Privacy (DP) provides a rigorous privacy guarantee, which has become the de facto privacy-preserving notion in many applications. Before presenting the formal definition of DP, we shall introduce the notion of the neighboring database first. For two database instances $\mathbf{D}$ and ${\mathbf{D}}^{\prime }$ ,the distance between $\mathbf{D}$ and ${\mathbf{D}}^{\prime }$ ,denoted $d\left( {\mathbf{D},{\mathbf{D}}^{\prime }}\right)$ ,is the minimum number of steps on which they differ. If $d\left( {\mathbf{D},{\mathbf{D}}^{\prime }}\right)  = 1$ ,we call $\mathbf{D},{\mathbf{D}}^{\prime }$ neighboring database instances.

考虑针对示例1.2中数据库实例的星型连接查询。该查询由以下单表谓词组成：在Date表中定义谓词${\phi }_{\text{Date }} = \left\{  {\mathbb{I}\left\lbrack  {{t}_{\text{month }} < 7}\right\rbrack  }\right\}$，在Supplier和Customer表中分别定义谓词${\phi }_{\text{Supp }} = \left\{  {\mathbb{I}\left\lbrack  {{t}_{\text{region }} = \operatorname{REGION}}\right\rbrack  }\right\}$与${\phi }_{\text{Cust }} = \left\{  {\mathbb{I}\left\lbrack  {{t}_{\text{region }} = \operatorname{REGION}}\right\rbrack  }\right\}$。该查询的复合谓词可表示为乘积$\Phi  : {\phi }_{\text{Date }} \land  {\phi }_{\text{Cust }} \land  {\phi }_{\text{Supp }}$。连接查询中的差分隐私。差分隐私（Differential Privacy，DP）提供了严格的隐私保障，已成为众多应用中事实上的隐私保护标准。在正式定义DP之前，需先引入相邻数据库的概念。对于两个数据库实例$\mathbf{D}$和${\mathbf{D}}^{\prime }$，其间距记作$d\left( {\mathbf{D},{\mathbf{D}}^{\prime }}\right)$，表示两者差异的最小步数。若$d\left( {\mathbf{D},{\mathbf{D}}^{\prime }}\right)  = 1$，则称$\mathbf{D},{\mathbf{D}}^{\prime }$为相邻数据库实例。

Definition 3.1 (Differential Privacy). A randomized algorithm $\mathcal{A}$ satisfies $\left( {\epsilon ,\delta }\right)$ - differential privacy,where $\epsilon ,\delta  > 0$ ,if for any pair of neighboring databases $\mathbf{D},{\mathbf{D}}^{\prime }$ and any output range $\mathcal{S} \subseteq$ Range(A),

定义3.1（差分隐私）。随机算法$\mathcal{A}$满足$\left( {\epsilon ,\delta }\right)$-差分隐私（其中$\epsilon ,\delta  > 0$），当且仅当对于任意相邻数据库对$\mathbf{D},{\mathbf{D}}^{\prime }$及任意输出范围$\mathcal{S} \subseteq$⊆Range(A)，

$$
\Pr \left\lbrack  {\mathcal{A}\left( \mathrm{D}\right)  \in  \mathcal{S}}\right\rbrack   \leq  {e}^{\epsilon } \cdot  \Pr \left\lbrack  {\mathcal{A}\left( {\mathrm{D}}^{\prime }\right)  \in  \mathcal{S}}\right\rbrack   + \delta , \tag{3}
$$

where the probability is taken over the randomness of $\mathcal{A}$ . When $\delta  = 0$ ,it is referred to as pure differential privacy,the algorithm $\mathcal{A}$ satisfies $\epsilon$ - differential privacy.

其中概率取自$\mathcal{A}$的随机性。当$\delta  = 0$时称为纯差分隐私，此时算法$\mathcal{A}$满足$\epsilon$-差分隐私。

In the above definition, $\epsilon$ refers to the privacy budget,which directly restricts the degree of the privacy protection of the algorithm $\mathcal{A}$ . Typically,a smaller value of $\epsilon$ corresponds to a stronger privacy guarantee. In addition, $\delta$ should be much smaller than $1/{N}_{\mathrm{D}}$ to ensure the privacy of each individual record,where ${N}_{\mathrm{D}}$ refers to the size of the database instance.

上述定义中，$\epsilon$表示隐私预算，直接制约算法$\mathcal{A}$的隐私保护强度。通常$\epsilon$值越小，隐私保障越强。此外，$\delta$应远小于$1/{N}_{\mathrm{D}}$以确保每条个体记录的隐私性，其中${N}_{\mathrm{D}}$表示数据库实例的规模。

Differential privacy is usually achieved by adding random noise drawn from a certain zero-mean probability distribution to the query result. Notably, the magnitude of the random perturbation positively correlates with the difference between the query results on $\mathrm{D}$ and ${\mathrm{D}}^{\prime }$ ,which refers to the notion of sensitivity. The most basic framework for achieving differential privacy is the Laplace mechanism, and the noise is scaled according to the global sensitivity of the query, defined as follows.

差分隐私通常通过向查询结果添加特定零均值概率分布生成的随机噪声实现。值得注意的是，随机扰动的幅度与$\mathrm{D}$和${\mathrm{D}}^{\prime }$上查询结果的差异（即敏感度概念）呈正相关。实现差分隐私的最基础框架是拉普拉斯机制，其噪声量根据查询的全局敏感度进行缩放，定义如下。

THEOREM 3.2 (LAPLACE MECHANISM). The algorithm $\mathcal{A}\left( \mathrm{D}\right)  = Q\left( \mathrm{D}\right)  + \operatorname{Lap}\left( \frac{G{S}_{Q}}{\epsilon }\right)$ is $\epsilon$ - differential privacy.

定理3.2（拉普拉斯机制）。算法$\mathcal{A}\left( \mathrm{D}\right)  = Q\left( \mathrm{D}\right)  + \operatorname{Lap}\left( \frac{G{S}_{Q}}{\epsilon }\right)$满足$\epsilon$-差分隐私。

Definition 3.3 (Global Sensitivity). Let $Q$ denote a particular query,then the global sensitivity of $Q$ ,denoted $G{S}_{Q}$ ,is

定义3.3（全局敏感度）。设$Q$表示特定查询，则$Q$的全局敏感度记作$G{S}_{Q}$，其值为

$$
G{S}_{Q} = \mathop{\max }\limits_{{\mathbf{D},{\mathbf{D}}^{\prime },d\left( {\mathbf{D},{\mathbf{D}}^{\prime }}\right)  = 1}}\begin{Vmatrix}{Q\left( \mathbf{D}\right)  - Q\left( {\mathbf{D}}^{\prime }\right) }\end{Vmatrix}. \tag{4}
$$

The global sensitivity of the query is defined as the maximal ${L}_{1}$ -norm distance between the exact answers of the query $Q$ on any neighboring databases $\mathrm{D}$ and ${\mathrm{D}}^{\prime }$ . However,unfortunately,the global sensitivity of many queries can be very high. What is worse, for the join operator the global sensitivity can be unbounded. Nissim et al. [26] proposed a local measure of sensitivity:

查询的全局敏感度定义为该查询$Q$在任意相邻数据库$\mathrm{D}$与${\mathrm{D}}^{\prime }$上精确答案间${L}_{1}$范数距离的最大值。然而遗憾的是，许多查询的全局敏感度可能极高。更糟的是，连接运算符的全局敏感度可能无界。Nissim等人[26]提出了一种局部敏感度度量方法：

Definition 3.4 (Local Sensitivity). For a query $Q$ ,the local sensitivity of $Q$ given the database instance $\mathbf{D}$ ,denoted as $L{S}_{Q}\left( \mathbf{D}\right)$ is as follows:

定义3.4（局部敏感度）。对于查询$Q$，给定数据库实例$\mathbf{D}$时的局部敏感度（记为$L{S}_{Q}\left( \mathbf{D}\right)$）定义如下：

$$
L{S}_{Q}\left( \mathbf{D}\right)  = \mathop{\max }\limits_{{{\mathbf{D}}^{\prime },d\left( {\mathbf{D},{\mathbf{D}}^{\prime }}\right)  = 1}}\begin{Vmatrix}{Q\left( \mathbf{D}\right)  - Q\left( {\mathbf{D}}^{\prime }\right) }\end{Vmatrix}. \tag{5}
$$

where the maximum is taken over all neighbors ${\mathrm{D}}^{\prime }$ of the particular instance $\mathrm{D}$ .

其中最大值取自特定实例$\mathrm{D}$的所有相邻实例${\mathrm{D}}^{\prime }$。

Note that, $G{S}_{Q} = \mathop{\max }\limits_{\mathrm{D}}L{S}_{Q}\left( \mathrm{D}\right)$ . The local sensitivity is much smaller than global sensitivity in most real-world scenarios. However, an algorithm that releases query results with noise scale proportional to $L{S}_{Q}\left( \mathrm{D}\right)$ on instance $\mathrm{D}$ may not satisfy differential privacy,since $L{S}_{Q}\left( \mathrm{D}\right)$ and $L{S}_{Q}\left( {\mathbf{D}}^{\prime }\right)$ can differ a lot on two neighboring instances $D$ and ${D}^{\prime }$ . Large differences in the amounts of noise added to $Q\left( \mathbf{D}\right)$ and $Q\left( {\mathbf{D}}^{\prime }\right)$ may leak sensitive information. To address the issue,Nissim et al. [26] proposed the approach that selects noise magnitude according to a smooth upper bound on the local sensitivity instead of using the local sensitivity itself. But differently, compared with the local sensitivity, it is the maximum local sensitivity attained among neighboring instances, the tightest bound is called the smooth sensitivity. The smooth sensitivity is based on the local sensitivity at distance $t$ ,i.e., $L{S}_{Q}^{\left( t\right) }\left( \mathbf{D}\right)$ ,which is defined as

需注意$G{S}_{Q} = \mathop{\max }\limits_{\mathrm{D}}L{S}_{Q}\left( \mathrm{D}\right)$。在多数实际场景中，局部敏感度远小于全局敏感度。然而，若算法发布查询结果时添加与实例$\mathrm{D}$上$L{S}_{Q}\left( \mathrm{D}\right)$成比例的噪声，可能无法满足差分隐私要求——因为在相邻实例$D$与${D}^{\prime }$上，$L{S}_{Q}\left( \mathrm{D}\right)$与$L{S}_{Q}\left( {\mathbf{D}}^{\prime }\right)$可能存在显著差异。对$Q\left( \mathbf{D}\right)$和$Q\left( {\mathbf{D}}^{\prime }\right)$添加噪声量的巨大差异可能导致敏感信息泄露。为解决此问题，Nissim等人[26]提出根据局部敏感度的平滑上界而非其本身来选择噪声量级。不同的是，平滑敏感度是相邻实例间局部敏感度的最大值，最紧上界称为平滑敏感度。该敏感度基于距离$t$处的局部敏感度（即$L{S}_{Q}^{\left( t\right) }\left( \mathbf{D}\right)$），其定义为

$$
L{S}_{Q}^{\left( t\right) }\left( \mathbf{D}\right)  = \mathop{\max }\limits_{{{\mathbf{D}}^{\prime },d\left( {\mathbf{D},{\mathbf{D}}^{\prime }}\right)  \leq  t}}L{S}_{Q}\left( {\mathbf{D}}^{\prime }\right) . \tag{6}
$$

Definition 3.5 (Smooth Sensitivity). The $\beta$ - smooth sensitivity of $Q$ ,denoted $S{S}_{Q}\left( \mathbf{D}\right)$ ,is

定义3.5（平滑敏感度）。$Q$的$\beta$-平滑敏感度（记为$S{S}_{Q}\left( \mathbf{D}\right)$）为：

$$
S{S}_{Q}\left( \mathbf{D}\right)  = \mathop{\max }\limits_{{t \geq  0}}{e}^{-{\beta t}}L{S}_{Q}^{\left( t\right) }\left( \mathbf{D}\right) . \tag{7}
$$

$S{S}_{Q}\left( \mathrm{D}\right)$ and $S{S}_{Q}\left( {\mathrm{D}}^{\prime }\right)$ differ by at most a constant factor on any two neighboring instances $\mathrm{D}$ and ${\mathbf{D}}^{\prime }$ to ensure the "smoothness" of $S{S}_{Q}\left( \cdot \right)$ ,and the level of smoothness is parameterized by a value $\beta$ (a smaller value leads to a smooth bound) that depends on $\epsilon$ .

为确保$S{S}_{Q}\left( \cdot \right)$的"平滑性"，$S{S}_{Q}\left( \mathrm{D}\right)$与$S{S}_{Q}\left( {\mathrm{D}}^{\prime }\right)$在任意相邻实例$\mathrm{D}$和${\mathbf{D}}^{\prime }$上最多相差一个常数因子，平滑程度由参数$\beta$（较小值产生更平滑边界）控制，该参数取决于$\epsilon$。

### 3.2 Problem Definition

### 3.2 问题定义

DP in Star-join Query. Star-join queries are the most prevalent kind of queries in data warehousing, OLAP and business intelligence applications. Hence, answering star-join query under differential privacy can definitely benefit wide applications in privacy-preserving tasks in the OLAP scenarios. Therefore, in this work, we aim to propose the first DP-compliant star-join solution. However, before presenting the solution, due to the special characteristics of the query, it is necessary to reconsider the definition of differential privacy of the star-join query. In this subsection, we introduce differential privacy in the star-join query, including neighboring database instances in different situations (fact table and dimension table), and differential privacy in single private relation and multi-private relations with star-join query afterwards.

星型连接查询中的差分隐私。星型连接查询是数据仓库、OLAP和商业智能应用中最普遍的查询类型。因此，在差分隐私下实现星型连接查询必将推动OLAP场景中隐私保护任务的广泛应用。本研究旨在提出首个符合差分隐私的星型连接解决方案。但在展示方案前，由于该查询的特殊性，需重新审视星型连接查询的差分隐私定义。本节将介绍星型连接查询中的差分隐私，包括不同情境下（事实表与维度表）的相邻数据库实例，以及后续涉及单私有关系和星型连接多私有关系的差分隐私。

Consider a database instance ${\mathbf{D}}_{s}$ over star schema $\mathbf{R} : \left\{  {{R}_{0},{R}_{1},\ldots ,{R}_{n}}\right\}$ ,where ${R}_{0}$ is a fact table and the rest are $n$ dimension tables. Given a star-join query $Q$ shown in Definition 1.1,let $N = \left| {\mathbf{D}}_{s}\right|$ be the input size,and denote the result of $Q$ on ${\mathrm{D}}_{s}$ as $Q\left( {\mathrm{D}}_{s}\right)$ . We consider a DP-compliant star-join based on neighboring instances ${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$ .

考虑一个星型模式$\mathbf{R} : \left\{  {{R}_{0},{R}_{1},\ldots ,{R}_{n}}\right\}$下的数据库实例${\mathbf{D}}_{s}$，其中${R}_{0}$为事实表，其余为$n$个维度表。给定定义1.1所示的星型连接查询$Q$，设$N = \left| {\mathbf{D}}_{s}\right|$为输入规模，并将${\mathrm{D}}_{s}$上执行$Q$的结果记为$Q\left( {\mathrm{D}}_{s}\right)$。我们研究基于相邻实例${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$、符合差分隐私的星型连接。

Definition 3.6 (Differential Privacy in Star-Join Query). A randomized mechanism $\mathcal{A}$ satisfies $\epsilon$ - differential privacy if for neighboring instances ${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$ over star-join,where $\epsilon  > 0$ ,and any output range $\mathcal{S} \subseteq  \operatorname{Range}\left( \mathcal{A}\right)$ ,

定义3.6（星型连接查询的差分隐私）。随机机制$\mathcal{A}$满足$\epsilon$-差分隐私的条件是：对于星型连接上的相邻实例${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$（其中$\epsilon  > 0$），以及任意输出范围$\mathcal{S} \subseteq  \operatorname{Range}\left( \mathcal{A}\right)$，

$$
\Pr \left\lbrack  {\mathcal{A}\left( {\mathrm{D}}_{s}\right)  \in  \mathcal{S}}\right\rbrack   \leq  {e}^{\epsilon } \cdot  \Pr \left\lbrack  {\mathcal{A}\left( {\mathrm{D}}_{s}^{\prime }\right)  \in  \mathcal{S}}\right\rbrack  , \tag{8}
$$

where the probability is taken over the randomness of $\mathcal{A}$ .

其中概率取自$\mathcal{A}$的随机性。

In the above definition,neighboring instances ${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$ over star schema should differ by one tuple according to the notion of the neighboring database. However, in the star schema, each dimension table is independent of each other and has a foreign key constraint referenced by the fact table. The tuples in the fact table and dimension tables exert different effects on the query result due to the asymmetric characteristics for both types of tables within the star-join procedure. Therefore, it is necessary to revisit the definition for neighboring instances ${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$ . At the same time,database instances may contain a single private relation or multi-private relations in practical applications. Based on the above reasons, in this subsection, we consider the following situations of neighboring database instances ${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$ .

上述定义中，根据相邻数据库的概念，星型模式下的相邻实例${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$应相差一个元组。然而在星型模式中，各维度表相互独立且通过外键约束与事实表关联。由于星型连接过程中两类表具有非对称特性，事实表与维度表中的元组对查询结果会产生不同影响。因此需要重新审视相邻实例${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$的定义。同时实际应用中，数据库实例可能包含单个私有关系或多个私有关系。基于上述原因，本节将考虑以下相邻数据库实例${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$的情形。

Scenario-dependent Neighboring Database Instance. As we have discussed above, the unique characteristics of star-join rely on the fact that there exist a large number of foreign key constraints between the fact and dimension tables. As a result, the difference in a single tuple within a dimension table may result in a group of different tuples in the fact one. Hence, the asymmetry between both types of tables leads to different scenarios for neighboring instances. In the following, we shall discuss them accordingly.

场景相关的相邻数据库实例。如前所述，星型连接的独特之处在于事实表与维度表间存在大量外键约束。这导致维度表中单个元组的差异可能引发事实表中一组元组的变化。因此两类表的非对称性形成了不同的相邻实例场景，下文将分别讨论。

Definition 3.7 $(\left( {a,b}\right)$ -private). Given the star-join task shown in Definition 1.1,which contains at least one sensitive table,we refer to the scenario as(a,b)-private if a number of $a\left( {a \in  \{ 0,1\} }\right)$ fact tables and $b\left( {b \leq  n,a + b \geq  1}\right)$ dimension ones are sensitive.

定义3.7 $(\left( {a,b}\right)$-私有）。对于定义1.1所示、包含至少一个敏感表的星型连接任务，若存在$a\left( {a \in  \{ 0,1\} }\right)$个事实表和$b\left( {b \leq  n,a + b \geq  1}\right)$个维度表为敏感表，则称该场景为(a,b)-私有。

(1)(0,k)-private. The private relations are all dimension tables, ${R}_{p}^{1},\ldots ,{R}_{p}^{k} \in  \left\{  {R}_{i}\right\}  (i \in  \left\lbrack  n\right\rbrack  ,k \leq$ $n)$ . When the database instance ${\mathrm{D}}_{s}$ exists the foreign key constraint that table has foreign key referencing the primary key (PK) of the other table,the two instances ${\mathbf{D}}_{s}$ and ${\mathbf{D}}_{s}^{\prime }$ are considered as neighbors if ${\mathbf{D}}_{s}^{\prime }$ can be obtained from ${\mathbf{D}}_{s}$ by: deleting a tuple $t$ from the referenced table,and a set of tuples that reference $t$ in the referencing table. As each dimension table has a foreign key constraint with the fact table, we adopt the DP policy in star-join query, which defines neighboring instances by taking foreign key constraints into consideration. The basic private relation of(0,k)-private is to only include one dimension table,that is,when $k = 1$ . Therefore,we refer to ${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$ as neighboring instances over star schema if all tuples in the difference between ${\mathbf{D}}_{s}$ and ${\mathbf{D}}_{s}^{\prime }$ reference a single tuple ${t}_{p}$ in the private dimension table ${R}_{p}^{k}$ . In particular, ${t}_{p}\left( {{t}_{p} \in  {R}_{p}^{k}}\right)$ may also be deleted,in which case all tuples referencing ${t}_{p}$ in the fact table must be deleted in order to preserve the foreign key constraints. When $k > 1$ ,since each dimension table is independent of each other and the fact table has foreign keys referencing the primary key of each dimension one, thus we assign unique identifiers to the conjunction of all foreign keys in the fact table. If ${\mathbf{D}}_{s}^{\prime }$ can be obtained from ${\mathbf{D}}_{s}$ by deleting a tuple ${t}_{p}^{i} \in  {R}_{p}^{i}$ for each private relations,as well as all the tuples in the fact table referencing the same tuple $t \in  {t}_{p}^{1}\left( {PK}\right)  \land  \ldots  \land  {t}_{p}^{k}\left( {PK}\right)$ ,we call ${\mathrm{D}}_{s},{\mathrm{D}}_{s}^{\prime }$ neighboring instances in this case.

(1)(0,k)-隐私。私有关系均为维度表，${R}_{p}^{1},\ldots ,{R}_{p}^{k} \in  \left\{  {R}_{i}\right\}  (i \in  \left\lbrack  n\right\rbrack  ,k \leq$ $n)$。当数据库实例${\mathrm{D}}_{s}$存在外键约束（即某表的外键引用另一表的主键时），若实例${\mathbf{D}}_{s}$可通过以下方式转换为${\mathbf{D}}_{s}^{\prime }$：从被引用表中删除元组$t$，并删除引用表中所有引用$t$的元组集合，则视二者为相邻实例。由于每个维度表与事实表间存在外键约束，我们在星型连接查询中采用差分隐私策略，该策略通过考量外键约束来定义相邻实例。(0,k)-隐私的基本私有关系仅包含单个维度表，即当$k = 1$时。因此，若${\mathbf{D}}_{s}$与${\mathbf{D}}_{s}^{\prime }$的差异元组均引用私有维度表${R}_{p}^{k}$中的同一元组${t}_{p}$，则称${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$为星型模式下的相邻实例。特别地，${t}_{p}\left( {{t}_{p} \in  {R}_{p}^{k}}\right)$也可能被删除，此时需同步删除事实表中所有引用${t}_{p}$的元组以维持外键约束。当$k > 1$时，由于各维度表相互独立且事实表包含指向各维度表主键的外键，我们为事实表中所有外键的组合分配唯一标识符。若${\mathbf{D}}_{s}^{\prime }$可通过从${\mathbf{D}}_{s}$中删除每个私有关系的一个元组${t}_{p}^{i} \in  {R}_{p}^{i}$及事实表中所有引用同一元组$t \in  {t}_{p}^{1}\left( {PK}\right)  \land  \ldots  \land  {t}_{p}^{k}\left( {PK}\right)$的元组获得，则称${\mathrm{D}}_{s},{\mathrm{D}}_{s}^{\prime }$为该情形下的相邻实例。

(2)(1,k)-private. The private relations contain the fact table. The simplest scenario of(1,k)- private is $k = 0$ ,which means that only the fact table is private. When $k = 0$ ,two instances can only differ at one tuple in the fact table, ${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$ are referred to as neighboring instances, $d\left( {{\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }}\right)  = 1$ . Another scenario of(1,k)-private is the case when $k \neq  0$ ,i.e.,some of the dimension tables are private. In this case,two neighboring instances ${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$ ,can differ at one tuple in the fact table. Moreover,similar to(0,k)-private, ${\mathbf{D}}_{s}^{\prime }$ also needs to be obtained from ${\mathbf{D}}_{s}$ by deleting a tuple ${t}_{p}^{i} \in  {R}_{p}^{i}$ from each private dimension tables, as well as all the tuples in the fact table referencing the same tuple $t \in  {t}_{p}^{1}\left( {PK}\right)  \land  \ldots  \land  {t}_{p}^{k}\left( {PK}\right)$ .

(2)(1,k)-隐私。私有关系包含事实表。(1,k)-隐私的最简场景是$k = 0$，即仅事实表为私有。当$k = 0$时，两实例仅能在事实表的单个元组上存在差异，${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$被称为相邻实例，$d\left( {{\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }}\right)  = 1$。(1,k)-隐私的另一场景是$k \neq  0$，即部分维度表为私有。此时，相邻实例${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$除可在事实表的一个元组上存在差异外，类似(0,k)-隐私，${\mathbf{D}}_{s}^{\prime }$还需通过从${\mathbf{D}}_{s}$中删除各私有维度表的一个元组${t}_{p}^{i} \in  {R}_{p}^{i}$及事实表中所有引用同一元组$t \in  {t}_{p}^{1}\left( {PK}\right)  \land  \ldots  \land  {t}_{p}^{k}\left( {PK}\right)$的元组获得。

The above outlines the different cases for neighboring instances ${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$ in the star-join query. In Definition 1.1, star-join queries are transformed to predicate queries in the multidimensional data cube. Therefore, the algorithm that satisfies differential privacy is implemented for each predicate constraint of the star-join query $Q$ ,so that the query $Q$ conforms to differential privacy.

上述内容概述了星型连接查询中相邻实例${\mathbf{D}}_{s},{\mathbf{D}}_{s}^{\prime }$的不同情况。在定义1.1中，星型连接查询被转换为多维数据立方体中的谓词查询。因此，针对星型连接查询$Q$的每个谓词约束实现了满足差分隐私的算法，使得查询$Q$符合差分隐私要求。

## 4 BASIC MECHANISM FOR STAR-JOIN QUERY: OUTPUT PERTURBATION

## 4 星型连接查询基础机制：输出扰动

In order to systematically find the ideal solution for answering star-join query under DP, we investigate ways through both the output and input perturbations. In this section, we propose the basic approach for DP-compliant star-join query by a pair of output-based perturbation mechanisms. Aside from that, we also conduct a theoretical utility study, which shows that the basic mechanism achieves a satisfactory (although not elegant) trade-off between utility, efficiency, and scalability.

为系统性地寻找差分隐私下星型连接查询的理想解决方案，我们通过输出扰动和输入扰动两种途径进行研究。本节提出基于输出扰动的双机制基础方案，使星型连接查询符合差分隐私要求。此外，我们还进行了理论效用分析，表明该基础机制在效用性、效率和可扩展性之间取得了令人满意（虽非最优）的平衡。

Intuitively,following the standard DP solutions,we can propose a basic strategy by approximating real-valued functions based on adding a small amount of random noise to the true answer. In particular, we introduce both a data-independent approach and a data-dependent one to the star-join query result according to whether the global sensitivity of star-join query is bounded. In a data-independent approach, if the global sensitivity of star-join query is bounded, the server is in charge of adding random noise to the query result. The most popular method is to rely on the Laplace Mechanism (LM) that scales according to the global sensitivity $G{S}_{Q}$ of the star-join query $Q$ . The variance of the Laplace Mechanism is $2{\left( \frac{G{S}_{Q}}{\epsilon }\right) }^{2}$ .

直观而言，遵循标准差分隐私解决方案，我们可通过在真实答案上添加微量随机噪声来逼近实值函数，从而提出基础策略。具体针对星型连接查询结果，根据其全局敏感性是否受限，我们分别引入数据无关方法和数据相关方法。对于全局敏感性受限的情况，服务器负责向查询结果添加随机噪声，最常用的是依据星型连接查询$Q$的全局敏感性$G{S}_{Q}$进行缩放的拉普拉斯机制(LM)，其方差为$2{\left( \frac{G{S}_{Q}}{\epsilon }\right) }^{2}$。

In star-join query,this method is only applicable with the(1,0)-private scenario,where the fact table is the only one that is sensitive. Besides that, the Laplace mechanism will fail to work in the $\left( {\cdot ,k}\right)$ -private relation contains dimension table due to the unbounded global sensitivity. Notably,in practical scenarios, sensitive information is mostly contained in the dimension tables rather than the fact one (e.g., Customer is a private relation that needs to be protected).

在星型连接查询中，该方法仅适用于(1,0)-隐私场景（事实表为唯一敏感表）。当$\left( {\cdot ,k}\right)$-隐私关系包含维度表时，由于全局敏感性无界，拉普拉斯机制将失效。值得注意的是，实际场景中敏感信息多存在于维度表而非事实表（如需要保护的客户隐私关系）。

For those cases when private relation includes dimension table, that is, the global sensitivity is unbounded, we first consider adopting a data-independent approach by utilizing the Truncation Mechanism (TM) that bounds the global sensitivity by simply deleting all records, the sensitivity of which is larger than a predefined threshold $\tau$ ,before adding random noise to the true answer. However, a well-known limitation of the truncation mechanism is the bias-variance trade-off: a large threshold $\tau$ will lead to large random noise with tremendous variance; while a small $\tau$ may introduce a bias as large as the query result itself. When the private relation contains dimension table, due to the aforementioned limitation of LM and TM in the data-independent approach, we select to adopt a data-dependent approach by injecting data-dependent noise into the query result.

当隐私关系包含维度表（即全局敏感性无界）时，我们首先考虑采用数据无关的截断机制(TM)：通过删除所有超过预设阈值$\tau$的记录来限定全局敏感性，再添加随机噪声。但该机制存在偏差-方差权衡的固有缺陷：阈值$\tau$过大会导致噪声方差剧增，过小则可能引入与查询结果相当的偏差。鉴于LM和TM在数据无关方法中的局限性，我们转而采用数据相关方法，向查询结果注入数据依赖性噪声。

The data-dependent approach involves applying Local Sensitivity (LS) and Race-to-the-Top (R2T) to the star-join query. The LS is usually a two-phase strategy as follows.

数据相关方法涉及将局部敏感性(LS)和竞顶机制(R2T)应用于星型连接查询。局部敏感性通常采用如下两阶段策略：

(1) compute the upper bound of local sensitivity ${\widehat{LS}}_{Q}\left( {\mathbf{D}}_{s}\right)$ in star-join query $Q$ with database instance ${\mathrm{D}}_{s}$ ;

(1) 计算数据库实例${\mathrm{D}}_{s}$下星型连接查询$Q$的局部敏感性${\widehat{LS}}_{Q}\left( {\mathbf{D}}_{s}\right)$上界；

(2) add the noise that calibrates the size of ${\widehat{LS}}_{Q}\left( {\mathbf{D}}_{s}\right)$ to the query result.

(2) 向查询结果添加与${\widehat{LS}}_{Q}\left( {\mathbf{D}}_{s}\right)$规模校准的噪声。

In general, there are two mechanisms for implementing LS, namely Cauchy Mechanism and Laplace Mechanism. Cauchy Mechanism works by setting $\beta  = \frac{\epsilon }{2\left( {\gamma  + 1}\right) }$ ,and then adds noise Cauchy $\left( \frac{{\widehat{\mathrm{{LS}}}}_{Q}\left( {\mathbf{D}}_{s}\right) }{\beta }\right)$ to the query answer $Q\left( {\mathbf{D}}_{s}\right)$ . It preserves $\epsilon$ - differential privacy,where $\operatorname{Cauchy}\left( \cdot \right)$ is drawn from the general Cauchy distribution. For instance,suppose we set $\gamma  = 4$ for which $\operatorname{Var}\left( {\operatorname{Cauchy}\left( \cdot \right) }\right)  = 1$ , and the noise level of Cauchy Mechanism is thus ${\left( \frac{{10}{L}_{Q}\left( {\mathrm{D}}_{s}\right) }{\epsilon }\right) }^{2}$ . Notably,as there is a long tail in the general Cauchy distribution, which decays only polynomially compared with the exponential decay within the Laplace distribution, one can use the Laplace distribution to achieve a better concentration. However,the Laplace Mechanism only yields $\left( {\epsilon ,\delta }\right)$ - differential privacy. The Laplace Mechanism works by setting $\beta  = \frac{\epsilon }{{2ln}\left( \frac{2}{\delta }\right) }$ ,and adds noise $\operatorname{Lap}\left( \frac{2{\widehat{\mathrm{{LS}}}}_{Q}\left( {\mathbf{D}}_{s}\right) }{\epsilon }\right)$ to the true answer $Q\left( {\mathbf{D}}_{s}\right)$ . Since $\operatorname{Var}\left( {\operatorname{Lap}\left( \cdot \right) }\right)  = 2$ ,the noise level of Laplace Mechanism is $8{\left( \frac{{\widehat{LS}}_{Q}\left( {\mathrm{D}}_{s}\right) }{\epsilon }\right) }^{2}$ .

通常，实现LS有两种机制，即柯西机制(Cauchy Mechanism)和拉普拉斯机制(Laplace Mechanism)。柯西机制通过设置$\beta  = \frac{\epsilon }{2\left( {\gamma  + 1}\right) }$，然后向查询结果$Q\left( {\mathbf{D}}_{s}\right)$添加柯西噪声$\left( \frac{{\widehat{\mathrm{{LS}}}}_{Q}\left( {\mathbf{D}}_{s}\right) }{\beta }\right)$。该机制满足$\epsilon$-差分隐私，其中$\operatorname{Cauchy}\left( \cdot \right)$采样自广义柯西分布。例如，假设我们设置$\gamma  = 4$使得$\operatorname{Var}\left( {\operatorname{Cauchy}\left( \cdot \right) }\right)  = 1$，此时柯西机制的噪声水平为${\left( \frac{{10}{L}_{Q}\left( {\mathrm{D}}_{s}\right) }{\epsilon }\right) }^{2}$。值得注意的是，由于广义柯西分布存在长尾特性（其衰减速度仅为多项式级别，而拉普拉斯分布呈指数衰减），因此采用拉普拉斯分布可获得更好的集中性。但拉普拉斯机制仅满足$\left( {\epsilon ,\delta }\right)$-差分隐私。该机制通过设置$\beta  = \frac{\epsilon }{{2ln}\left( \frac{2}{\delta }\right) }$，向真实答案$Q\left( {\mathbf{D}}_{s}\right)$添加噪声$\operatorname{Lap}\left( \frac{2{\widehat{\mathrm{{LS}}}}_{Q}\left( {\mathbf{D}}_{s}\right) }{\epsilon }\right)$。由于$\operatorname{Var}\left( {\operatorname{Lap}\left( \cdot \right) }\right)  = 2$，拉普拉斯机制的噪声水平为$8{\left( \frac{{\widehat{LS}}_{Q}\left( {\mathrm{D}}_{s}\right) }{\epsilon }\right) }^{2}$。

Another method in the data-dependent approach is Race-to-the-Top (R2T). It is a truncation mechanism with foreign key constraints in join query, and can be used in combination with any truncation method. The basic idea of R2T is to try geometrically increasing values of truncation threshold $\tau$ and somehow pick the "winner" from all the trials. The R2T first computes the query result $Q\left( {{\mathbf{D}}_{s},\tau }\right)$ with various threshold $\tau$ ,and then adds $\operatorname{Lap}\left( \frac{\tau }{\epsilon }\right)$ to $Q\left( {{\mathbf{D}}_{s},\tau }\right)$ to get the noise result $\widehat{Q}\left( {{\mathbf{D}}_{s},\tau }\right)$ ,which would turn it into an $\epsilon$ - differential privacy mechanism. Finally,returning the maximum $\widehat{Q}\left( {{\mathbf{D}}_{s},\tau }\right)$ preserves DP by the post-processing property of differential privacy. The R2T works as follows:

数据依赖型方法中的另一种机制是竞优机制(Race-to-the-Top, R2T)。这是一种针对连接查询中带外键约束的截断机制，可与任何截断方法结合使用。R2T的核心思想是尝试几何级数增长的截断阈值$\tau$，并通过特定方式从所有试验中选出"优胜者"。该机制首先计算不同阈值$\tau$下的查询结果$Q\left( {{\mathbf{D}}_{s},\tau }\right)$，然后对$Q\left( {{\mathbf{D}}_{s},\tau }\right)$添加$\operatorname{Lap}\left( \frac{\tau }{\epsilon }\right)$得到噪声结果$\widehat{Q}\left( {{\mathbf{D}}_{s},\tau }\right)$，从而将其转化为满足$\epsilon$-差分隐私的机制。最终，通过差分隐私的后处理特性，返回最大值的$\widehat{Q}\left( {{\mathbf{D}}_{s},\tau }\right)$仍能保持差分隐私。R2T的具体流程如下：

For ${\tau }^{\left( j\right) },j = 1,\ldots ,\log \left( {G{S}_{Q}}\right)$ ,

对于${\tau }^{\left( j\right) },j = 1,\ldots ,\log \left( {G{S}_{Q}}\right)$，

$$
\widehat{Q}\left( {{\mathbf{D}}_{s},{\tau }^{\left( j\right) }}\right)  = Q\left( {{\mathbf{D}}_{s},{\tau }^{\left( j\right) }}\right)  + \operatorname{Lap}\left( {\log \left( {GS}_{Q}\right) \frac{{\tau }^{\left( j\right) }}{\epsilon }}\right)  - \log \left( {GS}_{Q}\right) \ln \left( \frac{\log \left( {GS}_{Q}\right) }{\alpha }\right)  \cdot  \frac{{\tau }^{\left( j\right) }}{\epsilon }, \tag{9}
$$

and then outputs $\max \left\{  {\mathop{\max }\limits_{j}\widehat{Q}\left( {{\mathbf{D}}_{s},{\tau }^{\left( j\right) }}\right) ,Q\left( {{\mathbf{D}}_{s},0}\right) }\right\}$ ,where $\alpha$ is the probability concern about the utility. The R2T mechanism satisfies $\epsilon$ - differential privacy by the basic composition theorem [12]. Note that, $Q\left( {{\mathrm{D}}_{s},\tau }\right)$ is different in queries with and without self-join,it may rely on Linear Program(LP)- based truncation mechanism when there exists self-join in the query. For the utility of R2T, we have $Q\left( {\mathbf{D}}_{s}\right)  - {4log}\left( {G{S}_{Q}}\right) \ln \left( \frac{\log \left( {G{S}_{Q}}\right) }{\alpha }\right) \frac{{\tau }^{ * }\left( {\mathbf{D}}_{s}\right) }{\epsilon } \leq  \widehat{Q}\left( {\mathbf{D}}_{s}\right)$ with probability at least $1 - \alpha$ . Hereby ${\tau }^{ * }\left( {\mathbf{D}}_{s}\right)$ means a bound of threshold that holds for any $\tau  \geq  {\tau }^{ * }\left( {\mathbf{D}}_{s}\right) ,Q\left( {{\mathbf{D}}_{s},\tau }\right)  = Q\left( {\mathbf{D}}_{s}\right)$ .

随后输出$\max \left\{  {\mathop{\max }\limits_{j}\widehat{Q}\left( {{\mathbf{D}}_{s},{\tau }^{\left( j\right) }}\right) ,Q\left( {{\mathbf{D}}_{s},0}\right) }\right\}$，其中$\alpha$表示关于效用的概率考量。R2T机制通过基本组合定理[12]满足$\epsilon$-差分隐私。需注意，$Q\left( {{\mathrm{D}}_{s},\tau }\right)$在含自连接与不含自连接的查询中存在差异，当查询存在自连接时可能依赖基于线性规划(LP)的截断机制。对于R2T的效用，我们以至少$1 - \alpha$的概率得到$Q\left( {\mathbf{D}}_{s}\right)  - {4log}\left( {G{S}_{Q}}\right) \ln \left( \frac{\log \left( {G{S}_{Q}}\right) }{\alpha }\right) \frac{{\tau }^{ * }\left( {\mathbf{D}}_{s}\right) }{\epsilon } \leq  \widehat{Q}\left( {\mathbf{D}}_{s}\right)$。此处${\tau }^{ * }\left( {\mathbf{D}}_{s}\right)$表示适用于任意$\tau  \geq  {\tau }^{ * }\left( {\mathbf{D}}_{s}\right) ,Q\left( {{\mathbf{D}}_{s},\tau }\right)  = Q\left( {\mathbf{D}}_{s}\right)$的阈值边界。

Remark 1. In the star-join query, the sensitivity of the query plays an important role in the output mechanism. From the aspect of the output perturbation, the utility is directly affected by the noise that is scaled according to the sensitivity of the star-join query. Both the global and the local sensitivity are extremely high, due to the existence of join operations in star-join query. Especially for an $n$ -way star join,the global sensitivity can be as high as $O\left( {N}^{n - 1}\right)$ ,which is unbounded as $N = \left| {\mathbf{D}}_{s}\right|$ is the input size. Therefore,this brings down the utility because the $G{S}_{Q}$ of the star-join query can be $\infty$ under pure DP. Although using the instance-depended noise,the output mechanism has the intrinsic limitation on achieving high utility due to the fact that the smooth upper bound of $L{S}_{Q}\left( {\mathbf{D}}_{s}\right)$ is very large in practical applications,and the computational cost of it is extremely high. In short, the high sensitivity of star-join query results extremely limits the utility level that the basic output perturbation mechanism can achieve.

注1. 在星型连接查询中，查询敏感度对输出机制起关键作用。从输出扰动角度看，根据星型连接敏感度调整的噪声直接影响效用。由于星型连接中存在连接操作，其全局敏感度与局部敏感度均极高。特别对于$n$路星型连接，全局敏感度可高达$O\left( {N}^{n - 1}\right)$（该值无界，因$N = \left| {\mathbf{D}}_{s}\right|$表示输入规模）。这导致纯差分隐私下星型连接查询的$G{S}_{Q}$可能低至$\infty$。尽管采用实例依赖噪声，但由于$L{S}_{Q}\left( {\mathbf{D}}_{s}\right)$的平滑上界实际应用中极大且计算成本极高，输出机制在实现高效用方面存在固有局限。简言之，星型连接的高敏感度极大限制了基础输出扰动机制可达到的效用水平。

Remark 2. Although the output mechanism adopts the smooth sensitivity to reduce the noise for better utility, in fact, it is shown that for certain problems, computing or even approximating the smooth sensitivity is NP-hard [36]. Therefore, the computational hardness of the smooth sensitivity of star-join queries increases with the increase of multi-way joins. [8] argues that it may not be NP-hard, and even if there is a polynomial-time algorithm to compute the smooth sensitivity, it will be inevitably too complicated in practice. Thus it is challenging for the output perturbation mechanism to achieve satisfactory scalability and is impractical in realistic scenarios.

注2. 虽然输出机制采用平滑敏感度来降低噪声提升效用，但事实上已证明对于特定问题，计算甚至近似平滑敏感度属于NP难问题[36]。因此星型连接查询的平滑敏感度计算难度随多路连接增加而递增。[8]指出这可能并非NP难问题，但即便存在多项式时间算法计算平滑敏感度，其实际复杂度仍将过高。这使得输出扰动机制难以实现令人满意的可扩展性，在实际场景中缺乏可行性。

## 5 ADVANCED APPROACH: DP-STARJ

## 5 进阶方法：DP-STARJ

Motivated by the limitation in achieving an elegant tradeoff between utility, efficiency, and scalability under the output mechanism, we propose an advanced approach of DP-starJ, which can achieve strict DP with higher utility and efficiency to answer the star-join query. The main idea of DP-starJ is to decompose high sensitivity using the intrinsic characteristics of star-join to balance the utility and efficiency. Compared with the output perturbation mechanism, DP-starJ avoids the high sensitivity of star-join queries while improving the utility and reducing the computation cost.

针对输出机制在效用、效率与可扩展性之间难以取得平衡的局限，我们提出DP-starJ进阶方法，能以更高效用和效率实现严格差分隐私来响应星型连接查询。DP-starJ的核心思想是利用星型连接的内在特性分解高敏感度，从而平衡效用与效率。相较于输出扰动机制，DP-starJ在提升效用并降低计算成本的同时，规避了星型连接查询的高敏感度问题。

<!-- Media -->

<!-- figureText: Create_Predicate ${\text{Pred}}_{1}$ Noisy Pred 1 ${\mathrm{D}}_{s}$ $\vdots$ Noisy Pred ${}_{i}$ Noisy Noisy Result Query $\vdots$ ${\text{NoisyPred}}_{n}$ Answering Perturbation Query Star-join Query ${R}_{1}$ $\vdots$ $\vdots$ Star-join Differential Create_Predicate ${\text{Pred}}_{i}$ Privacy Query Mechanism $\vdots$ $\vdots$ Create_Predicate ${\text{Pred}}_{n}$ Extract Predicates -->

<img src="https://cdn.noedgeai.com/01965995-a2c5-7ee2-a7da-5837c049adeb_10.jpg?x=165&y=256&w=1239&h=303&r=0"/>

Fig. 2. Three Execution Phases of DP-starJ

图2. DP-starJ的三个执行阶段

<!-- Media -->

The overall intuition of DP-starJ is to add noise to star-join queries from the view of input, which turns out to be a challenging task. In the following, we first present a framework of DP-starJ to answer star-join query under DP and then propose a mechanism of input perturbation in DP-starJ, namely, Predicate Mechanism (PM). Afterwards, we introduce DP-starJ to support various types of star-join queries. At last, we give the granularity of privacy and utility study.

DP-starJ的核心思想是从输入视角对星型连接查询添加噪声，这被证明是一项具有挑战性的任务。下文首先提出DP-starJ框架来实现差分隐私下的星型连接查询应答，继而提出DP-starJ中的输入扰动机制——谓词机制(PM)。随后介绍DP-starJ对各类星型连接查询的支持，最后给出隐私粒度与效用研究的分析。

### 5.1 DP-starJ

### 5.1 DP-starJ

As discussed in Section 4, none of the existing mechanisms can overcome all three key challenges (utility, efficiency, and scalability) in DP-compliant star-join query. To address this problem, we first propose a framework called DP-starJ that answers the star-join query under DP. Its main idea is to add random noise to star-join query procedure rather than the query result. DP-starJ decomposes the predicates of star-join query to reduce the high global sensitivity of the query. Specifically, DP-starJ mainly consists of three phases as depicted in Figure 2:

如第4节所述，现有机制均无法同时解决符合差分隐私(DP)的星型连接查询中效用性、效率与可扩展性三大核心挑战。为此，我们首次提出名为DP-starJ的框架，该框架在DP约束下应答星型连接查询。其核心思想是在查询过程中而非结果上添加随机噪声。DP-starJ通过分解查询谓词来降低查询的高全局敏感度，具体包含图2所示的三个阶段：

Phase 1. Extract Predicates. In DP-starJ,given the star-join query $Q$ with $n$ dimension tables and a fact table ${R}_{0}$ ,the server first extracts predicates from the query. The star-join query can be expressed as a predicate query because of the star structure of the database instance and the independence of dimension tables. Therefore, in this phase, the server mainly extracts predicates of each dimension table according to the star-join query and database schema. In star-join query, the type of predicate typically includes the range constraint and point constraint of the attributes in the dimension table. Hence, this phase extracts predicates based on the dimension table involved in the given query. If the star-join query includes all dimension tables, the server will create one predicate for each of the $n$ dimension tables,resulting in a total of $n$ predicates.

阶段1：谓词提取。DP-starJ接收含$Q$个维度表与事实表$n$的星型连接查询${R}_{0}$后，服务器首先提取查询谓词。由于数据库实例的星型结构及维度表独立性，该查询可表示为谓词查询。本阶段主要根据查询语句与数据库模式提取各维度表谓词，通常包括维度表属性的范围约束与点约束。若查询涉及全部维度表，则将为每个$n$维度表生成一个谓词，共$n$个谓词。

Phase 2. Perturbation Query. In this phase, we employ some perturbation mechanisms to the star-join query to ensure differential privacy of the DP-starJ framework. The main process involves adding random noise into the predicates generated in Phase 1. Then, it aggregates all noise predicates together into noised star-join query, where the perturbation mechanism adopted is orthogonal and various specific methods can be employed, e.g., the Laplace Mechanism for each attribute adopted in this paper.

阶段2：扰动查询。本阶段对星型连接查询施加扰动机制以确保DP-starJ框架的差分隐私性。主要流程为：对阶段1生成的谓词添加随机噪声，再将所有噪声谓词聚合为含噪星型连接查询。所采用的扰动机制具有正交性，可选用多种具体方法，例如本文采用的各属性拉普拉斯机制。

Phase 3. Answering Star-join Query. In this phase,the server answers the star-join query $Q$ in a DP manner by accessing the database instance ${\mathbf{D}}_{s}$ with the noisy star-join query $\widehat{Q}$ .

阶段3：查询应答。服务器通过含噪星型连接查询$\widehat{Q}$访问数据库实例${\mathbf{D}}_{s}$，以差分隐私方式响应星型连接查询$Q$。

To balance utility, efficiency, and scalability, DP-starJ responds to the star-join query in the form of an input perturbation. In addition, it decomposes predicates to reduce the sensitivity of the query in order to improve the utility. In the following, we present Predicate Mechanism to implement the DP-starJ framework, which helps us identify the key problems for developing DP-starJ.

为平衡效用性、效率与可扩展性，DP-starJ采用输入扰动形式响应查询，并通过谓词分解降低查询敏感度以提升效用。下文将阐述实现该框架的谓词机制(Predicate Mechanism)，该机制揭示了开发DP-starJ的关键问题。

<!-- Media -->

Algorithm 1: Predicate Mechanism

算法1：谓词机制

---

Input: Star-join query $Q$ ,Data instance ${\mathbf{D}}_{s}$ ,Data Matrix $\mathbf{W}$ ,parameter $\epsilon$

输入：星型连接查询$Q$、数据实例${\mathbf{D}}_{s}$、数据矩阵$\mathbf{W}$、参数$\epsilon$

Output: Noisy result: $\widehat{Q}\left( {\mathrm{D}}_{s}\right)$

输出：噪声结果$\widehat{Q}\left( {\mathrm{D}}_{s}\right)$

$\Phi  \leftarrow  Q$ ;

${\epsilon }_{i} = \frac{\epsilon }{n};$

for each predicate ${\phi }_{{a}_{i}} \in  \Phi$ do

对每个谓词${\phi }_{{a}_{i}} \in  \Phi$执行：

	${\widehat{\phi }}_{{a}_{i}} \leftarrow  {\phi }_{{a}_{i}} + \operatorname{Lap}\left( \frac{\operatorname{dom}\left( {a}_{i}\right) }{{\epsilon }_{i}}\right)$

$\widehat{\Phi } \leftarrow  {\widehat{\phi }}_{{a}_{1}} \land  \ldots  \land  {\widehat{\phi }}_{{a}_{n}}$

$\widehat{Q}\left( {\mathbf{D}}_{s}\right)  = \widehat{\Phi } \cdot  \mathbf{W};$

Return $\widehat{Q}\left( {\mathbf{D}}_{s}\right)$

返回$\widehat{Q}\left( {\mathbf{D}}_{s}\right)$

---

<!-- Media -->

### 5.2 Predicate Mechanism

### 5.2 谓词机制

Let ${\mathrm{D}}_{s}$ be a database instance over star schema and a star-join query $Q$ aggregates over the join result $J\left( {\mathrm{D}}_{s}\right)$ . Since $\Phi$ is an indicate function,we simplify Equation 2 to the following form:

设${\mathrm{D}}_{s}$为星型模式数据库实例，星型连接查询$Q$对连接结果$J\left( {\mathrm{D}}_{s}\right)$进行聚合。由于$\Phi$是指示函数，可将公式2简化为：

$$
Q\left( {\mathbf{D}}_{s}\right)  = \mathop{\sum }\limits_{{t \in  J\left( {\mathbf{D}}_{s}\right) }}\Phi \left( t\right)  \cdot  \mathbf{w}\left( t\right)  = \mathbf{\Phi } \cdot  \mathbf{w}\left( t\right) . \tag{10}
$$

Hereby $\Phi$ refers to a predicate matrix of star-join query with all records. Since in star-join queries each dimension table ${R}_{i},i \in  \left\lbrack  n\right\rbrack$ is independent of each other and places filter predicates towards the attributes locally,thus $\Phi$ can reflect the conjunctions of the predicate, $\Phi  \mathrel{\text{:=}} {\phi }_{{a}_{1}} \land  {\phi }_{{a}_{2}} \land  \ldots  \land  {\phi }_{{a}_{n}}$ . Moreover,we can vectorize the weight function of tuple $\mathbf{w}\left( t\right)$ as $\mathbf{W}$ ,so the above equation can be transformed into the following form:

此处$\Phi$表示含所有记录的星型连接查询谓词矩阵。由于星型查询中各维度表${R}_{i},i \in  \left\lbrack  n\right\rbrack$相互独立且对属性施加本地过滤谓词，因此$\Phi$能反映谓词$\Phi  \mathrel{\text{:=}} {\phi }_{{a}_{1}} \land  {\phi }_{{a}_{2}} \land  \ldots  \land  {\phi }_{{a}_{n}}$的合取关系。进一步将元组$\mathbf{w}\left( t\right)$的权重函数向量化为$\mathbf{W}$，则上式可转换为：

$$
Q\left( {\mathbf{D}}_{s}\right)  = \Phi  \cdot  \mathbf{w}\left( t\right)  = \Phi  \cdot  \mathbf{W} = \left( {{\phi }_{{a}_{1}} \land  {\phi }_{{a}_{2}} \land  \ldots  \land  {\phi }_{{a}_{n}}}\right)  \cdot  \mathbf{W}, \tag{11}
$$

where ${\phi }_{{a}_{i}}$ is the predicate condition of dimension table ${R}_{i}$ in the star-join query $Q$ .

其中${\phi }_{{a}_{i}}$表示星型连接查询$Q$中维度表${R}_{i}$的谓词条件。

Unlike the output perturbation, Predicate Mechanism adds random perturbations to the predicates involved in the star-join query procedure before touching the raw database instance.

与输出扰动不同，谓词机制在接触原始数据库实例前，先对星型连接查询过程涉及的谓词添加随机扰动。

$$
\widehat{Q}\left( {\mathbf{D}}_{s}\right)  = \left( {\Phi  + \operatorname{Lap}\left( \frac{G{S}_{\Phi }}{\epsilon }\right) }\right)  \cdot  \mathbf{W}
$$

$$
 = \left( {\left( {{\phi }_{{a}_{1}} + \operatorname{Lap}\left( \frac{G{S}_{{\phi }_{{a}_{1}}}}{{\epsilon }_{1}}\right) }\right)  \land  \ldots  \land  \left( {{\phi }_{{a}_{n}} + \operatorname{Lap}\left( \frac{G{S}_{{\phi }_{{a}_{n}}}}{{\epsilon }_{n}}\right) }\right) }\right)  \cdot  \mathbf{W} \tag{12}
$$

$$
 = \left( {{\widehat{\phi }}_{{a}_{1}} \land  \ldots  \land  {\widehat{\phi }}_{{a}_{n}}}\right)  \cdot  \mathbf{W}
$$

$$
 = \widehat{\Phi } \cdot  \mathbf{W},
$$

where the privacy cost is ${\epsilon }_{i} = \frac{\epsilon }{n}$ ,and the global sensitivity $G{S}_{{\phi }_{{a}_{i}}}$ of each predicate ${\phi }_{{a}_{i}}$ is the domain size of attribute ${a}_{i}$ in dimension table ${R}_{i}$ . Algorithm 1 shows the pseudo-code of PM,the server (i) generates the predicate $\Phi$ in a star-join query $Q$ ,(ii) decomposes $\Phi$ into dimension table predicates ${\phi }_{{a}_{i}}$ based on $Q$ and adds noise to the predicates ${\phi }_{{a}_{i}}$ ,and (iii) answers star-join query $Q$ according to the noised predicate $\widehat{\Phi }$ and finally obtains the ${dp}$ result $\widehat{Q}\left( {\mathbf{D}}_{s}\right)$ . The main idea of PM is to add random noise to each predicate ${\phi }_{{a}_{i}}$ of dimension tables in star-join query $Q$ because the predicates of each dimension table are independent of each other. We now carry on with the predicate perturbation of each single-dimension table for the predicate mechanism implementation.

其中隐私成本为${\epsilon }_{i} = \frac{\epsilon }{n}$，且每个谓词${\phi }_{{a}_{i}}$的全局敏感度$G{S}_{{\phi }_{{a}_{i}}}$等于维度表${R}_{i}$中属性${a}_{i}$的域大小。算法1展示了PM的伪代码：服务器(i)在星型连接查询$Q$中生成谓词$\Phi$，(ii)基于$Q$将$\Phi$分解为维度表谓词${\phi }_{{a}_{i}}$并对这些谓词${\phi }_{{a}_{i}}$添加噪声，(iii)根据加噪后的谓词$\widehat{\Phi }$回答星型连接查询$Q$，最终获得${dp}$结果$\widehat{Q}\left( {\mathbf{D}}_{s}\right)$。PM的核心思想是对星型连接查询$Q$中每个维度表的谓词${\phi }_{{a}_{i}}$添加随机噪声，因为各维度表的谓词彼此独立。接下来我们将针对单维度表谓词进行扰动，以实现谓词机制。

In the predicate perturbation, a straightforward solution is to perturb each predicate separately using a single Laplace perturbation algorithm, such that every attribute is given a privacy budget ${\epsilon }_{i} = \epsilon /n$ . Then,it is well known that the Laplace perturbation is suitable for real values,but the predicate of the query may contain point constraints and range constraints of an attribute. For two classes of predicates, we use Laplace noise to perturb predicates with point constraints and range constraints,respectively. The specific process is as follows. In an attribute ${a}_{i}$ ,the predicate ${\phi }_{{a}_{i}}$ in dimension table ${R}_{i}$ may contain either range constraints ${a}_{i} \in  \left\lbrack  {l,r}\right\rbrack$ ,or point constraints ${a}_{i} = v$ . If the predicate is a point constraint, the predicate perturbation is directly adding the Laplace noise to the value $v$ . When the predicate is a range constraint, ${a}_{i} \in  \left\lbrack  {l,r}\right\rbrack$ ,the predicate perturbation is to perturb both ends of the interval $\left\lbrack  {l,r}\right\rbrack$ independently using a Laplace perturbation algorithm,such that every attribute is given a privacy budget $\epsilon /2$ . The specific process is shown in Algorithm 2.

在谓词扰动过程中，直接方案是使用单一拉普拉斯扰动算法分别扰动每个谓词，为每个属性分配隐私预算${\epsilon }_{i} = \epsilon /n$。众所周知，拉普拉斯扰动适用于实数值，但查询谓词可能包含属性的点约束和范围约束。针对这两类谓词，我们分别采用拉普拉斯噪声来扰动点约束谓词和范围约束谓词。具体流程如下：在属性${a}_{i}$中，维度表${R}_{i}$的谓词${\phi }_{{a}_{i}}$可能包含范围约束${a}_{i} \in  \left\lbrack  {l,r}\right\rbrack$或点约束${a}_{i} = v$。若谓词为点约束，则直接对值$v$添加拉普拉斯噪声；当谓词为范围约束${a}_{i} \in  \left\lbrack  {l,r}\right\rbrack$时，则使用拉普拉斯扰动算法独立扰动区间两端$\left\lbrack  {l,r}\right\rbrack$，为每个属性分配隐私预算$\epsilon /2$。具体流程如算法2所示。

<!-- Media -->

Algorithm 2: PM for An Attribute $\left( {\mathrm{{PM}}}_{\mathrm{A}}\right)$

算法2：属性$\left( {\mathrm{{PM}}}_{\mathrm{A}}\right)$的PM实现

---

Input: Predicate ${\phi }_{{a}_{i}}$ of an attribute ${a}_{i}$ ,parameter $\epsilon$

输入：属性${a}_{i}$的谓词${\phi }_{{a}_{i}}$，参数$\epsilon$

Output: Noisy Predicate: ${\widehat{\phi }}_{{a}_{i}}$

输出：加噪谓词：${\widehat{\phi }}_{{a}_{i}}$

if ${\phi }_{{a}_{i}}$ is ${a}_{i} = v$ then

若${\phi }_{{a}_{i}}$为${a}_{i} = v$则

	$\widehat{v} = v + \operatorname{Lap}\left( {\operatorname{dom}\left( {a}_{i}\right) /\epsilon }\right) ;$

	${\widehat{\phi }}_{{a}_{i}} \leftarrow  {a}_{i} = \widehat{v}$

else

否则

	${\phi }_{{a}_{i}} \leftarrow  {a}_{i} \in  \left\lbrack  {l,r}\right\rbrack$

	$\widehat{l} = l + \operatorname{Lap}\left( \frac{2 \cdot  \operatorname{dom}\left( {a}_{i}\right) }{\epsilon }\right) ;$

	$\widehat{r} = r + \operatorname{Lap}\left( \frac{2 \cdot  \operatorname{dom}\left( {a}_{i}\right) }{\epsilon }\right) ;$

	while $\widehat{l} < \widehat{r}$ do

	当$\widehat{l} < \widehat{r}$时循环

		${\widehat{\phi }}_{{a}_{i}} \leftarrow  {a}_{i} \in  \left\lbrack  {\widehat{l},\widehat{r}}\right\rbrack$

Return ${\widehat{\phi }}_{{a}_{i}}$

返回${\widehat{\phi }}_{{a}_{i}}$

---

<!-- Media -->

### 5.3 DP-starJ Applications

### 5.3 DP-starJ应用场景

To further boost the robust performance, in this section, we discuss specific solutions for the predicate mechanism in DP-starJ for various types of star-join tasks. The main idea of DP-starJ is to inject random data-independent noise into star-join query, which is an application of the predicate mechanism on different star-join queries. Therefore, we present the predicate mechanism for aggregated star-join queries, "Group_By" operation, and star-join workload queries as follow. PM for Aggregated Star-join Queries. We now consider the case for the star-join aggregation query that aggregates the number of tuples that satisfy the filter conditions. In this case, the solution is to perturb each predicate independently using a single predicate perturbation algorithm (Algorithm 2),such that every attribute is given a privacy budget ${\epsilon }_{i} = \epsilon /n$ . The specific process is shown in Algorithm 3, where the data matrix is 1 in which the value of all tuples is 1 . If the aggregation function is the SUM in the star-join, the element of the data matrix is the value of the attribute, which is the summation over the attributes in the star-join query. In addition, if the star-join query involves "Group_By" operation, similar to COUNT queries and SUM queries, we shall only perturb the predicates of the query before "Group_By" operation. Therefore, DP-StarJ supports not only ordinary aggregate queries but also "Group_By" statement in star-joins.

为进一步提升鲁棒性能，本节将探讨DP-starJ中针对各类星型连接任务的谓词机制具体实现方案。DP-starJ的核心思想是在星型连接查询中注入与数据无关的随机噪声，这是谓词机制在不同星型连接查询中的应用体现。因此，我们分别提出针对聚合星型连接查询、"Group_By"操作及星型连接工作负载查询的谓词机制如下。聚合星型连接查询的PM：针对满足过滤条件的元组数量进行聚合的星型连接查询，解决方案是采用单谓词扰动算法（算法2）独立扰动每个谓词，为每个属性分配隐私预算${\epsilon }_{i} = \epsilon /n$。具体流程如算法3所示，其中数据矩阵为全1矩阵。若聚合函数为SUM，则数据矩阵元素取属性值之和。此外，对于涉及"Group_By"操作的查询，与COUNT/SUM查询类似，我们仅需在"Group_By"操作前扰动查询谓词。因此DP-StarJ不仅支持常规聚合查询，还能处理星型连接中的"Group_By"语句。

PM for Star-join Workload Queries. In addition, as workload tasks are ubiquitous in OLAP scenarios [33], we extend DP-StarJ to answer star-join workload queries under differential privacy by using PM. Given a workload of $l$ star-join queries $\mathbf{L},\mathbf{L} = \left\{  {{Q}_{1},{Q}_{2},\ldots ,{Q}_{l}}\right\}$ . One straightforward solution is to process each query ${Q}_{i}$ independently by using the Predicate Mechanism. Unfortunately, this strategy fails to exploit the correlations between different queries, which has been exhaustively studied and justified to be valuable in designing a more effective DP solution, i.e., the matrix mechanism [21, 41]. The matrix mechanism is originally proposed for linear counting queries, which achieves better utility by taking into account the correlations of workload queries. Consider a workload of three different queries, ${Q}_{1}$ is interested in the total number of products sold in the first half of this year,while ${Q}_{2}$ is interested in the total number of products sold in the second half of this year,and ${Q}_{3}$ asks for the total number throughout the whole year. Clearly,the three queries are correlated with each other as ${Q}_{3} = {Q}_{1} + {Q}_{2}$ . Given that fact,an alternative strategy for answering these queries is to process only ${Q}_{1}$ and ${Q}_{2}$ ,and use their sum to answer ${Q}_{3}$ . Inspired by this phenomenon, we propose a Workload Decomposition (WD) strategy to answer star-join workload queries under differential privacy in the following.

星型连接工作负载查询的PM：鉴于工作负载任务在OLAP场景中的普遍性[33]，我们扩展DP-StarJ通过PM机制在差分隐私下应答星型连接工作负载查询。给定包含$l$个星型连接查询的工作负载$\mathbf{L},\mathbf{L} = \left\{  {{Q}_{1},{Q}_{2},\ldots ,{Q}_{l}}\right\}$，初始方案是采用谓词机制独立处理每个查询${Q}_{i}$。但该策略未能利用查询间相关性，而矩阵机制[21,41]已证实这种相关性对提升DP方案有效性具有重要价值。以三个关联查询为例：${Q}_{1}$统计上半年产品销量，${Q}_{2}$统计下半年销量，${Q}_{3}$要求全年总量。显然三者存在${Q}_{3} = {Q}_{1} + {Q}_{2}$的关联性。基于此，替代策略是仅处理${Q}_{1}$和${Q}_{2}$，通过求和应答${Q}_{3}$。受此启发，下文提出工作负载分解(WD)策略来实现差分隐私下的星型连接工作负载查询应答。

<!-- Media -->

Algorithm 3: PM for Star-join Counting Query

算法3：星型连接计数查询的PM

---

Input: Star-join counting query ${Q}_{c}$ ,Data instance ${\mathbf{D}}_{s}$ ,Data Matrix $\mathbf{W}$ ,parameter $\epsilon$

输入：星型连接计数查询${Q}_{c}$，数据实例${\mathbf{D}}_{s}$，数据矩阵$\mathbf{W}$，参数$\epsilon$

Output: Noisy result: ${\widehat{Q}}_{c}\left( {\mathbf{D}}_{s}\right)$

输出：噪声处理结果：${\widehat{Q}}_{c}\left( {\mathbf{D}}_{s}\right)$

$\Phi  \leftarrow  {Q}_{c};$

${\epsilon }_{i} = \frac{\epsilon }{n};$

for each predicate ${\phi }_{{a}_{i}} \in  \Phi$ do

遍历每个谓词${\phi }_{{a}_{i}} \in  \Phi$执行

	${\widehat{\phi }}_{{a}_{i}} \leftarrow  {\mathrm{{PM}}}_{\mathrm{A}}\left( {{\phi }_{{a}_{i}},{\epsilon }_{i}}\right)$

$\widehat{\Phi } \leftarrow  {\widehat{\phi }}_{{a}_{1}} \land  \ldots  \land  {\widehat{\phi }}_{{a}_{n}};$

${\widehat{Q}}_{c}\left( {\mathbf{D}}_{s}\right)  = \widehat{\Phi } \cdot  \mathbf{W};$

Return ${\widehat{Q}}_{c}\left( {\mathrm{D}}_{s}\right)$

返回${\widehat{Q}}_{c}\left( {\mathrm{D}}_{s}\right)$

---

<!-- Media -->

Consider the star-join workload queries $\mathbf{L} = \left\{  {{Q}_{1},{Q}_{2},\ldots ,{Q}_{l}}\right\}$ . According to our discussion in Section 3,each star-join query ${Q}_{i}$ can be represented by its predicate ${\Phi }_{i}$ . Following that way,the star-join workload queries $\mathbf{L}$ can be accordingly represented as a set of predicates ${\Phi }_{i},\mathbf{L} \mathrel{\text{:=}} \left\{  {{\Phi }_{1},{\Phi }_{2},\ldots ,{\Phi }_{l}}\right\}$ . Each predicate ${\Phi }_{1}$ refers to filter conditions for different dimension tables, ${\Phi }_{i} \mathrel{\text{:=}} {\phi }_{{a}_{1}}^{i} \land  {\phi }_{{a}_{2}}^{i} \land  \ldots  \land  {\phi }_{{a}_{n}}^{i}$ .

考虑星型连接工作负载查询$\mathbf{L} = \left\{  {{Q}_{1},{Q}_{2},\ldots ,{Q}_{l}}\right\}$。根据第3节的讨论，每个星型连接查询${Q}_{i}$可通过其谓词${\Phi }_{i}$表示。依此方式，星型连接工作负载查询$\mathbf{L}$可相应地表示为谓词集合${\Phi }_{i},\mathbf{L} \mathrel{\text{:=}} \left\{  {{\Phi }_{1},{\Phi }_{2},\ldots ,{\Phi }_{l}}\right\}$。每个谓词${\Phi }_{1}$对应不同维度表的过滤条件，${\Phi }_{i} \mathrel{\text{:=}} {\phi }_{{a}_{1}}^{i} \land  {\phi }_{{a}_{2}}^{i} \land  \ldots  \land  {\phi }_{{a}_{n}}^{i}$。

Firstly,we adopt one-hot-encoding to quantify ${\Phi }_{i}$ into a series of vectors. As shown in Example 1.2, the predicate of the star-join query is $\Phi  = {\phi }_{\text{Date }} \land  {\phi }_{\text{Cust }} \land  {\phi }_{\text{Supp }}$ ,assume that the domain of region is $\{ \mathrm{A},\mathrm{B},\mathrm{C}\}$ and $\mathrm{{REGION}} = \mathrm{C}$ ,we can vectorized $\Phi$ as $\left\lbrack  {111111000000001001}\right\rbrack$ . Similarly,the vector representation for ${\phi }_{\text{Date }}$ and ${\phi }_{\text{Cust }}\left( {\phi }_{\text{Supp }}\right)$ are $\left\lbrack  {111111000000}\right\rbrack$ and $\left\lbrack  {001}\right\rbrack  \left( \left\lbrack  {001}\right\rbrack  \right)$ ,respectively. Therefore,the workload queries L,i.e.,a collection of $l$ star-join queries,can be arranged by rows and forms an $l \times  {m}_{d}$ matrix and ${m}_{d} = \mathop{\prod }\limits_{{i = 1}}^{n}{m}_{i}$ . The predicate matrix ${\mathbf{P}}_{i}^{\mathbf{L}}$ of each dimension table ${R}_{i}$ on workload queries $\mathbf{L}$ is an $l \times  {m}_{i}$ matrix,hereby ${m}_{i}$ is the domain size of attribute on dimension table ${R}_{i}$ .

首先，我们采用独热编码将${\Phi }_{i}$量化为向量序列。如示例1.2所示，星型连接查询的谓词为$\Phi  = {\phi }_{\text{Date }} \land  {\phi }_{\text{Cust }} \land  {\phi }_{\text{Supp }}$，假设地区域的取值域是$\{ \mathrm{A},\mathrm{B},\mathrm{C}\}$和$\mathrm{{REGION}} = \mathrm{C}$，则可将$\Phi$向量化为$\left\lbrack  {111111000000001001}\right\rbrack$。同理，${\phi }_{\text{Date }}$和${\phi }_{\text{Cust }}\left( {\phi }_{\text{Supp }}\right)$的向量表示分别为$\left\lbrack  {111111000000}\right\rbrack$和$\left\lbrack  {001}\right\rbrack  \left( \left\lbrack  {001}\right\rbrack  \right)$。因此，工作负载查询L（即包含$l$个星型查询的集合）可按行排列，构成$l \times  {m}_{d}$矩阵和${m}_{d} = \mathop{\prod }\limits_{{i = 1}}^{n}{m}_{i}$。每个维度表${R}_{i}$在工作负载查询$\mathbf{L}$上的谓词矩阵${\mathbf{P}}_{i}^{\mathbf{L}}$是$l \times  {m}_{i}$矩阵，此处${m}_{i}$表示维度表${R}_{i}$上属性的取值域大小。

Secondly,for each predicate matrix ${\mathbf{P}}_{i}^{\mathrm{L}}$ ,we shall perform a matrix decomposition as follow:

其次，对于每个谓词矩阵${\mathbf{P}}_{i}^{\mathrm{L}}$，我们需执行如下矩阵分解：

Definition 5.1 (Matrix Decomposition). Given a predicate matrix $\mathbf{M}$ and a strategy matrix $\mathbf{A}$ ,we say $\mathbf{M}$ decomposes into $\mathbf{{XA}}$ if each predicate in $\mathbf{M}$ can be expressed as a linear combination of predicates in $\mathbf{A}$ . In other words,there exists a solution matrix $\mathbf{X}$ to $\mathbf{M} = \mathbf{{XA}}$ .

定义5.1（矩阵分解）。给定谓词矩阵$\mathbf{M}$和策略矩阵$\mathbf{A}$，若$\mathbf{M}$中每个谓词均可表示为$\mathbf{A}$中谓词的线性组合，则称$\mathbf{M}$可分解为$\mathbf{{XA}}$。换言之，存在解矩阵$\mathbf{X}$满足$\mathbf{M} = \mathbf{{XA}}$。

For each predicate matrix on the workload queries $\mathrm{L},{\mathrm{P}}_{i}^{\mathrm{L}}$ ,MD shall finds a new strategy matrix ${\mathrm{A}}_{i}$ to support ${\mathbf{P}}_{i}^{\mathbf{L}}$ ,and then evaluates the strategy matrix ${\mathbf{A}}_{i}$ using the Predicate Mechanism to obtain a noisy strategy matrix ${\widehat{\mathbf{A}}}_{i}$ . Afterwards,we can reconstruct a noisy predicate matrix from the noisy strategy matrix ${\widehat{\mathbf{A}}}_{i},{\widehat{\mathbf{P}}}_{i}^{\mathrm{L}} = {\mathbf{A}}_{i}^{ + }{\widehat{\mathbf{A}}}_{i}$ .

针对工作负载查询中的每个谓词矩阵$\mathrm{L},{\mathrm{P}}_{i}^{\mathrm{L}}$，MD应找到新的策略矩阵${\mathrm{A}}_{i}$以支持${\mathbf{P}}_{i}^{\mathbf{L}}$，随后通过谓词机制评估策略矩阵${\mathbf{A}}_{i}$，获得含噪策略矩阵${\widehat{\mathbf{A}}}_{i}$。之后，可从含噪策略矩阵${\widehat{\mathbf{A}}}_{i},{\widehat{\mathbf{P}}}_{i}^{\mathrm{L}} = {\mathbf{A}}_{i}^{ + }{\widehat{\mathbf{A}}}_{i}$重构出含噪谓词矩阵。

Finally,we connect the noisy predicate matrix ${\widehat{\mathbf{P}}}_{i}^{\mathrm{L}}$ to each corresponding dimension table into the noisy predicate matrix $\widehat{\mathbf{P}}$ . The server answers the star-join workload queries $\widehat{\mathbf{L}}$ in a DP manner by accessing the database instance with the noisy predicate matrix $\widehat{\mathbf{P}}$ on the noisy star-join workload queries $\widehat{\mathbf{L}}$ .

最终，我们将含噪谓词矩阵${\widehat{\mathbf{P}}}_{i}^{\mathrm{L}}$连接到各对应维度表，形成星型连接工作负载查询的含噪谓词矩阵$\widehat{\mathbf{P}}$。服务器通过访问带有含噪谓词矩阵$\widehat{\mathbf{P}}$的数据库实例，以差分隐私(DP)方式响应星型连接工作负载查询$\widehat{\mathbf{L}}$。

<!-- Media -->

Algorithm 4: PM for Star-join Workload Queries

算法4：星型连接工作负载查询的谓词机制

---

	19ut: Star-join Workload Queries $\mathbf{L} = \left\{  {{Q}_{1},{Q}_{2},\ldots ,{Q}_{l}}\right\}$ ,Data instance ${\mathbf{D}}_{s}$ ,Data Matrix

	输入：星型连接工作负载查询$\mathbf{L} = \left\{  {{Q}_{1},{Q}_{2},\ldots ,{Q}_{l}}\right\}$，数据实例${\mathbf{D}}_{s}$，数据矩阵

					$\mathbf{W}$ ,parameter $\epsilon$

					$\mathbf{W}$，参数$\epsilon$

Output: Noisy result: ${\widehat{Q}}_{L}\left( {\mathrm{D}}_{s}\right)$

输出：含噪结果：${\widehat{Q}}_{L}\left( {\mathrm{D}}_{s}\right)$

$\mathrm{P} \leftarrow$ One - Hot - Encoding(L) ;

$\mathrm{P} \leftarrow$ 独热编码(L)；

${\epsilon }_{i} = \frac{\epsilon }{n}$ ;

for each predicate matrix ${\mathbf{P}}_{i}^{\mathbf{L}} \in  \mathbf{P}$ do

遍历每个谓词矩阵${\mathbf{P}}_{i}^{\mathbf{L}} \in  \mathbf{P}$执行

		${\mathrm{A}}_{i} \leftarrow  \operatorname{MatrixDecom}\left( {\mathrm{P}}_{i}^{\mathrm{L}}\right)$ ;

			${\phi }_{{a}_{i}} \leftarrow  {\mathbf{A}}_{i}$ ;

		${\widehat{\phi }}_{{a}_{i}} \leftarrow  {\mathbf{{PM}}}_{\mathrm{A}}\left( {{\phi }_{{a}_{i}},{\epsilon }_{i}}\right) ;$

			${\widehat{\mathbf{A}}}_{i} \leftarrow  {\widehat{\phi }}_{{a}_{i}}$ ;

		${\widehat{\mathbf{P}}}_{i}^{\mathrm{L}} = {\mathbf{A}}_{i}^{ + }{\widehat{\mathbf{A}}}_{i};$

$\widehat{\mathbf{P}} \leftarrow  {\widehat{\mathbf{P}}}_{1}^{\mathrm{L}},\ldots ,{\widehat{\mathbf{P}}}_{n}^{\mathrm{L}};$

$\widehat{\mathbf{L}} \leftarrow  \widehat{\mathbf{P}};$

${\widehat{Q}}_{L}\left( {\mathbf{D}}_{s}\right)  = \widehat{\mathbf{L}} \cdot  \mathbf{W};$

Return ${\widehat{Q}}_{L}\left( {\mathbf{D}}_{s}\right)$

返回${\widehat{Q}}_{L}\left( {\mathbf{D}}_{s}\right)$

---

<!-- Media -->

Algorithm 4 outlines the above procedure. It first uses one-hot-encoding to represent the predicate of star-join the workload queries $\mathbf{L}$ and assigns the privacy budget to the predicate matrix that decomposes the predicate according to the dimension table (Lines 1-2). After that, matrix decomposition is performed on each predicate matrix ${\mathbf{P}}_{i}^{\mathrm{L}}$ to get the corresponding strategy matrix ${\mathbf{A}}_{i}$ and applies Predicate Mechanism on the strategy matrix to obtain a noised strategy matrix ${\mathbf{A}}_{i}$ , and reconstruct the noise-injected predicate matrix ${\widehat{\mathbf{P}}}_{i}^{\mathrm{L}}$ through the noise-injected strategy matrix (Lines 3-9). Afterwards,it connects each noise-injected predicate matrix ${\widehat{\mathbf{P}}}_{i}^{\mathrm{L}}$ into $\widehat{\mathbf{P}}$ of the noisy star-join workload queries $\widehat{\mathbf{L}}$ and obtain the noisy result ${\widehat{Q}}_{L}\left( {\mathbf{D}}_{s}\right)$ of the workload by accessing the database instance ${\mathbf{D}}_{s}$ (Lines 10-12),and finally outputs the noisy result ${\widehat{Q}}_{L}\left( {\mathbf{D}}_{s}\right)$ .

算法4概述了上述流程。首先采用独热编码表示星型连接工作负载查询$\mathbf{L}$的谓词，并根据维度表分解谓词分配隐私预算至谓词矩阵（1-2行）。随后对每个谓词矩阵${\mathbf{P}}_{i}^{\mathrm{L}}$进行矩阵分解得到对应策略矩阵${\mathbf{A}}_{i}$，在策略矩阵上应用谓词机制获得含噪策略矩阵${\mathbf{A}}_{i}$，并通过含噪策略矩阵重构出注入噪声的谓词矩阵${\widehat{\mathbf{P}}}_{i}^{\mathrm{L}}$（3-9行）。接着将各含噪谓词矩阵${\widehat{\mathbf{P}}}_{i}^{\mathrm{L}}$连接至星型连接工作负载查询$\widehat{\mathbf{L}}$的$\widehat{\mathbf{P}}$中，通过访问数据库实例${\mathbf{D}}_{s}$获取工作负载的含噪结果${\widehat{Q}}_{L}\left( {\mathbf{D}}_{s}\right)$（10-12行），最终输出含噪结果${\widehat{Q}}_{L}\left( {\mathbf{D}}_{s}\right)$。

PM for snowflake queries. Besides star-join, the proposed PM can also be applied to the snowflake model (resp., snowflake query), which further hierarchizes the dimension tables of the star schema, resulting in a more normalized structure. For example, in Figure 1 and Example 1.2, Date can be decomposed into dimension tables such as Year, Quarter, Month, and Day, reducing redundancy. Therefore, the star-join query in Example 1.2 can be extended to snowflake query by changing Date.month $< 7$ to Date. ${MK} =$ Month.MK AND Month.month $< 7$ . At this point,we can directly apply PM to perturb the predicate to obtain the DP snowflake query. This does not affect the functionality of DP-starJ while extending star-join queries to queries on the snowflake model.

针对雪花查询的谓词扰动机制(PM)。除星型连接外，所提出的PM还可应用于雪花模型（即雪花查询），该模型对星型模式的维度表进行进一步层级化，形成更规范化的结构。例如图1和示例1.2中，Date可分解为Year、Quarter、Month、Day等维度表以减少冗余。因此示例1.2的星型连接查询可通过将Date.month$< 7$替换为Date.${MK} =$Month.MK AND Month.month$< 7$来扩展为雪花查询。此时可直接应用PM对谓词进行扰动以获得差分隐私(DP)雪花查询，这在不影响DP-starJ功能性的同时，将星型连接查询扩展至雪花模型查询。

### 5.4 Theoretical Study of Privacy and Utility

### 5.4 隐私性与效用性的理论研究

We now conduct a theoretical study on the privacy guarantee as well as the utility of the proposed Predicate Mechanism. In this section, we first study the privacy guarantee of the Predicate Mechanism and DP-starJ in terms of Definition 3.6. After that, we theoretically study the utility of the Predicate Mechanism.

现对谓词扰动机制的隐私保障与效用进行理论研究。本节首先基于定义3.6分析谓词扰动机制与DP-starJ的隐私保障，随后从理论上研究谓词扰动机制的效用性。

THEOREM 5.2. Algorithm 2 satisfies $\epsilon$ -differential privacy.

定理5.2. 算法2满足$\epsilon$-差分隐私。

Proof. Algorithm 2 adds Laplace noise to the predicate, and the scale of Laplace is the ratio between the domain and privacy cost. In the worst case, the number of predicate influences caused by a change in a record is equal to its domain size. Therefore, its global sensitivity is the size of the domain. That is, Algorithm 2 essentially implements the Laplace mechanism on the predicate. According to Theorem 3.2,Algorithm 2 satisfies $\epsilon$ -differential privacy.

证明. 算法2对谓词添加拉普拉斯噪声，其噪声尺度为定义域与隐私代价之比。最坏情况下，单条记录变更引发的谓词影响数等于其定义域大小，故全局敏感度即为定义域尺寸。因此算法2本质是对谓词实施拉普拉斯机制，根据定理3.2可知其满足$\epsilon$-差分隐私。

## THEOREM 5.3. PM and DP-star7 satisfy $\epsilon$ -differential privacy.

## 定理5.3. PM与DP-star7均满足$\epsilon$-差分隐私

Proof. The Predicate Mechanism decomposes $\Phi$ into dimension table predicates ${\phi }_{{a}_{i}}$ based on $Q$ and adds noise to the predicates ${\phi }_{{a}_{i}}$ ,the proof of PM is transformed into proving that each predicate ${\widehat{\phi }}_{{a}_{i}}$ satisfies $\frac{\epsilon }{n}$ -differential privacy according to the Theorem 5.2,and whether the predicate $\widehat{\Phi }$ satisfies $\epsilon$ -differential privacy. According to the fact that $\Phi  \mathrel{\text{:=}} {\phi }_{{a}_{1}} \land  \ldots  \land  {\phi }_{{a}_{1}}$ and each ${\phi }_{{a}_{1}}$ is independent of each other,we thus have $\Pr \left\lbrack  \Phi \right\rbrack   = \Pr \left\lbrack  {{\phi }_{{a}_{1}} \land  \ldots  \land  {\phi }_{{a}_{1}}}\right\rbrack   = \Pr \left\lbrack  {\phi }_{{a}_{1}}\right\rbrack   \cdot  \Pr \left\lbrack  {\phi }_{{a}_{2}}\right\rbrack  \cdots \Pr \left\lbrack  {\phi }_{{a}_{n}}\right\rbrack$ . Meanwhile,each predicate ${\phi }_{{a}_{i}}$ satisfies $\frac{\varepsilon }{n}$ -differential privacy, $\Pr \left\lbrack  {\phi }_{{a}_{i}}\right\rbrack   \leq  {e}^{\frac{\varepsilon }{n}} \cdot  \Pr \left\lbrack  {\widehat{\phi }}_{{a}_{i}}\right\rbrack$ . Therefore,

证明. 谓词扰动机制基于$Q$将$\Phi$分解为维度表谓词${\phi }_{{a}_{i}}$并施加噪声，其证明转化为：根据定理5.2需证各谓词${\widehat{\phi }}_{{a}_{i}}$满足$\frac{\epsilon }{n}$-差分隐私，以及谓词$\widehat{\Phi }$是否满足$\epsilon$-差分隐私。由于$\Phi  \mathrel{\text{:=}} {\phi }_{{a}_{1}} \land  \ldots  \land  {\phi }_{{a}_{1}}$与各${\phi }_{{a}_{1}}$相互独立，故有$\Pr \left\lbrack  \Phi \right\rbrack   = \Pr \left\lbrack  {{\phi }_{{a}_{1}} \land  \ldots  \land  {\phi }_{{a}_{1}}}\right\rbrack   = \Pr \left\lbrack  {\phi }_{{a}_{1}}\right\rbrack   \cdot  \Pr \left\lbrack  {\phi }_{{a}_{2}}\right\rbrack  \cdots \Pr \left\lbrack  {\phi }_{{a}_{n}}\right\rbrack$。同时各谓词${\phi }_{{a}_{i}}$满足$\frac{\varepsilon }{n}$-差分隐私，$\Pr \left\lbrack  {\phi }_{{a}_{i}}\right\rbrack   \leq  {e}^{\frac{\varepsilon }{n}} \cdot  \Pr \left\lbrack  {\widehat{\phi }}_{{a}_{i}}\right\rbrack$。因此

$$
\Pr \left\lbrack  \Phi \right\rbrack   = \Pr \left\lbrack  {\phi }_{{a}_{1}}\right\rbrack   \cdot  \Pr \left\lbrack  {\phi }_{{a}_{2}}\right\rbrack  \cdots \Pr \left\lbrack  {\phi }_{{a}_{n}}\right\rbrack  
$$

$$
 \leq  {e}^{\epsilon } \cdot  \left( {\Pr \left\lbrack  {\widehat{\phi }}_{{a}_{1}}\right\rbrack   \cdot  \Pr \left\lbrack  {\widehat{\phi }}_{{a}_{2}}\right\rbrack  \cdots \Pr \left\lbrack  {\widehat{\phi }}_{{a}_{n}}\right\rbrack  }\right)  = {e}^{\epsilon } \cdot  \Pr \left\lbrack  \widehat{\Phi }\right\rbrack   \tag{13}
$$

The Predicate Mechanism satisfies $\epsilon$ -differential privacy. Similar to Predicate Mechanism,DP-starJ can be proved to satisfy $\epsilon$ -differential privacy in the same way.

谓词扰动机制满足$\epsilon$-差分隐私。同理可证DP-starJ以相同方式满足$\epsilon$-差分隐私。

Both Algorithm 3&4 adopt the Predicate Mechanism, we shall study the privacy guarantee of them accordingly as follows.

算法3&4均采用谓词扰动机制，其隐私保障研究如下。

THEOREM 5.4. Algorithm 3 satisfies $\epsilon$ -differential privacy.

定理5.4. 算法3满足$\epsilon$-差分隐私。

Proof. Algorithm 3 decomposes query predicates by dimension tables, allocating the privacy budget of $\frac{\epsilon }{n}$ to each predicate. According to Theorem 5.2,each noisy predicate satisfies $\frac{\epsilon }{n}$ -differential privacy via Algorithm 2. Within each predicate, there is sequential composition because adding or removing a record affects all predicates. According to Sequential Composition [12], Algorithm 3 satisfies $\epsilon$ -differential privacy.

证明。算法3通过维度表分解查询谓词，将$\frac{\epsilon }{n}$的隐私预算分配给每个谓词。根据定理5.2，每个加噪谓词通过算法2满足$\frac{\epsilon }{n}$-差分隐私。在单个谓词内部存在顺序组合性，因为增删记录会影响所有谓词。根据顺序组合定理[12]，算法3满足$\epsilon$-差分隐私。

THEOREM 5.5. Algorithm 4 satisfies $\epsilon$ -differential privacy.

定理5.5。算法4满足$\epsilon$-差分隐私。

Proof. Algorithm 4 is suitable to answer star-join workload queries indirectly, by first perturbing a set of intermediate predicates under differential privacy via Algorithm 2, and then combining their predicates to answer the star-join workload queries. Thus,Algorithm 4 satisfies $\epsilon$ -differential privacy.

证明。算法4通过先用算法2对中间谓词集进行差分隐私扰动，再组合这些谓词来间接回答星型连接查询，因此满足$\epsilon$-差分隐私。

Besides the privacy guarantee, we now conduct a theoretical study on the utility of the Predicate Mechanism.

除隐私保障外，现对谓词机制的效用进行理论研究。

THEOREM 5.6 (LOOSE BOUND OF PREDICATE MECHANISM). Let $Q$ be the star-join query with $n$ dimension tables and a fact table,the variance of using Predicate Mechanism is ${\left( \frac{2{n}^{2}}{{\epsilon }^{2}}\right) }^{n} \cdot  \mathop{\prod }\limits_{{i = 1}}^{n}\operatorname{dom}{\left( {a}_{i}\right) }^{2}$ .

定理5.6（谓词机制的宽松界）。设$Q$为含$n$个维度表与事实表的星型连接查询，使用谓词机制的方差为${\left( \frac{2{n}^{2}}{{\epsilon }^{2}}\right) }^{n} \cdot  \mathop{\prod }\limits_{{i = 1}}^{n}\operatorname{dom}{\left( {a}_{i}\right) }^{2}$。

Proof. The proof can be found in our report [13].

证明。证明详见我们的报告[13]。

Theorem 5.7 (Tight Bound of Predicate Mechanism). Let $Q$ be the star-join query with $n$ dimension tables and a fact table,the variance of using the Predicate Mechanism is $\left( \frac{2{n}^{2}}{{\epsilon }^{2}}\right)  \cdot  \mathop{\sum }\limits_{{i = 1}}^{n}\operatorname{dom}{\left( {a}_{i}\right) }^{2}$ .

定理5.7（谓词机制的紧致界）。设$Q$为含$n$个维度表与事实表的星型连接查询，使用谓词机制的方差为$\left( \frac{2{n}^{2}}{{\epsilon }^{2}}\right)  \cdot  \mathop{\sum }\limits_{{i = 1}}^{n}\operatorname{dom}{\left( {a}_{i}\right) }^{2}$。

Proof. The proof can be found in our report [13].

证明。证明详见我们的报告[13]。

From the above analysis, it can be seen that the noise of PM is mainly related to the domain size, which avoids the high sensitivity of star-join queries. However, this also means that the noise magnitude is constrained by the database instance.

由上述分析可知，PM的噪声主要与域大小相关，这避免了星型连接查询的高敏感性。但这也意味着噪声量级受数据库实例约束。

<!-- Media -->

Table 1. Relative error(%) of various mechanisms PM, R2T, LS on SSB queries by varying $\epsilon$ .

表1. 不同$\epsilon$下PM、R2T、LS机制在SSB查询上的相对误差(%)。

<table><tr><td colspan="2">Query type</td><td colspan="4">COUNT</td><td colspan="3">SUM</td><td colspan="2">GROUP BY</td></tr><tr><td colspan="2">Query</td><td>${Q}_{c1}$</td><td>${Q}_{c2}$</td><td>${Q}_{c3}$</td><td>${Q}_{c4}$</td><td>${Q}_{s2}$</td><td>${Q}_{s3}$</td><td>${Q}_{s4}$</td><td>Qg2</td><td>${Q}_{g4}$</td></tr><tr><td rowspan="3">$\epsilon  = {0.1}$</td><td>PM</td><td>11.89</td><td>9.46</td><td>19.02</td><td>8.22</td><td>12.07</td><td>16.3</td><td>17.36</td><td>11</td><td>28.63</td></tr><tr><td>R2T</td><td>120.87</td><td>41.51</td><td>29.63</td><td>20.41</td><td>80.61</td><td>80.22</td><td>80.14</td><td colspan="2">Not supported*</td></tr><tr><td>LS</td><td>180.9</td><td>73.39</td><td>78.12</td><td>80.44</td><td colspan="5">Not supported</td></tr><tr><td rowspan="3">$\epsilon  = {0.2}$</td><td>PM</td><td>11.93</td><td>9.28</td><td>16.48</td><td>5.12</td><td>11.55</td><td>13.07</td><td>12.39</td><td>10.6</td><td>18.8</td></tr><tr><td>R2T</td><td>59.76</td><td>30.38</td><td>19.4</td><td>15.16</td><td>79.91</td><td>80.17</td><td>79.83</td><td colspan="2">Not supported*</td></tr><tr><td>LS</td><td>121.68</td><td>61.8</td><td>58.61</td><td>83</td><td colspan="5">Not supported</td></tr><tr><td rowspan="3">$\epsilon  = {0.5}$</td><td>PM</td><td>8.66</td><td>7.61</td><td>15.42</td><td>4.3</td><td>11.58</td><td>12.45</td><td>10.43</td><td>9.88</td><td>11.83</td></tr><tr><td>R2T</td><td>84.48</td><td>22.9</td><td>19.67</td><td>11.55</td><td>79.46</td><td>80.08</td><td>79.61</td><td colspan="2">Not supported*</td></tr><tr><td>LS</td><td>86.84</td><td>47.6</td><td>20.38</td><td>52.09</td><td colspan="5">Not supported</td></tr><tr><td rowspan="3">$\epsilon  = {0.8}$</td><td>PM</td><td>5.1</td><td>7.86</td><td>13.35</td><td>3.71</td><td>11.43</td><td>12.59</td><td>7.58</td><td>9.25</td><td>6.45</td></tr><tr><td>R2T</td><td>76.16</td><td>17.46</td><td>14.56</td><td>9.21</td><td>79.21</td><td>80.03</td><td>79.17</td><td colspan="2">Not supported*</td></tr><tr><td>LS</td><td>77.23</td><td>32.99</td><td>13.28</td><td>31.89</td><td colspan="5">Not supported</td></tr><tr><td rowspan="3">$\epsilon  = 1$</td><td>PM</td><td>5</td><td>7.53</td><td>11.76</td><td>1.92</td><td>10.51</td><td>12.18</td><td>5.02</td><td>8.99</td><td>4.02</td></tr><tr><td>R2T</td><td>61.77</td><td>13.1</td><td>15.63</td><td>7.71</td><td>79.04</td><td>79.97</td><td>79.38</td><td colspan="2">Not supported*</td></tr><tr><td>LS</td><td>84.06</td><td>27.99</td><td>20.19</td><td>14.97</td><td colspan="5">Not supported</td></tr></table>

<table><tbody><tr><td colspan="2">查询类型</td><td colspan="4">计数</td><td colspan="3">求和</td><td colspan="2">分组依据</td></tr><tr><td colspan="2">查询</td><td>${Q}_{c1}$</td><td>${Q}_{c2}$</td><td>${Q}_{c3}$</td><td>${Q}_{c4}$</td><td>${Q}_{s2}$</td><td>${Q}_{s3}$</td><td>${Q}_{s4}$</td><td>Qg2</td><td>${Q}_{g4}$</td></tr><tr><td rowspan="3">$\epsilon  = {0.1}$</td><td>PM</td><td>11.89</td><td>9.46</td><td>19.02</td><td>8.22</td><td>12.07</td><td>16.3</td><td>17.36</td><td>11</td><td>28.63</td></tr><tr><td>R2T</td><td>120.87</td><td>41.51</td><td>29.63</td><td>20.41</td><td>80.61</td><td>80.22</td><td>80.14</td><td colspan="2">不支持*</td></tr><tr><td>LS</td><td>180.9</td><td>73.39</td><td>78.12</td><td>80.44</td><td colspan="5">不支持</td></tr><tr><td rowspan="3">$\epsilon  = {0.2}$</td><td>PM</td><td>11.93</td><td>9.28</td><td>16.48</td><td>5.12</td><td>11.55</td><td>13.07</td><td>12.39</td><td>10.6</td><td>18.8</td></tr><tr><td>R2T</td><td>59.76</td><td>30.38</td><td>19.4</td><td>15.16</td><td>79.91</td><td>80.17</td><td>79.83</td><td colspan="2">不支持*</td></tr><tr><td>LS</td><td>121.68</td><td>61.8</td><td>58.61</td><td>83</td><td colspan="5">不支持</td></tr><tr><td rowspan="3">$\epsilon  = {0.5}$</td><td>PM</td><td>8.66</td><td>7.61</td><td>15.42</td><td>4.3</td><td>11.58</td><td>12.45</td><td>10.43</td><td>9.88</td><td>11.83</td></tr><tr><td>R2T</td><td>84.48</td><td>22.9</td><td>19.67</td><td>11.55</td><td>79.46</td><td>80.08</td><td>79.61</td><td colspan="2">不支持*</td></tr><tr><td>LS</td><td>86.84</td><td>47.6</td><td>20.38</td><td>52.09</td><td colspan="5">不支持</td></tr><tr><td rowspan="3">$\epsilon  = {0.8}$</td><td>PM</td><td>5.1</td><td>7.86</td><td>13.35</td><td>3.71</td><td>11.43</td><td>12.59</td><td>7.58</td><td>9.25</td><td>6.45</td></tr><tr><td>R2T</td><td>76.16</td><td>17.46</td><td>14.56</td><td>9.21</td><td>79.21</td><td>80.03</td><td>79.17</td><td colspan="2">不支持*</td></tr><tr><td>LS</td><td>77.23</td><td>32.99</td><td>13.28</td><td>31.89</td><td colspan="5">不支持</td></tr><tr><td rowspan="3">$\epsilon  = 1$</td><td>PM</td><td>5</td><td>7.53</td><td>11.76</td><td>1.92</td><td>10.51</td><td>12.18</td><td>5.02</td><td>8.99</td><td>4.02</td></tr><tr><td>R2T</td><td>61.77</td><td>13.1</td><td>15.63</td><td>7.71</td><td>79.04</td><td>79.97</td><td>79.38</td><td colspan="2">不支持*</td></tr><tr><td>LS</td><td>84.06</td><td>27.99</td><td>20.19</td><td>14.97</td><td colspan="5">不支持</td></tr></tbody></table>

* It is a future work of [7].

* 这是文献[7]的后续研究方向。

Table 2. Comparison between PM,R2T,TM on $k$ -star queries by varying $\epsilon$ .

表2. 通过变化$\epsilon$对$k$星型查询的PM、R2T、TM方法比较

<table><tr><td colspan="3">Privacy budget</td><td colspan="2">$\epsilon  = {0.1}$</td><td colspan="2">$\epsilon  = {0.5}$</td><td colspan="2">$\epsilon  = 1$</td></tr><tr><td colspan="3">Result type</td><td>Relative error(%)</td><td>Time(s)</td><td>Relative error(%)</td><td>Time(s)</td><td>Relative error(%)</td><td>Time(s)</td></tr><tr><td rowspan="6">Deezer</td><td rowspan="3">${Q}_{2 * }$</td><td>PM</td><td>38.25</td><td>0.14</td><td>35.91</td><td>0.11</td><td>30.53</td><td>0.11</td></tr><tr><td>R2T</td><td>52.45</td><td>15.02</td><td>74.56</td><td>15.03</td><td>63.36</td><td>15.46</td></tr><tr><td>TM</td><td>2431.4</td><td>5.53</td><td>339.55</td><td>5.27</td><td>279.18</td><td>4.9</td></tr><tr><td rowspan="3">${Q}_{3 * }$</td><td>PM</td><td>65.06</td><td>0.84</td><td>58.85</td><td>1.25</td><td>56.67</td><td>1.15</td></tr><tr><td>R2T</td><td colspan="6">Over time limit</td></tr><tr><td>TM</td><td>385.75</td><td>164.05</td><td>306.49</td><td>164.45</td><td>117.3</td><td>160.77</td></tr><tr><td rowspan="6">Amazon</td><td rowspan="3">${Q}_{2 * }$</td><td>PM</td><td>17.67</td><td>0.67</td><td>11.41</td><td>0.60</td><td>7.39</td><td>0.75</td></tr><tr><td>R2T</td><td>23.91</td><td>127.25</td><td>10.63</td><td>131.86</td><td>8.38</td><td>145.39</td></tr><tr><td>TM</td><td>3750.34</td><td>80.4</td><td>482.01</td><td>83.51</td><td>42.03</td><td>76.33</td></tr><tr><td rowspan="3">${Q}_{3 * }$</td><td>PM</td><td>16.25</td><td>4.62</td><td>14.78</td><td>4.70</td><td>7.90</td><td>4.33</td></tr><tr><td>R2T</td><td colspan="6">Over time limit</td></tr><tr><td>TM</td><td colspan="6">Over time limit</td></tr></table>

<table><tbody><tr><td colspan="3">隐私预算(Privacy budget)</td><td colspan="2">$\epsilon  = {0.1}$</td><td colspan="2">$\epsilon  = {0.5}$</td><td colspan="2">$\epsilon  = 1$</td></tr><tr><td colspan="3">结果类型(Result type)</td><td>相对误差(Relative error)(%)</td><td>时间(Time)(秒)</td><td>相对误差(Relative error)(%)</td><td>时间(Time)(秒)</td><td>相对误差(Relative error)(%)</td><td>时间(Time)(秒)</td></tr><tr><td rowspan="6">Deezer</td><td rowspan="3">${Q}_{2 * }$</td><td>PM</td><td>38.25</td><td>0.14</td><td>35.91</td><td>0.11</td><td>30.53</td><td>0.11</td></tr><tr><td>R2T</td><td>52.45</td><td>15.02</td><td>74.56</td><td>15.03</td><td>63.36</td><td>15.46</td></tr><tr><td>TM</td><td>2431.4</td><td>5.53</td><td>339.55</td><td>5.27</td><td>279.18</td><td>4.9</td></tr><tr><td rowspan="3">${Q}_{3 * }$</td><td>PM</td><td>65.06</td><td>0.84</td><td>58.85</td><td>1.25</td><td>56.67</td><td>1.15</td></tr><tr><td>R2T</td><td colspan="6">超时(Over time limit)</td></tr><tr><td>TM</td><td>385.75</td><td>164.05</td><td>306.49</td><td>164.45</td><td>117.3</td><td>160.77</td></tr><tr><td rowspan="6">亚马逊(Amazon)</td><td rowspan="3">${Q}_{2 * }$</td><td>PM</td><td>17.67</td><td>0.67</td><td>11.41</td><td>0.60</td><td>7.39</td><td>0.75</td></tr><tr><td>R2T</td><td>23.91</td><td>127.25</td><td>10.63</td><td>131.86</td><td>8.38</td><td>145.39</td></tr><tr><td>TM</td><td>3750.34</td><td>80.4</td><td>482.01</td><td>83.51</td><td>42.03</td><td>76.33</td></tr><tr><td rowspan="3">${Q}_{3 * }$</td><td>PM</td><td>16.25</td><td>4.62</td><td>14.78</td><td>4.70</td><td>7.90</td><td>4.33</td></tr><tr><td>R2T</td><td colspan="6">超时(Over time limit)</td></tr><tr><td>TM</td><td colspan="6">超时(Over time limit)</td></tr></tbody></table>

<!-- Media -->

## 6 EXPERIMENTS

## 6 实验

We conducted empirical studies to test the performance of our model on a pair of benchmarking datasets. To evaluate the performance in various applications, the empirical study involves not only general star-join queries in OLAP scenarios,but also $k$ -star counting queries in the graph,which is a fundamental task in graph database and representative instance of star-join in specific applications.

我们通过实证研究测试模型在一对基准数据集上的性能。为评估不同应用场景下的表现，该研究不仅涵盖OLAP场景中的通用星型连接查询，还包括图数据库中的$k$-星计数查询——这是图数据库的基础任务，也是特定应用中星型连接的典型实例。

For general star-join queries, we compare with a pair of state-of-the-art DP-compliant query schemes,namely R2T [7] and the local sensitivity-based mechanism (LS) [36]. For $k$ -star counting queries, in line with [7], we compare PM with R2T and naive truncation with smooth sensitivity (TM) [17],which dominates LS in DP-compliant $k$ -star tasks.

针对通用星型连接查询，我们比较了两种最先进的差分隐私兼容查询方案：R2T[7]和基于局部敏感度的机制(LS)[36]。对于$k$-星计数查询，遵循[7]的方法，我们将PM与R2T及采用平滑敏感度的朴素截断法(TM)[17]对比，后者在差分隐私兼容的$k$-星任务中优于LS。

Dataset. To test the performance on general star-join queries, we perform experiments using the Star Schema Benchmark (SSB) [28], a variation of the TPC-H benchmark widely adopted in star-join studies [27, 34]. It changes the snowflake model adopted in TPC-H into a star model. SSB has one fact table and four dimension tables. Each dimension table contains hierarchical attributes, the value of which can be categorized into three types based on the hierarchy: large, medium, and small. For example, the Customer table contains attributes with different domain values of city, region, and address. In a star-join query, the predicate only involves one of them.

数据集。测试通用星型连接查询性能时，我们采用星型模式基准(SSB)[28]——这是TPC-H基准的变体，被广泛用于星型连接研究[27,34]。它将TPC-H采用的雪花模型改为星型模型。SSB包含一个事实表和四个维度表，每个维度表具有分层属性，其值按层级分为大、中、小三类。例如客户表包含城市、地区、地址等不同域值的属性，星型连接查询的谓词仅涉及其中之一。

<!-- Media -->

<!-- figureText: PM WD ${W}_{2}$ 20 15 10 5 0.1 0.2 0.5 0.8 ${Q}_{c3}$ ${Q}_{c4}$ 25 16 20 12 15 8 10 0 0 0.25 0.5 0.75 1 0.25 0.5 0.75 1 0.8 0.8 0.6 0.6 0.4 0.4 0.2 0.2 0 0.25 0.5 0.75 1 0.25 0.5 0.75 Scale Scale ${W}_{1}$ 15 Error Level 10 0.1 0.2 0.5 0.8 Fig. 3. Error level of PM and WD for different $\epsilon$ PM R2T LS ${Q}_{c1}$ ${Q}_{c2}$ 100 60 80 50 Error Level 40 60 30 40 20 20 10 0 0.25 0.5 0.75 0.25 0.5 0.75 1 1 0.8 0.8 Running Time(s) 0.6 0.6 0.4 0.4 0.2 0.2 0 0.25 0.5 0.75 0.25 0.5 0.75 1 Scale Scale -->

<img src="https://cdn.noedgeai.com/01965995-a2c5-7ee2-a7da-5837c049adeb_17.jpg?x=138&y=260&w=1305&h=1000&r=0"/>

Fig. 4. Running times and error level of PM, R2T, LS for different data scales (COUNT).

图4. PM、R2T、LS在不同数据规模(COUNT)下的运行时间与误差水平。

<!-- Media -->

For $k$ -star counting queries,we adopt two real-world network datasets [20],namely Deezer and Amazon. The former collects all friendship relations of users from 3 European countries using the music streaming service Deezer, containing 144,000 nodes (i.e., users) and 847,000 edges (i.e., friendships). The latter is an Amazon co-purchasing network, which contains 335,000 nodes and 926,000 edges. The $k$ -star counting queries predicate refers to its node range,so the domain size is its number of vertices.

对于$k$-星计数查询，我们采用两个真实网络数据集[20]：Deezer和Amazon。前者通过音乐流媒体服务Deezer收集了3个欧洲国家用户的全部好友关系，包含144,000个节点(用户)和847,000条边(好友关系)；后者是亚马逊联合购买网络，含335,000个节点和926,000条边。$k$-星计数查询的谓词指向其节点范围，因此域大小即其顶点数量。

### 6.1 Setup

### 6.1 实验设置

Queries. We test 9 queries out of three standard star-join tasks from SSB, including counting queries $\left\{  {{Q}_{c1},{Q}_{c2},{Q}_{c3},{Q}_{c4}}\right\}$ ,sum queries $\left\{  {{Q}_{s2},{Q}_{s3},{Q}_{s4}}\right\}$ ,and group-by queries $\left\{  {{Q}_{g2},{Q}_{g4}}\right\}$ . As an example, $\left\{  {Q}_{c1}\right\}$ involves a dimension table, $\left\{  {{Q}_{c2},{Q}_{c3}}\right\}$ contains 3 dimension tables,and ${Q}_{c4}$ involves all the dimension tables.

查询。我们从SSB的三个标准星型连接任务中选取9个查询进行测试，包括计数查询$\left\{  {{Q}_{c1},{Q}_{c2},{Q}_{c3},{Q}_{c4}}\right\}$、求和查询$\left\{  {{Q}_{s2},{Q}_{s3},{Q}_{s4}}\right\}$和分组查询$\left\{  {{Q}_{g2},{Q}_{g4}}\right\}$。例如$\left\{  {Q}_{c1}\right\}$涉及单个维度表，$\left\{  {{Q}_{c2},{Q}_{c3}}\right\}$包含3个维度表，${Q}_{c4}$则涉及全部维度表。

For star-join workload queries,we utilize two types of the counting queries, $\left\{  {{W}_{1},{W}_{2}}\right\}  .{W}_{1}$ contains all point constraints for one of three dimension tables. ${W}_{2}$ contains constraints for three dimension tables, one of which is a cumulative distribution (i.e., each query sums the unit counts in a range $\left\lbrack  {1,i}\right\rbrack$ ,where $i$ is in the domain of an attribute). The $\left\{  {{W}_{1},{W}_{2}}\right\}$ are as follows:

针对星型连接工作负载查询，我们使用两类计数查询：$\left\{  {{W}_{1},{W}_{2}}\right\}  .{W}_{1}$包含三个维度表之一的全部点约束；${W}_{2}$包含三个维度表的约束，其中一个是累积分布(即每个查询对$\left\lbrack  {1,i}\right\rbrack$范围内的单位计数求和，$i$属于某属性的域)。$\left\{  {{W}_{1},{W}_{2}}\right\}$具体如下：

$$
{W}_{1} = \left\lbrack  \begin{matrix} 1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\  0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\  0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\  0 & 0 & 1 & 0 & 0 & 0 & 0 & 1 & 0 & 0 \\  0 & 0 & 1 & 0 & 0 & 0 & 1 & 0 & 0 & 0 \\  0 & 0 & 0 & 1 & 0 & 0 & 1 & 0 & 0 & 0 \\  0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 \\  0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 \\  0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 \\  0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 1 & 0 \\  0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\  0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 1 & 0 \\  0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 \\  0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 1 & 0 \\  0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 \\  0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 1 & 0 \\  0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 \\  0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 1 & 0 \\  0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 \\   & & & & & & & & &  \end{matrix}\right\rbrack  .
$$

For $k$ -star counting queries,we test two different tasks: 2-star counting ${Q}_{2 * }$ and 3-star counting ${Q}_{3 * }$ .

对于$k$-星计数查询，我们测试两种任务：2-星计数${Q}_{2 * }$和3-星计数${Q}_{3 * }$。

Evaluation Metrics. Relative error is used as the utility measure and the privacy budget is varied from $\{ {0.1},{0.2},{0.5},{0.8},1\}$ . In addition,we also evaluate the running time for all the compared solutions ${}^{1}$ .

评估指标。采用相对误差作为效用度量，隐私预算从$\{ {0.1},{0.2},{0.5},{0.8},1\}$不等。此外，我们还评估了所有对比方案${}^{1}$的运行时间。

### 6.2 Empirical results

### 6.2 实证结果

In each experiment, we report the average response time of 10 independent runs, each of which is kept within a time limit (i.e., 3 hours).

每个实验中，我们报告10次独立运行的平均响应时间，每次运行均控制在3小时时限内。

Utility. We test the utility of different solutions by varying $\epsilon$ ,and the results are shown in Table 1 and 2, respectively. As the privacy budget increases, the error level gradually decreases as expected. In particular, according to Table 1, both PM and R2T achieve high utility under star-join count queries,while LS achieves poor utility except for very large $\epsilon$ . R2T achieves similar utility as PM on counting queries, but is much worse on sum queries. Table 1 shows that PM achieves order-of-magnitude improvements over R2T and LS in terms of utility. More importantly, PM supports a wider variety of star-join queries than R2T and LS. Remarkably, in all star-join queries, PM consistently achieves errors below ${20}\%$ (even $< {15}\%$ when $\epsilon  \geq  {0.5}$ ).

效用性。我们通过调整$\epsilon$来测试不同解决方案的效用性，结果分别呈现在表1和表2中。随着隐私预算增加，误差水平如预期般逐步降低。特别地，根据表1显示，在星型连接计数查询中PM和R2T都实现了高效用，而LS除极大$\epsilon$值外表现欠佳。在计数查询上R2T与PM效用相近，但在求和查询中远逊于PM。表1表明PM相较R2T和LS实现了数量级的效用提升。更重要的是，PM支持的星型连接查询种类比R2T和LS更广泛。值得注意的是，在所有星型连接查询中，PM始终保持误差低于${20}\%$（当$\epsilon  \geq  {0.5}$时甚至可达$< {15}\%$）。

Obviously, PM performs better on the SSB dataset. This is because the error of PM is proportional to the sum of domains according to our theoretical study in Section 5.4. Therefore, larger dimension tables in star-join queries lead to smaller relative errors. Compared with R2T and LS, PM exhibits much little change by varying $\epsilon$ . In general,the DP-starJ is more stable and accurate than R2T and LS in light of star-join queries. Similarly, Table 2 also justifies the superiority of PM in terms of utility on Deezer and Amazon for $k$ -star queries,offering order-of-magnitude improvements over other methods in different cases. In workload queries, the error level of PM and WD mechanisms are shown in Figure 3. As the figure demonstrates, WD always introduces lower error than PM, especially on ${W}_{1}$ .

显然，PM在SSB数据集上表现更优。根据5.4节理论分析，这是因为PM的误差与域总和成正比。因此星型连接查询中维度表越大，相对误差越小。相较于R2T和LS，PM受$\epsilon$变化的影响微乎其微。总体而言，针对星型连接查询，DP-starJ比R2T和LS更稳定精确。类似地，表2也证实了PM在Deezer和Amazon数据集上对$k$-星查询的效用优势，在不同场景中均实现数量级提升。在工作负载查询中，PM与WD机制的误差水平如图3所示。数据显示WD始终比PM误差更低，尤其在${W}_{1}$场景。

Efficiency. The running time of all mechanisms over the $k$ -star counting queries are shown in Table 2. On Deezer dataset, across all the compared solutions, R2T can only complete within the 3-hour time limit on 3-star queries, although it achieves smaller errors on 2-star queries than TM. Compared with R2T and TM, PM is much faster (at least 40 times faster than TM) as it does not require additional data truncation. Both R2T and TM exceed the time limit on Amazon dataset, which can be attributed to their increased number of joins on this larger-scale dataset. Additionally, R2T needs to solve linear programming problems to determine truncation thresholds and TM involves local sensitivity computation, both of which lead to extra computational overhead. On the other hand, as the running time on SSB does not vary much either across approaches or under different privacy budgets, as have been shown in Figure 4 and 5, we choose not to explicitly report it in Table 1.

效率性。表2展示了所有机制在$k$-星计数查询上的运行时间。在Deezer数据集上，R2T虽在2-星查询中误差小于TM，但仅能在3小时内完成3-星查询。相较R2T和TM，PM因无需数据截断而显著提速（比TM快至少40倍）。在更大规模的Amazon数据集上，R2T和TM均超时，这归因于其增加的连接操作。此外R2T需通过线性规划求解截断阈值，TM涉及局部敏感度计算，均产生额外开销。而SSB数据集上各方法的运行时间（如图4、5所示）受隐私预算影响较小，故未在表1中单独列出。

---

<!-- Footnote -->

${}^{1}$ All the query statements are listed in our report [13]. The src code and all implementation details can be found in https://github.com/ccfu0321/dp-starj.git

${}^{1}$所有查询语句详见报告[13]。源代码及实现细节请访问https://github.com/ccfu0321/dp-starj.git

<!-- Footnote -->

---

<!-- Media -->

<!-- figureText: ${Q}_{s2}$ ${Q}_{s3}$ ${Q}_{S4}$ 100 80 60 40 20 0.5 0.75 1 0.25 0.5 0.75 1 0.8 0.6 0.4 0.2 0.75 0.25 0.5 0.75 Scale Scale 100 80 80 Error Level 60 60 40 40 20 20 0 0 0.25 0.5 0.75 1 0.25 1 0.8 Running Time(s) 0.6 0.6 0.4 0.4 0.2 0.2 0.25 0.5 0.75 0.25 Scale -->

<img src="https://cdn.noedgeai.com/01965995-a2c5-7ee2-a7da-5837c049adeb_19.jpg?x=268&y=266&w=1028&h=595&r=0"/>

Fig. 5. Running times and error level of PM and R2T for different data scales (SUM).

图5. PM与R2T在不同数据规模下的运行时间及误差水平（求和查询）。

<!-- figureText: Uniform Exponential Gamma 80 70 60 50 40 30 0.7 0.1 0.5 0.8 100 60 0.5 0.6 0.7 0.8 0.2 0.4 0.6 0.7 0.8 0.9 Scale Scale 40 30 60 Error Level 50 40 10 0.2 0.3 0.6 0.4 100 100 80 80 Error Level 60 60 20 40 0 20 0.2 0.4 0.5 0.6 0.7 0.9 0.3 Scale -->

<img src="https://cdn.noedgeai.com/01965995-a2c5-7ee2-a7da-5837c049adeb_19.jpg?x=204&y=938&w=1155&h=619&r=0"/>

Fig. 6. Error level of PM,R2T,LS for different distributions on ${Q}_{c3}$ (top) and ${Q}_{s3}$ (bottom) with different data scales.

图6. PM、R2T、LS在${Q}_{c3}$（上）与${Q}_{s3}$（下）不同数据规模下的误差水平对比。

<!-- Media -->

Scalability. In addition, we also test the scalability of the approaches by varying the volume of the database, using SSB with scale factors ranging from 0.25 to 1 . The results are shown in Figure 4 and 5. Obviously, the error of PM barely increases with the data size. The reason is that our error only depends on the domain size of attributes in queries, which does not change much by the scale of SSB data. On the other hand,the behavior of R2T is more complicated. For ${Q}_{c2}$ and ${Q}_{c3}$ ,its error decreases first but then increases later; for ${Q}_{c4}$ ,its error increases first but then decreases later. The reason is that R2T needs to choose the optimal result based on the truncation threshold, which is closely related to the scale of the database instance. The utility of LS linearly increases with the data size as expected. In terms of running time, all mechanisms linearly increase with the data size, among which the increment of PM is smaller. Compared with the basic solutions, DP-starJ shows superior performance in various star-join query types.

可扩展性。我们通过调整SSB数据集规模因子（0.25至1）测试各方法的扩展能力，结果见图4和5。显然PM的误差几乎不随数据量增长，因其仅取决于查询属性的域大小（与SSB数据规模无关）。而R2T表现更复杂：在${Q}_{c2}$和${Q}_{c3}$上误差先降后升，在${Q}_{c4}$上则先升后降。这是因为R2T需根据与数据实例规模密切相关的截断阈值选择最优结果。LS效用如预期般随数据量线性增长。运行时间方面，所有机制均线性增长，其中PM增幅最小。相比基础方案，DP-starJ在各类星型连接查询中均展现出优越性能。

<!-- Media -->

<!-- figureText: 100 $5 \times  {10}^{3}$ ${250} \times  {366}$ ${250} \times  {10}^{3}$ Domain size 80 Error Level 60 40 20 0 $5 \times  7$ 5x366 -->

<img src="https://cdn.noedgeai.com/01965995-a2c5-7ee2-a7da-5837c049adeb_20.jpg?x=592&y=270&w=383&h=287&r=0"/>

Fig. 7. Error level of PM, R2T, LS w.r.t. domain size.

图7. PM、R2T、LS相对于域大小的误差水平。

<!-- figureText: $G{M}_{\left( {1,1}\right)  + \left( {9,1}\right) }$ $G{M}_{\left( {1,2}\right)  + \left( {5,2}\right) }$ $G{M}_{\left( {5,4}\right)  + \left( {9,4}\right) }$ 60 40 20 0.5 0.8 0.1 0.2 0.5 0.8 75 60 45 30 15 0.5 0.8 0.1 0.2 0.5 0.8 1 value of $\epsilon$ value of $\epsilon$ 30 80 60 Error Level 20 40 10 20 0 0.1 0.2 0.5 0.8 1 0.1 0.2 75 75 60 60 Error Level 45 45 30 30 15 15 0 0 0.1 0.2 0.5 0.8 1 0.1 0.2 value of $\epsilon$ -->

<img src="https://cdn.noedgeai.com/01965995-a2c5-7ee2-a7da-5837c049adeb_20.jpg?x=266&y=621&w=1025&h=629&r=0"/>

Fig. 8. Error level of PM,R2T,LS for mixture of Gaussian distributions on ${Q}_{c3}$ (top) and ${Q}_{s3}$ (bottom) with different skewness.

图8. PM、R2T、LS方法在高斯混合分布下的误差水平（不同偏度条件下），上图为${Q}_{c3}$，下图为${Q}_{s3}$。

<!-- figureText: ${Q}_{c1}$ ${Q}_{c2}$ ${Q}_{c3}$ ${Q}_{c4}$ 240 240 180 180 120 120 60 ${10}^{5}$ ${10}^{6}$ ${10}^{7}$ ${10}^{8}$ ${10}^{5}$ ${10}^{6}$ ${10}^{7}$ ${10}^{8}$ $G{S}_{C}$ $G{S}_{O}$ 240 240 180 180 Error Level 120 120 60 60 0 ${10}^{5}$ ${10}^{6}$ ${10}^{7}$ ${10}^{8}$ ${10}^{5}$ ${10}^{6}$ ${10}^{7}$ ${10}^{8}$ ${GS}_{O}$ $G{S}_{Q}$ -->

<img src="https://cdn.noedgeai.com/01965995-a2c5-7ee2-a7da-5837c049adeb_20.jpg?x=146&y=1360&w=1272&h=331&r=0"/>

Fig. 9. Error level of PM, R2T, LS for different $G{S}_{Q}$ .

图9. PM、R2T、LS方法在不同$G{S}_{Q}$下的误差水平。

<!-- Media -->

The impact of Domain size. To further evaluate the impact of domain size on PM, we extended the star-join count query on the SSB dataset, and set up five queries with different domain value combinations involving two dimension tables. The results are shown in Figure 7. Due to the increase in noise with the domain size, the error of the PM will experience a slight increase. When PM perturbs the predicate, its perturbation result is still within the domain value range, which weakens the impact of noise on the results to a certain extent. In addition, the error of PM is still orders of magnitude smaller than R2T and LS.

域值范围的影响。为评估域值范围对PM的影响，我们在SSB数据集上扩展了星型连接计数查询，设置了涉及两个维度表的五种不同域值组合查询。结果如图7所示。由于噪声随域值范围增大而增加，PM的误差会出现轻微上升。当PM对谓词进行扰动时，其扰动结果仍保持在域值范围内，这在一定程度上削弱了噪声对结果的影响。此外，PM的误差仍比R2T和LS小几个数量级。

Different distributions. As shown in Figures 4 and 5, the performance of PM has differences between count and sum queries. In order to further investigate the reasons, we construct data instances following different distributions based on the SSB dataset. The results are shown in Figure 6. Firstly, the PM performs best on Uniform distribution, and the error gradually increases as the data distribution becomes more skewed (e.g., Exponential and Gamma distributions). Secondly, for count queries, the error growth rate is higher. Lastly, with increasing data volume, the error of the PM decreases after an initial increase for sum queries. The main reason for this difference is that the result of sum queries depends on the values of the data itself, while the result of count queries depends on the data distribution. To further justify the impact of skewed data on the PM, extensive experiments are conducted by using data following a mixture of Gaussian distributions with different parameters. The results are shown in Figure 8. It is obvious that PM has a greater impact on count queries on skewed data. This observation partially suggests that count query results are more dependent on the data distribution.

不同分布的影响。如图4和图5所示，PM在计数查询与求和查询中的性能存在差异。为探究原因，我们基于SSB数据集构建了遵循不同分布的数据实例。结果如图6所示：首先，PM在均匀分布上表现最优，随着数据分布偏斜程度增加（如指数分布和伽马分布），误差逐渐增大；其次，计数查询的误差增长率更高；最后，对于求和查询，随着数据量增加，PM误差在初始上升后会下降。这种差异的主要原因是求和查询结果取决于数据本身取值，而计数查询结果取决于数据分布。为验证偏斜数据对PM的影响，我们使用不同参数的高斯混合分布数据进行了大量实验，结果如图8所示。显然PM在偏斜数据上的计数查询受更大影响，这间接说明计数查询结果更依赖数据分布。

<!-- Media -->

<!-- figureText: ${Q}_{tc}$ ${Q}_{ts}$ 100 80 60 40 20 0 0.1 0.5 value of $\epsilon$ 80 60 Error Level 40 20 0 0.1 0.5 1 value of $\epsilon$ -->

<img src="https://cdn.noedgeai.com/01965995-a2c5-7ee2-a7da-5837c049adeb_21.jpg?x=400&y=267&w=759&h=321&r=0"/>

Fig. 10. Error level of PM, R2T, LS on TPC-H queries.

图10. PM、R2T、LS方法在TPC-H查询上的误差水平。

<!-- Media -->

Dependency on $G{S}_{Q}$ . Our last set of experiments examines the effect $G{S}_{Q}$ brings to the utilities of PM, R2T, and LS. We conduct experiments using counting queries with different values $G{S}_{Q}$ . The results are shown in Figure 9. It is observed that PM is insensitive with $G{S}_{Q}$ as $G{S}_{Q}$ of PM is only related to the queries. When $G{S}_{Q}$ increases,the errors of R2T and LS increase rapidly.

$G{S}_{Q}$依赖性。最后实验研究了$G{S}_{Q}$对PM、R2T和LS效用的影响。我们使用不同$G{S}_{Q}$值的计数查询进行实验，结果如图9所示。观察到PM对$G{S}_{Q}$不敏感，因为PM的$G{S}_{Q}$仅与查询相关。当$G{S}_{Q}$增大时，R2T和LS的误差会快速上升。

Evaluation on snowflake query. To illustrate the effect of PM on snowflake queries, we select two queries from the TPC-H benchmark,referred to as ${Q}_{tc}$ and ${Q}_{ts}$ ,which are count and sum queries, respectively. The results are shown in Figure 10, it is observed that PM outperforms both R2T and LS.

雪花查询评估。为说明PM在雪花查询中的效果，我们从TPC-H基准测试中选取${Q}_{tc}$（计数查询）和${Q}_{ts}$（求和查询）两个查询。图10结果显示PM优于R2T和LS。

## 7 CONCLUSIONS

## 7 结论

In this paper, we have presented a novel solution to answer star-join query under differential privacy. We have proposed the definitions of neighboring database instances in different cases of star-join, taking into account the non-trivial number of foreign key constraints. Inspired by the latest output mechanism framework, we have proposed DP-starJ under DP for answering star-join queries, in which we have designed a new mechanism using predicate perturbation to achieve reasonable utility, efficiency, and scalability.

本文提出了一种在差分隐私下回答星型连接查询的新方案。我们针对星型连接的不同情况定义了相邻数据库实例，考虑了非平凡的外键约束数量。受最新输出机制框架启发，提出了DP框架下的DP-starJ方法，通过设计基于谓词扰动的新机制，实现了合理的效用性、高效性和可扩展性。

## ACKNOWLEDGMENTS

## 致谢

This work was supported by the National Natural Science Foundation of China 61972309, 62272369, 62206207 and the Key Technology Innovation Project of Hangzhou (2022AIZD0132).

本研究得到国家自然科学基金（61972309、62272369、62206207）和杭州市关键技术攻关项目（2022AIZD0132）资助。

## REFERENCES

## 参考文献

[1] Myrto Arapinis, Diego Figueira, and Marco Gaboardi. 2016. Sensitivity of counting queries. In International Colloquium on Automata, Languages, and Programming (ICALP).

[2] Boaz Barak, Kamalika Chaudhuri, Cynthia Dwork, Satyen Kale, Frank McSherry, and Kunal Talwar. 2007. Privacy, accuracy, and consistency too: a holistic solution to contingency table release. In Proceedings of the twenty-sixth ACM SIGMOD-SIGACT-SIGART symposium on Principles of database systems. 273-282.

[3] Jaroslaw Blasiok, Mark Bun, Aleksandar Nikolov, and Thomas Steinke. 2019. Towards instance-optimal private query release. In Proceedings of the Thirtieth Annual ACM-SIAM Symposium on Discrete Algorithms. SIAM, 2480-2497.

[4] Kuntai Cai, Xiaokui Xiao, and Graham Cormode. 2023. PrivLava: Synthesizing Relational Data with Foreign Keys under Differential Privacy. arXiv preprint arXiv:2304.04545 (2023).

[5] Graham Cormode, Cecilia Procopiuc, Divesh Srivastava, Entong Shen, and Ting Yu. 2012. Differentially private spatial decompositions. In 2012 IEEE 28th International Conference on Data Engineering. IEEE, 20-31.

[6] Wei-Yen Day, Ninghui Li, and Min Lyu. 2016. Publishing graph degree distribution with node differential privacy. In Proceedings of the 2016 International Conference on Management of Data. 123-138.

[7] Wei Dong, Juanru Fang, Ke Yi, Yuchao Tao, and Ashwin Machanavajjhala. 2022. R2t: Instance-optimal truncation for differentially private query evaluation with foreign keys. In Proceedings of the 2022 International Conference on Management of Data. 759-772.

[8] Wei Dong and Ke Yi. 2021. Residual Sensitivity for Differentially Private Multi-Way Joins. In Proceedings of the 2021 International Conference on Management of Data. 432-444.

[9] Wei Dong and Ke Yi. 2022. A Nearly Instance-optimal Differentially Private Mechanism for Conjunctive Queries. In Proceedings of the 41st ACM SIGMOD-SIGACT-SIGAI Symposium on Principles of Database Systems. 213-225.

[10] Cynthia Dwork. 2006. Differential privacy. In Automata, Languages and Programming: 33rd International Colloquium, ICALP 2006, Venice, Italy, July 10-14, 2006, Proceedings, Part II 33. Springer, 1-12.

[11] Cynthia Dwork, Frank McSherry, Kobbi Nissim, and Adam Smith. 2006. Calibrating noise to sensitivity in private data analysis. In Theory of Cryptography: Third Theory of Cryptography Conference, TCC 2006, New York, NY, USA, March 4-7, 2006. Proceedings 3. Springer, 265-284.

[12] Cynthia Dwork, Aaron Roth, et al. 2014. The algorithmic foundations of differential privacy. Foundations and Trends® in Theoretical Computer Science 9, 3-4 (2014), 211-407.

[13] Congcong Fu, Hui Li, Jian Lou, and Jiangtao Cui. 2023. A Differential Private Scheme towards Analytical Star-Join Queries. Technical Report. http://arxiv.org/abs/2310.04711.

[14] Cesar A Galindo-Legaria, Torsten Grabs, Sreenivas Gukal, Steve Herbert, Aleksandras Surna, Shirley Wang, Wei Yu, Peter Zabback, and Shin Zhang. 2008. Optimizing star join queries for data warehousing in microsoft sql server. In 2008 IEEE 24th International Conference on Data Engineering. IEEE, 1190-1199.

[15] Moritz Hardt, Katrina Ligett, and Frank McSherry. 2012. A simple and practical algorithm for differentially private data release. Advances in neural information processing systems 25 (2012).

[16] Noah Johnson, Joseph P Near, and Dawn Song. 2018. Towards practical differential privacy for SQL queries. Proceedings of the VLDB Endowment 11, 5 (2018), 526-539.

[17] Shiva Prasad Kasiviswanathan, Kobbi Nissim, Sofya Raskhodnikova, and Adam D Smith. 2013. Analyzing Graphs with Node Differential Privacy.. In TCC, Vol. 13. Springer, 457-476.

[18] Fumiyuki Kato, Tsubasa Takahashi, Shun Takagi, Yang Cao, Seng Pei Liew, and Masatoshi Yoshikawa. 2022. HD-PView: Differentially Private Materialized View for Exploring High Dimensional Relational Data. arXiv preprint arXiv:2203.06791 (2022).

[19] Ios Kotsogiannis, Yuchao Tao, Xi He, Maryam Fanaeepour, Ashwin Machanavajjhala, Michael Hay, and Gerome Miklau. 2019. Privatesql: a differentially private sql query engine. Proceedings of the VLDB Endowment 12, 11 (2019), 1371-1384.

[20] Jure Leskovec and Andrej Krevl. 2016. SNAP datasets: Stanford large network dataset collection(2014). http: //snap.stanford.edu/data

[21] Chao Li, Gerome Miklau, Michael Hay, Andrew McGregor, and Vibhor Rastogi. 2015. The matrix mechanism: optimizing linear counting queries under differential privacy. The VLDB journal 24 (2015), 757-781.

[22] Seng Pei Liew, Tsubasa Takahashi, Shun Takagi, Fumiyuki Kato, Yang Cao, and Masatoshi Yoshikawa. 2022. Network shuffling: Privacy amplification via random walks. In Proceedings of the 2022 International Conference on Management of Data. 773-787.

[23] Frank D McSherry. 2009. Privacy integrated queries: an extensible platform for privacy-preserving data analysis. In Proceedings of the 2009 ACM SIGMOD International Conference on Management of data. 19-30.

[24] Arjun Narayan and Andreas Haeberlen. 2012. DJoin: Differentially private join queries over distributed databases. In Presented as part of the 10th \{USENIX\} Symposium on Operating Systems Design and Implementation (\{OSDI\} 12). 149-162.

[25] Aleksandar Nikolov, Kunal Talwar, and Li Zhang. 2013. The geometry of differential privacy: the sparse and approximate cases. In Proceedings of the forty-fifth annual ACM symposium on Theory of computing. 351-360.

[26] Kobbi Nissim, Sofya Raskhodnikova, and Adam Smith. 2007. Smooth sensitivity and sampling in private data analysis. In Proceedings of the thirty-ninth annual ACM symposium on Theory of computing. 75-84.

[27] Patrick O'Neil, Elizabeth O'Neil, Xuedong Chen, and Stephen Revilak. 2009. The star schema benchmark and augmented fact table indexing. In Performance Evaluation and Benchmarking: First TPC Technology Conference, TPCTC 2009, Lyon, France, August 24-28, 2009, Revised Selected Papers 1. Springer, 237-252.

[28] Patrick E O’Neil, Elizabeth J O’Neil, and Xuedong Chen. 2007. The star schema benchmark (SSB). Pat 200, 0 (2007), 50.

[29] Catuscia Palamidessi and Marco Stronati. 2012. Differential privacy for relational algebra: Improving the sensitivity bounds via constraint systems. arXiv preprint arXiv:1207.0872 (2012).

[30] Davide Proserpio, Sharon Goldberg, and Frank McSherry. 2012. Calibrating data to sensitivity in private data analysis. arXiv preprint arXiv:1203.3453 (2012).

[31] Wahbeh Qardaji, Weining Yang, and Ninghui Li. 2013. Understanding hierarchical methods for differentially private histograms. Proceedings of the VLDB Endowment 6, 14 (2013), 1954-1965.

[32] Wahbeh Qardaji, Weining Yang, and Ninghui Li. 2014. Priview: practical differentially private release of marginal contingency tables. In Proceedings of the 2014 ACM SIGMOD international conference on Management of data. 1435-1446.

[33] Uwe Röhm, Klemens Böhm, and Hans-Jörg Schek. 2000. OLAP query routing and physical design in a database cluster. In Advances in Database Technology-EDBT 2000: 7th International Conference on Extending Database Technology Konstanz, Germany, March 27-31, 2000 Proceedings 7. Springer, 254-268.

[34] Jimi Sanchez. 2016. A review of star schema benchmark. arXiv preprint arXiv:1606.00295 (2016).

[35] Shun Takagi, Tsubasa Takahashi, Yang Cao, and Masatoshi Yoshikawa. 2021. P3GM: Private high-dimensional data release via privacy preserving phased generative model. In 2021 IEEE 37th International Conference on Data Engineering (ICDE). IEEE, 169-180.

[36] Yuchao Tao, Xi He, Ashwin Machanavajjhala, and Sudeepa Roy. 2020. Computing local sensitivities of counting queries with joins. In Proceedings of the 2020 ACM SIGMOD International Conference on Management of Data. 479-494.

[37] Tianhao Wang, Milan Lopuhaä-Zwakenberg, Zitao Li, Boris Skoric, and Ninghui Li. 2019. Locally differentially private frequency estimation with consistency. arXiv preprint arXiv:1905.08320 (2019).

[38] Royce J Wilson, Celia Yuxin Zhang, William Lam, Damien Desfontaines, Daniel Simmons-Marengo, and Bryant Gipson. 2020. Differentially private SQL with bounded user contribution. Proceedings on privacy enhancing technologies 2020, 2 (2020), 230-250.

[39] Xiaokui Xiao, Guozhang Wang, and Johannes Gehrke. 2010. Differential privacy via wavelet transforms. IEEE Transactions on knowledge and data engineering 23, 8 (2010), 1200-1214.

[40] Jia Xu, Zhenjie Zhang, Xiaokui Xiao, Yin Yang, Ge Yu, and Marianne Winslett. 2013. Differentially private histogram publication. The VLDB journal 22 (2013), 797-822.

[41] Ganzhao Yuan, Zhenjie Zhang, Marianne Winslett, Xiaokui Xiao, Yin Yang, and Zhifeng Hao. 2015. Optimizing batch linear queries under exact and approximate differential privacy. ACM Transactions on Database Systems (TODS) 40,2 (2015), 1-47.

[42] Sepanta Zeighami, Ritesh Ahuja, Gabriel Ghinita, and Cyrus Shahabi. 2021. A neural database for differentially private spatial range queries. arXiv preprint arXiv:2108.01496 (2021).

[43] Xiaojian Zhang, Rui Chen, Jianliang Xu, Xiaofeng Meng, and Yingtao Xie. 2014. Towards accurate histogram publication under differential privacy. In Proceedings of the 2014 SIAM international conference on data mining. SIAM, 587-595.

[44] Shuyuan Zheng, Yang Cao, and Masatoshi Yoshikawa. 2022. Secure Shapley Value for Cross-Silo Federated Learning. arXiv preprint arXiv:2209.04856 (2022).