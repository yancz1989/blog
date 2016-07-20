# Topics on optimization

This is a series of topics on optimization which I wanted to summarize for a long time. For most machine learning and engineering applications, optimization has vital importance, for which a better optimization algorithm will lead better experiments results. Topics I will talk about are listed below:

1. Essentials, containing basic concepts, notations;
2. Basic theory, including Farkas theorem, Slater theorem, KKT theorem, Theorem of alternatives.
3. Naive optimization algorithms like gradient descent, Newton method, interior-point methods.
4. Optimization algorithms for sparse learning, focusing on ISTAs, Bregmens, etc.
5. Optimization algorithms for deep learning,
6. Distributed or parallel optimization algorithms.

The first 3 parts mainly comes from the book **Convex Optimization** by Stephen Boyd and Lieven Vandenberghe, including the charts and figures. This very first article is the first part of this series contain 1 and 2.

## Basic Concepts
In mathematics, a optimization problem has the form

\begin{align}
\begin{split}
\min_x&\,\,f_0(x)\\\\
\mathrm{s.t.}&\,\,f_i(x)\leq b_i
\end{split}
\end{align}

Here we call $f_0(x)$ as objective, and $f_i(x)$ as constraints. Optimization problems are divided into 2 categories, convex and non-convex. A convex optimization problem is defined that all the objectives and constraints are convex, which satisfied $$f_i(\alpha x+\beta y)\leq \alpha f_i(x)+\beta f_i(y),\alpha,\beta\geq 0,\alpha+\beta=1, x,y\in\mathbf{R}^n$$ Convex and non-convex optimization mainly differs in the relations between the global optimal and local optimal: if convexity is satisfied, then local optimal is guaranteed to be global optimal, which may be not unique and have multiple global optimal, while under non-convex assumption, one have to deal with local minimals.
![Test](/Users/yancz1989/Documents/working/doc/review/images/0001_segment.png "segments and lines")
In geometry, line, segments can all be defined in form of $y=\theta x_1+(1-\theta)x_2$,$x_1\neq x_2$, with segments assume $0\leq\theta\leq 1$. Affine sets are defined on set $C\subseteq \mathbf{R}^n$ that any 2 distinct points $x_1, x_2$ and $\theta\in \mathbf{R}$, we have $\theta x_1 + (1-\theta)x_2\in C$. Here no assumption on $\theta$ is made. An affine composition is defined as $\theta_1x_1+\theta_2x_2+...+\theta_nx_n$ assume that $\sum_{i=1}^n\theta_i=1$. The affine hull of $C$ denoted as$$\mathbf{aff}C= \\{ \theta_1x_1+\theta_2x_2+...\theta_kx_k|x_i\in C,\sum_{i=1}^k\theta_i=1\\}$$which is the smallest affine set that contains C, that is, if S is a affine set and $C\subseteq S$, then $\mathbf{aff}C\subseteq S$.
Here another important concept is relative interior, defined as$$\mathbf{relint}C=\\{x\in C|B(x, r)\cap \mathbf{aff}C\subseteq C for some r>0\\}$$in which $B(x, r)$ is a ball centered at $x$, and have radius $r$.
![Test](/Users/yancz1989/Documents/working/doc/review/images/0002_convex.png "segments and lines")
For convex set, just add one more requirement of $0\leq\theta\leq 1$, and for any point $x_1, x_2$, $\theta x_1+(1-\theta)x_2\in C$. A hyperplane is defined as $\\{x|a^Tx=b\\}$. A hyperplane divide the entire space into 2 halves $\\{x|a^Tx>b\\}$ and its complement. For cones, $\theta_1, \theta_2\geq 0, x_1,x_2\in C$, there is $\theta_1x_1+\theta_2x_2\in C$. In case of cone combination $\theta_1x_1+\theta_2x_2+...+\theta_kx_k$ with $\theta_i\geq0$.

These concepts are basic set concepts for convex optimization. A function $f:\mathbf{R}^n\rightarrow\mathbf{R}$ is called convex if $\mathbf{dom} f$ is a convex set and for $x,y\in\mathbf{dom}f, \theta\in[0,1]$, and have $$f(\theta x+(1-\theta)y)\leq\theta f(x)+(1-\theta)f(y)$$

There are 2 alternative conditions to judge whether a function is convex, which are both sufficient and necessary:

1. First order condition: $f(y)\geq f(x)+\nabla f(x)^T(y-x)$ for all $x,y\in\mathbf{dom}f$ which is a convex set.
2. Second order condition: Hessian matrix of $f$ is semidefinite that for all $x\in\mathbf{dom}f, \nabla^2f(x)\succeq 0$ and $\mathbf{dom}f$ is a convex set.

For any convex function, there is a famous and useful inequality called Jensen's inequality, states that:$$
f(\theta_1x_1+...+\theta_kx_k)\leq\theta_1f(x_1)+...+\theta_kf(x_k),\theta_1+...+theta_k=1
$$This can be extended to infinite sums, integrals, etc.
The last concept is conjugate function. Given $f: \mathbf{R}^N\rightarrow\mathbf{R}$ and $f^\*: \mathbf{R}^N\rightarrow\mathbf{R}$, if$$f^\*(y)=\sup_{x\in\mathbf{dom}f}(y^Tx-f(x))$$ Then $f^*$ is called the conjugate function of function $f$.

## Theory and theorems
In brief, there are 5 important theorems need to be stressed.
### Separating Hyperplane Theorem
This theorem states that imagine 2 convex sets C and D with no intersection. There exist $a\neq0$ and b such that for all $x\in C$ satisfy $a^Tx\leq b$ and $a^Tx\geq b$ for all $x\in D$. This also means that the affine function is negative and positive on each set.
Here we briefly prove this. Since C and D are disjoint sets, we assume their distance as $\mathbf{dist}(C,D)=\inf\\{||u-v||_2|u\in C,v\in D\\}$. Assume point c and d achieve the minimum distance, define $a=d-c$, $b=(||d||^2_2-||c||_2^2)/2$, then we have $f(x)=a^Tx-b=(d-c)^T(x-(1/2)(d+c))$ which is positive and negative on each set.
### Support Hyperplane Theorem
To specify this theorem, first we define boundary of set as $\mathbf{bd}C=\mathbf{cl}C\backslash \mathbf{int}C$. A support hyperplane to C at point $x_0$ is that the point $x_0$ and the set C are separated by hyperplane. A theorem called support hyperplane theorem states that for any nonempty convex C, and point $x_0\in \mathbf{bd}C$, there exists a supporting hyperplane at point $x_0$. Note that this theorem doesn't states there exists only one such hyperplane. You can easily find a opposite example of $\ell_1$ norm ball. And the converse theorem also stands: If a set is closed, has nonempty interior, and has a support hyperplane at each point on its boundary, it is convex.
The importance of supporting hyperplanes theorem is that it explain the convex in a supporting hyperplane way. From the converse theorem, one can conclude that the convex set is formed with a close set of points and the intersection of halves generated by its supporting hyperplanes.
### Farkas Theorem

### Slater Condition

### KKT Condition

### Theorem of Alternatives
