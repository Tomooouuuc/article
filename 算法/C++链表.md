```c++
#include<bits/stdc++.h>
using namespace std;

struct Node{
	int num;
	Node *next;
};

class List{
	int cnt;
	Node *head;
public:
	List():cnt(0),head(nullptr){}
	List(initializer_list<int> lst):cnt(0),head(nullptr){
		for(int x:lst){
			insert_tail(x);
		}
	}
	List(vector<int> arr):cnt(0),head(nullptr){
		for(int x:arr){
			insert_tail(x);
		}
	}
	~List(){
		Node *cur=head;
		while(cur){
			Node *tmp=cur;
			cur=cur->next;
			delete tmp;
		}
	}
	void insert_head(int x){
		Node *newNode=new Node{x,head};
		head=newNode;
		++cnt;
	}
	void insert_tail(int x){
		Node *newNode=new Node{x,nullptr};
		if(!head){
			head=newNode;
			++cnt;
			return;
		}
		Node *pre=head;
		while(pre->next){
			pre=pre->next;
		}
		pre->next=newNode;
		++cnt;
	}
	void insert_node(int x,int index){
		if(index<1||index>cnt+1){
			throw invalid_argument("插入位置越界");
		}
		if(index==1){
			insert_head(x);
			return;
		}
		Node *newNode=new Node{x,nullptr};
		Node *pre=head;
		for(int i=1;i<index-1;i++){
			pre=pre->next;
		}
		newNode->next=pre->next;
		pre->next=newNode;
		++cnt;
	}
	void delete_node(int index){
		if(index<1||index>cnt){
			throw std::invalid_argument("删除位置越界");
		}
		if(index==1){
			Node *del=head;
			head=head->next;
			delete del;
			--cnt;
			return;
		}
		Node *pre=head;
		for(int i=1;i<index-1;i++){
			pre=pre->next;
		}
		Node *del=pre->next;
		pre->next=pre->next->next;
		delete del;
		--cnt;
	}
	int get(int index){
		if(index<1||index>cnt){
			throw std::invalid_argument("位置越界");
		}
		Node *tmp=head;
		for(int i=1;i<index;i++){
			tmp=tmp->next;
		}
		return tmp->num;
	}
	int size(){
		return cnt;
	}
	friend ostream& operator<<(ostream& os,const List& lst){
		Node *tmp=lst.head;
		while(tmp){
			os<<tmp->num<<"-->";
			tmp=tmp->next;
		}
		os<<"NULL"<<endl;
		return os;
	}
};

int main(){
	List l1;
	
	return 0;
}

```

