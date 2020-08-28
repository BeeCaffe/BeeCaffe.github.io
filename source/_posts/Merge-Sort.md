---
title: Merge Sort
date: 2020-05-15 12:11:31
tags: Introduce To Algorithm
---
Based on the idea of divide and conquer, merge sort divides the unordered array with length of n as two sub-array of which length is n/2 until the length of array is 1. As we all know, the array with length 1 is an ordered sequence. Then, meger two sorted sub-array we can get the result.
## Code
```
#include<iostream>
#include<vector>
using namespace std;
void merge(vector<int> &nums,int left,int mid,int right){
    vector<int> arr;
    int p1=left,p2=mid+1;
    while(p1<=mid && p2<=right){
        if(nums[p1]<nums[p2]){
            arr.push_back(nums[p1++]);
        }else arr.push_back(nums[p2++]);
    }
    while(p1<=mid) arr.push_back(nums[p1++]);
    while(p2<=right) arr.push_back(nums[p2++]);
    int j=left;
    for(int i=0;i<arr.size();++i){
        nums[j++]=arr[i];
    }
}

void merge_sort(vector<int> &nums,int left,int right){
    if(left>=right) return;
    int mid=left + (right-left)/2;
    merge_sort(nums,left,mid);
    merge_sort(nums,mid+1,right);
    merge(nums,left,mid,right);
}


int main(){
    vector<int> nums={1,2,5,6,7,4,3,9,8};
    merge_sort(nums,0,nums.size()-1);
    for(auto i:nums) cout<<i<<" ";
    cout<<endl;
    exit(0);
}
```