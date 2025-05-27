# PECJ: Stream Window Join on Disorder Data Streams with Proactive Error Compensation

# PECJ：基于主动误差补偿的无序数据流流窗口连接

XIANZHI ZENG, Singapore University of Technology and Design, Singapore

曾宪梓，新加坡科技设计大学，新加坡

SHUHAO ZHANG, Nanyang Technological University, Singapore

张书豪，南洋理工大学，新加坡

HONGBIN ZHONG, 4paradigm Inc., Beijing

钟鸿彬，第四范式有限公司，北京

HAO ZHANG, 4paradigm Inc., Singapore

张浩，第四范式有限公司，新加坡

MIAN LU, 4paradigm Inc., Singapore

卢勉，第四范式有限公司，新加坡

ZHAO ZHENG, 4paradigm Inc., Beijing

郑昭，第四范式有限公司，北京

YUQIANG CHEN, 4paradigm Inc., Beijing

陈玉强，第四范式有限公司，北京

Stream Window Join (SWJ), a vital operation in stream analytics, struggles with achieving a balance between accuracy and latency due to out-of-order data arrivals. Existing methods predominantly rely on adaptive buffering, but often fall short in performance, thereby constraining practical applications. We introduce PECJ, a solution that proactively incorporates unobserved data to enhance accuracy while reducing latency, thus requiring robust predictive modeling of stream oscillation. At the heart of PECJ lies a mathematical formulation of the posterior distribution approximation (PDA) problem using variational inference (VI). This approach circumvents error propagation while meeting the low-latency demands of SWJ. We detail the implementation of PECJ, striking a balance between complexity and generality, and discuss both analytical and learning-based approaches. Experimental evaluations reveal PECJ's superior performance. The successful integration of PECJ into a multi-threaded SWJ benchmark testbed further establishes its practical value, demonstrating promising advancements in enhancing data stream processing capabilities amidst out-of-order data.

流窗口连接(SWJ)作为流分析中的关键操作，由于数据乱序到达问题，始终难以在准确性与延迟之间取得平衡。现有方法主要依赖自适应缓冲策略，但往往性能欠佳，制约了实际应用。我们提出PECJ解决方案，通过主动纳入未观测数据来提升准确性并降低延迟，这要求对数据流振荡建立强大的预测模型。PECJ的核心是采用变分推断(VI)构建后验分布近似(PDA)问题的数学框架，在满足SWJ低延迟需求的同时规避误差传播。我们详述了PECJ在复杂度与通用性之间取得平衡的实现方案，并探讨了解析方法与学习方法的结合。实验评估表明PECJ具有卓越性能。该方案成功集成到多线程SWJ基准测试平台，进一步验证了其实际价值，为提升乱序数据环境下的流处理能力提供了创新思路。

## CCS Concepts: $\cdot$ Information systems $\rightarrow$ Stream management; $\cdot$ Mathematics of computing $\rightarrow$ Variational methods.

## CCS概念：$\cdot$信息系统$\rightarrow$流管理；$\cdot$计算数学$\rightarrow$变分方法

Additional Key Words and Phrases: data stream, variational methods, out-of-order arrival, error compensation

附加关键词：数据流、变分方法、乱序到达、误差补偿

## ACM Reference Format:

## ACM参考文献格式：

Xianzhi Zeng, Shuhao Zhang, Hongbin Zhong, Hao Zhang, Mian Lu, Zhao Zheng, and Yuqiang Chen. 2024. PECJ: Stream Window Join on Disorder Data Streams with Proactive Error Compensation. Proc. ACM Manag. Data 2, 1 (SIGMOD), Article 13 (February 2024), 24 pages. https://doi.org/10.1145/3639268

曾宪梓、张书豪、钟鸿彬、张浩、卢勉、郑昭、陈玉强。2024。PECJ：基于主动误差补偿的无序数据流流窗口连接。ACM数据管理会刊2卷1期(SIGMOD)，第13篇文章（2024年2月），24页。https://doi.org/10.1145/3639268

## 1 INTRODUCTION

## 1 引言

Stream Window Join (SWJ) is an operation for joining two input streams within distinct, finite subsets, or 'windows', of infinite streams. SWJ, a crucial component of data stream analytics [49], departs from traditional relational join operations. Rather than waiting for the full input data to become available, SWJ is tasked with generating join results in real-time. This requirement

流窗口连接(SWJ)是在无限数据流中针对两个输入流的离散有限子集（即"窗口"）进行连接的操作。作为数据流分析的关键组件[49]，SWJ与传统关系型连接操作存在本质区别。它无需等待完整输入数据就绪，而是必须实时生成连接结果。这一特性

© 2024 Copyright held by the owner/author(s).

© 2024 版权归作者所有

ACM 2836-6573/2024/2-ART13

ACM 2836-6573/2024/2-ART13

https://doi.org/10.1145/3639268 arises from its essential role across various sectors, such as financial markets [13], fraud detection systems [2], and sensor networks [35].

其重要性源于在金融市场[13]、欺诈检测系统[2]和传感器网络[35]等各领域的关键作用。

---

<!-- Footnote -->

Authors' addresses: Xianzhi Zeng, Singapore University of Technology and Design, Singapore, xianzhi_xianzhi@mymail.sutd.edu.sg; Shuhao Zhang, Nanyang Technological University, Singapore, shuhao.zhang@ntu.edu.sg; Hongbin Zhong, 4paradigm Inc., Beijing, zhonghongbin@4paradigm.com; Hao Zhang, 4paradigm Inc., Singapore, zhanghao@4paradigm.com; Mian Lu, 4paradigm Inc., Singapore, lumian@4paradigm.com; Zhao Zheng, 4paradigm Inc., Beijing, zhengzhao@4paradigm.com; Yuqiang Chen, 4paradigm Inc., Beijing, chenyuqiang@4paradigm.com.

作者联系方式：曾宪治，新加坡科技设计大学，新加坡，xianzhi_xianzhi@mymail.sutd.edu.sg；张书豪，南洋理工大学，新加坡，shuhao.zhang@ntu.edu.sg；钟宏彬，第四范式公司，北京，zhonghongbin@4paradigm.com；张浩，第四范式公司，新加坡，zhanghao@4paradigm.com；卢勉，第四范式公司，新加坡，lumian@4paradigm.com；郑昭，第四范式公司，北京，zhengzhao@4paradigm.com；陈玉强，第四范式公司，北京，chenyuqiang@4paradigm.com。

690 This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike International 4.0 License.

690 本作品采用知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议进行许可。

<!-- Footnote -->

---

One of the challenges complicating SWJ is the disorderly arrival of data, primarily due to factors like network delays,often termed as stream oscillation $\left\lbrack  {6,7,9}\right\rbrack$ . The management of these disordered data streams typically involves buffering input data [22, 23], providing a more comprehensive view of in-window data, thereby facilitating higher accuracy results from running SWJ directly on potentially disordered data streams. However, the additional buffering time needed to gain this comprehensive view often leads to substantial latency costs. These costs become particularly pronounced when waiting for straggling tuples, a situation exacerbated by the non-linear nature of SWJ $\left\lbrack  {{22},{49}}\right\rbrack$ .

滑动窗口连接（SWJ）面临的核心挑战是数据无序到达问题，主要源于网络延迟等因素（常称为流振荡$\left\lbrack  {6,7,9}\right\rbrack$）。传统解决方案通过缓冲输入数据[22,23]来获取更完整的窗口数据视图，从而直接在可能无序的数据流上执行SWJ以获得更高精度。但这种缓冲策略所需的额外等待时间往往导致显著的延迟代价，尤其在等待滞后元组时，SWJ的非线性特性$\left\lbrack  {{22},{49}}\right\rbrack$会进一步放大这种代价。

To address these issues, we propose a novel solution: PECJ (Proactive Error Compensation-Join) algorithm, designed to proactively manage disordered data streams. Unlike existing methods, which rely exclusively on already-arrived data (i.e., in-window data), PECJ actively takes into account the contributions of future, disordered data to enhance join accuracy. This innovative approach to disorder management introduces a promising avenue for achieving significant accuracy enhancements without corresponding increases in latency. Notably, while subjects such as disorder handling parallelization $\left\lbrack  {{27},{29},{34}}\right\rbrack$ and efficient buffer structures $\left\lbrack  {11}\right\rbrack$ have been thoroughly explored in prior studies, these aspects are orthogonal to our work.

针对这些问题，我们提出创新解决方案PECJ（主动误差补偿连接）算法。与仅依赖已到达数据（即窗口内数据）的现有方法不同，PECJ主动考虑未来无序数据的贡献值以提升连接精度。这种突破性的无序管理方法为实现精度显著提升而不增加延迟开辟了新途径。需说明的是，虽然现有研究已深入探讨了无序处理并行化$\left\lbrack  {{27},{29},{34}}\right\rbrack$和高效缓冲结构$\left\lbrack  {11}\right\rbrack$等课题，但这些方向与本工作正交。

Application Example: Consider a sophisticated online anomaly detection system deployed in a stock exchange data center [3]. This system aims to identify irregular trading behaviors, such as "malicious short-selling" [15], through routine evaluations. It functions within designated time-based windows and employs intra-window joins ${}^{1}$ [49] to establish correlations between quotes and trades. Subsequent to this correlation, an aggregation function, commonly COUNT (   ), generates a scalar output that acts as the basis for issuing alerts. The complexity escalates when factoring in stream oscillations, which can be induced by network latencies, data source inconsistencies, and even geopolitical events affecting the timeliness of data streams. For example, consider an overseas transaction potentially aimed at malicious short-selling; it would ideally be processed within a latency as low as ${200}\mathrm{\;{ms}}$ [14]. However,due to the unpredictable effects of stream oscillations, this transaction might experience significant delays,potentially as long as ${800}\mathrm{\;{ms}}$ or more [17]. Traditional methods $\left\lbrack  {9,{22},{23},{29}}\right\rbrack$ present two undesirable options: either wait for the delayed data, risking further latency, or proceed with incomplete data, which risks inaccuracy. Both options are problematic in a high-stakes financial environment. PECJ offers a proactive approach for identifying suspect trading activities by integrating predictive analytics for delayed data. By utilizing variational inference methods for estimating the posterior distribution of unobserved data, PECJ achieves a balance between computational efficiency and prediction accuracy unparalleled by existing methods. This enables the system to operate effectively even in latency-sensitive financial contexts.

应用示例：假设某证券交易所数据中心部署的复杂异常检测系统[3]，该系统通过例行评估识别"恶意做空"[15]等异常交易行为。其在设定时间窗口内运行，采用窗口内连接${}^{1}$[49]建立报价与交易关联，再通过COUNT()聚合函数生成警报标量。当考虑网络延迟、数据源不一致乃至地缘政治事件引发的流振荡时，复杂度剧增。例如某海外恶意做空交易本应在${200}\mathrm{\;{ms}}$[14]毫秒内处理完成，但因流振荡影响可能延迟至${800}\mathrm{\;{ms}}$毫秒以上[17]。传统方法$\left\lbrack  {9,{22},{23},{29}}\right\rbrack$面临两难选择：等待延迟数据将增加风险敞口，而使用不完整数据则可能产生误判。PECJ通过整合延迟数据的预测分析，采用变分推断方法估算未观测数据的后验分布，在计算效率与预测精度间取得现有方法无法实现的平衡，使系统能在高时效要求的金融场景中有效运作。

Contributions and Outline: PECJ aims to augment the reliability of SWJ by proactively accounting for the yet-to-arrive disordered data, without incurring additional latency. The architecture of PECJ is founded on a three-stage approach. In the first stage, we redefine the problem of SWJ with disordered data streams as a Posterior Distribution Approximation (PDA) problem. This avoids the pitfalls of single data-point predictions and instead focuses on the collective impact of all unobserved data. This framework is compatible with any scalar-output aggregation functions, such as ${SUM}\left( \right)$ ,and ${COUNT}\left( \right)$ ,without requiring per-tuple decompositions. The second stage focuses on the optimization of our probabilistic model's parameters. Instead of utilizing the conventional but impractical brute-force parameterization, we propose to employ Variational Inference (VI) techniques to enhance efficiency $\left\lbrack  {{21},{42}}\right\rbrack$ . The final stage translates these concepts into practice through two implementations: ${PEC}{J}_{\text{analytical }}$ for simpler cases and ${PEC}{J}_{\text{learning }}$ for more complex scenarios. PEC ${j}_{\text{analytical }}$ employs low-overhead linear modeling,while ${PEC}{j}_{\text{learning }}$ uses neural networks for improving posterior distribution accuracy.

贡献与框架：PECJ旨在通过主动处理尚未到达的乱序数据来提升SWJ的可靠性，同时避免引入额外延迟。PECJ架构基于三阶段方法：第一阶段将乱序数据流下的SWJ问题重新定义为后验分布近似(PDA)问题，规避单数据点预测缺陷，转而关注所有未观测数据的整体影响。该框架兼容任何标量输出聚合函数（如${SUM}\left( \right)$和${COUNT}\left( \right)$），无需元组级分解。第二阶段聚焦概率模型参数优化，采用变分推断(VI)技术替代传统低效的暴力参数化方法$\left\lbrack  {{21},{42}}\right\rbrack$。最终阶段通过两种实现方案落地：针对简单场景的${PEC}{J}_{\text{analytical }}$与复杂场景的${PEC}{J}_{\text{learning }}$，其中${j}_{\text{analytical }}$采用低开销线性建模，${PEC}{j}_{\text{learning }}$则通过神经网络提升后验分布精度。

---

<!-- Footnote -->

${}^{1}$ This is a specific type of SWJ; see Section 2.1 for more details.

${}^{1}$这是SWJ的特殊类型，详见2.1节说明。

<!-- Footnote -->

---

The efficacy of PECJ is principally evaluated through a comprehensive algorithmic comparison, substantiating its advantages over existing methods [9, 22]. As a supplementary validation, PECJ is also integrated into AllianceDB, a multi-threaded SWJ benchmark testbed [49]. This additional evaluation demonstrates PECJ's robustness in mitigating out-of-order processing errors while upholding scalability. Although our primary experiments focus on intra-window joins with $\operatorname{SUM}\left( \right)$ and $\operatorname{COUNT}\left( \right)$ as example aggregations,PECJ's mathematical formulation accommodates a wide array of scalar-output aggregations. Its flexibility also allows for future adaptability to other SWJ variants. Issues concerning computational reuse in alternative types of SWJ are designated for future research $\left\lbrack  {{37},{39},{43}}\right\rbrack$ .

通过全面算法对比验证PECJ效能，其优势较现有方法[9,22]显著。补充验证中，PECJ被集成至多线程SWJ基准测试平台AllianceDB[49]，证明其在维持扩展性的同时有效降低乱序处理错误。虽然主实验采用$\operatorname{SUM}\left( \right)$和$\operatorname{COUNT}\left( \right)$作为窗内连接示例，但PECJ数学模型支持广泛标量聚合函数，其灵活性也为未来适配其他SWJ变体预留空间。其他SWJ类型的计算复用问题将留待后续研究$\left\lbrack  {{37},{39},{43}}\right\rbrack$。

- Section 3 introduces the PECJ algorithm, tailored to balance both accuracy and latency in SWJ operations amid disordered data. The distinct advantage of PECJ lies in its proactive approach of incorporating the impact of yet-to-be-seen data for join error compensation.

- 第3章阐述PECJ算法，该算法专为在乱序数据中平衡SWJ操作精度与延迟而设计，其核心优势在于前瞻性地纳入未到达数据的影响进行连接误差补偿。

- In Section 4, we delve into the mathematical formulation of how PECJ addresses the challenge of forecasting the effects of stream oscillation. The disorder SWJ handling is initially abstracted into a posterior distribution approximation (PDA) problem, which is followed by optimizing the parameterization of its probability model via variational inference (VI).

- 第4章深入解析PECJ应对流振荡影响的数学建模：先将乱序SWJ处理抽象为后验分布近似(PDA)问题，继而通过变分推断(VI)优化概率模型参数化。

- Section 5 presents two practical implementations of PECJ, demonstrating its adaptability. We begin with a straightforward, analytical implementation suitable for less severe stream oscillation and gradually progress to a more generalized form (learning-based) that employs machine learning for handling complex oscillation cases.

- 第5章展示PECJ两种实践方案：从适用于温和流振荡的解析式实现，逐步过渡到采用机器学习处理复杂振荡的通用(学习型)实现。

- Our experimental results, highlighted in Section 6, offer a comprehensive comparison between PECJ and the existing state-of-the-art methods. We provide data from both standalone tests and system integration tests, underscoring the superior performance of PECJ.

- 第6章实验数据全面对比PECJ与现有最优方法，包含独立测试与系统集成测试结果，突显PECJ的卓越性能。

## 2 PRELIMINARY

## 2 预备知识

This section provides a detailed introduction to Stream Window Join (SWJ), including the buffering mechanisms for handling disorder prevalent in existing research. Afterwards, we introduce a better strategy than state-of-art and discuss its technical challenges.

本节详述流窗口连接(SWJ)及现有研究中主流的乱序缓冲机制，随后介绍优于现有技术的策略并讨论其技术挑战。

### 2.1 Stream Window Join and Key Definitions

### 2.1 流窗口连接与关键定义

Table 1 summarizes the notations used in this paper. For the purposes of this paper, we define a tuple $y$ as $y = {\tau }_{\text{event }},\kappa ,v,{\tau }_{\text{arrival }},{\tau }_{\text{emit }}$ ,where ${\tau }_{\text{event }},\kappa$ ,and $v$ represent the event timestamp,key,and payload of the tuple, respectively. The tuple's arrival time at a system is denoted by ${\tau }_{\text{arrival }}$ ,while ${\tau }_{\text{emit }}$ signifies the moment the final result incorporating $y$ is released to the user. An input stream, referred to as $R$ or $S$ ,is a sequence of tuples arriving at the system (e.g.,a query processor),which may arrive out-of-order with respect to their event timestamp.

表1汇总本文符号体系。定义元组$y$为$y = {\tau }_{\text{event }},\kappa ,v,{\tau }_{\text{arrival }},{\tau }_{\text{emit }}$，其中${\tau }_{\text{event }},\kappa$、$v$分别表示事件时间戳、键值与载荷数据，${\tau }_{\text{arrival }}$记录元组到达系统时间，${\tau }_{\text{emit }}$表示含$y$的最终结果交付用户时刻。输入流$R$或$S$指到达系统（如查询处理器）的元组序列，其到达顺序可能相对于事件时间戳乱序。

We adopt the windows concept from Zhang et al. [49] to handle infinite stream joins over limited subsets of data. Here,a window is defined as an arbitrary time range $\left\lbrack  {{t1},{t2}}\right\rbrack$ ,denoted as $\mathbb{W} = \left\lbrack  {{t1},{t2}}\right\rbrack$ . A tuple $y$ is considered part of $\mathbb{W}$ if its timestamp ${t}_{e}$ falls within this range. The length of the window is represented as $\left| \mathbb{W}\right|$ . As discussed in the motivating example in Section 1, we use intra-window joins [49] as an example SWJ in this work. For given input streams $R$ and $S$ and a window $\mathbb{W}$ ,the intra-window join,hereafter referred to simply as SWJ,is represented as $R{ \bowtie  }_{\mathbb{W}}S = \left( {r \cup  s}\right)  \mid  r \in  R,s \in  S,r \in  \mathbb{W},s \in  \mathbb{W}$ . The result of $R{ \bowtie  }_{\mathbb{W}}S$ is subsequently condensed into a scalar output, $O$ ,via an aggregation function,which commonly either counts the joined tuples-i.e., ${COUNT}\left( \right)$ -or performs a sum operation on $R.v$ and $S.v$ ,denoted as ${SUM}\left( \right)$ . When $O$ is dispatched to the user at the time point ${\tau }_{\text{emit }}$ ,we consider the following two performance metrics:

我们采用Zhang等人[49]提出的窗口概念来处理有限数据子集上的无限流连接。此处，窗口被定义为任意时间范围$\left\lbrack  {{t1},{t2}}\right\rbrack$，记作$\mathbb{W} = \left\lbrack  {{t1},{t2}}\right\rbrack$。若元组$y$的时间戳${t}_{e}$落在此范围内，则被视为$\mathbb{W}$的组成部分。窗口长度表示为$\left| \mathbb{W}\right|$。如第1节动机示例所述，本文以窗口内连接[49]作为示例性流窗口连接(SWJ)。给定输入流$R$和$S$及窗口$\mathbb{W}$，该窗口内连接（下文简称SWJ）表示为$R{ \bowtie  }_{\mathbb{W}}S = \left( {r \cup  s}\right)  \mid  r \in  R,s \in  S,r \in  \mathbb{W},s \in  \mathbb{W}$。$R{ \bowtie  }_{\mathbb{W}}S$的结果随后通过聚合函数压缩为标量输出$O$，该函数通常对连接元组进行计数（即${COUNT}\left( \right)$），或对$R.v$和$S.v$执行求和运算（记作${SUM}\left( \right)$）。当$O$在时间点${\tau }_{\text{emit }}$分发给用户时，我们考量以下两个性能指标：

<!-- Media -->

Table 1. Notations used in this paper

表1. 本文使用的符号说明

