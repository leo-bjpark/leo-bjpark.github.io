---
layout: post
title: Causal Abstraction as Intervention-Aligned Representation Search
permalink: /blog/2025/causality-interpretability-en/
mini-title: Interpretability Geometry
---

## 1. From Decodable Information to Causal Structure

A common question in neural network interpretability is whether a hidden representation contains an interpretable concept. Suppose, for example, that a linear probe can predict whether a sentence contains negation from an internal representation $h$. This tells us that information about negation is available in $h$. It does not, however, establish that the decoded information plays the causal role that negation is supposed to play in the model's computation.

This distinction matters because information can be present without being causally responsible for a behavior. A hidden state may contain many variables that are correlated with the model's output, including variables that are consequences of earlier computation, redundant copies of other information, or features that happen to be predictive without participating in the hypothesized mechanism.

**Causal abstraction** asks for a stronger relationship. Rather than asking whether an interpretable variable can be decoded from a neural representation, it asks whether an interpretable causal model describes how the neural system responds to interventions. <citation key="geiger2024finding"></citation>

Let $\mathcal{H}$ denote a **high-level causal model** whose variables have an interpretable meaning, and let $\mathcal{L}$ denote a **low-level causal model**, such as a neural network. The high-level model describes a computation using variables such as equality, negation, or lexical entailment, whereas the low-level model describes the actual internal states and mechanisms of the neural system.

An alignment between the two models specifies how variables at the high level correspond to states at the low level. In the paper, an alignment is written as

$$
\Pi =
\left(
\{\Pi_X\}_X,
\{\tau_X\}_X
\right).
$$

Here, $X$ denotes a high-level variable, $\Pi_X$ denotes the low-level variables associated with $X$, and $\tau_X$ maps values of those low-level variables into values of the high-level variable. Collectively, these mappings define an abstraction map $\tau$ between the low-level and high-level descriptions.

The crucial requirement is that the correspondence must continue to hold when the system is changed.

For a low-level input $x$, an intervention $I \leftarrow i$ fixes a set of low-level variables $I$ to values $i$. Constructive causal abstraction requires

$$
\tau\left(
\mathcal{L}_{I\leftarrow i}(x)
\right)
=
\mathcal{H}_{\tau(I\leftarrow i)}
\left(
\tau(x)
\right).
$$

Here, $\mathcal{L}_{I\leftarrow i}(x)$ is the low-level state obtained by running $\mathcal{L}$ on $x$ while intervening on $I$, and $\tau(\mathcal{L}_{I\leftarrow i}(x))$ is its corresponding high-level description. On the right-hand side, $\tau(x)$ is the high-level version of the input, while $\tau(I\leftarrow i)$ is the corresponding high-level intervention.

The equation therefore compares two paths:

$$
\begin{aligned}
&\text{intervene at the low level}
\rightarrow
\text{abstract to the high level},
\\
&\text{abstract to the high level}
\rightarrow
\text{intervene at the high level}.
\end{aligned}
$$

A faithful causal abstraction requires these two paths to agree.

$$
\boxed{
\text{Corresponding interventions must produce corresponding effects.}
}
$$

This is substantially stronger than ordinary probing. A probe asks whether a neural state contains enough information to predict a high-level variable. Causal abstraction asks whether manipulating the proposed neural realization of that variable reproduces the consequences predicted by the high-level causal model.

---

## 2. Interchange Interventions

To test causal correspondence, we need a practical way to intervene on internal variables. The difficulty is that, at the neural level, we usually do not know which numerical activation should represent a high-level intervention such as setting an equality relation to true.

**Interchange intervention** avoids this problem by obtaining the intervention value from another naturally occurring input.

Let $b$ denote a **base input**, and let $s_j$ denote the $j$-th **source input**. Suppose $X_j$ is an intermediate variable whose value we want to replace. Instead of manually choosing a new value for $X_j$, we run the model on $s_j$ and take the value that $X_j$ naturally assumes under that source.

