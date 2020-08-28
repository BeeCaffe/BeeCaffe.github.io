---
title: 17.05 Letters and Numbers
date: 2020-05-30 10:18:49
tags: Cracking the Coding Interview
---
**prefix**: the prefix in an array is a subarray whose length is less than or equal to the length of the array from left to right;

**prefix sum** : the sum of the prefix subarray of an array;

![12.JPG](/images/12.jpg)

The general idea is as follows:

* since there are only **letter** and **number** in the array, we can write **letter** as -1 and **number** as 1 to simplify the problem.

* the problem requires the same number of characters and Numbers in the subarray, so we can solve the problem by solving the prefix and. The solution can be                                                       divided into two cases:

  * 1. The values of two prefixes and are the same, for example: prefix[I]==prefix[j], indicating that there are the same number of extra digits or characters between [0, I] and [0,j]. Then if we use the interval [0,j] minus the interval [0, I], the number of digits and characters between [I,j] must be the same, so [(I,j] may be a set of solutions (** note that the interval is left open and right closed **);

  * 2. Where the prefix[I]=0, for example: prefix[I]=0, the number of Numbers and characters between [0, I] are the same, so [0,j] may be a set of solutions;

* since you need to find the longest subarray and start with the smallest subarray on the left at the same length, you need to find the largest subarray with the ** prefix and the same subarray with the ** and ** prefixes of 0**.

So let's convert the array to -1 and 1, and figure out all the prefixes and the values of the array;

![10.JPG](/images/10.jpg)

Then, we walk through the prefix and array once, and use a mapping (dictionary) inordered_map to record the value of the prefix and its leftmost subscript;

Every time we find the prefix and/or the prefix and/or the prefix and/or the sum in the previous example, we update the answer according to the rule.

![11.JPG](/images/11.jpg)

As shown in the figure above, the answer to the example given in the question is a subarray from the first prefix and the position of 1 to the last prefix and the position of 1.

### Time/Space

Time: O (n)

Space: O (n)

### Code

````cpp
class Solution {
public:
    vector<string> findLongestSubarray(vector<string>& array) {
        int n=array.size();
        vector<int> prefix(n,0);
        unordered_map<int,int> M;   //key,left_index
        int left=0,right=-1;
        for(int i=0;i<n;++i){
            char ch=array[i][0];
            if(ch>='A' && ch<='z') prefix[i]=-1;
            else prefix[i]=1;
        }

        for(int i=1;i<n;++i){
            prefix[i]+=prefix[i-1];
        }

        for(int i=0;i<n;++i){
            auto it=M.find(prefix[i]);
            if(prefix[i]==0){
                if(right-left+1 < i+1){
                    right=i;left=0;
                }
                continue;
            }
            if(it==M.end()) M[prefix[i]]=i;
            else {
                if(right-left+1 < i-it->second){
                    right=i;left=it->second+1;
                }
            }
        }
        vector<string> ans;
        for(int i=left;i<=right;++i) ans.push_back(array[i]);
        return ans;
    }
};

```
