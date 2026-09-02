---
layout: post
title: Knowledge Revision Survey - Fable/AttributeConflict
permalink: /blog/2026/knowledge-conflict-sep-en/
---

## 1. Revision in Scientific Agents

### From Accumulating Knowledge to Revising Hypotheses

Recent developments in long-horizon agents suggest that the relevant challenge is no longer simply whether a model can acquire and retain more information. In scientific and agentic settings, new observations can invalidate assumptions that were previously reasonable, requiring the agent to revise rather than merely extend its current knowledge state.

The release of Claude Fable 5.1 and Claude Mythos 5.1 provides an interesting example of this transition. The two systems share the same underlying model, while Mythos exposes a different safeguard regime for trusted scientific use. Anthropic demonstrates Mythos 5.1 in protein design and computational biology, where the model operates within workflows involving tools, candidate generation, computation, and experimental validation.

> May be,,, safety --> revision stability?

From the perspective of belief revision, these safeguards may also contribute to **revision stability**. By constraining how the model interacts with tools, evidence, and experimental feedback, such workflows can provide clearer boundaries for determining when an intermediate belief should be retained, revised, or rejected.

Fable 5.1 provides an even clearer example of revision during long-horizon work. In one reported 38-hour machine-learning run, the model determined that a previous result was caused by a label artifact, corrected its interpretation, and launched six new experiments. In another scientific evaluation, it identified a missing test in an existing research project and proposed a new hypothesis that redirected the investigation. <citation key="anthropic2026fablemythos"></citation>

> May be, if we can provide reasoning structure, the token usage can be saved.

These examples suggest that long-horizon reasoning may benefit from explicitly maintaining the structure of prior reasoning. If dependencies among assumptions, intermediate conclusions, and downstream results are represented explicitly, a new observation need not trigger reconstruction of the entire reasoning history. Instead, revision can be localized to the affected parts of the reasoning structure, potentially reducing redundant inference and token usage.


These examples suggest that scientific agents increasingly operate over a changing **hypothesis space**. If an agent maintains a set of hypotheses $H_t$, the arrival of new evidence $e_t$ should not necessarily result in simple accumulation,

$$
H_{t+1} = H_t \cup \{e_t\}.
$$

Instead, the evidence may reduce the credibility of some hypotheses, eliminate others, introduce new candidates, and invalidate experiments or plans that depended on earlier assumptions:

$$
H_{t+1} = \operatorname{Revise}(H_t, e_t).
$$

This makes scientific reasoning inherently non-monotonic. A result that was previously accepted may cease to be usable after a methodological flaw, contradictory observation, or reviewer criticism is discovered. Importantly, the revision is rarely local. If a hypothesis was used to justify subsequent analyses, experiments, or decisions, changing that hypothesis can require reconsidering a much larger region of the agent's reasoning history. ReviseBench exposes a related difficulty in scientific writing: even when LLM agents receive explicit reviewer feedback, they often struggle to propagate the requested revision consistently across the paper, experiments, and resulting claims. <citation key="luo2026revisebench"></citation>

The central problem is therefore not simply whether an LLM can **think differently** after receiving new information. It is whether the system can determine which parts of its previous reasoning should no longer govern its current behavior, and whether that change remains consistent throughout subsequent reasoning and action.


## 2. Revision in LLM Agents

### A Closer Look at Implicit Conflict

A recent benchmark that makes this problem particularly concrete is **STALE**, which studies situations in which a later observation invalidates an earlier memory without explicitly contradicting it. The paper calls this an **implicit conflict** and distinguishes two forms. <citation key="chao2026stale"></citation>

**Type I: Co-referential Conflict** occurs when the old and new observations refer to the same underlying attribute but imply mutually incompatible values. For example, suppose a user previously said that they live in Seattle. Months later, they mention signing a lease and setting up utilities in a new apartment in Portland. The later statement never explicitly says, "I no longer live in Seattle." Nevertheless, both observations concern the same latent attribute, `current residence`, and imply different current values:

$$
\text{Residence} = \text{Seattle}
\quad\rightarrow\quad
\text{Residence} = \text{Portland}.
$$

The conflict therefore does not exist at the surface level of the two sentences. It emerges only after recognizing that they update the same underlying state variable.

**Type II: Propagated Conflict** is more demanding. Here, the new observation changes a different attribute, and that change indirectly invalidates an older belief through a causal or logical dependency. For example, a user may previously say that they commute to work by bicycle and later mention that they broke their leg and must wear a cast for six weeks. The new observation does not mention commuting at all:

$$
\text{Physical Condition} = \text{Injured}.
$$

Yet this change makes the old belief

$$
\text{Commute} = \text{Bicycle}
$$

temporarily unusable. The required revision must therefore propagate across attributes:

$$
\text{Physical Condition}
\rightarrow
\text{Mobility}
\rightarrow
\text{Commute}.
$$

