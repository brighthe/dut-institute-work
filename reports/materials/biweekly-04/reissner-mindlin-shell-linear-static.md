---
title: "四节点 Reissner–Mindlin 壳单元线性静力分析"
type: material
tags:
  - reports
  - structural-dynamics
status: final
cycle: 2026-09-07..2026-09-18
origin: "壳结构线性静力分析.md"
author: 胡凯（算海）
origin_date: 2026-09-12
date_added: 2026-09-14
date_update: 2026-09-14
---

> 来源：胡凯（算海），2026-09-12 发出（`壳结构线性静力分析.md`，87.8 KB）。全文为壳单元公式的数学推导，引用公开文献，不含源码、内部路径与算例数据。原文照录归档，未作改写。

# 四节点 Reissner–Mindlin 壳单元线性静力分析


## 1. 壳中面几何与运动学

壳体的厚度远小于面内尺度，因此可用一张二维中面和中面两侧的厚度坐标描述三维几何。设

$$
\boldsymbol r:\widehat S\subset\mathbb R^2\longrightarrow\mathbb R^3,
\qquad
(\xi^1,\xi^2)\longmapsto\boldsymbol r(\xi^1,\xi^2)
$$

是中面的正则参数化，即

$$
\boldsymbol r_{,1}\times\boldsymbol r_{,2}\ne\boldsymbol0,
$$

中面为

$$
S=\boldsymbol r(\widehat S)\subset\mathbb R^3.
$$

两个参数 $\xi^1,\xi^2$ 用来确定中面上的一个点；函数值 $\boldsymbol r=r^i\boldsymbol e_i$ 是该点在三维空间中的位置向量。$\boldsymbol r$ 只有一个空间分量轴 $i$，元素 $r^i$ 是位置在基向量 $\boldsymbol e_i$ 上的分量。参数个数描述函数定义域的维数，并不改变函数值 $\boldsymbol r$ 的张量阶数。

希腊指标 $\alpha,\beta,\gamma,\delta\in\{1,2\}$ 表示中面切向方向，拉丁指标 $i,j\in\{1,2,3\}$ 表示三维空间方向；重复指标按照 Einstein 约定求和。逗号表示对参数求偏导，例如 $\boldsymbol r_{,\alpha}=\partial\boldsymbol r/\partial\xi^\alpha$。

中面的协变基向量为

$$
\boldsymbol a_\alpha
=
\boldsymbol r_{,\alpha},
$$

写成分量形式为

$$
\boldsymbol a_\alpha
=
a^i{}_\alpha\boldsymbol e_i,
\qquad
a^i{}_\alpha
=
\frac{\partial r^i}{\partial\xi^\alpha}.
$$

$\boldsymbol a_1,\boldsymbol a_2$ 张成点 $\boldsymbol r(\xi^1,\xi^2)$ 处的切平面 $T_{\boldsymbol r}S$。固定 $\alpha$ 后，$\boldsymbol a_\alpha$ 是一个三维向量；把两个向量一起写成 $a^i{}_\alpha$ 时便出现两个轴：$\alpha$ 选择参数方向，$i$ 选择输出的空间分量。元素 $a^i{}_\alpha$ 表示沿参数方向 $\xi^\alpha$ 移动时，第 $i$ 个空间坐标的变化率。整个 $3\times2$ 数组是参数映射 $\boldsymbol r$ 的 Jacobian。

中面的第一基本形式，即度量张量，为

$$
\boldsymbol a
=
a_{\alpha\beta}\,
\mathrm d\xi^\alpha\otimes\mathrm d\xi^\beta,
\qquad
a_{\alpha\beta}
=
\boldsymbol a_\alpha\cdot\boldsymbol a_\beta.
$$

$\boldsymbol a\in\operatorname{Sym}^2(T^*_{\boldsymbol r}S)$ 是曲面余切空间上的对称二阶张量。它有两个切向输入轴；分量 $a_{\alpha\beta}$ 是两个坐标切向量的内积。对任意切向线元

$$
\mathrm d\boldsymbol r
=
\boldsymbol a_\alpha\mathrm d\xi^\alpha,
$$

其长度平方为

$$
\mathrm ds^2
=
\mathrm d\boldsymbol r\cdot\mathrm d\boldsymbol r
=
a_{\alpha\beta}
\mathrm d\xi^\alpha\mathrm d\xi^\beta,
$$

所以 $a_{11}$、$a_{22}$ 给出两个坐标方向的长度尺度，$a_{12}=a_{21}$ 给出两方向的夹角信息。度量张量的两个轴分别接收一个切向方向，元素 $a_{\alpha\beta}$ 表示这两个方向组合对长度平方的贡献。

逆度量 $a^{\alpha\beta}$ 满足

$$
a^{\alpha\gamma}a_{\gamma\beta}
=
\delta^\alpha_\beta,
$$

并定义逆变基向量

$$
\boldsymbol a^\alpha
=
a^{\alpha\beta}\boldsymbol a_\beta.
$$

$a^{\alpha\beta}$ 是矩阵 $(a_{\alpha\beta})$ 的逆矩阵元素。逆变基与协变基满足

$$
\boldsymbol a^\alpha\cdot\boldsymbol a_\beta
=
\delta^\alpha_\beta.
$$

任意切向量都可写成

$$
\boldsymbol v
=
v^\alpha\boldsymbol a_\alpha
=
v_\alpha\boldsymbol a^\alpha,
\qquad
v_\alpha=a_{\alpha\beta}v^\beta.
$$

逆变基是协变基的对偶基。它既用于升降指标，也用于组成切平面投影和曲面梯度：

$$
\boldsymbol P
=
\boldsymbol a_\alpha\otimes\boldsymbol a^\alpha,
$$

$$
\boldsymbol v\otimes\nabla_S
=
\boldsymbol v_{,\alpha}\otimes\boldsymbol a^\alpha.
$$

$\boldsymbol P$ 的第一轴是投影结果的空间方向，第二轴是被投影向量的空间方向；元素 $P_{ij}$ 表示输入方向 $j$ 对输出切向分量 $i$ 的贡献。$\boldsymbol v\otimes\nabla_S$ 的第一轴表示向量场的输出分量，第二轴表示沿哪个切向方向观察其变化。

单位法向量为

$$
\boldsymbol n
=
\frac{\boldsymbol a_1\times\boldsymbol a_2}
{\|\boldsymbol a_1\times\boldsymbol a_2\|}.
$$

$\boldsymbol n=n_i\boldsymbol e_i$ 是一阶空间张量，满足

$$
\boldsymbol n\cdot\boldsymbol a_\alpha=0,
\qquad
\boldsymbol n\cdot\boldsymbol n=1.
$$

它的唯一轴 $i$ 表示三维空间方向，元素 $n_i$ 是单位法向在方向 $\boldsymbol e_i$ 上的分量。前面的切平面投影因而也可写成 $\boldsymbol P=\boldsymbol I-\boldsymbol n\otimes\boldsymbol n$。

沿曲面移动时，切向基和法向都会变化。Gauss–Weingarten 关系为

$$
\boxed{
\begin{aligned}
\boldsymbol a_{\alpha,\beta}
&=
\Gamma^\gamma_{\alpha\beta}\boldsymbol a_\gamma
+b_{\alpha\beta}\boldsymbol n,
\\
\boldsymbol n_{,\alpha}
&=
-b_\alpha{}^\beta\boldsymbol a_\beta,
\qquad
b_\alpha{}^\beta=a^{\beta\gamma}b_{\alpha\gamma}.
\end{aligned}
}
$$

$\Gamma^\gamma_{\alpha\beta}$ 是 Christoffel 符号，描述坐标基在切平面内的变化，其分量由

$$
\Gamma^\gamma_{\alpha\beta}
=
\boldsymbol a^\gamma\cdot
\boldsymbol a_{\alpha,\beta}
$$

给出。第二基本形式，即曲率张量，定义为

$$
\boldsymbol b
=
b_{\alpha\beta}\,
\mathrm d\xi^\alpha\otimes\mathrm d\xi^\beta,
\qquad
b_{\alpha\beta}
=
-\boldsymbol a_\alpha\cdot\boldsymbol n_{,\beta}
=
\boldsymbol n\cdot\boldsymbol a_{\alpha,\beta}.
$$

$\boldsymbol b\in\operatorname{Sym}^2(T^*_{\boldsymbol r}S)$ 有两个切向输入轴，并把一对切向量映射为标量。分量 $b_{\alpha\beta}$ 表示坐标方向 $\alpha$、$\beta$ 这一组合上的弯曲程度。升高一个指标得到的 $b_\alpha{}^\beta$ 才是切空间到切空间的形算子分量；它出现在 $\boldsymbol n_{,\alpha}=-b_\alpha{}^\beta\boldsymbol a_\beta$ 中，描述沿方向 $\alpha$ 移动时法向怎样在切平面内转动。

正则光滑参数化满足 $\boldsymbol r_{,\alpha\beta}=\boldsymbol r_{,\beta\alpha}$，所以 $b_{\alpha\beta}=b_{\beta\alpha}$。这就是第二基本形式属于对称二阶张量的原因。

给定非零切向量 $\boldsymbol t=\boldsymbol a_\alpha\mathrm d\xi^\alpha$，对应的法曲率为

$$
\boxed{
k_n
=
\frac{
b_{\alpha\beta}\mathrm d\xi^\alpha\mathrm d\xi^\beta
}{
a_{\alpha\beta}\mathrm d\xi^\alpha\mathrm d\xi^\beta
}.
}
$$

分母是 $\boldsymbol t$ 的长度平方，分子是曲率张量在同一方向上作用两次的结果，因此这个商与参数尺度无关。改变 $\boldsymbol t$ 的方向会得到不同的法曲率，其极值是两个主曲率 $k_1,k_2$。平面满足 $k_1=k_2=0$；半径为 $R$ 的圆柱在圆周方向的曲率大小为 $1/R$、沿轴向为零；球面两个主曲率的大小均为 $1/R$。同时反转 $\boldsymbol n$ 会使 $\boldsymbol b$ 和法曲率的符号同时反转。

曲面面积元为

$$
\mathrm dA
=
\sqrt{\det(a_{\alpha\beta})}
\,\mathrm d\xi^1\mathrm d\xi^2.
$$

引入厚度坐标 $z\in[-t/2,t/2]$ 后，三维壳体的参考位置映射为

$$
\boxed{
\boldsymbol X(\xi^1,\xi^2,z)
=
\boldsymbol r(\xi^1,\xi^2)
+z\boldsymbol n(\xi^1,\xi^2).
}
$$

$t$ 是壳厚，$z=0$ 对应中面。把第三个材料坐标记为 $\xi^3=z$，三维参考构形的协变基为

$$
\boxed{
\begin{aligned}
\boldsymbol G_\alpha
&=
\boldsymbol X_{,\alpha}
=
\boldsymbol a_\alpha+z\boldsymbol n_{,\alpha}
=
(\delta_\alpha^\beta-zb_\alpha{}^\beta)
\boldsymbol a_\beta,
\\
\boldsymbol G_3
&=
\boldsymbol X_{,3}
=
\boldsymbol n.
\end{aligned}
}
$$

$\boldsymbol G_\alpha$ 随 $z$ 改变，因为曲面的平行层具有不同的面内长度尺度。圆柱外侧圆周长于中面、内侧圆周短于中面，就是 $z\boldsymbol n_{,\alpha}$ 的几何含义。初始曲率参与壳应变的各个耦合项也来自这一部分。

Reissner–Mindlin 壳运动学把每条厚度材料线作为一个整体处理：该材料线在变形后保持直线，其长度变化被忽略，但它可以转动，且不要求始终垂直于变形后的中面。描述厚度材料线方向的矢量称为厚度指向矢量（director）。

中面位移写成

$$
\boldsymbol u_0
=
u^\alpha\boldsymbol a_\alpha+w\boldsymbol n,
$$

$\boldsymbol u_0$ 是一阶空间张量，其唯一轴表示位移方向；$u^1,u^2$ 是切向位移的逆变分量，$w=\boldsymbol u_0\cdot\boldsymbol n$ 是法向位移。设小转角向量为 $\boldsymbol\theta$，厚度方向的一阶增量为

$$
\boldsymbol\beta
=
\boldsymbol\theta\times\boldsymbol n,
\qquad
\boldsymbol\beta\cdot\boldsymbol n=0.
$$

$\boldsymbol\beta$ 是切向的一阶空间张量。它的唯一轴表示厚度方向增量的空间分量，约束 $\boldsymbol\beta\cdot\boldsymbol n=0$ 使其只有两个独立分量。变形后的厚度指向矢量在线性近似下为

$$
\boldsymbol d'
\approx
\boldsymbol n+\boldsymbol\beta.
$$

参考点 $\boldsymbol X=\boldsymbol r+z\boldsymbol n$ 在变形后的位置为

$$
\boldsymbol x
=
\boldsymbol r+\boldsymbol u_0
+z(\boldsymbol n+\boldsymbol\beta).
$$

两者相减给出三维位移场

$$
\boxed{
\boldsymbol u(\xi^1,\xi^2,z)
=
\boldsymbol u_0(\xi^1,\xi^2)
+z\boldsymbol\beta(\xi^1,\xi^2).
}
$$

位移关于 $z$ 是一次函数。中面只有 $\boldsymbol u_0$；位于 $z=+t/2$ 与 $z=-t/2$ 的两个表面，其附加位移分别为 $+(t/2)\boldsymbol\beta$ 和 $-(t/2)\boldsymbol\beta$。空间中恒定的 $\boldsymbol\beta$ 本身可能只是刚体转动；真正的弯曲应变取决于 $\boldsymbol\beta$ 沿中面的变化以及它与初始曲率、$\boldsymbol u_0$ 的耦合。

在局部平面坐标 $\boldsymbol n=\boldsymbol e_z$ 下，若

$$
\boldsymbol\theta
=
\theta_x\boldsymbol e_x
+\theta_y\boldsymbol e_y
+\theta_z\boldsymbol e_z,
$$

则

$$
\boldsymbol\beta
=
\theta_y\boldsymbol e_x
-\theta_x\boldsymbol e_y.
$$

因此常见的局部分量对应关系为

$$
\beta_x=\theta_y,
\qquad
\beta_y=-\theta_x.
$$

法向转角

$$
\theta_n=\boldsymbol\theta\cdot\boldsymbol n
$$

称为钻转自由度。由于

$$
(\theta_n\boldsymbol n)\times\boldsymbol n
=
\boldsymbol0,
$$

$\theta_n$ 不改变厚度指向矢量，因而不进入标准 Reissner–Mindlin 连续应变能。若离散空间保留这一节点自由度，就需要补充一个与连续壳能量相容的离散稳定化项；其数学形式在第 4 节给出。

## 2. 壳应变与截面本构张量

令三维材料坐标为

$$
q^A=(\xi^1,\xi^2,z),
\qquad
A,B\in\{1,2,3\}.
$$

参考构形的基向量为 $\boldsymbol G_A=\boldsymbol X_{,A}$。材料线元在变形前为

$$
\mathrm d\boldsymbol X
=
\boldsymbol G_A\,\mathrm dq^A,
$$

变形后的位置和线元分别为

$$
\boldsymbol x
=
\boldsymbol X+\boldsymbol u,
\qquad
\mathrm d\boldsymbol x
=
(\boldsymbol G_A+\boldsymbol u_{,A})\,\mathrm dq^A.
$$

变形前后的度量分量为

$$
g_{AB}
=
\boldsymbol G_A\cdot\boldsymbol G_B,
\qquad
g'_{AB}
=
\boldsymbol x_{,A}\cdot\boldsymbol x_{,B}.
$$

两者之差可直接展开为

