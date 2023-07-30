---
layout: post
mathjax: true
title: Paper Notes (Ongoing)
---

Notes and thoughts on various papers. 

#### Reasoning or Reciting? Exploring the Capabilities and Limitations of Language Models Through Counterfactual Tasks  

[https://duckduckgo.com](https://arxiv.org/abs/2307.02477)](https://arxiv.org/abs/2307.02477)  

Evaluating models in adversarial, out of distribution, and real-world scenarios is key for understanding their capabilities and limitations. However, this type of "counterfactual evaluation" absolutely begs for a human comparison. Their first example is "Arithmetic using base-9". Sure, if you understand numbers well you should be able to solve problems in base-9 just fine. But if you're given some arithmetic tasks to solve and must use base-9, how much slower or likely to make mistakes will you be? Imagine giving this task to grade school students. How much would their performance on arithmetic tasks decrease? I think the only conclusion that can be directly drawn from this paper is that LLMs obviously do generalize and reason, because these evaluations show that performance only decreases somewhat rather than dropping to random chance when tasks are modified to be adversarial. It would be interesting to see in future work humans completing both versions of the evaluations so we can learn something about how well these models reason and generalize compared to humans.  
