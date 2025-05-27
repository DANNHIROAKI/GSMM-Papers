# Correlation Joins over Time Series Data Streams Utilizing Complementary Dimension Reduction and Transformation

基于互补降维与变换的时间序列数据流相关性连接技术

AMIRREZA ALIZADE NIKOO, University of Zurich, Switzerland MICHAEL H. BÖHLEN, University of Zurich, Switzerland SVEN HELMER, University of Zurich, Switzerland

阿米尔雷扎·阿里扎德·尼库，瑞士苏黎世大学；迈克尔·H·伯伦，瑞士苏黎世大学；斯文·赫尔默，瑞士苏黎世大学

A common analysis task over a stream of time series is to find all pairs of windows whose correlation is above a given threshold. For a large number of streams, doing so naively, i.e., checking the Cartesian product, is too expensive. In essence, finding correlated pairs in a non-naive way boils down to a high-dimensional similarity join in a Euclidean space. While there are similarity join algorithms,such as Quickjoin and $\epsilon$ -kdB tree,they are inefficient for high-dimensional data. We propose Corržoin, short for Correlation Join, that combines a complementary dimension reduction and transformation step with a subsequent double-filtering step. In the first step, we reduce the dimensionality of data stream windows by combining a fast but inaccurate method, Piecewise Aggregate Approximation (PAA), with an accurate and slow one, Singular Value Decomposition (SVD). Not only does SVD compensate for the weaknesses of PAA, it also transforms the data to make the first filter based on bucketing more effective. The second filter, which uses Euclidean distances, reduces the number of false positives before computing exact correlations. Our experiments reveal that in common settings, CorrJoin is an order of magnitude faster than state-of-the-art approaches (up to 20 times faster than Quickjoin).

时间序列流分析中的常见任务是找出所有相关性超过阈值的窗口对。面对海量数据流时，直接计算笛卡尔积的方法代价过高。本质上，高效发现相关对可转化为欧氏空间中的高维相似性连接问题。现有算法如Quickjoin和$\epsilon$-kdB树对高维数据处理效率低下。我们提出CorrJoin（相关性连接）算法，通过结合互补的降维转换步骤与双重过滤机制：首先采用快速但不精确的分段聚合近似(PAA)与精确但耗时的奇异值分解(SVD)相结合进行降维，SVD不仅能弥补PAA缺陷，还能通过数据变换提升基于分桶的首层过滤效率；其次通过欧氏距离过滤减少精确相关性计算前的误判。实验表明在常规场景下，CorrJoin比现有最优方法快一个数量级（较Quickjoin最高提速20倍）。

## CCS Concepts: - Information systems $\rightarrow$ Join algorithms.

## CCS概念体系：- 信息系统 $\rightarrow$ 连接算法

Additional Key Words and Phrases: correlation, join processing, time series, data streams

关键词：相关性、连接处理、时间序列、数据流

## ACM Reference Format:

## ACM文献格式：

AmirReza Alizade Nikoo, Michael H. Böhlen, and Sven Helmer. 2023. Correlation Joins over Time Series Data Streams Utilizing Complementary Dimension Reduction and Transformation. Proc. ACM Manag. Data 1, 4 (SIGMOD), Article 235 (December 2023), 26 pages. https://doi.org/10.1145/3626722

阿米尔雷扎·阿里扎德·尼库，迈克尔·H·伯伦，斯文·赫尔默。2023。基于互补降维与变换的时间序列数据流相关性连接。《ACM数据管理汇刊》1卷4期(SIGMOD)，文章235（2023年12月），26页。https://doi.org/10.1145/3626722

## 1 INTRODUCTION

## 1 引言

Due to the ubiquity of sensors and sensor networks, the analysis of time series data in a timely manner or (near) real-time has gained in importance. Time series data analysis can be found in areas such as running scientific experiments, keeping track of industrial equipment and vehicles, collecting economic statistics, and monitoring the health of patients. An increasingly important application is monitoring the performance of thousands of servers in a data center [44]. There is a trend in this area to move towards observability platforms, such as Prometheus and Dynatrace, which provide more functionality compared to monitoring [37]. These platforms help DevOps engineers in analyzing an incident by supporting the search for root causes. This analysis very often involves investigating time series data (for instance,Prometheus stores its data as time series ${}^{1}$ ) and finding correlations between these time series. For example, keeping track of the latency of a

随着传感器网络的普及，时间序列数据的实时分析愈发重要。其应用场景涵盖科学实验、工业设备监控、经济统计和患者健康监测等领域。数据中心数千台服务器的性能监控成为新兴重点，行业正转向可观测性平台（如Prometheus和Dynatrace），这些平台通过支持根因分析帮助运维工程师处理事故。此类分析通常涉及检查时间序列数据（例如Prometheus以${}^{1}$格式存储数据）并发现序列间相关性。例如当观测到

Authors' addresses: AmirReza Alizade Nikoo, University of Zurich, Zurich, Switzerland, nikoo@ifi.uzh.ch; Michael H. Böhlen, University of Zurich, Zurich, Switzerland, boehlen@ifi.uzh.ch; Sven Helmer, University of Zurich, Zurich, Switzerland, helmer@ifi.uzh.ch.

作者联系方式：阿米尔雷扎·阿里扎德·尼库，瑞士苏黎世大学，nikoo@ifi.uzh.ch；迈克尔·H·伯伦，瑞士苏黎世大学，boehlen@ifi.uzh.ch；斯文·赫尔默，瑞士苏黎世大学，helmer@ifi.uzh.ch。

This work is licensed under a Creative Commons Attribution International 4.0 License. web application we observe several peaks of high latency. Taking a closer look at other time series we find that the latency of the backend database system behaves very similarly, i.e., the two latency time series are highly correlated, indicating an issue with the database system. Now, correlation does not necessarily mean causation, but it helps DevOps engineers to find the cause of an incident much faster. ${}^{2}$ Currently,the analysis is triggered after an incident and then relevant time series data have to be found in log files and compared to each other to find correlated series. With the help of our correlation join, we can keep track of the correlations between time series as the data becomes available, making it possible to provide this information instantly when needed. We envision a system that can provide observability in near real-time.

本作品采用知识共享署名国际4.0许可协议。某网络应用出现高延迟峰值时，通过对比其他时间序列可发现后端数据库系统的延迟曲线高度相似，这种强相关性暗示数据库系统存在问题。虽然相关性不等于因果性，但能极大加速故障定位。当前分析方法需事后从日志中提取相关时间序列进行比对，而我们的相关性连接技术能在数据到达时实时追踪关联性，实现近实时的可观测性系统。

---

<!-- Footnote -->

${}^{1}$ https://prometheus.io/docs/concepts/data_model/

${}^{1}$ https://prometheus.io/docs/concepts/data_model/

<!-- Footnote -->

---

On a technical level, our goal is to efficiently compute correlation joins over time series data streams. Thus,given a data stream consisting of $m$ unbounded time series,we determine all pairs of time series that are locally correlated in terms of the Pearson correlation. Monitoring an unbounded time series is handled by a window of length $n$ that is shifted over each time series and uses the most recent $n$ data points (dimensions) to answer correlation queries. Two windows are considered correlated if their correlation is above a given threshold, and uncorrelated otherwise. Determining correlated pairs boils down to evaluating a self join among the windows of all streams with a join predicate checking for correlation.

在技术层面上，我们的目标是高效计算时间序列数据流上的相关性连接。给定由$m$条无界时间序列组成的数据流时，我们需找出所有在皮尔逊相关系数上具有局部相关性的时间序列对。通过长度为$n$的滑动窗口处理无界时间序列，该窗口利用最新的$n$个数据点（维度）来响应相关性查询。若两个窗口的相关系数超过给定阈值则判定为相关，否则不相关。确定相关对最终转化为在所有流窗口间执行自连接操作，并通过检查相关性的连接谓词进行筛选。

Partition- or hash-based joins are among the fastest join algorithms because they eliminate many pairs from consideration, and only look at a small subset of the Cartesian product containing potential join partners. When applying these kinds of joins for finding pairs of correlated windows in data streams, we face two challenges. First, the join predicate is not based on equality, which makes it difficult to apply hash-based algorithms. Second, we are dealing with high-dimensional data, as typical data stream windows contain hundreds or thousands of values. In order to tackle the first challenge, we map the Pearson correlation to the Euclidean distance and determine pairs of windows with a distance less or equal to $\epsilon$ (which is the distance threshold derived from the correlation threshold (see Section 2.1)). By assigning the windows to buckets of width $\epsilon$ ,we eliminate all pairs of windows from non-neighboring buckets. However, we cannot apply the bucketing approach directly as the high dimensionality of the data would lead to buckets with a large number of neighbors (this number increases exponentially with the number of dimensions). At the heart of our novel approach CorrJoin, short for Correlation Join, is a complementary dimension reduction and transformation. Using an innovative combination of Piecewise Aggregate Approximation (PAA), an efficient but not very accurate dimensionality reduction technique, with Singular Value Decomposition (SVD), an accurate but slow method, we are able to efficiently leverage bucketing for the computation of correlation joins.

基于分区或哈希的连接是最快的连接算法之一，因其能排除大量无效对，仅需检查笛卡尔积中包含潜在连接对象的微小子集。将此类连接应用于数据流中相关窗口对的发现时，我们面临双重挑战：其一，连接谓词不基于等值条件，导致哈希算法难以直接应用；其二，数据具有高维特性，典型数据流窗口包含数百至数千个数值。针对首个挑战，我们将皮尔逊相关性映射为欧氏距离，并筛选距离小于等于$\epsilon$的窗口对（该距离阈值由相关系数阈值推导得出，见2.1节）。通过将窗口分配至宽度为$\epsilon$的桶中，可排除非相邻桶的所有窗口对。然而直接应用分桶策略会因数据高维性导致邻桶数量激增（该数量随维度呈指数增长）。我们提出的CorrJoin（相关性连接）方法核心在于结合降维与变换技术：创新性地整合分段聚合近似（PAA，高效但精度一般的降维方法）与奇异值分解（SVD，精确但计算昂贵的方法），从而高效实现相关性连接的分桶计算。

In the following, we briefly describe the steps of our algorithm (see Figure 2 for a visualization of its overall structure). The first step of CorrJoin is responsible for preprocessing the input time series windows by reducing their dimensionality via PAA, which is able to do this very efficiently while retaining aggregated information. We actually produce two sets of dimensionality-reduced windows. The windows in the first set are reduced to ${k}_{s}$ dimensions and are fed into SVD,while the windows in the second set are reduced to ${k}_{e}$ dimensions and are used in a later filter step involving the Euclidean distance. SVD prepares the data for bucketing by transforming the dimensions and selecting the ${k}_{b}$ dimensions that best spread the data (i.e.,have the highest variances). The second step of CorrJoin utilizes double-filtering, passing the data from step one through two filters. Considering a quadratic number of pairs, i.e., all pairs of the Cartesian product, is clearly inefficient. So,the first filter,called bucketing filter,partitions the data into buckets of width $\epsilon$ and only compares windows located in the same or neighboring buckets. The earlier mentioned SVD transformation ensures that the data is spread across buckets in the most effective way. Since the bucketing filter is not perfect, i.e., the output includes pairs that are not correlated, the pairs that pass the bucketing filter are passed on to a second filter, the Euclidean distance filter, to check if the Euclidean distance is below distance threshold $\epsilon$ using the windows with ${k}_{e}$ dimensions. This further decreases the number of pairs for which the exact correlation has to be computed.

下文简要描述算法步骤（整体结构见图2可视化）。CorrJoin第一步通过PAA降维预处理输入时间序列窗口，该方法能高效保留聚合信息。实际生成两组降维窗口：第一组降至${k}_{s}$维输入SVD处理，第二组降至${k}_{e}$维用于后续欧氏距离过滤。SVD通过变换维度并选取${k}_{b}$个数据分布最分散的维度（即方差最大者）为分桶做准备。第二步采用双重过滤机制：首轮分桶过滤器将数据按宽度$\epsilon$分桶，仅比较同桶或邻桶窗口——经SVD变换确保数据最优分布；次轮欧氏距离过滤器用${k}_{e}$维窗口验证距离是否小于阈值$\epsilon$，从而进一步减少需精确计算相关性的窗口对数量。

---

<!-- Footnote -->

${}^{2}$ https://cloud.redhat.com/blog/observability-superpower-correlation

${}^{2}$ https://cloud.redhat.com/blog/observability-superpower-correlation

<!-- Footnote -->

---

In summary, CorrJoin strikes a good balance between performance (from PAA) and accuracy (from SVD). PAA-reduced windows are essential to speed up the SVD transformation and the Euclidean distance filter. SVD in turn is needed to make the bucketing work efficiently. In particular, our contributions are the following:

总之，CorrJoin在PAA的高效性与SVD的精确性间取得平衡：PAA降维窗口加速了SVD变换与欧氏距离过滤，而SVD则使分桶策略高效可行。具体贡献如下：

- We show how dimensionality reduction, dimensionality transformation, bucket filtering, and distance filtering must be combined to get a robust reduce-filter-and-refine approach for time series data that scales not only with the length but also with the number of time series. While each of these techniques is well understood, none of them leads to an efficient algorithm if used on its own. For a correlation join, these methods have to be combined in novel and very specific ways to reach a high level of efficiency.

- 我们阐明如何结合降维、维度变换、分桶过滤与距离过滤，构建适用于时间序列数据的"降维-过滤-精炼"框架，其扩展性不仅关乎序列长度更涉及序列数量。这些技术单独使用时均无法实现高效算法，而相关性连接要求以新颖且特定的方式整合它们才能达成极高效率。

- We introduce a novel approach for computing threshold-based correlations between windows of time series by combining Piecewise Aggregate Approximation (PAA), a fast but inaccurate reduction technique, with Singular Value Decomposition (SVD), a slow but accurate transformation and reduction technique.

- 我们提出了一种新颖方法，通过将分段聚合近似（PAA，一种快速但不精确的降维技术）与奇异值分解（SVD，一种缓慢但精确的变换和降维技术）相结合，计算时间序列窗口间基于阈值的相关性。

- We decrease the number of pairwise comparisons by a bucketing filter that uses the most important dimensions for partitioning the windows into buckets. The bucketing guarantees that only windows in the same or in neighboring buckets must be compared. We present the first approach that leverages complementary dimension reductions and transformations to improve the efficiency of a bucketing filter.

- 我们采用分桶过滤器，利用最重要的维度将窗口划分到不同桶中，从而减少成对比较次数。该分桶机制确保只需比较同一或相邻桶中的窗口。我们首次提出利用互补的降维和变换技术来提升分桶过滤器的效率。

- Using synthetic and real datasets, we conduct an extensive set of experiments to evaluate the performance of CorrJoin and compare it to the state-of-the-art approaches. Our experiments reveal that the proposed algorithm is at least an order of magnitude faster than the state-of-the-art approaches,and reduces the number of comparisons by at least ${80}\%$ for widely used ranges of correlation thresholds.

- 基于合成和真实数据集，我们进行了大量实验评估CorrJoin性能，并与前沿方法对比。实验表明，在广泛使用的相关性阈值范围内，该算法比现有技术快至少一个数量级，并将比较次数减少至少${80}\%$。

## 2 DEFINITIONS AND TERMINOLOGY

## 2 定义与术语

We start out by defining the notation and terminology used in this paper. Throughout, we consider a dataset with $m$ time series.

首先定义本文使用的符号与术语。我们始终考虑包含$m$条时间序列的数据集。

Definition 2.1. A time series ${x}_{p} = \left\{  {{x}_{p}\left\lbrack  1\right\rbrack  ,{x}_{p}\left\lbrack  2\right\rbrack  ,{x}_{p}\left\lbrack  3\right\rbrack  ,\ldots }\right\}  ,1 \leq  p \leq  m$ ,is an unbounded sequence of data points that are equally spaced in time. Each data point is a real number.

定义2.1 时间序列${x}_{p} = \left\{  {{x}_{p}\left\lbrack  1\right\rbrack  ,{x}_{p}\left\lbrack  2\right\rbrack  ,{x}_{p}\left\lbrack  3\right\rbrack  ,\ldots }\right\}  ,1 \leq  p \leq  m$是时间等间隔分布的无限数据点序列，每个数据点为实数。

Definition 2.2. A window ${w}_{p}\left\lbrack  {i + 1..i + n}\right\rbrack$ of a time series ${x}_{p}$ is a continuous subsequence of ${x}_{p}$ of length $n$ with starting time $i + 1$ ,i.e., ${w}_{p}\left\lbrack  {i + 1..i + n}\right\rbrack   = \left\{  {{x}_{p}\left\lbrack  {i + 1}\right\rbrack  ,{x}_{p}\left\lbrack  {i + 2}\right\rbrack  ,\ldots ,{x}_{p}\left\lbrack  {i + n}\right\rbrack  }\right\}$ .

定义2.2 时间序列${x}_{p}$的窗口${w}_{p}\left\lbrack  {i + 1..i + n}\right\rbrack$是指长度为$n$、起始时间为$i + 1$的连续子序列，即${w}_{p}\left\lbrack  {i + 1..i + n}\right\rbrack   = \left\{  {{x}_{p}\left\lbrack  {i + 1}\right\rbrack  ,{x}_{p}\left\lbrack  {i + 2}\right\rbrack  ,\ldots ,{x}_{p}\left\lbrack  {i + n}\right\rbrack  }\right\}$。

As new data arrives windows are shifted by $h$ newly arriving data points,called the stride. We write ${w}_{p}\left\lbrack  {{\alpha h} + 1..{\alpha h} + n}\right\rbrack$ to refer to window $\alpha$ of time series ${x}_{p}$ with stride $h$ . A window of length $n$ is an $n$ -dimensional object,in which each data point represents a dimension.

随着新数据到达，窗口会以$h$个新数据点（称为步长）为单位滑动。记${w}_{p}\left\lbrack  {{\alpha h} + 1..{\alpha h} + n}\right\rbrack$表示时间序列${x}_{p}$采用步长$h$的第$\alpha$个窗口。长度为$n$的窗口是$n$维对象，每个数据点代表一个维度。

We abbreviate ${w}_{p}\left\lbrack  {i + 1..i + n}\right\rbrack$ and ${w}_{q}\left\lbrack  {i + 1..i + n}\right\rbrack$ by $x = \{ x\left\lbrack  1\right\rbrack  ,x\left\lbrack  2\right\rbrack  ,\ldots ,x\left\lbrack  n\right\rbrack  \}$ and $y =$ $\{ y\left\lbrack  1\right\rbrack  ,y\left\lbrack  2\right\rbrack  ,\ldots ,y\left\lbrack  n\right\rbrack  \}$ with averages $\bar{x}$ and $\bar{y}$ ,respectively. The Pearson correlation is defined for equal-length windows, $x$ and $y$ :

