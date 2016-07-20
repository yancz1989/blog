---

layout: post
title: Diffusion Model and Ito Lemma
modified:
categories: ['Finance Quantitative']
excerpt:
tags: []
image:
feature:

date: 2015-08-07T09:09:22+08:00
---

1.	Diffusion Model $$ X*{t+1}=\rho X_t + \sigma\epsilon*{t+1}, E*t(X*{t+i})=\rho^i X*t,\\ X_t=\sum*{j=0}^\infty{\rho^{j}\sigma\epsilon*{t-j}}\Rightarrow \sigma^2_t(X*{t+1})=(1 + \rho^2 + ...+\rho^{2j})\sigma^2 $$
2.	Random Walk
3.	Discrete Case:$$Z*t - Z_0 = \sum*{j=1}^t\epsilon_{t-j}, E(Z_t-Z_0)=0, \sigma(Z_t-Z_0)=t$$
4.	Continous Case For continous case, the theory become more flexible, hence have:
$$Z*{t+\Delta}-Z_t\sim N(0,\Delta)$$
$$\Rightarrow \lim_{\Delta\rightarrow 0}(Z*{t+\Delta}-Z_t)\sim\sqrt{dt}\Rightarrow \frac{dz}{dt}=\frac{1}{\sqrt{dt}}$$
$$E_t(dZ_t)=0\Leftrightarrow E_t(\epsilon_t)=0, var_t(dZ_t)=E_t(dZ_t^2)=dZ^2=dt$$

Substitute to the diffusion model, we have:
$$ X*{t+1}-X*t=\mu+\sigma\epsilon*{t-1}\Rightarrow dX*t=\mu dt+\sigma dZ_t,\\ E_t(dX_t)=\mu d_t dX_t^2=\mu^2dt^2+\sigma^2dt+2\mu dtdz=0\\ \Rightarrow \sigma^2_t(dX_t)=E_t(dX_t^2)-\sigma^2dt\Leftrightarrow \frac{dP_t}{P_t}=\mu dt+\sigma^2 dt $$ take $$\Phi==1-\rho$$, we have $$X*{t+1}-X*t=-\Phi X_t+\sigma\epsilon*{t-1}, dX_t==\Phi X_tdt+\sigma dZ_t$$

Next is the Ito lamma

Remember, the only difference between stochastic calculus and ordinary calculus is we take 2nd order taylor expansion for the reason that the second order of taylor express the variance part of stochastic calculus.

$$ dX_t=\mu dt+\sigma dZ_t, dy_t=\frac{\partial f}{\partial X}dX_t+\frac{1}{2}\frac{\partial^2f}{\partial X^2}dX_t^2=\sigma^2dZ^2=\sigma^2dt $$

For example, $$y_t=\log P_t$$, we have $$dy_t=\frac{1}{P_t}dP_t-\frac{1}{2}\frac{1}{P^2_t}dP_t^2=\mu dt+\sigma dZ_t-\frac{1}{2}\sigma^2dt=(\mu-\frac{1}{2}\sigma^2)dt+\sigma dZ_t$$

Another example has 2 variable $$X_t, Y_t$$, we have:

$$d(X_t,Y_t)=\frac{\partial xy}{\partial x}dX_t+\frac{\partial xy}{\partial y}dY_t+\frac{1}{2}\frac{\partial^2(xy)}{\partial x\partial y}dXdY\\ =Y_tdX_t+X_tdY_t+dX_tY_t$$
