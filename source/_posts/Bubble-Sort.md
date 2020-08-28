---
title: Bubble Sort
date: 2020-05-15 12:09:28
tags: Introduce To Algorithm
---
## Code
```
#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;
void swap(int &a,int &b){
    int tmp=a;
    a=b;
    b=tmp;
}
void bubble_sort(vector<int> &nums){
    int n=nums.size();
    for(int i=0;i<n-1;++i){
        for(int j=1;j<n-i-1;++j){
            if(nums[j-1]>nums[j]) swap(nums[j-1],nums[j]);
        }
    }
}
int main(){
    vector<int> nums={2,1,3,6,5,4,7,8,9};
    bubble_sort(nums);
    for(auto i:nums) cout<<i<<" ";
    cout<<endl;
}
```