---
layout: post
title: Verified AI - Formalization, Theorem Proving, and Vericoding
permalink: /blog/2026/verified-ai-formalization-kr/
---


## 1. 왜 Verified AI인가?

AI가 생성한 결과를 우리는 어떻게 신뢰할 수 있을까? 지금까지 AI의 신뢰성을 높이는 접근은 주로 모델 자체를 개선하는 방향이었다. 더 좋은 학습 데이터, 더 긴 reasoning, self-correction, evaluation, interpretability 등을 통해 모델이 더 정확한 결과를 내도록 만드는 것이다. 하지만 모델이 아무리 강해져도 마지막에는 한 가지 질문이 남는다. **생성된 결과가 실제로 옳다는 것을 어떻게 알 수 있을까?**

Max Tegmark는 이와 관련된 방향을 **Vericoding**이라는 개념을 통해 이야기한다. Vericoding은 LLM이 단순히 코드를 생성하는 데서 끝나는 것이 아니라, 그 코드가 **formal specification**을 만족하는지 검증할 수 있는 형태까지 만드는 것을 목표로 한다. <citation key="bursuc2025vericoding"></citation>

기존의 AI coding 과정을 단순화하면 **사용자 목적 → 코드 생성 → 테스트 → 피드백 → 수정**이라고 볼 수 있다. Verified workflow에서는 여기에 하나의 층이 더 추가된다. **사용자 목적 → Formal specification → Code + proof artifacts → Formal verifier → Feedback → Revision**이다. 검증에 실패하면 구현된 코드 자체를 수정할 수도 있고, 검증에 필요한 proof structure를 수정할 수도 있다.

중요한 변화는 단순히 코드를 더 잘 작성하는 것이 아니라 **신뢰를 어디에 둘 것인가**에 있다. 테스트는 우리가 실행한 몇 가지 사례에서 프로그램이 잘 작동했음을 보여준다. 반면 formal verification은 명시적으로 정의된 성질이 formal system의 규칙 아래에서 실제로 성립하는지를 확인한다.

> 목표는 반드시 모델 자체를 완벽하게 신뢰할 수 있도록 만드는 것이 아니라, 모델의 **출력을 독립적으로 검증할 수 있게 만드는 것**일 수도 있다.


<figure style="width: 50%;">
    <img src="https://d2acbkrrljl37x.cloudfront.net/MatrixFigures/Research/2026-anthropic-fermat-cartoon.webp"/>
</figure>


이런 구조가 완전히 새로운 것은 아니다. **AlphaGeometry**는 neural model이 유용한 보조 구성을 제안하고, symbolic deduction engine이 명시적인 규칙에 따라 결과를 도출하는 방식을 사용했다. <citation key="trinh2024alphageometry"></citation> 최근 theorem proving과 vericoding에 대한 관심은 이와 유사한 아이디어를 훨씬 더 큰 수학적·소프트웨어적 산출물로 확장하고 있다.

Anthropic의 **Fermat’s Last Theorem(FLT)** formalization은 이를 잘 보여주는 사례다. Claude가 새로운 FLT 증명을 발견한 것은 아니다. Andrew Wiles의 증명을 단순화한 기존 exposition을 따라가며, 필요한 수학적 dependency를 **Lean**으로 옮겼다. 약 11일 동안 수만 개의 중간 theorem을 formalize했고, 인간이 작성한 거대한 수학적 증명을 기계적으로 확인 가능한 구조로 바꾸었다. 핵심은 FLT를 다시 푼 것이 아니라, 인간의 수학을 **machine-checkable한 proposition과 proof의 구조로 변환했다는 것**에 있다.

---

## 2. Formalization과 Theorem Proving

**Formal system**은 어떤 언어와 객체를 사용할 수 있는지, 그리고 어떤 inference rule을 통해 하나의 명제에서 다른 명제로 이동할 수 있는지를 정의한다. **Formal methods**는 이러한 formal system을 사용해 수학, 프로그램, 하드웨어, 프로토콜 등을 명세하고 검증하는 더 넓은 방법론이다.

이 안에서 **Formalization**은 자연어나 기존 수학으로 표현된 개념과 논증을 정확한 formal object로 옮기는 과정이다. **Theorem proving**은 그렇게 표현된 proposition에 대한 proof를 찾는 과정이고, **Formal verification**은 만들어진 proof나 program이 실제 formal statement를 만족하는지를 기계적으로 확인하는 과정이다.

<figure>
    <img src="https://d2acbkrrljl37x.cloudfront.net/MatrixFigures/Research/2026-anthropic-fermat-dag.webp" />
</figure>

예를 들어 어떤 수가 다른 수의 **배수**라는 관계를 다음과 같이 정의할 수 있다.

```lean
def MultipleOf (d n : ℕ) : Prop :=
  ∃ k, n = d * k

theorem multiple_of_30_is_multiple_of_10
    (n : ℕ)
    (h : MultipleOf 30 n) :
    MultipleOf 10 n := by

  rcases h with ⟨k, hk⟩
  refine ⟨3 * k, ?_⟩
  rw [hk]
  ring
```