将${w}_{p}\left\lbrack  {i + 1..i + n}\right\rbrack$和${w}_{q}\left\lbrack  {i + 1..i + n}\right\rbrack$简记为$x = \{ x\left\lbrack  1\right\rbrack  ,x\left\lbrack  2\right\rbrack  ,\ldots ,x\left\lbrack  n\right\rbrack  \}$和$y =$$\{ y\left\lbrack  1\right\rbrack  ,y\left\lbrack  2\right\rbrack  ,\ldots ,y\left\lbrack  n\right\rbrack  \}$，其均值分别为$\bar{x}$和$\bar{y}$。皮尔逊相关性定义于等长窗口$x$与$y$：

$$
\operatorname{corr}\left( {x,y}\right)  = \frac{\mathop{\sum }\limits_{{i = 1}}^{n}\left( {x\left\lbrack  i\right\rbrack   - \bar{x}}\right) \left( {y\left\lbrack  i\right\rbrack   - \bar{y}}\right) }{\sqrt{\mathop{\sum }\limits_{{i = 1}}^{n}{\left( x\left\lbrack  i\right\rbrack   - \bar{x}\right) }^{2}}\sqrt{\mathop{\sum }\limits_{{i = 1}}^{n}{\left( y\left\lbrack  i\right\rbrack   - \bar{y}\right) }^{2}}} \tag{1}
$$

It is well known that the Pearson correlation can be computed incrementally over the windows [4]. We refer to this approach as incremental Pearson (IncP),which computes ${s}_{1} = \mathop{\sum }\limits_{{i = 1}}^{n}x\left\lbrack  i\right\rbrack  ,{s}_{2} =$ $\mathop{\sum }\limits_{{i = 1}}^{n}x{\left\lbrack  i\right\rbrack  }^{2},{s}_{3} = \mathop{\sum }\limits_{{i = 1}}^{n}y\left\lbrack  i\right\rbrack  ,{s}_{4} = \mathop{\sum }\limits_{{i = 1}}^{n}y{\left\lbrack  i\right\rbrack  }^{2},{s}_{5} = \mathop{\sum }\limits_{{i = 1}}^{n}x\left\lbrack  i\right\rbrack   \cdot  y\left\lbrack  i\right\rbrack$ ,and assembles these sums to

众所周知，皮尔逊相关系数可以在滑动窗口上增量计算[4]。我们将这种方法称为增量皮尔逊(IncP)，它计算${s}_{1} = \mathop{\sum }\limits_{{i = 1}}^{n}x\left\lbrack  i\right\rbrack  ,{s}_{2} =$$\mathop{\sum }\limits_{{i = 1}}^{n}x{\left\lbrack  i\right\rbrack  }^{2},{s}_{3} = \mathop{\sum }\limits_{{i = 1}}^{n}y\left\lbrack  i\right\rbrack  ,{s}_{4} = \mathop{\sum }\limits_{{i = 1}}^{n}y{\left\lbrack  i\right\rbrack  }^{2},{s}_{5} = \mathop{\sum }\limits_{{i = 1}}^{n}x\left\lbrack  i\right\rbrack   \cdot  y\left\lbrack  i\right\rbrack$，并将这些求和结果组合成

$$
\operatorname{corr}\left( {x,y}\right)  = \frac{n{s}_{5} - {s}_{1}{s}_{3}}{\sqrt{\left( {n{s}_{2} - {s}_{1}^{2}}\right) \left( {n{s}_{4} - {s}_{3}^{2}}\right) }} \tag{2}
$$

Ideally, we only calculate the exact correlation (according to Equation (1) or (2)) for a small number of potentially correlated pairs. Dimensionality reduction techniques achieve this by reducing the length of the windows and checking the correlation strength in a lower dimensional space, which can be done more cheaply. We use Piecewise Aggregate Approximation (PAA) as a dimensionality reduction technique since it is one of the fastest methods. ${}^{3}$ It guarantees that no correlated pairs are missed, i.e., there are no false negatives. False positives, i.e., non-qualifying pairs in the candidate set are eliminated afterward. Although PAA is very efficient, it is not very accurate, i.e., it produces a substantial number of false positives, especially if we drastically reduce the number of dimensions to very low values. In order to compensate for this, we employ Singular Value Decomposition (SVD) in combination with PAA. In the following, we briefly describe the two methods.

理想情况下，我们只需为少量潜在相关对计算精确相关系数（根据公式(1)或(2)）。降维技术通过缩短窗口长度并在低维空间中检查相关强度来实现这一目标，这种操作计算成本更低。我们采用分段聚合近似(PAA)作为降维技术，因其是最快速的方法之一。${}^{3}$该方法保证不会遗漏任何相关对，即不存在假阴性。假阳性（即候选集中不符合条件的配对）会在后续环节被剔除。虽然PAA效率极高，但其准确性欠佳，特别是在将维度急剧缩减至极低值时会产生大量假阳性。为弥补这一缺陷，我们结合使用奇异值分解(SVD)与PAA。下文将简要介绍这两种方法。

### 2.1 Piecewise Aggregate Approximation (PAA)

### 2.1 分段聚合近似(PAA)

Piecewise Aggregate Approximation (PAA), also known as Piecewise Constant Approximation (PCA) [34,49],reduces the dimensionality of a window from $n$ to $k$ dimensions by dividing it into $k$ equi-length segments with the segment length equal to $\frac{n}{k}\left\lbrack  {{24},{56}}\right\rbrack$ . The reduced representation is a vector containing the mean values of the segments.

分段聚合近似(PAA)，亦称分段常数近似(PCA)[34,49]，通过将窗口划分为$k$个等长子段（每段长度为$\frac{n}{k}\left\lbrack  {{24},{56}}\right\rbrack$），将窗口维度从$n$降至$k$。降维后的表示形式是包含各子段均值的向量。

Example $\left( {k = 4}\right)$ :

示例$\left( {k = 4}\right)$：

- $x = \{ 6,2,3,1,4,5,8,7\}$

- $X = {PAA}\left( {x,4}\right)  = \{ 4,2,{4.5},{7.5}\}$

The time complexity for reducing the dimensionality of a window using PAA is $O\left( n\right)$ [30]. Whenever $h$ new data points become available,the mean value vector is updated at cost $O\left( {h + k}\right)$ .

使用PAA进行窗口降维的时间复杂度为$O\left( n\right)$[30]。每当$h$个新数据点可用时，均值向量的更新代价为$O\left( {h + k}\right)$。

Let $\widehat{x} = \{ \widehat{x}\left\lbrack  1\right\rbrack  ,\widehat{x}\left\lbrack  2\right\rbrack  ,\ldots ,\widehat{x}\left\lbrack  n\right\rbrack  \}$ be the normalization of window $x$ such that $\widehat{x}\left\lbrack  i\right\rbrack   = (x\left\lbrack  i\right\rbrack   -$ $\bar{x})/\sqrt{\mathop{\sum }\limits_{{i = 1}}^{n}{\left( x\left\lbrack  i\right\rbrack   - \bar{x}\right) }^{2}}$ where $\bar{x}$ is the average of $x$ . The Euclidean distance $d$ between two windows is the square root of the sum of the squares of the differences between the corresponding data points of the windows:

设$\widehat{x} = \{ \widehat{x}\left\lbrack  1\right\rbrack  ,\widehat{x}\left\lbrack  2\right\rbrack  ,\ldots ,\widehat{x}\left\lbrack  n\right\rbrack  \}$为窗口$x$的归一化结果，满足$\widehat{x}\left\lbrack  i\right\rbrack   = (x\left\lbrack  i\right\rbrack   -$$\bar{x})/\sqrt{\mathop{\sum }\limits_{{i = 1}}^{n}{\left( x\left\lbrack  i\right\rbrack   - \bar{x}\right) }^{2}}$，其中$\bar{x}$表示$x$的平均值。两个窗口间的欧氏距离$d$是其对应数据点差值平方和的平方根：

$$
d\left( {x,y}\right)  = \sqrt{\mathop{\sum }\limits_{{i = 1}}^{n}{\left( x\left\lbrack  i\right\rbrack   - y\left\lbrack  i\right\rbrack  \right) }^{2}} \tag{3}
$$

The Pearson correlation corr can be expressed in terms of the Euclidean distance $d$ [58],[43]:

皮尔逊相关系数corr可通过欧氏距离$d$[58][43]表示为：

$$
\operatorname{corr}\left( {x,y}\right)  = 1 - \frac{1}{2}{d}^{2}\left( {\widehat{x},\widehat{y}}\right)  \tag{4}
$$

The higher the correlation between two pairs is, the smaller their distance becomes, as according to Equation 4 the correlation and the distance are inversely related. It has been shown that the Euclidean distance between windows $X$ and $Y$ whose dimensions have been reduced with PAA lower bounds the distance between the original windows $x$ and $y$ : this guarantees that there are no false negatives (but we still need to remove false positives afterward). More precisely, $d\left( {X,Y}\right)  \leq  \sqrt{k/n} \cdot  d\left( {x,y}\right) \left\lbrack  {{24},{56}}\right\rbrack$ . Thus,assuming the PAA-representations of the normalized windows are denoted by $\widehat{X}$ and $\widehat{Y}$ ,we get $d\left( {\widehat{X},\widehat{Y}}\right)  \leq  \sqrt{k/n} \cdot  d\left( {\widehat{x},\widehat{y}}\right)$ . According to this property and Equation 4,we determine if $\operatorname{corr}\left( {x,y}\right)  \geq  T$ by checking $d\left( {\widehat{X},\widehat{Y}}\right)  \leq  \epsilon$ where $\epsilon  = \sqrt{{2k}\left( {1 - T}\right) /n}$ ,since

两对数据间的相关性越高，其距离就越小，因为根据公式4，相关性与距离呈反比关系。研究表明，经过PAA降维处理的窗口$X$与$Y$之间的欧氏距离，其下限等于原始窗口$x$与$y$之间的实际距离：这保证了不会出现假阴性情况（但仍需后续消除假阳性）。更准确地说，$d\left( {X,Y}\right)  \leq  \sqrt{k/n} \cdot  d\left( {x,y}\right) \left\lbrack  {{24},{56}}\right\rbrack$。因此，假设归一化窗口的PAA表示记为$\widehat{X}$和$\widehat{Y}$，我们得到$d\left( {\widehat{X},\widehat{Y}}\right)  \leq  \sqrt{k/n} \cdot  d\left( {\widehat{x},\widehat{y}}\right)$。根据该性质与公式4，我们通过检验$d\left( {\widehat{X},\widehat{Y}}\right)  \leq  \epsilon$（其中$\epsilon  = \sqrt{{2k}\left( {1 - T}\right) /n}$）来判断$\operatorname{corr}\left( {x,y}\right)  \geq  T$，因为

$$
\operatorname{corr}\left( {x,y}\right)  \geq  T \Rightarrow  1 - 1/2{d}^{2}\left( {\widehat{x},\widehat{y}}\right)  \geq  T \Rightarrow  d\left( {\widehat{x},\widehat{y}}\right)  \leq  \sqrt{2\left( {1 - T}\right) }
$$

---

<!-- Footnote -->

${}^{3}$ Also,Keogh et al. [24] have shown that there is no big difference between various dimensionality reduction techniques for time series, but that PAA has a slight edge over the other methods.

${}^{3}$此外，Keogh等人[24]已证明，在时间序列数据降维技术中，各种方法差异不大，但PAA略优于其他方法。

<!-- Footnote -->

---

Thus, $d\left( {\widehat{X},\widehat{Y}}\right)  \leq  \sqrt{k/n} \cdot  d\left( {\widehat{x},\widehat{y}}\right)  \leq  \sqrt{k/n} \cdot  \sqrt{2\left( {1 - T}\right) }$ .

因此，$d\left( {\widehat{X},\widehat{Y}}\right)  \leq  \sqrt{k/n} \cdot  d\left( {\widehat{x},\widehat{y}}\right)  \leq  \sqrt{k/n} \cdot  \sqrt{2\left( {1 - T}\right) }$。

### 2.2 Singular Value Decomposition (SVD)

### 2.2 奇异值分解（SVD）

Singular Value Decomposition (SVD) is a matrix decomposition technique that decomposes a matrix $M$ into three matrices $U,D,{V}^{\top }$ (transpose of $V$ ) such that $M = {UD}{V}^{\top }\left\lbrack  {{25},{28},{51}}\right\rbrack$ . The matrices are defined as follows:

奇异值分解（SVD）是一种矩阵分解技术，将矩阵$M$分解为三个矩阵$U,D,{V}^{\top }$（$V$的转置），使得$M = {UD}{V}^{\top }\left\lbrack  {{25},{28},{51}}\right\rbrack$。这些矩阵定义如下：

- Matrix $M$ is an $m \times  n$ matrix that consists of $mn$ -dimensional windows.

- 矩阵$M$是由$mn$维窗口构成的$m \times  n$矩阵。

- Matrix $U$ is an $m \times  p$ matrix where $p = \min \left( {m,n}\right)$ . The columns of $U$ are the orthonormal eigenvectors of $M{M}^{\top }$ .

- 矩阵$U$是$m \times  p$矩阵，其中$p = \min \left( {m,n}\right)$。该矩阵的列向量是$M{M}^{\top }$的标准正交特征向量。

- Matrix $D$ is a $p \times  n$ diagonal matrix with nonnegative elements ${\sigma }_{1},{\sigma }_{2},\ldots ,{\sigma }_{n}$ along its diagonal. These elements are the square roots of the eigenvalues of ${M}^{\top }M$ ,and are ranked in decreasing order such that ${\sigma }_{1} \geq  {\sigma }_{2} \geq  \ldots , \geq  {\sigma }_{n} \geq  0$ .

- 矩阵$D$是$p \times  n$对角矩阵，其对角线元素为非负值${\sigma }_{1},{\sigma }_{2},\ldots ,{\sigma }_{n}$。这些元素是${M}^{\top }M$特征值的平方根，并按降序排列，满足${\sigma }_{1} \geq  {\sigma }_{2} \geq  \ldots , \geq  {\sigma }_{n} \geq  0$。

- Matrix $V$ is an $n \times  n$ matrix whose columns are the orthonormal eigenvectors of ${M}^{\top }M$ .

- 矩阵$V$是$n \times  n$矩阵，其列向量为${M}^{\top }M$的标准正交特征向量。

As an example,consider three windows: $a = \{ 3,1\} ,b = \{  - 1,2\}$ and $c = \{ 2,4\}$ . Then,we have

例如，考虑三个窗口：$a = \{ 3,1\} ,b = \{  - 1,2\}$和$c = \{ 2,4\}$。此时可得

$$
\begin{array}{l} a \leftarrow  \left\lbrack  \begin{matrix} 3 & 1 \\   - 1 & 2 \\  0 & 0 \end{matrix}\right\rbrack   = \left\lbrack  \begin{matrix} 0\left( {3 \times  2}\right) & \\  {0.5} &  - {0.7} \\  0 & {0.2} \end{matrix}\right\rbrack   \times  \left\lbrack  \begin{matrix} 0\left( {3 \times  2}\right) & \\  {0.5} & {0.7} \\  0 & {0.2} \end{matrix}\right\rbrack   \times  \left\lbrack  \begin{matrix} 0\left( {2 \times  2}\right) \\  0\left( {2 \times  2}\right) \\  0\left( {2 \times  2}\right)  \end{matrix}\right\rbrack  \\  \end{array}
$$

The product ${UD}$ contains a set of windows that are rotated from the original windows in matrix $M$ . As they are merely rotated,their Euclidean distances are preserved. The dimensionality reduction of the windows from $n$ to $k$ is performed by discarding (or zeroing-out) the $n - k$ smallest ${\sigma }_{i}$ from matrix $D$ (yielding the truncated version ${D}_{k}$ ) and also discarding the $n - k$ corresponding entries in matrix $U$ (yielding the truncated version ${U}_{k}$ ). The product ${U}_{k}{D}_{k}$ consists of $k$ -dimensional windows in the reduced dimensionality space.

该产品${UD}$包含一组从矩阵$M$中的原始窗口旋转而来的窗口。由于仅是旋转操作，它们的欧几里得距离得以保留。通过舍弃（或置零）矩阵$D$中$n - k$个最小的${\sigma }_{i}$（生成截断版本${D}_{k}$），并同时舍弃矩阵$U$中对应的$n - k$个条目（生成截断版本${U}_{k}$），实现了窗口从$n$到$k$维度的降维。最终产品${U}_{k}{D}_{k}$由降维空间中的$k$维窗口构成。

SVD must examine the entire dataset and collects the $m$ windows of all series in a matrix $M$ . SVD has a computational complexity of $O\left( {m\left( {{np} + {k}^{2}}\right) }\right)$ for computing matrices $U$ and $D$ and reducing the dimensionality of windows.

奇异值分解(SVD)需要分析整个数据集，并将所有序列的$m$个窗口汇集到矩阵$M$中。SVD在计算矩阵$U$和$D$以及降低窗口维度时，具有$O\left( {m\left( {{np} + {k}^{2}}\right) }\right)$的计算复杂度。

## 3 RELATED WORK

## 3 相关工作

We first look at related work that uses dimensionality reduction to implement approaches for lowering the costs of distance (or correlation) computations. These approaches do not decrease the number of pairwise comparisons, they only speed up the distance computations.

我们首先回顾利用降维技术来降低距离（或相关性）计算成本的相关研究。这些方法并未减少配对比较的数量，仅加速了距离计算过程。

