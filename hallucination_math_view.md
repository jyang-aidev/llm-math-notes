# Why Hallucination Cannot Be Eliminated: A Mathematical View

## Abstract

Large Language Models (LLMs) often produce outputs that are fluent but factually incorrect, commonly referred to as *hallucinations*. While many practical methods attempt to reduce hallucinations (e.g., retrieval augmentation, RLHF, tool use), this article argues that hallucination is not merely an engineering flaw but a *mathematical inevitability* rooted in how LLMs are trained and optimized. We provide an intuitive yet rigorous explanation grounded in probability theory, information theory, and optimization.

---

## 1. What Is Hallucination?

An LLM generates text by modeling a probability distribution:

[
P(x_t \mid x_1, x_2, ..., x_{t-1})
]

At each step, the model selects the next token based on this learned distribution. A *hallucination* occurs when:

> The generated token sequence has high probability under the model but does not correspond to factual reality.

This immediately suggests a key insight:

> The model is optimizing for *likelihood*, not *truth*.

---

## 2. Training Objective: Likelihood, Not Truth

LLMs are trained using Maximum Likelihood Estimation (MLE), minimizing:

[
\mathcal{L} = -\mathbb{E}*{(x_1,...,x_T) \sim D} \sum*{t=1}^T \log P_\theta(x_t \mid x_{<t})
]

This objective has two important consequences:

1. The model approximates the *data distribution*, not ground truth.
2. If the data contains noise, ambiguity, or contradictions, the model will learn a *smoothed mixture* of them.

Thus, even a perfectly optimized model will reproduce inconsistencies present in the data.

---

## 3. Distribution Approximation Error Is Unavoidable

Let the true data distribution be (P^*(x)), and the model distribution be (P_\theta(x)).

Training minimizes KL divergence:

[
D_{KL}(P^* | P_\theta)
]

However:

* The model has **finite capacity**
* The dataset is **finite and biased**
* Optimization is **imperfect**

Therefore:

[
P_\theta(x) \neq P^*(x)
]

Even with infinite compute, if the data itself is incomplete or inconsistent, the learned distribution cannot perfectly represent reality.

---

## 4. Ambiguity in Language Implies Multiple Valid Outputs

Natural language is inherently ambiguous. For many prompts, there is no single correct answer.

Example:

"What is the best programming language?"

There is no ground-truth label. The model learns a distribution over *opinions*.

Now consider a factual-looking query where the model lacks knowledge:

"What is the population of City X in 1893?"

If this is rare or missing in training data, the model will still produce a token sequence by sampling from nearby patterns.

Mathematically, this is *extrapolation under uncertainty*, not retrieval.

---

## 5. Softmax Forces a Choice (Even When Uncertain)

At each step, token probabilities are computed via softmax:

[
P(x_t = i) = \frac{e^{z_i}}{\sum_j e^{z_j}}
]

Key implication:

> The probabilities must sum to 1.

This means:

* The model **cannot say “I don’t know” unless explicitly trained to do so**
* Even low-confidence predictions must distribute probability mass

Thus, when the model is uncertain, it still produces a *confident-looking output*

---

## 6. Information-Theoretic Perspective

From information theory, we can think of the model as compressing the dataset into parameters.

Let:

* Dataset entropy: (H(D))
* Model capacity: (C)

If:

[
C < H(D)
]

Then information is lost during compression.

This loss manifests as:

* Missing facts
* Blended patterns
* Approximate reconstructions

Hallucination is one form of *lossy reconstruction error*.

---

## 7. Optimization Bias Toward Fluency

The loss function penalizes unlikely tokens, not incorrect facts.

Consider two outputs:

1. Grammatically perfect but wrong
2. Hesitant or incomplete but correct

MLE typically prefers (1) because:

* It matches training patterns better
* It has higher likelihood under the model

Thus, the optimization process implicitly favors *fluency over truth*.

---

## 8. RLHF Does Not Solve the Core Problem

Reinforcement Learning from Human Feedback (RLHF) modifies the objective:

[
\max_\theta \mathbb{E}[R(x)]
]

But:

* Human feedback is sparse and subjective
* Reward models are themselves approximations

Thus RLHF shifts behavior but does not eliminate:

* distribution mismatch
* uncertainty
* information loss

---

## 9. Formalizing Hallucination as Bayesian Uncertainty

We can reinterpret generation as:

[
P(x \mid \text{context}, \theta)
]

But the true objective is:

[
P(x \mid \text{context}, \text{world})
]

Since the model only approximates the world through data, there is always epistemic uncertainty.

When uncertainty is high, the model samples from a broad distribution — increasing the chance of hallucination.

---

## 10. Why It Cannot Be Eliminated

Combining the above:

Hallucination arises from:

1. Objective mismatch (likelihood vs truth)
2. Imperfect data
3. Finite model capacity
4. Language ambiguity
5. Forced probabilistic normalization (softmax)

Even in an idealized scenario, these factors cannot all be removed simultaneously.

Therefore:

> Hallucination is not a bug, but a fundamental property of probabilistic language modeling.

---

## 11. What We *Can* Do Instead

While elimination is impossible, mitigation is effective:

* Retrieval-Augmented Generation (RAG)
* Tool use (search, calculators, databases)
* Uncertainty estimation
* Calibration techniques
* Structured decoding (e.g., constrained generation)

These methods reduce hallucination by *injecting external grounding*.

---

## 12. Key Takeaway

LLMs do not "know" facts — they approximate distributions over text.

When we ask for truth, we are querying a system optimized for probability.

The gap between these two is where hallucination lives.

---

## References (Suggested)

* Vaswani et al., 2017 — Attention Is All You Need
* Goodfellow et al., 2016 — Deep Learning
* Bishop — Pattern Recognition and Machine Learning
* OpenAI / Anthropic technical blogs on LLM behavior

---

## Author Note

If you found this helpful, consider connecting or discussing further improvements, especially around:

* uncertainty-aware decoding
* hybrid symbolic + neural systems
* evaluation benchmarks for hallucination
