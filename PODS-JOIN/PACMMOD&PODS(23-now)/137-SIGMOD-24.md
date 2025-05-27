# Towards a Converged Relational-Graph Optimization Framework

迈向融合式关系-图查询优化框架

YUNKAI LOU, Alibaba Group, China

娄云凯，阿里巴巴集团，中国

LONGBIN LAI, Alibaba Group, China

来龙斌，阿里巴巴集团，中国

BINGQING LYU, Alibaba Group, China

吕冰清，阿里巴巴集团，中国

YUFAN YANG, Alibaba Group, China

杨雨凡，阿里巴巴集团，中国

XIAOLI ZHOU, Alibaba Group, China

周晓丽，阿里巴巴集团，中国

WENYUAN YU, Alibaba Group, China

余文渊，阿里巴巴集团，中国

YING ZHANG, Zhejiang Gongshang University, China

张颖，浙江工商大学，中国

JINGREN ZHOU, Alibaba Group, China

周靖人，阿里巴巴集团，中国

The recent ISO SQL:2023 standard adopts SQL/PGQ (Property Graph Queries), facilitating graph-like querying within relational databases. This advancement, however, underscores a significant gap in how to effectively optimize SQL/PGQ queries within relational database systems. To address this gap, we extend the foundational SPJ (Select-Project-Join) queries to SPJM queries, which include an additional matching operator for representing graph pattern matching in SQL/PGQ. Although SPJM queries can be converted to SPJ queries and optimized using existing relational query optimizers, our analysis shows that such a graph-agnostic method fails to benefit from graph-specific optimization techniques found in the literature. To address this issue, we develop a converged relational-graph optimization framework called RelGo for optimizing SPJM queries, leveraging joint efforts from both relational and graph query optimizations. Using DuckDB as the underlying relational execution engine, our experiments show that RelGo can generate efficient execution plans for SPJM queries. On well-established benchmarks,these plans exhibit an average speedup of ${21.90} \times$ compared to those produced by the graph-agnostic optimizer.

最新ISO SQL:2023标准采纳了SQL/PGQ（属性图查询），使得在关系型数据库中进行图式查询成为可能。然而这一进展凸显出关系数据库系统在有效优化SQL/PGQ查询方面存在显著空白。为解决这一问题，我们将基础SPJ（选择-投影-连接）查询扩展为SPJM查询，新增了用于表示SQL/PGQ中图模式匹配的匹配运算符。虽然SPJM查询可转换为SPJ查询并通过现有关系查询优化器进行优化，但分析表明这种无视图特性的方法无法利用文献中的图专属优化技术。为此，我们开发了名为RelGo的融合式关系-图优化框架，通过关系与图查询优化的协同作用来优化SPJM查询。基于DuckDB关系执行引擎的实验表明，RelGo能为SPJM查询生成高效执行计划。在成熟基准测试中，这些计划相比无视图特性的优化器产出方案平均加速达${21.90} \times$倍。

## CCS Concepts: - Information systems $\rightarrow$ Database query processing; Relational database model; Network data models.

## CCS概念：- 信息系统$\rightarrow$数据库查询处理；关系数据库模型；网络数据模型

Additional Key Words and Phrases: query optimization, converged optimization framework, SPJM queries, SQL/PGQ, graph-aware

附加关键词：查询优化，融合优化框架，SPJM查询，SQL/PGQ，图感知

## ACM Reference Format:

## ACM参考文献格式：

Yunkai Lou, Longbin Lai, Bingqing Lyu, Yufan Yang, Xiaoli Zhou, Wenyuan Yu, Ying Zhang, and Jingren Zhou. 2024. Towards a Converged Relational-Graph Optimization Framework. Proc. ACM Manag. Data 2, 6 (SIGMOD), Article 252 (December 2024), 27 pages. https://doi.org/10.1145/3698828

娄云凯、来龙斌、吕冰清、杨雨凡、周晓丽、余文渊、张颖、周靖人。2024。迈向融合式关系-图查询优化框架。《ACM数据管理汇刊》2卷6期(SIGMOD)，文章252（2024年12月），27页。https://doi.org/10.1145/3698828

Authors' Contact Information: Yunkai Lou, Alibaba Group, Hangzhou, China, louyunkai.lyk@alibaba-inc.com; Longbin Lai, Alibaba Group, Hangzhou, China, longbin.lailb@alibaba-inc.com; Bingqing Lyu, Alibaba Group, Hangzhou, China, bingqing.lbq@alibaba-inc.com; Yufan Yang, Alibaba Group, Hangzhou, China, xiaofan.yyf@alibaba-inc.com; Xiaoli Zhou, Alibaba Group, Hangzhou, China, yihe.zxl@alibaba-inc.com; Wenyuan Yu, Alibaba Group, Hangzhou, China, wenyuan. ywy@alibaba-inc.com; Ying Zhang, Zhejiang Gongshang University, Hangzhou, China, ying.zhang@zjgsu.edu.cn; Jingren Zhou, Alibaba Group, Hangzhou, China, jingren.zhou@alibaba-inc.com.

作者联系方式：娄云凯，阿里巴巴集团，杭州，中国，louyunkai.lyk@alibaba-inc.com；来龙斌，阿里巴巴集团，杭州，中国，longbin.lailb@alibaba-inc.com；吕冰清，阿里巴巴集团，杭州，中国，bingqing.lbq@alibaba-inc.com；杨雨凡，阿里巴巴集团，杭州，中国，xiaofan.yyf@alibaba-inc.com；周晓丽，阿里巴巴集团，杭州，中国，yihe.zxl@alibaba-inc.com；余文渊，阿里巴巴集团，杭州，中国，wenyuan.ywy@alibaba-inc.com；张颖，浙江工商大学，杭州，中国，ying.zhang@zjgsu.edu.cn；周靖人，阿里巴巴集团，杭州，中国，jingren.zhou@alibaba-inc.com。

Permission to make digital or hard copies of all or part of this work for personal or classroom use is granted without fee provided that copies are not made or distributed for profit or commercial advantage and that copies bear this notice and the full citation on the first page. Copyrights for components of this work owned by others than the author(s) must be honored. Abstracting with credit is permitted. To copy otherwise, or republish, to post on servers or to redistribute to lists, requires prior specific permission and/or a fee. Request permissions from permissions@acm.org. © 2024 Copyright held by the owner/author(s). Publication rights licensed to ACM. ACM 2836-6573/2024/12-ART252 https://doi.org/10.1145/3698828

允许出于个人或课堂教学目的无偿制作本作品全部或部分的数字或纸质副本，前提是副本不得用于盈利或商业用途，且须在首页保留本声明及完整引用。必须尊重非作者所有的作品组成部分的版权。允许在注明出处的情况下进行文摘转载。其他形式的复制、再版、服务器发布或重新分发，需事先获得特定许可和/或支付费用。请向permissions@acm.org申请许可。©2024版权归所有者/作者所有。出版权由ACM独家授权。ACM 2836-6573/2024/12-ART252 https://doi.org/10.1145/3698828

## 1 Introduction

## 1 引言

In the realms of data management and analytics, relational databases have long been the bedrock of structured data storage and retrieval, empowering a plethora of applications. The ubiquity of these databases has been supported by the advent of Structured Query Language (SQL) [9], a standardized language that has been adopted widely by various relational database management systems for managing data through schema-based operations.

在数据管理与分析领域，关系型数据库长期作为结构化数据存储与检索的基石，支撑着无数应用场景。结构化查询语言（SQL）[9]的出现进一步巩固了这一地位，这种标准化语言已被各类关系型数据库管理系统广泛采用，通过基于模式的操作来管理数据。

Despite its considerable success and broad adoption, SQL has its limitations, particularly when it comes to representing and querying intricately linked data. Consider, for instance, the relational tables of Person and Knows, the latter symbolizing a many-to-many relationship between instances of the former. Constructing a SQL query to retrieve a group of four persons who are all mutually acquainted is not a straightforward endeavor, potentially leading to a cumbersome and complex SQL expression.

尽管SQL取得了显著成功并被广泛采用，但其在表示和查询复杂关联数据时仍存在局限。以"Person"（人员）和"Knows"（认识）这两个具有多对多关系的表为例，若要通过SQL查询找出四个彼此相识的人员群体，不仅难以直观实现，往往还会生成冗长复杂的SQL表达式。

In comparison, such a scenario could be succinctly addressed using graph query languages such as Cypher [3], where queries are expressed as graph pattern matching. This discrepancy between the relational and graph querying paradigms has given rise to the innovative SQL/Property Graph Queries (SQL/PGQ), an extension formally adopted in the ISO SQL:2023 standard [40]. SQL/PGQ is designed to amalgamate the extensive capabilities of SQL with the inherent benefits of graph pattern matching. With SQL/PGQ, it is now possible to define and query graphs within SQL expressions, transforming otherwise complex relational queries - characterized by multiple joins - into simpler and more intuitive graph queries.

相比之下，使用Cypher[3]等图查询语言可以简洁地处理此类场景——这类语言将查询表述为图模式匹配。正是关系型与图查询范式间的这种差异，催生了创新的SQL/属性图查询（SQL/PGQ），该扩展已正式纳入ISO SQL:2023标准[40]。SQL/PGQ旨在融合SQL的强大功能与图模式匹配的天然优势，使得开发者可以直接在SQL表达式中定义和查询图结构，将原本需要多重连接操作的复杂关系查询，转化为更简单直观的图查询。

<!-- Media -->

---

SELECT p2_name, place.name

SELECT p2_name, place.name

FROM GRAPH_TABLE (G

FROM GRAPH_TABLE (G

	MATCH

		(p1:Person)-[:Likes]->(m:Message),

		(p1:Person)-[:Likes]->(m:Message),

		(p2:Person)-[:Likes]->(m),

		(p2:Person)-[:Likes]->(m),

		(p1)-[:Knows]->(p2)

		(p1)-[:Knows]->(p2)

	COLUMNS(   )

	COLUMNS(   )

		p1.name AS p1_name,

		p1.name AS p1_name,

		p1.place_id AS p1_place_id,

		p1.place_id AS p1_place_id,

		p2.name AS p2_name

		p2.name AS p2_name

	)

) g

JOIN Place p ON g.p1_place_id = p.id

JOIN Place p ON g.p1_place_id = p.id

WHERE g.p1_name = 'Tom';

WHERE g.p1_name = 'Tom';

		Fig. 1. An example of SQL/PGQ query.

		图1. SQL/PGQ查询示例

---

<!-- Media -->

Example 1. Consider the four relational tables in the database: Person(id, name, place_id), Message(id, content, date),Like(p_id, m_id, date), and Place(id, name). Using SQL/PGQ, a property graph $G$ is articulated as a GRAPH_TABLE,established on the basis of the first three tables. In this mapping, rows from Person and Message are interpreted as vertices with labels "Person" and "Message" respectively, while rows from Like represent edges with the label "Likes". This mapping process will be elaborated as RGMapping in Sec. 2.1. An SQL/PGQ query to discover the friends of a person named "Tom" and the place they live in, where "Tom" and friends share an affinity for the same message, can be formulated as shown in Fig. 1. In graph G, a GRAPH PATTERN MATCHING is employed to decode the intricate relationships between persons and messages. Upon executing the pattern matching, a COLUMNS clause projects the results into a tabular format, enumerating essential attributes. Then the RELATIONAL JOIN is performed on resultant table $\mathrm{g}$ and Place table to obtain the place's name.

示例1. 考虑数据库中的四个关系表：人员表（含id、name、place_id字段）、消息表（含id、content、date字段）、点赞表（含p_id、m_id、date字段）以及地点表（含id、name字段）。使用SQL/PGQ时，属性图$G$被表述为基于前三个表构建的GRAPH_TABLE。在此映射中，人员表和消息表的行分别被解释为带有"人员"和"消息"标签的顶点，而点赞表的行则代表带有"喜欢"标签的边。该映射过程将在第2.1节作为RGMapping详述。图1展示了用SQL/PGQ查询查找名为"Tom"的人员及其居住地（要求"Tom"与朋友对同条消息存在点赞关系）的语句。在图G中，通过GRAPH PATTERN MATCHING技术解析人员与消息间的复杂关系。模式匹配执行后，COLUMNS子句将结果投射为表格形式并枚举关键属性。随后对结果表$\mathrm{g}$与地点表执行RELATIONAL JOIN操作以获取地点名称。

<!-- Media -->

Table 1. Frequently used notations.

表1. 常用符号说明

<table><tr><td>Notation</td><td>Definition</td></tr><tr><td>$R$</td><td>a relation or relational table</td></tr><tr><td>$\tau$ and $\tau$ .attr</td><td>a tuple in a relation,and the value of an attribute of $\tau$</td></tr><tr><td>$G\left( {V,E}\right)$</td><td>a property graph with $V$ and $E$</td></tr><tr><td>$\mathcal{P}\left( {V,E}\right)$</td><td>a pattern graph with $V$ and $E$</td></tr><tr><td>$\operatorname{id}\left( \epsilon \right) ,\ell \left( \epsilon \right) ,\epsilon$ .attr</td><td>the identifier,label,and the value of given attribute of a graph element $\epsilon$</td></tr><tr><td>$\mathcal{N}\left( u\right)$ and ${\mathcal{N}}^{E}\left( u\right)$</td><td>neighbors and adjacent edges of $u$</td></tr><tr><td>${GR}$</td><td>a graph relation</td></tr><tr><td>$\mathcal{M}\left( {{GR},\mathcal{P}}\right) ,\mathcal{M}\left( \mathcal{P}\right)$</td><td>matching $\mathcal{P}$ on a graph relation ${GR}$ or a graph $G$</td></tr><tr><td>${\pi }_{A},{\sigma }_{\Psi }, \bowtie$</td><td>projection, selection, and join operators over relations</td></tr><tr><td>${\widehat{\pi }}_{A * },\widehat{ \bowtie  }$</td><td>projection and join operators over graph relations</td></tr><tr><td>${\lambda }_{\ell }^{s}\left( e\right) ,{\lambda }_{\ell }^{t}\left( e\right)$</td><td>the total functions for mapping tuples in an edge relation to source and target vertex relations</td></tr></table>

<table><tbody><tr><td>符号表示</td><td>定义</td></tr><tr><td>$R$</td><td>关系或关系表</td></tr><tr><td>$\tau$ 和 $\tau$ .attr</td><td>关系中的元组，以及$\tau$属性的值</td></tr><tr><td>$G\left( {V,E}\right)$</td><td>具有$V$和$E$的属性图</td></tr><tr><td>$\mathcal{P}\left( {V,E}\right)$</td><td>具有$V$和$E$的模式图</td></tr><tr><td>$\operatorname{id}\left( \epsilon \right) ,\ell \left( \epsilon \right) ,\epsilon$ .attr</td><td>图元素$\epsilon$的标识符、标签及给定属性值</td></tr><tr><td>$\mathcal{N}\left( u\right)$ 和 ${\mathcal{N}}^{E}\left( u\right)$</td><td>$u$的邻居节点与相邻边</td></tr><tr><td>${GR}$</td><td>图关系</td></tr><tr><td>$\mathcal{M}\left( {{GR},\mathcal{P}}\right) ,\mathcal{M}\left( \mathcal{P}\right)$</td><td>在图关系${GR}$或图$G$上匹配$\mathcal{P}$</td></tr><tr><td>${\pi }_{A},{\sigma }_{\Psi }, \bowtie$</td><td>关系上的投影、选择与连接操作符</td></tr><tr><td>${\widehat{\pi }}_{A * },\widehat{ \bowtie  }$</td><td>图关系上的投影与连接操作符</td></tr><tr><td>${\lambda }_{\ell }^{s}\left( e\right) ,{\lambda }_{\ell }^{t}\left( e\right)$</td><td>将边关系中的元组映射到源顶点关系和目标顶点关系的全函数</td></tr></tbody></table>

<!-- Media -->

The SQL/PGQ standardization, while a significant leap forward in the realm of relational databases, primarily addresses language constructs. A discernible gap exists in the theoretical landscape, particularly in analyzing, transforming, and optimizing SQL/PGQ queries with hybrid relational and graph semantics.

SQL/PGQ标准化虽在关系型数据库领域实现了重大飞跃，但主要解决的是语言构造问题。理论层面仍存在明显空白，特别是在分析、转换和优化兼具关系与图语义的SQL/PGQ查询方面。

Relational query optimization has historically leaned on the SPJ (selection-projection-join) skeleton $\left\lbrack  {{11},{43}}\right\rbrack$ ,which provides a systematic approach for analyzing query complexity $\left\lbrack  {{10},{21}}\right\rbrack$ , devising heuristic optimization rules $\left\lbrack  {{12},{16}}\right\rbrack$ ,and computing optimal join order $\left\lbrack  {{13},{18}}\right\rbrack$ . Recently, graph techniques have been introduced to optimize relational queries $\left\lbrack  {{18},{23},{32},{33}}\right\rbrack$ . In particular, GRainDB [23] introduced a predefined join operator that materializes the adjacency list (rows) of vertices, enabling more efficient join execution. While these techniques can be empowered by graph techniques, they target purely relational query rather than the relational-graph hybrid query of SQL/PGQ.

关系型查询优化历来依赖于SPJ（选择-投影-连接）框架$\left\lbrack  {{11},{43}}\right\rbrack$，该框架为分析查询复杂度$\left\lbrack  {{10},{21}}\right\rbrack$、设计启发式优化规则$\left\lbrack  {{12},{16}}\right\rbrack$及计算最优连接顺序$\left\lbrack  {{13},{18}}\right\rbrack$提供了系统方法。近年来，图技术被引入以优化关系型查询$\left\lbrack  {{18},{23},{32},{33}}\right\rbrack$。其中GRainDB[23]提出了预定义连接算子，通过物化顶点的邻接表（行）实现更高效的连接执行。尽管这些技术可借助图技术增强，但其目标仍是纯关系型查询，而非SQL/PGQ的关系-图混合查询。

In parallel to relational query optimization, significant strides have been made in optimizing graph pattern matching. A common practice is to leverage join-based techniques to optimize the query $\left\lbrack  {5,{27},{28},{51}}\right\rbrack$ . Scalable join algorithms,such as binary-join [27],worst-case optimal (abbr. wco) join [5], and their hybrid variants [29, 36, 51], have been proposed for solving the problem over large-scale graphs. However, despite the effectiveness of these techniques for pattern matching on graphs, they cannot be directly applied to relational databases due to the inherent differences in data models.

与关系型查询优化并行，图模式匹配优化也取得显著进展。常见做法是采用基于连接的技术优化查询$\left\lbrack  {5,{27},{28},{51}}\right\rbrack$。针对大规模图处理，已提出可扩展连接算法如二元连接[27]、最坏情况最优（简称wco）连接[5]及其混合变体[29,36,51]。然而，尽管这些技术对图模式匹配有效，但由于数据模型固有差异，无法直接应用于关系型数据库。

In this paper, we propose the first converged optimization framework, RelGo, that optimizes relational-graph hybrid queries in a relational database, in response to the advent of SQL/PGQ. A straightforward implementation $\left\lbrack  {1,{47},{48}}\right\rbrack$ can involve directly transforming the graph component in SQL/PGQ queries into relational operations, allowing the entire query to be optimized and executed in any existing relational engine. While we contribute to building the theory to make such a transformation workable, this graph-agnostic optimization approach suffers from several issues, including graph-unaware join orders, suboptimal join plans, and increased search space, as will be discussed in Sec. 3.1.2.

本文提出首个融合优化框架RelGo，以响应SQL/PGQ的出现，优化关系型数据库中的关系-图混合查询。基础实现$\left\lbrack  {1,{47},{48}}\right\rbrack$可将SQL/PGQ查询中的图组件直接转换为关系操作，使整个查询能在现有关系引擎中优化执行。尽管我们构建了实现该转换的理论基础，但这种与图无关的优化方法存在诸多问题，包括忽视图特性的连接顺序、次优连接计划及增大的搜索空间（详见3.1.2节）。

To address these challenges, RelGo is proposed to leverage the strengths of both relational and graph query optimization techniques. Building upon the foundation of SPJ queries, we introduce the SPJM query skeleton, which extends SPJ with a matching operator to represent graph queries. We adapt state-of-the-art graph optimization techniques, such as the decomposition method [51] and the cost-based optimizer [29], to the relational context, effectively producing worst-case optimal graph subplans for the matching operator. To facilitate efficient execution of the matching operator, we introduce graph index inspired by GRainDB's predefined join [23], based on which graph-based physical operations are implemented. The relational part of the query, together with the optimized graph subplans encapsulated within a special operator called SCAN_GRAPH_TABLE, is then optimized using standard relational optimizers. Finally, we incorporate heuristic rules, such as FilterIntoMatchRule, to handle cases unique to SPJM queries that involve the interplay between relational and graph components.

为解决这些挑战，RelGo融合了关系型与图查询优化技术的优势。在SPJ查询基础上引入SPJM框架，通过匹配算子扩展SPJ以表示图查询。我们将前沿图优化技术（如分解法[51]和基于代价的优化器[29]）适配到关系型场景，为匹配算子生成最坏情况最优图子计划。为实现匹配算子高效执行，受GRainDB预定义连接[23]启发设计图索引，并基于此实现图物理操作。查询的关系部分与封装在SCAN_GRAPH_TABLE特殊算子中的优化图子计划，将通过标准关系优化器进行优化。最后引入FilterIntoMatchRule等启发式规则，处理SPJM查询中关系与图组件交互的特有场景。

We have made the following contributions in this paper:

本文主要贡献如下：

(1) We map relational data models to property graph models as specified by SQL/PGQ using RGMapping. Based on RGMapping, we introduce a new query skeleton called SPJM, which is designed to better analyze relational-graph hybrid queries.

(1) 通过RGMapping将关系数据模型映射至SQL/PGQ定义的属性图模型。基于RGMapping提出SPJM新查询框架，专为分析关系-图混合查询设计。

(Sec. 2)

(2) We construct the theory for transforming any SPJM query into an SPJ query. Such a graph-agnostic approach enables existing relational databases to handle SPJM queries without low-level modifications. We also formally prove that the search space of the graph-agnostic approach can be exponentially larger than our solution.

(2) 构建将任意SPJM查询转换为SPJ查询的理论体系。这种与图无关的方法使现有关系型数据库无需底层修改即可处理SPJM查询。我们严格证明该方法搜索空间可能呈指数级大于本方案。

(Sec. 3)

(3) We introduce RelGo, a converged optimization framework that leverages the strengths of both relational and graph query optimization techniques to optimize SPJM queries. RelGo adapts state-of-the-art graph optimization techniques to the relational context, and implements graph-based physical operations based on graph index for efficient query execution. (Sec. 4)

(3) 提出融合优化框架RelGo，结合关系型与图查询优化技术优势优化SPJM查询。RelGo将前沿图优化技术适配至关系型场景，基于图索引实现图物理操作以高效执行查询。（第4节）

(4) We develop RelGo by integrating it with the industrial relational optimization framework, Calcite [17], and employing DuckDB [2] for execution runtime. We conducted extensive experiments to evaluate its performance. The results on the LDBC Social Network Benchmark [30] indicate that RelGo significantly surpasses the performance of the graph-agnostic baseline, with an average speedup of ${21.9} \times$ ,and ${5.4} \times$ even after graph index is enabled for the baseline. (Sec. 5)

(4) 我们通过将RelGo与工业级关系优化框架Calcite[17]集成，并采用DuckDB[2]作为执行运行时，开发了该系统。通过大量实验评估其性能，LDBC社交网络基准测试[30]结果表明，RelGo显著超越无视图结构的基线方案，平均加速比达到${21.9} \times$，即便基线方案启用图索引后仍保持${5.4} \times$的优势。（见第5节）

This paper is organized in the order of the contributions. We survey related work in Sec. 6 and conclude the paper in Sec. 7.

本文按贡献顺序组织内容：第6节综述相关工作，第7节总结全文。

## 2 Preliminaries

## 2 基础知识

In this section, we propose the utilized data model and define the SPJM query processed in this paper. Frequently used notations in this paper are summarized in Table 1.

本节提出采用的数据模型，并定义本文处理的SPJM查询。常用符号见表1汇总。

### 2.1 Data Model

### 2.1 数据模型

A schema,denoted as $S = \left( {{a}_{1},{a}_{2},\ldots ,{a}_{n}}\right)$ ,is a collection of attributes. Each attribute ${a}_{i}$ is associated with a specific data domain ${D}_{i}$ ,which defines the set of permissible values that ${a}_{i}$ can take. A relation $R$ is defined as a set of tuples. We consider $R$ to be a relation over schema $S$ ,if and only if, every tuple $\tau  = \left( {{d}_{1},{d}_{2},\ldots ,{d}_{n}}\right)$ in $R$ adheres to the schema’s constraints,such that the value ${d}_{i}$ for each position in the tuple corresponds to the data domain ${D}_{i}$ of the attribute ${a}_{i}$ in $S$ . In other words, each value ${d}_{i}$ in a tuple $\tau$ is drawn from the appropriate data domain ${D}_{i}$ for its corresponding attribute ${a}_{i}$ . Moreover,for any tuple $\tau$ in the relation $R$ ,the notation $\tau .{a}_{i} = {d}_{i}$ signifies that the attribute ${a}_{i}$ in tuple $\tau$ has value ${d}_{i}$ . A table is a representation of a relation with rows corresponds to tuples in the relation, and columns represent attributes in the schema. In this paper, we use the terms of relation and table interchangeably.

