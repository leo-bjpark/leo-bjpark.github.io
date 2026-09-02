---
layout: post
title: Causal Abstraction as Intervention-Aligned Representation Search
permalink: /blog/2025/causality-interpretability-kr/
---

## 1. 표현에서 인과적 메커니즘으로

신경망 해석 가능성의 핵심 목표 중 하나는 내부 표현이 무엇을 의미하는지 이해하는 것이다. 일반적인 접근은 hidden state로부터 해석 가능한 개념을 예측하는 probe를 학습하는 것이다. 예를 들어 선형 분류기가 내부 표현으로부터 문장에 부정 표현이 포함되어 있는지를 복원할 수 있다면, 이는 해당 표현 안에 부정에 관한 정보가 어느 정도 인코딩되어 있음을 의미한다.

그러나 **decodability가 곧 causal relevance를 의미하지는 않는다**. 어떤 표현이 출력과 상관관계를 가지는 정보를 포함하고 있더라도, 그 정보가 실제로 출력을 만들어내는 계산 과정에 참여한다는 보장은 없다. probe가 찾아낸 특징은 실제 메커니즘의 중복된 복사본일 수도 있고, 이미 계산이 끝난 뒤 생성된 결과일 수도 있으며, 실제 원인이 되는 다른 변수와 단순히 높은 상관관계를 가질 수도 있다.

**Causal abstraction**은 이보다 더 강한 해석 기준을 제안한다. 해석 가능한 변수가 신경 표현으로부터 단순히 복호화될 수 있는지를 묻는 대신, 그 변수의 신경적 구현으로 가정한 표현을 실제로 조작했을 때 해석 가능한 high-level causal model이 예측하는 인과적 효과가 나타나는지를 묻는다. <citation key="geiger2024finding"></citation> 이러한 관점에서는 어떤 표현의 의미가 그 값 자체뿐 아니라, **그 표현을 변화시켰을 때 전체 시스템이 어떻게 변화하는지**에 의해 규정된다.

이러한 intervention 중심의 관점은 최근 mechanistic interpretability에서 점점 중요해지고 있다. Distributed Alignment Search(**DAS**)는 high-level causal variable에 대한 intervention과 동일한 counterfactual behavior를 만들어내는 neural subspace를 탐색한다. 특정 개념이 하나의 neuron에 대응한다고 가정하는 대신, 그 변수가 여러 차원에 걸쳐 분산되어 표현될 수 있다고 본다. 이후 제안된 **Boundless DAS**는 기존 DAS에 남아 있던 discrete search 요소들을 학습 가능한 parameter로 대체하여 이러한 접근을 Alpaca 7B까지 확장하였다. <citation key="wu2023interpretability"></citation>

동시에 representation intervention은 모델을 **해석하는 도구**에서 모델의 행동을 **제어하는 도구**로도 확장되고 있다. **Inference-Time Intervention (ITI)**은 truthful response와 관련된 activation direction을 찾아 inference 과정에서 이를 조정한다. <citation key="li2023iti"></citation> **Representation Engineering (RepE)**은 한 단계 더 넓게, population-level neural representation 자체를 분석과 조작의 대상으로 바라보는 관점을 제안한다. <citation key="zou2023representation"></citation> **Representation Finetuning (ReFT)**은 여기서 더 나아가 기존 language model의 parameter를 고정한 채 hidden representation에 직접 intervention을 학습하며, LoReFT에서는 이를 저차원 subspace를 통해 수행한다. <citation key="wu2024reft"></citation>

이러한 연구들은 해석 가능성의 질문이 점차 **무엇이 표현되어 있는가**에서 **그 표현을 바꾸면 무엇이 달라지는가**로 이동하고 있음을 보여준다. DAS가 특히 흥미로운 이유는 representation을 찾기 위한 supervision 자체가 counterfactual causal behavior에서 나온다는 점이다.

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

## 2. Causal Abstraction과 Interchange Intervention

$\mathcal{H}$를 해석 가능한 **high-level causal model**, $\mathcal{L}$을 실제 신경망에 해당하는 **low-level neural model**이라고 하자. High-level model은 equality, negation, lexical entailment처럼 의미를 해석할 수 있는 변수들로 계산 과정을 기술하고, low-level model은 실제 neural network 내부의 hidden state와 mechanism을 기술한다. 두 모델 사이의 alignment는 어떤 low-level state가 각각의 high-level variable에 대응하며, 그 상태를 어떻게 해석해야 하는지를 정의한다.

중요한 조건은 이러한 대응 관계가 일반적인 입력에서만 성립하는 것이 아니라 **intervention 이후에도 유지되어야 한다는 것**이다. $\tau$가 low-level state를 high-level description으로 변환하는 abstraction map이라면, causal abstraction은 다음 조건을 요구한다.

$$
\tau\left(\mathcal{L}_{I \leftarrow i}(x)\right)
=
\mathcal{H}_{\tau(I \leftarrow i)}\left(\tau(x)\right).
$$

