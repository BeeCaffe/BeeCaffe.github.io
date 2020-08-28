---
title: Dijkstra
date: 2020-05-15 12:20:11
tags: Data Structure
---
# Conception
Dijkstra is a single source shortest path algorithm, which is able to find the shorest path of two node in a graph in O(n2) time complexity. The basic idea is to maintain two set. One set, called visited set  , contains the node of which the shortest path form source node to this is found. The nodes in another set, called unvisited set, are not found. For each time, we try find the shorest node in set two and update the distance of set 1 nodes until reaching the distination.
# Graph
![](images/1.png)
# Code
```cpp
#include<iostream>
#include<vector>
#include<string>
#include<assert.h>
#include<limits.h>
#define INF 99999
using namespace std;
class Graph{
public:
    Graph(vector<string>s):m_vex(s),m_arcs(s.size(),vector<int>(s.size(),INF)),m_falgs(s.size()),m_vexNum(s.size()){
    }

   void addEdge(int i,int j,int weight){
        if(i>m_vexNum||i<0) assert(false);
        if(j>m_vexNum||j<0) assert(false);
        if(i==j){
            m_arcs[i][j]=0;
            m_arcs[j][i]=0;
            return;
        }
        m_arcs[i][j]=weight;
        m_arcs[j][i]=weight;
    }

    void dijkstra(int i){
        if(i<0||i>m_vexNum) assert(false);
        /* initializes shorest paths from i to j*/
        if(m_paths.empty()){
            m_paths=vector<vector<int>>(m_vexNum,vector<int>(m_vexNum,0));
            for(int i=0;i<m_vexNum;++i){
                for(int j=0;j<m_vexNum;++j){
                    m_paths[i][j]=i;        //by default we regrad the shortest path is i->j directly
                }
            }
        }

        if(m_shorts.empty()) m_shorts=m_arcs;

        vector<bool> visited(m_vexNum,0);   //record visited nodes and unvisited nodes.

        int start=i;                        //i is start node
        visited[start]=1;
        /*except for node i, we must find m_vexNum-1 nodes'shortest path in total*/
        for(int count=0;count<m_vexNum-1;++count){
            int min_dist=INT_MAX;
            int cur=i;
            //find the smallest path of start->j, where j belongs to unvisited set.
            for(int j=0;j<m_vexNum;++j){
                if(!visited[j]&&m_shorts[start][j]<min_dist){
                    min_dist=m_shorts[start][j];
                    cur=j;
                }
            }

            //update the shortest path in visited set.
            visited[cur]=1;
            for(int j=0;j<m_vexNum;++j){
                if(!visited[j]&&m_shorts[start][j]>m_shorts[start][cur]+m_shorts[cur][j]){
                    m_shorts[start][j]=m_shorts[start][cur]+m_shorts[cur][j];
                    m_paths[start][j]=cur;
                }
            }
        }
    }

    void dijkstraAll(){
        for(int i=0;i<m_vexNum;++i){
            dijkstra(i);
        }
    }

    void showPath(int i,int j){
        if(i<0||i>m_vexNum) assert(false);
        if(j<0||j>m_vexNum) assert(false);
        if(m_paths.size()!=m_vexNum||m_paths[0].size()!=m_vexNum){
            cout<<"[error] have not get shortest path";
            assert(false);
        }

        if(m_paths[i][j]==i){
            cout<<m_vex[i]<<"->"<<m_vex[j];
        }else{
            cout<<m_vex[m_paths[i][j]]<<"->";
            showPath(m_paths[i][j],j);
        }
    }

    void showAllPaths(){
        for(int i=0;i<m_vexNum;++i){
            for(int j=i+1;j<m_vexNum;++j){
                cout<<m_vex[i]<<"->"<<m_vex[j]<<" "<<m_shorts[i][j]<<" shortest path is: ";
                showPath(i,j);
                cout<<endl;
            }
        }
    }

private:
    vector<vector<int>> m_arcs;
    vector<string> m_vex;
    vector<bool> m_falgs;
    int m_vexNum;
    vector<vector<int>> m_paths;
    vector<vector<int>> m_shorts;
};

int main(){
    vector<string> nodes={"A","B","C","D","E","F","G"};
    Graph g(nodes);
    g.addEdge(0,1,12);
    g.addEdge(0,5,16);
    g.addEdge(0,6,14);
    g.addEdge(1,5,7);
    g.addEdge(5,6,9);
    g.addEdge(1,2,10);
    g.addEdge(5,2,6);
    g.addEdge(5,4,2);
    g.addEdge(6,4,8);
    g.addEdge(2,4,5);
    g.addEdge(2,3,3);
    g.addEdge(4,3,4);
    g.dijkstraAll();
    g.showAllPaths();
    return 0;
}
```

result

![image.png](https://upload-images.jianshu.io/upload_images/13348038-6d01f1034ce622f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