模式（schema）$S = \left( {{a}_{1},{a}_{2},\ldots ,{a}_{n}}\right)$是属性的集合。每个属性${a}_{i}$关联特定数据域${D}_{i}$，该域定义了${a}_{i}$可取的合法值集合。关系$R$定义为元组的集合，当且仅当每个元组$\tau  = \left( {{d}_{1},{d}_{2},\ldots ,{d}_{n}}\right)$都遵循模式约束——即元组中每个位置的值${d}_{i}$对应于模式$S$中属性${a}_{i}$的数据域${D}_{i}$时，称$R$是模式$S$上的关系。换言之，元组$\tau$中的每个值${d}_{i}$都取自其对应属性${a}_{i}$的适当数据域${D}_{i}$。此外，对于关系$R$中的任意元组$\tau$，记号$\tau .{a}_{i} = {d}_{i}$表示该元组中属性${a}_{i}$的值为${d}_{i}$。表是关系的表现形式，行对应关系中的元组，列表示模式中的属性。本文中关系与表两术语可互换使用。

We define a Property Graph as $G = \left( {{V}_{G},{E}_{G}}\right)$ ,where $V$ stands for the set of vertices. Let $E \subseteq  V \times  V$ denote the set of edges in the graph. An edge $e \in  E$ is represented as an ordered pair $e = \left( {{v}_{s},{v}_{t}}\right)$ , where ${v}_{s} \in  V$ is the source vertex and ${v}_{t} \in  V$ is the target vertex,indicating that the edge $e$ connects from ${v}_{s}$ to ${v}_{t}$ . For any graph element $\epsilon$ that is either a vertex or an edge,we denote id $\left( \epsilon \right)$ and $\ell \left( \epsilon \right)$ as the globally unique ID and the label of $\epsilon$ ,respectively. Given an attribute ${a}_{i},\epsilon .{a}_{i}$ denotes the value of the attribute $a$ of $\epsilon$ .

属性图定义为$G = \left( {{V}_{G},{E}_{G}}\right)$，其中$V$表示顶点集合。令$E \subseteq  V \times  V$表示图中边的集合，边$e \in  E$表示为有序对$e = \left( {{v}_{s},{v}_{t}}\right)$，其中${v}_{s} \in  V$为源顶点，${v}_{t} \in  V$为目标顶点，表示边$e$从${v}_{s}$连接到${v}_{t}$。对于任意图元素$\epsilon$（顶点或边），其全局唯一ID和标签分别记作id$\left( \epsilon \right)$和$\ell \left( \epsilon \right)$。给定属性${a}_{i},\epsilon .{a}_{i}$时，$a$表示$\epsilon$的该属性值。

Given a vertex $v$ ,we denote its adjacent edges as ${\mathcal{N}}_{G}^{E}\left( v\right)  = \left\{  {e = \left( {v,{v}_{t}}\right)  \mid  e \in  E}\right\}$ and its adjacent vertices (i.e.,neighbors) as ${\mathcal{N}}_{G}\left( v\right)  = \left\{  {{v}_{t} \mid  \left( {v,{v}_{t}}\right)  \in  E}\right\}$ . It is important to note that the adjacent edges and vertices can be defined for both directions of an edge $e = \left( {{v}_{s},{v}_{t}}\right)$ ,i.e.,when $v = {v}_{s}$ or $v = {v}_{t}$ . However, for simplicity, we only define one direction in this notation. In the actual semantics of the paper,both directions may be considered. The degree of $v$ is defined as ${d}_{G}\left( v\right)  = \left| {{\mathcal{N}}_{G}\left( v\right) }\right|$ ,and the average degree of all vertices in the graph is ${\bar{d}}_{G} = \frac{1}{\left| {V}_{G}\right| }\mathop{\sum }\limits_{{v \in  {V}_{G}}}{d}_{G}\left( v\right)$ . In the rest of the paper,when the context is clear,we may remove $G$ from the subscript for simplicity,for example $G = \left( {V,E}\right)$ .

给定顶点$v$，我们将其相邻边记为${\mathcal{N}}_{G}^{E}\left( v\right)  = \left\{  {e = \left( {v,{v}_{t}}\right)  \mid  e \in  E}\right\}$，相邻顶点（即邻居）记为${\mathcal{N}}_{G}\left( v\right)  = \left\{  {{v}_{t} \mid  \left( {v,{v}_{t}}\right)  \in  E}\right\}$。需注意相邻边和顶点可针对边$e = \left( {{v}_{s},{v}_{t}}\right)$的双向定义，即当$v = {v}_{s}$或$v = {v}_{t}$时。但为简化表述，本表示法仅定义单向。论文实际语义中可能考虑双向情况。$v$的度数定义为${d}_{G}\left( v\right)  = \left| {{\mathcal{N}}_{G}\left( v\right) }\right|$，图中所有顶点的平均度数为${\bar{d}}_{G} = \frac{1}{\left| {V}_{G}\right| }\mathop{\sum }\limits_{{v \in  {V}_{G}}}{d}_{G}\left( v\right)$。后文若上下文明确，为简化可能省略下标中的$G$，例如$G = \left( {V,E}\right)$。

<!-- Media -->

<!-- figureText: Knows Person Message Graph Component Relational Component Knows Edge Projection place_name Selection p1.name = "Tom" Join F Pattern Graph $\mathcal{P}$ p1.place_id = place_id Matched Subgraphs-- Projection place_id name Conceptualize planeme p1.place_id Denmark ${e}_{2}$ p2.name China ${v}_{{p}_{1}}$ ${v}_{m}$ ${e}_{k}$ ${e}_{l}$ ${v}_{{m}_{1}}$ ${e}_{{k}_{i}}$ ${e}_{l}$ ${v}_{{m}_{2}}$ ${e}_{{k}_{3}}$ ${e}_{L}$ Match ${e}_{l}$ Graph Relation $G{R}^{\mathcal{P}}$ (b) Apply Pattern Matching on $G$ and (c) The SPJM Query Conceptualize the Matching Results id1 pid 2023-01-15 Likes Edge ${p}_{2}$ 2023-01-15 ${p}_{2}$ Bot $p{l}_{2}$ 2023-02-18 Likes ${e}_{k}$ . 2024-03-31 message_id content ${m}_{1}$ 2024-03-28 2024-03-21 Property Graph $G$ CREATE PROPERTY GRAPH G Knows knows id (PK) ER Diagram Person PROPERTIES(person_id, name), pid1 (FK) Message PROPERTIES(message_id, content) Likes Message date likes_id (PK) IMMESSAGELID pid (FK) content SOURCE KEY (pid) REFERENCE Person (person_id) Person DESTINATION KEY(mid) REFERENCE Message (message id) date PROPERTIES (date). person_id (PK) Knows name SOURCE KEY (pid1) REFERENCE Person (person_id) FK: Foreign Key Relations RGMapping Sentence (a) The Process of RGMapping -->

<img src="https://cdn.noedgeai.com/0196599e-8e15-7d66-a6ce-e26720ffa9f7_4.jpg?x=161&y=263&w=1237&h=452&r=0"/>

Fig. 2. An example of RGMapping.

图2. RGMapping示例

<!-- Media -->

Considering two graphs ${G}_{1}$ and ${G}_{2}$ ,we assert that ${G}_{2}$ is a subgraph of ${G}_{1}$ ,symbolized as ${G}_{2} \subseteq  {G}_{1}$ , if and only if ${V}_{{G}_{2}} \subseteq  {V}_{{G}_{1}}$ ,and ${E}_{{G}_{2}} \subseteq  {E}_{{G}_{1}}$ . Furthermore, ${G}_{2}$ qualifies as an induced subgraph of ${G}_{1}$ under the condition that ${G}_{2}$ is already a subgraph of ${G}_{1}$ ,and for every pair of vertices in ${G}_{2}$ ,any edge $e$ that exists between them in ${G}_{1}$ must also present in ${G}_{2}$ .

考虑两个图${G}_{1}$和${G}_{2}$，当且仅当${V}_{{G}_{2}} \subseteq  {V}_{{G}_{1}}$且${E}_{{G}_{2}} \subseteq  {E}_{{G}_{1}}$时，我们称${G}_{2}$是${G}_{1}$的子图，记作${G}_{2} \subseteq  {G}_{1}$。此外，若${G}_{2}$已是${G}_{1}$的子图，且对于${G}_{2}$中任意顶点对，它们在${G}_{1}$中存在的边$e$也必须在${G}_{2}$中存在，则${G}_{2}$构成${G}_{1}$的导出子图。

To illustrate the integration of graph syntax within the realm of relational data, we introduce the concept of a Relations-to-Graph Mapping (i.e. RGMapping), to facilitate the transformation of relational data structures into a property graph.

为阐明关系数据领域中图语法的集成，我们引入关系-图映射（即RGMapping）概念，用于将关系数据结构转换为属性图。

An RGMapping consists of an vertex mapping and an edge mapping that map tuples in relations to unique vertices or edges. To better describe these vertex and edge mappings, we can leverage the Entity-Relationship (ER) diagram [14, 46]. In relational data modeling, an ER diagram includes entities and relationships. Consequently, vertices can be mapped from relations corresponding to entities, and edges can be mapped from relations corresponding to relationships. Relations mapped to vertices and edges are referred to as vertex relations and edge relations, respectively.

RGMapping由顶点映射和边映射组成，将关系中的元组映射为唯一顶点或边。为更好描述这些映射，可借助实体-关系图（ER图）[14,46]。在关系数据建模中，ER图包含实体与关系。因此，顶点可从实体对应关系映射而来，边则从关系对应关系映射而来。映射为顶点和边的关系分别称为顶点关系和边关系。

In detail,if a tuple $\tau$ in relation $R$ is mapped to a vertex $v \in  V$ (or an edge $e = \left( {{v}_{s},{v}_{t}}\right)  \in  E$ ), it is assigned an ID id(v)(or id(e)),a label $\ell \left( v\right)$ (or $\ell \left( e\right)$ ) that corresponds to the name of $R$ ,and attributes $v$ .attr* (or e.attr*) that reflect the attributes attr* of $\tau$ . For an edge relation ${R}_{e}$ ,there must exist two vertex relations, ${R}_{{p}_{s}}$ and ${R}_{{p}_{t}}$ . Two total functions are defined: ${\lambda }_{e}^{s} : {R}_{e} \rightarrow  {R}_{{p}_{s}}$ and ${\lambda }_{e}^{t} : {R}_{e} \rightarrow  {R}_{{p}_{t}}$ . Consider a tuple $\tau  \in  {R}_{e}$ mapped to an edge $e$ ,and tuples ${\tau }_{s} \in  {R}_{{p}_{s}}$ and ${\tau }_{t} \in  {R}_{{p}_{t}}$ , where ${\lambda }_{e}^{s}\left( e\right)  = {\tau }_{s}$ and ${\lambda }_{e}^{t}\left( e\right)  = {\tau }_{t}$ . Through the vertex mapping, ${\tau }_{s}$ is mapped to the source vertex ${v}_{s}$ and ${\tau }_{t}$ to the target vertex ${v}_{t}$ of the edge $e$ . The two total functions are often established through primary-foreign key relationships, as illustrated in an ER diagram.

具体而言，若关系$R$中的元组$\tau$被映射至顶点$v \in  V$（或边$e = \left( {{v}_{s},{v}_{t}}\right)  \in  E$），则为其分配一个标识符id(v)（或id(e)）、一个与$R$名称对应的标签$\ell \left( v\right)$（或$\ell \left( e\right)$），以及反映$\tau$属性attr*的特性$v$.attr*（或e.attr*）。对于边关系${R}_{e}$，必须存在两个顶点关系${R}_{{p}_{s}}$和${R}_{{p}_{t}}$。定义了两个全函数：${\lambda }_{e}^{s} : {R}_{e} \rightarrow  {R}_{{p}_{s}}$和${\lambda }_{e}^{t} : {R}_{e} \rightarrow  {R}_{{p}_{t}}$。假设元组$\tau  \in  {R}_{e}$映射为边$e$，且存在元组${\tau }_{s} \in  {R}_{{p}_{s}}$和${\tau }_{t} \in  {R}_{{p}_{t}}$，其中${\lambda }_{e}^{s}\left( e\right)  = {\tau }_{s}$且${\lambda }_{e}^{t}\left( e\right)  = {\tau }_{t}$。通过顶点映射，${\tau }_{s}$被映射为边$e$的源顶点${v}_{s}$，${\tau }_{t}$则映射为目标顶点${v}_{t}$。这两个全函数通常通过主外键关系建立，如ER图所示。

EXAMPLE 2. In Fig. 2(a), we have illustrated some relational tables and their corresponding ER diagram. An RGMapping can be defined following the grammar of SQL/PGQ with CREATE PROPERTY GRAPH statements. The described RGMapping involves assigning tuples from vertex relations (i.e. entities), such as ${R}_{\text{Person }}$ and ${R}_{\text{Message }}$ ,to graph vertices. For instance,the vertex ${v}_{{p}_{1}}$ is associated with the tuple ${\tau }_{{p}_{1}}$ in ${R}_{\text{Person }}$ ,and thus assigned the label "Person" and the name attribute "Tom". Similarly,edge relations (i.e. relationships) ${R}_{\text{Likes }}$ and ${R}_{\text{Knows }}$ correspond to graph edges. Regarding ${R}_{\text{Likes }}$ that is mapped to graph edges,two total functions can be identified,namely ${\lambda }_{\text{Likes }}^{s} : {R}_{\text{Likes }} \rightarrow  {R}_{\text{Person }}$ and ${\lambda }_{\text{Likes }}^{t} : {R}_{\text{Likes }} \rightarrow  {R}_{\text{Message. }}$ . Let’s consider the edge ${e}_{{l}_{1}}$ . It originates from the tuple ${\tau }_{{l}_{1}}$ in the ${R}_{\text{Likes }}$ relation. Its source vertex ${v}_{{p}_{1}}$ is linked to the tuple ${\tau }_{{p}_{1}}$ in ${R}_{\text{Person }}$ via the function ${\lambda }_{\text{Likes }}^{s}$ ,following the primary-foreign key relationship " ${\tau }_{{l}_{1}}$ .pid $= {\tau }_{{p}_{1}}$ .person_id". Similarly,its target vertex ${v}_{{m}_{1}}$ is associated with the tuple ${\tau }_{{m}_{1}}$ in ${R}_{\text{Message }}$ via the function ${\lambda }_{\text{Likes }}^{t}$ ,following " ${\tau }_{{l}_{1}}$ .mid $= {\tau }_{{m}_{1}}$ .message_id". As a result of this mapping,the edge ${e}_{{l}_{1}}$ is assigned the label "Likes" and the attribute "date" with the value “2024-03-31”.

示例2. 在图2(a)中，我们展示了一些关系表及其对应的ER图。RG映射可以按照SQL/PGQ语法通过CREATE PROPERTY GRAPH语句定义。该RG映射涉及将来自顶点关系（即实体）的元组（如${R}_{\text{Person }}$和${R}_{\text{Message }}$）分配给图顶点。例如，顶点${v}_{{p}_{1}}$与${R}_{\text{Person }}$中的元组${\tau }_{{p}_{1}}$相关联，因此被赋予标签"Person"和属性名"Tom"。类似地，边关系（即关系）${R}_{\text{Likes }}$和${R}_{\text{Knows }}$对应图的边。对于映射到图边的${R}_{\text{Likes }}$，可以识别出两个全函数，即${\lambda }_{\text{Likes }}^{s} : {R}_{\text{Likes }} \rightarrow  {R}_{\text{Person }}$和${\lambda }_{\text{Likes }}^{t} : {R}_{\text{Likes }} \rightarrow  {R}_{\text{Message. }}$。以边${e}_{{l}_{1}}$为例，它源自${R}_{\text{Likes }}$关系中的元组${\tau }_{{l}_{1}}$。其源顶点${v}_{{p}_{1}}$通过函数${\lambda }_{\text{Likes }}^{s}$与${R}_{\text{Person }}$中的元组${\tau }_{{p}_{1}}$相连，遵循主外键关系"${\tau }_{{l}_{1}}$.pid$= {\tau }_{{p}_{1}}$.person_id"。类似地，其目标顶点${v}_{{m}_{1}}$通过函数${\lambda }_{\text{Likes }}^{t}$与${R}_{\text{Message }}$中的元组${\tau }_{{m}_{1}}$相关联，遵循"${\tau }_{{l}_{1}}$.mid$= {\tau }_{{m}_{1}}$.message_id"。通过此映射，边${e}_{{l}_{1}}$被赋予标签"Likes"和值为"2024-03-31"的属性"date"。

### 2.2 Matching Operator

### 2.2 匹配操作符

Consider a property graph $G\left( {{V}_{G},{E}_{G}}\right)$ ,alongside a connected pattern graph,represented as $\mathcal{P}\left( {{V}_{\mathcal{P}},{E}_{\mathcal{P}}}\right)$ . Here, $\mathcal{P}$ is a property graph that does not possess attributes,and we denote $n$ and $m$ as the number of vertices and edges in the $\mathcal{P}$ ,respectively. Graph pattern matching seeks to determine all subgraphs in $G$ that are homomorphic to $\mathcal{P}$ . Formally,given a subgraph $g \subseteq  G$ ,a homomorphism from $\mathcal{P}$ to $g$ is a surjective,total mapping $f : {V}_{\mathcal{P}} \cup  {E}_{\mathcal{P}} \rightarrow  {V}_{g} \cup  {E}_{g}$ that satisfies the following conditions: (1) For every vertex $u \in  {V}_{\mathcal{P}}$ ,there is a corresponding vertex $v = f\left( u\right)  \in  {V}_{g}$ with $\ell \left( v\right)  = \ell \left( u\right)$ ; (2) For each edge $e = \left( {{u}_{s},{u}_{t}}\right)  \in  {E}_{\mathcal{P}}$ ,there is a corresponding edge $f\left( e\right)  = \left( {{v}_{s},{v}_{t}}\right)  \in  {E}_{q}$ ,ensuring that the mapping preserves the edge’s the label,as well as its source and target vertices,that is $\ell \left( e\right)  = \ell \left( {f\left( e\right) }\right)$ ,and $f\left( {u}_{s}\right)  = {v}_{s},f\left( {u}_{t}\right)  = {v}_{t}$ . It’s important to highlight the homomorphism semantics,as one of the widely used semantics for graph pattern matching [6], do not require each pattern vertex and edge being uniquely mapped to distinct vertices and edges in the data graph. This facilitates a seamless integration between graph pattern matching and relational operations, but alternative semantics for graph pattern matching such as isomorphism can also be adopted, as will be further discussed in Sec. 3.1.

考虑一个属性图$G\left( {{V}_{G},{E}_{G}}\right)$及其连接的模式图，表示为$\mathcal{P}\left( {{V}_{\mathcal{P}},{E}_{\mathcal{P}}}\right)$。其中$\mathcal{P}$是无属性的属性图，我们分别用$n$和$m$表示$\mathcal{P}$中的顶点数和边数。图模式匹配旨在找出$G$中所有与$\mathcal{P}$同态的子图。形式化地说，给定子图$g \subseteq  G$，从$\mathcal{P}$到$g$的同态是一个满射的完全映射$f : {V}_{\mathcal{P}} \cup  {E}_{\mathcal{P}} \rightarrow  {V}_{g} \cup  {E}_{g}$，需满足：(1) 对每个顶点$u \in  {V}_{\mathcal{P}}$，存在对应顶点$v = f\left( u\right)  \in  {V}_{g}$且满足$\ell \left( v\right)  = \ell \left( u\right)$；(2) 对每条边$e = \left( {{u}_{s},{u}_{t}}\right)  \in  {E}_{\mathcal{P}}$，存在对应边$f\left( e\right)  = \left( {{v}_{s},{v}_{t}}\right)  \in  {E}_{q}$，确保映射保留边的标签及端点，即$\ell \left( e\right)  = \ell \left( {f\left( e\right) }\right)$和$f\left( {u}_{s}\right)  = {v}_{s},f\left( {u}_{t}\right)  = {v}_{t}$。需强调的是，作为图模式匹配的常用语义之一[6]，同态语义不要求模式图的每个顶点和边必须唯一映射到数据图的不同元素。这实现了图模式匹配与关系运算的无缝衔接，但也可采用如图同构等其他语义，详见第3.1节讨论。

The outcomes of graph pattern matching can be succinctly modeled as a relation $G{R}_{G}^{\mathcal{P}}$ ,or more compactly $G{R}^{\mathcal{P}}$ in clear contexts,defined over the schema $S = {V}_{\mathcal{P}} \cup  {E}_{\mathcal{P}}$ . Here,the sets ${V}_{G}$ and ${E}_{G}$ serve as the respective domains for the vertices and edges identified through the matching process. Within this framework, we refer to such a relation as a Graph Relation, a construct where all attributes are derived from the domain of a property graph. It is essential to recognize that any property graph $G$ can be conceptualized as a graph relation $G{R}^{G}$ ,represented by a singular tuple that collectively encompasses all of its vertices and edges. Throughout this paper, we treat the notions of a property graph and a tuple of graph relation as essentially interchangeable terms. In alignment with this perspective, we elaborate on the Matching operator as follows.

图模式匹配的结果可简洁建模为关系$G{R}_{G}^{\mathcal{P}}$（清晰语境下简记作$G{R}^{\mathcal{P}}$），其模式定义为$S = {V}_{\mathcal{P}} \cup  {E}_{\mathcal{P}}$。这里集合${V}_{G}$和${E}_{G}$分别作为匹配过程中识别的顶点和边的值域。在此框架下，我们称此类关系为图关系——其所有属性均源自属性图的定义域。值得注意的是，任何属性图$G$都可概念化为图关系$G{R}^{G}$，表现为包含其全部顶点和边的单一元组。本文中，我们将属性图与图关系元组视为可互换概念。基于此视角，我们对匹配运算符定义如下。

Definition 1 (Matching Operation, $\mathcal{M}$ ). The Matching Operator,denoted as $\mathcal{M}$ ,is designed to perform graph pattern matching on a given graph relation GR against a specified pattern graph $\mathcal{P}$ . For each graph instance $g$ in ${GR},\mathcal{M}$ identifies all subgraphs of $g$ that are homomorphic to $\mathcal{P}$ ,and subsequently, aggregates these mappings to construct a comprehensive graph relation. The operation of the matching Operator can be formally articulated as $\mathcal{M}\left( {{GR},\mathcal{P}}\right)  = \mathop{\bigcup }\limits_{{g \in  {GR}}}G{R}_{g}^{\mathcal{P}}$ .

定义1（匹配运算，$\mathcal{M}$）。匹配运算符记作$\mathcal{M}$，用于对给定图关系GR执行指定模式图$\mathcal{P}$的图模式匹配。对于${GR},\mathcal{M}$中的每个图实例$g$，该运算符会找出所有与$\mathcal{P}$同态的子图，并将这些映射聚合为完整的图关系。该运算形式化表述为$\mathcal{M}\left( {{GR},\mathcal{P}}\right)  = \mathop{\bigcup }\limits_{{g \in  {GR}}}G{R}_{g}^{\mathcal{P}}$。

EXAMPLE 3. Let $G$ denote the property graph derived from the relations via RGMapping in Example 2. Given a pattern graph $\mathcal{P}$ in Fig. 2(b),the results of graph pattern matching are subgraphs of $G$ that are homomorphic to $\mathcal{P}$ ,represented as a graph relation $G{R}^{\mathcal{P}} = \mathcal{M}\left( {G{R}^{G},\mathcal{P}}\right)$ ,each tuple corresponds to one matched subgraph.

示例3. 设$G$表示通过示例2中RGMapping从关系导出的属性图。给定图2(b)中的模式图$\mathcal{P}$，图模式匹配结果是$G$中与$\mathcal{P}$同构的子图，表示为图关系$G{R}^{\mathcal{P}} = \mathcal{M}\left( {G{R}^{G},\mathcal{P}}\right)$，每个元组对应一个匹配子图。

This definition ensures that the matching operator is inherently closed regarding graph relations, which adheres to the language opportunities of "nested matching" (specified as PGQ-079) in SQL/PGQ [40]. In this paper,we only handle cases where $G$ represents the entire property graph, and thereafter simplify the matching operator notation to $\mathcal{M}\left( \mathcal{P}\right)$ when the context is clear.

该定义确保匹配运算符在图关系上具有内在封闭性，符合SQL/PGQ[40]中"嵌套匹配"(编号PGQ-079)的语法特性。本文仅处理$G$表示整个属性图的情况，后续在上下文明确时将匹配运算符简记为$\mathcal{M}\left( \mathcal{P}\right)$。

### 2.3 Problem Definition

### 2.3 问题定义

To study relational query optimization, it is common to focus on SPJ queries, which consists of three most frequently used operations: select, project, and (natural) join. These operations form the backbone of many relational queries. Given a set of relations ${R}_{1},{R}_{2},\ldots ,{R}_{m}$ ,an SPJ query is formally represented as:

