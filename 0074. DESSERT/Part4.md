# $\textbf{1. }$定理$\textbf{4.2}$的内容

> ## $\textbf{1.1. }$一些符号
>
> > :one:一些基础的符号
> >
> > 1. 基本符号：
> >    | 符号 | 含义                                                         |
> >    | :--: | :----------------------------------------------------------- |
> >    | $Q$  | 查询集，此处只考虑一个$Q$并且$Q{=}\{q_1,q_2,...,q_{m_q}\}$(此处假设$m_q$为常数)，记其子向量为$q_r{∈}Q$ |
> >    | $S$  | 目标集，此处假定每个${\mid}S{\mid}{=}m$(常数)，$S^*$与$Q$评分最大(其元素为$x^*_j{∈}S^*$)，其余都记为$S_i$(其元素为$x_{ij}{∈}S_i$) |
> >    | $N$  | 目标集的集即$D\text{=}\{S_1,...,S_N\}$，记其元素为$S_i{∈}N$  |
> > 2. 相似度集合
> >    |          符号           | 含义                                                         |
> >    | :---------------------: | :----------------------------------------------------------- |
> >    |   ${\mathbf{s}}_{ri}$   | $q_r$与$\forall{x_{ij}}{∈}S_i$的精确相似度的集合，即${\mathbf{s}}_{ri}{=}\{\text{Sim}(q_r,x_{i1}),...,\text{Sim}(q_r,x_{im})\}$ |
> >    | $\hat{\mathbf{s}}_{ri}$ | $q_r$与$\forall{x_{ij}}{∈}S_i$的近似相似度的集合，即$\hat{\mathbf{s}}_{ri}{=}\{\hat{\text{Sim}}(q_r,x_{i1}),...,\hat{\text{Sim}}(q_r,x_{im})\}$ |
> >    |   ${\mathbf{s}}_r^*$    | $q_r$与$\forall{}x^*_j{∈}S^*$的精确相似度的集合，即${\mathbf{s}}_{ri}{=}\{\text{Sim}(q_r,x_1^*),...,\text{Sim}(q_r,x_m^*)\}$ |
> >    | $\hat{\mathbf{s}}_r^*$  | $q_r$与$\forall{}x^*_j{∈}S^*$的近似相似度的集合，即${\mathbf{\hat{s}}}_{ri}{=}\{\hat{\text{Sim}}(q_r,x_1^*),...,\hat{\text{Sim}}(q_r,x_m^*)\}$ |
> > 3. 相似度及其最大值
> >    |                 符号                  | 含义                                                         |
> >    | :-----------------------------------: | :----------------------------------------------------------- |
> >    |        $s_{rij}$和$s_{ri\max}$        | $q_r$与$\forall{x_{ij}}{∈}S_i$中的$s_{rij}{=}\text{Sim}(q_r,x_{ij})$，其最大值记作$s_{ri\max}{=}\max({\mathbf{s}}_{ri})$且此时向量记为$x_{ij}^*$ |
> >    |  $\hat{s}_{rij}$和$\hat{s}_{ri\max}$  | $q_r$与$\forall{x_{ij}}{∈}S_i$中的$\hat{s}_{rij}{=}\hat{\text{Sim}}(q_r,x_{ij})$，其最大值记作$\hat{s}_{ri\max}{=}\max(\hat{{\mathbf{s}}}_{ri})$且此时向量记为$x_{ij}^*$ |
> >    |      ${s}_{rj}^*$和$s_{r\max}^*$      | $q_r$与$\forall{}x^*_j{∈}S^*$中的${s}_{rj}^*{=}\text{Sim}(q_r,x_j^*)$，其最大值记作$s_{r\max}^*{=}\max({\mathbf{s}}_r^*)$且此时向量记为$x_j^{**}$ |
> >    | $\hat{s}_{rj}^*$和$\hat{s}_{r\max}^*$ | $q_r$与$\forall{}x^*_j{∈}S^*$中的$\hat{s}_{rj}^*{=}\hat{\text{Sim}}(q_r,x_j^*)$，其最大值记作$\hat{s}_{r\max}^*{=}\max({\mathbf{\hat{s}}}_r^*)$且此时向量记为$x_j^{**}$ |
> > 4. 几种聚合：($w_r$为权值)
> >    - 内部聚合：即$σ$或$\max$(最大值聚合)，以$\sigma$为例对不同集合的聚合记作$σ({\mathbf{s}}_{ri})/σ(\hat{\mathbf{s}}_{ri})/σ({\mathbf{s}}_r^*)/σ(\hat{\mathbf{s}}_r^*)$
> >    - 外部聚合：聚合$σ({\mathbf{s}}_{ri})/σ(\hat{\mathbf{s}}_{ri})/σ({\mathbf{s}}_r^*)/σ(\hat{\mathbf{s}}_r^*)$得到评分
> >      - 对$S_i$有：$F\left({Q,S_i}\right)\text{=}\displaystyle{}\frac{1}{m_q}\sum_{r=1}^{m_q}w_rσ({\mathbf{s}}_{ri})$和$\hat{F}\left({Q,S_i}\right)\text{=}\displaystyle{}\frac{1}{m_q}\sum_{r=1}^{m_q}w_rσ({\mathbf{\hat{s}}}_{ri})$
> >      - 对$S^*$有：${F}\left({Q,S^*}\right)\text{=}\displaystyle{}\frac{1}{m_q}\sum_{r=1}^{m_q}w_rσ({\mathbf{s}}_r^*)$和$\hat{F}\left({Q,S^*}\right)\text{=}\displaystyle{}\frac{1}{m_q}\sum_{r=1}^{m_q}w_rσ(\hat{\mathbf{s}}_r^*)$
> >
> > :two:定理参数与结论
> >
> > 1. 界限参数：对于$0\text{<}β\text{≤}1\text{≤}α$ 
> >    |          符号          | 含义                                                         |
> >    | :--------------------: | :----------------------------------------------------------- |
> >    | $F(Q, S^*)$的下界$B^*$ | $B^*\text{=}\displaystyle{}\frac{β}{m_q}\sum_{r=1}^{m_q}w_rs_{r\max}^*$即$S^*$的保守估计 |
> >    | $F(Q, S_i)$的上界$B_i$ | $B_i\text{=}\displaystyle{}\fracα{m_q}\sum_{r=1}^{m_q}w_r\hat{s}_{ri\max}$即$S_i$的乐观估计，$S^*{\notin}\{S_i\}$时$B_i$最大值为$B_{i\max}$，及$\Delta{\text{=}}\cfrac{B^*–B_{i\max}}{3}$ |
> > 2. 其它参数：(有些是证明过程中的)
> >    |         符号         | 含义                                                         |
> >    | :------------------: | :----------------------------------------------------------- |
> >    |     失败概率$δ$      | 算法的失败概率，目标是以至少$1{-}δ$的概率正确返回$S^*$       |
> >    |     哈希数量$L$      | $\text{DESSERT}$中对每个$q_r{∈}Q$和$x_{ij}{∈}S_i$进行$L$次分桶，此处设为$L\text{=}\displaystyle{}O\left({\log\left(\frac{N{m}_{q}m}{δ}\right)}\right)$ |
> >    |     上界$γ_{ri}$     | 关于$\Delta_{ir}/s_{ri\max}/\tau_{ir}$的函数($\tau_{ri}{=}αs_{ri\max}{+}\Delta_{ri}$)，当$\Delta_{ri}$固定时最大值$(γ_{ri})_{\max}{∈}\left(1{-}\cfrac{\Delta_{ri}}{α},1\right)$ |
> >    | 指示随机$\mathbb{1}$ | 例如事件$A$发生了则有$\mathbb{1}_A{=}1$，而本文中$\mathbb{1}{=}1$表示所有上界和下界条件同时满足 |
>
> ## $\textbf{1.2. }$定理内容
>
> > :one:定理结论：设定$\Delta\text{＞}0$
> >
> > 1. 第一种表述：$\Pr\left[\forall{i}\left(\hat{F}(Q,S^*){>}\hat{F}(Q,S_i)\right)\right]{≥}1{-}δ$
> > 2. 第一种表述：$\text{DESSERT}$算法结构能以$1{-}δ$的概率，返回与$Q$相似度最高的$S^*\text{=}\mathop{\operatorname{argmax}}\limits_{{i{∈}\{1,\ldots,N\}}}F\left( {Q,S_{i}}\right)$   
> >
> > :two:证明思路：要证$\Pr\left[\forall{i}\left(\hat{F}(Q,S^*){>}\hat{F}(Q,S_i)\right)\right]{≥}1{-}δ$ 
> >
> > 0. 总体思路：找到一个$L$满足
> >    - 上界控制：对于所有$S_i{≠}S^*$，确保其估计得分$\hat{F}(Q,S_i)$不超过某个阈值
> >    - 下界控制：对于$S^*$，确保其估计得分$\hat{F}(Q,S^*)$不低于某个阈值
> >    - 联合界限：上述条件同时以高概率成立，从而保证$\hat{F}(Q,S^*){>}\hat{F}(Q,S_i)$ 
> > 1. 上下界限：当$L{=}\max\left\{\cfrac{\ln\left(\cfrac{δ}{2(N{-}1)m_qm}\right)}{\ln{\left((γ_{ri})_{\max}\right)}},\cfrac{β^2\ln\left(\cfrac{4m_q}{\delta}\right)}{2\Delta_{ri}^2}\right\}$时
> >    - 上界：$S_i{≠}S^*$中共$(N{-}1)m_q$个$q_r$，有$\Pr\left[\forall{q_r}\left({σ(\hat{\mathbf{s}}_{ri}){≥}α s_{ri\max}{+}\Delta_{ri}}\right)\right]{≤}\cfrac{δ}{2}$即$σ(\hat{\mathbf{s}}_{ri})$高概率在上界$αs_{ri\max}{+}\Delta_{ri}$下
> >    - 下界：$S^*$中共$m_q$个$q_r$，有$\Pr\left[\forall{q_r}\left({σ(\hat{\mathbf{s}}_r^*){≤}βs_{r\max}^*{-}\Delta_{ri}}\right)\right]{≤}\cfrac{δ}{2}$即$σ(\hat{\mathbf{s}}_r^*)$高概率在下界$βs_{r\max}^*{-}\Delta_{ri}$上
> > 2. 联合界限：保证$\hat{F}(Q,S^*){>}\hat{F}(Q,S_i)$高概率成立
> >    - 假定：$\mathbb{1}$为一个二元指示，当$\mathbb{1}{=}1$时表示上述$(N{-}1)m_q$个上界和$m_q$个下界同时成立
> >      - 上界：对所有$S_i{≠}S^*$中共$(N{-}1)m_q$个$q_r$，有$σ(\hat{\mathbf{s}}_{ri})$都在上界$αs_{ri\max}{+}\Delta_{ri}$下
> >      - 下界：所有$S^*$中共$m_q$个$q_r$，有$σ(\hat{\mathbf{s}}_r^*)$都在下界$βs_{r\max}^*{-}\Delta_{ri}$上
> >    - 思路：先证明蕴含关系即$\mathbb{1}{=}1$时事件$\forall{i}\left(\hat{F}(Q,S^*){>}\hat{F}(Q,S_i)\right)$成立，再证明$\Pr(\mathbb{1}{=}1){=}1{-}\delta$ 

