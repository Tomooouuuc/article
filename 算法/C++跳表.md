```c++
#include<bits/stdc++.h>
using namespace std;

struct Node{
	int val,level;
	Node **next;
	Node(int val,int level):val(val),level(level){
		next=new Node*[level]();
	}
	~Node(){
		delete[] next;
	}
};

class Skiplist{
	int max_level,skip_level,cnt;
	Node *head;
	mt19937 seed;
	double p;
	int random_level(){
		int level=1;
		uniform_real_distribution<double> gen(0.0,1.0);
		while(gen(seed)<p&&level<max_level){
			++level;
		}
		return level;
	}
	int get_skip_level(){
		for(int i=max_level-1;i>=0;i--){
			if(head->next[i]){
				return i+1;
			}
		}
		return 0;
	}
public:
	Skiplist(int max_level=16,double p=0.5):max_level(max_level),p(p),skip_level(0),cnt(0),seed(time(nullptr)){
		head=new Node(INT_MIN,max_level);
	}
	~Skiplist(){
		while(head){
			Node *del=head;
			head=head->next[0];
			delete del;
		}
	}
	void insert_node(int val,int level){
//		int level=random_level();
		Node *node=new Node(val,level);
		Node *cur=head;
		for(int i=level-1;i>=0;i--){
			while(cur->next[i]&&cur->next[i]->val<=val){
				cur=cur->next[i];
			}
			node->next[i]=cur->next[i];
			cur->next[i]=node;
		}
		++cnt;
		skip_level=max(skip_level,level);
	}
	void delete_node(int val){
		if(cnt==0){
			return;
		}
		Node *cur=head,*del;
		for(int i=skip_level-1;i>=0;i--){
			while(cur->next[i]&&cur->next[i]->val<val){
				cur=cur->next[i];
			}
			if(cur->next[i]&&cur->next[i]->val==val){
				del=cur->next[i];
				cur->next[i]=cur->next[i]->next[i];
			}
		}
		delete del;
		--cnt;
		skip_level=get_skip_level();
	}
	void delete_nodes(int val){
		if(cnt==0){
			return;
		}
		Node *cur=head;
		for(int i=skip_level-1;i>=0;i--){
			while(cur->next[i]&&cur->next[i]->val<val){
				cur=cur->next[i];
			}
			if(cur->next[i]&&cur->next[i]->val==val){
				Node *del,*tmp_del=cur->next[i];
				while(tmp_del&&tmp_del->val==val){
					del=tmp_del;
					tmp_del=tmp_del->next[i];
					if(i==0){
						delete del;
						--cnt;
					}
				}
				cur->next[i]=tmp_del;
			}
		}
		skip_level=get_skip_level();
	}
	void delete_idx(int index){
		if(index<0||index>=cnt){
			return;
		}
		Node *del=head,*cur=head;
		for(int i=0;i<=index;i++){
			del=del->next[0];
		}
		for(int i=del->level-1;i>=0;i--){
			while(cur->next[i]&&cur->next[i]!=del){
				cur=cur->next[i];
			}
			if(cur->next[i]&&cur->next[i]==del){
				cur->next[i]=del->next[i];
			}
		}
		delete del;
		--cnt;
		skip_level=get_skip_level();
	}
	void pop_back(){
		if(cnt==0){
			return;
		}
		Node *cur=head;
		for(int i=skip_level-1;i>=0;i--){
			while(cur->next[i]){
				cur=cur->next[i];
			}
		}
		Node *del=cur;
		cur=head;
		for(int i=skip_level-1;i>=0;i--){
			while(cur->next[i]&&cur->next[i]!=del){
				cur=cur->next[i];
			}
			if(cur->next[i]&&cur->next[i]==del){
				cur->next[i]=del->next[i];
			}
		}
		delete del;
		--cnt;
		skip_level=get_skip_level();
	}
	void pop_front(){
		if(cnt==0){
			return;
		}
		Node *cur=head;
		Node *del=head->next[0];
		for(int i=del->level-1;i>=0;i--){
			cur->next[i]=cur->next[i]->next[i];
		}
		delete del;
		--cnt;
		skip_level=get_skip_level();
	}
	int back(){
		if(cnt==0){
			throw invalid_argument("跳表为空");
		}
		Node *cur=head;
		for(int i=skip_level-1;i>=0;i--){
			while(cur->next[i]){
				cur=cur->next[i];
			}
		}
		return cur->val;
	}
	bool exists(int val){
		if(cnt==0){
			return false;
		}
		Node *cur=head;
		for(int i=skip_level-1;i>=0;i--){
			while(cur->next[i]&&cur->next[i]->val<val){
				cur=cur->next[i];
			}
			if(cur->next[i]&&cur->next[i]->val==val){
				return true;
			}
		}
		return false;
	}
	bool empty(){
		return cnt==0;
	}
	int get(int index);
	int size(){
		return cnt;
	}
	int skiplist_level(){
		return skip_level;
	}
	friend ostream& operator<<(ostream& os,const Skiplist& s){
		os<<"====================="<<endl;
		for(int i=s.skip_level-1;i>=0;i--){
			Node *cur=s.head->next[i];
			os<<"level:"<<i<<" ";
			while(cur){
				os<<cur->val<<"-->";
				cur=cur->next[i];
			}
			os<<"NULL"<<endl;
		}
		os<<"====================="<<endl;
		return os;
	}
};

int main(){
	Skiplist s;
	s.insert_node(4,4);
	s.insert_node(4,2);
	s.insert_node(4,1);
	s.insert_node(4,1);
	s.insert_node(4,4);
	cout<<s<<endl;
	s.delete_idx(3);
	cout<<s<<endl;
}

```