研究关系查询优化时，通常聚焦SPJ查询——由最常用的三种操作组成：选择(select)、投影(project)和(自然)连接(join)。这些操作构成多数关系查询的核心。给定关系集${R}_{1},{R}_{2},\ldots ,{R}_{m}$，SPJ查询形式化表示为：

$$
Q = {\pi }_{A}\left( {{\sigma }_{\Psi }\left( {{R}_{1} \bowtie  \cdots  \bowtie  {R}_{m}}\right) }\right) .
$$

Inspired from the SPJ paradigm, we introduce a novel category of queries, termed SPJM queries, to logically formulate SQL/PGQ [40] queries that blend relational and graph operations. The SPJM framework augments SPJ queries by incorporating a matching operator to enrich the query's expressive power, to seamlessly navigate both relational and graph data domains. Given the set of relations and a property graph $G$ constructed from these relations via an RGMapping,an SPJM query is articulated as:

受SPJ范式启发，我们提出SPJM查询这一新类别，用于逻辑化表述融合关系与图操作的SQL/PGQ[40]查询。SPJM框架通过引入匹配运算符增强SPJ查询，提升表达力以无缝衔接关系与图数据领域。给定关系集及通过RGMapping构建的属性图$G$，SPJM查询表述为：

$$
Q = {\pi }_{A}\left( {{\sigma }_{\Psi }\left( {{R}_{1} \bowtie  \cdots  \bowtie  {R}_{m} \bowtie  \left( {{\widehat{\pi }}_{A * }{\mathcal{M}}_{G}\left( \mathcal{P}\right) }\right) }\right) }\right)  \tag{1}
$$

In this formulation, ${\widehat{\pi }}_{A * }{\mathcal{M}}_{G}\left( \mathcal{P}\right)$ is the graph component of the query,while the remaining part of the query is an SPJ expression referred to as the relational component. Here, ${\mathcal{M}}_{G}\left( \mathcal{P}\right)$ represents the process of matching the pattern $\mathcal{P}$ on the graph $G$ and returns a graph relation as defined in Def. 1. The operator ${\widehat{\pi }}_{A * }$ is a graph-calibrated projection operator that extracts the ID,label,and other attributes from the vertices and edges in the matched results. This process helps "flatten" graph elements into relational tuples. For example,given a graph relation ${GR}$ that contains a vertex of \{ID:0,label:Person,name: "Tom"\},the projection ${\widehat{\pi }}_{\mathrm{{id}}\left( v\right) } \rightarrow  {\mathrm{v}}_{ - }{\mathrm{{id}}}_{ \circ  }\ell \left( v\right)  \rightarrow  {\mathrm{v}}_{ - }$ label, $v$ . name $\rightarrow  {\mathrm{v}}_{ - }$ name(GR)turns the vertex into a relational tuple of (0,Person,"Tom"). The projection is designed to reflect the COLUMNS clause in SQL/PGQ to retrieve specific attributes from vertices and edges as required. For simplicity, we assume that all attributes are extracted unless otherwise specified.

该式中，${\widehat{\pi }}_{A * }{\mathcal{M}}_{G}\left( \mathcal{P}\right)$是查询的图成分，其余部分称为关系成分的SPJ表达式。此处${\mathcal{M}}_{G}\left( \mathcal{P}\right)$表示在图$G$上匹配模式$\mathcal{P}$的过程，返回定义1所述的图关系。运算符${\widehat{\pi }}_{A * }$是图校准投影运算符，从匹配结果的顶点和边中提取ID、标签等属性，将图元素"扁平化"为关系元组。例如给定包含顶点\{ID:0,标签:Person,姓名:"Tom"\}的图关系${GR}$，投影${\widehat{\pi }}_{\mathrm{{id}}\left( v\right) } \rightarrow  {\mathrm{v}}_{ - }{\mathrm{{id}}}_{ \circ  }\ell \left( v\right)  \rightarrow  {\mathrm{v}}_{ - }$标签,$v$.姓名$\rightarrow  {\mathrm{v}}_{ - }$name(GR)将其转为关系元组(0,Person,"Tom")。该投影设计对应SQL/PGQ的COLUMNS子句以按需获取属性。简化为未特别说明时提取所有属性。

In this paper, we study the problem of optimizing SPJM queries in Eq. 1. Fig. 2(c) illustrates the SPJM query skeleton corresponding to the SQL/PGQ query in Example 1.

本文研究公式1中SPJM查询的优化问题。图2(c)展示了示例1中SQL/PGQ查询对应的SPJM查询框架。

## 3 Optimizing Matching Operator

## 3 匹配运算符优化

In this section, we focus on handling the matching operator, which plays a distinct role within the SPJM queries. We discuss two main perspectives of optimizing the matching operator: logical transformation and physical implementation. Logical transformation is responsible for transforming a matching operator into a logically equivalent representation, while physical implementation focuses on how the matching operator can be efficiently executed.

本节重点处理SPJM查询中具有特殊作用的匹配运算符，从逻辑转换和物理实现两个维度探讨其优化方法。逻辑转换负责将匹配运算符转为逻辑等价表示，物理实现则关注高效执行策略。

### 3.1 Logical Transformation

### 3.1 逻辑转换

We commence with an intuitive, graph-agnostic transformation before introducing a graph-aware technique grounded on the concept of decomposition tree, which is the key to the optimization of graph pattern matching in the literature $\left\lbrack  {{29},{51}}\right\rbrack$ .

我们先介绍与图无关的直观转换方法，再提出基于分解树概念的图感知技术——这是文献$\left\lbrack  {{29},{51}}\right\rbrack$中图模式匹配优化的关键。

Before proceeding,we introduce the concept of pattern decomposition that decomposes $\mathcal{P}$ into two overlapping patterns, ${\mathcal{P}}_{1}$ and ${\mathcal{P}}_{2}$ ,with shared vertices ${V}_{o} = {V}_{{\mathcal{P}}_{1}} \cap  {V}_{{\mathcal{P}}_{2}}$ and shared edges ${E}_{o} = {E}_{{\mathcal{P}}_{1}} \cap  {E}_{{\mathcal{P}}_{2}}$ . Denote $\mathcal{P} = {\mathcal{P}}_{1} \cup  {\mathcal{P}}_{2}$ . Under the homomorphism semantics,the matching of $\mathcal{P}$ can be represented as:

在继续之前，我们引入模式分解的概念，即将$\mathcal{P}$分解为两个重叠模式${\mathcal{P}}_{1}$和${\mathcal{P}}_{2}$，它们共享顶点${V}_{o} = {V}_{{\mathcal{P}}_{1}} \cap  {V}_{{\mathcal{P}}_{2}}$和边${E}_{o} = {E}_{{\mathcal{P}}_{1}} \cap  {E}_{{\mathcal{P}}_{2}}$。记作$\mathcal{P} = {\mathcal{P}}_{1} \cup  {\mathcal{P}}_{2}$。在同态语义下，$\mathcal{P}$的匹配可表示为：

$$
\mathcal{M}\left( \mathcal{P}\right)  = \mathcal{M}\left( {\mathcal{P}}_{1}\right) {\widehat{ \bowtie  }}_{{V}_{o},{E}_{o}}\mathcal{M}\left( {\mathcal{P}}_{2}\right) , \tag{2}
$$

where $\widehat{ \bowtie  }$ is a natural join operator for joining two graph relations based on the common vertices and edges. Note that Eq. 2 is also applicable to alternative semantics, including isomorphism and non-repeated-edge [6]. To support these semantics, a special all-distinct operator can be applied as a filter to remove results that contain duplicate vertices and/or edges. The adoption of the all-distinct operator is compatible with all techniques in this paper.

其中$\widehat{ \bowtie  }$是基于公共顶点和边连接两个图关系的自然连接运算符。需注意公式2同样适用于其他语义，包括同构和非重复边[6]。为支持这些语义，可采用特殊全异运算符作为过滤器，剔除包含重复顶点和/或边的结果。全异运算符的运用与本文所有技术兼容。

3.1.1 Graph-agnostic Transformation. If the matching operator can be transformed into purely relational operations, the SPJM query becomes a standard SPJ query, which can then be optimized using existing relational optimizers (Sec. 4.1). This graph-agnostic approach is intuitive and easy to implement on top of existing relational databases, making it a straightforward choice in prototyped systems $\left\lbrack  {1,{47},{48}}\right\rbrack$ . However,there is no theoretical guarantee that such a transformation is lossless in the context of RGMapping. In this subsection, we bridge this gap by demonstrating the lossless transformation of the matching operator under RGMapping.

3.1.1 图无关转换。若匹配运算符可转换为纯关系运算，SPJM查询即转化为标准SPJ查询，可通过现有关系优化器进行优化（第4.1节）。这种图无关方法直观且易于在现有关系数据库上实现，使其成为原型系统$\left\lbrack  {1,{47},{48}}\right\rbrack$中的直接选择。但理论上无法保证此类转换在RGMapping背景下是无损的。本小节我们将通过论证RGMapping下匹配运算符的无损转换来填补这一空白。

Consider a pattern graph $\mathcal{P}$ and one of its edges $e = \left( {{u}_{s},{u}_{t}}\right)$ . According to the definition of the matching operator (Sec. 2.2),the graph edges and vertices that can be matched with $e$ must have the labels $\ell \left( e\right) ,\ell \left( {u}_{s}\right)$ ,and $\ell \left( {u}_{t}\right)$ . We further denote the relations corresponding to these edges and vertices via RGMapping as ${R}_{\ell \left( e\right) },{R}_{\ell \left( {u}_{s}\right) }$ ,and ${R}_{\ell \left( {u}_{t}\right) }$ ,respectively. Moreover,there must be total functions ${\lambda }_{\ell \left( e\right) }^{s}$ and ${\lambda }_{\ell \left( e\right) }^{t}$ for mapping tuples from ${R}_{\ell \left( e\right) }$ to ${R}_{\ell \left( {u}_{s}\right) }$ and ${R}_{\ell \left( {u}_{t}\right) }$ ,respectively. We define the following EVJoin relational operation regarding ${\lambda }_{\ell \left( e\right) }^{s}$ as:

考虑模式图$\mathcal{P}$及其某条边$e = \left( {{u}_{s},{u}_{t}}\right)$。根据匹配运算符定义（第2.2节），能与$e$匹配的图边和顶点必须具有标签$\ell \left( e\right) ,\ell \left( {u}_{s}\right)$和$\ell \left( {u}_{t}\right)$。我们进一步通过RGMapping将这些边和顶点对应的关系记为${R}_{\ell \left( e\right) },{R}_{\ell \left( {u}_{s}\right) }$和${R}_{\ell \left( {u}_{t}\right) }$。此外，必须存在全函数${\lambda }_{\ell \left( e\right) }^{s}$和${\lambda }_{\ell \left( e\right) }^{t}$，分别将${R}_{\ell \left( e\right) }$的元组映射至${R}_{\ell \left( {u}_{s}\right) }$和${R}_{\ell \left( {u}_{t}\right) }$。我们定义关于${\lambda }_{\ell \left( e\right) }^{s}$的EVJoin关系运算如下：

$$
{R}_{\ell \left( e\right) }{ \bowtie  }_{\epsilon v}{R}_{\ell \left( {u}_{s}\right) } = \left\{  \left( {{\tau }_{e},{\tau }_{s}}\right) \right.  \tag{3}
$$

$$
\left. {{\tau }_{e} \in  {R}_{\ell \left( e\right) } \land  {\tau }_{s} \in  {R}_{\ell \left( {u}_{s}\right) } \land  {\lambda }_{\ell \left( e\right) }^{s}\left( {\tau }_{e}\right)  = {\tau }_{s}}\right\}  .
$$

The EVJoin regarding ${\lambda }_{\ell \left( e\right) }^{t}$ is defined analogously. Although called EVJoin,the operation is associative like any relation join, meaning that the order in which the edge and vertex relations are joined does not affect the final result.

关于${\lambda }_{\ell \left( e\right) }^{t}$的EVJoin定义类似。尽管称为EVJoin，该运算如同任何关系连接一样具有结合性，意味着边关系与顶点关系的连接顺序不影响最终结果。

We have the following lemma.

我们得到如下引理。

LEMMA 1. Under RGMapping, the matching operation in an SPJM query can be losslessly transformed into a sequence of relational joins involving $n$ vertex relations and $m$ edge relations.

引理1. 在RGMapping下，SPJM查询中的匹配运算可无损转换为涉及$n$个顶点关系和$m$个边关系的关系连接序列。

Proof. Consider a pattern ${\mathcal{P}}_{m}$ of $m$ edges,where the $i$ -th vertex is denoted as ${u}_{i}$ ,and the $i$ -th edge is ${e}_{i} = \left( {{u}_{{s}_{i}},{u}_{{t}_{i}}}\right)$ .

证明. 考虑具有$m$条边的模式${\mathcal{P}}_{m}$，其中第$i$个顶点记为${u}_{i}$，第$i$条边为${e}_{i} = \left( {{u}_{{s}_{i}},{u}_{{t}_{i}}}\right)$。

The proof proceeds by induction,starting with a pattern graph ${\mathcal{P}}_{0}$ with a single vertex only. It is clear that $\mathcal{M}\left( {\mathcal{P}}_{0}\right)$ yields a subset of vertices with label $\ell \left( {u}_{0}\right)$ ,which is mapped from the relation ${R}_{\ell \left( {u}_{0}\right) }$ via RGMapping. As a result,we have ${R}_{0} = {\widehat{\pi }}_{A * }\left( {\mathcal{M}\left( {\mathcal{P}}_{0}\right) }\right)  = {R}_{\ell \left( {u}_{0}\right) }$ .

证明过程采用归纳法，从仅含单一顶点的模式图${\mathcal{P}}_{0}$开始。显然$\mathcal{M}\left( {\mathcal{P}}_{0}\right)$生成带有标签$\ell \left( {u}_{0}\right)$的顶点子集，该子集通过RGMapping从关系${R}_{\ell \left( {u}_{0}\right) }$映射而来。因此我们得到${R}_{0} = {\widehat{\pi }}_{A * }\left( {\mathcal{M}\left( {\mathcal{P}}_{0}\right) }\right)  = {R}_{\ell \left( {u}_{0}\right) }$。

Next,consider ${\mathcal{P}}_{1}$ with one edge, ${e}_{1} = \left( {{u}_{{s}_{1}},{u}_{{t}_{1}}}\right)$ . Matching ${\mathcal{P}}_{1}$ is equivalent to retrieving the edge relation, together with the corresponding source and target vertices. Therefore, we have:

接着考虑含一条边${e}_{1} = \left( {{u}_{{s}_{1}},{u}_{{t}_{1}}}\right)$的${\mathcal{P}}_{1}$。匹配${\mathcal{P}}_{1}$等价于检索边关系及对应的源顶点与目标顶点。因此可得：

$$
{R}_{1} = {\widehat{\pi }}_{A * }\left( {M\left( {\mathcal{P}}_{1}\right) }\right)  = {R}_{\ell \left( {u}_{{s}_{1}}\right) }{ \bowtie  }_{\epsilon v}{R}_{\ell \left( {e}_{1}\right) }{ \bowtie  }_{\epsilon v}{R}_{\ell \left( {u}_{{t}_{1}}\right) }
$$

Assume that when $m = k - 1,{\widehat{\pi }}_{A * }\left( {\mathcal{M}\left( {\mathcal{P}}_{k - 1}\right) }\right)$ can be converted to a sequence of relational operators, resulting in ${R}_{k - 1}$ . When $m = k$ ,we consider ${\mathcal{P}}_{k}$ of $k$ edges constructed from ${\mathcal{P}}_{k - 1}$ by adding edge ${e}_{k} = \left( {{u}_{{s}_{k}},{u}_{{t}_{k}}}\right)$ . For ${\mathcal{P}}_{k}$ to be connected,it must share at least one common vertex ${V}_{o}$ with ${\mathcal{P}}_{k - 1}$ . According to Eq. 2, we have:

假设当$m = k - 1,{\widehat{\pi }}_{A * }\left( {\mathcal{M}\left( {\mathcal{P}}_{k - 1}\right) }\right)$可转换为关系运算符序列时，将生成${R}_{k - 1}$。当$m = k$时，我们考察通过添加边${e}_{k} = \left( {{u}_{{s}_{k}},{u}_{{t}_{k}}}\right)$从${\mathcal{P}}_{k - 1}$构建的$k$条边形成的${\mathcal{P}}_{k}$。要使${\mathcal{P}}_{k}$保持连通，必须与${\mathcal{P}}_{k - 1}$共享至少一个公共顶点${V}_{o}$。根据公式2可得：

$$
\mathcal{M}\left( {\mathcal{P}}_{k}\right)  = \mathcal{M}\left( {\mathcal{P}}_{{e}_{k}}\right) {\widehat{ \bowtie  }}_{{V}_{o}}\mathcal{M}\left( {\mathcal{P}}_{k - 1}\right) ,
$$

where ${\mathcal{P}}_{{e}_{k}}$ denotes a pattern that contains only the edge ${e}_{k}$ ,and ${V}_{o}$ is the common vertex shared by ${\mathcal{P}}_{k - 1}$ and ${\mathcal{P}}_{{e}_{k}}$ . Applying ${\widehat{\pi }}_{A * }$ to the above equation,we get:

其中${\mathcal{P}}_{{e}_{k}}$表示仅含边${e}_{k}$的模式，${V}_{o}$是${\mathcal{P}}_{k - 1}$与${\mathcal{P}}_{{e}_{k}}$的共享顶点。对上述等式应用${\widehat{\pi }}_{A * }$后得到：

$$
{R}_{k} = {\widehat{\pi }}_{A * }\left( {\mathcal{M}\left( {\mathcal{P}}_{k}\right) }\right) 
$$

$$
 = {\widehat{\pi }}_{{A}_{1} * }\left( {\mathcal{M}\left( {\mathcal{P}}_{{e}_{k}}\right) }\right) { \bowtie  }_{{V}_{o}\text{. attr }}{\widehat{\pi }}_{{A}_{2} * }\left( {\mathcal{M}\left( {\mathcal{P}}_{k - 1}\right) }\right) 
$$

$$
 = {R}_{\ell }\left( {u}_{{s}_{k}}\right) { \bowtie  }_{\epsilon v}{R}_{\ell \left( {e}_{k}\right) }{ \bowtie  }_{\epsilon v}{R}_{\ell \left( {u}_{{t}_{k}}\right) }{ \bowtie  }_{{V}_{o}\text{. attr }}{R}_{k - 1}
$$

By induction,denoting ${R}_{i}^{\prime } = {R}_{\ell \left( {u}_{{s}_{i}}\right) }{ \bowtie  }_{\epsilon v}{R}_{\ell \left( {e}_{i}\right) }{ \bowtie  }_{\epsilon v}{R}_{\ell \left( {u}_{{t}_{i}}\right) }$ ,we have the matching operator losslessly converted to a sequence of relational join operations:

通过归纳法，记${R}_{i}^{\prime } = {R}_{\ell \left( {u}_{{s}_{i}}\right) }{ \bowtie  }_{\epsilon v}{R}_{\ell \left( {e}_{i}\right) }{ \bowtie  }_{\epsilon v}{R}_{\ell \left( {u}_{{t}_{i}}\right) }$，我们已将匹配运算符无损转换为关系连接运算序列：

$$
{\widehat{\pi }}_{A * }\left( {\mathcal{M}\left( {\mathcal{P}}_{k}\right) }\right)  = {R}_{k}^{\prime } \bowtie  {R}_{k - 1}^{\prime } \bowtie  \cdots  \bowtie  {R}_{1}^{\prime } \bowtie  {R}_{0}. \tag{4}
$$

We thus conclude the proof.

由此完成证明。

Example 4. Given pattern graph $\mathcal{P}$ in Fig. 2(b),the matching operation $\mathcal{M}\left( \mathcal{P}\right)$ can be converted to a sequence of join operations as follows. Without loss of generality,we start from ${\mathcal{P}}_{0}$ containing only the vertex ${u}_{{p}_{1}}$ ,and we have ${R}_{0} = {R}_{\text{Person }}^{1}$ (note that the superscript 1 is used to differentiate relations of the same name). Next,we sequentially add the edges ${e}_{1} = \left( {{u}_{{p}_{1}},{u}_{{p}_{2}}}\right) ,{e}_{2} = \left( {{u}_{{p}_{1}},{u}_{m}}\right)$ ,and ${e}_{3} = \left( {{u}_{{p}_{2}},{u}_{m}}\right)$ to ${\mathcal{P}}_{0}$ ,resulting in the following relations:

示例4. 给定图2(b)中的模式图$\mathcal{P}$，匹配操作$\mathcal{M}\left( \mathcal{P}\right)$可按以下步骤转换为连接运算序列。不失一般性，我们从仅含顶点${u}_{{p}_{1}}$的${\mathcal{P}}_{0}$开始，得到${R}_{0} = {R}_{\text{Person }}^{1}$（注意上标1用于区分同名关系）。随后依次向${\mathcal{P}}_{0}$添加边${e}_{1} = \left( {{u}_{{p}_{1}},{u}_{{p}_{2}}}\right) ,{e}_{2} = \left( {{u}_{{p}_{1}},{u}_{m}}\right)$和${e}_{3} = \left( {{u}_{{p}_{2}},{u}_{m}}\right)$，生成下列关系：

$$
{R}_{1}^{\prime } = {R}_{\text{Person }}^{1}{ \bowtie  }_{\text{person_id } = \text{ pid1 }}{R}_{\text{Knows }}{ \bowtie  }_{\text{pid2 } = \text{ person_id }}{R}_{\text{Person }}^{2}\text{,}
$$

$$
{R}_{2}^{\prime } = {R}_{\text{Person }}^{1}{ \bowtie  }_{\text{person_id } = \text{ pid }}{R}_{\text{Likes }}^{1}{ \bowtie  }_{\text{mid = message_id }}{R}_{\text{Message }}\text{,}
$$

$$
{R}_{3}^{\prime } = {R}_{\text{Person }}^{2}{ \bowtie  }_{\text{person_id } = \text{ pid }}{R}_{\text{Likes }}^{2}{ \bowtie  }_{\text{ mid }} = \text{ message_id }{R}_{\text{Message }}.
$$

Finally,we have ${\widehat{\pi }}_{A * }\left( {\mathcal{M}\left( \mathcal{P}\right) }\right)  = {R}_{3}^{\prime } \bowtie  {R}_{2}^{\prime } \bowtie  {R}_{1}^{\prime } \bowtie  {R}_{0}$ . Note that ${R}_{\text{Person }}^{1}$ in ${R}_{2}^{\prime }$ ,as well as ${R}_{\text{Person }}^{2}$ and ${R}_{\text{Message }}$ in ${R}_{3}^{\prime }$ ,are redundant and can be removed from the final join. By eliminating them,we obtain a sequence of joins with 3 vertex relations and 3 edge relations.

最终，我们得到${\widehat{\pi }}_{A * }\left( {\mathcal{M}\left( \mathcal{P}\right) }\right)  = {R}_{3}^{\prime } \bowtie  {R}_{2}^{\prime } \bowtie  {R}_{1}^{\prime } \bowtie  {R}_{0}$。注意在${R}_{2}^{\prime }$中的${R}_{\text{Person }}^{1}$，以及${R}_{3}^{\prime }$中的${R}_{\text{Person }}^{2}$和${R}_{\text{Message }}$都是冗余的，可以从最终连接中移除。通过消除它们，我们获得了一个包含3个顶点关系和3个边关系的连接序列。

3.1.2 Graph-aware Transformation. We introduce a graph-aware transformation that incorporates key ideas from the literature on graph optimization. Following Eq. 2, we can recursively decompose $\mathcal{P}$ ,forming a tree structure called the decomposition tree. The tree has a root node that represents $\mathcal{P}$ , and each non-leaf intermediate node is a sub-pattern (a subgraph of the pattern) ${\mathcal{P}}^{\prime } \subset  \mathcal{P}$ ,which has a left and right child node,denoted as ${\mathcal{P}}_{l}^{\prime }$ and ${\mathcal{P}}_{r}^{\prime }$ ,respectively. The leaf nodes of the tree are called Minimum Matching Components (MMC), correspond to indivisible patterns directly solvable with specific physical operations as will be introduced in Sec. 3.2. The decomposition tree naturally forms a logical plan for solving $\mathcal{M}\left( \mathcal{P}\right)$ ,as demonstrated in Fig. 3. For any non-leaf node ${\mathcal{P}}^{\prime }$ ,there exists a relationship $\mathcal{M}\left( {\mathcal{P}}^{\prime }\right)  = \mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) \widehat{ \boxtimes  }\mathcal{M}\left( {\mathcal{P}}_{r}^{\prime }\right)$ according to Eq. 2. The plan allows for the recursive computation of the entire pattern.

