---
layout: post
title:  "Deep Learning Super Sampling"
category: common
---

NVIDIA provides a technique which takes samples of your scene and sends algorithms to your TensorCores that know how a frame is upscaled. This means you have a way of obtaining a higher resolution version of the texture, without using linear filtering. This process is called image/texture magnification filtering and this is done using Deep Learning Super Sampling (DLSS). The AI takes various screenshots of your game (at target size) and uses a minification filter to get a mip. This mip is then scaled by the AI and compared to the actual result and tweaked until it matches.The AI requires thousands of anti-aliased frames during training. The final result is that edges get blurred and sharpening edges for important areas.
On their own end, they use 64x supersampling with 4k resolution, essentially performing half a billion lighting calculations per frame. This then trains their AI to generate algorithm weights for determining a high res image from a low res image. These weights are included in the NVIDIA drivers and so they have to include it in their drivers (however, they say it will be free to use). These weights are tens of MBs.

## How will it work with raytracing?

DLSS allows you to render a lower quality image, while still having the same quality as before. It does so by rendering a few thousand frames in high resolution and downsampling. It then tries to replicate the high resolution image from the low resolution image. This AI is trained after a couple thousand frames and can then be used to estimate what your low resolution scene view should look like.

## How is it implemented?

This is integrated with the card and no major changes have to be made to the game: “DLSS is a post-processing effect that can be integrated into any modern engine. It doesn’t require any art or content changes and should function well in titles that support Temporal Anti-Aliasing (TAA).” [0]. 

## Why don’t more games/engines use it?

This technique is very new and no games have been released with it yet, they are probably waiting for consumers to have RTX cards before implementing it. Some games are already working on implementing this and samples of NVIDIA show the difference between TAA and DLSS.

## Links

[What does DLSS mean for game developers?](https://news.developer.nvidia.com/dlss-what-does-it-mean-for-game-developers/) [0]

[DLSS FAQ](https://www.techarp.com/computer/nvidia-dlss-deep-learning-super-sampling/2/) [1]

[DLSS explained](https://wccftech.com/nvidia-dlss-explained-nvidia-ngx/) [2]