For a causal model $\mathcal{M}$, source inputs $$\{s_j\}_{j=1}^{k}$$, and non-overlapping intermediate variable sets $$\{X_j\}_{j=1}^{k}$$, the interchange intervention is defined by replacing each $X_j$ with the value produced by its corresponding source.

Conceptually,

$$
X_j^{b}
\leftarrow
X_j^{s_j}.
$$

The base input still determines the rest of the computation. Only the selected intermediate variable is replaced.

With two variables, for example,

$$
[
X_1^b,
X_2^b,
X_3^b
]
\rightarrow
[
X_1^{s_1},
X_2^{s_2},
X_3^b
].
$$

The resulting computation is counterfactual. It asks what the model would have done for the base input if selected internal variables had taken the values they would have taken under other source inputs.

This procedure is especially useful for causal abstraction because the same operation can be defined at both levels. At the high level, we know which interpretable variable is being exchanged. At the low level, we attempt to exchange the representation that realizes that variable.

If the proposed alignment is correct, the two interventions should have the same downstream effect.

---

## 3. Why Distributed Representations Require a Change of Basis

A straightforward approach would be to align each high-level variable with a particular neuron or a fixed group of neurons. This assumes that the meaningful causal variables are already aligned with the standard coordinate axes of the neural representation.

There is no reason for this assumption to hold.

Suppose a hidden representation is

$$
h =
\begin{bmatrix}
h_1 \\
h_2
\end{bmatrix},
$$

where $h_1$ and $h_2$ are two neuron activations. A high-level variable $X_1$ may not be represented exclusively by either $h_1$ or $h_2$. Instead, information about $X_1$ and another variable $X_2$ may be mixed across both dimensions.

For instance, the meaningful directions could correspond to linear combinations of the original neurons. In that case, a different coordinate system may expose the relevant structure.

DAS therefore introduces a learned transformation

$$
R : N \rightarrow Y,
$$

where $N$ denotes the selected low-level neural representation space and $Y$ denotes the transformed representation space. In the experiments, $R$ is parameterized as an **orthogonal matrix**, so the transformation acts as a rotation or reflection of the coordinate system rather than an arbitrary rescaling.

For a neural activation $h \in N$, the transformed representation is

$$
y = Rh.
$$

The important point is that $R$ does not retrain the neural model or create new information. It provides a different basis for describing the same representation.

> DAS asks whether a high-level causal structure becomes visible in some alternative basis of the neural representation.

The use of an orthogonal transformation is useful because an orthogonal matrix satisfies

$$
R^\top R = I,
$$

where $R^\top$ is the transpose of $R$ and $I$ is the identity matrix. Consequently,

$$
R^{-1}=R^\top.
$$

An orthogonal transformation preserves inner products and Euclidean lengths. The search therefore focuses on orientation rather than introducing arbitrary scaling into the representation.

This restriction is methodological rather than a claim that neural representations must fundamentally be orthogonal. The authors explicitly note that more general invertible and differentiable transformations could be used in principle. If a high-level variable were encoded on a nonlinear submanifold, for example, a linear orthogonal transformation might be insufficient.

---

## 4. Orthogonal Subspaces for High-Level Variables

After applying $R$, DAS decomposes the transformed space $Y$ into mutually orthogonal subspaces:

$$
Y
=
Y_0
\oplus
Y_1
\oplus
\cdots
\oplus
Y_k.
$$

Here, $\oplus$ denotes an **orthogonal direct sum**. The subspaces together span the relevant transformed representation space, while each $Y_i$ is orthogonal to every $Y_j$ for $i\neq j$.

The intended correspondence is

$$
X_j
\leftrightarrow
Y_j,
\qquad
j=1,\ldots,k,
$$

where $X_j$ is the $j$-th intermediate variable of the high-level model and $Y_j$ is the low-level subspace hypothesized to implement its causal role.