$$
\begin{aligned}
\frac12(g'_{AB}-g_{AB})
=\frac12\bigl(&
\boldsymbol G_A\cdot\boldsymbol u_{,B}
+
\boldsymbol G_B\cdot\boldsymbol u_{,A}
\\
&+
\boldsymbol u_{,A}\cdot\boldsymbol u_{,B}
\bigr).
\end{aligned}
$$

小变形理论舍去位移导数的二次项 $\boldsymbol u_{,A}\cdot\boldsymbol u_{,B}$，三维线性应变的协变分量因而为

$$
\boxed{
\varepsilon_{AB}
=
\frac12
\left(
\boldsymbol G_A\cdot\boldsymbol u_{,B}
+
\boldsymbol G_B\cdot\boldsymbol u_{,A}
\right).
}
$$

$\boldsymbol\varepsilon$ 是对称二阶张量，两个轴 $A,B$ 都接收材料线元的方向。对由 $\mathrm dq^A$ 指定的任意材料线元，其一阶相对长度变化为

$$
\frac{\delta(\mathrm ds)}{\mathrm ds}
=
\frac{
\varepsilon_{AB}\,\mathrm dq^A\mathrm dq^B
}{
g_{CD}\,\mathrm dq^C\mathrm dq^D
}.
$$

在一般曲线坐标中，应通过二次型 $\varepsilon_{AB}\,\mathrm dq^A\mathrm dq^B$ 解释应变。只有在局部正交单位基中，$\varepsilon_{AA}$ 才可直接解释为第 $A$ 个基方向的相对伸长，$2\varepsilon_{AB}$ 才是两个不同基方向之间的工程剪切应变。该定义也会自动消去刚体小转动，因为刚体转动不改变度量 $g_{AB}$。



由

$$
\boldsymbol u(\xi^1,\xi^2,z)
=
\boldsymbol u_0(\xi^1,\xi^2)
+z\boldsymbol\beta(\xi^1,\xi^2)
$$

得到

$$
\boxed{
\boldsymbol u_{,\alpha}
=
\boldsymbol u_{0,\alpha}
+z\boldsymbol\beta_{,\alpha},
\qquad
\boldsymbol u_{,3}
=
\boldsymbol\beta.
}
$$

把 $\boldsymbol G_\alpha=\boldsymbol a_\alpha+z\boldsymbol n_{,\alpha}$ 与上述位移导数代入 $\varepsilon_{\alpha\beta}$，得到

$$
\begin{aligned}
\varepsilon_{\alpha\beta}(z)
=\frac12\Big[&
(\boldsymbol a_\alpha+z\boldsymbol n_{,\alpha})
\cdot
(\boldsymbol u_{0,\beta}+z\boldsymbol\beta_{,\beta})
\\
&+
(\boldsymbol a_\beta+z\boldsymbol n_{,\beta})
\cdot
(\boldsymbol u_{0,\alpha}+z\boldsymbol\beta_{,\alpha})
\Big].
\end{aligned}
$$

按照 $z$ 的幂次收集各项可得

$$
\boxed{
\varepsilon_{\alpha\beta}(z)
=
\varepsilon^m_{\alpha\beta}
+z\kappa_{\alpha\beta}
+z^2\rho_{\alpha\beta},
}
$$

其中

$$
\boxed{
\varepsilon^m_{\alpha\beta}
=
\frac12
\left(
\boldsymbol a_\alpha\cdot\boldsymbol u_{0,\beta}
+
\boldsymbol a_\beta\cdot\boldsymbol u_{0,\alpha}
\right),
}
$$

$$
\boxed{
\begin{aligned}
\kappa_{\alpha\beta}
=\frac12\bigl(&
\boldsymbol a_\alpha\cdot\boldsymbol\beta_{,\beta}
+
\boldsymbol a_\beta\cdot\boldsymbol\beta_{,\alpha}
\\
&+
\boldsymbol n_{,\alpha}\cdot\boldsymbol u_{0,\beta}
+
\boldsymbol n_{,\beta}\cdot\boldsymbol u_{0,\alpha}
\bigr),
\end{aligned}
}
$$

以及

$$
\boxed{
\rho_{\alpha\beta}
=
\frac12
\left(
\boldsymbol n_{,\alpha}\cdot\boldsymbol\beta_{,\beta}
+
\boldsymbol n_{,\beta}\cdot\boldsymbol\beta_{,\alpha}
\right).
}
$$

位移虽关于 $z$ 线性，参考基 $\boldsymbol G_\alpha(z)$ 的厚度依赖仍使一般曲壳的 $\varepsilon_{\alpha\beta}(z)$ 含有 $z^2\rho_{\alpha\beta}$。

设 $k_1,k_2$ 是中面的主曲率。薄壳的一阶厚度理论要求

$$
\boxed{
t\max(|k_1|,|k_2|)\ll1,
}
$$

也就是壳厚远小于局部曲率半径。在这一条件下，$z\boldsymbol n_{,\alpha}$ 相对 $\boldsymbol a_\alpha$ 是小量，$z^2\rho_{\alpha\beta}$ 比保留的一次项再高一阶。舍去该项后才得到常用的厚度线性分布：

$$
\boxed{
\varepsilon_{\alpha\beta}(z)
\simeq
\varepsilon^m_{\alpha\beta}
+z\kappa_{\alpha\beta}.
}
$$

对于局部平直的壳面，$\boldsymbol n_{,\alpha}=\boldsymbol0$，故 $\rho_{\alpha\beta}=0$；在给定的 Reissner–Mindlin 位移场内，上式此时不需要再舍去曲率引起的 $z^2$ 项。

$\boldsymbol\varepsilon^m,\boldsymbol\kappa\in\operatorname{Sym}^2(T^*S)$ 都是中面余切空间上的对称二阶张量。它们各有两个切向输入轴 $\alpha,\beta$：$\varepsilon^m_{\alpha\beta}$ 是中面处对应方向组合的度量变化，$\kappa_{\alpha\beta}$ 是该面内应变沿厚度方向的一阶变化率。标量 $z$ 只缩放 $\boldsymbol\kappa$，不会增加张量轴。

膜应变还可从中面度量的变化直接识别。变形后中面的切向基与度量分别为

$$
\boldsymbol a'_\alpha
=
\boldsymbol a_\alpha+\boldsymbol u_{0,\alpha},
\qquad
a'_{\alpha\beta}
=
\boldsymbol a'_\alpha\cdot\boldsymbol a'_\beta.
$$

保留位移的一阶项便有

$$
a'_{\alpha\beta}-a_{\alpha\beta}
=
\boldsymbol a_\alpha\cdot\boldsymbol u_{0,\beta}
+
\boldsymbol a_\beta\cdot\boldsymbol u_{0,\alpha}
=
2\varepsilon^m_{\alpha\beta}.
$$

因此，$\boldsymbol\varepsilon^m$ 正是中面度量变化的一半。令

$$
u_\alpha=a_{\alpha\beta}u^\beta,
\qquad
u_{\alpha|\beta}
=
u_{\alpha,\beta}
-\Gamma^\gamma_{\alpha\beta}u_\gamma,
$$

其中竖线表示曲面协变导数。Gauss–Weingarten 关系给出

$$
\boldsymbol a_\alpha\cdot\boldsymbol u_{0,\beta}
=
u_{\alpha|\beta}-b_{\alpha\beta}w,
$$

所以

$$
\boxed{
\varepsilon^m_{\alpha\beta}
=
\frac12
\left(
u_{\alpha|\beta}
+u_{\beta|\alpha}
\right)
-b_{\alpha\beta}w,
}
$$

在局部正交单位基中，$\varepsilon^m_{11}$、$\varepsilon^m_{22}$ 是两个切向方向的正应变，$2\varepsilon^m_{12}$ 是中面内的工程剪切应变。第一项来自切向位移的对称协变梯度。第二项说明初始曲率会把法向位移转化为膜应变；平板的 $b_{\alpha\beta}=0$，而曲壳沿法向移动时即使切向分量为零，也可能改变周长和夹角。

弯曲应变是面内应变对厚度坐标的一阶导数：

$$
\kappa_{\alpha\beta}
=
\left.
\frac{\partial\varepsilon_{\alpha\beta}}
{\partial z}
\right|_{z=0}.
$$

定义 $\beta_\alpha=\boldsymbol a_\alpha\cdot\boldsymbol\beta$，再利用 Gauss–Weingarten 关系，可把前面得到的 $\kappa_{\alpha\beta}$ 展开为纯曲面分量：

$$
\boxed{
\begin{aligned}
\kappa_{\alpha\beta}
={}&
\frac12
\left(
\beta_{\alpha|\beta}
+\beta_{\beta|\alpha}
\right)
\\
&-
\frac12
\left(
b_\alpha{}^\gamma u_{\gamma|\beta}
+b_\beta{}^\gamma u_{\gamma|\alpha}
\right)
+
b_\alpha{}^\gamma b_{\gamma\beta}w.
\end{aligned}
}
$$

第一项是厚度指向矢量增量沿中面的对称梯度；第二项是初始曲率与切向位移梯度的耦合；第三项是初始曲率、法向位移与曲率再次作用形成的耦合。$\boldsymbol\kappa$ 的量纲是长度的倒数，$z\boldsymbol\kappa$ 才是某一厚度层上的无量纲附加面内应变。

在一般 Reissner–Mindlin 壳中，$\boldsymbol d'=\boldsymbol n+\boldsymbol\beta$ 是独立的厚度指向矢量，不必等于变形后中面的单位法向，因此 $\boldsymbol\kappa$ 首先是指向矢量的广义弯曲应变。令

$$
\widehat b'_{\alpha\beta}
=
-(\boldsymbol a_\alpha+\boldsymbol u_{0,\alpha})
\cdot
(\boldsymbol n+\boldsymbol\beta)_{,\beta}
$$

表示变形后切向基相对于厚度指向矢量的曲率量。其一阶变化为

$$
\delta\widehat b_{\alpha\beta}
=
\widehat b'_{\alpha\beta}-b_{\alpha\beta}
=
-\boldsymbol u_{0,\alpha}\cdot\boldsymbol n_{,\beta}
-\boldsymbol a_\alpha\cdot\boldsymbol\beta_{,\beta}.
$$

与 $\kappa_{\alpha\beta}$ 的定义比较可得

$$
\boxed{
\kappa_{\alpha\beta}
=
-\frac12
\left(
\delta\widehat b_{\alpha\beta}
+\delta\widehat b_{\beta\alpha}
\right).
}
$$

当横向剪切为零、厚度指向矢量就是变形后中面的法向时，$\widehat{\boldsymbol b}'$ 退化为变形后中面的第二基本形式 $\boldsymbol b'$，并且

$$
\boxed{
\boldsymbol\kappa
=
-(\boldsymbol b'-\boldsymbol b).
}
$$

这个负号由 $\boldsymbol X=\boldsymbol r+z\boldsymbol n$、$b_{\alpha\beta}=-\boldsymbol a_\alpha\cdot\boldsymbol n_{,\beta}$ 和 $\boldsymbol\varepsilon(z)=\boldsymbol\varepsilon^m+z\boldsymbol\kappa$ 三个定义共同确定。令 $\Delta\boldsymbol b=\boldsymbol b'-\boldsymbol b$。对任意单位切向量 $\boldsymbol\tau$，有符号关系为

$$
\Delta\boldsymbol b(\boldsymbol\tau,\boldsymbol\tau)>0
\quad\Longrightarrow\quad
\boldsymbol\kappa(\boldsymbol\tau,\boldsymbol\tau)
=
-\Delta\boldsymbol b(\boldsymbol\tau,\boldsymbol\tau)<0.
$$

因此在 $z>0$ 一侧，弯曲附加应变 $z\boldsymbol\kappa(\boldsymbol\tau,\boldsymbol\tau)$ 为负；$z<0$ 一侧则符号相反。

横向剪切来自三维应变的混合分量。由 $\boldsymbol G_3=\boldsymbol n$ 和 $\boldsymbol u_{,3}=\boldsymbol\beta$ 得

$$
\begin{aligned}
2\varepsilon_{\alpha3}
={}&
\boldsymbol G_\alpha\cdot\boldsymbol u_{,3}
+
\boldsymbol G_3\cdot\boldsymbol u_{,\alpha}
\\
={}&
(\boldsymbol a_\alpha+z\boldsymbol n_{,\alpha})
\cdot\boldsymbol\beta
+
\boldsymbol n\cdot
(\boldsymbol u_{0,\alpha}+z\boldsymbol\beta_{,\alpha})
\\
={}&
\boldsymbol a_\alpha\cdot\boldsymbol\beta
+
\boldsymbol n\cdot\boldsymbol u_{0,\alpha}
\\
&+
z\left(
\boldsymbol n_{,\alpha}\cdot\boldsymbol\beta
+
\boldsymbol n\cdot\boldsymbol\beta_{,\alpha}
\right).
\end{aligned}
$$

由于 $\boldsymbol n\cdot\boldsymbol\beta=0$，其曲面导数满足

$$
\boldsymbol n_{,\alpha}\cdot\boldsymbol\beta
+
\boldsymbol n\cdot\boldsymbol\beta_{,\alpha}
=
(\boldsymbol n\cdot\boldsymbol\beta)_{,\alpha}
=0.
$$

因此横向剪切在该一阶运动学中不随 $z$ 改变，并定义为

$$
\boxed{
\gamma_\alpha
:=
2\varepsilon_{\alpha3}
=
\boldsymbol a_\alpha\cdot\boldsymbol\beta
+
\boldsymbol n\cdot\boldsymbol u_{0,\alpha}.
}
$$

利用 $\boldsymbol u_0=u^\beta\boldsymbol a_\beta+w\boldsymbol n$ 可进一步得到

$$
\boxed{
\gamma_\alpha
=
\beta_\alpha
+w_{|\alpha}
+b_\alpha{}^\beta u_\beta.
}
$$

$\boldsymbol\gamma=\gamma_\alpha\,\mathrm d\xi^\alpha\in T^*S$ 是具有一个切向轴的协变一阶张量。利用度量升指标后，也可把它表示为切向量 $\boldsymbol\gamma^\sharp=\gamma_\alpha\boldsymbol a^\alpha\in TS$。它的几何意义可由变形后中面切向量和厚度指向矢量的内积看出：

$$
\begin{aligned}
(\boldsymbol a_\alpha+\boldsymbol u_{0,\alpha})
\cdot
(\boldsymbol n+\boldsymbol\beta)
&=
\gamma_\alpha
+O(\|\boldsymbol u\|^2).
\end{aligned}
$$

参考构形中的 $\boldsymbol a_\alpha$ 与 $\boldsymbol n$ 正交；变形后内积不再为零的线性部分就是 $\gamma_\alpha$。因此，每个元素 $\gamma_\alpha$ 测量厚度指向矢量与第 $\alpha$ 个变形后中面切向方向之间偏离直角的程度。它属于切向—厚度平面内的剪切，与中面内部由 $2\varepsilon^m_{12}$ 描述的面内剪切不同。

对于平直中面，$b_{\alpha\beta}=0$，故 $\gamma_\alpha=w_{,\alpha}+\beta_\alpha$。条件 $\gamma_\alpha=0$ 给出 $\beta_\alpha=-w_{,\alpha}$，此时厚度指向矢量始终垂直于变形后的中面，得到 Kirchhoff–Love 运动学。Reissner–Mindlin 理论把 $\boldsymbol\beta$ 保留为独立变量，因此允许 $\boldsymbol\gamma\ne\boldsymbol0$。

厚度正应变也由同一个三维应变定义得到：

$$
\boxed{
\varepsilon_{33}
=
\boldsymbol G_3\cdot\boldsymbol u_{,3}
=
\boldsymbol n\cdot\boldsymbol\beta
=0.
}
$$

它表示 Reissner–Mindlin 运动学忽略厚度材料线沿自身方向的伸长。$\varepsilon_{33}=0$ 是运动学约束；后续二维截面本构中的 $\sigma^{33}=0$ 是平面应力约化条件，两者承担不同作用。

对于局部平直壳面，$b_{\alpha\beta}=0$，协变导数退化为普通偏导数，因而

$$
\varepsilon^m_{\alpha\beta}
=
\frac12
\left(
u_{\alpha,\beta}+u_{\beta,\alpha}
\right),
$$

$$
\kappa_{\alpha\beta}
=
\frac12
\left(
\beta_{\alpha,\beta}+\beta_{\beta,\alpha}
\right),
$$

$$
\gamma_\alpha
=
w_{,\alpha}+\beta_\alpha.
$$

壳的二维本构关系由三维材料本构经过平面应力约化和厚度积分得到。记厚度位置 $z$ 处约化后的面内弹性张量为

$$
\mathbb C(z):
\operatorname{Sym}^2(T^*S)
\longrightarrow
\operatorname{Sym}^2(TS).
$$

$\mathbb C$ 有四个切向指标轴。后两个轴 $\gamma,\delta$ 接收面内应变，前两个轴 $\alpha,\beta$ 输出面内应力；元素 $C^{\alpha\beta\gamma\delta}(z)$ 表示单位 $\varepsilon_{\gamma\delta}$ 对 $\sigma^{\alpha\beta}$ 的贡献。平面应力约化要求厚度法向应力满足 $\sigma^{33}=0$，并将厚度方向材料响应消入 $\mathbb C(z)$。这里的 $\sigma^{33}=0$ 是本构约化条件，前述 $\varepsilon_{33}=0$ 是运动学条件，两者作用在不同层次。

面内应力为

$$
\sigma^{\alpha\beta}(z)
=
C^{\alpha\beta\gamma\delta}(z)
\left(
\varepsilon^m_{\gamma\delta}
+z\kappa_{\gamma\delta}
\right).
$$

$\boldsymbol\sigma_{\parallel}=\sigma^{\alpha\beta}\boldsymbol a_\alpha\otimes\boldsymbol a_\beta\in\operatorname{Sym}^2(TS)$ 是二阶面内应力张量。它的两个轴分别对应牵引方向和面内截面法向；元素 $\sigma^{\alpha\beta}(z)$ 是厚度位置 $z$ 处相应方向组合上的应力分量。

曲壳的精确参考体积元为

$$
\mathrm dV
=
\det(\boldsymbol I-z\boldsymbol b^\sharp)
\,\mathrm dA\,\mathrm dz
=
(1-2Hz+Kz^2)
\,\mathrm dA\,\mathrm dz,
$$

其中 $\boldsymbol b^\sharp$ 是升高一个指标后的形算子，$H=(k_1+k_2)/2$ 是平均曲率，$K=k_1k_2$ 是 Gauss 曲率。条件 $t\max(|k_1|,|k_2|)\ll1$ 允许采用 $\mathrm dV\simeq\mathrm dA\,\mathrm dz$。后面的截面内力和刚度积分均采用这一阶薄壳近似。

沿厚度积分应力，得到膜内力张量、弯矩张量和横向剪力向量：

$$
N^{\alpha\beta}
=
\int_{-t/2}^{t/2}
\sigma^{\alpha\beta}(z)\,\mathrm dz,
$$

$$
M^{\alpha\beta}
=
\int_{-t/2}^{t/2}
z\sigma^{\alpha\beta}(z)\,\mathrm dz,
$$

$$
Q^\alpha
=
\int_{-t/2}^{t/2}
\sigma^{\alpha3}(z)\,\mathrm dz.
$$

$\boldsymbol N,\boldsymbol M\in\operatorname{Sym}^2(TS)$ 都是二阶曲面张量，两个轴与面内应力相同。元素 $N^{\alpha\beta}$ 是单位长度截面上的膜力分量，元素 $M^{\alpha\beta}$ 是单位长度截面上的力矩分量。$\boldsymbol Q=Q^\alpha\boldsymbol a_\alpha\in TS$ 是一阶切向张量，元素 $Q^\alpha$ 是相应切向方向上的横向剪力合量。

一般截面的膜、膜弯耦合和弯曲本构张量分别定义为

$$
\mathbb A
=
\int_{-t/2}^{t/2}\mathbb C(z)\,\mathrm dz,
\qquad
\mathbb B
=
\int_{-t/2}^{t/2}z\mathbb C(z)\,\mathrm dz,
$$

$$
\mathbb D
=
\int_{-t/2}^{t/2}z^2\mathbb C(z)\,\mathrm dz.
$$

$\mathbb A,\mathbb B,\mathbb D:\operatorname{Sym}^2(T^*S)\to\operatorname{Sym}^2(TS)$ 都保持四个指标轴，轴的意义与 $\mathbb C$ 相同：前两个轴选择输出的截面内力或弯矩分量，后两个轴选择输入的膜应变或曲率分量。元素分别表示膜刚度、膜弯耦合刚度和弯曲刚度的一个方向耦合系数。

横向剪切本构张量记为

$$
\mathbb S
=
S^{\alpha\beta}
\boldsymbol a_\alpha\otimes\boldsymbol a_\beta,
\qquad
S^{\alpha\beta}
=
\int_{-t/2}^{t/2}
c_s C_s^{\alpha\beta}(z)\,\mathrm dz,
$$

$C_s^{\alpha\beta}(z)$ 是厚度位置 $z$ 处约化横向剪切刚度的分量，$c_s$ 是剪切修正系数。对各向同性材料，$C_s^{\alpha\beta}=\mu a^{\alpha\beta}$，其中 $\mu$ 是剪切模量。$\mathbb S:T^*S\to TS$ 是二阶线性映射，其分量由上式的 $S^{\alpha\beta}$ 给出；第一轴 $\alpha$ 表示输出剪力方向，第二轴 $\beta$ 表示输入剪切应变方向。

膜—弯耦合映射的伴随 $\mathbb B^*$ 由功共轭关系定义：

$$
(\mathbb B:\boldsymbol\kappa):\boldsymbol\varepsilon^m
=
(\mathbb B^*:\boldsymbol\varepsilon^m):\boldsymbol\kappa.
$$

其分量满足

$$
(B^*)^{\alpha\beta\gamma\delta}
=
B^{\gamma\delta\alpha\beta}.
$$

当材料张量具有主对称性 $C^{\alpha\beta\gamma\delta}=C^{\gamma\delta\alpha\beta}$ 时，厚度积分保持这一互易性，因而 $\mathbb B^*=\mathbb B$。保留伴随记号可以清楚显示输入空间与输出空间的交换。截面本构统一写成

$$
\boxed{
\begin{aligned}
\boldsymbol N
&=
\mathbb A:\boldsymbol\varepsilon^m
+\mathbb B:\boldsymbol\kappa,\\
\boldsymbol M
&=
\mathbb B^*:\boldsymbol\varepsilon^m
+\mathbb D:\boldsymbol\kappa,\\
\boldsymbol Q
&=
\mathbb S\boldsymbol\gamma.
\end{aligned}
}
$$

逐个元素写成

$$
\begin{aligned}
N^{\alpha\beta}
&=
A^{\alpha\beta\gamma\delta}
\varepsilon^m_{\gamma\delta}
+
B^{\alpha\beta\gamma\delta}
\kappa_{\gamma\delta},\\
M^{\alpha\beta}
&=
(B^*)^{\alpha\beta\gamma\delta}
\varepsilon^m_{\gamma\delta}
+
D^{\alpha\beta\gamma\delta}
\kappa_{\gamma\delta},\\
Q^\alpha
&=
S^{\alpha\beta}\gamma_\beta.
\end{aligned}
$$

重复的指标是被收缩的输入轴。在前两式中，自由指标 $\alpha,\beta$ 构成两个输出轴；在最后一式中，只有 $\alpha$ 是自由指标。由自由指标的数量可以直接判断：前两式输出二阶张量，最后一式输出一阶张量。

冒号表示张量缩并。例如

$$
\boldsymbol\varepsilon^m:
\mathbb A:
\boldsymbol\varepsilon^m
=
\varepsilon^m_{\alpha\beta}
A^{\alpha\beta\gamma\delta}
\varepsilon^m_{\gamma\delta}.
$$

对于各向同性平面应力材料，记 $E$ 为杨氏模量、$\nu$ 为泊松比，约化后的四阶面内弹性张量为

$$
\boxed{
C^{\alpha\beta\gamma\delta}
=
\frac{E}{1-\nu^2}
\left[
\nu a^{\alpha\beta}a^{\gamma\delta}
+
\frac{1-\nu}{2}
\left(
a^{\alpha\gamma}a^{\beta\delta}
+a^{\alpha\delta}a^{\beta\gamma}
\right)
\right].
}
$$

若材料沿厚度均匀，且参考面位于中性面，则 $\mathbb C(z)=\mathbb C$，并有

$$
\boxed{
\mathbb A=t\mathbb C,
\qquad
\mathbb B=\boldsymbol0,
\qquad
\mathbb D=\frac{t^3}{12}\mathbb C,
}
$$

$$
\boxed{
S^{\alpha\beta}
=
c_s\mu t\,a^{\alpha\beta},
\qquad
\mu=\frac{E}{2(1+\nu)}.
}
$$

这些厚度尺度直接来自

$$
\int_{-t/2}^{t/2}1\,\mathrm dz=t,
\qquad
\int_{-t/2}^{t/2}z\,\mathrm dz=0,
\qquad
\int_{-t/2}^{t/2}z^2\,\mathrm dz=\frac{t^3}{12}.
$$

所以膜刚度与横向剪切刚度按 $t$ 缩放，弯曲刚度按 $t^3$ 缩放；关于中性面对称的截面没有膜—弯耦合。三组应变与截面合力的功共轭关系为

$$
\delta w_{\mathrm{int}}
=
\boldsymbol N:\delta\boldsymbol\varepsilon^m
+
\boldsymbol M:\delta\boldsymbol\kappa
+
\boldsymbol Q\cdot\delta\boldsymbol\gamma.
$$

前述推导和本构关系可归纳为：

| 应变量 | 从三维应变中提取 | 张量空间与指标轴 | 每个分量的几何意义 | 一阶理论中的厚度分布 | 功共轭截面量 |
|---|---|---|---|---|---|
| 膜应变 $\boldsymbol\varepsilon^m$ | $\varepsilon^m_{\alpha\beta}=\varepsilon_{\alpha\beta}(0)$ | $\operatorname{Sym}^2(T^*S)$；两个切向轴 $\alpha,\beta$ | 中面在方向 $\alpha,\beta$ 上的度量变化的一半，即长度与夹角变化 | $z$ 的常数项 | 膜力 $\boldsymbol N$ |
| 弯曲应变 $\boldsymbol\kappa$ | $\kappa_{\alpha\beta}=\left.\partial_z\varepsilon_{\alpha\beta}\right|_{z=0}$ | $\operatorname{Sym}^2(T^*S)$；两个切向轴 $\alpha,\beta$ | 厚度指向矢量沿中面的相对转动变化；在 Kirchhoff–Love 极限下等于中面曲率变化的负值 | 以 $z\boldsymbol\kappa$ 进入面内应变，上下两侧符号相反 | 弯矩 $\boldsymbol M$ |
| 横向剪切 $\boldsymbol\gamma$ | $\gamma_\alpha=2\varepsilon_{\alpha3}$ | $T^*S$；一个切向轴 $\alpha$ | 厚度指向矢量与变形后第 $\alpha$ 个中面切向方向偏离正交的程度 | Reissner–Mindlin 一阶运动学中沿厚度不变 | 横向剪力 $\boldsymbol Q$ |

## 3. 总势能与壳的弱形式

中面位移和厚度指向矢量的转动组成基本未知场

$$
\boldsymbol y
=
(\boldsymbol u_0,\boldsymbol\beta),
$$

相应的虚位移场为

$$
\delta\boldsymbol y
=
(\boldsymbol v_0,\boldsymbol\eta).
$$

$\boldsymbol y$ 和 $\delta\boldsymbol y$ 是位移场空间与厚度指向矢量增量空间的乘积空间元素。两个分量属于不同物理空间，广义场本身不视为二阶物理张量。

壳的弹性应变能为

$$
\begin{aligned}
U(\boldsymbol y)
=\frac12\int_S
\bigl[&
\boldsymbol\varepsilon^m:
\mathbb A:
\boldsymbol\varepsilon^m
+2\boldsymbol\varepsilon^m:
\mathbb B:
\boldsymbol\kappa\\
&+
\boldsymbol\kappa:
\mathbb D:
\boldsymbol\kappa
+
\boldsymbol\gamma\cdot
\mathbb S\boldsymbol\gamma
\bigr]\,\mathrm dA.
\end{aligned}
$$

$U(\boldsymbol y)$ 是零阶张量，也就是标量。式中的每个二阶应变张量先与四阶本构张量收缩两个轴，再与另一个二阶张量收缩剩余两个轴，所以所有方向轴都被消去，最终得到单位面积应变能密度。

设 $\boldsymbol f_S$ 是单位中面面积上的分布力，$\bar{\boldsymbol t}$ 是边界线上的给定力，$p$ 是沿参考法向施加的压力，$\boldsymbol c_S$ 是单位面积上的物理分布偶力，并令 $\partial S_N$ 表示给定边界力的自然边界部分。由于 $\boldsymbol\eta=\delta\boldsymbol\beta=\delta\boldsymbol\theta\times\boldsymbol n$，切向虚转角满足 $\delta\boldsymbol\theta=\boldsymbol n\times\boldsymbol\eta$。定义与 $\boldsymbol\beta$ 功共轭的广义载荷

$$
\boldsymbol m_\beta
:=
\boldsymbol c_S\times\boldsymbol n,
$$

便有 $\boldsymbol c_S\cdot\delta\boldsymbol\theta=\boldsymbol m_\beta\cdot\boldsymbol\eta$。在线性静力分析中，外载荷泛函写成

$$
\begin{aligned}
\ell(\delta\boldsymbol y)
=
&\int_S
\boldsymbol f_S\cdot\boldsymbol v_0
\,\mathrm dA
+
\int_{\partial S_N}
\bar{\boldsymbol t}\cdot\boldsymbol v_0
\,\mathrm ds\\
&+
\int_S
p\boldsymbol n\cdot\boldsymbol v_0
\,\mathrm dA
+
\int_S
\boldsymbol m_\beta\cdot\boldsymbol\eta
\,\mathrm dA.
\end{aligned}
$$

$\boldsymbol f_S,\bar{\boldsymbol t},\boldsymbol c_S,\boldsymbol m_\beta$ 都是一阶张量，唯一轴表示力或偶力方向；$p$ 是零阶标量。它们与相应虚位移或虚转角作点积后，方向轴被收缩，因而 $\ell(\delta\boldsymbol y)$ 也是标量。法向钻转偶力与离散钻转角功共轭，应在保留该离散自由度时另行加入。

线性静力中的压力方向取参考构形法向。若压力始终跟随变形后的法向，则载荷关于位移非线性，不再属于线性小变形模型。

总势能为

$$
\Pi(\boldsymbol y)
=
U(\boldsymbol y)-\ell(\boldsymbol y).
$$

对允许的虚位移求一阶变分，得到双线性型

$$
\begin{aligned}
a(\boldsymbol y,\delta\boldsymbol y)
=\int_S
\bigl[&
\boldsymbol\varepsilon^m(\delta\boldsymbol y):
\mathbb A:
\boldsymbol\varepsilon^m(\boldsymbol y)\\
&+
\boldsymbol\varepsilon^m(\delta\boldsymbol y):
\mathbb B:
\boldsymbol\kappa(\boldsymbol y)\\
&+
\boldsymbol\kappa(\delta\boldsymbol y):
\mathbb B^*:
\boldsymbol\varepsilon^m(\boldsymbol y)\\
&+
\boldsymbol\kappa(\delta\boldsymbol y):
\mathbb D:
\boldsymbol\kappa(\boldsymbol y)\\
&+
\boldsymbol\gamma(\delta\boldsymbol y)
\cdot\mathbb S
\boldsymbol\gamma(\boldsymbol y)
\bigr]\,\mathrm dA.
\end{aligned}
$$

$a(\boldsymbol y,\delta\boldsymbol y)$ 是取两个广义场并返回标量的双线性映射。第一个函数槽对应试探解，第二个函数槽对应允许的虚位移；离散后，这两个函数槽分别成为刚度矩阵的列轴和行轴。

定义满足本质边界条件的试探空间 $\mathcal U$ 和满足齐次本质边界条件的测试空间 $\mathcal V$。壳的弱形式为：求

$$
\boldsymbol y\in\mathcal U,
$$

使得

$$
\boxed{
a(\boldsymbol y,\delta\boldsymbol y)
=
\ell(\delta\boldsymbol y),
\qquad
\forall\delta\boldsymbol y\in\mathcal V.
}
$$

该弱形式同时包含膜、弯曲和横向剪切贡献。钻转角不属于连续未知场 $\boldsymbol y=(\boldsymbol u_0,\boldsymbol\beta)$；当离散空间另外保留节点钻转自由度时，可把面内位移与钻转角组成的离散自由度记为 $\boldsymbol q_m$，相应的附加双线性型记为

$$
a_{\mathrm{drill}}(\boldsymbol q_m,\delta\boldsymbol q_m),
$$

它作用于离散节点运动中的面内位移与钻转角，其具体形式在 4.4 节中由钻转残差的二次能量定义。

## 4. 四节点壳单元的离散表达

在参考四边形

$$
(\xi,\eta)\in[-1,1]^2
$$

上，四个双线性形函数为

$$
\begin{aligned}
N_1&=\frac14(1-\xi)(1-\eta),&
N_2&=\frac14(1+\xi)(1-\eta),\\
N_3&=\frac14(1+\xi)(1+\eta),&
N_4&=\frac14(1-\xi)(1+\eta).
\end{aligned}
$$

$N_a(\xi,\eta)$ 对固定节点标签 $a$ 是零阶标量场。四个形函数组成四元组 $(N_1,N_2,N_3,N_4)$，离散指标 $a$ 选择相应节点形函数；$N_a$ 是节点 $a$ 对当前点场值的插值权重。

选定单元代表平面，在该平面上取一点 $\boldsymbol X_c$ 以及局部正交基 $(\boldsymbol e_x,\boldsymbol e_y,\boldsymbol e_z)$，其中 $\boldsymbol e_z$ 是平面法向，记为 $\boldsymbol n_h:=\boldsymbol e_z$。材料中面节点 $\boldsymbol X_{\mathrm{mat},a}$ 在代表平面上的投影坐标为

$$
x_a
=
(\boldsymbol X_{\mathrm{mat},a}-\boldsymbol X_c)
\cdot\boldsymbol e_x,
\qquad
y_a
=
(\boldsymbol X_{\mathrm{mat},a}-\boldsymbol X_c)
\cdot\boldsymbol e_y.
$$

代表平面内的等参映射为

$$
\boxed{
\boldsymbol r_h(\xi,\eta)
=
\boldsymbol X_c
+x_h(\xi,\eta)\boldsymbol e_x
+y_h(\xi,\eta)\boldsymbol e_y,
}
$$

其中

$$
x_h=\sum_{a=1}^4N_ax_a,
\qquad
y_h=\sum_{a=1}^4N_ay_a.
$$

$\boldsymbol X_{\mathrm{mat},a}$ 是材料中面上第 $a$ 个几何节点的位置，离散积分区域定义为投影四边形 $S_e=\boldsymbol r_h([-1,1]^2)$。若四个节点共面，这一投影精确保持其几何；若节点不共面，本节采用代表平面离散，并由 4.5 节的能量一致映射换算节点运动。运动自由度位于另一参考面时，$\boldsymbol X_{\mathrm{mat},a}$ 与参考节点位置之间的关系也由 4.5 节的有符号偏置给出。

因此，下列形函数梯度与面积积分均定义在代表平面上。真实三维双线性曲面离散需要采用 $3\times2$ 曲面 Jacobian、面积因子 $\|\boldsymbol r_{h,\xi}\times\boldsymbol r_{h,\eta}\|$ 和点态曲面基，属于另一种几何离散。代表平面等参映射的 $2\times2$ Jacobian 给出形函数对局部物理坐标 $x,y$ 的导数：

$$
\begin{bmatrix}
N_{a,x}\\
N_{a,y}
\end{bmatrix}
=
\begin{bmatrix}
x_{h,\xi}&y_{h,\xi}\\
x_{h,\eta}&y_{h,\eta}
\end{bmatrix}^{-1}
\begin{bmatrix}
N_{a,\xi}\\
N_{a,\eta}
\end{bmatrix}.
$$

左侧的第一轴选择求导方向 $x$ 或 $y$，节点标签 $a$ 则选择被求导的形函数。后续所有应变矩阵都由 $N_a$、$N_{a,x}$ 和 $N_{a,y}$ 组成。

位移和物理转角采用同一组形函数插值。这里先显式标出“运动学局部量”的上标：

$$
\boldsymbol u_{0,h}
=
\sum_{a=1}^4N_a\boldsymbol u_a^{\mathrm{kin}},
\qquad
\boldsymbol\theta_h
=
\sum_{a=1}^4N_a\boldsymbol\theta_a^{\mathrm{kin}}.
$$

厚度指向矢量的增量由

$$
\boldsymbol\beta_h
=
\boldsymbol\theta_h\times\boldsymbol n_h
$$

得到。将节点运动变换到单元局部基，并完成偏置等局部运动学换算后，每个节点进入应变插值的广义自由度记为

$$
\boldsymbol q_a^{\mathrm{kin}}
=
\begin{bmatrix}
\boldsymbol u_a^{\mathrm{kin}}\\
\boldsymbol\theta_a^{\mathrm{kin}}
\end{bmatrix}
\in\mathbb R^6,
$$

四节点单元向量为

$$
\boldsymbol q_e^{\mathrm{kin}}
=
\begin{bmatrix}
\boldsymbol q_1^{\mathrm{kin}}\\
\boldsymbol q_2^{\mathrm{kin}}\\
\boldsymbol q_3^{\mathrm{kin}}\\
\boldsymbol q_4^{\mathrm{kin}}
\end{bmatrix}
\in\mathbb R^{24}.
$$

$\boldsymbol q_a^{\mathrm{kin}}$ 和 $\boldsymbol q_e^{\mathrm{kin}}$ 都是一阶广义张量，也就是自由度向量。$\boldsymbol q_a^{\mathrm{kin}}$ 的唯一轴有六个取值，依次表示三个平移方向和三个转角方向；元素 $(q_a^{\mathrm{kin}})_s$ 是节点 $a$ 在运动学局部坐标中的第 $s$ 个广义位移。$\boldsymbol q_e^{\mathrm{kin}}$ 的唯一轴是组合指标

$$
I=(a,s),
\qquad
a\in\{1,2,3,4\},
\quad
s\in\{u_x,u_y,u_z,\theta_x,\theta_y,\theta_z\},
$$

所以该轴共有 $4\times6=24$ 个取值。节点编号和物理分量可看成两个独立指标轴，也可合并为一个组合自由度轴；两种表示都对应同一个 $24$ 维单元空间。

为使局部应变矩阵保持简洁，本节以下把 $\boldsymbol u_a^{\mathrm{kin}}$、$\boldsymbol\theta_a^{\mathrm{kin}}$ 的分量简写为 $u_a,v_a,w_a,\theta_{x,a},\theta_{y,a},\theta_{n,a}$。这些符号始终指运动学局部量。根据不同的变形机制，可把每个节点的六个局部自由度分成两个三分量块：

$$
\boldsymbol q_{m,a}
=
\begin{bmatrix}
u_a&v_a&\theta_{n,a}
\end{bmatrix}^{\mathsf T},
\qquad
\boldsymbol q_{p,a}
=
\begin{bmatrix}
w_a&\theta_{x,a}&\theta_{y,a}
\end{bmatrix}^{\mathsf T}.
$$

四个节点分别组成 $\boldsymbol q_m,\boldsymbol q_p\in\mathbb R^{12}$。引入布尔选择映射 $\mathbf P_m,\mathbf P_p\in\mathbb R^{12\times24}$，使

$$
\boldsymbol q_m
=
\mathbf P_m\boldsymbol q_e^{\mathrm{kin}},
\qquad
\boldsymbol q_p
=
\mathbf P_p\boldsymbol q_e^{\mathrm{kin}}.
$$

$\boldsymbol q_m$ 承载膜变形和钻转稳定化，$\boldsymbol q_p$ 承载板弯曲和横向剪切。两组自由度互不重叠且覆盖全部局部自由度，因而

$$
\boldsymbol q_e^{\mathrm{kin}}
=
\mathbf P_m^{\mathsf T}\boldsymbol q_m
+
\mathbf P_p^{\mathsf T}\boldsymbol q_p.
$$

这个分块只是同一个单元自由度空间的重排，不改变节点运动学。

### 4.1 协调膜应变与内部非协调应变

连续层面的 $\boldsymbol\varepsilon^m$ 是对称二阶曲面张量。选定局部正交基后，它的独立分量编排为工程应变列向量：

$$
\underline{\varepsilon}^{m}
:=
\mathcal V_\varepsilon(\boldsymbol\varepsilon^m)
=
\begin{bmatrix}
\varepsilon^m_{xx}\\
\varepsilon^m_{yy}\\
2\varepsilon^m_{xy}
\end{bmatrix}
\in\mathbb R^3.
$$

下划线明确表示“张量在已选基中的坐标列阵”，从符号上区分三分量数组与几何二阶张量。相应的功共轭膜力列阵取为

$$
\underline N
=
\begin{bmatrix}
N^{xx}\\
N^{yy}\\
N^{xy}
\end{bmatrix},
$$

于是

$$
\boldsymbol N:\boldsymbol\varepsilon^m
=
\underline N^{\mathsf T}
\underline{\varepsilon}^{m}.
$$

工程剪应变列阵的第三项含系数 $2$，功共轭内力列阵的第三项不含系数 $2$，正是为了保持这一功等价关系。

更一般地，若截面线性映射 $\mathbb L$ 把对称应变张量 $\boldsymbol e$ 映射为与之功共轭的截面力张量 $\boldsymbol R$，则记

$$
\underline R
=
[\mathbb L]_{\mathrm V}\,
\underline e,
\qquad
\boldsymbol R:\boldsymbol e
=
\underline R^{\mathsf T}
\underline e.
$$

$[\mathbb L]_{\mathrm V}$ 由所选工程应变坐标及其功共轭截面力坐标共同诱导。剪切应变坐标中的系数 $2$ 必须计入这一矩阵表示，后文的 $\mathbf A_{\mathrm V}$、$\mathbf B_{\mathrm V}$ 和 $\mathbf D_{\mathrm V}$ 均按此定义。

四节点协调位移插值给出

$$
\underline{\varepsilon}^{m}_{\mathrm{comp}}
=
\mathbf B_m\boldsymbol q_m,
\qquad
\mathbf B_m
=
\begin{bmatrix}
\mathbf B_{m,1}&
\mathbf B_{m,2}&
\mathbf B_{m,3}&
\mathbf B_{m,4}
\end{bmatrix},
$$

其中每个节点对应的 $3\times3$ 块为

$$
\boxed{
\mathbf B_{m,a}
=
\begin{bmatrix}
N_{a,x}&0&0\\
0&N_{a,y}&0\\
N_{a,y}&N_{a,x}&0
\end{bmatrix}.
}
$$

行轴依次是 $\varepsilon^m_{xx}$、$\varepsilon^m_{yy}$、$2\varepsilon^m_{xy}$，列轴依次是节点 $a$ 的 $u_a$、$v_a$、$\theta_{n,a}$。第三列为零，表明连续膜应变不含钻转角；钻转角的离散稳定化在 4.4 节单独形成。

为了扩充四节点双线性位移插值可以表示的膜应变空间，再引入四个单元内部参数

$$
\boldsymbol\zeta
=
\begin{bmatrix}
\zeta_{1x}&\zeta_{1y}&\zeta_{2x}&\zeta_{2y}
\end{bmatrix}^{\mathsf T}
\in\mathbb R^4.
$$

参考单元上的两个内部标量模式可取为

$$
M_1(\xi,\eta)=1-\xi^2,
\qquad
M_2(\xi,\eta)=1-\eta^2.
$$

这里 $r\in\{1,2\}$ 选择标量模式 $M_r$，$c\in\{x,y\}$ 选择该模式的面内幅值方向；四维内部参数轴可用组合指标 $\ell=(r,c)$ 表示。

记第 $g$ 个积分点的局部几何 Jacobian 及其行列式为

$$
\mathbf J_g
=
\begin{bmatrix}
x_{h,\xi}&y_{h,\xi}\\
x_{h,\eta}&y_{h,\eta}
\end{bmatrix}_g,
\qquad
j_g=\det\mathbf J_g,
$$

并记单元中心处的相应量为 $\mathbf J_0,j_0$。内部模式在物理坐标中的导数定义为

$$
\boxed{
\begin{bmatrix}
d_{rx}\\
d_{ry}
\end{bmatrix}_g
=
\frac{j_0}{j_g}
\mathbf J_0^{-1}
\begin{bmatrix}
M_{r,\xi}\\
M_{r,\eta}
\end{bmatrix}_g,
\qquad r\in\{1,2\}.
}
$$

对仿射四边形，$\mathbf J_g=\mathbf J_0$，上式退化为普通的梯度坐标变换。行列式比值 $j_0/j_g$ 使内部应变模式在一般等参映射下随面积尺度变化。由这些导数组成非协调应变矩阵

$$
\boxed{
\mathbf B_{\mathrm{inc}}
=
\begin{bmatrix}
d_{1x}&0&d_{2x}&0\\
0&d_{1y}&0&d_{2y}\\
d_{1y}&d_{1x}&d_{2y}&d_{2x}
\end{bmatrix}
\in\mathbb R^{3\times4}.
}
$$

完整膜应变列阵为

$$
\boxed{
\underline{\varepsilon}^{m}
=
\mathbf B_m\boldsymbol q_m
+
\mathbf B_{\mathrm{inc}}\boldsymbol\zeta.
}
$$

$\mathbf B_m:\mathbb R^{12}\to\mathbb R^3$ 把节点膜自由度映射为协调应变；$\mathbf B_{\mathrm{inc}}:\mathbb R^4\to\mathbb R^3$ 把内部参数映射为附加应变。前者由相邻单元共享的节点位移决定，后者只存在于当前单元内部。$\boldsymbol\zeta$ 不参加跨单元连续性条件，也没有总体自由度编号，而是在单元形成阶段通过静态凝聚消去。

$\mathbf B_m$ 和 $\mathbf B_{\mathrm{inc}}$ 在 $2\times2$ Gauss 积分的四个积分点上取值；中心点的几何量用于定义内部模式的坐标变换和钻转残差。

### 4.2 板弯曲与 MITC4 横向剪切

在局部正交基中，弯曲应变张量和横向剪切协向量的坐标列阵定义为

$$
\underline\kappa
=
\begin{bmatrix}
\kappa_{xx}\\
\kappa_{yy}\\
2\kappa_{xy}
\end{bmatrix},
\qquad
\underline\gamma
=
\begin{bmatrix}
\gamma_x\\
\gamma_y
\end{bmatrix}.
$$

对局部平直壳面，有 $\beta_x=\theta_y$、$\beta_y=-\theta_x$。连续弯曲应变公式 $\boldsymbol\kappa=\operatorname{sym}(\boldsymbol\beta\otimes\nabla_S)$ 因而给出

$$
\underline\kappa
=
\mathbf B_\kappa\boldsymbol q_p,
\qquad
\mathbf B_\kappa
=
\begin{bmatrix}
\mathbf B_{\kappa,1}&
\mathbf B_{\kappa,2}&
\mathbf B_{\kappa,3}&
\mathbf B_{\kappa,4}
\end{bmatrix},
$$

$$
\boxed{
\mathbf B_{\kappa,a}
=
\begin{bmatrix}
0&0&N_{a,x}\\
0&-N_{a,y}&0\\
0&-N_{a,x}&N_{a,y}
\end{bmatrix}.
}
$$

每个块的三列依次作用于 $(w_a,\theta_{x,a},\theta_{y,a})$。第一行形成 $\theta_{y,x}$，第二行形成 $-\theta_{x,y}$，第三行形成工程扭曲率 $\theta_{y,y}-\theta_{x,x}$。

若直接插值横向剪切，$\gamma_x=w_{,x}+\theta_y$、$\gamma_y=w_{,y}-\theta_x$ 给出每节点的原始剪切块

$$
\boxed{
\mathbf B_{\gamma,a}^{\mathrm{raw}}
=
\begin{bmatrix}
N_{a,x}&0&N_a\\
N_{a,y}&-N_a&0
\end{bmatrix}.
}
$$

四个节点块组装成完整映射

$$
\boxed{
\mathbf B_\gamma^{\mathrm{raw}}
=
\begin{bmatrix}
\mathbf B_{\gamma,1}^{\mathrm{raw}}&
\mathbf B_{\gamma,2}^{\mathrm{raw}}&
\mathbf B_{\gamma,3}^{\mathrm{raw}}&
\mathbf B_{\gamma,4}^{\mathrm{raw}}
\end{bmatrix},
\qquad
\underline\gamma^{\mathrm{raw}}
=
\mathbf B_\gamma^{\mathrm{raw}}\boldsymbol q_p.
}
$$

$\mathbf B_{\gamma,a}^{\mathrm{raw}}\in\mathbb R^{2\times3}$ 只作用于节点 $a$ 的三个板块自由度，而 $\mathbf B_\gamma^{\mathrm{raw}}\in\mathbb R^{2\times12}$ 作用于完整的 $\boldsymbol q_p$。四节点双线性 $w$ 与转角插值在薄壳极限下通常不能同时充分满足 $\underline\gamma=\boldsymbol0$，原始剪切场会产生本不应存在的正剪切能，单元因而表现得过硬，这就是剪切锁死。

MITC4 不在所有积分点直接使用 $\mathbf B_\gamma^{\mathrm{raw}}$。在参考单元四条边的中点

$$
A=(0,-1),\quad
B=(1,0),\quad
C=(0,1),\quad
D=(-1,0)
$$

提取与相应边相切的自然坐标剪切分量，再在单元内部插值：

$$
\begin{bmatrix}
\gamma_\xi^{\mathrm{raw}}\\
\gamma_\eta^{\mathrm{raw}}
\end{bmatrix}
=
\mathbf J
\begin{bmatrix}
\gamma_x^{\mathrm{raw}}\\
\gamma_y^{\mathrm{raw}}
\end{bmatrix},
\qquad
\mathbf J
=
\begin{bmatrix}
x_{h,\xi}&y_{h,\xi}\\
x_{h,\eta}&y_{h,\eta}
\end{bmatrix}.
$$

$$
\gamma_\xi^A
:=
\gamma_\xi^{\mathrm{raw}}(0,-1),
\qquad
\gamma_\xi^C
:=
\gamma_\xi^{\mathrm{raw}}(0,1),
$$

$$
\gamma_\eta^B
:=
\gamma_\eta^{\mathrm{raw}}(1,0),
\qquad
\gamma_\eta^D
:=
\gamma_\eta^{\mathrm{raw}}(-1,0).
$$

$$
\boxed{
\begin{aligned}
\widetilde\gamma_\xi(\xi,\eta)
&=
\frac12
\left[
(1-\eta)\gamma_\xi^A
+(1+\eta)\gamma_\xi^C
\right],
\\
\widetilde\gamma_\eta(\xi,\eta)
&=
\frac12
\left[
(1+\xi)\gamma_\eta^B
+(1-\xi)\gamma_\eta^D
\right].
\end{aligned}
}
$$

在任意评价点，假定自然分量再变换回局部物理分量：

$$
\underline\gamma^{\mathrm{MITC4}}
=
\mathbf J^{-1}
\begin{bmatrix}
\widetilde\gamma_\xi\\
\widetilde\gamma_\eta
\end{bmatrix}.
$$

整个过程是一个从原始剪切场到 MITC4 假定剪切场的线性投影，可写成

$$
\underline\gamma^{\mathrm{MITC4}}
=
\Pi_{\mathrm{MITC4}}
\underline\gamma^{\mathrm{raw}}
=
\mathbf B_s^{\mathrm{MITC4}}
\boldsymbol q_p.
$$

$\mathbf B_s^{\mathrm{MITC4}}\in\mathbb R^{2\times12}$ 的行轴选择局部横向剪切分量 $c\in\{x,y\}$，列轴选择板块自由度；元素 $(B_s^{\mathrm{MITC4}})_{cI}$ 表示第 $I$ 个单位板块自由度对第 $c$ 个假定剪切分量的贡献。连续运动学仍是 Reissner–Mindlin 理论，MITC4 只替换离散剪切应变空间，使其在薄壳极限下能更准确地接近 $\boldsymbol\gamma=\boldsymbol0$。

### 4.3 内部变量的静态凝聚

将第 2 节的截面张量写成局部工程分量的矩阵：

$$
[\mathbb A]_{\mathrm V}=\mathbf A_{\mathrm V},
\qquad
[\mathbb B]_{\mathrm V}=\mathbf B_{\mathrm V},
\qquad
[\mathbb D]_{\mathrm V}=\mathbf D_{\mathrm V},
\qquad
[\mathbb S]_{\mathrm V}=\mathbf S_{\mathrm V}.
$$

$\mathbf A_{\mathrm V},\mathbf B_{\mathrm V},\mathbf D_{\mathrm V}\in\mathbb R^{3\times3}$ 分别是膜、膜—弯耦合和弯曲刚度矩阵，$\mathbf S_{\mathrm V}\in\mathbb R^{2\times2}$ 是横向剪切刚度矩阵。离散截面能因而为

$$
\begin{aligned}
U_e(\boldsymbol q_m,\boldsymbol q_p,\boldsymbol\zeta)
=\frac12\int_{S_e}
\Big[&
\left(\underline\varepsilon^{m}\right)^{\mathsf T}
\mathbf A_{\mathrm V}
\underline\varepsilon^{m}
+2
\left(\underline\varepsilon^{m}\right)^{\mathsf T}
\mathbf B_{\mathrm V}
\underline\kappa
\\
&+
\underline\kappa^{\mathsf T}
\mathbf D_{\mathrm V}
\underline\kappa
+
(\underline\gamma^{\mathrm{MITC4}})^{\mathsf T}
\mathbf S_{\mathrm V}
\underline\gamma^{\mathrm{MITC4}}
\Big]\,\mathrm dA,
\end{aligned}
$$

其中

$$
\underline\varepsilon^{m}
=
\mathbf B_m\boldsymbol q_m
+\mathbf B_{\mathrm{inc}}\boldsymbol\zeta,
\quad
\underline\kappa
=
\mathbf B_\kappa\boldsymbol q_p,
\quad
\underline\gamma^{\mathrm{MITC4}}
=
\mathbf B_s^{\mathrm{MITC4}}\boldsymbol q_p.
$$

在积分点 $g$ 记 $J_g:=|j_g|>0$ 为面积 Jacobian，$\omega_g$ 为 Gauss 权重。展开上式中含 $\boldsymbol q_m$ 和 $\boldsymbol\zeta$ 的二次项，得到

$$
\begin{aligned}
\mathbf K_{mm}^{0}
&=
\sum_g
\mathbf B_{m,g}^{\mathsf T}
\mathbf A_{\mathrm V,g}
\mathbf B_{m,g}
J_g\omega_g,\\
\mathbf K_{m\zeta}
&=
\sum_g
\mathbf B_{m,g}^{\mathsf T}
\mathbf A_{\mathrm V,g}
\mathbf B_{\mathrm{inc},g}
J_g\omega_g,\\
\mathbf K_{\zeta\zeta}
&=
\sum_g
\mathbf B_{\mathrm{inc},g}^{\mathsf T}
\mathbf A_{\mathrm V,g}
\mathbf B_{\mathrm{inc},g}
J_g\omega_g.
\end{aligned}
$$

下标 $m$ 表示 12 个膜块节点自由度，下标 $\zeta$ 表示 4 个单元内部参数。三个矩阵的形状依次为 $12\times12$、$12\times4$ 和 $4\times4$。例如 $(K_{m\zeta})_{I\ell}$ 表示第 $\ell=(r,c)$ 个内部幅值与第 $I$ 个膜节点测试自由度之间的能量耦合。

膜—弯耦合能还会使板块自由度与内部参数发生耦合。定义

$$
\mathbf K_{p\zeta}
=
\sum_g
\mathbf B_{\kappa,g}^{\mathsf T}
\mathbf B_{\mathrm V,g}^{\mathsf T}
\mathbf B_{\mathrm{inc},g}
J_g\omega_g,
$$

以及未经凝聚的膜—板交叉块

$$
\mathbf K_{mp}^{0}
=
\sum_g
\mathbf B_{m,g}^{\mathsf T}
\mathbf B_{\mathrm V,g}
\mathbf B_{\kappa,g}
J_g\omega_g.
$$

$\mathbf K_{mp}^{0}$ 是未经凝聚的膜—板交叉块。由于单元能量是标量，其 Hessian 的混合块互为转置：

$$
\mathbf K_{pm}^{0}
=
(\mathbf K_{mp}^{0})^{\mathsf T}
=
\sum_g
\mathbf B_{\kappa,g}^{\mathsf T}
\mathbf B_{\mathrm V,g}^{\mathsf T}
\mathbf B_{m,g}
J_g\omega_g.
$$

若弹性截面还满足材料互易性，则 $\mathbf B_{\mathrm V}^{\mathsf T}=\mathbf B_{\mathrm V}$。

未凝聚的板块刚度为

$$
\mathbf K_{pp}^{0}
=
\sum_g
\left[
\mathbf B_{\kappa,g}^{\mathsf T}
\mathbf D_{\mathrm V,g}
\mathbf B_{\kappa,g}
+
(\mathbf B_{s,g}^{\mathrm{MITC4}})^{\mathsf T}
\mathbf S_{\mathrm V,g}
\mathbf B_{s,g}^{\mathrm{MITC4}}
\right]J_g\omega_g.
$$

把内部参数与两个节点自由度块的耦合合并为

$$
\mathbf K_{q\zeta}
:=
\begin{bmatrix}
\mathbf K_{m\zeta}\\
\mathbf K_{p\zeta}
\end{bmatrix},
\qquad
\mathbf K_{\zeta q}
=
\mathbf K_{q\zeta}^{\mathsf T}.
$$

于是未凝聚节点刚度可写成

$$
\boxed{
\mathbf K_{qq}^{0}
=
\begin{bmatrix}
\mathbf K_{mm}^{0}&\mathbf K_{mp}^{0}\\
(\mathbf K_{mp}^{0})^{\mathsf T}&\mathbf K_{pp}^{0}
\end{bmatrix}.
}
$$

把两个节点自由度块合并为

$$
\widehat{\boldsymbol q}_e
=
\begin{bmatrix}
\boldsymbol q_m\\
\boldsymbol q_p
\end{bmatrix},
$$

则含内部参数的单元能量可整理为

$$
\begin{aligned}
U_e(\widehat{\boldsymbol q}_e,\boldsymbol\zeta)
={}&
\frac12
\widehat{\boldsymbol q}_e^{\mathsf T}
\mathbf K_{qq}^{0}
\widehat{\boldsymbol q}_e
\\
&+
\widehat{\boldsymbol q}_e^{\mathsf T}
\mathbf K_{q\zeta}
\boldsymbol\zeta
+
\frac12
\boldsymbol\zeta^{\mathsf T}
\mathbf K_{\zeta\zeta}
\boldsymbol\zeta.
\end{aligned}
$$

内部参数没有外载荷与之功共轭。对 $\boldsymbol\zeta$ 求驻值给出

$$
\frac{\partial U_e}{\partial\boldsymbol\zeta}
=
\mathbf K_{\zeta q}\widehat{\boldsymbol q}_e
+
\mathbf K_{\zeta\zeta}\boldsymbol\zeta
=
\boldsymbol0.
$$

若 $\mathbf K_{\zeta\zeta}$ 可逆，则

$$
\boxed{
\boldsymbol\zeta
=
-\mathbf K_{\zeta\zeta}^{-1}
\left(
\mathbf K_{m\zeta}^{\mathsf T}\boldsymbol q_m
+
\mathbf K_{p\zeta}^{\mathsf T}\boldsymbol q_p
\right).
}
$$

将这个局部最优内部参数代回 $U_e$，得到只含节点自由度的凝聚能量

$$
U_e^{\mathrm{cond}}(\widehat{\boldsymbol q}_e)
=
\frac12
\widehat{\boldsymbol q}_e^{\mathsf T}
\mathbf K_{qq}^{\mathrm{cond}}
\widehat{\boldsymbol q}_e,
$$

其中

$$
\boxed{
\mathbf K_{qq}^{\mathrm{cond}}
=
\mathbf K_{qq}^{0}
-
\mathbf K_{q\zeta}
\mathbf K_{\zeta\zeta}^{-1}
\mathbf K_{\zeta q}.
}
$$

这就是 Schur 补。静态凝聚在固定节点运动 $\widehat{\boldsymbol q}_e$ 时先求出使单元势能驻定的 $\boldsymbol\zeta$，再把该值精确代回，因而属于单元内部的精确消元。按膜块和板块展开为

$$
\boxed{
\begin{aligned}
\mathbf K_{mm}
&=
\mathbf K_{mm}^{0}
-\mathbf K_{m\zeta}\mathbf K_{\zeta\zeta}^{-1}
\mathbf K_{m\zeta}^{\mathsf T},\\
\mathbf K_{pp}
&=
\mathbf K_{pp}^{0}
-\mathbf K_{p\zeta}\mathbf K_{\zeta\zeta}^{-1}
\mathbf K_{p\zeta}^{\mathsf T},\\
\mathbf K_{mp}
&=
\mathbf K_{mp}^{0}
-\mathbf K_{m\zeta}\mathbf K_{\zeta\zeta}^{-1}
\mathbf K_{p\zeta}^{\mathsf T}.
\end{aligned}
}
$$

$\mathbf K_{pp}^{0}$ 的第一项是板弯曲能，第二项是 MITC4 横向剪切能。$\mathbf K_{p\zeta}=\boldsymbol0$ 时，内部参数只受膜自由度驱动；该矩阵只有在膜—弯耦合存在时才非零。静态凝聚只修正节点刚度，不增加总体未知量；每个单元仍只通过 24 个节点自由度参与总体装配。

### 4.4 钻转稳定化与完整局部刚度

钻转角 $\theta_n$ 不进入标准 Reissner–Mindlin 连续应变能。为使节点钻转与面内位移场的小转动保持一致，可选取以下两个残差构成一种离散稳定化。第一个残差为

$$
\boxed{
d_1
=
\frac12
\left(v_{,x}-u_{,y}\right)_{(0,0)}
-
\frac14\sum_{a=1}^4\theta_{n,a}
=
\mathbf B_{\mathrm{dr}}^{(1)}\boldsymbol q_m.
}
$$

第一项是单元中心面内位移场的小转角，第二项是四个节点钻转角的平均值。$d_1$ 约束二者保持一致。第二个残差为

$$
\boxed{
d_2
=
\frac14
(\theta_{n,1}-\theta_{n,2}+\theta_{n,3}-\theta_{n,4})
=
\mathbf B_{\mathrm{dr}}^{(2)}\boldsymbol q_m,
}
$$

它控制四节点钻转角的交替符号模式。两个行矩阵 $\mathbf B_{\mathrm{dr}}^{(1)},\mathbf B_{\mathrm{dr}}^{(2)}\in\mathbb R^{1\times12}$ 的行轴只有一个残差分量，列轴是 12 个膜块自由度。

相应的离散稳定化能量为

$$
U_{\mathrm{drill}}
=
\frac12c_1d_1^2
+
\frac12c_2d_2^2.
$$

对 $\boldsymbol q_m$ 求两次导数得到钻转刚度

$$
\boxed{
\mathbf K_{\mathrm{drill}}
=
c_1
(\mathbf B_{\mathrm{dr}}^{(1)})^{\mathsf T}
\mathbf B_{\mathrm{dr}}^{(1)}
+
c_2
(\mathbf B_{\mathrm{dr}}^{(2)})^{\mathsf T}
\mathbf B_{\mathrm{dr}}^{(2)}.
}
$$

对任意 $\boldsymbol q_m$，都有

$$
\boldsymbol q_m^{\mathsf T}
(\mathbf B_{\mathrm{dr}}^{(r)})^{\mathsf T}
\mathbf B_{\mathrm{dr}}^{(r)}
\boldsymbol q_m
=
\left\|
\mathbf B_{\mathrm{dr}}^{(r)}\boldsymbol q_m
\right\|^2
\ge0,
$$

所以两个稳定化项均为半正定，只对相应的钻转残差增加能量。它们不会产生负刚度，但系数过大时会人为抬高本应柔软的转动响应。因此 $c_1,c_2\ge0$ 应按膜剪切刚度和单元几何尺度选取，使钻转零能模式被排除，同时稳定化能量不主导物理的膜与弯曲响应。$c_1,c_2$ 属于离散稳定化参数，与连续模型的材料常数分属不同层次。

在前述膜块、板块连续排列的坐标 $\widehat{\boldsymbol q}_e$ 中，对应的块刚度为

$$
\boxed{
\widehat{\mathbf K}_e
=
\begin{bmatrix}
\mathbf K_{mm}+\mathbf K_{\mathrm{drill}}
&
\mathbf K_{mp}\\
\mathbf K_{mp}^{\mathsf T}
&
\mathbf K_{pp}
\end{bmatrix}.
}
$$

节点交错的运动学向量 $\boldsymbol q_e^{\mathrm{kin}}$ 与块向量之间存在一个置换矩阵 $\mathbf P_{mp}$：

$$
\widehat{\boldsymbol q}_e
=
\mathbf P_{mp}\boldsymbol q_e^{\mathrm{kin}}.
$$

令

$$
\mathbf P_{mp}
=
\begin{bmatrix}
\mathbf P_m\\
\mathbf P_p
\end{bmatrix},
$$

则节点交错顺序下的刚度为

$$
\boxed{
\mathbf K_e^{\mathrm{kin}}
=
\mathbf P_{mp}^{\mathsf T}
\widehat{\mathbf K}_e
\mathbf P_{mp}.
}
$$

$\mathbf K_e^{\mathrm{kin}}\in\mathbb R^{24\times24}$ 是从单元运动学自由度空间到其对偶力空间的线性映射。第一轴 $I$ 选择输出广义内力或测试自由度，第二轴 $J$ 选择输入位移或试探自由度；元素

$$
(K_e^{\mathrm{kin}})_{IJ}
=
a_e^h(\boldsymbol\Phi_J,\boldsymbol\Phi_I)
$$

其中 $\boldsymbol\Phi_I$ 是第 $I$ 个运动学自由度对应的离散基函数，$a_e^h$ 是凝聚后的完整离散单元双线性型：它以第 3 节的连续能量为基础，并包含假定剪切投影、非协调膜模式的 Schur 补和钻转稳定化。因此 $(K_e^{\mathrm{kin}})_{IJ}$ 表示第 $J$ 个单元自由度产生单位位移时，在第 $I$ 个自由度上形成的广义内力。

矩阵 $\mathbf B_m$、$\mathbf B_\kappa$ 和 $\mathbf B_s^{\mathrm{MITC4}}$ 从节点自由度块映射到相应应变空间；它们的转置是功共轭映射，把截面内力分量送回节点广义力空间。$\mathbf B_{\mathrm{inc}}$ 的输入空间是四维内部参数空间，所以 $\mathbf B_{\mathrm{inc}}^{\mathsf T}$ 输出与 $\boldsymbol\zeta$ 功共轭的内部残量；内部驻值条件要求该残量为零。

从单元能量中可辨认出五类贡献：膜变形、板弯曲、MITC4 横向剪切、钻转稳定化以及膜—弯耦合。

非协调内部模式通过 Schur 补同时修正膜块、板块和膜—板交叉块，膜—弯耦合位于两个自由度块之间的非对角位置，因此这些贡献在矩阵表示中通过共同的 Schur 补和非对角块发生耦合。

截面性质通过 $\mathbf A_{\mathrm V},\mathbf B_{\mathrm V},\mathbf D_{\mathrm V},\mathbf S_{\mathrm V}$ 进入单元能量。对均匀且关于中性面对称的截面，膜、弯曲和横向剪切刚度分别按 $t$、$t^3$ 和 $t$ 缩放，而 $\mathbf B_{\mathrm V}=\boldsymbol0$。对非对称分层截面，厚度积分一般给出 $\mathbf B_{\mathrm V}\ne\boldsymbol0$，因而产生膜—弯耦合。当厚度、材料性质或材料方向随中面位置变化时，这四个截面刚度矩阵也是积分点位置的函数，并必须在同一局部基中表示后才能收缩与累加。

### 4.5 偏置与节点坐标变换

若同一物理位移在两个自由度坐标中的关系为

$$
\boldsymbol q_{\mathrm{old}}
=
\mathbf H\boldsymbol q_{\mathrm{new}},
$$

则二次能量满足

$$
\frac12\boldsymbol q_{\mathrm{old}}^{\mathsf T}
\mathbf K_{\mathrm{old}}
\boldsymbol q_{\mathrm{old}}
=
\frac12\boldsymbol q_{\mathrm{new}}^{\mathsf T}
\underbrace{
(\mathbf H^{\mathsf T}\mathbf K_{\mathrm{old}}\mathbf H)
}_{\mathbf K_{\mathrm{new}}}
\boldsymbol q_{\mathrm{new}}.
$$

刚度变换必须同时作用在测试轴和试探轴上，这就是合同变换。参考面偏置和非共面节点所引起的自由度换算也遵守同一能量关系。

第 1–4 节以材料中面为 $z=0$。若离散节点所在的参考面与材料中面不重合，定义有符号偏置 $z_{\mathrm{off}}$ 使

$$
\boldsymbol X_{\mathrm{mat}}
=
\boldsymbol X_{\mathrm{node}}
+z_{\mathrm{off}}\boldsymbol n_h,
$$

其中 $\boldsymbol n_h$ 是代表单元平面的单位法向，正号沿 $\boldsymbol n_h$ 方向。节点参考面的运动记为 $(\boldsymbol u_{\mathrm{ref}},\boldsymbol\theta)$，应变插值所需的材料中面运动为

$$
\boldsymbol u_{\mathrm{mat}}
=
\boldsymbol u_{\mathrm{ref}}
+z_{\mathrm{off}}\boldsymbol\beta
=
\boldsymbol u_{\mathrm{ref}}
+z_{\mathrm{off}}(\boldsymbol\theta\times\boldsymbol n_h).
$$

定义叉乘矩阵 $[\boldsymbol n_h]_\times\boldsymbol v=\boldsymbol n_h\times\boldsymbol v$。由于 $\boldsymbol\theta\times\boldsymbol n_h=-[\boldsymbol n_h]_\times\boldsymbol\theta$，偏置前后的节点运动满足

$$
\boxed{
\underbrace{
\begin{bmatrix}
\boldsymbol u_{\mathrm{mat}}\\
\boldsymbol\theta
\end{bmatrix}
}_{\boldsymbol q_{\mathrm{mat}}}
=
\underbrace{
\begin{bmatrix}
\boldsymbol I&-z_{\mathrm{off}}[\boldsymbol n_h]_\times\\
\boldsymbol0&\boldsymbol I
\end{bmatrix}
}_{\mathbf T_{\mathrm{off}}}
\underbrace{
\begin{bmatrix}
\boldsymbol u_{\mathrm{ref}}\\
\boldsymbol\theta
\end{bmatrix}
}_{\boldsymbol q_{\mathrm{surf}}}
.
}
$$

因而 $\mathbf T_{\mathrm{off}}$ 明确地把节点参考面运动映射为材料中面运动，其平移—转角非对角块与 $z_{\mathrm{off}}$ 成正比。对四个不严格共面的节点，可先选定代表单元中面的局部基，再构造保持刚体运动的局部运动学映射。所有这类映射都通过 $\mathbf H^{\mathsf T}\mathbf K\mathbf H$ 保持内能不变。

把全局节点分量旋转到单元代表平面的局部基所用的正交映射记为 $\mathbf O_e$，把偏置及非共面节点引起的局部运动学换算合并记为 $\mathbf H_e$；二者均属于 $\mathbb R^{24\times24}$。其中 $\mathbf H_e$ 的偏置块采用上式的 $\mathbf T_{\mathrm{off}}$，因而映射方向是“节点参考面运动到材料中面运动”。三种单元自由度之间的关系为

$$
\boxed{
\boldsymbol q_e^{\mathrm{loc}}
=
\mathbf O_e\boldsymbol q_e^{\mathrm{node}},
\qquad
\boldsymbol q_e^{\mathrm{kin}}
=
\mathbf H_e\boldsymbol q_e^{\mathrm{loc}}.
}
$$

这里 $\boldsymbol q_e^{\mathrm{node}}$ 是全局节点坐标中的自由度，$\boldsymbol q_e^{\mathrm{loc}}$ 是只完成坐标旋转后的局部参考面自由度，$\boldsymbol q_e^{\mathrm{kin}}$ 才是进入 $\mathbf P_m$、$\mathbf P_p$ 及各应变矩阵的运动学自由度。定义总映射

$$
\boxed{
\mathbf T_e
=
\mathbf H_e\mathbf O_e,
\qquad
\boldsymbol q_e^{\mathrm{kin}}
=
\mathbf T_e\boldsymbol q_e^{\mathrm{node}}.
}
$$

由虚功不变性，力和刚度的逐层变换为

$$
\boxed{
\boldsymbol f_e^{\mathrm{loc}}
=
\mathbf H_e^{\mathsf T}\boldsymbol f_e^{\mathrm{kin}},
\qquad
\boldsymbol f_e^{\mathrm{node}}
=
\mathbf O_e^{\mathsf T}\boldsymbol f_e^{\mathrm{loc}}
=
\mathbf T_e^{\mathsf T}\boldsymbol f_e^{\mathrm{kin}},
}
$$

$$
\boxed{
\mathbf K_e^{\mathrm{loc}}
=
\mathbf H_e^{\mathsf T}\mathbf K_e^{\mathrm{kin}}\mathbf H_e,
\qquad
\mathbf K_e^{\mathrm{node}}
=
\mathbf O_e^{\mathsf T}\mathbf K_e^{\mathrm{loc}}\mathbf O_e
=
\mathbf T_e^{\mathsf T}\mathbf K_e^{\mathrm{kin}}\mathbf T_e.
}
$$

$\mathbf T_e\in\mathbb R^{24\times24}$ 的行轴选择运动学自由度，列轴选择全局节点自由度；元素 $(T_e)_{IJ}$ 表示第 $J$ 个单位全局节点自由度对第 $I$ 个运动学分量的贡献。刚度形成和结果恢复必须使用同一个 $\mathbf H_e$ 与 $\mathbf O_e$。

## 5. 载荷与线性运动约束

压力 $p$ 在单元中面上的虚功为

$$
\delta W_p^e
=
\int_{S_e}
p\boldsymbol n_h\cdot\delta\boldsymbol u_{0,h}
\,\mathrm dA.
$$

$p$ 是零阶张量，$\boldsymbol n_h$ 是一阶空间张量，因此 $p\boldsymbol n_h$ 是一阶面力向量。它的唯一轴表示力的空间方向，元素 $p(n_h)_i$ 是单位面积上沿方向 $i$ 的力。

令 $\boldsymbol N_u^{\mathrm{kin}}:\mathbb R^{24}\to\mathbb R^3$ 表示从运动学自由度到材料中面位移的插值映射：

$$
\boldsymbol u_{0,h}
=
\boldsymbol N_u^{\mathrm{kin}}
\boldsymbol q_e^{\mathrm{kin}}.
$$

其行轴 $i$ 是位移的空间方向，列轴 $I$ 是 24 个运动学自由度；平移自由度对应形函数系数，转角列在纯中面位移插值中为零。代入虚功后，先得到运动学坐标中的等效载荷，再映射到节点坐标：

$$
\boxed{
\boldsymbol f_e^{p,\mathrm{kin}}
=
\int_{S_e}
(\boldsymbol N_u^{\mathrm{kin}})^*
(p\boldsymbol n_h)
\,\mathrm dA,
\qquad
\boldsymbol f_e^{p,\mathrm{node}}
=
\mathbf T_e^{\mathsf T}
\boldsymbol f_e^{p,\mathrm{kin}}.
}
$$

等价地，定义直接从节点坐标到中面位移的映射

$$
\boldsymbol N_u^{\mathrm{node}}
=
\boldsymbol N_u^{\mathrm{kin}}\mathbf T_e,
$$

便有

$$
\boldsymbol f_e^{p,\mathrm{node}}
=
\int_{S_e}
(\boldsymbol N_u^{\mathrm{node}})^*
(p\boldsymbol n_h)
\,\mathrm dA.
$$

伴随映射把连续面力转换到相应自由度的对偶空间。$\boldsymbol f_e^{p,\mathrm{node}}$ 的唯一轴是单元节点自由度 $I$；元素 $(f_e^{p,\mathrm{node}})_I$ 是与虚位移 $\delta q_{e,I}^{\mathrm{node}}$ 功共轭的等效节点力或力矩。偏置存在时，$\mathbf T_e^{\mathsf T}$ 会把材料中面上的压力合力同时转换为节点参考面上的力与力矩。将压力与其他单元载荷贡献相加，得到总单元节点载荷 $\boldsymbol f_e^{\mathrm{node}}$。

设 $N_n$ 为节点总数，节点 $a$ 在全局节点坐标中的广义自由度记为

$$
\boldsymbol q_a^{\mathrm{node}}
=
\begin{bmatrix}
\boldsymbol u_a^{\mathrm{node}}\\
\boldsymbol\theta_a^{\mathrm{node}}
\end{bmatrix}.
$$

这里 $\boldsymbol u_a^{\mathrm{node}}$ 与 $\boldsymbol\theta_a^{\mathrm{node}}$ 分别是节点参考面上的全局平移和全局转角；它们经过 $\mathbf O_e$ 与 $\mathbf H_e$ 后才成为应变插值所用的 $\boldsymbol u_a^{\mathrm{kin}}$ 与 $\boldsymbol\theta_a^{\mathrm{kin}}$。所有节点自由度组装成全局向量

$$
\boldsymbol U
=
\begin{bmatrix}
\boldsymbol q_1^{\mathrm{node}}\\
\boldsymbol q_2^{\mathrm{node}}\\
\vdots\\
\boldsymbol q_{N_n}^{\mathrm{node}}
\end{bmatrix}.
$$

$\boldsymbol U$ 是一阶全局自由度张量。它的唯一轴是全局自由度编号 $I$，该编号组合了节点、平移或转动类型以及空间方向；元素 $U_I$ 是对应自由度的广义位移。与之对偶的 $\boldsymbol f$ 也只有一个全局自由度轴，元素 $f_I$ 是与 $U_I$ 功共轭的广义力。

单点本质约束、刚体从属约束和加权运动插值约束均可写成统一的线性关系

$$
\boxed{
\boldsymbol C\boldsymbol U
=
\boldsymbol g.
}
$$

设共有 $m$ 条标量约束和 $n$ 个完整自由度，则 $\boldsymbol C\in\mathbb R^{m\times n}$ 是二阶代数张量。第一轴 $r$ 选择约束方程，第二轴 $I$ 选择全局自由度；元素 $C_{rI}$ 表示自由度 $U_I$ 对第 $r$ 条约束左端的系数：

$$
C_{rI}U_I=g_r.
$$

$\boldsymbol g\in\mathbb R^m$ 是一阶约束值张量，唯一轴 $r$ 与约束方程对应，元素 $g_r$ 是第 $r$ 条约束的规定值。约束必须相容，即至少存在一个 $\boldsymbol U$ 满足该方程。重复约束虽然不改变允许位移空间，却会使拉格朗日乘子不唯一；因此应先提取约束矩阵的线性无关行，再构造满列秩的零空间基 $\boldsymbol T$。

对于单点本质约束，设 $\bar{\boldsymbol u}_a^{\mathrm{node}}$、$\bar{\boldsymbol\theta}_a^{\mathrm{node}}$ 是给定的节点平移和转角，$\boldsymbol P_u$ 和 $\boldsymbol P_\theta$ 是选择受约束方向的投影张量，则节点约束写成

$$
\boldsymbol P_u
(\boldsymbol u_a^{\mathrm{node}}-\bar{\boldsymbol u}_a^{\mathrm{node}})
=
\boldsymbol0,
$$

$$
\boldsymbol P_\theta
(\boldsymbol\theta_a^{\mathrm{node}}-\bar{\boldsymbol\theta}_a^{\mathrm{node}})
=
\boldsymbol0.
$$

当节点的三个平移分量和三个转角分量全部被固定时，对应的投影为

$$
\boldsymbol P_u=\boldsymbol I,
\qquad
\boldsymbol P_\theta=\boldsymbol I,
\qquad
\boldsymbol q_a^{\mathrm{node}}
=
\begin{bmatrix}
\bar{\boldsymbol u}_a^{\mathrm{node}}\\
\bar{\boldsymbol\theta}_a^{\mathrm{node}}
\end{bmatrix}.
$$

齐次固定条件 $\bar{\boldsymbol u}_a^{\mathrm{node}}=\bar{\boldsymbol\theta}_a^{\mathrm{node}}=\boldsymbol0$ 才进一步给出 $\boldsymbol q_a^{\mathrm{node}}=\boldsymbol0$。

$\boldsymbol P_u$ 和 $\boldsymbol P_\theta$ 都是二阶选择张量。第一轴是输出的约束分量，第二轴是节点输入分量；元素等于 1 时选中相应方向，等于 0 时将其忽略。

刚体从属约束规定从属节点相对独立节点只能作刚体运动。设

$$
\boldsymbol d_a
=
\boldsymbol X_a^{\mathrm{node}}-\boldsymbol X_0^{\mathrm{node}}
$$

是参考构形中从独立节点指向从属节点的固定臂向量，小转动关系为

$$
\boldsymbol u_a^{\mathrm{node}}
=
\boldsymbol u_0^{\mathrm{node}}
+
\boldsymbol\theta_0^{\mathrm{node}}\times\boldsymbol d_a,
\qquad
\boldsymbol\theta_a^{\mathrm{node}}
=
\boldsymbol\theta_0^{\mathrm{node}}.
$$

定义交叉乘积张量 $[\boldsymbol d_a]_\times$，满足

$$
[\boldsymbol d_a]_\times\boldsymbol v
=
\boldsymbol d_a\times\boldsymbol v.
$$

$[\boldsymbol d_a]_\times$ 是 $3\times3$ 的二阶张量。第一轴是叉积结果的输出方向，第二轴是输入向量 $\boldsymbol v$ 的方向；元素 $([\boldsymbol d_a]_\times)_{ij}$ 表示单位输入 $v_j$ 对输出分量 $(\boldsymbol d_a\times\boldsymbol v)_i$ 的贡献。

采用 Levi–Civita 符号可写成

$$
(\boldsymbol d_a\times\boldsymbol v)_i
=
\varepsilon_{ijk}(d_a)_jv_k,
\qquad
([\boldsymbol d_a]_\times)_{ik}
=
\varepsilon_{ijk}(d_a)_j.
$$

重复指标 $j$ 被收缩，自由指标 $i$ 是输出方向，$k$ 是线性映射的输入方向。

于是

$$
\boxed{
\boldsymbol q_a^{\mathrm{node}}
=
\underbrace{
\begin{bmatrix}
\boldsymbol I&-[\boldsymbol d_a]_\times\\
\boldsymbol0&\boldsymbol I
\end{bmatrix}
}_{\boldsymbol H_a}
\boldsymbol q_0^{\mathrm{node}}.
}
$$

$\boldsymbol H_a$ 是 $6\times6$ 的二阶运动变换张量。第一轴选择从属节点的输出自由度，第二轴选择独立节点的输入自由度；元素 $(H_a)_{IJ}$ 表示独立节点单位运动 $(q_0^{\mathrm{node}})_J$ 在从属节点分量 $(q_a^{\mathrm{node}})_I$ 中产生的刚体运动。

若只约束从属节点的部分自由度，则用选择矩阵 $\boldsymbol S_a$ 写成

$$
\boldsymbol S_a
(\boldsymbol q_a^{\mathrm{node}}-\boldsymbol H_a\boldsymbol q_0^{\mathrm{node}})
=
\boldsymbol0.
$$

$\boldsymbol S_a$ 是二阶选择张量，第一轴列出需要执行的约束分量，第二轴列出从属节点的六个自由度；每一行从括号内的运动差中选出一个必须为零的分量。

这种约束通过删除节点组的相对变形模式限制允许运动空间，其结构刚化效应来自运动学约束。

加权运动插值约束将参考节点运动表示为若干独立节点运动的线性组合：

$$
\boxed{
\boldsymbol q_{\mathrm{ref}}^{\mathrm{node}}
=
\sum_{a=1}^{N_R}
\mathbf R_a\boldsymbol q_a^{\mathrm{node}}.
}
$$

$\mathbf R_a$ 是二阶运动插值张量。第一轴选择参考节点的输出自由度，第二轴选择独立节点 $a$ 的输入自由度；元素 $(R_a)_{IJ}$ 表示 $(q_a^{\mathrm{node}})_J$ 对参考运动 $(q_{\mathrm{ref}}^{\mathrm{node}})_I$ 的加权贡献。它包含权重、分量选择、坐标变换及平移—转动耦合。只保留同一方向的标量平移，并且不涉及力矩平衡引起的几何耦合时，上式退化为

$$
u_{\mathrm{ref}}^{\mathrm{node}}
=
\frac{\sum_{a=1}^{N_R}\omega_au_a^{\mathrm{node}}}
{\sum_{a=1}^{N_R}\omega_a}.
$$

这里 $N_R$ 是参与插值的独立节点数，$\omega_a>0$ 是相应的标量权重；它与节点法向位移分量 $w_a$ 是不同的量。

这类约束的载荷分配由虚功相容性确定。参考节点广义力 $\boldsymbol f_{\mathrm{ref}}$ 所作虚功为

$$
\delta W
=
\boldsymbol f_{\mathrm{ref}}^{\mathsf T}
\delta\boldsymbol q_{\mathrm{ref}}^{\mathrm{node}}.
$$

代入运动插值可得

$$
\delta W
=
\sum_a
\left(
\mathbf R_a^{\mathsf T}
\boldsymbol f_{\mathrm{ref}}
\right)^{\mathsf T}
\delta\boldsymbol q_a^{\mathrm{node}},
$$

所以第 $a$ 个独立节点接收的广义载荷为

$$
\boxed{
\boldsymbol f_a
=
\mathbf R_a^{\mathsf T}
\boldsymbol f_{\mathrm{ref}}.
}
$$

运动通过 $\mathbf R_a$ 插值，广义力通过其伴随 $\mathbf R_a^{\mathsf T}$ 传递。这一对偶关系保证加权运动插值约束满足虚功相容性。

对统一约束 $\boldsymbol C\boldsymbol U=\boldsymbol g$，取一个约束特解 $\boldsymbol U_0$ 和齐次约束空间的一组基 $\boldsymbol T$：

$$
\boldsymbol C\boldsymbol U_0
=
\boldsymbol g,
\qquad
\boldsymbol C\boldsymbol T
=
\boldsymbol0.
$$

全部允许位移可以写成

$$
\boxed{
\boldsymbol U
=
\boldsymbol T\boldsymbol q
+
\boldsymbol U_0,
}
$$

若完整自由度数为 $n$、独立自由度数为 $n_r$，则 $\boldsymbol T\in\mathbb R^{n\times n_r}$ 是二阶约束变换张量。第一轴 $I$ 是输出的完整自由度，第二轴 $A$ 是输入的独立自由度；元素 $T_{IA}$ 表示单位独立位移 $q_A$ 对完整位移 $U_I$ 的贡献。$\boldsymbol U_0$ 是一阶完整自由度张量，元素给出非齐次约束的一个特解。

$\boldsymbol q$ 只包含独立自由度。$\boldsymbol T$ 同时编码单点本质约束、刚体从属约束、加权运动插值约束及其他线性多点约束，并满足

$$
C_{rI}T_{IA}=0.
$$

## 6. 总体方程、约束消元与结果恢复

### 6.1 约束消元与能量一致性

设 $\boldsymbol L_e\in\mathbb R^{24\times n}$ 从全局自由度提取第 $e$ 个单元的节点坐标自由度：$\boldsymbol q_e^{\mathrm{node}}=\boldsymbol L_e\boldsymbol U$。它是二阶布尔映射：第一轴 $p$ 是单元节点坐标自由度，第二轴 $I$ 是全局自由度；元素 $(L_e)_{pI}=1$ 表示单元自由度 $p$ 对应全局自由度 $I$，其余元素为零。未约束总体刚度和载荷由节点坐标中的单元贡献装配：

$$
\boldsymbol K
=
\sum_e
\boldsymbol L_e^{\mathsf T}
\mathbf K_e^{\mathrm{node}}
\boldsymbol L_e,
$$

$$
\boldsymbol f
=
\sum_e
\boldsymbol L_e^{\mathsf T}
\boldsymbol f_e^{\mathrm{node}}
+
\boldsymbol f_{\mathrm{nodal}}.
$$

$\boldsymbol f_{\mathrm{nodal}}$ 表示不经过单元区域积分、直接施加在总体自由度上的集中节点广义载荷。

$\boldsymbol K\in\mathbb R^{n\times n}$ 是二阶总体刚度张量。第一轴 $I$ 是输出广义内力或测试自由度，第二轴 $J$ 是输入位移或试探自由度；元素 $K_{IJ}$ 表示单位位移 $U_J$ 在平衡方程 $I$ 上产生的内力。$\boldsymbol f\in\mathbb R^n$ 是一阶载荷张量，轴 $I$ 与总体平衡方程对应，元素 $f_I$ 是该自由度上的外部广义力。

装配公式展开为指标形式就是

$$
K_{IJ}
=
\sum_e
(L_e)_{pI}
(K_e^{\mathrm{node}})_{pq}
(L_e)_{qJ},
$$

$$
f_I
=
\sum_e
(L_e)_{pI}(f_e^{\mathrm{node}})_p
+(f_{\mathrm{nodal}})_I.
$$

局部自由度轴 $p,q$ 在乘积中被收缩，保留下来的全局轴 $I,J$ 构成总体矩阵的两个轴。

离散总势能为

$$
\Pi(\boldsymbol U)
=
\frac12
\boldsymbol U^{\mathsf T}
\boldsymbol K
\boldsymbol U
-
\boldsymbol U^{\mathsf T}
\boldsymbol f.
$$

约束后的离散试探空间与齐次测试空间分别为

$$
\mathcal U_g
=
\left\{
\boldsymbol U:\boldsymbol C\boldsymbol U=\boldsymbol g
\right\},
\qquad
\mathcal V_0
=
\ker(\boldsymbol C).
$$

因此，受约束的离散弱形式为：求 $\boldsymbol U\in\mathcal U_g$，使

$$
\boxed{
\delta\boldsymbol U^{\mathsf T}
(\boldsymbol K\boldsymbol U-\boldsymbol f)
=0,
\qquad
\forall\delta\boldsymbol U\in\mathcal V_0.
}
$$

这个式子表达了约束静力平衡的本质：平衡残差与全部允许虚位移正交。

代入

$$
\boldsymbol U
=
\boldsymbol T\boldsymbol q+\boldsymbol U_0
$$

并对独立自由度 $\boldsymbol q$ 求驻值，得到约化系统

$$
\boxed{
\bar{\boldsymbol K}\boldsymbol q
=
\bar{\boldsymbol f},
}
$$

其中

$$
\boxed{
\bar{\boldsymbol K}
=
\boldsymbol T^{\mathsf T}
\boldsymbol K
\boldsymbol T,
}
$$

$$
\boxed{
\bar{\boldsymbol f}
=
\boldsymbol T^{\mathsf T}
\left(
\boldsymbol f-
\boldsymbol K\boldsymbol U_0
\right).
}
$$

$\bar{\boldsymbol K}\in\mathbb R^{n_r\times n_r}$ 是独立自由度空间中的二阶刚度张量。它的第一轴 $A$ 是独立测试自由度，第二轴 $B$ 是独立试探自由度：

$$
\bar K_{AB}
=
T_{IA}K_{IJ}T_{JB}.
$$

$\bar{\boldsymbol f}\in\mathbb R^{n_r}$ 是一阶约化载荷张量，其元素为

$$
\bar f_A
=
T_{IA}
\left(
f_I-K_{IJ}(U_0)_J
\right).
$$

完整自由度轴 $I,J$ 均被收缩，最终只保留独立自由度轴。这个指标式直接说明了约束变换为何同时作用在刚度矩阵的两个轴上。

若全部线性约束均为齐次约束，即 $\boldsymbol g=\boldsymbol0$，则可取 $\boldsymbol U_0=\boldsymbol0$。此时

$$
\bar{\boldsymbol f}
=
\boldsymbol T^{\mathsf T}\boldsymbol f.
$$

约束消元保持刚度的能量结构：

$$
\boldsymbol q^{\mathsf T}
\bar{\boldsymbol K}
\boldsymbol q
=
(\boldsymbol T\boldsymbol q)^{\mathsf T}
\boldsymbol K
(\boldsymbol T\boldsymbol q).
$$

若 $\boldsymbol K$ 为半正定矩阵，则 $\bar{\boldsymbol K}$ 仍为半正定。约化矩阵正定的条件是允许位移空间中不再保留未受约束的零能模式：

$$
\boxed{
\mathcal R(\boldsymbol T)
\cap
\ker(\boldsymbol K)
=
\{\boldsymbol0\}.
}
$$

这要求单点约束与多点运动约束共同排除整体刚体运动，以及其他由连接不足造成的机构模式。若仍存在非零向量

$$
\boldsymbol U_r
\in
\mathcal R(\boldsymbol T)
\cap
\ker(\boldsymbol K),
$$

则该位移既满足全部约束，又不产生应变能。此时约化矩阵仍然奇异，更换线性求解器也不会消除该零能模式。

### 6.2 拉格朗日乘子形式与约束力

约束也可以不经消元，而以拉格朗日乘子 $\boldsymbol\lambda$ 保留在方程中：

$$
\begin{bmatrix}
\boldsymbol K & \boldsymbol C^{\mathsf T}\\
\boldsymbol C & \boldsymbol0
\end{bmatrix}
\begin{bmatrix}
\boldsymbol U\\
\boldsymbol\lambda
\end{bmatrix}
=
\begin{bmatrix}
\boldsymbol f\\
\boldsymbol g
\end{bmatrix}.
$$

$\boldsymbol\lambda\in\mathbb R^m$ 是一阶约束乘子张量。它的唯一轴 $r$ 对应约束方程，元素 $\lambda_r$ 是约束残量 $C_{rI}U_I-g_r$ 的对偶坐标；若第 $r$ 条约束整体乘以非零常数，$\lambda_r$ 会作相反比例的变化，因此单个乘子分量并不是坐标无关的物理节点力。乘积

$$
(\boldsymbol C^{\mathsf T}\boldsymbol\lambda)_I
=
C_{rI}\lambda_r
$$

收缩约束轴 $r$，保留全局自由度轴 $I$，因此把约束空间中的乘子映射为完整自由度空间中的广义力项。按本文的拉格朗日函数符号，物理反力是这一项的负值。块矩阵的行轴由“全局平衡方程”和“约束方程”两部分组成，列轴由“完整位移”和“约束乘子”两部分组成。

第一行给出平衡关系

$$
\boldsymbol K\boldsymbol U
+
\boldsymbol C^{\mathsf T}\boldsymbol\lambda
=
\boldsymbol f.
$$

上述方程来自拉格朗日函数

$$
\mathcal L
=
\frac12\boldsymbol U^{\mathsf T}\boldsymbol K\boldsymbol U
-\boldsymbol f^{\mathsf T}\boldsymbol U
+\boldsymbol\lambda^{\mathsf T}
(\boldsymbol C\boldsymbol U-\boldsymbol g).
$$

在这一符号约定下，$\boldsymbol C^{\mathsf T}\boldsymbol\lambda$ 位于平衡方程左端，而约束对结构施加的反力为

$$
\boldsymbol f_{\mathrm{reac}}
=
\boldsymbol K\boldsymbol U-
\boldsymbol f
=
-\boldsymbol C^{\mathsf T}\boldsymbol\lambda.
$$

若拉格朗日函数中的约束项改取负号，乘子的符号也随之反转，但物理反力不变。

另一种常见方式是罚函数法。把

$$
\frac{\eta_{\mathrm{pen}}}{2}
\|\boldsymbol C\boldsymbol U-\boldsymbol g\|^2
$$

加入总势能，可得

$$
\left(
\boldsymbol K+
\eta_{\mathrm{pen}}\boldsymbol C^{\mathsf T}\boldsymbol C
\right)
\boldsymbol U
=
\boldsymbol f+
\eta_{\mathrm{pen}}\boldsymbol C^{\mathsf T}\boldsymbol g.
$$

有限的罚参数 $\eta_{\mathrm{pen}}>0$ 只近似满足约束；过大的 $\eta_{\mathrm{pen}}$ 又会放大系统的条件数。对需要精确执行单点约束和多点运动约束的线性静力分析，消元或拉格朗日乘子形式更便于控制约束误差。

乘子形式可由 $-\boldsymbol C^{\mathsf T}\boldsymbol\lambda$ 恢复约束反力，但系统成为不定的鞍点系统。消元形式只求独立自由度，通常保留正定性；求解完成后仍可由完整平衡残差恢复节点反力：

$$
\boxed{
\boldsymbol f_{\mathrm{reac}}
=
\boldsymbol K\boldsymbol U-
\boldsymbol f.
}
$$

$\boldsymbol f_{\mathrm{reac}}$ 是一阶全局广义力张量，唯一轴 $I$ 与完整自由度对应；元素 $(f_{\mathrm{reac}})_I$ 是约束系统在该自由度上对结构施加的力或力矩。

约化平衡条件等价于

$$
\boxed{
\boldsymbol T^{\mathsf T}\boldsymbol f_{\mathrm{reac}}
=
\boldsymbol0.
}
$$

其分量形式为

$$
T_{IA}(f_{\mathrm{reac}})_I=0.
$$

全局自由度轴 $I$ 被收缩，保留的独立自由度轴 $A$ 上每个元素都为零。它表示反力在全部允许虚位移方向上不作功。只有完全未参与多点约束的自由分量，才可直接要求对应的 $(f_{\mathrm{reac}})_I$ 接近零。对多点运动约束而言，单个原始节点上的残差通常没有独立物理意义，应通过 $\boldsymbol T^{\mathsf T}\boldsymbol f_{\mathrm{reac}}=\boldsymbol0$ 或 $-\boldsymbol C^{\mathsf T}\boldsymbol\lambda$ 检查其合力与合力矩。

### 6.3 求解与结果恢复

约化系统求解后，首先恢复完整节点自由度：

$$
\boldsymbol U
=
\boldsymbol T\boldsymbol q+
\boldsymbol U_0.
$$

随后对每个单元执行

$$
\boldsymbol q_e^{\mathrm{node}}
=
\boldsymbol L_e\boldsymbol U,
\qquad
\boldsymbol q_e^{\mathrm{loc}}
=
\mathbf O_e
\boldsymbol q_e^{\mathrm{node}},
\qquad
\boldsymbol q_e^{\mathrm{kin}}
=
\mathbf H_e\boldsymbol q_e^{\mathrm{loc}}
=
\mathbf T_e\boldsymbol q_e^{\mathrm{node}},
$$

再从 $\boldsymbol q_e^{\mathrm{kin}}$ 提取 $\boldsymbol q_m$ 和 $\boldsymbol q_p$。这保证应变恢复与 $\mathbf K_e^{\mathrm{node}}=\mathbf T_e^{\mathsf T}\mathbf K_e^{\mathrm{kin}}\mathbf T_e$ 使用相同的坐标旋转和运动学映射。由于内部非协调参数没有进入总体系统，结果恢复时还要在单元内部把它重新求出：

$$
\boldsymbol\zeta
=
-\mathbf K_{\zeta\zeta}^{-1}
\left(
\mathbf K_{m\zeta}^{\mathsf T}\boldsymbol q_m
+\mathbf K_{p\zeta}^{\mathsf T}\boldsymbol q_p
\right).
$$

对于关于中性面对称的截面，$\mathbf B_{\mathrm V}=\boldsymbol0$，因而 $\mathbf K_{p\zeta}=\boldsymbol0$。此时内部参数只由膜块自由度驱动：

$$
\boldsymbol\zeta
=
-\mathbf K_{\zeta\zeta}^{-1}
\mathbf K_{m\zeta}^{\mathsf T}\boldsymbol q_m.
$$

随后在积分点 $g$ 计算

$$
\begin{aligned}
\underline\varepsilon^{m}_g
&=
\mathbf B_{m,g}\boldsymbol q_m
+\mathbf B_{\mathrm{inc},g}\boldsymbol\zeta,
\\
\underline\kappa_g
&=
\mathbf B_{\kappa,g}\boldsymbol q_p,
\\
\underline\gamma_g^{\mathrm{MITC4}}
&=
\mathbf B_{s,g}^{\mathrm{MITC4}}\boldsymbol q_p.
\end{aligned}
$$

第一式同时恢复协调膜应变和内部非协调应变；第二式与第 2 节的曲率符号一致；第三式使用与单元能量相同的 MITC4 假定剪切插值。采用与刚度形成相同的应变映射，恢复量就是离散能量对相应广义应变的导数。

积分点处的截面合力工程分量列阵为

$$
\boxed{
\begin{aligned}
\underline N_g
&=
\mathbf A_{\mathrm V,g}\underline\varepsilon_g^m
+
\mathbf B_{\mathrm V,g}\underline\kappa_g,
\\
\underline M_g
&=
\mathbf B_{\mathrm V,g}^{\mathsf T}
\underline\varepsilon_g^m
+
\mathbf D_{\mathrm V,g}\underline\kappa_g,
\\
\underline Q_g
&=
\mathbf S_{\mathrm V,g}
\underline\gamma_g^{\mathrm{MITC4}}.
\end{aligned}
}
$$

内部参数的驻值条件还给出一个结果校验关系：

$$
\boxed{
\sum_g
\mathbf B_{\mathrm{inc},g}^{\mathsf T}
\underline N_g
J_g\omega_g
=
\boldsymbol0.
}
$$

它表示恢复后的膜力对每个单元内部非协调模式都满足离散平衡。

经功共轭 Voigt 映射的逆映射，可由 $\underline N_g,\underline M_g,\underline Q_g$ 重构第 2 节定义的截面张量 $\boldsymbol N,\boldsymbol M,\boldsymbol Q$；其中 $\mathbf B_{\mathrm V}^{\mathsf T}$ 正是连续伴随映射 $\mathbb B^*$ 的工程分量表示。

若需要壳厚方向上的应力，则在指定厚度坐标 $z$ 处恢复

$$
\boldsymbol\sigma_{\!\parallel}(z)
=
\mathbb C(z):
\left(
\boldsymbol\varepsilon^m
+
z\boldsymbol\kappa
\right),
$$

再由局部壳坐标变换到全局坐标。

于是整个离散计算可以写成一条连续的映射链：

$$
\boxed{
\begin{aligned}
&(\boldsymbol X_a^{\mathrm{node}},\,z_{\mathrm{off}},\,t,\,\mathbb C,\,p,
\,\text{线性运动约束})
\\
&\quad\longrightarrow
(\boldsymbol K,\boldsymbol f,\boldsymbol C,\boldsymbol g)
\\
&\quad\longrightarrow
\boldsymbol U=\boldsymbol T\boldsymbol q+\boldsymbol U_0
\\
&\quad\longrightarrow
\boldsymbol T^{\mathsf T}\boldsymbol K\boldsymbol T\boldsymbol q
=
\boldsymbol T^{\mathsf T}
(\boldsymbol f-\boldsymbol K\boldsymbol U_0)
\\
&\quad\longrightarrow
(\boldsymbol U,\boldsymbol f_{\mathrm{reac}},
\boldsymbol\varepsilon^m,\boldsymbol\kappa,\boldsymbol\gamma,
\boldsymbol N,\boldsymbol M,\boldsymbol Q,
\boldsymbol\sigma_{\!\parallel}(z)).
\end{aligned}
}
$$

### 6.4 约束与区域分解求解器的衔接

当约化系统采用迭代法求解时，约束映射决定区域分解方法实际作用的代数算子。

采用约束消元时，区域分解方法处理的对象是施加约束后的代数系统

$$
\bar{\boldsymbol K}
=
\boldsymbol T^{\mathsf T}
\boldsymbol K
\boldsymbol T,
$$

多点约束会改变自由度之间的耦合关系：一个主自由度可能通过 $\boldsymbol T$ 同时控制多个从属节点，原本局部的单元作用由此投影到约化空间。若保留拉格朗日乘子形式，则求解对象是前述鞍点系统，需要使用与不定系统相适配的区域分解方法。

从图的角度看，刚体从属约束和加权运动插值约束可视为连接多个节点的超边。若这些节点跨越子域，约化矩阵中会出现跨子域耦合。分区、局部矩阵提取以及粗空间构造都必须包含这些耦合；否则局部子问题与真实总体算子不一致。

对于 BDDC、FETI-DP 或重叠 Schwarz 方法，约束处理与区域分解的逻辑次序可概括为：

$$
\boxed{
\text{模型层线性运动约束}
\;\longrightarrow\;
\text{独立自由度系统}
\;\longrightarrow\;
\text{子域划分与界面定义}
\;\longrightarrow\;
\text{预条件与迭代求解}.
}
$$

这里的线性运动约束是离散结构模型给出的物理或运动学条件；BDDC 中选取的主连续性约束（primal constraints）、FETI-DP 中的界面连续性条件以及 Schwarz 方法中的粗空间，则属于线性求解器层。两类约束可能作用于相关自由度，但其来源和职责不同。

### 6.5 离散对象的轴与元素

一个几何张量在固定点上通过其协变或逆变指标表示方向结构；离散后还可以增加单元、积分点和节点等离散索引。这些索引用于区分多个离散对象，不增加单个物理张量的阶数。

主要离散对象的轴与元素意义如下：

| 离散对象族 | 形状 | 各轴的意义 | 单个元素的意义 |
|---|---:|---|---|
| 参考节点坐标族 $\mathcal X^{\mathrm{node}}$ | $(N_n,3)$ | 节点轴、空间方向轴 | $\mathcal X^{\mathrm{node}}_{ai}=(X_a^{\mathrm{node}})^i$ 是节点 $a$ 的第 $i$ 个参考坐标 |
| 节点自由度族 $\mathcal Q^{\mathrm{node}},\mathcal Q^{\mathrm{kin}}$ | $(N_e,4,6)$ | 单元轴、局部节点轴、物理自由度轴 | $\mathcal Q^{\mathrm{node}}_{eas}$ 是节点基中的广义位移；$\mathcal Q^{\mathrm{kin}}_{eas}$ 是经 $\mathbf T_e$ 映射后进入应变插值的分量 |
| 应变映射族 $\mathcal B$ | $(N_e,N_g,n_\varepsilon,n_q)$ | 单元轴、积分点轴、应变分量轴、输入自由度轴 | $\mathcal B_{egcI}$ 是第 $I$ 个单位输入自由度对第 $c$ 个应变分量的贡献 |
| 单元刚度族 $\mathcal K$ | $(N_e,24,24)$ | 单元轴、测试自由度轴、试探自由度轴 | $\mathcal K_{eIJ}=(K_e^{\mathrm{node}})_{IJ}$ 是单元 $e$ 中单位位移 $J$ 对内力分量 $I$ 的贡献 |
| 约束矩阵 $\boldsymbol C$ | $(m,n)$ | 约束方程轴、完整自由度轴 | $C_{rI}$ 是自由度 $I$ 在约束 $r$ 中的系数 |
| 约束变换 $\boldsymbol T$ | $(n,n_r)$ | 完整自由度轴、独立自由度轴 | $T_{IA}$ 是独立自由度 $A$ 对完整自由度 $I$ 的贡献 |

$N_n$、$N_e$ 和 $N_g$ 分别是节点数、单元数和每个单元的积分点数。采用 $2\times2$ Gauss 积分时 $N_g=4$。对 $\mathcal B$，膜应变与弯曲应变有 $n_\varepsilon=3$，横向剪切有 $n_\varepsilon=2$；输入轴维数 $n_q$ 由所作用的节点块或内部参数空间决定。单元轴与积分点轴属于离散索引轴；固定 $e,g$ 后的 $\mathcal B_{eg}$ 才是从一个自由度空间到应变空间的线性映射，固定 $e$ 后的 $\mathbf K_e^{\mathrm{node}}$ 才是二阶刚度映射。

若完整自由度数为 $n$，并记 $r_C=\operatorname{rank}(\boldsymbol C)$，在约束彼此相容时有

$$
\dim\ker(\boldsymbol C)
=
n-r_C.
$$

因此，独立自由度数由约束映射的秩决定，不能只按约束的表面数量作算术相减。

整个数学过程由一条能量链贯穿：连续层面的膜应变、弯曲应变和横向剪切应变经截面本构变成膜力、弯矩和横向剪力；四节点离散以非协调膜应变和静态凝聚扩充膜变形空间，以 MITC4 假定剪切控制薄壳剪切锁死，以半正定钻转能量排除非物理零能模式；线性运动学约束再把总体方程投影到独立自由度空间，所有环节都由虚功和势能的不变性联系起来。

## 参考资料

1. J. N. Reddy, *Theory and Analysis of Elastic Plates and Shells*.
2. K.-J. Bathe, *Finite Element Procedures*.
3. T. J. R. Hughes, *The Finite Element Method: Linear Static and Dynamic Finite Element Analysis*.
4. K.-J. Bathe and E. N. Dvorkin, “A four-node plate bending element based on Mindlin/Reissner plate theory and a mixed interpolation,” *International Journal for Numerical Methods in Engineering*, 1985.
5. J. C. Simo and M. S. Rifai, “A class of mixed assumed strain methods and the method of incompatible modes,” *International Journal for Numerical Methods in Engineering*, 1990.
