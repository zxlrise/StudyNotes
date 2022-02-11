## LeetCode 热题 HOT 100 （5）

### [494. 目标和](https://leetcode-cn.com/problems/target-sum/)

**思路**

**(动态规划， 01背包)**    $O(n * s)$

**状态表示：**`f[i][j]`表示从`nums`数组的前`i`个数中选，且总和是`j`的方案数。

**状态计算：** 

对于最后一个选择的数字`nums[i]`，我们可以取正也可以取负，因此有两种方案：

- 取正，则`f[i][j] = f[i - 1][j - nums[i]]`；
- 取负，则`f[i][j] = f[i - 1][j + nums[i]]`;

两种方案数相加，**状态转移方程为** `f[i][j] =  f[i - 1][j - nums[i]] + f[i - 1][j + nums[i]] `。

**实现细节：**

由于`-1000 <= target <= 1000`，因此`j`可能会取到负数，为了避免负数，在这里我们需要加上偏移量`Offset`。

**初始化：** `f[0][0] = 1`，从前`0`个数中选，和为`0`的方案数为`1`。

**时间复杂度分析：** 状态数量为$O(n * s)$，状态计算为$O(1)$，故总的时间复杂度为$O(n * s)$。 

**c++代码**

```c++
class Solution {
public:
    // 动态规划
    int findTargetSumWays(vector<int>& nums, int target) {
        int n = nums.size(), Offset = 1000;  // offset为偏移量
        vector<vector<int>> f(n + 1, vector<int>(2000 + 10));
        f[0][Offset] = 1;  //对应f[0][0] = 1, 前0个数，和为0的方案数为1
        for(int i = 1; i <= n; i++){
            for(int j = - 1000; j <= 1000; j++){
                if(j - nums[i - 1] >= -1000)
                    f[i][j + Offset] += f[i - 1][j + Offset - nums[i - 1]];
                if(j + nums[i - 1] <= 1000)
                    f[i][j + Offset] += f[i - 1][j + Offset + nums[i - 1]];    
            }
        }
        return f[n][target + Offset];
    }
};
```

### [538. 把二叉搜索树转换为累加树](https://leetcode-cn.com/problems/convert-bst-to-greater-tree/)

**思路**

**(二叉搜索树，递归)**   $O(n)$

本题中要求我们将每个节点的值修改为原来的节点值加上所有大于它的节点值之和。这样我们只需要反序中序遍历该二叉搜索树，记录过程中的节点值之和，并不断更新当前遍历到的节点的节点值，即可得到题目要求的累加树。

**二叉搜索树的中序遍历是：**左->根->右，此时节点**从小到大**排列。

**反序中序遍历：**右->根->左，此时节点**从大到小**排列。

**时间复杂度分析：**  每个节点仅会被遍历一遍，因此时间复杂度为$O(n)$。

**c++代码**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int sum = 0;
    TreeNode* convertBST(TreeNode* root) {
        if(root != nullptr){
            convertBST(root->right);
            sum += root->val;
            root->val = sum;
            convertBST(root->left);
        }
        return root;
    }
};
```

### [543. 二叉树的直径](https://leetcode-cn.com/problems/diameter-of-binary-tree/)

**思路**

**(递归，树的遍历）** $O(n)$ 

在这道题目中，路径是指从树中某个节点开始，沿着树中的边走，走到某个节点为止，路过的所有边的个数。**直径指的是二叉树路径长度中的最大值。** 

对于一棵树，我们可以将其划分为很多的子树，如下图所示，虚线矩形围起来的子树。我们把这颗子树的蓝色节点称为该子树最高节点。用最高节点可以将整条路径分为两部分：从该节点向左子树延伸的路径，和从该节点向右子树延伸的路径。 

<img src="LeetCode 热题 HOT 100 （5）.assets/image-20220119120400876.png" alt="image-20220119120400876" style="zoom:50%;" />

因此对于任何一棵树，它里面的任何一条路径一定存在一个最高点。所以只要枚举树里面的每个节点作为最高点，然后比较以该节点作为最高点的左子树和右子树的最大高度之和，取其中的最大值，就是**二叉树的直径**。 

**递归函数的返回值定义为当前这颗子树的最大高度。** 

**递归过程：**

<img src="LeetCode 热题 HOT 100 （5）.assets/image-20220119120624149.png" alt="image-20220119120624149" style="zoom:50%;" />

1、首先递归左右子树，求出左右子树的最大高度，我们记为`lh`，`rh`。

2、枚举树里面的每个节点作为最高点，然后比较以该节点作为最高点的左子树和右子树的最大高度之和（`lh` + `rh`），取其中的最大值。

3、具体实现细节看代码。

**时间复杂度分析：** $O(n)$，其中 $n$ 为二叉树的节点数，即遍历一棵二叉树的时间复杂度，每个结点只被访问一次。

**c++代码**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int res = 0;
    int diameterOfBinaryTree(TreeNode* root) {
        if(!root) return 0;
        dfs(root);
        return res;
    }

    int dfs(TreeNode* root){
        if(!root) return 0;
        int lh = dfs(root->left), rh = dfs(root->right);
        res = max(res, lh + rh);
        return max(lh, rh) + 1;
    }
};
```

