<代码随想录>https://programmercarl.com/

# 基础算法

## 循环n次

```cpp
// 执行k-1次
while (--k) {
    cout << k << endl;
    cout << "dd" << endl;
}

// 执行k次
k = 5;
while (k--) {
    cout << k << endl;
    cout << "ee" << endl;
}
```



# 树

前缀树/字典树

* 为多叉树

![image-20230608171608199](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230608171608199.png)



```cpp
class Trie {
    bool isend;
    Trie* childen[28];
    // 工作指针
    Trie* oper;
public:

    Trie() {
        isend = false;
        for(int i = 0;i < 28;i++){
            childen[i] = nullptr;
        }
        oper = this;
    }
    
	// 插入
    void insert(string word) {
        for(auto elem :word){
            int index = elem - 'a';
            if(oper->childen[index] == nullptr){
                Trie* new_node = new Trie();
                oper->childen[index] = new_node;
            }
            oper = oper->childen[index];
        }
        oper->isend = true;
    }

    // 查询word
    bool search(string word) {
       for(auto elem :word){
            int index = elem - 'a';
            if(oper->childen[index] == nullptr) return false;
            oper = oper->childen[index];
        }
        return oper->isend;
    }
    
    // 查询
    bool startsWith(string prefix) {
        for(auto elem :prefix){
            int index = elem - 'a';
            if(oper->childen[index] == nullptr) return false;
            oper = oper->childen[index];
        }
        return true;
    }
};
 
```

# 数组

## 1 二分查找法

* 条件：有序、无重复
* 注意：定义查询范围为闭区间

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1; // 定义target在左闭右闭的区间里，[left, right]
        while (left <= right) { // 当left==right，区间[left, right]依然有效，所以用 <=
            int middle = left + ((right - left) / 2);// 防止溢出 等同于(left + right)/2
            if (nums[middle] > target) {
                right = middle - 1; // target 在左区间，所以[left, middle - 1]
            } else if (nums[middle] < target) {
                left = middle + 1; // target 在右区间，所以[middle + 1, right]
            } else { // nums[middle] == target
                return middle; // 数组中找到目标值，直接返回下标
            }
        }
        
        return -1;// 未找到目标值
        return left; // 插入位置 == right + 1,  此时left > right ，right为小于target的最后一个元素
    }
};
```

### 1.1 重复值范围

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> result(2,-1) ;
        // 数组为空
        if(nums.empty()) return result;
        result[0] = findStart(nums,target);
        result[1] = findEnd(nums,target);
        return result;
    }
    
    auto findStart(vector<int>& nums, int target) -> int {
        int len = nums.size();
        int left = 0, right = len - 1,start = -1;
        while(left <= right){
            int mid = left + ((right - left) / 2);
            if(nums[mid] > target){
                right = mid - 1;
            }else if(nums[mid] < target){
                left = mid + 1;
            }else{
                // mid 左侧寻找
                start = mid;
                right = mid - 1;
            }          
        }
        return start;
    }

    auto findEnd(vector<int>& nums, int target) -> int {
        int len = nums.size();
        int left = 0, right = len - 1,end = -1;
        while(left <= right){
            int mid = left + ((right - left) / 2);
            if(nums[mid] > target){
                right = mid - 1;
            }else if(nums[mid] < target){
                left = mid + 1;
            }else{
                // mid 右侧寻找
                end = mid;
                left = mid + 1;
            }           
        }
        return end;
        
    }
};
```

* 优化

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> result(2,-1) ;
        // 数组为空
        if(nums.empty()) return result;
        int len = nums.size();
        int start = -1,end = -1;
        int left = 0, right = len - 1;
        while(left <= right){
            int mid = left + ((right - left) / 2);
            if(nums[mid] > target){
                right = mid - 1;
            }else if(nums[mid] < target){
                left = mid + 1;
            }else{
                // mid 右侧寻找
                start = mid;
                end = mid;
                while(start>0 && nums[start -1]==target) start--;
                while(end<len-1 && nums[end + 1]==target) end++;
                break;
            }           
        }
        result[0] = start;
        result[1] = end;
        return result;
    }
};
```

### 1.2 解平方根

```cpp
class Solution {
public:
    int mySqrt(int x) {
        if(x<=0) return 0;
        int left = 1, num = -1, right = x;
        while(left <= right){
            int mid = left+((right-left)/2);
            if(mid > (x/mid)){
                right = mid -1;
            }else{
                // 平方根小于x，向右侧查找
                num = mid;
                left = mid+1;
            }
        }
        return num;
    }     
};