<table><tr><td>Type</td><td>Notations</td><td>Description</td></tr><tr><td rowspan="6">Tuple property</td><td>$\kappa$</td><td>Key of a tuple</td></tr><tr><td>$v$</td><td>Payload of a tuple</td></tr><tr><td>${\tau }_{event}$</td><td>The time of event occurrence of an input tuple</td></tr><tr><td>${\tau }_{\text{arrival }}$</td><td>The input tuple arrival time</td></tr><tr><td>${\tau }_{emit}$</td><td>The time to emit an output tuple</td></tr><tr><td>$\delta$</td><td>The delay from event occurrence $\left( {\tau }_{\text{event }}\right)$ to event arrival $\left( {\tau }_{\text{arrival }}\right)$ of an input tuple</td></tr><tr><td rowspan="11">Stream property</td><td>$R,S$</td><td>Two input streams to join</td></tr><tr><td>W</td><td>A bounded subset of data stream to join</td></tr><tr><td>O</td><td>The aggregated results of $R{ \bowtie  }_{\mathbb{W}}S$</td></tr><tr><td>$\epsilon$</td><td>The relative error of output</td></tr><tr><td>$l$</td><td>The processing latency</td></tr><tr><td>ω</td><td>The assumed time point of window completeness</td></tr><tr><td>$n$</td><td>The number of tuples</td></tr><tr><td>$\sigma$</td><td>The join selectivity, as defined by [22]</td></tr><tr><td>$\alpha$</td><td>The average payload of joined tuples</td></tr><tr><td>${\bar{r}}_{n}$</td><td>Window-averaged tuple rate corresponding to $n$</td></tr><tr><td>$\Delta$</td><td>Maximum delay among all events from the time of occurrence $\left( {\tau }_{\text{event }}\right)$ to the time of arrival $\left( {\tau }_{\text{arrival }}\right)$ . $\Delta  = \mathop{\max }\limits_{{\forall i}}\left( {{\tau }_{\text{arrival }} - {\tau }_{\text{event }}}\right)$</td></tr><tr><td rowspan="7">PDA abstraction</td><td>${\mu }_{w}$</td><td>A global variable for describing window-averaged contribution</td></tr><tr><td>${\varphi }_{w}$</td><td>A variable for describing other global information of a window</td></tr><tr><td>$U$</td><td>The set of global variables, including the interested ${\mu }_{w}$ and ${\varphi }_{w}$</td></tr><tr><td>$X$</td><td>The set of observations made on acquired tuples</td></tr><tr><td>$p\left( \right)$</td><td>The probability distribution</td></tr><tr><td>$\mathbb{E}\left( k\right)$</td><td>The expectation of $k$</td></tr><tr><td>$Z$</td><td>The set of latent variables</td></tr><tr><td rowspan="4">VI optimization</td><td>$q\left( \right)$</td><td>The approximation function in variational family [21]</td></tr><tr><td>${\mathbb{E}}_{j}\left( k\right)$</td><td>The expectation of $k$ ,regarding on $j$ (i.e.,replace $j$ by $\mathbb{E}\left( j\right)$ during estimating $\mathbb{E}\left( k\right)$ )</td></tr><tr><td>${ELBO}_{a}$</td><td>The evidence lower bound</td></tr><tr><td>$H$</td><td>The set of remapped parameters in $U,Z$</td></tr></table>

<table><tbody><tr><td>类型</td><td>符号表示</td><td>描述</td></tr><tr><td rowspan="6">元组属性</td><td>$\kappa$</td><td>元组键</td></tr><tr><td>$v$</td><td>元组有效载荷</td></tr><tr><td>${\tau }_{event}$</td><td>输入元组的事件发生时间</td></tr><tr><td>${\tau }_{\text{arrival }}$</td><td>输入元组到达时间</td></tr><tr><td>${\tau }_{emit}$</td><td>输出元组发射时间</td></tr><tr><td>$\delta$</td><td>输入元组从事件发生$\left( {\tau }_{\text{event }}\right)$到事件到达$\left( {\tau }_{\text{arrival }}\right)$的延迟</td></tr><tr><td rowspan="11">流属性</td><td>$R,S$</td><td>待连接的两个输入流</td></tr><tr><td>W</td><td>待连接数据流的有界子集</td></tr><tr><td>O</td><td>$R{ \bowtie  }_{\mathbb{W}}S$的聚合结果</td></tr><tr><td>$\epsilon$</td><td>输出相对误差</td></tr><tr><td>$l$</td><td>处理延迟</td></tr><tr><td>ω</td><td>假设的窗口完整性时间点</td></tr><tr><td>$n$</td><td>元组数量</td></tr><tr><td>$\sigma$</td><td>连接选择率（按文献[22]定义）</td></tr><tr><td>$\alpha$</td><td>连接元组的平均有效载荷</td></tr><tr><td>${\bar{r}}_{n}$</td><td>与$n$对应的窗口平均元组速率</td></tr><tr><td>$\Delta$</td><td>所有事件从发生时间$\left( {\tau }_{\text{event }}\right)$到到达时间$\left( {\tau }_{\text{arrival }}\right)$的最大延迟。$\Delta  = \mathop{\max }\limits_{{\forall i}}\left( {{\tau }_{\text{arrival }} - {\tau }_{\text{event }}}\right)$</td></tr><tr><td rowspan="7">PDA抽象模型</td><td>${\mu }_{w}$</td><td>描述窗口平均贡献的全局变量</td></tr><tr><td>${\varphi }_{w}$</td><td>描述窗口其他全局信息的变量</td></tr><tr><td>$U$</td><td>全局变量集合，包括目标变量${\mu }_{w}$和${\varphi }_{w}$</td></tr><tr><td>$X$</td><td>已获取元组的观测值集合</td></tr><tr><td>$p\left( \right)$</td><td>概率分布</td></tr><tr><td>$\mathbb{E}\left( k\right)$</td><td>$k$的期望</td></tr><tr><td>$Z$</td><td>潜变量集合</td></tr><tr><td rowspan="4">VI优化</td><td>$q\left( \right)$</td><td>变分族[21]中的近似函数</td></tr><tr><td>${\mathbb{E}}_{j}\left( k\right)$</td><td>$k$关于$j$的期望（即在估计$\mathbb{E}\left( k\right)$时将$j$替换为$\mathbb{E}\left( j\right)$）</td></tr><tr><td>${ELBO}_{a}$</td><td>证据下界</td></tr><tr><td>$H$</td><td>$U,Z$中的重映射参数集合</td></tr></tbody></table>

<!-- Media -->

- Accuracy: This metric assesses the precision of $O$ and is quantified by its relative error $\epsilon$ . Specifically, $\epsilon  = \frac{\left| {O}^{opr} - {O}^{exp}\right| }{{O}^{exp}}$ ,where ${O}^{opr}$ represents the aggregated value produced by an algorithm and ${O}^{exp}$ is the expected value. A larger $\epsilon$ means that the ${O}^{opr}$ is further from the expected outcome ${O}^{\text{exp }}$ .

- 准确度：该指标评估$O$的精确性，并通过其相对误差$\epsilon$进行量化。具体而言，$\epsilon  = \frac{\left| {O}^{opr} - {O}^{exp}\right| }{{O}^{exp}}$，其中${O}^{opr}$表示算法生成的聚合值，${O}^{exp}$为期望值。$\epsilon$值越大，表明${O}^{opr}$与预期结果${O}^{\text{exp }}$偏差越大。

- Latency: For all tuples contributing to the generation of $O$ ,their ${\tau }_{\text{emit }}$ is defined as the moment when $O$ is produced,and the latency $l$ for each tuple is calculated as $l = {\tau }_{\text{emit }} - {\tau }_{\text{arrival }}$ . In this study, we report the 95th percentile of the worst-case latency, a commonly used measure, referring to it as ${95}\% l$ . A larger $l$ indicates more time to process SWJ and its follow-up aggregation function.

- 延迟：对于所有参与生成$O$的元组，其${\tau }_{\text{emit }}$定义为$O$产生的时刻，每个元组的延迟$l$计算公式为$l = {\tau }_{\text{emit }} - {\tau }_{\text{arrival }}$。本研究采用最坏情况下延迟的第95百分位数（常用度量指标），记为${95}\% l$。$l$值越大，表示处理SWJ及其后续聚合函数所需时间越长。

It should be noted that while this paper predominantly employs ${COUNT}\left( \right)$ and ${SUM}\left( \right)$ as example aggregation functions due to their widespread use, PECJ is versatile enough to support any aggregation function yielding a scalar result. Additionally, other variants of SWJ, such as sliding window joins $\left\lbrack  {{37},{39}}\right\rbrack$ ,are also worth mentioning. These alternate approaches often introduce additional computational challenges, particularly in the realm of computational reuse for overlapping windows. While important, these aspects are outside the purview of this paper and are designated as topics for future investigation $\left\lbrack  {{37},{39},{43}}\right\rbrack$ .

需特别说明，虽然本文主要采用应用广泛的${COUNT}\left( \right)$和${SUM}\left( \right)$作为示例聚合函数，但PECJ框架具有通用性，可支持任何生成标量结果的聚合函数。此外，SWJ的其他变体如滑动窗口连接$\left\lbrack  {{37},{39}}\right\rbrack$也值得关注。这些替代方案通常会带来额外的计算挑战，特别是在重叠窗口计算复用领域。尽管重要，这些内容已超出本文研究范围，将作为未来研究方向$\left\lbrack  {{37},{39},{43}}\right\rbrack$。

<!-- Media -->

<!-- figureText: $\omega  = {10ms}$ $\omega  = {50ms}$ $\omega  = {10}\mathrm{{ms}}$ W= 10ms W= 10ms IR1 R4 R3 R2 R4 R3 R2 $\mathbb{R}1$ 152 S4 S3 S1 S2 S4 S3 S2 S1 (c) Buffering mechanism (d) Proactively leverages with large $\omega$ (i.e.,50 ms) unobserved data (i.e., R1, S2) W= 10ms W= 10ms R4 R3 R2 R1 R1 R4 R3 R2 S4 S3 S2 S1 S2 S4 S3 S1 (a) Ideal scenario of (b) Buffering mechanism in-order arrival with small $\omega$ (i.e.,10 ms) -->

<img src="https://cdn.noedgeai.com/01965997-3577-7b96-be8c-0534d2f3544b_4.jpg?x=231&y=263&w=1110&h=292&r=0"/>

Fig. 1. Disorder handling of SWJ $\left( {R \boxtimes  \left| \mathbb{W}\right|  = {10}\mathrm{\;m}s\;S}\right)$

图1. SWJ的乱序处理机制$\left( {R \boxtimes  \left| \mathbb{W}\right|  = {10}\mathrm{\;m}s\;S}\right)$

<!-- Media -->

### 2.2 Limitations of Current Approaches

### 2.2 现有方法的局限性

The optimal condition for SWJ is when data arrives in sequence-meaning, the ordering defined by ${\tau }_{\text{event }}$ perfectly aligns with the one determined by ${\tau }_{\text{arrival }}$ as depicted in Figure 1(a). In this situation, all data is fully accessible to the system, enabling the completion of the calculated window. However, this idealistic case is rare in the real world due to the stream oscillation [47, 48].

SWJ的理想条件是数据按序到达——即${\tau }_{\text{event }}$定义的顺序与${\tau }_{\text{arrival }}$确定的顺序完全一致，如图1(a)所示。这种情况下系统可完全访问所有数据，确保计算窗口的完整性。但由于流式振荡现象[47,48]，这种理想情况在现实中极为罕见。

In contrast,a disordered arrival is more common and the ${\tau }_{\text{arrival }}$ sequence diverges from that defined by ${\tau }_{\text{event }}$ . Figures 1(b) and (c) illustrate the example scenarios of the disordered arrival. Under these circumstances, ensuring window completeness becomes challenging due to the late-arriving tuples,e.g., ${R1}$ and ${S2}$ ,highlighted in red. Ignoring such unobserved data compromises accuracy. Conversely, waiting for this late data to arrive induces an indeterminate rise in processing latency, given the unpredictable arrival times of these tuples.

相比之下，乱序到达更为常见，${\tau }_{\text{arrival }}$序列与${\tau }_{\text{event }}$定义的序列存在偏差。图1(b)和(c)展示了乱序到达的典型场景。在此类情况下，由于延迟到达的元组（如标红的${R1}$和${S2}$），确保窗口完整性变得困难。忽略这些未观测数据会降低准确性，而等待延迟数据则会导致处理延迟不可控地增加——因为这些元组的到达时间具有不可预测性。

Existing methodologies attempt to combat disordered arrivals using a buffering mechanism, where observed data is retained in buffers while the system awaits a more complete set of window data. The longer the system waits, the fewer unobserved data points there are. To prevent infinite waiting,these systems often designate a certain point in time, $\omega$ ,at which they assume the window is complete and all data has been observed,marking the end of data buffering. Join result $O$ is then emitted at ${\tau }_{\text{emit }}$ ,where ${\tau }_{\text{emit }}$ equals $\omega$ plus the processing time. Given that $\omega$ tends to be smaller than the ${\tau }_{\text{arrival }}$ of late tuples,it effectively decreases the overall processing latency. Previous studies $\left\lbrack  {9,{22},{23},{29}}\right\rbrack$ have proposed both explicit and implicit methodologies for determining $\omega$ .

现有方法试图通过缓冲机制应对乱序到达：系统将已观测数据保留在缓冲区，等待更完整的窗口数据集。等待时间越长，未观测数据点越少。为避免无限等待，这些系统通常设定时间点$\omega$，假定此时窗口已完整且所有数据均已观测，终止数据缓冲。连接结果$O$将在${\tau }_{\text{emit }}$时刻输出，其中${\tau }_{\text{emit }}$等于$\omega$加上处理时间。由于$\omega$通常小于延迟元组的${\tau }_{\text{arrival }}$，该方法能有效降低整体处理延迟。先前研究$\left\lbrack  {9,{22},{23},{29}}\right\rbrack$提出了显式和隐式两种确定$\omega$的方法。

Despite providing potentially autonomous and adaptable $\omega$ decisions,these approaches still frequently neglect the impact of unobserved data-data arriving post- $\omega$ -on the results. For example, in Figure 1(b),a $\omega$ of ${10}\mathrm{\;{ms}}$ causes ${R1}$ and ${S2}$ to be missed,leading to an inaccurate output. To rectify this, $\omega$ can be extended to ensure ${R1}$ and ${S2}$ are included,as shown by the ${50}\mathrm{\;{ms}}\omega$ in Figure 1(c). However,increasing $\omega$ from ${10}\mathrm{\;{ms}}$ to ${50}\mathrm{\;{ms}}$ significantly raises latency,creating an inescapable sub-optimal trade-off between accuracy and latency.

尽管这些方法能提供潜在自主且适应性强的$\omega$决策，但仍频繁忽略未观测数据（即$\omega$后到达的数据）对结果的影响。例如图1(b)中，${10}\mathrm{\;{ms}}$的$\omega$导致${R1}$和${S2}$被遗漏，产生错误输出。为此可将$\omega$扩展至包含${R1}$与${S2}$，如图1(c)${50}\mathrm{\;{ms}}\omega$所示。但将$\omega$从${10}\mathrm{\;{ms}}$增至${50}\mathrm{\;{ms}}$会显著增加延迟，导致准确性与延迟间不可避免的次优权衡。

### 2.3 Proactive Incorporation: A Better Strategy?

### 2.3 主动整合：更优策略？

To avoid the sub-optimal trade-off encountered in the state-of-art, a natural idea is to proactively incorporate the unobserved data into the processing workflow of SWJ ahead of its arrival, as showcased in Figure 1(d), rather than merely waiting. This strategy enables improved accuracy under the same $\omega$ compared to Figure 1(b),without needing to increase $\omega$ as in Figure 1(c). The most straightforward approach to realize such an idea might suggest leveraging time series prediction techniques to anticipate the contributions from each unseen tuple [45]. However, this approach can potentially lead to inconsistent accuracy levels. The prediction of individual tuple contributions is contingent on the estimation of the tuple volume, which further amplifies the risk of error propagation. Moreover, individual estimation of tuples' contribution itself enforces an assumption that the aggregation function is decomposable, which limits the applicability.

为避免现有技术中的次优权衡，自然思路是在SWJ未观测数据到达前就主动将其纳入处理流程（如图1(d)），而非被动等待。该策略能在保持$\omega$不变情况下获得比图1(b)更高的精度，无需如图1(c)增加$\omega$。最直观的实现方式是采用时间序列预测技术预估未观测元组的贡献[45]，但可能导致精度波动。单个元组贡献预测依赖于元组数量的估计，这会放大误差传播风险。此外，元组贡献的独立估计需假设聚合函数可分解，限制了方法适用性。

Further compounding the problem is the escalating complexity associated with time series predictions. As the length of the data increases, the complexity of predicting attributes of a specific and predetermined number of future data points can scale super-linearly [45]. This brings about substantial predictive overhead, which becomes increasingly pronounced when a large number of tuples remain unobserved. Furthermore, the challenges are not solely limited to predictive accuracy and computational overhead. The need to keep latency within permissible thresholds adds another layer of complexity. The interplay between accuracy, computational efficiency, and latency management needs to be carefully navigated, requiring a more innovative and sophisticated approach than traditional methods can offer. Those challenges motivate our proposal of PECJ.

时间序列预测的复杂度加剧了问题。随着数据长度增加，预测特定数量未来数据点属性的复杂度可能超线性增长[45]，产生显著预测开销——当大量元组未观测时尤为明显。挑战不仅限于预测精度与计算开销，还需将延迟控制在阈值内，这要求精准协调精度、计算效率与延迟管理的复杂关系，需要比传统方法更创新的解决方案。这些挑战促使我们提出PECJ。

## 3 OVERVIEW OF PECJ

## 3 PECJ框架概述

This section commences with the preliminary theoretical foundations for PECJ. Subsequently, it offers an overview of PECJ's conceptual framework, accompanied by illustrative examples.

本节首先阐述PECJ的理论基础，随后通过示例说明其概念框架。

### 3.1 Theoretical Foundations for PECJ

### 3.1 PECJ理论基础

To realize the proactive incorporation of unobserved data and address the difficulties discussed in Section 2.3, PECJ solves a posterior distributions approximation (PDA) problem, optimized via variational inference (VI).

为实现未观测数据的主动整合并解决2.3节的难题，PECJ通过变分推理(VI)优化后验分布近似(PDA)问题。

Posterior Distribution Approximation (PDA) is a fundamental problem in Bayesian analysis [10], aiming to update beliefs about probability models' parameters (i.e., the so-called model parameterization process) in response to observations, thereby understanding and interpreting uncertainties. Despite its straightforward concept, deriving the exact posterior distribution analytically can be highly challenging or even impossible when dealing with high-dimensional or nonlinear relationships within probability models. This is because exponential growth complexity of summation and integration will be involved.

后验分布近似(PDA)是贝叶斯分析的基本问题[10]，旨在根据观测数据更新概率模型参数（即模型参数化过程），从而理解不确定性。虽然概念直观，但在处理高维或非线性概率模型时，解析推导精确后验分布极其困难甚至不可行，因其涉及求和与积分的指数级复杂度增长。

Variational Inference [8, 21, 42] (VI) is an optimization technique for simplifying model parameterization in PDA. Rather than brutal force computation, it approximates the true posterior distribution with a tractable distribution family of functions (each denoted as $q\left( \right)$ ),known as the variational family, and a popular choice of variational family is the mean-field variational family. Specifically, VI brings the variational family close to the truth by maximizing the evidence lower bound $\left( {{ELB}{O}_{q}}\right)$ with the gathered pieces of evidence,leading to significantly improved computation efficiency. VI is superior to traditional approaches in four major aspects. First, VI is less prone to overfitting compared to Maximum Likelihood Estimation (MLE) [10]. MLE often struggles to estimate latent variables in complex models accurately. Second, VI incurs less computational overhead than Markov Chain Monte Carlo (MCMC)) [10], making it more suitable for latency-sensitive applications. Third, unlike regularization methods like L1 and L2 [19], VI can robustly handle evolving observations without the need for additional hyperparameter tuning. Lastly, VI is capable of incrementally integrating new observations into the existing distributions [12] and enables continual learning. Therefore, VI is widely used in latent dirichlet allocation [20], autoencoder construction [44], and concept drift detection [8], etc.

变分推断[8,21,42]（VI）是一种用于简化概率数据分析（PDA）模型参数化的优化技术。相较于暴力计算，它通过采用可处理的函数分布族（每个记为$q\left( \right)$，称为变分族）来近似真实后验分布，其中均值场变分族是常用选择。具体而言，VI通过最大化证据下界$\left( {{ELB}{O}_{q}}\right)$使变分族逼近真实分布，从而显著提升计算效率。VI在四大方面优于传统方法：其一，较之最大似然估计（MLE）[10]更不易过拟合，MLE在复杂模型中常难以准确估计隐变量；其二，比马尔可夫链蒙特卡洛（MCMC）[10]计算开销更低，更适合延迟敏感场景；其三，不同于L1/L2正则化方法[19]，VI能稳健处理动态观测而无需超参数调优；其四，VI支持将新观测增量整合至现有分布[12]，实现持续学习。因此VI广泛应用于潜在狄利克雷分配[20]、自编码器构建[44]和概念漂移检测[8]等领域。

### 3.2 Conceptual Framework of PECJ

### 3.2 PECJ概念框架

Designed to actively incorporate unobserved data, PECJ compensates for errors that arise in SWJ when dealing with disordered data streams. This subsection outlines the conceptual framework of PECJ, as depicted in Figure 2.

PECJ专为主动吸纳未观测数据而设计，可修正SWJ处理无序数据流时产生的误差。本小节将结合图2阐述其概念框架。

Abstraction: The first step involves directly abstracting the accurate SWJ result by extracting essential information from the disordered data streams to avoid the error propagation caused by per-tuple estimation (discussed in detail in Section 4.1). This phase essentially constitutes a PDA problem, requiring the development of a probability model that is conscious of the stream oscillation.

