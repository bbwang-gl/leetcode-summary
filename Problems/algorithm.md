- [编程技巧](#编程技巧)
    - [`<bits/stdc++.h>`](#bitsstdch)
- [stl 常用容器，方法](#stl-常用容器方法)
  - [string](#string)
    - [to\_string](#to_string)
    - [string to digit](#string-to-digit)
- [大根堆](#大根堆)
- [排序](#排序)
  - [堆排序](#堆排序)
- [二叉树](#二叉树)
  - [遍历](#遍历)
    - [先序遍历144.二叉树前序遍历](#先序遍历144二叉树前序遍历)
    - [二叉树的中序遍历](#二叉树的中序遍历)
    - [后续遍历](#后续遍历)
- [搜索](#搜索)
  - [深搜](#深搜)
    - [深搜小节](#深搜小节)
      - [适用场景](#适用场景)
      - [代码模版](#代码模版)
  - [广搜](#广搜)
- [栈](#栈)
- [线性表](#线性表)
  - [数组](#数组)
  - [单链表](#单链表)

# 编程技巧
### `<bits/stdc++.h>`   
#include <bits/stdc++.h> 是 GCC（GNU Compiler Collection）编译器提供的一种非标准头文件，它的作用是一次性包含 C++ 标准库中的几乎所有常用头文件。

# stl 常用容器，方法
## string
header `<string>` 
### to_string 
int/long/long long/unsigned/unsigned long/unsigned long long/float/double/long double to string.
### string to digit
stod/ stof/ stoi/ stol/ stold/ stoll/stoull/stoul  
```cpp 
unsigned long ul = std::stoul (str,nullptr,0);
```

# 大根堆
```cpp
#include <iostream>
#include <vector>
#include <stdexcept> // 

class MaxHeap {
private:
    std::vector<int> heap; // internal storage (0-based)

    // Helper functions (1-based logic)
    int parent(int i) { return (i - 1) / 2; }     // for 0-based: parent = (i-1)/2
    int left(int i) { return 2 * i + 1; }         // for 0-based: left = 2*i + 1
    int right(int i) { return 2 * i + 2; }        // for 0-based: right = 2*i + 2

    // Max-Heapify as described in CLRS (0-based adaptation)
    void maxHeapify(int i) {
        int l = left(i);
        int r = right(i);
        int largest = i;

        if (l < static_cast<int>(heap.size()) && heap[l] > heap[largest])
            largest = l;
        if (r < static_cast<int>(heap.size()) && heap[r] > heap[largest])
            largest = r;

        if (largest != i) {
            std::swap(heap[i], heap[largest]);
            maxHeapify(largest);
        }
    }

public:
    // Constructor: build a max-heap from a given vector
    MaxHeap(const std::vector<int>& arr) : heap(arr) {
        if (!heap.empty()) {
            // Start from last non-leaf node and heapify down
            // 时间复杂度是 O（n）；原因是 高度为 h 的节点最多有 peak( n/ 2**(h+1))
            int n = static_cast<int>(heap.size());
            for (int i = (n / 2) - 1; i >= 0; --i) {
                maxHeapify(i);
            }
        }
    }

    // Default constructor (empty heap)
    MaxHeap() = default;

    // Insert a new key into the heap
    void insert(int key) {
        heap.push_back(key);
        int i = static_cast<int>(heap.size()) - 1;

        // Bubble up to maintain max-heap property
        while (i > 0 && heap[parent(i)] < heap[i]) {
            std::swap(heap[i], heap[parent(i)]);
            i = parent(i);
        }
    }

    // Extract the maximum element (root)
    int extractMax() {
        if (heap.empty()) {
            throw std::runtime_error("Heap is empty");
        }

        int maxVal = heap[0];
        heap[0] = heap.back();
        heap.pop_back();

        if (!heap.empty()) {
            maxHeapify(0);
        }

        return maxVal;
    }

    // Utility: get current size
    size_t size() const {
        return heap.size();
    }

    // Utility: check if empty
    bool empty() const {
        return heap.empty();
    }

    // For debugging: print heap (in array form)
    void print() const {
        for (int val : heap) {
            std::cout << val << " ";
        }
        std::cout << std::endl;
    }
};

// Example usage
int main() {
    // Initialize heap from an array
    std::vector<int> arr = {4, 1, 3, 2, 16, 9, 10, 14, 8, 7};
    MaxHeap h(arr);

    std::cout << "Initial max-heap: ";
    h.print(); // Should satisfy max-heap property

    // Insert new element
    h.insert(15);
    std::cout << "After inserting 15: ";
    h.print();

    // Extract max repeatedly
    std::cout << "Extracted max elements: ";
    while (!h.empty()) {
        std::cout << h.extractMax() << " ";
    }
    std::cout << std::endl;

    return 0;
}
```
# 排序
https://leetcode.cn/problems/sort-an-array/description/
## 堆排序
```cpp
class Solution {
    void maxHeapify(vector<int>& nums, int i, int len) {
        for (; (i << 1) + 1 <= len;) {
            int lson = (i << 1) + 1;
            int rson = (i << 1) + 2;
            int large;
            if (lson <= len && nums[lson] > nums[i]) {
                large = lson;
            } else {
                large = i;
            }
            if (rson <= len && nums[rson] > nums[large]) {
                large = rson;
            }
            if (large != i) {
                swap(nums[i], nums[large]);
                i = large;
            } else {
                break;
            }
        }
    }
    void buildMaxHeap(vector<int>& nums, int len) {
        for (int i = len / 2; i >= 0; --i) {
            maxHeapify(nums, i, len);
        }
    }
    void heapSort(vector<int>& nums) {
        int len = (int)nums.size() - 1;
        buildMaxHeap(nums, len);
        for (int i = len; i >= 1; --i) {
            swap(nums[i], nums[0]);
            len -= 1;
            maxHeapify(nums, 0, len);
        }
    }
public:
    vector<int> sortArray(vector<int>& nums) {
        heapSort(nums);
        return nums;
    }
};

// 堆排序
/**
作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/sort-an-array/solution/pai-xu-shu-zu-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。 **/
```

# 二叉树
## 遍历 
### 先序遍历[144.二叉树前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/description/)
- 先序遍历递归
    ```cpp
    void preorderTraversalRecursive(TreeNode* n, vector<int>& vec) {
        if (!n) {
            return;
        }
        vec.push_back(n->val);   
        preorderTraversalRecursive(n->left, vec);
        preorderTraversalRecursive(n->right, vec);
    }
    ```
-  先序遍历非递归
    ```cpp
    vector<int> preorderTraversalStack(TreeNode* root) {
        vector<int> ret;
        if (!root) {
            return ret;
        }
        stack<TreeNode*> st;
        TreeNode* p = root;
        st.push(root);
        while (!st.empty()) {
            p = st.top();
            st.pop();
            ret.push_back(p->val);
            if (p->right) {
                st.push(p->right);
            }
            if (p->left) {
                st.push(p->left);
            }
        }
        return ret;
    }
    ```
-  先序遍历非递归-2
    ```cpp
   void preorderTraversal(TreeNode* root, vector<int>* vec) {
        stack<TreeNode*> st;
        TreeNode* p = root;
        while (p || !st.empty()) {
            while (p) {
                vec->emplace_back(p->val);
                st.push(p);
                p = p->left;
            }
            if (!st.empty()) {
                p = st.top();
                st.pop();
                p = p->right;
            }
        }
    }
    ```
### [二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/description/)
- 递归
  ```cpp
    void inorderTraversal(TreeNode* n, vector<int>& vec) {
        if (!n) {
            return;
        }
        inorderTraversal(n->left, vec);
        vec.push_back(n->val);
        inorderTraversal(n->right, vec);
    }
  ```
- 非递归
  ```cpp
    void inorderTraversalStack(TreeNode* root, vector<int>& vec) {
        if (!root) {
            return;
        }
        TreeNode* p = root;
        stack<TreeNode*> st;
        while (p || !st.empty()) {
            while (p) {
                st.push(p);
                p = p->left;
            }
            p = st.top();
            st.pop();
            vec.push_back(p->val);
            p = p->right;
        }
    }
  ```
### [后续遍历](https://leetcode.cn/problems/binary-tree-postorder-traversal/description)
- 递归
  ```cpp
    void postorderTraversal(TreeNode* p , vector<int>& vec) {
        if (!p) {
            return;
        }
        postorderTraversal(p->left, vec);
        postorderTraversal(p->right, vec);
        vec.push_back(p->val);
    }
  ```
- 非递归
  ```cpp
    void postorderTraversalStack(TreeNode* root, vector<int>& vec) {
        if (!root) {
            return;
        }
        TreeNode* p = root;
        TreeNode* prev = nullptr;
        stack<TreeNode*> st;
        while (p || !st.empty()) {
            while (p) {
                st.push(p);
                p = p->left;
            }
            p = st.top();
            st.pop();
            /***
             p->right == nullptr 是必须的， 因为有些prev 不是nullptr 但是p->right 是nullptr
             if 里需要重置 prev 和 p， 重置p是因为， 处理左右子树和根之后， 需要让算法继续弹出栈顶数据。
            ***/
            if (p->right == prev || p->right == nullptr) {
                vec.push_back(p->val);
                prev = p;
                p = nullptr;
            } else {
                st.push(p);
                p = p->right;
            }
        }
    }
  ```
- 非递归-2
  ```cpp
    void postorderTraversal_2(TreeNode* root, vector<int>& vec) {
        if (!root) {
            return;
        }
        TreeNode* p = root;
        using stack_t = pair<TreeNode*, int>;
        stack<stack_t> st;
        while (p || !st.empty()) {
            while (p) {
                st.emplace(make_pair(p, 0));
                p = p->left;
            }
            stack_t tmp = st.top();
            st.pop();
            if (tmp.second == 0) {
                p = tmp.first->right;
                st.emplace(make_pair(tmp.first, 1));
            } else if (tmp.second == 1) {
                vec.emplace_back(tmp.first->val);
                p = nullptr;
            }
        }
    }
  ```
- 非递归-3
  ```cpp
    void postorder2(TreeNode* root, vector<int>* vec) {
        /**
        后续遍历的顺序是 left， right , root
        先序遍历的顺序是 root， left， right
        我们可以按照先序 遍历的方法： root， right， left  遍历， 最后反转结果就是后续遍历了。
        **/
        stack<TreeNode*> st;
        if (!root) {
            return ;
        }
        TreeNode* p = root;
        while ( p || !st.empty()) {
            while (p) {
                st.emplace(p);
                vec->emplace_back(p->val);
                p = p->right;
            }
            if (!st.empty()) {
                p = st.top();
                p = p->left;
                st.pop();
            }
        }
        reverse(vec->begin(), vec->end());
    }
  ```
# 搜索
## 深搜
### 深搜小节
ref: https://github.com/soulmachine/leetcode

#### 适用场景
递归必然是深搜。 所以如果是递归数据结构，（单链表、二叉树、集合）都可以用深搜。
#### 代码模版
```cpp
void dfs(input, path, result, int cur or gap) {
    
}
```

[200. 岛屿数量](https://leetcode.cn/problems/number-of-islands/description/)
```cpp
class Solution {
public:
    vector<int> index;
    int m = 0, n = 0;
    int numIslands(vector<vector<char>>& grid) {
        m  = grid.size();
        n = grid[0].size();
        index = {0,1,0,-1,0};
        int ret  = 0;
        for (int i  = 0; i < grid.size(); ++i) {
            for (int j = 0; j < grid[0].size(); ++j) {
                if (grid[i][j] == '1') {
                    ++ret;
                    dfs(grid, i, j);
                }
            }
        }
        return ret;
    }
    void dfs (vector<vector<char>>& grid, int i, int j) {
        grid[i][j] = 'x';
        
        int ii = 0, jj = 0;
        for (int id = 0; id < 4; ++id) {
            ii = i + index[id];
            jj = j + index[id +1];
            if (ii >= 0 && ii < m && jj >= 0 && jj < n ) {
                if (grid[ii][jj] == '1') {
                    dfs(grid, ii, jj);
                }
            }
        }
    }
};
```

## 广搜
[207. 课程表](https://leetcode.cn/problems/course-schedule/description) 

```cpp
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        vector<int> inDegree(numCourses, 0);
        vector<vector<int>> out_edge(numCourses);
        for (auto& vec : prerequisites ) {
            inDegree[vec[0]]++;
            out_edge[vec[1]].push_back(vec[0]);
        }
        queue<int> q;
        for (int i = 0; i < numCourses; i++) {
            if (inDegree[i] == 0) {
                q.push(i);
            }
        }
        int visited_Node = 0;
        while (!q.empty()) {
            ++visited_Node;
            int i = q.front();
            q.pop();
            for (auto out : out_edge[i]) {
                --inDegree[out];
                if (inDegree[out] == 0) {
                    q.push(out);
                }
            }
        }
        return visited_Node >= numCourses;
    }
};
```
# 栈
[32. 最长有效括号](https://leetcode.cn/problems/longest-valid-parentheses/description/)
```cpp
/*
思路： stack 里存储的数据应该是： 上一个匹配失败的“）”的index， 等待匹配的“（”的index的集合， 这样的话未被匹配的“（” 可能有机会连续起来获得最大子串长度，而栈底 元素也方便计算本轮匹配的最大长度。
*/
class Solution {
public:
    int longestValidParentheses(string s) {
        stack<int> st;
        st.push(-1); // 初始基准
        int maxLen = 0;

        for (int i = 0; i < s.size(); i++) {
            if (s[i] == '(') {
                st.push(i);
            } else {
                st.pop();
                if (st.empty()) {
                    st.push(i);
                } else {
                    maxLen = max(maxLen, i - st.top());
                }
            }
        }
        return maxLen;
    }
};
```
# 线性表
## 数组
[80. 删除有序数组中的重复项 II](https://leetcode.cn/problems/remove-duplicates-from-sorted-array-ii)
```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int k = 0, count = 0;
        for (int i = 0; i < nums.size(); i++){
            if(i == 0 || nums[i] != nums[i-1]){ 
                count = 1;
                nums[k++] = nums[i];
            } else {
                if (count < 2) {
                    nums[k++] = nums[i];
                    count++;
                }
            }
        }
        return k;
    }
};
```
- [20.有效括号](https://leetcode.cn/problems/valid-parentheses/description/)
[26. 删除有序数组中的重复项](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/description/)


## 单链表
[92. 反转链表 II](https://leetcode.cn/problems/reverse-linked-list-ii/)
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        if (left == right) {
            return head;
        }

        ListNode dummy;
        dummy.next = head;
        ListNode *p = head, *pre = &dummy;
        ListNode* tail = nullptr;
        int i = 1;
        while (i < left && p) {
            i++;
            pre = p;
            p = p->next;
        };
        if (!p) {
            return head;
        }
        tail = p;
        i = left + 1;
        p = p->next;
        while (p && i <= right) {
            tail->next = p->next;
            p->next = pre->next;
            pre->next = p;
            // next
            p = tail->next;
            i++;
        }
        return dummy.next; // 注意输出的是dummy.next， 而不是head， head 可能不再是新链表的head
    }
};
```
- [206.反转链表](https://leetcode.cn/problems/reverse-linked-list/description/)
- 快慢指针  
  - [142.环形链表II](https://leetcode.cn/problems/linked-list-cycle-ii/description/)
  - [141.环形链表](https://leetcode.cn/problems/linked-list-cycle/)