```

## 2 删除

### 2.1 暴力循环

* O( n^2^)

```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int size = nums.size();
        for (int i = 0; i < size; i++) {
            if (nums[i] == val) { // 发现需要移除的元素，就将数组集体向前移动一位
                for (int j = i + 1; j < size; j++) {
                    nums[j - 1] = nums[j];
                }
                i--; // 因为下标i以后的数值都向前移动了一位，所以i也向前移动一位
                size--; // 此时数组的大小-1
            }
        }
        return size;

    }
};
```



### 2.2 快慢指针

* O( n )

* 记忆：判断不相等，数据值不变

* 总结：

  1. 只需要访问操作本数据块： `slow = 0`

  1. 需要访问或操作前后两个数据块: `slow = -1`

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230619200951222.png" alt="image-20230619200951222" style="zoom: 80%;" />

![27.移除元素-双指针法](https://code-thinking.cdn.bcebos.com/gifs/27.%E7%A7%BB%E9%99%A4%E5%85%83%E7%B4%A0-%E5%8F%8C%E6%8C%87%E9%92%88%E6%B3%95.gif)

  

```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int slow = 0;
        for(int quick = 0;quick<nums.size();quick++){
            if(nums[quick] != val){         // 不相等
                nums[slow++] = nums[quick]; // 不变
            }
        }
        return slow;

    }
};
```

### 2.3 删除重复元素

* 升序排列

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if (nums.size() < 2) return nums.size();
        int j = 0;
        for (int i = 1; i < nums.size(); i++) // 从第二个元素开始
            if (nums[j] != nums[i]) { // 不相等
                nums[++j] = nums[i]; // 不变
            }
        return ++j;
    }
};

```

### 2.4 将0移动到末尾

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int len = nums.size();
        int j = 0;
        for(int i =0; i<len;i++){
            if(nums[i] != 0){
                nums[j++] = nums[i];
            }
        }
        // 删除后重新赋值
        for(int i =j;i<len;i++){
            nums[i]=0;
        }
    }
};
```

### 2.5 #回退字符串

![image-20230619204127985](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230619204127985.png)

![image-20230619204202836](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230619204202836.png)

```cpp
class Solution {
public:
    bool backspaceCompare(string s, string t) {
        s = result(s);
        t = result(t);
        return s == t;
    }
    
    string result(string s) {
        int len = s.length();
        int j = -1;
        for (int i = 0; i < len; i++) {
            if (s[i] != '#') {
                s[++j] = s[i];
            } else {
                if (j >= 0) {
                    j--;
                }
            }
        }
        return s.substr(0, j + 1);
    }
};
```

## 3 有序数组平方

* 双指针法

![img](https://code-thinking.cdn.bcebos.com/gifs/977.%E6%9C%89%E5%BA%8F%E6%95%B0%E7%BB%84%E7%9A%84%E5%B9%B3%E6%96%B9.gif)

```cpp
#include<vector>
#include<iostream>
using namespace std;

class Solution {
public:
    vector<int> sortSquares(vector<int>& nums) {
        vector<int> result(nums.size(),-1);
        int k =nums.size()-1;
        for(int i=0,j=nums.size()-1;i<=j;){
            if(nums[i]*nums[i] < nums[j]*nums[j]){
                result[k--] = nums[j]*nums[j];
                j--;
            }else{
                // >=
                result[k--] = nums[i]*nums[i];
                i++;
            }
        }
        return result;

    }
};

int main(){
	vector<int> a = {-4,-1,2,3,10};
	Solution solu;
	vector<int> b = solu.sortSquares(a);
	for(int i:b){
		cout<<i<<" ";
		
	}
	cout<< endl;
	return 0;
	}
