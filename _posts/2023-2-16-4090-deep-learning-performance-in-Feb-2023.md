---
layout: post
mathjax: true
title: Deep learning on an RTX 4090 in February 2023 (ongoing log)
---

2/16/22
My 4090 Arrived!
Set up WSL, everything works very slick, integrations are far improved over the WSL experience from a couple years ago. Setup is straightforward.
Tested out SD performance using HF diffusers and xformers, upgraded build from conda to micromamba. Performance is only 50% (12 it/s) of a 3090?? That can’t be right, WSL must be adding overhead.

2/17/22

Let’s do it the old way, dual boot some ubuntu.
In theory one could add a bootable linux to an existing drive and boot without external usb using unetbootin, but it failed when I tried it. Doing some research, apparently this is method is risky on windows 11 so I’m just going to use a flash drive.
Windows didn’t want to shrink the windows partition beyond 50%, so I used another disk tool.
Ubuntu installed fine, now to install the nvidia driver. The latest runfile from nvidia failed. The kernel failed to load because secure boot didn’t like the signature. Using the driver install via ubuntu software update also failed. After reading online, apparently you should not use the “open, tested” driver, but the proprietary one. Tried the proprietary one, but it also didn’t work. Tried manually signing the driver, that didn’t work, but after registering the signature in the UEFI (you only have a couple seconds to catch this when booting) it finally worked! Installed the same SD image as WSL and performance was unchanged! Running nanoGPT, initially got model flops utilization (MFU) 5% of A100 compute max of 312 tflops. 4090 should be either 165 or 330 Tflops for fp16, so that also suggests that it could be much better. Okay, so the issue wasn’t WSL. Why is it slow then? There’s a few threads between github, reddit, nvidia & pytorch forums on 4090 performance, and there’s a lot of claims without specific details (this one is the most chaotic https://github.com/huggingface/diffusers/issues/952). In two places people said that cudnn needed to be updated to get good performance. Pytorch does not yet officially support or offer builds for cuda 12 or cudnn 8.7. Tried manually replacing cudnn binaries in pytorch install, but this didn’t seem to take/have any effect. Nvidia does offer pytorch containers built with cuda 12, but they are huge (10gb). Loaded those containers, and tried running base SD model and got 20 it/s. That’s an improvement! With xformers it should be a lot faster. Using a container wouldn’t be ideal for development, so let’s try building pytorch from source. People online said using pytorch 2 didn’t help, but I’m going to build that just to see. First build was python3.11 and it said that torch.compile wasn’t supported yet, but nanoGPT now ran with 15% A100 MFU! Let’s try again using python3.10 to use torch.compile. I messed up my path a bit this time, which caused an error linking lcuda. Also good to use MAX_JOBS=n to reduce memory usage during pytorch build. Okay now SD runs at 40 it/s and with torch.compile nanoGPT is up to 23% A100 MFU! Now that’s decent!

2/18/22
Longer runs steadily grows memory, does nanoGPT have a memory leak?
Scaling up to gpt2 124M size, and easily hits 34% mfu or 107 tflops while pulling about 420 watts.
