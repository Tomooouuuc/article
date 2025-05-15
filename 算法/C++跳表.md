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
	SkipList(int max_level,double p=0.5): max_level(max_level),p(p),skip_level(0),size(0), seed(static_cast<unsigned>(time(nullptr))) {
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
		if(search_node(val)){
			cout << val << "已经存在" << endl;
			return;
		}
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
		if(!search_node(val)){
			cout << val << "删除失败" << endl;
			return;
		}
		Node *cur = head;
		Node *del;
		for (int i = max_level - 1; i >= 0; i--) {
			while (cur->next[i] && cur->next[i]->val < val) {
				cur=cur->next[i];
			}
			if(cur->next[i]&&cur->next[i]->val==val){
				del=cur->next[i];
				cur->next[i]=cur->next[i]->next[i];
			}
		}
		delete del;
		size--;
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
	s->delete_node(100);
	s->print();
	cout<<((s->search_node(INT_MIN))?"找到了":"没找到")<<endl;
	delete s;
	cout<<"删除成功"<<endl;
}


```

