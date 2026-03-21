### Intro
常微分方程作为表示复杂多项式的解的手段，用途实在是太广泛，物理，理论力学，各种你能想到的函数与其导数同时出现的地方，你都能找到它的存在。因此，掌握对简单的微分方程的基本的处理方法是极为重要的。

### Definition
**常微分方程**是自变量只有一个的实值 [微分方程](微分方程  Differential Equition).

### Subconcept
* 特解
	  
* 通解
	  

### Instance
##### 分离变量
形如：
$$
\frac{dy}{dx} = 2xy
$$
形式的方程，显然可将变量 y 与 x 完全分离，进而积分得到通解：
$$
\frac{1}{y}dy = 2x dx
$$
$$
\ln y = x^{2} + \mathbb{C}
$$
代入具体情况即可得到特解。

##### 齐次微分方程
若方程可转化为 $\frac{dy}{dx} = \phi\left( \frac{y}{x} \right)$ 的形式，则称其为齐次微分方程，可采用以下方法求解：
$$
\frac{dy}{dx}= \phi\left( \frac{y}{x} \right)
$$
令
$$
u = \frac{y}{x}
$$
则
$$
dy = dxu = udx + xdu
$$
$$
\frac{dy}{dx} = \frac{udx+xdu}{dx} = u+\frac{x}{dx}du = \phi\left(u \right)$$
$$
\frac{du}{\phi(u)-u} = \frac{dx}{x} = \ln x + C
$$
即求解 
$$
\frac{du}{\phi(u)-u} = \ln x + C
$$
得到 u 后，还原 y 得到通解.

##### 一阶线性微分方程
推导比较繁琐，有时间再弄 (TODO)
目前是结论：

对于齐次一阶线性微分方程
$$
y' + P(x) y = 0
$$
有：
$$
y = Ce^{-\int P(x)dx}
$$
对于一阶线性非齐次微分方程
$$
y'+P(x)y=Q(x)
$$
有：
$$
y = e^{-P(x)dx}\left( \int Q(x)e^{\int P(x)dx}dx+C \right)
$$
其中 P(x) Q(x) 可以为常数.


##### 二阶常系数线性微分方程
形式如下：
$$
y'' + p y' + q y = 0
$$
先化为特征方程：
$$
y^{2}+py+q = 0
$$
解出特征根 $r_{1},r_{2}$ ,代入方程：
$$
y = C_{1}e^{r_{1}x}+C_{2}e^{r_{2}x}
$$
若是复数根 $\alpha \pm \beta i$，则使用 [[欧拉公式]]：
$$
e^{i\theta} = \cos \theta+i\sin \theta
$$
代入可得到：
$$
y = e^{\alpha x}(C_{1}\cos \beta x+C_{2}\sin \beta x)
$$
得到方程通解.


### Reference
[第七章——微分方程 - 知乎](https://zhuanlan.zhihu.com/p/99271908)
[常微分方程 | 中文数学 Wiki | Fandom](https://math.fandom.com/zh/wiki/%E5%B8%B8%E5%BE%AE%E5%88%86%E6%96%B9%E7%A8%8B)