Agrawal et al. [2] authored a seminal work about efficiently comparing two time series at scale. They measure the similarity of two sequences using the Euclidean distance, reducing the dimensionality of the time series via DFT. Additionally, they demonstrate how to map the Pearson correlation to the Euclidean distance (with the help of Parseval's theorem [47]), making their approach applicable for finding highly correlated time series. The idea of speeding up the correlation computation between time series by reducing their dimensionality can also be found in the following approaches: Popivanov and Miller [41] use wavelet transformation instead of DFT, Chen et al. [8] propose the use of linear approximation, and Qiu et al. [42] combine DFT with a neural network. Mueen et al. [36] show how to improve the performance by optimizing the I/O cost of the DFT-based approach. Xu et al. propose TSUBASA [53], which focuses on storing historical time series data in a preprocessed form to accelerate the search for correlated pairs later on. The main differences to our approach are that the data is queried an arbitrary number of times (which makes the preprocessing worthwhile) and that all pairs are compared to each other during query evaluation. While TSUBASA can be applied in an online setting, it cannot play out its strength in such an environment and behaves similarly to the naive IncP approach. Several other methods have been used in the past to reduce the dimensionality of windows and use a filter-and-refine approach. These range from Piecewise Aggregate Approximation (PAA) [34, 49], Adaptive Piecewise Constant Approximation (APCA) [7, 14, 21], Multiscale Segment Mean (MSM) [32], Discrete Haar Wavelet Transform (DHWT) [19, 26], Piecewise Linear Approximation (PLA) [8, 23], Singular Value Decomposition (SVD) [28, 51], all the way to Chebyshev Polynomials [5]. Additionally, there are approaches that approximate the answer, i.e., they produce false negatives. There is an approach based on neural networks [42], one on hierarchical domain transformation [29], another DFT-based approach [58], and two computing dot products with random vectors [9, 54].

Agrawal等人[2]发表了关于高效大规模比较时间序列的开创性研究。他们采用欧几里得距离衡量序列相似度，并通过离散傅里叶变换(DFT)实现时间序列降维。此外，他们演示了如何借助帕塞瓦尔定理[47]将皮尔逊相关性映射到欧几里得距离，使其方法适用于高相关时间序列发现。以下研究也体现了通过降维加速时间序列相关性计算的思路：Popivanov和Miller[41]使用小波变换替代DFT，Chen等人[8]提出线性近似法，Qiu等人[42]将DFT与神经网络结合。Mueen等人[36]阐述了如何通过优化基于DFT方法的I/O成本来提升性能。Xu等人提出的TSUBASA[53]专注于以预处理形式存储历史时间序列数据，以加速后续相关对搜索。与我们的方法主要区别在于：数据会被任意次数查询（这使得预处理具有价值），且查询评估期间需对所有配对进行比较。虽然TSUBASA可应用于在线场景，但其优势无法在此类环境中充分发挥，表现与朴素IncP方法类似。历史上还出现过多种采用"过滤-优化"策略的窗口降维方法，包括分段聚合近似(PAA)[34,49]、自适应分段常数近似(APCA)[7,14,21]、多尺度分段均值(MSM)[32]、离散哈尔小波变换(DHWT)[19,26]、分段线性近似(PLA)[8,23]、奇异值分解(SVD)[28,51]，直至切比雪夫多项式[5]。另有若干近似解法会产生假阴性结果，包括基于神经网络的方法[42]、分层域变换法[29]、另一种DFT方法[58]，以及两种采用随机向量点积计算的方法[9,54]。

Next, we look at approaches that propose solutions to reduce the number of pairs that have to be compared. These approaches avoid checking a quadratic number of pairs but their performance deteriorates once we have significant costs for comparing two windows, which is the case for time series data. Generally, we have the following alternatives to avoid all pairwise comparisons: index-based approaches, in which matching objects are looked up via an index, sort-based approaches, in which objects are first sorted on a dimension and then scanned in an interleaved fashion, and partitioning/hash-based approaches, in which objects are partitioned and only compatible partitions are considered. From these alternatives, we do not further pursue sort-based approaches, as they have been shown to be outperformed by partitioning algorithms [20].

接下来我们考察旨在减少待比较配对数量的解决方案。这些方法避免了二次方数量的配对检查，但其性能会因窗口比较成本过高（时间序列数据的典型情况）而下降。通常我们有以下替代方案来避免全配对比较：基于索引的方法（通过索引查找匹配对象）、基于排序的方法（先在某一维度排序对象再交错扫描）、基于分区/哈希的方法（分区后仅考虑兼容分区）。其中我们不继续研究基于排序的方法，因已有研究表明分区算法性能更优[20]。

We first look at index structures that support queries searching for all time series that are similar to a given query time series [6, 11, 15, 27, 33, 39, 40, 46, 50, 59]. The two major issues with these indexes are that they are expensive to construct and they struggle with high-dimensional data $\left\lbrack  {{12},{13},{17},{18},{52}}\right\rbrack$ . For our purposes,we need light-weight methods allowing us to build the indexes efficiently on the fly, as we throw them away after the current batch of windows has been processed and need to build new ones for the next batch. Additionally, we are dealing with high-dimensional windows,a use case indexes generally struggle with. The only suitable index we found is the $\epsilon$ -kdB tree by Shim et al. [46], with which we compare our approach.

我们首先考察支持查询与给定时间序列相似的所有时间序列的索引结构[6,11,15,27,33,39,40,46,50,59]。这类索引存在两大问题：构建成本高昂且难以处理高维数据$\left\lbrack  {{12},{13},{17},{18},{52}}\right\rbrack$。针对我们的需求，需要轻量级方法以便动态高效构建索引——当前批次的窗口处理完毕后会丢弃这些索引，并为下一批次构建新索引。此外，我们处理的是高维窗口场景，这正是传统索引技术普遍面临的难题。我们发现唯一适用的索引是Shim等人提出的$\epsilon$-kdB树[46]，本文将与之进行对比。

Next, we discuss partition-based approaches. Jacox and Samet [20] apply the idea of Quicksort to find the most similar data objects, creating their Quickjoin algorithm. They use a reference point in space and the distance of the points in the dataset to this reference point to partition the data. This approach is used recursively to divide the dataset into smaller and smaller subsets. Once these subsets reach a size that is small enough, finding potential matches is started inside the subsets and in the neighboring subsets. This approach has been shown to be highly effective, outperforming sort-based approaches, which is why we compare our approach to it. Sarma et al. [10] have generalized this approach to make it more suitable for parallel processing. There are approximate methods for identifying candidate pairs in high-dimensional spaces: one based on locality sensitive hashing (LSH) [1] and another based on randomly shifted grids [3]. Since these methods produce false negatives, we do not consider them here.

接下来探讨基于分区的方法。Jacox和Samet[20]借鉴快速排序思想开发了Quickjoin算法，通过空间参考点与数据点到该点的距离进行分区。该方法递归地将数据集划分为更小的子集，当子集规模足够小时，开始在子集及其相邻子集中寻找潜在匹配。该方案已被证明高效优于基于排序的方法，故本文将其作为对比基准。Sarma等人[10]对该方法进行了泛化以适配并行处理。针对高维空间候选对识别存在两种近似方法：基于局部敏感哈希(LSH)[1]和基于随机偏移网格[3]的技术。由于会产生假阴性结果，本文不予考虑。

Finally, complementary to our work are approaches using correlation to improve the accuracy of predictions: Yi et al. [55], Gao et al. [16], and Lian et al. [31]. They rely on utilizing correlation for their predictions, but do not propose new algorithms for the efficient computation of threshold-based correlations.

最后，与本研究形成互补的是利用相关性提升预测精度的方案：Yi等[55]、Gao等[16]和Lian等[31]。这些研究虽依赖相关性进行预测，但未提出新的阈值相关高效计算算法。

## 4 CORRELATION JOIN

## 4 相关性连接

Our goal is to determine all pairs of windows whose Pearson correlation is above threshold $T$ . As we map the correlation to the Euclidean distance (see Equation (4)), this boils down to returning pairs whose distance is less or equal to $\epsilon$ . We start by illustrating how the bucketing filter works in principle and why it should not be applied naively to a correlation join. The difficulties we run into serve as a motivation for the solution we propose: a carefully crafted pipeline with several steps (cf. Figure 2). We show how every single one of the difficulties can be overcome by combining complementary dimensionality reduction and transformation techniques with a two-filter approach for maximal efficiency.

我们的目标是找出皮尔逊相关系数超过阈值$T$的所有窗口对。由于将相关性映射为欧氏距离（见公式(4)），实质是返回距离小于等于$\epsilon$的窗口对。首先阐述分桶过滤器的基本原理，以及为何不能直接应用于相关性连接。这些难点促使我们提出解决方案：精心设计的多阶段处理流程（见图2）。我们将展示如何通过结合互补的降维转换技术与双重过滤策略，逐一克服这些难题以实现最大效率。

### 4.1 Bucketing Filter

### 4.1 分桶过滤器

We avoid comparing all ${m}^{2}$ pairs by partitioning the input windows of the $m$ data streams into buckets. To keep things simple, we first show the principle for one-dimensional data points. We divide the value domain of the data points into buckets of width $\epsilon$ ,i.e.,bucket $b$ contains the data points whose value $w$ falls into the interval $\lbrack \left( {b - 1}\right) \epsilon ,{b\epsilon })$ ,i.e.,the first bucket is labeled $b = 1$ . Thus, given a value $w,\left\lfloor  \frac{w}{\epsilon }\right\rfloor   + 1$ is the label of the bucket $w$ belongs to.

通过将$m$数据流的输入窗口分桶来避免比较所有${m}^{2}$窗口对。为简化说明，先以一维数据点为例：将数据点的值域划分为宽度为$\epsilon$的桶，即桶$b$包含值$w$落在区间$\lbrack \left( {b - 1}\right) \epsilon ,{b\epsilon })$内的数据点（首桶标记为$b = 1$）。因此给定值$w,\left\lfloor  \frac{w}{\epsilon }\right\rfloor   + 1$即可确定$w$所属桶的标签。

Example 4.1. Let $m = 6,\epsilon  = {0.5}$ ,and windows ${w}_{1} = \{ {0.1}\} ,{w}_{2} = \{ {0.2}\} ,{w}_{3} = \{ {0.3}\} ,{w}_{4} =$ $\{ {1.2}\} ,{w}_{5} = \{ {1.4}\} ,{w}_{6} = \{ {1.8}\}$ . Window ${w}_{4}$ falls into bucket $\left\lfloor  \frac{1.2}{0.5}\right\rfloor   + 1 = 3$ ,which means that ${w}_{4}$ is located in bucket 3. Figure 1 shows the final bucketing (Bkt is used as an abbreviation of bucket).

示例4.1 设$m = 6,\epsilon  = {0.5}$，窗口${w}_{1} = \{ {0.1}\} ,{w}_{2} = \{ {0.2}\} ,{w}_{3} = \{ {0.3}\} ,{w}_{4} =$$\{ {1.2}\} ,{w}_{5} = \{ {1.4}\} ,{w}_{6} = \{ {1.8}\}$。窗口${w}_{4}$落入桶$\left\lfloor  \frac{1.2}{0.5}\right\rfloor   + 1 = 3$，即位于3号桶。图1展示了最终分桶结果（Bkt为桶的缩写）。

<!-- Media -->

<!-- figureText: $\epsilon$ $\epsilon$ $\epsilon$ $\epsilon$ Bkt 3 Bkt 4 ${w}_{6}$ Bkt 1 Bkt 2 ${w}_{1}{w}_{2}{w}_{3}$ -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_6.jpg?x=445&y=1251&w=675&h=150&r=0"/>

Fig. 1. An example for window bucketing

图1. 窗口分桶示例

<!-- Media -->

Bucketing facilitates the search for pairs of data points within a distance of $\epsilon$ of each other,since it allows us to distinguish three different cases. First, the distance between two data points in the same bucket is definitely less than $\epsilon$ . Second,two data points located in non-neighboring buckets cannot possibly be within distance $\epsilon$ of each other. Finally,for data points from neighboring buckets we have to check if their distance is less or equal to $\epsilon$ .

分桶机制能有效搜索彼此距离在$\epsilon$范围内的数据点对，因其可区分三种情况：首先，同桶内数据点距离必然小于$\epsilon$；其次，非相邻桶的数据点距离绝不可能小于$\epsilon$；最后，相邻桶数据点需验证其距离是否≤$\epsilon$。

<!-- Media -->

<!-- figureText: $m \times  n \rightarrow$ $m \times  {k}_{s} \rightarrow$ $m \times  {k}_{b} -  \downarrow$ ${m}^{2}{r}_{1} \times  {k}_{e} \rightarrow$ ${m}^{2}{r}_{2} \times  n \downarrow$ Euclidean correlation distance filter computation $\rightarrow  {m}^{2}{r}_{1}$ $\rightarrow  {m}^{2}{r}_{2}$ dim reduction dim transfor- bucketing (PAA) mation (SVD) filter $\rightarrow  m \times  {k}_{s}$ $\rightarrow  m \times  {k}_{b}$ -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_6.jpg?x=142&y=1778&w=1287&h=173&r=0"/>

Fig. 2. CorrJoin computation pipeline: input dimensionality is stated above each box, output dimensionality and candidate set sizes (for filtering steps) below each box; parameters to be tuned are ${k}_{s},{k}_{e}$ and ${k}_{b}$ .

图2. CorrJoin计算流程：各框上方标注输入维度，下方标注输出维度及候选集大小（过滤步骤）；待调参数为${k}_{s},{k}_{e}$和${k}_{b}$。

<!-- Media -->

### 4.2 Moving to Higher Dimensions

### 4.2 高维数据扩展

Bucketing also works for high-dimensional data points. However, applying the approach in a straightforward fashion leads to performance issues. Assuming that we have $n$ -dimensional data points and that we need to divide the value domain of each dimension into $B$ partitions of width $\epsilon$ , yields ${B}^{n}$ hypercubes for the bucketing scheme and each hypercube has up to ${3}^{n} - 1$ neighbors. Applying bucketing to the windows of our data streams directly is infeasible since the windows have hundreds or thousands of dimensions. Instead, we need to reduce the number of dimensions before bucketing. However, reducing the number of dimensions negatively impacts the quality of the bucketing approach. For example, if we reduce the number of dimensions by either picking them randomly or using PAA, the distance between the data points in the reduced space is only a lower bound of their true distance. As a consequence, the dimensionality reduction moves data points closer together, meaning that we now have more data points assigned to the same bucket or neighboring buckets as before,even though their true distance is greater than $\epsilon$ . Data points located in the same or neighboring buckets must be compared. Reducing the dimensionality to low values to make bucketing feasible means that we get a rougher approximation of the original data. For the datasets we used in the experimental evaluation, we typically had to go down to three dimensions (based on the investigations shown in Figures 4 and 7), leading to inaccurate distance estimations using PAA.

分桶机制同样适用于高维数据，但直接应用会导致性能问题。假设处理$n$维数据且每维值域需划分为$B$个宽度为$\epsilon$的分区，将生成${B}^{n}$个超立方体，每个超立方体最多有${3}^{n} - 1$个相邻体。直接对数据流窗口分桶不可行，因其维度高达数百上千。需先降维再分桶，但这会降低分桶质量。例如随机选取维度或使用PAA降维后，数据点在降维空间的距离仅为其真实距离的下界，导致更多数据点被误分至同桶或相邻桶（即使真实距离＞$\epsilon$）。实验数据显示，通常需降至三维（见图4、7），但PAA会导致距离估算不准确。

In order to counteract the negative impact of dimensionality reduction, we apply Singular Value Decomposition (SVD) to the $m$ windows computed by PAA. After applying SVD,we pick the dimensions with the highest variance, which leads to a better spread of data points among the buckets, increasing the likelihood of data points ending up in non-neighboring buckets. In the end, we have low-dimensional data (for our datasets, we used three dimensions), but the data created by applying PAA and then SVD reflects the original distances much better than picking dimensions randomly or using only PAA.

为抵消降维负面影响，我们对PAA生成的$m$维窗口应用奇异值分解(SVD)，选取方差最大的维度。这使数据点在桶间分布更分散，增加落入非相邻桶的概率。最终获得低维数据（实验采用三维），经PAA+SVD处理的数据比随机选维或仅用PAA更能反映原始距离关系。

### 4.3 Euclidean Distance Filter

### 4.3 欧氏距离过滤器

Before computing the correlation between pairs of windows from the same or neighboring buckets, we use a Euclidean distance filter to further reduce the number of candidate pairs, but this time using a higher number of dimensions compared to the dimensionality used by SVD before bucketing. We denote the number of dimensions used for SVD by ${k}_{s}$ and the number of dimensions used for the Euclidean distance filter by ${k}_{e}$ . By using ${k}_{e}$ dimensions we obtain a better accuracy and, thus, fewer false positives. We can afford to use a higher number of dimensions, as we apply the comparison to a smaller number of pairs. Only the pairs passing the bucketing filter will go through the Euclidean distance filter.

在计算同桶或相邻桶窗口间的相关性前，采用欧氏距离过滤器进一步减少候选对数量。此时使用维度数${k}_{e}$高于分桶前SVD所用维度数${k}_{s}$。更高维度带来更精确的过滤效果，减少误判。由于待比较对数已减少，可承受更高维计算。仅通过分桶过滤的候选对需经此过滤器。

### 4.4 Complete Algorithm

### 4.4 完整算法

The complete CorrJoin algorithm assembles the different components into the pipeline shown in Figure 2. The input to the pipeline are $m$ windows,each with $n$ dimensions. In a first step,we reduce the dimensionality of the windows using PAA. We generate representations with ${k}_{s}$ and ${k}_{e}$ dimensions as depicted in Figure 3 (the original windows are preserved in an $m \times  n$ matrix). We generate the ${k}_{e}$ -dimensional representation at this point already to save another scan through the windows later on. The concrete values of ${k}_{s}$ and ${k}_{e}$ have a significant impact on the performance, and we discuss the tuning of these parameters in Section 6.2. The ${k}_{s}$ -dimensional windows are fed into the algorithm computing the SVD. From the output of SVD we choose the first ${k}_{b}$ dimensions for the bucketing step (the calibration of parameter ${k}_{b}$ is discussed later). This produces a set of candidate pairs ${C}_{1}$ that contains ${r}_{1}$ percent of the pairs in the Cartesian product. We call the pruning rate for this step the join pruning rate and it is equal to $1 - {r}_{1}$ . Note that we do not pass the actual windows through the pipeline but their matrix indices (red numbers in Figure 3) for lookup during the processing. The values for ${r}_{1}$ observed in our experimental evaluation are typically below 40%. We apply a Euclidean filter step on this candidate set,this time using the ${k}_{e}$ -dimensional windows. This yields candidate set ${C}_{2}$ with ${r}_{2}$ percent of the Cartesian product. Consequently, ${r}_{2}$ determines the overall pruning rate,which is equal to $1 - {r}_{2}$ . Typical values for ${r}_{2}$ are below 10%. Finally,we compute the true value of the Pearson correlation for all pairs in the second candidate set, resulting in the overall answer set.

完整的CorrJoin算法将各组件组装成如图2所示的处理流程。流程输入为$m$个窗口，每个窗口含$n$个维度。首先使用PAA方法降低窗口维度，生成如图3所示的${k}_{s}$维和${k}_{e}$维表示（原始窗口保存在$m \times  n$矩阵中）。此处提前生成${k}_{e}$维表示可避免后续再次扫描窗口。维度参数${k}_{s}$和${k}_{e}$的取值对性能影响显著，我们将在6.2节讨论其调优方法。${k}_{s}$维窗口输入至SVD计算算法，从其输出中选择前${k}_{b}$个维度进行分桶处理（参数${k}_{b}$的校准后文详述），生成包含笛卡尔积${r}_{1}$%候选对的集合${C}_{1}$。此步骤的剪枝率称为连接剪枝率，值为$1 - {r}_{1}$。注意流程中传递的是窗口的矩阵索引（图3中红色数字）而非实际窗口数据。实验评估中${r}_{1}$的观测值通常低于40%。对候选集应用欧氏距离过滤（使用${k}_{e}$维窗口），得到含笛卡尔积${r}_{2}$%的候选集${C}_{2}$，故${r}_{2}$决定整体剪枝率（值为$1 - {r}_{2}$）。典型场景下${r}_{2}$低于10%。最后计算第二候选集中所有对的皮尔逊相关系数真值，得到最终结果集。

