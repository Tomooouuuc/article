```c++
#include<bits/stdc++.h>
using namespace std;

struct Node {
	int val, level;
	Node **next;
	Node() = default;
	Node(int val, int level): val(val), level(level) {
		next = new Node*[level]();
	}
	~Node(){
		delete[] next;
	}
};

class SkipList {
	int max_level,skip_level,size;
	double p;
	Node *head;
	mt19937 seed;
	int random_int() {
		int level = 1;
		uniform_real_distribution<double> gen(0.0, 1.0);
		while (gen(seed) < p) {
			++level;
		}
		return min(level, max_level);
	}
public:
	SkipList(int max_level=16,double p=0.5): max_level(max_level),p(p),skip_level(0),size(0), seed(static_cast<unsigned>(time(nullptr))) {
		head = new Node(INT_MIN, max_level);
	}
	~SkipList(){
		Node *cur=head->next[0];
		while(cur){
			Node *tmp=cur;
			cur=cur->next[0];
			delete tmp;
		}
		delete head;
	}
	void insert_node(int val) {
		int level = random_int();
		Node *newNode = new Node(val, level);
		Node *cur = head;
		for (int i = level - 1; i >= 0; i--) {
			while (cur->next[i] && cur->next[i]->val <= val) {
				cur = cur->next[i];
			}
			newNode->next[i] = cur->next[i];
			cur->next[i] = newNode;
		}
		skip_level=max(skip_level,level);
		size++;
	}
	void delete_node(int val) {
		Node *cur = head;
		Node *del;
		for (int i = skip_level - 1; i >= 0; i--) {
			while (cur->next[i] && cur->next[i]->val < val) {
				cur=cur->next[i];
			}
			if(cur->next[i]&&cur->next[i]->val==val){
				Node *tmp_del=cur->next[i];
				while(tmp_del&&tmp_del->val==val){
					del=tmp_del;
					tmp_del=tmp_del->next[i];
					if(i==0){
						delete del;
						size--;
					}
				}
				cur->next[i]=tmp_del;
			}
		}
	}
	bool pop_front(){
		if(size==0){
			return false;
		}
		Node *cur=head;
		Node *del=head->next[0];
		for(int i=del->level-1;i>=0;i--){
			cur->next[i]=cur->next[i]->next[i];
		}
		delete del;
		size--;
		return true;
	}
	bool pop_back(){
		if(size==0){
			return false;
		}
		Node *cur=head;
		Node *del;
		for(int i=skip_level-1;i>=0;i--){
			while (cur->next[i] && cur->next[i]->next[i]) {
				cur=cur->next[i];
			}
			if(cur->next[i]){
				if(i==0){
					del=cur->next[i];
				}
				cur->next[i]=cur->next[i]->next[i];
			}
		}
		delete del;
		size--;
		return true;
	}
	bool search_node(int val){
		Node *cur=head;
		for(int i=skip_level-1;i>=0;i--){
			while(cur->next[i]&&cur->next[i]->val<val){
				cur=cur->next[i];
			}
		}
		if(cur->next[0]&&cur->next[0]->val==val){
			return true;
		}else{
			return false;
		}
	}
	void print() {
		cout << "====================" << endl;
		Node *cur = head->next[0];
		while (cur) {
			for (int i = 0; i < cur->level; i++) {
				cout << cur->val << " ";
			}
			cout << endl;
			cur = cur->next[0];
		}
		cout << "====================" << endl;
	}
	int get_size(){
		return size;
	}
	int get_level(){
		return skip_level;
	}
};

int main() {
	SkipList *s = new SkipList(16);
	s->insert_node(3);
	s->insert_node(5);
	s->insert_node(6);
	s->insert_node(15);
	s->insert_node(63);
	s->insert_node(5);
	s->insert_node(9);
	s->insert_node(7);
	cout<<s->get_level()<<endl;
	s->print();
	s->delete_node(5);
	s->pop_back();
	s->pop_front();
	s->print();
	cout<<s->get_size()<<endl;
	cout<<((s->search_node(6))?"找到了":"没找到")<<endl;
	delete s;
	cout<<"删除成功"<<endl;
}

```

