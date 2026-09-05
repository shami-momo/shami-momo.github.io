---
layout: post
title: "원형 제한 3체 문제 (CR3BP)"
date: 2026-04-26
tags: [태양계천문학, 궤도역학]
---

## 원형 제한 3체 문제
질량이 각각 $m_1$과 $m_2$인 두 주천체가 서로 $a$만큼 떨어져 서로의 중력에 의해 $n$의 공전각속도로 원운동하고 있다고 생각해보자. 이때 이 두 주천체에 비해 무시할 만큼 질량이 작은 제3천체의 운동을 다루는 것이 바로 **원형 제한 3체 문제**(CR3BP)이다.

우선 편의를 위해 다음과 같이 질량, 길이, 시간을 정규화한다.

$$\mathsf{M}:m_1+m_2,\quad\mathsf{L}:a,\quad\mathsf{T}:\frac{1}{n}=\sqrt{\frac{a^3}{G(m_1+m_2)}}$$

참고로 케플러 제3법칙 $n^2a^3=G(m_1+m_2)$이었다. 이 무차원 단위계에서는 두 주천체의 질량 합과 거리, 공전각속도 및 중력계수가 모두 1이 된다. 이때 질량비는 다음과 같다.

$$\mu_1=\frac{m_1}{m_1+m_2}, \quad \mu_2=\frac{m_2}{m_1+m_2}$$

이는 주천체들의 정규화된 질량에 해당한다. 이제 이를 바탕으로 운동을 기술해보자.

## 유효 퍼텐셜
먼저 질량중심을 원점으로 하는 관성좌표계를 생각하자. 제3천체의 위치를 $(\xi,\eta,\zeta)$, 두 주천체의 위치를 각각 $(\xi_1,\eta_1,\zeta_1)$, $(\xi_2,\eta_2,\zeta_2)$라 하면, 제3천체의 운동방정식은 다음과 같다.

$$\begin{bmatrix} \ddot{\xi} \\ \ddot{\eta} \\ \ddot{\zeta}\end{bmatrix} = \begin{bmatrix} \mu_1\frac{\xi_1-\xi}{r_1^3}+\mu_2\frac{\xi_2-\xi}{r_2^3} \\ \mu_1\frac{\eta_1-\eta}{r_1^3}+\mu_2\frac{\eta_2-\eta}{r_2^3} \\ \mu_1\frac{\zeta_1-\zeta}{r_1^3}+\mu_2\frac{\zeta_2-\zeta}{r_2^3}\end{bmatrix}$$

여기서 $r_1$과 $r_2$는 제3천체에서 각 주천체까지의 거리이다.

$$\begin{aligned} r_1^2 &= (\xi_1-\xi)^2+(\eta_1-\eta)^2+(\zeta_1-\zeta)^2 \\ r_2^2 &= (\xi_2-\xi)^2+(\eta_2-\eta)^2+(\zeta_2-\zeta)^2 \end{aligned}$$

질량중심의 정의에 따르면 두 주천체는 질량중심으로부터 각각 $\mu_2$, $\mu_1$만큼 떨어져 있다. 그러면 이를 고려해 질량중심을 원점으로 하는 회전 좌표계를 도입해, 제3천체의 위치를 $(x,y,z)$, 두 주천체의 위치를 각각 $(-\mu_2,0,0)$, $(\mu_1,0,0)$라 하자.

이 회전좌표계는 관성좌표계에 대해 두 주천체의 각속도 $n=1$으로 회전한다. 따라서 회전행렬을 다음과 같이 놓을 수 있다.

$$\mathbf{R}(t)=\begin{bmatrix} \cos t & -\sin t & 0 \\ \sin t & \cos t & 0 \\ 0 & 0 & 1 \end{bmatrix}$$

그러면 제3천체의 위치는 다음과 같다.

$$\begin{bmatrix} \xi \\ \eta \\ \zeta\end{bmatrix} = \mathbf{R}(t)\begin{bmatrix} x \\ y \\ z \end{bmatrix}$$

