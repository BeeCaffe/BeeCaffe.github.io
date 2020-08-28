---
title: RWLock In Linux
date: 2020-05-15 22:28:33
tags: Linux
---
Mutex Lock blocks all the threads which attempts to enter critical section. However, the enter cirtical section operation can be divided into read data and write data. According to this principle, the lock can be separted as read lock and write lock. The read-write lock requires to obey the following rules:

* as long as no thread holds any write lock, any threads can get any type (read or write) lock to read.
* when and only when no lock is used to write or read, write lock can be allocated and used to write.

That is to say, as long as no thread is modifing critical section, any type lock can be used to read. When and only when on any other threads are modifing critical section, write lock is able to write.
# header
---
```cpp
#include<pthread.h>
```
## category

[pthread_rwlock_t](#pthread_rwlock_t)

[pthread_rwlock_rdlock](#pthread_rwlock_rdlock)

[pthread_rwlock_wrlock](#pthread_rwlock_wrlock)

[pthread_rwlock_unlock](#pthread_rwlock_unlock)

[pthread_rwlock_tryrdlock](#pthread_rwlock_trywrlock)

[pthread_rwlock_trywrlock](#pthread_rwlock_trywrlock)

[pthread_rwlock_destroy](pthread_rwlock_destroy)

[pthread_rwlockattr_init](#pthread_rwlockattr_init)

[pthread_rwlockattr_destroy](pthread_rwlockattr_destroy)

[pthread_rwlockattr_getpshared](pthread_rwlockattr_getpshared)

[pthread_rwlockattr_setpshared](pthread_rwlockattr_setpshared)

## pthread_rwlock_t
---
```cpp
//The defination of pthread_rwlock_t
typedef struct{
    pthread_mutex_t rw_mutex;   /*basic lock on this struct*/
    pthread_cond_t rw_condreaders;  /*for reader threads waiting*/
    pthread_cond_t rw_condwriters;  /*for writer threads waiting*/
    int rw_magic;   /*for error checking*/
    int rw_nwaitreaders;    /*the number waiting*/
    int rw_nwaitwriters;    /*the number waiting*/
    int rw_refcount;    /*-1 if writer has the lock, else readers holding the lock*/
}pthread_rwlock_t;

#define PTHREAD_RWLOCK_INITIALIZER{
    PTHREAD_LOCK_INITIALIZER,
    PTHREAD_COND_INITIALIZER,
    PTHREAD_COND_INITIALIZER,
    RW_MAGIC,
    0,0,0
}
```

## pthread_rwlock_rdlock
---
```cpp
//get a read lock, if the lock hold by other thread, blocking and waiting for unlocking.
int pthread_rwlock_rdlock(pthread_rwlock_t *rwptr);

args:
    -rwptr: the read-write lock
    
return:
    seccessed: 0
    error: EXXX

```

## pthread_rwlock_wrlock
---
```cpp
//get a write lock, if lock is got by other threads, blocking and waiting for unlocking.
int pthread_rwlock_wrlock(pthread_rwlock_t *rwptr);

args:
    -rwptr: the read-write lock
    
return:
    seccessed: 0
    error: EXXX
```
## pthread_rwlock_unlock
---
```cpp
//release a read-wirte lock
int pthread_rwlock_unlock(pthread_rwlock_t *rwptr);

args:
    -rwptr: the read-write lock
    
return:
    seccessed: 0
    error: EXXX
```

## pthread_rwlock_tryrdlock
---
```cpp
//gets a read lock, if the lock hold by other thread, return a EXXX without blocking
int pthread_rwlock_tryrdlock(pthread_rwlock_t *rwptr);

args:
    -rwptr: the read-write lock
    
return:
    seccessed: 0
    error: EXXX
```

## pthread_rwlock_trywrite
---
```cpp
//gets a write lock, if the lock hold by other thread, return a EXXX without blocking
int pthread_rwlock_trywrlock(pthread_rwlock_t *rwptr);

args:
    -rwptr: the read-write lock
    
return:
    seccessed: 0
    error: EXXX
```

## pthread_rwlock_init
---
```cpp
//initializes a read-write lock
int pthread_rwlock_init(pthread_rwlock_t *rwptr,const pthread_rwlockattr_t *attr);

args:
    -rwptr: the read-write lock
    -attr: the attribute

return:
    seccessed: 0
    error: EXXX
```

## pthread_rwlock_destroy
---
```cpp
//destroy a read-write lock
int pthread_rwlock_destroy(pthread_rwlock_t *rwptr);

args:
    -rwptr: the read-write lock

return:
    seccessed: 0
    error: EXXX
```
## pthread_rwlockattr_init
---
```cpp
//set a pthread_rwlockattr_t
int pthread_rwlockattr_init(pthread_rwlock_t *attr);

args:
    -attr: the attribute

return:
    seccessed: 0
    error: EXXX
```

## pthread_rwlockattr_destroy
---
```cpp
//destroy a pthread_rwlockattr_t
int pthread_rwlockattr_destroy(pthread_rwlock_t *attr);

args:
    -attr: the attribute

return:
    seccessed: 0
    error: EXXX
```

## pthread_rwlockattr_getpshared
---
```cpp
int pthread_rwlockattr_getpshared(pthread_rwlock_t *attr,int *valptr);

args:
    -attr: the attribute
    -valptr: the attribute value
return:
    seccessed: 0
    error: EXXX
```

## pthread_rwlockattr_setpshared
---
```cpp
int pthread_rwlockattr_setpshared(pthread_rwlock_t *attr,int *valptr);

args:
    -attr: the attribute
    -valptr: the attribute value
return:
    seccessed: 0
    error: EXXX
```
