拉格朗日乘子法是将约束问题转化 [[无约束优化问题]] 的利器，通过引入拉格朗日乘子，可将有 d 个变量与 k 个约束条件的最优化问题转化为具有 d+k 个变量的无约束优化问题求解。

在理论力学方面，我们看到其是分析力学的一大基础，其导出的 [[一类拉格朗日方程]] 使我们能够在冗余 广义[[坐标 下分析物体的运动学方程。
### Definition
方程 f(x) 在约束 g(x) 下，其最值可以用下述方法处理：
$$
\begin{cases}
\nabla f(x) + \lambda_{1} \nabla g_{1}(x) = 0 \\ \\ 
\nabla f(x) + \lambda_{2} \nabla g_{2}(x) = 0 \\ \\

\dots
 \\ \\
 
g_{1}(x) = 0 \\ \\
g_{2}(x) = 0 \\ \\

\dots
\end{cases}
$$
解出的结果即为所求极值。

### Discription
想象最简单的约束情况：单等式约束，举个例子：
$$
\begin{cases}
f(x) = x_{1}^{2} + x_{2}^{2} \\
 \\
g(x) = x_{1}^{2}x_{2} = 0
\end{cases}
$$
g 为 f 的约束。

图像如下，蓝线为约束。
![[拉格朗日乘子法例子.png]]

显然，求最小值，取圆的半径从小到大，将在相切处取得。对于一个连续的函数来说，取到最值时就是在相切的时候。那么，此时可将最值条件转化为：f 与 g 相切的点。显然有 f, g 的 [[梯度]] 在同一方向上，即：
$$
\nabla f(x) + \lambda \nabla g(x) = 0
$$
与约束条件联立，即得到我们定义处写出的方程。
对于方程组
$$
\begin{cases}
\nabla f(x) + \lambda \nabla g(x) = 0 \\
 \\
g(x) = 0
\end{cases}
$$
发现拉格朗日函数
$$
\mathcal{L(x)} = f(x) + \lambda g(x)
$$
与它们有很巧妙的关系：
$$
\begin{array}{ll}
\frac{\partial \mathcal{\mathcal{L(x)}}}{\partial x} = \nabla f(x) + \lambda \nabla g(x) \\ \\

\frac{\partial L(x)}{\partial \lambda} = g(x)
\end{array}
$$

对于非等式约束，其可分类为约束无效与转化为等式约束两类问题来处理。约束无效即 $\lambda$ 为 0. 联立条件，即 $\lambda g(x) = 0$.

### Reference
[优化-拉格朗日乘子法 - 知乎](https://zhuanlan.zhihu.com/p/154517678#)