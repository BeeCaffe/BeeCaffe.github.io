---
title: BFS Of Graph
date: 2020-05-15 22:21:49
tags: Data Structure
---
## Conception
Breath First Search, called BFS, which is one of graphics traversing or searching methods. The basic idea is exploreing as borad as possible of one node from any start node. BFS algorithm can be implementted by a queue, like the level traverse of tree.First of all, we push a node into queue as the start tarverse node of graph, when queue is not empty. We pop each node in queue, access it and find all the neiborhood node of current node, then push them into queue, until queue is empty. after a node is visited, it's visit flag should be setted as true. 

There are two types of grapics storage structures -- adjacent matrix storage and adjacency list storage. In this blog, we take adjacent matrix as the storage structure of graphics.

There is a simple implementation of BFS as follows:
## **Code**
```cpp
#include<iostream>
#include<vector>
using namespace std;
////DFS
////using adjacent matrix storage
class Graph{
private:
    ////vertices 
    vector<string> m_vex;
    ////arcs
    vector<vector<int>> m_arcs;
    ////is visited flag
    vector<bool> m_flags;
    ////vertex number
    int m_vexnum;
    
public:

    //// constructor
    Graph(vector<string> vex){
        m_vex=vex;
        m_vexnum=vex.size();
        m_arcs=vector<vector<int>>(m_vexnum,vector<int>(m_vexnum,0));
        m_flags=vector<bool>(m_vexnum,false);
    }
    
    ////add edge
    void addEdge(int i,int j){
        if(i<0||i>=m_vexnum) throw "index bound exceed";
        if(j<0||j>=m_vexnum) throw "index bound exxceed";
        m_arcs[i][j]=1;
        m_arcs[j][i]=1;
    }
    
    void visit(int i){
        if(i<0||i>=m_vexnum) throw "index bound exceed";
        cout<<m_vex[i]<<" ";
    } 
    
    ////traverse
    void bfs(){
        for(int i=0;i<m_vexnum;++i){
            if(!m_flags[i]){
                queue<int> Q;
                Q.push(i);
                while(!Q.empty()){
                    for(int j=Q.size()-1;j>=0;--j){
                        int cur=Q.top();Q.pop();
                        visit(cur);
                        m_flags[cur]=1;
                        for(int k=0;k<m_vecnum;++k){
                         if(!m_flags[k]&&m_arcs[j][k]) Q.push(k);
                        }
                    }
                }
            }
        }
    }
};

int main(){
vector<string> vex={"node1","node2","node3","node4","node5","node6"};
Graph g(vex);
g.addEdge(0,1);
g.addEdge(0,4);
g.addEdge(0,2);
g.addEdge(2,3);
g.addEdge(4,5);
g.bfs();
return 0;
}
```