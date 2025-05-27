[原论文](https://arxiv.org/abs/2405.19504)

[详细的理论证明](https://github.com/DANNHIROAKI/Reading-Papers/tree/main/0035.%20MUVERA/%E7%90%86%E8%AE%BA%E4%BF%9D%E8%AF%81%E7%9A%84%E8%AF%81%E6%98%8E)，包含了所有细节
@[toc]

# $\textbf{1. }$定理$\textbf{2.1}$及其证明

> ## $\textbf{1.0. }$定理$\textbf{2.1}$内容：$\textbf{FDE}$对$\textbf{Chamfer}$相似度的概率近似界定理
>
> > 👉前提$1$：设定$\forall{}\varepsilon,\delta\text{>}0$(其中$\varepsilon{}\text{≤}\cfrac{1}{2}$且$\delta{≤}\varepsilon$)，给定单位向量集$P,Q\text{⊆}\mathbb{R}^d$并满足$m\text{=}|Q|\text{+}|P|$
> >
> > 👉前提$2$：选择参数$k_{\text{sim}}\text{=}O\left(\cfrac{1}{\varepsilon}\log{\left(\cfrac{m}{\delta}\right)}\right)$，$d_{\text{proj}}\text{=}O\left(\cfrac{1}{\varepsilon^2}\log{\left(\cfrac{m}{\varepsilon\delta}\right)}\right)$，$R_{\text{reps}}\text{=}1$
> >
> > 👉结论：$\text{Chamfer}(Q,P)\text{–}\varepsilon\text{≤}\text{FDE}(Q,P)\text{≤}\text{Chamfer}(Q,P)\text{+}\varepsilon$以$\text{Pr}\text{≥}1\text{–}\delta$概率成立，**即给出了$\textbf{FDE}$相似度的上下界**
>
> ## $\textbf{1.1. }$无投影无重复时$\textbf{FDE}$相似度的上界
>
> > :arrow_right:$\text{Chamfer}$后期交互的本质：一套针对$q_i$向量的映射策略
> >
> > <img src="https://i-blog.csdnimg.cn/direct/3e451b4ee68a4c60886f50cc99a071ba.png" alt="image-20250223225238538" width=697 />  
> >
> > 1. $f(q_i)$操作为将$q_i$映射到某一个$p_{q_i}\text{∈}P\text{=}\{p_1,p_2,...,p_m\}$向量
> > 2. 在$\text{Chamfer}$后期交互中$f(q_i)\text{=}p_{q_i}\text{=}\arg\limits_{p\text{∈}P}\text{MaxSim}(q_i,P)$，即遍历$p_{q_i}\text{∈}P\text{=}\{p_1,p_2,...,p_m\}$，最终选取使内积$\langle{q_i,p_{q_i}}\rangle$最大的$p_{q_i}$ 
> > 3. 最终合并所有的内积$\displaystyle{\sum_{i\text{=1}}^n}\langle{q_i,p_{q_i}}\rangle$是为$\text{Chamfer}$相似度，即$\displaystyle{\sum_{i\text{=1}}^n}\langle{q_i,p_{q_i}}\rangle\text{=}\text{Chamfer}(Q,P)\text{=}\langle{Q,P^\prime}\rangle$
> >
> > :arrow_right:$\text{Muvera}$分桶的本质：另一套针对$q_i$向量的映射策略
> >
> > <img src="https://i-blog.csdnimg.cn/direct/a6aa7552b6e4459eaee07e57cc6bd5c1.png" alt="image-20250227173148677" width=597 /> 
> >
> > 1. 对于单个桶$\text{Bucket-k}$中有$\langle{\vec{q}_{(k)},\vec{p}_{(k)}}\rangle\text{=}\left\langle{\displaystyle\sum_{k_i}{}q_{k_i},\vec{p}_{(k)}}\right\rangle\text{=}\displaystyle\sum_{k_i}{}\left\langle{q_{k_i},\vec{p}_{(k)}}\right\rangle$，相当于为每个落入该桶$k$的$q_{k_i}$映射了一个$\vec{p}_{(k)}$
> > 2. 合并$B$个桶的结果有$\displaystyle\sum_{k\text{=}1}^{B}\langle{\vec{q}_{(k)},\vec{p}_{(k)}}\rangle\text{=}\displaystyle\sum_{q_i\text{∈}Q}{}\left\langle{q_{i},\vec{p}_{(k_{q_i})}}\right\rangle$，相当于为每个$q_i\text{∈}Q$都映射一个$\vec{p}_{(k_{q_i})}\text{=}p_{q_i}^*\text{∈}\{\vec{p}_{(1)},...,\vec{p}_{(B)}\}$
> >
> > :arrow_right:对两种映射方案的对比：有$\langle{q_i,p_{q_i}^*}\rangle\text{≤}\langle{q_i,p_{q_i}}\rangle$
> >
> > <img src="https://i-blog.csdnimg.cn/direct/b8aa8bfb183640a2b5eb8385e20bb7ad.png" alt="image-20250227181053033" width=550 />  
> >
> > 1. $p_{q_i}$的得到有且只有一种情况，就是$\displaystyle{}p_{q_i}\text{=}\arg\max_{p\text{∈}P}\langle{q_i,p}\rangle\text{=}\arg\limits_{p\text{∈}P}\text{MaxSim}(q_i,P)$
> > 2. $p_{q_i}^*$的得到分为三种情况，即$\text{Case-0/Case-n}$(其中视$\text{Case-0}$为$\text{Case-n}$种$\text{n=1}$的情形)
> >    - $\text{Case-0}$时，假设$q_i$所落入的是桶$k$，则选取离桶$k$编码最近的一点$\hat{p}_{k}$，即$p_{q_i}^*\text{=}\displaystyle{}\vec{p}_{(k)}\text{=}\hat{p}_{k}$ 
> >    - $\text{Case-n}$时，假设$q_i$所落入的是桶$k$，则$p_{q_i}^*\text{=}\displaystyle{}\vec{p}_{(k)}\text{=}\displaystyle{}\sum_{\varphi(p_j)\text{=}k}\cfrac{1}{\left|P\text{∩}\varphi^{–1}(k)\right|}p_j$，相当于桶$k$种所有$p$向量的质心
> > 3. 定义集合$\text{Pdt=}\{\langle{q_i,p_{1}}\rangle,\langle{q_i,p_{2}}\rangle,...,\langle{q_i,p_{m}}\rangle\}$，根据$p_{q_i}$的定义可知$\langle{q_i,p_{q_1}}\rangle$是$\text{Pdt}$集合的最大值
> >    - $\text{Case-0}$时，显然有$\langle{q_i,p_{q_i}^*}\rangle\text{=}\langle{q_i,\hat{p}_{k}}\rangle\text{∈Pdt}$，因此$\langle{q_i,p_{q_i}^*}\rangle\text{≤}\langle{q_i,p_{q_i}}\rangle$即小于集合最大值
> >    - $\text{Case-n}$时，对每个$p_j$都有$\langle{q_i,p_{q_i}^*}\rangle\text{=}\left\langle{q_i},\displaystyle{}\sum_{\varphi(p_j)\text{=}k}\cfrac{1}{\left|P\text{∩}\varphi^{–1}(k)\right|}p_j\right\rangle\text{=}\displaystyle{}\sum_{\varphi(p_j)\text{=}k}\cfrac{1}{\left|P\text{∩}\varphi^{–1}(k)\right|}\langle{q_i,p_j}\rangle$
> >      - 集合$\text{Pdt}$中$\displaystyle{}\sum_{\varphi(p_j)\text{=}k}\cfrac{1}{\left|P\text{∩}\varphi^{–1}(k)\right|}\langle{q_i,p_j}\rangle$是子集均值，$\langle{q_i,p_{q_i}}\rangle$是全集最大值，后者必然更大
> >      - 所以$\langle{q_i,p_{q_i}^*}\rangle\text{=}\displaystyle{}\sum_{\varphi(p_j)\text{=}k}\cfrac{1}{\left|P\text{∩}\varphi^{–1}(k)\right|}\langle{q_i,p_j}\rangle\text{≤}\langle{q_i,p_{q_i}}\rangle$
> >
> > :arrow_right:两种相似评分的对比
> >
> > <img src="https://i-blog.csdnimg.cn/direct/536061d1086d469c84bc725ed7a549b4.png" alt="image-20250227195846002" width=815 />   
> >
> > 1. $\text{Chamfer}$相似度：根据定义为$\text{Chamfer}(Q,P)\text{=}\displaystyle{\sum_{i\text{=1}}^n}\langle{q_i,p_{q_i}}\rangle\text{=}\langle{Q,P}\rangle$ 
> > 2. $\text{FDE}$相似度：根据定义没经过投影和重复的$\text{FDE}$相似度可写作$\text{FDE}(Q,P)\text{=}\displaystyle\sum_{k\text{=}1}^{B}\langle{\vec{q}_{(k)},\vec{p}_{(k)}}\rangle\text{=}\sum_{i\text{=1}}^n\langle{q_i,p_{q_i}^*}\rangle\text{=}\langle{Q,P^\prime}\rangle$
> > 3. 已证了$\langle{q_i,p_{q_i}^*}\rangle\text{≤}\langle{q_i,p_{q_i}}\rangle$故$\text{FDE}(Q,P)\text{≤}\text{Chamfer}(Q,P)$
>
> ## $\textbf{1.2. }$加上一定维度的投影后原上界近似成立
>
> > :arrow_right:$\text{Muvera}$分桶$\text{+}$投影的过程
> >
> > <img src="https://i-blog.csdnimg.cn/direct/f825b1be147c4d62b5d34528b1a7a6f1.png" alt="image-20250227204328958" width=630 /> 
> >
> > 1. 同样首先相当于为每个$q_i\text{∈}Q$都映射一个$\vec{p}_{(k_{q_i})}\text{=}p_{q_i}^*\text{∈}\{\vec{p}_{(1)},...,\vec{p}_{(B)}\}$，但是后续参与交互(内积)的从原始向量变为了投影向量
> > 2. 所以原有的$\text{FDE}(Q,P)$为直接将内积$\left\langle{q_i,p^*_{q_i}}\right\rangle$累加，现在变成将各自投影后的内积$\left\langle{\psi(q_i),\psi\left(p^*_{q_i}\right)}\right\rangle$累加
> >    - $\text{FDE}$相似度(无投影)：可写作$\text{FDE}_{\text{orgn}}(Q,P)\text{=}\displaystyle\sum_{k\text{=}1}^{B}\langle{\vec{q}_{(k)},\vec{p}_{(k)}}\rangle\text{=}\sum_{i\text{=1}}^n\langle{q_i,p_{q_i}^*}\rangle$，且已证$\text{FDE}_{\text{orgn}}(Q,P)\text{≤}\text{Chamfer}(Q,P)$
> >    - $\text{FDE}$相似度(无投影)：可写作$\text{FDE}_{\text{proj}}(Q,P)\text{=}\displaystyle\sum_{k\text{=}1}^{B}\left\langle{\psi\left(\vec{q}_{(k)}\right),\psi\left(\vec{p}_{(k)}\right)}\right\rangle\text{=}\sum_{i\text{=1}}^n\left\langle{\psi(q_i),\psi\left(p^*_{q_i}\right)}\right\rangle$ 
> >
> > :arrow_right:[事实$\text{A2}$](https://dannhiroaki.blog.csdn.net/article/details/146030060)表明了：给定$d_{\text{proj}}\text{=}O\left(\cfrac{1}{\varepsilon^{2}}\log\cfrac{m}{\delta}\right)$<font color=red>则$\langle{}x,y\rangle\text{–}\varepsilon\text{≤}\langle\psi(x),\psi(y)\rangle{}\text{≤}\langle{}x,y\rangle\text{+}\varepsilon$以$\text{Pr}\text{≥}1\text{–}\delta$概率成立</font> 
> >
> > 1. 将$d_{\text{proj}}\text{=}O\left(\cfrac{1}{\varepsilon^{2}}\log\cfrac{m}{\delta}\right)$作为前提引入，代入结论有$\displaystyle\sum_{i\text{=1}}^n\langle{q_i,p_{q_i}^*}\rangle\text{–}|Q|\varepsilon\text{≤}\sum_{i\text{=1}}^n\left\langle{\psi(q_i),\psi\left(p^*_{q_i}\right)}\right\rangle\text{≤}\sum_{i\text{=1}}^n\langle{q_i,p_{q_i}^*}\rangle\text{+}|Q|\varepsilon$
> >    - 概率调整为，以$\text{Pr}\text{≥}(1\text{–}\delta)^n\text{≥}1\text{–}|Q|\delta$概率成立
> >    - 也可写作<mark>$\displaystyle\text{FDE}_{\text{orgn}}(Q,P)\text{–}|Q|\varepsilon\text{≤}\text{FDE}_{\text{proj}}(Q,P)\text{≤}\text{FDE}_{\text{orgn}}(Q,P)\text{+}|Q|\varepsilon$
> > 2. 稍作调整，则有$\text{FDE}_{\text{proj}}(Q,P)\text{≤}\text{FDE}_{\text{orgn}}(Q,P)\text{+}|Q|\varepsilon\text{≤}\text{Chamfer}(Q,P)\text{+}|Q|\varepsilon$，以$\text{Pr}\text{≥}1\text{–}|Q|\delta$概率成立
> > 3. 再按常数$Q$的比例调整$\delta$(注意$\varepsilon$为与后续统一故不调整)，则有$\text{FDE}_{\text{proj}}(Q,P)\text{≤}\text{Chamfer}(Q,P)\text{+}|Q|\varepsilon$，以$\text{Pr}\text{≥}1\text{–}\delta$概率成立
>
> ## $\textbf{1.3. }$已知无重复$\textbf{FDE}$上界👉还需什么才能给出无重复$\textbf{FDE}$的上下界
>
> > :arrow_right:不妨假设需要证明的是$\text{Chamfer}(Q,P)\text{–}|Q|\varepsilon\text{≤}\text{FDE}(Q,P)\text{≤}\text{Chamfer}(Q,P)\text{+}|Q|\varepsilon$以$\text{Pr}\text{≥}1\text{–}\delta$概率成立
> >
> > 1. 令下界成立为事件$F_L/$上界成立为事件$F_U/$上下界都成立为事件$F$，原问题变为要证明$\text{Pr}[F]\text{≥}1\text{–}\delta$
> > 2. 不难得到当$\varepsilon{}\text{≤}\cfrac{1}{2}$时，同时满足$\text{Pr}[F_L]\text{≥}1\text{–}\varepsilon\delta$和$\text{Pr}[F_U]\text{≥}1\text{–}\varepsilon\delta$，可推导出$\text{Pr}[F]\text{≥}1\text{–}\delta$
> > 3. <font color=red>由已证明的上界结论</font>可知设定$d_{\text{proj}}\text{=}O\left(\cfrac{1}{\varepsilon^2}\log{\left(\cfrac{m}{\varepsilon\delta}\right)}\right)$则有$\text{Pr}[F_U]\text{≥}1\text{–}\varepsilon\delta$
> > 4. 所以增加前提$d_{\text{proj}}\text{=}O\left(\cfrac{1}{\varepsilon^2}\log{\left(\cfrac{m}{\varepsilon\delta}\right)}\right)$以及$\varepsilon{}\text{≤}\cfrac{1}{2}$后，原问题变为要证明$\text{Pr}\left[F_{L}\right]\text{≥}1\text{–}\varepsilon\delta$
> >
> > :arrow_right:对$\text{Chamfer}(Q,P)\text{–}|Q|\varepsilon\text{≤}\text{FDE}(Q,P)$的进一步变换
> >
> > <img src="https://i-blog.csdnimg.cn/direct/876ce4f32d0c4530995d669dd8575135.png" alt="image-20250227232407524" width=850 />  
> >
> > 1. 根据$\text{Chamfer}$的定义有$\text{Chamfer}(Q,P)\text{=}\displaystyle{}\sum_{q_i\text{∈}Q}\text{MaxSim}(q_i,P)$
> > 2. 又由于$\text{FDE}$对$q_i$的处理式独立线性的，所以$\text{FDE}(Q,P)\text{=}\displaystyle{}\sum_{q_i\text{∈}Q}\text{FDE}(q_i,P)$
> > 3. 所以原问题变为需证$\displaystyle{}\sum_{q_i\text{∈}Q}\left(\text{MaxSim}(q_i,P)\text{–}\varepsilon\right)\text{≤}\sum_{q_i\text{∈}Q}\text{FDE}(q_i,P)$，以$\text{Pr}\text{≥}1\text{–}\varepsilon\delta$概率成立
> > 4. 去掉求和号后，原问题变为需证$\text{MaxSim}(q_i,P)\text{–}\varepsilon\text{≤}\text{FDE}(q_i,P)$，以$\text{Pr}\text{≥}1–\cfrac{\varepsilon\delta}{|Q|}$概率成立
> >
> > :arrow_right:后续称$\text{FDE}(q_i,P)$的上下界为$\text{FDE}$的<mark>**子上下界**</mark>
>
> ## $\textbf{1.4. }$无投影无重复时$\textbf{FDE}$相似度的子下界
>
> > ➡️结合之前的分析，对于$\displaystyle\text{FDE}(q_i,P)\text{=}\left\langle{q_i,p_{q_i}^*}\right\rangle$，在无投影时总存在一个子集$S\text{⊆}P$，使得$p_{q_i}^*\text{=}\displaystyle{}\cfrac{1}{|S|}\sum_{p_j\text{∈}S}p_j\text{=}S_{\text{center}}$
> >
> > <img src="https://i-blog.csdnimg.cn/direct/4ec607027640453882876993fe4d79a9.png" alt="image-20250228001845209" width=850 />  
> >
> > 1. 当单个$q_i$落入的桶属于$\text{Case-0}$情形时，$S$是单个点$\displaystyle{}\hat{p}_k\text{=}\arg{}\min_{p_j\text{∈}P}\|\varphi(q_i)–\varphi(p_j)\|_{0}$，也就是离桶编码$\varphi(q_i)$海明距离最近的点
> > 2. 当单个$q_i$落入的桶属于$\text{Case-n}$情形时，$S$元素需满足$\varphi(q_i)\text{=}\varphi(p_j)$，也就是所有落入该桶的$q_j$点
> >
> > ➡️对[事实$\text{A4}$](https://blog.csdn.net/qq_64091900/article/details/146046666)给定$k_{\text{sim}}\text{=}O\left(\cfrac{1}{\varepsilon}\log{\left(\cfrac{m}{\delta}\right)}\right)$时<font color=red>$\forall{p_j}\text{∈}P$有$\left|\cfrac{\|\varphi(q_i)–\varphi(p_j)\|_0}{k_{\mathrm{sim}}}\text{–}\cfrac{\theta(q_i,p_j)}{\pi}\right|\text{≤}\sqrt{\varepsilon}$以$\text{Pr}\text{＞}1–\cfrac{\varepsilon{}\delta{}}{m^2}$概率成立</font>
> >
> > <img src="https://i-blog.csdnimg.cn/direct/ee7518bcf9304fa09345bedcfb2fa631.png" alt="image-20250228004958647" width=430 /> 
> >
> > 1. 对于${q_i}$和$p^*_{q_1}$还有$p_{q_1}$，总是会有$\cfrac{\|\varphi(q_i)–\varphi(p^*_{q_1})\|_0}{k_{\mathrm{sim}}}\text{≤}\cfrac{\|\varphi(q_i)–\varphi(p_{q_1})\|_0}{k_{\mathrm{sim}}}$ 
> >    - 对于$\text{Case-0}$情形，$p^*_{q_i}$为离编号为$\varphi(q_i)$的桶海明距离最近的$p_j$向量，所以${\|\varphi(q_i)–\varphi(p^*_{q_i})\|_0}\text{≤}{\|\varphi(q_i)–\varphi(p_{q_i})\|_0}$ 
> >    - 对于$\text{Case-n}$情形，$p^*_{q_i}$为与$q_i$同桶的所有$p_j$向量的质心，即$S\text{=}\left\{p_j\text{∈}P|{\varphi}(q_i)\text{=}\varphi(p_j)\right\}$的质心$S_{\text{center}}$
> >      - 回忆$\text{SimHash}$分桶的本质，就是用$k_\text{sim}$个法平面将空间切割为$2^{k_\text{sim}}$个子空间
> >      - 所有$p_j\text{∈}S$都落入同一桶即在同一子空间，则其质心$p^*_{q_i}\text{=}S_{\text{center}}$也一定在该子空间中，所以$\varphi(q_i)\text{=}\varphi(p_j)\text{=}\varphi(p^*_{q_i})$
> >      - 所以${\|\varphi(q_i)–\varphi(p^*_{q_i})\|_0}\text{=}0\text{≤}{\|\varphi(q_i)–\varphi(p_{q_i})\|_0}$ 
> > 2. $p_{q_1}$和$p^*_{q_1}$都代入事实$\text{A4}$，则$\cfrac{\theta({q_i,p^*_{q_1}})}{\pi}–\sqrt{\varepsilon}\text{≤}\cfrac{\|\varphi(q_i)–\varphi(p^*_{q_1})\|_0}{k_{\mathrm{sim}}}\text{≤}\cfrac{\|\varphi(q_i)–\varphi(p_{q_i})\|_0}{k_{\mathrm{sim}}}\text{≤}\cfrac{\theta({q_i,p_{q_i}})}{\pi}\text{+}\sqrt{\varepsilon}$以$\text{Pr}\text{＞}1–\cfrac{\varepsilon{}\delta{}}{m^2}$概率成立
> >    - 因此$\left|\theta(q_i,p^*_{q_1})–\theta({q_i,p_{q_i}})\right|\text{=}O\left(\sqrt{\varepsilon}\right)$以$\text{Pr}\text{＞}1–\cfrac{\varepsilon{}\delta{}}{m^2}$概率成立
> >    - 上式可得$\left|\cos\theta(q_i,p^*_{q_1})–\cos\theta({q_i,p_{q_i}})\right|\text{<}O(\varepsilon)$以$\text{Pr}\text{＞}1–\cfrac{\varepsilon{}\delta{}}{m^2}$成立，其中$\begin{cases}\cos\theta(q_i,p^*_{q_1})\text{=}\langle{q_i,p^*_{q_1}}\rangle\text{=}\text{FDE}(q_i,P)\\\\\cos\theta({q_i,p_{q_i}})\text{=}\langle{q_i,p_{q_i}}\rangle\text{=}\text{MaxSim}(q_i,P)\end{cases}$
> >    - ==所以$\text{MaxSim}(q_i,P)–O(\varepsilon)\text{≤}\text{FDE}(q_i,P)\text{≤}\text{MaxSim}(q_i,P)\text{+}O(\varepsilon)$== 以$\text{Pr}\text{＞}1–\cfrac{\varepsilon{}\delta{}}{m^2}$概率成立
> >
> > ➡️事实$\text{A4}$给出了$\text{FDE}(q_i,P)$的子上下界
> >
> > 1. 取结论的左半边$\text{MaxSim}(q_i,P)–O(\varepsilon)\text{≤}\text{FDE}(q_i,P)$并给概率放水$\text{Pr}\text{＞}1–\cfrac{\varepsilon{}\delta{}}{m^2}\text{>}1–\cfrac{\varepsilon\delta}{|Q|}$，便得到了一个更弱的结论
> > 2. 再对$O(\varepsilon)$进行常数缩放为$\varepsilon$，该更弱的结论就变成了所要证的$\text{FDE}$相似度的子下界
>
> ## $\textbf{1.5. }$加上一定维度的投影后原子下界近似成立
>
> > ➡️投影前后的对比：用$\text{FDE}_{\text{orgn}}$和$\text{FDE}_{\text{proj}}$表示区分
> >
> > <img src="https://i-blog.csdnimg.cn/direct/876ce4f32d0c4530995d669dd8575135.png" alt="image-20250227232407524" width=850 />   
> >
> > 1. 投影前为$\displaystyle{}\text{FDE}_{\text{orgn}}(q_i,P)\text{=}\left\langle{q_i,p_{q_i}^*}\right\rangle\text{=}\left\langle{}q_i,\cfrac{1}{|S|}\sum_{p_j\text{∈}S}p_j\right\rangle{}\text{=}\cfrac{1}{|S|}\sum_{p_j\text{∈}S}{\langle{}q_i,p_j\rangle}$ 
> > 2. 投影后为$\displaystyle{}\text{FDE}_{\text{proj}}(q_i,P)\text{=}\left\langle{\psi(q_i),\psi\left(p_{q_i}^*\right)}\right\rangle\text{=}\left\langle{}{\psi}(q_i),{\psi}\left(\cfrac{1}{|S|}\sum_{p_j\text{∈}S}p_j\right)\right\rangle{}\text{=}\cfrac{1}{|S|}\sum_{p_j\text{∈}S}\left\langle{\psi(q_i),\psi\left(p_j\right)}\right\rangle$
> >
> > ➡️应用事实$\text{A2}$：用$\cfrac{\varepsilon\delta}{|Q|}$替代$\delta$以设定$d_{\text{proj}}\text{=}O\left(\cfrac{1}{\varepsilon^{2}}\log\left(\cfrac{|Q|}{\varepsilon\delta}\right)\right)\text{=}O\left(\cfrac{1}{\varepsilon^{2}}\log\left(\cfrac{m}{\varepsilon\delta}\right)\right)$ 
> >
> > 1. 则有<font color=red>内积的偏差$\langle{}x,y\rangle\text{–}\varepsilon\text{≤}\langle\psi(x),\psi(y)\rangle{}\text{≤}\langle{}x,y\rangle\text{+}\varepsilon$以$\text{Pr}\text{≥}1\text{–}\cfrac{\varepsilon\delta}{|Q|}$概率成立概率成立</font>
> > 2. 将事实$\text{A2}$代回则有$\displaystyle{}\cfrac{1}{|S|}\sum_{p_j\text{∈}S}{\langle{}q_i,p_j\rangle}\text{–}\varepsilon\text{≤}\cfrac{1}{|S|}\sum_{p_j\text{∈}S}\left\langle{\psi(q_i),\psi{}p_j}\right\rangle\text{≤}\cfrac{1}{|S|}\sum_{p_j\text{∈}S}{\langle{}q_i,p_j\rangle}\text{+}\varepsilon$，以$\text{Pr}\text{≥}1\text{–}\cfrac{\varepsilon\delta}{|Q|}$概率成立概率成立
> >    - 其中$\displaystyle{}\text{FDE}_{\text{orgn}}(q_i,P)\text{=}\cfrac{1}{|S|}\sum_{p_j\text{∈}S}{\langle{}q_i,p_j\rangle}$，以及$\displaystyle{}\text{FDE}_{\text{proj}}(q_i,P)\text{=}\cfrac{1}{|S|}\sum_{p_j\text{∈}S}\left\langle{\psi(q_i),\psi\left(p_j\right)}\right\rangle$
> >    - 所以<mark>$\displaystyle{}\text{FDE}_{\text{orgn}}(q_i,P)\text{–}\varepsilon\text{≤}\text{FDE}_{\text{proj}}(q_i,P)\text{≤}\text{FDE}_{\text{orgn}}(q_i,P)\text{+}\varepsilon$</mark>，以$\text{Pr}\text{≥}1\text{–}\cfrac{\varepsilon\delta}{|Q|}$概率成立概率成立
> > 3. 已证$\text{MaxSim}(q_i,P)–O(\varepsilon)\text{≤}\text{FDE}_{\text{proj}}(q_i,P)\text{≤}\text{MaxSim}(q_i,P)\text{+}O(\varepsilon)$以$\text{Pr}\text{≥}1–\cfrac{\varepsilon\delta}{|Q|}$概率成立
> >    - 代入得$\displaystyle\text{FDE}_{\text{proj}}(q_i,P)\text{≥}\text{FDE}_{\text{orgn}}(q_i,P)\text{–}\varepsilon\text{≥}\displaystyle{}\text{MaxSim}(q_i)\text{–}\varepsilon–O(\varepsilon)$
> >    - 对$\varepsilon$进行常数因子缩放后，即证毕