```

## 4 滑动窗口

* 解决  --  求==满足条件==的最短or最长的==连续==子数组
* 解决 --  两次遍历数组得到所有情况

### 4.0 模板

```cpp
// 必须明确，到达什么条件触发缩小窗口的操作
for(int i = 0; i < len; i++){
    默认操作;
    while(默认操作达到某条件){
        
        缩小窗口
    }
        
}
```



### 4.1 和大于target的最短字符串

* leetcode 209

![209.长度最小的子数组](https://code-thinking.cdn.bcebos.com/gifs/209.%E9%95%BF%E5%BA%A6%E6%9C%80%E5%B0%8F%E7%9A%84%E5%AD%90%E6%95%B0%E7%BB%84.gif)

```cpp
输入：target = 7, nums = [2,3,1,2,4,3]
输出：2
解释：子数组 [4,3] 是该条件下的长度最小的子数组。
    
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int len = nums.size();
        int strlen=0;
        int end = INT32_MAX;
        int sum = 0;
        int j = 0;
        for(int i = 0; i<len;i++){
            sum+=nums[i];
            while(sum>=target){   // 1、满足条件
                strlen = i-j+1;
                end = end < strlen ? end : strlen;  // 2、最短
                sum-=nums[j]; // 3、窗口缩小
                j--;
            }
            
        }
        return end == INT32_MAX ? 0 : end;
        

    }
};
```

### 4.2 果篮问题

* leetcode904

![image-20230620191739731](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230620191739731.png)

```cpp
// 暴力破解
class Solution {
public:
    int totalFruit(vector<int>& fruits) {
        int len = fruits.size();
        int num = 0;
        int end = 0;
        vector<int> lanzi(2,-1);

        for(int i = 0;i<len;i++){
            num = 0;
            lanzi[0] = -1;
            lanzi[1] = -1;
            for(int j = i;j<len;j++){             
                // 设置篮子
                if(lanzi[0]==-1 ) lanzi[0] = fruits[j];
                else if(lanzi[0] != -1 && lanzi[0] != fruits[j] && lanzi[1]==-1) lanzi[1] = fruits[j];
                
                if(lanzi[0]==fruits[j] || lanzi[1]==fruits[j]) num+=1;
                else break;
            }
            end = end > num ? end : num;
        }
    return end;
    }
};
```

```cpp
// // 滑动窗口
class Solution {
public:
    int totalFruit(vector<int>& fruits) {
        int str = 0, ans = 0, j = 0;
        int len = fruits.size();
        map<int, int> lanzi;
        
        for (int i = 0; i < len; i++) {
            lanzi[fruits[i]]++; // 默认操作
            
            while (lanzi.size() > 2) {  // 满足条件，更新窗口
                lanzi[fruits[j]]--;
                if (lanzi[fruits[j]] == 0) {
                    lanzi.erase(fruits[j]);
                }
                j++;
            }
            str = i - j + 1;
            ans = max(ans, str);
        }
        return ans;
    }
};
```

### 4.3 最小覆盖字串

* leetcode 76

![image-20230620201345663](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230620201345663.png)

```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        string ans = "";
        int ns = s.size(), nt = t.size();
        if(ns < nt) return ans;

        // 构造哈希表
        unordered_map<char, int> ht;
        for(int i = 0; i < nt; ++i) ht[t[i]]++;

        int need = nt; // 记录t是否全部在子串中
        int str = 0;
        int min_len = INT32_MAX; 
        int index = -1; // 用来保存min_len下的子串起始下表

        for(int l = 0, r = 0; r < ns; ++r) {
            if(ht.count(s[r])) { // 判断字符是否再哈希表中
                if(ht[s[r]] > 0) need--;
                ht[s[r]]--;
            }
            while(need == 0) {
                str = r-l+1;
                if(str<min_len){
                    min_len = str;
                    index = l;
                    
                }

                // 缩小窗口
                if(ht.count(s[l])) { // 删除的元素是t字符串中的元素
                    if(ht[s[l]] == 0) need++;
                    ht[s[l]]++;
                }
                l++;
            }
        }
        return index==-1 ? ans :s.substr(index, min_len);
    }
};


```

## 5 螺旋矩阵

### 5.1 方阵

* leetcode 59

![image-20230621110344118](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230621110344118.png)

* 逻辑：下一位有数，就拐弯
* 下dx位的索引：(x + dx + n) % n   1、真实下一位下标  2、循环数组下一位下标

```cpp
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        
        // 二位数组
        vector<vector<int>> res(n, vector<int>(n, 0));
        int dx = 0, dy = 1;
        int x = 0, y = 0;
        for (int i = 1; i <= n * n; ++i) {
            res[x][y] = i;
            // 拐弯
            if (res[(x + dx +n) % n][(y + dy + n) % n] != 0) {
                int tmp = dy;
                dy = -dx;
                dx = tmp;
            }
            x += dx;
            y += dy;
        }
        return res;
    }
};
```



### 5.2 矩阵

* leetcode 54

![image-20230621113658007](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230621113658007.png)

![image-20230621113716564](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230621113716564.png)

```cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {

        vector<int> ans1;
        if(matrix.empty()) return ans1;
        else{
            int m = matrix.size();
            int n = matrix[0].size();
            vector<int> ans (m*n,0);
            int non = 101;
            int dx = 0, dy = 1;
            int x = 0, y = 0;

            for(int i = 0; i < m * n; i++){  
            ans[i] = matrix[x][y];
            matrix[x][y] = non;
            // 掉头
            if(matrix[(x + dx + m) % m][(y + dy + n) % n] == non){
                int tep = dy;
                dy = -dx;
                dx = tep;
            }
            x +=dx;
            y += dy;
            }
            return ans;
        }
    }
};
```

# 链表

![image-20230626161853689](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230626161853689.png)

## 1 构建链表类

```cpp
# include<iostream>
using namespace std;

class MyLinkedList {
public:
	struct node {
        int value;
        node* next;
        // 默认构造
        node(){
            next = nullptr;
        };
        // 有参构造
        node(int val):value(val),next(nullptr){};
    };
    
    MyLinkedList() {
        head = new node();
        tail = nullptr;
    }

    // 求总长
    int getLength(){
        node* tmp = head;
        int count = 0;
        while(tmp->next != nullptr){
            tmp = tmp->next;
            count ++;
        }
        cout<< count <<endl;
        return count;
    }

    void print(){
	   node* tmp = head;
	   int len = getLength();
	   for(int i = 0;i<len;i++){
	   	tmp = tmp->next;
	   	if(i == len-1) cout<< tmp->value<<endl;
	   	else cout<< tmp->value << "->";
	   }
    }
    
    int get(int index) {
        if(index < 0 || index >=getLength()) return -1;
        node * tmp = head;
        for(int i = 0; i<=index;i++){
            tmp = tmp->next;
        }
        cout<<tmp->value<<endl;
        return tmp->value;
    }
    