### [560. 和为 K 的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

**思路**

**(前缀和，哈希)**  $O(n)$ 

- 1、对原数组求前缀和，用一个`unordered_map` 哈希表记录每个前缀和出现的次数。
- 2、枚举区间的右端点，对于每一个右端点，都去哈希表找一下有多少个左端点符合。

**时间复杂度分析：** 每个位置仅遍历一次，故总时间复杂度为 $O(n)$。

**c++代码**

```c++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int sum = 0;  // 前缀和
        int res = 0;
        unordered_map<int, int>hash;
        hash[0] = 1;  // 前缀和为0，在初始化时出现了一次 
        for(int i = 0; i < nums.size(); i++) {
            sum += nums[i];
            res += hash[sum - k]; // 对于每一个右端点，都去找一下有多少个左端点符合
            hash[sum]++;  // 当前前缀和的次数+1
        }
        return res;
    }
};
```

### [581. 最短无序连续子数组](https://leetcode-cn.com/problems/shortest-unsorted-continuous-subarray/)

**思路**

**(排序)**  $O(nlogn)$ 

- 1、将原数组拷贝一份，然后对拷贝后的数组排序。
- 2、对比原数组和排序后的数组，除去前半部分和后半部分相同的数字后，剩余数字的长度就是答案。

**c++代码**

```c++
class Solution {
public:
    int findUnsortedSubarray(vector<int>& nums) {
        int n = nums.size();
        vector<int> a(nums);
        sort(a.begin(), a.end());
        int i = 0, j = n - 1;
        while(i < n && a[i] == nums[i])  i++;
        while(j >= 0 && a[j] == nums[j]) j--;
        return max(0, j - i + 1);
    } 
};
```

原数组满足：**有序 + 无序 + 有序** 

递增有序的最左边界满足：

- 1、从小到大排序
- 2、左边最后一个元素 <= 右侧最小值

**具体过程：**

- 1、遍历数组找到左边保持升序的最后一个点的位置`l`,和从右向左看保持降序的最后一个点的位置`r`。
- 2、从`l + 1`的位置向右扫描，如果遇到有比`nums[l]`小的元素，说明最起码`l`不在正确位置上，则`l--`。
- 3、从`r - 1`的位置向左扫描，如果遇到有比`nums[r]`大的元素，说明最起码`nums[r]`不在正确的位置上，`r ++`。
- 4、最后返回`r - l - 1`。

**时间复杂度分析：**   $O(n)$。

**c++代码**

```c++
class Solution {
public:
    int findUnsortedSubarray(vector<int>& nums) {
        int n = nums.size();
        int l = 0, r = n - 1;
        while(l + 1 < n && nums[l + 1] >= nums[l])  l++;
        if(l == r) return 0;
        while(r - 1 >= 0 && nums[r - 1] <= nums[r]) r--;
        for(int i = l + 1; i < n; i++){
            while(l >= 0 && nums[l] > nums[i]) l--;
        }
        for(int i = r - 1; i >= 0; i--){
            while(r < n && nums[r] < nums[i]) r++;
        }
        return r - l - 1;
    }
};
```

### [617. 合并二叉树](https://leetcode-cn.com/problems/merge-two-binary-trees/)

**思路**

**(递归)**  $O(n + m)$

我们以第一颗树为基准，将第二颗树往第一颗树上加。

**具体过程如下：** 

