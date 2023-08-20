---
layout: post
mathjax: false
title: Finetuning GPT-2 for Shadertoy GLSL Code Completion
---

This page is mirrored from the original readme of the project located here:   
[https://github.com/PWhiddy/shader-gen-serve/](https://github.com/PWhiddy/shader-gen-serve/)  
  
---

# Shadertoy Live GLSL Autocomplete

An experiment fine-tuning gpt-2 (small 100M) on a dataset of public shadertoy shaders. The auto generated comments are perhaps more entertaining than the glsl.
Scaling up the model would certainly give much better results.

Training/model code were slight modifications to huggingface's example   
[https://github.com/huggingface/transformers/tree/master/examples/language-modeling](https://github.com/huggingface/transformers/tree/master/examples/language-modeling)

Shadertoy dataset was generated using this repository  
[https://github.com/PWhiddy/Shadertoy-Generator](https://github.com/PWhiddy/Shadertoy-Generator)

![img](https://i.imgur.com/LQq6IsO.gif)

![img](https://i.imgur.com/sJZkaTT.gif)

![img](https://i.imgur.com/465Yozj.png)
