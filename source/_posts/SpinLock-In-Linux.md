---
title: SpinLock In Linux
date: 2020-05-15 22:30:05
tags: Linux
---
## header
```cpp
#include<pthread.h>
```
## category
[pthread_spinlock_t](#pthread_spinlock_t)

[pthread_spin_init](#pthread_spin_init)

[pthread_spin_destroy](#pthread_spin_destroy)

[pthread_spin_lock](#pthread_spin_lock)

[pthread_spin_trylock](#pthread_spin_trylock)

[pthread_spin_unlock](#pthread_spin_unlock)
[code](#code)

## pthread_spinlock_t
---
```cpp
 //initializes method
 pthread_spinlock_t spinlock;
 pthread_spin_init(&spinlock,nullptr);
```
## pthread_spin_init
---
```cpp
int pthread_spin_init(pthread_spinlock_t *lock,int pshared);
args:
    -lock: the spinlock type in linux
    -pshared: 
        -PTHREAD_PROCESS_PRIVATE:spinlock can only be operated by the threads in the same process.
        -PTHREAD_PROCESS_SHARED:spinlock can be operated by any thread in any process.
return:
    successed: 0
    error: Exxx
```
## pthread_spin_destroy
---
```cpp
 int pthread_spin_destroy(pthread_spinlock_t *lock);
 args:
    -lock: the spinlock type in linux
 return:
    successed: 0
    error: Exxx
```
## pthread_spin_lock
---
```cpp
int pthread_spin_lock(pthread_spinlock_t *lock);
 args:
    -lock: the spinlock type in linux
 return:
    successed: 0
    error: Exxx
```
## pthread_spin_trylock
---
```cpp
int pthread_spin_trylock(pthread_spinlock_t *lock)
 args:
    -lock: the spinlock type in linux
 return:
    successed: 0
    error: Exxx
```
## pthread_spin_unlock
---
```cpp
 int pthread_spin_unlock(pthread_spinlock_t *lock);
 args:
    -lock: the spinlock type in linux
 return:
    successed: 0
    error: Exxx
```

## code
---
* **producer and comsumer model 1: N-1** In this model, we build a class, called CritialZone, where we have a pool to write and read (produce and consume). In this model, we simplely create 10 threads as producer to produce data. After pool is full, we call consumer to read all data in one time.
```cpp
#include<pthread.h>
#include<iostream>
#include<vector>
#include<assert.h>
using namespace std;
class CriticalZone{
public:
    static pthread_spinlock_t s_mutex;
    static vector<int> s_pool;
    static int s_size;
    static int s_cur;
    static int s_val;
};
pthread_spinlock_t CriticalZone::s_mutex;
int CriticalZone::s_size=1024;
vector<int> CriticalZone::s_pool;
int CriticalZone::s_cur=0;
int CriticalZone::s_val=0;

void* consumer(void*){
    for(int i=0;i<CriticalZone::s_size;++i){
        std::cout<<"consuming data: "<<CriticalZone::s_pool[CriticalZone::s_cur]<<endl;
        CriticalZone::s_cur--;
        CriticalZone::s_pool.pop_back();
    }
}

void* producer(void*){
    for(;;){
        pthread_spin_lock(&CriticalZone::s_mutex);
        cout<<"thread_"<<pthread_self()<<" is writing value:"<<CriticalZone::s_val<<endl;
        if(CriticalZone::s_cur>=CriticalZone::s_size){
            cout<<"pool is full"<<endl;
            pthread_spin_unlock(&CriticalZone::s_mutex);
            return nullptr;
        }
        CriticalZone::s_pool.push_back(CriticalZone::s_val);
        CriticalZone::s_val++;
        CriticalZone::s_cur++;
        pthread_spin_unlock(&CriticalZone::s_mutex);
    }
}

int main(){
    vector<pthread_t> tids(10);
    pthread_spin_init(&CriticalZone::s_mutex,0);
    for(int i=0;i<10;++i){
        if(pthread_create(&tids[i],nullptr,producer,nullptr)){
            assert(false);
        }
    }
    for(int i=0;i<10;++i){
        if(pthread_join(tids[i],nullptr)){
            assert(false);
        }
    }
    pthread_t tid;
    if(pthread_create(&tid,nullptr,consumer,nullptr)){
        assert(false);
    }


    if(pthread_join(tid,nullptr)){
        assert(false);
    }

    cout<<"main end!"<<endl;
    return 0;
}
```

* **producer and consumer model 2: N-1** In this model, we create 10 threads to write and one threads to read. When there is no data in the pool, the consumer should recurrently wait for the data, util the pool is not empty.
```cpp
#include<pthread.h>
#include<iostream>
#include<vector>
#include<assert.h>
using namespace std;
class CriticalZone{
public:
    static pthread_spinlock_t s_mutex;
    static vector<int> s_pool;
    static int s_size;
    static int s_cur;
    static int s_val;
};
pthread_spinlock_t CriticalZone::s_mutex;
int CriticalZone::s_size=1024;
vector<int> CriticalZone::s_pool;
int CriticalZone::s_cur=0;
int CriticalZone::s_val=0;
void consume_wait(int i){

    for(;;){
        pthread_spin_lock(&CriticalZone::s_mutex);
        if(i<CriticalZone::s_cur){
            pthread_spin_unlock(&CriticalZone::s_mutex);
            return;
        }
        pthread_spin_unlock(&CriticalZone::s_mutex);
    }
}

void* consumer(void*){
    for(int i=0;i<CriticalZone::s_size;++i){
        consume_wait(i);
        std::cout<<"consuming data: "<<i<<endl;
    }
}

void* producer(void*){
    for(;;){
        pthread_spin_lock(&CriticalZone::s_mutex);
        cout<<"thread_"<<pthread_self()<<" is writing value:"<<CriticalZone::s_val<<endl;
        if(CriticalZone::s_cur>=CriticalZone::s_size){
            cout<<"pool is full"<<endl;
            pthread_spin_unlock(&CriticalZone::s_mutex);
            return nullptr;
        }
        CriticalZone::s_pool.push_back(CriticalZone::s_val);
        CriticalZone::s_val++;
        CriticalZone::s_cur++;
        pthread_spin_unlock(&CriticalZone::s_mutex);
    }
}

int main(){
    vector<pthread_t> tids(10);
    pthread_spin_init(&CriticalZone::s_mutex,0);
    for(int i=0;i<10;++i){
        if(pthread_create(&tids[i],nullptr,producer,nullptr)){
            assert(false);
        }
    }
    pthread_t tid;
    if(pthread_create(&tid,nullptr,consumer,nullptr)){
        assert(false);
    }

    for(int i=0;i<10;++i){
        if(pthread_join(tids[i],nullptr)){
            assert(false);
        }
    }


    if(pthread_join(tid,nullptr)){
        assert(false);
    }

    cout<<"main end!"<<endl;
    return 0;
}
```
* **producer and consumer model 3: N-N** In this model, we create 10 threads to write and 10 threads to read. when and only when, there are on writers or readers writing or reading writer and reader can write and read. That is to say, lock is monopolized by one object.
```cc
```
