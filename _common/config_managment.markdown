---
layout: post
title:  "Configuration Management"
date:   2018-09-27 11:43:16 +0200
permalink: /config_management/
weight: 100
---

## What is Configuration Management
During development, new ideas will constantly come up to improve the system(s). 
To make sure the project as a whole moves toward completion, changes must be officially submitted to the change-control board.

The change-control board is comprised of Emilio (Producer) and Viktor (Tech Lead). The board meets periodically to review proposed changes. It approves, disapproves, or defers each change. Changes will be considered in the context of what’s best for the project overall.

## Types of Changes

* **Design Change**: The developer requests to re-design an entire system/class/etc.
* **Code Change**: The developer requests to re-implement a working system/class/etc. 

## How to Submit a Change

Changes must be submitted through the issue tracking functionality on our git page. When submitting a change, estimate the time it would take to make the change, including review of the code for the change and retesting the whole system. In your estimate, include time for dealing with the change’s ripple effect through requirements to design, to code, to test, to changes in the user documentation, etc. Regardless of how optimistic you feel when the change is first suggested, refrain from giving an off-the-cuff estimate. Such estimates are often mistaken by a factor of 2 or more. _(Quote: Code Complete, A Practical Handbook of Software Construction, 2nd Edition by Steve McConnell, Chapter 28.2)_

When submitting a change, write a concise explanation of the change in the title, followed by a time estimation wrapped in square brackets. See the following example:

```
Re-designing Memory Manager [8H 30M]
```

Go into more details on what parts you want to change and why inside the comment itself.  

Finally, use the appropriate label to mark the issue from the following ones

![](https://i.imgur.com/IvUpJ82.png)
