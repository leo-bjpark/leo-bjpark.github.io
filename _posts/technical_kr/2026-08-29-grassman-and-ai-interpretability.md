---
layout: post
title: Grassmannian and AI Interpretability
permalink: /blog/2026/grassman-and-ai-interpretability-kr/
mini-title: Interpretability Geometry
---

## 1. Block-Sparse Featurizer

[Block-Sparse Featurizer (BSF)](https://www.goodfire.com/research/bsf-vision)는 최근 block-sparse visual concept manifold 연구 <citation key="structuring-sparsity"></citation>에서 제안된 방법으로, **neural representation의 geometry를 보다 적극적으로 고려한다는 점**에서 흥미롭다. 기존의 sparse feature 방법들은 하나의 해석 가능한 concept이 activation space의 하나의 direction에 대응한다고 가정하는 경우가 많다. 반면 BSF는 하나의 concept이 **자체적인 내부 geometry를 가지는 low-dimensional space**를 차지할 수 있다고 본다.

이러한 관점은 특히 vision에서 자연스럽다. Pose, viewpoint, orientation, shape, lighting, texture와 같은 시각적 속성은 연속적으로 변화하지만 여전히 동일한 semantic concept에 속할 수 있다. 어떤 물체의 pose나 viewpoint가 바뀐다고 해서 그 물체의 정체성이 사라지는 것은 아니다. 따라서 이러한 concept은 하나의 scalar axis를 따라 표현되기보다는, 훨씬 큰 activation space 안의 구조화된 low-dimensional region으로 표현될 가능성이 있다.

여기에는 흥미로운 역사적 연결점이 있다. 초기의 curve detector 연구 <citation key="curve-detectors"></citation>는 vision에서 neuron-level interpretability를 보여준 대표적인 사례다. InceptionV1의 개별 neuron들이 서로 다른 orientation을 가진 curve에 선택적으로 반응한다는 것이 관찰되었다. BSF는 이 고전적인 사례를 다시 분석하면서, 서로 독립적인 것처럼 보였던 여러 curve detector를 사실은 **하나의 연속적인 curve manifold를 서로 다르게 읽어내는 readout**으로 해석할 수 있음을 보여준다. 즉, 독립적인 여러 feature처럼 보였던 것들이 실제로는 하나의 geometric object를 서로 다른 방식으로 바라본 결과일 수 있다.

비슷한 문제는 language model에서도 발견된다. 일부 LLM feature는 본질적으로 multidimensional할 수 있다는 연구가 있다 <citation key="multidimensional-features"></citation>. 특히 days of the week이나 months of the year와 같은 concept이 circular representation을 형성한다는 것이 관찰되었으며, intervention을 통해 이러한 구조가 modular arithmetic과 같은 계산에도 실제로 관여한다는 증거가 제시되었다.

보다 최근의 Anthropic 연구 <citation key="counting-manifolds"></citation> 역시 LLM 내부의 계산이 structured low-dimensional geometry를 통해 구성될 수 있다는 흥미로운 사례를 보여준다.

이러한 연구들을 함께 보면 interpretability의 질문 자체가 조금씩 바뀌고 있음을 알 수 있다. 단순히

> 어떤 neuron 또는 direction이 하나의 concept을 표현하는가?

만을 묻는 것이 아니라,

> 어떤 geometric object가 그 concept을 표현하며, 모델은 그 geometry를 이용해 어떻게 계산하는가?

를 묻기 시작한 것이다.

이 점은 Goodfire의 최근 연구 방향이 특히 흥미로운 이유이기도 하다. Geometry를 feature를 추출한 뒤 사후적으로 시각화하는 대상으로만 보는 것이 아니라, **featurizer를 설계할 때부터 고려해야 하는 design constraint**로 다룬다.

이론적 동기는 sparse autoencoder와 concept geometry 사이의 duality를 다룬 연구 <citation key="projecting-assumptions"></citation>에서 온다. 이 연구의 핵심은 featurizer의 architecture와, 그 featurizer가 암묵적으로 가정하는 neural concept의 geometry 사이에 깊은 연결이 있다는 것이다. 즉, featurizer는 중립적인 관찰 도구가 아니다. 특정 architecture를 선택한다는 것은 동시에 어떤 종류의 representational geometry를 잘 발견할 것인지 선택하는 것이기도 하다.

BSF는 여기에 Additive Mixture of Manifolds 가설 <citation key="concept-manifolds"></citation>을 결합한다. 이 가설에서는 하나의 activation을 소수의 active manifold-valued factor가 더해진 결과로 본다. 이 두 가정의 formal definition은 글 마지막에서 따로 정리한다.

Architecture 관점에서의 결론은 단순하다. **Concept 자체가 multidimensional할 수 있다면, featurizer의 기본 단위 역시 multidimensional해야 한다.**

하나의 block $g$에 대해 BSF는

$$
D_g\in\mathbb{R}^{k\times d},
$$

를 사용한다. $D_g$의 $k$개 row는 함께 하나의 low-dimensional representational subspace를 정의한다. Activation $x\in\mathbb{R}^d$가 주어지면, 해당 block의 좌표는

$$
z_g=D_gx\in\mathbb{R}^k
$$

로 얻어진다.

BSF는 $z_{g,i}$의 각 coordinate를 독립적으로 sparse하게 만드는 대신, block 전체를 하나의 단위로 취급한다. Block의 activation 정도는 다음과 같은 block norm으로 측정한다.

$$
\|z_g\|_2
=
\sqrt{\sum_{i=1}^{k}z_{g,i}^2}.
$$

이후 소수의 block만 남기고 나머지는 제거한다. 반면 선택된 block 내부의 coordinate들은 자유롭게 변화할 수 있다. 따라서 BSF에서는 **어떤 concept이 활성화되었는가**와 **그 concept 내부에서 representation이 어디에 위치하는가**를 자연스럽게 분리할 수 있다.

이러한 의미에서 BSF는 어느 정도 **Mixture-of-Experts (MoE)**와 비슷하게 볼 수도 있다. MoE가 소수의 computational expert를 sparse하게 선택한다면, BSF는 소수의 representational factor 또는 subspace를 sparse하게 선택한다. 물론 완전히 동일한 구조는 아니다. BSF의 block은 독립적인 neural network expert가 아니다. 하지만 복잡한 상태를 소수의 specialized component를 통해 설명한다는 점에서는 유사한 직관을 가진다.


<figure>
    <img src="https://d2acbkrrljl37x.cloudfront.net/MatrixFigures/Research/bfs-and-grassmannian.webp" />
    <figcaption>
    <figtitle>Block-Sparse Featurizer와 Grassmannian 해석.</figtitle>
    <figdetail>
하나의 concept을 단일 feature direction으로 표현하는 대신, BSF는 concept마다 low-dimensional block을 할당한다. 입력 representation $x$에 대해 각 block은 $z_g=D_gx$라는 좌표를 만들고, block sparsity를 통해 강하게 활성화된 소수의 factor만 남긴다. 이때 $\|z_g\|_2$는 block-level activation을 나타내고, block 내부의 coordinate들은 해당 concept 안에서 나타나는 multidimensional variation을 보존한다.
    </figdetail>
    </figcaption>
</figure>


## 2. Encoder–Decoder Matching and Subspace Projection

**Grassmannian BSF**에서 특히 흥미로운 부분은 encoder와 decoder의 matching이다. Column-vector notation을 사용하고 설명을 단순화하기 위해 learned global scale은 생략하자. Encoder는 입력 representation을 $z_g=D_gx$와 같이 block coordinate로 보내고, decoder는 동일한 basis의 transpose를 사용해 $\hat{x}_g=D_g^\top z_g$와 같이 다시 원래 activation space로 보낸다.

즉, 하나의 동일한 matrix가 representation을 어떻게 **decompose**할 것인지와, 다시 어떻게 **recombine**할 것인지를 동시에 결정한다.


---

### Example

원래 coordinate axis와 정렬되어 있지 않은 다음과 같은 block을 생각해보자.

$$
D_g=
\begin{bmatrix}
\frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}} & 0\\
0 & 0 & 1
\end{bmatrix},
\qquad
x=
\begin{bmatrix}
4\\
1\\
3
\end{bmatrix}.
$$

