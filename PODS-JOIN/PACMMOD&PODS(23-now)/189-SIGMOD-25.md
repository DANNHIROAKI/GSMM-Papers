## Data Chunk Compaction in Vectorized Execution

## 向量化执行中的数据块压缩

YIMING QIAO, Institute for Interdisciplinary Information Sciences, Tsinghua University, China HUANCHEN ZHANG*, Institute for Interdisciplinary Information Sciences, Tsinghua University, China

乔一鸣，清华大学交叉信息研究院，中国 张焕宸*，清华大学交叉信息研究院，中国

Modern analytical database management systems often adopt vectorized query execution engines that process columnar data in batches (i.e., data chunks) to minimize the interpretation overhead and improve CPU parallelism. However, certain database operators, especially hash joins, can drastically reduce the number of valid entries in a data chunk, resulting in numerous small chunks in an execution pipeline. These small chunks cannot fully enjoy the benefits of vectorized query execution, causing significant performance degradation. The key research question is when and how to compact these small data chunks during query execution. In this paper, we first model the chunk compaction problem and analyze the trade-offs between different compaction strategies. We then propose a learning-based algorithm that can adjust the compaction threshold dynamically at run time. To answer the "how" question, we propose a compaction method for the hash join operator, called logical compaction, that minimizes data movements when compacting data chunks. We implemented the proposed techniques in the state-of-the-art DuckDB and observed up to ${63}\%$ speedup when evaluated using the Join Order Benchmark, TPC-H, and TPC-DS.

现代分析型数据库管理系统常采用向量化查询执行引擎，通过批量处理列式数据（即数据块）来降低解释开销并提升CPU并行度。然而某些数据库操作符（尤其是哈希连接）会大幅减少数据块中的有效条目，导致执行流水线中出现大量小数据块。这些小数据块无法充分享受向量化查询执行的优势，造成显著的性能下降。核心研究问题在于如何动态判定并压缩这些小型数据块。本文首先建立数据块压缩问题模型，分析不同压缩策略的权衡关系，随后提出一种基于学习的算法，可在运行时动态调整压缩阈值。针对"如何压缩"问题，我们为哈希连接操作符设计了逻辑压缩方法，在压缩数据块时最小化数据移动。将所提技术实现在前沿的DuckDB系统后，基于Join Order Benchmark、TPC-H和TPC-DS的测试显示最高可达${63}\%$的性能提升。

CCS Concepts: - Information systems $\rightarrow$ Database query processing.

CCS概念: - 信息系统 $\rightarrow$ 数据库查询处理

Additional Key Words and Phrases: Vectorized Query Execution

附加关键词: 向量化查询执行

## ACM Reference Format:

## ACM参考文献格式:

Yiming Qiao and Huanchen Zhang. 2025. Data Chunk Compaction in Vectorized Execution. Proc. ACM Manag. Data 3, 1 (SIGMOD), Article 26 (February 2025), 25 pages. https://doi.org/10.1145/3709676

乔一鸣, 张焕宸. 2025. 向量化执行中的数据块压缩. ACM数据管理汇刊 3, 1 (SIGMOD), 文章26 (2025年2月), 25页. https://doi.org/10.1145/3709676

## 1 Introduction

## 1 引言

Vectorized execution refers to the query processing model where each database operator computes on a vector of tuples (i.e., a data chunk) rather than a single tuple at a time. Many modern analytical databases $\left\lbrack  {{10},{11},{31},{46},{50}}\right\rbrack$ adopt vectorized execution to accelerate query processing because it reduces the interpretation overhead and improves CPU parallelism [5]. The vector size is critical to the overall query performance. If the vector is too large to fit in the CPU cache, performance will suffer from cache misses. On the other hand, if the vector contains too few tuples, vectorized execution will degenerate into the classic volcano model [12], losing the aforementioned advantages.

向量化执行是指查询处理模型中的每个操作符对元组向量（即数据块）而非单个元组进行计算。现代分析型数据库$\left\lbrack  {{10},{11},{31},{46},{50}}\right\rbrack$采用向量化执行来加速查询处理，因其能降低解释开销并提升CPU并行度[5]。向量大小对查询性能至关重要——过大的向量会超出CPU缓存导致缓存失效，而过小的向量会使向量化执行退化为经典的火山模型[12]，丧失原有优势。

Boncz et al. [5] showed empirically that the optimal size of a data chunk is in a few thousand tuples (e.g., 2048). Although we can initialize the input data chunks to this optimal size, the number of valid tuples within each chunk can be reduced by certain operators during query execution [21]. We call these operators Chunk-Reducing Operators (CROs). The most common CROs are filters and hash joins. After a data chunk goes through a filter operator, it updates its selection vector or bitmap and thus reduces its size effectively [29]. A vectorized hash join implementation takes a data chunk to probe hash table buckets in parallel [32]. Because each bucket can contain many

Boncz等[5]通过实验证明最优数据块大小约为数千个元组（如2048）。虽然可将输入数据块初始化为该最优尺寸，但查询执行过程中某些操作符会减少块内有效元组数量[21]，我们称这类操作符为块缩减操作符(CRO)。最常见的CRO是过滤器和哈希连接。数据块经过滤器处理后，会更新其选择向量或位图从而有效缩小尺寸[29]。向量化哈希连接实现会并行探测哈希表桶[32]，由于每个桶可能包含大量

This work is licensed under a Creative Commons Attribution International 4.0 License. items due to repeated values and hash collisions [45], an input data chunk often generates multiple (smaller) output chunks with unmatched tuples invalidated after the hash join.

本作品采用知识共享署名国际4.0许可协议。因重复值和哈希冲突产生的条目[45]，输入数据块常生成多个（更小的）输出块，其中未匹配元组在哈希连接后失效。

---

<!-- Footnote -->

*Huanchen Zhang is also affiliated with the Shanghai Qi Zhi Institute. Corresponding author.

*张焕宸同时隶属上海期智研究院。通讯作者。

Authors' Contact Information: Yiming Qiao, Institute for Interdisciplinary Information Sciences, Tsinghua University, Beijing, China, qiaoym21@mails.tsinghua.edu.cn; Huanchen Zhang, Institute for Interdisciplinary Information Sciences, Tsinghua University, Beijing, China, huanchen@tsinghua.edu.cn.

作者联系方式：乔一鸣，清华大学交叉信息研究院，北京，中国，qiaoym21@mails.tsinghua.edu.cn；张焕宸，清华大学交叉信息研究院，北京，中国，huanchen@tsinghua.edu.cn。

<!-- Footnote -->

---

<!-- Media -->

<!-- figureText: 10% 39% 1000 1250 1500 1750 2000 Chunk-reducing Factor 8% Percentage 6% 4% 2% 0% 1 250 500 750 -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_1.jpg?x=344&y=277&w=881&h=430&r=0"/>

Fig. 1. Distribution of the Chunk-Reducing Factor (CRF) - We executed the JOB on DuckDB and collected the CRF for every data chunk that goes through a hash join operator. The default chunk size is 2048. It shows that ${39}\%$ of all the data chunks have a CRF of 2048,which means each of them contains only one record.

图1. 块缩减因子(CRF)分布——在DuckDB上执行JOB测试，统计经哈希连接操作符的所有数据块CRF值（默认块大小2048）。结果显示${39}\%$的数据块CRF为2048，意味着每个块仅含单条记录。

<!-- Media -->

We define the Chunk-Reducing Factor (CRF) as the chunk size entering the operator divided by the chunk size exiting the operator. We executed the Join Order Benchmark (JOB) [26] on DuckDB [37] and collected the CRF for every data chunk that goes through a hash join operator. Figure 1 shows the statistics for CRF. We observe that a majority of the data chunks become significantly smaller after a hash join, and these smaller chunks lead to increased interpretation overhead and decreased CPU parallelism for the downstream operators.

我们将块缩减因子(CRF)定义为进入操作符的块大小与离开时的比值。在DuckDB[37]上执行Join Order Benchmark(JOB)[26]并收集经哈希连接的所有数据块CRF值。图1显示CRF统计结果：多数数据块经哈希连接后显著缩小，这些小数据块会导致下游操作符解释开销增加且CPU并行度降低。

Therefore, compacting small chunks during execution is essential for the vectorized execution model to achieve superior performance. However, compacting data chunks involves memory copies, and such costs may outweigh the benefits of having proper-sized vectors. DuckDB handles this trade-off by predefining a size threshold $\alpha  = {128}$ . When an output chunk contains $\leq  \alpha$ valid tuples, it is copied to a buffer chunk, and the buffer chunk is sent to the next operator when it accumulates enough tuples close to its capacity (i.e., 2048 tuples). This fixed-threshold approach can be inefficient because the trade-off between interpretation overhead and memory movement is different for each chunk-reducing operator and is dependent on the number of subsequent operators in the execution pipeline. For example, there is no need to perform chunk compaction if the operator is at the end of a pipeline (i.e., a sink operator).

因此，在执行过程中压缩小块数据对于向量化执行模型实现卓越性能至关重要。然而压缩数据块涉及内存拷贝，这种开销可能超过获得适当大小向量的收益。DuckDB通过预定义大小阈值$\alpha  = {128}$来处理这种权衡——当输出块包含$\leq  \alpha$个有效元组时，会将其复制到缓冲块，而缓冲块在累积接近其容量（即2048个元组）时会被发送至下一操作符。这种固定阈值方法可能效率低下，因为解释开销与内存移动之间的权衡关系会随每个块缩减操作符而变化，并取决于执行流水线中后续操作符的数量。例如，若操作符位于流水线末端（即接收操作符），则无需执行块压缩。

In this paper, we investigate when and how to compact small data chunks efficiently during query execution. We first define the chunk compaction problem and model the operator's interpretation cost and the chunk's compaction cost. We then answer the "when" question by introducing a lightweight learning-based algorithm (based on the Multi-Armed Bandit problem) to determine the compaction threshold dynamically for each CRO at run time. To approach the "how" question, we propose logical compaction that avoids unnecessary data movement when compacting small chunks for vectorized hash join probes. The key idea is to have separate selection vectors for the columns from both sides of the join operator within a chunk. We implemented the proposed techniques including learning-based dynamic compaction and logical compaction in DuckDB, a state-of-the-art analytical database, and achieved an end-to-end speedup of 11.8%, 6.1%, and 4.6% for all the queries in Join Order Benchmark (JOB) [26], TPC-DS [8], and TPC-H [9], respectively. For queries where hash joins have high CRFs, the performance improvement can be up to 63% compared to the DuckDB default.

本文研究查询执行过程中高效压缩小块数据的时机与方法。我们首先定义块压缩问题，建模操作符的解释成本与块压缩成本；随后通过引入基于多臂老虎机问题的轻量级学习算法，动态确定每个运行时块缩减操作符(CRO)的压缩阈值来解答"何时"问题；针对"如何"问题，我们提出逻辑压缩方案，在向量化哈希连接探测时避免不必要的数据移动——其核心思想是在块内为连接操作符两侧的列维护独立的选择向量。我们在先进分析型数据库DuckDB中实现了基于学习的动态压缩与逻辑压缩技术，使连接顺序基准(JOB)、TPC-DS和TPC-H全部查询分别获得11.8%、6.1%和4.6%的端到端加速。对于哈希连接具有高CRF的查询，相较DuckDB默认方案性能提升最高可达63%。

<!-- Media -->

<!-- figureText: Real Vector: A vector that directly allocates and manages its own memory Valid Tuples: Tuples marked by the SV RHS Columns RHS Columns RXM RSS 4 SV Columns SV Columns SV ② Result Chunk ③ Result Chunk Referencing Vector: A vector that holds a reference to data stored in another vector ✓ > x > v 42 → RHS Columns 34 Next 07 大 68 Columns SV 22 Columns Input Chunk ② ③ ① Result Chunk Hash Table Buckets -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_2.jpg?x=140&y=255&w=1291&h=419&r=0"/>

Fig. 2. Vectorized Hash Join - Hashes join keys, finds matched tuples, and gathers payloads. LHS columns are zero-copy, while RHS columns require copying. We call Next (   ) three times to generate three chunks, because the buckets have chains up to length three.

图2. 向量化哈希连接——哈希连接键、匹配元组并收集载荷。左侧列(LHS)实现零拷贝，右侧列(RHS)需复制。我们调用Next()三次生成三个块，因哈希桶链长度最大为三。

<!-- Media -->

We make four primary contributions in this paper. First, we define and provide a performance analysis of the chunk compaction problem. Second, we propose an online learning-based algorithm for adjusting the compaction threshold of each chunk-reducing operator. Third, we introduce logical compaction that can minimize data movement for chunks output by hash join probes. Finally, we verify in DuckDB that the proposed solutions improve end-to-end query performance, especially for queries with multiple joins.

本文四项主要贡献：首先定义并分析块压缩问题的性能特征；其次提出基于在线学习的算法动态调整块缩减操作符的压缩阈值；第三引入可最小化哈希连接探测输出块数据移动的逻辑压缩方案；最后在DuckDB中验证所提方案能提升端到端查询性能，特别针对含多连接的查询。

## 2 Background and Related Work

## 2 背景与相关工作

In this section, we offer the essential background on the vectorized query execution [5, 36] and the vectorized hash join operator [32].

本节提供向量化查询执行[5,36]与向量化哈希连接操作符[32]的基础背景。

### 2.1 Vectorized Model

### 2.1 向量化模型

Vectorized execution, typically implemented with the morsel-driven parallelism [25], has been widely adopted in modern analytical databases $\left\lbrack  {4,7,{10},{27},{37},{41}}\right\rbrack$ . The classic Volcano model [12] executes queries by calling the Next (   ) interface implemented by each relational algebra operator to pull one result tuple at a time [36]. However, the fixed overhead of repeatedly invoking the Next (   ) function is noticeable [17, 28]. The computational primitives in the operators must support a wide range of data types through programming techniques such as late-binding methods, function pointers, or extensive case switches, thereby introducing interpretation overhead [21, 29].

采用分片驱动并行[25]实现的向量化执行已被现代分析型数据库广泛采用$\left\lbrack  {4,7,{10},{27},{37},{41}}\right\rbrack$。经典火山模型[12]通过调用各关系代数操作符实现的Next()接口每次拉取一个结果元组[36]，但重复调用Next()的固定开销显著[17,28]。操作符中的计算原语必须通过延迟绑定方法、函数指针或大量条件分支等技术支持多种数据类型，从而产生解释开销[21,29]。

To amortize the interpretation overhead, the vectorized execution model processes a batch of tuples (e.g., 2048 tuples) for each call of Next (   ). The computational primitives are put in a tight for loop for the tuple batch to fully leverage the parallelism in modern super-scalar and out-of-order CPUs $\left\lbrack  {{13},{15}}\right\rbrack$ . Specifically,each execution pipeline processes a vector (i.e.,data chunk) at a time without the need to materialize the intermediate results. The vector size greatly impacts query performance: too small vectors cause the engine to degenerate to the classic Volcano model, while too large vectors cause excessive cache misses. Prior studies showed that the optimal vector size is in a few thousand tuples [5].

为分摊解释开销，向量化执行模型每次调用Next()处理一批元组(如2048个)。计算原语被置于紧凑循环中处理元组批，以充分利用现代超标量乱序CPU的并行能力$\left\lbrack  {{13},{15}}\right\rbrack$。具体而言，每个执行流水线每次处理一个向量(即数据块)，无需物化中间结果。向量大小显著影响查询性能：过小会导致引擎退化为火山模型，过大则引发过多缓存未命中。先前研究表明最优向量大小在数千元组量级[5]。

Each data chunk uses a selection vector or bitmap to identify the valid tuples [29, 33]. For example, DuckDB [37] and Vectorwise [50] use a selection vector, while DB2 with BLU [39] employs a selection bitmap. As shown in Figure 2,the selection vector(1,3)indicates that the first and the third tuples are still valid in the data chunk. Applying a selection vector can avoid unnecessary data copy between input and output chunks, but the number of valid tuples within a chunk (i.e., the chunk size) can keep decreasing during execution.

每个数据块使用选择向量或位图来标识有效元组[29,33]。例如DuckDB[37]和Vectorwise[50]采用选择向量，而搭载BLU的DB2[39]使用选择位图。如图2所示，选择向量(1,3)表示该数据块中第一和第三个元组仍有效。应用选择向量可避免输入输出块间不必要的数据拷贝，但块内有效元组数（即块大小）会在执行过程中持续递减。

### 2.2 Vectorized Hash Join

### 2.2 向量化哈希连接

A scalar lookup in a chaining hash table involves three steps: (1) hash the tuple’s join key ${k}_{1}$ to find the bucket; (2) compare ${k}_{1}$ to the first key ${k}_{2}$ in the bucket; (3) if ${k}_{1} = {k}_{2}$ ,copy the payload to the result tuple and continue to compare ${k}_{1}$ to the next key in that bucket. During this processing, if the hash table does not fit in the CPU cache,accessing ${k}_{2}$ from the bucket will cause a cache miss [43]. Such random memory accesses can easily become the performance bottleneck of a scalar hash table $\left\lbrack  {{21},{45}}\right\rbrack$ . One solution is to radix partition the table according to the join keys so that the hash table for each partition fits in cache $\left\lbrack  {2,3,{22},{40}}\right\rbrack$ . Although this approach reduces cache misses for hash table probes, it introduces the additional partitioning step that often dominates the join performance [2].

链式哈希表中的标量查找包含三步：(1)对元组连接键${k}_{1}$哈希定位桶；(2)将${k}_{1}$与桶内首个键${k}_{2}$比较；(3)若${k}_{1} = {k}_{2}$匹配，则复制载荷到结果元组并继续比较该桶中下一个键。此过程中若哈希表超出CPU缓存，访问桶内${k}_{2}$将引发缓存未命中[43]。这类随机内存访问易成为标量哈希表$\left\lbrack  {{21},{45}}\right\rbrack$的性能瓶颈。解决方案是按连接键进行基数分区，使各分区哈希表适配缓存$\left\lbrack  {2,3,{22},{40}}\right\rbrack$。虽然该方法减少了哈希表探测的缓存未命中，但引入的分区步骤常成为连接性能的主要开销[2]。

A vectorized hash join [25] addresses the above problems by issuing a batch of hash table probes at once to better utilize the memory bandwidth [6, 32]. Figure 2 shows an example. A chaining hash table is constructed for the right-hand-side (RHS) table. To perform a vectorized hash join, we first hash all the join keys in the input chunk from the left-hand-side (LHS) table and obtain a vector of bucket numbers(13,42,21,34,07,68,22). We then issue a batch of memory reads to load the first item in each of the selected buckets and compare it to the corresponding input join key(s). The output is a bitmap(1,1,1,1,1,0,1,1)indicating if each input join key finds a match. Finally,we construct the result chunk ① by referencing the input chunk for the LHS columns (zero-copy) and gathering the payloads from the matching tuples for the RHS columns [20]. The selection vector in this result chunk is $\mathrm{{SV}} = \left( {0,1,2,3,4,6,7}\right)$ because the 5th input tuple did not find a match.

