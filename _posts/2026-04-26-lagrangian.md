---
layout: post
title: "제한된 3체 문제와 라그랑주점"
date: 2026-04-26
tags: [태양계천문학, 궤도역학]
---

## 제한된 3체 문제
$m_2$의 질량을 가지는 천체를 정규화해 다음의 파라미터를 도입한다.

$$\bar{\mu}=\frac{m_2}{m_1+m_2}$$

그러면 우리는 $m_1$과 $m_2$의 질량을 가지는 두 천체에 대해 $\mu_1=1-\bar{\mu}$와 $\mu_2=\bar{\mu}$로 쓸 수 있다. 그러면 이 계에 놓인 임의의 제3의 천체의 관성좌표계 $P(\xi,\eta,\zeta)$에서의 운동방정식은 다음과 같다.

$$\begin{aligned} \ddot{\xi} &= \mu_1\frac{\xi_1-\xi}{r_1^3}+\mu_2\frac{\xi_2-\xi}{r_2^3} \\ \ddot{\eta} &= \mu_1\frac{\eta_1-\eta}{r_1^3}+\mu_2\frac{\eta_2-\eta}{r_2^3} \\ \ddot{\zeta} &= \mu_1\frac{\zeta_1-\zeta}{r_1^3}+\mu_2\frac{\zeta_2-\zeta}{r_2^3} \end{aligned}$$

이는 각 축방향으로의 만유인력을 고려한 것이다. 이때 $r_1$과 $r_2$는 $P$로부터 각 천체까지의 거리이다.

$$\begin{aligned} r_1^2 &= (\xi_1-\xi)^2+(\eta_1-\eta)^2+(\zeta_1-\zeta)^2 \\ r_2^2 &= (\xi_2-\xi)^2+(\eta_2-\eta)^2+(\zeta_2-\zeta)^2 \end{aligned}$$

이때, 질량중심의 정의에 따라 $m_1$ 천체로부터 질량중심까지의 거리, 그리고 $m_2$ 천체로부터 질량중심까지의 거리를 각각 $\mu_2$와 $\mu_1$으로 놓는 것에는 문제가 없어보인다. 그러면 이를 고려해 질량중심을 원점으로 하는 회전 좌표계 $P(x,y,z)$를 도입해, $m_1$과 $m_2$ 천체를 각각 $(-\mu_2,0,0)$, $(\mu_1,0,0)$에 두자. 

회전좌표계가 두 천체를 항상 x축 위의 고정된 위치에 보이게 하려면, 관성좌표계에 대해 두 천체의 공전 각속도 $n$에 대해 똑같이 $nt$만큼 회전시켜야 한다. 그러면 앞서 살펴본 관성좌표계 $P(\xi,\eta,\zeta)$는 회전좌표계 $P(x,y,z)$를 $nt$만큼 회전변환한 것으로 이해할 수 있다. 

$$\begin{bmatrix} \xi \\ \eta \\ \zeta \end{bmatrix} = \begin{bmatrix} \cos nt & -\sin nt & 0 \\ \sin nt & \cos nt & 0 \\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} x \\ y \\ z \end{bmatrix}$$

이를 두번 미분하면 다음과 같다. 단순한 곱의 미분과 행렬의 덧셈이므로 과정은 생략한다.

$$\begin{bmatrix} \ddot{\xi} \\ \ddot{\eta} \\ \ddot{\zeta} \end{bmatrix} = \begin{bmatrix} \cos nt & -\sin nt & 0 \\ \sin nt & \cos nt & 0 \\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} \ddot{x}-2n\dot{y}-n^2x \\ \ddot{y}+2n\dot{x}-n^2y \\ \ddot{z} \end{bmatrix}$$

우변을 보면, 기존의 $\ddot{x}$등의 항에 추가적으로 뭔가 붙은 것을 알 수 있다. 먼저 $2n\dot{y}$꼴로 되어있는 것은 코리올리 가속도와 관련된 항이고, $n^2x$꼴로 되어있는 것은 구심가속도와 관련된 항이다. 이를 앞서 살펴본 관성좌표계에서의 운동방정식과 연립하면 다음을 얻는다.