    // 头插
    void addAtHead(int val) {
        node* newnode = new node(val);
        if(head->next == nullptr){
            head->next = newnode;
            tail = newnode;

        }else{
            newnode->next = head->next;
            head->next = newnode;
        }

    }
    
    // 尾插
    void addAtTail(int val) {
        node* newnode = new node(val);
        if(head->next == nullptr) {
            head->next = newnode;
            tail = newnode;
        }else{
            tail->next = newnode;
            tail = newnode;
        }
    }
    
    void addAtIndex(int index, int val) {
        // 未判断链表长度 与 index的关系
        if(index < 0 || index >getLength()) return ;
        node * tmp = head;
        node* newnode = new node(val);
        if(index == getLength()) {
            addAtTail(val);
        }
        else if(index == 0) {
            addAtHead(val);
        }
        else{
            for(int i = 0; i<index;i++){
            tmp = tmp->next;
            }
            newnode->next = tmp->next;
            tmp->next = newnode;
        }      
    }
    
    void deleteAtIndex(int index) {
        if(index < 0 || index >=getLength()) return;
        node * tmp = head;
        // 前一个节点
        if(index == 0){
            tmp = head->next;
            head->next = head->next->next;
            delete tmp;
            if (head->next == nullptr) {
            tail = nullptr;
            }
        }
        else{
            for(int i = 0; i<index;i++){
            tmp = tmp->next;
            }
            node *del = tmp->next;
            tmp->next = tmp->next->next;
            delete del;
            if(del->next == nullptr) tail = tmp;
        }       
    }


    // 头节点无下标
    
    
private:
    node* head;
    node * tail;
};
```

```cpp
// 使用
int main(){
	MyLinkedList obj;
	obj.addAtTail(1);  //尾插
	obj.addAtTail(2);
	obj.getLength();  // 链表长度
	obj.print();
	obj.addAtHead(0); // 头插
	obj.addAtHead(-1);
	obj.addAtHead(-2);
	obj.getLength();
	obj.print();
	obj.addAtIndex(0,-3); // 指定下标插
	obj.addAtIndex(5,3);
	obj.addAtIndex(7,4);
	obj.getLength();
	obj.print();
	obj.get(3); // 得到指定下标value
    obj.get(7);
    obj.get(8);
	obj.deleteAtIndex(0);
	obj.print();
	obj.deleteAtIndex(2); // 删除指定节点
	obj.print();
	obj.deleteAtIndex(5);
	obj.print(); // 打印
	obj.deleteAtIndex(5);
	obj.print();

	return 0;
}

2
1->2
5
-2->-1->0->1->2
8
-3->-2->-1->0->1->3->2->4
-3
0
4
-1
-2->-1->0->1->3->2->4
-2->-1->1->3->2->4
-2->-1->1->3->2
-2->-1->1->3->2

