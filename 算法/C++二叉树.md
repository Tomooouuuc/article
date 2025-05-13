```c++
#include<bits/stdc++.h>
using namespace std;

struct Node{
	int num;
	Node *left;
	Node *right;
	Node(int val):num(val),left(nullptr),right(nullptr){}
};

class Tree{
	Node *head;
	void h_in_order(Node *node) {
		if (node == nullptr) return;
		h_in_order(node->left); 
		cout << node->num << " ";
		h_in_order(node->right);
	}
	void h_pre_order(Node *node) {
		if (node == nullptr) return;
		cout << node->num << " ";
		h_pre_order(node->left); 
		h_pre_order(node->right);
	}
	void h_post_order(Node *node) {
		if (node == nullptr) return;
		h_post_order(node->left); 
		h_post_order(node->right);
		cout << node->num << " ";
	}
public:
	Tree():head(nullptr){}
	Tree(vector<int> arr){
		head=new Node(arr[0]);
		queue<Node*> q;
		q.push(head);
		for(int i=0;i<arr.size()/2;i++){
			Node *tmp=q.front();
			q.pop();
			if(i*2+1<arr.size()){
				tmp->left=new Node(arr[i*2+1]);
				q.push(tmp->left);
			}
			if(i*2+2<arr.size()){
				tmp->right=new Node(arr[i*2+2]);
				q.push(tmp->right);
			}
		}
	}
	void order() {
		queue<Node*> q;
		q.push(head);
		while(!q.empty()){
			Node *tmp=q.front();
			cout<<tmp->num<<" ";
			q.pop();
			if(tmp->left){
				q.push(tmp->left);
			}
			if(tmp->right){
				q.push(tmp->right);
			}
		}
	}	
	void in_order(){
		h_in_order(head);
	}
	void pre_order(){
		h_pre_order(head);
	}
	void post_order(){
		h_post_order(head);
	}
};

int main(){
	vector<int> arr{1,2,3,4,5,6,7};
	Tree t=arr;
	cout<<endl;
	t.in_order();
	return 0;
}
```