抽象化：首先通过从无序流中提取关键信息直接抽象出SWJ准确结果，避免逐元组估计导致的误差传播（详见4.1节）。该阶段本质是PDA问题，需建立感知流振荡的概率模型。

<!-- Media -->

<!-- figureText: Mathmatical Formulation VI Instantiation Probability Q Analytical equivlant to: Brutal force $\sum$ or $\int$ ? complexity vs. generality R4 R3 R2 $\mathbb{R}1$ Learning-based S4 S3 S2 S1 Estimate based on collective prediction. Implementation Value Probability R1 R4 R3 R2 S2 S4 S3 Value The disorder datastreams 1 Abstraction 2 Optimization -->

<img src="https://cdn.noedgeai.com/01965997-3577-7b96-be8c-0534d2f3544b_6.jpg?x=136&y=260&w=1293&h=342&r=0"/>

Fig. 2. Conceptual framework of PECJ.

图2. PECJ概念框架

<!-- figureText: Observed ${n}_{R} = 5$ Observed ${n}_{S} = 5$ Abstraction Probability 0.8 Value $O = \sigma  \times  {n}_{R} \times  {n}_{S} = 8$ (JOIN-COUNT) 2 Optimization 3 Implementation Estimated matches with error compensation Observations ${n}_{R}$ , ${\alpha }_{R}$ -14/225 -1 shown for illustrative in streaming The total ✓ purposes only contribution (6,0.2) (c) Solving the PDA problem with VI (d) Estimated result with error compensated ${\tau }_{event}$ (ms) Observed matches ${R2}$ ${R1}$ ${R}_{2}$ ${R3}$ S3 2 ${R1}$ ${R3}$ 8 Observered: $O = 4$ (JOIN-COUNT) $O = {20}$ (JOIN-SUM) (a) The disordered datastreams (b) The observations -->

<img src="https://cdn.noedgeai.com/01965997-3577-7b96-be8c-0534d2f3544b_6.jpg?x=137&y=706&w=1291&h=287&r=0"/>

Fig. 3. Running Example of PECJ.

图3. PECJ运行示例

<!-- Media -->

Optimization: Given the inherent challenges of efficient PDA parameterization, we turn to the VI approach for theoretical optimization (explained in Section 4.2). As discussed in Section 3.1, VI drastically reduces the parameterization overhead of PDA, and inherently facilitates the evolution of the probability model in parallel with the data streams.

优化：鉴于PDA参数化的固有挑战，我们采用VI方法进行理论优化（见4.2节）。如3.1节所述，VI能大幅降低PDA参数化开销，并自然支持概率模型随数据流协同演化。

Implementation: Bridging the gap between the mathematical formulations of the previous stages and practical application, we provide both analytical and learning-based approaches of VI instantiations in Section 5. The analytical approach (Section 5.1) offers ultra-low overhead while accommodating relatively straightforward stream oscillation patterns. We realize it using both Stochastic Variational Inference [21] (SVI) iterations and an Adaptive Exponential Moving Average Filter [18, 36] (AEMA) in PECJ. SVI offers a general way of conducting analytical approach by utilizing gradient descent, while AEMA involves much lower complexity. The learning-based approach (Section 5.2) seeks to depict various stream dynamics in a more generalized manner. We accomplish this by incorporating VI principles with PECJ's parameters of interest to formulate a loss function and use a simple Multilayer Perceptron (MLP) to implement the core ideas.

实现：为衔接数学建模与实际应用，第5章提供VI实例化的解析式与学习式方案。解析式方案（5.1节）在处理简单流振荡模式时具有超低开销，我们通过随机变分推断[21]（SVI）迭代和自适应指数移动平均滤波器[18,36]（AEMA）实现——SVI利用梯度下降提供通用解析路径，AEMA则复杂度更低；学习式方案（5.2节）旨在泛化表征多样流动态，通过融合VI原理与PECJ目标参数构建损失函数，并采用多层感知机（MLP）实现核心思想。

### 3.3 Running Examples of PECJ

### 3.3 PECJ运行示例

To further elucidate the application of PECJ, we present a running example. The tuples to be joined are outlined in Figure 3(a),with a window length of $6\mathrm{\;{ms}}$ . These consist of 6 tuples from streams $R$ and $S$ ,formatted as ’Key $\left( \kappa \right)$ ,Payload(v),Event Time $\left( {\tau }_{\text{event }}\right.$ ,in ms)’. Intriguingly,tuples ${R4}$ and ${S1}$ have not been observed at a certain $\omega$ (e.g.,5.1ms).

为具体说明PECJ应用，图3(a)展示待连接元组运行示例（窗口长度$6\mathrm{\;{ms}}$），含流$R$和$S$的6个元组，格式为"键$\left( \kappa \right)$,有效载荷(v),事件时间$\left( {\tau }_{\text{event }}\right.$(毫秒)"。值得注意的是，元组${R4}$和${S1}$在特定$\omega$时刻（如5.1毫秒）未被观测到。

Applying PECJ to the observed data enables us to enumerate the tuples in $R,S$ . This yields ${n}_{S} = 5$ and ${n}_{R} = 5$ respectively (as displayed in Figure 3(b)). Additionally,PECJ detects 4 matches,of which two are under $\kappa  = A$ and the other two fall under $\kappa  = B$ . This leads to a join selectivity [22] $\sigma$ computed as $4/{25}$ . In the case of a ${JOIN} - {COUNT}\left( \right)$ query where the payload $v$ doesn’t affect results, $O$ aligns with the number of matches,resulting in a count of 4 . For a ${JOIN} - {SUM}\left( {R.v}\right)$ query where the $v$ of the joined $R$ is accumulated,we get $O = {20}$ . Moreover,the mean $v$ of the joined $R$ results in ${\alpha }_{R} = {20}/4 = 5$ . Nonetheless,these results do not reflect the true outcome as they exclude contributions from ${R4}$ and ${S1}$ who have not arrived by the $\omega$ .

将PECJ应用于观测数据，可枚举$R,S$中的元组，分别生成${n}_{S} = 5$和${n}_{R} = 5$（如图3(b)所示）。此外，PECJ检测到4个匹配项，其中两个属于$\kappa  = A$，另两个属于$\kappa  = B$。由此计算得出的连接选择性[22]$\sigma$为$4/{25}$。对于${JOIN} - {COUNT}\left( \right)$查询（其有效载荷$v$不影响结果），$O$与匹配数一致，故计数为4。而对于${JOIN} - {SUM}\left( {R.v}\right)$查询（需累加连接$R$的$v$），结果为$O = {20}$。连接$R$的$v$均值则为${\alpha }_{R} = {20}/4 = 5$。但上述结果未包含${R4}$和${S1}$在$\omega$前未到达的贡献，故不能反映真实情况。

To address the discrepancy of unobserved data, PECJ proposes to answer the question, 'what would $O$ appear like if the contributions from unobserved data were factored in?’ To do this,PECJ tackles a PDA problem using a VI approach, as shown in Figure 3(c). In this context, the PDA problem involves using patterns and hidden tendencies within data streams as evidence to estimate ${n}_{R},{n}_{S}$ , $\sigma$ ,and ${\alpha }_{R}$ . This represents an effort to account for the effects of stream oscillation on the observed data, which often distorts the true picture.

为解决未观测数据的偏差问题，PECJ提出核心问题："若纳入未观测数据的贡献，$O$将呈现何种形态？"如图3(c)所示，PECJ采用变分推断(VI)方法处理概率数据关联(PDA)问题，通过挖掘数据流中的模式和隐藏趋势作为证据，估算${n}_{R},{n}_{S}$、$\sigma$及${\alpha }_{R}$。该方法旨在量化流振荡对观测数据的扭曲效应，这类效应常导致真实情况失真。

Unfortunately, exhaustively computing every potential scenario of stream oscillation via brute-force methods is computationally infeasible. For this reason, PECJ adopts the VI approach and maximizes the evidence lower bound (Section 3.1) of describing stream oscillation. This theoretical optimization is practically implemented under the analytical or learning-based approaches, effectively tailoring the posterior distributions of the estimated values.

由于暴力计算流振荡所有潜在场景在计算上不可行，PECJ采用变分推断方法，最大化描述流振荡的证据下界（第3.1节）。该理论优化通过解析法或基于学习的方法实现，有效调整估计值的后验分布。

As an example, PECJ might detect a high probability of a distortion of approximately -1 for ${n}_{S},{n}_{R}$ . This would suggest that the estimated ${n}_{S},{n}_{R}$ should conform to a Gaussian Distribution of $\mathcal{N}\left( {6,{0.2}}\right)$ ,allowing us to use the expected value of 6 to estimate ${n}_{S},{n}_{R}$ . Upon amalgamating these estimated values of ${n}_{R},{n}_{S},\sigma$ ,and ${\alpha }_{R}$ ,PECJ can compute the rectified $O$ . The calculation for the JOIN - COUNT(   ) query would result in

例如，PECJ可能检测到${n}_{S},{n}_{R}$存在约-1的失真概率，表明估计值应符合$\mathcal{N}\left( {6,{0.2}}\right)$的高斯分布，故可用期望值6估算${n}_{S},{n}_{R}$。综合${n}_{R},{n}_{S},\sigma$和${\alpha }_{R}$的估计值后，PECJ可计算修正后的$O$。JOIN-COUNT()查询的计算结果将为

$$
O = \sigma  \times  {n}_{S} \times  {n}_{R}
$$

and for the ${JOIN} - {SUM}\left( {R.v}\right)$ query it would be

而对于${JOIN} - {SUM}\left( {R.v}\right)$查询，结果将是

$$
O = \sigma  \times  {n}_{S} \times  {n}_{R} \times  {\alpha }_{R}
$$

These computations integrate the contributions as if ${R4}$ and ${S1}$ had been present at the time of computation, as illustrated in Figure 3(d).

这些计算整合了贡献值，如同${R4}$和${S1}$在计算时已存在（图3(d)所示）。

### 3.4 Assumptions and Limitations

### 3.4 假设与局限性

PECJ is built upon the theoretical framework of VI, and it shares the common assumption that there exists a variational family of functions, typically the mean-field family, applicable to PDA [8, 12]. In addition, the analytical approach makes the further assumption that the mean-field family should converge to a specific analytical form, which reflects the distortion effects of stream oscillation as a reverse linear effect on the central limit theorem. However, as demonstrated in Figure 12, this assumption of a reverse linear distortion to the central limit theorem proves to be quite restrictive and struggles to accommodate situations with severe stream oscillations. In contrast, learning-based approach relaxes this assumption and only requires that the mean-field family converges within the capacity defined by the universal approximation theorem [10], i.e., the foundation of artificial neural networks. To summarize, our solution is not intended to handle the cases where mean-field family approximation and the universal approximation theorem do not hold, which still remains an untapped territory in the literature.

PECJ基于变分推断理论框架，其基本假设与现有研究一致：存在适用于概率数据关联(PDA)的变分函数族（通常为平均场族）[8,12]。解析法进一步假设平均场族需收敛至特定解析形式，将流振荡的扭曲效应体现为对中心极限定理的逆向线性影响。但图12显示，这种逆向线性扭曲假设限制性较强，难以适应剧烈流振荡场景。相比之下，基于学习的方法放宽该假设，仅要求平均场族在通用近似定理[10]（人工神经网络理论基础）定义的容量内收敛。需说明的是，本方案不适用于平均场族近似和通用近似定理失效的情况，这仍是学界未开发的领域。

## 4 MATHEMATICAL FORMULATION

## 4 数学建模

This section illustrates the detailed mathematical formulation of PECJ. We begin by extracting critical information from the oscillating data streams and formulating a streaming-aware probability model to minimize error propagation (Section 4.1). We then employ VI for efficient model parameterization, ensuring our solution caters to the low-latency demands of SWJ (Section 4.2).

本节阐述PECJ（概率误差补偿连接）的详细数学建模过程。首先从振荡数据流中提取关键信息，建立流式感知概率模型以最小化误差传播（第4.1节）。随后采用变分推断（VI）进行高效模型参数化，确保方案满足流式窗口连接（SWJ）的低延迟需求（第4.2节）。

<!-- Media -->

<!-- figureText: ${\mu }_{w}$ φw ${U}_{i}$ ... ${Z}_{i}$ X3 -->

<img src="https://cdn.noedgeai.com/01965997-3577-7b96-be8c-0534d2f3544b_8.jpg?x=285&y=285&w=349&h=240&r=0"/>

Fig. 4. Probability model.

图4. 概率模型

<!-- figureText: $U$ $U$ $U$ Z Z ${X}_{1}$ ${X}_{2}$ -->

<img src="https://cdn.noedgeai.com/01965997-3577-7b96-be8c-0534d2f3544b_8.jpg?x=839&y=314&w=376&h=189&r=0"/>

Fig. 5. Parameterization as continual learning.

图5. 持续学习式参数化

<!-- Media -->

### 4.1 Formulating the Probability Model

### 4.1 概率模型构建

PECJ approximates the posterior distribution of the total contribution from all tuples within a window, encompassing both observed and unobserved data. This strategy diverges from the approach of predicting individual tuples via time-series predictions [45]. Our solution eliminates the need for per-tuple approximation or compensation, thereby reducing potential error propagation. This propagation originates from the interdependent prediction of tuple number $\left( {{n}_{S},{n}_{R}}\right)$ and the contribution of each tuple to ${\alpha }_{R},\sigma$ (as discussed in Section 2.3). Specifically,PECJ estimates the parameters of the window-averaged total contribution $\left( {\mu }_{w}\right)$ directly,perpetually learning from the data stream observations. ${\mu }_{w}$ is defined as ${\mu }_{w} = \frac{1}{\left| \mathbb{W}\right| }f\left( \mathbb{W}\right)$ . Here, $f\left( \mathbb{W}\right)$ is a scalar function to represent an arbitrary process of the whole $\mathbb{W}$ ,and it’s then normalized by the windowlength $\left| \mathbb{W}\right|$ to define a ${\mu }_{w}.f\left( \mathbb{W}\right)$ does not have to be decomposable per tuple,in order to support an arbitrary window aggregation with scalar result. Each ${\mu }_{w}$ encapsulates a certain type of averaged global information within a window,such as join selectivity $\left( \sigma \right)$ or average payload $\left( \alpha \right)$ in Section 3.3. For the accumulated effects,represented by the $n$ notation,we convert it by the corresponding window average,e.g., $n = {\bar{r}}_{n} \times  \left| \mathbb{W}\right|$ ,where ${\bar{r}}_{n}$ refers to the averaged tuple rate and can also be viewed as a parameter of the window-averaged total contribution. It is crucial to note that $\sigma ,{\alpha }_{R}$ ,and ${\bar{r}}_{n}$ are abstracted in a manner similar to the ${\mu }_{w}$ notation as each of them describes a certain type of window-averaged total contribution. Furthermore, they can be estimated independently, avoiding the prediction dependency mentioned in Section 2.3.

PECJ通过近似窗口内所有元组（包括观测和未观测数据）总贡献的后验分布，区别于传统基于时间序列预测的逐元组预测方法[45]。该方案无需逐元组近似或补偿，从而降低由元组数量$\left( {{n}_{S},{n}_{R}}\right)$与各元组对${\alpha }_{R},\sigma$贡献度的相互依赖预测（如第2.3节所述）引发的误差传播。具体而言，PECJ直接估计窗口平均总贡献$\left( {\mu }_{w}\right)$的参数，持续从数据流观测中学习。${\mu }_{w}$定义为${\mu }_{w} = \frac{1}{\left| \mathbb{W}\right| }f\left( \mathbb{W}\right)$，其中$f\left( \mathbb{W}\right)$是表示整个$\mathbb{W}$任意过程的标量函数，经窗口长度$\left| \mathbb{W}\right|$归一化后形成${\mu }_{w}.f\left( \mathbb{W}\right)$。该函数无需满足元组可分解性，以支持生成标量结果的任意窗口聚合。每个${\mu }_{w}$封装窗口内某类平均全局信息，如第3.3节所述的连接选择率$\left( \sigma \right)$或平均负载$\left( \alpha \right)$。对于$n$表示的累积效应，我们转换为对应窗口平均值，例如$n = {\bar{r}}_{n} \times  \left| \mathbb{W}\right|$，其中${\bar{r}}_{n}$指平均元组速率，亦可视为窗口平均总贡献的参数。需注意$\sigma ,{\alpha }_{R}$与${\bar{r}}_{n}$均采用类似${\mu }_{w}$的抽象表示，各自描述某类窗口平均总贡献，且可独立估计，规避第2.3节所述的预测依赖性。

PECJ employs specific ${\mu }_{w}$ variables such as $\sigma$ to calculate the join aggregation output $O$ (as defined in Section 3.3), thereby facilitating proactive compensation for disorder handling errors. With the corresponding observations $X = \left\{  {{x}_{1},{x}_{2},\ldots }\right\}$ ,we can estimate ${\mu }_{w}$ by approximate the the posterior distribution $p\left( {{\mu }_{w} \mid  X}\right)$ . We might also desire additional parameters ${\varphi }_{w}$ ,such as the inverse variance of ${\mu }_{w}$ estimation,which is connected to the credible interval. Both ${\mu }_{w}$ and ${\varphi }_{w}$ form part of a window’s global information $U$ ,i.e., ${\mu }_{w},{\varphi }_{w} \in  U$ . For a general illustration,we utilize the $p\left( {U \mid  X}\right)$ notation,as it encompasses both $p\left( {{\mu }_{w} \mid  X}\right)$ and $p\left( {{\varphi }_{w} \mid  X}\right)$ . In summary,we are to achieve the following approximation objective:

PECJ采用特定${\mu }_{w}$变量（如$\sigma$）计算连接聚合输出$O$（定义见第3.3节），从而主动补偿乱序处理误差。通过观测值$X = \left\{  {{x}_{1},{x}_{2},\ldots }\right\}$，可近似后验分布$p\left( {{\mu }_{w} \mid  X}\right)$来估计${\mu }_{w}$。还可引入额外参数${\varphi }_{w}$（如${\mu }_{w}$估计的逆方差），该参数与置信区间相关。${\mu }_{w}$与${\varphi }_{w}$共同构成窗口全局信息$U$，即${\mu }_{w},{\varphi }_{w} \in  U$。为通用表述，我们采用$p\left( {U \mid  X}\right)$符号，因其同时涵盖$p\left( {{\mu }_{w} \mid  X}\right)$与$p\left( {{\varphi }_{w} \mid  X}\right)$。综上，我们的近似目标为：

Objective 1. Approximate the $p\left( {U \mid  X}\right)$ ,estimating the $U$ by utilizing its expectation given $X$ ,i.e., $\widehat{U} = \mathbb{E}\left( {U \mid  X}\right) .$

目标1. 近似计算$p\left( {U \mid  X}\right)$，通过利用给定$X$条件下的期望值来估计$U$，即$\widehat{U} = \mathbb{E}\left( {U \mid  X}\right) .$

The dynamics and randomness caused by stream oscillation can cause significant deviations in the observations $X$ from the global $U$ [8]. Unlike evaluating a static dataset $\left\lbrack  {{28},{46}}\right\rbrack$ ,a straightforward statistics approximation will be inaccurate due to the highly distorted observations under stream oscillation. To achieve a better reflection on the effects of stream oscillation, we employ latent variables $Z = \left\{  {{z}_{1},{z}_{2},\ldots }\right\}$ in our model. We use directed arrows to denote probabilistic dependencies in our model,as shown in Figure 4. Specifically,our observations $X$ depend on both the global variables $U$ and the latent variables $Z$ ,while the latent variables $Z$ may also be influenced by the global variables $U$ . Each variable ${z}_{i}$ in $Z$ directly influences specific observations in $X$ ,embodying temporal or local dynamics of the stream oscillation. For instance,in Figure 4, ${z}_{1}$ impacts both ${x}_{1}$ and ${x}_{2}$ ,while ${z}_{2}$ only affects ${x}_{3}$ . To encapsulate a wide spectrum of oscillation patterns,we emphasize that $Z : 1$ ) does not necessarily have to correspond to $X$ in length,2) can contain variables $\left( {z}_{i}\right)$ of any dimension,and 3) might include variables that are influenced by $U$ or other latent variables. By introducing $Z$ ,we can expose patterns and trends in the data streams that might not be immediately noticeable when examining $X$ alone,providing a better reflection on the stream oscillation and therefore achieving more accurate $U$ estimation.

数据流振荡引发的动态性与随机性会导致观测值$X$与全局$U$产生显著偏差[8]。与评估静态数据集$\left\lbrack  {{28},{46}}\right\rbrack$不同，在流振荡下高度失真的观测值会使简单统计近似失效。为更好反映流振荡效应，我们在模型中引入潜变量$Z = \left\{  {{z}_{1},{z}_{2},\ldots }\right\}$。如图4所示，采用有向箭头表示概率依赖关系：观测值$X$同时受全局变量$U$和潜变量$Z$影响，而潜变量$Z$也可能受全局变量$U$调控。每个$Z$中的变量${z}_{i}$会直接影响$X$中的特定观测值，体现流振荡的时序或局部动态特征。例如图4中，${z}_{1}$同时影响${x}_{1}$和${x}_{2}$，而${z}_{2}$仅作用于${x}_{3}$。为涵盖广泛振荡模式，我们强调：1)$Z : 1$长度不必与$X$对应；2)可包含任意维度变量$\left( {z}_{i}\right)$；3)可能含有受$U$或其他潜变量影响的变量。通过引入$Z$，能揭示单独分析$X$时难以察觉的数据流模式与趋势，从而更准确反映流振荡并提升$U$估计精度。

