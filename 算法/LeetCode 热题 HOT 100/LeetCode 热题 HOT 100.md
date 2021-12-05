## LeetCode 热题 HOT 100

### [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

**思路**

**(暴力枚举)** $O(n^2)$

两重循环枚举下标` i,j`，然后判断 `nums[i]+nums[j]` 是否等于 `target`。

**(哈希表)** $O(n)$

使用C++中的哈希表`unordered_map<int, int> hash`

- 用哈希表存储前面遍历过的数，当枚举到当前数时，若哈希表中存在`target - nums[i]`的元素，则表示已经找到符合条件的两个数。
- 若不存在`target - nums[i]`的元素则枚举完当前数再把当前数放进哈希表中

**时间复杂度：**由于只扫描一遍，且哈希表的插入和查询操作的复杂度是 $O(1)$，所以总时间复杂度是 $O(n)$.

**c++代码**

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> hash;
        for(int i = 0; i < nums.size(); i++){
            if(hash.count(target - nums[i])){
                return {i, hash[target - nums[i]]};
            }
            hash[nums[i]] = i;
        }
        return {};
    }
};
```

### [2. 两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

**思路**   $O(n)$。

**(模拟)**   

这是道模拟题，模拟我们小时候列竖式做加法的过程：

1. 从最低位至最高位，逐位相加，如果和大于等于`10`，则保留个位数字，同时向前一位进`1`。
2. 如果最高位有进位，则需在最前面补`1`。

**具体实现**

1. 同时从头开始枚举两个链表，将`l1`和`l2`指针指向的元素相加存到`t`中，再将`t % 10 `的元素存到`dummy`链表中，再`t / 10`去掉存进去的元素，`l1`和`l2`同时往后移动一格。
2. 当遍历完所有元素时，如果`t != 0`，再把`t`存入到`dummy`链表中。

<img src="../LeetCode 热题 HOT 100.assets/image-20211205135046436.png" alt="image-20211205135046436" style="zoom:50%;" />

做有关链表的题目，有个常用技巧：添加一个虚拟头结点：`ListNode *head = new ListNode(-1)`;，可以简化边界情况的判断。

**时间复杂度：**由于总共扫描一遍，所以时间复杂度是 $O(n)$。

**c++代码**

```c++
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
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* dummy = new ListNode(-1);
        ListNode* cur = dummy;
        int t = 0;
        while(l1 || l2){
            if(l1) t += l1->val, l1 = l1->next;
            if(l2) t += l2->val, l2 = l2->next;
            cur = cur->next = new ListNode(t % 10); 
            t /= 10;
        }
        if(t) cur->next = new ListNode(t);
        return dummy->next;
    }
};
```

### [3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

**思路**

**(双指针扫描)** $O(n)$

定义两个指针 $i,j(i<=j)$，表示当前扫描到的子串是 $[i,j]$ (闭区间)。扫描过程中维护一个哈希表`unordered_map <chat,int>hash`，表示 $[i,j]$中每个字符出现的次数。

线性扫描时，每次循环的流程如下：

- 1.指针`j` 向后移一位, 同时将哈希表中 `s[j`] 的计数加一，即` hash[s[j]]++`;
- 2.假设 `j `移动前的区间 `[i,j]`​中没有重复字符，则 `j` 移动后，只有 `s[j]`可能出现`2`次。因此我们不断向后移动 `i`，直至区间 `[i,j]`中 `s[j]` 的个数等于`1`为止；
- 3.当确保`[i, j]`中不存在重复元素时，更新`res`；

**时间复杂度分析：**由于 `i`，`j` 均最多增加`n`次，且哈希表的插入和更新操作的复杂度都是 $O(1)$，因此，总时间复杂度 $O(n)$。

**c++代码**

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_map<char, int> hash;
        int res = 0;
        for(int j = 0, i = 0; j < s.size(); j++){
            hash[s[j]]++;
            while(hash[s[j]]> 1){
                hash[s[i]]--;
                i++;
            }
            res = max(res, j - i + 1);
        }
        return res;
    }
};
```

### [4. 寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

**思路**

**(递归)**   $O(log(n+m))$ 

找出两个正序数组的**中位数**等价于找出两个正序数组中的**第k小数**。如果两个数组的大小分别为`n`和`m ` ，那么第 `k = (n + m)/2` 小数就是我们要求的中位数。

**如何寻找第k小的元素？** 

**过程如下：**

1、考虑一般情况，我们在 `nums1`和`nums2`数组中各取前`k/2`个元素  

<img src="../LeetCode 热题 HOT 100.assets/image-20210722154205811.png" alt="image-20210722154205811" style="zoom:50%;" />

我们默认`nums1`数组比`nums2`数组的有效长度小 。`nums1`数组的有效长度从`i`开始，`nums2`数组的有效长度从`j`开始，其中`[i,si - 1]`是`nums1`数组的前`k / 2`个元素，`[j, sj - 1]`是`nums2`数组的前` k / 2`个元素。

2、接下来我们去比较`nums1[si - 1]`和`nums2[sj - 1]`的大小。

- 如果`nums1[si - 1] > nums2[sj - 1]` ，则说明 `nums1` 中取的元素过多，`nums2` 中取的元素过少。因此`nums2` 中的前 `k/2`个元素一定都小于等于第 `k` 小数，即`nums2[j,sj-1]`中元素。我们可以舍去这部分元素，在剩下的区间内去找第`k - k / 2`小的元素，也就是说第`k`小一定在`[i,n]`与`[sj,m]`中。
- 如果`nums1[si - 1] <= nums2[sj - 1]`，同理可说明`nums2`中的前 `k/2`个元素一定都小于等于第 `k` 小数，即`nums1[i,si-1]`中元素。我们可以舍去这部分元素，在剩下的区间内去找第`k - k / 2`小的元素，也就是说第`k`小一定在`[si,n]`与`[j,m]`中。

3、递归过程`2`，每次可将问题的规模减少一半，最后剩下的一个数就是我们要找的第`k`小数。 

**递归边界：** 

- 当`nums1`数组为空时，我们直接返回`nums2`数组的第`k`小数。
- 当`k == 1`时，且两个数组均不为空，我们返回两个数组首元素的最小值，即`min(nums1[i], nums2[j])`。

**奇偶分析：** 

- 当两个数组元素个数的总和`total`为偶数时，找到第`total / 2`小`left`和第`total / 2 + 1`小`right`，结果是`(left + right / 2.0)`。

- 当`total`为奇数时，找到第`total / 2 + 1`小，即为结果。

**时间复杂度分析：** $k=(m+n)/2$，且每次递归 $k$ 的规模都减少一半，因此时间复杂度是$O(log(m+n))$.

**c++代码**

```c++
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int tot = nums1.size() + nums2.size();
        if(tot % 2 == 0){
            int left = find(nums1, 0, nums2, 0, tot / 2);
            int right =find(nums1, 0, nums2, 0, tot / 2 + 1);
            return (left + right) / 2.0;
        }else{
            return find(nums1, 0, nums2, 0, tot / 2 + 1);
        }
    }

    int find(vector<int>& nums1,int i, vector<int>& nums2, int j, int k){
        if(nums1.size() - i > nums2.size() - j) return find(nums2, j, nums1, i, k);
        if(k == 1){
            //当第一个数组已经用完
            if(i == nums1.size()) return nums2[j];
            else return min(nums1[i], nums2[j]);
        }
        //当nums1数组为空时，我们直接返回nums2数组的第k小数。
        if (nums1.size() == i) return nums2[j + k - 1];
        int si = min((int)nums1.size(), i + k / 2), sj = j + k - k / 2;
        if(nums1[si - 1] > nums2[sj - 1]){
            return find(nums1, i, nums2, sj, k - (sj - j));
        }else{
            return find(nums1, si, nums2, j, k - (si - i));
        }
    }
};
```

### [5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

**思路**

**(双指针)** $O(n^2)$

- 1、枚举数组中的每个位置`i`，从当前位置开始向两边扩散
- 2、当回文子串的长度是奇数时，从`i - 1`,`i + 1`开始往两边扩散
- 3、当回文子串的长度是偶数时，从`i`，`i + 1`开始往两边扩散
- 4、找到以`i`为中心的最长回文子串的长度，若存在回文子串比以前的长，则更新答案。

**图示:**

<img src="../LeetCode 热题 HOT 100.assets/image-20210529111940128.png" alt="image-20210529111940128" style="zoom:50%;" />

**时间复杂度分析：**枚举数组中的每个位置`i`需要$O(n)$的时间复杂度，求回文子串需要$O(n)$的时间复杂度，因此总的时间复杂度为$O(n^2)$。

**c++代码 **

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        string res;
        for(int i = 0; i < s.size(); i++){
            int l = i, r = i + 1;  //回文串长度为偶数
            while(l >= 0 && r < s.size() && s[l] == s[r]) l--, r++;
            if(res.size() < r - l - 1) res = s.substr(l + 1, r - l - 1);
            l = i - 1, r = i + 1;  //回文串长度为奇数
            while(l >= 0 && r < s.size() && s[l] == s[r]) l--, r++;
            if(res.size() < r - l - 1) res = s.substr(l + 1, r - l - 1);
        }
        return res;
    }
};
```

### [11. 盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)

**思路**

**(双指针扫描)**  $O(n)$

**过程如下：** 

1、定义两个指针`i`和`j`，分别表示容器的左右边界，初始化`i = 0`， `j = h.size() - 1`，容器大小为`min(i, j)*(j  - i)`。

2、若`h[i] < h[j]`，则`i++`，否则`j--`，每次迭代更新最大值。

**证明：** 

容器大小由短板决定, 移动长板的话, 水面高度不可能再上升, 而宽度变小了, 所以只有通过移动短板, 才有可能使水位上升。

**时间复杂度分析：** 两个指针总共扫描 $n$ 次，因此总时间复杂度是 $O(n)$。

**c++代码**

```c++
class Solution {
public:
    int maxArea(vector<int>& h) {
        int res = 0;
        for(int i = 0, j = h.size() - 1; i < j;){
            res = max(res, min(h[i], h[j])*(j - i));
            if(h[i] < h[j]) i++;
            else j--;
        }
        return res;
    }
};
```

