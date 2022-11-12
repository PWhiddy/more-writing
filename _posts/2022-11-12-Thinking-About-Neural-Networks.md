---
layout: post
mathjax: true
title: How I think about neural networks (outline)
---

Reference 3b1b linear algebra series?

One - the critical linear algebra
1. Vectors - geometric (arrow) and abstract (object) interpretations
2. Dot product - geometric (angle) and abstract (similarity) interpretations
3. Matrix multiplication - geometric (transformations of various types) and abstract (a collection of comparisons)

Two - basic neural network
1. Network parameters store transformations, reference objects, randomly initialized
2. Derivative of loss, "what if" for every parameter in the network (backprop is just efficient way to compute, not necessary to understand)
3. Optimization
4. Layers, Hierarchy, Levels of abstraction
5. Activations

Three - real neural networks
1. Tricks to make optimization work better - minibatch, momentum, batch norm, residuals
2. Architectures - CNN, transformer

Limitations - Curse of dimensionality