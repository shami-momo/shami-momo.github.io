---
layout: post
title: "원형 제한 3체 문제와 라그랑주점"
date: 2026-04-26
tags: [태양계천문학, 궤도역학]
---

## 원형 제한 3체 문제
질량이 각각 $m_1$과 $m_2$인 두 천체가 서로의 중력에 의해 원운동하고 있다고 생각해보자. 이때 이 두 천체에 비해 무시할만큼 질량이 작은 제3천체의 운동을 다루는 것이 바로 원형 제한 3체 문제이다.

두 주천체 사이의 거리를 $a$, 질량합을 $M=m_1+m_2$라 하자. 먼저 질량비는 다음과 같다.

$\mu_1=\frac{m_1}{m_1+m_2} \quad \mu_2=\frac{m_2}{m_1+m_2}$

편의를 위해 $\bar{\mu}=\frac{m_2}{m_1+m_2}$를 도입하자.

$$\mu_1=1-\bar{\mu} \quad \mu_2=\bar{\mu}$$

또한, 두 천체의 공전 각속도는 다음과 같다.

$$n^2=\frac{G(m_1+m_2)}{a^3}$$

이는 케플러 제3법칙에서 얻어지는 것이다.

## 관성좌표계
먼저 질량중심을 원점으로 하는 관성좌표계 $(\xi,\eta,\zeta)$를 생각하자. 제3천체의 위치를 $P(\xi,\eta,\zeta)$, 두 주천체의 위치를 각각 $(\xi_1,\eta_1,\zeta_1)$, $(\xi_2,\eta_2,\zeta_2)$라 하면, 제3천체의 운동방정식은 다음과 같다.

$$\begin{aligned} \ddot{\xi} &= n^2\left(\mu_1\frac{\xi_1-\xi}{r_1^3}+\mu_2\frac{\xi_2-\xi}{r_2^3}\right) \\ \ddot{\eta} &= n^2\left(\mu_1\frac{\eta_1-\eta}{r_1^3}+\mu_2\frac{\eta_2-\eta}{r_2^3}\right) \\ \ddot{\zeta} &= n^2\left(\mu_1\frac{\zeta_1-\zeta}{r_1^3}+\mu_2\frac{\zeta_2-\zeta}{r_2^3}\right) \end{aligned}$$

여기서 $r_1$과 $r_2$는 제3천체에서 각 주천체까지의 거리이다.

$$\begin{aligned} r_1^2 &= (\xi_1-\xi)^2+(\eta_1-\eta)^2+(\zeta_1-\zeta)^2 \\ r_2^2 &= (\xi_2-\xi)^2+(\eta_2-\eta)^2+(\zeta_2-\zeta)^2 \end{aligned}$$

질량중심의 정의에 따르면 두 주천체는 질량중심으로부터 각각 $\mu_2$, $\mu_1$만큼 떨어져있다. 그러면 이를 고려해 질량중심을 원점으로 하는 회전 좌표계 $P(x,y,z)$를 도입해, $m_1$과 $m_2$ 천체를 각각 $(-\mu_2,0,0)$, $(\mu_1,0,0)$에 두자. 

이 회전좌표계는 관성좌표계에 대해 두 주천체의 각속도 $n$으로 회전한다. 따라서 시간 $t$에서의 좌표계를 생각하면, 회전행렬을 다음과 같이 놓을 수 있다.

$$R(nt)=\begin{bmatrix} \cos nt & -\sin nt & 0 \\ \sin nt & \cos nt & 0 \\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix}$$

그러면 제3천체의 좌표는 다음과 같다.

$$R(nt)\begin{bmatrix} x \\ y \\ z \end{bmatrix}$$

이를 두번 미분하면 제3천체의 가속도를 얻는다.

$$R(nt)\begin{bmatrix} \ddot{x}-2n\dot{y}-n^2x \\ \ddot{y}+2n\dot{x}-n^2y \\ \ddot{z} \end{bmatrix}$$

여기서 $(\dot{x},\dot{y})$에 비례하는 항은 코리올리 항이며, $(n^2x,n^2y)$에 비례하는 항은 원심항에 해당한다.

이와 마찬가지로 두 주천체의 관성좌표계에서의 좌표는 다음과 같다.

$$R(nt) \begin{bmatrix} \mu_1\\0\\0 \end{bmatrix}$$

따라서 제3천체에서 각 주천체를 향하는 위치벡터는 