이를 두 번 미분하면 제3천체의 가속도를 얻는다.

$$\begin{bmatrix} \ddot{\xi} \\ \ddot{\eta} \\ \ddot{\zeta}\end{bmatrix} = \mathbf{R}(t)\begin{bmatrix} \ddot{x}-2\dot{y}-x \\ \ddot{y}+2\dot{x}-y \\ \ddot{z} \end{bmatrix}$$

여기서 $(\dot{x},\dot{y})$에 비례하는 항은 코리올리 항이다. $(-x,-y)$는 구심가속도 항으로, 우변으로 넘기면 원심항이 된다.

앞선 두 주천체의 위치 가정을 생각하면, 주천체의 관성좌표계에서의 좌표는 다음과 같다.

$$\mathbf{R}(t) \begin{bmatrix} -\mu_2\\0\\0 \end{bmatrix}, \quad \mathbf{R}(t) \begin{bmatrix} \mu_1\\0\\0 \end{bmatrix}$$

따라서 제3천체에서 각 주천체를 향하는 위치벡터는 

$$\begin{aligned} \begin{bmatrix}\xi_1-\xi \\ \eta_1-\eta \\ \zeta_1-\zeta \end{bmatrix} &= \mathbf{R}(t) \begin{bmatrix} -(x+\mu_2) \\ -y \\ -z \end{bmatrix} \\ \begin{bmatrix}
\xi_2-\xi \\ \eta_2-\eta \\ \zeta_2-\zeta \end{bmatrix}
&= \mathbf{R}(t) \begin{bmatrix} -(x-\mu_1) \\ -y \\ -z\end{bmatrix}\end{aligned}$$

회전변환은 길이를 보존하므로, 회전좌표계에서의 거리는 다음과 같이 얻어진다.

$$\begin{aligned} r_1^2&=(x+\mu_2)^2+y^2+z^2 \\ r_2^2&=(x-\mu_1)^2+y^2+z^2 \end{aligned}$$

이를 관성좌표계 운동방정식에 대입하면 다음을 얻는다.

$$\begin{bmatrix} \ddot{\xi} \\ \ddot{\eta} \\ \ddot{\zeta} \end{bmatrix}
= \mathbf{R}(t) \begin{bmatrix} -\left(\mu_1\frac{x+\mu_2}{r_1^3}+\mu_2\frac{x-\mu_1}{r_2^3}\right) \\
-\left(\frac{\mu_1}{r_1^3}+\frac{\mu_2}{r_2^3}\right)y \\-\left(\frac{\mu_1}{r_1^3}+\frac{\mu_2}{r_2^3}\right)z \end{bmatrix}$$

따라서 이를 회전좌표계에서의 가속도와 비교하면 다음을 얻을 수 있다.

$$\begin{aligned} \ddot{x}-2\dot{y}-x &= -\left(\mu_1\frac{x+\mu_2}{r_1^3}+\mu_2\frac{x-\mu_1}{r_2^3}\right) \\ \ddot{y}+2\dot{x}-y &= -\left(\frac{\mu_1}{r_1^3}+\frac{\mu_2}{r_2^3}\right)y \\ \ddot{z} &= -\left(\frac{\mu_1}{r_1^3}+\frac{\mu_2}{r_2^3}\right)z \end{aligned}$$

$-x$과 $-y$를 우변으로 보내 그 우변의 항들을 $U$라는 스칼라 함수의 그래디언트로 써보자.

$$\begin{aligned} \ddot{x}-2\dot{y} &= \pdv{U}{x} \\ \ddot{y}+2\dot{x} &= \pdv{U}{y} \\ \ddot{z} &= \pdv{U}{z} \end{aligned}$$

이때, 이 $U$를 **유효 퍼텐셜**(Effective Potential)이라 하고 그 값은 다음과 같다.

