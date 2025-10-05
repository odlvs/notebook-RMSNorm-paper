# notebook-RMSNorm_paper

## LayerNorm
一个原始输入向量 $\mathbf{x} \in \mathbb{R}^m$，经过线性变换、非线性激活之后，被映射为输出向量 $\mathbf{y} \in \mathbb{R}^n$：

$$
a_i = \sum_{j=1}^{m} \omega_{ij} x_j \space, \quad y_i = f(a_i + b_i) \space,
$$

此时输入分布的变化将对参数梯度的稳定性产生负面影响，于是 LayerNorm 对输入分布进行了调整：

$$
\overline{a}_i = \frac{a_i - \mu}{\sigma} g_i \space , \quad \mu = \frac{1}{n} \sum_{i=1}^{n} a_i \space , \quad \sigma = \sqrt{\frac{1}{n} \sum_{i=1}^{n} (a_i - \mu)^2} \space , \quad y_i = f(\overline{a}_i + b_i) \space,
$$

引入 $\mathbf{g} \in \mathbb{R}^n$ 是为了恢复因归一化而损失的表达能力。

## RMSNorm
RMSNorm 执行了不同的调整：

$$
\overline{a}_i = \frac{a_i}{RMS(\mathbf{a})} g_i \space , \quad 
$$

