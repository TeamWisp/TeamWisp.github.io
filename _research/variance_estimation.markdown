---
layout: post
title:  "Variance Estimation"
category: research 
---

**Time estimation:** 1 hour

## What is it

With variance estimation you are taking a series of samples, and comparing them to each other or a specific to see how much of a difference there is between them.

## What can we use it for

Variance estimation is used temporarily to see how much noise there is at that specific pixel. This can be used to avoid filtering for noise in areas without any noise. It can also be used in other cases where you want to know the amount of difference between samples.

## How to use it

Every frame, for the current pixel, have a value that is the average of previous pixels for that location. By subtracting the current pixel from it, getting the difference, you can get a value indicating how much it differs from previous frames. After this, you add the value of the current pixel to the average (for quickly doing this, keep a count of the number of pixels used, multiply the previous value by this, add the current value, increment the count by one, and then divide by the count). When using this for denoising, it is possible sudden movements (camera or rigidbody) can cause pixels on the screen to not have any data. In this case, for the first four frames, nvidia’s algorithm takes the average of a square of 7x7 pixels instead. Storing the previous pixels should be done in a Gbuffer (the 4th value of the vector can be used to store the number of pixels the average is created from)