向量化哈希连接[25]通过批量发起哈希表探测来优化内存带宽利用率[6,32]，从而解决上述问题。如图2示例，首先为右侧表(RHS)构建链式哈希表。对左侧表(LHS)输入块中所有连接键哈希后获得桶号向量(13,42,21,34,07,68,22)，然后批量读取各选定桶的首项并与对应输入键比较，生成匹配位图(1,1,1,1,1,0,1,1)。最终通过引用LHS列输入块（零拷贝）和收集RHS列匹配元组载荷[20]构建结果块①，其选择向量为$\mathrm{{SV}} = \left( {0,1,2,3,4,6,7}\right)$（因第5个输入元组未匹配）。

We repeat the above process for the second item in each selected bucket. Again, the memory probes are issued in parallel,and the key comparison result is(0,1,0,1,0,0,0,0). Consequently, the next result chunk ② contains an $\mathrm{{SV}} = \left( {1,3}\right)$ . This batch-probing process continues until we reach the end of the bucket with the longest chain. A key characteristic of the vectorized hash join, therefore, is that an input/probing chunk can generate multiple smaller output chunks. This issue is mainly unavoidable due to data skew (i.e., repeated join keys) and remains independent of hashing schemes.

对每个选定桶的第二项重复上述过程，此时批量内存探测得到的键比较结果为(0,1,0,1,0,0,0,0)，因此下一个结果块②包含$\mathrm{{SV}} = \left( {1,3}\right)$。此批处理探测持续至最长链桶尾端。向量化哈希连接的关键特性在于：输入/探测块可能生成多个较小输出块，该现象主要由数据倾斜（重复连接键）引起且与哈希方案无关。

Trade-off: Chunk Size vs. Zero-copy Benefit. To avoid small chunks, many databases, such as Apache DataFusion [23] and CockroachDB [46], sacrifice the zero-copy benefit. They copy both the LHS and RHS columns to produce full output data chunks [18, 19]. On the other hand, DuckDB and Velox prefer the zero-copy approach. The trade-off of this approach is that it can generate under-full chunks. DuckDB then predefines a size threshold and only compacts chunks with the number of tuples smaller than the threshold. In the next section, we analyze the inefficiency of current approaches.

权衡：块大小与零拷贝收益。为避免小块问题，Apache DataFusion[23]和CockroachDB[46]等系统牺牲零拷贝优势，通过拷贝LHS和RHS列生成完整输出块[18,19]。而DuckDB和Velox选择零拷贝方案，代价是可能产生未填满块。DuckDB预设大小阈值，仅压缩元组数低于阈值的块。下一节我们将分析现有方法的低效性。

## 3 The Chunk Compaction Problem

## 3 块压缩问题

In this section, we formalize the compaction problem by analyzing the trade-off between the interpretation overhead and the data-copying cost.

本节通过分析解释开销与数据拷贝成本的权衡，形式化定义压缩问题。

### 3.1 Motivation

### 3.1 研究动机

A Chunk-Reducing Operator (CRO) refers to an operator that can reduce the number of valid entries in a data chunk. The most common CROs are filters and hash joins. A query pipeline consisting of CROs can reduce the chunk sizes progressively while generating more chunks. For example, Figure 3 shows a query plan of joining four tables $R,S,T,U$ on columns $A,B$ ,and $C$ . This plan comprises four pipelines: three building pipelines ①-③ and one probing pipeline ④. As shown in the example, pipeline ④ receives 10 input chunks, each of 2000 tuples. The filter operator makes the data chunks $3/4$ full on average. At the first hash join,the operator produces 400 chunks out of the 10 input chunks with each containing 75 tuples on average. The next join uses these 400 chunks to probe the hash table and generates 8000 chunks with a total of only 10,000 tuples, averaging 1.25 tuples per chunk. Such small data chunks, therefore, cause significant interpretation overhead for the final hash join.

块缩减运算符(CRO)指能减少数据块中有效条目数量的运算符。最常见的CRO是过滤器和哈希连接。由CRO组成的查询管道可以在生成更多数据块的同时逐步减小块大小。例如，图3展示了在列$R,S,T,U$、$A,B$和$C$上连接四个表的查询计划。该计划包含四个管道：三个构建管道①-③和一个探测管道④。如示例所示，管道④接收10个输入块，每块含2000个元组。过滤器运算符使数据块$3/4$平均达到满载。在第一个哈希连接处，运算符将10个输入块转换为400个块，每块平均含75个元组。下一个连接用这400个块探测哈希表，生成共8000个块但仅含10000个元组，平均每块1.25个元组。如此小的数据块会导致最终哈希连接产生显著的解释开销。

<!-- Media -->

<!-- figureText: #of Chunks 1.25 tuples/chunk! ④ 8000 400 30,000 10 15,000 10 20,000 -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_4.jpg?x=333&y=268&w=904&h=360&r=0"/>

Fig. 3. Pipelines of a Joining Query - The chunk becomes smaller and smaller in the probe pipeline as shown on the left.

图3. 连接查询的管道 - 如左图所示，探测管道中的数据块变得越来越小

<!-- Media -->

Compacting smaller chunks into larger ones can reduce the interpretation overhead for subsequent operators but it involves allocating new data chunks and copying tuples from the smaller chunks into them. The decision of when to perform the compaction depends on balancing the overhead of interpretation and tuple copying. In general, the smaller the chunk, the greater the benefit from such compaction.

将小块压缩成大块可降低后续运算符的解释开销，但需要分配新数据块并将元组从小块复制到其中。何时执行压缩取决于解释开销与元组复制开销的平衡。一般而言，块越小，此类压缩的收益越大。

### 3.2 Problem Formulation

### 3.2 问题表述

Consider $n$ data chunks with sizes $S = \left\{  {{d}_{1},\cdots ,{d}_{n}}\right\}$ ,where ${d}_{i}$ is an integer. A chunk can contain a maximum of $D\left( {D = {2048}\text{by default}}\right)$ tuples,i.e., $1 \leq  {d}_{i} \leq  D$ for all $i$ . These chunks are processed by a pipeline with $k$ chunk-reducing operators. The pipeline needs time ${\mathcal{F}}_{k}\left( d\right)$ to process a chunk of size $d$ . Let $M$ denote a compaction on chunk set $S$

考虑$n$个大小为$S = \left\{  {{d}_{1},\cdots ,{d}_{n}}\right\}$的数据块，其中${d}_{i}$为整数。每个块最多含$D\left( {D = {2048}\text{by default}}\right)$个元组，即对所有$i$有$1 \leq  {d}_{i} \leq  D$。这些块由含$k$个块缩减运算符的管道处理。管道处理大小为$d$的块需时${\mathcal{F}}_{k}\left( d\right)$。设$M$表示对块集$S$的压缩

$$
M : S \rightarrow  R \triangleq  \left\{  {{d}_{1}^{\prime },\cdots ,{d}_{m}^{\prime }}\right\}  
$$

that produces chunk set $R$ with a reduced number of chunks $\left( {1 \leq  m \leq  n}\right)$ while preserving the total tuple count. Let $\mathcal{G}\left( {M,S}\right)$ denote the time required for applying the compaction $M$ on $S$ . The goal is to minimize the total execution time $\mathop{\sum }\limits_{{{d}^{\prime } \in  R}}{\mathcal{F}}_{k}\left( {d}^{\prime }\right)  + \mathcal{G}\left( {M,S}\right)$ . Each operator in a pipeline encounters such a compaction challenge and must decide when to perform a compaction locally and collectively establish a globally optimal compaction policy.

该压缩生成块集$R$，在保持总元组数前提下减少块数至$\left( {1 \leq  m \leq  n}\right)$。设$\mathcal{G}\left( {M,S}\right)$表示对$S$应用压缩$M$所需时间。目标是使总执行时间$\mathop{\sum }\limits_{{{d}^{\prime } \in  R}}{\mathcal{F}}_{k}\left( {d}^{\prime }\right)  + \mathcal{G}\left( {M,S}\right)$最小化。管道中每个运算符都会遇到此类压缩挑战，需在本地决定何时执行压缩，并共同建立全局最优压缩策略。

Compaction Cost. Let $g\left( d\right)$ denote the time cost of a particular compaction in $M$ where chunks ${d}_{i},\cdots ,{d}_{j}$ are compacted into a chunk of size $d \leq  D$ . We model $g\left( d\right)$ in two parts. First, $g$ scales linearly with the total number of tuples in the compacted chunks because of the per-tuple memory copy cost ${C}_{2}$ . Additionally,the compaction incurs a fixed cost ${C}_{1}$ ,which is independent of the chunk size. Therefore,

压缩成本。设$g\left( d\right)$表示$M$中特定压缩的时间成本，其中块${d}_{i},\cdots ,{d}_{j}$被压缩成大小为$d \leq  D$的块。我们将$g\left( d\right)$建模为两部分：首先，$g$与压缩块中元组总数呈线性关系，因为存在每元组内存复制成本${C}_{2}$；此外压缩还产生固定成本${C}_{1}$，与块大小无关。因此，

$$
g\left( d\right)  = {C}_{1} + d \cdot  {C}_{2}
$$

For example, as shown in Figure 4, we profiled the compaction operation in DuckDB and obtained ${C}_{1} = {0.25\mu s}$ and ${C}_{2} = {0.04\mu s}$ .

例如，如图4所示，我们分析了DuckDB中的压缩操作，测得${C}_{1} = {0.25\mu s}$和${C}_{2} = {0.04\mu s}$。

Compute Cost. Let $f\left( d\right)$ denote the time needed to process a data chunk of size $d$ by a CRO:

计算成本。设$f\left( d\right)$表示CRO处理大小为$d$的数据块所需时间：

$$
f\left( d\right)  = {C}_{3} + d \cdot  {C}_{4}
$$

where ${C}_{3}$ represents the interpretation overhead,and ${C}_{4}$ is the per-tuple computational cost. Figure 4 gives an example of ${C}_{3} = {2.4\mu }\mathrm{s}$ and ${C}_{4} = {0.08\mu }\mathrm{s}$ via profiling the hash-table probes in DuckDB.

其中${C}_{3}$表示解释开销，${C}_{4}$为每元组计算成本。图4通过分析DuckDB中的哈希表探查，展示了${C}_{3} = {2.4\mu }\mathrm{s}$与${C}_{4} = {0.08\mu }\mathrm{s}$的实例。

<!-- Media -->

<!-- figureText: $g\left( d\right)  = {C}_{1} + d \cdot  {C}_{2}$ $f\left( d\right)  = {C}_{3} + d \cdot  {C}_{4}$ 125 Zoom on Y-Intercept Probing Cost (us) 8 100 75 40 80 50 25 0 0 256 512 768 1024 Chunk Size Compaction Cost (us) Zoom on Y-Intercept 60 1.0 0.5 40 0.0 10 20 20 0 0 256 512 768 1024 Chunk Size -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_5.jpg?x=310&y=275&w=943&h=427&r=0"/>

Fig. 4. Parameter Profiling - We measure the fixed and per-tuple cost of chunk compaction and hash-table probing in DuckDB,getting ${C}_{1} = {0.25\mu s},{C}_{2} = {0.04\mu s}$ and ${C}_{3} = {2.4\mu s},{C}_{4} = {0.08\mu s}$ .

图4. 参数分析 - 我们测量了DuckDB中块压缩和哈希表探查的固定成本与每元组成本，得到${C}_{1} = {0.25\mu s},{C}_{2} = {0.04\mu s}$和${C}_{3} = {2.4\mu s},{C}_{4} = {0.08\mu s}$。

<!-- Media -->

Suppose that the number of tuples produced by each operator is $v > 0$ times the number of input tuples with a Chunk-Reducing Factor (CRF,defined in Section 1) of $r \geq  1$ . For example,if $v = 2$ and $r = 4$ for an operator,then an input chunk containing 16 valid tuples will produce ${16} \times  2 = {32}$ tuples with each output chunk consisting of ${16}/4 = 4$ tuples. The total number of output chunks is thus $v \cdot  r = 8$ . Both filters and hash join probes are CROs. A filter operator typically generates one output chunk $\left( {v \cdot  r = 1}\right)$ for each input chunk,where $1/r$ represents the filter selectivity. On the other hand,a hash join probe can produce multiple result chunks $\left( {v \cdot  r \geq  1}\right)$ out of an input chunk, as described in Section 2.2.

假设每个算子生成的元组数量是输入元组的$v > 0$倍，其块缩减因子(CRF，定义见第1节)为$r \geq  1$。例如当某算子的$v = 2$和$r = 4$时，含16个有效元组的输入块将生成${16} \times  2 = {32}$个元组，每个输出块包含${16}/4 = 4$个元组，因此输出块总数为$v \cdot  r = 8$。过滤器和哈希连接探查都属于CRO。过滤器算子通常为每个输入块生成一个输出块$\left( {v \cdot  r = 1}\right)$，其中$1/r$代表过滤选择性；而如第2.2节所述，哈希连接探查可能从单个输入块生成多个结果块$\left( {v \cdot  r \geq  1}\right)$。

The time for a $k$ -CRO pipeline to process an input chunk of size $d$ ,therefore,is

因此，$k$-CRO流水线处理大小为$d$的输入块所需时间为

$$
{\mathcal{F}}_{k}\left( d\right)  = {C}_{3} \cdot  \mathop{\sum }\limits_{{j = 1}}^{k}\min \left\{  {{r}^{j - 1},d}\right\}   \cdot  {v}^{j - 1} + {C}_{4} \cdot  d \cdot  k \tag{1}
$$

The last term is the per-tuple computing cost, which scales linearly with the number of tuples and CROs. The first term represents the interpretation cost. It is calculated based on the number of small chunks generated by the $j$ -th operator. The $j$ -th operator generates a total of $d \cdot  {v}^{j - 1}$ tuples, distributed across at most ${\left( v \cdot  r\right) }^{j - 1}$ chunks. Because the number of chunks cannot exceed the number of available tuples,the $j$ -th operator outputs at most $\min \left\{  {{r}^{j - 1},d}\right\}   \cdot  {v}^{j - 1}$ small chunks.

末项是与元组数和CRO数量成线性关系的每元组计算成本。首项表示解释成本，其计算基于第$j$个算子生成的小块数量。该算子共生成$d \cdot  {v}^{j - 1}$个元组，最多分布在${\left( v \cdot  r\right) }^{j - 1}$个块中。由于块数不超过可用元组数，第$j$个算子最多输出$\min \left\{  {{r}^{j - 1},d}\right\}   \cdot  {v}^{j - 1}$个小块。

### 3.3 A Near-optimal Greedy Strategy

### 3.3 近似最优贪心策略

In this section, we introduce a near-optimal greedy strategy, called Sort Compaction, that compacts small chunks aggressively whenever it is beneficial according to the models in Section 3.2. For an operator’s output chunk set $S$ ,we first sort the chunks by size in ascending order (i.e., $\left\{  {d}_{1}\right.  \leq$ $\left. \left. {,\cdots , \leq  {d}_{n}}\right\}  \right)$ and create a buffer chunk $B = \left\{  {d}_{1}\right\}$ with an initial size ${d}_{B} = {d}_{1}$ . We then iterate the chunk list and try to decide for each chunk ${d}_{i}$ whether to copy it into the buffer. Note that if the current buffer chunk $B$ does not have enough capacity to hold ${d}_{i}\left( {{d}_{B} + {d}_{i} > D}\right)$ ,we create a new buffer chunk that contains ${d}_{i}$ and send the current $B$ to the next operator as input. We define the benefit of compacting ${d}_{i}$ into $B$ as

本节介绍名为"排序压缩"的近似最优贪心策略，该策略根据3.2节模型在有利时积极压缩小块。对于算子的输出块集$S$，我们首先按块大小升序排序（即$\left\{  {d}_{1}\right.  \leq$$\left. \left. {,\cdots , \leq  {d}_{n}}\right\}  \right)$），并创建初始大小为${d}_{B} = {d}_{1}$的缓冲块$B = \left\{  {d}_{1}\right\}$。随后遍历块列表，决定是否将每个块${d}_{i}$复制到缓冲块中。若当前缓冲块$B$容量不足以容纳${d}_{i}\left( {{d}_{B} + {d}_{i} > D}\right)$，则创建包含${d}_{i}$的新缓冲块，并将当前$B$作为输入发送给下一算子。定义将${d}_{i}$压缩至$B$的收益为

$$
\operatorname{Gains}\left( {{d}_{B},{d}_{i}}\right)  \triangleq  {\mathcal{F}}_{k}\left( {d}_{B}\right)  + {\mathcal{F}}_{k}\left( {d}_{i}\right)  - {\mathcal{F}}_{k}\left( {{d}_{B} + {d}_{i}}\right)  - g\left( {d}_{i}\right) 
$$

where $k$ denotes the number of subsequent operators in this pipeline. ${\mathcal{F}}_{k}\left( {d}_{B}\right)  + {\mathcal{F}}_{k}\left( {d}_{i}\right)$ and ${\mathcal{F}}_{k}\left( {{d}_{B} + {d}_{i}}\right)$ represent the time cost without and with this particular compaction,respectively. $g\left( {d}_{i}\right)$ is the time cost for this compaction.

其中$k$表示该流水线中后续操作符的数量。${\mathcal{F}}_{k}\left( {d}_{B}\right)  + {\mathcal{F}}_{k}\left( {d}_{i}\right)$和${\mathcal{F}}_{k}\left( {{d}_{B} + {d}_{i}}\right)$分别代表未采用与采用此特定压缩时的耗时。$g\left( {d}_{i}\right)$是本轮压缩的耗时。

<!-- Media -->

<!-- figureText: $r = 4,k = 3,{C}_{1} = 6,{C}_{2} = 1,{C}_{3} = {60},{C}_{4} = 2$ $r = \mathbf{{16}},k = 3,{C}_{1} = 6,{C}_{2} = 1,{C}_{3} = {60},{C}_{4} = 2$ $r = \mathbf{{256}},k = 3,{C}_{1} = 6,{C}_{2} = 1,{C}_{3} = {60},{C}_{4} = 2$ Compaction Cost 1470 Compaction Cost 300 Compute Cost Estimated Cost Compute Cost $\underline{161.9}$ 100 60.2 60.2 150.6 40.2 40.1 20.1 Binary Dynamic Sort Nc Full Dynamic Sort Case 2: Medium CRF Case 3: High CRF $r = {16},k = \mathbf{4},{C}_{1} = 6,{C}_{2} = \mathbf{{10}},{C}_{3} = {60},{C}_{4}$ $r = {16},k = \mathbf{5},{C}_{1} = 6,{C}_{2} = \mathbf{{10}},{C}_{3} = {60},{C}_{4}$ Compaction Cost 2760 Compaction Cost Compute Cost 1500 Compute Cost Estimated Cost 1000 1012.0 $\underline{813.5}$ 569.9 500 771.3 572.2 520.6 200.0 0.0 Sort No Full Binary Dynamic Sort Case 5: Case 6: Large Tuple & Medium Pipeline Large Tuple & Deep Pipeline 250 Compaction Cost 300 279.9 Estimated Cost 200 Compute Cost Estimated Cost 200 181.9 100 60.2 $\underline{181.9}$ 150 $\underline{152.4}$ 132.3 132.3 132.3 100 60.2 50 20.1 0.0 0 No Full ry Dynamic Sort No Full Case 1: Low CRF $r = {16},k = \mathbf{3},{C}_{1} = 6,{C}_{2} = \mathbf{{10}},{C}_{3} = {60},{C}_{4} =$ 800 Compaction Cost 1250 1520 $\underline{721.9}$ Compute Cost Estimated Cost 1000 750 500 Estimated Cost 600 400 $\underline{279.9}$ $\underline{279.9}$ 200 250 0.0 0 No Full Binary Dynamic Sort No Full Case 4: Large Tuple & Shallow Pipeline -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_6.jpg?x=142&y=264&w=1286&h=735&r=0"/>