The subspace $Y_0$ has a different role. It represents the part of the base neural state that is **not replaced** during the distributed intervention. It can therefore be understood as a residual or base-preserving subspace.

A high-level variable does not need to correspond to a single direction. Each $Y_j$ may contain many dimensions. In the paper's BERT experiment, for example, high-level variables are aligned with large multidimensional subspaces rather than with individual neurons.

Orthogonality should not be confused with independence. The condition

$$
Y_i \perp Y_j
$$

is a geometric statement about the representation space. It does not imply that the corresponding high-level variables $X_i$ and $X_j$ are statistically independent or causally unrelated.

A high-level causal model may contain relationships such as

$$
X_1
\rightarrow
X_2
\rightarrow
O,
$$

where $O$ denotes the high-level output, while still representing $X_1$ and $X_2$ in orthogonal neural subspaces.

The purpose of the orthogonal decomposition is instead to make the candidate representational components separately projectable and therefore separately intervenable.

---

## 5. Distributed Interchange Intervention

The rotation becomes useful when it is combined with intervention.

Let $F_N$ denote the causal mechanism in the low-level model that produces the value of the target neural variables $N$. Let $v$ denote the values of the causal parents on which $F_N$ depends. Under the original model,

$$
N = F_N(v).
$$

Let $s_1,\ldots,s_k$ denote source inputs. For each source $s_j$, the model $\mathcal{M}$ produces a total state $\mathcal{M}(s_j)$, from which the corresponding target representation can be obtained.

The distributed interchange intervention replaces the original mechanism $F_N$ with

$$
F_N^*(v)
=
R^{-1}
\left(
\operatorname{Proj}_{Y_0}
\left(
R(F_N(v))
\right)
+
\sum_{j=1}^{k}
\operatorname{Proj}_{Y_j}
\left(
R(F_N(\mathcal{M}(s_j)))
\right)
\right).
$$

Here, $F_N^*$ denotes the new mechanism after intervention. The operator $\operatorname{Proj}_{Y_j}$ is the orthogonal projection from $Y$ onto the subspace $Y_j$. The transformation $R$ maps neural representations from $N$ into the rotated space $Y$, while $R^{-1}$ maps the constructed counterfactual representation back into the original neural space.

The first component,

$$
\operatorname{Proj}_{Y_0}
\left(
R(F_N(v))
\right),
$$

comes from the current base computation. It preserves the residual subspace $Y_0$.

Each term

$$
\operatorname{Proj}_{Y_j}
\left(
R(F_N(\mathcal{M}(s_j)))
\right)
$$

extracts the $Y_j$ component from the representation generated by source $s_j$.

For example, suppose the rotated base representation is

$$
R(h_b)
=
[
Y_0^b,
Y_1^b,
Y_2^b
],
$$

where $h_b$ is the low-level hidden state produced by base input $b$.

Two source inputs produce

$$
R(h_{s_1})
=
[
Y_0^{s_1},
Y_1^{s_1},
Y_2^{s_1}
]
$$

and

$$
R(h_{s_2})
=
[
Y_0^{s_2},
Y_1^{s_2},
Y_2^{s_2}
].
$$

The intervention constructs

$$
[
Y_0^b,
Y_1^{s_1},
Y_2^{s_2}
].
$$

The residual component remains from the base, while the two target components come from different sources.

The representation is then mapped back into the original neural coordinate system:

$$
h_{\mathrm{cf}}
=
R^{-1}
[
Y_0^b,
Y_1^{s_1},
Y_2^{s_2}
],
$$

where $h_{\mathrm{cf}}$ denotes the resulting **counterfactual hidden state**.

The remainder of the neural network then continues its ordinary computation from $h_{\mathrm{cf}}$.

This explains why the paper describes the operation as a **soft intervention**. A hard intervention would replace the complete variable with a fixed value and thereby sever its dependence on its causal parents. Distributed interchange intervention preserves the $Y_0$ component produced by the base computation, so part of the original dependence on the parent state $v$ remains intact.

