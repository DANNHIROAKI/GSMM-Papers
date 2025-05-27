[原论文](https://arxiv.org/abs/2405.19504)

[详细的理论证明](https://github.com/DANNHIROAKI/Reading-Papers/tree/main/0035.%20MUVERA/%E7%90%86%E8%AE%BA%E4%BF%9D%E8%AF%81%E7%9A%84%E8%AF%81%E6%98%8E)，包含了所有细节
@[toc]

# $\textbf{2. }$定理$\textbf{2.2}$证明的思路

> ## $\textbf{2.0. }$定理$\textbf{2.2}$的主要内容
>
> > 👉条件$1$：给定单个查询$Q$以及多个段落$P\text{=}\left\{P_{1},\ldots,P_{n}\right\}$并且$Q,\forall{}P_i\text{⊆}\mathbb{R}^{d}$，并令$\displaystyle{}m\text{=}|Q|\text{+}\max_{i\text{∈}[n]}\left|P_{i}\right|$
> >
> > 👉条件$2$：给定$\forall\varepsilon\text{>}0$，设置参数$k_{\text{sim}}\text{=}O\left(\cfrac{\log{m}}{\varepsilon}\right),d_{\text {proj}}\text{=}O\left(\cfrac{1}{\varepsilon^{2}}\log\left(\cfrac{m}{\varepsilon}\right)\right),R_{\text{reps}}\text{=}O\left(\cfrac{1}{\varepsilon^{2}}\log{n}\right)$ 
> >
> > 👉条件$3$：令$\displaystyle{}i^{*}\text{=}\arg\max_{i\text{∈}[n]}\text{FDE}(Q,P_i)$，即$P_{i^*}$是通过$\text{Muvera}$方法找到的，与查询$Q$最相似的段落
> >
> > 👉结论$1$：$\displaystyle{}\cfrac{1}{|Q|}\text{Chamfer}\left(Q, P_{i^{*}}\right)\text{≥}\max_{i\text{∈}[n]}\cfrac{1}{|Q|}\text{Chamfer}\left(Q, P_{i}\right)–\varepsilon$以$\text{Pr=}1\text{–}\cfrac{1}{\text{poly}(n)}$概率成立 
>
> ## $\textbf{2.1. }$定理$\textbf{2.2}$的证明概要
>
> > ➡️在考虑重复$R_{\text{reps}}$次的情况下，对于每个重复$k\text{∈}\left[R_{\text{reps}}\right]$，设定每次重复对最终相似度的贡献为$\text{FDE}^k(Q,P_\alpha)$
> >
> > 1. 对于最终相似度，有$\text{FDE}(Q,P_\alpha)\text{=}\displaystyle\sum_{k\text{=}1}^{R_{\text{reps}}}\text{FDE}^k(Q,P_\alpha)$，不妨设定随机变量$X_k\text{=}\cfrac{1}{|Q|}\text{FDE}^k(Q,P_\alpha)$ 
> >
> > ➡️最关键的一步在于，对$X_k$尝试运用<font color=red>$\text{Chernoff}$界限</font>，即$\forall{X_i}\text{∈}[a,b]$有$\displaystyle{}\text{Pr}\left[\left|\frac{1}{R}\sum_{i=1}^RX_i–\mu\right|\text{≥}\varepsilon\right]\text{≤}2e^{\left(–\frac{2R\varepsilon^2}{(b–a)^2}\right)}$ 
> >
> > 1. 对$\text{Chernoff}$界限中参数的确定
> >    - 将上式中$R$视作$R_{\text{reps}}$，并将$R_{\text{reps}}\text{=}O\left(\cfrac{1}{\varepsilon^{2}}\log{n}\right)$作为前提引入
> >    - 对于$\mu$即均值，根据<font color=red>定理$\text{2.1}$</font>引入前提$k_{\text{sim}}\text{=}O\left(\cfrac{\log{m}}{\varepsilon}\right),d_{\text {proj}}\text{=}O\left(\cfrac{1}{\varepsilon^{2}}\log\left(\cfrac{m}{\varepsilon}\right)\right)$后，有$\mathbb{E}[X_k]\text{∈}\cfrac{1}{|Q|}\text{Chamfer}(Q,P_\alpha)\text{±}\varepsilon$ 
> >    - 对于$[a,b]$即$X_k$的范围，不难得到$X_k\text{∈}[–m,m]$
> > 2. 将以上参数套用到$\text{Chernoff}$界限则有
> >    - 概率：以$\text{Pr}\text{≥}1–2e^{\left(–\frac{R_{\text{reps}}\varepsilon^2}{2m^2}\right)}$概率成立，可以进一步转化为$\text{Pr}\text{≥}1–2e^{\left(–\frac{R_{\text{reps}}\varepsilon^2}{2m^2}\right)}\text{≥}1–\cfrac{2}{n^C}\text{=}1\text{–}\cfrac{1}{\text{poly}(n)}$  
> >    - 事件：$\displaystyle{}\left|\sum_{k\text{=}1}^{R_{\text{reps}}}\cfrac{X_k}{R_{\text{reps}}}–\cfrac{1}{|Q|}\text{Chamfer}(Q,P_\alpha)\right|\text{≤}2\varepsilon$
> >      - 代入$\text{FED}$最相似文档$P_{\alpha^*}$后，有$\cfrac{1}{|Q|}\text{Chamfer}(Q,P_\alpha^*)\text{≥}\cfrac{1}{|Q|R_{\text{reps}}}\text{FDE}(Q,P_\alpha^*)–2\varepsilon\text{=}\displaystyle{}\max_{\alpha\text{∈}[n]}\cfrac{1}{|Q|R_{\text{reps}}}\text{FDE}(Q,P_\alpha)–2\varepsilon$  
> >      - 根据<font color=red>定理$\text{2.1}$</font>又可以知道$\displaystyle{}\max_{\alpha\text{∈}[n]}\cfrac{1}{|Q|R_{\text{reps}}}\text{FDE}(Q,P_\alpha)\text{≥}\max_{\alpha\text{∈}[n]}\cfrac{1}{|Q|}\text{Chamfer}(Q,P_\alpha)\text{–}\varepsilon$ 
> >      - 所以最终原事件可转化为$\displaystyle{}\cfrac{1}{|Q|}\text{Chamfer}(Q,P_\alpha^*)\text{≥}\max_{\alpha\text{∈}[n]}\cfrac{1}{|Q|}\text{Chamfer}(Q,P_\alpha)\text{–}3\varepsilon$，对$\varepsilon$做常数倍变换即证毕