이 theorem은 **임의의 자연수 `n`이 30의 배수라면, 반드시 10의 배수라는 것**을 증명한다.

- **`h : MultipleOf 30 n`**: `n = 30 * k`인 어떤 `k`가 존재한다는 가정이다.
- **`rcases`**: 그 `k`와 `n = 30 * k`라는 equality를 꺼낸다.
- **`refine ⟨3 * k, ?_⟩`**: `n`을 `10 × (...)` 형태로 표현하기 위해 `3 * k`를 새로운 **witness**로 제시한다.
- **`rw [hk]`**: `n` 대신 `30 * k`를 대입한다.
- **`ring`**: 남은 `30 * k = 10 * (3 * k)`라는 대수적 관계가 성립하는지 확인한다.

즉 일반적인 수학에서는 **“n = 30k라면 n = 10(3k)이므로 n은 10의 배수다”**라고 짧게 표현하는 reasoning을 Lean에서는 formal proof로 작성한다.

>  물론 이것은 일종의 **formal proof language**이므로 각 문법을 모두 이해할 필요는 없다. 중요한 것은 사람이나 LLM이 proof script를 작성하면 Lean이 이를 형식적인 proof로 변환하고, **kernel이 최종적으로 그 proof가 theorem statement를 실제로 만족하는지 검사한다**는 점이다.

이 과정에서 자주 등장하는 몇 가지 핵심 연산은 다음과 같다.

| Operation | 역할 | 예시 |
| --- | --- | --- |
| **Unification** | 기존 theorem의 변수와 현재 goal의 변수·type을 대응한다 | theorem의 $x$를 현재 변수 $n$과 대응 |
| **Rewrite** | 이미 증명된 equality를 이용해 식을 대입한다 | $n=2k$에서 $n^2$을 $(2k)^2$으로 변환 |
| **Witness** | existential proposition에서 실제 값을 꺼내거나 제시한다 | $\exists m,\;n^2=2m$에서 $m=2k^2$를 선택 |
| **Apply** | 이미 존재하는 theorem을 현재 goal에 연결한다 | $A\rightarrow B$가 있을 때 goal $B$를 $A$를 증명하는 문제로 변경 |
| **Ring normalization** | polynomial expression을 canonical form으로 변환한다 | $(2k)^2=4k^2$를 확인 |

이 연산들은 가능한 tactic을 무작위로 전부 실행해서 선택되는 것이 아니다. **현재 proof state의 형태가 어떤 종류의 연산이 필요한지를 상당 부분 알려준다.** existential goal이라면 witness가 필요하고, 현재 식과 연결되는 equality가 있다면 rewrite가 자연스럽다. 남은 문제가 polynomial identity라면 algebraic normalization을 사용할 수 있다.

LLM이 theorem proving에서 유용한 이유도 여기에 있다. 모델은 현재 goal을 보고 관련 theorem을 검색하고, intermediate lemma를 제안하고, 어떤 transformation이 적절한지 선택할 수 있다. 그리고 Lean은 그 선택이 실제로 valid한지 확인한다.

---

## 3. Mathlib에서 Fermat’s Last Theorem까지

FLT처럼 거대한 정리를 formalize하면 자연스럽게 한 가지 의문이 생긴다. 하나의 proposition을 증명하기 위해 더 작은 proposition이 필요하고, 다시 그것들을 증명하기 위해 더 작은 proposition이 필요하다면, 이 과정은 끝없이 내려가는 것이 아닐까?

실제로는 어느 순간 이미 formalize되어 있는 수학에 도달한다. Lean에서는 그 기반의 상당 부분을 **Mathlib**이라는 거대한 verified mathematics library가 제공한다.

전체 구조를 단순화하면 다음과 같다.

**Lean foundations → Mathlib → 새로운 intermediate theorems → FLT-specific mathematics → FLT**


<figure>
    <img src="https://d2acbkrrljl37x.cloudfront.net/MatrixFigures/Research/2026-anthropic-fermat-flow.webp" />
</figure>



따라서 대규모 formalization에는 두 방향이 동시에 존재한다. 최종 theorem에서 아래로 내려가며 **“이 theorem을 증명하려면 무엇이 필요한가?”**를 묻는 **backward decomposition**이 있고, 아래에서 이미 검증된 theorem을 하나씩 쌓아 올리는 **forward accumulation**이 있다.

예를 들어 최종 proposition을 증명하기 위해 $P_1$, $P_2$, $P_3$가 필요하고, 다시 $P_1$을 위해 $P_{11}$과 $P_{12}$가 필요하다면 자연스럽게 dependency graph가 만들어진다. 아래쪽 proposition의 proof가 완성되면 그 결과는 상위 proposition의 proof에서 재사용될 수 있다.

Anthropic의 FLT 프로젝트에서 **Prove2Me**가 중요했던 이유도 바로 이 구조 때문이다. <citation key="chen2026prove2me"></citation> 초기 Claude agent들은 개별적인 theorem에서는 어느 정도 성과를 냈지만, 프로젝트가 커지면서 무엇이 이미 증명되었는지, 어떤 theorem이 다른 theorem에 의존하는지, 다른 agent가 무엇을 하고 있는지를 점차 잃어버렸다.

