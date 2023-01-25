---
title: "Historical notes on GPT architecture"
description: "Technical history of GPT from an architectural viewpoint. Some industry-level read-throughs."
date: 2023-01-22
layout: post
usemathjax: true
---

_This document is a work in progress.
While structured as an explanatory article,
it's mostly a log of my investigations into LLM architecture.
Sections may appear or disappear randomly.
Proceed with caution._

#### 2017: Transformer

Here is the canonical transformer diagram, from Google Brain's "Attention Is All You Need" (2017):

![](/assets/images/transformer_arch.png)

It's rather confusing that this diagram is canonical,
because the most prominent use case of the transformer architecture is GPT,
which it doesn't actually describe.

We need to remember that the transformer architecture was originally designed to perform _sequence transformation_,
not language modeling;
like seq2seq, the model described in
"Attention Is All You Need"
aims to learn a generative model $$\mathcal{X} \rightarrow p(\mathcal{Y})$$
that maps input sequences $$x$$ to distributions over output sequences $$y$$.

This is a semi-supervised learning problem
over datasets $$\{(x^{(i)}, y^{(i)})\}$$
where $$y^{(i)} \sim p(y|x^{(i)})$$.
The architecture above takes in an example $$(x^{(i)}, y^{(i)})$$ and outputs a vector $$\hat p^{(i)}$$ which represents an autoregressive model of $$p(y^{(i)}|x^{(i)})$$ where $$\hat p^{(i)}_j \triangleq p^{(i)}(y^{(i)}_j | x^{(i)}, y^{(i)}_{<j})$$.
This lets us optimize some loss $$\ell(\hat p)$$ to train a model.

At inference time, we can compute the embedding of the input and then use it to autoregressively sample $$y \sim \hat p(y\vert x)$$.
This two-step procedure is why the part of the network on the left, which is evaluated once to embed $$x$$, is known as the _encoder_,
and the part on the right, which is evaluated repeatedly to sample autoregressively from $$\hat p(y|x)$$, is known as the _decoder_.

The real development of the paper,
and the defining characteristic of a transformer model,
is that it encodes and decodes sequences using masked ("causal") self-attention
instead of recurrence.
This fixes the depth of the computation graph,
enabling parallelization in training.

> The Transformer allows for significantly more parallelization and can reach a new state of the art in
> translation quality after being trained for as little as twelve hours on eight P100 GPUs.

---

#### 2018: Generative pretraining

The architectural idea behind generative pretraining (GPT), which was introduced in OpenAI's "Improving Language Understanding With Unsupervised Learning" (2018),
is that language modeling is a special case of sequence transformation.
In particular, we imagine that $$\mathcal{X}$$ is irrelevant,
or that it has only one element;
then our training examples take the form $$y^{(i)}$$ and our models compute
$$\hat p(y_j|y_{<j})$$.

When we knock out the inputs $$x$$, we arrive at this simple, "decoder-only" architecture:

![](/assets/images/gpt_arch.png)

This perfectly matches the official diagram of GPT.

![](/assets/images/gpt_arch_2.png)

The main idea of the GPT paper is that because language modeling entails comprehension and can be learned from a vast amount of open-source data,
it is an excellent "unsupervised pretraining" task for models requiring comprehension.
Models finetuned from GPT weights perform well on "classical" NLP tasks:

> Overall, our approach achieves new state-of-the-art results in 9 out of the 12 datasets we evaluate
> on, outperforming ensembles in many cases.
> Our results also indicate that our approach works well
> across datasets of different sizes, from smaller datasets such as STS-B (~5.7k training examples) –
> to the largest one – SNLI (~550k training examples).

---

#### 2019-2022: Parameter scaling &c.

OpenAI burst into the public spotlight
with the release of GPT-2 in 2019.
Ironically, it's not clear to me that there have been many significant, discrete leaps in LLM architecture since 2018 besides the proper residualization of the transformer blocks.
From "Language Models are Unsupervised Multitask Learners" (2019):

> Layer normalization (Ba et al., 2016)
> was moved to the input of each sub-block, similar to a
> pre-activation residual network (He et al., 2016) and an
> additional layer normalization was added after the final self-attention block. A modified initialization which accounts
> for the accumulation on the residual path with model depth
> is used. We scale the weights of residual layers at initialization by a factor of $$1/\sqrt{N}$$ where $$N$$ is the number of
> residual layers.

