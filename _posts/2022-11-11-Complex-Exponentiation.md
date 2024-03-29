---
layout: post
mathjax: true
title: Complex Exponentiation
---

Complex or "imaginary" numbers are a useful tool for understanding a wide range of topics in geometry, signal processing, electrical engineering, quantum mechanics and more. The fourier transform is concisely defined<sup>1</sup> as an integral of a complex function, is ubiquitous across math and science, and is an essential part of any audio or image processing system.

Ultimately a complex number is just a pair of numbers (a 2D vector, denoted $$a+bi$$) and special rules for how to perform calculations with them. These rules map operations on complex numbers to operations on pairs of regular (real) numbers. 
  
To add two complex numbers, the components are added together separately:

$$\\~\\\large(a+bi) + (c+di) = (a+c)+(b+d)i\\$$

To multiply two complex numbers, the rule is:

$$\\~\\\large(a+bi)(c+di) = (ac - bd) + (ad + bc)i\\ $$

Using just $$i$$ in this formula gives
  
$$\\~\\\large(0+i)(0+i) = (0\cdot 0 - 1\cdot1) + (0\cdot1 + 1\cdot 0) \\~\\\large = (-1 + 0i) \\~\\\large = -1\\$$

  
This is the famous property $$i^2 = -1$$, neat!

There are rules for other kinds of operations on complex numbers, such as Eulers formula:

$$\large e^{ix}=\cos(x)+i\sin(x) $$

So exponents can be complex too. But what about the general case, raising any complex number to any complex power? For example what if we wanted to know $$\small 2^i$$, or $$\normalsize i^i$$? There are many tricks for special cases, but in general can we compute $$\normalsize (a+bi)^{(c+di)} $$, ideally without any knowledge of complex numbers whatsoever? Yes!
  
The general formula is:

$$
\large (a+bi)^{(c+di)} = m[\cos(s) + i\sin(s)]
\\  
$$
  
#### where

$$
\\  
\large m = e^{rc - \theta d}
\\
\large s = rd + \theta c
\\
\large r = \log{\sqrt{a^2 + b^2}}
\\
\large \theta = atan2(a, b)
\\
$$

This allows us to do a huge number of different calculations using just one equation. However it's a bit cumbersome to compute by hand, so let's implement this function in javascript:
    
```javascript
function complexExponent(a, b, c, d) {
   const r = Math.log( Math.sqrt( a * a + b * b ) );
   const ang = atan2(a, b);
   const real = Math.exp( r * c - ang * d);
   const imag = r * d + ang * c;
   return [ real*Math.cos(imag), real*Math.sin(imag) ];
}

// computes the angle of a vector
function atan2(x, y) {
  if (x == 0.0) {
    return y > 0.0 ? Math.PI/2 : -Math.PI/2;
  } else {
    return Math.atan( y / x );
  }
}

// round away error from floating point imprecision
function prettyPrint(c) {
   return [ c[0].toFixed(5), c[1].toFixed(5) ];
}

```
  
Complex numbers are a superset of the real numbers, so as a sanity check, we can test regular exponentiation:
  
  
$$3^2 = 9$$
```javascript
prettyPrint(
   complexExponent(3, 0, 2, 0)
)
// [ '9.00000', '0.00000' ]
```

Great. Naturally this function can also compute roots:  
 
$$\sqrt{25} = 25^{1/2} = 5$$
```javascript
complexExponent(25, 0, 1/2, 0)
// [ '5.00000', '0.00000' ]
```

Let's test the identity from earlier:
  
$$i^2 = -1$$
```javascript
complexExponent(0, 1, 2, 0)
// [ '-1.00000', '0.00000' ]
```

Great. How about Euler's formula?
  
$$e^{i\pi} = -1$$
```javascript
complexExponent(Math.E, 0, 0, Math.PI)
// [ '-1.00000', '0.00000' ]
```

No problem. What about $$i^i$$?
  
$$i^{i} = e^{-\pi/2}$$
```javascript
complexExponent(0, 1, 0, 1)
// [ '0.20788', '0.00000' ]
```

This checks out! But as you can see, using this code implementation you'll get just a numerical value and can miss the exact answers. However you can still use the original equation by hand to get exact values, or better yet implement the code using a symbolic algebra system!


<html>
    <head>
      <script>
         function complexExponent(a, b, c, d) {
            const r = Math.log( Math.sqrt( a * a + b * b ) );
            const ang = atan2(a, b);
            const real = Math.exp( r * c - ang * d);
            const imag = r * d + ang * c;
            return [ real*Math.cos(imag), real*Math.sin(imag) ];
         }

         // computes the angle of a vector
         function atan2(x, y) {
         if (x == 0.0) {
            return y > 0.0 ? Math.PI/2 : -Math.PI/2;
         } else {
            return Math.atan( y / x );
         }
         }

         // round away error from floating point imprecision
         function prettyPrint(c) {
            return [ c[0].toFixed(5), c[1].toFixed(5) ];
         }
         const calc = () => alert("eyyy");
      </script>
   </head>
   <!--
   <input/>
   <button id="testb" onclick="calc()" style="height: 3em;width: 8em;">
      Calculate
   </button>
   -->
</html>

Can we have some fun with this function? Here's a demo using complex exponentiation to drive a particle system:
  
[![NOVA](https://i.imgur.com/XjYNMAz.gif)](https://openprocessing.org/sketch/465937)

And why does this equation contain trigonometry, exponential and log functions? It's much easier to understand complex numbers as geomtric objects, rather than as "numbers". Group Theory taxonomizes and collects all these number-like objects, and Geometric Algebra attempts to reframe complex numbers and more as general set of underlying geometric structures.

  
    
### Footnotes    
1. $${\hat {f}}(\omega )=\int_{-\infty }^{\infty }f(x)\ e^{-i\omega x}\,dx, $$