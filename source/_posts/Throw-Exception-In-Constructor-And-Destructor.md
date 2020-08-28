---
title: Throw Exception In Constructor And Destructor
date: 2020-05-15 22:35:57
tags: C++
---
## What will happen if throw a exception in constructor?
* **Conculsion**: Throwing an exception in constructor may case two problem:
  (1) The destructor of this Class can not be called;
  (2) Memory leaky;
* **Explain**:
As the following code, class ObjectA is a base class which is inherient by class Normal.Meanwhile, there is also another class, class Evil, which has throwed an exception in constructor. Class Normal has a member variable of Class Evil. In the constructor of Class Normal, when the constructor is called, there are four member should be constrcut, their are  m_a,m_evil,m_resource and m_value in Class ObjectA. In Class Normal, m_a,m_resource and m_value have been constructed correctly, however when compile attempts to construct m_evil, there has throwed and exception.It cause the construction of Class Normal has been stopped, thus, Class Normal's destructor can not be called correctly and m_resource can not be delete. So, there is a memory leaky in this code.
* **Code**:
```
#include<stdio.h>
using namespace std;
class ObjectA{
    public:
        explicit ObjectA(int a):m_value(new int(a)){}
    private:
        int *m_value;
};

//Throw a exception in Class Evil
class Evil{
    public:
        Evil(){throw 10;};
        ~Evil(){};
};

//destructor can not be called, and m_resource can not be delete, which causes memory leaky.
class Normal:public ObjectA{
    public:
        explicit Normal(int a):ObjectA(a), m_a(a+1),m_resource(new int(a+2)){}
        ~Normal(){delete m_resource;}
    private:
        ObjectA m_a;
        Evil m_evil;
        int *m_resource;
};

static void hasMemoryLeak(){
    try{
        Normal n(1);
    }catch(...){
    }
}

int main(){
    hasMemoryLeak();
    return 0;
}
```
