---
layout: post
mathjax: false
title: Local Coding Models are becoming useful
---

Local coding models seem to be finally crossing a threshold of usability.
Most recently I've tested qwen3.6-27b (q3_k_m).  
  
[https://github.com/PWhiddy/local-coding-model-experiments](https://github.com/PWhiddy/local-coding-model-experiments)  
  
This task is a little tricker than it may seem because almost all code on the internet with these libraries uses older api versions, so pretty much any model will write the wrong version on the first try, will need to inspect the errors, search the docs for api changes, and then correct it. Final message asks it to add animation.

  All user messages (mainly just coaxing it to continue):
  ```
- create a rust project which draws 10k random triangles in a window using wgpu and winit. make sure you used the latest versions of these libraries - wgpu 29.0.1 and winit 0.30.13  
- focus  
- /context  
- hows it going  
- done?  
- hows it going  
- continue  
- try running it  
- triangles are rendering  
- can you make all the triangles animate in circles?  
- done?
```

![10k triangles](https://i.imgur.com/JaLWh9A.gif)

They certainly can't solve problems as hard as the best models, but are capable of simple and useful tasks, which is crazy considering they are like <1% the size. On my 4090 i get ~50 tok/sec. When I bought it a few years ago I wondered if it would be someday capaple of this. I probably would have estimated this with 30% chance at the time, moderately surprising!
