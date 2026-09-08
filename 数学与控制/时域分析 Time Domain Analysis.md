##### Intro
时域分析法是一种直接在时间域中对线性控制系统进行分析的方法，具有 **直观、准确** 的优点，并且可以提供系统时间相应的全部信息。在研究 [[控制系统]] 时，我们使用 [[典型输入信号]]。常选择系统可能遇到的最不利的信号作为系统的典型输入信号。噪声影响大时，不能使用典型输入信号，使用[[随机过程理论]]。

### Subconcepts
* 动态过程和稳态过程
* 动态性能
	* 上升时间 (rise time)    $t_{r}$
		  响应从终值 10% 上升到 90% 所需时间.
		  对于 **有振荡** 的系统，可定义为相应由零 **第一次** 上升到终值的时间。
	* 峰值时间 (peak time)   $t_{p}$
		  响应超过其终值 **第一次** 上升到终值所需的时间.
	* 调节时间 (settling time)   $t_{s}$
		  响应到达并保持在终值 $\pm$%5(2) 内所需的最短时间
	* 超调量 (overshoot)   $\sigma\%$
		  相应的最大偏差量 $c(t_{p})$ 与终值 $c(\infty)$ 的差与终值的比的百分数：
		  $$
		  \sigma\% = \frac{c(tp)-c(\infty)}{c(\infty)} \times 100\%
		  $$
* 稳态性能

### Instance
#### 一阶系统时域分析
以一阶微分方程作为运动方程的控制系统，称为 **一阶系统**。
数学建模为：
$$
T\dot{c}(t)+c(t)=r(t)
$$
0 输入条件下，其传递函数为：
$$
G(s)=\frac{1}{Ts+1}
$$
##### 单位阶跃响应
$$
G(s) = \frac{C(s)}{1(s)}
$$
$$
C(s) = \frac{1}{(Ts+1)s} = \frac{1}{T}\frac{1}{ \left( s+\frac{1}{T} \right)s} = \frac{1}{s}-\frac{1}{s+\frac{1}{T}}
$$
$$
c(t) = 1 - e^{-t/T}
$$
![[一阶系统的单位跃阶相应曲线.png]]


##### 单位脉冲响应
$$
G(s) = \frac{C(s)}{1} = C(s) = \frac{1}{Ts+1}
$$
$$
c(t) = \frac{1}{T} e^{-t/T}
$$
![[一阶系统单位脉冲响应.png]]


##### 单位斜坡相应
$$
G(s)= \frac{C(s)}{\frac{1}{s^{2}}}
$$
$$
C(s) = \frac{1}{s^{2}(Ts+1)}  =\frac{T^{2}}{Ts+1}+\frac{1}{s^{2}}
 - \frac{T}{s} $$
$$
c(t) = Te^{-t/T} + t - T 
$$
![[一阶单位斜坡相应.png]]

##### 单位加速度相应

$$
G(s)= \frac{C(s)}{\frac{1}{s^{3}}}
$$
$$
C(s) = \frac{1}{s^{3}(Ts+1)}  = -\frac{T^{3}}{Ts+1} + \frac{1}{s^{3}} - \frac{T}{s ^{2}} + \frac{T^{2}}{s}
$$
$$
c(t) = \frac{1}{2}t^{2} - Tt + T^{2}(1-e^{-t/T})
$$
![[一阶系统单位加速度相应.png]]



系统对输入信号导数的响应，就等于系统对该输入信号响应的导数；或者，系统对输入信号积分的响应，就等于系统对该输入信号响应的积分，而积分常数由零输 出初始条件确定。这是线性定常系统的一个重要特性，适用于任何阶线性定常系统，但 **不适用于** 线性时变系统和非线性系统。因此，研究线性定常系统的时间响应，不必对每种输入信号形式进行测定和计算，往往只取其中一种典型形式进行研究。



### 二阶系统时域分析
以二阶微分方程描述运动方程的控制系统，称为 **二阶系统**。
数学建模为：
$$
\frac{1}{\omega_{n}^{2}}\frac{d^{2}c(t)}{dt^{2}}+\frac{2\zeta}{\omega_{n}} \frac{dc(t)}{dt}+ c(t) = r(t)
$$
0 输入条件下，其开环传递函数为:
$$
	G_{o}(s) = \frac{K}{s(T_{m} s+1)}
$$
对应闭环传递函数为：
$$
G(s) = \frac{G_{o}(s)}{G_{o}(s) - 1}  = \frac{\omega_{n}^{2}}{s^{2}+2\zeta \omega_{n}s+\omega_{n}^{2}}
$$
*  $\omega_{n} = \sqrt{\frac{K}{T_m}}$      为自然频率（无阻尼振荡频率）
*   $\zeta\,\,=\frac{1}{2\sqrt{ T_{m}K }}$  为阻尼比
*  $K$  为 [[开环增益]]
*  $T_{m}$ 为 [[机电时间常数]]

![[二阶系统.png]]