Fig. 5. Compaction Simulation - Comparing compaction strategies by varying the CRF and the number of join operators.

图5. 压缩模拟实验 - 通过改变CRF与连接操作符数量来对比不同压缩策略。

<!-- Media -->

If $\operatorname{Gains}\left( {{d}_{B},{d}_{i}}\right)  \geq  0$ ,we add ${d}_{i}$ to $B$ and update ${d}_{B} = {d}_{B} + {d}_{i}$ . Otherwise,we finish the compaction as the remaining chunks are all larger than ${d}_{i}$ ,therefore,leading to lower gains. We simplify the Gains function by substituting Equation (1):

若$\operatorname{Gains}\left( {{d}_{B},{d}_{i}}\right)  \geq  0$，则将${d}_{i}$添加至$B$并更新${d}_{B} = {d}_{B} + {d}_{i}$。否则终止压缩流程，因剩余数据块均大于${d}_{i}$，继续操作收益递减。我们通过代入公式(1)简化收益函数：

$$
\operatorname{Gains}\left( {{d}_{B},{d}_{i}}\right)  = \underset{\text{Direct Gain }}{\underbrace{\left( {C}_{3} - g\left( {d}_{i}\right) \right) }} + \underset{\text{Pipeline-level Gain }}{\underbrace{\left( {C}_{3} \cdot  \mathop{\sum }\limits_{{j = 1}}^{{k - 1}}{A}_{j}\right) }} \tag{2}
$$

where ${A}_{j} = {v}^{j} \cdot  \left\{  {\min \left( {{r}^{j},{d}_{B}}\right)  + \min \left( {{r}^{j},{d}_{i}}\right)  - \min \left( {{r}^{j},{d}_{B} + {d}_{i}}\right) }\right\}$ . The Gains function consists of two terms: 1) the Direct Gain, representing the immediate benefit to the current operator from reducing its output chunks by one, and 2) the Pipeline-level Gain, reflecting the benefit for the subsequent operators. The term ${A}_{j}$ quantifies the reduction in output chunks produced by the $j$ -th operator in the pipeline when ${d}_{i}$ is compacted with ${d}_{B}$ .

其中${A}_{j} = {v}^{j} \cdot  \left\{  {\min \left( {{r}^{j},{d}_{B}}\right)  + \min \left( {{r}^{j},{d}_{i}}\right)  - \min \left( {{r}^{j},{d}_{B} + {d}_{i}}\right) }\right\}$。收益函数包含两项：1)直接收益，表示当前操作符因减少一个输出块获得的即时收益；2)流水线级收益，反映后续操作符获得的收益。项${A}_{j}$量化了当${d}_{i}$与${d}_{B}$压缩时，流水线中第$j$个操作符产生的输出块减少量。

Because of the pipeline-level gain, the position of an operator within the pipeline affects its compaction policy: the preceding operators should adopt an aggressive policy, while the subsequent operators should apply a conservative policy. Specifically, even if the direct gain for the current operator is negative,compacting ${d}_{i}$ may still be beneficial. This is because it reduces the number of chunks that subsequent operators must process. For example, consider a pipeline with two joins $\left( {k = 2}\right)$ where $S = \{ {100},{400}\} ,r = {110}$ ,and $v = 1$ . Despite a negative direct gain in Gains(100,400) $\left( {{C}_{3} - g\left( {400}\right)  =  - {13.85}}\right)$ ,compaction reduces the interpretation overhead for the subsequent operators: without compaction, the 2nd join processes 210 chunks; with compaction, this number drops to 110. This reduction of ${A}_{1} = {100}$ chunks leads to a pipeline-level gain of ${C}_{3} \cdot  {A}_{1} = {240}$ ,resulting in a positive total gain.

由于存在流水线级收益，操作符在流水线中的位置会影响其压缩策略：前置操作符应采取激进策略，而后置操作符应采用保守策略。具体而言，即使当前操作符的直接收益为负，压缩${d}_{i}$仍可能有利，因为这减少了后续操作符需要处理的数据块数量。例如某包含两个连接操作符$\left( {k = 2}\right)$的流水线，其中$S = \{ {100},{400}\} ,r = {110}$且$v = 1$。尽管Gains(100,400)$\left( {{C}_{3} - g\left( {400}\right)  =  - {13.85}}\right)$的直接收益为负，但压缩使后续操作符的解析开销降低：未压缩时第二个连接需处理210个数据块，压缩后降至110个。这${A}_{1} = {100}$个数据块的减少带来了${C}_{3} \cdot  {A}_{1} = {240}$的流水线级收益，最终实现正向总收益。

### 3.4 Simulation-Based Analysis

### 3.4 基于模拟的分析

In this section, we introduce three practical compaction strategies. By simulating different scenarios, we assess the performance of each strategy, providing insights into their strengths and weaknesses. We consider the following strategies:

本节介绍三种实用压缩策略。通过模拟不同场景，我们评估各策略性能，揭示其优劣特性。具体策略如下：

- No Compaction & Full Compaction. No Compaction method is the simplest approach where no chunk is compacted, while Full Compaction, on the contrary, compacts all chunks containing less than $D$ tuples. Apache Data Fusion takes Full Compaction,which they call batch coalescence [23].

- 无压缩 & 全压缩。无压缩是最简单策略，不压缩任何数据块；全压缩则相反，会压缩所有包含少于$D$元组的数据块。Apache Data Fusion采用全压缩策略，他们称之为批次合并[23]。

- Binary Compaction. DuckDB employs a compaction strategy where all chunks smaller than a predefined threshold $\widehat{\alpha }$ are compacted. Given a chunk set $S$ ,DuckDB initializes an empty buffer chunk $B$ ,with size ${d}_{B} = 0$ . As we iterate over each chunk ${d}_{i} \in  S$ ,if ${d}_{i} \leq  \widehat{\alpha }$ ,we add ${d}_{i}$ to $B$ . When chunk $B$ is near-full (i.e., ${d}_{B} \geq  D - \widehat{\alpha }$ ),it is sent to the next operator,and $B$ is then reset.

- 二分压缩。DuckDB采用的策略会压缩所有小于预设阈值$\widehat{\alpha }$的数据块。给定数据块集合$S$时，DuckDB初始化空缓冲块$B$（大小为${d}_{B} = 0$）。遍历每个数据块${d}_{i} \in  S$时，若${d}_{i} \leq  \widehat{\alpha }$则将${d}_{i}$加入$B$。当缓冲块接近满载（即${d}_{B} \geq  D - \widehat{\alpha }$）时，将其发送至下一操作符并重置$B$。

- Dynamic Compaction. We assign each operator its own compaction threshold, computed based on Section 3.3. We assume any chunk with more than $D/2$ tuples does not require compaction. Initially,we calculate the optimal threshold $\alpha  \in  \lbrack 0,D/2)$ such that $\operatorname{Gains}\left( {D/2,\alpha }\right)  \geq  0$ and Gains $\left( {D/2,\alpha  + 1}\right)  < 0$ . Chunks containing $\leq  \alpha$ valid tuples are then pushed into the buffer chunk, following the same procedure as the Binary Compaction.

- 动态压缩（Dynamic Compaction）。我们为每个算子分配独立的压缩阈值，该阈值基于第3.3节计算得出。假设任何包含超过$D/2$个元组的块无需压缩。初始时，我们计算最优阈值$\alpha  \in  \lbrack 0,D/2)$使得$\operatorname{Gains}\left( {D/2,\alpha }\right)  \geq  0$且增益$\left( {D/2,\alpha  + 1}\right)  < 0$。随后将包含$\leq  \alpha$个有效元组的块按二进制压缩相同流程推入缓冲块。

We simulate the chunk compaction problem described in Section 3.2 by constructing a pipeline consisting of CROs,each with a fixed CRF. We set the chunk capacity to $D = {2048}$ and the predefined threshold for Binary Compaction to $\widehat{\alpha } = {128}$ . The intermediate relation produced by each operator contains the same number of tuples as the input table $\left( {v = 1}\right)$ ,which contains 20 million tuples. We vary the CRF $\left( {r = 2,{16},{256}}\right)$ and the operator number $\left( {k = 3,4,5}\right)$ to analyze the trade-offs between these strategies. We set parameters ${C}_{1 - 4}$ based on the proportional relationship obtained from Figure 4. We also consider the case that a table has large tuples, leading to the increased compaction cost $\left( {{C}_{2} = {10}\text{instead of}{C}_{2} = 1}\right)$ .

我们通过构建由CRO（固定压缩率因子）组成的流水线来模拟第3.2节描述的块压缩问题。设置块容量为$D = {2048}$，二进制压缩预定义阈值为$\widehat{\alpha } = {128}$。每个算子产生的中间关系所含元组数与输入表$\left( {v = 1}\right)$相同（该表含2000万元组）。通过调整CRF$\left( {r = 2,{16},{256}}\right)$和算子数量$\left( {k = 3,4,5}\right)$来分析策略间的权衡。参数${C}_{1 - 4}$根据图4所得比例关系设定。同时考虑大元组表导致压缩成本$\left( {{C}_{2} = {10}\text{instead of}{C}_{2} = 1}\right)$增加的情况。

In Figure 5, the first row shows that a higher CRF increases the benefit of compaction. The second row highlights the significant impact of pipeline depth on the compaction problem. Comparing Case 2 to Case 4, it is evident that for large tuples, chunk compaction hurts the overall performance because of the high costs of data copying. Figure 5 yields three conclusions. First, compaction strategies can affect query execution time significantly. Second, Binary Compaction struggles to handle diverse workloads adaptively because it relies on a predefined threshold for all CROs. Third, Dynamic Compaction, which assigns specific thresholds for each operator, demonstrates near-optimal performance. For example,in Case 1,thresholds $\overrightarrow{\alpha } = \{ {293},{53},0\}$ are assigned to the pipeline’s three operators,while in Case $3,\overrightarrow{\alpha } = \{ {1024},{53},0\}$ are used. It agrees with our analysis in Section 3.3: the earlier the operator is in the pipeline, the more aggressive its strategy is.

图5中，首行显示较高CRF能提升压缩效益。次行强调流水线深度对压缩问题的显著影响。对比案例2与4可知，大元组情况下数据拷贝的高成本导致块压缩损害整体性能。图5得出三结论：其一，压缩策略显著影响查询执行时间；其二，二进制压缩因依赖全局固定阈值而难以自适应多样化负载；其三，动态压缩为各算子分配特定阈值，展现近最优性能。例如案例1中阈值$\overrightarrow{\alpha } = \{ {293},{53},0\}$分配给流水线三个算子，而案例$3,\overrightarrow{\alpha } = \{ {1024},{53},0\}$采用不同阈值。这符合第3.3节分析：算子位置越靠前，其策略应越激进。

Dynamic Compaction is hardly applicable to real databases as it relies on the function Gains to compute thresholds $\alpha$ ,which are affected by both database design $\left( {C}_{1 - 4}\right)$ and workload characteristics(k,r). In Section 4,we propose a learning-based approach to approximate Dynamic Compaction,estimating $\alpha$ without depending on these parameters. Furthermore,to minimize the substantial costs of data copying shown in Figure 5, we focus on how to compact chunks efficiently in Section 5 and introduce logical compaction, which can compact small chunks from hash joins without requiring data copying.

动态压缩难以应用于真实数据库，因其依赖增益函数Gains计算阈值$\alpha$，该函数受数据库设计$\left( {C}_{1 - 4}\right)$和工作负载特征(k,r)双重影响。第4节提出基于学习的方法逼近动态压缩，无需依赖这些参数即可估算$\alpha$。此外，为最小化图5所示数据拷贝的高成本，第5节重点研究高效块压缩方法，引入无需数据拷贝的逻辑压缩技术来处理哈希连接产生的小块。

## 4 Learning Compaction

## 4 学习式压缩

This section introduces a learning module designed to address the compaction problem. Although Dynamic Compaction, as previously discussed, is not feasible for real databases, this learning module serves as an approximation.

本节介绍解决压缩问题的学习模块。尽管前文讨论的动态压缩不适用于实际数据库，该学习模块可作为其近似方案。

Learning-based Solution. The learning module directly estimates $\alpha$ from the feedback of execution (i.e., the latency). In morsel-driven parallelism, data is divided into chunks, with each thread responsible for fetching and processing a chunk through the entire pipeline before moving on to the next. Consequently, each chunk can serve as a sample for a learning algorithm [38]. Our objective is to determine the optimal threshold $\alpha  \in  \lbrack 0,{1024})$ for each CRO. In this module,we cast the optimization of selecting the best $\alpha$ at runtime as a multi-armed bandit (MAB) problem [44].

基于学习的解决方案。学习模块直接从执行反馈（即延迟）估算$\alpha$。在分片驱动并行中，数据被划分为块，每个线程负责获取并通过整个流水线处理一个块后才会处理下一个。因此每个块都可作为学习算法样本[38]。我们的目标是为每个CRO确定最优阈值$\alpha  \in  \lbrack 0,{1024})$。本模块将运行时最优$\alpha$选择问题建模为多臂老虎机（MAB）问题[44]。

### 4.1 Multi-Armed Bandits

### 4.1 多臂老虎机

The MAB problem involves a decision-maker with $r$ options,or "arms," each with uncertain reward probabilities with an expectation ${\mu }_{i}$ . At each time step,the decision-maker selects an arm and receives a reward sampled from the associated probability distribution. The objective is to maximize cumulative reward over time. This requires the decision-maker to balance the trade-off between exploring arms to learn their rewards and exploiting arms to get high rewards.

多臂赌博机(MAB)问题涉及一个决策者面对$r$个可选操作（或称"臂"），每个臂具有不确定的奖励概率，其期望值为${\mu }_{i}$。决策者每步选择一个臂，并从对应概率分布中获得奖励样本。目标是通过时间累积最大化奖励，这要求决策者在探索各臂奖励信息与利用高奖励臂之间取得平衡。

In the pipeline context, each operator faces its own MAB problem. It selects a compaction threshold $\alpha  \in  \lbrack 0,{2048})$ to compact its output small chunks. These chunks are then sent to subsequent operators. The reward for each arm is related to the execution latency of the pipeline and the compaction cost. Operators try different thresholds to select the optimal one, with their decisions collectively contributing to a global compaction policy.

在流水线场景中，每个算子都面临自身的MAB问题。它选择压缩阈值$\alpha  \in  \lbrack 0,{2048})$来压缩输出的小数据块，这些块随后被发送给后续算子。每个臂的奖励与流水线执行延迟及压缩成本相关。算子通过尝试不同阈值来选择最优解，其决策共同构成全局压缩策略。

A probing table may hold up to 20 million tuples, allowing the operator to explore arms across as many as 20,000 iterations to gather corresponding rewards. This ample sample size is adequate for fine-tuning a single parameter, $\alpha$ . Additionally,if operators fail to identify the optimal threshold, they can revert to Binary Compaction, using a pre-defined threshold. The performance of Binary Compaction is the lower bound of the learning approach. Lastly, the module incurs low overhead, as it merely requires statistics on the execution times of operators within a pipeline - data that modern databases already collect when profiling is enabled [37].

探测表可容纳多达2000万条元组，允许算子通过多达2万次迭代探索各臂以收集对应奖励。如此充足的样本量足以微调单个参数$\alpha$。此外，若算子未能找到最优阈值，可回退至采用预设阈值的二分压缩(Binary Compaction)。二分压缩的性能是学习方法的底线保障。最后，该模块开销极低，仅需统计流水线内算子的执行时间——这些数据在现代数据库启用性能分析时已自动收集[37]。

### 4.2 Online Compaction Learning

### 4.2 在线压缩学习

The Compaction Learner, depicted in Figure 6, optimizes the compaction trade-off during query execution. We place one compactor after each filter and hash join operator, each designated by a threshold ${\alpha }_{i}$ ,where $1 \leq  i \leq  3$ . Before fetching a chunk from table $R$ ,the executor sets the thresholds for the compactors by invoking ${\alpha }_{i} = \operatorname{SelectArm}\left( i\right)$ . In response,the compaction learner provides a threshold. After processing the chunk, we measure the pipeline's latency to update the compaction learner. Specifically,the $i$ -th compactor calls UpdateArm $\left( {i,{\alpha }_{i},{t}_{i} + \cdots  + {t}_{3}}\right)$ ,where ${\alpha }_{i}$ is the arm used in the last execution,and ${t}_{i} + \cdots  + {t}_{3}$ represents the processing latency of all subsequent operators in the pipeline. This process is repeated for each chunk until all are processed.

如图6所示的压缩学习器(Compaction Learner)在查询执行期间优化压缩权衡。我们在每个过滤器和哈希连接算子后放置动态压缩器，各由阈值${\alpha }_{i}$标识（其中$1 \leq  i \leq  3$）。执行器从表$R$获取数据块前，通过调用${\alpha }_{i} = \operatorname{SelectArm}\left( i\right)$设置压缩器阈值。压缩学习器响应阈值建议，处理完数据块后测量流水线延迟以更新学习器。具体而言，第$i$个压缩器调用UpdateArm$\left( {i,{\alpha }_{i},{t}_{i} + \cdots  + {t}_{3}}\right)$，其中${\alpha }_{i}$是上次执行使用的臂，${t}_{i} + \cdots  + {t}_{3}$代表流水线中所有后续算子的处理延迟。该过程对每个数据块循环执行直至处理完毕。

The compaction learner maintains a statistical model for each compactor. This statistical model consists of three vectors: the candidate thresholds $\left\{  {{x}_{1},\cdots ,{x}_{a}}\right\}$ (Arm),the estimated rewards $\left\{  {{\widehat{\mu }}_{1},\cdots ,{\widehat{\mu }}_{a}}\right\}$ (Reward),and the frequency with which each threshold is selected $\left\{  {{n}_{1},\cdots ,{n}_{a}}\right\}$ (Confidence). For instance, as illustrated in Figure 6, the model of the first compactor includes candidates: $\{ 0,{128},{1024}\}$ . Through processing chunks many times,it estimates that arm 1024 yields the highest reward. Also, the confidences of arms 0 and 128 are both 3, indicating that they have been selected only three times. This limited selection frequency may suggest a higher estimation error.

