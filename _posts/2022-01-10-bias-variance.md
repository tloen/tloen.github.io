---
title: "The bias-variance tradeoff is not a statistical concept"
description: "Quick note on certain strawmen of statistical learning theory."
date: 2023-01-10
layout: post
usemathjax: true
---

Some recent work in the theory of deep learning has been devoted to understanding
the "model-wise double-descent" phenomenon empirically observed in deep models[^openai2019].
Within certain families of deep models, trained under certain conditions,
increasing the model's expressivity leads to the following series of stages:

- First, train and test loss fall simultaneously.
- Then, test loss begins to rise.
- Then (uniquely for deep models), test loss begins to fall again.

The OpenAI paper, written in 2019,
terms the first two stages the "classical regime,"
and the last the "modern regime."

![](/assets/images/double_descent.png)

While the authors of the paper are careful not to overplay their hand,
commentators have claimed that the existence of the modern regime in double descent
contradicts the "bias-variance tradeoff" predicted by statistical learning theory.
If a statistical theorem could be falsified and contradicted like this,
it would imply that stastistics is a nonrigorous science.
But while a "bias-variance tradeoff" does exist in statistics,
in itself it says nothing at all.

---

Let us demonstrate this by constructing the bias-variance tradeoff from first principles.
Recall that supervised learning is function approximation by example.

- _Function approximation_ means we want to produce a function $$\hat f: \mathcal X \rightarrow \mathcal Y$$
  that approximates a ground truth $$f: \mathcal X \rightarrow \mathcal Y$$.
- _By example_ means we want to do so using a dataset $$D \in \mathcal{D}$$ of examples
  $$(x, y)$$ where $$y$$ is related in some way to $$f(x)$$.
- Thus a supervised learning predictor takes the form $$\hat f: (\mathcal X \times \mathcal D) \rightarrow \mathcal Y$$.

Consider a parametric family of predictors
$$F = \{ \hat f_{\lambda} : \lambda \geq 0\}$$.
We evaluate these predictors by the mean squared error,
which admits the well-known _bias-variance decomposition_:

$$
\begin{align}
MSE(\hat f_{\lambda})
&\triangleq E_{x,D}[(\hat f_{\lambda}(x;D) - f(x))^2] \\
&= (E_{x,D}[\hat f_{\lambda}(x;D) - f(x)])^2 + E_x Var_D[\hat f_{\lambda}(x;D)] \\
&= Bias[\hat f_\lambda]^2 + Variance[\hat f_\lambda].
\end{align}
$$

Note that our learning process is a black box;
we have made no claims so far about the relationship between $$f(x)$$ and $$y$$,
or about the relationship between $$\lambda$$ and $$\hat f_\lambda$$.
Nevertheless, we can still plot $$F$$'s bias against its variance; consider the graph $$G(F) \subseteq \mathbb{R}_{\geq 0}^2$$, which we define as

$$\left\{\left(Bias[\hat f_\lambda]^2, Variance[\hat f_\lambda] \right) : \lambda \geq 0\right\}.$$

With the paucity of assumptions made so far,
we can say very little about the shape of $$G(F)$$;
we simply have a scattering of points in the upper quadrant.
But this is all we need to construct the bias-variance tradeoff.
Consider the functions

$$
\begin{align}
b_F(v) &= \inf \left\{Bias[\hat f_\lambda]^2: \lambda \geq 0, Variance[\hat f_\lambda] \leq v\right\}, \\
v_F(b) &= \inf \left\{Variance[\hat f_\lambda]: \lambda \geq 0, Bias[\hat f_\lambda]^2 \leq b\right\}
\end{align}
$$

where $$\inf {\emptyset} = +\infty$$.
The functions represent the "best" bias that can be attained
below a certain variance, and vice versa.
It is easy to show, based only on the fact that $$G(F) \subseteq \mathbb{R}^2_{\geq 0}$$,
that both functions
are nonincreasing;
in other words, the "best" variance attainable at a given maximum bias
can only improve if we increase the maximum bias,
and the "best" bias attainable at a given maximum variance
can only improve if we increase the maximum variance.

![](/assets/images/frontier_2.png)

Insofar as a universal "bias-variance tradeoff" exists, it consists precisely in this nonincreasingness; to do "better" than the "best" predictor on one axis, we need to compromise on the other.
But we have arrived at it assuming practically nothing here
about $$\hat f_\lambda$$ or $$P(x, y)$$ or $$\mathcal{D}$$;
moreover, we didn't even use any mathematical property
of bias or variance except that both are nonnegative.

A "bias-variance tradeoff" exists in statistics
in the same sense that a "risk-return spectrum" exists in finance;
that is to say, it follows analytically from our framing of the problem
in terms of efficient frontiers.
It is not a deep statistical truth about how two quantities interact.

---

Why, then, has the bias-variance tradeoff come to be associated
with a collection of wrong intuitions about model complexity?
For a number of specific predictor families $$F$$ studied in classical statistics,
the following additional properties can be demonstrated:

- $$Var_D[\hat f_{\lambda}(x;D)]$$ is nondecreasing in $$\lambda$$ for all $$x$$.
- $$(E_{x,D}[\hat f_{\lambda}(x;D) - f(x)])^2$$ is nondecreasing in $$\lambda$$ for all $$x$$.

Under these conditions, $$\lambda$$ comes to represent some concept of "regularization,"
and the inverse of $$\lambda$$ comes to represent some concept of "complexity."
We can see this move in studies of lasso and ridge regression, as well as _k_-nearest neighbors models.

Moreover, as bias and variance can be computed for any form of predictor,
they are often used to make comparisons between different families of predictors,
_e.g._ OLS and _k_-NN.
While numerous clear-cut situations can be constructed
where one family clearly dominates the other
in both metrics, comparisons between families will often focus on "murky"
situations where both families have members on the efficient frontier.

![](/assets/images/classical.png)

All of this creates the false impression that the bias-variance tradeoff
refers to some sort of natural limit on the performance of any statistical predictors,
and that $$G(F)$$ is a curve parametrized by some measure of "model complexity."
After all, it is true that in such situations, the right choice of model family
and value of $$\lambda$$ (or of "complexity")
is coextensive with the relative priority that one
assigns to bias and variance.
But there is no indication that the same relationships between complexity, bias, and variance apply to deep neural networks.

The phenomenon of model double descent does not demonstrate that
classical learning theory has been "invalidated" in some sense.
What it really teaches us is that
we should stop abusing the bias-variance tradeoff,
which exists for all predictor families,
to smuggle in intuition from our experience with regularized regression models.
At the same time, it does raise valid questions
about whether the bias-variance decomposition
is particularly relevant
in non-regressive or high-dimensional contexts where dataset variance
is difficult to reason about.

[^openai2019]: [Nakkiran _et al._ 2019](https://arxiv.org/abs/1912.02292)