取 $G(s)$ 分母为 0，得到 [[特征方程]] $s^{2}+2\zeta \omega_{n}s+\omega_{n}^{2} = 0$, 其两个根分别为 $-\zeta \omega_{n}\pm \omega_{n}\sqrt{ \zeta^{2}-1 }$
称为 [[闭环极点]].


#### 二阶系统单位阶跃响应
##### 欠阻尼  $\zeta \in (0,1)$
记
$$
\sigma =\zeta \omega_{n}\,,\omega_{d}=\omega_{n}\sqrt{ 1-\zeta^{2} }
$$
称 $\sigma$ 为**衰减系数**，$\omega_{d}$ 为**阻尼振荡频率**.
有
$$
s_{1,2} = -\sigma\pm j\omega_{d}
$$
当 $R(s) = \frac{1}{s}$ 时：
$$
C(s) = \frac{\omega_{n}^{2}}{s(s^{2}+2\zeta \omega _{n}s+\omega_{n}^{2})} = \frac{1}{s} - \frac{s+2\zeta \omega_{n}}{(s+\omega_{n}\zeta)^{2}+(1-\zeta^{2})\omega_{n^{2}}}
$$
$$
=\frac{1}{s}- \frac{s + \zeta \omega_{n}}{(s + \zeta \omega_{n})^{2} + \omega_{d}^{2}} - \frac{\zeta \omega_{n}}{(s+\zeta \omega_{n})^{2}+\omega_{d}^{2}}
$$
$$
c(t) = 1 - \frac{1}{\sqrt{ 1-\zeta^{2} }} e^{-\sigma t} \sin(\omega_{d}t+\beta),\,\,t\geq 0，\,\beta = \arccos \zeta = \arctan\left( \frac{\sqrt{ 1-\zeta^{2} }}{\zeta} \right)
$$
其中，1 称为 **稳态分量**，阻尼正弦振荡项称为 **瞬态分量**，其振荡频率为 $\omega_{d}$.
其 [[包络线]] 为 $1\pm \frac{e^{-\zeta \omega_{n}t}}{\sqrt{ 1-\zeta }^{2}}$, 当 $\zeta$ 一定，其收敛速度取决于 $\zeta \omega_{n}$, 即衰减系数 $\sigma$.

![[omegan.gif]]

随 $\zeta$ 增大，函数变化趋势如上图.

![[zeta.gif]]

随 $\omega _n$ 增大，函数变化趋势如上图.

对应动态性能计算：
$$
		  \sigma\% = e^{\frac{-\zeta \pi}{\sqrt{ 1-\zeta^{2} }}} \times 100\%
		  $$
  $$
			t_{p}=\frac{\pi}{\omega_{n}\sqrt{ 1 - \zeta^{2} }} = \frac{\pi}{\omega _{d}}
  $$
  $$
			t_{r} = \frac{\pi - \beta}{\omega_{n} \sqrt{  1 - \zeta^{2}}} = \frac{\pi - \beta}{\omega_{d}}
  $$
  $$
	t_{s} = \frac{3.5}{\omega _{n}\zeta} = \frac{3.5}{\sigma} 
  $$
  若误差带 $\Delta = 0.02$
  $$
	t_{s} = \frac{4.4}{\omega_{n}\zeta} = \frac{4.4}{\sigma}
  $$
  
##### 无阻尼 $\zeta = 0$
$$c(t) = 1 - \cos \omega_{n }t\, , t\geq 0$$

##### 临界阻尼  $\zeta = 1$
$$
C(s) = \frac{\omega_{n}^{2}}{s(s+\omega_{n})^{2}} = \frac{1}{s} - \frac{\omega_{n}}{(s_{_{n}}+\omega_{n})^{2}}-\frac{1}{s+\omega_{n}}
$$
$$
c(t) = 1-e^{-\omega_{n}t}(1+\omega_{n}t),\,t\geq 0
$$
![[Pasted image 20260330211515.png]]

##### 过阻尼  $\zeta>1$
记
$$
T_{1}=\frac{1}{\omega_{n}(\zeta-\sqrt{ \zeta^{2}-1 })}=- \frac{1}{-\sigma-j\omega_{d}},\,T_{2}=\frac{1}{\omega_{n}(\zeta+\sqrt{ \zeta^{2} -1})}=-\frac{1}{-\sigma+j\omega_{d}}
$$
有
$$
C(s)=\frac{\omega^{2}}{s\left( s+\frac{1}{T_{1}} \right)\left( s-\frac{1}{T_{2}} \right)}
$$
$$
c(t)=1+\frac{e^{-t/T_{1}}}{\frac{T_{2}}{T_{1}}-1}+\frac{e^{-t/T_{2}}}{\frac{T_{1}}{T_{2}}-1}, \,t\geq0
$$

动态系数如下：
$$
	t_{r}= \frac{1 + 1.5\zeta +\zeta^{2}
}{\omega_{n}}$$
$$
	t_{s} = 
$$
![[Pasted image 20260330212333.png]]

#### 误差分析