3.1.2 图感知转换。我们引入了一种融合图优化文献关键思想的图感知转换。根据公式2，可以递归分解$\mathcal{P}$，形成称为分解树的树状结构。该树的根节点表示$\mathcal{P}$，每个非叶中间节点都是子模式（模式的子图）${\mathcal{P}}^{\prime } \subset  \mathcal{P}$，其左右子节点分别记为${\mathcal{P}}_{l}^{\prime }$和${\mathcal{P}}_{r}^{\prime }$。树的叶节点称为最小匹配组件（MMC），对应不可分割的模式，可直接通过第3.2节将介绍的特定物理操作求解。如图3所示，分解树自然形成了求解$\mathcal{M}\left( \mathcal{P}\right)$的逻辑计划。对于任何非叶节点${\mathcal{P}}^{\prime }$，根据公式2存在关系$\mathcal{M}\left( {\mathcal{P}}^{\prime }\right)  = \mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) \widehat{ \boxtimes  }\mathcal{M}\left( {\mathcal{P}}_{r}^{\prime }\right)$。该计划支持对整个模式进行递归计算。

Following state-of-the-art graph optimizers [29, 51], to guarantee a worst-case optimal execution plan [39],all intermediate sub-patterns in the decomposition tree must be induced subgraphs of $\mathcal{P}$ . Furthermore, MMC is restricted to be a single-vertex pattern and a complete star. A star-shaped pattern is denoted as $\mathcal{P}\left( {u;{V}_{s}}\right)$ ,where $u$ is the root vertex and ${V}_{s}$ is the set of leaf vertices ${}^{1}$ . In the decomposition tree,given ${\mathcal{P}}^{\prime } = {\mathcal{P}}^{\prime \prime } \cup  \mathcal{P}\left( {u;{V}_{s}}\right) ,\mathcal{P}\left( {u;{V}_{s}}\right)$ is a complete star if and only if it is a right child and ${V}_{s} \subseteq  {V}_{{\mathcal{P}}^{\prime \prime }}$ ,meaning that the leaf vertices of the complete star must all be common vertices for the decomposition. A single-edge pattern is a special case of a complete star. The complete star logically represents the physical operations of EXPAND_INTERSECT, which will be discussed in Sec. 3.2. As shown in Fig. 3,a single-edge pattern,such as ${\mathcal{P}}_{3}$ ,is further decomposed into a single-vertex pattern and the pattern itself, allowing the optimizer to select from which vertex the edge can be expanded. The intermediate sub-patterns pruned from the decomposition tree are also presented in Fig. 3. Some previous studies, such as EmptyHeaded [4] and CLFTJ [24], have also explored decomposition trees. However, our method significantly differs from theirs. Specifically, in these previous methods, the tree nodes represent sets of relations, and the edges in the decomposition trees connect nodes with common join keys. In contrast, the nodes in our decomposition trees represent sub-patterns (relations that can form a graph after RGMapping) of queries. Each edge in our tree connects two nodes such that the child sub-pattern can be computed from the parent sub-pattern in some execution plan.

遵循最先进的图优化器[29,51]方案，为确保最坏情况下的最优执行计划[39]，分解树中所有中间子模式必须是$\mathcal{P}$的导出子图。此外，MMC被限制为单顶点模式和完整星型结构。星型模式记为$\mathcal{P}\left( {u;{V}_{s}}\right)$，其中$u$是根顶点，${V}_{s}$是叶顶点集合${}^{1}$。在分解树中，当且仅当${\mathcal{P}}^{\prime } = {\mathcal{P}}^{\prime \prime } \cup  \mathcal{P}\left( {u;{V}_{s}}\right) ,\mathcal{P}\left( {u;{V}_{s}}\right)$是右子节点且${V}_{s} \subseteq  {V}_{{\mathcal{P}}^{\prime \prime }}$时，该节点才构成完整星型——这意味着星型结构的叶顶点必须全部是分解过程中的公共顶点。单边模式是完整星型的特例。完整星型在逻辑上对应EXPAND_INTERSECT物理操作（详见3.2节）。如图3所示，单边模式（如${\mathcal{P}}_{3}$）会被进一步分解为单顶点模式和其自身，这使得优化器能选择边的扩展起点。图3同时展示了从分解树剪枝的中间子模式。早期研究如EmptyHeaded[4]和CLFTJ[24]也探索过分解树，但我们的方法存在本质差异：这些方法中树节点表示关系集合，分解树的边通过连接键关联节点；而我们的分解树节点表示查询子模式（经RGMapping后可构成图的关系），每条边连接的两个节点满足子模式可通过父模式在某个执行计划中计算得出。

REMARK 1. The graph-aware transformation is fundamentally different from its graph-agnostic counterpart. While the graph-agnostic approach consistently converts pattern matching operations into relational joins between vertex and edge relations, the graph-aware transformation does not, due to the constraints imposed by pattern decomposition. While the graph-agnostic approach is straightforward, it has the following drawbacks:

注1. 图感知转换与图无关转换存在根本差异。图无关方法始终将模式匹配操作转换为顶点关系与边关系之间的连接运算，而图感知转换受模式分解约束不采用此方式。虽然图无关方法直观，但存在以下缺陷：

Graph-unaware Join Order: It may lead the relational optimizer to reorder the join of vertex and edge relations, potentially missing chances to use graph indexes for efficiently computing adjacent edges and vertices, as discussed in Sec. 3.2.1.

非图感知的连接顺序：可能导致关系优化器对顶点和边关系连接进行重排序，错失使用图索引高效计算邻接边和顶点的机会（如3.2.1节所述）。

---

<!-- Footnote -->

${}^{1}$ Edge directions between $u$ and ${V}_{s}$ are not important,and we assume they all point from $u$ to ${V}_{s}$ .

${}^{1}$ $u$与${V}_{s}$间的边方向不重要，我们假定均从$u$指向${V}_{s}$。

<!-- Footnote -->

---

<!-- Media -->

<!-- figureText: A Join ${}_{1}$ Filtered Intermediate Sub-patterns Containing edges but not all adjacent vertice Intermediate Pattern Complete-star $\left( {u}_{{p}_{1}}\right.$ ... Patterr Not an induced subgraph Single-edge Pattern Single-vertex Pattern Joi ${n}_{2}$ $\mathcal{M}\left( {\mathcal{P}}_{2}\right)$ Pattern $\mathcal{P}$ $\mathcal{M}\left( {\mathcal{P}}_{3}\right) \mathcal{M}\left( {\mathcal{P}}_{4}\right)$ Logical Plan Pattern ${\mathcal{P}}_{1}$ Pattern ${\mathcal{P}}_{2}$ $\left( {u}_{{p}_{1}}\right)$ Pattern ${\mathcal{P}}_{3}$ Pattern ${\mathcal{P}}_{4}$ Decompose Process -->

<img src="https://cdn.noedgeai.com/0196599e-8e15-7d66-a6ce-e26720ffa9f7_9.jpg?x=266&y=263&w=1027&h=497&r=0"/>

Fig. 3. Example of decomposition trees and the corresponding logical plans. Note that sub-pattern ${\mathcal{P}}_{2}$ can be a leaf node, but it cannot be an intermediate node.

图3. 分解树及对应逻辑计划示例。注意子模式${\mathcal{P}}_{2}$可作为叶节点，但不能作为中间节点。

<!-- figureText: ${10}^{15}$ Graph-Aware ${10}^{5}$ Agnostic / Aware ${10}^{4}$ ${10}^{3}$ ${10}^{2}$ ${10}^{1}$ 1 3 4 6 8 9 10 Edge Number (a) Search Space Comparison. Calcite RelGo Queries of IC* (b) Optimization Time Cost on LDBC Queries. Search Space ${10}^{13}$ Graph-Agnostic ${10}^{11}$ ${10}^{9}$ ${10}^{7}$ ${10}^{5}$ ${10}^{3}$ ${10}^{1}$ 2 4 5 6 8 10 Edge Number ${10}^{6}$ Opt. Time (ms) ${10}^{5}$ ${10}^{3}$ ${10}^{2}$ ${10}^{1}$ ${10}^{0}$ ${10}^{-1}$ ${10}^{-2}$ -->

<img src="https://cdn.noedgeai.com/0196599e-8e15-7d66-a6ce-e26720ffa9f7_9.jpg?x=264&y=905&w=1028&h=738&r=0"/>

Fig. 4. Compare the search space and optimization time.

图4. 搜索空间与优化时间对比。

<!-- Media -->

Suboptimal Join Plans: It generates plans that consistently reflect edge-based join plans that have been shown to be suboptimal in terms of worst-case performance [27].

次优连接计划：生成的计划始终反映基于边的连接方案，这些方案在最坏情况下性能已被证明是次优的[27]。

Increased Search Space: Compared to the graph-aware transformation, it can lead to an exponentially larger search space when computing optimal plans, which will be discussed in the following.

搜索空间膨胀：相较于图感知转换，该方法在计算最优计划时可能导致搜索空间呈指数级增长（下文将详述）。

3.1.3 The Search Space: Graph-agnostic vs Graph-aware. After applying graph-agnostic transformations to the matching operator, the optimizer searches for the optimal join order. In contrast, applying graph-aware transformations leads to a search for the optimal decomposition tree. The search space for the graph-agnostic approach is clearly larger than that of the graph-aware approach, given the constraints imposed on the decomposition tree in the latter approach. However, the precise difference in search space complexity between the two approaches has not been rigorously analyzed. In this subsection, we analyze the gap between the two search spaces and conclude that the graph-aware approach can be exponentially more efficient in this regard.

3.1.3 搜索空间：图无关 vs 图感知。对匹配算子应用图无关转换后，优化器需搜索最优连接顺序；而应用图感知转换则需搜索最优分解树。由于后者对分解树施加了约束条件，其搜索空间明显小于前者。但两种方法搜索空间复杂度的精确差异尚未被严格分析。本小节我们分析两者差距，得出结论：图感知方法在此方面可具有指数级效率优势。

<!-- Media -->

<!-- figureText: likes_id pid date pid_rowid mid_rowid Rperson ${R}_{Likes}$ ${R}_{Message}$ Row Id Row Id Row Id 0 0 0 1 1 0 2 2 1 3 1 (b) The VE-Index on ${R}_{\text{Person }}$ in the CSR Format ${l}_{1}$ ${p}_{1}$ ${m}_{1}$ ... 0 0 ${l}_{2}$ ${p}_{2}$ ${m}_{1}$ ... 1 0 ${l}_{3}$ ${p}_{2}$ ${m}_{2}$ 1 1 ${l}_{4}$ ${p}_{3}$ ${m}_{2}$ ... 2 1 (a) The EV-Index on ${R}_{\text{Likes }}$ -->

<img src="https://cdn.noedgeai.com/0196599e-8e15-7d66-a6ce-e26720ffa9f7_10.jpg?x=196&y=262&w=1151&h=335&r=0"/>

Fig. 5. The graph index constructed among relations ${R}_{\text{Person }},{R}_{\text{Likes }}$ and ${R}_{\text{Message }}$ in Fig. 2(a).

图5. 图2(a)中关系${R}_{\text{Person }},{R}_{\text{Likes }}$与${R}_{\text{Message }}$间构建的图索引。

<!-- Media -->

THEOREM 1. The search space in graph-aware transformation can be exponentially smaller than that of the graph-agnostic transformation, for optimizing the matching operator in an SPJM query.

定理1. 对于SPJM查询中的匹配算子优化，图感知转换的搜索空间可能比图无关转换小指数级别。

3.1.4 Comparison of Search Space and Optimization Time. To further illustrate Theorem 1, we used a special case of a path graph to compare the search spaces directly. We conducted a micro-benchmark experiment using a path graph with $m$ edges,programming an enumerator to explore the search space of both graph-agnostic and graph-aware approaches while varying $m$ . The results, shown in Fig. 4a, confirm the significant difference in search space size between the two approaches.

3.1.4 搜索空间与优化时间的对比。为更直观说明定理1，我们采用路径图特例直接对比两种方法的搜索空间。通过构建含$m$条边的路径图进行微基准测试，编程实现枚举器分别遍历图无关与图感知方法的搜索空间，同时动态调整$m$。图4a所示结果证实了两种方法在搜索空间规模上的显著差异。

Additionally, we compared the optimizer's query optimization time. In our comparison, Apache Calcite, a generic relational optimization framework, served as the optimizer for the graph-agnostic method. In contrast, our RelGo, implemented based on Calcite, acts as the optimizer for the graph-aware method. Both RelGo and Calcite are implemented in Java, utilizing the VolcanoPlanner of Calcite with default rules. Notably, we did not consider aggressive pruning rules as used in commercialized database like DuckDB [2] for either Calcite or RelGo, providing a fair comparison and a clear demonstration of the reduced search space. The optimization time was evaluated using the queries in our experiment (details in Sec. 5). Optimizations that do not complete within 10 minutes are recorded as taking 10 minutes. Since Calcite often exceeds the 10-minute limit on JOB queries[35], we only report the results on LDBC queries. The results in Fig. 4b indicate that RelGo can complete optimizing almost all queries within 10-100 milliseconds. Besides, the results demonstrate RelGo's significant superiority over Calcite in query optimization speed. For instance, on ${\mathrm{{IC}}}_{5 - 1}$ ,the optimization time using RelGo is more than ${10}^{4}$ times faster compared to Calcite.

我们还对比了优化器的查询优化耗时。实验中，通用关系型优化框架Apache Calcite作为图无关方法的优化器，而基于Calcite实现的RelGo则作为图感知方法的优化器。两者均采用Java实现，使用Calcite的VolcanoPlanner默认规则。值得注意的是，我们未采用DuckDB[2]等商业化数据库的激进剪枝规则，确保公平对比并清晰展示搜索空间的缩减效果。优化时间基于实验查询集评估（详见第5节），超时10分钟的优化任务按10分钟记录。由于Calcite处理JOB查询[35]常超时，我们仅展示LDBC查询结果。图4b显示RelGo能在10-100毫秒内完成绝大多数查询优化，且速度显著优于Calcite。例如在${\mathrm{{IC}}}_{5 - 1}$场景下，RelGo的优化速度比Calcite快${10}^{4}$倍以上。

### 3.2 Physical Implementation

### 3.2 物理实现

In the graph view,given a vertex $v$ ,it is efficient to obtain its adjacent edges and vertices (i.e., neighbors). However, in the relational view, such adjacency relationships between vertices and edges are not directly stored in relations but must be computed via the EVJoin operations (Eq. 3). While there are multiple ways to construct the graph view in the literature [20, 45], we refer to the method introduced in GRainDB [23], which is free from materializing the graph. This approach avoids the extra storage cost associated with graph materialization and ensures compatibility with the relational context, Specifically, GRainDB introduces an indexing technique called pre-defined join to improve the performance of join operations. As the pre-defined join essentially materializes the adjacency relationships, we treat it as a graph index in this work.

在图视图中，给定顶点$v$可高效获取其邻接边与顶点（即邻居）。而在关系视图中，这种顶点与边的邻接关系并非直接存储，需通过EVJoin操作（公式3）计算获得。现有文献[20,45]提出了多种图视图构建方法，本文采用GRainDB[23]提出的非物化方案，既避免了图物化的额外存储开销，又确保与关系型上下文的兼容性。具体而言，GRainDB通过预定义连接索引技术提升连接操作性能，由于该索引实质物化了邻接关系，本工作将其视为图索引。

3.2.1 Graph Index. As shown in Fig. 5,given the three relations ${R}_{\text{Person }},{R}_{\text{Likes }}$ ,and ${R}_{\text{Message }}$ ,the complete information of "Person likes messages" can be obtained by conducting the join:

3.2.1 图索引。如图5所示，给定${R}_{\text{Person }},{R}_{\text{Likes }}$、${R}_{\text{Message }}$三个关系表，通过执行连接操作可获取"人员喜欢消息"的完整信息：

$$
{R}_{\text{Person }}{ \bowtie  }_{\text{person_id = pid }}{R}_{\text{Likes }}{ \bowtie  }_{\text{mid = message_id }}{R}_{\text{Message }}\text{.}
$$

GRainDB introduces two kinds of indexes to the relational tables to efficiently process the join: the EV-index and the VE-index. The EV-index, shown in Fig. 5(a), is constructed by appending extra columns to the table ${R}_{\text{Likes }}$ . The column "pid_rowid" stores the row ID of the corresponding tuple in the table ${R}_{\text{Person }}$ ,denoted as rid $\left( {\tau }_{p}\right)$ ,where ${\tau }_{p} \in  {R}_{\text{Person }}$ . Similarly,the column "mid_rowid" stores the row ID of the corresponding tuple in the table ${R}_{\text{Message }}$ ,denoted as rid $\left( {\tau }_{m}\right)$ ,where ${\tau }_{m} \in  {R}_{\text{Message }}$ . These row ids help quickly route a tuple ${\tau }_{l} \in  {R}_{\text{Likes }}$ to the joinable tuples ${\tau }_{p}$ and ${\tau }_{m}$ without additional operations like hash-table lookup or sorting.

GRainDB为关系表引入两类索引以加速连接操作：EV索引与VE索引。图5(a)所示的EV索引通过在${R}_{\text{Likes }}$表追加额外列实现。"pid_rowid"列存储${R}_{\text{Person }}$表中对应元组的行ID（记为rid$\left( {\tau }_{p}\right)$，其中${\tau }_{p} \in  {R}_{\text{Person }}$）；同理"mid_rowid"列存储${R}_{\text{Message }}$表对应元组行ID（记为rid$\left( {\tau }_{m}\right)$，其中${\tau }_{m} \in  {R}_{\text{Message }}$）。这些行ID能直接将元组${\tau }_{l} \in  {R}_{\text{Likes }}$路由至可连接元组${\tau }_{p}$和${\tau }_{m}$，无需哈希查找或排序等额外操作。

The VE-index in Fig. 5(b) is created on ${R}_{\text{Person }}$ for efficiently computing its "liked messages". For each tuple ${\tau }_{p} \in  {R}_{\text{Person }}$ ,the VE-index records the row ids of ${\tau }_{l} \in  {R}_{\text{Likes }}$ and the corresponding ${\tau }_{m} \in  {R}_{\text{Message }}$ that are joinable with ${\tau }_{p}$ . In the graph view,treating "Person-[Likes]->Messages" as an edge of a property graph, the VE-index maintains the adjacent edges and vertices of each person.

图5(b)中的VE索引创建于${R}_{\text{Person }}$之上，用于高效计算其"点赞消息"。对于每个元组${\tau }_{p} \in  {R}_{\text{Person }}$，该索引记录了${\tau }_{l} \in  {R}_{\text{Likes }}$的行标识符及可与${\tau }_{p}$连接的对${\tau }_{m} \in  {R}_{\text{Message }}$。在图形视图中，将"人物-[点赞]->消息"视为属性图的边时，VE索引维护着每个人物的相邻边和顶点。

We can adopt GRainDB's approach to construct the graph indexes during the RGMapping process. Given an edge relation ${R}_{e}$ and its associated vertex relations ${R}_{{v}_{s}}$ and ${R}_{{v}_{t}}$ ,the EV-index can be constructed on ${R}_{e}$ for each tuple ${\tau }_{e} \in  {R}_{e}$ by including rid $\left( {{\lambda }_{e}^{s}\left( {\tau }_{e}\right) }\right)$ and rid $\left( {{\lambda }_{e}^{t}\left( {\tau }_{e}\right) }\right)$ ,which are the row ids of the corresponding tuples in ${R}_{{v}_{s}}$ and ${R}_{{v}_{t}}$ ,respectively. Meanwhile,the VE-index can be constructed on ${R}_{{v}_{s}}$ for each tuple ${\tau }_{{v}_{s}} \in  {R}_{{v}_{s}}$ by including the row ids of all tuples ${\tau }_{e} \in  {R}_{e}$ such that ${\lambda }_{e}^{s}\left( {\tau }_{e}\right)  = {\tau }_{{v}_{s}}$ ,along with the row ids of the corresponding tuples ${\tau }_{{v}_{t}} \in  {R}_{{v}_{t}}$ such that ${\lambda }_{e}^{t}\left( {\tau }_{e}\right)  = {\tau }_{{v}_{t}}$ . The construction of VE-index on ${R}_{{v}_{t}}$ is analogous.

我们可采用GRainDB的方法在RG映射过程中构建图形索引。给定边关系${R}_{e}$及其关联的顶点关系${R}_{{v}_{s}}$和${R}_{{v}_{t}}$，可通过以下方式构建EV索引：在${R}_{e}$上为每个元组${\tau }_{e} \in  {R}_{e}$包含rid$\left( {{\lambda }_{e}^{s}\left( {\tau }_{e}\right) }\right)$和rid$\left( {{\lambda }_{e}^{t}\left( {\tau }_{e}\right) }\right)$（分别对应${R}_{{v}_{s}}$和${R}_{{v}_{t}}$中的元组行标识符）。同时，在${R}_{{v}_{s}}$上为每个元组${\tau }_{{v}_{s}} \in  {R}_{{v}_{s}}$构建VE索引时，需包含满足条件${\lambda }_{e}^{s}\left( {\tau }_{e}\right)  = {\tau }_{{v}_{s}}$的所有${\tau }_{e} \in  {R}_{e}$元组行标识符，以及满足${\lambda }_{e}^{t}\left( {\tau }_{e}\right)  = {\tau }_{{v}_{t}}$的对${\tau }_{{v}_{t}} \in  {R}_{{v}_{t}}$元组行标识符。${R}_{{v}_{t}}$上的VE索引构建方法与此类似。

3.2.2 The Graph-Aware Execution Plan. We delve into the physical implementation of the execution plan provided by the graph-aware method for solving $\mathcal{M}\left( \mathcal{P}\right)$ . The entry point of the plan is always matching a single-vertex pattern ${\mathcal{P}}_{u}$ ,which is one of the leaf nodes in the decomposition tree.

3.2.2 图感知执行计划。我们深入探究图感知方法为解决$\mathcal{M}\left( \mathcal{P}\right)$提供的执行计划物理实现。该计划的入口始终是匹配单顶点模式${\mathcal{P}}_{u}$，这是分解树中的叶节点之一。

The implementation of $\mathcal{M}\left( {\mathcal{P}}_{u}\right)$ is straightforward: scanning the corresponding vertex relation ${R}_{\ell \left( u\right) }$ and encoding each tuple as a graph vertex object that contains its ID,label (mandatory) and necessary attributes. The row ID of the tuple in the relation can be directly used as the ID. To ensure globally uniqueness, the name of the relation can be incorporated as a prefix of the ID. Advanced encoding techniques are necessary for production use, but they are beyond the scope of this paper.

$\mathcal{M}\left( {\mathcal{P}}_{u}\right)$的实现较为直接：扫描对应顶点关系${R}_{\ell \left( u\right) }$，将每个元组编码为包含ID（必填）、标签及必要属性的图顶点对象。关系中元组的行ID可直接用作ID。为确保全局唯一性，可将关系名称作为ID前缀。实际生产环境需要更高级的编码技术，但这已超出本文讨论范围。

The plan is then constructed in a bottom-up manner. As shown in Fig. 3, there are three fundamental cases to consider when implementing the plan.

随后以自底向上方式构建执行计划。如图3所示，计划实施时需要考虑三种基本情形。

Case I: Solving $\mathcal{M}\left( {\mathcal{P}}^{\prime }\right)  = \mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) {\widehat{ \sim  }}_{{V}_{o},{E}_{o}}\mathcal{M}\left( {\mathcal{P}}_{r}^{\prime }\right)$ ,where ${\mathcal{P}}_{l}^{\prime }$ and ${\mathcal{P}}_{r}^{\prime }$ are both intermediate patterns in the decomposition tree. The implementation of such a join is similar to a conventional relational join. The join is constrained to a natural join, where the join condition is simply the equality of the common vertices ${V}_{o}$ and edges ${E}_{o}$ between ${\mathcal{P}}_{l}^{\prime }$ and ${\mathcal{P}}_{r}^{\prime }$ . During the implementation of the join,the identifiers of the vertices and edges can serve as the keys for comparison. Note that the input and output of the join are both graph relations, which will not be projected into relational tuples until the last stage that obtains the results $\mathcal{M}\left( \mathcal{P}\right)$ .

案例一：求解$\mathcal{M}\left( {\mathcal{P}}^{\prime }\right)  = \mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) {\widehat{ \sim  }}_{{V}_{o},{E}_{o}}\mathcal{M}\left( {\mathcal{P}}_{r}^{\prime }\right)$，其中${\mathcal{P}}_{l}^{\prime }$与${\mathcal{P}}_{r}^{\prime }$均为分解树中的中间模式。此类连接的实现方式类似于传统关系型连接，限定为自然连接——连接条件仅为${\mathcal{P}}_{l}^{\prime }$与${\mathcal{P}}_{r}^{\prime }$之间公共顶点${V}_{o}$和边${E}_{o}$的等同性。连接实现过程中，顶点与边的标识符可用作比对键值。需注意该连接的输入输出均为图关系，直至最终获取结果$\mathcal{M}\left( \mathcal{P}\right)$的阶段才会投影为关系元组。

Case II: Solving $\mathcal{M}\left( {\mathcal{P}}^{\prime }\right)  = \mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) {\widehat{ \bowtie  }}_{{u}_{s}}\mathcal{M}\left( {\mathcal{P}}_{e}\right)$ ,where ${\mathcal{P}}_{e}$ is a single-edge pattern,and ${u}_{s}$ is the source vertex in ${\mathcal{P}}_{l}^{\prime }$ from which the edge $e = \left( {{u}_{s},{u}_{t}}\right)$ is expanded. Note that it’s not possible for both ${u}_{s}$ and ${u}_{t}$ to be in ${\mathcal{P}}_{l}^{\prime }$ ,as it would violate the fact that ${\mathcal{P}}_{l}^{\prime }$ is either a single vertex or an induced sub-pattern.

