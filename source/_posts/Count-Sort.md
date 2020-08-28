---
title: Count Sort
date: 2020-05-28 09:14:32
tags: Introduce To Algorithm
---
**Count Sort** bases on the condition that all elements of waiting to sort are belong to a range in [0,k]. For example, **nums={2,5,3,0,2,3,0,3}**, all elements in **nums** are limited in range form 0 to 7, so k=7 for **nums**. (This is a very improtant condition for count sort, if array does not statisfy this priciple, count sort can not be used.)

The basic idea of Count Sort is: for each elements x, supposing we know how many element smaller than x we can put x to the correct place directly.

However, how can we know how many elements samller than x in nums? Inorder to know that, we can build a new array, which we call **countArray** to count each element occurs times in nums. Then, we traver **countArray** from begin to end to accumulate the value in **countArray**, after that, the elements in **countArray** are the correct index of sorted value of array **nums**. Traveing array **nums** and using **countArray** to find the correct location for ecah value in **nums** and put it to result array **outputArray**. 

And, notice that, there are same element exist in **nums**. Therefore, when we put a **nums** value to **outputArray**, **countArray[nums[i]]--** should be performed, after that, when next **nums[i]** value occurs in **nums**, this value can be  putted to correct location.
## Diagram
![image](/images/4.jpg)

## Code
```cpp
#include<iostream>
#include<vector>
using namespace std;
void count_sort(vector<int> &nums){
    int n=nums.size();
    vector<int> countArray(n,0);
    //counting occur times of each element in nums
    for(auto i:nums) countArray[i]++;
    //accumulating the correct location of each element
    for(int i=1;i<countArray.size();++i){
        countArray[i]=countArray[i]+countArray[i-1];
    }
    //put nums element to correct location in outputArray
    vector<int> outputArray(n,0);
    for(int i=0;i<n;++i){
        outputArray[countArray[nums[i]]]=nums[i];
        countArray[nums[i]]--;
    }
    nums=outputArray;
}

int main(){
    vector<int> nums={2,5,3,0,2,3,0,3};
    count_sort(nums);
    for(auto i:nums) cout<<i<<" ";
    cout<<endl;
    return 0;
}
```
## Time Complexity
**Count Sort** is not a comparsion based sorting algorithm, thus, althought it's condition is strictliy than comparsion based sorting method, it has a fater time complextiy. In this algorithm, we only travel the array without back trace operation, so the time complexity is **O(n)**, which is better than **O(nlog(n))**.
