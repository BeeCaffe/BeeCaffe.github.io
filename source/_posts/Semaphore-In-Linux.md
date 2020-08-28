---
title: Semaphore In Linux
date: 2020-05-15 22:25:51
tags: Linux
---
## header
---
```
#include<semaphore.h>
```
## Category
---
[sem_t](#sem_t)

[sem_init](#sem_init)

[sem_open](#sem_open)

[sem_destroy](#sem_destroy)

[sem_wait](#sem_wait)

[sem_post](#sem_post)

[code](#code)

## sem_t
---
In C language, the sem_t is the semphore structor, which is a long int in basic.
```
typedef long long sem_t;
```
## sem_init
---
```
int sem_init(sem_t *sem, int pshared, unsigned int value);
```
* **brief:**  initializes the unnamed semaphore at the address pointed to by sem.  The value argument specifies the initial value for the semaphore.
* **arguments:**
    * **-sem:** The semaphore flag.
    * **-pshared:** indicates whether this semaphore is to be shared between the threads of a process, or between processes.
    If pshared has the value 0, then the semaphore is shared between the threads of a process, and should be located at some address that is visible to
       all threads (e.g., a global variable, or a variable allocated dynamically on the heap).

       If pshared is nonzero, then the semaphore is shared between processes, and should be located  in  a  region  of  shared  memory  (see  shm_open(3),
       mmap(2),  and shmget(2)).  (Since a child created by fork(2) inherits its parent's memory mappings, it can also access the semaphore.)  Any process
       that can access the shared memory region can operate on the semaphore using sem_post(3), sem_wait(3), and so on.

       Initializing a semaphore that has already been initialized results in undefined behavior.
    * **-value:** 
* **return value:**
sem_init() returns 0 on success; on error, -1 is returned, and errno is set to indicate the error.
* **code:**
```cpp
//initializes unamed semaphore
sem_t *mutex;
sem_init(mutex,0,1);
```

## sem_open
---
```
sem_t *sem_open(const char *name, int oflag);
sem_t *sem_open(const char *name, int oflag,mode_t mode, unsigned int value);
```
* **brief:**  initialize and open a named semaphore
* **arguments:** 
    * **-name:** name of semphore.
    * **-flag:** specifies flags that control the operation of the call. (Definitions  of  the  flags  values  can  be  obtained  by  including
       <fcntl.h>.)  If O_CREAT is specified in oflag, then the semaphore is created if it does not already exist.  The owner (user ID) of the semaphore is
       set to the effective user ID of the calling process.  The group ownership (group ID) is set to the effective group ID of the calling  process.   If
       both O_CREAT and O_EXCL are specified in oflag, then an error is returned if a semaphore with the given name already exists.

       If  O_CREAT is specified in oflag, then two additional arguments must be supplied.  The mode argument specifies the permissions to be placed on the
       new semaphore, as for open(2).  (Symbolic definitions for the permissions bits can be obtained by including <sys/stat.h>.)   The  permissions  set‐
       tings are masked against the process umask.  Both read and write permission should be granted to each class of user that will access the semaphore.
       The value argument specifies the initial value for the new semaphore.  If O_CREAT is specified, and a semaphore with the given name already exists,
       then mode and value are ignored.
* **return value:**  On  success,  sem_open()  returns the address of the new semaphore; this address is used when calling other semaphore-related functions.  On error,
       sem_open() returns SEM_FAILED, with errno set to indicate the error.
```cpp
//initializes a named semaphore
sem_t mutex;
mutex=sem_open("name_mutex",O_CREATE,0644,1);
```

## sem_desroy
---
```
 int sem_destroy(sem_t *sem);
````
* **brief:** destroy an unnamed semaphore, sem_destroy() destroys the unnamed semaphore at the address pointed to by sem. Only a semaphore that has been initialized by sem_init(3) should be destroyed using sem_destroy().Destroying a semaphore that other processes or threads are currently blocked on (in sem_wait(3)) produces undefined behavior.Using a semaphore that has been destroyed produces undefined results, until the semaphore has been reinitialized using sem_init(3).
* **arguments:** 
    * **-sem:** the semaphore ID.
* **return value:** sem_destroy() returns 0 on success; on error, -1 is returned, and errno is set to indicate the error.
* **code**
```
//initializes a semaphore and destory it.
sem_t* mutex;
sem_init(mutex,0,1);
sem_desotroy(mutex);
```

## sem_wait, sem_trywait, sem_timewait
```
//blocking lock a semaphore
int sem_wait(sem_t *sem);
args: 
    -sem: the semaphore ID.

//unblocking lock a semaphore
int sem_trywait(sem_t *sem);
args:
    -sem: the semaphore ID

//
int sem_timedwait(sem_t *sem, const struct timespec *abs_timeout);
args:
    -sem: the semaphore ID
```
* **brief:** 
    * **sem_wait()** decrements (locks) the semaphore pointed to by sem.  If the semaphore's value is greater than zero, then the decrement proceeds, and thefunction returns, immediately.  If the semaphore currently has the value zero, then the call blocks until either it becomes possible to perform the decrement (i.e., the semaphore value rises above zero), or a signal handler interrupts the call.
    
    *  **sem_trywait()**  is  the  same  as sem_wait(), except that if the decrement cannot be immediately performed, then call returns an error (errno set to EAGAIN) instead of blocking.
    
    *  **sem_timedwait()** is the same as sem_wait(), except that abs_timeout specifies a limit on the amount of time that the call should block if the decre‐ment cannot be immediately performed.  If the operation can be performed immediately, then sem_timedwait() never fails with a timeout error, regardless of the value of abs_timeout.  Fur‐thermore, the validity of abs_timeout is not checked in this case.
    
* **return value:**   All of these functions return 0 on success; on error, the value of the semaphore is left unchanged, -1 is returned, and errno is  set  to  indicate the error.
* **code:**
```cpp
//place holder
```

## sem_post
```
//increments (unlock) a semaphore
int sem_post(sem_t *sem);
args:
    -sem: the semaphore ID
```

* **brief:**  sem_post()  increments  (unlocks)  the  semaphore pointed to by sem.  If the semaphore's value consequently becomes greater than zero, then another process or thread blocked in a sem_wait(3) call will be woken up and proceed to lock the semaphore.

* **return value:**  sem_post() returns 0 on success; on error, the value of the semaphore is left unchanged, -1 is returned, and errno is set to indicate the error.

## Code
There is my own simple semaphore class , which based on semaphore in linux. There are three files in total: semaphore.h, semaphore.cc and semaphore_test
```cpp
//semaphore.h
#ifndef __BEE_MUTEX_H
#define __BEE_MUTEX_H
#include<pthread.h>
#include<semaphore.h>
#include"macro.h"
/*************************************************************************
 *Class Semaphore
 ************************************************************************/
namespace bee{
class Semaphore{
public:
    Semaphore(int count=0);

   ~Semaphore();

    void wait();

    void trywait();

    void notify();

private:
    //The semaphore
    sem_t m_semaphore;
};
};
#endif
```
```cpp
//semaphore.cc
#include"mutex.h"
/*************************************************************************
 *Class Semaphore
 ************************************************************************/
namespace bee{
Semaphore::Semaphore(int count){
    if(sem_init(&m_semaphore,0,count)==-1){
        ASSERT_DESC(false,"sem_init error!");
    }
}

Semaphore::~Semaphore(){
    if(sem_destroy(&m_semaphore)==-1){
        ASSERT_DESC(false,"sem_destroy error!");
    }
}

void Semaphore::wait(){
    if(sem_wait(&m_semaphore)==-1){
        ASSERT_DESC(false,"sem_wait error!");
    }
}

void Semaphore::trywait(){
    if(sem_trywait(&m_semaphore)==-1){
        ASSERT_DESC(false,"sem_trywait error!");
    }
}

void Semaphore::notify(){
    if(sem_post(&m_semaphore)){
        ASSERT_DESC(false,"sem_post error");
    }
}
};
```

And There is a test program of my Semaphore class, where we use semaphore to make  main thread thread 1 run a function func(). We set a global semaphore instance sem, and initializes it's semaphore as 1. When main function or thread 1 are going to run, they must get a semaphore. Therefore, the program would be alternately executed in turn.
```cpp
//semaphore test
#include<iostream>
#include<pthread.h>
#include<assert.h>
#include"../net/mutex.h"
using namespace std;
using namespace bee;
static bee::Semaphore sem(1);
void* func(void*){
    for(int i=0;i<5;++i){
        sem.wait();
        cout<<"func is running ..."<<endl;
        sem.notify();
        sleep(2);
    }
    return nullptr;
}

int main(){
    pthread_t tid;
    if(pthread_create(&tid,nullptr,func,nullptr)==-1){
        assert(false);
    }

    for(int i=0;i<5;++i){
        sem.wait();
        cout<<"main is running ..."<<endl;
        sem.notify();
        sleep(2);
    }

    if(pthread_join(tid,nullptr)==-1){
        assert(false);
    }
    return 0;
}
```
result:

```cpp
main is running ...
func is running ...
main is running ...
func is running ...
main is running ...
func is running ...
main is running ...
func is running ...
main is running ...
func is running ...
```
