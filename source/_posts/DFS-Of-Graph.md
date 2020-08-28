---
title: DFS Of Graph
date: 2020-05-15 22:22:42
tags: Data Structure
---
## Conception
Depth First Search, called DFS, which is one of graphics traversing or searching methods. The basic idea is exploreing as far as possible along each branch from any start node. DFS algorithm can be implementted recurrently, after a node is visited, it's visit flag should be setted as true. if the current node has no other un-traveled adjacent nodes, it should return, and find another un-traveled branch.

There are two types of grapics storage structures -- adjacent matrix storage and adjacency list storage. In this blog, we take adjacent matrix as the storage structure of graphics.

There is a simple implementation of DFS as follows:
## Code

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
            void traverse(int i){
                if(i<0||i>=m_vexnum) throw "index bound exceed";
                visit(i);
                for(int j=0;j<m_vexnum;++j){
                    if(m_arc[i][j]==1&&m_flags[j]==false) traverse(j);
                }
            }
            
            ////dfs
            void dfs(){
                for(int i=0;i<m_vexnum;++i){
                    if(m_flags[i]==false) traverse(i);
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
        g.dfs();
        return 0;
    }