왼쪽은 먼저 neural system에 intervention을 수행하고 그 결과를 high-level representation으로 해석하는 과정이다. 반대로 오른쪽은 먼저 원래 시스템을 high-level causal model로 추상화한 뒤, 그에 대응하는 high-level intervention을 수행한다. 두 경로가 동일한 결과를 만들어낼 때 low-level system이 high-level model의 faithful causal abstraction이라고 할 수 있다.

이 조건은 일반적인 probing보다 훨씬 강하다. Probe는 neural state $h$에서 변수 $X$를 예측할 수 있는지를 확인한다. 반면 causal abstraction은 $X$의 neural realization이라고 가정한 representation을 변화시켰을 때, high-level model에서 $X$를 변화시킨 것과 동일한 downstream effect가 발생하는지를 확인한다. 따라서 표현의 의미는 그 안에서 무엇을 읽어낼 수 있는지만으로 결정되지 않고, **그 표현이 조작되었을 때 어떤 역할을 수행하는가**에 의해 결정된다.

<figure>
    <img src="https://d2acbkrrljl37x.cloudfront.net/MatrixFigures/Research/distributed_alignment_search.webp" />
</figure>

이러한 intervention을 실제로 수행하기 위해 DAS는 **interchange intervention**을 사용한다. $b$를 base input, $s$를 source input이라고 하자. 중간 변수 $X$에 어떤 숫자 값을 임의로 지정하는 대신, source input에서 자연스럽게 생성된 값을 가져와 base input의 값을 대체한다.

$$
X^b \leftarrow X^s.
$$

나머지 계산은 여전히 base input에 의해 결정된다. 따라서 결과적으로 만들어지는 계산은 counterfactual하다. 즉, “base input에 대해 계산하고 있었지만 특정 내부 변수만 다른 입력에서 나타난 값을 가졌다면 모델은 어떤 결과를 만들었을까?”라는 질문에 해당한다.

---

## 3. Distributed Alignment Search

Neural level에서 가장 중요한 문제는 high-level variable이 하나의 neuron이나 고정된 neuron 집합에 깔끔하게 대응하지 않을 가능성이 높다는 것이다. 하나의 개념에 대한 정보는 hidden representation의 여러 차원에 걸쳐 분산되어 있을 수 있다. DAS는 이러한 경우를 고려하여, 의미 있는 causal variable들이 개별적으로 조작 가능한 subspace로 나타나는 새로운 좌표계를 탐색한다.

Hidden representation $h$에 대해 DAS는 다음의 orthogonal transformation을 학습한다.

$$
y = Rh,
\qquad
R^\top R = I.
$$

$R$이 orthogonal하기 때문에 이 transformation은 representation의 기본적인 geometry를 유지하면서 좌표계의 방향만 바꾼다. 즉, 새로운 정보를 만들어내는 것이 아니라 동일한 representation을 다른 basis에서 바라보는 것이다.

변환된 representation space는 다음과 같이 서로 orthogonal한 subspace로 분해된다.

$$
Y = Y_0 \oplus Y_1 \oplus \cdots \oplus Y_k.
$$

이때 DAS가 찾고자 하는 대응 관계는 $X_j \leftrightarrow Y_j$이다. 각 $Y_j$는 high-level variable $X_j$를 구현하는 neural representation의 후보이고, $Y_0$는 intervention 과정에서도 base computation으로부터 유지되는 나머지 representation을 담당한다. 여기서 중요한 점은 $Y_j$가 하나의 방향일 필요가 없다는 것이다. 하나의 causal variable은 여러 차원으로 이루어진 subspace 전체에 대응할 수 있다.

이제 distributed interchange intervention은 다음과 같이 간결하게 표현할 수 있다.

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

$h_b$는 base input에서 생성된 hidden representation이고, $h_{s_j}$는 source input $s_j$에서 생성된 representation이다. $Y_0$에 해당하는 성분은 base input에서 유지하고, intervention 대상인 각 $Y_j$ 성분만 대응하는 source representation에서 가져온다. 이렇게 구성된 representation은 다시 원래 neural coordinate system으로 변환된 뒤, network의 나머지 계산에 그대로 전달된다.

개념적으로 보면 전체 과정은 매우 단순하다.

$$
\boxed{
\text{Rotate}
\rightarrow
\text{Replace Selected Subspaces}
\rightarrow
\text{Unrotate}
}
$$

여기서 중요한 것은 DAS가 hidden state 전체를 다른 값으로 교체하는 것이 아니라 **counterfactual hidden state**를 구성한다는 점이다. 계산의 대부분은 기존 base input에 그대로 묶여 있고, causal variable의 후보로 가정한 일부 representational component만 source input의 값으로 바뀐다.

---

## 4. Causal Alignment 학습

올바른 transformation $R$은 처음부터 알려져 있지 않다. DAS는 이를 $R^\theta$로 parameterize하고, high-level causal model이 제공하는 counterfactual behavior를 supervision으로 사용하여 alignment를 학습한다.