Algorithm 1 shows the pseudocode for our CorrJoin algorithm. For better readability, the bucketing is shown in Algorithm 2. Matrices $W,{W}_{s},{W}_{b}$ ,and ${W}_{e}$ with $m$ rows and $n,{k}_{s},{k}_{b}$ and ${k}_{e}$ columns, store the values of the windows in the original and reduced dimensionality spaces, respectively. Additionally,there is an input buffer ${\mathrm{B}}_{p}$ of size $h$ ,which holds the next batch of elements from time series ${x}_{p}$ ,i.e.,in each step, $h$ new values become available in the data streams. Operators $\ominus$ and $\oplus$ drop previous and add new data points to a window,respectively. In Algorithm 2,BKT includes all the buckets of windows after bucketing according to ${k}_{b}$ dimensions. We note that in our implementation, we store the windows indices in the buckets rather than the windows itself.

算法1展示了CorrJoin算法的伪代码，分桶处理详见算法2。矩阵$W,{W}_{s},{W}_{b}$和${W}_{e}$分别以$m$行、$n,{k}_{s},{k}_{b}$列和${k}_{e}$列存储原始维度与降维后的窗口数据。另设大小为$h$的输入缓冲区${\mathrm{B}}_{p}$，用于暂存时间序列${x}_{p}$的新批次数据（即数据流每步产生$h$个新值）。运算符$\ominus$和$\oplus$分别实现窗口数据的滚动更新。算法2中，BKT包含按${k}_{b}$维分桶后的所有窗口桶，实际实现中桶内存储窗口索引而非窗口数据本身。

<!-- Media -->

<!-- figureText: W ${\mathrm{W}}_{\mathrm{s}}$ ${\mathrm{W}}_{\mathrm{e}}$ ${\mathrm{W}}_{\mathrm{b}}$ ${\mathrm{m}}^{2}{\mathrm{r}}_{1} \times  2$ $\mathrm{m} \times  {\mathrm{k}}_{\mathrm{e}}$ $\mathrm{m} \times  {\mathrm{k}}_{\mathrm{b}}$ ${\mathrm{C}}_{2}$ 2.5 [-1.6 ${\mathrm{m}}^{2}{\mathrm{r}}_{2} \times  2$ 3.5 7.5 4 -0.2 R 7.5 6.5 1.5 8.5 2.1 4 7 1.6 6 6.5 1.5 5.5 6.5 7.5 -0.6 1.5 7.5 - 2 3 2.5 1 -2.6 2.5 5.5 -0.1 $\mathbf{m} \times  \mathbf{n}$ $\mathrm{m} \times  {\mathrm{k}}_{\mathrm{s}}$ 5.7 6.5 8 4 3.7 3.5 9 5.7 5 3.2 6.5 2.5 4 3.2 6.2 2.5 6.5 7 7.5 6.2 4.5 7.5 3 2 3 4.7 1.7 6.5 2 4.5 5.2 6.5 -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_8.jpg?x=145&y=942&w=1286&h=421&r=0"/>

Fig. 3. Example for utilized data structures: matrices with $m$ rows and $n,{k}_{s},{k}_{b}$ and ${k}_{e}$ columns store windows in the original and reduced dimensionality spaces. Candidate sets ${C}_{1}$ and ${C}_{2}$ contain window pairs that have to be checked.

图3. 数据结构示例：$m$行$n,{k}_{s},{k}_{b}$列和${k}_{e}$列的矩阵分别存储原始维度与降维窗口数据。候选集${C}_{1}$和${C}_{2}$包含待检测的窗口对。

<!-- Media -->

## 5 COST MODEL AND ANALYSIS

## 5 成本模型与分析

We now determine the run-time of the pipeline illustrated in Figure 2 and compare it to the cost of the baseline algorithm. The total costs of the pipeline are comprised of five components, one for each phase: computing the PAA representation, computation of SVD, bucketing filter, a Euclidean distance filter, and the computation of the true correlation. If the time series data has not been normalized yet,we need to first do the normalization. Altogether,the total costs ${c}_{\text{total }}$ are made up of

现计算图2所示流程的运行时成本，并与基线算法对比。流程总成本包含五个阶段：PAA表示计算、SVD计算、分桶过滤、欧氏距离过滤及真实相关系数计算。若时间序列数据未标准化需先进行归一化处理，总成本${c}_{\text{total }}$由以下部分构成：

$$
\left( {{c}_{\text{norm }} + }\right) {c}_{PAA} + {c}_{SVD} + {c}_{bkt} + {c}_{\text{filter }} + {c}_{\text{true }} \tag{5}
$$

We look at all these subcosts in turn and compare them to the costs of the brute-force baseline approach,which computes the Pearson correlation for all ${m}^{2}$ pairs on the full-sized windows, resulting in ${c}_{\text{base }} = O\left( {{m}^{2}n}\right)$ .

我们依次分析这些子成本，并与暴力基线方法（计算全尺寸窗口所有${m}^{2}$对数据的皮尔逊相关系数，成本为${c}_{\text{base }} = O\left( {{m}^{2}n}\right)$）进行对比。

The normalization of the time series data can be done in time ${mn}$ ,so

时间序列数据归一化耗时${mn}$，因此

$$
{c}_{\text{norm }} = O\left( {mn}\right)  \tag{6}
$$

which is cheaper than ${c}_{\text{base }}$ by a factor of $m$ .

其价格比${c}_{\text{base }}$低$m$倍

<!-- Media -->

Algorithm 1: CorrJoin $\left( {{x}_{1},\ldots ,{x}_{m},n,h,T,{k}_{s},{k}_{e},{k}_{b}}\right)$

算法1：CorrJoin(关联连接)$\left( {{x}_{1},\ldots ,{x}_{m},n,h,T,{k}_{s},{k}_{e},{k}_{b}}\right)$

---

Input: Stream with $m$ series ${x}_{1},{x}_{2},\ldots ,{x}_{m}$ (each with an input buffer ${\mathrm{B}}_{p}$ ),window size $n$ ,stride

输入：包含$m$个序列${x}_{1},{x}_{2},\ldots ,{x}_{m}$的数据流（每个序列带输入缓冲区${\mathrm{B}}_{p}$），窗口大小$n$，步长

						$h$ ,correlation threshold $T$ ,number of dimensions ${k}_{s},{k}_{e},{k}_{b}$

						$h$，相关性阈值$T$，维度数${k}_{s},{k}_{e},{k}_{b}$

Output: Report threshold-based correlations

输出：基于阈值的相关性报告

${\epsilon }_{1} \leftarrow  \sqrt{2{k}_{s}\left( {1 - T}\right) /n},{\epsilon }_{2} \leftarrow  \sqrt{2{k}_{e}\left( {1 - T}\right) /n}$

$\alpha  \leftarrow  1$

while input buffers are arriving do

当输入缓冲区持续到达时：

		for $p \leftarrow  1$ to $m$ do

		循环从$p \leftarrow  1$到$m$执行：

					${w}_{p}\left\lbrack  \alpha \right\rbrack   \leftarrow  {w}_{p}\left\lbrack  {\alpha  - 1}\right\rbrack  \left\lbrack  {1..h}\right\rbrack   \ominus  {w}_{p}\left\lbrack  {\alpha  - 1}\right\rbrack   \oplus  {\mathrm{B}}_{p}$

				$W\left\lbrack  p\right\rbrack   \leftarrow$ normalization of ${w}_{p}\left\lbrack  \alpha \right\rbrack$ (see Section 2.1)

				$W\left\lbrack  p\right\rbrack   \leftarrow$对${w}_{p}\left\lbrack  \alpha \right\rbrack$进行归一化处理（参见第2.1节）

				${W}_{s}\left\lbrack  p\right\rbrack  ,{W}_{e}\left\lbrack  p\right\rbrack   \leftarrow  \operatorname{PAA}\left( {W\left\lbrack  p\right\rbrack  ,{k}_{s},{k}_{e}}\right)$

		${W}_{b} \leftarrow  \operatorname{SVD}\left( {W}_{s}\right)$

		${C}_{1} \leftarrow$ BucketingFilter $\left( {{W}_{b},{k}_{b},{\epsilon }_{1}}\right)$

		${C}_{1} \leftarrow$分桶过滤器$\left( {{W}_{b},{k}_{b},{\epsilon }_{1}}\right)$

		${C}_{2} \leftarrow  \varnothing$

		for $\langle p,q\rangle  \in  {C}_{1}$ do

		循环执行：

			1 if $d\left( {{W}_{e}\left\lbrack  p\right\rbrack  ,{W}_{e}\left\lbrack  q\right\rbrack  }\right)  \leq  {\epsilon }_{2}$ then ${C}_{2} \leftarrow  {C}_{2} \cup  \langle p,q\rangle$

			当$d\left( {{W}_{e}\left\lbrack  p\right\rbrack  ,{W}_{e}\left\lbrack  q\right\rbrack  }\right)  \leq  {\epsilon }_{2}$成立时执行${C}_{2} \leftarrow  {C}_{2} \cup  \langle p,q\rangle$

		for $\langle p,q\rangle  \in  {C}_{2}$ do

		循环执行：

				if $\left| {\operatorname{corr}\left( {W\left\lbrack  p\right\rbrack  ,W\left\lbrack  q\right\rbrack  }\right) }\right|  \geq  T$ then Report $\left( {p,q,\alpha }\right)$

				若$\left| {\operatorname{corr}\left( {W\left\lbrack  p\right\rbrack  ,W\left\lbrack  q\right\rbrack  }\right) }\right|  \geq  T$成立则报告$\left( {p,q,\alpha }\right)$

		$\alpha  \leftarrow  \alpha  + 1$

---

Algorithm 2: BucketingFilter $\left( {{W}_{b},{k}_{b},\epsilon }\right)$

算法2：分桶过滤器$\left( {{W}_{b},{k}_{b},\epsilon }\right)$

---

Input: Matrix ${W}_{b}$ of windows,number of dimensions ${k}_{b}$ ,distance threshold $\epsilon$

输入：窗口矩阵${W}_{b}$，维度数${k}_{b}$，距离阈值$\epsilon$

Output: Candidate set of likely correlated pairs of windows

输出：可能相关的窗口候选对集合

${C}_{1} \leftarrow  \varnothing$

$m \leftarrow$ number of windows in ${W}_{b}$

$m \leftarrow$ ${W}_{b}$中的窗口数量

${BKT} \leftarrow$ initialize ${k}_{b}$ -dimensional bucketing scheme

${BKT} \leftarrow$ 初始化${k}_{b}$维分桶方案

for $p \leftarrow  1$ to $m$ do

从$p \leftarrow  1$到$m$循环执行

	assign ${W}_{b}\left\lbrack  p\right\rbrack$ according to ${k}_{b}$ columns to ${Bk}{t}_{i} \in  {BKT}$

	根据${k}_{b}$列将${W}_{b}\left\lbrack  p\right\rbrack$分配到${Bk}{t}_{i} \in  {BKT}$

for ${Bkt} \in  {BKT}$ do

循环执行${Bkt} \in  {BKT}$

	for ${W}_{b}\left\lbrack  i\right\rbrack   \in  {Bkt}$ do

	对${W}_{b}\left\lbrack  i\right\rbrack   \in  {Bkt}$执行循环

		for ${W}_{b}\left\lbrack  j\right\rbrack   \in  {Bkt}$ do

		对${W}_{b}\left\lbrack  j\right\rbrack   \in  {Bkt}$执行循环

				if $i < j$ and $d\left( {{W}_{b}\left\lbrack  i\right\rbrack  ,{W}_{b}\left\lbrack  j\right\rbrack  }\right)  \leq  \epsilon$ then ${C}_{1} = {C}_{1} \cup  \langle i,j\rangle$

							若$i < j$且$d\left( {{W}_{b}\left\lbrack  i\right\rbrack  ,{W}_{b}\left\lbrack  j\right\rbrack  }\right)  \leq  \epsilon$成立，则执行${C}_{1} = {C}_{1} \cup  \langle i,j\rangle$

	for each neighbor ${Bk}{t}^{\prime }$ of ${Bkt}$ do

	遍历${Bkt}$的每个邻居${Bk}{t}^{\prime }$

		for ${W}_{b}\left\lbrack  i\right\rbrack   \in  {Bkt}$ do

		对${W}_{b}\left\lbrack  i\right\rbrack   \in  {Bkt}$执行循环

				for ${W}_{b}\left\lbrack  j\right\rbrack   \in  {Bk}{t}^{\prime }$ do

							对${W}_{b}\left\lbrack  j\right\rbrack   \in  {Bk}{t}^{\prime }$执行循环

					if $d\left( {{W}_{b}\left\lbrack  i\right\rbrack  ,{W}_{b}\left\lbrack  j\right\rbrack  }\right)  \leq  \epsilon$ then ${C}_{1} = {C}_{1} \cup  \langle i,j\rangle$

									若$d\left( {{W}_{b}\left\lbrack  i\right\rbrack  ,{W}_{b}\left\lbrack  j\right\rbrack  }\right)  \leq  \epsilon$成立，则执行${C}_{1} = {C}_{1} \cup  \langle i,j\rangle$

return ${C}_{1}$

返回${C}_{1}$

---

<!-- Media -->

Computing the PAA representation has the same time complexity, so its costs are

计算PAA表示法具有相同的时间复杂度，因此其成本为

$$
{c}_{PAA} = O\left( {mn}\right)  \tag{7}
$$

Again, this is much faster than the baseline approach. These steps take little time (together less than 5%) and are preparation steps to speed up the following steps.

这仍远快于基准方法。这些步骤耗时极短（合计不足5%），是为加速后续步骤所做的准备工作。

SVD's costs are quadratic in the number of dimensions. This is the reason why it requires low-dimensional data to perform efficiently. As the input data has ${k}_{s}$ dimensions,we get

SVD的成本随维度数量呈二次方增长。这就是其需要低维数据以实现高效运算的原因。由于输入数据具有${k}_{s}$个维度，我们得到

$$
{c}_{SVD} = O\left( {m{k}_{s}^{2}}\right)  \tag{8}
$$

Clearly, ${k}_{s}$ is always smaller than $n$ ,since we are reducing the dimensionality with PAA. However, we also have to make sure that ${k}_{s}^{2}$ is smaller than $m$ ,otherwise the computation of SVD will dominate the computation time.

显然${k}_{s}$始终小于$n$，因为PAA会降低维度。但还需确保${k}_{s}^{2}$小于$m$，否则SVD计算将主导整体耗时。

Next we turn to the costs for the bucketing and joining. The cost for the bucketing is $m{k}_{b}$ as assigning each window to a bucket is done considering ${k}_{b}$ dimensions. Let ${m}_{i}$ be the number of elements in the $i$ -th bucket and let ${m}_{ij}$ be the number of elements in the $j$ -th neighbor of bucket $i$ . Basically,we need to determine the costs for joining the elements within a bucket (the summation) and the costs for joining these elements with elements in neighboring buckets (the double-summation):

接下来分析分桶与连接的成本。分桶成本为$m{k}_{b}$，因每个窗口分配到桶时需考虑${k}_{b}$个维度。设${m}_{i}$为第$i$个桶的元素数量，${m}_{ij}$为第$i$个桶第$j$邻接桶的元素数量。本质上，我们需要计算桶内元素连接（单重求和）与邻接桶元素连接（双重求和）的成本：

$$
{c}_{bkt} = O\left( {m{k}_{b} + \mathop{\sum }\limits_{{i = 1}}^{{B}^{{k}_{b}}}{m}_{i}^{2}{k}_{b} + \mathop{\sum }\limits_{{i = 1}}^{{B}^{{k}_{b}}}\mathop{\sum }\limits_{{j = 1}}^{\frac{{3}^{{k}_{b}} - 1}{2}}{m}_{i}{m}_{ij}{k}_{b}}\right)  \tag{9}
$$

Clearly,choosing a value for ${k}_{b}$ that is too large leads to a prohibitively expensive run time,due to the exponential growth of the summation limits. The reason we use bucketing is to prune many non-matching pairs without having to check them. Note that while the costs increase exponentially in ${k}_{b}$ ,the join pruning rate does not. On the contrary,increasing ${k}_{b}$ leads to diminishing returns, i.e., the improvement of the pruning rate becomes smaller and smaller. On top of that, using SVD allows us to pick the dimensions in order of importance, i.e., every additional dimension we choose is worse than the preceding ones. We investigate this empirically in Section 6.2. Note that the costs ${c}_{bkt}$ are impacted by the distribution of the data over the buckets. If the elements are clustered in a few buckets, we compute the join of a considerable number of pairs in a nested-loop fashion. This is the reason why it is important to spread out the elements over the buckets with the help of SVD.

显然，若${k}_{b}$取值过大，求和极限的指数级增长将导致运行时间不可承受。分桶技术的核心在于无需检查即可剪除大量不匹配对。需注意虽然成本随${k}_{b}$指数增长，但连接剪除率并非如此。相反，增大${k}_{b}$会导致收益递减，即剪除率提升幅度越来越小。此外，SVD技术能按重要性顺序选择维度，每新增维度效果均逊于前序维度。我们将在6.2节实证研究这一点。值得注意的是，成本${c}_{bkt}$受数据在桶间分布的影响——若元素集中在少数桶中，我们需以嵌套循环方式计算大量配对，这正是借助SVD分散元素到各桶的关键原因。

The costs of the Euclidean distance filter applied next depend on the size ${m}^{2}{r}_{1}$ of the first candidate set $\left( {r}_{1}\right.$ is the ratio of pairs passing the bucketing filter,i.e.,the join pruning rate is equal to $\left. {1 - {r}_{1}}\right)$ :

后续应用的欧氏距离过滤器成本取决于首候选集大小${m}^{2}{r}_{1}$，其中$\left( {r}_{1}\right.$是通过分桶过滤的配对比例，即连接剪除率等于$\left. {1 - {r}_{1}}\right)$：

$$
{c}_{\text{filter }} = O\left( {{m}^{2}{r}_{1}{k}_{e}}\right)  \tag{10}
$$

Clearly, ${k}_{e}$ is much smaller than $n$ and ${m}^{2}{r}_{1}$ is also smaller than ${m}^{2}$ (the candidate set contains a subset of the Cartesian product) as we see in the experimental evaluation. The smaller ${r}_{1}$ ,the faster this step will run. As ${m}^{2}{r}_{1}$ is lower-bounded by the number of pairs in the final result set,this has implications on the use of our algorithm. If we expect a very large result set, we may be better off running the baseline approach. This is not surprising and true for all join algorithms, e.g., for a large result set, a hash join cannot play out its strengths compared to a nested-loop join (see also Section 6.3.3).

显然，${k}_{e}$远小于$n$，且${m}^{2}{r}_{1}$也小于${m}^{2}$（候选集包含笛卡尔积的子集），这在实验评估中得以验证。${r}_{1}$越小，该步骤执行速度越快。由于${m}^{2}{r}_{1}$的下界是最终结果集中的对数，这影响了我们算法的适用场景。若预期结果集极大，采用基线方法可能更优。此现象对所有连接算法均成立，例如面对大规模结果集时，哈希连接相比嵌套循环连接无法发挥优势（参见第6.3.3节）。

Finally, we have the costs for computing the answer set given the second candidate set produced by the filter step,which has size ${m}^{2}{r}_{2}$ . Thus,

最后，我们计算基于过滤步骤生成的第二候选集（规模为${m}^{2}{r}_{2}$）的答案集成本，因此

$$
{c}_{\text{true }} = O\left( {{m}^{2}{r}_{2}n}\right)  \tag{11}
$$

As in the second candidate set the number of candidate pairs is further reduced, ${r}_{2}$ is smaller than ${r}_{1}$ ,and the arguments we made for ${c}_{\text{filter }}$ also hold for ${c}_{\text{true }}$ . We achieve an overall pruning rate of $1 - {r}_{2}$ .

由于第二候选集中候选对数量进一步减少，${r}_{2}$小于${r}_{1}$，此前针对${c}_{\text{filter }}$的论证同样适用于${c}_{\text{true }}$。我们实现了$1 - {r}_{2}$的整体剪枝率。

## 6 EXPERIMENTAL EVALUATION

## 6 实验评估

We evaluate our algorithm and compare it to the state-of-the-art and baseline approaches empirically. By doing so, we back up claims made earlier and demonstrate the effectiveness and efficiency of CorrJoin. We consider the following approaches:

我们通过实证评估算法性能，并与前沿方法及基线方法对比。此举既验证了前期论断，也证明了CorrJoin的有效性与高效性。我们考察了以下方法：

- $\epsilon$ -kdB tree: partitions the data hierarchically into several tiles by randomly selecting dimensions. It reduces the number of comparisons by checking the windows inside a tile and neighboring tiles, and computes the correlations for qualifying pairs.

- $\epsilon$-kdB树：通过随机选择维度将数据分层划分为多个区块，通过检查区块内部及相邻区块的窗口减少比较次数，并为符合条件的配对计算相关性。

- TSUBASA: finds correlated pairs by considering the correlation of all pairs. It divides each window into multiple basic windows and obtains the statistics of basic windows to identify pairwise correlations among the windows.

- TSUBASA：通过分析所有配对的相关性来发现关联对。该方法将每个窗口划分为多个基础窗口，通过基础窗口的统计量识别窗口间的成对相关性。

- Quickjoin: uses a reference window and the distance of the windows in the dataset to this reference window to partition the data. It reduces the number of comparisons by recursively partitioning the data into subsets. Once the subsets reach a size that is small enough, finding potential matches is started inside the subsets and in the neighboring subsets. Finally, the correlations are computed for qualifying matches.

- Quickjoin：利用参考窗口与数据集中各窗口的距离进行数据分区，通过递归分区减少比较次数。当子集规模足够小时，开始在子集内部及相邻子集中寻找潜在匹配，最终为符合条件的匹配计算相关性。

- Inc ${P}^{PAA}$ : computes correlated pairs by checking the correlation of all pairs. It speeds up the calculation of the correlation by using the PAA filter-and-refine approach. Note that we consider Inc ${P}^{PAA}$ as our baseline approach,but it has been shown to be an efficient approach, performing an order of magnitude faster than naive IncP [30].

- Inc${P}^{PAA}$：通过检查所有配对的相关性进行计算，采用PAA过滤-优化方法加速相关计算。需注意虽然我们将Inc${P}^{PAA}$作为基线方法，但其已被证明是高效方案，比原生IncP快一个数量级[30]。

### 6.1 Setup and Datasets

### 6.1 实验设置与数据集

First, we describe our methodology, i.e., the setup of our experiments and the time series dataset we used. We implemented the algorithms from scratch in R without using additional libraries, as we want to measure their performance and not that of any libraries. We conducted the experiments on a ${2.2}\mathrm{{GHz}}6 -$ Core Mac with ${16}\mathrm{{GB}}$ of memory. We used the following time series datasets ${}^{4}$ :

首先说明方法论，包括实验配置及所用时间序列数据集。为避免第三方库性能干扰，我们使用R语言从头实现所有算法。实验环境为配备${16}\mathrm{{GB}}$内存的${2.2}\mathrm{{GHz}}6 -$核Mac设备，采用以下时间序列数据集${}^{4}$：

- Synthetic dataset is a collection of time series generated by the random walk model [2]. Thus,each time series ${x}_{p} = \left\{  {{x}_{p}\left\lbrack  1\right\rbrack  ,{x}_{p}\left\lbrack  2\right\rbrack  ,\ldots ,{x}_{p}\left\lbrack  {n}^{\prime }\right\rbrack  }\right\}$ is generated as

- 合成数据集：通过随机游走模型[2]生成的时间序列集合，每条时间序列${x}_{p} = \left\{  {{x}_{p}\left\lbrack  1\right\rbrack  ,{x}_{p}\left\lbrack  2\right\rbrack  ,\ldots ,{x}_{p}\left\lbrack  {n}^{\prime }\right\rbrack  }\right\}$的生成公式为

$$
{x}_{p}\left\lbrack  i\right\rbrack   = {x}_{p}\left\lbrack  {i - 1}\right\rbrack   + {z}_{i},\;i = 2,3,\ldots ,{n}^{\prime } \tag{12}
$$

where ${x}_{1}$ and ${z}_{i}$ are independent,identically distributed (IID) variables uniformly distributed in the range(-1,1). For each experiment with synthetic datasets,we generate as many time series as we need.

其中${x}_{1}$和${z}_{i}$是(-1,1)范围内均匀分布的独立同分布(IID)变量。每次合成数据集实验均按需生成相应数量的时间序列。

- Random dataset is a collection of time series where each time series ${x}_{p} = \left\{  {{x}_{p}\left\lbrack  1\right\rbrack  ,{x}_{p}\left\lbrack  2\right\rbrack  ,\ldots }\right.$ , $\left. {{x}_{p}\left\lbrack  {n}^{\prime }\right\rbrack  }\right\}$ is generated as ${x}_{p}\left\lbrack  i\right\rbrack   = {z}_{i},i = 1,2,\ldots ,{n}^{\prime }$ ,where ${z}_{i}$ is an independent,identically distributed variable uniformly distributed in the range(-1,1).

- 随机数据集是由多个时间序列组成的集合，其中每个时间序列${x}_{p} = \left\{  {{x}_{p}\left\lbrack  1\right\rbrack  ,{x}_{p}\left\lbrack  2\right\rbrack  ,\ldots }\right.$、$\left. {{x}_{p}\left\lbrack  {n}^{\prime }\right\rbrack  }\right\}$的生成方式为${x}_{p}\left\lbrack  i\right\rbrack   = {z}_{i},i = 1,2,\ldots ,{n}^{\prime }$，其中${z}_{i}$是独立同分布的随机变量，均匀分布于区间(-1,1)内。

- Stock dataset consists of daily closing prices for $m = {3878}$ companies traded on the NASDAQ stock exchange from 2016 to 2020 collected by Yahoo Finance ${}^{5}$ .

- 股票数据集包含2016至2020年间雅虎财经${}^{5}$收集的纳斯达克交易所$m = {3878}$家上市公司每日收盘价数据。

- Gas dataset contains 128 time series collected between 2007 and 2011 from a gas delivery platform situated at the ChemoSignals Laboratory at the University of California in San Diego (one measurement for every 6 hours) [45, 48]. We replicated every series in the dataset 40 times,creating one with $m = {5120}$ series (as we are particularly interested in scaling issues). To each replica we added an IID variable uniformly distributed in the range(-0.05,0.05),so that all time series are unique and the correlation rate of the dataset roughly stayed constant.

- 气体数据集包含2007至2011年间从加州大学圣地亚哥分校化学信号实验室的气体输送平台采集的128个时间序列（每6小时测量一次）[45,48]。我们将每个序列复制40次，生成包含$m = {5120}$个序列的数据集（重点研究规模扩展问题）。每个副本添加了均匀分布于(-0.05,0.05)区间的独立同分布噪声，确保所有时间序列唯一且数据集整体相关性基本保持不变。

---

<!-- Footnote -->

${}^{4}$ The code and datasets are available at https://drive.google.com/drive/folders/1skrE2x2DMgIms51ZR04kiOlLvvna2zNs ${}^{5}$ https://finance.yahoo.com/

${}^{4}$代码及数据集下载地址：https://drive.google.com/drive/folders/1skrE2x2DMgIms51ZR04kiOlLvvna2zNs ${}^{5}$ https://finance.yahoo.com/

<!-- Footnote -->

---

- Chlorine dataset consists of 161 time series (pipe junctions) of a water distribution network and measurement of the Chlorine concentration level at all these junctions during 15 days (one measurement for every 5 minutes) [36, 38]. Every series in the dataset is replicated 30 times with small additive noise to keep the correlation rate constant, creating a dataset with $m = {4830}$ series.

- 氯气数据集包含供水管网161个管道节点的时间序列，记录15天内各节点氯浓度测量值（每5分钟测量一次）[36,38]。通过为每个序列添加微小噪声并复制30次，生成包含$m = {4830}$个序列的数据集，保持整体相关性不变。

When we selected the datasets, we took care to consider the oscillation (or variability) of the time series. Basically, oscillation is the speed and scale of changes in amplitude. For instance, a high-oscillation dataset contains time series with many high peaks and low valleys. The oscillation has an impact on the performance of dimensionality reduction approaches, such as PAA: the lower the oscillation, the better these approaches can preserve the general shape of a time series. We selected datasets with low, medium, and high oscillation for our experiments: these are the chlorine, stock, and gas datasets, respectively.

数据集筛选时我们特别关注时间序列的振荡性（即波动幅度变化的速度与规模）。例如高振荡数据集包含大量陡峭波峰和深谷。振荡性会影响PAA等降维方法的性能：振荡性越低，这些方法越能保持时间序列的整体形态。我们实验选用低、中、高三种振荡性数据集：分别是氯气、股票和气体数据集。

### 6.2 Tuning our approach

### 6.2 参数调优

Before comparing our approach to its competitors,we show how to tune parameters ${k}_{s},{k}_{e}$ ,and ${k}_{b}$ to achieve the best performance. We also demonstrate the impact of the different steps of the CorrJoin pipeline on the performance, illustrating why the particular setup we have chosen for the pipeline makes sense.

在与竞品对比前，我们先展示如何调优参数${k}_{s},{k}_{e}$和${k}_{b}$以获得最佳性能。同时说明CorrJoin流程各步骤对性能的影响，解释当前管道设计的合理性。

6.2.1 Dimensionality of Bucketing. We start by looking at the dimensionality ${k}_{b}$ used for the bucketing, as this parameter drives the whole setup and determines the requirements for the dimensionality reduction step. Figure 4 shows the runtime for different values of ${k}_{b}$ for the different parts of the pipeline. In each subfigure the costs for computing the exact correlations (red part) is the same,since the candidate set produced by the Euclidean distance filter is independent of ${k}_{b}$ . We are particularly interested in the bucketing filter step (green part) and the Euclidean distance filter step (orange part). Increasing ${k}_{b}$ makes the distance filter more effective due to the decreasing number of candidate pairs created by the bucketing filter, at the cost of increased computational overhead for bucketing. However, the gains become smaller and smaller, meaning that these are diminishing returns. The overall runtime first decreases,but when going beyond ${k}_{b} = 3$ ,it increases again, as a larger number of dimensions slows down the bucketing filter due to checking a growing number of buckets with an increasing number of neighbors.

6.2.1 分桶维度。首先分析驱动整个流程的核心参数——分桶维度${k}_{b}$，该参数决定了降维步骤的要求。图4展示管道各环节在不同${k}_{b}$值下的运行时分解。各子图中精确相关性计算耗时（红色部分）恒定，因为欧氏距离过滤器生成的候选集与${k}_{b}$无关。重点观察分桶过滤（绿色部分）和欧氏距离过滤（橙色部分）：增大${k}_{b}$能通过减少分桶候选对提升距离过滤效率，但会增加分桶计算开销。随着维度增加，性能收益呈现边际递减效应。总运行时间先降后升，当超过${k}_{b} = 3$时，由于需要检查更多相邻桶导致分桶过滤器效率下降。

<!-- Media -->

<!-- figureText: $\dot{P}{AA}$ ${SVD}$ Bucketing Stock dataset Gas dataset Random dataset 1500 1500 500 50 5 3 1 5 ${k}_{b}$ ${k}_{b}$ ${k}_{b}$ ${k}_{s} = {15},{k}_{e} = {30}$ Synthetic dataset Chlorine dataset 150 150 150 Total runtime (sec) 100 Total runtime (sec) 100 Total runtime (sec) 100 50 50 0 2 4 2 ${k}_{b}$ ${k}_{b}$ -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_12.jpg?x=151&y=1601&w=1280&h=283&r=0"/>

Fig. 4. Breakdown of runtime of CorrJoin for increasing ${k}_{b}$

图4. CorrJoin随${k}_{b}$增加的运行时分解

<!-- Media -->

Having to use a small value for ${k}_{b}$ has a significant impact on the steps occurring before the bucketing. We have to be able to bring the number of original dimensions, $n$ ,down to ${k}_{b} = 3$ without losing too much accuracy. This is the topic of the following Section 6.2.2. 6.2.2 Evaluation of PAA and SVD. One of the easiest and fastest ways to reduce the dimensionality of a window down to ${k}_{b} = 3$ dimensions is to select the dimensions randomly. We call this approach $C{J}^{Rand}$ .

为${k}_{b}$采用较小值会对分桶前的处理步骤产生重大影响。我们需要在不损失过多精度的情况下，将原始维度数$n$降至${k}_{b} = 3$。这将是第6.2.2节讨论的主题。6.2.2 PAA与SVD的评估。将窗口维度降至${k}_{b} = 3$最简单快速的方法之一是随机选择维度，我们称此方法为$C{J}^{Rand}$。

The left-hand side of Figure 5) shows that this leads to a very poor runtime, since randomly selected dimensions produce a lot of intermediate pairs that do not meet the correlation threshold. The right-hand side of Figure 5 confirms that randomly picking 3 dimensions in a window results in a terrible accuracy. The join pruning rate is almost zero for a correlation threshold of up to .95 and in the end merely reaches ${20}\%$ . We compare the random selection of dimensions with the $C{J}^{PAA}$ approach, which uses PAA to reduce the dimensionality. PAA aggregates all available information in a window into mean values. This results in a much better overall runtime since the pruning rate of $C{J}^{PAA}$ is much higher (see Figure 5).