压缩学习器为每个压缩器维护统计模型，包含三个向量：候选阈值$\left\{  {{x}_{1},\cdots ,{x}_{a}}\right\}$(Arm)、预估奖励$\left\{  {{\widehat{\mu }}_{1},\cdots ,{\widehat{\mu }}_{a}}\right\}$(Reward)、各阈值被选频率$\left\{  {{n}_{1},\cdots ,{n}_{a}}\right\}$(Confidence)。例如图6所示，首个压缩器模型包含候选值：$\{ 0,{128},{1024}\}$。经过多次数据块处理，预估臂1024能产生最高奖励。同时臂0和128的置信度均为3，表明它们仅被选中三次，有限的选取频次可能意味着较高的预估误差。

SelectArm: Exploration-exploitation Dilemma. Each model within the compaction learner confronts a dual challenge. On one hand, it seeks to select the arm with the highest reward, emphasizing exploitation. On the other hand, it must explore other arms to potentially discover more optimal solutions or to enhance the confidence in its reward estimations, particularly when the initial confidence is zero. Therefore, a well-designed policy is crucial to manage this dilemma.

选臂机制：探索-利用困境。压缩学习器中的每个模型都面临双重挑战：既要选择奖励最高的臂（强调利用），又需探索其他臂以发现更优解或提升奖励预估置信度（尤其初始置信度为零时）。因此需要精心设计的策略来应对这一困境。

<!-- Media -->

<!-- figureText: Pipeline Executor $\;{t}_{1}$ (Processing Latency) ${t}_{2}$ ${t}_{3}$ ${a}_{3}$ // before execution // after execution Model 3 Confidence Arm Reward Confidence 3 0 5.5 879 123 128 4.8 78 13 1024 4.1 9 For each chunk from $R$ : For each threshold ${\alpha }_{\mathrm{i}} : {\alpha }_{\mathrm{i}} =$ SelectArm(i); For each threshold ${\alpha }_{\mathrm{i}} :$ Update $\operatorname{Arm}\left( {i,{\alpha }_{\mathrm{i}},{t}_{\mathrm{i}} + \ldots  + {t}_{3}}\right)$ Compaction Learner has interfaces: SelectArm and UpdateArm. Model 1 Model 2 Arm Reward Confidence Arm Reward 0 1.2 3 0 1.5 128 1.3 3 128 3.4 1024 2.4 97 1024 2.1 SelectArm(i): returns the arm with highest reward from the Model $i$ . UpdateArm $\left( {i,{\alpha }_{\mathrm{i}}\text{,latency}}\right)$ : updates the arm ${\alpha }_{\mathrm{i}}$ for the Model $i$ . -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_9.jpg?x=302&y=269&w=955&h=588&r=0"/>

Fig. 6. Learning Module Overview - Each $\alpha$ is a dynamic compactor,fetching the threshold from the Compaction Learner, and then updating the learner with the execution latency.

图6. 学习模块概览 - 每个$\alpha$都是动态压缩器，从压缩学习器获取阈值，随后用执行延迟更新学习器。

<!-- Media -->

The Upper Confidence Bound (UCB) algorithm is a classic approach to the multi-armed bandit problem. It selects arm ${x}_{j}$ based on the highest value of ${\widehat{\mu }}_{j} + \sqrt{\left( {2\ln n}\right) /{n}_{j}}$ ,where $n = \mathop{\sum }\limits_{{j = 1}}^{a}{n}_{j}$ and $1 \leq  j \leq  a$ . As the number of processing times $n$ increases,the second term $\sqrt{\left( {2\ln n}\right) /{n}_{j}}$ provides less-explored arms with a boost, even if their estimated reward is low. This property proves beneficial for balancing exploration and exploitation. A fine-tuned version of UCB takes the measured variance of rewards into account. It selects arm $j$ based on the highest value of

置信区间上界(UCB)算法是解决多臂老虎机问题的经典方法。它根据${\widehat{\mu }}_{j} + \sqrt{\left( {2\ln n}\right) /{n}_{j}}$的最大值选择臂${x}_{j}$，其中$n = \mathop{\sum }\limits_{{j = 1}}^{a}{n}_{j}$和$1 \leq  j \leq  a$。随着处理次数$n$增加，第二项$\sqrt{\left( {2\ln n}\right) /{n}_{j}}$会为探索不足的臂提供增益，即使其预估奖励值较低。这一特性能有效平衡探索与利用。改进版UCB算法会考虑奖励值的测量方差，根据

$$
\sqrt{\left( {\ln n/{n}_{j}}\right)  \cdot  \min \left( {{0.25},{V}_{j}\left( {n}_{j}\right) }\right) }\text{,}
$$

where

其中

$$
{V}_{j}\left( d\right)  \triangleq  \left( {\frac{1}{d}\mathop{\sum }\limits_{{\tau  = 1}}^{d}{X}_{j,\tau }^{2}}\right)  - {\bar{X}}_{j,d}^{2} + \sqrt{\left( {2\ln n}\right) /d},
$$

and ${X}_{j,\tau }$ represents the reward of the $\tau$ -th attempt of trying arm $j$ . This version places more trials on arms with unstable rewards.

且${X}_{j,\tau }$表示第$\tau$次尝试臂$j$的奖励值。该版本会在奖励不稳定的臂上分配更多试验次数。

The compactor threshold ${\alpha }_{i}$ lies in [0,1024]. We discretize this interval and set the candidates as $\{ 0,{32},{64},{128},{256},{384},{512},{768},{1024}\}$ . This simplification reduces the parameter space without affecting overall performance because similar threshold values yield comparable compaction performance. We employ the fine-tuned version of UCB for the SelectArm function because of the high noise level in the pipeline execution. Additionally, at the beginning of query execution, the compaction learner attempts each arm several (e.g., 8) times to initialize their statistics. Despite resulting in a fixed overhead, this mechanism enhances the stability of the learner.

压缩器阈值${\alpha }_{i}$的取值范围为[0,1024]。我们将该区间离散化，候选值设为$\{ 0,{32},{64},{128},{256},{384},{512},{768},{1024}\}$。这种简化在不影响整体性能的前提下缩减了参数空间，因为相近阈值会产生相似的压缩性能。由于管道执行中存在较高噪声，我们为SelectArm函数采用改进版UCB算法。此外在查询开始时，压缩学习器会对每个臂进行多次(如8次)尝试以初始化统计数据。虽然会产生固定开销，但该机制能提升学习器的稳定性。

UpdateArm: Taking the Moving Average. The compaction learner receives the execution latency of each chunk, measured in milliseconds, as feedback. In addressing the Multi-Armed Bandit (MAB) problem within compaction, we define the reward as the reciprocal of the latency. Initially, the rewards for all arms/thresholds are set to zero. To update the reward for each arm, we employ a moving average approach. Specifically, this involves calculating the average of the most recent 16 reward values for each arm.

UpdateArm：采用移动平均法。压缩学习器以毫秒为单位接收每个数据块的执行延迟作为反馈。在压缩场景的多臂老虎机问题中，我们将奖励值定义为延迟的倒数。所有臂/阈值的初始奖励设为零。更新臂奖励时采用移动平均法，具体是计算每个臂最近16次奖励值的平均值。

One concern is that processing latency may vary greatly, while the UCB algorithm assumes that the reward should lie in a limited range. However, this is not problematic for the compaction learner focusing on the pipeline level. Typically, pipelines in most analytical queries involve less than four joins. As a result, the latencies - and consequently the rewards - received by the learner usually range from ${50\mu }\mathrm{s}$ to ${500\mu }\mathrm{s}$ . This translates to reward values between 2 and 20,a range well-suited for effective handling by the UCB algorithm.

需要注意的是处理延迟可能波动较大，而UCB算法假设奖励值应处于有限范围。但这对于关注管道层级的压缩学习器不成问题。典型分析查询中的管道通常包含不超过4个连接操作，因此学习器获得的延迟值(及对应奖励值)通常介于${50\mu }\mathrm{s}$到${500\mu }\mathrm{s}$之间，即奖励值在2到20范围内，这个区间非常适合UCB算法有效处理。

<!-- Media -->

<!-- figureText: Col ${\mathrm{{Col}}}_{2}$ ${\mathrm{{Col}}}_{3}$ 0 Col ${\mathrm{{Col}}}_{2}$ ${\mathrm{{Col}}}_{3}$ @ 2 C # b & ${\mathrm{{SV}}}_{2}$ Three Marked Tuples 2 @ 1 & d ${\mathrm{{SV}}}_{1}$ % A Data Chunk with Two SVs -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_10.jpg?x=361&y=263&w=844&h=290&r=0"/>

Fig. 7. A Data Chunk Example with Multiple SVs - SV ${}_{1}$ marks the first two vectors,and ${\mathrm{{SV}}}_{2}$ marks the third.

图7. 含多个SV的数据块示例 - SV${}_{1}$标记前两个向量，${\mathrm{{SV}}}_{2}$标记第三个向量。

<!-- Media -->

Additionally, to manage complex queries with potentially unstable latencies, we have implemented a robust monitoring mechanism capable of handling skewed workloads. The compaction learner periodically captures a snapshot of the estimated rewards for every $\eta$ chunk $(\eta  = {1024}$ by default). The snapshot is denoted by $\overrightarrow{\mu } = \left\{  {{\widehat{\mu }}_{1},\cdots ,{\widehat{\mu }}_{a}}\right\}$ . After updating an arm ${x}_{i}$ and obtaining a new snapshot ${\overrightarrow{\mu }}^{\prime }$ ,we check for anomalies by comparing the ratios ${\widehat{\mu }}_{i}^{\prime }/{\widehat{\mu }}_{i}$ and ${\widehat{\mu }}_{i}/{\widehat{\mu }}_{i}^{\prime }$ . If either ratio equals or exceeds 2, we identify an anomaly and reset the UCB algorithm. This reset involves clearing existing rewards and confidence levels, and each arm is tested multiple times (e.g., 8) to reestablish baseline statistics.