$$U=\frac{1}{2}(x^2+y^2)+\frac{\mu_1}{r_1}+\frac{\mu_2}{r_2}$$

이 값은 보존되지 않는 값이므로 뭔가 보존되는 값이 있으면 기준으로 쓰기에 좋을 것 같다.

## 야코비 상수
앞서 살펴본 식의 양변에 각각 $\dot{x}$, $\dot{y}$, $\dot{z}$를 곱해보자.

$$\begin{aligned} (\ddot{x}-2\dot{y})\cdot\dot{x} &= \pdv{U}{x}\cdot\dot{x} \\ (\ddot{y}+2\dot{x})\cdot\dot{y} &= \pdv{U}{y}\cdot\dot{y} \\ \ddot{z}\cdot\dot{z} &= \pdv{U}{z}\cdot\dot{z} \end{aligned}$$

그리고 세 식을 전부 더한다.

$$\dot{x}\ddot{x}+\dot{y}\ddot{y}+\dot{z}\ddot{z}=\pdv{U}{x}\pdv{x}{t}+\pdv{U}{y}\pdv{y}{t}+\pdv{U}{z}\pdv{z}{t}=\odv{U}{t}$$

회전좌표계에서의 속도 $V^2=\dot{x}^2+\dot{y}^2+\dot{z}^2$임을 유념해 양변을 적분해보자. 

$$\frac{1}{2}\left(\dot{x}^2+\dot{y}^2+\dot{z}^2\right)=\frac{1}{2}V^2=U+\mathrm{const.}$$

정리하면 다음과 같은 중요한 식을 얻는다.

$$V^2 = 2U - C_J$$

여기서 $C_J$는 **야코비 상수**(Jacobi Constant, Jacobi Integral)라 하며, 그 값은 다음과 같고 그 값은 일정하다.

$$C_J = x^2+y^2+2\left(\frac{\mu_1}{r_1}+\frac{\mu_2}{r_2}\right)-V^2$$

즉, 유효 퍼텐셜 $U$ 자체는 일반적으로 궤도를 따라 일정하지 않지만, $U$와 회전좌표계에서의 속력을 결합하면 보존량을 얻을 수 있다. 

주어진 $C_J$에 대하여

$$V^2=2U-C_J \ge 0$$

이므로, 제3천체는 $2U\ge C_J$인 영역에서만 운동할 수 있다. 반대로 $2U< C_J$인 영역은 운동이 불가능하다. 두 영역의 경계인 $2U=C_J$에서는 회전좌표계에서의 순간 속력이 0이며, 이 경계를 영속도면이라 한다. 평면 운동에서는 영속도 곡선이라 부른다.

## 티세랑 파라미터
제3천체가 $m_2$의 질량을 가지는 주천체(이를 섭동천체라 한다)로부터 충분히 멀리 떨어져 있을 때를 생각해보자.

$$\frac{\mu_2}{r_2}\ll\frac{\mu_1}{r_1}$$

중심 천체($m_1$)까지의 거리를 $r$라 하면, 야코비 상수는 다음과 같이 간단해진다.

$$C_J \approx x^2+y^2+\frac{2\mu_1}{r}-v_\mathrm{rot}^2$$

$V$는 회전좌표계에서의 속도이므로 $v_\mathrm{rot}$로 표기했다. 그러면 관성좌표계에서의 속도는 이렇게 쓸 수 있다.

$$\mathbf{v}=\mathbf{v}_\mathrm{rot}+\boldsymbol{\omega}\times\mathbf{r}$$

정리하면 다음과 같다.

$$v_\mathrm{rot}^2=\lVert\mathbf{v}-\boldsymbol{\omega}\times\mathbf{r}\rVert^2=v^2+\lVert\boldsymbol{\omega}\times\mathbf{r}\rVert^2-2\mathbf{v}\cdot(\boldsymbol{\omega}\times\mathbf{r})$$

