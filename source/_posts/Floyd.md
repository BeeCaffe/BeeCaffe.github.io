---
title: Floyd
date: 2020-05-15 22:20:49
tags: Data Structure
---
## Conception
The basic idea of floyd algorithm is very simple, which is based on dynamic programming. If we are going to get the shortest path between vertex i and vertex j, we can get a vertex k as the transfer station, if the path i->k->j is shortter than i->j, update the shortest path of i->j as i->k->j. Meanwhile, we require another matrix to record the path, the path[i][j] is initialized as path[i][j]=i, which means, the shortest path is from i to j. And, if we find a shortter transfer station k, then the path[i][j]=path[k][j].
## graph
![image.png](https://upload-images.jianshu.io/upload_images/13348038-94dd3a728fb581f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Code
```cpp
#include<iostream>
#include<vector>
#include<string>
#include<assert.h>
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

    void floyd(){
        vector<vector<int>> paths(m_vexNum,vector<int>(m_vexNum,0));
        vector<vector<int>> shortPaths(m_arcs);
        for(int i=0;i<m_vexNum;++i){
            for(int j=0;j<m_vexNum;++j){
                paths[i][j]=i;
            }
        }

        for(int k=0;k<m_vexNum;++k){
            for(int i=0;i<m_vexNum;++i){
                for(int j=0;j<m_vexNum;++j){
                    if(shortPaths[i][j]>shortPaths[i][k]+shortPaths[k][j]){
                        shortPaths[i][j]=shortPaths[i][k]+shortPaths[k][j];
                        paths[i][j]=paths[k][j];
                    }
                }
            }
        }
        m_paths=paths;
        m_shorts=shortPaths;
    }

    void showPath(int i,int j){
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
    g.floyd();
    g.showAllPaths();
    return 0;
}
```
result:

![image.png](https://upload-images.jianshu.io/upload_images/13348038-35ea8f98e47273d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


