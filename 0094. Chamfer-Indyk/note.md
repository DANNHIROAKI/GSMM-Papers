# 1.导论

:one:各种各样的距离

1. 给定两个集合$A=\{a_1,a_2,...,a_n\}$，$B=\{b_1,b_2,...,b_m\}$

   - 定义距离$d_X\left(a_i,b_j\right)$为$\ell_p$范数，$d_X\left(a_i,b_j\right){=}\|a_i{-}b_j\|_{\ell}{=}\left(\displaystyle{\sum_{}|a_i[r]{-}b_j[r]|^p}\right)^{1/p}$

2. 推土机距离

   - 现有分布$\mu$：将总质量$W$分布在$A$中所有的点上，即每个$a_i$对应质量$w_i$且$\displaystyle\sum_{i=1}^n{w_i}{=}W$
   - 预期分布$\nu$：将总质量$W$分布在$B$中所有的点上，即每个$b_j$对应质量$w_j^\prime$且$\displaystyle\sum_{j=1}^m{w_j^\prime}{=}W$

   - 让$\pi_{ij}{>}0$表示从$a_i$传输到$b_j$的质量，则推土机距离为$\text{EMD}(\mu,\nu){=}\min\displaystyle\sum_{i=1}^n\sum_{i=1}^m\pi_{ij}d_X\left(a_i,b_j\right)$
   - 满足流量守恒，$a_i$质量全部流出$\displaystyle\sum_{j=1}^m\pi_{ij}{=}w_i$，$b_j$流入总质量与预期一致$\displaystyle\sum_{i=1}^n\pi_{ij}{=}w_j^\prime$

3. $\text{Chamfer}$距离

   - 为每个$a_i{\in}A$找到$B$中距离最小的点，距离为$\displaystyle\min_{b_j{\in}B}d_X\left(a_i,b_j\right)$
   - 将每个$a{\in}A$所找到的距离相加，即为最后的$\text{Chamfer}$距离$\text{CH}(A,B){=}\displaystyle\sum_{a_i{\in}A}\min_{b_j{\in}B}d_X\left(a_i,b_j\right)$
   - 是推土机距离的一种近似，舍弃了推土机的全局最优约束，用每个$a_i{\in}A$的局部最优代替

# 2.算法分析

:one:定理$\text{2.1}$：近线性时间估算$\text{Chamfer}$距离($\text{Chamfer-Estimate}$算法)

1. 条件：给定精度参数$1{<}\varepsilon{<}1$，向量集$A{,}B{\subset}\mathbb{R}^d$满足$|A|{,}|B|{\leq}n$，向量距离采用欧氏$\left({\ell}_{2}\right)$或曼哈顿$\left({\ell}_{1}\right)$距离
2. 结论：$\text{Chamfer-Estimate}$算法能在$O\left(\cfrac{nd\log{n}}{\varepsilon^2}\right)$时间内，以$\text{99\%}$的概率给出估计量$\eta$
   - $(1{-}\varepsilon)\text{CH}(A{,}B){\leq}\eta{\leq}(1{+}\varepsilon)\text{CH}(A{,}B)$ 