案例二：求解$\mathcal{M}\left( {\mathcal{P}}^{\prime }\right)  = \mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) {\widehat{ \bowtie  }}_{{u}_{s}}\mathcal{M}\left( {\mathcal{P}}_{e}\right)$，其中${\mathcal{P}}_{e}$为单边模式，${u}_{s}$是${\mathcal{P}}_{l}^{\prime }$中扩展出边$e = \left( {{u}_{s},{u}_{t}}\right)$的源顶点。需注意${u}_{s}$与${u}_{t}$不可能同时存在于${\mathcal{P}}_{l}^{\prime }$中，否则将违背${\mathcal{P}}_{l}^{\prime }$作为单顶点或诱导子模式的本质。

When there is no graph index, $\mathcal{M}\left( {\mathcal{P}}_{e}\right)$ is computed via ${R}_{\ell \left( {u}_{s}\right) }{ \bowtie  }_{\epsilon v}{R}_{\ell \left( e\right) }{ \bowtie  }_{\epsilon v}{R}_{\ell \left( {u}_{t}\right) }$ . This case is then reduced to Case I.

当不存在图索引时，$\mathcal{M}\left( {\mathcal{P}}_{e}\right)$通过${R}_{\ell \left( {u}_{s}\right) }{ \bowtie  }_{\epsilon v}{R}_{\ell \left( e\right) }{ \bowtie  }_{\epsilon v}{R}_{\ell \left( {u}_{t}\right) }$计算得出。此情形即归约为案例一。

When graph indexes exist, the implementation is handled by the physical operators of EXPAND_EDGE and GET_VERTEX. For each tuple $\tau  \in  \mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) ,\tau .{u}_{s}$ must record a graph vertex ${v}_{s}$ that matches ${u}_{s}$ in the pattern ${\mathcal{P}}_{l}^{\prime }$ . The EXPAND_EDGE operator looks up the VE-index of ${v}_{s}$ ,which allows it to efficiently computes ${v}_{s}$ ’s adjacent edges (more precisely,it’s the corresponding edge tuples). Furthermore, the GET_VERTEX operator is used to obtain the matched vertex ${v}_{t}$ that is connected to ${v}_{s}$ via the previous matched edges, which can be achieved by looking up the EV-index of the matched edges. By combining the results of EXPAND_EDGE and GET_VERTEX,the tuple of $\left( {\tau ,{\mathcal{N}}^{E}\left( {v}_{s}\right) ,\mathcal{N}\left( {v}_{s}\right) }\right)$ is rendered. For example,in Fig. 5(b),if we apply EXPAND_EDGE and GET_VERTEX to a tuple $\tau$ from ${v}_{{p}_{2}}$ ,the result $\left( {\tau ,\left\lbrack  {{e}_{{l}_{2}},{e}_{{l}_{3}}}\right\rbrack  ,\left\lbrack  {{v}_{{m}_{1}},{v}_{{m}_{2}}}\right\rbrack  }\right)$ is returned. Furthermore,to obtain $\mathcal{M}\left( {\mathcal{P}}^{\prime }\right)$ ,we flatten the adjacent edges and vertices and pair them up. In the case of $\left( {\tau ,\left\lbrack  {{e}_{{l}_{2}},{e}_{{l}_{3}}}\right\rbrack  ,\left\lbrack  {{v}_{{m}_{1}},{v}_{{m}_{2}}}\right\rbrack  }\right)$ ,two tuples $\left( {\tau ,{e}_{{l}_{2}},{v}_{{m}_{1}}}\right)$ and $\left( {\tau ,{e}_{{l}_{3}},{v}_{{m}_{2}}}\right)$ are generated.

当存在图索引时，其实现由物理操作符EXPAND_EDGE和GET_VERTEX处理。对于每个元组$\tau  \in  \mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) ,\tau .{u}_{s}$必须记录与模式${\mathcal{P}}_{l}^{\prime }$中${u}_{s}$匹配的图顶点${v}_{s}$。EXPAND_EDGE操作符会查找${v}_{s}$的VE索引，从而高效计算${v}_{s}$的相邻边（更准确地说，是相应的边元组）。接着，GET_VERTEX操作符用于获取通过先前匹配边连接到${v}_{s}$的匹配顶点${v}_{t}$，这可通过查找匹配边的EV索引实现。结合EXPAND_EDGE和GET_VERTEX的结果，最终生成$\left( {\tau ,{\mathcal{N}}^{E}\left( {v}_{s}\right) ,\mathcal{N}\left( {v}_{s}\right) }\right)$元组。例如图5(b)中，若对${v}_{{p}_{2}}$的元组$\tau$应用这两个操作符，将返回结果$\left( {\tau ,\left\lbrack  {{e}_{{l}_{2}},{e}_{{l}_{3}}}\right\rbrack  ,\left\lbrack  {{v}_{{m}_{1}},{v}_{{m}_{2}}}\right\rbrack  }\right)$。进一步地，为获取$\mathcal{M}\left( {\mathcal{P}}^{\prime }\right)$，我们将相邻边与顶点展平并配对。对于$\left( {\tau ,\left\lbrack  {{e}_{{l}_{2}},{e}_{{l}_{3}}}\right\rbrack  ,\left\lbrack  {{v}_{{m}_{1}},{v}_{{m}_{2}}}\right\rbrack  }\right)$的情况，会生成$\left( {\tau ,{e}_{{l}_{2}},{v}_{{m}_{1}}}\right)$和$\left( {\tau ,{e}_{{l}_{3}},{v}_{{m}_{2}}}\right)$两个元组。

In practice, a vertex may be adjacent to multiple types of edges. For example, in Fig. 2, a Person vertex can be connected to both Likes and Knows edges. To handle such cases, we can record edge's ID instead of just the row ID of the tuple. Given that the edge's ID is a combination of its label and the tuple's row ID, the adjacent edges of a specific label can be easily obtained from the VE-Index. Case III: Solving $\mathcal{M}\left( {\mathcal{P}}^{\prime }\right)  = \mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) {\widehat{ \bowtie  }}_{{V}_{s},{E}_{s}}\mathcal{M}\left( {\mathcal{P}\left( {u;{V}_{s}}\right) }\right)$ ,where pattern $\mathcal{P}\left( {u;{V}_{s}}\right)$ is a complete $k$ -star with ${V}_{s} = \left\{  {{u}_{1},\ldots ,{u}_{k}}\right\}$ .

实际应用中，一个顶点可能邻接多种边类型。例如图2中，Person顶点可同时连接Likes和Knows边。为此，我们可记录边的ID而非仅元组的行ID。由于边ID是其标签与元组行ID的组合，特定标签的相邻边可通过VE-Index轻松获取。案例III：求解$\mathcal{M}\left( {\mathcal{P}}^{\prime }\right)  = \mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) {\widehat{ \bowtie  }}_{{V}_{s},{E}_{s}}\mathcal{M}\left( {\mathcal{P}\left( {u;{V}_{s}}\right) }\right)$，其中模式$\mathcal{P}\left( {u;{V}_{s}}\right)$是带${V}_{s} = \left\{  {{u}_{1},\ldots ,{u}_{k}}\right\}$的完整$k$星型结构。

When there is no graph index,solving Case III involves continuously joining $\left| {V}_{s}\right|$ single-edge patterns. When graph indexes are available, the EXPAND_INTERSECT operator can be used to efficiently compute the join. Unlike HUGE [51], which has a graph storage that naturally supports EXPAND_INTERSECT, we have implemented this operator directly on a relational database. Given a tuple $\tau  \in  \mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right)$ ,let $\left\{  {{v}_{1},\ldots ,{v}_{k}}\right\}$ be the vertices in $\tau$ that match the leaf vertices $\left\{  {{u}_{1},\ldots ,{u}_{k}}\right\}$ in the complete star $\mathcal{P}\left( {u;{V}_{s}}\right)$ . Vertices matching the root vertex $u$ of the star must be common neighbors of all the leaf vertices.

当不存在图索引时，求解案例III需要持续连接$\left| {V}_{s}\right|$单边模式。若图索引可用，则可采用EXPAND_INTERSECT算子高效计算连接。与HUGE[51]不同（其图存储天然支持EXPAND_INTERSECT），我们直接在关系数据库上实现了该算子。给定元组$\tau  \in  \mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right)$，令$\left\{  {{v}_{1},\ldots ,{v}_{k}}\right\}$表示$\tau$中匹配完整星型$\mathcal{P}\left( {u;{V}_{s}}\right)$叶顶点$\left\{  {{u}_{1},\ldots ,{u}_{k}}\right\}$的顶点集。匹配星型根顶点$u$的顶点必须是所有叶顶点的共同邻居。

Consequently,for the tuple $\tau$ ,the physical EXPAND_INTERSECT operator performs the following steps:

因此，对于元组$\tau$，物理EXPAND_INTERSECT算子执行以下步骤：

(1) For each leaf vertex ${u}_{i} \in  {V}_{s}\left( {1 \leq  i \leq  k}\right)$ ,apply the EXPAND_EDGE

(1) 对每个叶顶点${u}_{i} \in  {V}_{s}\left( {1 \leq  i \leq  k}\right)$应用EXPAND_EDGE

and GET_VERTEX operators to obtain the adjacent edges and neighbors of the corresponding vertices ${v}_{i}$ respectively.

和GET_VERTEX算子，分别获取对应顶点${v}_{i}$的邻边与邻居。

(2) Compute the intersections of all adjacent edges and neighbors returned by the EXPAND_EDGE and GET_VERTEX operators.

(2) 计算EXPAND_EDGE与GET_VERTEX算子返回的所有邻边及邻居的交集。

(3) Return a new tuple as follows; for the sake of simplicity, the details of the edges are omitted: $\left( {\tau ,\mathop{\bigcap }\limits_{{1 \leq  i \leq  k}}\mathcal{N}\left( {v}_{i}\right) }\right)$

(3) 返回如下新元组（为简化表述省略边细节）：$\left( {\tau ,\mathop{\bigcap }\limits_{{1 \leq  i \leq  k}}\mathcal{N}\left( {v}_{i}\right) }\right)$

Note that the above step (1) and (2) can be computed in a pipeline manner, following a certain order of among the leaf vertices. Similar to Case II, we flatten the common edges and vertices and pair them up to obtain the final result.

注意上述步骤(1)(2)可沿叶顶点特定顺序以流水线方式计算。类似案例II，我们将公共边与顶点展平并配对以获得最终结果。

Example 5. Given $\mathcal{P}$ in Fig. 3,a decomposition tree and its corresponding logical plan are presented. We illustrate the physical implementation of $\mathcal{M}\left( {\mathcal{P}}_{1}\right) \widehat{ \boxtimes  }\mathcal{M}\left( {\mathcal{P}}_{2}\right)$ using EXPAND_INTERSECTwhen a graph index is available. Consider the tuple $\left( {{v}_{{p}_{1}},{e}_{{k}_{1}},{v}_{{p}_{2}}}\right)$ from $\mathcal{M}\left( {\mathcal{P}}_{1}\right)$ as an example. First,the EXPAND_EDGE and GET_VERTEX operators are applied to obtain the adjacent edges and neighbors of ${v}_{{p}_{1}}$ and ${v}_{{p}_{2}}$ ,resulting in

示例5. 给定图3中$\mathcal{P}$，展示其分解树及对应逻辑计划。我们说明图索引可用时$\mathcal{M}\left( {\mathcal{P}}_{1}\right) \widehat{ \boxtimes  }\mathcal{M}\left( {\mathcal{P}}_{2}\right)$采用EXPAND_INTERSECT的物理实现。以$\mathcal{M}\left( {\mathcal{P}}_{1}\right)$中的元组$\left( {{v}_{{p}_{1}},{e}_{{k}_{1}},{v}_{{p}_{2}}}\right)$为例：首先应用EXPAND_EDGE和GET_VERTEX算子获取${v}_{{p}_{1}}$与${v}_{{p}_{2}}$的邻边及邻居，得到

$$
\left( {{v}_{{p}_{1}},{e}_{{k}_{1}},{v}_{{p}_{2}},\left\lbrack  {e}_{{l}_{1}}\right\rbrack  ,\left\lbrack  {v}_{{m}_{1}}\right\rbrack  }\right) \text{and}\left( {{v}_{{p}_{1}},{e}_{{k}_{1}},{v}_{{p}_{2}},\left\lbrack  {{e}_{{l}_{2}},{e}_{{l}_{3}}}\right\rbrack  ,\left\lbrack  {{v}_{{m}_{1}},{v}_{{m}_{2}}}\right\rbrack  }\right) \text{.}
$$

Next,the intersection process is conducted. Since $\mathcal{N}\left( {v}_{{p}_{1}}\right)  \cap  \mathcal{N}\left( {v}_{{p}_{2}}\right)  = \left\lbrack  {v}_{{m}_{1}}\right\rbrack$ ,the edges in both sets that have ${v}_{{m}_{1}}$ as the target vertex are retained,resulting in $\left( {{v}_{{p}_{1}},{e}_{{k}_{1}},{v}_{{p}_{2}},\left\lbrack  \left( {{e}_{{l}_{1}},{e}_{{l}_{2}},{v}_{{m}_{1}}}\right) \right\rbrack  }\right)$ . Finally,the tuple is flattened to $\left( {{v}_{{p}_{1}},{e}_{{k}_{1}},{v}_{{p}_{2}},{e}_{{l}_{1}},{e}_{{l}_{2}},{v}_{{m}_{1}}}\right)$ .

随后执行交集运算。由于$\mathcal{N}\left( {v}_{{p}_{1}}\right)  \cap  \mathcal{N}\left( {v}_{{p}_{2}}\right)  = \left\lbrack  {v}_{{m}_{1}}\right\rbrack$，保留两个集合中以${v}_{{m}_{1}}$为目标顶点的边，得到$\left( {{v}_{{p}_{1}},{e}_{{k}_{1}},{v}_{{p}_{2}},\left\lbrack  \left( {{e}_{{l}_{1}},{e}_{{l}_{2}},{v}_{{m}_{1}}}\right) \right\rbrack  }\right)$。最终将该元组展平为$\left( {{v}_{{p}_{1}},{e}_{{k}_{1}},{v}_{{p}_{2}},{e}_{{l}_{1}},{e}_{{l}_{2}},{v}_{{m}_{1}}}\right)$。

## 4 The Converged Optimization Framework

## 4 融合优化框架

This section presents RelGo, a converged relational/graph optimization framework designed to optimize the query processing of SPJM queries. We begin by introducing a naive solution built upon the graph-agnostic method for solving the matching operator. We then delve into the converged workflow of RelGo, which leverages the graph-aware method for solving the matching operator and introduces a complete workflow that aims to integrate techniques from both relational and graph optimization modules.

本节提出RelGo——一个融合关系/图模型的优化框架，用于优化SPJM查询处理。我们首先介绍基于图无关方法求解匹配算子的基础方案，继而深入RelGo的融合工作流：该方法采用图感知技术求解匹配算子，并通过整合关系型与图优化模块技术构建完整工作流。

<!-- Media -->

<!-- figureText: Projection Core Workflow Projection PROJECTION p2.name place.name p2.name place.name Join Germany Place HASH_JOIN (place_id, name) p1.place_id = place_id China p1.place_id = place_id SCAN_TABLE Place PROJECTION* PROJECTION* p1.place_id p2.name Relational p1.place_id p2.name Optimization EXPAND_INTERSECT m EXPAND_INTERSECT IT EXPAND p2 ○ EXPAND p2 ○ ○ SCAN p1 (name = "Tom") ◯ SCAN p1 (name = "Tom") ◯ SCAN_GRAPH_TABLE SCAN_GRAPH_TABLE (c) SPJ with Scan Graph Table (d) Optimized Execution Plan p2.name place.name ${P}_{1}O - O$ Projection Selection p2.name place.name p1.name = "Tom" Join p1.place_id = place_id p1.place_id = place_id FilterIntoMatch place_id name Projection Optimization $p{l}_{1}$ Germany p1.place_id p1.name $p{l}_{1}$ Germany p2.name China GET_VERTEX p1.place_id Denmark Place -① p2.name China Match TrimAndFuse Match C. (b) Optimized SPJM ${\mathcal{P}}_{3}$ ○ (a) SPJM -->

<img src="https://cdn.noedgeai.com/0196599e-8e15-7d66-a6ce-e26720ffa9f7_13.jpg?x=137&y=262&w=1294&h=488&r=0"/>

Fig. 6. The converged optimization workflow

图6. 收敛优化工作流程

<!-- Media -->

### 4.1 Graph-Agnostic Approach

### 4.1 图无关方法

The graph-agnostic approach is straightforward: it applies the graph-agnostic transformation for the matching operator in an SPJM query into a series of relational operations (Lemma 1), effectively converting the SPJM query into an SPJ query. The resulting SPJ query can then be optimized by any existing relational optimizer, producing an execution plan. As an improvement, if a graph index (Sec. 3.2.1) is available, certain hash-join operators in the execution plan can be replaced by the predefined-join operator, as discussed in GRainDB [23]. The main advantage of this solution is its easy integration with any existing relational database. However, it suffers from two significant drawbacks discussed in Remark 1.

图无关方法直截了当：它将SPJM查询中的匹配运算符应用图无关转换，转化为一系列关系运算（引理1），从而有效地将SPJM查询转换为SPJ查询。生成的SPJ查询随后可由任何现有关系优化器进行优化，生成执行计划。作为一种改进，若存在图索引（见第3.2.1节），执行计划中的某些哈希连接运算符可替换为预定义连接运算符，如GRainDB[23]所述。该解决方案的主要优势在于易于与任何现有关系数据库集成。然而，如备注1所述，它存在两个显著缺陷。

### 4.2 The Converged Approach

### 4.2 融合式方法

As illustrated in Fig. 6, the core workflow of the RelGo framework consists of two components: graph optimization and relational optimization. The graph optimization is responsible for handling the graph component in an SPJM query, leveraging graph optimization techniques to determine the optimal decomposition tree of the matching operator. On the other hand, the relational optimization takes over to optimize the relational component in the query. The order in which these two components are applied is not strictly defined. However, for the purpose of our discussion, we will first focus on the graph optimization and then proceed to the relational optimization. In addition to the core workflow, we further explore heuristic rules that highlight the non-trivial interplay between the relational and graph components in an SPJM query.

如图6所示，RelGo框架的核心工作流包含两个组件：图优化和关系优化。图优化负责处理SPJM查询中的图结构组件，运用图优化技术确定匹配算子的最优分解树；关系优化则负责优化查询中的关系组件。这两个组件的应用顺序并无严格规定。为便于讨论，我们将先聚焦图优化，再探讨关系优化。除核心工作流外，我们还将深入探索启发式规则，这些规则揭示了SPJM查询中关系组件与图组件之间非平凡的交互作用。

4.2.1 The Graph Optimization. We adopt the graph optimization techniques developed in GLogS [29]. However, it is crucial to note that GLogS was originally designed for native graph data, whereas our framework deals with relational data, which necessitates a careful adaptation of GLogS's techniques to the relational setting.

4.2.1 图优化。我们采用GLogS[29]开发的图优化技术。但需特别注意，GLogS最初是为原生图数据设计的，而本框架处理的是关系型数据，因此需要谨慎地将GLogS技术适配到关系型场景。

GLogue Construction. GLogS is built upon a data structure called GLogue, which is essentially a graph ${\mathbb{G}}_{\mathcal{P}}\left( {V,E}\right)$ . In this graph,each vertex represents a pattern ${\mathcal{P}}^{\prime }$ consisting of up to $k$ vertices (typically, $k = 3$ ) that has non-empty matched instances in the original graph. There is an edge from ${\mathcal{P}}^{\prime \prime }$ to ${\mathcal{P}}^{\prime }$ ,if there is a decomposition tree where ${\mathcal{P}}^{\prime \prime }$ is a child node of ${\mathcal{P}}^{\prime }$ .

GLogue构建。GLogS基于名为GLogue的数据结构实现，该结构本质上是图${\mathbb{G}}_{\mathcal{P}}\left( {V,E}\right)$。图中每个顶点代表一个模式${\mathcal{P}}^{\prime }$，该模式由最多$k$个顶点（通常为$k = 3$）组成，且在原图中具有非空匹配实例。若存在${\mathcal{P}}^{\prime \prime }$作为${\mathcal{P}}^{\prime }$子节点的分解树，则从${\mathcal{P}}^{\prime \prime }$到${\mathcal{P}}^{\prime }$存在边。

Each vertex ${\mathcal{P}}^{\prime }$ in GLogue maintains $\left| {\mathcal{M}\left( {\mathcal{P}}^{\prime }\right) }\right|$ ,denoting the cardinality of the pattern. To reduce computation costs,GLogS employs a sparsification technique to construct a subgraph ${G}^{\prime }$ . The pattern cardinality can then be estimated using $\left| {{\mathcal{M}}_{{G}^{\prime }}\left( {\mathcal{P}}^{\prime }\right) }\right|$ based on subgraph ${G}^{\prime }$ . In our work,we adapt this sparsification technique to construct GLogue. We sample a subset of vertex and edge relations in the RGMapping process. Once the subset of relations is obtained, they can serve as the input tables to the techniques presented in [45] for constructing the sparsified graph ${G}^{\prime }$ .

GLogue中每个顶点${\mathcal{P}}^{\prime }$维护着$\left| {\mathcal{M}\left( {\mathcal{P}}^{\prime }\right) }\right|$，表示模式的基数。为降低计算成本，GLogS采用稀疏化技术构建子图${G}^{\prime }$，随后基于子图${G}^{\prime }$使用$\left| {{\mathcal{M}}_{{G}^{\prime }}\left( {\mathcal{P}}^{\prime }\right) }\right|$估算模式基数。本工作中，我们改进该稀疏化技术来构建GLogue。在RGMapping过程中对顶点和边关系进行抽样，所得关系子集可作为输入表，应用文献[45]所述技术构建稀疏化图${G}^{\prime }$。

Cost Calculation. The optimization process is essentially searching for the execution plan that incurs the minimal cost. Let the cost of an execution plan $\Phi$ for computing $\mathcal{M}\left( \mathcal{P}\right)$ be ${\operatorname{Cost}}_{\Phi }\left( \mathcal{P}\right)$ .

成本计算。优化过程本质是寻找成本最低的执行计划。设计算$\mathcal{M}\left( \mathcal{P}\right)$的执行计划$\Phi$成本为${\operatorname{Cost}}_{\Phi }\left( \mathcal{P}\right)$。

Consider $\mathcal{M}\left( {\mathcal{P}}^{\prime }\right)  = \mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) \widehat{ \bowtie  }\mathcal{M}\left( {\mathcal{P}}_{r}^{\prime }\right)$ as an intermediate computation in an execution plan. We have:

将$\mathcal{M}\left( {\mathcal{P}}^{\prime }\right)  = \mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) \widehat{ \bowtie  }\mathcal{M}\left( {\mathcal{P}}_{r}^{\prime }\right)$视为执行计划中的中间计算，可得：

$$
{\operatorname{Cost}}_{\Phi }\left( {\mathcal{P}}^{\prime }\right)  = {\operatorname{Cost}}_{{\Phi }_{l}}\left( {\mathcal{P}}_{l}^{\prime }\right)  + {\operatorname{Cost}}_{{\Phi }_{r}}\left( {\mathcal{P}}_{r}^{\prime }\right)  + \operatorname{Cost}\left( \widehat{ \bowtie  }\right) ,
$$

where ${\Phi }_{l}$ and ${\Phi }_{r}$ are the execution plans for computing $\mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right)$ and $\mathcal{M}\left( {\mathcal{P}}_{r}^{\prime }\right)$ ,respectively,and $\operatorname{Cost}\left( \widehat{ \bowtie  }\right)$ is the cost of the join operation.

其中${\Phi }_{l}$和${\Phi }_{r}$分别是计算$\mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right)$与$\mathcal{M}\left( {\mathcal{P}}_{r}^{\prime }\right)$的执行计划，$\operatorname{Cost}\left( \widehat{ \bowtie  }\right)$为连接操作成本。

When a graph index is available,there are three physical implementations of $\widehat{ \otimes  }$ ,depending on the type of ${\mathcal{P}}_{r}^{\prime }$ ,and the calculation of $\operatorname{Cost}\left( \widehat{ \bowtie  }\right)$ differs accordingly:

当存在图索引时，$\widehat{ \otimes  }$有三种物理实现方式，具体取决于${\mathcal{P}}_{r}^{\prime }$类型，$\operatorname{Cost}\left( \widehat{ \bowtie  }\right)$的计算方式也相应变化：

- If ${\mathcal{P}}_{r}^{\prime }$ is a single-edge pattern, $\widehat{ \bowtie  }$ is implemented using the EXPAND_EDGE operator followed by GET_VERTEX. The cost is calculated based on the cardinality of $\mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right)$ (can be looked up in the GLogue) and the average degree of the graph,namely $\left| {\mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) }\right|  \times  \bar{d}$ .