此外为处理可能具有不稳定延迟的复杂查询，我们实现了能应对倾斜工作负载的鲁棒监控机制。压缩学习器默认每处理$\eta$个数据块(默认为$(\eta  = {1024}$)就会捕获一次预估奖励的快照，记为$\overrightarrow{\mu } = \left\{  {{\widehat{\mu }}_{1},\cdots ,{\widehat{\mu }}_{a}}\right\}$。在更新臂${x}_{i}$并获得新快照${\overrightarrow{\mu }}^{\prime }$后，通过比较比值${\widehat{\mu }}_{i}^{\prime }/{\widehat{\mu }}_{i}$和${\widehat{\mu }}_{i}/{\widehat{\mu }}_{i}^{\prime }$来检测异常。任一比值达到或超过2时，将重置UCB算法：清除现有奖励和置信度，并对每个臂进行多次(如8次)测试以重建基准统计数据。

### 4.3 Multi-threading in Learning

### 4.3 学习过程中的多线程处理

The compaction module is designed to be compatible with morsel-driven parallelism [25]. First, a thread must acquire a lock each time it accesses the statistics from a compactor model. Second, we increment the confidence level by one, rather than in the UpdateArm function. This differentiation is crucial to ensure that when multiple threads (e.g., 96) simultaneously invoke the SelectArm function, they do not all select the same arm.

压实模块设计兼容分片驱动并行机制[25]。首先，线程每次访问压实器模型的统计量时需获取锁；其次，我们选择在SelectArm函数中递增置信度而非UpdateArm函数。这一差异至关重要，它能确保当多线程（如96个）同时调用SelectArm函数时，不会全部选择相同的分片。

In the multi-threading environment, an interesting phenomenon occurs. Even when the SelectArm function chooses an arm,say $j$ ,based on the highest confidence ${n}_{j}$ - rather than rewards - the compaction learner is still capable of providing the near-optimal arm. This happens because threads that select the optimal arm can process chunks more rapidly, leading to more frequent updates and, consequently, increased confidence in that arm. This dynamic, where each thread's update frequency effectively feeds back into the learning algorithm, is unique to morsel-driven parallelism.

多线程环境下会出现一个有趣现象：即使SelectArm函数基于最高置信度${n}_{j}$（而非奖励值）选择分片$j$，压实学习器仍能提供近似最优分片。这是因为选择最优分片的线程能更快处理数据块，从而更频繁地更新该分片置信度。这种线程更新频率反哺学习算法的动态特性，是分片驱动并行机制独有的。

## 5 Logical Compaction

## 5 逻辑压实

In this section, we introduce an operator called compacted vectorized compacted hash join. Figure 5 shows that the compaction cost constitutes a large portion of the overall estimated cost. To address this, we redesign the data chunk and the hash join operator. The core idea is that small chunks generated from the same probing chunk can be compacted logically without actual data copying.

本节介绍名为"向量化压实哈希连接"的算子。图5显示压实成本占总体预估成本的很大比重。为此我们重新设计数据块和哈希连接算子，核心思想是：相同探测块生成的小数据块可通过逻辑方式压实，无需实际数据拷贝。

### 5.1 Data Chunk Design

### 5.1 数据块设计

The current design of a data chunk comprises several data vectors and a selection vector (or bitmap), where a selection vector (SV) is a dense, sorted list of row identifiers (RIDs) indicating which tuples in the batch are valid. The DBMS marks tuples as invalid by modifying the SV alone, without copying data [29]. Figure 8 provides an example of this chunk design.

当前数据块设计包含若干数据向量和选择向量（或位图），其中选择向量(SV)是表示批次中有效元组的行标识符(RID)稠密有序列表。DBMS仅通过修改SV即可标记无效元组，无需复制数据[29]。图8展示了该块设计示例。

<!-- Media -->

<!-- figureText: Real Vector: ① Old Result Chunk ② Old Result Chunk ③ Old Result Chunk RHS Columns RHS Columns SV Columns SV Columns SV With compacted hash join, ② and ③ share the same Result Chunk RHS Columns RHS Columns RHS Columns ② & ③ Directly write the data of Chunk ③ into the Chunk ② vectors A vector that directly allocates RHS Columns and manages its own memory Referencing Vector: A vector that holds a reference to data stored in another vector Valid Tuples: Columns Tuples marked by the SV ① New Result Chunk 42 dot Probe 34 N ✘ Next ✓ Columns SV 22 ① LHS Columns Input Chunk Hash Table Buckets -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_11.jpg?x=140&y=262&w=1291&h=580&r=0"/>

Fig. 8. Compacted Vectorized Hash Join - Within each chunk, we use multiple extended SVs to manage columns from various sources. This approach enables the hash join to compact chunks without any additional cost, thus producing fewer yet larger chunks.

图8. 向量化压实哈希连接——每个块内使用多个扩展SV管理不同来源的列。该方法使哈希连接能零成本压实数据块，从而生成更少但更大的块。

<!-- Media -->

A hash join operator must modify the SV and gather the payload, to generate the result chunks. One idea is to compact chunks during the chunk generation process. Since all result chunks reference the same LHS data, we can concatenate their SVs directly. For example, we concatenate the SVs of the 2nd and 3rd old result chunks in Figure 8,setting $\mathrm{{SV}} = \left( {1,3,0,1,2,7}\right)$ . However,this concatenation leads to RID conflicts in the SV: the term RID = 1 appears twice. The current chunk design requires that the $1\mathrm{{st}}$ and 4th tuples are both placed in the row indexed by $\mathrm{{RID}} = 1$ . For the RHS vectors, however, placing two different values in the same place is impossible.

哈希连接算子必须修改SV并收集有效载荷以生成结果块。创新思路是在块生成过程中进行压实：由于所有结果块引用相同的左表数据，可直接串联它们的SV。例如图8中串联第2、3个旧结果块的SV时设置$\mathrm{{SV}} = \left( {1,3,0,1,2,7}\right)$，但这会导致SV中出现RID冲突（RID=1重复出现）。当前块设计要求$1\mathrm{{st}}$和第4个元组必须置于$\mathrm{{RID}} = 1$索引行，而右表向量无法在同一位置存放两个不同值。

We propose a new data chunk design consisting of several data vectors and multiple extended SVs. Unlike traditional SVs, the extended SV differs in two ways: (1) it is a dense, unsorted list, and (2) it can have repeated IDs. Each SV manages a group of vectors in this data chunk. Figure 7 gives an example of a data chunk with two SVs. It uses ${\mathrm{{SV}}}_{1}$ to mark the first two vectors and uses ${\mathrm{{SV}}}_{2}$ to mark the third vector. Then,for the $i$ -th valid tuple,its values in the first two columns are placed in the row indexed by ${\mathrm{{SV}}}_{1}\left\lbrack  i\right\rbrack$ ,and its value in the third column is placed in the row indexed by ${\mathrm{{SV}}}_{2}\left\lbrack  i\right\rbrack$ . Thus,the chunk represents three valid tuples.

我们提出包含多个数据向量和扩展SV的新块设计。与传统SV不同，扩展SV具备两点差异：(1)是稠密无序列表；(2)允许重复ID。每个SV管理块内一组向量。图7展示含两个SV的数据块示例：用${\mathrm{{SV}}}_{1}$标记前两个向量，${\mathrm{{SV}}}_{2}$标记第三个向量。对于$i$号有效元组，前两列值置于${\mathrm{{SV}}}_{1}\left\lbrack  i\right\rbrack$索引行，第三列值置于${\mathrm{{SV}}}_{2}\left\lbrack  i\right\rbrack$索引行，最终该块表示三个有效元组。

### 5.2 Compacted Vectorized Hash Join

### 5.2 向量化压实哈希连接

Figure 8 illustrates the compacted hash join using extended SVs. Upon receiving a full data chunk from the LHS table, we probe its join keys, resulting in 8 hash table buckets. Subsequently, we invoke Next (   ) to retrieve the output chunks.

图8演示使用扩展SV的压实哈希连接。收到左表完整数据块后，探测其连接键得到8个哈希表桶，随后调用Next()获取输出块。

The first output chunk contains 7 valid tuples. It uses two extended SVs: one for the LHS vectors/columns and the other for the RHS vectors. For the LHS vectors, we employ the zero-copy technique, referencing the data in the probing chunk. For the RHS vectors, payloads must be copied sequentially from the hash table, and these do not necessarily align with those in the LHS vectors of the same row. Since a chunk can hold up to 8 tuples, the 2nd result chunk, containing 2 tuples, cannot be compacted with the 1st chunk. Therefore, we directly output the first chunk as-is.

首个输出块含7个有效元组，使用两个扩展SV：分别管理左表向量/列和右表向量。左表向量采用零拷贝技术引用探测块数据，右表向量则需从哈希表顺序拷贝载荷，且不必与同行左表向量对齐。由于每块最多容纳8个元组，含2个元组的第2结果块无法与第1块压实，因此直接原样输出第一块。

The 2nd and 3rd chunks have 2 and 4 tuples, respectively. We can compact them into a larger chunk. The second chunk follows the same format as the first, as shown by the transparent part in Figure 8. It has 2 valid tuples, with separate SVs for the LHS and RHS vectors. As for the 3rd chunk, instead of allocating new memory for it, we directly write its data into the 2nd data chunk. This decision is motivated by the fact that they hold the reference to the same vector. We can directly concatenate their SVs for the LHS columns without data copying. In Figure 8, the tuples indexed by (0,1,2,3,7)are selected at least once in the SV of the 1st or 2nd chunks.

第二和第三数据块分别包含2个和4个元组。我们可以将其压缩为更大的数据块。第二个数据块遵循与第一个相同的格式（如图8透明部分所示），包含2个有效元组，其左值(LHS)和右值(RHS)向量具有独立的选择向量(SV)。对于第三数据块，我们并未为其分配新内存，而是直接将数据写入第二数据块——这是因为它们持有相同向量的引用。我们可以无需数据拷贝，直接拼接其左值列的SV。在图8中，索引为(0,1,2,3,7)的元组在第一或第二数据块的SV中至少被选中一次。

<!-- Media -->

<!-- figureText: Data Chunk Data Chunk with Dictionary Encoding 0 2 3 4 5 Flat Vectors Dictionary Vector 0 Columns SV -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_12.jpg?x=330&y=262&w=902&h=316&r=0"/>

Fig. 9. Compressed Vector in the Chunk - The chunk design can seamlessly integrate with column compression.

图9. 块内压缩向量 - 块设计可与列压缩无缝集成。

<!-- Media -->

For the RHS columns, we gather the 3rd chunk's payload from the hash table and append it to the RHS vectors of the 2nd chunk, starting at index 2. We add a fully dense SV for the RHS columns in the result chunk; the shared result chunk has an $\mathrm{{SV}} = \left( {0,1,2,3,4,5}\right)$ for the RHS columns because there are six valid records in total in this chunk: two entries from the 2nd chunk and four entries from the 3rd chunk. Consequently, these two chunks share the same physical memory, as shown in the rightmost, solid part of Figure 8. If more result chunks are available, we continue writing their data into the memory of the current chunk, compacting as much as possible until it can no longer hold the next chunk. Once capacity is reached, we output the current chunk and allocate a new one. Finally, the proposed compacted hash join yields two result chunks with sizes 7 and 6, resulting in a lower CRF than the original hash join. This approach utilizes zero-copy techniques for the LHS vectors and a single gathering operation for the RHS vectors, enhancing efficiency.

对于右侧列，我们从哈希表收集第3个块的有效载荷，并将其追加到第2个块的右侧向量中，起始索引为2。在结果块中为右侧列添加完全稠密的SV；共享结果块中右侧列包含$\mathrm{{SV}} = \left( {0,1,2,3,4,5}\right)$，因为该块共有六条有效记录：两条来自第2个块，四条来自第3个块。因此，这两个块共享相同的物理内存，如图8最右侧实线部分所示。若有更多结果块可用，我们会继续将其数据写入当前块内存，尽可能紧凑存储直至无法容纳下一个块。达到容量后即输出当前块并分配新块。最终，所提出的紧凑哈希连接生成大小分别为7和6的两个结果块，其CRF低于原始哈希连接。该方法对左侧向量采用零拷贝技术，对右侧向量仅需单次收集操作，从而提升效率。

Additionally, the result chunks, with multiple SVs, can be processed by the remaining operators in the pipeline. For example,consider a filter operator applied to a chunk with two SVs, ${\mathrm{{sel}}}_{1} = \left( {1,3,1,2}\right)$ and ${\operatorname{sel}}_{2} = \left( {0,1,2,2}\right)$ ,encompassing four tuples. Suppose the tuples at indices 0,2,and 3 satisfy the filter criteria and are retained. This yields a result vector res $= \left( {0,2,3}\right)$ . To derive the selection vectors (SVs) for the result chunk,we update SVs using ${\operatorname{sel}}_{1}^{\prime }\left\lbrack  i\right\rbrack   = {\operatorname{sel}}_{1}\left\lbrack  {\operatorname{res}\left\lbrack  i\right\rbrack  }\right\rbrack$ for $i = 0,1,2$ ,producing ${\mathrm{{sel}}}_{1}^{\prime } = \left( {1,1,2}\right)$ and ${\mathrm{{sel}}}_{2}^{\prime } = \left( {0,2,2}\right)$ .

此外，含多个SV的结果块可由流水线中剩余算子处理。例如，对包含两个SV（${\mathrm{{sel}}}_{1} = \left( {1,3,1,2}\right)$和${\operatorname{sel}}_{2} = \left( {0,1,2,2}\right)$）的块应用过滤算子，该块涵盖四个元组。假设索引0、2和3的元组满足过滤条件被保留，则生成结果向量res$= \left( {0,2,3}\right)$。为获取结果块的选择向量(SV)，我们使用${\operatorname{sel}}_{1}^{\prime }\left\lbrack  i\right\rbrack   = {\operatorname{sel}}_{1}\left\lbrack  {\operatorname{res}\left\lbrack  i\right\rbrack  }\right\rbrack$更新$i = 0,1,2$的SV，生成${\mathrm{{sel}}}_{1}^{\prime } = \left( {1,1,2}\right)$和${\mathrm{{sel}}}_{2}^{\prime } = \left( {0,2,2}\right)$。

### 5.3 Overhead of Extended SVs

### 5.3 扩展SV的开销

The compacted hash join benefits from the representation flexibility provided by the extended SVs. These extended SVs bring minimal overhead for two reasons. First, the data chunk adds only one additional extended SV when it undergoes a hash join probing. At the sink operator [25] of a pipeline, the data chunk is materialized, resetting the number of SVs to one. Since a pipeline typically has few joins, the number of SVs is not likely to become a performance bottleneck. Second, found, the data chunk with extended SVs maintains the same interface as the original design, avoiding the need to redesign other database components. The only change lies in determining which SV to use when accessing the column values. An optimization is that we can omit the SV from the build side after the hash join (because it is always fully dense). However, we must bring back this omitted SV if there are subsequent hash joins in the pipeline because the columns associated with the omitted SV will be on the probe side for the next join.

紧凑哈希连接受益于扩展SV提供的表示灵活性。这些扩展SV带来的开销极小，原因有二：首先，数据块在进行哈希连接探测时仅添加一个额外扩展SV；在流水线的sink算子[25]处，数据块被物化并将SV数量重置为1。由于流水线通常包含少量连接操作，SV数量不太可能成为性能瓶颈。其次，具有扩展SV的数据块保持与原设计相同的接口，无需重新设计其他数据库组件。唯一变化在于访问列值时需确定使用哪个SV。一个优化点是哈希连接后可以省略构建侧的SV（因其始终完全稠密），但若流水线中存在后续哈希连接，则必须恢复该SV，因为被省略SV关联的列将成为下一连接的探测侧。

### 5.4 Column Compression

### 5.4 列压缩

Compression is a crucial technique for reducing database size and enhancing query performance [35, 47]. The data chunk design, consisting of several data vectors and a variable number of extended SVs, seamlessly integrates with the in-memory compressed data format during execution [1, 24]. Column compression is widely used and effective for data storage as it reduces storage size and accelerates I/O when loading data into memory. When loading the compressed data into memory, modern databases generate an in-memory data format during execution. This allows for a more compressed representation and potentially enables compressed execution throughout the system.

压缩是减小数据库体积、提升查询性能的关键技术[35,47]。由若干数据向量和可变数量扩展SV构成的数据块设计，在执行期间可与内存压缩数据格式无缝集成[1,24]。列压缩通过减少存储空间及加速数据加载至内存时的I/O操作，被广泛用于高效数据存储。现代数据库将压缩数据加载至内存时，会在执行期间生成内存数据格式，从而实现更高压缩率，并可能支持全系统范围的压缩执行。

For example, consider a table where one of its columns is encoded using a dictionary. As we load this table into memory chunk by chunk, the vector representing the compressed column adopts dictionary encoding, referred to as a dictionary vector. In this scenario, the extended SV can function as the dictionary codes for this vector, with the dictionary serving as the vector data. Figure 9 illustrates the differences between two chunks: one without dictionary encoding (left) and the other with (right). The chunk on the left contains three uncompressed columns, while the one on the right encodes one column using a dictionary. As a result, the chunk on the right consists of two flat vectors and one dictionary vector. Note that the dictionary vector cannot logically represent elements exceeding the chunk size, aiming at enhancing cache efficiency.

例如，考虑一个表格，其中某一列采用字典编码。当我们逐块将该表加载到内存时，表示压缩列的向量采用字典编码，称为字典向量。在此场景中，扩展的选择向量(SV)可充当该向量的字典编码，而字典则作为向量数据。图9展示了两块数据之间的差异：左侧块未使用字典编码，右侧块则采用字典编码。左侧块包含三个未压缩列，右侧块则对其中一列使用字典编码。因此，右侧块由两个扁平向量和一个字典向量组成。需注意，字典向量在逻辑上无法表示超过块大小的元素，此举旨在提升缓存效率。

### 5.5 Logical and Learning Compaction

### 5.5 逻辑压缩与学习压缩

The proposed hash join method is specifically designed to compact small result chunks originating from the same probing chunk, as it requires the chunks to be compacted to share identical LHS vectors. The proposed hash join and the traditional hash join are both chunk-reducing operators, but the former has a lower CRF. The performance of the compacted hash join is significantly influenced by the chain lengths in the hash table buckets.

提出的哈希连接方法专为压缩源自同一探测块的小结果块而设计，因其要求被压缩块必须共享相同的左向量(LHS)。提出的哈希连接与传统哈希连接都是块缩减运算符，但前者具有更低的块缩减因子(CRF)。压缩哈希连接的性能显著受哈希表桶中链长度影响。

In a Join-style Case, where the operator outputs many reduced chunks from a single probing chunk, these small chunks become a performance bottleneck. The proposed hash join effectively addresses this by "compacting" the small chunks without actually performing data copying. For this reason, this method is termed logical compaction. Conversely, in a Filter-style Case, where the operator outputs only one reduced chunk from a probing chunk, the proposed hash join does not offer an advantage over the traditional method and still outputs small chunks. In such cases, we rely on the learning module described in Section 4 to tackle the issue.

在连接型案例中，当运算符从单个探测块输出多个缩减块时，这些小块会成为性能瓶颈。提出的哈希连接通过"逻辑压缩"这些小块（无需实际执行数据复制）有效解决此问题，因此该方法被称为逻辑压缩。相反，在过滤型案例中，当运算符从探测块仅输出一个缩减块时，该方法相较传统方式并无优势，仍会输出小块。此类情况下，我们依赖第4节所述的学习模块解决问题。

Importantly, the learning module is agnostic to the specific implementation of the hash join operator; it focuses solely on compacting small chunks produced by the hash join and other chunk-reducing operators. The integration of the compacted vectorized hash join with the learning module provides a comprehensive solution to the compaction problem.

值得注意的是，学习模块不依赖哈希连接运算符的具体实现，仅专注于压缩哈希连接及其他块缩减运算符产生的小块。压缩向量化哈希连接与学习模块的结合，为压缩问题提供了完整解决方案。

## 6 Microbenchmark Evaluation

## 6 微基准测试评估

We now evaluate the proposed compaction learning module, referred to as Learning Compaction, and the compacted vectorized hash join, referred to as Logical Compaction.

现评估提出的压缩学习模块（称为学习压缩）与压缩向量化哈希连接（称为逻辑压缩）。

For the evaluation, we implement a vectorized execution engine supporting scan, filter, and hash join operators. The default size of a full chunk is set to 2048. During execution, this engine retrieves a data chunk from an in-memory data collection and processes it through a pipeline of operators. For filtering, we utilize a selection vector to mark valid tuples, and for hash joins, we implement a vectorized version similar to that in DuckDB. This hash join utilizes a chaining hash table with a load factor of 0.5 . The load factor is defined as the ratio of the number of tuples to the number of buckets in the hash table. This vectorized execution engine is implemented in C++ without any explicit SIMD instructions.

评估中，我们实现了支持扫描、过滤和哈希连接运算符的向量化执行引擎。默认全块大小设为2048。执行时，该引擎从内存数据集合获取数据块，并通过运算符管道进行处理。过滤操作使用选择向量标记有效元组，哈希连接则采用类似DuckDB的向量化实现。该哈希连接使用链式哈希表，负载因子为0.5。负载因子定义为哈希表中元组数量与桶数量的比值。该向量化执行引擎采用C++实现，未使用显式SIMD指令。

<!-- Media -->

<!-- figureText: No Cpt. Full Cpt. Binary Cpt. Learning Cpt. Logical Cpt. 20 10 5 20 25 30 20 25 Chunk-reducing Factor Chunk-reducing Factor #of Join: 2, Tuple Length: 100 bytes 10 Execution Time (s) 12.5 10.0 5.0 2.5 5 10 25 30 5 Chunk-reducing Factor -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_14.jpg?x=144&y=269&w=1277&h=427&r=0"/>

Fig. 10. Execution Time vs. Chunk-Reducing Factor - This figure shows the execution times for various compaction methods across different CRFs with join numbers 2,3, and 4 . The tuple length of the probing table is fixed at 100 bytes by adjusting its string column str.

图10. 执行时间与块缩减因子关系 - 本图展示连接数为2、3、4时，不同压缩方法在不同CRF下的执行时间。探测表的元组长度通过调整其字符串列str固定为100字节。

<!-- Media -->

Baselines. We compare our solutions to three other compaction methods: (1) No Compaction; (2) Apache Data Fusion's Full Compaction [23]; and (3) DuckDB's Binary Compaction. No Compaction is the simplest approach, where no chunk undergoes compaction. Full Compaction maintains a tuple buffer after each filter and hash join operator. It pushes any non-full chunk into this buffer and outputs a full chunk if there are enough tuples in the buffer. Binary Compaction also utilizes a tuple buffer but only compacts chunks containing $\leq  {128}$ tuples,and it outputs a near-full chunk if there are $\geq  {1920}$ tuples in the buffer.

基线方案。我们将所提方案与三种压缩方法对比：(1)无压缩；(2)Apache Data Fusion的完全压缩[23]；(3)DuckDB的二进制压缩。无压缩是最简单方式，不对任何块执行压缩。完全压缩在每个过滤器和哈希连接运算符后维护元组缓冲区，将非完整块推入缓冲区，当缓冲区内元组足够时输出完整块。二进制压缩也使用元组缓冲区，但仅压缩包含$\leq  {128}$个元组的块，当缓冲区有$\geq  {1920}$个元组时输出接近完整的块。

Experimental Setup. We run experiments using our in-house server with 512 GB of DDR5 main memory at ${4800}\mathrm{{MHz}}$ and a $1\mathrm{\;{TB}}{\text{Intel}}^{ \otimes  }$ SSD D5-P5530. The server is equipped with two sockets of Intel ${}^{ \oplus  }$ Xeon ${}^{ \oplus  }$ 8474C 2.1 GHz processors (48 cores),each capable of supporting 96 threads. We use Debian GNU/Linux 12 and GCC 12.2 with -03 enabled. Experiments are conducted with a single thread unless stated otherwise.

实验设置。使用配备512GB DDR5内存（频率${4800}\mathrm{{MHz}}$）和$1\mathrm{\;{TB}}{\text{Intel}}^{ \otimes  }$ SSD D5-P5530的内部服务器进行实验。服务器配置两颗Intel${}^{ \oplus  }$至强${}^{ \oplus  }$ 8474C 2.1GHz处理器（48核），每颗支持96线程。系统采用Debian GNU/Linux 12和GCC 12.2（启用-O3优化）。除特别说明外，实验均在单线程下进行。

### 6.1 Synthetic Experiment

### 6.1 合成实验

We first evaluate these compaction methods using a left-deep join query comprising a probing side table $R$ and $k$ building side tables ${S}_{1},\ldots ,{S}_{k}$ . The table $R$ includes the columns $\left( {i{d}_{1},\ldots ,i{d}_{k},{str}}\right)$ , while each table ${S}_{i}$ includes the columns $\left( {i{d}_{i},\text{ misc }}\right)$ . The data type of column $i{d}_{i}$ is a 64-bit integer, and the data types of str and misc are strings. By default, values in the column misc are fixed at 8 bytes,making each tuple in ${S}_{i}{16}$ bytes in length. The table $R$ has 20 million tuples,while each table ${S}_{i}$ has 2 million tuples. The query plan performs a natural join of $R$ with ${S}_{1}$ through ${S}_{k}$ . For each join, the resulting output relation has the same cardinality as the probing relation, neutralizing the impact of intermediate result sizes. We vary the join number $k$ from 2 to 4 and the chunk-reducing factor $r$ from 2 to 32 in this experiment. We record the time of query execution,excluding the time spent building the hash table.

我们首先采用左深连接查询评估这些压缩方法，该查询包含一个探测侧表$R$和$k$个构建侧表${S}_{1},\ldots ,{S}_{k}$。表$R$包含列$\left( {i{d}_{1},\ldots ,i{d}_{k},{str}}\right)$，而每个表${S}_{i}$包含列$\left( {i{d}_{i},\text{ misc }}\right)$。列$i{d}_{i}$的数据类型为64位整数，str和misc的数据类型为字符串。默认情况下，misc列的值固定为8字节，使得${S}_{i}{16}$中的每个元组长度为字节。表$R$包含2000万元组，而每个表${S}_{i}$包含200万元组。该查询计划通过${S}_{k}$将$R$与${S}_{1}$进行自然连接。每次连接产生的输出关系基数与探测关系相同，从而消除中间结果大小的影响。本实验中，我们将连接数$k$从2调整到4，块缩减因子$r$从2调整到32。记录查询执行时间（不包括构建哈希表的时间）。

Figure 10 shows the results. First, Logical Compaction is the most efficient among these methods, providing a speedup of up to $3 \times$ compared to Binary Compaction. This can be attributed to its ability to compact most chunks without incurring data-copying costs, thereby adopting a more aggressive compaction policy. Second, when the join number is two, Learning Compaction exhibits significantly lower execution latency (by $2 \times$ ) compared to Binary Compaction. This is because Learning Compaction adopts a more conservative compaction policy in this scenario. Although Binary Compaction compacts more chunks, it does not yield substantial benefits. However, for deeper pipelines or higher CRFs, the performance of Learning Compaction and Binary Compaction tends to converge. This is because deeper pipelines or higher CRFs necessitate a more aggressive compaction policy, which is precisely what Binary Compaction offers. Finally, Full Compaction proves to be more stable than the no-compaction policy. This is because the cost of compaction is generally much lower than the interpretation cost. Consequently, in scenarios involving deep pipelines and high CRFs, No Compaction suffers from higher interpretation costs.

图10展示了实验结果。首先，逻辑压缩是这些方法中最有效的，相比二进制压缩可实现高达$3 \times$的加速。这归因于其能在不产生数据复制成本的情况下压缩大多数数据块，因而采用更激进的压缩策略。其次，当连接数为2时，学习压缩的执行延迟显著低于二进制压缩（降低$2 \times$）。这是因为在此场景下学习压缩采用更保守的压缩策略。虽然二进制压缩处理更多数据块，但并未带来实质性收益。然而对于更深流水线或更高CRF值，学习压缩与二进制压缩的性能趋于接近。这是因为更深流水线或更高CRF需要更激进的压缩策略，而这正是二进制压缩的优势所在。最后，完全压缩被证明比无压缩策略更稳定，因为压缩成本通常远低于解释成本。因此在深流水线和高CRF场景中，无压缩策略会承受更高的解释成本。

<!-- Media -->

<!-- figureText: 30 Learning Cpt. Logical Cpt. 600 800 1000 Tuple Length (bytes) Execution Time (s) No Cpt. 25 Full Cpt. 20 Binary Cpt. 15 10 0 32 200 400 -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_15.jpg?x=157&y=300&w=608&h=349&r=0"/>

Fig. 11. Execution Time vs. Tuple Length - With a join number of 4 and a CRF of 8 , we vary the tuple length of the table $R$ to demonstrate the robustness of Learning Compaction.

图11. 执行时间vs元组长度 - 在连接数4和CRF8的条件下，我们改变表$R$的元组长度以验证学习压缩的鲁棒性。

<!-- figureText: ${\alpha }_{1}$ ${\alpha }_{2}$ ${\alpha }_{3}$ 0.85 [ ase ] 0.99 Z aseJ 768 Threshold Values 0.4 Percentages 0.2 0.0 0.4 0.2 0.0 384 -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_15.jpg?x=802&y=278&w=608&h=395&r=0"/>

Fig. 12. Distribution of Learned Thresholds - We execute a left-deep query of joining tables $R,{S}_{1},{S}_{2}$ ,and ${S}_{3}$ . In case 1,the tuple length of table ${S}_{2}$ is 16 bytes, and in case 2, it is 1000 bytes.

图12. 学习阈值分布 - 我们执行连接表$R,{S}_{1},{S}_{2}$和${S}_{3}$的左深查询。案例1中表${S}_{2}$的元组长度为16字节，案例2中为1000字节。

<!-- Media -->

### 6.2 The Compaction Trade-off

### 6.2 压缩权衡

We then explore the trade-off between compaction cost and interpretation cost by varying the tuple length. We considered the following pipeline of joining five tables: $\operatorname{Scan}\left( R\right)  \rightarrow  \operatorname{Join}\left( {R,{S}_{1}}\right)  \rightarrow$ $\operatorname{Join}\left( {R,{S}_{2}}\right)  \rightarrow  \operatorname{Join}\left( {R,{S}_{3}}\right)  \rightarrow  \operatorname{Join}\left( {R,{S}_{4}}\right)$ with $R$ having 20 million tuples and each of ${S}_{i}$ having 2 million tuples. Each tuple in ${S}_{i}$ has a fixed length of 16 bytes. We vary the tuple length in $R$ from 32 bytes to 1000 bytes to show the performance impact of different compaction costs. We set the CRF to 8 in this experiment.

接着我们通过改变元组长度来探索压缩成本与解释成本之间的权衡。考虑以下连接五个表的流水线：包含2000万元组的$\operatorname{Scan}\left( R\right)  \rightarrow  \operatorname{Join}\left( {R,{S}_{1}}\right)  \rightarrow$$\operatorname{Join}\left( {R,{S}_{2}}\right)  \rightarrow  \operatorname{Join}\left( {R,{S}_{3}}\right)  \rightarrow  \operatorname{Join}\left( {R,{S}_{4}}\right)$与$R$，其中每个${S}_{i}$包含200万元组。${S}_{i}$中每个元组固定长度为16字节。我们将$R$的元组长度从32字节调整到1000字节，以展示不同压缩成本对性能的影响。本实验设定CRF为8。

Figure 11 demonstrates the impact of tuple length on compaction strategies. For tuples under 600 bytes, Full Compaction is more effective than No Compaction. However, for tuples over 600 bytes, No Compaction performs better because of the high costs of copying long tuples. While the benefits of compaction remain consistent across different tuple lengths, the cost of compaction becomes higher. Additionally, Learning Compaction consistently shows lower latency than Binary Compaction for longer tuples. This is because Binary Compaction, which excels when interpretation costs dominate, applies aggressive strategies even when compaction costs are high, leading to suboptimal performance. In contrast, Learning Compaction effectively balances the compaction trade-offs, resulting in superior performance. Furthermore, as expected, tuple length has little effect on Logical Compaction.

图11展示了元组长度对压缩策略的影响。对于600字节以下的元组，完全压缩（Full Compaction）比不压缩（No Compaction）更有效。然而，对于超过600字节的元组，由于复制长元组的高成本，不压缩表现更优。虽然压缩的收益在不同元组长度间保持稳定，但压缩成本会随之增加。此外，学习型压缩（Learning Compaction）对于较长元组始终展现出比二分压缩（Binary Compaction）更低的延迟。这是因为二分压缩在解释成本占主导时表现优异，但即便压缩成本很高时仍采用激进策略，导致性能欠佳。相比之下，学习型压缩能有效权衡压缩利弊，从而实现更优性能。另外正如预期，元组长度对逻辑压缩（Logical Compaction）影响甚微。

### 6.3 Distribution of Learned Thresholds

### 6.3 学习阈值的分布规律

We then show that Learning Compaction indeed learns something useful across diverse workloads. Using the same left-deep query as described in Section 6.1 with $k = 3$ and a CRF of $r = 8$ ,we join four tables $R,{S}_{1},{S}_{2}$ ,and ${S}_{3}$ . Comparing two scenarios,in the first case,tuple lengths for $R$ , ${S}_{1},{S}_{2}$ ,and ${S}_{3}$ are 32,16,16,and 16 bytes,respectively. In the second case,tuple lengths are 32,16, 1000,and 16 bytes. Both cases involve three hash join operators,with the $i$ -th operator joining its probing table with the building table ${S}_{i}$ and employing a threshold ${\alpha }_{i}$ for compaction. We execute the queries with Learning Compaction and record the chosen arms for each ${\alpha }_{i}$ .

我们通过实验证明学习型压缩确实能在多样化工作负载中学到有效策略。采用与6.1节相同的左深查询（left-deep query），其中$k = 3$且冲突解决因子（CRF）为$r = 8$，我们对四个表$R,{S}_{1},{S}_{2}$和${S}_{3}$进行连接。比较两种场景：第一种情况下，$R$、${S}_{1},{S}_{2}$和${S}_{3}$的元组长度分别为32、16、16和16字节；第二种情况下则为32、16、1000和16字节。两种场景均包含三个哈希连接运算符，其中第$i$个运算符将其探测表与构建表${S}_{i}$连接，并采用阈值${\alpha }_{i}$决定压缩策略。我们使用学习型压缩执行查询，并记录每个${\alpha }_{i}$对应的选择策略。

<!-- Media -->

<!-- figureText: Single Selection Vector Multiple Selection Vector Evaluate Expression Update Sel Vectors 64 2 4 16 32 64 Number of Columns Execution Time (s) 1.00 Evaluate Expression Update Sel Vectors 0.75 0.50 0.25 0.00 2 4 16 32 Number of Columns -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_16.jpg?x=151&y=270&w=699&h=323&r=0"/>

Fig. 13. Overhead of Updating Additional SVs - We provide a breakdown of the filter execution. The left figure represents the single SV chunk, while the right figure represents the chunk where each column has its own SV.

图13. 更新选择向量(SV)的额外开销 - 我们分解展示了过滤器执行过程。左图表示单一SV数据块，右图表示每列拥有独立SV的数据块。

<!-- figureText: Join Number: 4, Payload Length: 100 bytes ${2}^{1}$ ${2}^{3}$ Load Factor Execution Time (s) No Cpt. 40 Full Cpt. Binary Cpt. Learning Cpt. 20 Logical Cpt. 10 ${2}^{-3}$ ${2}^{-1}$ -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_16.jpg?x=884&y=272&w=527&h=320&r=0"/>

Fig. 14. Load Factor vs. Execution Time - A large hash table can reduce the effect of hash collisions. With chaining hash tables, load factor $= \#$ of inserted tuples / $\#$ of buckets.

图14. 负载因子与执行时间关系 - 大型哈希表可降低哈希冲突的影响。对于链式哈希表，负载因子$= \#$=已插入元组数/$\#$桶数量。

<!-- Media -->

Figure 12 shows the learned distribution for the two scenarios. First, both cases prioritize selecting ${\alpha }_{3} = 0$ because it is for the last join with no subsequent operators. Second,we observe the trend ${\bar{\alpha }}_{1} \geq  {\bar{\alpha }}_{2} \geq  {\bar{\alpha }}_{3}$ ,where ${\bar{\alpha }}_{i}$ is the most frequent chosen value for ${\alpha }_{i}$ . The trend aligns with the conclusions drawn from our simulations in Section 3.4: the position of an operator within a pipeline influences its compaction policy because of the pipeline-level gain. Third, in case 2, the compaction learner adapts by employing a more aggressive compaction policy for the first join, thereby alleviating compaction pressure for the subsequent join. Specifically,its ${\alpha }_{1}$ is larger,and ${\alpha }_{2}$ is smaller compared to Case 1. This adjustment is prompted by the presence of longer tuples in table ${S}_{2}$ ,which leads to increased compaction costs for the 2nd and 3rd joins. This comparison shows how each operator can collaborate to establish a globally optimal compaction policy. Consequently, in case 2,Learning Compaction is ${1.76} \times$ faster than Binary Compaction.

图12展示了两种场景下学习到的策略分布。首先，两种案例都优先选择${\alpha }_{3} = 0$，因为该策略用于没有后续运算符的最终连接。其次，我们观察到${\bar{\alpha }}_{1} \geq  {\bar{\alpha }}_{2} \geq  {\bar{\alpha }}_{3}$趋势，其中${\bar{\alpha }}_{i}$成为${\alpha }_{i}$最常选择的阈值。这一趋势与3.4节模拟实验结论一致：运算符在流水线中的位置会影响其压缩策略，因为涉及流水线级收益。第三，在案例2中，压缩学习器通过为第一个连接采用更激进的压缩策略来自适应调整，从而缓解后续连接的压缩压力。具体表现为：相较于案例1，其${\alpha }_{1}$值更大而${\alpha }_{2}$值更小。这种调整是由于表${S}_{2}$中存在较长元组，导致第二、第三个连接的压缩成本增加。该对比表明各运算符如何协作形成全局最优压缩策略。最终在案例2中，学习型压缩比二分压缩快${1.76} \times$。

### 6.4 Overhead of Selection Vectors

### 6.4 选择向量的开销分析

We then show that even though the compacted vectorized hash join introduces additional SVs, they bring minimal overhead. We execute a filter on a table $R$ with $k$ columns $\left( {i{d}_{1},\cdots ,i{d}_{k}}\right)$ ,where the data type of each column is a 64-bit integer. The column $i{d}_{1}$ contains uniformly distributed values between 0 and 100 . The filter query is SELECT * FROM $R$ WHERE $i{d}_{1}/{100} < {0.3}$ ,with a selectivity of 0.3. We consider two chunk formats,both containing $k$ vectors for data. The first format holds one SV for all columns, while in the second format, each column holds an SV. Thus, the filter operator needs to update all SVs for the second chunk format. Since a filter is a very lightweight operator in databases, this experiment can effectively reflect the overhead of multiple SVs.

随后我们证明，尽管压缩向量化哈希连接引入了额外的选择向量(SVs)，但其带来的开销微乎其微。我们在表$R$上执行过滤操作，该表包含$k$个$\left( {i{d}_{1},\cdots ,i{d}_{k}}\right)$列，每列数据类型为64位整数。其中列$i{d}_{1}$包含0到100之间均匀分布的数值。过滤查询语句为SELECT * FROM $R$ WHERE $i{d}_{1}/{100} < {0.3}$，选择率为0.3。我们考察两种数据块格式：第一种所有列共享一个SV，第二种每列独立拥有SV。由于过滤是数据库中极轻量级的操作，本实验能有效反映多SV更新的开销。

Figure 13 shows that updating SVs becomes a bottleneck when their number exceeds eight. Therefore,it is safe to have $\leq  8\mathrm{{SVs}}$ in a chunk. Since the number of SVs increases by one only when passing through a hash join operator, and queries with numerous joins are rare, the time cost of updating SVs will not be a performance bottleneck.

图13显示当SV数量超过8个时，其更新操作将成为瓶颈。因此每个数据块设置$\leq  8\mathrm{{SVs}}$个SV是安全的。鉴于SV数量仅在经过哈希连接算子时增加，且多连接查询场景罕见，更新SV的时间成本不会成为性能瓶颈。

### 6.5 The Chain in Hash Table Buckets

### 6.5 哈希表桶中的链式结构

A hash table bucket may have a chain of length $> 1$ either due to hash collisions or multiple tuples in the build table with the same key. We show that the impact of hash collisions decreases with increasing hash table size. Following the setup described in Section 6.1, we set the join number $k = 4$ and the length of each tuple in ${S}_{i}$ to 100 bytes. We ensure that each building side table ${S}_{i}$ has 2 million tuples. And vary the number of buckets in each hash table, such that the load factor ranges from ${2}^{-4}$ to ${2}^{3}$ . Figure 14 shows that we can reduce the effect of hash collisions by setting a load factor of less than 1 for the hash table. Therefore, the load factor of the hash table is set to 0.5 in our experiments.

哈希表桶可能因哈希冲突或构建表中存在$> 1$个相同键值的元组而形成链。我们证明随着哈希表容量增大，哈希冲突的影响会减弱。沿用6.1节的实验配置，设置连接数$k = 4$，每个${S}_{i}$的元组长度为100字节，确保每个构建表${S}_{i}$包含200万元组。通过调整哈希表桶数量使负载因子在${2}^{-4}$到${2}^{3}$之间变化。图14表明将负载因子控制在1以下可有效抑制哈希冲突，故实验采用0.5的负载因子。

<!-- Media -->

<!-- figureText: #of Join: 3, $\mathrm{{CRF}} = 8$ #of Join: 3, $\mathrm{{CRF}} = {16}$ No Cpt. Full Cpt. Logical Cpt. 8M 8 256 8K 256K 8M Block Size Execution Time (s) 40 No Cpt. Full Cpt. 30 Logical Cpt. 20 10 0 8 256 8K 256K Block Size -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_17.jpg?x=151&y=306&w=706&h=378&r=0"/>

Fig. 15. Varying the Data Chunk Sizes - We show the robustness of Logical Compaction by varying the data chunk sizes.

图15. 不同数据块尺寸下的表现 - 通过改变数据块尺寸验证逻辑压缩(Logical Compaction)的鲁棒性。

<!-- figureText: Norm. Execution Time 1.8 Mixed Filter & Join Compaction Cases Binary Cpt. Learning Cpt. Logical Cpt. Smart Cpt. - 10% 15% Filter Selectivity 1.6 1.4 1.2 1.0 5% -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_17.jpg?x=897&y=299&w=513&h=376&r=0"/>

Fig. 16. Filter & Join-style Cases - We adjust the filter selectivity to transition from a Filter & Join-style case to a purely Join-style case.

图16. 过滤&连接混合场景 - 通过调整过滤选择率实现从过滤主导到纯连接场景的过渡。

<!-- Media -->

### 6.6 Block Sizes

### 6.6 块大小优化

We then show the effectiveness of Logical Compaction over different data chunk sizes. We consider the same pipeline as in Section 6.1 by setting join number $k = 3$ ,i.e.,Scan $\left( R\right)  \rightarrow  \operatorname{Join}\left( {R,{S}_{1}}\right)$ $\rightarrow$ Join $\left( {R,{S}_{2}}\right)  \rightarrow$ Join $\left( {R,{S}_{3}}\right)$ with $R$ having 20 million tuples and each of ${S}_{i}$ having 2 million tuples. The tuple lengths of $R,{S}_{1},{S}_{2},{S}_{3}$ are 32,16,16 and 16 bytes,respectively. We vary the chunk sizes to measure the execution time. We consider No Compaction, Full Compaction, and Logical Compaction in this experiment because they do not have pre-defined parameters that depend on the block size. Figure 15 shows the result, which agrees with the conclusion that the optimal data chunk size is in a few thousand tuples [5]. It also shows that Logical Compaction reduces the performance degradation caused by small data chunks, i.e., it flattens the curve.

我们通过不同数据块尺寸验证逻辑压缩的有效性。采用与6.1节相同的流水线结构：设置连接数$k = 3$，即扫描$\left( R\right)  \rightarrow  \operatorname{Join}\left( {R,{S}_{1}}\right)$ $\rightarrow$后连接$\left( {R,{S}_{2}}\right)  \rightarrow$和$\left( {R,{S}_{3}}\right)$，其中$R$包含2000万元组，${S}_{i}$各含200万元组。$R,{S}_{1},{S}_{2},{S}_{3}$的元组长度分别为32、16、16和16字节。实验对比无压缩、完全压缩和逻辑压缩方案（这些方案不受预定义块大小参数影响）。图15结果显示：1) 最优数据块尺寸在数千元组量级[5]；2) 逻辑压缩能有效缓解小数据块导致的性能下降，使曲线趋于平缓。