### 4.2 Optimizing Model Parameterization with VI

### 4.2 基于变分推断的模型参数优化

Despite the better reflection of stream oscillation, latent variables entail undesirable exponential computational complexity for parameterizing the probability model, as discussed in Section 3.1. Moreover, we continuously need to update the model parameters to handle new incoming data and promptly make inferences. Therefore, we employ VI [8, 21, 42] for model parameterization. VI is advantageous for PECJ compared with traditional approaches in both accuracy and latency, and it inherently supports continual learning on stream oscillation (Section 3.1). Specifically, PECJ utilizes $\forall I$ to approximate the true posterior $p\left( {U \mid  X}\right)$ in Objective 1,without resorting to brute-force integration or summation on analyzing $U$ and $Z$ . Although the successful use of VI in other problems is acknowledged $\left\lbrack  {8,{20},{44}}\right\rbrack$ ,these existing works aren't designed for the PDA process involved in SWJ. These works are meant for different probability models where estimating the global information $U$ from data streams isn’t required. In the following sections,we delve deeper into our VI approach's mechanics.

尽管潜变量能更好反映流振荡，但如3.1节所述，其会导致概率模型参数化面临指数级计算复杂度。此外，我们需要持续更新模型参数以处理新数据并实时推理。因此采用变分推断(VI)[8,21,42]进行参数化。相比传统方法，VI在PECJ中兼具精度与延迟优势，并天然支持流振荡的持续学习(3.1节)。具体而言，PECJ利用$\forall I$近似目标1中的真实后验$p\left( {U \mid  X}\right)$，避免对$U$和$Z$进行暴力积分或求和。虽然VI在其他问题中的应用已获认可$\left\lbrack  {8,{20},{44}}\right\rbrack$，但这些研究并非针对SWJ涉及的PDA流程设计——它们适用于无需从数据流估计全局信息$U$的不同概率模型。后续章节将深入探讨我们的VI实现机制。

Approximation of $p\left( {U \mid  X}\right)$ . We use variation family of $q\left( \right)$ functions (Section 3.1) to approximate the $p\left( \right)$ distributions,and use the $\approx$ symbol to indicate an approximation process. We illustrate the approximations to our target distribution $p\left( {U \mid  X}\right)$ (Objective 1),conditional prior distribution of $Z \mid  U$ ,and joint distribution of $U,Z$ in Equations 1 to 3,respectively. By decomposing each variable into separate distributions during the approximation, we can apply divide and conquer to each variable and avoid brute force summation or integration.

$p\left( {U \mid  X}\right)$的近似。采用$q\left( \right)$函数变分族(3.1节)逼近$p\left( \right)$分布，并用$\approx$符号表示近似过程。公式1-3分别展示了目标分布$p\left( {U \mid  X}\right)$(目标1)、$Z \mid  U$条件先验分布及$U,Z$联合分布的近似过程。通过在近似时将各变量分解为独立分布，可对每个变量分而治之，避免暴力求和或积分。

$$
q\left( U\right)  = \mathop{\prod }\limits_{{{\mu }_{w} \in  U}}q\left( {\mu }_{w}\right)  \times  \mathop{\prod }\limits_{{{\varphi }_{w} \in  U}}q\left( {\varphi }_{w}\right)  \approx  p\left( {U \mid  X}\right)  \tag{1}
$$

$$
q\left( {Z \mid  U}\right)  = \mathop{\prod }\limits_{{{z}_{i} \in  Z}}q\left( {z}_{i}\right)  \approx  p\left( {\left( {Z \mid  U}\right)  \mid  X}\right)  \tag{2}
$$

$$
q\left( {U,Z}\right)  = q\left( U\right)  \times  q\left( {Z \mid  U}\right)  \approx  p\left( {U,Z \mid  X}\right)  \tag{3}
$$

VI solves an optimization problem of bringing $q\left( \right)$ close to $p\left( \right)$ ,by maximizing the evidence lower bound $\left( {{ELB}{O}_{q}}\right)$ ,as defined in Equation 4. The key insight of Equation 4 is to optimize the utilization on the $X$ (i.e.,used as the evidence) by finding the balance between explaining the observations and retaining uncertainty. The first term, ${\mathbb{E}}_{q}\left( {\log \left( \left( {p\left( {U,Z,X}\right) }\right) \right) \text{,represents the expected log-likelihood}}\right.$ of our observations given the model. It encourages the model to explain the $X$ well. The second term, ${\mathbb{E}}_{q}\left( {\log \left( \left( {q\left( {U,Z}\right) }\right) \right) }\right)$ ,is the entropy of the approximation function $q\left( \right)$ . This term encourages the model to remain uncertain and not commit to a single explanation prematurely. In this way, we can find a good approximation of the posterior $p\left( {U \mid  X}\right)$ .

变分推断（VI）通过最大化证据下界$\left( {{ELB}{O}_{q}}\right)$（如公式4所定义），解决了使$q\left( \right)$逼近$p\left( \right)$的优化问题。公式4的核心思想是通过在解释观测数据与保留不确定性之间找到平衡，优化对$X$（即作为证据使用）的利用率。第一项${\mathbb{E}}_{q}\left( {\log \left( \left( {p\left( {U,Z,X}\right) }\right) \right) \text{,represents the expected log-likelihood}}\right.$表示给定模型时观测数据的对数似然，促使模型充分解释$X$。第二项${\mathbb{E}}_{q}\left( {\log \left( \left( {q\left( {U,Z}\right) }\right) \right) }\right)$是近似函数$q\left( \right)$的熵，该项促使模型保持不确定性，避免过早锁定单一解释。通过这种方式，我们能找到后验分布$p\left( {U \mid  X}\right)$的良好近似。

Objective 2. maximize ${ELB}{O}_{q}$

目标2. 最大化${ELB}{O}_{q}$

$$
\text{ s.t.,}{ELB}{O}_{q} = {\mathbb{E}}_{q}\left( {\log \left( \left( {p\left( {U,Z,X}\right) }\right) \right)  - {\mathbb{E}}_{q}\left( {\log \left( \left( {q\left( {U,Z}\right) }\right) \right) }\right) }\right)  \tag{4}
$$

Continual Learning from Observations. The uncertainties and distortion effects brought by stream oscillation further require the capacity of continual learning, i.e., to assimilate new information progressively, while retaining previously learned knowledge. However, effectively implementing continual learning in the face of endless data streams poses its challenges. Specifically, it’s impractical to store the complete history of $X$ and execute $\mathrm{{VI}}$ for every new addition to $X$ . Thus, we treat model parameterization as a continual learning process as illustrated in Figure 5.

观测数据的持续学习。数据流振荡带来的不确定性与畸变效应，进一步要求系统具备持续学习能力——即在逐步吸收新信息的同时保留已学知识。然而，面对无限数据流实现有效的持续学习存在挑战：存储完整的$X$历史记录并对每次新增数据执行$\mathrm{{VI}}$显然不切实际。因此，我们将模型参数化视为如图5所示的持续学习过程。

Assume that we have drawn insights from a previous observation ${X}_{1}$ and have established approximations for $U$ and $Z$ . These approximations can then be updated with the new observation ${X}_{2}$ ,eliminating the need to recompute using the entire $X = \left\{  {{X}_{1},{X}_{2}}\right\}$ . In line with the method proposed in [12],we employ the prior distribution of $U$ (i.e., $p\left( U\right)$ ) as the initial conditions,with "starting" not indicating a clean slate. The following equation, Equation 5, illustrates this process. The $q\left( {U}_{1}\right)$ ,derived from old observation ${X}_{1}$ ,can act as the new prior distribution. This prior can then be integrated with the impacts from the new observation (i.e., $p\left( {{X}_{2} \mid  U}\right)$ ) to update our approximation. We acknowledge the complexity of continual learning optimization methodologies such as coreset selection [32] and designate them as subjects for future research.

假设我们已从先前的观测${X}_{1}$中获取洞见，并建立了$U$和$Z$的近似值。这些近似值可通过新观测${X}_{2}$进行更新，而无需使用整个$X = \left\{  {{X}_{1},{X}_{2}}\right\}$重新计算。参照[12]提出的方法，我们采用$U$的先验分布（即$p\left( U\right)$）作为初始条件，其中"起始"并非指从零开始。公式5展示了该过程：从旧观测${X}_{1}$推导出的$q\left( {U}_{1}\right)$可作为新的先验分布，该先验再与新观测的影响（即$p\left( {{X}_{2} \mid  U}\right)$）结合以更新近似值。我们承认核心集选择[32]等持续学习优化方法的复杂性，将其列为未来研究方向。

$$
p\left( {U \mid  X}\right)  = p\left( {U \mid  {X}_{1},{X}_{2}}\right)  \propto  p\left( {{X}_{2} \mid  U}\right) p\left( {U \mid  {X}_{1}}\right)  \approx  p\left( {{X}_{2} \mid  U}\right) q\left( {U}_{1}\right)  \tag{5}
$$

## 5 INSTANTIATION OF VI

## 5 变分推断的实例化

Implementing PECJ necessitates the instantiation of the VI equations as delineated in Section 4.2. However,the precise organization and interrelationships between $U$ and $Z$ have substantial implications for PECJ's overhead and versatility, requiring a judicious design approach. This section explores two pragmatic instantiations, initially focusing on the analytical method [21], and subsequently examining the learning-based approach [8, 42]. For each approach, we present an overview at the beginning, then introduce its 1) key derivation steps, 2) conclusions, and 3) implementation usage.

实现PECJ需要如4.2节所述对VI方程进行实例化。但$U$与$Z$的具体组织方式及相互关系会显著影响PECJ的开销与泛用性，需采用审慎的设计策略。本节探讨两种实用实例化方案：先聚焦解析方法[21]，再考察基于学习的方法[8,42]。针对每种方法，我们先概述其原理，再分别介绍1)关键推导步骤，2)结论，以及3)实现用法。

### 5.1 Analytical Instantiation

### 5.1 解析实例化

The analytical instantiation is designed to provide a straightforward interpretation of stream oscillation, and it relies on several assumptions about the oscillation patterns to simplify the instantiation process. In particular,we enforce that the latent variable set $Z$ matches the size of the observations $X$ ,and each ${z}_{i}$ is treated as a scalar. Furthermore, ${z}_{i}$ is typically correlated with a certain physical quantity that causes the stream oscillation.

解析实例化旨在对数据流振荡提供直观解释，其依赖于对振荡模式的若干假设以简化实例化过程。具体而言，我们要求潜变量集$Z$与观测数据$X$的规模匹配，且每个${z}_{i}$被视为标量。此外，${z}_{i}$通常与引发数据流振荡的某种物理量相关联。

Derivation Steps. We undertake a three-step process to extend the central limit theorem's (CLT) applicability to the context of handling stream oscillation with an intuitive example. First, if stream oscillation does not exist,our observations $X = \left\{  {{x}_{1},{x}_{2},\ldots ,{x}_{n}}\right\}$ should approximately match a Gaussian Distribution with mean ${\mu }_{w}$ and inverse variance ${\varphi }_{w}$ . This can be expressed as ${x}_{i} \sim  N\left( {{\mu }_{w},1/{\varphi }_{w}}\right)$ according to the CLT. Several factors as discussed in Section 4.1 support this approximation: 1) We have defined ${\mu }_{w}$ as one factor of the window-averaged total contribution, and 2) Each ${x}_{i}$ observes the same entity, ${\mu }_{w}$ ,and these observations are independently made. To illustrate,suppose ${\mu }_{w} = 1$ ,i.e., ${1K}$ transactions happening at the remote source per second in Section 1’s example. If transaction reporting monopolizes the whole bandwidth,each ${x}_{i}$ should be observed as 1 on average by the online anomaly detection system.

推导步骤。我们通过一个直观示例，采用三步法将中心极限定理(CLT)的适用性扩展到流振荡处理场景。首先，若不存在流振荡，观测值$X = \left\{  {{x}_{1},{x}_{2},\ldots ,{x}_{n}}\right\}$应近似符合均值为${\mu }_{w}$、逆方差为${\varphi }_{w}$的高斯分布。根据CLT可表示为${x}_{i} \sim  N\left( {{\mu }_{w},1/{\varphi }_{w}}\right)$。第4.1节讨论的若干因素支持此近似：1)我们将${\mu }_{w}$定义为窗口平均总贡献的因子之一；2)每个${x}_{i}$观测的是同一实体${\mu }_{w}$，且这些观测相互独立。举例说明，假设${\mu }_{w} = 1$（即第1节示例中远程源每秒发生${1K}$笔交易）。若交易报告独占全部带宽，在线异常检测系统观测到的每个${x}_{i}$平均值应为1。

Second, we introduce three assumptions of reflecting the stream oscillation: 1) stream oscillation independently affects each observation ${x}_{i},2$ ) it is independent of the set of concerned global variables $U$ ,and 3) it can be reflected by single-dimension. For example,let’s assume stream oscillation occurs as the number of stock services (using ${z}_{i}$ to describe) varies over time,and the bandwidth is equally shared by all services. In this case, ${x}_{i}$ will be observed to be $1/2$ on average if ${z}_{i} = 2$ ,as only half of the bandwidth is used for transaction reporting. In essence, we are characterizing stream oscillations by incorporating a reverse linear distortion of $Z$ into the CLT-based approximation. Specifically,we have ${x}_{i} \times  {z}_{i} \sim  N\left( {{\mu }_{w},1/{\varphi }_{w}}\right)$ ,or equivalently ${x}_{i} \sim  \mathcal{N}\left( {{\mu }_{w}/{z}_{i},1/\left( {{z}_{i}^{2}{\varphi }_{w}}\right) }\right)$ . Therefore, the conditional probability function of ${x}_{i}$ is formulated as Equation 6,and it implies that ${x}_{i}$ is influenced not solely by the global mean ${\mu }_{w}$ and global variance $1/{\varphi }_{w}$ of a Gaussian Distribution, but also by the stream oscillations (i.e.,reflected under ${z}_{i}$ ). When we couple Equation 6 with the prior distribution of ${\mu }_{w},{\varphi }_{w},{z}_{i}$ ,denoted as $p\left( {\mu }_{w}\right) ,p\left( {\varphi }_{w}\right) ,p\left( {z}_{i}\right)$ respectively,we can derive the joint distribution function $p\left( {U,Z,X}\right)$ for all variables in Equation 7,where $Z = \left\{  {{z}_{1},{z}_{2},\ldots {z}_{n}}\right\}$ and $X = \left\{  {{x}_{1},{x}_{2},\ldots ,{x}_{n}}\right\}$ . Noted that,the $U \mid  Z$ notations in Equation 2 are simplified into disjoint parts,as we have independent $U,{z}_{i}$ here.

其次，我们提出反映流振荡的三个假设：1)流振荡独立影响每个观测值${x}_{i},2$；2)与相关全局变量集$U$无关；3)可通过单维度反映。例如，假设流振荡表现为股票服务数量（用${z}_{i}$描述）随时间变化，且带宽被所有服务均分。此时若${z}_{i} = 2$，则${x}_{i}$的观测平均值将为$1/2$，因仅半数带宽用于交易报告。本质上，我们通过将$Z$的反向线性畸变引入CLT近似来表征流振荡。具体表现为${x}_{i} \times  {z}_{i} \sim  N\left( {{\mu }_{w},1/{\varphi }_{w}}\right)$或等价式${x}_{i} \sim  \mathcal{N}\left( {{\mu }_{w}/{z}_{i},1/\left( {{z}_{i}^{2}{\varphi }_{w}}\right) }\right)$。因此，${x}_{i}$的条件概率函数如公式6所示，表明其不仅受高斯分布的全局均值${\mu }_{w}$和全局方差$1/{\varphi }_{w}$影响，还受流振荡影响（即${z}_{i}$下的反映）。将公式6与${\mu }_{w},{\varphi }_{w},{z}_{i}$的先验分布（分别记为$p\left( {\mu }_{w}\right) ,p\left( {\varphi }_{w}\right) ,p\left( {z}_{i}\right)$）结合，可推导出公式7中所有变量的联合分布函数$p\left( {U,Z,X}\right)$，其中$Z = \left\{  {{z}_{1},{z}_{2},\ldots {z}_{n}}\right\}$且$X = \left\{  {{x}_{1},{x}_{2},\ldots ,{x}_{n}}\right\}$。注意公式2中的$U \mid  Z$符号简化为互斥部分，因此处$U,{z}_{i}$相互独立。

$$
f\left( {{x}_{i} \mid  {\mu }_{w},{\varphi }_{w},{z}_{i}}\right)  = {e}^{-{\left( {z}_{i} \times  {x}_{i} - {\mu }_{w}\right) }^{2} * {\varphi }_{w}/2} \times  \sqrt{{\varphi }_{w}} \times  \text{ const } \tag{6}
$$

$$
p\left( {U,Z,X}\right)  = \text{ const } \times  {\varphi }_{w}^{n/2} \times  {e}^{\mathop{\sum }\limits_{{i = 1}}^{n}{\left( {z}_{i} \times  {x}_{i} - {\mu }_{w}\right) }^{2} \times  {\varphi }_{w}/2} \times  
$$

$$
p\left( {\mu }_{w}\right) p\left( {\varphi }_{w}\right) \mathop{\prod }\limits_{{i = 1}}^{n}p\left( {z}_{i}\right)  \tag{7}
$$

$$
q\left( {\mu }_{w}\right)  = {\mathbb{E}}_{{\varphi }_{w},Z}\left( {f\left( {U,Z,X}\right) }\right) 
$$

$$
 = \text{const} \times  p\left( {\mu }_{w}\right) {e}^{-{\left( {\mu }_{w} - g\left( X,Z\right) \right) }^{2} \times  \left( {n\mathbb{E}\left( {\varphi }_{w}\right) /2}\right) } \tag{8}
$$

$$
\text{where}g\left( {X,Z}\right)  = \mathop{\sum }\limits_{{i = 1}}^{n}\frac{\mathbb{E}\left( {z}_{i}\right)  * {x}_{i}}{n}
$$

$\exists$ vector $K$ and scalar $b$ ,s.t., $\bar{{\mu }_{w}} = \mathbb{E}\left( {{\mu }_{w} \mid  X}\right)  = {KX} + b$

$\exists$向量$K$与标量$b$，满足$\bar{{\mu }_{w}} = \mathbb{E}\left( {{\mu }_{w} \mid  X}\right)  = {KX} + b$

$$
\text{where}{KX} = \frac{{ng}\left( {X,Z}\right) }{{\tau }_{0} + n},b = \frac{{\tau }_{0}{\mu }_{0}}{{\tau }_{0} + n} \tag{9}
$$

$\forall$ credible interval $\delta  \in  \left( {0,1}\right)$ ,

$\forall$置信区间$\delta  \in  \left( {0,1}\right)$

$$
\bar{{\mu }_{w}} - i\left( \delta \right) \frac{1}{\sqrt{\left( {{\tau }_{0} + n}\right) \mathbb{E}\left( {\varphi }_{w}\right) }} \leq  {\mu }_{w} \leq  \bar{{\mu }_{w}} + i\left( \delta \right) \frac{1}{\sqrt{\left( {{\tau }_{0} + n}\right) \mathbb{E}\left( {\varphi }_{w}\right) }}
$$

$$
\text{where}i\left( \delta \right) \text{is the}\delta \text{interval quantile of a standard Gaussian.} \tag{10}
$$

Third,when VI converges to a mean-field family of $q\left( \right)$ and Equation 4 is achieved,an analytical solution $\left\lbrack  {{10},{21}}\right\rbrack$ exists for $q\left( {\mu }_{w}\right)$ ,as shown in Equation 8. The notation ${\mathbb{E}}_{{\varphi }_{w},Z}$ indicates that the approximations of ${\mu }_{w}$ can be facilitated by the expectations of other variables,specifically ${\varphi }_{w}$ and $Z$ ,rather than performing exhaustive computation of their integration or summation. This is a consequence of the decoupling property inherent to the mean-field family. Moreover, if the prior distribution of ${\mu }_{w}$ is a Gaussian $\mathcal{N}\left( {{\mu }_{0},1/{\tau }_{0}}\right) ,q\left( {\mu }_{w}\right)$ culminates in a Gaussian posterior distribution of ${\mu }_{w}$ expressed as ${\mu }_{w} \sim  \mathcal{N}\left( {\frac{{\tau }_{0}{\mu }_{0} + {ng}\left( {X,Z}\right) }{{\tau }_{0} + n},\frac{1}{\left( {{\tau }_{0} + n}\right) \mathbb{E}\left( {\varphi }_{w}\right) }}\right)$ . Suppose we have observed ${x}_{1} = 1/2$ and ${x}_{2} = 1/3$ ,obtained that $\mathbb{E}\left( {z}_{1}\right)  = 2,\mathbb{E}\left( {z}_{2}\right)  = 3$ through the converging process of the VI,and initially held a prior knowledge that ${\mu }_{0} = {\tau }_{0} = 1$ . In this context,we can construct the posterior distribution of ${\mu }_{w}$ and conclude it should be 1 on average,which aligns well with the truth.

