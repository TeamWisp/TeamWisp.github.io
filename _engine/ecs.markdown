---
layout: post
title:  "Entity Component System"
category: common
---

## [EnTT](https://github.com/skypjack/entt)

`EnTT` is a header-only, tiny and easy to use entity-component system (and much
more) written in modern C++ and even
[used by Mojang in Minecraft](https://minecraft.net/en-us/attribution/).<br/>
The entity-component-system (also known as _ECS_) is an architectural pattern
used mostly in game development. For further details:

* [Entity Systems Wiki](http://entity-systems.wikidot.com/)
* [Evolve Your Hierarchy](http://cowboyprogramming.com/2007/01/05/evolve-your-heirachy/)
* [ECS on Wikipedia](https://en.wikipedia.org/wiki/Entity%E2%80%93component%E2%80%93system)

A long time ago, the sole entity-component system was part of the project. After
a while the codebase has grown and more and more classes have become part of the
repository.<br/>
Here is a brief, yet incomplete list of what it offers today:

* Statically generated integer identifiers for types (assigned either at
  compile-time or at runtime).
* A constexpr utility for human readable resource identifiers.
* A minimal configuration system built on top of the monostate pattern.
* An incredibly fast entity-component system based on sparse sets, with its own
  views and a _pay for what you use_ policy to adjust performance and memory
  usage according to users' requirements.
* A lot of facilities built on top of the entity-component system to help
  developers and avoid reinventing the wheel (ie dependencies, snapshot, actor
  class for those who aren't confident with the architecture and so on).
* The smallest and most basic implementation of a service locator ever seen.
* A cooperative scheduler for processes of any type.
* All what is needed for resource management (cache, loaders, handles).
* Delegates, signal handlers (with built-in support for collectors) and a tiny
  event dispatcher.
* A general purpose event emitter, that is a CRTP idiom based class template.
* An event dispatcher for immediate and delayed events to integrate in loops.

Consider it a work in progress. The whole API is also fully documented in-code
for those who are brave enough to read it.

Currently, `EnTT` is tested on Linux, Microsoft Windows and OS X. It has proven
to work also on both Android and iOS.<br/>
Most likely it will not be problematic on other systems as well, but has not
been sufficiently tested so far.

## Integration With the Engine

***coming soon***