```

## 2 翻转链表

* 采用头插法

  ```java
  // 迭代方法：增加虚头结点，使用头插法实现链表翻转
  public static ListNode reverseList1(ListNode head) {
      // 创建虚头结点
      ListNode dumpyHead = new ListNode(-1);
      dumpyHead.next = null;
      // 遍历所有节点
      ListNode cur = head;
      while(cur != null){
          ListNode temp = cur.next;
          // 头插法
          cur.next = dumpyHead.next;
          dumpyHead.next = cur;
          cur = temp;
      }
      return dumpyHead.next;
  }
  ```

## 3 节点两两交换

```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        ListNode* dummyHead = new ListNode(0); // 设置一个虚拟头结点
        dummyHead->next = head; // 将虚拟头结点指向head，这样方面后面做删除操作
        ListNode* cur = dummyHead;
        while(cur->next != nullptr && cur->next->next != nullptr) {
            ListNode* tmp = cur->next; // 记录临时节点
            ListNode* tmp1 = cur->next->next->next; // 记录临时节点

            cur->next = cur->next->next;    // 步骤一
            cur->next->next = tmp;          // 步骤二
            cur->next->next->next = tmp1;   // 步骤三

            cur = cur->next->next; // cur移动两位，准备下一轮交换
        }
        return dummyHead->next;
    }
};
```

## 4 删除链表倒数第n个节点

O( n )

O( 1 )

- 定义fast指针和slow指针，初始值为虚拟头结点，如图：

<img src="https://code-thinking.cdn.bcebos.com/pics/19.%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B9.png" alt="img" style="zoom:50%;" />

- fast首先走n + 1步 ，为什么是n+1呢，因为只有这样同时移动的时候slow才能指向删除节点的上一个节点（方便做删除操作），如图： <img src="https://code-thinking.cdn.bcebos.com/pics/19.%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B91.png" alt="img" style="zoom:50%;" />

- fast和slow同时移动，直到fast指向末尾，如题：

-  <img src="https://code-thinking.cdn.bcebos.com/pics/19.%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B92.png" alt="img" style="zoom:50%;" />

- 删除slow指向的下一个节点，如图：

   <img src="https://code-thinking.cdn.bcebos.com/pics/19.%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B93.png" alt="img" style="zoom:50%;" />

```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* dummyHead = new ListNode(0);
        dummyHead->next = head;
        ListNode* slow = dummyHead;
        ListNode* fast = dummyHead;
        while(n-- && fast != NULL) {
            fast = fast->next;
        }
        fast = fast->next; // fast再提前走一步，因为需要让slow指向删除节点的上一个节点
        while (fast != NULL) {
            fast = fast->next;
            slow = slow->next;
        }
        slow->next = slow->next->next; 
        
        // ListNode *tmp = slow->next;  C++释放内存的逻辑
        // slow->next = tmp->next;
        // delete nth;
        
        return dummyHead->next;
    }
};
```

## 5 环形链表

### 5.1 下一位索引

`index = (index + dx +n) % n`

index为索引，从0 出发

### 5.2 判断链表是否有环

* 快慢指针
* 快指针走2，慢指针走1

```cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode* fast = head;
        ListNode* slow = head;
        // 判断是否有环
        while(fast!= nullptr && fast->next != nullptr){ // 无头节点
        while(fast->next!= nullptr && fast->next->next != nullptr){ // 有头节点
            fast = fast->next->next;
            slow = slow->next;
            if(fast == slow){
                ListNode* index1 = fast;
                ListNode* index2 = head;
                // 寻找环入口
                while(index1 != index2){
                    index1 = index1->next;
                    index2 = index2->next;
                }
                return index1;

            }
        }
        return nullptr;
    }
};
```

注意事项：

1. 在每次循环中，`fast`指针每次移动两步，而`slow`指针每次移动一步。如果在判断`fast->next`是否为空之前，直接判断`fast->next->next`是否为空，那么在`fast`指针已经处于链表的最后一个节点时，`fast->next`就会为空，此时再访问`fast->next->next`就会导致空指针异常。

   因此，为了避免访问空指针，需要先判断`fast->next`是否为空，然后再判断`fast`是否为空。如果`fast`为空，就说明链表中没有环，循环结束。如果`fast`不为空，再继续判断`fast->next`是否为空，以确保可以安全地继续移动`fast`指针

2. 只适用于存在1个环

3. `x = z`![img](https://code-thinking-1253855093.file.myqcloud.com/pics/20220925103433.png)



# 哈希表

* 用来存储键值对类型数据

## 1 理论

* 哈希函数

![哈希表2](https://code-thinking-1253855093.file.myqcloud.com/pics/2021010423484818.png)

* 哈希碰撞

两个数据索引到一块位置

1. 拉链法
2. 线性探测法   -- 寻找下一个空位

### 1.3 三种哈希结构

用于：快速查找某个元素

当我们想使用哈希法来解决问题的时候，我们一般会选择如下三种数据结构。

- 数组 ：字符串映射
- set （集合）： 大量数据映射，数组消耗空间大
- map(映射)：需要key-value存储

这里数组就没啥可说的了，我们来看一下set。

在C++中，set 和 map 分别提供以下三种数据结构，其底层实现以及优劣如下表所示：

| 集合               | 底层实现 | 是否有序 | key是否可以重复 | 查询效率   | 增删效率   |
| ------------------ | -------- | -------- | --------------- | ---------- | ---------- |
| std::set           | 红黑树   | 有序     | 唯一            | O( log n ) | O(long n)  |
| std::mutiset       | 红黑树   | 有序     | ==是==          | O( log n ) | O( log n ) |
| std::unordered_set | 哈希表   | ==无序== | 唯一            | O( 1 )     | O( 1 )     |
| std::map           | 红黑树   | 有序     | 唯一            | O( log n ) | O( log n ) |
| std::mutimap       | 红黑树   | 有序     | ==是==          | O( log n ) | O( log n ) |
| std::unorder_map   | 哈希表   | ==无序== | 唯一            | O( 1 )     | O( 1 )     |



### 1.4 用法

* set系列

  ```cpp
  # include <unordered_set>
  // 创建空set
  std::unordered_Set<int> myset;
  // 将vector数据插入到set
  std::unordered_set<int> myset (v.begin(),v.end());
  
  // 增
  myset.insert(10);
  myset.emplace(10);
  
  // 删
  myset.erase(10);
  
  // 查
  auto it = myset.find(10);
  if(it != myset.end()){}
  
  // 遍历
  for(const auto& element : myset){
      cout<< element << endl;
  }
  
  // 为空
  myset.empty();
  
  // 大小
  myset.size();
  ```

* map系列

  ```cpp
  // 创建
  std::map<int, std::string> myMap;
  
  // 增
  myMap.insert({1, "apple"});
  myMap.insert(std::make_pair(2, "banana"));
  myMap.emplace(2, "banabna")
  myMap[3] = "orange";
  
  // 删
  myMap.erase(1);
  
  // 查
  auto it = myMap.find(2);
  if (it != myMap.end()) {
      std::cout << "Value of key 2: " << it->second << std::endl;
  }
  // 遍历
  for (const auto& pair : myMap) {
      std::cout << "Key: " << pair.first << ", Value: " << pair.second << std::endl;
  }
  
  // 为空
  mymap.empty();
      
  // 大小
  mymap.size();
  ```



## 2 字符串映射到数组

数组：当映射的数据较少时候，采用数组

* 判断是否为异位字符串

```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        
        int record[26] = {0};
        for (int i = 0; i < s.size(); i++) {
            record[s[i] - 'a']++;
        }
        
        for (int i = 0; i < t.size(); i++) {
            record[t[i] - 'a']--;
        }
        
        for (int i = 0; i < 26; i++) {
            if (record[i] != 0) {
                // record数组如果有的元素不为零0，说明字符串s和t 一定是谁多了字符或者谁少了字符。
                return false;
            }
        }
        // record数组所有元素都为零0，说明字符串s和t是字母异位词
        return true;
    }
};
```

## 3 两个数组相交

set : 当映射的数据非常大、稀疏、不确定时，使用set

* 交集元素唯一

```cpp
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        
        // 具有去重功能
        unordered_set<int> tmp;
        // 将nums1映射为哈希表
        unordered_set<int> record(nums1.begin(),nums1.end());
        for(int num : nums2){
            if(record.find(num) != record.end()) tmp.insert(num);
        }
        vector<int> res(tmp.begin(),tmp.end());
        return res;


    }
};
```

* 交集元素为数组中出现最少的次数

```cpp
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        unordered_map<int,int>record1;
        unordered_map<int,int>record2;
        vector<int>res;
        for(int elem : nums1){
            ++record1[elem];
        }
        for(int elem : nums2){
            ++record2[elem];
        }
        unordered_set<int> record3(nums1.begin(),nums1.end());
        unordered_set<int> record4(nums2.begin(),nums2.end());
        for(int elem : record4){
            if(record3.find(elem) != record3.end()){
                int n = min(record1[elem], record2[elem]);
                for(int i = 0; i<n;++i) res.push_back(elem);

            }
        }
        return res;

    }
};
```



## 4 快乐数

* 数组循环问题：采用双指针

```cpp
class Solution {
public:
    int bitSquareSum(int n) {
        // 计算某个数各个位数的和
        int sum = 0;
        while(n > 0)
        {
            int bit = n % 10;
            sum += bit * bit;
            n = n / 10;
        }
        return sum;
    }
    