- 1、从根节点开始，如果`root1 `和`root2`都为空，则返回空。
- 2、如果`root1`不为空，`root2`为空，则返回`root1`，同理如果`root2`不为空，`root1`为空，则返回`root2`。
- 3、如果`root1 `和`root2`均不为空，则将节点值累加到`root1`上。
- 4、递归`root1`的左子树和右子树，令 `root1`的左儿子指向递归返回的左子树，`root1`的右儿子指向递归返回的右子树，最后返回 `root1`。

**c++代码**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        if(!root1 && !root2) return nullptr;
        if(root1 && !root2)  return root1;
        if(!root1 && root2)  return root2;
        root1->val += root2->val;
        root1->left = mergeTrees(root1->left, root2->left);
        root1->right = mergeTrees(root1->right, root2->right);
        return root1;
    }
};
```

### [621. 任务调度器](https://leetcode-cn.com/problems/task-scheduler/)

**思路**

**c++代码**

```c++
class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
        unordered_map<char, int> hash;
        for (auto c: tasks) hash[c] ++ ;
        int maxc = 0, cnt = 0;
        for (auto [k, v]: hash) maxc = max(maxc, v);
        for (auto [k, v]: hash)
            if (maxc == v)
                cnt ++ ;
        return max((int)tasks.size(), (maxc - 1) * (n + 1) + cnt);
    }
};
```

### [647. 回文子串](https://leetcode-cn.com/problems/palindromic-substrings/)

**思路** 

**(中心扩展)**  $O(n^2)$

- 1、每次固定回文子串的中间位置，然后向左右开始扩展。
- 2、每次固定后，分为奇数长度和偶数长度两种情况，然后统计答案。

**c++代码** 

```c++
class Solution {
public:
    int countSubstrings(string s) {
        int res = 0;
        for(int i = 0; i < s.size(); i++){
            for(int j = i, k = i; j >= 0 && k < s.size(); j--, k++){   //奇数长度
                if(s[j] != s[k]) break;
                else res++;
            }
            for(int j = i, k = i + 1; j >= 0 && k < s.size(); j--, k++){//偶数长度
                if(s[j] != s[k]) break;
                else res++;
            }
        }
        return res;
    }
};
```

**(动态规划)**   $O(n^2)$

**状态表示：**`f[i][j]`表示字符串`s`在区间`[i, j]`的子串是否为一个回文串。`f[i][j]`有两种状态，如果是，则`f[i][j] = ture`，如果不是，则`f[i][j] = false`。

**状态计算：**

对于字符串`s`的区间子串`s[i,,,j]`，我们去判断`s[i]`和`s[j]`是否相等：

- 1、如果`s[i] == s[j]`，那么此时`f[i][j]`的状态就取决于`f[i + 1][j - 1]`，即`f[i][j] = f[i + 1][j - 1]`。
- 2、如果`s[i] != s[j]`，那么可以肯定`s[i,,,j]`一定不是回文串，即`f[i][j] = false`。

因此**，状态转移方程为：** 

**边界：**

`s[i] == s[j] && (j - i) <= 2`时， `f[i][j] = true`。

**c++代码** 

 ```c++
class Solution {
public:
    int countSubstrings(string s) {
        int n = s.size();
        vector<vector<bool>> f(n + 1, vector<bool>(n + 1, false));
        int res = 0;
        for(int i = n - 1; i >= 0; i--){
            for(int j = i; j < n; j++){
                if(s[i] == s[j]) f[i][j] = (j - i <= 2) || f[i + 1][j - 1];
                else f[i][j] = false;
                if(f[i][j]) res++;
            }
        }
        return res;
    }
};
 ```

### [739. 每日温度](https://leetcode-cn.com/problems/daily-temperatures/)

**思路**

**(单调栈)**   $O(n)$

利用递减单调栈找到右边大于栈顶的元素，弹出栈顶直到满足单调递减，同时记录弹出元素与当前`i`的距离，保存在`res`数组中即是答案。

![38262_3c970524a3-单调递减栈的规律](LeetCode 热题 HOT 100 （5）.assets/38262_3c970524a3-单调递减栈的规律.jpg)

**时间复杂度分析：** 每个元素仅会进栈出栈一次，所以时间复杂度为$O(n)$。

**c++代码**

```c++
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& t) {
        vector<int> res(t.size());
        stack<int> st;
        for(int i = 0; i < t.size(); i++){
            while(st.size() && t[st.top()] < t[i]){
                res[st.top()] = i - st.top();
                st.pop();
            }
            st.push(i);
        }
        return res;
    }
};
```

