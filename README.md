## LayerNorm

一个原始输入向量 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathbf{x}\in\mathbb{R}^m)，经过线性变换、非线性激活之后，被映射为输出向量 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathbf{y}\in\mathbb{R}^n)：

<p align="center">
<img src="https://latex.codecogs.com/svg.latex?a_i=\sum_{j=1}^{m}\omega_{ij}x_j,\quad{}y_i=f(a_i+b_i)," alt="LaTeX">
</p>

此时输入分布的变化将对参数梯度的稳定性产生负面影响，于是 LayerNorm 对输入分布进行了固定均值与方差的归一化：

<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\overline{a}_i=\frac{a_i-\mu}{\sigma}g_i,\quad{}\mu=\frac{1}{n}\sum_{i=1}^{n}a_i,\quad{}\sigma=\sqrt{\frac{1}{n}\sum_{i=1}^{n}(a_i-\mu)^2},\quad{}y_i=f(\overline{a}_i+b_i)," alt="LaTeX">
</p>

引入 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathbf{g}\in\mathbb{R}^n) 是为了恢复因归一化而损失的表达能力。
<br><br><br>

## RMSNorm

RMSNorm 执行了不同的归一化：

<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\overline{a}_i=\frac{a_i}{\textbf{RMS}(\mathbf{a})}g_i,\quad{}\textbf{RMS}(\mathbf{a})=\sqrt{\frac{1}{n}\sum_{i=1}^{n}a^2_i}," alt="LaTeX">
</p>

RMSNorm 相比 LayerNorm 没有减均值操作，并且在数学形式上就是输入均值 ![LaTeX](https://latex.codecogs.com/svg.latex?\mu=0) 时的 LayerNorm。即便原始输入向量 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathbf{x}) 或权重矩阵 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathbf{W}\in\mathbb{R}^{n\times{}m}) 发生缩放（各元素乘以同一个标量），RMSNorm 归一化后的向量 ![LaTeX](https://latex.codecogs.com/svg.latex?\overline{\mathbf{a}}\in\mathbb{R}^n) 的二范数也只与其隐藏维度数 ![LaTeX](https://latex.codecogs.com/svg.latex?n) 有关：


<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\|\overline{\mathbf{a}}\|_2=\sqrt{\sum_{i=1}^n\overline{a}_i^2}=\sqrt{\sum_{i=1}^n\left(\frac{a_i}{\textbf{RMS}(\mathbf{a})}\right)^2}=\sqrt{\frac{1}{\textbf{RMS}(\mathbf{a})^2}\sum_{i=1}^{n}a_i^2}=\sqrt{\frac{1}{\frac{1}{n}\sum_{i=1}^{n}a_i^2}\sum_{i=1}^{n}a_i^2}=\sqrt{n}," alt="LaTeX">
</p>

从而各分量数值可控，非线性激活后的各分量数值也可控。
<br><br><br>

## 不变性

RMS 具有线性，即：

<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\textbf{RMS}(\alpha{}x)=\sqrt{\frac{1}{n}\sum_{i=1}^{n}(\alpha{}x_i)^2}=\sqrt{\frac{1}{n}\alpha^2\sum_{i=1}^{n}x_i^2}=\alpha\sqrt{\frac{1}{n}\sum_{i=1}^{n}x_i^2}=\alpha\textbf{RMS}(x)," alt="LaTeX">
</p>

进而：

<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\frac{\alpha{}x}{\textbf{RMS}(\alpha{}x)}=\frac{\alpha{}x}{\alpha\textbf{RMS}(x)}=\frac{x}{\textbf{RMS}(x)}," alt="LaTeX">
</p>

于是设：

<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\mathbf{a}'=\delta\mathbf{a}=\mathbf{W}(\delta\mathbf{x})\quad{}or\quad{}(\delta\mathbf{W})\mathbf{x}," alt="LaTeX">
</p>

从而有：

<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\mathbf{y}'=f\left(\frac{\mathbf{a}'}{\textbf{RMS}(\mathbf{a}')}\odot\mathbf{g}+\mathbf{b}\right)=f\left(\frac{\delta\mathbf{a}}{\textbf{RMS}(\delta\mathbf{a})}\odot\mathbf{g}+\mathbf{b}\right)=f\left(\frac{\mathbf{a}}{\textbf{RMS}(\mathbf{a})}\odot\mathbf{g}+\mathbf{b}\right)=\mathbf{y}," alt="LaTeX">
</p>

这意味着，无论原始输入向量 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathbf{x}) 缩放一个标量 ![LaTeX](https://latex.codecogs.com/svg.latex?\delta) 还是权重矩阵 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathbf{W}) 缩放一个标量 ![LaTeX](https://latex.codecogs.com/svg.latex?\delta)，非线性激活层的输出向量 ![LaTeX](https://latex.codecogs.com/svg.latex?y) 均不会发生改变。但如果 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathbf{x}) 或 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathbf{W}) 发生平移（各元素加减同一个标量），则没有这种不变性。<br>
总的来说，<br>
LayerNorm 通过减均值 ![LaTeX](https://latex.codecogs.com/svg.latex?\mu) 实现“重新中心化”以获得平移不变性，通过除标准差 ![LaTeX](https://latex.codecogs.com/svg.latex?\sigma) 实现“重新缩放”以获得缩放不变性；<br>
RMSNorm 则仅通过除以根均方

<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\sqrt{\frac{1}{n}\sum_{i=1}^{n}a_i^2}" alt="LaTeX">
</p>

实现“重新缩放”以获得缩放不变性。作者团队通过实验发现 RMSNorm 的效果与 LayerNorm 的相比，持平甚至更优。
<br><br><br>

## 梯度

Santurkar 等人指出归一化方法的成功并非来自于输入稳定性的增强，而是缘于优化景观平滑性的改善。

<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\mathbf{y}=f(\mathbf{v}),\quad{}\mathbf{v}=\frac{\mathbf{a}}{\textbf{RMS}(\mathbf{a})}\odot\mathbf{g}+\mathbf{b},\quad{}\mathbf{a}=\mathbf{W}\mathbf{x}," alt="LaTeX">
</p>

其中 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathbf{g},\mathbf{b},\mathbf{W}) 是作为优化对象的可学习参数，损失 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathcal{L}) 关于 ![LaTeX](https://latex.codecogs.com/svg.latex?\mathbf{g},\mathbf{b}) 的梯度分别为：
