---
title: Mutex In Linux
date: 2020-05-15 22:27:58
tags: Linux
---
## header
---
```
#include<thread.h>
```
## category
---
[pthread_mutex_t](#pthread_mutex_t)

[pthread_mutex_lock](#pthread_mutex_lock)

[pthread_mutex_trylock](#pthread_mutex_trylock)

[pthread_muext_init](pthread_mutex_init)

[pthread_mutex_unlock](#pthread_mutex_unlock)

[pthread_mutex_destroy](#pthread_mutex_destroy)

## pthread_mutex_t
The pthread_mutex_t is a mutex data type, which can be initialized by two methods.
```cpp
// if pthread_mutex_t is declared as a static variable
static pthread_mutex_t lock=PTHREAD_MUTEX_INITILIZER;

// if pthread_mutex_t is declared as a dynamic variable
pthread_mutex_t* lock;
pthread_mutex_init(lock);
```

## pthread_mutex_lock
Try to lock a mutex, if the mutex is locked, blocking and waiting for until it is unlock.
```cpp
//lock a mutex variable
int pthread_mutex_lock(pthread_mutex_t *mptr);

args:
    -mptr: a pointer of mutex.
return: 
    successed: 0
    error: EXXX
```

## pthread_mutex_trylock
Try to lock a mutex, if it is locked, return a EBUSY.
```cpp
int pthread_mutex_trylock(pthread_mutex_t *mptr);

args:
    -mptr: a pointer of mutex.
return: 
    successed: 0
    error: EXXX
```

## pthread_mutex_unlock
unlock a mutex, if it is unlock, blocking and waitting.
```cpp
int pthread_mutex_unlock(pthread_mutex_t *mptr);

args:
    -mptr: a pointer of mutex
return:
    successed: 0
    error: EXXX
```
## pthread_mutex_destroy
```cpp
int pthread_mutex_init(pthread_mutex_t *mutex,const pthread_mutexattr_t attr);

args:
    -mutex: the mutex type
    -attr: attribute of mutex
return:
    successed: 0
    error: Exxx
```
## pthread_mutex_init
```cpp
int pthread_mutex_destroy(pthread_mutex_t* mutex);

args:
    -mutex: the mutex type
return:
    successed: 0
    error: EXXX
```
## Code:
```cpp
//consumer and producer model
#include<pthread.h>
#include<vector>
#include<iostream>
#include<unistd.h>
using namespace std;
class Shared{
public:
        static pthread_mutex_t lock;
        static vector<int> array;
        static int nmax;
        static int ncur;
        static int nval;
};
pthread_mutex_t Shared::lock=PTHREAD_MUTEX_INITIALIZER;
vector<int> Shared::array;
int Shared::nmax=0;
int Shared::ncur=0;
int Shared::nval=0;

void* producer(void* arg){

        for(;;){
                pthread_mutex_lock(&Shared::lock);
                std::cout<<"thread ["<<pthread_self()<<"] is writing value: "<<Shared::nval<<std::endl;
                if(Shared::ncur>=Shared::nmax){
                        pthread_mutex_unlock(&Shared::lock);
                        return nullptr;
                }

                Shared::array.push_back(Shared::nval);
                Shared::ncur++;
                Shared::nval++;
                pthread_mutex_unlock(&Shared::lock);
                sleep(1);
        }

}

void* consumer(void* arg){
        for(int i=0;i<Shared::array.size();++i){
                std::cout<<"consume value: "<<Shared::array[i]<<std::endl;
        }
}

int main(int argc,char** argv){
        if(argc!=3){
                std::cout<<"arguments number error"<<std::endl;
                return -1;
        }

        int nmax=atoi(argv[1]);
        int nthreads=atoi(argv[2]);

        vector<pthread_t> producer_tids(nthreads);
        pthread_t consumer_tid;
        Shared::nmax=nmax;

        for(int i=0;i<nthreads;++i){
                if(pthread_create(&producer_tids[i],nullptr,producer,nullptr)){
                        std::cout<<"pthread_create error"<<std::endl;
                        return -1;
                }
        }

        for(int i=0;i<nthreads;++i){
                if(pthread_join(producer_tids[i],nullptr)){
                        std::cout<<"pthread_join error"<<std::endl;
                        return -1;
                }
                std::cout<<"thread "<<i<<" Done!"<<std::endl;
        }

        if(pthread_create(&consumer_tid,nullptr,consumer,nullptr)){
                std::cout<<"pthread_create error"<<std::endl;
                return -1;
        }
        if(pthread_join(consumer_tid,nullptr)){
                std::cout<<"pthread_join error"<<std::endl;
                return -1;
        }
        std::cout<<"Main Function Done!"<<std::endl;
        return 0;
}
```