第三，当变分推断(VI)收敛至$q\left( \right)$的平均场族且达成公式4时，$q\left( {\mu }_{w}\right)$存在解析解$\left\lbrack  {{10},{21}}\right\rbrack$，如公式8所示。符号${\mathbb{E}}_{{\varphi }_{w},Z}$表示${\mu }_{w}$的近似可通过其他变量的期望值（特别是${\varphi }_{w}$和$Z$）来简化，而无需进行耗尽的积分或求和计算。这是平均场族固有解耦特性的结果。此外，若${\mu }_{w}$的先验分布为高斯分布$\mathcal{N}\left( {{\mu }_{0},1/{\tau }_{0}}\right) ,q\left( {\mu }_{w}\right)$，则最终会形成表示为${\mu }_{w} \sim  \mathcal{N}\left( {\frac{{\tau }_{0}{\mu }_{0} + {ng}\left( {X,Z}\right) }{{\tau }_{0} + n},\frac{1}{\left( {{\tau }_{0} + n}\right) \mathbb{E}\left( {\varphi }_{w}\right) }}\right)$的高斯后验分布。假设我们已观测到${x}_{1} = 1/2$和${x}_{2} = 1/3$，通过VI收敛过程获得<b11></b1>，并初始持有${\mu }_{0} = {\tau }_{0} = 1$的先验知识。在此背景下，我们可以构建${\mu }_{w}$的后验分布并推断其均值应为1，这与实际情况高度吻合。

Conclusions. We can deduce two crucial insights from the derivation above:

结论。从上述推导中可得出两个关键见解：

(1) The estimated value of ${\mu }_{w}$ (denoted as $\overline{{\mu }_{w}}$ ) behaves like a linear function of $X$ as shown in Equation 9. Notably,the coefficient vector $K$ correlates with the expectations of each latent variable,represented as $\mathbb{E}\left( {z}_{i}\right)$ .

(1) ${\mu }_{w}$的估计值（记为$\overline{{\mu }_{w}}$）表现为$X$的线性函数，如公式9所示。值得注意的是，系数向量$K$与各潜变量的期望值$\mathbb{E}\left( {z}_{i}\right)$相关。

(2) The credible interval for estimating ${\mu }_{w}$ is related to $\mathbb{E}\left( {\varphi }_{w}\right)$ ,as depicted in Equation 10. For example,the ${95}\%$ credible interval is calculated as $\bar{{\mu }_{w}} \pm  {1.96}\frac{1}{\sqrt{\left( {{\tau }_{0} + n}\right) \mathbb{E}\left( {\varphi }_{w}\right) }}$ .

(2) 估计${\mu }_{w}$的置信区间与$\mathbb{E}\left( {\varphi }_{w}\right)$相关，如公式10所示。例如，${95}\%$置信区间计算为$\bar{{\mu }_{w}} \pm  {1.96}\frac{1}{\sqrt{\left( {{\tau }_{0} + n}\right) \mathbb{E}\left( {\varphi }_{w}\right) }}$。

Implementation Usage. We can use Stochastic Variational Inference (SVI) [21] to conduct the analytical instantiation by extending Equation 8 to calculate ${\varphi }_{w}$ and ${z}_{i}$ . We then employ gradient descent to maximize ${ELB}{O}_{q}$ . Technically,gradient descent minimizes functions,but by applying it to the negative of ${ELB}{O}_{q}$ ,we can effectively maximize ${ELB}{O}_{q}$ . Alternatively,given the straightforward linear form, techniques such as the Exponential Moving Average (EMA) or the ARIMA model $\left\lbrack  {{18},{36}}\right\rbrack$ can also be applied. However,a distinguishing aspect of our scenario is that the parameters of the filter should dynamically evolve with the data streams, rather than being preset. This dynamic adaptability ensures accurate on-the-fly approximation of $\mathbb{E}\left( {z}_{i}\right)$ .

实施应用。我们可采用随机变分推断(SVI)[21]进行解析实例化：扩展公式8计算${\varphi }_{w}$和${z}_{i}$，再运用梯度下降最大化${ELB}{O}_{q}$。技术上梯度下降用于最小化函数，但通过作用于${ELB}{O}_{q}$的负值可有效实现最大化。或者，鉴于其简单线性形式，亦可应用指数移动平均(EMA)或ARIMA模型$\left\lbrack  {{18},{36}}\right\rbrack$。但本场景的特殊性在于滤波器参数应随数据流动态演化而非预设，这种动态适应性确保了对$\mathbb{E}\left( {z}_{i}\right)$的实时精确近似。

By default, PECJ employs a variant of the EMA, which we term as an Adaptive EMA (AEMA). In AEMA, the decay parameter of the EMA is not fixed but continuously updated based on rule-based learning from the data streams. This choice is motivated by the expectation that an adaptive approach will incur significantly less overhead compared to SVI, while also being simpler to design and adjust.

默认情况下，PECJ采用了一种改进型指数移动平均（EMA）方法，我们称之为自适应EMA（AEMA）。在AEMA中，EMA的衰减参数并非固定值，而是基于数据流的规则学习持续更新。这一设计源于以下考量：相比SVI（随机变分推断），自适应方法预计将显著降低计算开销，同时更易于设计和调整。

### 5.2 Learning-based Instantiation

### 5.2 基于学习的实例化

Although more intricate $U,Z$ relationships are possible to represent more complex patterns of stream oscillation, this approach demands significant manual effort and potentially leads to an impractical implementation (see Appendix for details). To overcome the challenges of capturing complex stream oscillation, we refer back to the abstract ELBO definition in Equation 4 for a more universal solution. This approach eliminates the need for prior knowledge or additional assumptions about $Z$ and its interactions with $U$ . Instead,we treat $Z$ as a learnable black box,without knowing about its size, element dimensions, or dependency relationships.

虽然通过更复杂的$U,Z$关系可以表征流振荡的复杂模式，但这种方法需要大量人工干预，并可能导致实施方案不可行（详见附录）。为攻克复杂流振荡建模的难题，我们回溯到公式4中的抽象ELBO定义以寻求普适性解决方案。该方法无需预先了解$Z$及其与$U$交互作用的先验知识或附加假设，而是将$Z$视为可学习的黑箱，无需知晓其规模、元素维度或依赖关系。

