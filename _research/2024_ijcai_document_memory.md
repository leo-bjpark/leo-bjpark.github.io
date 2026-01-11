---
layout: default
authors: 
    - name: <strong> Bumjin Park </strong>
      affiliations:
        name: KAIST
    - name: Jaesik Choi
      affiliations:
        name: KAIST, INEEJI
bibliography: all.bib
giscus_comments: false
disqus_comments: false
date: 2024-02-01
featured: true
img: assets/img/logos/ijcai2024.png
title: 'Document Memory for LLMs'
category: 'Research Highlights'
tags: ['IJCAI2024', Memory, LLM, Interpretability]
description: 'A novel document-wise memory architecture and guidance loss for tracking and manipulating document memories in large language models.'
---

<header>
  <h1>Memorizing Documents with Guidance</h1>
  <h2>Memorizing Documents with Guidance in Large Language Models </h2>
  <p><strong>IJCAI 2024 Main Conference</strong></p>
  <p>
    <a href="https://leo-bjpark.github.io">Bumjin Park</a>, 
    Jaesik Choi<br/>
    KAIST AI, INEEJI<br/>
    <a href="mailto:bumjin@kaist.ac.kr">bumjin@kaist.ac.kr</a>
  </p>
</header>

<div class="styled-logo-container">
  <img src="/assets/img/logos/ijcai2024.png" class="styled-logo">
</div>


<section id="resources">
  <h3>Resources</h3>
  <ul>
    <li>
    <a href="https://www.ijcai.org/proceedings/2024/0714.pdf" target="_blank">  📄 Paper (IJCAI) </a> / 
    <a href="https://arxiv.org/abs/2406.15996" target="_blank">📄 Arxiv</a></li>
    <li><a href="https://github.com/fxnnxc/DocGuidanceLLM" target="_blank">💻 Code & Datasets</a></li>
  </ul>
</section>

<section id="tldr">
  <h3>TL;DR</h3>
  <p>
    We propose a <strong><u>document-wise memory architecture</u></strong> and <strong><u>document guidance loss</u></strong> to track memory entries per document. This enables <strong>controlled generation</strong> and <strong>traceable knowledge location</strong> in LLMs, promoting safer and more explainable AI.
  </p>
</section>

<section id="overview">
  <h3>Key Concepts</h3>
  <ul>
    <li><strong>Document-wise Memory</strong>: Memory structure aligned with document representations</li>
    <li><strong>Document Guidance Loss</strong>: Encourages disentangled memory entries across documents</li>
    <li><strong>Continuity Assumption</strong>: Lipschitz-based theoretical guarantee for memory space geometry</li>
  </ul>
</section>

<section id="task">
  <h3>Research Task</h3>

  <h4> Document-specific Memory Selection</h4>
  <p>
    This section defines the core problem: how can large language models (LLMs) select different memory entries depending on the document? 
    The idea is to associate each document with a unique memory footprint to trace and control document-specific content during generation.
  </p>
  <img src="https://d2acbkrrljl37x.cloudfront.net/research/publication/ijcai2024_1.png" width="90%" height="auto" class="styled-image"/>
  <p class="caption">
    <strong>Figure:</strong> The model learns to select different memory entries per document, laying the foundation for document-specific memory design.
  </p>

  <br>
  <h4> Result Highlight: Learning Dynamics of Memory Competition</h4>
  <p>
    We visualize how document memories evolve during training. 
    As training progresses, documents increasingly specialize their memory entries, competing for distinct slots—leading to better separation and less contamination.
  </p>
  <img src="https://d2acbkrrljl37x.cloudfront.net/research/publication/ijcai2024_dynamics.gif" width="90%" height="auto" class="styled-image"/>
  <p class="caption">
    <strong>Figure:</strong> Competitive learning dynamics promote distinct memory entries across documents over training steps.
  </p>
</section>

<section id="analysis">
  <h3>Analysis Highlights</h3>


  <h4> Key-Value Memory Selection Architecture</h4>
  <p>
    This figure illustrates how document representations modulate memory selection. 
    A key vector derived from a document representation is used to mask or activate specific memory entries in the MLP layers of a transformer, forming a soft selection mechanism.
  </p>
  <img src="https://d2acbkrrljl37x.cloudfront.net/research/publication/ijcai2024_2.png" width="90%" height="auto" class="styled-image"/>
  <p class="caption">
    <strong>Figure:</strong> Document-based key vectors enable selective activation in the key-value memory structure of LLMs.
  </p>

<br>
  <h4> Document Guidance Loss</h4>
  <p>
    We introduce a training method based on guidance loss to align memory entries with document semantics. 
    This loss increases the likelihood of the document text when using the correct document representation, while reducing it when using negative document representations.
  </p>
  <img src="https://d2acbkrrljl37x.cloudfront.net/research/publication/ijcai2024_3.png" width="90%" height="auto" class="styled-image"/>
  <p class="caption">
    <strong>Figure:</strong> Document guidance loss entangles memory selection with the intended document while forgetting others.
  </p>

<br>
  <h4> Continuity in Document-to-Memory Mapping</h4>
  <p>
    We theorize and visualize how small changes in document representations should lead to smooth changes in memory selection. 
    This Lipschitz continuity ensures stability and consistency across the memory selection manifold.
  </p>
  <img src="https://d2acbkrrljl37x.cloudfront.net/research/publication/ijcai2024_4.png" width="90%" height="auto" class="styled-image"/>
  <p class="caption">
    <strong>Figure:</strong> The continuity assumption preserves similarity between documents and their corresponding memory entries.
  </p>

  <br>
  <h4> Improved Accuracy with Document-wise Memory</h4>
  <p>
    Experiments show that our method significantly improves the accuracy of content recall for the target document, compared to shared memory baselines.
    We evaluate this using ROUGE and IV-ROUGE scores on Wikitext-103.
  </p>
  <img src="https://d2acbkrrljl37x.cloudfront.net/research/publication/ijcai2024_exp1.png" width="90%" height="auto" class="styled-image"/>
  <p class="caption">
    <strong>Figure:</strong> Document-wise memories improve generation quality by focusing on document-specific information.
  </p>
</section>


<section id="citation">
  <h3>Citation</h3>
  <pre>
@inproceedings{park2024guidance,
  title     = {Memorizing Documents with Guidance in Large Language Models},
  author    = {Park, Bumjin and Choi, Jaesik},
  booktitle = {Proceedings of the 33rd International Joint Conference on Artificial Intelligence (IJCAI)},
  year      = {2024},
}
  </pre>
</section>


<section id="contact">
  <h3>Contact</h3>
  <p>If you have questions or collaboration ideas, feel free to reach out:</p>
  <p><strong>Email:</strong> <a href="mailto:bumjin@kaist.ac.kr">bumjin@kaist.ac.kr</a></p>
</section>

<footer>
  <p>© 2025 Bumjin Park · KAIST AI · IJCAI 2025</p>
</footer>
