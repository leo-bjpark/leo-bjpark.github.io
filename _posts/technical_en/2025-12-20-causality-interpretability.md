---
layout: post
title: Causal Abstraction as Intervention-Aligned Representation Search
permalink: /blog/2025/causality-interpretability-en/
---
## 1. From Representation to Causal Mechanism

A central goal of neural network interpretability is to understand what internal representations mean. A common approach is to train a probe that predicts an interpretable concept from a hidden state. If a linear classifier can recover whether a sentence contains negation, for example, this suggests that information about negation is encoded somewhere in the representation. However, **decodability does not imply causal relevance**. A representation may contain information that is correlated with the output without actually participating in the computation that produces it. The decoded feature may be redundant, downstream of the true mechanism, or merely predictive of another variable that drives the model.

**Causal abstraction** proposes a stronger criterion. Rather than asking only whether an interpretable variable can be decoded from a neural representation, it asks whether manipulating the proposed neural realization of that variable produces the causal effects predicted by an interpretable high-level model. <citation key="geiger2024finding"></citation> In this view, a representation becomes mechanistically meaningful when its intervention behavior corresponds to the intervention behavior of the variable it is supposed to implement.

This intervention-centered perspective has increasingly shaped mechanistic interpretability. Distributed Alignment Search (**DAS**) searches for neural subspaces whose interventions reproduce the counterfactual behavior of high-level causal variables. Instead of assuming that a concept must correspond to an individual neuron, DAS allows the relevant variable to be distributed across a multidimensional representation. Its extension, **Boundless DAS**, replaces the remaining discrete components of the search with learnable parameters and scales this approach to Alpaca 7B. <citation key="wu2023interpretability"></citation>

The same idea has also moved beyond interpretation toward model control. **Inference-Time Intervention (ITI)** identifies activation directions associated with truthful responses and modifies them during inference. <citation key="li2023iti"></citation> **Representation Engineering (RepE)** develops a broader view in which population-level neural representations become objects that can be analyzed and deliberately manipulated. <citation key="zou2023representation"></citation> **Representation Finetuning (ReFT)** goes further by freezing the underlying language model and learning interventions directly on its hidden representations; its LoReFT variant performs these interventions through learned low-dimensional subspaces. <citation key="wu2024reft"></citation>

Together, these works suggest a shift from asking **what information is represented** to asking **what happens when the representation is changed**. DAS is particularly useful in this context because it uses counterfactual behavior itself as supervision for finding the representation.

$$
\boxed{
\text{Representation}
\rightarrow
\text{Intervention}
\rightarrow
\text{Counterfactual Effect}
}
$$

---

## 2. Causal Abstraction and Interchange Intervention

Let $\mathcal{H}$ denote an interpretable **high-level causal model** and $\mathcal{L}$ a **low-level neural model**. The high-level model describes a computation using variables with interpretable meanings, while the low-level model describes the actual hidden states and mechanisms of the neural network. An alignment specifies which low-level states correspond to each high-level variable and how those states should be interpreted.

The important requirement is that this correspondence must continue to hold under intervention. If $\tau$ maps low-level states to their high-level descriptions, then a causal abstraction requires

$$
\tau\left(\mathcal{L}_{I \leftarrow i}(x)\right)
=
\mathcal{H}_{\tau(I \leftarrow i)}\left(\tau(x)\right).
$$

The left-hand side first intervenes on the neural system and then interprets the resulting state at the high level. The right-hand side first abstracts the original system into the high-level causal model and then performs the corresponding high-level intervention. A faithful abstraction requires these two paths to agree.

This is stronger than ordinary probing. A probe establishes that a variable $X$ can be predicted from a neural state $h$. Causal abstraction instead asks whether changing the proposed neural realization of $X$ produces the same downstream effect as changing $X$ in the high-level model. The representation is therefore identified not merely by what can be read from it, but by the role it plays when manipulated.

<figure>
    <img src="https://d2acbkrrljl37x.cloudfront.net/MatrixFigures/Research/distributed_alignment_search.webp" />
</figure>

To perform such interventions in practice, DAS uses **interchange intervention**. Suppose $b$ is a base input and $s$ is a source input. Rather than manually deciding what numerical value should represent an intervention on an intermediate variable $X$, the method replaces the value produced by the base input with the value naturally produced by the source:

$$
X^b \leftarrow X^s.
$$

The rest of the computation remains tied to the base input. The resulting computation is therefore counterfactual: it asks what the model would have produced for the base input if one internal variable had taken the value it takes under another input.

---

## 3. Distributed Alignment Search

The main difficulty at the neural level is that a high-level variable is unlikely to correspond neatly to a single neuron. Information about a concept may instead be distributed across many dimensions of the hidden representation. DAS therefore searches for a coordinate system in which the relevant causal variables become separately manipulable subspaces.

Given a hidden representation $h$, DAS learns an orthogonal transformation

$$
y = Rh,
\qquad
R^\top R = I.
$$

Because $R$ is orthogonal, the transformation preserves the geometry of the representation while changing its basis. DAS is therefore not creating new information; it is searching for a different coordinate system in which the existing causal structure may become easier to isolate.