And from "Language Models are Few-Shot Learners" (2020):

> We use the same model and architecture as GPT-2, including the modified initialization, pre-normalization,
> and reversible tokenization described therein, with the exception that we use alternating dense and locally banded sparse
> attention patterns in the layers of the transformer, similar to the Sparse Transformer.

What we know about GPT-3, then, is that it uses:

- normalized weight initializations ("modified initialization")
- different placements of the layer normalizations ("pre-normalization")
- attention sparsity ("alternating dense and locally banded sparse attention patterns")

![](/assets/images/prenorm.png)

The use of prenormalization (b) instead of postnormalization (a)
in transformers
has been a bit of folk wisdom since 2017,
and is mentioned in the 2019 Sparse Transformer paper.
Microsoft Research Asia has a solid paper,
"On Layer Normalization in the Transformer Architecture" (2020),
which justifies this in terms of gradient norms.
But in truth,
a proper reading of the original ResNet paper indicates that
prenormalization, shown as (b) in the diagram below,
was always better aligned with the principles of
the original Microsoft Research paper on ResNets,
in light of which the paper from 2020 seems rather
like a perfunctory application of a general principle
to a specific case.

The normalization of weight initializations is motivated by
the same body of literature,
which aims to control vanishing and exploding gradients.
Might be worth a deeper look.

Attention sparsity is a variant of the causal attention pattern
which computes only a subset of the elements of the attention matrix.
This saves time and memory in a fairly predictable way, and enables
the modeling of long sequences.
It still appears that GPT-3 uses the dense attention pattern
for half of its layers.

While a number of developments in performance optimization have occurred
since 2019, they're outside the scope of this post.
Instead, for the rest of this section I'll focus on the main current
propelling AI progress today: parameter scaling.

Where are all those parameters, anyway? Let's break it down, wrapping $$b$$ into $$W$$ for simplicity. The model consists of

- Token embeddings $$W_{te} \in \mathbb{R}^{d_m \times n_v}$$
- Positional embeddings $$W_{pe} \in \mathbb{R}^{d_m \times n_b}$$
- $$n_\ell$$ transformer layers, each of which has:
  - a residual attention block, which has:
    - $$n_h$$ linear projections $$W^Q_i, W^K_i, W^V_i \in \mathbb{R}^{(d_m/n_h) \times (d_m + 1)}$$
    - another linear projection $$W^O \in \mathbb{R}^{d_m \times (d_m + 1)}$$ of the concatenated heads
  - a residual MLP block, which has:
    - a layer with weights $$W^1 \in \mathbb{R}^{4d_m \times (d_m + 1)}$$
    - a layer with weights $$W^2 \in \mathbb{R}^{d_m \times (4d_m + 1)}$$

where

- $$n_\ell$$ is the number of transformer layers,
- $$n_h$$ is the number of heads,
- $$d_m$$ is the embedding size, and is divisible by $$n_h$$,
- $$n_b$$ is the block size, or maximum sequence length, and
- $$n_v$$ is the vocabulary size,

so the total number of parameters is

$$d_mn_v + d_mn_b \\ + n_\ell\left(4d_m(d_m+1) + 4d_m(d_m+1) + d_m(4d_m + 1) \right),$$

which simplifies to

$$
d_mn_v + d_mn_b + 12n_\ell d_m^2 + 9n_\ell d_m.
$$

Let's check our work. We have the following table from the GPT-3 paper:

![](/assets/images/gpt_table.png)

We reproduce these results almost exactly, with $$n_v = 50257$$ and $$n_b = 2048$$:

| Model  | $$n_l$$ | $$n_h$$ | $$d_{m}$$ | $$n_{params}$$  |
| ------ | ------- | ------- | --------- | --------------- |
| Small  | 12      | 12      | 768       | 125 187 840     |
| Medium | 24      | 16      | 1024      | 355 771 392     |
| Large  | 24      | 16      | 1536      | 760 149 504     |
| XL     | 24      | 24      | 2048      | 1 315 522 560   |
| 2.7B   | 32      | 32      | 2560      | 2 651 220 480   |
| 6.7B   | 32      | 32      | 4096      | 6 657 871 872   |
| 13B    | 40      | 40      | 5140      | 12 952 106 100  |
| 175B   | 96      | 96      | 12288     | 174 599 516 160 |