### 6.7 Mixed Filter & Join Compaction Cases

### 6.7 混合过滤&连接压缩场景

Logical Compaction is designed for joins while Learning Compaction is mostly effective on filters. We combine them and refer to this combination approach as Smart Compaction. We create a pipeline containing both filters and joins and then vary the filter selectivities to show their relative significance. We first apply a filter on table $R$ ’s column $i{d}_{1}$ ,and then join the filtered results with table ${S}_{1},{S}_{2}$ ,and ${S}_{3}$ . The tuple length of $R,{S}_{1},{S}_{2}$ and ${S}_{3}$ are32,2000,16,and 16 bytes,respectively. The query pipeline is $\operatorname{Scan}\left( R\right)  \rightarrow$ Filter $\left( R\right)  \rightarrow  \operatorname{Join}\left( {R,{S}_{1}}\right)  \rightarrow  \operatorname{Join}\left( {R,{S}_{2}}\right)  \rightarrow  \operatorname{Join}\left( R\right.$ , ${S}_{3}$ ). The CRF of each join is set to 5 . Table $R$ has 200 million tuples,and each ${S}_{i}$ has 2 million tuples.

逻辑压缩(Logical Compaction)专为连接操作设计，而学习压缩(Learning Compaction)在过滤场景中效果显著。我们将二者结合，称这种混合方法为智能压缩(Smart Compaction)。构建包含过滤和连接的查询管道后，通过调整过滤选择性来展示其相对重要性：先对表$R$的$i{d}_{1}$列进行过滤，再将结果与表${S}_{1},{S}_{2}$和${S}_{3}$连接。各表元组长度分别为32、2000、16和16字节。查询管道结构为$\operatorname{Scan}\left( R\right)  \rightarrow$过滤$\left( R\right)  \rightarrow  \operatorname{Join}\left( {R,{S}_{1}}\right)  \rightarrow  \operatorname{Join}\left( {R,{S}_{2}}\right)  \rightarrow  \operatorname{Join}\left( R\right.$后连接${S}_{3}$，所有连接操作的CRF值设为5。其中表$R$含2亿条元组，每个${S}_{i}$含200万条元组。

Figure 16 shows the execution time of each method normalized by that of Smart Compaction. When the filter selectivity is low, the pipeline presents a Filter & Join-style compaction case. Both Logical Compaction and the Learning Compaction can only handle this case partially. But their combination, Smart Compaction, can tackle the mixed-style case effectively. When the filter selectivity is high, the pipeline presents a pure Join-style case. Thus, Smart Compaction has the same performance as Logical Compaction. We conclude that Logical Cpt. is effective only for Join-style cases; therefore, Learning Cpt. is necessary to handle Filter-style compaction cases.

图16显示各方法执行时间与智能压缩的标准化比值。当过滤选择性较低时，管道呈现"过滤+连接"混合场景，单独使用逻辑压缩或学习压缩都只能部分处理，而智能压缩能有效应对这种混合模式。当过滤选择性较高时，管道转为纯连接场景，此时智能压缩表现与逻辑压缩相当。由此得出结论：逻辑压缩仅适用于纯连接场景，而处理过滤型压缩必须依赖学习压缩。

## 7 Full DBMS Evaluation

## 7 全数据库系统评估

In this section, we integrated our solutions into DuckDB (v0.8.1) [37] and measured the end-to-end performance using three benchmarks: the Join Order Benchmark (JOB) [26], TPC-H [9] and TPC-DS [8]. DuckDB is a state-of-the-art, in-process OLAP database system. It contains a columnar-vectorized query execution engine that is specifically designed to handle OLAP workloads. We integrated the compaction learning module (Learning Cpt.) into the system and replaced the default hash join operator with our compacted version (Logical Cpt.) ${}^{1}$ . We refer to this integrated approach as Smart Cpt.

本节将我们的方案集成至DuckDB(v0.8.1)[37]，使用连接顺序基准(JOB)[26]、TPC-H[9]和TPC-DS[8]三个基准测试端到端性能。DuckDB是最先进的进程内OLAP数据库系统，其列式向量化查询引擎专为OLAP工作负载设计。我们将学习压缩模块集成到系统中，并用压缩版哈希连接算子(逻辑压缩)替代默认实现${}^{1}$，称该集成方案为智能压缩。

<!-- Media -->

<!-- figureText: No Cpt. Full Cpt. Binary Cpt. Smart Cpt. 140 140.2 159.4 Total Time (s) 120 120.9 117.7 105.8 100 80 153.4 60 10) JOB 50 180 174.4 Total Time (s) 45 44.5 Total Time (s) 160 140 120 41.7 41.2 40 39.2 35 30 25 100 TPC-DS -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_18.jpg?x=306&y=274&w=957&h=347&r=0"/>