$D_g$의 두 row는 하나의 2-dimensional subspace에 대한 orthonormal basis를 형성한다. Encoder는 먼저 $x$를 이 basis에서의 coordinate로 표현한다.

$$
z_g
=
D_gx
=
\begin{bmatrix}
\frac{5}{\sqrt{2}}\\
3
\end{bmatrix}.
$$

Matched decoder는 이 coordinate를 다시 원래 representation space로 보낸다.

$$
D_g^\top z_g
=
\begin{bmatrix}
\frac{5}{2}\\
\frac{5}{2}\\
3
\end{bmatrix}.
$$

결과는 원래의 $x$와 동일하지 않다. 대신 해당 block이 표현하는 subspace 위로 $x$를 projection한 결과가 된다. 따라서 encoder는 $x$를 **특정 subspace 안에서 표현**하고, decoder는 동일한 subspace가 설명할 수 있는 $x$의 성분을 다시 reconstruction한다고 볼 수 있다.

---

보다 일반적으로 $D_g$의 row가 orthonormal하여 $D_gD_g^\top=I_k$를 만족한다고 하자. 그러면 encoding 이후 decoding을 수행한 결과는

$$
\hat{x}_g
=
D_g^\top D_gx
=
P_gx,
\qquad
P_g:=D_g^\top D_g
$$