이때 정규화에 의해 $\lVert\boldsymbol{\omega}\rVert=1$이었으므로, $z$-축이 회전축인 것을 생각하면 $\lVert\boldsymbol{\omega}\times\mathbf{r}\rVert^2=x^2+y^2$이다. 또한 삼중곱 계산에 의해 $\mathbf{v}\cdot(\boldsymbol{\omega}\times\mathbf{r})=\boldsymbol{\omega}\cdot(\mathbf{r}\times\mathbf{v})=h_z$이다.

따라서 계산하면 회전좌표계에서의 속도는 다음과 같다.

$$v_\mathrm{rot}^2=v^2+x^2+y^2-2h_z$$

이를 야코비 상수에 대입해보자.

$$C_J \approx x^2+y^2+\frac{2\mu_1}{r}-(v^2+x^2+y^2-2h_z)=\frac{2\mu_1}{r}-v^2+2h_z$$

깔끔하게 소거되는 모습을 볼 수 있다. 여기서 $v^2$에 활력방정식 $v^2=\mu_1\left(\frac{2}{r}-\frac{1}{a}\right)$을 대입한다.

$$C_J \approx \frac{\mu_1}{a}+2h_z$$

이때 앞선 글에서 살펴본 바와 같이 각운동량의 $z$-축 성분은 이렇게 쓸 수 있었다.

$$h_z=h\cos i=h\sqrt{\mu_1 a(1-e^2)}\cos i$$

이때 섭동천체의 질량이 중심천체에 비해 매우 작으면 $\mu_1\approx 1$이므로, 결국 야코비 상수는 이렇게 된다.

$$C_J \approx \frac{1}{a}+2\sqrt{a(1-e^2)}\cos i$$

여기서 $a$는 정규화된 값이므로, 원래의 길이로 복원하여 쓰면 섭동천체 $P$에 대한 **티세랑 파라미터**(Tisserand Parameter)를 얻는다.

$$T_P = \frac{a_P}{a}+2\sqrt{\frac{a}{a_P}(1-e^2)}\cos i$$

$a_P$는 섭동천체의 궤도장반경, $a$는 제3천체의 궤도장반경이다. 

<figure>
  <img src="{{ '/assets/images/posts/2026-04-26-lagrangian/parameter.webp' | relative_url }}">
  <figcaption>장기적 관점에서 2022AA29의 궤도 요소와 야코비 상수, 티세랑 파라미터의 변화. 2600년경 지구의 준위성이 된다.</figcaption>
</figure>

티세랑 파라미터는 섭동천체의 영향을 무시할 수 있을 때 야코비 상수를 간략화한 형태이므로 근사적으로 보존되는 값이다. 섭동천체와 근접 조우하더라도, 조우 전후 제3천체가 섭동천체로부터 충분히 멀리 떨어져 있다면 티세랑 파라미터의 값은 거의 같게 유지된다. 그러나 근접 조우하는 중에는 무시했던 $\frac{2\mu_2}{r_2}$항이 커지므로 이때는 변하긴 한다.

## 라그랑주점

<figure>
  <img src="{{ '/assets/images/posts/2026-04-26-lagrangian/lagrangian.webp' | relative_url }}">
  <figcaption>라그랑주점의 분포. @NASA</figcaption>
</figure>

제3천체가 회전좌표계에서 정지하는 평형점인 라그랑주점을 구해보자. 이는 $\grad{U}=0$의 해이다.

$$\begin{aligned} \pdv{U}{x} &= x-\mu_1\frac{x+\mu_2}{r_1^3}-\mu_2\frac{x-\mu_1}{r_2^3}=0 \\ \pdv{U}{y} &= y\left(1-\frac{\mu_1}{r_1^3}-\frac{\mu_2}{r_2^3}\right)=0 \\ \pdv{U}{z} &= -z\left(\frac{\mu_1}{r_1^3}+\frac{\mu_2}{r_2^3}\right)=0 \end{aligned}$$