Fig. 17. Benchmarks Overview - We compare the total execution times of all compaction methods across three benchmarks.

图17. 基准测试概览 - 对比三种基准测试中所有压缩方法的总执行时间

<!-- Media -->

The JOB benchmark is based on real data, specifically the IMDB dataset [42], comprising 113 multi-join queries that offer a challenging, varied, and authentic workload. In contrast, TPC-H is based on synthetic data, where the benchmark requires that data for database columns be generated from a uniform distribution. Although some columns in TPC-DS are generated using skewed distributions, the dataset still does not utilize real data.

JOB基准基于真实IMDB数据集[42]，包含113个多连接查询，提供具有挑战性、多样性且真实的工作负载。相比之下，TPC-H采用合成数据，要求数据库列数据服从均匀分布生成。虽然TPC-DS部分列使用偏态分布生成，但仍未采用真实数据。

### 7.1 Performance Overview

### 7.1 性能概览

We present a performance overview of all compaction methods across three benchmarks in DuckDB, with the scale factors for TPC-H and TPC-DS set to 10. Each benchmark is run on a single thread using DuckDB's internal benchmark tools. Given the extensive number of queries in each benchmark, we measure and sum the total execution times. Figure 17 illustrates that our proposed Smart Compaction consistently and significantly outperforms both the No Compaction and DuckDB's default method, Binary Compaction, across all benchmarks. Specifically, in the JOB benchmark, Smart Compaction boosts DuckDB's performance by up to 10%.

在TPC-H和TPC-DS比例因子设为10的条件下，我们展示DuckDB中所有压缩方法在三个基准测试的表现。使用DuckDB内置基准工具单线程运行测试，鉴于每个基准包含大量查询，我们统计总执行时间。图17显示，智能压缩在所有基准测试中持续显著优于无压缩和DuckDB默认的二进制压缩方法，其中在JOB基准中最高可提升DuckDB性能10%。

Additionally, No Compaction performs the worst. Full Compaction and Binary Compaction methods have similar performance. This indicates that the compaction trade-off is predominantly influenced by interpretation costs, similar to Case 3 in Figure 5. We note that standard benchmarks may not accurately represent real-world workloads [14, 48]. For example, none of them include tables with large tuples, which leads to high compaction costs. Despite these considerations, the Smart Compaction performs well. We then explore the underlying reasons in the following sections.

无压缩方法表现最差，全压缩与二进制压缩性能相近，这表明压缩权衡主要受解释成本影响（类似图5案例3）。需要注意的是，标准基准测试可能无法准确反映真实工作负载[14,48]，例如它们都不包含大元组表（会导致高压缩成本）。尽管如此，智能压缩仍表现优异，具体原因将在后续章节探讨。

### 7.2 Benchmark Analysis

### 7.2 基准测试分析

We perform an in-depth analysis across three benchmarks to identify the queries that benefit most from compaction strategies. By profiling each hash join operator, we evaluate two key metrics for every benchmark: (1) the average chunk size at runtime, derived from the size of all result chunks generated by hash joins, indicating the degree of chunk reduction; and (2) the average result chunk number, computed as the ratio of result chunk number to input chunk number for each hash join operator, illustrating the number of child chunks generated per probing chunk. As shown in Table 1, the speedup provided by Smart Compaction is closely correlated with these two metrics. Smaller chunk sizes and larger chunk numbers correspond to greater advantages offered by Smart Compaction. For instance, in the JOB benchmark, the average chunk size is only 54, and long bucket chains result in the generation of $\geq  {600}$ child chunks from a single input chunk.

我们通过三个基准测试进行深入分析，以确定哪些查询最能从压缩策略中获益。通过剖析每个哈希连接算子，我们评估了每个基准测试的两项关键指标：(1) 运行时平均分块大小，该值源自哈希连接生成的所有结果分块尺寸，反映分块缩减程度；(2) 平均结果分块数量，计算为每个哈希连接算子的结果分块数与输入分块数之比，表示每个探测分块生成的子分块数量。如表1所示，智能压缩(Smart Compaction)带来的加速效果与这两个指标密切相关。分块尺寸越小、分块数量越大，智能压缩的优势就越显著。例如在JOB基准测试中，平均分块大小仅为54，且长桶链会导致单个输入分块生成$\geq  {600}$个子分块。

---

<!-- Footnote -->

${}^{1}$ Our patch [34],introducing Logical Compaction to DuckDB,was merged in DuckDB v1.1.3.

${}^{1}$ 我们提交的逻辑压缩(Logical Compaction)补丁[34]已被合并至DuckDB v1.1.3版本。

<!-- Footnote -->

---

<!-- Media -->

<!-- figureText: 10 No Cpt. Full Cpt. Binary Cpt. Smart Cpt. 1.69 JOB-17f JOB-17d JOB-17b JOB-17c JOB-17e JOB-11d Execution Time (s) 8 JOB-08c JOB-08d JOB-19d JOB-16b JOB-09d -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_19.jpg?x=144&y=274&w=1274&h=348&r=0"/>

Fig. 18. Execution Time for JOB Queries with Hash Join Bottlenecks - This figure illustrates the impact of different compaction methods on the performance of selected JOB queries, all of which feature hash join bottlenecks that require compaction.

图18. 存在哈希连接瓶颈的JOB查询执行时间 - 本图展示了不同压缩方法对选定JOB查询性能的影响，这些查询均存在需要压缩处理的哈希连接瓶颈。

Table 1. Profile of Hash Join Operators - We collect the runtime statistics for all three benchmarks executed by DuckDB default.

表1. 哈希连接算子性能分析 - 我们收集了DuckDB默认执行所有三个基准测试时的运行时统计信息。

<table><tr><td/><td>Avg. Chunk Size</td><td>Avg. # of Chunks</td><td>Smart Cpt. Speedup</td></tr><tr><td>TPC-H</td><td>125.58</td><td>3.16</td><td>${1.13} \times$</td></tr><tr><td>TPC-DS</td><td>219.58</td><td>397.4</td><td>${1.21} \times$</td></tr><tr><td>JOB</td><td>54.05</td><td>689.6</td><td>${1.32} \times$</td></tr></table>

<table><tbody><tr><td></td><td>平均块大小</td><td>平均块数量</td><td>智能压缩加速比</td></tr><tr><td>TPC-H（事务处理性能委员会基准测试H）</td><td>125.58</td><td>3.16</td><td>${1.13} \times$</td></tr><tr><td>TPC-DS（事务处理性能委员会基准测试DS）</td><td>219.58</td><td>397.4</td><td>${1.21} \times$</td></tr><tr><td>JOB（作业）</td><td>54.05</td><td>689.6</td><td>${1.32} \times$</td></tr></tbody></table>

<!-- Media -->

Therefore, we conclude that a query benefits from chunk compaction if it includes at least one hash join operator that: (1) constitutes a major bottleneck in the query execution, accounting for at least ${10}\%$ of the total execution time; and (2) on average,generates many ( $\geq  {50}$ ) result chunks from a single input chunk. After identifying all such hash join operators across benchmarks, we select queries containing at least one qualifying operator. This process identifies 12 queries: 11 from JOB, 1 from TPC-DS, and none from TPC-H, reflecting the performance improvements seen in each benchmark. Among these, Smart Compaction offers a geometric mean performance enhancement of 34% over the standard DuckDB that employs Binary Compaction.

因此我们得出结论：当查询包含至少一个满足以下条件的哈希连接运算符时，采用分块压缩能带来性能提升：(1)该运算符构成查询执行的主要瓶颈，占总执行时间的至少${10}\%$；(2)平均每个输入分块会生成大量($\geq  {50}$)结果分块。通过识别所有基准测试中符合条件的哈希连接运算符，我们筛选出包含至少一个此类运算符的12个查询（JOB基准11个，TPC-DS基准1个，TPC-H基准0个），这与各基准测试的性能改进情况相符。其中智能压缩技术相比采用二分压缩的标准DuckDB实现了34%的几何平均性能提升。

Figure 18 displays the selected queries from the JOB benchmark. Notably, query 19d achieves the highest speedup in our experiments,reaching up to ${2.34} \times   -$ a ${63}\%$ improvement over Binary Compaction. Additionally, Full Compaction approaches the performance of Binary Compaction, because these queries have high CRFs, resulting in interpretation costs dominating the compaction trade-off. Smart Compaction provides distinct advantages over other methods because 1) it greatly reduces the compaction cost, and 2) estimates a specified compaction threshold for each CRO.

图18展示了JOB基准测试中筛选出的查询。值得注意的是，查询19d在我们的实验中实现了最高加速比，较二分压缩提升达${2.34} \times   -$倍${63}\%$。由于这些查询具有较高的压缩率因子(CRF)，解释成本成为压缩权衡的主导因素，全压缩方案性能接近二分压缩。智能压缩技术具有独特优势：1)大幅降低压缩成本；2)为每个压缩率对象(CRO)估算特定的压缩阈值。

### 7.3 Case Study

### 7.3 案例研究

We then conduct a detailed analysis of two queries, JOB 19d and TPC-H Q9, to understand the source of benefits, focusing on both the compute cost and the compaction cost.

我们随后对JOB 19d和TPC-H Q9两个查询进行详细分析，从计算成本和压缩成本两个维度探究性能提升来源。

For each hash join operator, Figure 19 displays the average number of child chunks, along with the average size of these child chunks. These child chunks are then compacted through physical memory copying before being passed to the next operator. We also record the total time spent on probing and chunk compaction.

图19展示了每个哈希连接运算符产生的子分块平均数量及大小，这些子分块通过物理内存拷贝进行压缩后传递给下一运算符。我们还记录了探测操作和分块压缩的总耗时。

<!-- Media -->

<!-- figureText: Binary Cpt. Smart Cpt. 499.5 2in Execute Time (s) 1.50 1.25 1.00 0.75 0.50 0.25 0.00 7 9 1 3 6 8 Hash Join Operator Hash Join Operator Join Execute Time (s) 4 9 2 1 Q3 5 Hash Join Operator Hash Join Operator $\times  {10}^{3}$ Avg. # of Child Chunks 2.0 $\times  {10}^{2}$ 1.5 1.0 0.5 0.0 JOB 19d vg. Child Chunk Size 1.2 1.0 904.2 0.8 0.6 0.4 0.2 0.0 1 6 8 2 Hash Join Operator $\times  {10}^{3}$ 10 TPC-H Q9 g. Child Chunk Size 3.0 Avg. # of Child Chunks 8 4 2 2.5 2040.8 2.0 1.5 1.0 309.5 Hash Join Operator -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_20.jpg?x=140&y=270&w=1285&h=806&r=0"/>

Fig. 19. Case Study - A Profile of Joins - For each hash join operator, we measure the average size of output chunks, the average number of child chunks generated by a single input chunk, and the execution time. We select two representative queries from a real-data benchmark (JOB) and a synthetic benchmark (TPC-H).

图19. 案例研究-连接操作分析-我们测量了每个哈希连接运算符输出分块的平均大小、单个输入分块生成的子分块数量及执行时间。选取了真实数据基准(JOB)和合成基准(TPC-H)中各一个代表性查询。

<!-- Media -->

7.3.1 JOB 19d. Query 19d from the JOB benchmark involves joining 10 tables, resulting in 9 hash join operators. DuckDB optimizes this query into a right-deep query plan, where most pipelines consist of only one join operator. This is reasonable because the JOB benchmark follows a well-defined relational schema, resulting in a joined result table smaller than other base tables. Typically, hash tables are built on the smaller tables, and since the build side is on the right, the entire plan adopts a right-deep style [26].

7.3.1 JOB 19d查询。该查询涉及10表连接，产生9个哈希连接运算符。DuckDB将其优化为右深查询计划，多数流水线仅含单个连接运算符。这是由于JOB基准采用规范化的关系模式，连接结果表通常小于基表。哈希表通常在较小表上构建，且构建端位于右侧，因此整体采用右深计划[26]。

In this query, the hash join operators exhibit a high CRF and long bucket chains. Traditional hash join operators, in Binary Compaction, produce many small result chunks. The compacted hash join, utilized in Smart Compaction, effectively decreases the number of result chunks and increases their sizes, offering significant improvements over traditional hash joins.

该查询中的哈希连接运算符具有高压缩率因子和长桶链。传统二分压缩生成的哈希连接会产生大量小结果分块，而智能压缩采用的压缩式哈希连接能有效减少结果分块数量并增大其尺寸，相较传统方案优势显著。

The 6th hash join operator is the only one whose average chunk size decreases when using Smart Compaction, compared to the default Binary Compaction. This anomaly occurs because: 1) this hash join operator has a bucket chain length of one, presenting a filter-style compaction case, as introduced in Section 5.5; and 2) the input chunks to the 6th operator are smaller when Smart Compaction is employed, as the preceding operator compacts fewer chunks before the 6th hash join compared to Binary Compaction.

第6个哈希连接运算符是唯一在使用智能压缩时分块平均尺寸反而减小的特例。这是因为：1)该运算符桶链长度为1，属于第5.5节所述的过滤式压缩场景；2)智能压缩中第6个运算符的输入分块更小，因其前驱运算符产生的待压缩分块少于二分压缩方案。

7.3.2 TPC-H Q9. Most queries in the TPC-H benchmark have joins with relatively short chains. Among these, Query 9 exhibits the longest chain. Query 9 comprises five hash join operators, with only one of them benefiting from Smart Compaction,achieving a speedup of ${1.18} \times$ . For the remaining joins, both Smart and Binary Compaction exhibit similar performance. This result is expected because the TPC-H benchmark's average chain length is only 3.16. Notably, Smart Compaction reduces the number of results chunks to one for all hash joins, demonstrating its effectiveness. Our experiment reveals a significant gap in data distributions between synthetic benchmarks (TPC-H, TPC-DS) and real-data benchmarks (JOB), which substantially impacts the compaction problem.

7.3.2 TPC-H Q9查询。TPC-H基准多数查询的连接链较短，其中Q9包含5个哈希连接运算符，仅1个从智能压缩中获益，加速比达${1.18} \times$。其余连接操作在两种压缩方案下性能相当，这与TPC-H基准平均链长仅3.16的情况相符。值得注意的是，智能压缩将所有哈希连接的结果分块数降至1个，证明了其有效性。实验揭示了合成基准(TPC-H/TPC-DS)与真实数据基准(JOB)在数据分布上的显著差异，这对压缩问题影响重大。

<!-- Media -->

<!-- figureText: No Cpt. Learning Cpt. Logical Cpt. Smart Cpt. Mixed-style Compaction Case 1.2 1.0 0.8 0.6 JOB-17b JOB-17c TPCH-Q9 Join-style Compaction Case 1.8 只 1.4 1.0 0.6 JOB-9d JOB-11d Filter-style Compaction Case Norm. Time 1.2 1.0 0.8 0.6 JOB-2c TPCH-Q3 TPCH-Q19 Compaction Not Beneficial Norm. Time 1.2 1.0 0.8 0.6 TPCH-Q4 TPCH-Q13 TPCH-Q20 -->

<img src="https://cdn.noedgeai.com/01965999-5b72-70be-8442-a080a54269bf_21.jpg?x=238&y=270&w=1092&h=518&r=0"/>

Fig. 20. Relative Significance of Learning and Logical Compaction - We select queries to show various compaction cases. The execution time of each query is normalized by that of Smart Cpt.

图20. 学习机制与逻辑压缩的相对重要性-我们选取不同压缩场景的查询进行展示，各查询执行时间以智能压缩方案为基准进行归一化。

<!-- Media -->

Summary. This case study yields three key conclusions. First, Real-data workloads (JOB) have significantly smaller average chunk sizes than synthetic benchmarks (TPC-H). Second, small chunks are a major latency bottleneck, especially for hash join operations. Third, Smart Compaction outperforms Binary Compaction by reducing data-copying overhead, using adaptive compaction thresholds at runtime, and the compacted vectorized hash join.

摘要。本案例研究得出三个关键结论：首先，真实数据工作负载(JOB)的平均块大小显著小于合成基准测试(TPC-H)；其次，小块是主要的延迟瓶颈，尤其对哈希连接操作影响显著；第三，智能压缩技术通过减少数据复制开销、运行时自适应调整压缩阈值以及采用压缩向量化哈希连接，性能优于二进制压缩方案。

### 7.4 Relative Significance

### 7.4 相对重要性

We then show the relative significance of proposed techniques by categorizing queries into four groups: 1) benefiting only from Logical Compaction; 2) benefiting only from Learning Compaction; 3) benefiting from both, and 4) benefiting from neither. As described in Section 6.7, Logical Compaction is designed for joins while Learning Compaction is mostly effective on filters.

