# notebook-RMSNorm_paper

## LayerNorm

一个原始输入向量 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathbf{x}\in\mathbb{R}^m)，经过线性变换、非线性激活之后，被映射为输出向量 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathbf{y}\in\mathbb{R}^n)：
<p align="center">
<img src="https://latex.codecogs.com/svg.latex?a_i=\sum_{j=1}^{m}\omega_{ij}x_j,\quad{}y_i=f(a_i+b_i)," alt="LaTeX">
</p>
此时输入分布的变化将对参数梯度的稳定性产生负面影响，于是 LayerNorm 对输入分布进行了固定均值与方差的归一化：
<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\overline{a}_i=\frac{a_i-\mu}{\sigma}g_i,\quad{}\mu=\frac{1}{n}\sum_{i=1}^{n}a_i,\quad{}\sigma=\sqrt{\frac{1}{n}\sum_{i=1}^{n}(a_i-\mu)^2},\quad{}y_i=f(\overline{a}_i+b_i)," alt="LaTeX">
</p>
引入 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathbf{x}\in\mathbb{R}^m) 是为了恢复因归一化而损失的表达能力。

## RMSNorm

RMSNorm 执行了不同的归一化：

$$
\overline{a}_i = \frac{a_i}{\text{RMS}(\mathbf{a})} g_i \space , \quad \text{RMS}(\mathbf{a}) = \sqrt{\frac{1}{n} \sum_{i=1}^{n} a^2_i} \space ,
$$

RMSNorm 相比 LayerNorm 没有减均值操作，并且在数学形式上就是输入均值 $\mu = 0$ 时的 LayerNorm。即便原始输入向量 $\mathbf{x}$ 或权重矩阵 $\mathbf{W} \in \mathbb{R}^{n \times m}$ 