图5左侧显示这种方法会导致极差的运行时性能，因为随机选择的维度会产生大量不满足相关性阈值的中间对。图5右侧证实，在窗口中随机选取3个维度会得到极差的准确度——当相关性阈值达到0.95时，连接剪枝率几乎为零，最终仅达到${20}\%$。我们将随机选择维度与采用PAA降维的$C{J}^{PAA}$方法进行对比。PAA将窗口内所有可用信息聚合为均值，这使得$C{J}^{PAA}$的剪枝率显著提高（见图5），从而获得更好的整体运行时性能。

<!-- Media -->

<!-- figureText: $C{J}^{\text{Rand }}$ $C{I}^{PAA}$ Synthetic dataset 1 Pruning rate 0.8 0.6 0.4 0.2 0 0.85 0.9 0.95 Correlation threshold(T) Synthetic dataset Total Runtime (sec) 2,000 1,500 , 000 500 0.85 0.9 0.95 Correlation threshold(T) -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_13.jpg?x=449&y=730&w=669&h=339&r=0"/>

Fig. 5. Total runtime and join pruning rate of $C{J}^{\text{Rand }}$ and $C{J}^{PAA}$

图5. $C{J}^{\text{Rand }}$与$C{J}^{PAA}$的总运行时间及连接剪枝率

<!-- Media -->

We can do better by leveraging SVD, a technique that transforms the data and allows us to pick dimensions with a higher variance than any individual dimensions in the original window. Running SVD directly on the original windows is out of the question, though. We ran some experiments and it took SVD more than an hour to compute the decomposition for windows with 1000 dimensions. Consequently, we must reduce the dimensionality of the original windows before using SVD. Our experiments show that 15-dimensional data as input for SVD gives good results (we will come back to this in Section 6.2.3). Thus, we apply a two-staged approach: in the first stage the dimensionality is brought down to 15 dimensions and in the second stage we apply SVD to transform and select dimensions. Figure 6 shows the results for using a random selection $\left( {C{J}^{{Rand} + {SVD}}}\right)$ and PAA $\left( {C{J}^{{PAA} + {SVD}}}\right)$ in the first stage,respectively. Again,the PAA variant performs much better than the random one. Although $C{J}^{{Rand} + {SVD}}$ is better than $C{J}^{Rand}$ ,it is still much worse than $C{J}^{{PAA} + {SVD}}$ or $C{J}^{PAA}$ . The clear winner is $C{J}^{{PAA} + {SVD}}$ ,which outperforms $C{J}^{PAA}$ by a factor of 2.5.

利用SVD（一种数据转换技术）可以取得更好效果，它能让我们选取比原始窗口中任何单一维度方差更大的维度。但直接在原始窗口上运行SVD不可行——实验表明，对1000维窗口进行SVD分解需要超过1小时。因此，我们必须先对原始窗口降维。实验证明，将15维数据作为SVD输入效果良好（详见6.2.3节）。因此我们采用两阶段方案：第一阶段将维度降至15维，第二阶段应用SVD转换并选择维度。图6展示了第一阶段分别采用随机选择$\left( {C{J}^{{Rand} + {SVD}}}\right)$和PAA$\left( {C{J}^{{PAA} + {SVD}}}\right)$的结果，PAA方案再次显著优于随机方案。虽然$C{J}^{{Rand} + {SVD}}$优于$C{J}^{Rand}$，但仍远逊于$C{J}^{{PAA} + {SVD}}$或$C{J}^{PAA}$——最终$C{J}^{{PAA} + {SVD}}$以2.5倍优势成为明显赢家。

<!-- Media -->