Prove2Me는 이러한 정보를 theorem statement와 dependency의 **directed acyclic graph(DAG)**로 외부화한다. 서로 다른 agent가 별도의 sub-theorem을 증명할 수 있고, 검증된 결과는 다른 proof에서 다시 사용할 수 있는 node가 된다. 하위 dependency가 모두 증명되면 그 결과가 위쪽 theorem으로 전달되고, 최종적으로 root theorem까지 닫힐 수 있다.

중요한 점은 전체 FLT proof가 하나의 거대한 LLM reasoning trace 안에 존재할 필요가 없다는 것이다. 전체 proof는 오히려 **작고 독립적으로 검증된 mathematical state들의 외부 그래프**로 존재할 수 있다.

$$
\text{LLM local reasoning}
+
\text{external proof state}
+
\text{formal verifier}
$$

이 구조에서 Claude는 local proof search를 수행하고, Prove2Me는 전체 dependency와 state를 관리하며, Lean은 어떤 결과가 verified knowledge로 들어올 수 있는지를 결정한다.

---

## 4. Formal Tools와 Verified AI

Lean은 formal methods의 여러 도구 중 하나다. **Lean**은 dependent type theory를 기반으로 하는 interactive theorem prover이며, proposition을 type으로, proof를 그 type의 inhabitant로 다룬다. 최종 proof term은 kernel이 검사한다.

**Rocq**는 기존 Coq의 새로운 이름으로, Calculus of Inductive Constructions를 기반으로 하는 proof assistant다. **Isabelle/HOL**은 higher-order logic을 기반으로 하고 있으며 interactive theorem proving과 강한 proof automation을 함께 제공한다.

반면 **Dafny**는 verified programming에 더 직접적으로 초점을 둔다. 수학적 proposition을 증명하는 것보다는 프로그램에 precondition, postcondition, invariant 등을 명시하고, 실제 구현이 그 specification을 만족하는지 verification condition과 SMT solving을 통해 확인한다.

| System | 주요 목적 | 간단한 예시 |
| --- | --- | --- |
| **Lean** | Interactive theorem proving | 짝수의 제곱이 짝수임을 formal proof로 증명 |
| **Rocq** | Interactive theorem proving | $\forall n,\;n+0=n$ 같은 theorem을 tactic이나 proof term으로 증명 |
| **Isabelle/HOL** | Higher-order logic과 proof automation | 논리적·대수적 성질을 HOL theorem으로 표현하고 증명 |
| **Dafny** | Verified programming | 함수가 특정 postcondition을 항상 만족하는지 검증 |

Lean, Rocq, Isabelle은 주로 **“이 proposition을 증명할 수 있는가?”**라는 질문과 연결된다. Dafny는 **“이 program이 이 specification을 만족하는가?”**라는 질문에 더 가깝다. 따라서 AI-assisted mathematical formalization은 전자와, Vericoding은 후자와 자연스럽게 연결된다.

하지만 formal verification이 신뢰의 모든 문제를 해결해주는 것은 아니다. 예를 들어 $FakePrime(n)$을 단순히 `True`라고 정의한다면 모든 $n$이 `FakePrime`이라는 theorem을 완벽하게 증명할 수 있다. proof 자체는 formal system 안에서 옳지만, 그 definition은 우리가 일반적으로 의미하는 prime number와 전혀 다르다.

따라서 **proof correctness**와 **semantic correctness**는 구분되어야 한다. Formal verifier는 proof가 formal statement를 만족하는지는 확인할 수 있지만, 그 formal statement가 우리가 실제로 의도한 의미를 정확하게 표현하는지는 스스로 보장하지 못한다. 어떤 definition을 사용할 것인지, 어떤 specification을 신뢰할 것인지, 어떤 library와 axiom을 허용할 것인지는 여전히 중요한 문제다.

이 한계는 동시에 **Verified AI**가 무엇을 의미할 수 있는지도 보여준다. Verified AI는 단순히 LLM 뒤에 verifier 하나를 붙이는 것이 아니다. 더 넓게 보면 모델 내부의 reasoning 자체에 모든 신뢰를 두는 대신, **독립적으로 검증할 수 있는 artifact로 신뢰의 일부를 이동시키는 구조**다.

> **AI proposes. Formal systems check. Verified results become reusable knowledge.**

Formalization, theorem proving, 그리고 vericoding은 서로 다른 대상을 다루지만 같은 방향을 가리킨다. AI가 얼마나 그럴듯하게 reasoning했는지만 묻는 대신, **AI가 만든 결과를 다른 시스템이 검증할 수 있는 형태로 표현할 수 있는가**를 묻는 것이다.

## Links

- [Prove2Me](https://prove2.me/)
- [Anthropic Fermat's Last Theorem — GitHub](https://github.com/anthropics/fermats-last-theorem)
- [맥스 테그마크 - 신경망 해석 가능성: 대칭성, 기하학, 그리고 형식 검증 Video](https://www.youtube.com/watch?v=nRrt7AczYV4)