Derivation Steps. We prove the learnable effects of stream oscillation by four major steps of parameter remapping and the divide-and-conquer policy as follows. First, we remap the entire parameter space of $U$ and $Z$ into another space, $H = \left\{  {{h}_{1},{h}_{2},\ldots ,{h}_{m}}\right\}$ ,i.e., $U,Z \rightarrow  H$ . Hence, Equation 4 can be rewritten as Equation 11. Second,we further constrain $H$ by ensuring 1 ) the independent ${\mu }_{w}$ and ${\varphi }_{w}$ presented in Equation 6 and Equation 7 are assigned to ${h}_{1}$ and ${h}_{2}$ , respectively,and 2) the remaining factors ${h}_{3},{h}_{4},\ldots {h}_{m}$ form an Orthogonal Basis (i.e.,they are independent of each other) given ${h}_{1},{h}_{2}$ . As a result,the $\log \left( \left( {p\left( {H,X}\right) }\right) \right.$ term can be decomposed as shown in Equations 12~13. Note that $\log \left( \left( {p\left( {X \mid  H}\right) }\right) \right)$ is the $\log$ -likelihood of $X$ in the $H$ space, and $\log \left( \left( {p\left( {h}_{i}\right) }\right) \right)$ is the log-prior-distribution of ${h}_{i}$ . As both are irrelevant to $q$ (   ) functions,we can conveniently remove the ${\mathbb{E}}_{q}$ notations. Third,based on the mean-field property [10,21], ${\mathbb{E}}_{q}\left( {\log \left( {q\left( H\right) }\right) }\right)$ can be further decomposed as per Equation 14. Finally,by separating $q\left( {\mu }_{w}\right)$ and $q\left( {\varphi }_{w}\right)$ from the other $q\left( {h}_{i}\right)$ ,we can derive Equation 15,and each item in Equation 15 is a scalar value.

推导步骤。我们通过参数重映射的四步策略和分治法证明了流振荡的可学习效应：首先将$U$和$Z$的整个参数空间重映射至$H = \left\{  {{h}_{1},{h}_{2},\ldots ,{h}_{m}}\right\}$空间（即$U,Z \rightarrow  H$），从而将公式4改写为公式11；其次通过双重约束重构$H$空间：1）将公式6-7中的独立变量${\mu }_{w}$和${\varphi }_{w}$分别分配给${h}_{1}$和${h}_{2}$，2）剩余因子${h}_{3},{h}_{4},\ldots {h}_{m}$在给定${h}_{1},{h}_{2}$条件下构成正交基（即彼此独立），最终将$\log \left( \left( {p\left( {H,X}\right) }\right) \right.$项分解如公式12~13所示。其中$\log \left( \left( {p\left( {X \mid  H}\right) }\right) \right)$表示$X$在$H$空间中的$\log$似然度，$\log \left( \left( {p\left( {h}_{i}\right) }\right) \right)$为${h}_{i}$的对数先验分布，因二者均与$q$函数无关，故可移除${\mathbb{E}}_{q}$标记；再次基于平均场理论[10,21]，${\mathbb{E}}_{q}\left( {\log \left( {q\left( H\right) }\right) }\right)$可按公式14进一步分解；最终通过分离$q\left( {\mu }_{w}\right)$和$q\left( {\varphi }_{w}\right)$与其他$q\left( {h}_{i}\right)$项，推导出公式15，其中各项均为标量值。

$$
{ELB}{O}_{q} = {\mathbb{E}}_{q}(\log \left( \left( {p\left( {H,X}\right) }\right) \right)  - {\mathbb{E}}_{q}(\log \left( \left( {q\left( H\right) }\right) \right)  \tag{11}
$$

$$
 = {\mathbb{E}}_{q}\left( {\log \left( \left( {p\left( {X \mid  H}\right) p\left( H\right) }\right) \right) }\right)  - {\mathbb{E}}_{q}(\log \left( \left( {q\left( H\right) }\right) \right)  \tag{12}
$$

$$
 = \log \left( {p\left( {X \mid  H}\right) }\right)  + \log \left( {p\left( {\mu }_{w}\right) }\right)  + \log \left( {p\left( {\varphi }_{w}\right) }\right) 
$$

$$
 + \mathop{\sum }\limits_{{i = 3}}^{m}\log \left( {p\left( {{h}_{i} \mid  {\mu }_{w},{\varphi }_{w}}\right) }\right)  - {\mathbb{E}}_{q}\left( {\log \left( {q\left( H\right) }\right) }\right)  \tag{13}
$$

$$
 = \log \left( {p\left( {X \mid  H}\right) }\right)  + \log \left( {p\left( {\mu }_{w}\right) }\right)  + \log \left( {p\left( {\varphi }_{w}\right) }\right) 
$$

$$
 + \mathop{\sum }\limits_{{i = 3}}^{m}\log \left( {p\left( {{h}_{i} \mid  {\mu }_{w},{\varphi }_{w}}\right) }\right)  - \left( {\mathop{\sum }\limits_{i}{\mathbb{E}}_{q}\left( {\log \left( {q\left( {h}_{i}\right) }\right) }\right) }\right)  \tag{14}
$$

$$
 = \log \left( {p\left( {X \mid  H}\right) }\right)  + \log \left( {p\left( {\mu }_{w}\right) }\right)  + \log \left( {p\left( {\varphi }_{w}\right) }\right) 
$$

$$
 + \mathop{\sum }\limits_{{i = 3}}^{m}\log \left( {p\left( {{h}_{i} \mid  {\mu }_{w},{\varphi }_{w}}\right) }\right)  - \left( {\mathop{\sum }\limits_{{i = 3}}^{m}{\mathbb{E}}_{q}\left( {\log \left( {q\left( {h}_{i}\right) }\right) }\right) }\right. 
$$

$$
 + \log \left( \underline{\mathbb{E}\left( {{\mu }_{w} \mid  X}\right) }\right)  + \log \left( \underline{\mathbb{E}\left( {{\varphi }_{w} \mid  X}\right) }\right)  \tag{15}
$$

Conclusions. Similar to Section 5.1’s case,the resulting $\mathbb{E}\left( {{\mu }_{w} \mid  X}\right)$ and $\mathbb{E}\left( {{\varphi }_{w} \mid  X}\right)$ can be directly utilized for the estimated value in PECJ's error compensation, as discussed in Section 4.1. Moreover, Equation 15 can further be leveraged to regulate the behavior of neural networks (NNs), enabling them to conform to the PDA process through an ELBO-driven learning process as follows:

结论。与5.1节类似，所得$\mathbb{E}\left( {{\mu }_{w} \mid  X}\right)$和$\mathbb{E}\left( {{\varphi }_{w} \mid  X}\right)$可直接用于PECJ误差补偿的估计值（如4.1节所述）。此外，公式15可进一步用于调控神经网络（NN）行为，使其通过ELBO驱动学习过程遵循PDA流程：

(1) Construct an NN for function fitting, ensuring that the final output is at least seven-dimensional to correspond with the seven scalars depicted in Equation 15.

(1) 构建用于函数拟合的神经网络，确保最终输出至少为七维以对应公式15中的七个标量；

(2) Conduct supervised pre-training over the entire NN so that each dimension accurately estimates the target scalar,such as $\log \left( {\mathbb{E}\left( {{\mu }_{w} \mid  X}\right) }\right)$ . Given that pre-training is fundamentally a function-fitting process, loss functions that have been originally designed for fitting, such as the mean square error, are appropriately suitable for this task.

(2) 对整个网络进行监督预训练，使各维度精确估计目标标量（如$\log \left( {\mathbb{E}\left( {{\mu }_{w} \mid  X}\right) }\right)$）。鉴于预训练本质是函数拟合过程，原设计用于拟合的损失函数（如均方误差）完全适用于此任务。

(3) During continual learning in a streaming environment, the whole Equation 15 can be employed to optimize NN loss. For example, if gradient descent is implemented via ADAM or SGD [4],the loss function can be designed to decrease monotonically with ${ELB}{O}_{q}$ . Note that, if the NN is overly ’confident,the numerical evaluation of ${ELB}{O}_{q}$ could potentially be $\infty$ . In such instances,we use bounded functions such as $- \operatorname{sigmoid}\left( {{ELB}{O}_{q}}\right)$ as the loss function.

(3) 在流式环境下的持续学习过程中，可采用完整公式15来优化神经网络损失。例如，若通过ADAM或SGD[4]实现梯度下降，可将损失函数设计为随${ELB}{O}_{q}$单调递减。需注意，当神经网络过度"自信"时，${ELB}{O}_{q}$的数值评估可能$\infty$。此类情况下，我们采用有界函数如$- \operatorname{sigmoid}\left( {{ELB}{O}_{q}}\right)$作为损失函数。

Implementation Usage. In PECJ, we implemented a straightforward multilayer perceptron (MLP) to briefly illustrate this concept, leaving more powerful structures like LSTM [8] or transformer [42] for future exploration. Furthermore, given the necessity for NNs to meet low latency requirements, it's critical to efficiently perform their inference and learning processes. As a result, an effective solution for deploying PECJ across various dynamic situations is to integrate a well-structured NN with high-performance computing. Pursuing this combination represents an important area of ongoing work.

实现应用。在PECJ中，我们实现了一个简易多层感知机(MLP)来简要说明该概念，将LSTM[8]和Transformer[42]等更强大的结构留待未来探索。此外，鉴于神经网络需满足低延迟要求，高效执行其推理和学习过程至关重要。因此，在各种动态场景中部署PECJ的有效解决方案是将结构良好的神经网络与高性能计算相结合。追求这种组合是当前工作的重要方向。

## 6 EVALUATION

## 6 评估

In this section, we present a comprehensive evaluation of PECJ in comparison with other state-of-the-art techniques. In summary, across various aspects of our investigation, we have made the following key observations.

本节将PECJ与其他前沿技术进行全面对比评估。总体而言，在研究的各个方面中，我们得出以下关键发现。

- PECJ has consistently proven superior in managing disordered data. From an end-to-end comparison with two popular state-of-art algorithms [9, 22], PECJ emerged as more effective, maintaining lower error rates even under intricate disorder arrival patterns and lenient real-time requirements (Section 6.3).

- PECJ在处理无序数据方面持续表现出优越性。通过与两种主流算法[9,22]的端到端对比，PECJ展现出更高效率，即使在复杂的无序到达模式和宽松实时要求下仍保持较低错误率（第6.3节）。

- The efficiency of PECJ was further validated under different workload conditions and algorithm configurations. In particular, PECJ can handle a severe stream oscillation (i.e., where the arrival delay of tuples oscillates from $0 \sim  {1000}\mathrm{{ms}}$ ) with low error (i.e.,4.2%) by using learning-based instantiation,i.e., ${PEC}{\mathcal{J}}_{\text{learning }}$ (Section 6.4).

- PECJ的效率在不同工作负载条件和算法配置下得到进一步验证。特别地，通过基于学习的实例化（即${PEC}{\mathcal{J}}_{\text{learning }}$），PECJ能以较低错误率（4.2%）处理严重的流振荡（即元组到达延迟在$0 \sim  {1000}\mathrm{{ms}}$间波动）（第6.4节）。

- Lastly, the integration of PECJ into PRJ and SHJ demonstrated substantial error rate reductions without significantly impacting latency or scalability (Section 6.5).

- 最后，将PECJ集成至PRJ和SHJ中，在未显著影响延迟或可扩展性的前提下实现了错误率的大幅降低（第6.5节）。

### 6.1 Experimental Setup

### 6.1 实验设置

We established a robust experimental setup to thoroughly evaluate the performance of PECJ. The various components of this setup are detailed below.

我们建立了完善的实验环境以全面评估PECJ性能，该设置的各个组件详述如下。

Server: The experiments were conducted on a state-of-the-art multicore server powered by Intel Xeon Gold 6252 processors, which feature 24 cores and support 2 threads per core through HyperThreading. The server has a considerable L3 cache size of ${35.75}\mathrm{{MB}}$ and a massive memory capacity of 384GB. It operates on the Ubuntu 22.04 system and uses the g++ 11.3.0 compiler for the compilation of the source codes.

服务器：实验在配备Intel至强金牌6252处理器的先进多核服务器上进行，该处理器具有24个核心并通过超线程技术支持每核2线程。服务器拥有${35.75}\mathrm{{MB}}$的大容量L3缓存和384GB内存，运行Ubuntu 22.04系统，使用g++ 11.3.0编译器进行源代码编译。

Datasets: The evaluation was carried out using a diverse collection of four widely-used real-world datasets - Stock, Rovio, Logistics, Retail, and a synthetic dataset known as Micro. Stock is based on a real-world stock exchange dataset [3]. Micro and Rovio are from recent benchmark studies [26, 49]. Logistics and Retail datasets were obtained from a recent open source project [48]. Stock are the streams of financial quotes and trades, while Rovio continuously monitors user actions within a specific game. Additionally, Logistics and Retail involve streams of online decision augmentation labels and actions in logistics and retail applications, respectively. For more detailed characteristics, please consult Table 3 in [49] and Table 3 in [48]. To simulate a realistic scenario of stream oscillation, we introduced disorder in the data arrival by reordering the arrival timestamps ${\tau }_{\text{arrival }}$ differently from the event timestamps ${\tau }_{\text{emit }}$ (as mentioned in Section 2). The difference between ${\tau }_{\text{arrival }}$ and ${\tau }_{\text{emit }}$ ,i.e., $\delta$ ,was set randomly for all tuples. We kept the event rate (controlled by event timestamp ${\tau }_{\text{emit }}$ ) of both $\mathrm{R}$ and $\mathrm{S}$ streams consistent at ${100}\mathrm{K}$ tuples $/\mathrm{s}$ unless stated otherwise. By default, we employ the Stock datasets, which align with the motivating example presented in Section 1. To ensure a thorough evaluation, other datasets are utilized.

数据集：评估采用四种广泛使用的真实数据集（Stock、Rovio、Logistics、Retail）和一个合成数据集Micro。Stock基于真实股票交易数据集[3]，Micro和Rovio来自近期基准研究[26,49]，Logistics和Retail数据集取自最新开源项目[48]。Stock是金融报价和交易流，Rovio持续监测特定游戏中的用户行为，Logistics和Retail则分别涉及物流和零售应用中的在线决策增强标签流与操作流。更多特征细节请参阅文献[49]表3和[48]表3。为模拟真实的流振荡场景，我们通过将到达时间戳${\tau }_{\text{arrival }}$与事件时间戳${\tau }_{\text{emit }}$（如第2节所述）进行不同排序来制造数据到达紊乱。所有元组的${\tau }_{\text{arrival }}$与${\tau }_{\text{emit }}$间差值（即$\delta$）均随机设定。除非特别说明，$\mathrm{R}$和$\mathrm{S}$流的事件速率（由事件时间戳${\tau }_{\text{emit }}$控制）均保持为每秒${100}\mathrm{K}$个元组$/\mathrm{s}$。默认使用与第1节示例对应的Stock数据集，其他数据集用于确保评估全面性。

Queries: Three different queries were employed in our evaluation. Q1: This query entails a SWJ aggregated by COUNT (Section 3.3),with a $\left| \mathbb{W}\right|$ of ${10}\mathrm{\;{ms}}$ ,and a maximum value of $\delta$ among all tuples,i.e., $\Delta$ ,set as $5\mathrm{\;{ms}}$ . The small $\Delta$ is representative of a scenario where the stream processing is geographically close to the data source, such as on the edge of a cloud network [47]. Q2: This query modifies $\mathbf{{Q1}}$ by changing the aggregation function to SUM (Section 3.3),with all other settings retained as per $\mathbf{{Q1}}$ . $\mathbf{{Q3}}$ : This query extends $\mathbf{{Q1}}$ by altering the disordered arrival pattern of data and setting the $\Delta$ to ${1000}\mathrm{\;{ms}}$ . The significant $\Delta$ simulates situations where the stream analytic is situated far from the data source, such as during multiple intercontinental communications within a TOR network [16].

查询：我们在评估中采用了三种不同查询。Q1：该查询包含一个按COUNT聚合的SWJ（第3.3节），其$\left| \mathbb{W}\right|$为${10}\mathrm{\;{ms}}$，所有元组中的最大值为$\delta$，即$\Delta$设为$5\mathrm{\;{ms}}$。较小的$\Delta$代表流处理在地理上接近数据源的场景，例如云网络边缘[47]。Q2：该查询通过将聚合函数改为SUM（第3.3节）来修改$\mathbf{{Q1}}$，其余设置与$\mathbf{{Q1}}$保持一致。$\mathbf{{Q3}}$：该查询通过改变数据无序到达模式并将$\Delta$设为${1000}\mathrm{\;{ms}}$来扩展$\mathbf{{Q1}}$。较大的$\Delta$模拟流分析远离数据源的情景，例如TOR网络中多次跨大陆通信[16]。

While $\mathbf{Q}\mathbf{1}$ and $\mathbf{Q}\mathbf{2}$ are tailored to require ultra-low latency processing,typically tens of milliseconds or less [1], Q3 cannot expect such low latency due to the large arrival delay. Nonetheless,the goal is to achieve a latency below ${200}\mathrm{\;{ms}}$ as discussed in our motivating example in Section 1.

虽然$\mathbf{Q}\mathbf{1}$和$\mathbf{Q}\mathbf{2}$专为需要超低延迟处理（通常为数十毫秒或更短[1]）而设计，但由于较大的到达延迟，Q3无法实现如此低的延迟。尽管如此，如第1节动机案例所述，目标是将延迟控制在${200}\mathrm{\;{ms}}$以下。

### 6.2 Implementation Details

### 6.2 实现细节

In our evaluation, we scrutinize the performance of PECJ using two distinct setups: standalone and integrated implementations. Each setup facilitates a comprehensive comparison with different existing approaches. Note that,while the automatic determination of suitable $\omega$ is orthogonal to this work, it serves as a tuning knob for all mechanisms during the experiments. Specifically, we set $\omega$ to $\left| \mathbb{W}\right|$ of three queries,i.e., ${10}\mathrm{\;{ms}}$ by default and manually tune it in the experiments.

评估中，我们通过独立实现和集成实现两种设置来严格检验PECJ性能。每种设置均支持与现有方法的全面对比。需注意，虽然自动确定合适$\omega$与本工作正交，但实验中它作为所有机制的调节旋钮。具体而言，我们将$\omega$默认设为三个查询的$\left| \mathbb{W}\right|$（即${10}\mathrm{\;{ms}}$），并在实验中手动调整。

A) Standalone Implementation: In the standalone implementation setup, we're aiming for an algorithmic comparison between PECJ and two existing methodologies, namely K-Slack-Join (KSJ) [22] and Watermark-Join (WMJ) [9]. For these standalone implementations, we employed the same $\mathrm{C} +  +$ codebase for ${KS}\mathcal{J},{WM}\mathcal{J}$ ,and PECJ.

A) 独立实现：该设置旨在对比PECJ与两种现有方法——K-Slack-Join (KSJ)[22]和Watermark-Join (WMJ)[9]的算法性能。对于这些独立实现，我们使用相同的$\mathrm{C} +  +$代码库实现${KS}\mathcal{J},{WM}\mathcal{J}$和PECJ。

Our implementation of PECJ included three separate approaches for the analytical and learning-based approaches. For the former (discussed in Section 5.1), we utilized both the Adaptive Exponential Moving Average (AEMA) and Stochastic Variational Inference (SVI) instantiations. For learning-based (Section 5.2), we opted for a simple learning approach of Multi-Layer Perceptron (MLP). The AEMA instantiation served as the default configuration for PECJ's analytical approach.

我们的PECJ实现包含分析型和基于学习型两类共三种方法。前者（第5.1节）采用自适应指数移动平均(AEMA)和随机变分推理(SVI)实现；后者（第5.2节）选用多层感知机(MLP)的简单学习方案。AEMA实现作为PECJ分析型方法的默认配置。

KSJ uses a k-slack buffer approach to manage the disorder in data streams. After data streams are preprocessed through the k -slack buffer, KSJ conducts a standard hash-join operation, treating the data as ordered. Importantly,our tuning parameter, $\omega$ ,is tied to the k -slack buffer’s control conditions,as discussed in Section 2. On the other hand, WMJ applies the watermark mechanism [9] for data preprocessing,eliminating the need for a k-slack buffer. Each watermark indicates the arrival of tuples with ${\tau }_{\text{event }} < T$ ,enabling the computation to commence early upon watermarks’ arrival. However,the emission of $O$ waits until the $\omega$ is reached.

KSJ采用k-slack缓冲区管理数据流乱序。数据流经k-slack缓冲预处理后，KSJ执行标准哈希连接操作并视数据为有序。关键的是，如第2节所述，我们的调节参数$\omega$与k-slack缓冲控制条件绑定。而WMJ应用水印机制[9]进行数据预处理，无需k-slack缓冲。每个水印表示具有${\tau }_{\text{event }} < T$的元组到达，使计算可提前启动。但$O$的发射需等待$\omega$条件满足。

<!-- Media -->

<!-- figureText: 41.5 WMJ PEC. Error (%) KSJ 43.8 PECJ Latency (ms) 21.9 (c) Latency CDF under $\omega  =$ 0.0 Tuning knob $\omega$ (ms) 12ms 12 Tuning knob $\omega \left( \mathrm{{ms}}\right)$ Tuning knob $\omega$ (ms) (a) ${95}\%$ latency (b) $\epsilon$ comparison -->

<img src="https://cdn.noedgeai.com/01965997-3577-7b96-be8c-0534d2f3544b_15.jpg?x=144&y=282&w=1284&h=216&r=0"/>

Fig. 6. End-to-end comparison of Q1.

图6. Q1的端到端对比

Fig. 7. $\epsilon$ comparison of

图7. $\epsilon$对比

Q2.

<!-- figureText: 514.5 Error (%) Probability (%) 75 -PEGU 50 WMA PEGJ Latency (ms) 95% latency (ms) (c) Latency CDF under $\omega  =$ (d) Trade-off space. 600ms 343.0 KSJ 171.5 $\operatorname{PECJ}\left( {\omega  - {100}}\right)$ 0.0 Tuning knob $\omega$ (ms) Tuning knob $\omega$ (ms) (a) ${95}\%$ latency (b) $\epsilon$ comparison -->

<img src="https://cdn.noedgeai.com/01965997-3577-7b96-be8c-0534d2f3544b_15.jpg?x=148&y=642&w=1271&h=217&r=0"/>

Fig. 8. End-to-end comparison of Q3. PECJ (ω-100) refers to subtracting the $\omega$ of PECJ by 100ms.

图8. Q3的端到端对比。PECJ(ω-100)表示将PECJ的$\omega$减少100毫秒

<!-- figureText: [222] 95% latency (ms) 0.12 0.09 0.063 0.03 0.00 0.0 MMJ -->

<img src="https://cdn.noedgeai.com/01965997-3577-7b96-be8c-0534d2f3544b_15.jpg?x=150&y=1061&w=318&h=132&r=0"/>

Fig. 9. Evaluation under in-order data.

图9. 有序数据下的评估

<!-- figureText: Event Rate (KTuple/s) Event Rate (KTuple/s) (b) ${95}\%$ latency of varying (c) $\epsilon$ by varying event rate #Key (a) $\epsilon$ by varying key number event rate -->

<img src="https://cdn.noedgeai.com/01965997-3577-7b96-be8c-0534d2f3544b_15.jpg?x=472&y=1002&w=931&h=219&r=0"/>

Fig. 10. Impacts of key number and event rate.

图10. 关键参数与事件速率的影响

<!-- Media -->

B) Integrated Implementations: This setup is designed to assess PECJ's performance when incorporated into an existing multi-threaded stream processing system AllianceDB [49], which is a recent multi-threaded SWJ testbed and serves as our integration platform. In this environment, we selected two representative parallel SWJ algorithms, Parallel Radix Join (PRJ) and Symmetric Hash Join (SHJ), to perform our assessment.

B) 集成实施方案：该配置旨在评估PECJ（性能评估组件）在集成至现有多线程流处理系统AllianceDB [49]时的表现。AllianceDB是最新的多线程流式窗口连接(SWJ)测试平台，作为我们的集成平台。在此环境中，我们选择了两种具有代表性的并行SWJ算法——并行基数连接(PRJ)与对称哈希连接(SHJ)进行评估。

PRJ adopts a 'lazy' approach, delaying the join operation until all tuples have arrived. Conversely, SHJ pursues an 'eager' strategy, initiating the join process as soon as a portion of tuples arrives. Both PRJ and SHJ operate under the assumption of in-order arrival, and consider a window complete when the first tuple’s arrival timestamp $\left( {\tau }_{\text{arrival }}\right)$ surpasses the window’s boundary.

PRJ采用"惰性"策略，延迟连接操作直至所有元组到达。相反，SHJ采用"积极"策略，当部分元组到达时立即启动连接过程。PRJ和SHJ均基于顺序到达假设运行，并当首个元组的到达时间戳$\left( {\tau }_{\text{arrival }}\right)$超过窗口边界时，视该窗口为完整。

### 6.3 End-to-End Comparison

### 6.3 端到端对比

We initiate our analysis by juxtaposing PECJ, KSJ, and WMJ under the conditions stipulated by Q1~Q3 using the Stock dataset. The assumed time point of window completeness $\omega$ is fine-tuned to $7\mathrm{\;{ms}},{10}\mathrm{\;{ms}}$ ,and ${12}\mathrm{\;{ms}}$ for each methodology under $\mathbf{{Q1}}$ and $\mathbf{{Q2}}$ ,and to ${200}\mathrm{\;{ms}},{300}\mathrm{\;{ms}}$ ,and ${600}\mathrm{\;{ms}}$ under Q3. We further include an in-order case for a more comprehensive comparison.

我们首先在Q1~Q3条件下使用Stock数据集对PECJ、KSJ和WMJ进行并列分析。窗口完整性假设时间点$\omega$在$\mathbf{{Q1}}$和$\mathbf{{Q2}}$下分别微调为$7\mathrm{\;{ms}},{10}\mathrm{\;{ms}}$和${12}\mathrm{\;{ms}}$，在Q3条件下调整为${200}\mathrm{\;{ms}},{300}\mathrm{\;{ms}}$和${600}\mathrm{\;{ms}}$。为进一步完善对比，我们还纳入了顺序处理案例。

<!-- Media -->

<!-- figureText: 100 100 Error (%) 12 (b) $\mathrm{Q}3$ KSJ PECJanalytical 25 PECJ learning 11 (a) Q1 -->

<img src="https://cdn.noedgeai.com/01965997-3577-7b96-be8c-0534d2f3544b_16.jpg?x=170&y=280&w=852&h=235&r=0"/>

Fig. 11. Impacts of Algorithm Configuration.

图11. 算法配置影响

<!-- figureText: 100 600 800 1000 $\Delta$ (ms) Error (%) 200 400 -->

<img src="https://cdn.noedgeai.com/01965997-3577-7b96-be8c-0534d2f3544b_16.jpg?x=1035&y=270&w=359&h=174&r=0"/>

Fig. 12. Impacts of stream oscillation. The larger $\Delta$ ,the severer oscillation.

图12. 流振荡影响。$\Delta$值越大，振荡越剧烈

<!-- Media -->

Comparison under Q1. We apply the analytical instantiation in PECJ,i.e., PECJ analytical is deployed. We elucidate the ensuing ${95}\%$ processing latency $\left( {{95}\% l}\right)$ and relative error $\left( \epsilon \right)$ in Figures 6(a) and 6(b). We also report the cumulative distribution function (CDF) of processing latency under $\omega  = {12}\mathrm{\;{ms}}$ in Figure 6(c). Three critical insights emerge from this comparative analysis. Initially,it is observed that for the same $\omega$ ,each strategy incurs a similar latency,as depicted in Figures 6(a) and 6(c). This congruity arises mainly due to the similar overhead incurred from waiting for a more comprehensive window of data. Relative to this waiting overhead, the specific overheads engendered by ${WM}\mathfrak{J},{KS}\mathfrak{J}$ ,and PECJ are marginal. The extra overhead of ${PEC}{\mathfrak{J}}_{\text{analytical }}$ to conduct error compensation and update its model is within ${1ms}$ in total,thanks to the proven straightforward linear form (Equations 8, 9, and 10). Secondly, as anticipated, the error generated by ${WM}\mathcal{J}$ and ${KS}\mathcal{J}$ exhibits similarity and consistently decreases with larger $\omega$ values. Despite their distinct mechanisms for handling disordered data, they have an identical level of data completeness within a given window under the same $\omega$ . Consequently,their ignorance extent towards unobserved data also aligns. Most notably, PECJ manifests its superior performance in significantly lower errors compared to ${WM}\mathcal{J}$ and ${KS}\mathcal{J}$ . For instance,when $\omega$ is set to $7\mathrm{{ms}}$ ,PECJ can maintain an error as low as $\leq  {16}\%$ with a ${95}\% l$ of $\leq  {5.5}\mathrm{\;{ms}}$ . In contrast,WMJ and KSJ register an error in excess of ${20}\%$ ,even when the ${95}\% l$ escalates above ${9.5}\mathrm{\;{ms}}$ by setting $\omega$ to ${12}\mathrm{\;{ms}}$ . As expounded earlier,this improved performance is attributed to PECJ's proactive strategy of incorporating the contributions of unobserved data,unlike the passive waiting approach of ${WM}\oint$ and ${KS}\oint$ (Section 3).

Q1条件下的对比。我们在PECJ中应用解析实例化（即部署PECJ解析模式），通过图6(a)和6(b)阐明随之产生的${95}\%$处理延迟$\left( {{95}\% l}\right)$与相对误差$\left( \epsilon \right)$，并在图6(c)中展示$\omega  = {12}\mathrm{\;{ms}}$条件下处理延迟的累积分布函数(CDF)。该对比分析揭示三个关键发现：首先，如图6(a)和6(c)所示，相同$\omega$条件下各策略产生的延迟相近，这主要源于等待更完整数据窗口产生的相似开销。相较于等待开销，${WM}\mathfrak{J},{KS}\mathfrak{J}$和PECJ产生的特定开销可忽略不计。由于已验证的简单线性形式（公式8、9、10），${PEC}{\mathfrak{J}}_{\text{analytical }}$执行误差补偿及模型更新的额外开销总计不超过${1ms}$。其次，正如预期，${WM}\mathcal{J}$和${KS}\mathcal{J}$产生的误差相似且随$\omega$增大持续降低——尽管它们处理乱序数据的机制不同，但在相同$\omega$下具有相同的窗口数据完整性，因此对未观测数据的忽略程度也一致。最显著的是，PECJ表现出远超${WM}\mathcal{J}$和${KS}\mathcal{J}$的误差控制能力。例如当$\omega$设为$7\mathrm{{ms}}$时，PECJ在${95}\% l$为$\leq  {5.5}\mathrm{\;{ms}}$的情况下可将误差控制在$\leq  {16}\%$，而WMJ和KSJ即使将$\omega$设为${12}\mathrm{\;{ms}}$使${95}\% l$超过${9.5}\mathrm{\;{ms}}$，误差仍高于${20}\%$。如前所述，这种性能提升源于PECJ主动纳入未观测数据贡献的策略，而非${WM}\oint$和${KS}\oint$的被动等待方式（第3节）。

Comparison under Q2. Given the similar latency patterns across PECJ, KSJ, and WMJ, we primarily present the resulting relative error $\left( \epsilon \right)$ in Figure 7. Despite Q2 demanding a more intricate syntax and involving additional parameters compared to Q1 (Section 3.3), PECJ retains its superior performance,evident through its significantly reduced error. For instance,when the $\omega$ is adjusted to ${10}\mathrm{\;{ms}}$ ,the error incurred by PECJ is as low as ${25.0}\%$ ,compared to a substantial ${52}\%$ for $W\mathrm{M}j$ and 51.5% for ${KS}\mathcal{J}$ . The minor 0.5% $\epsilon$ reduction of ${KS}\mathcal{J}$ compared with ${WM}\mathcal{J}$ is due to the partial re-ordering inherent in the k-slack methodology.

Q2条件下的对比。鉴于PECJ、KSJ和WMJ具有相似的延迟模式，我们主要在图表7中展示相对误差$\left( \epsilon \right)$的结果。尽管Q2相比Q1（第3.3节）需要更复杂的语法并涉及更多参数，PECJ仍保持其优越性能，这通过其显著降低的误差得以体现。例如，当$\omega$调整为${10}\mathrm{\;{ms}}$时，PECJ产生的误差低至${25.0}\%$，而$W\mathrm{M}j$的误差高达${52}\%$，${KS}\mathcal{J}$则为51.5%。${KS}\mathcal{J}$相比${WM}\mathcal{J}$仅微降0.5%$\epsilon$，这是由于k-slack方法中固有的部分重排序所致。

Comparison under Q3. Q3 involves much severer stream oscillation than Q1 and Q2, and we adjust PECJ from analytical to learning-based. The corresponding ${95}\% l$ and $\epsilon$ are shown in Figures 8(a) and 8(b),and we report the latency CDF under $\omega  = {600}\mathrm{\;{ms}}$ in Figure 8(c). Our findings show that ${WM}\mathcal{J}$ and ${KS}\mathcal{J}$ fall short in adapting to this scenario,where stream oscillation and its resulting data disordering manifests in an extreme fashion. Notably,even with $\omega$ set to a lenient ${600}\mathrm{\;{ms}}$ ,allowing for a latency of around ${530}\mathrm{\;{ms}}$ ,they still yield an unacceptably high error over 70%. Contrarily, PECJ consistently maintains the error within 3%, leveraging the learning-based PDA to compensate for the error (Section 5.2). It's important to acknowledge that the learning-based approach of PECJ introduces an additional latency of around ${90}\mathrm{{ms}}$ (Figure 8(a)). However,as this extra latency is a by-product of a constant inference process, it can be circumvented by reducing $\omega$ by ${100}\mathrm{{ms}}$ ,i.e.,the PECJ $\left( {\omega  - {100}}\right)$ configuration. Consequently,the PECJ $\left( {\omega  - {100}}\right)$ still manages to maintain the error within $5\%$ .

Q3条件下的对比。Q3涉及比Q1和Q2更剧烈的流振荡，我们将PECJ从解析式调整为基于学习的方法。相应的${95}\% l$和$\epsilon$如图8(a)和8(b)所示，图8(c)展示了$\omega  = {600}\mathrm{\;{ms}}$下的延迟累积分布。研究发现${WM}\mathcal{J}$和${KS}\mathcal{J}$难以适应这种流振荡及其导致的极端数据乱序场景。值得注意的是，即使将$\omega$设为宽松的${600}\mathrm{\;{ms}}$，允许约${530}\mathrm{\;{ms}}$的延迟，其误差仍超过70%不可接受。相反，PECJ通过基于学习的PDA补偿误差（第5.2节），始终将误差控制在3%以内。需说明的是，PECJ的学习方法会引入约${90}\mathrm{{ms}}$的额外延迟（图8(a)），但由于这是固定推理过程的副产品，可通过将$\omega$减少${100}\mathrm{{ms}}$（即PECJ$\left( {\omega  - {100}}\right)$配置）来规避。因此PECJ$\left( {\omega  - {100}}\right)$仍能将误差维持在$5\%$以内。

<!-- Media -->

<!-- figureText: SHJ 95% latency (ms) 22.2 50.9 Error (%) 38.2 25.5 12.7 0.0 Stock Rovio Logistics Retail Datasets (b) $\epsilon$ comparison 16.6 11.1 5.5 0.0 PRJ PECJ-SHJ WIMPLING PECJ-PRJ Stock Rovio Logistics Retail Datasets (a) ${95}\%$ latency -->

<img src="https://cdn.noedgeai.com/01965997-3577-7b96-be8c-0534d2f3544b_17.jpg?x=158&y=284&w=1240&h=313&r=0"/>

Fig. 13. Single thread assessment of integrated implementation, using four real-world datasets.

图13. 使用四个真实数据集的集成实现单线程评估

<!-- figureText: 40.9 100.0 20 25 Throughput (KTuple/s) 3800.0 2850.0 950.0 0.0 10 25 #Threads #Threads (b) $\epsilon$ comparison (c) Throughput 35.8 75.0 -SHJ 30.6 50.0 PRJ PECJ-SH 25.0 20.3 15 #Threads (a) ${95}\%$ latency -->

<img src="https://cdn.noedgeai.com/01965997-3577-7b96-be8c-0534d2f3544b_17.jpg?x=155&y=768&w=1248&h=234&r=0"/>

Fig. 14. Scaling-up evaluation of integrated implementation, using Stock dataset.

图14. 使用Stock数据集的集成实现扩展性评估

<!-- Media -->

In aligning with the motivation example in Section 1,we vary $\omega$ from ${50} \sim  {1100}\mathrm{{ms}}$ in order to examine the trade-off space of latency and accuracy offered by PECJ, KSJ, and WMJ, as depicted in Figure 8(d). For a clearer reference,we also plot the user demand (within ${200}\mathrm{{ms}}l$ [14] and ${20}\% \epsilon \left\lbrack  {{24},{41}}\right\rbrack  )$ and the theoretical best condition,i.e.,when PECJ uses a perfect learning-based instantiation with zero overhead, in Figure 8(d). Note that, the maximum oscillation magnitude of tuples' arrival delay is ${1000}\mathrm{\;{ms}}$ ,and each mechanism can observe all data in a window when $\omega  > {1010}\mathrm{\;{ms}}$ . In the case where ${KS}\mathcal{J}$ and ${WM}\mathcal{J}$ can access all data of a window,their accuracy approaches ${100}\%$ but latency is too high (i.e.,exceeds ${800}\mathrm{\;{ms}}$ ) to meet the user’s demands (i.e., within ${200}\mathrm{\;{ms}}$ latency). In contrast,PECJ outputs earlier (e.g.,about ${160}\mathrm{\;{ms}}$ ) with marginal errors (e.g., 4.2%). In other words, the tradeoff provided by PECJ is much more practically useful than alternative solutions.

为呼应第1节的动机案例，我们将<b0></b1>从${50} \sim  {1100}\mathrm{{ms}}$进行调整，以探究PECJ、KSJ和WMJ在延迟与准确率间的权衡空间（如图8(d)）。为清晰参照，图中同时标注了用户需求（${200}\mathrm{{ms}}l$[14]和${20}\% \epsilon \left\lbrack  {{24},{41}}\right\rbrack  )$范围内）及理论最佳条件（即PECJ采用零开销的完美学习实例时）。需注意元组到达延迟的最大振荡幅度为${1000}\mathrm{\;{ms}}$，当$\omega  > {1010}\mathrm{\;{ms}}$时各机制可观测窗口内全部数据。当${KS}\mathcal{J}$和${WM}\mathcal{J}$能访问窗口所有数据时，其准确率接近${100}\%$，但延迟过高（超过${800}\mathrm{\;{ms}}$）无法满足用户需求（即${200}\mathrm{\;{ms}}$延迟内）。相比之下，PECJ能提前输出（如约${160}\mathrm{\;{ms}}$）且仅产生边际误差（如4.2%），其提供的权衡方案显然更具实用价值。

Comparison under In-order data. We evaluate PECJ, KSJ, and WMJ under a query with in-order data. This query shares the same settings with $\mathbf{Q}\mathbf{1}$ ,except for $\Delta  = 0\mathrm{{ms}}$ ,i.e.,the event time $\left( {\tau }_{\text{event }}\right)$ and arrival time $\left( {\tau }_{\text{arrival }}\right)$ of each tuple remain consistently synchronized,eliminating any issues related to disordered arrivals or stream oscillations. We allow up to $1\mathrm{\;m}$ s processing delay for the benchmark program to ingest tuples and set $\omega$ to ${11}\mathrm{\;{ms}}$ for each mechanism,the resulting ${95}\% l$ and $\epsilon$ are demonstrated in Figure 9. There are two key observations: 1) The latency of ${KS}\mathcal{J},{WM}\mathcal{J}$ , and PECJ remains comparable. 2) KSJ and WMJ lead to zero error while PECJ results in minor, about ${0.1}\%$ overcompensation,i.e.,it may mistakenly think there are still some tuples missing. This over-compensation phenomenon is caused by a lagged response to analyzing stream tendencies, and the impact of historical observation is incrementally fading in PECJ instead of immediately removed. We acknowledge that addressing the challenge of continually learning new tendencies while preventing the catastrophic forgetting of historical observations is a fundamental research challenge, as discussed in [32]. We envision future work aimed at resolving this issue.