<!-- figureText: CI ${}^{{Rand} + {SVD}}$ $C{J}^{{PAA} + {SVD}}$ $C{J}^{PAA}$ Synthetic dataset Pruning rate 0.8 0.4 0.2 0 0.85 0.9 0.95 Correlation threshold(T) Synthetic dataset Total runtime (sec) ${10}^{2}$ ${10}^{1}$ 0.85 0.9 0.95 Correlation threshold(T) -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_13.jpg?x=453&y=1713&w=661&h=336&r=0"/>

Fig. 6. Total runtime and join pruning rate of our approach with and without PAA

图6. 采用/不采用PAA时本方案的总运行时间及连接剪枝率

<!-- Media -->

It is also interesting to note that we get diminishing returns out of SVD, i.e., the first couple of dimensions of a transformed window are the most important ones, as they exhibit the highest variability. With every additional dimension, the variability drops, see Figure 7 for an illustration. Thus, in general there is little reason to go beyond three dimensions for the bucketing, since the additional dimensions do not help in distinguishing windows from each other.

值得注意的是，SVD存在收益递减现象：转换后窗口的前几个维度因具有最高变异性而最为重要，后续维度的变异性会逐渐下降（见图7示意）。因此对于分桶操作，通常没有理由选择超过三个维度，因为额外维度无助于区分不同窗口。

<!-- Media -->

<!-- figureText: 30 Synthetic Chlorine Gas 10 ${k}_{b}$ Eigenvalue 20 10 -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_14.jpg?x=628&y=486&w=322&h=248&r=0"/>

Fig. 7. Quickly falling eigenvalues for increasing ${k}_{b}$

图7. 随${k}_{b}$增加而快速衰减的特征值

<!-- Media -->

In summary, by combining PAA and SVD we increase the efficiency of bucketing. PAA first reduces the dimensionality of a window to a level that allows us to make use of SVD, which in turn prepares the data for the bucketing by allowing us to transform dimensions and select dimensions with high variability.

综上所述，通过结合PAA与SVD，我们提升了分桶效率。PAA先将窗口维度降至适合SVD处理的水平，而SVD则通过转换维度和筛选高变异性维度，为后续分桶做好准备。

6.2.3 Filtering and Refining. Before computing the true correlation and returning the answer set, we apply a Euclidean distance filter to check the pairs coming out of the bucketing filter. Figure 8 shows a clear difference in runtime when running our pipeline with and without this distance filter. Since computing the true correlation on the full-dimensional windows is more costly than doing so on windows with a reduced dimensionality, it is beneficial to first filter out more false positives. Since the bucketing phase has already removed many non-qualifying pairs, we can use a dimension count of ${k}_{e} = {30}$ to obtain a better accuracy.

6.2.3 过滤与优化。在计算真实相关性并返回结果集之前，我们采用欧氏距离过滤器对桶式过滤器输出的配对进行校验。图8展示了启用与禁用该距离过滤器时运行时间的显著差异。由于在全维度窗口上计算真实相关性比降维窗口成本更高，优先过滤更多误报对能有效提升效率。鉴于分桶阶段已排除大量不合格配对，可采用${k}_{e} = {30}$维计数以获得更优精度。

<!-- Media -->

<!-- figureText: without ED with ED Synthetic dataset 0.95 Correlation threshold(T) Total runtime (sec) 100 0 0.85 0.9 -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_14.jpg?x=610&y=1305&w=356&h=332&r=0"/>

Fig. 8. Total runtime of CorrJoin with and without Euclidean distance computations

图8. 启用与禁用欧氏距离计算时CorrJoin的总运行时间对比

<!-- Media -->

We now turn to optimizing parameters ${k}_{s}$ and ${k}_{e}$ ,the dimensionality used for SVD and the Euclidean distance filter,respectively. This involves various trade-offs. Increasing ${k}_{s}$ leads to a more accurate approximation of the windows at the cost of increased computational overhead for SVD. On the other hand,increasing ${k}_{e}$ results in fewer candidate pairs at the cost of increased computational overhead for the distance filtering. Figure 9 shows a heatmap of the runtime of our approach for a range of values for ${k}_{s}$ and ${k}_{e}$ . We observe the best performance of CorrJoin for ${k}_{s} = {15}$ and ${k}_{e} = {30}$ . These are the values we use for the remainder of our experiments. As an extreme case, we generate a random dataset whose series exhibit a random behavior, for which there is a change in the optimal values for ${k}_{s}$ and ${k}_{e}$ . However,such a dataset is rarely seen in a real world application and even if it does appear, it only changes the optimal parameter values slightly.

接下来我们优化参数${k}_{s}$(SVD降维数)和${k}_{e}$(欧氏距离过滤器)，这涉及多重权衡。增加${k}_{s}$能提升窗口近似精度，但会加重SVD计算负担；而增大${k}_{e}$可减少候选对数，却会增加距离过滤的计算开销。图9热力图展示了不同${k}_{s}$和${k}_{e}$取值下的算法运行时间，实验表明当取值为${k}_{s} = {15}$和${k}_{e} = {30}$时CorrJoin性能最优，后续实验均采用该参数组合。极端情况下，我们生成具有随机行为的测试数据集时，${k}_{s}$和${k}_{e}$的最优值会发生变化，但此类数据在真实场景中极为罕见，即便出现也仅会引起参数值的微小波动。

<!-- Media -->

<!-- figureText: 50 50 50 Best Best 30 30 20 20 10 10 40 50 10 20 50 20 50 (c) Stock (d) Gas (e) Random 40 Best 40 Best 40 Best 30 30 30 20 20 20 10 50 50 (a) Synthetic (b) Chlorine -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_15.jpg?x=117&y=263&w=1300&h=329&r=0"/>

Fig. 9. Heatmap of the runtime of CorrJoin with varying ${k}_{s}$ and ${k}_{e}$

图9. CorrJoin在不同${k}_{s}$和${k}_{e}$取值下的运行时间热力图

<!-- Media -->

### 6.3 Performance

### 6.3 性能表现

We compare the performance of CorrJoin with the baseline approach $\operatorname{Inc}{P}^{PAA}$ and its competitors $\epsilon$ - kdB tree and Quickjoin. We look at the total runtime and pruning rates (join and overall), investigate the impact of low correlation thresholds,and follow up by showing the effects of increasing $m$ ,the number of time series,and increasing $n$ ,the size of the windows. Finally,we demonstrate how to improve the performance of Quickjoin by applying dimensionality-reduction techniques to it.

我们将CorrJoin与基线方法$\operatorname{Inc}{P}^{PAA}$及其竞争算法$\epsilon$(kdB树与Quickjoin)进行性能对比，评估指标包括总运行时间、剪枝率(连接剪枝率与整体剪枝率)，探究低相关阈值的影响，并展示时间序列数量$m$和窗口尺寸$n$增大时的效果。最后演示如何通过降维技术提升Quickjoin的性能。

6.3.1 Total Runtime and Pruning Rate. Figure 10 compares the runtime and pruning rates of our approach to the $\epsilon$ -kdB tree,TSUBASA and the baseline approach (the join pruning rate is indicated with a dashed line,the overall pruning rate with a solid line). The $\epsilon$ -kdB tree executes the join by choosing the dimensions for partitioning the data sequentially one by one until no further partitioning is possible. Essentially, this means that each dimension is chosen randomly and the results in Figure 10 reflect this: they look very similar to the results for $C{J}^{\text{Rand }}$ in Figure 5 and confirm that the random selection of dimensions does not work for high-dimensional data. On the other hand, TSUBASA has a similar time complexity as the naive IncP since it does not reduce the number of pairwise comparisons. Since the $\epsilon$ -kdB tree and TSUBASA are an order of magnitude worse than the baseline approach, we drop them from further investigation and focus on the comparison of CorrJoin with Quickjoin (to better see the comparison between CorrJoin and $\operatorname{Inc}{P}^{PAA}$ ,look at the first plot in Figure 19).

6.3.1 总运行时间与剪枝率。图10将本方法与$\epsilon$-kdB树、TSUBASA及基线方法进行对比(虚线表示连接剪枝率，实线表示整体剪枝率)。$\epsilon$-kdB树通过逐维随机选择分区维度执行连接，直至无法继续分区，图10结果显示这种随机维度选择策略无法应对高维数据——其表现与图5中$C{J}^{\text{Rand }}$的结果高度相似。TSUBASA由于未减少配对比较次数，其时间复杂度与朴素IncP相当。鉴于$\epsilon$-kdB树和TSUBASA性能较基线方法差一个数量级，后续研究将聚焦CorrJoin与Quickjoin的对比(详细对比参见图19首个子图)。

<!-- Media -->

<!-- figureText: ${IncP}^{PAA}$ $\epsilon$ -kdB TSUBASA $C{I}^{{PAA} + {SVD}}$ Synthetic dataset Pruning rate 0.8 0.6 0.4 0.2 0.95 Correlation threshold(T) Synthetic dataset Total runtime (sec) ${10}^{3}$ ${10}^{2}$ ${10}^{1}$ Correlation threshold(T) -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_15.jpg?x=439&y=1453&w=695&h=335&r=0"/>

Fig. 10. Total runtime and pruning rate of CorrJoin, $\epsilon$ -kdB tree and TSUBASA

图10. CorrJoin、$\epsilon$-kdB树与TSUBASA的总运行时间及剪枝率

<!-- Media -->

We also look into the performance of $\epsilon$ -kdB tree for different numbers of dimensions (n) in Figure 11. For a very low number of dimensions $\left( {n \leq  {30}}\right)$ ,the $\epsilon$ -kdB tree shows a better performance than CorrJoin, but it quickly starts to deteriorate as the number of dimensions grows.

图11进一步研究了$\epsilon$-kdB树在不同维度数(n)下的表现。当维度数$\left( {n \leq  {30}}\right)$极低时，$\epsilon$-kdB树性能优于CorrJoin，但随着维度增长其性能迅速恶化。

We continue by running CorrJoin and Quickjoin on three different real-world datasets: chlorine, stock, and gas, which exhibit a low, medium, and high variability, respectively. Figure 12 shows the total runtime and pruning rates for the chlorine, stock, and gas dataset. CorrJoin performs very well on the datasets exhibiting a low or medium variability (chlorine and stock), since in these cases PAA provides a more accurate approximation of the windows in the reduced dimensionality space. PAA is able to preserve the overall shape of a time series more accurately, which in turn makes the output of SVD in the following step more accurate as well. In terms of the runtime, CorrJoin outperforms Quickjoin by a factor of up to 22 and Inc ${P}^{PAA}$ by up to an order of magnitude $\left( {T \geq  {0.95}}\right)$ . Dimensionality reduction techniques do not work as well for datasets with a high variability (this is a worst case for our technique). This is why for gas dataset, the pruning rate of CorrJoin is not better than the pruning rate of Quickjoin, but in terms of performance, CorrJoin still performs better than all the other algorithms. It is interesting to note that Quickjoin's performance for all three datasets is worse than that of the baseline approach.

我们继续在三个真实数据集（氯气、股票和天然气）上运行CorrJoin和Quickjoin算法，这些数据集分别呈现低、中、高波动性。图12展示了三个数据集的总运行时间和剪枝率。CorrJoin在低/中波动性数据集（氯气和股票）上表现优异，因为PAA（分段聚合近似）能在降维空间中更精确地近似时间窗口。PAA能更准确地保留时间序列的整体形态，从而使得后续SVD（奇异值分解）的输出也更精确。就运行时间而言，CorrJoin比Quickjoin快22倍，比Inc${P}^{PAA}$快一个数量级$\left( {T \geq  {0.95}}\right)$。降维技术对高波动性数据集（本技术的极限情况）效果欠佳，因此天然气数据集的剪枝率未优于Quickjoin，但性能仍超越所有其他算法。值得注意的是，Quickjoin在三个数据集上的表现均弱于基线方法。

<!-- Media -->

<!-- figureText: ${Inc}{P}^{PAA}$ $\epsilon$ -kdB $C{I}^{{PAA} + {SVD}}$ Synthetic dataset Speedup 30 500 1000 1500 Window size(n) Synthetic dataset Total runtime (sec) $T = {0.9}$ ,500 ,000 500 500 1000 1500 Window size(n) -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_16.jpg?x=450&y=261&w=670&h=339&r=0"/>

Fig. 11. The $\epsilon$ -kdB tree performs better for very small $n$ ,but quickly starts to deteriorate as $n$ increases

图11. $\epsilon$-kdB树在极小$n$值时表现良好，但随着$n$增大性能迅速下降

<!-- figureText: ${\operatorname{IncP}}^{PAA}$ Quickjoin CI ${}^{{PAA} + {SVD}}$ Chlorine dataset 1 Pruning rate 0.8 0.4 0.2 0.85 0.9 0.95 Correlation threshold(T) Stock dataset 1 Pruning rate 0.8 0.4 0.2 0.85 0.9 0.95 Correlation threshold(T) Gas dataset 1 Pruning rate 0.8 0.4 0.2 0.85 0.9 0.95 Correlation threshold(T) Chlorine dataset 600 Total runtime (sec) 400 200 0.85 0.9 0.95 Correlation threshold(T) Stock dataset Total runtime (sec) 400 200 0.85 0.9 0.95 Correlation threshold(T) Gas dataset Total runtime (sec) 500 ,000 500 0.85 0.9 0.95 Correlation threshold(T) -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_16.jpg?x=446&y=1173&w=676&h=876&r=0"/>

Fig. 12. Total runtime and pruning rate of the approaches for different datasets

图12. 不同数据集上各方法的总运行时间及剪枝率

<!-- Media -->

Overall, the pruning rate of CorrJoin is better than that of Quickjoin, explaining part of the difference in runtime. Due to the application of dimensionality-reduction techniques, CorrJoin can also improve the runtime of the correlation computation compared to Quickjoin.

总体而言，CorrJoin的剪枝率优于Quickjoin，这解释了部分运行时差异。由于采用降维技术，CorrJoin在相关性计算方面的运行效率也高于Quickjoin。

6.3.2 Memory Usage. Figure 13 shows the memory usage of the different approaches. Inc ${P}^{PAA}$ uses less memory than our approach and Quickjoin, since it does not reduce the number of comparisons and, thus, does not need additional space for doing so. Nevertheless, CorrJoin only uses around 18 Megabytes more than IncP ${}^{PAA}$ .

6.3.2 内存使用。图13展示了各方法的内存占用情况。Inc${P}^{PAA}$因无需减少比较次数而不需要额外存储空间，内存占用低于本方法和Quickjoin。但CorrJoin仅比IncP${}^{PAA}$多占用约18兆字节。

<!-- Media -->

<!-- figureText: Input data Preprocessing Partitioning Corr Synthetic dataset 100 Memory usage (MB) 80 40 20 -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_17.jpg?x=507&y=595&w=554&h=350&r=0"/>

Fig. 13. Memory usage of different approaches

图13. 各方法内存使用对比

<!-- Media -->

6.3.3 Low Correlation Thresholds. We now investigate the impact of lower correlation rates. Previous work on determining pairwise correlation $\left\lbrack  {9,{30},{35},{42},{57},{58}}\right\rbrack$ looked almost exclusively at high or very high correlation thresholds (0.9 or higher). While this covers an important category of applications, there are application domains, such as the social sciences, in which correlated pairs reach a correlation threshold of at most 0.5 or 0.6 [22]. The correlation threshold has a direct impact on the parameter $\epsilon$ used for the Euclidean distance: $\epsilon$ determines the width of the buckets. The higher the correlation threshold,the lower $\epsilon$ ,which spreads out the windows among more buckets, resulting in a smaller number of windows found in neighboring buckets. Figure 14 shows the effects of varying the correlation threshold for the different real-world datasets. For the chlorine dataset with a low variability, although CorrJoin also experiences a slowdown and lower join pruning rate of the bucketing filter due to a larger answer set, it is still much faster than the other approaches. For the medium and high variability datasets, stock and gas, CorrJoin still has an edge over the other approaches in terms of performance.

6.3.3 低相关性阈值。我们研究了较低相关性阈值的影响。既往关于成对相关性$\left\lbrack  {9,{30},{35},{42},{57},{58}}\right\rbrack$的研究多聚焦高阈值（0.9以上），虽然这覆盖了重要应用场景，但社会科学等领域相关对的阈值通常仅为0.5或0.6[22]。相关性阈值直接影响欧氏距离参数$\epsilon$——该参数决定桶宽度：阈值越高，$\epsilon$越小，时间窗口分布到更多桶中，导致相邻桶内窗口数减少。图14展示了不同真实数据集上阈值变化的影响。对于低波动性的氯气数据集，虽然CorrJoin因结果集扩大导致桶过滤减速和剪枝率降低，但仍显著快于其他方法。在中高波动性的股票和天然气数据集上，CorrJoin性能仍具优势。

The reason for the performance degradation of the algorithms is the growing size of the answer set for decreasing correlation thresholds, leading to higher costs for generating this set. Even the performance of the baseline approach, which uses a nested-loop strategy, suffers, as more pairs meet the thresholds and have to be checked. For join-based approaches, which avoid computing the Cartesian product, this also has another impact, as the techniques used to filter out non-correlated pairs will pay off less and less. We generated synthetic datasets with different correlation rates, i.e., the proportion of correlated pairs compared to the Cartesian product, to measure the effect of a growing answer set. Figure 15 illustrates the speedup of CorrJoin and Quickjoin compared to the baseline approach. For datasets with low variability (such as the synthetic dataset), in absolute numbers the performance of Quickjoin and Inc ${P}^{PAA}$ deteriorates very similarly when increasing the size of the answer set (cf. top left diagram of Figure 14). For the speedup factor, this means that it will actually increase, since Quickjoin's performance is worse than the baseline approach (which also causes it to stay below one). For CorrJoin, once the rate reaches around 20%, the difference in performance between CorrJoin and IncP ${}^{PAA}$ is not discernible anymore.

算法性能下降的原因在于，随着相关性阈值降低，答案集的规模不断扩大，导致生成该集合的成本升高。即便是采用嵌套循环策略的基线方法，其性能也会因更多数据对满足阈值而需被检测而受损。对于避免计算笛卡尔积的基于连接的方法而言，这还带来另一重影响：用于过滤非相关对的技术收益将逐渐递减。我们通过生成具有不同相关率（即相关对占笛卡尔积的比例）的合成数据集，量化了答案集规模扩大的影响。图15展示了CorrJoin与Quickjoin相较基线方法的加速比。在低变异性的数据集（如合成数据集）中，当答案集规模增大时，Quickjoin与Inc${P}^{PAA}$的绝对性能退化程度高度相似（参见图14左上子图）。这意味着加速因子实际上会上升，因为Quickjoin的表现已劣于基线方法（这也导致其加速比始终低于1）。对于CorrJoin而言，当相关率达到约20%时，其与IncP${}^{PAA}$的性能差异便不可辨识。

<!-- Media -->

