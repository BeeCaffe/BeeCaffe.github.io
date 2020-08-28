---
title: Fiber In Linux
date: 2020-05-15 22:29:20
tags: Linux
---

Coroutines are computer program components that generalize subroutines for non-preemptive multitasking, by allowing execution to be suspended and resumed. Coroutines are well-suited for implementing familiar program components such as cooperative tasks, exceptions, event loops, iterators, infinite lists and pipes.

Coroutines are very similar to threads. However, coroutines are cooperatively multitasked, whereas threads are typically preemptively multitasked. This means that coroutines provide concurrency but not parallelism. The advantages of coroutines over threads are that they may be used in a hard-realtime context (switching between coroutines need not involve any system calls or any blocking calls whatsoever), there is no need for synchronisation primitives such as mutexes, semaphores, etc. in order to guard critical sections, and there is no need for support from the operating system.

It is possible to implement coroutines using preemptively-scheduled threads, in a way that will be transparent to the calling code, but some of the advantages (particularly the suitability for hard-realtime operation and relative cheapness of switching between them) will be lost [1].

In this blog, we will introduce the Linux library "ucontext.h" and use it to implement a simple coroutines program.

## header
---
```cpp
#include<ucontext.h>
```
## category
---

- [ucontext_t](#ucontext_t)

<!--[mcontext_t](#mcontext_t)-->

- [getcontext](#getcontext)

- [setcontext](#setcontext)

- [makcontext](#makecontext)

- [swapcontext](#swapcontext)
- [code](#code)

## ucontext_t
---
The  mcontext_t  type  is  machine-dependent and opaque.  The
ucontext_t type is a structure that has at least the following fields:
```cpp
typedef struct ucontext_t {
            //points to the context that will be resumed  when  thecurrent  context  terminates
           struct ucontext_t *uc_link;  
           // the set of  signals  blocked  in this context
           sigset_t          uc_sigmask;
           //the stack used by this context
           stack_t           uc_stack;
           //the  machine-specific  representation of the saved context, that includes  the  calling  thread machine registers.
           mcontext_t        uc_mcontext;
           ...
        } ucontext_t;
```
## getcontext
---
```cpp
 //get the user context
 int getcontext(ucontext_t *ucp);
 
 args:
    -ucp: the ucontext_t type, points to the context.
 return:
    seccessed: 0
    error: -1 and set errno appropriately.
```

## setcontext
---
```cpp
 //set the user context
 int setcontext(const ucontext_t *ucp);
 
 args:
    -ucp: the ucontext_t type, points to the context.
 return:
    seccessed: does not return.
    error: -1 and set errno appropriately.
```

## makecontext
---
```cpp
// modifies the context pointed to by ucp
void makecontext(ucontext_t *ucp, void (*func)(), int argc, ...);

args:
    -ucp: the context pointer.
    -func: When this context is later activated the function func is called, 
    -argc: passed the  series  of  integer arguments  that  follow  argc to func; the caller must specify the number of these arguments in argc .
return: 
    no
```
## swapcontext
---
```cpp
//saves the current context in the structure pointed to by oucp, and then activates the context pointed to by ucp.
int swapcontext(ucontext_t *oucp, const ucontext_t *ucp);

args:
    -oucp: The swapcontext() function saves the current context in the structure pointed to by oucp.
    -ucp:  the context pointer.
return:
    successed: does not return, (But we may return later, in case oucp is activated, in which case  it  looks  like  swapcontext() returns 0.)
    error: -1 and sets errno appropriately.
```
## code
---
In this program, we use getcontext() to get the context before print "hello world", then we use setcontext() to recover the context which we stored. Therefore, this program print "hello world" recurrently.
```cpp
//using getcontext and setcontext
#include<ucontext.h>
#include<unistd.h>
#include<iostream>
int main(int argc,char** argv){
        ucontext_t context;

        getcontext(&context);
        std::cout<<"hello world!"<<std::endl;
        sleep(1);
        setcontext(&context);
        return 0;
}
```
result:
![image.png](https://upload-images.jianshu.io/upload_images/13348038-76df75111b70b830.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Reference
[1] https://en.wikipedia.org/wiki/Coroutine#Comparison_with_threads