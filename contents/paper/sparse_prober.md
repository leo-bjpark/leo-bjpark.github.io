---
layout: distill-reading
bibliography: all.bib
giscus_comments: false
disqus_comments: false
date: 2025-12-28
featured: true
img: assets/img/feigenbaum.png
title: 'Sparse Prober'
category: 'AI'
description: ''
_styles: >
    table {
        padding-top: 200px;
        margin-bottom: 2.5rem;
        border-bottom: 2px;
        width: 120%;
        margin-left: -10%;
        margin-right: -10%;
        table-layout: fixed;
    }
    .table {
        padding-top: 200px;
        margin-bottom: 2.5rem;
        border-bottom: 2px;
        width: 120%;
        margin-left: -10%;
        margin-right: -10%;
        table-layout: fixed;
    }
    .p {
        font-size: 20px;
        font-weight: 250;
    }
    .styled-image {
        border-radius: 15px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        margin: 20px auto;
        transition: transform 0.3s ease;
        display: block;
    }
    .styled-image:hover {
        transform: scale(1.2);
    }
    td, th {
        font-size: 1.10rem;
        font-family: 'Times New Roman', Times, serif;
    }
---

Causal Sparse Prober: Understanding Linear Probing in Transformers


- 본 논문은 Transformer MLP에 대한 **linear probing의 학습 원리와 작동 메커니즘**을 이해하기 위해,  
  MLP output 공간에서의 concept direction 탐지와 MLP hidden 뉴런 공간에서의 probing 사이의 연결고리를 이론적으로 정립한다.  
  이를 통해 output-level linear probing이 hidden-level neuron probing으로 환원될 수 있음을 보인다.

- 컨셉 뉴런의 탐지를 **noise neuron 제거 문제**로 재해석함으로써,  
  sparse prober를 설계하기 위한 정량적 기준과 원칙을 제시한다.  
  이는 단순히 활성화가 큰 뉴런을 선택하는 접근과 구별되는, 해석 가능하고 목적 지향적인 sparsity의 근거를 제공한다.

- 제안하는 sparse prober는 기존의 다양한 neuron ranking 방법들에 비해,  
  **불필요한 뉴런 제거(neuron ablation)** 과제에서 더 높은 AUROC 성능을 보이며,  
  concept-relevant neuron을 선별하는 데 있어 실질적인 우수성을 입증한다.

- 또한 sparse prober의 한계와 적용 범위를 명확히 논의함으로써,  
  단순한 activation 상관관계 포착을 넘어 **prober의 역할을 확장할 수 있는 향후 연구 방향**을 제시한다.

- Sparse prober는 뉴런이 **국소적(local) 개념을 부분적으로 담지할 수 있음**을 강조한다.  
  최근 distributed representation 관점에서는 회전 불변성이나 sparse dictionary learning을 통해 의미를 해석해야 한다는 주장이 제기되고 있으나,  
  Transformer를 **key–value memory와 residual stream에 대한 read/write 메커니즘**으로 해석할 경우,  
  개념의 형성과 탐지에 기여하는 **sparse하게 작동하는 뉴런을 명시적으로 식별하는 문제는 여전히 중요하다**.

- Transformer에서 선형 prober를 넘어선 non-linear prober는,  
  무작위에 가까운 레이블에도 쉽게 학습되는 것으로 알려져 있다.  
  따라서 뉴런과 레이블 간의 실제 상관관계를 이해하기 위해서는,  
  **선형 모델을 통해 해석 가능성을 최대한 보존하는 접근이 필요하다**.  
  다만 학습 관점에서 보면 모든 뉴런을 포함하는 linear probing이 성능 면에서는 유리할 수 있다.  
  그러나 **뉴런이 활성화되었다는 사실이 곧 모델 내부에서 실제로 사용되었음을 의미하지는 않는다**.  
  이에 본 논문은 뉴런의 실사용 여부를 반영하기 위해,  
  **causality를 고려한 weighting**이 중요도 추정에 효과적임을 논의한다.

- Transformer의 key–value memory 관점에서 MLP output에 대해 선형 prober를 학습하는 것은,  
  해당 뉴런들의 활성화를 통해 **residual representation에 기록되는 방향(direction)** 을 탐지하는 일반화된 과정으로 해석할 수 있다.  
  다시 말해, MLP output 공간에서의 linear probing은  
  MLP hidden 뉴런들의 활성화에 대한 probing으로 재구성될 수 있다.  
  더 나아가, MLP output에서 concept direction을 학습하는 과정은  
  hidden 뉴런들의 **positive/negative 기여도**와,  
  각 뉴런이 활성화될 때 output에 쓰이는 **writing vector의 방향성**을 동시에 고려하는 학습으로 볼 수 있다.  
  이 관찰을 바탕으로, 본 논문은 **causality-aware weighting을 통한 sparse prober 학습 방법**을 제안한다.


  ----

  ## Definition of Concept Direction 

  ## What We Expect From Probing
  - We construct positive and negative samples to train a prober, and expected to classify the test dataset correctly to detect the existence (or just check the separability as neural network may decode the direction if there is). 

  - Therefore, linear probing assumes that we can find a linear direction in transformer MLP output space to separate positive and negative samples. 

## Pruning 

  - If you adopt a frequentist approach, we may assume a ground truth concept direction $d^{\star}$ which separate positive and negative samples. However, given limited samples, the concept direction is unknown and we could not say, a neuron aligns properly to the concept direction $d^{\star}$. 
  - In more logical sense, $Cond (N) -> Concept Neuron (N)$ might hold. However, we could not define the proper definition of the concept direction as there are multiple reasons to conclude concept neuron. Instead, we may consider more weaker version, $Concept Neuron (N) \rightarrow Cond(N) or Cond(A) ... $ which states that if a neuron is a concept neuron, the following properties are required. Which opens many reasons. As such, we have \neg (Cond)(N) and \neg(Cond) then, \neg ConCept Neuron. Which opens more space for the possibility of concept neurons. 
  - Conditions -> Concept Neuron (Strict)
  - Concept Neuron -> Conditions (Weak)

  For example, if we define Activation is larger than 0.3 or Neuron direction is aligned more than 0.5 then, the neuron is a concept neuron. might not work. However, concept neuron-> activation is larger than 0.3 or Neuron direction is aligned more than 0.5. That is, we say if a concept neuron exists, then at least one of concept is satisfied. However, we do not want that at least one of conditions is satisfied, it is a concept neuron. 

  As such, we accept the weak definition of a concept neuron. and we use contraposition 
  At least of the conditions are not met, the neuron is not a concept neuron. 

  This type of filtering is known to increase precision by filtering the negative samples. See Appendix for more discussion and proof for the claim. Including experimental results. 

  Now, we turn our focus on 
  
  ## Causality


Theoretical Justifications

- Training MLP Output Prober is equivalent to find an average neuron directions weighted by activations. 

- Any MLP Output Prober can be represented by MLP hidden prober.

- Under the assumption that concept direction is true, the prober on concept neurons is better than the probers with noise neurons. 





  ## Causally Pruned Prober

  - Attribute neurons based on causality
  - Select only top-K neurons 
  - Train a linear prober with activations on the selected top-K neurons. 

  - We discuss more on the design choice: why don't we use initialization ... in Appendix 




  ## Experiments


  ## Results 

Empirical Observations based on CPP

## Why Do We Need Pruning? When L1 can be applied? 