<!-- figureText: ${\operatorname{IncP}}^{PAA}$ Quickjoin $C{I}^{{PAA} + {SVD}}$ Chlorine dataset Pruning rate 0.8 0.6 0.4 0.2 0.4 0.6 0.8 Correlation threshold(T) Stock dataset Pruning rate 0.8 0.6 0.4 0.2 0.4 0.6 0.8 Correlation threshold(T) Gas dataset Pruning rate 0.8 0.6 0.4 0.2 0.4 0.6 0.8 Correlation threshold(T) Chlorine dataset Total runtime (sec) 0,000 500 0.4 0.6 0.8 1 Correlation threshold(T) Stock dataset Total runtime (sec) ,000 500 0.4 0.6 0.8 Correlation threshold(T) Gas dataset 2,000 Total runtime (sec) 1,500 0.4 0.6 0.8 Correlation threshold(T) -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_18.jpg?x=453&y=260&w=674&h=878&r=0"/>

Fig. 14. Total runtime and pruning rate for low correlation thresholds.

图14. 低相关性阈值下的总运行时间与剪枝率

<!-- figureText: Quickjoin $C{I}^{{PAA} + {SVD}}$ Synthetic dataset $T = {0.9}$ 0.4 Rate of corr. pairs Speedup 0 0.2 -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_18.jpg?x=596&y=1240&w=383&h=335&r=0"/>

Fig. 15. Speedup for different correlation rates

图15. 不同相关率下的加速比

<!-- Media -->

6.3.4 Stride. One effect of utilizing sliding windows is that we do not have to recompute everything but are able to check for correlation incrementally. However, the impact of this effect is rather small, as updating the mean value vectors of PAA can be done in linear time. In Figure 16a, we compare the runtime of each method for different values of stride $h$ . We report the results for the synthetic dataset (similar results were obtained for the other datasets). The runtime of all methods only increases (very) slightly.

6.3.4 步长参数。采用滑动窗口的效应在于无需全量重算，而能增量检测相关性。但该效应的影响较小，因为PAA均值向量的更新可在线性时间内完成。图16a对比了不同步长值$h$下各方法的运行时间（合成数据集的结果显示于此，其他数据集结果类似）。所有方法的运行时间仅出现（极）轻微增长。

Another,more important,effect of $h$ is the tradeoff between throughput and latency. The larger the value $h$ ,the higher the latency (as we have to wait for more values to arrive before processing them) and the better the throughput (because we can processe larger batches). Figure 16b shows the maximum number of series we are able to process for different values of $h$ . For larger strides, we can handle more data streams,but have to wait longer for all $h$ values to arrive.

步长参数$h$更重要的影响在于吞吐量与延迟之间的权衡：该值越大，延迟越高（需等待更多数值到达才能处理），但吞吐量越好（因能处理更大批次）。图16b展示了不同步长值$h$下可处理的最大序列数。增大步长能处理更多数据流，但需等待更长时间以获取全部$h$个数值。

<!-- Media -->

<!-- figureText: Synthetic dataset Quickjoin $C{I}^{{PAA} + {SVD}}$ $\cdot  {10}^{4}$ Synthetic dataset Max. number of series 2 1 100 300 400 500 Stride(h) (b) Total runtime (sec) 400 200 100 200 300 400 500 Stride(h) (a) -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_19.jpg?x=441&y=259&w=666&h=366&r=0"/>

Fig. 16. Runtime and maximum number of series which can be handled by each method when increasing stride $h$

图16. 步长$h$增大时各方法的运行时间及可处理的最大序列数

<!-- Media -->

6.3.5 Number of Time Series. Next, we look at the scalability of the different approaches in terms of the number of processed time series. Figure 17 shows the speedup of CorrJoin and Quickjoin compared to the baseline approach. Quickjoin does not perform better than the baseline approach, which results in a speedup of less than 1 . For small values of $m$ ,i.e., $m \leq  {100}$ ,the speedup of CorrJoin is below one, since the overhead of the join operation (i.e., the preprocessing and partitioning of the input data) does not pay off and Inc ${P}^{PAA}$ is faster. As $m$ increases,i.e., $m > {100}$ ,CorrJoin quickly starts to outperform Inc ${P}^{PAA}$ and eventually levels off,asymptotically approaching a limit. The primary factor determining the speedup of CorrJoin over $\operatorname{Inc}{P}^{PAA}$ is that $\operatorname{Inc}{P}^{PAA}$ has to compute the Cartesian product, while CorrJoin eliminates a lot of pairs via the bucketing. The other factors are roughly comparable (cf. Section 5). Calculating the exact costs ${c}_{bkt}$ (as defined in Section 5) is difficult to do analytically. Nevertheless, we can estimate an upper bound for the best possible speedup CorrJoin can hope to achieve. The baseline approach Inc ${P}^{PAA}$ has to compare ${m}^{2}$ pairs. The size of the candidate set produced by CorrJoin is ${m}^{2}{r}_{1}$ ,which means we can lower-bound the number of pairs that were compared in the bucket filter (every pair showing up in the answer set was checked, otherwise it would not have appeared). In turn, this means that the ratio of comparisons between IncP ${}^{PAA}$ and CorrJoin can be upper-bounded,it is $\frac{{m}^{2}}{{m}^{2}{r}_{1}} = \frac{1}{{r}_{1}}$ . Consequently, the speedup of CorrJoin over $\operatorname{Inc}{P}^{PAA}$ is at most $\frac{1}{{r}_{1}}$ . For the setup in Figure 17,the join pruning rate was 0.89,so $\frac{1}{{r}_{1}} = \frac{1}{0.11} \approx  {9.09}$ and the speedup can never go beyond this. It actually leveled off sooner, as this upper bound is quite loose. The insights on scalability re-emphasize the findings depicted in Figure 15. A lower correlation rate among windows leads to a smaller value for ${r}_{1}$ , which in turn results in a higher speedup of CorrJoin compared to $\operatorname{Inc}{P}^{PAA}$ .

6.3.5 时间序列数量。接下来，我们考察不同方法在处理时间序列数量方面的可扩展性。图17展示了CorrJoin和Quickjoin相较于基准方法的加速比。Quickjoin表现未超越基准方法，其加速比始终低于1。当$m$取值较小时（即$m \leq  {100}$），CorrJoin的加速比亦小于1，这是因为连接操作（即输入数据的预处理和分区）的开销未能抵消，此时Inc${P}^{PAA}$更快。随着$m$增大（即$m > {100}$），CorrJoin迅速超越Inc${P}^{PAA}$并最终趋于平缓，渐近逼近极限值。CorrJoin相较$\operatorname{Inc}{P}^{PAA}$的加速优势主要源于：$\operatorname{Inc}{P}^{PAA}$需计算笛卡尔积，而CorrJoin通过分桶机制消除了大量配对。其他因素大致相当（参见第5节）。精确计算成本${c}_{bkt}$（如第5节定义）难以通过解析实现，但我们可以估算CorrJoin可能达到的最佳加速比上限。基准方法Inc${P}^{PAA}$需要比较${m}^{2}$组配对，而CorrJoin生成的候选集大小为${m}^{2}{r}_{1}$，这意味着我们可以下界估算桶过滤器中比较的配对数（答案集中出现的每个配对都经过验证，否则不会出现）。因此，IncP${}^{PAA}$与CorrJoin的比较次数比上限为$\frac{{m}^{2}}{{m}^{2}{r}_{1}} = \frac{1}{{r}_{1}}$，故CorrJoin相较$\operatorname{Inc}{P}^{PAA}$的加速比最大不超过$\frac{1}{{r}_{1}}$。在图17实验设置中，连接剪枝率为0.89，因此$\frac{1}{{r}_{1}} = \frac{1}{0.11} \approx  {9.09}$，加速比永不可能突破该值。实际上加速比更早趋于平缓，因为该上限较为宽松。这些可扩展性分析再次印证了图15的结论：窗口间较低的相关性会导致${r}_{1}$取值较小，从而使CorrJoin相较$\operatorname{Inc}{P}^{PAA}$获得更高加速比。

<!-- Media -->

<!-- figureText: ${Inc}{P}^{PAA}$ Quickjoin $C{J}^{{PAA} + {SVD}}$ Synthetic dataset 3 Speedup 5000 15000 25000 Number of series(m) Synthetic dataset Total runtime (sec) 6,000 ${T}^{\prime } = {0.9}$ 4,000 5000 15000 25000 Number of series(m) -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_19.jpg?x=449&y=1666&w=668&h=336&r=0"/>

Fig. 17. The benefit of our approach becomes more pronounced for larger $m$

图17. 当$m$取值较大时，本方法的优势更加显著

<!-- Media -->

6.3.6 Window Size. Finally,we consider the impact of the size of the original windows, $n$ ,on the performance of the join algorithms (see Figure 18 for an illustration). As the window size increases, the runtime of CorrJoin and Quickjoin goes up. For CorrJoin this does not happen entirely evenly, but accelerates slightly with larger window sizes: for $n = {5000}$ ,CorrJoin has a runtime of 105 seconds,for $n = {10000}$ ,one of 235 seconds. There are multiple causes for the increasing runtime and its unevenness. Clearly, larger windows take more time to process: this concerns the reduction of the dimensionality at the very beginning of the pipeline and the final filter step, which is run on the original windows again. As we can see on the right-hand side of Figure 18, $n$ also has an effect on the pruning rate. Since the dimensionality of the windows in the intermediate steps $\left( {{k}_{s},{k}_{e}}\right.$ ,and $\left. {k}_{b}\right)$ does not change,we are reducing larger and larger windows to the same (small) dimensionalities, which means that we lose more and more accuracy. Then there is also the effect of distance concentration for high-dimensional data, i.e., the higher the dimension, the closer the distance of a point to its closest neighbor becomes to the distance to its farthest neighbor. Thus, the effectiveness of separating windows into different buckets based on their distances from each other diminishes for very high dimensions. In contrast, Quickjoin's runtime increases steeply right from the start,as the window size $n$ directly leads to higher costs for comparing window pairs.

6.3.6 窗口尺寸。最后我们考察原始窗口尺寸$n$对连接算法性能的影响（如图18所示）。随着窗口尺寸增大，CorrJoin和Quickjoin的运行时间均呈上升趋势。CorrJoin的运行时间增长并非完全线性，而是在较大窗口尺寸时略微加速：当$n = {5000}$时运行时间为105秒，$n = {10000}$时增至235秒。运行时间增长及其不均衡性由多重因素导致：较大窗口需要更长的预处理时间（包括流程初始阶段的降维操作和最终需在原窗口上执行的过滤步骤）；如图18右侧所示，$n$还会影响剪枝率——由于中间步骤$\left( {{k}_{s},{k}_{e}}\right.$和$\left. {k}_{b}\right)$中的窗口维度不变，将越来越大的窗口降维至相同（较小）维度会导致精度持续损失；此外还存在高维数据的距离集中效应，即维度越高，最近邻与最远邻的距离差异越小，导致基于窗口间距的分桶策略在高维场景下效果衰减。相比之下，Quickjoin运行时间从开始就急剧上升，因为窗口尺寸$n$直接导致窗口对比较成本增加。

Thus, while the costs for CorrJoin rise with increasing window size, our method is feasible in practice, especially in the range of window sizes typical for applications.

因此，虽然CorrJoin成本随窗口尺寸增加而上升，但该方法在实际应用场景中（特别是典型窗口尺寸范围内）仍具可行性。

<!-- Media -->

<!-- figureText: ${Inc}{P}^{PAA}$ Quickjoin $C{I}^{{PAA} + {SVD}}$ Synthetic dataset 1 Pruning rate 0.8 0.6 0.4 0.2 0 1000 5000 10000 Window size(n) Synthetic dataset Total runtime (sec) $T = {0.9}$ 1,000 500 1000 5000 10000 Window size(n) -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_20.jpg?x=449&y=999&w=666&h=339&r=0"/>

Fig. 18. Total runtime and pruning rate of the approaches with increasing window size $n$

图18. 各方法在窗口尺寸$n$递增时的总运行时间及剪枝率

<!-- Media -->

6.3.7 Improving Quickjoin. Implementing Quickjoin as described by Jacox and Samet [20] leads to an algorithm that spends a substantial amount of its computations for checking distances between window pairs. This becomes apparent when increasing the window size (cf. Figure 18). One way to improve Quickjoin is by leveraging the first part of our approach and reducing the dimensionalities of the windows using PAA and filtering via the dimensionality-reduced windows. In Figure 19, we show the results of comparing the improved Quickjoin (Quickjoin+) with CorrJoin and IncP ${}^{PAA}$ . While applying PAA to Quickjoin certainly boosts its performance it is still clearly outperformed by CorrJoin (see left-hand column of Figure 19). Only for very high correlation thresholds does Quickjoin+ reach the same performance level as CorrJoin. Note that for very high correlation thresholds, algorithms generally show a good performance, i.e., this case is easy to handle. For low correlation thresholds,Quickjoin+ is even slightly slower than Inc ${P}^{PAA}$ . When looking at the pruning rate of Quickjoin+ and CorrJoin, we see that the first pruning done by CorrJoin via the bucketing already results in a better rate than Quickjoin+'s partitioning.

6.3.7 Quickjoin优化。Jacox和Samet[20]提出的Quickjoin实现将大量计算资源消耗在窗口对距离检测上（窗口尺寸增大时尤为明显，见图18）。改进方案之一是借鉴本方法前半部分，通过PAA降维并基于降维窗口进行过滤。图19显示优化版Quickjoin（Quickjoin+）与CorrJoin及IncP${}^{PAA}$的对比结果：虽然PAA提升了Quickjoin性能，但其仍明显逊色于CorrJoin（见图19左列）。仅当相关性阈值极高时Quickjoin+才能达到CorrJoin水平（但该场景本身易处理）；低阈值时其速度甚至略低于Inc${P}^{PAA}$。对比剪枝率可发现，CorrJoin通过分桶实现的首次剪枝已优于Quickjoin+的分区效果。

As a final improvement, we apply both PAA and SVD to Quickjoin and then run Quickjoin's partitioning on windows whose dimensionality is the same as that of CorrJoin's windows, i.e., the preprocessing now mirrors that of CorrJoin. While this improvement (shown as Quickjoin++ in Figure 20) improves the pruning rate of Quickjoin further and, in turn, its runtime, it is still slower than CorrJoin. We identified two aspects in which Quickjoin still differs from CorrJoin. First, in contrast to CorrJoin, which can directly assign the dimensionality-reduced windows to buckets, Quickjoin has to compare all windows in the current partition to a pivot. This results in a higher total number of comparisons between windows (see Figure 20). Second, CorrJoin achieves a more accurate partitioning for the filtering: it uses buckets with a width of exactly $\epsilon$ . Using a smaller value would mean we could not rule out pairs from non-neighboring buckets, as buckets of a distance of up to two positions could still contain matching pairs. On the other hand, using a larger value would lead to checking additional pairs in wider neighboring buckets that could have been ruled out with narrow buckets. Since Quickjoin partitions the data on the fly with pivots, it is highly unlikely that it ends up with partitions that have a width of exactly $\epsilon$ . Therefore,compared to CorrJoin, it has to check additional pairs.

最终我们为Quickjoin同时应用PAA和SVD预处理，使其在降维至与CorrJoin相同维度的窗口上执行分区（如图20中Quickjoin++所示）。虽然该改进进一步提升了剪枝率和运行速度，但仍慢于CorrJoin。差异主要来自两方面：其一，CorrJoin可直接将降维窗口分配至桶中，而Quickjoin需将当前分区所有窗口与枢轴点比较，导致总比较次数更高（见图20）；其二，CorrJoin采用精确宽度为$\epsilon$的桶实现更精准过滤——更小宽度会导致无法排除非相邻桶对（间距两位置的桶仍可能包含匹配对），更大宽度则需检查本可被窄桶排除的邻接桶对。由于Quickjoin动态枢轴分区机制，其几乎不可能生成精确宽度为$\epsilon$的分区，因此必须额外检查更多窗口对。

<!-- Media -->

