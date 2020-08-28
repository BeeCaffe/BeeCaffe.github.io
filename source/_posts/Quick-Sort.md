---
title: Quick Sort
date: 2020-05-26 10:55:42
tags: Introduce To Algorithm
---
###  1. Quick Sort
For each time, we ensure one nuhexo     mbers location in the whole array, which we called **key value**, then, we put the numbers which smaller than key value into the left of it, and the bigger number to the right of key value.

After that, we recurrently ensure the left numbers order and the right numbers order until the whole array are sorted.The following picture shows a simple example of quickly sort.

![quick_sort.jpg](https://upload-images.jianshu.io/upload_images/13348038-4b3fb69926be987e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


At the start of this program, **low=0** and **high=5**, we choose the first number of the range of [low,high], and use a variable **key=arr[low]=7** to record this number. Then we find that arr[high]=2 < 7, which means we must change the high index number to key value left. As a result for we have recorded the arr[low] in** key**, we set arr[low]=arr[high].

Then, we need to find the first bigger than key number in the left of the key. As we can see 8 is bigger than 7, so we stop and set arr[high]=arr[low].

In a word, we try to find the number smaller than key in the right and swap it into the left, by contrast, we search the value bigger than key in the left and swap it into right, until high==low, we set arr[high]=arr[low]=key. Then we do the same thing in the left subrange and right subrange.

The source code of this algorithm is shown in following.

### 2. Code
```cpp
#include<iostream>
#include<vector>
using namespace std;
int get_index(vector<int>& nums,int low,int high){
    int key=nums[low];
    //when low==high, loop is over
    while(low<high){
        //we must find the first number of smaller than key in the right of the key
        while(low<high&&nums[high]>=key) --high;
        nums[low]=nums[high];
        //we must find the first number of bigger than key in the left
        while(low<high&&nums[low]<=key) ++low;
        nums[high]=nums[low];
    }
    //there is key place
    nums[low]=key;
    return low;
}

void quick_sort(vector<int> &nums,int low,int high){
    if(low<high){
        int index=get_index(nums,low,high);
        quick_sort(nums,low,index-1);
        quick_sort(numsd,index+1,high);
    }
}
int main(){
    int n;
    cin>>n;
    vector<int> nums;
    for(int i=0;i<n;++i){
        int val;
        cin>>val;
        nums.push_back(val);
    }
    quick_sort(nums,0,nums.size()-1);
    for(auto i:nums) cout<<i<<" ";
    cout<<endl;
    return 0;
}
```    
### 3. Complexity

**Time complexity**:For each time we must change value n times, and the stack deep is logn, so the time complexity is **O****(nlogn)**. In the worst situation, the stack deepth changed into n, the time complexity would be O(n2).

**Space Complexity**: O(1)
