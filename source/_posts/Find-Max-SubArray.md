---
title: Find Max SubArray
date: 2020-05-15 11:57:34
tags:
---
This book introduces a algorithm to slove max sub-array problem, which bases on divided and conqure idea.Supposing we need to seach the maximum sub-array of array A[low,right]. Utilizing divided and conqure idea means we require to split array A[left ... right] to two part - A[left,mid] and A[mid+1,right].For any continous array, the sub array must locate in three position as following:
* located in sub-array A[left,mid], left part.
* located in sub-array A[mid+1,right], right part.
* crossed left and right part.
Thus, we can recurrently slove those three sub-problem, and combine those answer, then we can get the result.More detail about this alogrithm refers to the book and the following code.
## code
```
/**
 *@BeeCaffe, 2020.5.11
 *The implementation of "find max subarray" algorithm, which is a problem of <<introduce to algorithm>>, chapter 4.
 *The main idea of this problem is divide and conqure.
 **/
#include<vector>
#include<iostream>
#include<tuple>
#include<limits.h>
using namespace std;
//find the corss max subarray,the max array of nums[left,right] must start at mid and expand to left and right two direction.
tuple<int,int,int> findCrossMax(vector<int> &nums,int left,int mid,int right){
    int left_max=INT_MIN;
    int left_idx=left;
    int sum=0;
    for(int i=mid;i>=left;--i){
        sum+=nums[i];
        if(sum>left_max){
            left_max=sum;
            left_idx=i;
        }
    }

    int right_max=INT_MIN;
    int right_idx=0;
    sum=0;
    for(int i=mid+1;i<=right;++i){
        sum+=nums[i];
        if(sum>right_max){
            right_max=sum;
            right_idx=i;
        }
    }
    return {left_idx,right_idx,left_max+right_max};
}

//find max subarray recurently
tuple<int,int,int> findMax(vector<int> &nums,int left,int right){
    if(left==right) return {left,right,nums[left]};
    int mid=left+(right-left)/2;
    tuple<int,int,int> left_max_t=findMax(nums,left,mid);
    tuple<int,int,int> right_max_t=findMax(nums,mid+1,right);
    tuple<int,int,int> corss_max_t=findCrossMax(nums,left,mid,right);
    int left_low=get<0>(left_max_t),left_high=get<1>(left_max_t),left_max=get<2>(left_max_t);
    int right_low=get<0>(right_max_t),right_high=get<1>(right_max_t),right_max=get<2>(right_max_t);
    int cross_low=get<0>(corss_max_t),cross_high=get<1>(corss_max_t),cross_max=get<2>(corss_max_t);
    if(left_max>right_max && left_max>cross_max) return {left_low,left_high,left_max};
    else if(right_max>left_max && right_max>cross_max) return {right_low,right_high,right_max};
    else return {cross_low,cross_high,cross_max};
}

vector<int> maxSubarray(vector<int> &nums){
    int n=nums.size();
    tuple<int,int,int> ans_t=findMax(nums,0,n-1);
    int left=get<0>(ans_t);
    int right=get<1>(ans_t);
    return vector<int>(nums.begin()+left,nums.begin()+right+1);
}

int main(int argc,char** argv){
    vector<int> nums={13,-3,-25,20,-3,-16,-23,18,20,-7,12,-5,-22,15,-4,7};
    vector<int> ans=maxSubarray(nums);
    for(auto i:ans) cout<<i<<" ";
    cout<<endl;
}
```