In the transformed space, the representation is decomposed into orthogonal subspaces,

$$
Y = Y_0 \oplus Y_1 \oplus \cdots \oplus Y_k,
$$

with the intended correspondence $X_j \leftrightarrow Y_j$. Each $Y_j$ is a candidate neural realization of a high-level variable $X_j$, while $Y_0$ contains the part of the representation that remains tied to the base computation. Importantly, $Y_j$ does not need to be one-dimensional. A causal variable may correspond to an entire multidimensional subspace.

The distributed interchange intervention can then be written compactly as

$$
h_{\mathrm{cf}}
=
R^{-1}
\left(
\operatorname{Proj}_{Y_0}(Rh_b)
+
\sum_{j=1}^{k}
\operatorname{Proj}_{Y_j}(Rh_{s_j})
\right).
$$

Here, $h_b$ is the hidden representation of the base input and $h_{s_j}$ is the representation of source input $s_j$. The $Y_0$ component is preserved from the base, while each selected $Y_j$ component is replaced by the corresponding component from its source. The resulting representation is then mapped back into the original neural coordinate system and passed through the remainder of the network as usual.

Conceptually, the procedure is simply

$$
\boxed{
\text{Rotate}
\rightarrow
\text{Replace Selected Subspaces}
\rightarrow
\text{Unrotate}
}
$$

The important point is that DAS constructs a **counterfactual hidden state** rather than replacing the entire representation. Most of the computation remains associated with the original input, while only the candidate causal variables are exchanged.

---

## 4. Learning the Causal Alignment

The correct transformation $R$ is not known in advance. DAS therefore parameterizes it as $R^\theta$ and learns the alignment using the high-level causal model as supervision.

For each base input and collection of source inputs, the high-level model first determines what should happen under the corresponding interchange intervention. The neural model then performs the distributed interchange intervention using the current candidate alignment. DAS optimizes $\theta$ so that the resulting neural counterfactual behavior agrees with the counterfactual behavior predicted by the high-level model:

$$
\theta^*
=
\arg\min_\theta
\sum_{b,s_1,\ldots,s_k}
\operatorname{Loss}
\left(
\operatorname{DII}_{R^\theta}(b,s_1,\ldots,s_k),
\operatorname{II}_{\mathcal{H}}(b,s_1,\ldots,s_k)
\right).
$$

Crucially, the neural model $\mathcal{L}$ and the high-level causal model $\mathcal{H}$ remain fixed during this optimization. **Only the alignment is learned.** DAS therefore does not train the neural network to implement the proposed algorithm. Instead, it asks whether an appropriate subspace already exists inside the network whose intervention behavior matches that algorithm.

Suppose, for example, that changing $X_j$ in the high-level model causes the output to change from $A$ to $B$. DAS searches for a neural subspace $Y_j$ such that replacing that subspace with the corresponding source representation also produces the change $A \rightarrow B$. If a candidate alignment fails to reproduce the predicted effect, the intervention loss changes the rotation until a better alignment is found.

The key idea is therefore that **high-level counterfactual behavior provides supervision for finding low-level representations**. DAS does not identify a causal representation because its activations look similar to some interpretable variable. It identifies the representation because intervening on it produces the expected causal consequences.

---

## 5. Representation as Counterfactual Behavior

After learning an alignment, DAS evaluates it using **Interchange Intervention Accuracy (IIA)**. IIA measures the proportion of interventions for which the low-level neural model and the high-level causal model produce matching counterfactual outcomes:

$$
\operatorname{IIA}
=
\frac{1}{|\mathcal{D}|}
\sum_{d\in\mathcal{D}}
\mathbf{1}
\left[
\tau\left(\operatorname{DII}_{R^*}(d)\right)
=
\operatorname{II}_{\mathcal{H}}(d)
\right].
$$

An IIA of $1$ means that all tested neural interventions reproduce the outcomes predicted by the high-level model. This is fundamentally different from ordinary task accuracy. A neural network can produce the correct answers on all observed inputs while still having low IIA for a proposed causal interpretation. In that case, the network and the high-level model are behaviorally equivalent on the original task but respond differently when their internal variables are manipulated.

$$
\boxed{
\text{Behavioral Equivalence}
\neq
\text{Causal-Mechanistic Equivalence}
}
$$

This distinction leads to the central interpretation of DAS. A neural subspace $Y_j$ corresponds to a high-level variable $X_j$ not simply because $X_j$ can be decoded from $Y_j$, but because intervening on the two produces corresponding downstream effects.

$$
\boxed{
X_j \leftrightarrow Y_j
\quad
\text{when their interventions produce corresponding causal effects.}
}
$$

Representation can therefore be understood as a **pattern of counterfactual behavior**. What matters is not only what information is contained in a hidden state, but how the larger system changes when that state is manipulated.

This also clarifies the relationship between DAS and more recent representation-level methods. DAS and Boundless DAS use intervention to **discover and validate causal representations**. ITI and RepE use related representation-level structure to **steer model behavior**, while ReFT learns representational interventions directly as a mechanism for **task adaptation**. Their objectives differ, but they share the same broader move away from purely observational interpretation toward understanding and controlling neural systems through interventions on their internal representations.
