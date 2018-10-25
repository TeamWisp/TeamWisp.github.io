---
layout: post
title:  "Fibers"
category: renderer
---

## Why Fibers

A processor can have multiple threads all running at the same time (simultaneous multithreading). As a result, you have to be careful about concurrent data access, and protect your data with system synchronization primitives which is often tricky to get right.

With fibers, control only switches when you tell it to, typically with a function call named something like yield(). This makes concurrent data access easier, since you don't have to worry about atomicity of data structures or mutexes. As long as you don't yield, there's no danger of being preempted and having another fiber trying to read or modify the data you're working with.[2]

Context switching is another strong reason for using Fibers. Context switching can be expensive. You want to reduce lock contention to a minimum, and minimize sharing across operating system threads as much as you can. Fibers make optimal use of your threads, keep them busy, and reduce moving data across them. 

## What is a Fiber
Fibers is a particularly lightweight thread of execution.
Like threads, fibers share address space. However, fibers use cooperative multitasking while threads use preemptive multitasking. Threads often depend on the kernel's thread scheduler to preempt a busy thread and resume another thread; fibers yield themselves to run another fiber while executing. [1]

Fibers describe essentially the same concept as coroutines. The distinction, if there is any, is that coroutines are a language-level construct, a form of control flow, while fibers are a systems-level construct, viewed as threads that happen to not run in parallel. Priority is contentious; fibers may be viewed as an implementation of coroutines, or as a substrate on which to implement coroutines.[1]

## Task scheduler

Task schedulers use atomic counters to handle synchronization. In a fiber context the task scheduler manages allocations, frees resources and maintains the fiber pool. The fibers provide stack space but the task scheduler manages the stack & registers. 
A thread is created for each cpu core and acts as the execution unit, Fibers are the context. 

When a user's yields the task scheduler essentially makes a snapshot of the task and puts it in a wait list. The atomic counter assigned to each fiber can be decremented, allowing you to yield fibers until your conditions/dependencies are met e.g. waiting for another Fiber to finish execution. This allows for yielding in the middle of an execution (Including deep call stacks) then waiting for another Fiber to finish before gracefully continuing execution from where you have left off. 

## Pros and cons of Fiber + Task scheduler
**Pro’s**
* Allows lock-free taskifying of engine & gameplay code 
* Yield in the middle of an execution including deep call stacks
* Intuitive way of programming asynchronous operations
* Light-weight & performant
* Significantly less context switches compared to classical threading

**Con’s**
* System synchronization primitives (Mutexes, condition variables etc.) can no longer be used as these are locked to a particular thread while Fibers migrate between threads. [0]
* Synchronization has to be done on the hardware level. [0]
* many libraries yield a fiber implicitly as a method of conducting non-blocking I/O; as such, some caution and documentation reading is advised. [1]
* fibers cannot utilize multiprocessor machines without also using preemptive threads. [1]

**Notes**
* TLS addresses are optimizable by the compiler by allowing them to be cached for the duration of the function. When you switch fiber in the middle of a function and you wake up you end up with the wrong TLS pointer. MSVC has a special compilation flag to help avoid this. [0]

## Implementations

* [Boost::fibers (Documentation)](https://github.com/boostorg/fiber)
* [Libcoro (C-library implementation of coroutines)](https://github.com/RichieSams/FiberTaskingLib)
* [Fiber-Job-System by Freeeaky](https://github.com/Freeeaky/fiber-job-system)
* [FiberTaskingLib by RichieSams](https://github.com/RichieSams/FiberTaskingLib)

## Alternatives tools for asynchronous operations in c++

* [`std::future`](https://en.cppreference.com/w/cpp/thread/future) & [`std::promise`](https://en.cppreference.com/w/cpp/thread/promise) (and [chained continuations](https://vittorioromeo.info/index/blog/zeroalloc_continuations_p0.html) based on them)
* [Resumable Functions (by Microsoft)](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4134.pdf)
* [std::thread (Classical Multithreading)](http://www.cplusplus.com/reference/thread/thread/)

## Conclusion

Fibers in combination with a task-manager is extremely powerful yet simpler to use than normal threading. CPU’s are currently in a transition phase where high core count cpu’s become available to mainstream consumers. Thinking how to parallelize now will give you an edge in the coming years and allow your project to scale more efficiently. 

At the moment of writing this the exact product vision for the Wisp project is still being defined. Thus saying we should or shouldn’t use fibers is a stretch. However, when the product vision is defined and it indicates that parallelization is needed then fibers are the way to go.

## Sources

* [[0] Parallelizing-the-Naughty-Dog-Engine]()
* [[1] Fiber (Computer science) Wikipedia page]()
* [[2] Adam Rosenfield reply on"What is the difference between a thread and a fiber?"]()