<!-- figureText: ${IncP}^{PAA}$ Quickjoin- $C{I}^{{PAA} + {SVD}}$ Synthetic dataset Pruning rate 0.8 0.6 0.4 0.2 0.7 0.8 0.9 Correlation threshold(T) Chlorine dataset Pruning rate 0.8 0.6 0.4 0.2 0.7 0.8 0.9 Correlation threshold(T) Stock dataset Pruning rate 0.8 0.6 0.4 0.2 0.7 0.8 0.9 Correlation threshold(T) Gas dataset Pruning rate 0.8 0.6 0.4 0.2 0.7 0.8 0.9 Correlation threshold(T) Synthetic dataset Total runtime (sec) 100 0.8 0.9 1 Correlation threshold(T) Chlorine dataset 1,000 Total runtime (sec) 800 600 400 200 0.8 0.9 1 Correlation threshold(T) Stock dataset Total runtime (sec) 100 0.7 0.8 0.9 1 Correlation threshold(T) Gas dataset Total runtime (sec 1,500 1,000 500 0.7 0.8 0.9 Correlation threshold(T) -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_21.jpg?x=449&y=259&w=678&h=1146&r=0"/>

Fig. 19. Total runtime and pruning rate of IncP ${}^{PAA}$ , CorrJoin, and the improved Quickjoin+ (which is still worse than CorrJoin)

图19. IncP${}^{PAA}$、CorrJoin及改进版Quickjoin+（仍逊于CorrJoin）的总运行时间与剪枝率

<!-- figureText: ${IncP}^{PAA}$ Quickjoin++ $C{I}^{{PAA} + {SVD}}$ Synthetic dataset 1 Pruning rate 0.8 0.6 0.4 0.2 0.7 0.8 0.9 Correlation threshold(T) Synthetic dataset Total runtime (sec) 200 100 0.7 0.8 0.9 1 Correlation threshold(T) $\cdot  {10}^{7}$ Synthetic dataset Number of comparisons 0.5 0.7 0.8 0.9 1 Correlation threshold(T) -->

<img src="https://cdn.noedgeai.com/01965994-2da5-70dd-884b-2c65cf652f1a_22.jpg?x=457&y=259&w=661&h=609&r=0"/>

Fig. 20. Total runtime and pruning rate of IncP ${}^{PAA}$ , CorrJoin, and Quickjoin++ (employing both PAA and SVD)

图20. IncP${}^{PAA}$、CorrJoin及采用PAA与SVD双重技术的Quickjoin++总运行时间与剪枝率

<!-- Media -->

## 7 CONCLUSION AND FUTURE WORK

## 7 结论与未来工作

We propose CorrJoin, an efficient and effective algorithm for determining all pairs of data stream windows whose correlation is above a certain threshold. We carefully combine PAA, SVD, bucketing, and Euclidean distance filters to a novel and powerful reduce-filter-refine algorithm that leverages the complementary properties of dimension reduction and transformation to build a highly effective bucketing filter that outperforms state-of-the-art solution for this task. We illustrate its effectiveness and efficiency in an empirical evaluation using several real-world datasets, showing that CorrJoin outperforms state-of-the-art algorithms. It reduces the number of needed comparisons by at least ${80}\%$ and is several times faster than state-of-the-art algorithms and the baseline approach. Additionally, we exhibit how parts of our technique can even be applied to Quickjoin, one of our competitors, to boost its performance. While it is still (slightly) slower than our algorithm, it can now consistently outperform the baseline. This demonstrates that our approach has the potential to be applicable on a more general level.

我们提出CorrJoin算法，这是一种高效精准的方法，用于确定所有相关性超过特定阈值的数据流窗口对。通过巧妙整合PAA（分段聚合近似）、SVD（奇异值分解）、分桶技术和欧氏距离过滤器，我们构建出全新的"缩减-过滤-优化"流程。该算法充分发挥降维技术与转换方法的互补优势，所构建的分桶过滤器性能超越当前最优解决方案。基于多组真实数据集的实证评估表明，CorrJoin较现有最优算法至少减少${80}\%$次比较操作，速度提升数倍。值得注意的是，本技术的部分模块甚至可应用于竞争算法Quickjoin，使其性能突破基线水平（虽仍略逊于本算法），这证明我们的方法具有更广泛的适用潜力。

For future work, we plan to investigate the following aspects. The bucketing approach makes it possible to adapt our algorithm to distributed processing, for instance executing it in a map-reduce framework. The preprocessing involving PAA and SVD can be done independently for each window in the map step, while the bucketing determines in the shuffle step which reducers receive which potential window pairs to check for correlation. We also believe that our approach is applicable to high-dimensional similarity joins using dimensionality reduction techniques that lower-bound the true distance and plan to take a closer look at this setting.

未来工作将聚焦以下方向：分桶机制使算法适配分布式处理（如MapReduce框架），其中PAA与SVD预处理可在map阶段对各窗口独立执行，而shuffle阶段则通过分桶确定需校验相关性的窗口对分配方案。我们相信该方法同样适用于采用降维技术的高维相似性连接查询，这些技术能保持对真实距离的下界估计，后续将重点研究这一应用场景。

## REFERENCES

## 参考文献

[1] Pankaj K. Agarwal, Xiao Hu, Stavros Sintos, and Jun Yang. 2021. Dynamic Enumeration of Similarity Joins. In 48th Int. Colloquium on Automata, Languages, and Programming, ICALP 2021, July 12-16, 2021, Glasgow, Scotland (Virtual Conference) (LIPIcs). https://doi.org/10.4230/LIPIcs.ICALP.2021.11

[2] Rakesh Agrawal, Christos Faloutsos, and Arun Swami. 1993. Efficient Similarity Search In Sequence Databases. In Proceedings of the FODO Conference. 69-84.

[3] Dror Aiger, Haim Kaplan, and Micha Sharir. 2014. Reporting Neighbors in High-Dimensional Euclidean Space. SIAM J. Comput. 43, 4 (jan 2014), 1363-1395. https://doi.org/10.1137/12089867X

[4] Paul Bottinelli and Joppe Bos. 2016. Computational aspects of correlation power analysis. Journal of Cryptographic Engineering 7 (February 2016). https://doi.org/10.1007/s13389-016-0122-9

[5] Yuhan Cai and Raymond Ng. 2004. Indexing Spatio-Temporal Trajectories with Chebyshev Polynomials. In Proceedings of the 2004 ACM SIGMOD International Conference on Management of Data (Paris, France) (SIGMOD '04). Association for Computing Machinery, New York, NY, USA, 599-610. https://doi.org/10.1145/1007568.1007636

[6] Alessandro Camerra, Jin Shieh, Themis Palpanas, Thanawin Rakthanmanon, and Eamonn Keogh. 2014. Beyond one billion time series: Indexing and mining very large time series collections with iSAX2+. Knowledge and Information Systems (2014).

[7] Kaushik Chakrabarti, Eamonn Keogh, Sharad Mehrotra, and Michael Pazzani. 2002. Locally Adaptive Dimensionality Reduction for Indexing Large Time Series Databases. ACM Trans. Database Syst. 27, 2 (June 2002), 188-228. https: //doi.org/10.1145/568518.568520

[8] Qiuxia Chen, Lei Chen, Xiang Lian, Yunhao Liu, and Jeffrey Xu Yu. 2007. Indexable PLA for Efficient Similarity Search. In Proceedings of the 33rd International Conference on Very Large Data Bases (Vienna, Austria) (VLDB '07). VLDB Endowment, 435-446.

[9] Richard Cole, Dennis Shasha, and Xiaojian Zhao. 2005. Fast Window Correlations over Uncooperative Time Series. In Proceedings of the Eleventh ACM SIGKDD International Conference on Knowledge Discovery in Data Mining (KDD '05). Association for Computing Machinery, New York, NY, USA, 743-749. https://doi.org/10.1145/1081870.1081966

[10] Akash Das Sarma, Yeye He, and Surajit Chaudhuri. 2014. ClusterJoin: A Similarity Joins Framework Using Map-Reduce. Proc. VLDB Endow. (aug 2014), 1059-1070.

[11] Hui Ding, Goce Trajcevski, Peter Scheuermann, Xiaoyue Wang, and Eamonn Keogh. 2008. Querying and Mining of Time Series Data: Experimental Comparison of Representations and Distance Measures. Proc. VLDB Endow. (aug 2008), ${1542} - {1552}$

[12] Karima Echihabi. 2019. Truly Scalable Data Series Similarity Search. In Proceedings of the VLDB 2019 PhD Workshop, co-located with the 45th International Conference on Very Large Databases (VLDB 2019), Los Angeles, California, USA, August 26-30, 2019 (CEUR Workshop Proceedings). CEUR-WS.org.

[13] Karima Echihabi, Kostas Zoumpatianos, Themis Palpanas, and Houda Benbrahim. 2018. The Lernaean Hydra of Data Series Similarity Search: An Experimental Evaluation of the State of the Art. Proc. VLDB Endow. (oct 2018), 112-127.

[14] C. Faloutsos, H. Jagadish, A. Mendelzon, and T. Milo. 1997. A Signature Technique for Similarity-Based Queries. In Proceedings of the Compression and Complexity of Sequences 1997 (SEQUENCES '97). IEEE Computer Society, USA.

[15] Christos Faloutsos, M. Ranganathan, and Yannis Manolopoulos. 1994. Fast Subsequence Matching in Time-Series Databases. In Proceedings of the 1994 ACM SIGMOD International Conference on Management of Data (Minneapolis, Minnesota, USA) (SIGMOD '94). Association for Computing Machinery, New York, NY, USA, 419-429. https://doi.org/ 10.1145/191839.191925

[16] Like Gao and X. Sean Wang. 2002. Continually Evaluating Similarity-Based Pattern Queries on a Streaming Time Series. In Proceedings of the 2002 ACM SIGMOD International Conference on Management of Data (Madison, Wisconsin) (SIGMOD '02). Association for Computing Machinery, New York, NY, USA, 370-381. https://doi.org/10.1145/564691.564734

[17] Anna Gogolou, Theophanis Tsandilas, Karima Echihabi, Anastasia Bezerianos, and Themis Palpanas. 2020. Data Series Progressive Similarity Search with Probabilistic Quality Guarantees. In Proceedings of the 2020 ACM SIGMOD International Conference on Management of Data (SIGMOD '20). Association for Computing Machinery, 1857-1873.

[18] Tian Guo, Jean-Paul Calbimonte, H. Zhuang, and Karl Aberer. 2015. SigCO: Mining Significant Correlations via a Distributed Real-time Computation Engine. In 2015 IEEE International Conference on Big Data, Big Data 2015.747-756.

[19] Alfred Haar. [n. d.]. On the Theory of Orthogonal Function Systems. https://pdfs.semanticscholar.org/3b08/ b61ba914626db518b6add5b73ac21d62f0c1.pdf.

[20] Edwin H. Jacox and Hanan Samet. 2008. Metric Space Similarity Joins. ACM Trans. Database Syst. (jun 2008), 38 pages.

[21] Li Junkui and Wang Yuanzhen. 2007. APCAS: An Approximate Approach to Adaptively Segment Time Series Stream. In Proceedings of the Joint 9th Asia-Pacific Web and 8th International Conference on Web-Age Information Management Conference on Advances in Data and Web Management (Huang Shan, China) (APWeb/WAIM'07). Springer-Verlag, Berlin, Heidelberg, 554-565.

[22] Daniel Kahnemann, Olivier Sibony, and Cass Sunstein. 2021. Noise: A Flaw in Human Judgment. Little, Brown Spark, New York.

[23] E. Keogh. 1997. Fast similarity search in the presence of longitudinal scaling in time series databases. In Proceedings Ninth IEEE International Conference on Tools with Artificial Intelligence. 578-584.

[24] Eamonn Keogh, Kaushik Chakrabarti, Michael Pazzani, and Sharad Mehrotra. 2001. Dimensionality Reduction for Fast Similarity Search in Large Time Series Databases. Knowledge and Information Systems 3 (August 2001), 263-286.

[25] Mourad Khayati. 2015. Recovery of Missing Values using Matrix Decomposition Techniques. Ph. D. Dissertation. University of Zurich.

[26] Kin-Pong Chan and Ada Wai-Chee Fu. 1999. Efficient Time Series Matching by Wavelets. In Proceedings of the 15th International Conference on Data Engineering (ICDE '99). IEEE Computer Society, USA, 126.

[27] Haridimos Kondylakis, Niv Dayan, Kostas Zoumpatianos, and Themis Palpanas. 2018. Coconut: a scalable bottom-up approach for building data series indexes. Proceedings of the VLDB Endowment (02 2018), 677-690.

[28] Flip Korn, H. V. Jagadish, and Christos Faloutsos. 1997. Efficiently Supporting Ad Hoc Queries in Large Datasets of Time Sequences. In Proceedings of the 1997 ACM SIGMOD International Conference on Management of Data (Tucson, Arizona, USA) (SIGMOD '97). Association for Computing Machinery, New York, NY, USA, 289-300. https://doi.org/10.1145/253260.253332

[29] Chung-Sheng Li, Philip S. Yu, and Vittorio Castelli. 1996. HierarchyScan: A Hierarchical Similarity Search Algorithm for Databases of Long Sequences. In Proceedings of the Twelfth International Conference on Data Engineering. IEEE Computer Society, 546-553. https://doi.org/10.1109/ICDE.1996.492205

[30] Yuhong Li, Leong Hou U, Man Lung Yiu, and Zhiguo Gong. 2013. Discovering Longest-Lasting Correlation in Sequence Databases. Proc. VLDB Endow. 6, 14 (September 2013), 1666-1677. https://doi.org/10.14778/2556549.2556552

[31] X. Lian and L. Chen. 2008. Efficient Similarity Search over Future Stream Time Series. IEEE Transactions on Knowledge & Data Engineering 20, 01 (January 2008), 40-54. https://doi.org/10.1109/TKDE.2007.190666

[32] Xiang Lian, Lei Chen, Jeffrey Xu Yu, Jinsong Han, and Jian Ma. 2009. Multiscale Representations for Fast Pattern Matching in Stream Time Series. IEEE Trans. on Knowl. and Data Eng. 21, 4 (April 2009), 568-581. https://doi.org/10.1109/TKDE.2008.184

[33] Michele Linardi and Themis Palpanas. 2018. Scalable, Variable-Length Similarity Search in Data Series: The ULISSE Approach. Proc. VLDB Endow. (sep 2018), 2236-2248.

[34] Vasileios Megalooikonomou, Guo Li, and Qiang Wang. 2004. A Dimensionality Reduction Technique for Efficient Similarity Analysis of Time Series Databases (CIKM ’04). Association for Computing Machinery, New York, NY, USA, 160-161. https://doi.org/10.1145/1031171.1031203

[35] Abdullah Mueen, Hossein Hamooni, and Trilce Estrada. 2014. Time Series Join on Subsequence Correlation. In 2014 IEEE International Conference on Data Mining. 450-459. https://doi.org/10.1109/ICDM.2014.52

[36] Abdullah Mueen, Suman Nath, and Jie Liu. 2010. Fast approximate correlation for massive time-series data. In Proceedings of the ACM SIGMOD International Conference on Management of Data. 171-182. https://doi.org/10.1145/ 1807167.1807188

[37] Sina Niedermaier, Falko Koetter, Andreas Freymann, and Stefan Wagner. 2019. On Observability and Monitoring of Distributed Systems – An Industry Interview Study. In 17th Int. Conf. on Service-Oriented Computing (ICSOC’19). Toulouse, France, 36-52.

[38] Spiros Papadimitriou, Jimeng Sun, and Christos Faloutsos. 2005. Streaming Pattern Discovery in Multiple Time-Series. In Proceedings of the 31st International Conference on Very Large Data Bases (Trondheim, Norway) (VLDB '05). VLDB Endowment, 697-708.

[39] Botao Peng, Panagiota Fatourou, and Themis Palpanas. 2020. MESSI: In-Memory Data Series Indexing. In 2020 IEEE 36th International Conference on Data Engineering (ICDE). 337-348.

[40] Botao Peng, Panagiota Fatourou, and Themis Palpanas. 2021. ParIS+: Data Series Indexing on Multi-Core Architectures. IEEE Transactions on Knowledge and Data Engineering 33, 5 (2021), 2151-2164.

[41] I. Popivanov and R.J. Miller. 2002. Similarity search over time-series data using wavelets. In Proceedings 18th International Conference on Data Engineering. 212-221. https://doi.org/10.1109/ICDE.2002.994711

[42] Han Qiu, Hoang Thanh Lam, Francesco Fusco, and Mathieu Sinn. 2018. Learning Correlation Space for Time Series. https://arxiv.org/pdf/1802.03628.pdf.arXiv:1802.03628 [cs.LG]

[43] Davood Rafiei. 1999. On similarity-based queries for time series data. Proceedings 15th International Conference on Data Engineering (Cat. No.99CB36337) (1999), 410-417.

[44] Gang Ren, Eric Tune, Tipp Moseley, Yixin Shi, Silvius Rus, and Robert Hundt. 2010. Google-Wide Profiling: A Continuous Profiling Infrastructure for Data Centers. IEEE Micro 30, 4 (jul 2010), 65-79. https://doi.org/10.1109/MM.2010.68

[45] Irene Rodriguez-Lujan, Jordi Fonollosa, Alexander Vergara, M.L. Homer, and Ramón Huerta. 2013. On the calibration of sensor arrays for pattern recognition using the minimal number of experiments. Chemometrics and Intelligent Laboratory Systems 130 (January 2013). https://doi.org/10.1016/j.chemolab.2013.10.012

[46] Kyuseok Shim, Ramakrishnan Srikant, and Rakesh Agrawal. 1997. High-Dimensional Similarity Joins. In Proceedings of the Thirteenth International Conference on Data Engineering (ICDE '97). IEEE Computer Society, 301-311.

[47] A. V. Oppenheim und R. W. Schafer. 1975. Digital Signal Processing. Prentice Hall, Englewood Cliffs, New Jersey.

[48] Alexander Vergara, Shankar Vembu, Tuba Ayhan, Margaret A. Ryan, Margie L. Homer, and Ramón Huerta. 2012. Chemical gas sensor drift compensation using classifier ensembles. Sensors and Actuators B: Chemical 166-167 (2012), 320-329. https://doi.org/10.1016/j.snb.2012.01.074

[49] Qiang Wang and Vasileios Megalooikonomou. 2008. A Dimensionality Reduction Technique for Efficient Time Series Similarity Analysis. Information systems 33, 1 (March 2008), 115-132. https://doi.org/10.1016/j.is.2007.07.002

[50] Yang Wang, Peng Wang, Jian Pei, Wei Wang, and Sheng Huang. 2013. A Data-Adaptive and Dynamic Segmentation Index for Whole Matching on Time Series. Proc. VLDB Endow. (aug 2013), 793-804.

[51] Daniel Wu, Ambuj Singh, Divyakant Agrawal, Amr El Abbadi, and Terence R. Smith. 1996. Efficient Retrieval for Browsing Large Image Databases. In Proceedings of the Fifth International Conference on Information and Knowledge Management (Rockville, Maryland, USA) (CIKM '96). Association for Computing Machinery, New York, NY, USA, 11-18. https://doi.org/10.1145/238355.238365

[52] Qing Xie, Shuo Shang, Bo Yuan, Chaoyi Pang, and Xiangliang Zhang. 2013. Local Correlation Detection with Linearity Enhancement in Streaming Data. In Proceedings of the 22nd ACM International Conference on Information &amp; Knowledge Management (CIKM '13). Association for Computing Machinery, 309-318.

[53] Yunlong Xu, Jinshu Liu, and Fatemeh Nargesian. 2022. TSUBASA: Climate Network Construction on Historical and Real-Time Data. In Proceedings of the 2022 International Conference on Management of Data (Philadelphia, PA, USA) (SIGMOD '22). Association for Computing Machinery, New York, NY, USA, 286-295.

[54] Djamel Edine Yagoubi, Reza Akbarinia, Boyan Kolev, Oleksandra Levchenko, Florent Masseglia, Patrick Valduriez, and Dennis Shasha. 2018. ParCorr: efficient parallel methods to identify similar time series pairs across sliding windows. Data Mining and Knowledge Discovery (2018). https://doi.org/10.1007/s10618-018-0580-z

[55] B. . Yi, N. D. Sidiropoulos, T. Johnson, H. V. Jagadish, C. Faloutsos, and A. Biliris. 2000. Online data mining for co-evolving time sequences. In Proceedings of 16th International Conference on Data Engineering. 13-22.

[56] Byoung-Kee Yi and Christos Faloutsos. 2000. Fast Time Sequence Indexing for Arbitrary Lp Norms. Proceedings of the 26th International Conference on Very Large Data Bases, VLDB'00 (January 2000), 385-394.

[57] Sheng Zhong, Vinicius M.A. Souza, and Abdullah Mueen. 2020. FilCorr: Filtered and Lagged Correlation on Streaming Time Series. In 2020 IEEE International Conference on Data Mining (ICDM). 1436-1441. https://doi.org/10.1109/ ICDM50108.2020.00190

[58] Yunyue Zhu and Dennis E. Shasha. 2002. StatStream: Statistical Monitoring of Thousands of Data Streams in Real Time. In Proceedings of 28th International Conference on Very Large Data Bases, VLDB 2002, Hong Kong, August 20-23, 2002. 358-369. https://doi.org/10.1016/B978-155860869-6/50039-1

[59] Kostas Zoumpatianos, Stratos Idreos, and Themis Palpanas. 2016. ADS: The Adaptive Data Series Index. The VLDB Journal (dec 2016), 843-866.