    bool isHappy(int n) {
        int slow = n, fast = n;
        do{
            slow = bitSquareSum(slow);
            fast = bitSquareSum(fast);
            fast = bitSquareSum(fast);
        }while(slow != fast);
        
        return slow == 1;
    }
};

# include<iostream>
class Fun{
public:
    int myAdd(int n ){
        int sum = 0;
        while(!n){
            sum+= n%10;
            n = n / 10;
        }
        return sum;
    }
}
int main(){
    for(int i = 0;i<3;++i){
        cout<<"please input your num: "<<end;
    	int n = 0;
    	cin>>n;
    	cout<< myAdd(n)<<endl;
    }
    return 0;
    
}
```

## 5 两数之和

* unordered_map

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        
        vector<int>res;
        unordered_map<int,int> record;
        // 构造unordered_map,key:数值  value:下标
        for(int i =0;i<nums.size();++i){
            record[nums[i]] = i;
        }
        for(int i =0;i<nums.size();++i){
            if(record.find(target-nums[i]) != record.end()&& i !=record[target-nums[i]] ){
                res.push_back(i);
                res.push_back(record[target-nums[i]]);
                return res;
            }
            
        }
        return res;
    }
};
```

## 6 三数之和

* 双指针遍历
* set对结果集去重

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> ans;
        set<vector<int>> myset;

        for (int i = 0; i < nums.size(); ++i) {
            int left = i + 1;
            int right = nums.size() - 1;

            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];
                if (sum == 0) {
                    myset.insert({nums[i], nums[left], nums[right]});
                    ++left;
                    --right;
                } else if (sum < 0) {
                    ++left;
                } else {
                    --right;
                }
            }
        }

        ans.assign(myset.begin(), myset.end());
        return ans;
    }
};

```

## 7 四数之和

```cpp
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> ans;
        set<vector<int>> myset;

        for (int i = 0; i < nums.size() ; ++i) { 
            for(int j = i + 1; j < nums.size() ; ++j) { 
                int left = j + 1;
                int right = nums.size() - 1;
                while(left < right) {
                    if((long) nums[i] + nums[j]+nums[left] + nums[right] == target) {
                        myset.insert({nums[i], nums[j], nums[left], nums[right]});
                        ++left;
                        --right;
                    } else if((long) nums[i] + nums[j]+nums[left] + nums[right] < target) {
                        ++left;
                    } else {
                        --right;
                    }
                }
            }
        }
        
        ans.assign(myset.begin(), myset.end());
        return ans;
    }
};

