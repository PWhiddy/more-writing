---
layout: post
mathjax: true
title: Hyperoperators
---

What's the biggest number you've ever tried to imagine? Ever wonder whether functions exist that grow much faster than the exponential or
factorial? Both these notations are capable of representing huge numbers that are large enough for any real application. For example there are roughly $$10^{86}$$ elementary particles in the visible universe and $$8\cdot2^{10^{12}}$$ possible configurations in a 1 TB solid state drive. However one can still imagine plenty of numbers far larger than this, but with seemingly no way to express them.  
Recently, I came across the [wikipedia page for hyperoperators](https://en.wikipedia.org/wiki/Hyperoperation). The basic idea is that the sequence of operations-- addition, multiplication, and exponentiation each seem to build on each other in a way that can naturally be extended to an infinite series of operations.  
Suppose we have the hyperoperator function that takes two arguments:  

$$h_{n}(a,b)\,|\, n, a, b \in \mathbb{Z}^{+}$$
  
Where $$n$$ specifies the particular operator from the series that will be applied to $$a$$ and $$b$$.  
For example, $$h_{1}(a,b)$$ simply represents the addition of $$a$$ and $$b$$. 
This is equivalent to repeated incrementing $$a$$, $$b$$ times.  
  
$$h_{1}(a,b) = a + b = a+\underbrace{1+1+\ldots+1}_\text{b}$$  
  
$$h_{2}(a,b)$$ multiplies $$a$$ and $$b$$. This is equivalent to repeated addition.  
  
$$h_{2}(a,b) = a \cdot b = \underbrace{a+a+\ldots+a}_\text{b}$$  
  
$$h_{3}(a,b)$$ is the exponentiation $$a$$ raised to $$b$$. This is equivalent to repeated multiplication.  
  
$$h_{3}(a,b) = a^b = \underbrace{a\cdot a \cdot \ldots \cdot a}_\text{b}$$  
  
$$h_{4}(a,b)$$ is called tetration, and is equivalent to a stack of exponents with a height of $$b$$.  
  
$$h_{4}(a,b) = b\begin{cases}a^{\,a^{\,\ldots^{\,a}}}\end{cases}$$
  
See the pattern? Continuing, $$h_{n}(a,b)$$ applies the $$nth$$ hyperoperator to $$a$$ and $$b$$ by repeatedly using $$h_{n-1}$$.  
We now have a function that grows fast. In fact if one wanted to write down just $$h_{10}(10,10)$$ in stacked exponential notation, you could convert all of the universe's mass into sheets of paper, fill every page with writing and still make *effectively no progress* towards writing that number down. It's rather surprising that the first few operations in this little-known series just happen to be essential building blocks of mathematics, but when $$n>3$$ they become virtually useless. Why is this? It turns out those assumptions of equivalence we made above were not entirely correct. Researching applications of hyperoperators like tetration ($$n=4$$) you will likely come across [answers on Quora from Berkeley Math PhD Qiaochu Yuan](https://www.quora.com/Why-is-exponentiation-so-much-more-applicable-to-the-real-world-than-tetration/answer/Qiaochu-Yuan-1). He suggests to stop thinking about exponentiation as repeated multiplication, and multiplication as repeated addition. He points out that using this definition it is unclear how to extend the hyperoperator functions to Real and Complex numbers. After all, how do you repeat an operation a fractional or imaginary number of times? Yuan explains that "unlike multiplication and exponentiation it's extremely unclear what tetration of real values ought to mean. As far as I can tell, it's a mathematical dead end." Though it may be tempting to define addition, multiplication, and exponentiation as stacked versions of the same basic process, each is its own separate concern. Yet the opposite is commonly taught in early math education. Mathematicians have been discouraging this for quite some time. Stanford math professor Keith Devlin rants on his website that multiplication ["Aint no repeated addition"](https://www.maa.org/external_archive/devlin/devlin_06_08.html). This sentiment has existed for some time, as [Wikipedia traces the debate](https://en.wikipedia.org/wiki/Multiplication_and_repeated_addition) back to the early 90s. But despite all this, the idea of describing addition, multiplication, and exponentiation as just a few instances of an infinite series of operators still seems interesting. For just a moment let's indulge ourselves and see where it takes us. Perhaps we can write a definition in a language computers can execute.  
  
```  
   hyper 0 a b = succ a  
   hyper 1 a b = a + b  
   hyper 2 a b = a * b  
   hyper 3 a b = a ^ b  
   hyper n a b = ???  
```  
  
This obvious approach doesn't really help us extend to $$n$$. Accomplishing this might seem like a complex task, but with a functional approach it is actually quite simple.
Using the pattern laid out in the beginning, we can recursively define $$h_{n}$$ with repeated applications of $$h_{n-1}$$. It turns the final result can be expressed succinctly with a fold.  
  
```   
   hyper 0 a b = succ a
   hyper 1 a b = a + b
   hyper n a b = foldr1 (hyper (n-1)) replicate b a
```   
   
That's it! What happened? ```(hyper (n-1))``` is the previous operator, which fold applies between each element of a list consisting of $$b$$ $$a$$s. Sadly this implementation is totally impractical. Calculation is performed by repeated addition, so $$2^6$$ is $$2+2+2+2+2+2$$, and hyperoperators create numbers so large that even $$h_{5}$$ almost immediately goes beyond the limits of computation.  
Is it possible to extend the hyperoperators to real and imaginary numbers? This would be interesting to see.

It now becomes obvious the differences between these operators. For example, addition and multiplication are both associative and commutative, while exponentiation and higher are neither. 