# $\textbf{3. }$联合界限

> :zero:基本思路：保证$\hat{F}(Q,S^*){>}\hat{F}(Q,S_i)$高概率成立
>
> 1. 假定：$\mathbb{1}$为一个二元指示，当$\mathbb{1}{=}1$时表示$(N{-}1)m_q$个上界和$m_q$个下界同时成立
>    - 上界：对所有$S_i{≠}S^*$中共$(N{-}1)m_q$个$q_r$，有$σ(\hat{\mathbf{s}}_{ri})$都在上界$αs_{ri\max}{+}\Delta_{ri}$下
>    - 下界：所有$S^*$中共$m_q$个$q_r$，有$σ(\hat{\mathbf{s}}_r^*)$都在下界$βs_{r\max}^*{-}\Delta_{ri}$上
> 2. 思路：先证明蕴含关系即$\mathbb{1}{=}1$时事件$\forall{i}\left(\hat{F}(Q,S^*){>}\hat{F}(Q,S_i)\right)$成立，再证明$\Pr(\mathbb{1}{=}1){≥}1{-}\delta$ 
>
> :one:蕴含关系的证明：对$\Pr\left[\forall{i}\left(\hat{F}(Q,S^*){>}\hat{F}(Q,S_i)\right)\right]$的不断进行变换
>
> 1. 由定义$\hat{F}\left({Q,S_i}\right)\text{=}\displaystyle{}\frac{1}{m_q}\sum_{r=1}^{m_q}w_rσ({\mathbf{\hat{s}}}_{ri})$和$\hat{F}\left({Q,S^*}\right)\text{=}\displaystyle{}\frac{1}{m_q}\sum_{r=1}^{m_q}w_rσ(\hat{\mathbf{s}}_r^*)$
>    - 代入得，**原式**${=}\Pr\left[\forall{i}\left(\hat{F}(Q,S^*){-}\hat{F}(Q,S_i){>}0\right)\right]{=}\Pr\left[\forall{i}\left(\left(\displaystyle{}\frac{1}{m_q}\sum_{r=1}^{m_q}w_rσ(\hat{\mathbf{s}}_r^*){-}\displaystyle{}\frac{1}{m_q}\sum_{r=1}^{m_q}w_rσ({\mathbf{\hat{s}}}_{ri})\right){>}0\right)\right]$
>    - 化简得，**原式**${=}\Pr\left[\forall{i}\left(\displaystyle{}\sum_{r=1}^{m_q}w_r\left(σ(\hat{\mathbf{s}}_r^*){-}σ({\mathbf{\hat{s}}}_{ri})\right){>}0\right)\right]$ 
> 2. 考虑有$\Pr(A){≥}\Pr(A{∩}B){=}\Pr(A|B)\Pr(B)$，所以**原式**${≥}\displaystyle\Pr\left[\forall{i}\left(\sum_{r=1}^{m_q}w_r \left(\sigma(\hat{\mathbf{s}}_r^*){-}\sigma(\hat{\mathbf{s}}_{ri}) \right){>}0\middle|\mathbb{1}{=}1\right)\right]\Pr\left[\mathbb{1}{=}1\right]$ 
>    - 当$\mathbb{1}{=}1$时有$σ(\hat{\mathbf{s}}_{ri}){≤}αs_{ri\max}{+}\Delta_{ri}$以及$σ(\hat{\mathbf{s}}_r^*){≥}βs_{r\max}^*{-}\Delta_{ri}$，所以$σ(\hat{\mathbf{s}}_r^*){-}σ(\hat{\mathbf{s}}_{ri}){≥}βs_{r\max}^*{-}αs_{ri\max}{-}2\Delta_{ri}$ 
>    - 故$βs_{r\max}^*{-}αs_{ri\max}{-}2\Delta_{ri}{>}0$发生时，$σ(\hat{\mathbf{s}}_r^*){-}σ(\hat{\mathbf{s}}_{ri}){>}0$必发生，即$βs_{r\max}^*{-}αs_{ri\max}{-}2\Delta_{ri}{>}0$事件概率更小
>    - 故$\displaystyle\Pr\left[\forall{i}\left(\sum_{r=1}^{m_q}w_r \left(\sigma(\hat{\mathbf{s}}_r^*){-}\sigma(\hat{\mathbf{s}}_{ri}) \right){>}0\middle|\mathbb{1}{=}1\right)\right]{≥}\displaystyle\Pr\left[\forall{i}\left(\sum_{r=1}^{m_q}w_r \left(βs_{r\max}^*{-}αs_{ri\max}{-}2\Delta_{ri}\right){>}0\right)\right]$
>    - 稍作变换，**原式**${≥}\displaystyle\Pr\left[{{\forall }{i}\left({\sum_{{r=1}}^{{m}_{q}}{w}_{r}\left({βs_{r\max}^*{-}\alpha s_{ri\max}}\right){>}{2\Delta_{ri}}\sum_{{r = 1}}^{{m}_{q}}{w}_{r}}\right)}\right]\Pr(\mathbb{1}{=}1)$ 
> 3. 由定义$B^*\text{=}\displaystyle{}\frac{β}{m_q}\sum_{r=1}^{m_q}w_rs_{r\max}^*$和$B_i\text{=}\displaystyle{}\fracα{m_q}\sum_{r=1}^{m_q}w_r\hat{s}_{ri\max}$ 
>    - 代入得，**原式**${≥}\Pr\left[{\forall{i}\left({{m}_{q}\left({B^*{-}B_i}\right){>}{2\Delta_{ri} }\displaystyle\sum_{{r = 1}}^{{m}_{q}}{w}_r}\right) }\right]\Pr({\mathbb{1}{=}1})$ 
>    - 又由于$B_{i,\max}{>}B_i$，即$\Pr\left[{\forall{i}\left({{m}_{q}\left({B^*{-}B_i}\right){>}{2\Delta_{ri} }\displaystyle\sum_{{r=1}}^{{m}_{q}}{w}_r}\right) }\right]{≥}\Pr\left[{\forall{i}\left({{m}_{q}\left({B^*{-}B_{i\max}}\right){>}{2\Delta_{ri} }\displaystyle\sum_{{r=1}}^{{m}_{q}}{w}_r}\right) }\right]$ 
>    - 所以，**原式**${≥}\Pr\left[{\forall{i}\left({{m}_{q}\left({B^*{-}B_{i\max}}\right){>}{2\Delta_{ri} }\displaystyle\sum_{{r=1}}^{{m}_{q}}{w}_r}\right) }\right]\Pr({\mathbb{1}{=}1})$ 
> 4. 定义阈值：让所有的$\Delta_{ri}$都为固定值，且$\Delta{=}\cfrac{B^*–B_{i\max}}{3}$ 
>    - 所以，**原式**${≥}\Pr\left[{\forall{i}\left({3{m}_{q}\Delta{>}{2\Delta}\displaystyle\sum_{{r=1}}^{{m}_{q}}{w}_r}\right)}\right]\Pr({\mathbb{1}{=}1})$
>    - 又考虑到$1{≥}{w}_r$即$2m_q\Delta{≥}2\Delta\displaystyle\sum_{{r=1}}^{{m}_{q}}{w}_r$，所以$\Pr\left[{\forall{i}\left({3{m}_{q}\Delta{>}{2\Delta}\displaystyle\sum_{{r=1}}^{{m}_{q}}{w}_r}\right)}\right]{≥}\Pr\left[{\forall{i}\left({3{m}_{q}\Delta{>}2m_q\Delta}\right)}\right]$
>    - 所以**原式**${≥}\Pr\left[{\forall{i}\left({3{m}_{q}\Delta{>}{2\Delta}}\right)}\right]\Pr({\mathbb{1}{=}1}){=}\Pr({\mathbb{1}{=}1}){=}1{-}\Pr({\mathbb{1}{=}0})$ 
>
> :two:概率的证明：$\Pr(\mathbb{1}{=}1){=}1{-}\delta$ 
>
> 1. 试图证明$\Pr(\mathbb{1}{=}0)$，并将该失败事件分为
>    - 至少有一个上界失败，即$U_{\text{fail}}{=}\left({∃}(r,S_i{≠}S^*)\left({σ(\hat{\mathbf{s}}_{ri}){≥}α s_{ri\max}{+}\Delta_{ri}}\right)\right)$，已证设定$L$后$\Pr\left(U_{\text{fail}}\right){≤}\cfrac{\delta}{2}$
>    - 至少有一个下界失败，即$L_{\text{fail}}{=}\left({∃}(r,S_i{=}S^*)\left({σ(\hat{\mathbf{s}}_r^*){≤}βs_{r\max}^*{-}\Delta_{ri}}\right)\right)$，已证设定$L$后$\Pr\left(L_{\text{fail}}\right){≤}\cfrac{\delta}{2}$
> 2. 所以$\Pr(\mathbb{1}{=}0){≤}\Pr\left(U_{\text{fail}}\right){+}\Pr\left(L_{\text{fail}}\right){=}\delta$，即$\Pr(\mathbb{1}{=}1){≥}1{-}\delta$ 
> 3. 最后$\small{}L{=}\max\left\{\cfrac{\ln\left(\cfrac{2(N{-}1)m_qm}{δ}\right)}{\ln{\left(\cfrac{1}{(γ_{ri})_{\max}}\right)}},\cfrac{β^2\ln\left(\cfrac{4m_q}{\delta}\right)}{2\Delta_{ri}^2}\right\}{=}\max\left\{O\left( {\log \left( \cfrac{N{m}_{q}m}{\delta}\right) }\right),O\left( {\log \left( \cfrac{{m}_{q}}{\delta }\right) }\right)\right\}{=}O\left( {\log \left( \cfrac{N{m}_{q}m}{\delta}\right) }\right)$证毕