가 된다.

여기서 $P_g$는 $D_g$의 row들이 span하는 $k$-dimensional subspace에 대한 orthogonal projection이다.

중요한 점은 $k<d$일 때 $D_gD_g^\top=I_k$라고 해서 $D_g^\top D_g=I_d$가 되는 것은 아니라는 것이다. 대신 $D_g^\top D_g$는 rank-$k$ projection matrix가 된다. 즉, $x$에서 block subspace 안에 존재하는 성분은 보존하고 그 subspace와 orthogonal한 성분은 제거한다.

전체 과정은

$$
x
\;\longrightarrow\;
D_gx
\;\longrightarrow\;
D_g^\top D_gx
$$

로 볼 수 있다. 즉, 원래 representation에서 출발해 **subspace 내부의 coordinate**로 이동한 뒤, 다시 **그 subspace가 설명하는 원래 representation의 성분**으로 돌아오는 것이다.

이렇게 보면 encoder–decoder sharing의 geometric 의미도 명확해진다. Encoder는 $x$가 해당 subspace 안에서 어떻게 decomposed되는지를 결정하고, decoder는 그 component가 다시 원래 activation space에서 어떻게 recombined되는지를 결정한다. 두 과정이 같은 basis를 사용하기 때문에 decomposition과 reconstruction이 동일한 geometric object를 기준으로 이루어진다.

여러 block이 동시에 활성화되는 경우 reconstruction은

$$
\hat{x}
=
\sum_{g\in\mathcal{A}}D_g^\top z_g
$$

의 형태가 된다. 여기서 $\mathcal{A}$는 선택된 block들의 집합이다. 따라서 BSF는 하나의 activation을 sparse한 subspace-level contribution으로 분해한 뒤 다시 결합하는 구조로 볼 수 있다.


## 3. Grassmannian and Basis-Rotation Invariance

다음으로 중요한 것은 특정한 **basis** $D_g$와, 그 basis가 표현하는 **subspace**를 구분하는 것이다. $D_g$의 row들이 $\mathbb{R}^d$ 안의 $k$-dimensional subspace에 대한 orthonormal basis를 형성한다고 하자. 하지만 하나의 subspace를 표현하는 basis는 유일하지 않다.

임의의 orthogonal matrix $Q\in O(k)$에 대해

$$
D'_g=QD_g
$$

와 같이 block 내부의 basis를 회전시킬 수 있다.

각각의 basis direction은 달라지지만, 두 matrix가 span하는 subspace는 동일하다.