顺序数据下的性能比较。我们在顺序数据查询场景下评估PECJ、KSJ和WMJ。该查询设置与$\mathbf{Q}\mathbf{1}$相同，除了$\Delta  = 0\mathrm{{ms}}$——即每个元组的事件时间$\left( {\tau }_{\text{event }}\right)$与到达时间$\left( {\tau }_{\text{arrival }}\right)$保持完全同步，消除了乱序到达或流振荡相关问题。基准程序允许最多$1\mathrm{\;m}$秒的处理延迟来接收元组，并为每种机制设置$\omega$为${11}\mathrm{\;{ms}}$，最终${95}\% l$和$\epsilon$结果如图9所示。关键发现有二：1) ${KS}\mathcal{J},{WM}\mathcal{J}$与PECJ的延迟性能相当；2) KSJ和WMJ实现零误差，而PECJ存在约${0.1}\%$的轻微过补偿现象，即可能错误判定仍有元组缺失。这种过补偿源于对流趋势分析的滞后响应，且历史观测值的影响在PECJ中是渐弱式消退而非立即消除。我们认同文献[32]的观点：在持续学习新趋势的同时避免历史观测的灾难性遗忘，是本领域的基础性研究挑战，后续工作将着力解决该问题。

### 6.4 Sensitivity Study

### 6.4 敏感性研究

This subsection of the sensitivity study aims to contrast PECJ with the baseline models, WMJ and KSJ, under a range of characteristics, including 1) the number of join keys, 2) the event rate, and 3) the algorithm configurations,and 4) the magnitude of stream oscillations. By default,we fix $\omega$ to ${10}\mathrm{\;{ms}}$ and operate under a SWJ with a window length of ${10}\mathrm{\;{ms}}$ ,followed by ${SUM}\left( \right)$ aggregation.

本小节敏感性研究旨在对比PECJ与基线模型WMJ、KSJ在以下特征下的表现：1)连接键数量 2)事件发生率 3)算法配置 4)流振荡幅度。默认固定$\omega$为${10}\mathrm{\;{ms}}$，采用窗口长度为${10}\mathrm{\;{ms}}$的SWJ操作，后进行${SUM}\left( \right)$聚合。

Impacts of Join Keys. We utilize the synthetic dataset Micro [49] and set the $\Delta$ as $5\mathrm{\;m}$ s. The number of keys of both $\mathrm{R}$ and $\mathrm{S}$ randomly and vary the number of keys from 10 to 5000 ,while maintaining the event rate at our default setting of ${100}\mathrm{{Ktuple}}/\mathrm{s}$ . Since the number of join keys has virtually no impact on the latency of PECJ, WMJ, and KSJ (with a fluctuation of approximately $\pm  {0.6}\%$ around ${8.25}\mathrm{\;{ms}}$ at most),we present the relative error in Figure 10(a). In general,PECJ outperforms the baseline models across a wide range of the number of keys. However, when the number of keys increases to as high as 5000 , the likelihood of encountering a join match diminishes, which leads to fewer observations on join selectivity $\sigma$ and slightly elevates its error.

连接键数量影响。使用Micro合成数据集[49]，设定$\Delta$为$5\mathrm{\;m}$秒。保持默认事件发生率${100}\mathrm{{Ktuple}}/\mathrm{s}$，使$\mathrm{R}$和$\mathrm{S}$的键数量在10至5000间随机变化。由于键数量对PECJ、WMJ和KSJ的延迟影响微乎其微（最大波动约$\pm  {0.6}\%$，围绕${8.25}\mathrm{\;{ms}}$），图10(a)展示相对误差。总体而言，PECJ在大多数键数量区间优于基线模型。但当键数量增至5000时，连接匹配概率降低导致对连接选择性$\sigma$的观测减少，使其误差略有上升。

Impacts of Event Rate. We hold the number of join keys at 10 , and adjust the event rate from ${10}\mathrm{{KTuple}}/\mathrm{s}$ to ${400}\mathrm{{KTuple}}/\mathrm{s}$ . The resulting ${95}\% l$ and $\epsilon$ are displayed in Figure 10. Our findings show that KSJ experiences a latency ${50}\%$ higher than either WMJ or PECJ when the event rate reaches ${200}\mathrm{{KTuple}}/\mathrm{s}$ ,and its $\epsilon$ also begins to escalate under such high event rate. This phenomenon occurs because 1) the k-slack overhead swells with a larger number of tuples processed per unit of time (i.e.,the higher event rate),causing ${KS}\mathcal{J}$ to overload much more readily than ${WM}\mathcal{J}$ or PECJ, and 2) when an overload transpires, the partial reorder in KSJ becomes asynchronous, further increasing its error. Compared to WMJ, PECJ is slightly more prone to overload, particularly at event rates as high as ${400}\mathrm{{Ktuple}}/\mathrm{s}$ due to the extra overhead involved in making observations and executing compensations. Nonetheless, PECJ consistently achieves the smallest error under a non-overload rate, and even under a mild overload.

事件率的影响。我们将连接键数量固定为10，并将事件率从${10}\mathrm{{KTuple}}/\mathrm{s}$调整至${400}\mathrm{{KTuple}}/\mathrm{s}$。图10展示了由此产生的${95}\% l$和$\epsilon$。研究发现，当事件率达到${200}\mathrm{{KTuple}}/\mathrm{s}$时，KSJ的延迟比WMJ或PECJ高出${50}\%$，且其$\epsilon$在此高事件率下也开始攀升。这种现象源于：1) k-slack开销随着单位时间内处理元组数量（即更高事件率）的增加而膨胀，导致${KS}\mathcal{J}$比${WM}\mathcal{J}$或PECJ更容易过载；2)发生过载时，KSJ中的部分重排序会转为异步，进一步放大误差。与WMJ相比，PECJ在${400}\mathrm{{Ktuple}}/\mathrm{s}$等高事件率下因观测补偿的额外开销更易过载，但在非过载状态下始终保持最小误差，即便在轻度过载时亦然。

Impacts of Algorithm Configurations. We delve into a sensitivity analysis aimed at evaluating the accuracy of PECJ when implemented using varying strategies, specifically the analytical (referred to as ${PEC}{\mathcal{J}}_{\text{analytical }}$ henceforth,which demonstrates ${PEC}{\mathcal{J}}_{\text{analytical }}$ via the minimum error of SVI-based and AEMA-based methodologies) that leans on the central limit theorem as detailed in Section 5.1,and the learning-based (referred to as ${PEC}{\mathcal{J}}_{\text{learning }}$ henceforth),which prioritizes generalization and the capture of unobserved data as elaborated in Section 5.2. Initially, we examine the $\mathbf{Q}\mathbf{1}$ scenario,characterized by relatively slight stream oscillation and observation distortion. As illustrated in Figure 11(a),we perform a comparative analysis of the relative error $\left( \epsilon \right)$ between ${PEC}{J}_{\text{analytical }},{PEC}{J}_{\text{learning }}$ ,and two baseline methods, ${WMJ}$ and ${KSJ}$ ,while adjusting the $\omega$ within the range of $5\mathrm{\;{ms}}$ to ${12}\mathrm{\;{ms}}$ .

算法配置的影响。我们通过敏感性分析评估PECJ采用不同策略时的精度表现：基于中心极限定理的解析策略（下称${PEC}{\mathcal{J}}_{\text{analytical }}$，通过SVI与AEMA方法的最小误差体现，详见5.1节）与侧重泛化能力和未观测数据捕捉的学习策略（下称${PEC}{\mathcal{J}}_{\text{learning }}$，详见5.2节）。首先考察具有轻微流振荡和观测失真的$\mathbf{Q}\mathbf{1}$场景。如图11(a)所示，在$\omega$取值$5\mathrm{\;{ms}}$至${12}\mathrm{\;{ms}}$范围内，我们对比分析了${PEC}{J}_{\text{analytical }},{PEC}{J}_{\text{learning }}$与两种基线方法${WMJ}$、${KSJ}$的相对误差$\left( \epsilon \right)$。

Our analysis yields several key insights. First, as anticipated in Section 2,both WMJ and KSJ display similar error profiles across different $\omega$ values and consistently record higher errors compared to ${PEC}{\mathcal{J}}_{\text{analytical }}$ or ${PEC}{\mathcal{J}}_{\text{learning }}$ . Second,while ${PEC}{\mathcal{J}}_{\text{analytical }}$ adeptly corrects errors and mirrors the arrival pattern in $\mathbf{{Q1}}$ ,its accuracy is enhanced with a larger $\omega$ ,reflecting its reliance on the central limit theorem (refer to Section 5.1). In essence,a larger $\omega$ provides a more significant pool of observational data,hence boosting ${\operatorname{PEC}}_{j\text{ analytical }}$ ’s accuracy. Finally, ${\operatorname{PEC}}_{j\text{ learning }}$ ,engineered for broad applicability, extracts latent information from the data streams and rectifies errors more effectively than ${PEC}{J}_{\text{analytical. Notably,this robustness persists even when the pool of observational}}$ data is curtailed by a smaller $\omega$ .

分析获得三项关键发现：其一，如第2节预期，WMJ和KSJ在不同$\omega$值下误差特征相似，且始终高于${PEC}{\mathcal{J}}_{\text{analytical }}$或${PEC}{\mathcal{J}}_{\text{learning }}$；其二，${PEC}{\mathcal{J}}_{\text{analytical }}$虽能有效修正误差并复现$\mathbf{{Q1}}$中的到达模式，但其精度随$\omega$增大而提升，这印证了其对中心极限定理的依赖（见5.1节）——更大$\omega$意味着更丰富的观测数据池，从而提升${\operatorname{PEC}}_{j\text{ analytical }}$的精度；最后，为广泛适用性设计的${\operatorname{PEC}}_{j\text{ learning }}$能从数据流中提取潜在信息，在$\omega$较小时仍比${PEC}{J}_{\text{analytical. Notably,this robustness persists even when the pool of observational}}$更有效地纠正误差。

We then proceed to evaluate the Q3 scenario, which introduces severer stream oscillation due to a larger $\Delta$ . The $\omega$ is tuned from 50ms to ${700}\mathrm{\;{ms}}$ ,and the relative errors $\left( \epsilon \right)$ of all methods are illustrated in Figure 11(b). Generally, PEC $J$ analytical struggles to accurately reflect Q3’s arrival pattern and provides sub-optimal error compensation. Each observation on join selectivity or event rate is heavily biased, violating the preconditions for applying the central limit theorem (Section 5.1). While this bias can be reduced with a larger volume of observations,it necessitates a larger $\omega$ . Contrarily, ${PEC}{J}_{\text{learning }}$ is equipped to recognize these biases,overcoming the constraints of the central limit theorem, and thus delivers superior error compensations as a general instantiation method.

随后我们评估Q3场景，该场景由于更大的$\Delta$导致更严重的流振荡。将$\omega$从50ms调整至${700}\mathrm{\;{ms}}$，所有方法的相对误差$\left( \epsilon \right)$如图11(b)所示。总体而言，PEC$J$分析方法难以准确反映Q3的到达模式，提供的误差补偿效果欠佳。对连接选择性或事件率的每次观测都存在严重偏差，违背了应用中心极限定理的前提条件（第5.1节）。虽然增加观测样本量可降低此偏差，但需要更大的$\omega$。相反，${PEC}{J}_{\text{learning }}$能识别这些偏差，突破中心极限定理的限制，因此作为通用实例化方法可提供更优的误差补偿。

Impacts of Stream Oscillation. We conducted an investigation into the varying magnitudes of stream oscillation based on the different outcomes observed with ${PEC}{\mathcal{J}}_{\text{analytical }}$ and ${PEC}{\mathcal{J}}_{\text{learning }}$ under mild (Q1) and severe (Q3) stream oscillation scenarios. Specifically,we set the $\omega$ to ${100}\mathrm{{ms}}$ , gradually increase the maximum magnitude of tuples’ arrival delay $\left( \Delta \right)$ from 90ms to ${1000}\mathrm{{ms}}$ , and keep other settings the same as $\mathbf{{Q1}}$ . It’s important to note that increasing $\Delta$ results in larger magnitudes of stream oscillation. The resulting error is depicted in Figure 12. It is evident that the error of ${PEC}{\mathcal{J}}_{\text{analytical }}$ increases gradually with $\Delta$ ,surpassing ${50}\%$ when $\Delta$ reaches ${150}\mathrm{\;{ms}}$ or higher. Eventually,it matches the high error levels of ${WM}\oint$ or ${KS}\oint$ when $\Delta$ becomes sufficiently large. This behavior occurs because a large magnitude of stream oscillation,such as $\Delta  = {500}\mathrm{\;{ms}}$ ,renders the central limit theorem unsuitable, and the relatively simple analytic forms in Equations 8, 9, and 10 struggle to converge. In contrast, ${PEC}{\mathcal{J}}_{\text{learning }}$ is capable of handling more severe stream oscillation scenarios (e.g.,4.2% error when $\Delta  = {1000}\mathrm{{ms}}$ ). This capability is attributed to Equation 15, which offers a proven general approach to dealing with stream oscillation. Theoretically, it will only fail when mean-field family approximation and the universal approximation theorem do not hold, which is a research area yet to be thoroughly explored in the literature. In this evaluation, our neural network was pre-trained within a mere 2 minutes and required no further modification after deployment. However, we acknowledge the potential need for more complex scenarios in the future, where continuous retraining of the learning-based model may be necessary to further enhance our understanding of this study.

流振荡影响研究。我们基于${PEC}{\mathcal{J}}_{\text{analytical }}$和${PEC}{\mathcal{J}}_{\text{learning }}$在轻度（Q1）和重度（Q3）流振荡场景下的不同表现，探究了流振荡强度的变化影响。具体设置$\omega$为${100}\mathrm{{ms}}$，逐步将元组到达延迟最大值$\left( \Delta \right)$从90ms增至${1000}\mathrm{{ms}}$，其他参数保持与$\mathbf{{Q1}}$一致。需注意增大$\Delta$会导致更剧烈的流振荡。误差结果如图12所示，明显可见${PEC}{\mathcal{J}}_{\text{analytical }}$的误差随$\Delta$逐步上升，当$\Delta$达到${150}\mathrm{\;{ms}}$或更高时超过${50}\%$。最终当$\Delta$足够大时，其误差水平与${WM}\oint$或${KS}\oint$相当。这是因为如$\Delta  = {500}\mathrm{\;{ms}}$等强流振荡会使中心极限定理失效，公式8、9、10中的简单解析形式难以收敛。相较之下，${PEC}{\mathcal{J}}_{\text{learning }}$能处理更剧烈的流振荡（如$\Delta  = {1000}\mathrm{{ms}}$时误差仅4.2%），这归功于公式15提供的普适性流振荡处理方法。理论上仅当场均值近似族和通用近似定理不成立时才会失效，该领域尚待深入研究。本次评估中神经网络仅需2分钟预训练且部署后无需调整，但未来可能需要针对更复杂场景持续重训练学习模型以深化研究。

### 6.5 Integrated Implementation Evaluation

### 6.5 集成实现评估

In this evaluation, we contrast the original parallel SHJ and PRJ in AllianceDB with their corresponding modifications under PECJ, namely, PECJ-SHJ and PECJ-PRJ. It is important to note that the assumed time point of window completeness $\omega$ doesn’t impact SHJ and PRJ as they do not handle disordered data streams. For both PECJ-SHJ and PECJ-PRJ,we set it to ${10}\mathrm{{ms}}$ . We first conduct a single-thread assessment on four real-world datasets, followed by a scaling-up evaluation by using Stock as an example.

本评估对比了AllianceDB中原生并行SHJ/PRJ与PECJ改进版本PECJ-SHJ/PECJ-PRJ。需注意窗口完整性假设时间点$\omega$对SHJ和PRJ无影响，因其不处理乱序数据流。对PECJ-SHJ和PECJ-PRJ，我们将其设为${10}\mathrm{{ms}}$。首先在四个真实数据集上进行单线程测试，随后以Stock为例进行扩展性评估。

Single-thread Assessment. In this assessment,we report the ${95}\% l$ and $\epsilon$ of handling Stock, Rovio, Logistics, and Retail datasets under Q1, as illustrated in Figure 13. Three key observations stand out. Firstly, both PRJ and SHJ produce high error rates, for instance, a substantial 47% on the Stock dataset when faced with disordered arrivals. Secondly, PECJ-PRJ and PECJ-SHJ notably decrease these errors while managing to maintain similar latency to their counterparts, PRJ and SHJ. This outcome attests to the robust efficiency in the optimization and implementation of PECJ. Lastly, PECJ-SHJ showcases a lower $\epsilon$ than PECJ-PRJ,specifically, $1\%$ versus ${13}\%$ in the Stock dataset. This improvement is a consequence of PECJ-SHJ's real-time data stream analysis approach. In contrast to PECJ-PRJ which waits for a window of tuples before starting the processing, PECJ-SHJ promptly processes each input tuple upon arrival. This strategy enables PECJ-SHJ to rapidly detect and adapt to immediate and ongoing changes in the data streams.

单线程评估。本评估报告展示了Q1场景下处理Stock、Rovio、Logistics和Retail数据集时的${95}\% l$与$\epsilon$性能（如图13所示）。主要发现有三：首先，PRJ与SHJ在无序数据到达时均产生较高错误率，例如Stock数据集上高达47%；其次，PECJ-PRJ和PECJ-SHJ在保持与PRJ/SHJ相近延迟的同时显著降低了错误率，印证了PECJ优化实施的高效性；最后，PECJ-SHJ展现出比PECJ-PRJ更低的$\epsilon$（Stock数据集上$1\%$对比${13}\%$），这得益于其实时数据流分析机制——不同于PECJ-PRJ需等待元组窗口才启动处理，PECJ-SHJ在元组到达时即刻处理，从而快速响应数据流的动态变化。

Scaling-up Evaluation. In the scaling-up evaluation, we keep the Q1 query, gradually increase the number of Stock tuples in each window and ensure that the event rate of both $R$ and $S$ surpasses 1600KTuples/s. By varying the number of threads from 1 to 24,we depict the ${95}\% l,\epsilon$ ,and system throughput of each mechanism in Figure 14. It becomes clear that the lazy approaches, namely PRJ and PECJ-PRJ, consistently outshine their eager counterparts (SHJ and PECJ-SHJ), in terms of latency reduction and throughput improvement. This result aligns with previous studies [49] conducted under in-order arrival scenarios, reaffirming the enduring challenges faced by eager approaches such as cache thrashing, particularly when scaling up.