$$
\boxed{
\text{Rotate}
\rightarrow
\text{preserve }Y_0
\rightarrow
\text{replace selected }Y_j
\rightarrow
\text{unrotate}
}
$$

---

## 6. Learning the Rotation from Intervention Effects

The remaining problem is that the correct rotation $R$ is unknown.

This is where **Distributed Alignment Search (DAS)** enters. Instead of manually searching over neuron subsets, DAS parameterizes the rotation as $R^\theta$, where $\theta$ denotes the learnable parameters of the orthogonal transformation.

The high-level causal model provides the supervision.

Let $b$ denote a base input and let $s_1,\ldots,s_k$ denote source inputs. Let $X_1,\ldots,X_k$ denote the high-level intermediate variables that we want to align with low-level subspaces $Y_1,\ldots,Y_k$.

At the high level, we can perform the interchange intervention

$$
\operatorname{II}
\left(
\mathcal{H},
\{\tau(s_j)\}_{j=1}^{k},
\{X_j\}_{j=1}^{k}
\right)
(\tau(b)).
$$

Here, $\operatorname{II}$ denotes the high-level interchange intervention, $\tau(b)$ is the high-level representation of the base input, and $\tau(s_j)$ is the high-level representation of source $s_j$. The operation replaces each high-level variable $X_j$ using the value induced by its corresponding source.

This produces a **high-level counterfactual outcome**.

At the low level, DAS performs the corresponding distributed interchange intervention using the current candidate rotation $R^\theta$:

$$
\operatorname{DII}
\left(
\mathcal{L},
R^\theta,
\{s_j\}_{j=1}^{k},
\{Y_j\}_{j=0}^{k}
\right)
(b).
$$

Here, $\operatorname{DII}$ denotes distributed interchange intervention, $\mathcal{L}$ is the frozen low-level neural model, and $\{Y_j\}_{j=0}^{k}$ is the orthogonal decomposition of the rotated representation space.

DAS learns $\theta$ by minimizing a loss between the counterfactual behavior of these two models:

$$
\sum_{b,s_1,\ldots,s_k \in \mathrm{Inputs}_{\mathcal{L}}}
\operatorname{Loss}
\left(
\operatorname{DII}
\left(
\mathcal{L},
R^\theta,
\{s_j\},
\{Y_j\}
\right)
(b),
\operatorname{II}
\left(
\mathcal{H},
\{\tau(s_j)\},
\{X_j\}
\right)
(\tau(b))
\right).
$$

The set $\mathrm{Inputs}_{\mathcal{L}}$ denotes valid low-level inputs. The function $\operatorname{Loss}$ is a differentiable measure of disagreement between the low-level and high-level counterfactual outcomes.

In the experiments, the authors use cross-entropy between the corresponding high-level output distributions. Importantly, both $\mathcal{L}$ and $\mathcal{H}$ remain fixed during this optimization. **Only the alignment represented by $R^\theta$ is learned.**

This gives DAS its central interpretation.

Suppose that, in the high-level model, replacing $X_1$ with the value from source $s_1$ changes the output from $A$ to $B$. DAS searches for a rotated subspace $Y_1$ such that replacing $Y_1$ with its source value produces the corresponding $A\rightarrow B$ change in the neural model.

If a candidate rotation does not expose the correct subspace, the low-level counterfactual behavior will disagree with the high-level prediction, producing a larger loss. Gradient descent then changes $R^\theta$.

Across many base-source combinations, the optimization searches for a coordinate system in which the relevant low-level interventions reproduce the high-level causal effects.

$$
\boxed{
\text{High-level intervention behavior}
\rightarrow
\text{supervision for learning the low-level alignment}
}
$$

The method therefore does not search for representations by comparing static values. It searches by comparing **how the two systems change under corresponding interventions**.

---

## 7. Interchange Intervention Accuracy

After learning an alignment, DAS evaluates how faithfully the low-level interventions reproduce the corresponding high-level interventions.