We could also break down the results by component:

| Model  | $$n_{params}$$ | Attn   | FFN    | $$W_{te}$$ | $$W_{pe}$$ |
| ------ | -------------- | ------ | ------ | ---------- | ---------- |
| Small  | 1.25E+08       | 22.64% | 45.27% | 30.83%     | 1.26%      |
| Medium | 3.56E+08       | 28.32% | 56.62% | 14.47%     | 0.59%      |
| Large  | 7.60E+08       | 29.82% | 59.62% | 10.16%     | 0.41%      |
| XL     | 1.32E+09       | 30.62% | 61.23% | 7.82%      | 0.32%      |
| 2.7B   | 2.65E+09       | 31.65% | 63.30% | 4.85%      | 0.20%      |
| 6.7B   | 6.66E+09       | 32.26% | 64.52% | 3.09%      | 0.13%      |
| 13B    | 1.30E+10       | 32.64% | 65.28% | 1.99%      | 0.08%      |
| 175B   | 1.75E+11       | 33.21% | 66.42% | 0.35%      | 0.01%      |

Generally speaking, the feed-forward networks will have almost twice as many parameters
as the attention blocks.
This is because the attention blocks
have
$$n_\ell(4d_m^2 + 4d_m)$$ parameters,
and the feed-forward networks have
$$n_\ell(8d_m^2 + 5d_m)$$.
Thus the result follows because

$$\lim_{d_n \rightarrow \infty} \frac{n_\ell(4d_m^2 + 4d_m)}{n_\ell(8d_m^2 + 5d_m)} = 1/2.$$

---

#### 2023: The direction of the field

It would be misleading to say that parameter scaling is the _only_ thing OpenAI
has accomplished technologically;
efficient training and inference of massively scaled models is a significant
technical challenge requiring a good amount of clever engineering.

At the same time, we shouldn't overstate the degree to which that engineering was done
at OpenAI;
OpenAI employees have claimed credit for _e.g._ the prenormalized architecture,
which predates even the publication of "Attention Is All You Need,"
and a number of the performance optimizations were discovered outside OpenAI itself.

When faced with the observations that

- besides sparse attention,
  in-house technological achievements from OpenAI itself seem relatively few,
- OpenAI executives regularly speak of the enormous efficacy of its employees
  and the outcomes that small teams can attain, and
- employees at OpenAI have a penchant for spinning off into their own competitor corporations, _e.g._ Anthropic, and raising obscene amounts,

Occam's Razor would suggest that most of the organization's comparative
advantage comes from its unique position with respect to the capital markets,
that — despite protests to the contrary —
it is still mostly true that "Google invented the transformer and OpenAI jsut scaled it up,"
and that the employees who leave to start competitors are aware of this.

If scaling and the engineering it requires are the main accomplishments of OpenAI
since 2018, it seems clear that one can still build
a competitor LLM company by combining preferential access to the capital markets
with a keen and persistent bureaucratic acumen.
We are fortunate in this respsect to be in the early stages of this technology,
where most work is still open-source
and organizations are only beginning to reorient from splashy public demos
to the industrial production of secrets.
But as intellectual capital continues to form behind closed doors,
upstarts will have an increasingly difficult time competing.

The analogy to semiconductor fabs is compelling but flawed.
AI progress is sped along by the tailwinds of hardware advancements and Moore's law,
but there is no Moore's law for semiconductor capital equipment itself;
this latter fact alone justifies TSMC's enormous capex
and its symbiotic relationship with Apple.
Instead,
we should expect the systems layer of the LLM industry to increasingly resemble
the quantitative trading industry:
a dynamic landscape where a few players
compete to build fast-moving organizations
that can produce short-lived technical secrets on a regular basis,
and where the knowledge of how to build such organizations
can only be learned from within one.

In the limit, the power of the organization
all falls down to the executive's ability to raise capital and apply standards.
Who, then, shall contend with Sam Altman?

---

#### Appendix: the experts weigh in

![](/assets/images/horace.png)