扩展性评估。在扩展性评估中，我们固定Q1查询语句，逐步增加Stock数据集每个窗口的元组数量，确保$R$和$S$的事件率均超过1600KTuples/s。通过将线程数从1增至24，图14展示了各机制的${95}\% l,\epsilon$与系统吞吐量。显然，惰性处理策略（PRJ与PECJ-PRJ）在延迟降低和吞吐提升方面持续优于急切策略（SHJ与PECJ-SHJ），这与文献[49]在有序到达场景下的结论一致，印证了急切策略面临缓存颠簸等固有挑战。

Moreover, PECJ-PRJ matches PRJ in terms of efficient scalability, largely thanks to its reduced overhead in managing disorder. This reaffirms the efficacy of our theoretical optimization for the PDA problem, using VI as outlined in Section 4. The integration of low-overhead AEMA VI instantiation further contributes to an enhanced execution efficiency (Section 5.1). On the other hand, despite its earlier successes, PECJ-SHJ incurs higher errors than PECJ-PRJ under a heavy input workload, as illustrated in Figure 13(b). This can be attributed to distortions resulting from eager disorder handling, which can potentially mislead PECJ by providing inaccurate information for error compensation. Nonetheless, these findings collectively underscore PECJ's practicality in scaling up SWJ algorithms under challenging conditions of disordered data arrival.

此外，PECJ-PRJ凭借更低的无序管理开销，展现出与PRJ相当的扩展效率，这验证了第4节基于VI的PDA理论优化有效性。结合第5.1节低开销AEMA VI实例化，进一步提升了执行效率。但需注意，如图13(b)所示，在高负载输入时PECJ-SHJ错误率高于PECJ-PRJ，源于急切处理机制可能导致错误补偿信息失真。这些发现共同证明了PECJ在无序数据流环境下扩展SWJ算法的实用价值。

## 7 RELATED WORK

## 7 相关工作

This section discusses related research in Stream Window Join, Buffer-based Disorder Handling, and Approximate Query Processing.

本节综述流式窗口连接、基于缓冲区的无序处理及近似查询处理领域的研究。

Stream Window Join (SWJ). The predominant aim in optimizing stream window join operations has traditionally centred around enhancing efficiency and facilitating incremental processing. For example, both the Handshake Join [43] and the Split Join [37] use a dataflow model to achieve scalability on modern multicore architectures,whereas the IBWJ [39] utilizes a shared index structure to expedite tuple matching. An exhaustive experimental study conducted by Zhang et al. [49] contrasts these techniques across a wide spectrum of workload characteristics, application necessities, and hardware designs. This study also underscores the successful adaptation of relational join algorithms to hasten SWJ. Typically, these methodologies presume that data arrives in an ordered manner and is fully accessible. Our work, however, ventures into investigating ways to offset errors induced by incomplete data in the face of disorderly conditions.

流式窗口连接(SWJ)。现有优化主要聚焦效率提升与增量处理，例如Handshake Join[43]和Split Join[37]采用数据流模型实现多核扩展，IBWJ[39]则利用共享索引加速元组匹配。Zhang等[49]的系统实验对比了不同工作负载特征、应用需求及硬件设计下的技术表现，并证实关系连接算法对SWJ的加速适用性。这些方法通常假设数据有序且完整到达，而本研究重点解决无序条件下数据缺失引发的误差补偿问题。

Buffer-based Disorder Handling. A number of studies have delved into the accuracy-latency tradeoff utilizing buffers. To prevent potential infinite buffering, existing research employs different mechanisms for controlling buffer flushing and for making assumptions about the temporary completeness of incoming data. These mechanisms include k-slack [23, 31], watermarks [6, 9, 40], and punctuations [29]. For example, Ji et al. [22] introduced a k-slack-based disordered SWJ, which regards the tradeoff between accuracy and latency as a crucial factor. They highlight that joins inherently possess more complexity than single-stream linear operators, such as summation or average, when handling disordered data. This complexity stems from the mutual and non-linear relationships existing among multiple streams. Despite the variations in specific tradeoff rules and methodologies, these approaches rely on data that has already arrived to generate results, thus overlooking the contributions of future data. PECJ stands out by proactively compensating for this yet-to-be-received data.

基于缓冲区的无序处理。现有研究通过k-slack[23,31]、水位线[6,9,40]和标点符[29]等机制权衡精度与延迟，避免无限缓冲。Ji等[22]提出的k-slack无序SWJ特别强调该权衡的重要性，指出连接操作在处理无序数据时比求和/均值等单流线性算子更复杂，源于多流间非线性交互关系。这些方法依赖已到达数据生成结果，而PECJ创新性地通过前瞻性补偿机制处理未到达数据的影响。

Approximate Query Processing (AQP). The goal of AQP is to reduce computational overhead by selecting a data subset to approximate the result of the whole dataset $\left\lbrack  {{25},{30}}\right\rbrack$ . As data selection is system-controlled, error compensation can be predefined and is relatively stable in AQP. Compensation can use either linear [38] or non-linear formulas [5], depending on the algorithm's subset selection. More advanced AQP approaches employ machine learning [33] and bootstrap methods [46] to tackle ubiquitous queries under static data, albeit with higher computational costs. To address this issue, the Wander Join algorithm [28] applies stochastic and graph optimizations to reduce overhead and optimize online aggregation for joins. Our work addresses a different and more challenging problem-handling of disordered SWJ where observation distortion cannot be system-controlled. Therefore, we propose to solve a PDA problem by VI and discuss its implementations for disordered SWJ (Sections 4 and 5).

近似查询处理(AQP)。AQP的目标是通过选择数据子集来近似整个数据集的结果，从而降低计算开销$\left\lbrack  {{25},{30}}\right\rbrack$。由于数据选择由系统控制，误差补偿可预先定义且在AQP中相对稳定。补偿可采用线性[38]或非线性公式[5]，具体取决于算法的子集选择方式。更先进的AQP方法采用机器学习[33]和自助法[46]来处理静态数据下的普遍查询，尽管计算成本较高。为解决此问题，Wander Join算法[28]应用随机和图优化技术来降低开销，并优化连接操作的在线聚合。我们的研究针对一个更富挑战性的问题——处理观察失真无法被系统控制的无序SWJ，因此提出通过VI解决PDA问题，并讨论其在无序SWJ中的实现方案（第4、5节）。

## 8 CONCLUSION

## 8 结论

In this paper, we have introduced PECJ, a novel solution for executing SWJ, a critical operation in stream analytics, amidst the challenges posed by disordered data. What sets PECJ apart is its unique ability to proactively incorporate unobserved data, thereby enhancing the accuracy-latency tradeoff. This feat is achieved by leveraging a sophisticated approach to PDA using efficient VI instantiations. As evidenced by the successful implementation of PECJ in the multi-threaded SWJ benchmark testbed, this method presents a promising advancement for enhancing data stream processing capabilities under disordered data arrival conditions. Particularly, it has successfully reduced the relative error from 47% to a remarkable 1%, while maintaining constant latency. Looking ahead, an exciting prospect lies in expanding the applicability of PECJ and exploring how its principles can integrate with approximate computing methodologies. This includes techniques such as sampling and compression, which deliberately introduce data distortion to strike a balance between accuracy and latency. The integration of these approaches would certainly open up new avenues for future research.

本文提出了PECJ这一创新解决方案，用于在无序数据挑战下执行流分析中的关键操作SWJ。PECJ的独特之处在于能主动纳入未观测数据，从而优化准确性与延迟的权衡。这一成果通过采用基于高效VI实例化的PDA先进方法实现。多线程SWJ基准测试平台的成功实施证明，该方法为提升无序数据到达条件下的流处理能力提供了显著进步，尤其将相对误差从47%显著降至1%的同时保持恒定延迟。未来值得期待的是拓展PECJ的适用性，探索其原理如何与近似计算方法（如故意引入数据失真以平衡精度与延迟的采样和压缩技术）相融合，这将为后续研究开辟新途径。

Appendix: The data, results, code, scripts and an appendix with more discussions of this work can be downloaded from https://anonymous.4open.science/r/PECJ.

附录：本工作的数据、结果、代码、脚本及含更多讨论的附录可从https://anonymous.4open.science/r/PECJ下载。

## REFERENCES

## 参考文献

[1] [n. d.]. A Benchmark for Real-Time Relational Data Feature Extraction. https://github.com/decis-bench/febench.Last Accessed: 2023-01-03.

[2] [n. d.]. OpenMLDB Use Cases. https://openmldb.ai/docs/en/main/use_case/index.html.Last Accessed: 2022-09-23.

[3] 2018. Shanghai Stock Exchange, http://english.sse.com.cn/.Last Accessed: 2020-06-29.

[4] 2023. Pytorch homepage, https://pytorch.org/.

[5] Sameer Agarwal, Barzan Mozafari, Aurojit Panda, Henry Milner, Samuel Madden, and Ion Stoica. 2013. BlinkDB: queries with bounded errors and bounded response times on very large data. In Proceedings of the 8th ACM European conference on computer systems. 29-42.

[6] Tyler Akidau, Edmon Begoli, Slava Chernyak, Fabian Hueske, Kathryn Knight, Kenneth Knowles, Daniel Mills, and Dan Sotolongo. 2021. Watermarks in Stream Processing Systems: Semantics and Comparative Analysis ofApache Flink and Google Cloud Dataflow. Technical Report. Oak Ridge National Lab.(ORNL), Oak Ridge, TN (United States).

[7] Tyler Akidau, Robert Bradshaw, Craig Chambers, Slava Chernyak, Rafael J Fernández-Moctezuma, Reuven Lax, Sam McVeety, Daniel Mills, Frances Perry, Eric Schmidt, et al. 2015. The dataflow model: a practical approach to balancing correctness, latency, and cost in massive-scale, unbounded, out-of-order data processing. (2015).

[8] Abdullah Alsaedi, Nasrin Sohrabi, Redowan Mahmud, and Zahir Tari. 2023. RADAR: Reactive Concept Drift Management with Robust Variational Inference for Evolving IoT Data Streams. In Proceedings of the 39th IEEE International Conference on Data Engineering (ICDE2023). IEEE.

[9] Ahmed Awad, Jonas Traub, and Sherif Sakr. 2019. Adaptive Watermarks: A Concept Drift-based Approach for Predicting Event-Time Progress in Data Streams.. In EDBT. 622-625.

[10] Christopher M Bishop and Nasser M Nasrabadi. 2006. Pattern recognition and machine learning. Vol. 4. Springer.

[11] Savong Bou, Hiroyuki Kitagawa, and Toshiyuki Amagasa. 2021. Cpix: real-time analytics over out-of-order data streams by incremental sliding-window aggregation. IEEE Transactions on Knowledge and Data Engineering 34, 11 (2021), 5239-5250.

[12] Tamara Broderick, Nicholas Boyd, Andre Wibisono, Ashia C Wilson, and Michael I Jordan. 2013. Streaming variational bayes. Advances in neural information processing systems 26 (2013).

[13] Badrish Chandramouli, Mohamed Ali, Jonathan Goldstein, Beysim Sezgin, and Balan Sethu Raman. 2010. Data stream management systems for computational finance. Computer 43, 12 (2010), 45-52.

[14] Martijn de Heus, Kyriakos Psarakis, Marios Fragkoulis, and Asterios Katsifodimos. 2021. Distributed transactions on serverless stateful functions. In Proceedings of the 15th ACM International Conference on Distributed and Event-based Systems. 31-42.

[15] Kangqi Ding. 2022. Analysis of Short Selling. In 2022 7th International Conference on Financial Innovation and Economic Development (ICFIED 2022). Atlantis Press, 2030-2034.

[16] Roger Dingledine, Nick Mathewson, Paul F Syverson, et al. 2004. Tor: The second-generation onion router.. In USENIX security symposium, Vol. 4. 303-320.

[17] Hua Fan and Wojciech Golab. 2021. Gossip-based visibility control for high-performance geo-distributed transactions. The VLDB Journal 30, 1 (2021), 93-114.

[18] Behrouz A Forouzan. 2002. TCP/IP protocol suite. McGraw-Hill Higher Education.

[19] Tom Goldstein and Stanley Osher. 2009. The split Bregman method for L1-regularized problems. SIAM journal on imaging sciences 2, 2 (2009), 323-343.

[20] Matthew D. Hoffman, David M. Blei, and Francis Bach. 2010. Online Learning for Latent Dirichlet Allocation. In Proceedings of the 23rd International Conference on Neural Information Processing Systems - Volume 1 (Vancouver, British Columbia, Canada) (NIPS’10). Curran Associates Inc., Red Hook, NY, USA, 856-864.

[21] Matthew D Hoffman, David M Blei, Chong Wang, and John Paisley. 2013. Stochastic variational inference. Journal of Machine Learning Research (2013).

[22] Yuanzhen Ji, Jun Sun, Anisoara Nica, Zbigniew Jerzak, Gregor Hackenbroich, and Christof Fetzer. 2016. Quality-driven disorder handling for m-way sliding window stream joins. In 2016 IEEE 32nd International Conference on Data Engineering (ICDE). IEEE, 493-504.

[23] Yuanzhen Ji, Hongjin Zhou, Zbigniew Jerzak, Anisoara Nica, Gregor Hackenbroich, and Christof Fetzer. 2015. Quality-driven continuous query execution over out-of-order data streams. In Proceedings of the 2015 ACM SIGMOD International Conference on Management of Data. 889-894.

[24] Rasmus Kar Jørgensen and Christian Igel. 2021. Machine learning for financial transaction classification across companies using character-level word embeddings of text fields. Intelligent Systems in Accounting, Finance and Management 28, 3 (2021), 159-172.

[25] Srikanth Kandula, Anil Shanbhag, Aleksandar Vitorovic, Matthaios Olma, Robert Grandl, Surajit Chaudhuri, and Bolin Ding. 2016. Quickr: Lazily approximating complex adhoc queries in bigdata clusters. In Proceedings of the 2016 international conference on management of data. 631-646.

[26] Jeyhun Karimov, Tilmann Rabl, Asterios Katsifodimos, Roman Samarev, Henri Heiskanen, and Volker Markl. 2018. Benchmarking distributed stream data processing systems. In 2018 IEEE 34th International Conference on Data Engineering (ICDE). Ieee, 1507-1518.

[27] Nikos R Katsipoulakis, Alexandros Labrinidis, and Panos K Chrysanthis. 2020. Spear: Expediting stream processing with accuracy guarantees. In 2020 IEEE 36th International Conference on Data Engineering (ICDE). IEEE, 1105-1116.

[28] Feifei Li, Bin Wu, Ke Yi, and Zhuoyue Zhao. 2016. Wander join: Online aggregation via random walks. In Proceedings of the 2016 International Conference on Management of Data. 615-629.

[29] Jin Li, Kristin Tufte, Vladislav Shkapenyuk, Vassilis Papadimos, Theodore Johnson, and David Maier. 2008. Out-of-Order Processing: A New Architecture for High-Performance Stream Systems. Proc. VLDB Endow. 1, 1 (aug 2008), 274-288. https://doi.org/10.14778/1453856.1453890

[30] Kaiyu Li, Yong Zhang, Guoliang Li, Wenbo Tao, and Ying Yan. 2018. Bounded approximate query processing. IEEE Transactions on Knowledge and Data Engineering 31, 12 (2018), 2262-2276.

[31] Ming Li, Mo Liu, Luping Ding, Elke A Rundensteiner, and Murali Mani. 2007. Event stream processing with out-of-order data arrival. In 27th International Conference on Distributed Computing Systems Workshops (ICDCSW’07). IEEE, 67-67.

[32] Yiming Li, Yanyan Shen, and Lei Chen. 2022. Camel: Managing Data for Efficient Stream Learning. In Proceedings of the 2022 International Conference on Management of Data. 1271-1285.

[33] Qingzhi Ma and Peter Triantafillou. 2019. Dbest: Revisiting approximate query processing engines with machine learning models. In Proceedings of the 2019 International Conference on Management of Data. 1553-1570.

[34] Hongyu Miao, Heejin Park, Myeongjae Jeon, Gennady Pekhimenko, Kathryn S McKinley, and Felix Xiaozhu Lin. 2017. Streambox: Modern stream processing on a multicore machine. In 2017 USENIX Annual Technical Conference (USENIX ATC 17) (Santa Clara, CA, USA) (Usenix Atc '17). USENIX Association, Berkeley, CA, USA, 617-629.

[35] Adrian Michalke, Philipp M Grulich, Clemens Lutz, Steffen Zeuch, and Volker Markl. 2021. An energy-efficient stream join for the Internet of Things. In Proceedings of the 17th International Workshop on Data Management on New Hardware (DaMoN 2021). 1-6.

[36] Douglas C Montgomery, Cheryl L Jennings, and Murat Kulahci. 2015. Introduction to time series analysis and forecasting. John Wiley & Sons.

[37] Mohammadreza Najafi, Mohammad Sadoghi, and Hans-Arno Jacobsen. 2016. SplitJoin: A Scalable, Low-latency Stream Join Architecture with Adjustable Ordering Precision. In 2016 USENIX Annual Technical Conference (USENIX ATC 16). USENIX Association, Denver, CO, 493-505. https://www.usenix.org/conference/atc16/technical-sessions/ presentation/najafi

[38] Do Le Quoc, Ruichuan Chen, Pramod Bhatotia, Christof Fetzer, Volker Hilt, and Thorsten Strufe. 2017. Streamapprox: Approximate computing for stream analytics. In Proceedings of the 18th ACM/IFIP/USENIX Middleware Conference. 185-197.

[39] Amirhesam Shahvarani and Hans-Arno Jacobsen. 2020. Parallel Index-Based Stream Join on a Multicore CPU. In Proceedings of the 2020 ACM SIGMOD International Conference on Management of Data (Portland, OR, USA) (SIGMOD '20). Association for Computing Machinery, New York, NY, USA, 2523-2537. https://doi.org/10.1145/3318464.3380576

[40] Yang Song, Yunchun Li, Hailong Yang, Jun Xu, Zerong Luan, and Wei Li. 2021. Adaptive watermark generation mechanism based on time series prediction for stream processing. Frontiers of Computer Science 15 (2021), 1-15.

[41] Salvatore Stolfo, David W Fan, Wenke Lee, Andreas Prodromidis, and Philip Chan. 1997. Credit card fraud detection using meta-learning: Issues and initial results. In AAAI-97 Workshop on Fraud Detection and Risk Management. 83-90.

[42] Binh Tang and David S Matteson. 2021. Probabilistic transformer for time series analysis. Advances in Neural Information Processing Systems 34 (2021), 23592-23608.

[43] Jens Teubner and Rene Mueller. 2011. How Soccer Players Would Do Stream Joins. In Proceedings of the 2011 ACM SIGMOD International Conference on Management of Data (Athens, Greece) (Sigmod '11). Acm, New York, NY, USA, 625-636. https://doi.org/10.1145/1989323.1989389

[44] Arash Vahdat and Jan Kautz. 2020. NVAE: A deep hierarchical variational autoencoder. Advances in neural information processing systems 33 (2020), 19667-19679.

[45] Sifan Wu, Xi Xiao, Qianggang Ding, Peilin Zhao, Ying Wei, and Junzhou Huang. 2020. Adversarial sparse transformer for time series forecasting. Advances in neural information processing systems 33 (2020), 17105-17115.

[46] Kai Zeng, Shi Gao, Barzan Mozafari, and Carlo Zaniolo. 2014. The analytical bootstrap: a new method for fast error estimation in approximate query processing. In Proceedings of the 2014 ACM SIGMOD international conference on Management of data. 277-288.

[47] Steffen Zeuch, Ankit Chaudhary, Bonaventura Del Monte, Haralampos Gavriilidis, Dimitrios Giouroukis, Philipp M. Grulich, Sebastian Breß, Jonas Traub, and Volker Markl. 2020. The NebulaStream Platform for Data and Application Management in the Internet of Things. In CIDR 2020, 10th Conference on Innovative Data Systems Research, Amsterdam, The Netherlands, January 12-15, 2020, Online Proceedings. www.cidrdb.org. http://cidrdb.org/cidr2020/papers/p7-zeuch-cidr20.pdf

[48] Hao Zhang, Xianzhi Zeng, Shuhao Zhang, Xinyi Liu, Mian Lu, Zhao Zheng, and Yuqiang Chen. 2023. Scalable Online Interval Join on Modern Multicore Processors in OpenMLDB. In Proceedings of the 39th IEEE International Conference on Data Engineering (ICDE2023). IEEE.

[49] Shuhao Zhang, Yancan Mao, Jiong He, Philipp M Grulich, Steffen Zeuch, Bingsheng He, Richard TB Ma, and Volker Markl. 2021. Parallelizing intra-window join on multicores: An experimental study. In Proceedings of the 2021 International Conference on Management of Data. 2089-2101.

## ACKNOWLEDGMENTS

## 致谢

This work is partially supported by a MoE AcRF Tier 2 grant (MOE-T2EP20122-0010), and a startup grant of NTU (023452-00001). Any opinions, findings and conclusions or recommendations expressed in this material are those of the author(s) and do not reflect the views of the Ministry of Education, Singapore. Corresponding author is Shuhao Zhang.

本研究部分获新加坡教育部Tier 2基金(MOE-T2EP20122-0010)及南洋理工大学启动基金(023452-00001)资助。文中观点、发现和结论仅代表作者立场，不代表新加坡教育部意见。通讯作者为张书豪。