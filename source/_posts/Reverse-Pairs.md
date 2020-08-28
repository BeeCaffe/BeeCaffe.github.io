---
title: Reverse Pairs
date: 2020-05-15 12:08:22
tags: Introduce To Algorithm
---
## Code
```
#include<iostream>
#include<vector>
using namespace std;
int ans=0;
void merge(vector<int> &nums,int left,int mid,int right){
    int p1=left,p2=mid+1;
    vector<int> tmp;
    while(p1<=mid && p2<=right){
        if(nums[p1]<=nums[p2]){
            tmp.push_back(nums[p1++]);
        }else{
            tmp.push_back(nums[p2++]);
            ans+=mid-p1+1;
        }
    }
    while(p1<=mid) tmp.push_back(nums[p1++]);
    while(p2<=right) tmp.push_back(nums[p2++]);
    int j=0;
    for(int i=left;i<=right;++i){
        nums[i]=tmp[j++];
    }
}
void merge_sort(vector<int> &nums,int left,int right){
    if(left==right) return;
    int mid=left+(right-left)/2;
    merge_sort(nums,left,mid);
    merge_sort(nums,mid+1,right);
    merge(nums,left,mid,right);
}
int reversePairs(vector<int> &nums){
    int n=nums.size();
    if(n<2) return 0;
    merge_sort(nums,0,n-1);
    return ans;
}
int main(){
    vector<int> nums={2,1,4,5,3,7,9,8};
    cout<<reversePairs(nums)<<endl;
    exit(0);
}
```