각 base input과 source input 집합에 대해 high-level model은 먼저 해당 interchange intervention에서 어떤 결과가 발생해야 하는지를 계산한다. Neural model에서는 현재의 candidate alignment를 이용해 distributed interchange intervention을 수행한다. DAS는 두 counterfactual computation의 결과가 일치하도록 $\theta$를 최적화한다.

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

여기서 가장 중요한 점은 neural model $\mathcal{L}$과 high-level causal model $\mathcal{H}$ 모두 학습 과정에서 고정되어 있다는 것이다. **학습되는 것은 오직 alignment뿐이다.** 따라서 DAS는 neural network가 우리가 가정한 알고리즘을 구현하도록 새롭게 학습시키는 방법이 아니다. 대신 이미 network 내부에 존재하는 representation 중에서, intervention을 수행했을 때 우리가 가정한 high-level algorithm과 동일한 causal behavior를 보이는 subspace가 존재하는지를 탐색한다.

예를 들어 high-level model에서 $X_j$를 변화시켰을 때 출력이 $A$에서 $B$로 변한다고 하자. DAS는 neural representation 내부에서 어떤 $Y_j$를 source representation으로 교체했을 때 동일하게 $A \rightarrow B$의 변화가 발생하는지를 찾는다. 현재 alignment가 이러한 효과를 재현하지 못하면 intervention loss가 커지고, gradient descent를 통해 rotation이 수정된다.

따라서 DAS의 핵심은 **high-level counterfactual behavior가 low-level representation을 찾기 위한 supervision으로 사용된다는 것**이다. 어떤 representation이 high-level variable과 비슷한 activation pattern을 가지기 때문에 causal representation이라고 주장하는 것이 아니라, 그 representation을 실제로 변화시켰을 때 예상된 causal consequence가 나타나기 때문에 causal alignment를 인정한다.

---

## 5. Representation as Counterfactual Behavior

Alignment를 학습한 뒤에는 **Interchange Intervention Accuracy (IIA)**를 사용하여 그 alignment가 실제로 high-level causal model을 얼마나 충실하게 재현하는지 평가한다. IIA는 low-level neural intervention의 결과와 high-level causal intervention의 결과가 일치하는 비율이다.

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

IIA가 $1$이라면 테스트한 모든 neural intervention이 high-level causal model이 예측한 counterfactual outcome을 그대로 재현한다는 의미이다. 이 지표는 일반적인 task accuracy와 본질적으로 다른 것을 측정한다. Neural network가 모든 관찰 가능한 입력에 대해 올바른 답을 출력하더라도, 특정 causal interpretation에 대한 IIA는 낮을 수 있다. 이런 경우 neural network와 high-level model은 일반적인 입력에서는 동일하게 행동하지만, 내부 변수를 조작하면 서로 다른 방식으로 반응한다.

$$
\boxed{
\text{Behavioral Equivalence}
\neq
\text{Causal-Mechanistic Equivalence}
}
$$

즉, 두 시스템이 동일한 입출력 관계를 보인다고 해서 동일한 내부 causal mechanism을 구현한다고 말할 수는 없다.

이 차이가 DAS가 representation을 해석하는 핵심 기준이다. Neural subspace $Y_j$가 high-level variable $X_j$에 대응하는 이유는 단순히 $Y_j$에서 $X_j$를 decode할 수 있기 때문이 아니다. 두 변수를 각각 intervention했을 때 동일한 downstream causal effect가 발생하기 때문이다.

$$
\boxed{
X_j \leftrightarrow Y_j
\quad
\text{when their interventions produce corresponding causal effects.}
}
$$

따라서 representation은 단순한 정적인 vector나 information container가 아니라 **counterfactual behavior의 패턴**으로 이해할 수 있다. 중요한 것은 hidden state 안에서 무엇을 읽어낼 수 있는가뿐 아니라, 그 hidden state를 변화시켰을 때 전체 시스템의 계산이 어떻게 달라지는가이다.

이 관점에서는 DAS 이후 등장한 representation-level intervention 방법들의 관계도 자연스럽게 이해할 수 있다. DAS와 Boundless DAS는 intervention을 이용하여 **causal representation을 발견하고 검증**한다. ITI와 RepE는 representation-level structure를 이용해 **모델의 행동을 조정**하며, ReFT는 representational intervention 자체를 학습하여 **task adaptation의 메커니즘**으로 사용한다.

각 방법의 목적은 다르지만 공통적으로 neural representation을 단순히 관찰하는 대상에서 **직접 조작하고 그 결과를 확인하는 대상**으로 바라본다. 결국 중요한 변화는 다음과 같이 요약할 수 있다.

$$
\boxed{
\text{Decode}
\rightarrow
\text{Intervene}
\rightarrow
\text{Observe Counterfactual Effects}
}
$$

DAS는 이 원리를 **interpretation의 기준**으로 사용하고, ReFT와 관련된 방법들은 유사한 representational intervention을 **adaptation과 control의 방법**으로 확장한다.
