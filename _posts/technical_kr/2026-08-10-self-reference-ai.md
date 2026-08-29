---
layout: post
title: Self-Reference AI
permalink: /blog/2026/self-reference-ai-kr/
mini-title: Self-Reference AI
---

## 1. Mind Viruses

[Mind Viruses: Self-Propagating Ideas in Multi-Agent LLM Systems](https://arxiv.org/abs/2608.10218)는 아이디어나 지시가 AI agent 사이에서 어떻게 지속되고 전파될 수 있는지를 다룬다. 한 agent가 받은 instruction은 그 agent의 행동을 변화시키고, persistent file에 기록된 뒤, 이후의 agent나 같은 agent의 다음 session에 다시 영향을 줄 수 있다. 흥미로운 점은 단순히 정보가 전달된다는 데 있지 않다. 어떤 정보는 agent의 행동 자체를 변화시켜 **자기 자신이 다시 기록되고 이후에도 유지될 가능성을 높인다.** <citation key="papadopoulos-etal-2026-mind-viruses"></citation>

특히 `SOUL.md`가 흥미롭다. 대화 context는 session이 끝나면 사라질 수 있지만 persistent file에 기록된 내용은 남을 수 있고, 그 내용이 이후 session에서 다시 읽히면 이전에 생성된 언어가 미래 행동을 결정하는 조건의 일부가 된다. 따라서 `SOUL.md`는 단순한 memory라기보다, **agent가 자신에 대해 남긴 표현을 미래의 행동으로 다시 연결하는 persistent interface**로 볼 수 있다.

$$
\text{Behavior}_t
\rightarrow
\text{Self-Description}_t
\rightarrow
\texttt{SOUL.md}
\rightarrow
\text{Behavior}_{t+1}
$$

여기서 중요한 것은 agent가 자기 자신에 대해 말하는 것과 실제로 행동하는 것이 반드시 일치하지 않는다는 점이다. Agent는 자신이 특정한 원칙을 따른다고 기술하면서 실제로는 그 원칙과 다른 행동을 할 수 있고, 반대로 자신의 행동을 비교적 정확하게 기술하면서 그 description을 이후 행동에도 지속적으로 반영할 수도 있다. `SOUL.md`와 같은 persistent mechanism은 이러한 일치나 불일치를 한 번의 interaction에서 끝나는 문제가 아니라 시간에 따라 누적되는 문제로 만든다.

> Agent가 자기 자신에 대해 말하는 것과 실제로 행동하는 것 사이의 관계는 얼마나 안정적으로 유지될 수 있는가?

이 관점에서 관심 있는 문제는 단순한 memory가 아니라 **persistent self-representation과 behavioral consistency**이다. 부정확한 self-description이 남으면 이후 행동이 그 representation에 맞추어 변할 수 있고, 반대로 안정적인 self-description은 행동의 일관성을 유지하는 데 사용될 가능성도 있다.

<figure>
    <img src="https://d2acbkrrljl37x.cloudfront.net/MatrixFigures/Research/self-reference-ai.webp" />
</figure>


---

## 2. Quine and Self-Reference

Self-reference의 가장 단순한 computational example 중 하나가 [Quine program](https://en.wikipedia.org/wiki/Quine_(computing))이다. Quine은 자신의 source code를 외부 input으로 읽지 않고도 실행 결과로 자기 자신의 source code를 출력하는 program이다. 중요한 점은 이러한 자기 재현이 그 자체로 어떠한 contradiction도 만들지 않는다는 것이다. 즉, computational system은 자기 자신의 representation을 다시 만들어낼 수 있으며, **self-reference 자체가 곧 paradox를 의미하지는 않는다.** <citation key="wikipedia-quine-computing"></citation>

[Self-Reference and Paradox](https://plato.stanford.edu/entries/self-reference/)에서 논의되듯 실제 문제가 발생하는 지점은 단순히 어떤 것이 자기 자신을 참조한다는 사실이 아니다. 그보다 중요한 것은 **자기 자신에 대해 무엇을 주장하는지, 그리고 그 system이 그 representation에 어떤 operation이나 predicate를 적용할 수 있는지**이다. <citation key="sep-self-reference"></citation>


---

## 3. Gödel Coding and the Diagonal Lemma

Gödel은 formal system이 자기 자신의 syntax를 다룰 수 있도록 symbol, formula, sentence, proof와 같은 syntactic object를 자연수로 encoding하는 방법을 도입했다. 이를 [Gödel numbering](https://plato.stanford.edu/entries/goedel-incompleteness/sup1.html), 또는 **Gödel coding**이라고 한다. 문장 $\phi$의 Gödel number를 $\ulcorner \phi \urcorner$라고 쓰면, 문장 자체를 arithmetic object로 바꿔 다룰 수 있게 된다. 따라서 숫자를 다룰 수 있는 formal system은 숫자에 대한 reasoning을 통해 간접적으로 **자기 자신의 문장과 proof에 대해서도 말할 수 있게 된다.** <citation key="sep-goedel-numbering"></citation>

$$
\text{Sentence}
\rightarrow
\ulcorner \text{Sentence} \urcorner
$$

하지만 Gödel coding은 representation을 가능하게 할 뿐, 그 자체로 self-reference를 만들지는 않는다. 실제로 자기 자신을 가리키는 문장을 구성하게 해주는 핵심 장치가 [Diagonal Lemma](https://plato.stanford.edu/entries/self-reference/)이다. 충분한 표현력을 가진 theory $T$와 하나의 free variable을 가진 formula $\phi(x)$가 주어지면, 어떤 sentence $G$를 구성하여 다음을 만족시킬 수 있다. <citation key="sep-self-reference"></citation>

$$
T \vdash
G
\leftrightarrow
\phi(\ulcorner G \urcorner)
$$

즉 $G$는 자기 자신의 Gödel number에 $\phi$라는 property를 적용한 내용을 말한다. Self-reference를 단순히 "this sentence" 같은 자연어 표현으로 집어넣는 것이 아니라, formal system 내부에서 체계적으로 만들어낼 수 있다는 것이 중요하다.

$$
\boxed{
\text{Gödel Coding}
+
\text{Diagonalization}
\rightarrow
\text{Formal Self-Reference}
}
$$

이런 의미에서 Diagonal Lemma는 formal language 안에서 self-reference를 만들어내는 **fixed-point principle**로 볼 수 있다.


---

## 4. Tarski: The Limit of Internal Truth

[Tarski's work on truth](https://plato.stanford.edu/entries/tarski-truth/)는 충분한 표현력을 가진 language가 자기 자신의 sentence에 대한 truth를 그 language 내부에서 완전히 정의할 수 있는지를 다룬다. 예를 들어 동일한 language 내부에 모든 sentence $A$에 대해 다음을 만족하는 unrestricted truth predicate $True(x)$가 있다고 가정해보자. <citation key="sep-tarski-truth"></citation>

$$
True(\ulcorner A \urcorner)
\leftrightarrow
A
$$

이제 Diagonal Lemma에 $\phi(x)=\neg True(x)$를 적용하면, 자기 자신에 대해 "나는 참이 아니다"라고 말하는 형태의 sentence $L$을 구성할 수 있다.

$$
L
\leftrightarrow
\neg True(\ulcorner L \urcorner)
$$

그런데 앞서 가정한 truth condition에 따르면 $True(\ulcorner L \urcorner)\leftrightarrow L$ 역시 성립하므로, 두 관계를 결합하면 결국 $L\leftrightarrow\neg L$이라는 liar-style contradiction에 도달한다.

여기서 중요한 것은 **self-reference 자체가 contradiction을 일으킨다는 것이 아니다.** 문제는 충분한 표현력을 가진 language가 자기 자신의 모든 문장에 적용되는 unrestricted truth predicate까지 같은 language 내부에 포함하려 할 때 발생한다.

> 충분히 강한 language는 자기 자신의 모든 truth를 같은 language 내부에서 완전히 정의할 수 없다.

이 때문에 formal theory of truth에서는 보통 어떤 문장을 사용하는 **object language**와 그 문장의 truth를 바깥에서 이야기하는 **metalanguage**를 구분한다. 더 자세한 논의는 [Self-Reference and Paradox](https://plato.stanford.edu/entries/self-reference/)와 [Tarski's Truth Definitions](https://plato.stanford.edu/entries/tarski-truth/)를 참고할 수 있다. <citation key="sep-self-reference"></citation> <citation key="sep-tarski-truth"></citation>


---

## 5. Gödel's Incompleteness Theorems

[Gödel's Incompleteness Theorems](https://plato.stanford.edu/entries/goedel-incompleteness/)은 Tarski의 truth와는 다른 개념인 **provability**, 즉 무엇이 formal system 내부에서 증명 가능한지를 다룬다. $Prov_T(x)$를 "번호가 $x$인 sentence는 theory $T$에서 증명 가능하다"라는 의미라고 하자. Diagonal Lemma를 $\phi(x)=\neg Prov_T(x)$에 적용하면 다음을 만족하는 Gödel sentence $G_T$를 구성할 수 있다. <citation key="sep-goedel-incompleteness"></citation>

$$
T \vdash
G_T
\leftrightarrow
\neg Prov_T(\ulcorner G_T \urcorner)
$$

직관적으로 $G_T$는 **"이 문장은 $T$에서 증명될 수 없다"**고 말하는 문장이다. 이제 $T$가 실제로 $G_T$를 증명한다고 가정하면 문제가 생긴다. $G_T$는 자신이 증명될 수 없다고 말하고 있는데 실제로는 $T$ 안에서 증명된 셈이기 때문이다. 따라서 $T$가 consistent하다면 $G_T$를 증명할 수 없다.

$$
\text{Consistency of }T
\Rightarrow
T\nvdash G_T
$$

이것이 Gödel's First Incompleteness Theorem을 이해하는 가장 중요한 intuition이다. 결과는 충분히 강한 formal system이 반드시 contradiction에 빠진다는 것이 아니다. 오히려 **contradiction을 피하려면 모든 statement를 증명할 수 있다는 것을 포기해야 한다.**

$$
\boxed{
\text{Consistency}
\Rightarrow
\text{Incompleteness}
}
$$

이 지점에서 Tarski와 Gödel의 차이가 드러난다. Tarski에서는 자기 language의 **truth**를 unrestricted하게 내부화하려 할 때 liar-style contradiction이 발생한다. 반면 Gödel은 **provability**를 다룬다. 어떤 statement가 true하더라도 반드시 그 formal system 내부에서 prove할 수 있어야 하는 것은 아니기 때문에, diagonalization이 곧바로 contradiction으로 이어지는 대신 **truth와 provability 사이에 간극**을 만든다.

Gödel's Second Incompleteness Theorem은 이 한계를 system의 self-certification 문제로 확장한다. First Theorem에서 사용한 reasoning 자체도 충분히 강한 $T$ 내부에서 형식화할 수 있으며, 대략적으로는 $T$가 consistent하다면 자신의 Gödel sentence $G_T$가 성립해야 한다는 관계를 얻을 수 있다.

$$
Con(T)
\rightarrow
G_T
$$

그런데 만약 $T$가 자기 자신의 consistency인 $Con(T)$까지 증명할 수 있다면, 위의 implication과 결합하여 결국 $G_T$도 증명할 수 있게 된다. 하지만 consistent한 $T$는 앞서 본 것처럼 $G_T$를 증명할 수 없다. 따라서 필요한 조건들을 만족하는 consistent formal system은 자기 자신의 consistency를 내부에서 증명할 수 없다.

$$
\boxed{
T\nvdash Con(T)
}
$$

따라서 두 incompleteness theorem의 차이는 비교적 간단하다. First Theorem은 **체계가 모든 것을 증명할 수 없다는 것**을 보여주고, Second Theorem은 그보다 더 구체적으로 **체계가 자기 자신의 consistency조차 내부에서 완전히 증명할 수 없다는 것**을 보여준다. 이런 의미에서 Tarski는 internal truth의 한계, Gödel's First Theorem은 internal provability의 한계, Second Theorem은 internal self-certification의 한계를 다룬다고 볼 수 있다. <citation key="sep-goedel-incompleteness"></citation>


---

## 6. Self-Reference as a Broader Theme

Quine, Tarski, Gödel, 그리고 persistent AI agent는 모두 self-reference라는 넓은 주제와 관련되어 있지만, **서로 같은 문제를 다루는 것은 아니다.** Quine은 program이 자기 자신의 representation을 다시 생성할 수 있음을 보여주고, Gödel coding과 Diagonal Lemma는 formal system 내부에서 자기 자신을 참조하는 sentence를 구성하는 방법을 보여준다. Tarski는 language가 자기 자신의 truth를 내부에서 정의하는 데 따르는 한계를 다루며, Gödel은 formal system이 자기 자신의 provability를 다룰 때 발생하는 completeness와 self-certification의 한계를 다룬다.

반면 [Mind Viruses](https://arxiv.org/abs/2608.10218)가 다루는 것은 AI agent에서 정보가 behavior와 memory를 통해 지속되고 전파되는 현상이다. 따라서 이를 Gödel이나 Tarski의 theorem으로 설명할 이유는 없다. 공통점은 보다 느슨한 수준에 있다. 이들 모두 **자기 자신에 관한 representation이 다시 system의 operation에 들어올 때 어떤 일이 생기는가**라는 self-reference의 문제를 생각하게 만든다.

AI agent에서는 이 질문이 natural language를 통해 훨씬 직접적으로 나타난다. Agent는 자신의 goal, rule, identity, previous behavior에 대해 말할 수 있고, 그 description이 persistent state에 저장되면 이후 agent의 행동을 결정하는 context의 일부가 될 수 있다. 이때 self-description은 더 이상 자기 자신에 대한 단순한 report가 아니라, **미래의 자신을 구성하는 input**이 된다.

$$
\text{Self-Representation}_t
\rightarrow
\text{Persistent State}
\rightarrow
\text{Behavior}_{t+1}
$$

따라서 AI agent에서 흥미로운 문제는 Gödel이나 Tarski의 theorem이 직접 적용되는가가 아니다. 오히려 agent가 자기 자신에 대해 만든 representation과 실제 행동이 얼마나 일치하는지, 그리고 그 관계가 시간이 지나도 얼마나 안정적으로 유지되는지가 더 직접적인 문제이다.

> Agent가 자기 자신에 대해 만든 표현이 미래의 행동을 다시 구성할 때, 그 agent는 얼마나 일관된 자기 표현을 유지할 수 있는가?

이 점에서 `SOUL.md`는 단순한 memory가 아니라 **persistent self-representation을 가능하게 하는 mechanism**으로 볼 수 있다. 자기 자신에 대한 언어적 description이 일회성 output으로 사라지는 것이 아니라, 이후 행동을 형성하는 active component가 될 수 있기 때문이다.