```



## 8 四个数组各个元素和

* unordered_map

```cpp
class Solution {
public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
        unordered_map<int,int> rec1;
        unordered_map<int,int> rec2;
        int res = 0;
        // 映射为两个map，key:元素和 value：出现次数
        for(int ele1 : nums1){
            for(int ele2 : nums2){
                ++rec1[ele1+ele2];
            }
        }
        for(int ele1 : nums3){
            for(int ele2 : nums4){
                ++rec2[ele1+ele2];
            }
        }
        // 原理：两数之和
        for(const auto &pair : rec1 ){
            if(rec2.find(-pair.first) != rec2.end()){
                int count = pair.second * rec2[-pair.first];
                res+=count;
            }
        }
        return res;


    }
};
```



# 字符串



# 栈



# 队列

# 二叉树

## 1 递归遍历

### 1.1 先序

```cpp
class Solution {
public:
    void traversal(TreeNode* cur, vector<int>& vec) {
        if (cur == NULL) return;
        vec.push_back(cur->val);    // 中
        traversal(cur->left, vec);  // 左
        traversal(cur->right, vec); // 右
    }
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> result;
        traversal(root, result);
        return result;
    }
};
```



### 1.2 中序

```cpp
void traversal(TreeNode* cur, vector<int>& vec) {
    if (cur == NULL) return;
    traversal(cur->left, vec);  // 左
    vec.push_back(cur->val);    // 中
    traversal(cur->right, vec); // 右
}
```



### 1.3 后序

```cpp
void traversal(TreeNode* cur, vector<int>& vec) {
    if (cur == NULL) return;
    traversal(cur->left, vec);  // 左
    traversal(cur->right, vec); // 右
    vec.push_back(cur->val);    // 中
}
```



## 2 迭代遍历

* 栈实现

<img src="https://code-thinking.cdn.bcebos.com/gifs/%E4%BA%8C%E5%8F%89%E6%A0%91%E5%89%8D%E5%BA%8F%E9%81%8D%E5%8E%86%EF%BC%88%E8%BF%AD%E4%BB%A3%E6%B3%95%EF%BC%89.gif" alt="二叉树前序遍历（迭代法）" style="zoom:50%;" />

* 先序

```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        stack<TreeNode*> st;
        vector<int> result;
        if (root == NULL) return result;
        st.push(root);
        
        while (!st.empty()) {
            TreeNode* node = st.top();                       // 中
            st.pop();
            result.push_back(node->val);
            
            if (node->right) st.push(node->right);           // 右（空节点不入栈）
            if (node->left) st.push(node->left);             // 左（空节点不入栈）
        }
        return result;
    }
}
```

* 后序

```cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        stack<TreeNode*> st;
        vector<int> result;
        if (root == NULL) return result;
        st.push(root);
        
        while (!st.empty()) {
            TreeNode* node = st.top(); // 根
            st.pop();
            result.push_back(node->val);
            
            if (node->left) st.push(node->left); // 左
            if (node->right) st.push(node->right); // 右
        }
        reverse(result.begin(), result.end()); // 将结果反转之后就是左右中的顺序了
        return result;
    }
};
```



* 中序

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        TreeNode* cur = root;
        
        while (cur != NULL || !st.empty()) {
            if (cur != NULL) {             // 一路访问到左孩子最底层
                st.push(cur);              // 途经节点放入栈中
                cur = cur->left;                
            } else {
                cur = st.top();                 // 处理左孩子叶子节点
                st.pop();
                result.push_back(cur->val);     
                
                cur = cur->right;               // 右，若右孩子为空，则处理父亲节点
            }
        }
        return result;
    }
};
```



## 3 层序遍历

* 队列实现

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        
        queue<TreeNode *> qe;
        vector<vector<int>> res;

        
        if(root == nullptr) return res;
		// 根节点插入队列
        qe.push(root);

        // 遍历每一层
        while(!qe.empty()){
            int num = qe.size();
            vector<int> tmp;
            // 1 获取最大层数
            // int max_size ++;
			
            // 处理每个元素
            for(int i = 0; i< num;i++){

                TreeNode *cur = qe.front();
                tmp.push_back(cur->val);
                qe.pop();
                
				// 获取下一层
                if(cur->left != nullptr) qe.push(cur->left);
                if(cur->right != nullptr) qe.push(cur->right);
                // 2最小层数
                // if (!node->left && !node->right) return depth;
                
            }

            res.push_back(tmp);
            
        }
        return res;

 
    }
};
```



## 4 对称二叉树

#### 5.1  检测是否为对称二叉树

本质：检查两个节点是否相等

1. 两个节点是否相等    2、节点的某些子节点是否满足要求

```cpp
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if (root == nullptr) {
            return true; // 空树是对称的
        }
        return isMirror(root->left, root->right);
    }

    bool isMirror(TreeNode* leftSubtree, TreeNode* rightSubtree) {
        if (leftSubtree == nullptr && rightSubtree == nullptr) {
            return true; // 两个子树都为空，对称
        }
        if (leftSubtree == nullptr || rightSubtree == nullptr) {
            return false; // 一个子树为空，另一个不为空，不对称
        }
        
        // 1、比较当前节点的值，2、递归比较左右子树的子节点是否满足要去
        return (leftSubtree->val == rightSubtree->val) &&
               isMirror(leftSubtree->left, rightSubtree->right) &&
               isMirror(leftSubtree->right, rightSubtree->left);
    }
};
```

### 5.2 求一个树的堆成二叉树

```cpp
// 交换左右节点
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (root == NULL) return root;
        swap(root->left, root->right);  // 中
        invertTree(root->left);         // 左
        invertTree(root->right);        // 右
        return root;
    }
};
```







# 笔试题

## 精灵云

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231103142958884.png" alt="image-20231103142958884" style="zoom:50%;" />

* 从所有句子中找到完整的句子
* 将一句话全部反转，" . " 除外
* 在一句话内，找到一个单词
* 将单词反转

```cpp
#include <iostream>
#include <string>
using namespace std;
// 反转字符串
void reverse(std::string &s, int start, int end)
{
    while (start < end)
    {
        std::swap(s[start], s[end]);
        start++;
        end--;
    }
}