This is measured by **Interchange Intervention Accuracy (IIA)**.

Let $R$ denote the learned rotation, and let $\tau$ map the low-level output into the high-level output space. For each base input $b$ and source inputs $s_1,\ldots,s_k$, the method compares

$$
\tau
\left(
\operatorname{DII}
\left(
\mathcal{L},
R,
\{s_j\},
\{Y_j\}
\right)
(b)
\right)
$$

with

$$
\operatorname{II}
\left(
\mathcal{H},
\{\tau(s_j)\},
\{X_j\}
\right)
(\tau(b)).
$$

IIA is the proportion of intervention cases for which these two high-level outcomes are identical.

Conceptually,

$$
\operatorname{IIA}
=
\frac{
\text{number of matching low- and high-level intervention outcomes}
}{
\text{number of tested interventions}
}.
$$

An IIA of $1$ means that all tested low-level distributed interventions reproduce the corresponding high-level counterfactual outcomes. When IIA is below $1$, it provides a graded measure of approximate causal abstraction.

Task accuracy and IIA therefore measure different things. A neural network can solve the original task perfectly while having low IIA under a proposed alignment. Such a model agrees with the high-level algorithm on observed input-output behavior but does not respond to internal interventions in the way that the proposed algorithm predicts.

This distinction is precisely what makes intervention useful for interpretability.

---

## 8. Representation as a Pattern of Counterfactual Change

The conceptual contribution of DAS is easiest to see by contrasting it with ordinary representation analysis.

A conventional probe studies a relationship such as

$$
h
\rightarrow
X,
$$

where $h$ is a neural representation and $X$ is an interpretable variable. The question is whether $X$ can be predicted from $h$.

DAS asks a different question:

$$
\operatorname{Intervene}(Y_j)
\rightarrow
\text{downstream effect}.
$$

It then compares that effect with

$$
\operatorname{Intervene}(X_j)
\rightarrow
\text{high-level downstream effect}.
$$

The proposed correspondence

$$
X_j
\leftrightarrow
Y_j
$$

is therefore supported not merely because $Y_j$ contains information about $X_j$, but because manipulating $Y_j$ reproduces the consequences of manipulating $X_j$.

$$
\boxed{
X_j \leftrightarrow Y_j
\quad\text{when their corresponding interventions produce matching causal effects.}
}
$$

This shifts the interpretation of a neural representation from a static object to a **pattern of counterfactual behavior**. What matters is not only what information can be read from a representation, but how the larger system responds when that representation is changed.

The orthogonal rotation, the subspace decomposition, and the interchange intervention each serve a different purpose. The rotation searches beyond the neuron-aligned basis. The orthogonal decomposition creates separately manipulable representational components. The intervention tests whether those components have the causal roles assigned to the corresponding high-level variables.

DAS combines these elements into a single search procedure:

$$
\begin{aligned}
&\text{Specify a high-level causal model } \mathcal{H}
\\
&\qquad\downarrow
\\
&\text{Select a low-level neural representation } N
\\
&\qquad\downarrow
\\
&\text{Learn an orthogonal transformation } R^\theta
\\
&\qquad\downarrow
\\
&Y
=
Y_0
\oplus
Y_1
\oplus
\cdots
\oplus
Y_k
\\
&\qquad\downarrow
\\
&X_j
\leftrightarrow
Y_j
\\
&\qquad\downarrow
\\
&\text{Intervene on } X_j \text{ and } Y_j
\\
&\qquad\downarrow
\\
&\text{compare their counterfactual effects.}
\end{aligned}
$$

The high-level variables are therefore not discovered from scratch by DAS. They are supplied as part of an interpretable causal hypothesis. DAS searches for a low-level representational basis under which those variables can be realized as causally corresponding subspaces.

> A neural representation supports a high-level causal interpretation not merely when the corresponding variable can be decoded, but when changing the proposed neural subspace reproduces the counterfactual consequences predicted by the high-level model.