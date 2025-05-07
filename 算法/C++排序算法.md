```c++
#include<iostream>
#include<vector>
#include<algorithm>
#include<random>
#include<chrono>
using namespace std;

vector<int> getArray(int size, int maxValue,bool flag) {
	// flag:是否有负数，true表示有
	vector<int> arr(size);
	static random_device rd;
	static mt19937 gen(rd());
	int nega=flag?-maxValue:0;
	uniform_int_distribution dist(nega, maxValue);
	for (int i = 0; i < size; i++) {
		arr[i] = dist(gen);
	}
	return arr;
}

bool isEqual(vector<int> v1, vector<int> v2) {
	if (v1.size() != v2.size()) {
		return false;
	}
	for (size_t i = 0; i < v1.size(); i++) {
		if (v1[i] != v2[i]) {
			return false;
		}
	}
	return true;
}

void bubbleSort(vector<int> &arr){
	for(int i=arr.size()-1;i>0;i--){
		bool flag=true;
		for(int j=0;j<i;j++){
			if(arr[j]>arr[j+1]){
				int tmp=arr[j];
				arr[j]=arr[j+1];
				arr[j+1]=tmp;
				flag=false;
			}
		}
		if(flag){
			break;
		}
	}
}

void selectSort(vector<int> &arr){
	int n=arr.size();
	for(int i=0;i<n-1;i++){
		int min=i;
		for(int j=i+1;j<n;j++){
			if(arr[j]<arr[min]){
				min=j;
			}
		}
		int tmp=arr[i];
		arr[i]=arr[min];
		arr[min]=tmp;
	}
}

void insertSort(vector<int> &arr){
	int n=arr.size();
	for(int i=1;i<n;i++){
		for(int j=i;j>0&&arr[j]<arr[j-1];j--){
			int tmp=arr[j];
			arr[j]=arr[j-1];
			arr[j-1]=tmp;
		}
	}
}

void quickSort(vector<int> &arr,int left,int right){
	if(left>=right){
		return;
	}
	int i=left,j=right;
	int p=arr[i];
	while(i<j){
		while(i<j&&arr[j]>=p){
			j--;
		}
		arr[i]=arr[j];
		while(i<j&&arr[i]<=p){
			i++;
		}
		arr[j]=arr[i];
	}
	arr[i]=p;
	quickSort(arr,left,i-1);
	quickSort(arr,i+1,right);
}

void quickSort(vector<int> &arr){
	quickSort(arr,0,arr.size()-1);
}

void merge(vector<int> &arr,int left,int mid,int right){
	vector<int> res(right-left+1);
	int i=left,j=mid+1,k=0;
	while(i<=mid&&j<=right){
		if(arr[i]<=arr[j]){
			res[k++]=arr[i++];
		}else{
			res[k++]=arr[j++];
		}
	}
	while(i<=mid){
		res[k++]=arr[i++];
	}
	while(j<=right){
		res[k++]=arr[j++];
	}
	for(size_t m=0;m<res.size();m++){
		arr[left+m]=res[m];
	}
}

void mergeSort(vector<int> &arr,int left,int right){
	if(left>=right){
		return;
	}
	int mid=left+((right-left)>>1);
	mergeSort(arr,left,mid);
	mergeSort(arr,mid+1,right);
	merge(arr,left,mid,right);
}

void mergeSort(vector<int> &arr){
	mergeSort(arr,0,arr.size()-1);
}

void siftDown(vector<int> &arr,int i,int n){
	while(true){
		int l=2*i+1;
		int r=2*i+2;
		int max=i;
		if(l<n&&arr[l]>arr[max]){
			max=l;
		}
		if(r<n&&arr[r]>arr[max]){
			max=r;
		}
		if(max==i){
			break;
		}
		int tmp=arr[i];
		arr[i]=arr[max];
		arr[max]=tmp;
		i=max;
	}
}

void heapSort(vector<int> &arr){
	for(int i=arr.size()/2-1;i>=0;i--){
		siftDown(arr,i,arr.size());
	}
	for(int i=arr.size()-1;i>0;i--){
		int tmp=arr[0];
		arr[0]=arr[i];
		arr[i]=tmp;
		siftDown(arr,0,i);
	}
}

void shellSort(vector<int> &arr){
	int n=arr.size();
	for(int k=n/2;k>0;k/=2){
		for(int i=k;i<n;i++){
			for(int j=i;j>=k&&arr[j]<arr[j-k];j-=k){
				swap(arr[j],arr[j-k]);
			}
		}
	}
}

void countSort(vector<int> &arr){
	int n=arr.size();
	int maxn=arr[0],minn=arr[0];
	for(int num:arr){
		maxn=(num>maxn)?num:maxn;
		minn=(num<minn)?num:minn;
	}
	vector<int> count(maxn-minn+1,0);
	for(int num:arr){
		count[num-minn]++;
	}
	for(int i=0;i<maxn-minn;i++){
		count[i+1]+=count[i];
	}
	vector<int> res(n);
	for(int i=n-1;i>=0;i--){
		int num=arr[i];
		res[count[num-minn]-1]=num;
		count[num-minn]--;
	}
	arr=res;
}

void bucketSort(vector<int> &arr){
	int maxn=arr[0],minn=arr[0];
	for(int num:arr){
		maxn=(num>maxn)?num:maxn;
		minn=(num<minn)?num:minn;
	}
	int size=5;
	int count=(maxn-minn)/size+1;
	vector<vector<int>> bucket(count);
	for(int num:arr){
		bucket[(num-minn)/size].push_back(num);
	}
	for(int i=0;i<count;i++){
		sort(bucket[i].begin(),bucket[i].end());
	}
	int k=0;
	for(int i=0;i<count;i++){
		for(int num:bucket[i]){
			arr[k++]=num;
		}
	}
}

void radixSort(vector<int> &arr){
	int n=arr.size();
	int maxn=arr[0];
	for(int num:arr){
		maxn=(num>maxn)?num:maxn;
	}
	for(int k=1;k<=maxn;k*=10){
		vector<int> count(10,0);
		for(int num:arr){
			int d=num/k%10;
			count[d]++;
		}
		for(int i=0;i<9;i++){
			count[i+1]+=count[i];
		}
		vector<int> res(n);
		for(int i=n-1;i>=0;i--){
			int d=arr[i]/k%10;
			res[count[d]-1]=arr[i];
			count[d]--;
		}
		arr=res;
	}
}

void radixSort1(vector<int> &arr){
	int n=arr.size();
	int minn=arr[0],offset=0;
	for(int num:arr){
		minn=(num<minn)?num:minn;
	}
	if(minn<0){
		offset=-minn;
	}
	for(int i=0;i<n;i++){
		arr[i]=arr[i]+offset;
	}
	int maxn=arr[0];
	for(int num:arr){
		maxn=(num>maxn)?num:maxn;
	}
	for(int k=1;k<=maxn;k*=10){
		vector<int> count(10,0);
		for(int num:arr){
			count[num/k%10]++;
		}
		for(int i=0;i<9;i++){
			count[i+1]+=count[i];
		}
		vector<int> res(n);
		for(int i=n-1;i>=0;i--){
			int d=arr[i]/k%10;
			res[count[d]-1]=arr[i];
			count[d]--;
		}
		arr=res;
	}
	for(int i=0;i<n;i++){
		arr[i]=arr[i]-offset;
	}
}

void testSort(int testTime,int size,int maxValue,void (*doSort)(vector<int>&),string name){
	bool flag = false;
	chrono::time_point start = chrono::high_resolution_clock::now();
	for (int i = 0; i < testTime; i++) {
		vector<int> arr = getArray(size, maxValue,true);
		vector<int> arr1 = arr;
		
		doSort(arr);
		
		sort(arr1.begin(), arr1.end());
		flag = isEqual(arr, arr1);
		if (!flag) {
			break;
		}
	}
	chrono::time_point end = chrono::high_resolution_clock::now();
	chrono::milliseconds duration = chrono::duration_cast<chrono::milliseconds>(end - start);
	cout <<name<< "总耗时：" << duration.count() << " 毫秒" << endl;
	cout << boolalpha << flag << endl;
}

int main(){
	int testTime = 100000;
	int size = 100;
	int maxValue = 100;
	testSort(testTime,size,maxValue,bubbleSort,"bubbleSort");
	testSort(testTime,size,maxValue,selectSort,"selectSort");
	testSort(testTime,size,maxValue,insertSort,"insertSort");
	testSort(testTime,size,maxValue,quickSort,"quickSort");
	testSort(testTime,size,maxValue,mergeSort,"mergeSort");
	testSort(testTime,size,maxValue,heapSort,"heapSort");
	testSort(testTime,size,maxValue,countSort,"countSort");
	testSort(testTime,size,maxValue,bucketSort,"bucketSort");
	testSort(testTime,size,maxValue,radixSort,"radixSort");
	return 0;
}

//int main() {
//	int testTime = 100000;
//	int size = 100;
//	int maxValue = 100;
//	bool flag = false;
//	chrono::time_point start = chrono::high_resolution_clock::now();
//	for (int i = 0; i < testTime; i++) {
//		vector<int> arr = getArray(size, maxValue,true);
//		vector<int> arr1 = arr;
//		
//		radixSort(arr);
//		
//		sort(arr1.begin(), arr1.end());
//		flag = isEqual(arr, arr1);
//		if (!flag) {
//			break;
//		}
//	}
//	chrono::time_point end = chrono::high_resolution_clock::now();
//	chrono::milliseconds duration = chrono::duration_cast<chrono::milliseconds>(end - start);
//	cout << "总耗时：" << duration.count() << " 毫秒" << endl;
//	cout << boolalpha << flag << endl;
//	return 0;
//}
```

