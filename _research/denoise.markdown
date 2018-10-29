---
layout: post
title:  "AI-Accelerated Denoising"
category: common
---

Currently, Nvidia is working on adding a denoiser module to GameWorks. Where OptiX is made to denoise a full scene with hundreds or even thousands of iterations to provide a clear image, GameWorks is made for a hybrid renderer using one or two samples per pixel. The denoisers used in GameWorks work for raytraced shadows, ambient occlusion and reflections, exactly as we need. It is important to note that there is no single filter like used in OptiX but rather different filters are used for each of the mentioned effects. Even different filters are used for different types of light sources, this is due to the changing complexity of soft shadows depending on the size of a given light source.


[https://devblogs.nvidia.com/ray-tracing-games-nvidia-rtx/](https://devblogs.nvidia.com/ray-tracing-games-nvidia-rtx/)

