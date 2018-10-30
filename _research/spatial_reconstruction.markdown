---
layout: post
title:  "Spatial Reconstruction"
category: research 
---

## Definition
Spatial reconstruction doesn’t have a specific definition/implementation, since it can be implemented in different ways. Spatial Reconstruction is a way to reconstruct parts of an image. In this case, to reduce noise in the buffer output created by reflection rays.

![](../../images/sr/image_0.png)

The information that was given about this topic has been provided by Tomasz Stachowiak at DD2018:
For every full resolution pixel, use sixteen half-resolution (the resolution that the raw ray reflection output is rendered on) ray hits. These ray hits are selected by a poisson-disk distribution method. The local pixel its BRDF is then used to scale the intensity. The rays in PDF are used to reduce it. Practically it’s just a weighted sum.

```cpp
result = 0.0;
weightSum = 0.0;
for pixel in neighborhood {
   	weight = localBRDF(pixel.hit) / pixel.Pdf;
   	result += color(pixel.hit) * weight;
   	weightSum += weight;
}
result /= weightSum;
```

The set of rays that each pixel uses is quite interesting. Every two by two quad of pixels is rendered by one ray, since the frame buffer is rendered on half resolution. The half resolution result can be easily fixed by disjoint ray sets between those four pixels. The ideal result is to disjoint the pixels (color) as much as possible. Using blue noise gives you the ability to split the noise into four classes based on the gray value of the noise (see image below).

![](../../images/sr/image_1.png)

Each of these classes are assigned to one of the four pixels. This determines which ray hit information each pixel uses in the weight sum. Then a center is picked from each set and collect 16 samples by using Poisson Disk from this center point.

The samples can be sorted by distance to the center and use those in adaptive kernels to vary the number of rays used to get more filtering, but (potentially more bleeding).

## Use-case

Spatial reconstruction is used to up-sample the framebuffer. It might also be used for removing some noise, but it’s not very effective, since there will remain a lot of noise in the image.

![](../../images/sr/image_2.png)

Usually when you ray-trace a scene at a smaller resolution than your final front-buffer must be, you spatially reconstruct the image to the desired resolution. In this case, SEED from EA, used it as one of their methods to reduce noise in the generated frame buffer from reflection rays.

## Estimation

Because I’m not very experienced with graphics programming and ray-tracing I don’t feel like I can make an accurate estimation.
However, I think it is rather simple to apply this technique to remove some noise and up-sample the reflection ray-traced frame buffer. For myself, excluding time to research ray-trace in general, the estimation would be around 2 weeks, because you need to figure out how you implement the different algorithms to get the desired result and implement it in the architecture. It might be 3 weeks, because Tomasz Stachowiak probably didn’t explain all details and there is not a lot of specific information about it online.

## References

DD2018: Tomasz Stachowiak - Stochastic all the things: raytracing in hybrid real-time rendering

[https://www.youtube.com/watch?v=MyTOGHqyquU](https://www.youtube.com/watch?v=MyTOGHqyquU) 

DD18 - SEED - Raytracing in Hybrid Real-Time Rendering [https://www.slideshare.net/DICEStudio/dd18-seed-raytracing-in-hybrid-realtime-rendering](https://www.slideshare.net/DICEStudio/dd18-seed-raytracing-in-hybrid-realtime-rendering) 

Bidirectional Reflectance Distribution Function *Wikipedia.org*

[https://en.wikipedia.org/wiki/Bidirectional_reflectance_distribution_function](https://en.wikipedia.org/wiki/Bidirectional_reflectance_distribution_function) 

Phong BRDF (Bidirectional Reflectance Distribution Function)

[http://www.cs.uu.nl/docs/vakken/magr/portfolio/INFOMAGR/lecture13.pdf](http://www.cs.uu.nl/docs/vakken/magr/portfolio/INFOMAGR/lecture13.pdf) 

PDF (Probability Density Function) *Wikipedia.org*

[https://en.wikipedia.org/wiki/Probability_density_function](https://en.wikipedia.org/wiki/Probability_density_function) 

Mitchell’s Best-Candidate by Mike Bostock

[https://bl.ocks.org/mbostock/1893974](https://bl.ocks.org/mbostock/1893974) 

Poisson Disc Sampling by Jason Davies

[https://www.jasondavies.com/poisson-disc/](https://www.jasondavies.com/poisson-disc/)

SIGGRAPH 2015 Advances in Real-Time Rendering in Games course [http://advances.realtimerendering.com/s2015/](http://advances.realtimerendering.com/s2015/) 