$$
\operatorname{span}(D'_g)
=
\operatorname{span}(QD_g)
=
\operatorname{span}(D_g).
$$

반면 block 내부의 coordinate는 선택한 basis에 따라 달라진다. $z_g=D_gx$이므로 회전된 basis에서는

$$
z'_g
=
D'_gx
=
Qz_g
$$

가 된다.

즉, block 내부 coordinate 자체는 **basis-dependent**하다.

하지만 $Q$가 orthogonal transformation이기 때문에 $\ell_2$ norm은 보존된다.

$$
\|z'_g\|_2
=
\|Qz_g\|_2
=
\|z_g\|_2.
$$

따라서 내부 basis를 회전시키더라도 해당 block이 선택되는지 여부에는 영향을 주지 않는다.

더 중요한 것은 reconstructed component 역시 변하지 않는다는 것이다.

$$
D_g'^\top D'_g
=
D_g^\top Q^\top QD_g
=
D_g^\top D_g
=
P_g.
$$

따라서 내부 coordinate는 자유롭게 회전할 수 있지만, **어떤 block이 활성화되는가**와 **그 block이 원래 representation의 어떤 성분을 설명하는가**는 모두 그대로 유지된다.

이것이 자연스럽게 Grassmannian으로 이어진다. Grassmannian

$$
\mathrm{Gr}(k,d)
=
\left\{
V\subseteq\mathbb{R}^d
\mid
\dim(V)=k
\right\}
$$

은 $\mathbb{R}^d$ 안에 존재하는 모든 $k$-dimensional linear subspace의 공간이다.

$D_g$는 하나의 subspace를 표현하기 위한 특정 orthonormal frame일 뿐이다. $QD_g$와 같이 내부적으로 회전된 모든 frame은 Grassmannian 위에서는 정확히 같은 point를 나타낸다. 다시 말하면 하나의 feature를 다음 equivalence class로 생각할 수도 있다.

$$
[D_g]
=
\{QD_g\mid Q\in O(k)\}.
$$

따라서 의미 있는 대상은 특정한 basis $D_g$ 자체가 아니라

$$
V_g=\operatorname{span}(D_g)
$$

이다.

Basis는 feature를 기술하기 위해 선택한 coordinate system일 뿐이며, **실제 geometric object는 subspace 자체**이다.


## 4. What This Means for Interpretability

Grassmannian 관점은 interpretability에서 무엇을 하나의 기본 단위로 볼 것인지 바꾼다. 기존 sparse representation에서는 개별 direction에 semantic meaning을 부여하려는 경우가 많았다. 반면 BSF는 일부 concept이 **structured low-dimensional object**로 이해되는 것이 더 자연스러울 수 있음을 제안한다.

특히 continuous concept에서 이러한 관점이 유용하다. Block magnitude $\|z_g\|_2$는 **해당 factor가 활성화되었는가**를 나타낼 수 있고, block 내부에서 $z_g$가 가지는 위치나 방향은 **그 factor가 어떤 형태로 instantiated되었는가**를 표현할 수 있다.

Curve detector의 예시는 이 차이를 잘 보여준다. 초기 neuron-level 분석 <citation key="curve-detectors"></citation>에서는 서로 다른 curve orientation에 반응하는 여러 unit을 개별적으로 해석했다. 하지만 BSF 관점에서는 이를 하나의 shared continuous geometry를 서로 다른 방식으로 읽어내는 readout들로 볼 수 있다.

여기서 주의할 점은 **block subspace 자체와 concept manifold를 동일시해서는 안 된다는 것**이다. BSF는 하나의 concept manifold가 비교적 low-dimensional한 linear span 안에 존재한다고 가정한다. 하지만 그 span 안에서 실제 representation은 circle, sphere, curved surface 또는 다른 nonlinear structure를 형성할 수 있다. 즉, block은 manifold 그 자체라기보다 **그 manifold의 내부 geometry가 존재할 수 있는 coordinate space**를 제공한다.

이렇게 보면 block sparsity의 역할도 보다 명확해진다.

- block 사이의 sparsity는 **어떤 conceptual factor가 존재하는가**를 나타내고,
- block 내부의 coordinate는 **그 factor 안에서의 variation**을 표현하며,
- encoder–decoder matching은 representation이 임의적인 basis 선택에 의존하지 않도록 한다.

따라서 핵심적인 변화는

$$
\boxed{
\text{isolated feature direction}
\;\longrightarrow\;
\text{structured concept geometry}
}
$$

로 요약할 수 있다.

모든 interpretable concept이 반드시 one-dimensional하다고 미리 가정하는 대신, BSF는 multidimensional concept 자체가 interpretability의 atomic unit이 될 수 있도록 featurizer architecture를 바꾼다.

개인적으로 이 연구에서 가장 흥미로운 부분도 여기에 있다. Interpretability method는 이미 존재하는 구조를 단순히 있는 그대로 보여주는 중립적인 도구가 아니다. **어떤 architecture를 선택하느냐가 우리가 어떤 종류의 representation structure를 발견할 수 있는지를 결정한다.**


## Formal Foundations

BSF의 동기는 크게 두 가지 가정으로 정리할 수 있다. 하나는 **featurizer–geometry duality**, 다른 하나는 **Additive Mixture of Manifolds**이다.


<blockquote class="paper-definition" markdown="1">

<div class="paper-definition-title"><span>Definition 1.</span> Featurizer–Geometry Duality</div>

Featurizer–geometry duality framework <citation key="projecting-assumptions"></citation>를 따라, $\mathcal{F}$를 featurizer들의 family라고 하자. $\mathcal{F}$에는 다음과 같은 representational geometry들의 family가 대응된다.

$$
\mathfrak{G}(\mathcal{F}),
$$

여기서 $\mathfrak{G}(\mathcal{F})$는 해당 architecture가 atomic feature로서 자연스럽게 복원할 수 있는 구조들의 종류를 의미한다.

**Featurizer–geometry duality**는 $\mathcal{F}$의 architecture가 $\mathfrak{G}(\mathcal{F})$를 결정한다는 원리이다. 따라서 featurizer의 구조를 바꾸면 자연스럽게 복원할 수 있는 concept의 class 역시 달라진다.

개념적으로는 다음과 같이 표현할 수 있다.

$$
\boxed{
\text{featurizer architecture}
\quad\longleftrightarrow\quad
\text{assumed concept geometry}
}
$$

</blockquote>

예를 들어 direction-based sparse autoencoder는 개별 direction의 sparse combination으로 표현될 수 있는 concept을 자연스럽게 선호한다. 반대로 실제 concept들이 서로 다른 dimensionality를 가지거나 nonlinear internal structure를 가진다면, 이러한 architecture는 하나의 concept을 여러 feature로 분절하거나 그 구조를 제대로 드러내지 못할 수 있다.

따라서 interpretability 관점에서 중요한 결론은 **featurizer를 설계하는 것 자체가 neural representation의 geometry에 대한 하나의 hypothesis를 설정하는 것과 같다**는 것이다.


<blockquote class="paper-definition" markdown="1">

<div class="paper-definition-title"><span>Definition 2.</span> Additive Mixture of Manifolds</div>

Concept-manifold formulation <citation key="concept-manifolds"></citation>을 따라,

$$
\mathcal{N}_1,\ldots,\mathcal{N}_M
$$

을 $\dim\mathcal{N}_i\ll d$를 만족하는 abstract manifold라고 하자. 각각은

$$
\gamma_i:\mathcal{N}_i\rightarrow\mathbb{R}^d
$$

를 통해 activation space로 immersed되며, 그 image를

$$
\mathcal{M}_i
=
\gamma_i(\mathcal{N}_i)
\subseteq
\mathbb{R}^d
$$

라고 하자.

Activation $x$가 다음 조건을 만족하면 **Additive Mixture of Manifolds** model을 따른다고 한다.

$$
x
=
\sum_{i\in S}m_i,
\qquad
m_i\in\mathcal{M}_i,
\qquad
S\subseteq[M],
\qquad
|S|\ll M,
$$

여기서 $S$는 $x$에서 활성화된 소수의 factor를 나타낸다.

동일하게,

$$
x
\in
\sum_{i\in S}\mathcal{M}_i
$$

라고 쓸 수 있으며, 따라서 activation은 sparse한 **Minkowski sum of manifolds** 위에 존재한다고 볼 수 있다.

</blockquote>

이 model은 representation 문제를 자연스럽게 두 가지 질문으로 나눈다.

1. **어떤 factor가 활성화되어 있는가?**
2. **각 active factor 내부에서 representation은 어디에 위치하는가?**

첫 번째는 자연스럽게 sparsity의 문제가 된다. 반면 두 번째 문제를 표현하려면 각 factor의 internal geometry를 유지할 수 있을 만큼 충분한 dimension이 필요하다.

Block-sparse formulation <citation key="structuring-sparsity"></citation>은 여기에 각 manifold가 low-dimensional linear span 안에 존재한다는 추가 가정을 둔다.

$$
V_g
=
\operatorname{span}(\mathcal{M}_g),
\qquad
\dim(V_g)=k\ll d.
$$

$V_g$에 대한 orthonormal frame $D_g$를 선택하면 각 manifold contribution을 block coordinate $z_g$를 통해 표현할 수 있다. 따라서 activation은 개략적으로

$$
x
=
\sum_{g\in S}D_g^\top z_g+\varepsilon
$$

와 같이 쓸 수 있으며, 이때 nonzero block은 소수만 존재한다.

바로 이 지점에서 block sparsity가 underlying geometry에 맞는 prior가 된다. 즉, **sparsity는 factor 사이에 적용하고, 각 active factor 내부에서는 dense한 multidimensional variation을 그대로 유지한다.**