# $\textbf{Ps. }$运行时间分析

> :one:一些假设
>
> 1. 计算任何一个$\text{LSH}$函数$\psi{_t}(x)$的时间都为$O(d)$，其中$d$为向量$x$的维度
> 2. 让$|\mathcal{D}{\left\lbrack{}i\right\rbrack}_{t,h}|\text{＜}T$，即用哈希函数$\psi_t$对集合$S_i$分桶，落入哈希值为$h$的桶的向量数不超过常数$T$ 
> 3. 让哈希数量$L{=}O\left( {\log \left( \cfrac{N{m}_{q}m}{\delta}\right) }\right)$，于定理中的一致
>
> :two:复杂度：$O\left(m_qLd{+}\displaystyle\sum_{q_r=q_1}^{q_r=q_{m_q}}\sum_{S_i=S_1}^{S_i=S_N}\sum_{\psi_t=\psi_1}^{\psi_t=\psi_L}\mathcal{D}{\left\lbrack{}i\right\rbrack}_{t,\psi_{t}(q_r)}\right)$
>
> 1. $O\left(m_qLd\right)$表示对查询$Q$的处理，给$m_q$个向量$q_r$每个计算$L$个哈希值，每个哈希值计算耗时$O(d)$，所以为$O\left(m_qLd\right)$
> 2. $O\left(\displaystyle\sum_{q_r=q_1}^{q_r=q_{m_q}}\sum_{S_i=S_1}^{S_i=S_N}\sum_{\psi_t=\psi_1}^{\psi_t=\psi_L}\mathcal{D}{\left\lbrack{}i\right\rbrack}_{t,\psi_{t}(q_r)}\right)$表示对$D\text{=}\{S_1,S_2,...,S_N\}$的处理
>    - $\displaystyle\sum_{\psi_t=\psi_1}^{\psi_t=\psi_L}\mathcal{D}{\left\lbrack{}i\right\rbrack}_{t,\psi_{t}(q_r)}$表示，对于固定$q_r$和$S_i$，所姚检查的所有桶中的向量的总数，每检查一个向量(与$q_r$碰撞与否)耗时$O(1)$
>    - 对于所有的$q_r$和所有的$S_i$，一共要检查$O\left(\displaystyle\sum_{q_r=q_1}^{q_r=q_{m_q}}\sum_{S_i=S_1}^{S_i=S_N}\sum_{\psi_t=\psi_1}^{\psi_t=\psi_L}\mathcal{D}{\left\lbrack{}i\right\rbrack}_{t,\psi_{t}(q_r)}\right)$次
>    - 考虑到$|\mathcal{D}{\left\lbrack{}i\right\rbrack}_{t,h}|\text{＜}T$所以$O\left(\displaystyle\sum_{q_r=q_1}^{q_r=q_{m_q}}\sum_{S_i=S_1}^{S_i=S_N}\sum_{\psi_t=\psi_1}^{\psi_t=\psi_L}\mathcal{D}{\left\lbrack{}i\right\rbrack}_{t,\psi_{t}(q_r)}\right)\text{＜}O\left(\displaystyle\sum_{q_r=q_1}^{q_r=q_{m_q}}\sum_{S_i=S_1}^{S_i=S_N}\sum_{\psi_t=\psi_1}^{\psi_t=\psi_L}T\right)\text{=}O\left(m_qNLT\right)$
> 3. 将$L{=}O\left( {\log \left( \cfrac{N{m}_{q}m}{\delta}\right) }\right)$代回
>    - 最终$O\left(m_qLd{+}m_qNLT\right){=}O\left(m_qLd{+}m_qNLT\right){=}O\left(m_qd\log \left( \cfrac{N{m}_{q}m}{\delta}\right){+}m_qNT\log \left( \cfrac{N{m}_{q}m}{\delta}\right)\right)$
>    - 另外其实还可以忽略常数$T$ 