void reverseWordsInSentence(std::string &s)
{

    // 找到第一个句子
    int n = s.size();
    int start = 0, end = 0;

    while (start < n)
    {
        // 找到一个句子[start, end]
        while (end < n && s[end] != '.')
        {
            end++;
        }
        // 反转句子,除了标点
        cout << s[end] << endl;
        reverse(s, start, end - 1);

        // 反转单词
        int wstart = start;
        int wend = wstart;
        for (int i = wstart; i < end; i++)
        {
            while (wend < end && s[wend] != ' ')
            {
                wend++;
            }
            reverse(s, wstart, wend - 1);
            cout << "一个单词完成" << endl;
            wstart = wend + 1;
            wend = wstart;
        }

        // 查找下一个句子
        start = end + 2;
        end = start;
    }
}

int main()
{
    std::string str = "i am your sister. thank you.";
    reverseWordsInSentence(str);
    std::cout << str << std::endl;
    return 0;
}

```

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231103143155416.png" alt="image-20231103143155416" style="zoom:50%;" />

* 二分查找

```cpp
#include <iostream>
#include <vector>
#include <cstdlib>
using namespace std;

int findLastOccurrence(const std::vector<int>& vec, int x) {
    int left = 0, right = vec.size() - 1, result = -1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (vec[mid] == x) {
            result = mid; // 更新结果
            left = mid + 1; // 移动到右侧以查找最后一个出现的元素
        } else if (vec[mid] < x) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return result;
}


int main(int argc, char *argv[])
{
    if (argc != 2)
    {
        cout << "./find_index [x]" << endl;
        return 1; // 返回非零值表示程序出错
    }

    int x = atoi(argv[1]);
    vector<int> vec = {1, 2, 3, 3, 3, 4, 5, 6, 6, 7, 8, 8, 9};
    int ans = findLastOccurrence(vec, x);

    if (ans == -1)
    {
        cout << "Element " << x << " not found in the vector." << endl;
    }
    else
    {
        cout << "Last index of " << x << " in the vector: " << ans << endl;
    }

    return 0;
}

```

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231103154322350.png" alt="image-20231103154322350" style="zoom: 80%;" />

* 使用双向链表list记录key的使用顺序，list<key, value>,使用过的放在表头，当容量满时，删除表尾元素
* 使用unordered_map进行索引，unordered_map<key, list::iter>



```cpp
#include <iostream>
#include <unordered_map>
#include <list>

using namespace std;

class Cache
{
    int capacity;
    list<pair<int, int>> lru; // key, value
    // key所指的list节点顺序，代表最近是否使用
    unordered_map<int, list<pair<int, int>>::iterator> cache; // key list::iter【相当于列表的下表】

public:
    explicit Cache(int n) : capacity(n) {}

    bool put(int key, int value)
    {
        // 删除旧的，添加新的
        if (cache.find(key) != cache.end())
        {
            lru.erase(cache[key]);
        }
        lru.push_front({key, value});
        cache[key] = lru.begin();

        if (cache.size() > capacity)
        {
            int lastKey = lru.back().first;
            lru.pop_back();
            cache.erase(lastKey);
        }
        return true;
    }

    int get(int key)
    {
        if (cache.find(key) == cache.end())
        {
            return -1;
        }
        // 将 cache[key] 指向的元素从 lru 中删除，并将其移动到 lru 列表的开头
        lru.splice(lru.begin(), lru, cache[key]);
        return cache[key]->second;
    }
};

int main()
{
    Cache c(2);
    c.put(1, 10);
    c.put(2, 20);
    cout << c.get(1) << endl; // 10
    c.put(3, 30);             // 2 will be removed as it's the least recently used
    cout << c.get(2) << endl; // -1
    cout << c.get(3) << endl; // 30
    return 0;
}

```



# 双指针

1. 数组分块

![image-20231220005524874](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231220005524874.png)

[移动0](https://leetcode.cn/problems/move-zeroes/submissions/)： 数组分块

[复写0](https://leetcode.cn/problems/duplicate-zeros/submissions/)：找到最后一个复写数，从后往前复写
