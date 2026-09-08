### Info
拉普拉斯变换是建立在 [[赫维塞变换]] 的基础之上，其目的是使用运算方法求解  [常系数线性微分方程](常微分方程%20%20Ordinary%20Differential%20Equition)，即将时间函数转换为复变量 s 的函数，从而将微分方程转化为代数方程，并使用代数方法求解。其与工程问题的相性很好，故是分析 [[工程控制系统]] 的基本数学方法。值得注意的是，在物理上可以实现的系统，总是能使用拉普拉斯变换处理的。

### Definition
满足
$$
t>0,\exists x(t)
$$
$$
\exists n>0,S.T.\forall \sigma>n,\int_{0}^{\infty}|x(t)|e^{-\sigma t}\,dt<\infty
$$
条件的连续时间函数 $x(t)$，可以进行拉普拉斯变换 $X(s)$ :
$$
X(s) = \mathcal{L}[x(t)] = \int_{0}^{\infty}e^{-st}x(t)\,dt
$$
 其中s为一有正实部的复变量，Res 大于 $\sigma$.


### Instance

#### 常见函数拉氏变换
##### 指数函数
$$
x(t)= \begin{cases}
0&,t<0 \\
 \\
Ae^{-at}&,t\geq 0
\end{cases}
$$

$$
X(s) = \int_{0}^{+\infty}Ae^{-st}e^{-at}\,dt = -A\cdot\frac{1}{s+a}e^{-(s+a)t}|_{t=0}^{+\infty} = \frac{A}{s+a}
$$

##### 阶跃函数
$$
x(t) = \begin{cases}
0 & ,t<0\\
 \\
A& ,t\geq 0
\end{cases}
$$
$$
X(s) = \int_{0}^{+\infty}Ae^{-st}\,dt = \frac{A}{s}
$$
##### 斜坡函数
$$
x(t)= \begin{cases}
0&,t<0 \\
 \\
At&,t\geq 0
\end{cases}
$$
$$
X(s) = \int_{0}^{+\infty}At e^{-st}\,dt = \frac{A}{s^{2}}
$$
##### 正弦函数
$$
x(t)=\begin{cases}
0&,t<0 \\
 \\
A\sin \omega t&,t\geq 0
\end{cases}
$$
$$
X(s)= \int_{0}^{+\infty}A\sin \omega t e^{-st}\,dt = \frac{A\omega}{\omega^{2}+s^{2}}
$$
同理：
$$
X(s)_{A\cos \omega t} = \frac{As}{\omega^{2}+s^{2}}
$$


##### 平移函数
$x(t)$ 平移 $\alpha$ 后乘阶跃函数 $1(t-\alpha)$，确保信号是延迟至 $\alpha$ 时间开始。
$$
x_{1}(t) = x(t-\alpha)1(t-\alpha)
$$
$$
X_{1}(s)=\int_{0}^{+\infty}x_{1}(t)e^{-st}\,dt = \int_{\alpha}^{+\infty}x(t-\alpha)e^{-st}\,dt = \int_{0}^{+\infty}x(\tau)e^{-s(\tau+\alpha)}d\tau = e^{-s\alpha}X(s)
$$
##### 脉动函数
$$
x(t)=\begin{cases}
\frac{A}{t_{0}}&,t<t_{0} \\
 \\
0&,其他
\end{cases}
$$
$$
X(s) = \int_{0}^{t_{0}} \frac{A}{t_{0}}e^{-st}\,dt = \frac{A}{st_{0}}(1-e^{-st_{0}})
$$
##### 脉冲函数


$$
x(t)=\begin{cases}
\lim_{ t_{0} \to 0 } \frac{A}{t_{0}}&,t<t_{0} \\
 \\
0&,其他
\end{cases}
$$
$$
X(s) = \lim_{ t_{0} \to 0 } \int_{0}^{t_{0}} \frac{A}{t_{0}}e^{-st}\,dt = \lim_{ t_{0} \to 0 } sA\frac{1-e^{-t_{0}}}{st_{0}} =A 
$$
##### $e^{-\alpha t}\cdot x(t)$
$$
X_{1}(s)=\int_{0}^{+\infty} e^{-\alpha t}x(t)e^{-st}\,dt =  X(s+\alpha)
$$
##### 比例变换 $x\left( \frac{t}{\alpha} \right)$
$$
X_{1}(s) = \int_{0}^{+\infty}  e^{-st}x\left( \frac{t}{\alpha} \right)\,dt = \alpha X(\alpha s)
$$

#### 拉普拉斯变换定理
##### 时域微分定理 **Differentiation in the Time Domain**
$$
\mathcal{L}[x(t)]= \int_{0}^{\infty} e^{-st} x(t) \, dt = -\frac{1}{s}(e^{-st}x(t)|_{t=0}^{\infty} - \int_{0}^{\infty} e^{-st}x'(t) \, dt ) = - \frac{x(0)}{s} + L[x'(t)]
$$
$$
\mathcal{L}\left[ \frac{dx(t)}{dt} \right] = sL(x(t))- x(0)  
$$
同理：
$$
L\left[ \frac{d^{n}x(t)}{dt^{n}} \right] = s ^{n}L(x(t)) - s ^{n-1}x(0) - s ^{n-2}x'(0)- \dots -x^{(n-1)}(0)
$$

##### 复微分定理 Differentiation in the s-Domain
$$
L[tx(t)] = \int_{0}^{\infty} e^{-st}tx(t) \, dt = -\int_{0}^{\infty} x(t) \frac{de^{-st}}{ds} \, dt =  -\frac{d}{ds}\int_{0}^{\infty} e^{-st}x(t) \, dt = -\frac{d}{ds}L[x(t)]
$$
同理：
$$
L[t^{n}x(t)] = (-1)^{n}\frac{d^{n}}{ds^{n}}L[x(t)]
$$
##### 时域积分定理  Integration in Time Domain
若x(t)是 [[指数级]] 的，且$x(0^{+})=x(0^{-})=x(0)$,
则：
$$
\mathcal{L}\left[ \int x(t) \, dt  \right]= -\frac{1}{s}\left( e^{-st}\int x(t)\,dt|_{t=0}^{\infty}\, - \int_{0}^{\infty} x(t)e^{-st} \, dt \right)= \frac{1}{s}(x^{-1}(0)+\mathcal{L}[x(t)])
$$

##### 终值定理 Final Value Theorem
若 $\exists \lim_{ t \to \infty }x(t)$，且 $x(t)$ 与 $x'(t)$ 可拉普拉斯变换，则有：
$$
\lim_{ t \to \infty } x(t)= \lim_{ s \to 0 } sX(s)
$$
 $sX(s)$ 所有 [[极点]] 应在 [[复平面]] 的左半平面内且不在虚轴上。

##### 初值定理 Initial Value Theorem
若 $\exists \lim_{ s \to \infty }sX(s)$，且 $x(t)$ 与 $x'(t)$ 可拉普拉斯变换，则有：
$$
\lim_{ s \to \infty } sX(s)= x(0^{+})
$$
此时对极点没有限制。

##### 卷积定理 Convolution Theorem
对于两时域函数的 [[卷积]]，满足
$$
\mathcal{L}(f(t)*g(t))=F(s)\,G(s)
$$
#### 反拉普拉斯变换 Inverse Laplace Transform
查表，具体等遇到再写（TODO）