$$\begin{aligned} \ddot{x}-2n\dot{y}-n^2x &= -\left(\mu_1\frac{x+\mu_2}{r_1^3}+\mu_2\frac{x-\mu_1}{r_2^3}\right) \\ \ddot{y}+2n\dot{x}-n^2y &= -\left(\frac{\mu_1}{r_1^3}+\frac{\mu_2}{r_2^3}\right)y \\ \ddot{z} &= -\left(\frac{\mu_1}{r_1^3}+\frac{\mu_2}{r_2^3}\right)z \end{aligned}$$

다시 정리하여,

$$\begin{aligned} \ddot{x}-2n\dot{y} &= -\left(\mu_1\frac{x+\mu_2}{r_1^3}+\mu_2\frac{x-\mu_1}{r_2^3}-n^2x\right) \\ \ddot{y}+2n\dot{x} &= -\left(\frac{\mu_1}{r_1^3}+\frac{\mu_2}{r_2^3} -n^2\right)y \\ \ddot{z} &= -\left(\frac{\mu_1}{r_1^3}+\frac{\mu_2}{r_2^3}\right)z \end{aligned}$$

이 식들의 우변을 각각 $U$라는 스칼라 함수의 $x$, $y$, $z$에 대한 편미분으로 두자. 즉,

$$\begin{aligned} \ddot{x}-2n\dot{y} &= \pdv{U}{x} \\ \ddot{y}+2n\dot{x} &= \pdv{U}{y} \\ \ddot{z} &= \pdv{U}{z} \end{aligned}$$

이때, 이 $U$를 **유효 퍼텐셜**(effective potential)이라 하고 그 값은 다음과 같다.

$$U=\frac{1}{2}n^2(x^2+y^2)+\frac{\mu_1}{r_1}+\frac{\mu_2}{r_2}$$

이 값은 에너지에 해당하는 값이긴 하나, 2체 문제에서와 달리 보존되지 않는 값이다.

## 야코비 상수
앞서 살펴본 식의 양변에 각각 $\dot{x}$, $\dot{y}$, $\dot{z}$를 곱해보자.

$$\begin{aligned} (\ddot{x}-2n\dot{y})\cdot\dot{x} &= \pdv{U}{x}\cdot\dot{x} \\ (\ddot{y}+2n\dot{x})\cdot\dot{y} &= \pdv{U}{y}\cdot\dot{y} \\ \ddot{z}\cdot\dot{z} &= \pdv{U}{z}\cdot\dot{z} \end{aligned}$$

그리고 세 식을 전부 더한다.

$$\dot{x}\ddot{x}+\dot{y}\ddot{y}+\dot{z}\ddot{z}=\pdv{U}{x}\pdv{x}{t}+\pdv{U}{y}\pdv{y}{t}+\pdv{U}{z}\pdv{z}{t}=\odv{U}{t}$$

양변을 적분해보자. 

$$\frac{1}{2}\left(\dot{x}^2+\dot{y}^2+\dot{z}^2\right)=\frac{1}{2}V^2=U+\mathrm{const.}$$

정리하면 다음과 같은 중요한 식을 얻는다.

$$V^2 = 2U - C_J \ge 0$$

여기서 $C_J$는 **야코비 상수**라 하며, 그 값은 다음과 같고 그 값은 일정하다.

$$C_J = n(x^2+y^2)+2\left(\frac{\mu_1}{r_1}+\frac{\mu_2}{r_2}\right)-V^2$$

즉, 에너지 보존과 같이 쓸만한 상수값을 찾아냈다! 또한, $V^2$은 0보다 크거나 같으므로 $2U \ge C_J$가 되는데, 이는 곧 천체의 유효 퍼텐셜 값이 특정 값 이상으로 제한된다는 것을 의미한다. 다시 말해 천체가 존재할 수 있는 위치는 특정 $C_J$에 의해 정해진다는 것이다. 특히 $2U = C_J$는 $V=0$임을 의미하며, 천체가 존재할 수 있는 위치 범위의 경계면인 영속도 곡면을 의미한다.

## 티세랑 파라미터
