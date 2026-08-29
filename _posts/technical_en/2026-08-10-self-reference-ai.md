---
layout: post
title: Self-Reference AI
permalink: /blog/2026/self-reference-ai-en/
mini-title: Self-Reference AI
---


## 1. Mind Viruses

[Mind Viruses: Self-Propagating Ideas in Multi-Agent LLM Systems](https://arxiv.org/abs/2608.10218) studies how ideas and instructions can persist and propagate across AI agents. An instruction received by one agent may change its behavior, be written into a persistent file, and later influence another agent or a future session of the same agent. What is interesting is not simply that information is transmitted. Some information changes the agent's behavior in a way that **increases the chance that the information itself will be stored, preserved, and encountered again.** <citation key="papadopoulos-etal-2026-mind-viruses"></citation>

The role of `SOUL.md` is particularly interesting. Conversational context may disappear when a session ends, while information written into a persistent file can remain. If that information is loaded again in a later session, language generated at one point in time can become part of the conditions that determine future behavior. In this sense, `SOUL.md` is more than ordinary memory: it can act as a **persistent interface that reconnects an agent's representation of itself to its later behavior.**

$$
\text{Behavior}_t
\rightarrow
\text{Self-Description}_t
\rightarrow
\texttt{SOUL.md}
\rightarrow
\text{Behavior}_{t+1}
$$

This raises an important distinction. What an agent says about itself does not necessarily match what it actually does. An agent may describe itself as following a particular principle while behaving in ways that violate that principle. Conversely, it may form a reasonably accurate description of its own behavior and continue to act in accordance with that description. Mechanisms such as `SOUL.md` make this agreement or disagreement more than a one-session phenomenon: the relationship between self-description and behavior can persist and accumulate over time.

> How stable is the relationship between what an agent says about itself and how it actually behaves?

From this perspective, the interesting problem is not merely memory, but **persistent self-representation and behavioral consistency**. An inaccurate self-description may repeatedly steer later behavior toward that representation, while a stable and accurate description may potentially help preserve behavioral consistency.

<figure>
    <img src="https://d2acbkrrljl37x.cloudfront.net/MatrixFigures/Research/self-reference-ai.webp" />
</figure>


---

## 2. Quine and Self-Reference

One of the simplest computational examples of self-reference is the [Quine program](https://en.wikipedia.org/wiki/Quine_(computing)). A Quine outputs its own source code without reading that source code as an external input. The important point is that this act of self-reproduction does not itself create any contradiction. A computational system can therefore generate a representation of itself without becoming paradoxical. <citation key="wikipedia-quine-computing"></citation>

As discussed in [Self-Reference and Paradox](https://plato.stanford.edu/entries/self-reference/), the important issue is not simply whether something refers to itself. What matters is **what the system says about the self-referential object and what operations or predicates it is able to apply to that representation.** Self-reference itself is therefore not the paradox; additional expressive structure is required for a paradox to arise. <citation key="sep-self-reference"></citation>


---

## 3. Gödel Coding and the Diagonal Lemma

Gödel introduced a systematic way for a formal system to represent its own syntax by encoding syntactic objects such as symbols, formulas, sentences, and proofs as natural numbers. This is known as [Gödel numbering](https://plato.stanford.edu/entries/goedel-incompleteness/sup1.html), or **Gödel coding**. If $\phi$ is a sentence, its Gödel number can be written as $\ulcorner \phi \urcorner$. In this way, a sentence itself becomes an arithmetic object, allowing a system that reasons about numbers to indirectly **reason about its own sentences and proofs.** <citation key="sep-goedel-numbering"></citation>

$$
\text{Sentence}
\rightarrow
\ulcorner \text{Sentence} \urcorner
$$

Gödel coding, however, only gives the system a representation of its syntax. It does not by itself create self-reference. The key mechanism is the [Diagonal Lemma](https://plato.stanford.edu/entries/self-reference/). For a sufficiently expressive theory $T$ and a formula $\phi(x)$ with one free variable, there exists a sentence $G$ such that <citation key="sep-self-reference"></citation>

$$
T \vdash
G
\leftrightarrow
\phi(\ulcorner G \urcorner)
$$

The sentence $G$ therefore applies the property expressed by $\phi$ to its own Gödel number. If, for example, $\phi(x)$ means "the sentence with code $x$ is not provable," then $G$ becomes a sentence that says of itself that it is not provable.

The important point is that self-reference does not have to be introduced informally through expressions such as "this sentence." It can be constructed systematically within a formal system.

$$
\boxed{
\text{Gödel Coding}
+
\text{Diagonalization}
\rightarrow
\text{Formal Self-Reference}
}
$$

In this sense, the Diagonal Lemma can be understood as a **fixed-point principle for constructing self-reference in formal languages**.


---

## 4. Tarski: The Limit of Internal Truth

[Tarski's work on truth](https://plato.stanford.edu/entries/tarski-truth/) asks whether a sufficiently expressive language can completely define truth for its own sentences from within that same language. Suppose, for example, that the language contained an unrestricted truth predicate $True(x)$ satisfying the following condition for every sentence $A$. <citation key="sep-tarski-truth"></citation>

$$
True(\ulcorner A \urcorner)
\leftrightarrow
A
$$

The Diagonal Lemma can now be applied to $\phi(x)=\neg True(x)$, producing a sentence $L$ that effectively says, "I am not true."

$$
L
\leftrightarrow
\neg True(\ulcorner L \urcorner)
$$

But the assumed truth condition also gives $True(\ulcorner L \urcorner)\leftrightarrow L$. Combining the two produces the familiar liar-style structure $L\leftrightarrow\neg L$.

The important conclusion is not that **self-reference itself produces contradiction**. The problem arises when a sufficiently expressive language tries to contain an unrestricted truth predicate for all of its own sentences within that same language.

> A sufficiently expressive language cannot fully define its own truth within itself.

This is one reason formal theories of truth distinguish between an **object language** and a stronger **metalanguage** from which the truth of statements in the object language can be discussed. For further discussion, see [Self-Reference and Paradox](https://plato.stanford.edu/entries/self-reference/) and [Tarski's Truth Definitions](https://plato.stanford.edu/entries/tarski-truth/). <citation key="sep-self-reference"></citation> <citation key="sep-tarski-truth"></citation>


---

## 5. Gödel's Incompleteness Theorems

[Gödel's Incompleteness Theorems](https://plato.stanford.edu/entries/goedel-incompleteness/) concern a different notion from Tarski's truth predicate: **provability**, or what can be formally proved within a system. Let $Prov_T(x)$ mean that the sentence encoded by $x$ is provable in a theory $T$. Applying the Diagonal Lemma to $\phi(x)=\neg Prov_T(x)$ produces a Gödel sentence $G_T$ satisfying <citation key="sep-goedel-incompleteness"></citation>

$$
T \vdash
G_T
\leftrightarrow
\neg Prov_T(\ulcorner G_T \urcorner)
$$

Intuitively, $G_T$ says:

> This sentence is not provable in $T$.

Suppose that $T$ actually proved $G_T$. Then $G_T$ would be provable even though it says that it is not provable. A consistent theory therefore cannot prove $G_T$.

$$
\text{Consistency of }T
\Rightarrow
T\nvdash G_T
$$

This is the central intuition behind Gödel's First Incompleteness Theorem. The result is not that a sufficiently powerful formal system must fall into contradiction. Rather, **if the system is to remain consistent, it must give up the ability to prove every statement.**

$$
\boxed{
\text{Consistency}
\Rightarrow
\text{Incompleteness}
}
$$

This is also where the difference between Tarski and Gödel becomes clear. Tarski considers what happens when a system tries to internalize **truth** for its own language, in which case unrestricted self-application produces a liar-style contradiction. Gödel instead considers **provability**. A statement may be true without being provable inside a particular formal system, so diagonalization does not immediately force a contradiction. Instead, it exposes a gap between truth and provability.

Gödel's Second Incompleteness Theorem extends this limitation to self-certification. The reasoning behind the First Theorem can itself be formalized inside a sufficiently strong theory $T$. Roughly speaking, the system can establish that if it is consistent, then its Gödel sentence must hold.

$$
Con(T)
\rightarrow
G_T
$$

Now suppose that $T$ could prove its own consistency, $Con(T)$. It could then combine that proof with the implication above and derive $G_T$. But a consistent $T$ cannot prove $G_T$, as established by the First Incompleteness Theorem. Therefore, under the relevant assumptions, a consistent formal system cannot prove its own consistency from within itself.

$$
\boxed{
T\nvdash Con(T)
}
$$

The distinction between the two incompleteness theorems is therefore fairly intuitive. The First Theorem says that **the system cannot prove everything**. The Second goes one step further and says that **the system cannot even completely certify the consistency of its own proof system from within itself**. In this sense, Tarski concerns a limit on internal truth, Gödel's First Theorem a limit on internal provability, and Gödel's Second Theorem a limit on internal self-certification. <citation key="sep-goedel-incompleteness"></citation>


---

## 6. Self-Reference as a Broader Theme

Quines, Tarski, Gödel, and persistent AI agents all relate to the broad theme of self-reference, but **they do not address the same problem**. A Quine shows that a program can reproduce a representation of itself. Gödel coding and the Diagonal Lemma show how a formal system can construct sentences that refer to their own representations. Tarski concerns the limits of defining truth for a language within that same language, while Gödel concerns the limits of provability and self-certification in sufficiently expressive formal systems.

[Mind Viruses](https://arxiv.org/abs/2608.10218), by contrast, studies a different phenomenon: **the persistence and propagation of information through the behavior and memory mechanisms of AI agents**. There is therefore no need to explain Mind Viruses through Gödel's or Tarski's theorems. Their connection is much looser. They simply share a broader question about what happens when **a representation of the system itself re-enters the operation of that system.**

For AI agents, this question becomes especially concrete because self-reference can occur through ordinary natural language. An agent can describe its goals, rules, identity, or previous behavior, and if that description is stored in persistent state, it can later become part of the context that determines future behavior. At that point, self-description is no longer merely a report about the agent; it becomes **an input that can participate in constructing the agent's future behavior.**

$$
\text{Self-Representation}_t
\rightarrow
\text{Persistent State}
\rightarrow
\text{Behavior}_{t+1}
$$

The interesting question for AI agents is therefore not whether Gödel's or Tarski's theorems directly apply to them. A more immediate question is whether the agent's representation of itself remains aligned with its actual behavior, and whether that relationship remains stable over time.

> What happens when an agent's representation of itself becomes part of the mechanism that determines its future behavior?

This is what makes `SOUL.md` interesting. It can be viewed not simply as memory, but as a mechanism for **persistent self-representation**: a linguistic description of the agent can survive beyond a single interaction and become an active component of the agent's future behavior.