STALE evaluates whether models actually perform these revisions through three complementary probes. **State Resolution** asks whether the model can explicitly recognize that an old belief has become outdated. **Premise Resistance** embeds the stale belief inside the user's new query and tests whether the model resists that premise rather than blindly following it. **Implicit Policy Adaptation** does not mention the conflict at all and instead tests whether subsequent recommendations or actions naturally reflect the updated state. The distinction is important: models can sometimes correctly state that a memory is outdated while still behaving as if it were true in later tasks. <citation key="chao2026stale"></citation>

This provides a useful starting point, but it also exposes three broader questions about revision in LLM agents.



### When Should Revision Occur?

The problem is **when the consequences of a change should actually be computed**.

One possibility is eager revision: whenever new information arrives, the agent immediately recomputes every belief that might depend on it. This guarantees a continuously updated global state, but it can become unnecessarily expensive. If a user moves from Seattle to Portland, there is little reason to immediately recompute every location-dependent belief the system has ever stored about restaurants, commuting, weather, shopping, or local activities.

The opposite approach is to preserve the new observation and perform revision only when a relevant query arrives. There is evidence that this query-conditioned view is important. STALE's attention analysis finds that successful behavior is more strongly associated with **query-conditioned routing between old and new evidence** than with an explicit reconciliation between the two memories before answering. <citation key="chao2026stale"></citation>

A useful architecture may therefore separate two operations:

$$
\boxed{
\text{Write time: detect and invalidate}
\qquad
\text{Query time: propagate and recompute}
}
$$

When new evidence arrives, the system can record that some previously valid state is potentially stale. Full propagation does not need to occur until the information becomes relevant to a concrete query or action. At query time, the agent can inspect the relevant path from past state to current evidence and reconstruct only the portion of the belief state necessary for the current task.

Interestingly, CUPMem, the memory architecture introduced with STALE, follows part of this principle from the opposite direction: it performs explicit **write-side adjudication** so that stale states are identified before later retrieval, and then restricts query-time access to the resulting state. <citation key="chao2026stale"></citation> This suggests that the most promising design may not be purely write-time or purely query-time revision, but a hybrid in which **invalidation is early and recomputation is lazy**.


### How Should Revision Be Enacted?

The final problem is behavioral: even if an agent correctly identifies what should change, does it actually **act according to the revised belief**?

There is an important difference between

> "I no longer believe $P$."

and consistently reasoning as though $P$ is no longer true.

STALE makes this distinction visible through the gap between State Resolution and its behavioral probes. A model may correctly recognize that the user no longer lives in Seattle, yet still accept a later question that presupposes Seattle as the user's current location. <citation key="chao2026stale"></citation> Revision therefore requires more than verbal acknowledgment. The changed belief must constrain subsequent reasoning, recommendations, planning, and tool use.

This issue is closely related to recent work on **self-modeling**. Models exhibit some ability to predict how their own behavior would change under prompt modifications, but this ability remains limited and systematically fails on relatively simple behavioral counterfactuals. <citation key="zeng2026selfmodeling"></citation> A model's description of what it would do and its actual behavior are therefore not guaranteed to coincide.

At the same time, behavioral change alone is not sufficient evidence of rational revision. Sycophancy provides the clearest counterexample. LLMs can change their answers simply because a user expresses a conflicting opinion, even when the original answer was more accurate. <citation key="sharma2024sycophancy"></citation> Similarly, asking a model to reconsider its reasoning does not reliably improve the answer in the absence of useful external feedback; intrinsic self-correction can even decrease performance. <citation key="huang2024selfcorrect"></citation>

Thus,

$$
\text{Behavior Changed}
\not\Rightarrow
\text{Belief Rationally Revised}.
$$

A useful account of revision must distinguish **evidence-sensitive change** from conversational compliance, self-correction prompts, or other contextual pressures. Mechanistic studies of parametric and contextual knowledge already suggest that conflicting contextual and parametric information can coexist inside the residual stream rather than one representation simply replacing the other, with their eventual influence depending on how information is routed and accumulated across layers. <citation key="zhao2025reconciliation"></citation> This raises a deeper mechanistic question: when an LLM appears to "think differently," is an old representation actually suppressed, is a new one selected at readout, or are both retained while the context changes which one controls the final behavior?

Taken together, agentic revision can be organized around three questions:

$$
\boxed{
\text{Revision}
=
\underbrace{\text{What to revise}}_{\text{target and scope}}
+
\underbrace{\text{When to revise}}_{\text{timing}}
+
\underbrace{\text{How to enact it}}_{\text{behavioral consistency}}
}
$$

At minimum, we should evaluate whether an agent can identify the correct target of a revision, determine when its consequences need to be recomputed, and consistently act according to the resulting belief state. For long-horizon scientific agents such as Fable and Mythos, this may become increasingly important: discovering new evidence is only the beginning. A reliable agent must also know **what that evidence changes, how far the change should propagate, and whether its later behavior truly reflects the revision**.