- 若${\mathcal{P}}_{r}^{\prime }$为单边模式，则$\widehat{ \bowtie  }$通过EXPAND_EDGE算子后接GET_VERTEX算子实现。其成本根据$\mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right)$的基数（可查阅GLogue）与图的平均度数$\left| {\mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) }\right|  \times  \bar{d}$计算得出。

- If ${\mathcal{P}}_{r}^{\prime }$ is a complete star pattern, $\widehat{ \bowtie  }$ is implemented using the EXPAND_INTERSECT operator. The cost is calculated based on the cardinality of $\mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right)$ and the average intersection size of the neighbors of the vertices being intersected, which is maintained on the corresponding edge from ${P}^{\prime }$ to ${\mathcal{P}}_{l}^{\prime }$ in GLogue.

- 若${\mathcal{P}}_{r}^{\prime }$为完整星型模式，则$\widehat{ \bowtie  }$通过EXPAND_INTERSECT算子实现。其成本基于$\mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right)$的基数与被交叠顶点邻居的平均交叠规模计算，该数值存储在GLogue中从${P}^{\prime }$到${\mathcal{P}}_{l}^{\prime }$的对应边上。

- If ${\mathcal{P}}_{r}^{\prime }$ is any arbitrary pattern, $\widehat{ \bowtie  }$ is implemented as a HASH_JOIN. The cost is calculated as the product of the cardinalities of the two relations being joined,i.e., $\operatorname{Cost}\left( \widehat{ \bowtie  }\right)  = \left| {\mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) }\right|  \times  \left| {\mathcal{M}\left( {\mathcal{P}}_{r}^{\prime }\right) }\right|$ .

- 若${\mathcal{P}}_{r}^{\prime }$为任意模式，则$\widehat{ \bowtie  }$通过HASH_JOIN实现。其成本按连接的两个关系的基数乘积$\operatorname{Cost}\left( \widehat{ \bowtie  }\right)  = \left| {\mathcal{M}\left( {\mathcal{P}}_{l}^{\prime }\right) }\right|  \times  \left| {\mathcal{M}\left( {\mathcal{P}}_{r}^{\prime }\right) }\right|$计算。

In the absence of a graph index, HASH_JOIN is used for the entire plan of the matching operator for simplicity, and its cost is computed as the product of the cardinalities of the two relations being joined. Although other physical join implementations, such as nested loop join, may be more effective if the join condition is not selective, considering these alternatives is planned for future work.

若无图索引，为简化起见，匹配算子的整个执行计划将采用HASH_JOIN，其成本按连接的两个关系的基数乘积计算。虽然当连接条件不具备选择性时，嵌套循环连接等其他物理连接实现可能更高效，但考虑这些替代方案将留待后续工作。

Plan Computation. Searching for the optimal execution plan in RelGo remains the same as in GLogS. The optimal plan is obtained by searching for the shortest path in the GLogue from the single-vertex pattern to the queried pattern. Fig. 6(c) demonstrates a physical plan for matching the given triangle pattern when a graph index is present. The plan reflects the example in Example 5, with one exception: the pair of EXPAND_EDGE and GET_VERTEX operators is fused into a single EXPAND operator, which will be discussed as a heuristic rule called TrimAndFuseRule.

计划计算。RelGo中寻找最优执行计划的过程与GLogS相同，通过在GLogue中搜索从单顶点模式到查询模式的最短路径获得最优计划。图6(c)展示了存在图索引时匹配给定三角形模式的物理计划。该计划与示例5基本对应，唯一区别在于：EXPAND_EDGE与GET_VERTEX算子对被融合为单个EXPAND算子，这将作为名为TrimAndFuseRule的启发式规则讨论。

4.2.2 The Relational Optimization. Once the graph optimizer has computed the optimal execution plan for $\mathcal{M}\left( \mathcal{P}\right)$ ,the next step is to integrate this plan with the remaining relational operators in the SPJM query. The relational optimization is responsible for optimizing these remaining operators, which are all relational operators. Relational optimization has evolved into a well-established field, producing numerous significant results $\left\lbrack  {{11},{18}}\right\rbrack$ . Since existing relational optimization techniques can be seamlessly integrated into RelGo, we will focus on how graph optimization techniques can be applied to enhance relational queries.

4.2.2 关系优化。当图优化器计算出$\mathcal{M}\left( \mathcal{P}\right)$的最优执行计划后，下一步是将该计划与SPJM查询中的其余关系算子集成。关系优化负责优化这些剩余的关系算子。关系优化已发展成成熟领域，并产生诸多重要成果$\left\lbrack  {{11},{18}}\right\rbrack$。由于现有关系优化技术可无缝集成至RelGo，我们将重点探讨如何应用图优化技术增强关系查询。

Specifically, to prevent the relational optimizer delve into the internal details of the graph pattern matching process, we introduce a new physical operator called SCAN_GRAPH_TABLE, as shown in Fig. 6(c),which encapsulates the ${\widehat{\pi }}_{A * }$ operator and the optimal execution plan for $\mathcal{M}\left( \mathcal{P}\right)$ . The SCAN_GRAPH_TABLE operator acts as a bridge between the graph and relational components of the query. From the perspective of the relational optimizer, SCAN_GRAPH_TABLE behaves like a standard SCAN operator, providing a relational interface to the matched results.

具体而言，为避免关系优化器深入图模式匹配过程的内部细节，我们引入名为SCAN_GRAPH_TABLE的新物理算子（如图6(c)所示），该算子封装了${\widehat{\pi }}_{A * }$算子及$\mathcal{M}\left( \mathcal{P}\right)$的最优执行计划。SCAN_GRAPH_TABLE算子充当查询中图组件与关系组件的桥梁。从关系优化器视角看，其行为类似标准SCAN算子，为匹配结果提供关系型接口。

4.2.3 Heuristic Optimization Rules. In real-life use cases, heuristic rules may involve non-trivial interactions between the relational and graph components of an SPJM query. We explore two representative rules, FilterIntoMatchRule and TrimAndFuseRule, which can be applied at different stages of the optimization process to improve query performance.

4.2.3 启发式优化规则。在实际用例中，启发式规则可能涉及SPJM查询的关系组件与图组件间的复杂交互。我们研究两个代表性规则——FilterIntoMatchRule与TrimAndFuseRule，它们可在优化过程的不同阶段应用以提升查询性能。

FilterIntoMatchRule. To elaborate the rule,we extend the definition of a pattern $\left( {\mathcal{P},\Psi }\right)$ ,introducing constraints within $\Psi$ . For example,constraints can specify predicate $d$ such as $\operatorname{id}\left( {v}_{1}\right)  = {p}_{1}$ for a vertex ${v}_{1}$ ,or ${e}_{1}$ .date $>$ "2024-03-31" for an edge ${e}_{1}$ . With the constraints defined,any matching result of $\mathcal{P}$ must have the corresponding vertices and edges adhering to the predicates.

过滤转入匹配规则。为阐述该规则，我们扩展了模式$\left( {\mathcal{P},\Psi }\right)$的定义，在$\Psi$中引入约束条件。例如，约束可指定谓词$d$，如为顶点${v}_{1}$设置$\operatorname{id}\left( {v}_{1}\right)  = {p}_{1}$，或为边${e}_{1}$设置日期$>$"2024-03-31"。定义约束后，$\mathcal{P}$的任何匹配结果都必须使相应顶点和边符合这些谓词。

While writing queries, users may not specify constraints on the pattern but rather use the selection operator after matching results have been projected into the relational relation, described as:

编写查询时，用户可能不会直接指定模式约束，而是在匹配结果投影到关系表后使用选择运算符，表述如下：

$$
{\sigma }_{{d}_{{v}_{a}}^{\prime }}\left( {{\widehat{\pi }}_{v.a \rightarrow  \mathrm{v}\_ \mathrm{a},\ldots }\mathcal{M}\left( \mathcal{P}\right) }\right) 
$$

The predicate ${d}_{{v}_{a}}^{\prime }$ defines a predicate in terms of an attribute of the pattern vertex that is projected by $\widehat{\pi }$ from the matched results. The motivation example in Example 1 illustrates such a case,where the selection predicate $\mathrm{g}$ . p1_name $=$ "Tom" is applied to the pattern vertex ${v}_{{p}_{1}}$ . There is wasteful computation if the selection is applied after the costly pattern matching. A more efficient approach is to push the selection predicate down into the matching operator. The FilterIntoMatchRule is formally defined as:

谓词${d}_{{v}_{a}}^{\prime }$通过模式顶点的属性定义谓词，该属性由$\widehat{\pi }$从匹配结果中投影。示例1的动机案例展示了这种情况：选择谓词$\mathrm{g}$.p1_name$=$"Tom"被应用于模式顶点${v}_{{p}_{1}}$。若在耗时的模式匹配后才应用选择操作会产生冗余计算。更高效的方法是将选择谓词下推到匹配运算符中。过滤转入匹配规则正式定义为：

$$
{\sigma }_{\Psi }\left( {{\widehat{\pi }}_{v.a \rightarrow  \mathrm{v}\_ \mathrm{a},\ldots }\mathcal{M}\left( \mathcal{P}\right) }\right)  \equiv  {\sigma }_{{\Psi }^{\prime }}\left( {{\widehat{\pi }}_{v.a \rightarrow  \mathrm{v}\_ \mathrm{a},\ldots }\mathcal{M}\left( \left( {\mathcal{P},\left\{  {d}_{v}\right\}  }\right) \right) }\right) ,
$$

where ${\Psi }^{\prime } = \Psi  \smallsetminus  \left\{  {d}_{{v}_{a}}^{\prime }\right\}$ ,and $\left\{  {d}_{v}\right\}$ is the corresponding constraints that are appended to the pattern $\mathcal{P}$ .

其中${\Psi }^{\prime } = \Psi  \smallsetminus  \left\{  {d}_{{v}_{a}}^{\prime }\right\}$，且$\left\{  {d}_{v}\right\}$是附加到模式$\mathcal{P}$的对应约束条件。

It is recommended to apply the FilterIntoMatchRule before graph optimization, as this allows the optimizer to leverage the pushed-down constraints to recalculate the cost, potentially generating more efficient execution plans. Fig. 6(b) showcases the effects of applying the FilterIntoMatchRule, where the selection predicate g.p1_name = "Tom" is pushed down into the matching operator. TrimAndFuseRule. The TrimAndFuseRule is utilized to streamline a query plan by merging the EXPAND_EDGE and GET_VERTEX operators which are commonly coupled in the implementation of matching operations, into a single EXPAND operator that retrieves the neighboring vertices directly. However, such a fusion is permissible solely when the output edges by EXPAND_EDGE are deemed unnecessary, so this rule further incorporates a preceded field trim step. Specifically, the field trimmer would examine whether any subsequent relational processes rely on these edges, such as utilizing them for property projections or for filtering based on their attributes. If no such operations are found, the edges can be trimmed. Furthermore, the field trimmer would also consider a special case that the edges might be projected in the SCAN_GRAPH_TABLE operator as part of the matching results, but are subsequently unused in relational operations. In such cases, the edges can be trimmed as well. After the field trim step, if the output edges are trimmed, the EXPAND_EDGE operator can be fused with the GET_VERTEX operator to form a single EXPAND operator, which can directly retrieve the neighboring vertices efficiently by looking up the VE-index of the source vertex when the graph index is available.

建议在图优化前应用过滤转入匹配规则，这样优化器可利用下推的约束重新计算成本，可能生成更高效的执行计划。图6(b)展示了应用该规则的效果：选择谓词g.p1_name="Tom"被下推到匹配运算符中。修剪融合规则。该规则通过合并EXPAND_EDGE和GET_VERTEX运算符（它们在匹配操作实现中常成对出现）为单个EXPAND运算符来简化查询计划，该运算符可直接获取相邻顶点。但仅当EXPAND_EDGE输出的边被判定为不必要时才允许融合，因此该规则还前置了字段修剪步骤。具体而言，字段修剪器会检查后续关系处理是否依赖这些边（如用于属性投影或基于边属性的过滤）。若未发现此类操作，则可修剪边。此外，修剪器还会考虑特殊情况：边可能在SCAN_GRAPH_TABLE运算符中作为匹配结果被投影，但后续在关系操作中未被使用。此类情况下边也可被修剪。完成字段修剪后，若输出边被修剪，则EXPAND_EDGE运算符可与GET_VERTEX运算符融合为单个EXPAND运算符——当图索引可用时，该运算符能通过查找源顶点的VE索引直接高效获取相邻顶点。

Note that FilterIntoMatchRule is actually a global optimization rule because there are cases where pushing the predicate into the matching operator does not always yield better plans[35]. However, since it is mostly effective, we greedily apply FilterIntoMatchRule in the current version. A more comprehensive evaluation of this rule will be conducted in future work. On the other hand, TrimAndFuseRule is a local optimization rule specifically designed for graph optimization. The effectiveness of these two rules is validated in Sec. 5.2. Our RelGo framework is designed to be generic, allowing different optimization rules to be easily integrated.

需注意过滤转入匹配规则实际是全局优化规则，因为存在将谓词下推到匹配运算符并不总能产生更优计划的情况[35]。但由于其多数情况下有效，我们在当前版本中贪婪地应用该规则。未来工作将对此规则进行更全面评估。另一方面，修剪融合规则是专为图优化设计的局部优化规则。第5.2节验证了这两条规则的有效性。我们的RelGo框架设计具有通用性，可轻松集成不同的优化规则。

### 4.3 System Implementation

### 4.3 系统实现

We engineered the frontend of RelGo in Java and built it upon Apache Calcite [17] to utilize its robust relational query optimization infrastructure. Firstly, we enhanced Calcite's SQL parser to recognize SQL/PGQ extensions, specifically to parse the GRAPH_TABLE clause. We created a new ScanGraphTableRelNode that inherits from Calcite's core RelNode class, translating the GRAPH_TABLE clause into this newly defined operator within the logical plan. Following the formation of the logical plan, the frontend invokes the converged optimizer to generate the optimal physical plan. For the relational-graph interplay optimizations, we incorporate heuristic rules such as FilterIntoMatchRule and TrimAndFuseRule into Calcite's rule-based HepPlanner, by specifying the activation conditions and consequent transformations of each rule. For more nuanced optimization, we rely on the VolcanoPlanner, the cost-based planner in Calcite, to optimize the ScanGraphTableRelNode. We devised a top-down search algorithm that assesses the most efficient physical plan based on a cost model outlined in Sec. 4.2.1, combined with high-order statistics from GLogue for more accurate cost estimation. While low-order statistics primarily focus on the cardinalities of relational tables, high-order statistics also include the frequencies of sub-patterns (can be seen as the joined results of multiple tables of vertices and edges), which aids in more accurate cost estimation. It is important to note that RelGo remains functional with only low-order statistics, but the efficiency of the generated plan may decrease due to less accurate cost estimation.

我们使用Java设计了RelGo的前端，并基于Apache Calcite[17]构建，以利用其强大的关系查询优化基础设施。首先，我们扩展了Calcite的SQL解析器以识别SQL/PGQ扩展，特别是解析GRAPH_TABLE子句。创建了继承自Calcite核心RelNode类的新ScanGraphTableRelNode，将GRAPH_TABLE子句转换为逻辑计划中的新定义运算符。形成逻辑计划后，前端调用融合优化器生成最优物理计划。针对关系-图交互优化，我们在Calcite基于规则的HepPlanner中加入了FilterIntoMatchRule和TrimAndFuseRule等启发式规则，通过指定各规则的触发条件及相应转换。对于更精细的优化，依赖Calcite基于成本的VolcanoPlanner来优化ScanGraphTableRelNode。我们设计了一种自上而下的搜索算法，基于第4.2.1节所述的成本模型评估最有效物理计划，并结合GLogue的高阶统计量提升成本估算精度。需注意低阶统计量主要关注关系表基数，而高阶统计量还包含子模式频率（可视为多张顶点边表连接结果），这有助于更精准的成本估算。RelGo仅凭低阶统计量仍可运行，但生成计划的效率可能因成本估算精度下降而降低。

For the remaining relational operators in the query, we leverage Calcite's built-in optimizer, which already includes comprehensive relational optimization techniques. Lastly, the converged optimizer outputs an optimized and platform-independent plan formatted with Google Protocol Buffers (protobuf) [42], ensuring the adaptability of RelGo's output to various backend database systems.

对于查询中的其余关系运算符，我们利用Calcite内置优化器（已包含全面的关系优化技术）。最终，融合优化器输出采用Google Protocol Buffers（protobuf）[42]格式的优化后平台无关计划，确保RelGo输出可适配各类后端数据库系统。

We developed the RelGo framework’s backend in C++ using DuckDB as the relational execution engine to showcase its optimization capabilities. We integrated graph index support in GRainDB [23]. With graph index, the EXPAND, EXPAND_EDGE and GET_VERTEX operators can be optimized by directly using the predefined join in GRainDB. Note that we craft a new join on DuckDB called EI-Join for the support of EXPAND_INTERSECT. Without graph index, the HASH_JOIN operator is used throughout the entire plan. To execute the optimized plans within DuckDB, we introduced a runtime module that translates the optimized physical plan into a sequence of DuckDB/GRainDB-compatible executable operators. This runtime module essentially bridges the gap between the optimized plans produced by RelGo and DuckDB's execution engine, thereby validating RelGo's practicality and potential performance improvements for SPJM queries on an established relational database system.

我们使用C++开发RelGo框架后端，采用DuckDB作为关系执行引擎以展示其优化能力。在GRainDB[23]中集成了图索引支持。借助图索引，EXPAND、EXPAND_EDGE和GET_VERTEX运算符可通过直接使用GRainDB预定义连接进行优化。值得注意的是，我们在DuckDB上创新实现了名为EI-Join的新型连接以支持EXPAND_INTERSECT。若无图索引，则全程使用HASH_JOIN运算符。为在DuckDB中执行优化计划，我们开发了运行时模块，将优化后的物理计划转换为DuckDB/GRainDB兼容的可执行运算符序列。该模块本质上弥合了RelGo产生的优化计划与DuckDB执行引擎间的鸿沟，从而验证了RelGo在成熟关系数据库系统上处理SPJM查询的实用性和潜在性能提升。

## 5 Evaluation

## 5 评估

### 5.1 Experimental Settings

### 5.1 实验设置

Benchmarks. Our experiments leverage two widely used benchmarks to assess system performance, as follows:

基准测试。我们采用两个广泛使用的基准来评估系统性能：

LDBC SNB. We use ${LDBC10},{LDBC30}$ ,and ${LDBC100}$ with scale factors of 10,30,and 100,generated by the official LDBC Data Generator. These datasets were chosen because they can be accommodated in the main memory of a single configured machine. We select 10 queries from the LDBC Interactive workload for evaluation,denoted as ${\mathrm{{IC}}}_{1,\ldots ,9,{11},{12}}$ ,with 10,13,and 14 excluded since they involve either pre-computation or shortest-path that are not supported. To accommodate queries containing variable-length paths [23], we followed [23] to slightly modify them by separating each query into multiple individual queries with fixed-length paths. Each of these modified queries is denoted with a suffix "-l",where $l$ represents the length of the fixed-length path. In addition,we carefully designed two sets of queries for the comprehensiveness of evaluation,including (1) $Q{R}_{1\ldots 4}$ to test the effectiveness of FilterIntoMatchRule and TrimAndFuseRule in RelGo,and (2) $Q{C}_{1\ldots 3}$ ,comprising three typical patterns with cycles including triangle, square, and 4-clique, to assess the efficiency of EXPAND_INTERSECT introduced in Sec. 3.2.

LDBC SNB。使用官方LDBC数据生成器生成的${LDBC10},{LDBC30}$和${LDBC100}$数据集，比例因子为10、30和100。选择这些数据集因其可适配单台配置机器的内存容量。从LDBC交互式工作负载中选取10个查询进行评估（标记为${\mathrm{{IC}}}_{1,\ldots ,9,{11},{12}}$），排除涉及预计算或不支持最短路径的10、13和14号查询。为适配含可变长度路径的查询[23]，按[23]方法将其拆分为多个固定长度路径查询，每个修改后查询用后缀"-l"标记，$l$表示固定路径长度。此外，为评估全面性精心设计两组查询：(1)$Q{R}_{1\ldots 4}$用于测试RelGo中FilterIntoMatchRule和TrimAndFuseRule的有效性；(2)$Q{C}_{1\ldots 3}$包含三角形、方形和4-团三种典型循环模式，用于评估第3.2节介绍的EXPAND_INTERSECT效率。

JOB. The Join Order Benchmark (JOB) [31] on Internet Movie Database (IMDB) is adopted. We select the variants marked with "a" of all JOB queries,referred to as ${\mathrm{{JOB}}}_{1\ldots {33}}$ ,without loss of generality. These queries are primarily designed to test join order optimization, with each query containing an average of 8 joins.

JOB。采用互联网电影数据库（IMDB）上的连接顺序基准（JOB）[31]。不失一般性地选择所有标记为"a"的JOB查询变体（称为${\mathrm{{JOB}}}_{1\ldots {33}}$）。这些查询主要用于测试连接顺序优化，每个查询平均包含8个连接。

The largest dataset (i.e., LDBC100) contains 282 million tuples in vertex relations and 938 million tuples in edge relations. More detailed statistics of the datasets are available in the full version[35]. We manually implement the queries using SQL/PGQ, which are presented in the artifact [34]. Furthermore, we perform the RGMapping process in a manner that allows the construction of the same graph index on the LDBC and JOB datasets used in GRainDB's experiments [23]. Specifically, the EV-index and VE-index on potential edge relations are constructed on foreign keys and tables that depict many-to-many relationships.

最大规模的数据集（即LDBC100）包含2.82亿个顶点关系元组和9.38亿个边关系元组。完整版文献[35]提供了更详细的数据集统计信息。我们采用SQL/PGQ手动实现了查询语句，具体实现详见实验材料[34]。此外，我们执行的RGMapping流程确保能在LDBC和JOB数据集上构建与GRainDB实验[23]相同的图索引。具体而言，在潜在边关系上构建的EV索引和VE索引均基于外键及描述多对多关系的表。

Compared Systems. To ensure a fair comparison, all systems except Kùzu use DuckDB v0.9.2 as the relational execution engine, differing only in their optimizers. Since GRainDB was originally implemented on an older version of DuckDB, we have reimplemented it on DuckDB v0.9.2, which offers improved performance over the original version. Kùzu utilizes its own execution engine (v0.4.2) as a baseline of a graph database management system (GDBMS).

对比系统。为确保公平性，除Kùzu外所有系统均采用DuckDB v0.9.2作为关系执行引擎，仅优化器存在差异。由于GRainDB原版基于旧版DuckDB实现，我们已将其迁移至性能更优的v0.9.2版本。Kùzu使用其自有执行引擎(v0.4.2)作为图数据库管理系统(GDBMS)的基准。

DuckDB [2]: This system optimizes queries using the graph-agnostic approach, leveraging DuckDB's built-in optimizer as described in Sec. 4.1. It serves as the naive baseline for extending a relational database system to support SPJM.

DuckDB[2]：该系统采用图无关优化方法，利用DuckDB内置优化器（如第4.1节所述），作为关系数据库系统扩展支持SPJM的朴素基线。

GRainDB [23]: This system uses same optimizer as DuckDB but employs the graph index (Sec. 3.2.1) for query execution. It acts as the baseline to demonstrate that solely using graph index is insufficient for optimizing SPJM.

GRainDB[23]：该系统与DuckDB使用相同优化器，但执行时调用图索引（第3.2.1节），作为验证仅靠图索引不足以优化SPJM的基准。

Umbra $\left\lbrack  {{15},{37}}\right\rbrack$ : This system features an advanced hybrid optimizer capable of generating wco join plans. We obtained the Umbra executable from the authors and configured its parameters according to their recommendations for computing the execution plan. The execution plan is then executed on DuckDB ${}^{2}$ ,utilizing the graph index when applicable,as done in GRainDB. This helps demonstrate that even with an advanced relational optimizer and the addition of a graph index, it can still fall short in optimizing SPJM.

Umbra$\left\lbrack  {{15},{37}}\right\rbrack$：该系统配备能生成wco连接计划的高级混合优化器。我们获取了作者提供的可执行文件，并按建议配置参数生成执行计划。该计划在DuckDB${}^{2}$上执行时（如GRainDB方案）会适时调用图索引，用以证明即使结合高级关系优化器与图索引，仍可能无法充分优化SPJM。

RelGo: This system optimizes queries using the converged optimizer presented in Sec. 4.2 and utilizes the graph index for query execution. It demonstrates the full range of techniques introduced in this paper. There are some variants of RelGo for verifying the effectiveness of the proposed techniques, which will be introduced in the corresponding experiments.

RelGo：该系统采用第4.2节的融合优化器进行查询优化，并利用图索引执行查询，完整展示了本文提出的技术体系。为验证技术有效性，我们还设置了若干RelGo变体，将在对应实验中说明。

Kùzu [22]: This system is a GDBMS that adopts the property graph data model. We use it as a baseline to compare the performance gap between RelGo on relational databases and native graph databases.

