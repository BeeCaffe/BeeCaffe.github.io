---
title: Heap Sort
date: 2020-05-24 10:39:55
tags: Introduce To Algorithm
---
Actually, Heap is an array which is a complete binary tree in logical, excepting for the lowest layer all the layers are full binary tree. There are two types of heap, they are top heap and bottom heap. In heap sort algorithm we utilize top heap, and bottom heap is used to construct priority queue in gennerally. 

Suppose heap node i, it parent is i-1/2, we can define top heap and bottom heap as follows: 
* **Top Heap**: heap[(i-1)/2]>=heap[i]
* **Bottom Heap**: heap[(i-1)/2]<=heap[i]

## Adjust Heap
This place I use top heap as an example, top heap must satisify the priciple of heap[(i-1)/2]>=heap[i]. Therefore, we must make sure all the elements in heap satisfiy this relu. 
We know that, the child of node i are 2*i+1 and 2*i+2, that is:
* **Left Child**: 2*i+1
* **Right Child**: 2*i+2
We must make sure every elements in heap satisfy the font relue. Therefore, if heap[i]<heap[left_child] or heap[i]<heap[right_child], we should swap heap[i] with min(heap[left_child],heap[right_child]). Then, taking this operation recurrently. The code of adjust heap is as following:
* **code**:
    ```cpp
    void adjustHeap(vector<int> &heap,int i,int n){
        if(i>n) return;
        int left=2*i+1,right=2*i+2;
        int minNum=heap[i],minIdx=i;
        if(left<n && minNum>heap[left]){
            minNum=heap[left];
            minIdx=left;
        }
        if(right<n && minNum>heap[right]){
            minNum=heap[right];
            minIdx=right;
        }
        if(minIdx!=i){
            int tmp=heap[i];
            heap[i]=minNum;
            heap[minIdx]=tmp;
            adjustHeap(heap,minIdx,n);
        }
    }
    ```
generally, for a heap the time complexity of adjusting a heap is O(logn).

## Bild Heap
A heap can be bulit by a bottom-up process, where is to tranfer a normal array with length n to a heap. 

Why we choose bottom-up process rather than top-down process to build a heap?

If we choose top-down process to build a heap, the strat point must be the root node. When strarting to adjust the heap form root, the sub-tree of root are not satisty the heap rules, so, the build heap process may meet some problem. By constrast, if we choose bottom-up process to build a heap, before adjusting the top node, their children has already been adjusted. Therefore, the code of build heap is:

* **code**:
    ```cpp
    void buildHeap(vector<int> &heap){
        int n=heap.size();
        for(int i=n/2-1;i>=0;--i) adjustHeap(heap,i,n);
    }
    ```
The time complexity of build heap is O(n), more detail about the time complexity please refer to book.
## Heap Sort
As a result for the root is alway the maximum elements in the heap. Thus, for each time, we can get the maximum value, and swap it with the last value in the heap. Then, adjusting the heap again to get the next maximum value. After n steps, all the elements in the heap is ordered. The code is:
* **code**:
    ```cpp
    void swap(int &a,int &b){
        int tmp=a;
        a=b;
        b=tmp;
    }

    void heapSort(vector<int> &nums){
        int n=nums.size();
        buildHeap(nums);
        for(int i=n-1;i>=0;++i){
            swap(heap[0],heap[i]);
            adjustHeap(heap,0,i);        
        }
    }
    ```
## Code
The complete code of heap sort is as following:
    
    ```cpp
    #include<vector>
    using namespace std;
    void adjustHeap(vector<int> &heap,int i,int n){
        if(i>n) return;
        int left=2*i+1,right=2*i+2;
        int minNum=heap[i],minIdx=i;
        if(left<n && minNum>heap[left]){
            minNum=heap[left];
            minIdx=left;
        }
        if(right<n && minNum>heap[right]){
            minNum=heap[right];
            minIdx=right;
        }
        if(minIdx!=i){
            int tmp=heap[i];
            heap[i]=minNum;
            heap[minIdx]=tmp;
            adjustHeap(heap,minIdx,n);
        }
    }
    void buildHeap(vector<int> &heap){
        int n=heap.size();
        for(int i=n/2-1;i>=0;--i) adjustHeap(heap,i,n);
    }

    void swap(int &a,int &b){
        int tmp=a;
        a=b;
        b=tmp;
    }

    void heapSort(vector<int> &nums){
        int n=nums.size();
        buildHeap(nums);
        for(int i=n-1;i>=0;++i){
            swap(heap[0],heap[i]);
            adjustHeap(heap,0,i);        
        }
    }

    int main(){
        vector<int> nums={1,2,3,1,9,4,5,3};
        heapSort(nums);
    }
    ```