我们通过将查询分为四类来展示所提技术的相对重要性：1)仅受益于逻辑压缩；2)仅受益于学习压缩；3)同时受益于两者；4)均不受益。如第6.7节所述，逻辑压缩专为连接操作设计，而学习压缩主要对过滤操作有效。

Figure 20 shows the results. For example, TPC-H Q19, a filter-style query, has two filters (with selectivities of ${0.05}\%$ and ${14}\%$ . Its join operators produce near-full chunks,and thus Logical Compaction is ineffective. In join-style queries, Smart Compaction performs similarly to Logical Compaction, as the selective joins, not filters, present a performance bottleneck. In mixed-style queries, Smart Compaction significantly outperforms both individual techniques, confirming the need to combine them to handle this case effectively, consistent with our analysis in Section 6.7. Some queries gain no benefit from compaction, due to the absence of selective filters and joins.

图20展示了结果。以TPC-H Q19为例，这个过滤型查询包含两个选择率分别为${0.05}\%$和${14}\%$的过滤器。其连接算子产生接近满块的输出，因此逻辑压缩无效。在连接型查询中，智能压缩表现与逻辑压缩相当，因为性能瓶颈在于选择性连接而非过滤。对于混合型查询，智能压缩显著优于两种独立技术，印证了结合使用以有效处理此类场景的必要性，这与第6.7节的分析一致。部分查询因缺乏选择性过滤和连接而无法从压缩中获益。

## 8 Discussion

## 8 讨论

Most modern analytical database engines today adopt the vectorized execution model (e.g., Click-House [41], DataFusion [23], Photon [4], SnowFlake [10], and Velox [31]). Data chunk compaction is a general problem for these engines. For example, Velox has an open issue on optimizing the performance with small chunks [16]. DataFusion handles this problem by introducing a pre-configured switch to select between Full and No Compaction [30].

当前多数现代分析型数据库引擎采用向量化执行模型（如ClickHouse[41]、DataFusion[23]、Photon[4]、SnowFlake[10]和Velox[31]）。数据块压缩是这些引擎面临的共性问题。例如Velox存在关于优化小块性能的未解决问题[16]，而DataFusion通过预设开关在全压缩与无压缩之间选择来处理该问题[30]。

The vectorized hash join algorithms in many systems $\left\lbrack  {{23},{31},{46}}\right\rbrack$ were derived from Mon-etDB/X100 [5, 49]. Therefore, our proposed Logical Compaction also applies to these implementation variants. Systems such as DataFusion and CockroachDB copy both the probe- and build-side columns from the input chunks to the result chunks when performing the hash join [18, 19]. This is equivalent to the Full Compaction strategy in our paper. On the other hand, DuckDB avoids copying the probe-side columns by including references to the input chunks [20]. Although this approach significantly reduces the memory copy cost, the side-effect (identified in this paper) is that it can easily generate under-full chunks. Logical Compaction solves the chunk compaction problem for vectorized hash joins so that the "zero-copy" approach consistently exhibits performance advantages over Full Compaction.

多数系统的向量化哈希连接算法$\left\lbrack  {{23},{31},{46}}\right\rbrack$源自MonetDB/X100[5,49]。因此我们提出的逻辑压缩同样适用于这些实现变体。DataFusion和CockroachDB等系统在执行哈希连接时会将探测侧和构建侧列从输入块复制到结果块[18,19]，这等同于本文中的全压缩策略。而DuckDB通过引用输入块避免复制探测侧列[20]，虽然大幅降低内存复制成本，但副作用（本文发现）是易产生非满块。逻辑压缩解决了向量化哈希连接的块压缩问题，使"零复制"方法始终展现优于全压缩的性能优势。

The applicability of Logical Compaction is independent of whether the table is partitioned because partitioning (e.g. radix partitioning) happens before executing the vectorized hash join. It is also independent of the hash table types (e.g., chaining vs. open-addressing) because Logical Compaction is carried out on the already gathered matched tuples. Note that if the data chunk implementation uses bitmaps instead of selection vectors (SVs), Logical Compaction must convert the bitmaps to SVs (this can be done efficiently using vectorized instructions) before the compaction.

逻辑压缩的适用性与表是否分区无关，因为分区（如基数分区）发生在执行向量化哈希连接之前。其也与哈希表类型（链式vs.开放寻址）无关，因为逻辑压缩作用于已收集的匹配元组。需注意，若数据块实现使用位图而非选择向量(SVs)，逻辑压缩必须先将位图转换为SVs（可通过向量化指令高效完成）。

## 9 Conclusion

## 9 结论

In this paper, we formalized the chunk compaction problem, which involves balancing data copying costs and interpretation costs in vectorized query execution. We proposed learning compaction, which enables the dynamic adjustment of compaction policies during runtime. Additionally, we introduced logical compaction that can compact data chunks without actual data copying for vectorized hash joins. Our investigation reveals that learning compaction effectively addresses the when to compact challenge, while logical compaction improves how to compact. We integrated both methods into DuckDB and evaluated their performance against JOB, TPC-H, and TPC-DS. The results show that our proposed techniques achieve up to ${63}\%$ performance improvement over the default DuckDB.

本文形式化了块压缩问题——平衡向量化查询执行中数据复制成本与解析成本。我们提出学习压缩实现运行时动态调整压缩策略，并设计逻辑压缩使向量化哈希连接无需实际数据复制即可压缩数据块。研究表明：学习压缩有效解决"何时压缩"的难题，逻辑压缩优化了"如何压缩"。将两种方法集成至DuckDB后，在JOB、TPC-H和TPC-DS上的评估表明，所提技术较DuckDB默认实现最高可获得${63}\%$的性能提升。

## Acknowledgements

## 致谢

This work was supported (in part) by the Shanghai Qi Zhi Institute Innovation Program SQZ202406.

本研究（部分）得到上海期智研究院创新项目SQZ202406资助。

## References

## 参考文献

[1] Daniel J. Abadi, Samuel Madden, and Miguel Ferreira. 2006. Integrating compression and execution in column-oriented database systems. In Proceedings of SIGMOD'16. ACM, 671-682.

[2] Maximilian Bandle, Jana Giceva, and Thomas Neumann. 2021. To Partition, or Not to Partition, That is the Join Question in a Real System. In Proceedings of SIGMOD'21. ACM, 168-180.

[3] Claude Barthels, Gustavo Alonso, Torsten Hoefler, Timo Schneider, and Ingo Müller. 2017. Distributed Join Algorithms on Thousands of Cores. Proceedings of VLDB’17 10, 5 (2017), 517-528. https://doi.org/10.14778/3055540.3055545

[4] Alexander Behm, Shoumik Palkar, Utkarsh Agarwal, Timothy Armstrong, David Cashman, Ankur Dave, Todd Greenstein, Shant Hovsepian, Ryan Johnson, Arvind Sai Krishnan, Paul Leventis, Ala Luszczak, Prashanth Menon, Mostafa Mokhtar, Gene Pang, Sameer Paranjpye, Greg Rahn, Bart Samwel, Tom van Bussel, Herman Van Hovell, Maryann Xue, Reynold Xin, and Matei Zaharia. 2022. Photon: A Fast Query Engine for Lakehouse Systems. In Proceedings of SIGMOD'22. ACM, 2326-2339.

[5] Peter A. Boncz, Marcin Zukowski, and Niels Nes. 2005. MonetDB/X100: Hyper-Pipelining Query Execution. In Proceedings of CIDR'05. www.cidrdb.org, 225-237.

[6] Maximilian Böther, Lawrence Benson, Ana Klimovic, and Tilmann Rabl. 2023. Analyzing Vectorized Hash Tables Across CPU Architectures. Proceedings of VLDB'23 16, 11 (2023), 2755-2768.

[7] Biswapesh Chattopadhyay, Priyam Dutta, Weiran Liu, Ott Tinn, Andrew McCormick, Aniket Mokashi, Paul Harvey, Hector Gonzalez, David Lomax, Sagar Mittal, Roee Ebenstein, Nikita Mikhaylin, Hung-Ching Lee, Xiaoyan Zhao, Tony Xu, Luis Perez, Farhad Shahmohammadi, Tran Bui, Neil Mckay, Selcuk Aya, Vera Lychagina, and Brett Elliott. 2019. Procella: Unifying serving and analytical data at YouTube. Proceedings of VLDB'19 12, 12 (2019), 2022-2034.

[8] The Transaction Processing Council. 2021. TPC-DS Benchmark (Version 3.2.0).

[9] The Transaction Processing Council. 2022. TPC-H Benchmark (Version 3.0.1).

[10] Benoit Dageville, Thierry Cruanes, Marcin Zukowski, Vadim Antonov, Artin Avanes, Jon Bock, Jonathan Claybaugh, Daniel Engovatov, Martin Hentschel, Jiansheng Huang, Allison W. Lee, Ashish Motivala, Abdul Q. Munir, Steven Pelley, Peter Povinec, Greg Rahn, Spyridon Triantafyllis, and Philipp Unterbrunner. 2016. The Snowflake Elastic Data Warehouse. In Proceedings of SIGMOD'16. ACM, 215-226.

[11] Haowen Dong, Chao Zhang, Guoliang Li, and Huanchen Zhang. 2024. Cloud-Native Databases: A Survey. IEEE Trans. Knowl. Data Eng. 36, 12 (2024), 7772-7791.

[12] Goetz Graefe. 1994. Volcano - An Extensible and Parallel Query Evaluation System. IEEE Trans. Knowl. Data Eng. 6, 1 (1994), 120-135.

[13] Philipp M. Grulich, Aljoscha P. Lepping, Dwi Prasetyo Adi Nugroho, Varun Pandey, Bonaventura Del Monte, Steffen Zeuch, and Volker Markl. 2023. Towards Unifying Query Interpretation and Compilation. In Proceedings of CIDR'23.

[14] Andrey Gubichev and Peter A. Boncz. 2014. Parameter Curation for Benchmark Queries. In Proceedings of TPCTC’14 Performance Characterization and Benchmarking. Traditional to Big Data - 6th TPC Technology Conference (Lecture Notes in Computer Science, Vol. 8904). Springer, 113-129.

[15] Tim Gubner and Peter A. Boncz. 2021. Charting the Design Space of Query Execution using VOILA. Proceedings of VLDB'2114, 6 (2021), 1067-1079.

[16] Optimize Operator's Performance When Vector has Low Selectivity. 2023. https://github.com/facebookincubator/ velox/issues/7801

[17] Stratos Idreos, Fabian Groffen, Niels Nes, Stefan Manegold, K. Sjoerd Mullender, and Martin L. Kersten. 2012. MonetDB: Two Decades of Research in Column-oriented Database Architectures. IEEE Data Eng. Bull. 35, 1 (2012), 40-45.

[18] Apache DataFusion Hash Join Implementation. 2024. https://github.com/apache/datafusion/blob/ f7efd2d31adb51a67dc6bfb6d6eae6a525d60482/datafusion/physical-plan/src/joins/utils.rs#L1223

[19] CockroachDB Hash Join Implementation. 2024. https://github.com/cockroachdb/cockroach/blob/ 67e99ebec74c1f6a6dfbf1cc0bca2d255a55f867/pkg/sql/colexec/colexecjoin/hashjoiner.go#L631C3-L631C16

[20] DuckDB Hash Join Implementation. 2024. e2b177b759dbb7cabae0c0afd041bb7de2a9e698/src/execution/join_hashtable.cpp#L928

[21] Timo Kersten, Viktor Leis, Alfons Kemper, Thomas Neumann, Andrew Pavlo, and Peter A. Boncz. 2018. Everything You Always Wanted to Know About Compiled and Vectorized Queries But Were Afraid to Ask. Proceedings of VLDB'18 11, 13 (2018), 2209-2222.

[22] Changkyu Kim, Eric Sedlar, Jatin Chhugani, Tim Kaldewey, Anthony D. Nguyen, Andrea Di Blas, Victor W. Lee, Nadathur Satish, and Pradeep Dubey. 2009. Sort vs. Hash Revisited: Fast Join Implementation on Modern Multi-Core CPUs. Proceedings of VLDB'09 2, 2 (2009), 1378-1389.

[23] Andrew Lamb, Yijie Shen, Daniël Heres, Jayjeet Chakraborty, Mehmet Ozan Kabak, Liang-Chi Hsieh, and Chao Sun. 2024. Apache Arrow DataFusion: A Fast, Embeddable, Modular Analytic Query Engine. In Proceedings of SIGMOD’24. ACM, 5-17.

[24] Harald Lang, Tobias Mühlbauer, Florian Funke, Peter A. Boncz, Thomas Neumann, and Alfons Kemper. 2016. Data Blocks: Hybrid OLTP and OLAP on Compressed Storage using both Vectorization and Compilation. In Proceedings of SIGMOD'16. ACM, 311-326.

[25] Viktor Leis, Peter A. Boncz, Alfons Kemper, and Thomas Neumann. 2014. Morsel-driven parallelism: a NUMA-aware query evaluation framework for the many-core age. In Proceedings of SIGMOD'14. ACM, 743-754.

[26] Viktor Leis, Andrey Gubichev, Atanas Mirchev, Peter A. Boncz, Alfons Kemper, and Thomas Neumann. 2015. How Good Are Query Optimizers, Really? Proceedings of VLDB'15 9, 3 (2015), 204-215.

[27] Sergey Melnik, Andrey Gubarev, Jing Jing Long, Geoffrey Romer, Shiva Shivakumar, Matt Tolton, Theo Vassilakis, Hossein Ahmadi, Dan Delorey, Slava Min, Mosha Pasumansky, and Jeff Shute. 2020. Dremel: A Decade of Interactive SQL Analysis at Web Scale. Proceedings of VLDBV’20 13, 12 (2020), 3461-3472.

[28] Prashanth Menon, Andrew Pavlo, and Todd C. Mowry. 2017. Relaxed Operator Fusion for In-Memory Databases: Making Compilation, Vectorization, and Prefetching Work Together At Last. Proceedings of VLDB'17 11, 1 (2017), 1-13.

[29] Amadou Ngom, Prashanth Menon, Matthew Butrovich, Lin Ma, Wan Shen Lim, Todd C. Mowry, and Andrew Pavlo. 2021. Filter Representation in Vectorized Query Execution. In Proceedings of DaMoN@SIGMOD’21. ACM, 6:1-6:7.

[30] Configuration Settings of DataFusion. 2024. https://datafusion.apache.org/user-guide/configs.html

[31] Pedro Pedreira, Orri Erling, Maria Basmanova, Kevin Wilfong, Laith S. Sakka, Krishna Pai, Wei He, and Biswapesh Chattopadhyay. 2022. Velox: Meta's Unified Execution Engine. Proceedings of VLDB'22 15, 12 (2022), 3372-3384.

[32] Orestis Polychroniou, Arun Raghavan, and Kenneth A. Ross. 2015. Rethinking SIMD Vectorization for In-Memory Databases. In Proceedings of SIGMOD'15. ACM, 1493-1508.

[33] Orestis Polychroniou and Kenneth A. Ross. 2019. Towards Practical Vectorized Analytical Query Engines. In Proceedings of DaMoN@SIGMOD'19. ACM, 10:1-10:7.

[34] Yiming Qiao. 2024. Implement Logical Compaction in Hash Join Operator. https://github.com/duckdb/duckdb/pull/ 14956

[35] Yiming Qiao, Yihan Gao, and Huanchen Zhang. 2024. Blitzcrank: Fast Semantic Compression for In-memory Online Transaction Processing. Proceedings of VLDB'24 17, 10 (2024), 2528-2540.

[36] Mark Raasveldt and Hannes Mühleisen. 2016. Vectorized UDFs in Column-Stores. In Proceedings of SSDBM'16. ACM, ${16} : 1 - {16} : {12}$ .

[37] Mark Raasveldt and Hannes Mühleisen. 2019. DuckDB: an Embeddable Analytical Database. In Proceedings of SIGMOD'19. ACM, 1981-1984.

[38] Bogdan Raducanu, Peter A. Boncz, and Marcin Zukowski. 2013. Micro adaptivity in Vectorwise. In Proceedings of SIGMOD'13. ACM, 1231-1242.

[39] Vijayshankar Raman, Gopi K. Attaluri, Ronald Barber, Naresh Chainani, David Kalmuk, Vincent KulandaiSamy, Jens Leenstra, Sam Lightstone, Shaorong Liu, Guy M. Lohman, Tim Malkemus, René Müller, Ippokratis Pandis, Berni Schiefer, David Sharpe, Richard Sidle, Adam J. Storm, and Liping Zhang. 2013. DB2 with BLU Acceleration: So Much More than Just a Column Store. Proceedings of VLDB'13 6, 11 (2013), 1080-1091.

[40] Stefan Schuh, Xiao Chen, and Jens Dittrich. 2016. An Experimental Comparison of Thirteen Relational Equi-Joins in Main Memory. In Proceedings of SIGMOD'16. ACM, 1961-1976.

[41] Robert Schulze, Tom Schreiber, Ilya Yatsishin, Ryadh Dahimene, and Alexey Milovidov. 2024. ClickHouse - Lightning Fast Analytics for Everyone. Proceedings of VLDB'24 17, 12 (2024), 3731-3744.

[42] IMDb Data Set. 2024. https://www.imdb.com

[43] Ambuj Shatdal, Chander Kant, and Jeffrey F. Naughton. 1994. Cache Conscious Algorithms for Relational Query Processing. In Proceedings of VLDB'94. Morgan Kaufmann, 510-521.

[44] Aleksandrs Slivkins. 2019. Introduction to Multi-Armed Bandits. Found. Trends Mach. Learn. 12, 1-2 (2019), 1-286.

[45] Juliusz Sompolski, Marcin Zukowski, and Peter A. Boncz. 2011. Vectorization vs. compilation in query execution. In Proceedings of DaMoN@SIGMOD'11. ACM, 33-40.

[46] Rebecca Taft, Irfan Sharif, Andrei Matei, Nathan VanBenschoten, Jordan Lewis, Tobias Grieger, Kai Niemi, Andy Woods, Anne Birzin, Raphael Poss, Paul Bardea, Amruta Ranade, Ben Darnell, Bram Gruneir, Justin Jaffray, Lucy Zhang, and Peter Mattis. 2020. CockroachDB: The Resilient Geo-Distributed SQL Database. In Proceedings of SIGMOD’20. ACM, 1493-1509.

[47] Xinyu Zeng, Yulong Hui, Jiahong Shen, Andrew Pavlo, Wes McKinney, and Huanchen Zhang. 2023. An Empirical Evaluation of Columnar Storage Formats. Proceedings of VLDB'24 17, 2 (2023), 148-161.

[48] Junyi Zhao, Huanchen Zhang, and Yihan Gao. 2023. Efficient Query Re-optimization with Judicious Subquery Selections. Proceedings of SIGMOD'23 1, 2 (2023), 185:1-185:26.

[49] Marcin Zukowski et al. 2009. Balancing vectorized query execution with bandwidth-optimized storage. SIKS.

[50] Marcin Zukowski and Peter A. Boncz. 2012. From x100 to Vectorwise Opportunities, challenges and things most researchers do not think about. In Proceedings of SIGMOD'12. ACM, 861-862.