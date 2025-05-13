```c++
#include<bits/stdc++.h>
using namespace std;

struct Node{
	int num;
	Node *next;
};

class List{
	int size;
	Node *head;
public:
	List():size(0),head(nullptr){}
	List(initializer_list<int> lst):size(0),head(nullptr){
		for(int x:lst){
			insert_tail(x);
		}
	}
	List(vector<int> arr):size(0),head(nullptr){
		for(int x:arr){
			insert_tail(x);
		}
	}
	void insert_head(int x){
		Node *newNode=new Node{x,head};
		head=newNode;
		size++;
	}
	void insert_tail(int x){
		Node *newNode=new Node{x,nullptr};
		if(!head){
			head=newNode;
			size++;
			return;
		}
		Node *pre=head;
		while(pre->next){
			pre=pre->next;
		}
		pre->next=newNode;
		size++;
	}
	void insert_node(int x,int n){
		if(n<1||n>size+1){
			throw invalid_argument("插入位置越界");
		}
		if(n==1){
			insert_head(x);
			return;
		}
		Node *newNode=new Node{x,nullptr};
		Node *pre=head;
		for(int i=1;i<n-1;i++){
			pre=pre->next;
		}
		newNode->next=pre->next;
		pre->next=newNode;
		size++;
	}
	void delete_node(int n){
		if(n<1||n>size){
			throw std::invalid_argument("删除位置越界");
		}
		if(n==1){
			Node *del=head;
			head=head->next;
			delete del;
			size--;
			return;
		}
		Node *pre=head;
		for(int i=1;i<n-1;i++){
			pre=pre->next;
		}
		Node *del=pre->next;
		pre->next=pre->next->next;
		delete del;
		size--;
	}
	Node& get_node(int n){
		Node *tmp=head;
		for(int i=1;i<n;i++){
			tmp=tmp->next;
		}
		return *tmp;
	}
	int get_size(){
		return size;
	}
	void print(){
		Node *tmp=head;
		while(tmp){
			cout<<tmp->num<<" ";
			tmp=tmp->next;
		}
		cout<<endl;
	}
};

int main(){
	vector<int> arr{1,2,3,4,5,6,7};
	List l1=arr;
	l1.print();
	cout<<l1.get_size()<<endl;
	return 0;
}
```

