---
title: Radix Sort
date: 2020-05-30 10:57:36
tags: Introduce To Algorithm
---
Radix sort is an ancient sorting algorithm, which is uesed in card sorting machines. The basic idea of radix sort is sorting by each bits in the numbers from ones to the highest bits. Because different number has different bits, we fill the short number with zero, then sorting the numbers from lowest bits to highest bits.

There is an sample example about radix sort:

![radix_sort](/images/13.jpg)

Each bits sorting process can be tackled by **count sort**, which you can refer to [count sort](https://beecaffe.github.io/2020/05/28/Count-Sort/). And the time complexity is **O(d(n+k))**, where **d** is the bit number, **n** is the number of data, **k** is 10.

### Code
```cpp
#include<iostream>
#include<vector>
using namespace std;

void count_sort(vector<int> &array,int bits){
    int n=array.size();
    vector<int> countArray(10,0);
    vector<int> outputArray(n,0);
    for(int i=0;i<n;++i){
        countArray[(array[i]/bits)%10]++;
    }

    for(int i=1;i<10;++i){
        countArray[i]+=countArray[i-1];
    }

    for(int i=0;i<n;++i){
        outputArray[countArray[(array[i]/bits)%10]]=array[i];
        countArray[(array[i]/bits)%10]--;
    }
    array=outputArray;
}

int get_max(vector<int> &array){
    int ret=INT32_MIN;
    for(auto i:array) ret=max(ret,i);
    return ret;
}

void radix_sort(vector<int> &array){
    int bits=1;
    int max=get_max(array);
    for(;max/bits>0;bits*=10){
        count_sort(array,bits);
    }
}

int main(){
    vector<int> array={329,457,657,839,436,720,355};
    radix_sort(array);
    for(auto i:array) cout<<i<<" ";
    cout<<endl;
    return 0;
}
```