$$\begin{aligned} \begin{bmatrix}\xi_1-\xi \\ eta_1-\eta \\ zeta_1-\zeta \end{bmatrix} &= R(nt) \begin{bmatrix} -(x+\mu_2) \\ -y \\ -z \end{bmatrix} \\ \begin{bmatrix}
\xi_2-\xi \\ eta_2-\eta \\ zeta_2-\zeta \end{bmatrix}
&= R(nt) \begin{bmatrix} -(x-\mu_1) \\ -y \\ -z\end{bmatrix}\end{aligned}$$

회전변환은 길이를 보존하므로, 회전좌표계에서는 

$$\begin{aligned} r_1^2&=(x+\mu_2)^2+y^2+z^2 \\ r_2^2&=(x-\mu_1)^2+y^2+z^2 \end{aligned}$$

이를 관성좌표계 운동방정식에 대입하면 다음을 얻는다.

$$\begin{aligned} \ddot{x}-2n\dot{y}-n^2x &= -\left(\mu_1\frac{x+\mu_2}{r_1^3}+\mu_2\frac{x-\mu_1}{r_2^3}\right) \\ \ddot{y}+2n\dot{x}-n^2y &= -\left(\frac{\mu_1}{r_1^3}+\frac{\mu_2}{r_2^3}\right)y \\ \ddot{z} &= -\left(\frac{\mu_1}{r_1^3}+\frac{\mu_2}{r_2^3}\right)z \end{aligned}$$

다시 정리하여,

$$\begin{aligned} \ddot{x}-2n\dot{y} &= -\left(\mu_1\frac{x+\mu_2}{r_1^3}+\mu_2\frac{x-\mu_1}{r_2^3}-n^2x\right) \\ \ddot{y}+2n\dot{x} &= -\left(\frac{\mu_1}{r_1^3}+\frac{\mu_2}{r_2^3} -n^2\right)y \\ \ddot{z} &= -\left(\frac{\mu_1}{r_1^3}+\frac{\mu_2}{r_2^3}\right)z \end{aligned}$$

이 식들의 우변을 각각 $U$라는 스칼라 함수의 $x$, $y$, $z$에 대한 편미분으로 두자. 즉,

$$\begin{aligned} \ddot{x}-2n\dot{y} &= \pdv{U}{x} \\ \ddot{y}+2n\dot{x} &= \pdv{U}{y} \\ \ddot{z} &= \pdv{U}{z} \end{aligned}$$

이때, 이 $U$를 **유효 퍼텐셜**(effective potential)이라 하고 그 값은 다음과 같다.

$$U=\frac{1}{2}n^2(x^2+y^2)+\frac{\mu_1}{r_1}+\frac{\mu_2}{r_2}$$

이 값은 보존되지 않는 값이므로 뭔가 보존되는 값이 있으면 기준으로 쓰기에 좋을 것 같다.

## 야코비 상수
앞서 살펴본 식의 양변에 각각 $\dot{x}$, $\dot{y}$, $\dot{z}$를 곱해보자.

$$\begin{aligned} (\ddot{x}-2n\dot{y})\cdot\dot{x} &= \pdv{U}{x}\cdot\dot{x} \\ (\ddot{y}+2n\dot{x})\cdot\dot{y} &= \pdv{U}{y}\cdot\dot{y} \\ \ddot{z}\cdot\dot{z} &= \pdv{U}{z}\cdot\dot{z} \end{aligned}$$

그리고 세 식을 전부 더한다.

$$\dot{x}\ddot{x}+\dot{y}\ddot{y}+\dot{z}\ddot{z}=\pdv{U}{x}\pdv{x}{t}+\pdv{U}{y}\pdv{y}{t}+\pdv{U}{z}\pdv{z}{t}=\odv{U}{t}$$

$V^2=\dot{x}^2+\dot{y}^2+\dot{z}^2$임을 유념해 양변을 적분해보자. 

$$\frac{1}{2}\left(\dot{x}^2+\dot{y}^2+\dot{z}^2\right)=\frac{1}{2}V^2=U+\mathrm{const.}$$

정리하면 다음과 같은 중요한 식을 얻는다.

$$V^2 = 2U - C_J \ge 0$$

여기서 $C_J$는 **야코비 상수**라 하며, 그 값은 다음과 같고 그 값은 일정하다.

$$C_J = n^2(x^2+y^2)+2\left(\frac{\mu_1}{r_1}+\frac{\mu_2}{r_2}\right)-V^2$$

즉, 에너지 보존과 같이 쓸만한 상수값을 찾아냈다! 또한, 위 식을 살펴보면 $2U=C_J$인 곳에서는 회전좌표계에서의 순간 속력이 $V=0$이다. 따라서 이 식은 주어진 $C_J$에서 운동 가능한 영역과 불가능한 영역의 경계를 이루는 영속도 곡면을 의미한다.

## 티세랑 파라미터
