---
title: Selection Sort
date: 2020-05-15 12:12:26
tags: Introduce To Algorithm
---
Supposing we have a unordered array nums. Firstly, we swap the minmum element in nums to nums[0].Then, swaping to sub-minmum element in nums to nums[1] ..., the rest can do the same manner, until we find the last elemnts. This algorithm is so-called selection sort, because for each time we choose the minimum element in the rest array, and swap it to the front element.
## Code
```
#include<iostream>
#include<vector>
using namespace std;
void choose_sort(vector<int> &nums){
    int min_=0,idx=0;
    for(int i=0;i<nums.size()-1;++i){
        min_=nums[i];idx=i;
        for(int j=i+1;j<nums.size();++j){
            if(min_>nums[j]){
                min_=nums[j];
                idx=j;
            }
        }
        nums[idx]=nums[i];
        nums[i]=min_;
    }
}

int main(){
    vector<int> nums={3,1,2,4,6,7,9,8};
    choose_sort(nums);
    for(auto i:nums) cout<<i<<" ";
    cout<<endl;
    exit(0);
}
```