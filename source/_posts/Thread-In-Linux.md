---
title: Thread In Linux
date: 2020-05-15 22:27:20
tags: Linux
---
## Header

---

```cpp
#include<pthread.h>
```
## List

---
* [pthread_create](#pthread_create)()
* [pthread_join](#pthread_join)()
* [pthread_detach](#pthread_detach)()
* [pthread_slef](#pthread_self)()
* [pthread_exit](#pthread_exit)()

## pthread_create

---

```
int pthread_create(pthread_t *thread,const pthread_attr_t *attr,void* (*start_routine)(void*), void *arg);

```
* **brief:** The pthread_create() function starts a new thread in the calling process, the new thread starts execution by invoking start_routine();
* **arguments:**
    * **-thread:** The pointer points to thread identifier.
    * **-attr:** The  attr argument points to a pthread_attr_t structure whose contents are used at thread creation time to determine attributes for the new thread;this structure is initialized using pthread_attr_init(3) and related functions.  If  attr  is  NULL,  then  the  thread  is  created  with  default attributes.
    * **-start_routine:** The function address of thread run.
    * **-arg** The arguments of start_toutine.

* **return value:**
On success, pthread_create() returns 0; on error, it returns an error number, and the contents of *thread are undefined.
* **Code:**
    ```cpp
    #include<pthread>
    //this is a sample example of pthread_create.
    //start routine 1
    void* func1(void* arg){
        for(int i=0;i<10;++i) std::cout<<"thread_1: "<<i<<std::endl;
    }
    //start routine2
    void* func2(void* arg){
        for(int i=0;i<10;++i) std::cout<<"thread_2: "<<i<<std::endl;
    }
    
    int main(){
        pthread_t tidp1;
        pthread_t tidp2;
        //using pthread_create creates a function
        if(pthread_create(&tidp1,nullptr,func1,nullptr)==-1){
            return -1;
        }
        //using pthread_create creates a function
        if(pthread_create(&tidp2,nullptr,func2,nullptr)==-1){
            return -1;
        }
        for(int i=0;i<10;++i) std::cout<<"thread_main: "<<i<<std::endl;
        return 0;
    }
    ```
**Note:** The library "pthread.h" is not a build in library of linux, when you compile source code, you should add commain **-lpthread** like this:
```cmd
g++ thread_create.cc -lpthread
```
## pthread_join

---

```cpp
int pthread_join(pthread_t thread,void** retval);
```
* **brief:** The pthread_join() function waits for the thread specified by thread to terminate, If that thread has already terminated, then pthread_join run immediately.The thread specified by thread must be joinable.

* **arguments:** 
    * **-thread:** The thread identifier that is waitted.
    * **-retval:**  The return value of thread which is waitted
* **return value** On success, pthread_join() returns 0; on error, it returns an error number.

* **Note:** This function is a blocking funtion. Suppose we have two thread, thread A and thread B. If we call pthread_join() int thread A, thread A will blocking wait for the finish of thread B. If we do not call pthread_join, thread A may finish quickly, and thread B can not be executed.

* **Code:**
```cpp
#include <pthread.h>
#include<iostream>
#include<unistd.h>
#include<stdlib.h>
//In this program, we do not call pthread_join(), the main 
//function finished before thread_1 fininshed.
void* func1(void* arg){
        for(int i=0;i<10;++i){
                std::cout<<"thread_1: "<<i<<std::endl;
                sleep(1);
        }
}

int main(){
        pthread_t tid;
        if(pthread_create(&tid,nullptr,func1,nullptr)==-1){
                return -1;
        }

        for(int i=0;i<10;++i){
                std::cout<<"thread_main: "<<i<<std::endl;
        }
        std::cout<<"Main Function END!"<<std::endl;
        return 0;
}
```
Result: 
![image.png](https://upload-images.jianshu.io/upload_images/13348038-c5c6cfe7efe3895a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```cpp
#include <pthread.h>
#include<iostream>
#include<unistd.h>
#include<stdlib.h>
//The pthread_join() is called, the main thread waits for //thread 1 finish.
void* func1(void* arg){
        for(int i=0;i<10;++i){
                std::cout<<"thread_1: "<<i<<std::endl;
                sleep(1);
        }
}

int main(){
        pthread_t tid;
        if(pthread_create(&tid,nullptr,func1,nullptr)==-1){
                return -1;
        }

        for(int i=0;i<10;++i){
                std::cout<<"thread_main: "<<i<<std::endl;
        }
        if(pthread_join(tid,nullptr)){
                std::cout<<"error"<<std::endl;
        }

        std::cout<<"Main Function END!"<<std::endl;
        return 0;
}
```

![image.png](https://upload-images.jianshu.io/upload_images/13348038-4fd12eaa3611cca6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## pthread_detach

---
[link](https://note.youdao.com/)
```
int pthread_detach(pthread_t thread);
```
* **Brief:** The pthread_detach()  function  marks the thread identified by thread as detached.  When a detached thread terminates, its resources are automati‐cally released back to the system without the need for another thread to join with the terminated thread.Attempting to detach an already detached thread results in unspecified behavior.
* **Arguments:**
    * **-thread:** The detached thread identifier.
* **Return Value:**
On success, pthread_detach() returns 0; on error, it returns an error number.

* **Code:**
```
#include<pthread.h>
#include<iostream>
#include<unistd.h>
//The func is a dead loop, but the resources of pthread_detach() 
//marks program are automatically released back to system. So 
//the loop finished after Main process is finished.
void* func(void* arg){
        for(;;){
                std::cout<<"thread_1 running ..."<<std::endl;
        }
}

int main(){
        pthread_t tid;
        if(pthread_create(&tid,nullptr,func,nullptr)==-1){
                return -1;
        }
        pthread_detach(tid);
        std::cout<<"Main Function Done!"<<std::endl;
        return 0;
}
```

![image.png](https://upload-images.jianshu.io/upload_images/13348038-92c4f0747486ddab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## pthread_self
```
pthread_t pthread_self(void);
```
* **Brief:** The pthread_self() function returns the ID of the calling thread.  This is the same value that is returned in *thread in the pthread_create(3) call that created this thread.
* **Return:**
 This function always succeeds, returning the calling thread's ID.
* **Code:**
```
#include<iostream>
#include<pthread.h>
#include<unistd.h>

void* func(void* arg){
        pthread_detach(pthread_self());
        for(;;){
                std::cout<<"thread 1 running ...\n";
        }
        std::cout<<std::endl;
}

int main(){
        pthread_t tid;
        if(pthread_create(&tid,nullptr,func,nullptr)){
                return -1;
        }
        std::cout<<"Main Function Done!"<<std::endl;
        return 0;
}
```
## pthread_exit
```
void pthread_exit(void* retval);
```
* **Brief:**

    The  pthread_exit()  function terminates the calling thread and returns a value via retval that (if the thread is joinable) is available to another
    thread in the same process that calls pthread_join(3).
    
    Any clean-up handlers established by pthread_cleanup_push(3) that have not yet been popped, are popped (in the reverse of the order in  which  they
    were  pushed)  and  executed.   If the thread has any thread-specific data, then, after the clean-up handlers have been executed, the corresponding
    destructor functions are called, in an unspecified order.
    
    When a thread terminates, process-shared resources (e.g., mutexes, condition variables, semaphores, and file descriptors)  are  not  released,  and
    functions registered using atexit(3) are not called.
    
    After  the  last  thread  in  a  process terminates, the process terminates as by calling exit(3) with an exit status of zero; thus, process-shared
    resources are released and functions registered using atexit(3) are called.
* **Arguments:**
    * **-retval:** void* pointer, I do not know the mean of this argument.
* **Code:**
```
#include<iostream>
#include<pthread.h>
#include<unistd.h>

void* func(void* arg){
        pthread_detach(pthread_self());
        for(;;){
                std::cout<<"thread 1 running ...\n";
        }
        pthread_exit(0);
}

int main(){
        pthread_t tid;
        if(pthread_create(&tid,nullptr,func,nullptr)){
                return -1;
        }
        std::cout<<"Main Function Done!"<<std::endl;
        return 0;
}
```


