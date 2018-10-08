---
layout: post
title:  "Architecture"
date:   2018-09-27 11:43:16 +0200
category: common
---

## High Level

We choose to go for a very "simple stupid" approach to the architectural design. We desided to do this to reduce scope, allow for high performance code which allows for rapid itteration.

![High Level UML Diagram](../images/high_uml.png)

Let's start at the top. We have a engine project which handles stuff like the scene and handles the game logic.

Than we have a set of "systems" which are the building blocks for the engine. For example the engine is in charge of building a scene graph and a frame graph which get passed to the render system using the high level interface of the render system.

The render system than does all the rendering work using a low level graphics API and the Utility library.

Those systems are constructed upon a utility library which contains stuff like thread management.

## Low Level

### Dependencies

* Assimp
* BGFX
* FG
* Bullet Physics 3
* FMOD
* Entt
* Game Code 4 Resource Manager
* Gainput

![Low Level UML Diagram](../images/low_uml.jpg)
