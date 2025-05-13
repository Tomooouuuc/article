```c++
#include<bits/stdc++.h>
using namespace std;

class Graph{
	int num,edge_num;
	int *matrix;
public:
	Graph(int x):num(x),edge_num(0){
		matrix=new int[x*x];
		fill(matrix,matrix + x*x,0);
	}
	void add_undirect_edge(int l,int r){
		if(matrix[(l-1)*num+r-1]&&matrix[(r-1)*num+l-1]){
			return;
		}
		matrix[(l-1)*num+r-1]=1;
		matrix[(r-1)*num+l-1]=1;
		edge_num++;
	}
	void add_direct_edge(int l,int r){
		if(matrix[(l-1)*num+r-1]){
			return;
		}
		matrix[(l-1)*num+r-1]=1;
		edge_num++;
	}
	int get_edge_num(){
		return edge_num;
	}
	int get_num(){
		return num;
	}
	void print(){
		for(int i=0;i<num;i++){
			for(int j=0;j<num;j++){
				cout<<matrix[i*num+j]<<" ";
			}
			cout<<endl;
		}
	}
};
```

