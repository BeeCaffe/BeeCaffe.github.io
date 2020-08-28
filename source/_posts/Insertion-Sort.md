---
title: Insertion Sort
date: 2020-05-15 12:13:11
tags: Introduce To Algorithm
---
Insertion  sort is an effective algorithm for a small amount of elements, the working pricinple of this algorithm like sort the playing cards. Supposing we hold cards in our left hands, and then we use the right hands to choose a  card to find the right location in the cards for every card. This processing we called insertion sort, the actually steps of insertion sort is as following:
(1) traversing each elements from 1 to n-1, suppose is j and set key=nums[j];
(2) traversing elements from [0,j] and finding the correct position of insertion, suppose is i;
(3) if nums[i]<key, we should move nums[i] backward;
 
## code
```
#include<iostream>
#include<vector>
using namespace std;
//ascending
void insert_sort(vector<int> &nums){
    int i,j,key;
    for(j=1;j<nums.size();++j){
        key=nums[j];
        i=j-1;
        while(i>=0 && nums[i]>key){
            nums[i+1] = nums[i];
            --i;
        }
        nums[i+1]=key;
    }
}
//descending
void insert_sort2(vector<int> &nums){
    int i,j,key;
    for(i=1;i<nums.size();++i){
        key=nums[i];
        j=i-1;
        while(j>=0 && nums[j]<key){
            nums[j+1] = nums[j];
            --j;
        }
        nums[j+1]=key;
    }
}

int main(int argc,char** argv){
    vector<int> nums={2,1,3,5,3,7,9,8};
    insert_sort(nums);
    insert_sort2(nums);
    for(auto i:nums) cout<<i<<" ";
    cout<<endl;
    return 0;
}
```