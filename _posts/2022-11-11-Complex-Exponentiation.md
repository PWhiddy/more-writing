---
layout: post
mathjax: true
title: Complex Exponentiation
---

What does it mean to raise a complex number to a complex power? For example what is $$\small 2^i$$, or $$\normalsize i^i$$? In general, can we compute $$\normalsize (a+bi)^{(c+di)} $$ without needing any knowledge of complex numbers whatsoever? The magical formula is:

$$\large (a+bi)^{(c+di)} = m[\cos(s) + i\sin(s)]
\\  
~ 
\\
\large \text{where}
\\  
~
\\  
\large m = e^{rc - \theta d}
\\
\large s = rd + \theta c
\\
\large r = \log{\sqrt{a^2 + b^2}}
\\
\large \theta = atan2(a, b)$$

This allows us to do many different calculations using just one equation. However it's a bit cumbersome, 

```javascript
function complexExponent(a, b, c, d) {
   const r = Math.log( Math.sqrt( a * a + b * b ) );
   const ang = atan2(a, b);
   const real = Math.exp( r * c - ang * d);
   const imag = r * d + ang * c;
   return [ real*Math.cos(imag), real*Math.sin(imag) ];
}

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


As a sanity check for our function, we can test some basic cases:
  
$$3^2 = 9$$
```javascript
prettyPrint(
   complexExponent(3, 0, 2, 0)
)
// [ '9.00000', '0.00000' ]
```

$$25^0.5 = 5$$
```javascript
complexExponent(25, 0, 0.5, 0)
// [ '5.00000', '0.00000' ]
```

$$i^2 = -1$$
```javascript
complexExponent(0, 1, 2, 0)
// [ 8.999999999999998, 0 ]
```

As you can see, using this code implementation you'll miss getting exact answers. However you can still use the original equation by hand to get exact values, or better yet implement the code using a symbolic algebra system!
  

<html>
    <head>
      <script>
         const calc = () => alert("eyyy");
      </script>
   </head>
   This is a test
   <button id="testb" onclick="calc()" style="height: 3em;width: 8em;">
      Calculate
   </button>
</html>


And why does this equation contain trigonometry, exponential and log functions? It's much easier to understand complex numbers as geomtric objects, rather than as "numbers". Group theory taxonomizes and collects all these number-like objects, and geometric algebra attempts to reframe complex numbers and more as general set of underlying geometric structures.