Kùzu[22]：该系统是采用属性图模型的GDBMS，作为对比RelGo在关系数据库与原生图数据库性能差距的基准。

Configurations. Our experiments were conducted on a server equipped with an Intel Xeon E5-2682 CPU running at 2.50GHz and 256GB of RAM, with parallelism restricted to a single thread. For a comprehensive performance analysis, each query from the LDBC benchmark was run 50 times using the official parameters, while each query from the JOB benchmark was executed 10 times. We report the average time cost for each query to mitigate potential biases. We imposed a timeout limit of 10 minutes for each query, and queries that fail to finish within the limit are marked as OT.

实验配置。测试服务器搭载Intel Xeon E5-2682处理器（主频2.50GHz）和256GB内存，并行度限制为单线程。LDBC基准查询使用官方参数运行50次，JOB基准查询运行10次，取平均耗时以减少偏差。单次查询超时阈值设为10分钟，超时标记为OT。

---

<!-- Footnote -->

${}^{2}$ Notably,all Umbra’s plans for the benchmark queries exclude the multiway-join operator,allowing for direct transformation into DuckDB's runtime.

${}^{2}$值得注意的是，Umbra所有基准查询计划均未采用多路连接算子，可直接转换为DuckDB运行时。

<!-- Footnote -->

---

<!-- Media -->

<!-- figureText: RelGo Opt. RelGo Exe. GRainDB Opt. GRainDB Exe Time Cost (ms) 600 500 400 300 200 100 0 JOB1 JOB2 JOB3 JOB4 (b) E2E Time on IMDB. 1200 Time Cost (ms) 1000 800 600 400 200 ${\mathrm{C}}_{1 - 3}$ IC2 IC4 ${\mathrm{{IC}}}_{7}$ (a) E2E Time on ${LDBC30}$ . -->

<img src="https://cdn.noedgeai.com/0196599e-8e15-7d66-a6ce-e26720ffa9f7_18.jpg?x=253&y=269&w=1059&h=349&r=0"/>

Fig. 7. Experiments on optimization and execution cost

图7. 优化与执行成本实验

<!-- figureText: ${10}^{4}$ ${10}^{4}$ RelGo RelGoNoRule Time Cost (ms) ${10}^{3}$ ${10}^{2}$ ${10}^{1}$ ${10}^{0}$ ${\mathrm{{QR}}}_{1}$ ${\mathrm{{QR}}}_{2}$ ${\mathrm{{QR}}}_{3}$ QR4 (b) Time Cost on ${LDBC30}$ . RelGo RelGoNoRule Time Cost (ms) ${10}^{3}$ ${10}^{2}$ ${10}^{1}$ ${10}^{0}$ QR1 ${\mathrm{{QR}}}_{2}$ ${\mathrm{{QR}}}_{3}$ QR4 (a) Time Cost on ${LDBC10}$ -->

<img src="https://cdn.noedgeai.com/0196599e-8e15-7d66-a6ce-e26720ffa9f7_18.jpg?x=253&y=681&w=1059&h=342&r=0"/>

Fig. 8. Efficiency comparison of RelGo and RelGoNoRule

图8. RelGo与RelGoNoRule效率对比

<!-- Media -->

### 5.2 Micro Benchmarks on RelGo

### 5.2 RelGo微观基准测试

In this subsection, we conducted three micro benchmarks to evaluate the effectiveness of RelGo, including assessing the efficiency of the optimizer, testing its advanced optimization strategies, and examining its effectiveness in optimizing join order.

本节通过三项微观测试评估RelGo有效性：优化器效率测试、高级策略验证及连接顺序优化效果检验。

Optimization Efficiency Evaluation. First, we assessed the optimization efficiency by comparing RelGo with GRainDB[23]. We tested their optimization time and also evaluated the execution time for their optimized plans as a measure of the plan quality. We considered end-to-end time as optimization time plus execution time. We randomly selected two subsets of the LDBC and JOB queries, and conducted the experiments on LDBC30 and IMDB datasets.

优化效率评估。首先对比RelGo与GRainDB[23]的优化耗时，并通过优化后计划的执行耗时衡量方案质量。端到端时间计为优化与执行时间之和。实验随机选取LDBC和JOB查询子集，在LDBC30和IMDB数据集上进行。

The results in Fig. 7 reveal that RelGo significantly outperforms GRainDB in terms end-to-end time,achieving an average speedup of ${7.5} \times$ on ${LDBC30}$ and ${3.8} \times$ on IMDB. However,note that RelGo incurs a slightly higher optimization cost compared to GRainDB. Although RelGo theoretically has a narrower search space, as analyzed in Sec. 3.1.3, GRainDB benefits from DuckDB's optimizer, which includes very aggressive pruning strategies. Despite the slightly higher optimization cost, RelGo generates superior optimized plans,surpassing GRainDB by an average of ${9.7} \times$ on LDBC30 and ${4.3} \times$ on IMDB in execution time.

图7结果显示，RelGo在端到端时间上显著优于GRainDB，在${7.5} \times$上平均加速达到${LDBC30}$，在IMDB上达到${3.8} \times$。但需注意，RelGo的优化成本略高于GRainDB。虽然如第3.1.3节分析，RelGo理论搜索空间更小，但GRainDB受益于DuckDB优化器采用的激进剪枝策略。尽管优化成本稍高，RelGo生成的优化计划更优，执行时间在LDBC30上平均领先${9.7} \times$，在IMDB上领先${4.3} \times$。

For fair comparison, in the subsequent experiments, we evaluate the efficiency of different systems using the end-to-end time.

为确保公平比较，后续实验均采用端到端时间评估各系统效率。

Advanced Optimization Strategies. In this experiment, we assessed the advanced optimization strategies in RelGo, including the heuristic FilterIntoMatchRule and TrimAndFuseRule, and the optimized implementation of EXPAND_INTERSECT operator that aims to improve the efficiency of complete star join.

高级优化策略。本实验评估了RelGo中的启发式规则FilterIntoMatchRule与TrimAndFuseRule，以及旨在提升完全星型连接效率的EXPAND_INTERSECT算子优化实现。

We began by testing heuristic rules FilterIntoMatchRule and TrimAndFuseRule. We conducted experiments on ${LDBC10}$ and ${LDBC30}$ ,using $Q{R}_{1}$ and $Q{R}_{2}$ to test FilterIntoMatchRule,and $Q{R}_{3}$ and $Q{R}_{4}$ to test TrimAndFuseRule. The results in Fig. 8 compared the performance of RelGo with and without applying these rules, denoted as RelGo and RelGoNoRule, respectively. The results show that FilterIntoMatchRule significantly improves query performance, providing an average speedup of 299.4 $\times$ on ${LDBC10}$ and ${699.8} \times$ on ${LDBC30}$ . With TrimAndFuseRule,query execution is accelerated by an average of ${2.0} \times$ on ${LDBC10}$ and ${2.3} \times$ on ${LDBC30}$ . These findings suggest that the heuristic rules, particularly FilterIntoMatchRule, are highly effective in enhancing query execution efficiency.

我们首先测试启发式规则FilterIntoMatchRule和TrimAndFuseRule。在${LDBC10}$和${LDBC30}$数据集上，使用$Q{R}_{1}$和$Q{R}_{2}$测试FilterIntoMatchRule，$Q{R}_{3}$和$Q{R}_{4}$测试TrimAndFuseRule。图8结果显示，应用规则的RelGo（标记为RelGo）相比未应用规则的版本（RelGoNoRule）性能显著提升：FilterIntoMatchRule使${LDBC10}$平均加速299.4$\times$，${LDBC30}$加速${699.8} \times$；TrimAndFuseRule使${LDBC10}$平均加速${2.0} \times$，${LDBC30}$加速${2.3} \times$。这表明启发式规则（尤其是FilterIntoMatchRule）能有效提升查询效率。

<!-- Media -->

<!-- figureText: RelGo ${10}^{6}$ RelGo Time Cost (ms) RelGoNoEI ${10}^{5}$ ${10}^{4}$ ${\mathrm{{QC}}}_{1}$ ${\mathrm{{QC}}}_{2}$ ${\mathrm{{QC}}}_{3}$ (b) Time Cost on ${LDBC30}$ . Time Cost (ms) ${10}^{5}$ RelgoNoEl ${10}^{4}$ ${\mathrm{{QC}}}_{1}$ ${\mathrm{{QC}}}_{2}$ ${\mathrm{{QC}}}_{3}$ (a) Time Cost on ${LDBC10}$ . -->

<img src="https://cdn.noedgeai.com/0196599e-8e15-7d66-a6ce-e26720ffa9f7_19.jpg?x=252&y=263&w=1061&h=343&r=0"/>

Fig. 9. Efficiency comparison of RelGo and RelGoNoEl

图9. RelGo与RelGoNoEl效率对比

<!-- figureText: Time Cost (ms) RelGo GRainDB RelGoHash DuckDB 6 7 8 9 10 Queries of JOB * ${10}^{3}$ ${10}^{2}$ 1 2 3 4 -->

<img src="https://cdn.noedgeai.com/0196599e-8e15-7d66-a6ce-e26720ffa9f7_19.jpg?x=294&y=678&w=967&h=318&r=0"/>

Fig. 10. Experiments on join order efficiency

图10. 连接顺序效率实验

<!-- Media -->

Next, we evaluated the effectiveness of the EXPAND_INTERSECT, which focuses on improving the efficiency of complete star join. Without this optimization strategy, the EXPAND_INTERSECT operator would be implemented as a traditional multiple join, and we denote this variant as RelGoNoEl. Queries $Q{C}_{1\ldots 3}$ that contain cycles are used to compare the performance of RelGo and RelGoNoEl. The performance results in Fig. 9 suggest that, compared to RelGoNoEI, RelGo achieves an average speedup of ${1.22} \times$ on ${LDBC10}$ and ${1.31} \times$ on ${LDBC30}$ (excluding $Q{C}_{3}$ ). Notably,for $Q{C}_{3}$ ,which is a complex 4-clique, the plans optimized by RelGoNoEl confront an out-of-memory (OOM) error. The results indicate that EXPAND_INTERSECT

接着评估针对完全星型连接的EXPAND_INTERSECT优化效果。未优化时该算子以传统多连接方式实现（标记为RelGoNoEl）。使用含环查询$Q{C}_{1\ldots 3}$对比性能，图9显示：相比RelGoNoEI，RelGo在${LDBC10}$上平均加速${1.22} \times$，${LDBC30}$上加速${1.31} \times$（排除$Q{C}_{3}$）。值得注意的是，复杂4-团查询$Q{C}_{3}$在RelGoNoEl优化时出现内存溢出错误。结果表明EXPAND_INTERSECT

with an optimized implementation not only enhances query performance but also significantly reduces the spatial overhead.

的优化实现不仅提升查询性能，还能显著降低空间开销。

Efficiency of Join Order. We compared RelGo with GRainDB and DuckDB, focusing on the efficiency of the join order. For this purpose, we introduced a variant of RelGo called RelGoHash, which optimizes the plan in a converged manner like RelGo but deliberately bypasses the use of graph index. We selected 10 queries from the JOB benchmark and showed the performance results in Fig. 10. The results demonstrate that RelGo outperforms GRainDB on all the queries, accelerating the execution time by factors ranging from ${1.4} \times$ to ${7.5} \times$ ,with an average speedup of ${4.1} \times$ . Additionally,the plans optimized with RelGoHash are at least as good as those optimized by DuckDB,achieving an average speedup of ${1.6} \times$ . The effectiveness of RelGo and RelGoHash stems from their use of advanced graph-aware optimization techniques in optimizing the matching operator, resulting in good join order and thus robust performance regardless of graph index. It is worth noting that RelGo does not always generate the absolute best join orders, as it relies on the

连接顺序效率。我们对比RelGo、GRainDB和DuckDB的连接顺序优化效果，引入变体RelGoHash（采用RelGo的收敛式优化但刻意绕过图索引）。选取JOB基准中10个查询，图10显示：RelGo在所有查询上均优于GRainDB，加速倍数介于${1.4} \times$至${7.5} \times$，平均加速${4.1} \times$；RelGoHash优化计划至少与DuckDB相当，平均加速${1.6} \times$。RelGo与RelGoHash的优越性源于其采用先进的图感知优化技术来优化匹配算子，从而获得优质连接顺序。值得注意的是，由于依赖

<!-- Media -->

<!-- figureText: RelGo UmbraPlans GRainDB Kùzu 8GYPNG 0 Queries of IC* (a) Speedup Compared to DuckDB on ${LDBC100}$ . 8GYPNG 9 10 11 12 13 14 15 16 17 Queries of JOB * 8GH2NG 25 26 27 28 29 30 31 32 33 Queries of JOB * (b) Speedup Compared to DuckDB on IMDB. ${10}^{2}$ Speedup vs. DuckDB ${10}^{1}$ ${10}^{0}$ ${10}^{-1}$ ✓ ${10}^{2}$ Speedup vs. DuckDB ${10}^{1}$ ${10}^{0}$ ${10}^{-1}$ ${10}^{-2}$ 1 2 3 4 5 6 7 8 ${10}^{2}$ Speedup vs. DuckDB ${10}^{1}$ ${10}^{0}$ ${10}^{-1}$ 18 19 20 21 22 23 24 -->

<img src="https://cdn.noedgeai.com/0196599e-8e15-7d66-a6ce-e26720ffa9f7_20.jpg?x=259&y=266&w=1046&h=1260&r=0"/>

Fig. 11. Results of the comprehensive experiments. The speedup is computed as $\frac{\text{Time}\left( \text{OuchBB}\right) }{\text{Time}\left( \text{Compared Method}\right) }$ . estimated cost of the plans. However, its optimized plans generally remain competitive in most cases, thanks to its integration of GLogue that use high-order statistics for cost estimation.

图11. 综合实验结果。加速比计算方式为$\frac{\text{Time}\left( \text{OuchBB}\right) }{\text{Time}\left( \text{Compared Method}\right) }$。虽然其优化方案的成本估算存在差异，但得益于集成采用高阶统计量进行成本估算的GLogue系统，在多数情况下仍能保持竞争力。

<!-- Media -->

### 5.3 Comprehensive Experiments

### 5.3 综合实验

We conducted comprehensive experiments on the LDBC and JOB benchmarks to comprehensively evaluate the performance of RelGo compared to DuckDB, GRainDB, Umbra, and Kùzu. The experimental results are shown in Fig. 11. The results on LDBC10 and LDBC30 are omitted because they are comparable to those on LDBC100. Complete results are provided in the full version[35].

我们在LDBC和JOB基准测试上进行了全面实验，系统评估RelGo与DuckDB、GRainDB、Umbra及Kùzu的性能对比。实验结果如图11所示，LDBC10和LDBC30数据因与LDBC100结果相似予以省略，完整结果见文献[35]。

5.3.1 Comparison with DuckDB and GRainDB. Firstly, we compared the performance of RelGo with DuckDB and GRainDB. Specifically, for the LDBC benchmark, the execution time of the plans optimized by RelGo is about ${21.9} \times$ and ${5.4} \times$ faster on average than those generated by DuckDB and GRainDB on ${LDBC100}$ . It is important to note that RelGo is especially effective for queries containing cycles,which can benefit more from graph optimizations. For example,in query ${\mathrm{{IC}}}_{7}$ , which contains a cycle,RelGo outperforms DuckDB and GRainDB by ${76.3} \times$ and ${22.0} \times$ ,respectively. Conversely, the JOB benchmark, established for assessing join optimizations in relational databases, lacks any cyclic-pattern queries. Despite this, RelGo still achieves better performance compared to DuckDB and GRainDB,with an average speedup of ${8.2} \times$ and ${4.0} \times$ ,respectively.

5.3.1 与DuckDB和GRainDB的对比。首先将RelGo与DuckDB、GRainDB进行性能比较：在LDBC基准测试中，RelGo优化方案的平均执行速度分别比DuckDB和GRainDB快${21.9} \times$与${5.4} \times$（基于${LDBC100}$）。值得注意的是，RelGo对含环状结构的查询优化效果尤为显著——例如在含环查询${\mathrm{{IC}}}_{7}$中，其性能分别超越DuckDB和GRainDB达${76.3} \times$与${22.0} \times$。而针对关系型数据库连接优化设计的JOB基准测试虽无环状查询，RelGo仍保持优势，平均加速比分别达到${8.2} \times$和${4.0} \times$。

The experimental results reflect our discussions in Sec. 3.1.2. We summarize RelGo's superiority as follows. First, RelGo is designed to be aware of the existence of graph index in query optimization and can leverage the index to effectively retrieve adjacent edges and vertices. In contrast, for GRainDB, relational optimizers can occasionally alter the order of EVJoin operations, making graph index ineffective. DuckDB, on the other hand, does not consider graph index in query optimization and executes queries using conventional hash joins, which are often less efficient compared to graph-aware approaches. Second, by incorporating a matching operator in SPJM queries to capture the graph query semantics, RelGo is able to leverage advanced graph optimization techniques to optimize matching operators. These techniques include using high-order statistics to estimate the cost of plans more accurately and employing wco join implementations to optimize cyclic patterns. In contrast, DuckDB and GRainDB cannot benefit from these graph-specific optimizations, which may lead to suboptimal plans and inefficient execution. Third, RelGo considers optimization opportunities across both graph and relational query semantics, introducing effective heuristic rules such as FilterIntoMatchRule and TrimAndFuseRule. These rules can significantly improve the efficiency of the generated plans.

实验结果印证了第3.1.2节的讨论。RelGo的优越性主要体现在：第一，其查询优化器能主动识别并利用图索引高效获取邻接边与顶点，而GRainDB的关系优化器可能打乱EVJoin操作顺序致使图索引失效，DuckDB则完全未考虑图索引而采用传统哈希连接。第二，通过SPJM查询中引入匹配运算符捕捉图查询语义，RelGo能运用高阶统计量精确估算成本、采用wco连接优化环状模式等先进技术，而DuckDB和GRainDB无法获得这类图专属优化。第三，RelGo统筹考虑图与关系查询语义的优化机会，应用FilterIntoMatchRule和TrimAndFuseRule等启发式规则显著提升方案效率。

<!-- Media -->

<!-- figureText: PROJECTION SCAN NAME PROJECTION n.name n.name PROJECTION SELECTION JOIN n.name n.name JOIN (name STARTS WITH "B") SCAN SCAN SCAN CAST_INFO JOIN MOVIE_COMPANIES COMPANY NAME (country_code = "[us]" SCAN TITLE SCAN NAME SELECTION JOIN (name STARTS WITH "B") (country_code = "[us]") SCAN JOIN SCAN MOVIE COMPANIES - JOIN CAST_INFO SCAN SCAN KEYWORD JOIN name-in-title") SCAN (keyword = "character-name-in-title") (c) Query Plan of GRainDB (d) Query Plan of Umbra n.name n.name PROJECTION* SELECT MIN(g.n_name) AS member_in_charnamed_american_movie MIN(g.n_name) AS a1 FROM GRAPH_TABLE (graph MATCH EXPAND NAME (ci:CAST INFO)-[:CAST INFO NAME]->(n:NAME), (name STARTS WITH "B") (ci)-[:CAST_INFO_TITLE]->(t:TITLE) (t)-[:MOVIE_KEYWORD]->(k:KEYWORD) EXPAND (t) $<  -$ [:MOVIE COMPANIES TITLE]-(mc:MOVIE COMPANIES) COMPANY_NAME (mc)-[:MOVIE_COMPANIES_COMPANY_NAME]->(cn:COMPANY_NAME (country_code = "[us]") WHERE cn.country_code = '[us]' AND k.keyword = 'character-name-in-title AND n.name STARTS WITH 'B' EXPAND TITLE COLUMNS( n.name AS n_name ) g SCAN KEYWORD name-in-title") (a) ${\mathrm{{JOB}}}_{17}$ query (b) Query Plan of RelGo -->

<img src="https://cdn.noedgeai.com/0196599e-8e15-7d66-a6ce-e26720ffa9f7_21.jpg?x=139&y=986&w=1289&h=399&r=0"/>

Fig. 12. ${\mathrm{{JOB}}}_{17}$ ’s plans given by RelGo,GRainDB and Umbra. JOINs are implemented as GRainDB’s predefined joins if possible.

图12. RelGo、GRainDB与Umbra给出的${\mathrm{{JOB}}}_{17}$方案。JOIN操作优先采用GRainDB预定义实现。

<!-- Media -->

5.3.2 Comparsion with Umbra. We then compared the performance of RelGo and Umbra. In detail, the plans optimized by RelGo are about ${49.9} \times$ faster on average than those generated by Umbra on ${LDBC100}$ . On JOB benchmark,the plans generated by RelGo are on average ${1.7} \times$ more efficient than those given by Umbra. Several factors contribute to the results: (1) Umbra, due to its lack of a graph perspective, might generate query plans that encounter challenges in utilizing graph indexes effectively, similar to GRainDB; (2) Although Umbra's optimizer supports generating worst-case optimal plans that include multiway joins, none of Umbra's optimized plans for the tested queries in our experiments contained multiway joins. In contrast, RelGo excels at identifying opportunities to effectively utilize graph indices and adheres to worst-case optimality.

5.3.2 与Umbra的对比。RelGo优化方案在${LDBC100}$上的平均执行速度比Umbra快${49.9} \times$，在JOB基准测试中效率平均提升${1.7} \times$。主要原因包括：(1)Umbra缺乏图视角可能导致与GRainDB类似的图索引利用难题；(2)虽然Umbra优化器支持生成含多路连接的最坏情况最优方案，但实验中的所有优化方案均未包含多路连接。而RelGo既能有效发掘图索引应用场景，又能严格遵守最坏情况最优性原则。

There are instances where Umbra outperforms RelGo in execution plans. For example, when querying ${\mathrm{{JOB}}}_{30}$ on IMDB,the execution time of the plan generated by RelGo is approximately ${1.2} \times$ slower than that of Umbra. A potential reason is that RelGo has not yet considered the distributions of attribute values. For example,when the predicate "t.production_year > 2000" is present, knowing the distribution of the attribute "production_year" can help better estimate the results after filtering by the predicate. Hence, Umbra can sometimes estimate cardinalities more accurately when such predicates exist. Addressing this will be an important future work.

在某些执行计划中，Umbra的表现优于RelGo。例如，在IMDB上查询${\mathrm{{JOB}}}_{30}$时，RelGo生成的计划执行时间比Umbra慢约${1.2} \times$。一个可能的原因是RelGo尚未考虑属性值的分布情况。例如，当存在谓词"t.production_year > 2000"时，了解"production_year"属性的分布有助于更准确地估计该谓词过滤后的结果。因此，当存在此类谓词时，Umbra有时能更精确地估算基数。解决这一问题将是未来的重要工作。

5.3.3 Comparison with Kùzu. Finally, we compared RelGo with the GDBMS, Kùzu. The experimental results show that RelGo is approximately ${188.7} \times$ faster on average than Kùzu on LDBC100 and ${136.1} \times$ faster on the JOB benchmark. Some results of Kùzu are omitted (e.g.,IC ${}_{3 - 1}$ on ${LDBC100}$ ) due to OOM errors. As Kùzu is also developed based on DuckDB, we speculated that Kùzu may not sufficiently exploit graph-specific optimizations as RelGo does.

5.3.3 与Kùzu的对比。最后我们将RelGo与图数据库管理系统Kùzu进行对比。实验结果表明，在LDBC100基准测试中RelGo平均比Kùzu快约${188.7} \times$，在JOB基准测试中快${136.1} \times$。由于内存不足错误，Kùzu的部分结果（如${LDBC100}$上的IC${}_{3 - 1}$）被省略。由于Kùzu同样基于DuckDB开发，我们推测其可能未像RelGo那样充分利用图特有的优化手段。

### 5.4 Case Study

### 5.4 案例分析

To further illustrate why the plans generated by RelGo are superior to those produced by the baseline optimizers,we conducted a case study on ${\mathrm{{JOB}}}_{17}$ as an example,shown in Fig. 12(a). The optimized query plans by RelGo, GRainDB, and Umbra for this query are presented in Fig. 12(b)-(d). Fig. 11b shows that RelGo’s plan runs ${4.3} \times$ and ${1.8} \times$ faster than those optimized by GRainDB and Umbra, respectively.

为深入说明RelGo生成的计划为何优于基线优化器，我们以${\mathrm{{JOB}}}_{17}$为例进行案例分析（如图12(a)所示）。图12(b)-(d)展示了RelGo、GRainDB和Umbra针对该查询的优化计划。图11b显示RelGo的计划执行速度分别比GRainDB和Umbra优化的计划快${4.3} \times$和${1.8} \times$。

