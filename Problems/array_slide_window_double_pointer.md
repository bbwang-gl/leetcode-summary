- [数组](#数组)
  - [介绍](#介绍)
  - [左右两个方向扫描](#左右两个方向扫描)
    - [135. 分发糖果](#135-分发糖果)
    - [42. 接雨水](#42-接雨水)
    - [同类题](#同类题)
  - [数组原地处理](#数组原地处理)
    - [26. 删除有序数组中的重复项](#26-删除有序数组中的重复项)
    - [80. 删除有序数组中的重复项 II](#80-删除有序数组中的重复项-ii)
    - [27. 移除元素](#27-移除元素)
- [双指针](#双指针)
  - [125. 验证回文串](#125-验证回文串)
  - [双指针同类题](#双指针同类题)
- [滑动窗口](#滑动窗口)
  - [代码模版：](#代码模版)
  - [209. 长度最小的子数组](#209-长度最小的子数组)
  - [3. 无重复字符的最长子串](#3-无重复字符的最长子串)
  - [30. 串联所有单词的子串](#30-串联所有单词的子串)


### 数组

####  介绍
数组一般只能从左到右或者从右到左扫描， 有时候需要结合两个方向扫描； 有时候需要两个指针完成从左到右扫描； 某些时候扫描不是步进++， 而是jump。
#### 左右两个方向扫描
##### [135. 分发糖果](https://leetcode.cn/problems/candy/description/)

```cpp
class Solution {
/*
candy[i] 需要同时满足 左右两边的性质， 如果 ratings[i] > ratings[i-1]; 则 candy[i] > candy[i-1];
同时右边也是， 如果 ratings[i] > ratings[i+1]; 则 candy[i] > candy[i+1];
从左到右扫描数组， 使得 candy 满足左边的性质；
从右到左再扫描一边数组， 使得candy在上一次扫描的基础上满足右边的性质, 。
*/

public:
    int candy(vector<int>& ratings) {
        if (ratings.empty()) {
            return 0;
        }
        int n = ratings.size();
        vector<int> candy(n, 1);
        for (int i = 1; i < n; i++) {
            if (ratings[i] > ratings[i-1]) {
                candy[i] = candy[i-1] + 1;
            }
            // cout << "left: i " << i << ", candy[i] " << candy[i] << endl; 
        }
        int ret = candy.back();
        // cout << "i " << n-1 << ", candy[i] " << candy[n-1] << ", ret " << ret << endl; 
        for (int i = n - 2; i >= 0; --i) {
            if (ratings[i] > ratings[i+1]) {
                candy[i] = max(candy[i], candy[i+1] +1); 
            } 
            ret += candy[i];
            // cout << "i " << i << ", candy[i] " << candy[i] << ", ret " << ret << endl; 
        }
        return ret;
    }
};
```
##### [42. 接雨水](https://leetcode.cn/problems/trapping-rain-water)

```cpp
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        vector<int> max_r(n, 0);
        vector<int> max_l(n, 0);
        int max_v = 0;
        for (int i = 0; i < n; i++) {
            max_l[i] = max_v;
            max_v = max(max_v, height[i]);
        }
        max_v = 0;
        for (int i = n-1; i >= 0; i--) {
            max_r[i] = max_v;
            max_v = max(max_v, height[i]);
        }
        int ret = 0;
        for (int i = 0; i < n; i++) {
            ret += max(min(max_l[i], max_r[i]) - height[i], 0);
        }
        return ret;
    }
};
```
##### 同类题
[238. 除自身以外数组的乘积](https://leetcode.cn/problems/product-of-array-except-self/description)


#### 数组原地处理
* 一个指针指向新数组的end/back， 一个指针指向还未处理的数据。
* 两个或多个指针在原数组中划分好不同的数据区域， 或者待处理区域。

##### [26. 删除有序数组中的重复项](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/description/)

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int i = 1; // work_pointer
        int left = 0; // new arry back pointer
        for (; i < nums.size(); i++) {
            if (nums[left] != nums[i]) {
                nums[++left] = nums[i];
            }
        }
        nums.resize(left + 1);
        return left + 1;
    }
};
```

##### [80. 删除有序数组中的重复项 II](https://leetcode.cn/problems/remove-duplicates-from-sorted-array-ii/description/)

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if (nums.size() <= 2) {
            return nums.size();
        }
        int left_0 = 0 ; // new_array_end -1 
        // right is work pointer
        for (int right = 2; right < nums.size(); right++) {
            if (nums[right] != nums[left_0]) {
                nums[left_0 + 2] = nums[right];
                left_0++;
            }
        }
        return left_0 + 2;
    }
};
```
##### [27. 移除元素](https://leetcode.cn/problems/remove-element/description/)
```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        /*
        // 0 ~ left-1 存放预期结果数据， left 是end 指针
        // left ～ right 是待处理数据的区域
        // right +1  ～ n 存放 == val 的数据
        // right 是 work 指针
        */
        int left = 0; // 
        int right = nums.size(); // 
        
        // step 1： 把 nums[left] == val 的 用nums[right -1] 替换；
        // step 2：如果nums[right -1] == val, --right， 继续找right， 执行step 1
        while (left < right) {
            if (nums[left] == val) {
                nums[left] = nums[right - 1];
                right--;
            } else {
                left++;
            }
        }
        return left;
    }
};
```

### 双指针

#### [125. 验证回文串](https://leetcode.cn/problems/valid-palindrome/description/)

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int left=0;
        int right= s.size()-1;
        int len =s.size();
        
        //lowercase(s);
        while(left< right)
        {
            while(left < len && !isalnum(s[left]) )
                ++left;
            while(right >= 0 && !isalnum(s[right]) )
                --right;
            if(left>=len || right<0 || left>=right)
                break;
            if(tolower(s[left])!= tolower(s[right]) )
                return false;
            else
            {
                ++left;
                --right;
            }
        }
        return true;
    }
};

```

#### 双指针同类题
* [392. 判断子序列](https://leetcode.cn/problems/is-subsequence/)
* [两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/descriptio)
* [15. 三数之和](https://leetcode.cn/problems/3sum/description/)
* [11. 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/description/) 


### 滑动窗口
滑动窗口一般用来解决数组/字符串内的子串/子数组问题。 
在数组中， 要求一段子数组满足某种要求。 在求解的过程中， 我们可以利用上一个窗口的计算结果， 在新窗口中只需更新 right0 到right1 变化的部分以及 left0到left1 变化的部分， 比如假设 [left0, right0] 对应的是状态 State0， 而[left1, right1] 对应的是状态State2，则 State2 = State1 + State[right0, right1] - State[left0, left1]. 
#### 代码模版：
0. 定义窗口的check已经需要维护的状态。 
1.  初始化， 有时候需要先初始化一个 window，其他情况下可以在 step 1. 循环中建立第一个window.
2. 写一个循环， 用来移动right和left 边界：
3.  在循环内维护状态和另一边界的移动如left; 
4. 注意边界情况， left = 0 或者 right = length 是否需要单独。 



#### [209. 长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/description/) 

```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
    	  // 状态： ret， subSum
        int ret = nums.size() + 1;
        int subSum = 0;
        for (int left = 0, right = 0; right < nums.size(); right++) {
            subSum += nums[right];
            while (subSum >= target && left <= right) {
                ret = min(right - left + 1, ret);
                subSum -= nums[left];
                left++;
            }
        }
        return ret <= nums.size() ? ret : 0;
    }
};
```
#### [3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/submissions/)
```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_map<char, int> cmap;
        int ret = 0;
        int i = 0, j = 0;
        for (; j < s.size(); j++) {
            if (cmap.count(s[j]) && cmap[s[j]] >= i) {
                ret = max(ret,  j - i);
                i = cmap[s[j]] + 1;  
            }
            cmap[s[j]] = j;
        }
        ret =  max(ret, j - i);
        return ret;
    }
};
```

#### [30. 串联所有单词的子串](https://leetcode.cn/problems/substring-with-concatenation-of-all-words/)
```cpp
class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        vector<int> res;
        int n = s.size();

        // 边缘情况
        if (n <= 0 || words.empty())
        {
            return res;
        }

        // 单词的大小
        int wordSize = words[0].size();
        // 单词数量
        int numWords = words.size();

        // words的单词到次数的映射
        unordered_map<string, int> targetMap;
        for (string& word : words)
        {
            ++targetMap[word];
        }

        
        // 起点以 one_word来偏移，这样子就可以覆盖所有的情况
        for (int i = 0; i < wordSize; ++i)
        {
            // 记录当前统计的窗口的left和 right， 以及已匹配的单词个数 cnt
            int left = i;
            int right = i;
            int cnt = 0;

            // 当前窗口的映射表
            unordered_map<string, int> currMap;

            // 窗口移动
            while (right + wordSize <= n)
            {
                // 提取以right为边界的 wordSize为大小的单词
                string currWord = s.substr(right, wordSize);
                right += wordSize;

                if (targetMap.find(currWord) != targetMap.end())
                {
                    // 存在该单词
                    ++currMap[currWord];
                    ++cnt;
                    // 需要检查数量是否超过，超过则要左移left来缩小窗口
                    while (currMap[currWord] > targetMap[currWord])
                    {
                        string ts = s.substr(left, wordSize);
                        left += wordSize;
                        --cnt;
                        --currMap[ts];
                    }
                    if (cnt == numWords)
                    {
                        res.push_back(left);
                    }
                }
                else
                {
                    // 不存在单词
                    left = right;
                    cnt = 0;
                    currMap.clear();
                }
            }
        }

        return res;
    }
};
/**
作者：ffreturn
链接：https://leetcode-cn.com/problems/substring-with-concatenation-of-all-words/solution/cji-hu-shuang-bai-de-hua-dong-chuang-kou-6zch/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
**/
```
[3381. 长度可被 K 整除的子数组的最大元素和](https://leetcode.cn/problems/maximum-subarray-sum-with-length-divisible-by-k/)
```cpp 
class Solution {
public:
    long long maxSubarraySum(vector<int>& nums, int k) {
        int n = nums.size();
        long long prefixSum = 0, maxSum = LONG_LONG_MIN;
        vector<long long> kSum(k, 0);
        for (int i = 0; i < n; i++) {
            prefixSum += nums[i]; 
            if (i < k -1) {
                kSum[i] = prefixSum;
            } else {
                maxSum = max(maxSum, prefixSum - kSum[i % k]);
                kSum[i % k] = min(kSum[i % k], prefixSum);
            }
            
        }
        return maxSum;
    }
};
/*
对于滑动窗口的题型， 注意和 取模 类型的题结合使用，以及前缀和。
*/
```

