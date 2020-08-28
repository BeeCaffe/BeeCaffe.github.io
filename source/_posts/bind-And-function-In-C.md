---
title: bind And function In C++
date: 2020-05-15 22:34:51
tags: C++
---
## Header
```cpp
#include<functional>
```
## std::function()
There are four types callable objects in C++ in total.
* function
* lambda expression
* overload operator() class 
* std::function() object or funtion pointer

```
//example of callable object in C++
// function
int add(int a, int b){return a+b;} 

// lambda expression
auto mod = [](int a, int b){ return a % b;}

// calss with overloading operator()
struct divide{
    int operator()(int denominator, int divisor){
        return denominator/divisor;
    }
};
```

We can reserve the front callable object in std::funtion(), as following:

```cpp
std::function<int(int ,int)>  a = add; 
std::function<int(int ,int)>  b = mod ; 
std::function<int(int ,int)>  c = divide(); 
```
Therefore, std::function() is able to save function, lambda expression, function pointer and class with overloading operator(). Generally, std::function() is utilized as callback function to delay the function running time.

## std::bind()
As we know the function always has arguments list, and std::function is able to bind callable object with arguments. The result of std::bind() is able to save by std::function().

* **bind with function**
    ```cpp
    using std::placeholder::_1;
    int sum(int a,int b){
        return a+b;
    }
    
    //with out place holder
    auto cb=std::bind(sum,21,23);
    cout<<cb()<<endl;
    
    //with place holder
    auto cb=std::bind(sum,21,_1);
    cout<<cb(10)<<endl;
    ```
* **bind arguments to a void() function**

    Using std::function() and std::bind(), we can bind any numbers of  arguments with a std::function<void()> functor.
    ```cpp
    using std::placeholders::_1;
    void sum(int a,int b){
        cout<<a+b<<endl;
    }
    
    //bind std::function<void(int,int)> to a std::function<void()>
    std::function<void()> cb;
    cb=std::bind(sum,10,12);
    cb();
    ```
* **bind with member function**
    
    If you suppose to bind a member function, you need to bind three parts:
    * Member function reference, e.g. &Sum::sum
    * Object reference, e.g. &s
    * arguments.
    ```
    #include<functional>
    #include<iostream>
    using namespace std;
    using std::placeholders::_1;
    class Sum{
    public:
        void sum(int a,int b){
        cout<<a+b<<endl;
        }
    };
    int main(){
        Sum s;
        std::function<void()> cb;
        //bind class Sum function sum.
        cb=std::bind(&Sum::sum,&s,10,15);
        cb();
        return 0;
    }
    ```
* **bind with lambda**
    ```cpp
        #include<functional>
        #include<iostream>
        using namespace std;
        using std::placeholders::_1;
        int main(){
            auto lbda=[](int a,int b){
                cout<<a+b<<endl;
            };
            auto cb = std::bind(lbda,12,23);
            cb();
            return 0;
        }
    ```
    
## Rerference
[1] https://www.jianshu.com/p/f191e88dcc80