A key difference between the plan of RelGo and those of GRainDB and Umbra is that RelGo can consistently follow the graph query semantics by continuously expanding from a starting vertex to its neighbors, leveraging the graph index. For example, RelGo's plan begins with scanning ${R}_{\text{KEYWORD }}$ ,then expands to its neighbors ${R}_{\text{TITLE }}$ ,followed by ${R}_{\text{COMPANY_NAME }}$ ,and finally ${R}_{\text{NAME }}$ . In this order, the graph indices (both EV-index and VE-index) introduced in Sec. 3.2.1 are fully utilized to efficiently retrieve neighboring vertices. In contrast, GRainDB and Umbra, as relational optimizers, may not always adhere to this semantics. For instance, in GRainDB's plan, after joining ${R}_{\text{KEYWORD }}$ with ${R}_{\text{MOVIE_KEYWORD }}$ ,the plan misses the opportunity to immediately join ${R}_{\text{TITLE }}$ ,thus failing to use the EV-index constructed between ${R}_{\text{MOVIE_KEYWORD }}$ and ${R}_{\text{TITLE }}$ right away. A similar situation occurs in Umbra's plan.

RelGo计划与GRainDB和Umbra计划的关键区别在于：RelGo能始终遵循图查询语义，通过图索引从起始顶点持续扩展到相邻顶点。例如RelGo计划首先扫描${R}_{\text{KEYWORD }}$，然后扩展到相邻节点${R}_{\text{TITLE }}$，接着是${R}_{\text{COMPANY_NAME }}$，最后是${R}_{\text{NAME }}$。该顺序充分利用了第3.2.1节介绍的图索引（EV索引和VE索引）来高效检索相邻顶点。而作为关系型优化器，GRainDB和Umbra可能不总是遵循这种语义。例如GRainDB计划在连接${R}_{\text{KEYWORD }}$与${R}_{\text{MOVIE_KEYWORD }}$后，未能立即连接${R}_{\text{TITLE }}$，因此错过了使用${R}_{\text{MOVIE_KEYWORD }}$与${R}_{\text{TITLE }}$之间构建的EV索引的机会。Umbra计划中也存在类似情况。

## 6 Related Work

## 6 相关工作

Query Optimization for Relational Databases. Various studies of query optimization for relational databases were proposed to find the optimal join order $\left\lbrack  {{18},{21},{25},{26}}\right\rbrack$ . For example,Haffner et al. [18] converted join order optimization into finding the shortest path on directed graphs and used the ${\mathrm{A}}^{ * }$ algorithm to solve it. Kossmann et al. [25] summarized the methods to optimize queries with data dependencies, such as uniqueness constraints, foreign key constraints, and inclusion dependencies. Recently, researchers attempt to incorporate wco joins into plans to better handle queries with cycles and reduce the size of intermediate results [4, 50]. CLFTJ [24] introduces caching into trie join to reuse previously computed results. Umbra [15] proposes a new hash trie data structure and further reduces the cost of set intersection. All these techniques can be orthogonally adopted in RelGo's relational optimization.

关系型数据库查询优化。为寻找最优连接顺序$\left\lbrack  {{18},{21},{25},{26}}\right\rbrack$，学界提出了多种关系型数据库查询优化研究。例如Haffner等人[18]将连接顺序优化转化为有向图最短路径问题，采用${\mathrm{A}}^{ * }$算法求解。Kossmann等人[25]总结了利用唯一性约束、外键约束和包含依赖等数据依赖关系优化查询的方法。近期研究尝试将wco连接纳入计划，以更好地处理带环路的查询并减少中间结果规模[4,50]。CLFTJ[24]在trie连接中引入缓存机制复用先前计算结果。Umbra[15]提出新型哈希trie数据结构，进一步降低集合交集计算成本。这些技术均可正交地应用于RelGo的关系型优化中。

Query Optimization for Graph Databases. Graph pattern matching, a fundamental problem in graph query processing, has been extensively studied [6]. In sequential settings, Ullmann's backtracking algorithm [49] has been optimized using various techniques, such as tree indexing [44], symmetry breaking [19], and compression [7]. Join-based algorithms have been developed for distributed environments. These algorithms use cost estimation to optimize join order, with binary-join algorithms $\left\lbrack  {{27},{28}}\right\rbrack$ estimating costs using random graph models and worst-case-optimal join algorithms [5] ensuring a worst-case upper bound on the cost. Hybrid approaches[22, 36, 51] adaptively select between binary and wco joins based on the lower cost. Recent studies have focused on improving cost estimation in graph pattern matching, including decomposing graphs into star-shaped subgraphs [38] and comparing different cardinality estimation methods [41]. Some optimizers, like GLogS [29], search for the optimal plan by representing edges as binary joins or vertex-expansion subtasks. We follow the join-based methods such as [29, 51] due to their compatibility with the relational context for which RelGo is designed. Bridging Relational and Graph Models. There is a growing interest in studying the interaction between relational and graph models. DuckPGQ [47, 48] has demonstrated support for SQL/PGQ within the DuckDB [2], utilizing the straightforward, graph-agnostic approach to transform and process pattern matching. Hence, DuckPGQ loses the opportunity to optimize the query from a graph query perspective. Index-based methods, such as GQ-Fast [33] and GRainDB [23], work towards construct graph-like index on relational databases to improve the performance of join execution. RelGo leveraged GRainDB's indexing technique for implementing physical graph operations. In contrast, methods like GRFusion [20] and Gart [45] work towards materializing graph from the relational tables, so that graph queries can be executed directly on the materialized graph. Such methods incur additional storage costs and potential inconsistencies between relational and graph data.

图数据库查询优化技术研究。图模式匹配作为图查询处理的核心问题，已得到广泛研究[6]。在串行环境下，Ullmann回溯算法[49]通过树形索引[44]、对称性消除[19]和压缩技术[7]等优化手段得到改进。针对分布式环境开发的基于连接的算法，采用成本估算来优化连接顺序——二元连接算法$\left\lbrack  {{27},{28}}\right\rbrack$使用随机图模型进行成本估算，而最坏情况最优连接算法[5]则确保成本的最坏情况上界。混合方法[22,36,51]根据成本估算自适应选择二元连接或WCO连接。近期研究聚焦于改进图模式匹配的成本估算，包括将图分解为星型子图[38]和比较不同基数估算方法[41]。GLogS[29]等优化器通过将边表示为二元连接或顶点扩展子任务来搜索最优计划。我们采用[29,51]等基于连接的方法，因其与RelGo设计的关系型场景具有兼容性。关系型与图模型的融合研究。学界日益关注关系型与图模型的交互研究。DuckPGQ[47,48]在DuckDB[2]中实现了SQL/PGQ支持，采用与图无关的转换处理方法，因而错失了从图查询角度进行优化的机会。GQ-Fast[33]和GRainDB[23]等基于索引的方法致力于在关系数据库上构建类图索引以提升连接执行性能。RelGo采用GRainDB的索引技术实现物理图操作。相比之下，GRFusion[20]和Gart[45]等方法通过物化关系表生成图结构，使图查询可直接在物化图上执行，但需承担额外存储成本及关系与图数据间可能的不一致性。

## 7 Conclusions and Discussion

## 七、结论与讨论

In this paper, we introduce RelGo, a converged relational-graph optimization framework designed for SQL/PGQ queries. We formulate the SPJM query skeleton to better analyze and optimize the relational-graph hybrid queries introduced by SQL/PGQ. After discovering that a graph-agnostic approach can result in a larger search space and suboptimal query plans, we design RelGo to optimize the relational and graph components of SPJM queries using dedicated relational and graph optimization modules, respectively. Additionally, RelGo incorporates optimization rules, such as FilterIntoMatchRule, which optimize queries across the relational and graph components, further enhancing overall query efficiency. We conduct extensive experiments comparing RelGo to graph-agnostic baselines, demonstrating its superior performance and confirming the effectiveness of our optimization techniques.

本文提出RelGo——面向SQL/PGQ查询的关系-图融合优化框架。通过构建SPJM查询骨架，我们更好地分析与优化SQL/PGQ引入的关系-图混合查询。发现与图无关的方法会导致搜索空间膨胀和次优查询计划后，我们设计RelGo分别用专用模块优化SPJM查询的关系与图组件。此外，RelGo引入跨组件优化规则（如FilterIntoMatchRule），进一步提升整体查询效率。大量实验证明RelGo显著优于与图无关的基线方法，验证了优化技术的有效性。

One interesting future direction is to extend RelGo to directly process existing SPJ queries as inputs, enabling the automatic conversion from SPJ to SPJM queries while being aware of the presence of graph indices. Boudaoud et al. [8] may have discussed potential methods for such conversion. However, designing a global solution to determine which parts of an SPJ query can be converted into a matching operator is challenging. This decision involves exhaustively exploring the search space, now including both join and pattern matching options. Given the high cost of optimizing joins alone, an exhaustive search could become prohibitively expensive. Therefore, it is necessary to carefully balance and select appropriate global and local optimization rules for given queries.

未来可探索RelGo直接处理SPJ查询的扩展能力，在感知图索引存在的情况下实现SPJ到SPJM查询的自动转换。Boudaoud等[8]可能探讨过此类转换方法。但设计全局方案来确定SPJ查询中可转换为匹配算子的部分具有挑战性，这需要穷举包含连接与模式匹配选项的搜索空间。鉴于单独优化连接的高成本，穷举搜索可能代价过高，因此需针对给定查询精心权衡选择全局与局部优化规则。

## References

## 参考文献

[1] 2024. Apache Age. https://age.apache.org/.

[2] 2024. DuckDB. https://duckdb.org/.

[3] 2024. openCypher. https://opencypher.org/.

[4] Christopher R. Aberger, Susan Tu, Kunle Olukotun, and Christopher Ré. 2016. EmptyHeaded: A Relational Engine for Graph Processing. In Proceedings of the 2016 International Conference on Management of Data, SIGMOD Conference 2016, San Francisco, CA, USA, June 26 - July 01, 2016, Fatma Özcan, Georgia Koutrika, and Sam Madden (Eds.). ACM, 431-446. https://doi.org/10.1145/2882903.2915213

[5] Khaled Ammar, Frank McSherry, Semih Salihoglu, and Manas Joglekar. 2018. Distributed Evaluation of Subgraph Queries Using Worst-Case Optimal Low-Memory Dataflows. Proc. VLDB Endow. 11, 6 (oct 2018), 691-704. https: //doi.org/10.14778/3184470.3184473

[6] Renzo Angles, Marcelo Arenas, Pablo Barceló, Aidan Hogan, Juan Reutter, and Domagoj Vrgoč. 2017. Foundations of Modern Query Languages for Graph Databases. ACM Comput. Surv. 50, 5, Article 68 (sep 2017), 40 pages.

[7] Fei Bi, Lijun Chang, Xuemin Lin, Lu Qin, and Wenjie Zhang. 2016. Efficient subgraph matching by postponing cartesian products. In Proceedings of the 2016 International Conference on Management of Data. 1199-1214.

[8] Abdelkrim Boudaoud, Houari Mahfoud, and Azeddine Chikh. 2022. Towards a Complete Direct Mapping from Relational Databases to Property Graphs. In Model and Data Engineering: 11th International Conference, MEDI 2022,

Cairo, Egypt, November 21-24, 2022, Proceedings (Lecture Notes in Computer Science, Vol. 13761), Philippe Fournier-Viger, Ahmed Hassan Yousef, and Ladjel Bellatreche (Eds.). Springer, 222-235. https://doi.org/10.1007/978-3-031-21595-7_16

[9] Donald D. Chamberlin and Raymond F. Boyce. 1974. SEQUEL: A structured English query language. In Proceedings of the 1974 ACM SIGFIDET (Now SIGMOD) Workshop on Data Description, Access and Control (Ann Arbor, Michigan) (SIGFIDET '74). Association for Computing Machinery, New York, NY, USA, 249-264.

[10] S. Chatterji, S. S. K. Evani, S. Ganguly, and M. D. Yemmanuru. 2002. On the complexity of approximate query optimization. In Proceedings of the Twenty-First ACM SIGMOD-SIGACT-SIGART Symposium on Principles of Database Systems (PODS '02). Association for Computing Machinery, New York, NY, USA, 282-292.

[11] Surajit Chaudhuri. 1998. An overview of query optimization in relational systems. In Proceedings of the Seventeenth ACM SIGACT-SIGMOD-SIGART Symposium on Principles of Database Systems (PODS '98). Association for Computing Machinery, New York, NY, USA, 34-43.

[12] Surajit Chaudhuri and Kyuseok Shim. 1999. Optimization of queries with user-defined predicates. ACM Trans. Database Syst. 24, 2 (jun 1999), 177-228.

[13] Jin Chen, Guanyu Ye, Yan Zhao, Shuncheng Liu, Liwei Deng, Xu Chen, Rui Zhou, and Kai Zheng. 2022. Efficient Join Order Selection Learning with Graph-based Representation. In Proceedings of the 28th ACM SIGKDD Conference on Knowledge Discovery and Data Mining (Washington DC, USA) (KDD '22). Association for Computing Machinery, New York, NY, USA, 97-107.

[14] Peter Pin-Shan Chen. 1983. English sentence structure and entity-relationship diagrams. Information Sciences 29, 2-3 (1983), 127-149.

[15] Michael J. Freitag, Maximilian Bandle, Tobias Schmidt, Alfons Kemper, and Thomas Neumann. 2020. Adopting Worst-Case Optimal Joins in Relational Database Systems. Proc. VLDB Endow. 13, 11 (2020), 1891-1904. http: //www.vldb.org/pvldb/vol13/p1891-freitag.pdf

[16] Jonathan Goldstein and Per-Äke Larson. 2001. Optimizing queries using materialized views: a practical, scalable solution. SIGMOD Rec. 30, 2 (may 2001), 331-342.

[17] Goetz Graefe. 1995. The Cascades Framework for Query Optimization. IEEE Data Eng. Bull. 18, 3 (1995), 19-29. http://sites.computer.org/debull/95SEP-CD.pdf

[18] Immanuel Haffner and Jens Dittrich. 2023. Efficiently Computing Join Orders with Heuristic Search. Proc. ACM Manag. Data 1, 1, Article 73 (may 2023), 26 pages.

[19] Wook-Shin Han, Jinsoo Lee, and Jeong-Hoon Lee. 2013. Turboiso: Towards Ultrafast and Robust Subgraph Isomorphism Search in Large Graph Databases. In Proceedings of the 2013 ACM SIGMOD International Conference on Management of Data (New York, New York, USA) (SIGMOD ’13). Association for Computing Machinery, New York, NY, USA, 337-348. https://doi.org/10.1145/2463676.2465300

[20] Mohamed S. Hassan, Tatiana Kuznetsova, Hyun Chai Jeong, Walid G. Aref, and Mohammad Sadoghi. 2018. Extending In-Memory Relational Database Engines with Native Graph Support. In Proceedings of the 21st International Conference on Extending Database Technology, EDBT 2018, Vienna, Austria, March 26-29, 2018, Michael H. Böhlen, Reinhard Pichler, Norman May, Erhard Rahm, Shan-Hung Wu, and Katja Hose (Eds.). OpenProceedings.org, 25-36. https: //doi.org/10.5441/002/EDBT.2018.04

[21] Toshihide Ibaraki and Tiko Kameda. 1984. On the optimal nesting order for computing N-relational joins. ACM Trans. Database Syst. 9, 3 (sep 1984), 482-502.

[22] Guodong Jin, Xiyang Feng, Ziyi Chen, Chang Liu, and Semih Salihoglu. 2023. KUZU Graph Database Management System. In 13th Conference on Innovative Data Systems Research, CIDR 2023, Amsterdam, The Netherlands, January 8-11, 2023. www.cidrdb.org. https://www.cidrdb.org/cidr2023/papers/p48-jin.pdf

[23] Guodong Jin and Semih Salihoglu. 2022. Making RDBMSs Efficient on Graph Workloads Through Predefined Joins. Proc. VLDB Endow. 15, 5 (2022), 1011-1023. https://doi.org/10.14778/3510397.3510400

[24] Oren Kalinsky, Yoav Etsion, and Benny Kimelfeld. 2017. Flexible Caching in Trie Joins. In Proceedings of the 20th International Conference on Extending Database Technology, EDBT 2017, Venice, Italy, March 21-24, 2017, Volker Markl, Salvatore Orlando, Bernhard Mitschang, Periklis Andritsos, Kai-Uwe Sattler, and Sebastian Breß (Eds.). OpenProceedings.org, 282-293. https://doi.org/10.5441/002/EDBT.2017.26

[25] Jan Kossmann, Thorsten Papenbrock, and Felix Naumann. 2022. Data dependencies for query optimization: a survey. VLDB J. 31, 1 (2022), 1-22. https://doi.org/10.1007/s00778-021-00676-3

[26] Ravi Krishnamurthy, Haran Boral, and Carlo Zaniolo. 1986. Optimization of Nonrecursive Queries. In VLDB'86 Twelfth International Conference on Very Large Data Bases, August 25-28, 1986, Kyoto, Japan, Proceedings, Wesley W. Chu, Georges Gardarin, Setsuo Ohsuga, and Yahiko Kambayashi (Eds.). Morgan Kaufmann, 128-137. http://www.vldb.org/ conf/1986/P128.PDF

[27] Longbin Lai, Lu Qin, Xuemin Lin, and Lijun Chang. 2015. Scalable subgraph enumeration in mapreduce. Proceedings of the VLDB Endowment 8, 10 (2015), 974-985.

[28] Longbin Lai, Zhu Qing, Zhengyi Yang, Xin Jin, Zhengmin Lai, Ran Wang, Kongzhang Hao, Xuemin Lin, Lu Qin, Wenjie Zhang, Ying Zhang, Zhengping Qian, and Jingren Zhou. 2019. Distributed subgraph matching on timely dataflow. Proc. VLDB Endow. 12, 10 (jun 2019), 1099-1112. https://doi.org/10.14778/3339490.3339494

[29] Longbin Lai, Yufan Yang, Zhibin Wang, Yuxuan Liu, Haotian Ma, Sijie Shen, Bingqing Lyu, Xiaoli Zhou, Wenyuan Yu, Zhengping Qian, Chen Tian, Sheng Zhong, Yeh-Ching Chung, and Jingren Zhou. 2023. GLogS: Interactive Graph Pattern Matching Query At Large Scale. In 2023 USENIX Annual Technical Conference, USENIX ATC 2023, Boston, MA, USA, July 10-12, 2023, Julia Lawall and Dan Williams (Eds.). USENIX Association, 53-69. https://www.usenix.org/ conference/atc23/presentation/lai

[30] LDBC Social Network Benchmark. 2022. https://ldbcouncil.org/benchmarks/snb/.[Online; accessed 20-October-2022].

[31] Viktor Leis, Andrey Gubichev, Atanas Mirchev, Peter A. Boncz, Alfons Kemper, and Thomas Neumann. 2015. How Good Are Query Optimizers, Really? Proc. VLDB Endow. 9, 3 (2015), 204-215. https://doi.org/10.14778/2850583.2850594

[32] Feifei Li, Bin Wu, Ke Yi, and Zhuoyue Zhao. 2016. Wander Join: Online Aggregation via Random Walks. In Proceedings of the 2016 International Conference on Management of Data, SIGMOD Conference 2016, San Francisco, CA, USA, June 26 - July 01, 2016, Fatma Özcan, Georgia Koutrika, and Sam Madden (Eds.). ACM, 615-629. https://doi.org/10.1145/ 2882903.2915235

[33] Chunbin Lin, Benjamin Mandel, Yannis Papakonstantinou, and Matthias Springer. 2016. Fast In-Memory SQL Analytics on Typed Graphs. Proc. VLDB Endow. 10, 3 (2016), 265-276. https://doi.org/10.14778/3021924.3021941

[34] Yunkai Lou, Longbin Lai, Bingqing Lyu, Yufan Yang, XiaoLi Zhou, Wenyuan Yu, Ying Zhang, and Jingren Zhou. 2024. Towards a Converged Relational-Graph Optimization Framework (Artifact). https://anonymous.4open.science/r/relgo-artifact2-C4F0

[35] Yunkai Lou, Longbin Lai, Bingqing Lyu, Yufan Yang, XiaoLi Zhou, Wenyuan Yu, Ying Zhang, and Jingren Zhou. 2024. Towards a Converged Relational-Graph Optimization Framework (Full Version). https://anonymous.4open.science/r/relgo-artifact2-C4F0/paper/paper.pdf

[36] Amine Mhedhbi and Semih Salihoglu. 2019. Optimizing subgraph queries by combining binary and worst-case optimal joins. Proc. VLDB Endow. 12, 11 (jul 2019), 1692-1704.

[37] Thomas Neumann and Michael J. Freitag. 2020. Umbra: A Disk-Based System with In-Memory Performance. In 10th Conference on Innovative Data Systems Research, CIDR 2020, Amsterdam, The Netherlands, January 12-15, 2020, Online Proceedings. www.cidrdb.org. http://cidrdb.org/cidr2020/papers/p29-neumann-cidr20.pdf

[38] Thomas Neumann and Guido Moerkotte. 2011. Characteristic sets: Accurate cardinality estimation for RDF queries with multiple joins. In Proceedings of the 27th International Conference on Data Engineering, ICDE 2011, April 11-16, 2011, Hannover, Germany, Serge Abiteboul, Klemens Böhm, Christoph Koch, and Kian-Lee Tan (Eds.). IEEE Computer Society, 984-994. https://doi.org/10.1109/ICDE.2011.5767868

[39] Hung Q Ngo, Ely Porat, Christopher Ré, and Atri Rudra. 2018. Worst-case optimal join algorithms. Journal of the ACM (JACM) 65, 3 (2018), 1-40.

[40] Oracle. 2023. Property Graph Queries (SQL/PGQ). International Organization for Standardization. Retrieved June, 2023 from https://www.iso.org/standard/79473.html

[41] Yeonsu Park, Seongyun Ko, Sourav S. Bhowmick, Kyoungmin Kim, Kijae Hong, and Wook-Shin Han. 2020. G-CARE: A Framework for Performance Benchmarking of Cardinality Estimation Techniques for Subgraph Matching. In Proceedings of the 2020 International Conference on Management of Data, SIGMOD Conference 2020, online conference [Portland, OR, USA], June 14-19, 2020, David Maier, Rachel Pottinger, AnHai Doan, Wang-Chiew Tan, Abdussalam Alawini, and Hung Q. Ngo (Eds.). ACM, 1099-1114. https://doi.org/10.1145/3318464.3389702

[42] Protocol Buffers. 2024. https://protobuf.dev/overview/.

[43] Yuan Qiu, Yilei Wang, Ke Yi, Feifei Li, Bin Wu, and Chaoqun Zhan. 2021. Weighted Distinct Sampling: Cardinality Estimation for SPJ Queries. In SIGMOD '21: International Conference on Management of Data, Virtual Event, China, June 20-25, 2021, Guoliang Li, Zhanhuai Li, Stratos Idreos, and Divesh Srivastava (Eds.). ACM, 1465-1477.

[44] Haichuan Shang, Ying Zhang, Xuemin Lin, and Jeffrey Xu Yu. 2008. Taming Verification Hardness: An Efficient Algorithm for Testing Subgraph Isomorphism. Proc. VLDB Endow. 1, 1 (aug 2008), 364-375. https://doi.org/10.14778/ 1453856.1453899

[45] Sijie Shen, Zihang Yao, Lin Shi, Lei Wang, Longbin Lai, Qian Tao, Li Su, Rong Chen, Wenyuan Yu, Haibo Chen, Binyu Zang, and Jingren Zhou. 2023. Bridging the Gap between Relational OLTP and Graph-based OLAP. In 2023 USENIX Annual Technical Conference (USENIX ATC 23). USENIX Association, Boston, MA, 181-196. https://www.usenix.org/ conference/atc23/presentation/shen

[46] Il-Yeol Song, Mary Evans, and Eun K Park. 1995. A comparative analysis of entity-relationship diagrams. Journal of Computer and Software Engineering 3, 4 (1995), 427-459.

[47] Daniel ten Wolde, Tavneet Singh, Gábor Szárnyas, and Peter A. Boncz. 2023. DuckPGQ: Efficient Property Graph Queries in an analytical RDBMS. In 13th Conference on Innovative Data Systems Research, CIDR 2023, Amsterdam, The Netherlands, January 8-11, 2023. www.cidrdb.org. https://www.cidrdb.org/cidr2023/papers/p66-wolde.pdf

[48] Daniel ten Wolde, Gábor Szárnyas, and Peter A. Boncz. 2023. DuckPGQ: Bringing SQL/PGQ to DuckDB. Proc. VLDB Endow. 16, 12 (2023), 4034-4037. https://doi.org/10.14778/3611540.3611614

[49] Julian R Ullmann. 1976. An algorithm for subgraph isomorphism. Journal of the ${ACM}\left( {\oint {ACM}}\right) {23},1\left( {1976}\right) ,{31} - {42}$ .

[50] Yisu Remy Wang, Max Willsey, and Dan Suciu. 2023. Free Join: Unifying Worst-Case Optimal and Traditional Joins. Proc. ACM Manag. Data 1, 2 (2023), 150:1-150:23. https://doi.org/10.1145/3589295

[51] Zhengyi Yang, Longbin Lai, Xuemin Lin, Kongzhang Hao, and Wenjie Zhang. 2021. HUGE: An Efficient and Scalable Subgraph Enumeration System. In SIGMOD '21: International Conference on Management of Data, Virtual Event, China, June 20-25, 2021, Guoliang Li, Zhanhuai Li, Stratos Idreos, and Divesh Srivastava (Eds.). ACM, 2049-2062. https://doi.org/10.1145/3448016.3457237