$z$-방향 조건에서 괄호 안은 0보다 크므로 $z=0$이다. 즉, 라그랑주점은 반드시 공전궤도면에 위치한다. 그렇다면 $y$-방향 조건에서 $y=0$인지 $y\ne0$인지에 따라 경우가 달라진다.

### $y\ne 0$인 경우 ($L_4$, $L_5$)
$y\ne0$이므로 $y$-방향 조건은 이렇게 된다.

$$1-\frac{\mu_1}{r_1^3}-\frac{\mu_2}{r_2^3}=0$$

그러면 $x$-방향 조건이 간단해진다.

$$x-\mu_1\frac{x+\mu_2}{r_1^3}-\mu_2\frac{x-\mu_1}{r_2^3}= x\cancel{\left(1-\frac{\mu_1}{r_1^3}-\frac{\mu_2}{r_2^3}\right)}-\mu_1\mu_2\left(\frac{1}{r_1^3}-\frac{1}{r_2^3}\right)=0$$

따라서 $r_1=r_2$이다. 이를 다시 $y$-방향 조건에 대입하면

$$1-\frac{\mu_1+\mu_2}{r_1^3}=0$$

따라서 $r_1=r_2=1$이다. 두 주천체 사이의 거리는 1이므로, 결국 두 주천체와 제3천체가 정삼각형을 이루게 됨을 알 수 있다. 그렇게 총 두 개의 라그랑주점 $L_4$, $L_5$가 나온다. 즉 좌표는 이렇다.

$$L_4=\left(\frac{1}{2}-\mu_2, \frac{\sqrt{3}}{2}, 0\right), \quad L_5=\left(\frac{1}{2}-\mu_2, -\frac{\sqrt{3}}{2}, 0\right)$$

### $y= 0$인 경우 ($L_1$, $L_2$, $L_3$)
$y=0$이므로 $r_1=|x+\mu_2|$, $r_2=|x-\mu_1|$이고, 남는 방정식은 다음과 같다.

$$x-\mu_1\frac{x+\mu_2}{|x+\mu_2|^3}-\mu_2\frac{x-\mu_1}{|x-\mu_1|^3}=0$$

절댓값 내의 부호에 따라 총 세 부분으로 나눌 수 있는데, 이 때문에 해는 두 주천체 모두에 대해 왼쪽에 있는 곳($L_3$), 주천체 사이에 있는 곳($L_1$), 주천체 모두에 대해 오른쪽에 있는 곳($L_2$)의 세 가지가 나온다. 그러나 이는 풀기 복잡하므로 아래와 같은 근사해를 보통 사용한다.

$$r_{2,L_1}\approx\alpha-\frac{1}{3}\alpha^2,\quad r_{2,L_2}\approx\alpha+\frac{1}{3}\alpha^2,\quad r_{1,L_3}\approx1-\frac{7}{12}\frac{\mu_2}{\mu_1}$$

이때 $\alpha=\left(\frac{\mu_2}{3\mu_1}\right)^\frac{1}{3}$이다.

이때 $\alpha^2$은 매우 작은 값이므로 무시하면, $L_1$과 $L_2$는 섭동천체를 기준으로 비슷한 거리 $\alpha$만큼 떨어져 있는 것을 알 수 있다. 이는 정규화된 값이므로 원래의 길이 단위로 복원하면 다음과 같다.

$$r_H\approx a_P\left(\frac{m_2}{3m_1}\right)^\frac{1}{3}$$

이를 **힐 반지름**(Hill Radius)이라 하며, 이를 반지름으로 갖는 구를 **힐 구**(Hill Sphere)라고 한다. 힐 반지름은 제3천체가 섭동천체에 구속될 수 있는 영역의 크기를 나타낸다. 이 영역의 실제 경계는 완전한 구면이 아니지만, 이를 반지름이 $r_H$인 구로 근사한 것이 바로 힐 구이다. 