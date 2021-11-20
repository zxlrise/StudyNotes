## 剑指offer

### [剑指 Offer 03. 数组中重复的数字](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

**思路** 

(数组遍历) $O(n)$
首先遍历一遍数组，如果存在某个数不在0到n-1的范围内，则返回-1。

下面的算法的主要思想是把每个数放到对应的位置上，即让` nums[i] = i`。

从前往后遍历数组中的所有数，假设当前遍历到的数是 `nums[i]=x`，那么：

如果`x != i && nums[x] == x`，则说明 `x` 出现了多次，直接返回`x`即可；如果`nums[x] != x`那我们就把 `x`交换到正确的位置上，即 `swap(nums[x], nums[i])`，交换完之后如果`nums[i] != i`，则重复进行该操作。由于每次交换都会将一个数放在正确的位置上，所以`swap`操作最多会进行 `n` 次，不会发生死循环。
循环结束后，如果没有找到任何重复的数，则返回`-1`。

**时间复杂度分析:**  每次`swap`操作都会将一个数放在正确的位置上，最后一次`swap`会将两个数同时放到正确位置上，一共只有 `n` 个数和 `n` 个位置，所以`swap`最多会进行 `n−1`次。所以总时间复杂度是 $O(n)$。

**c++代码**

```c++
class Solution {
public:
    int findRepeatNumber(vector<int>& nums) {
        for(int i = 0; i < nums.size(); i++){
            while(nums[i] != nums[nums[i]]) swap(nums[i], nums[nums[i]]);
            if(nums[i] != i) return nums[i];
        }
        return -1;
    }
};
```

### [剑指 Offer 04. 二维数组中的查找](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

**(单调性扫描)** $O(n+m)$

在`m x n`矩阵 `matrix`中我们可以发现一个性质：对于每个子矩阵右上角的数`x`，`x`左边的数都小于等于`x`，`x`下边的数都大于`x`。 

<img src="剑指offer.assets/image-20210809224136112.png" alt="image-20210809224136112" style="zoom:50%;" />

因此我们可以从整个矩阵的右上角开始枚举，假设当前枚举的数是 `x`：

- 如果 `x` 等于`target`，则说明我们找到了目标值，返回`true`； 
- 如果 `x`小于`target`，则 `x`左边的数一定都小于`target`，我们可以直接排除当前一整行的数；
- 如果` x` 大于`target`，则 `x` 下边的数一定都大于`target`，我们可以直接排序当前一整列的数；

排除一整行就是让枚举的点的横坐标加一，排除一整列就是让纵坐标减一。当我们排除完整个矩阵后仍没有找到目标值时，就说明目标值不存在，返回`false`。

**具体过程如下：** 

- 1、初始化`i = 0`, `j = matrix[0].size() - 1`。
- 2、如果`matrix[i][j] == target`，返回`true`。
- 3、如果`matrix[i][j] < target`，`i++`，排除一行。
- 4、如果`matrix[i][j] > target`，`j--`，排除一列。
- 5、如果出界还未找到`target`，则返回`false`。

**时间复杂度分析：** 每一步会排除一行或者一列，矩阵一共有 $n$ 行，$m$ 列，所以最多会进行$ n+m $步。所以时间复杂度是 $O(n+m)$。

### [剑指 Offer 05. 替换空格](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)

**思路**

**(线性扫描)** $O(n)$

这个题在C++里比较好做，我们可以从前往后枚举原字符串：

- 1、如果遇到空格，则在string类型的答案中添加 `"%20"`；
- 2、如果遇到其他字符，则直接将它添加在答案中；

**时间复杂度分析:** 原字符串只会被遍历常数次，所以总时间复杂度是 $O(n)$。

**c++代码**

```c++
class Solution {
public:
    string replaceSpace(string s) {
        string res;
        for(int i = 0; i < s.size(); i++){
            if(s[i] == ' ') res += "%20";
            else res += s[i];
        }
        return res;
    }
};
```

### [剑指 Offer 06. 从尾到头打印链表](https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)

**思路**

**(遍历链表)** $O(n)$

单链表只能从前往后遍历，不能从后往前遍历，因此:

- 1、我们先从前往后遍历一遍输入的链表，将结果记录在答案数组中。
- 2、最后再将得到的数组逆序即可。 

**时间复杂度分析：** 链表和答案数组仅被遍历了常数次，所以总时间复杂度是 $O(n)$。

**c++代码**

```c++
class Solution {
public:
    vector<int> reversePrint(ListNode* head) {
        vector<int> res;
        ListNode* cur = head;
        while(cur){
            res.push_back(cur->val);
            cur = cur->next;
        }
        reverse(res.begin(), resa.end());
        return res;
    }
};
```

### [剑指 Offer 07. 重建二叉树](https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/)

**思路**

**(递归)** $O(n)$ 

二叉树前序遍历的顺序为：根左右

二叉树中序遍历的顺序为：左根右

递归建立整棵二叉树：先创建根节点，然后递归创建左右子树，并让指针指向两棵子树。

我们画个图来说明，二叉树的前序和中序遍历。

**二叉树：**

<img src="剑指offer.assets/image-20210626103011685.png" alt="image-20210626103011685" style="zoom: 50%;" />

**前序遍历：**

<img src="剑指offer.assets/image-20210626104344392.png" alt="image-20210626104344392" style="zoom: 50%;" />

**中序遍历：**

<img src="剑指offer.assets/image-20210626112255731.png" alt="image-20210626112255731" style="zoom: 50%;" />

**具体步骤如下：** 

- 1、先利用前序遍历找根节点：前序遍历的第一个数，就是根节点的值；
- 2、在中序遍历中找到根节点的位置 `pos`，则 `pos` 左边是左子树的中序遍历，右边是右子树的中序遍历；
- 3、假设左子树的中序遍历的长度是 `k`，则在前序遍历中，根节点后面的 `k` 个数，是左子树的前序遍历，剩下的数是右子树的前序遍历；
- 4、有了左右子树的前序遍历和中序遍历，我们可以先递归创建出根节点，然后再递归创建左右子树，再将这两颗子树接到根节点的左右位置；

**细节1：**如何在中序遍历中对根节点快速定位？

一种简单的方法是直接扫描整个中序遍历的结果并找出根节点，但这样做的时间复杂度较高。我们可以考虑使用哈希表来帮助我们快速地定位根节点。对于哈希映射中的每个键值对，键表示一个元素（节点的值），值表示其在中序遍历中的出现位置。

**细节2：**如何确定左右子树的前序遍历和中序遍历范围？

- 1、根据哈希表找到中序遍历的根节点位置，我们记作`pos` 

- 2、用`pos-il` (`il`为中序遍历左端点) 得到中序遍历的长度`k` ，由于一棵树的前序遍历和中序遍历的长度相等，因此前序遍历的长度也为`k`。有了前序和中序遍历的长度，根据如上具体步骤2，3，我们就能很快确定左右子树的前序遍历和中序遍历范围。如下图所示：

  <img src="剑指offer.assets/image-20210626112109646.png" alt="image-20210626112109646" style="zoom: 50%;" />

  `pl`,`pr`对应一棵子树的前序遍历区间的左右端点， `il`,`ir`对应一棵子树的中序遍历区间的左右端点。

**具体实现看代码。**

**时间复杂度分析：** $ O(n)$，其中 $n$ 是树中的节点个数。  

**c++代码** 

```c++
class Solution {
public:

    unordered_map<int,int> pos;
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int n = preorder.size();
        for(int i = 0; i < n; i++)
            pos[inorder[i]] = i; //记录中序遍历的根节点位置
        return dfs(preorder,inorder,0,n-1,0,n-1);        
    }
    //pl,pr对应一棵子树的前序遍历区间的左右端点
    //il,ir对应一棵子树的中序遍历区间的左右端点
    TreeNode* dfs(vector<int>&pre,vector<int>&in,int pl,int pr,int il,int ir)
    {
        if(pl > pr) return NULL; //左子树为空1
        int k = pos[pre[pl]] - il; // pos[pre[pl]]是中序遍历中根节点位置，k是子树前序和中序遍历的长度
        TreeNode* root = new TreeNode(pre[pl]);
        root->left = dfs(pre,in,pl+1,pl+k,il,il+k-1);  //左子树前序遍历，左子树中序遍历
        root->right = dfs(pre,in,pl+k+1,pr,il+k+1,ir); //右子树前序遍历，右子树中序遍历
        return root;
    }
};
```

### [剑指 Offer 09. 用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)

**思路**

**栈：** 先进后出

**队列：** 先进先出

**`push(x)` :**   直接将x插入主栈`stack1`中。

 **`pop()`：** 此时我们需要弹出最先进入栈的元素，也就是栈底元素。

- 1、在执行删除操作的时候我们首先看下第二个栈是否为空。如果为空，我们将第一个栈里的元素一个个弹出插入到第二个栈里，这样第二个栈里元素的顺序就是待删除的元素的顺序。
- 2、执行删除操作的时候我们直接弹出第二个栈的元素返回即可。

**c++代码**

```c++
class CQueue {
public:
     /**
        两个栈实现队列，栈： 先进后出
                     队列： 先进先出

        push(x) :   直接将x插入主栈stack1中
        pop()   ：
            此时我们需要弹出最先进入栈的元素，也就是栈底元素。
            在执行删除操作的时候我们首先看下第二个栈是否为空。如果为空，
            我们将第一个栈里的元素一个个弹出插入到第二个栈里，这样第二个
            栈里元素的顺序就是待删除的元素的顺序，要执行删除操作的时候我
            们直接弹出第二个栈的元素返回即可。
 
    **/
    stack<int> st1, st2;
    CQueue() {

    }
    void appendTail(int value) {
        st1.push(value);
    }
    
    int deleteHead() {
        if(st2.empty()){
            while(!st1.empty()){
                st2.push(st1.top());
                st1.pop();
            }
        }
        if(st2.empty()) return -1;
        int res = st2.top();
        st2.pop();
        return res;
    }
};
```

### [剑指 Offer 10- I. 斐波那契数列](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)

**思路**

**(递推)** $O(n)$ 

斐波那契数的边界条件是 `F(0)=0` 和 `F(1)=1`。当 `n>1`时，每一项的和都等于前两项的和，因此有如下递推关系：

`F(n)=F(n−1)+F(n−2)` 

边界条件为 `F(0)` 和 `F(1)`。

**时间复杂度分析：**  $O(n)$ 。

**c++代码**

```c++
class Solution {
public:
    int fib(int n) {
        if(n == 0 || n == 1) return n;
        vector<int> f(n + 1);
        f[0] = 0;
        f[1] = 1;
        for(int i = 2; i <= n; i++){
            f[i] = (f[i - 1] + f[i - 2]) % 1000000007;
        }
        return f[n];
    }
};
```

### [剑指 Offer 10- II. 青蛙跳台阶问题](https://leetcode-cn.com/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/)

**思路**

**(递推)** $O(n)$

**分析题目可以发现：**

- 上 1 阶台阶：有`1`种方式。

- 上 2 阶台阶：有`1+1`和`2`两种方式。 

- 上 3 阶台阶：到达第`3`阶的方法总数就是到第`1`阶和第`2`阶的方法数之和。

- 上 n 阶台阶，到达第`n`阶的方法总数就是到第 `(n-1) `阶和第 `(n-2)` 阶的方法数之和。  

因此，定义数组 `f[i]` 表示上`i` 级台阶的方案数，则枚举最后一步是上`1`级台阶，还是上`2`级台阶，所以有：
`f[i] = f[i−1]+f[i−2]`。  

<img src="剑指offer.assets/image-20210706210032020.png" alt="image-20210706210032020" style="zoom: 33%;" />

**时间复杂度分析：**递推状态数$O(n)$，转移时间复杂度是 $O(1)$，所以总时间复杂度是 $O(n)$。 

**c++代码**

```c++
class Solution {
public:
    int numWays(int n) {
        if(n == 0) return 1;
        int mod = 1e9 + 7;
        vector<int> f(n + 2);
        f[1] = 1, f[2] = 2;
        for(int i = 3; i <= n; i++){
            f[i] = (f[i - 1] + f[i - 2]) % mod;
        }
        return f[n];
    }
};
```

### [剑指 Offer 11. 旋转数组的最小数字](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)

**思路**



为了便于分析，我们先将数组中的数画在二维坐标系中，横坐标表示数组下标，纵坐标表示数组数值，如下所示： 

<img src="剑指offer.assets/image-20211115105715857.png" alt="image-20211115105715857" style="zoom:50%;" />

我们发现除了最后水平的一段（黄色水平那段）之外，其余部分满足二分性质：竖直虚线左边的数满足 $nums[i]≥nums[0]$，而竖直虚线右边的数满足$nums[i]< nums[0]$，分界点就是整个数组的最小值。

所以我们**先将最后水平的一段删除**，这样数组就具有了二分性，因此可以二分出最小值的位置。 

**为什么要删除最后水平的一段？**  

由于`nums`数组中可能存在 **重复** 元素值，比如`nums = {2, 2, 0, 1, 2}`，如果我们不将最后水平的一段删除，那么如上图所示，那么竖直虚线左边的数满足 $nums[i]≥nums[0]$，竖直虚线右边的数满足$nums[i]<= nums[0]$，这样就不满足数组的**二段性**了。在二分遇到`nums[0]`这样的特殊值时，无法坚定的选择一边，而二分的条件就是数组满足二段性，即数组的某种性质在左半部分和右半部分**严格互斥。** 

**过程如下：**

- 1、在`[l,r]`区间中，` l = 0`, `r = nums.size() - 1`，我们去二分`< num[0]`的最左边界。
- 2、为了保证二分性，我们先将最后水平的一段删除。
- 3、当`nums[mid] < nums[0]`时，往左边区域找，`r = mid。`。

<img src="剑指offer.assets/image-20210330211317780.png" alt="image-20210330211317780" style="zoom: 33%;" />



- 4、当`nums[mid] >= nums[0]`时，往右边区域找，`l = mid + 1`。 

  <img src="剑指offer.assets/image-20210727163554876.png" alt="image-20210727163554876" style="zoom: 33%;" />

- 5、当`l == r`，只剩下一个数时，就是最小值的位置，我们返回`nums[r]`。

**细节：**

- 当数组完全单调时，第一个数`nums[0]`最小，我们直接返回即可。

**时间复杂度分析：** 二分查找，所以时间复杂度是 $O(logn)$。 

**c++代码**

```c++
class Solution {
public:
    int minArray(vector<int>& nums) {
        int l = 0, r = nums.size() - 1;
        while(l < r && nums[0] == nums[r] ) r--; //去除最后水平的一段
        if(nums[r] >= nums[l]) return nums[0];   //数组单调，直接返回nums[0]
        while(l < r){  //二分 < nums[0]的最左边界
            int mid = l + r >> 1;
            if(nums[mid] < nums[0]) r = mid;
            else l = mid + 1;
        }
        return nums[r];
    }
};
```

### [剑指 Offer 12. 矩阵中的路径](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)

**思路**

**(回溯)** $O(n^2 3^k)$ 

深度优先搜索，我们定义这样一种搜索顺序，即先枚举单词的起点，然后依次枚举单词的每个字母。在这个过程中需要将已经使用过的字母改成一个特殊字母，以避免重复使用字符。

<img src="剑指offer.assets/image-20210803221749369.png" alt="image-20210803221749369" style="zoom:50%;" />

**递归函数设计：**

```c++
bool dfs(vector<vector<char>>& board, string& word,int u,int x,int y)
```

`u`代表当前枚举到了目标单词`word`第`u`个位置。 

`x`，`y`是当前搜索到的二维字符网格的横纵坐标。 

**搜索过程如下：**

- 1、在二维字符网格中枚举每个单词的起点。
- 2、从该起点出发向四周搜索单词`word`，并记录此时枚举到单词`word`的第`u`个位置 ( `u`从`0`开始)。  
- 3、如果当前搜索的位置`(x,y)`的元素`board[x][y] == word[u]`,则继续向四周搜索。
- 4、直到枚举到单词`word`的最后一个字母返回`ture`，否则返回`false`。

**递归边界：** 

- 1、当搜索过程出现当前位置`board[x][y] != word[u]` ，说明当前路径不合法，返回`false`。
- 2、`u == word.size() - 1`,成功搜索到单词末尾，返回`true`。

**实现细节：** 

- 1、搜索过的位置继续搜索下一层时，需要对当前位置进行标识，表示已经搜索

- 2、可以使用偏移数组来简化代码。 

  <img src="剑指offer.assets/image-20210804175416525.png" alt="image-20210804175416525" style="zoom:50%;" />

  

**时间复杂度分析：** 单词起点一共有 $n^2$ 个，单词的每个字母一共有上下左右四个方向可以选择，但由于不能走回头路，所以除了单词首字母外，仅有三种选择。所以总时间复杂度是  $O(n^2 3^k)$ 。 

**c++代码**

```c++
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        for(int i = 0; i < board.size(); i++)
            for(int j = 0; j < board[i].size(); j++)
                if(dfs(board,word,0,i,j)) return true;
        return false;        
    }
    int dx[4] = {-1,0,1,0}, dy[4] = {0,1,0,-1}; //方向数组
    bool dfs(vector<vector<char>>& board, string& word,int u,int x,int y)
    {
        if(board[x][y] != word[u]) return false;
        if(u == word.size() - 1)   return true;
        char t = board[x][y];
        board[x][y] = '.';
        for(int i = 0; i < 4; i++)
        {
            int a = x + dx[i], b = y + dy[i];
            //出界或者走到已经搜索过的位置
            if(a < 0 || a >= board.size() || b < 0 || b >= board[0].size() || board[a][b] == '.')  continue;
            if(dfs(board,word,u+1,a,b)) return true;
        }
        board[x][y] = t;
        return false;
    }
};
```

### [剑指 Offer 13. 机器人的运动范围](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)

**思路**

**c++代码**

```c++
 
```

### [剑指 Offer 19. 正则表达式匹配](https://leetcode-cn.com/problems/zheng-ze-biao-da-shi-pi-pei-lcof/)

**思路**

**(动态规划)**  $O(nm)$

**状态表示：**`f[i][j]` 表示字符串` s` 的前 `i `个字符和字符串 `p` 的前` j` 个字符能否匹配。

**状态计算：**

根据`p[j]` 是什么来划分集合：

- 1、`p[j] != '*' ` ，即`p[j]`是字符, 看`p[j]` 和`s[i]`的关系。如果`p[j] == s[i]`，则需判断 `s`的前`i - 1`个字母 能否和`p`的前`j -1`个字母匹配 ，即`f[i][j] == f[i - 1][j - 1]`，不匹配 , 无法转移。
- 2    `P[j]` 是匹配符:
  - 如果`p[j] == '.' `，则`p[j]` 和 `s[j]`匹配 ，则需判断 `s`的前`i - 1`个字母能否和`p`的前`j -1`个字母匹配 ，即`f[i][j] == f[i - 1][j - 1]`。
  - `p[j]` 为`'*'`，得看`p[j - 1]`和`s[i]`的关系。如果不匹配，即`p[j - 1] ！= s[i]`，那么`'*'`匹配`0`个`p[j - 1]`,则需判断 `s`的前`i`个字母 能否和`p`的前`j - 2`个字母匹配 ，即`f[i][j] == f[i][j - 2]`。如果匹配，即`p[j - 1] == s[i] || p[j - 1] == '.'`，若`'*'`匹配多个`p[j - 1]`,则需判断`s`的前`i - 1`个字母 能否和`p`的前`j`个字母匹配 ，即`f[i][j] == f[i - 1][j]) `。

---------------

<img src="剑指offer.assets/7416_d13e30f0aa-17362169-1494d2d44bb18bfe.png" alt="17362169-1494d2d44bb18bfe.png" style="zoom: 67%;" />

<img src="剑指offer.assets/7416_d610d808aa-17362169-f12d881c769cc544.png" alt="17362169-f12d881c769cc544.png" style="zoom: 80%;" />

--------------------

**总结:**

```c++
f[i][j] == f[i - 1][j - 1], 前提条件为p[j] == s[i] || p[j] == '.'
f[i][j] == f[i][j - 2], 前提条件为p[j] == '*' &&  p[j - 1] != s[i]
f[i][j] == f[i - 1][j], 前提条件为p[j] == '*' && ( p[j - 1] == s[i] || p[j - 1] == '.'）
```

**c++代码**

```c++
class Solution {
public:
    bool isMatch(string s, string p) {
        int n = s.size(), m = p.size();
        vector<vector<bool>>f(n + 1, vector<bool>(m + 1));
        s = ' ' + s;   // 下标从1开始
        p = ' ' + p;
        f[0][0] = true;
        for(int i = 0; i <= n; i++){
            for(int j = 1; j <= m; j++){
                if(j + 1 <= m && p[j + 1] == '*') continue;  // 不去处理*的前一位字符。
                if(p[j] != '*'){
                    if((s[i] == p[j] || p[j] == '.')&& i)
                        f[i][j] = f[i - 1][j - 1];
                }else{
                    if(j >= 2) f[i][j] = f[i][j - 2];  //匹配0个
                    if(i && (p[j - 1] == '.' || s[i] == p[j - 1] ) && f[i - 1][j]) //匹配多个
                        f[i][j] = f[i - 1][j];    
                }
            }
        }
        return f[n][m];
    }
};
```



### [剑指 Offer 41. 数据流中的中位数](https://leetcode-cn.com/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/)

**知识补充：**

https://www.cnblogs.com/wangchaowei/p/8288216.html

https://cloud.tencent.com/developer/article/1616910

**数据结构 - 堆**   

- Heap是一种数据结构具有以下的特点：
  (1）**完全二叉树**；
  (2）heap中存储的值是**偏序**；
- **Min-heap**:   父节点的值小于或等于子节点的值；
- **Max-heap**: 父节点的值大于或等于子节点的值；

<img src="剑指offer.assets/image-20211113224306149.png" alt="image-20211113224306149" style="zoom:50%;" />

**优先队列：** 

priority_queue称为“优先队列”，其底层是用堆实现。在优先队列中，队首元素一定是当前队列中优先级最高的哪一个。

默认的定义优先队列是大根堆，即父节点的值大于子节点的值。

**获取堆顶元素**

> top()：可以获得队首元素（堆顶元素），时间复杂度为O(1)。 与队列不一样的是，优先队列通过top()函数来访问队首元素（堆顶元素）。（队列是通过front()函数和back()函数访问下标）

**入队**

> push(x) ：令x入队，时间复杂度为O(logN)，其中N为当前优先队列中的元素个数。

**出队**

> pop()：   令队首元素（堆顶元素）出队，时间复杂度为O(logN)，其中N为当前优先队列中的元素个数。

**检测是否为空**

> empty()：检测优先队列是否为空，返回true为空，false为非空。时间复杂度为O（1）

**获取元素个数**

> size()：用来获得优先队列中元素的个数，时间复杂度为 O(1)

**案例代码** 

```c++
#include
#include
using namespace std;
int main(){
    priority_queue q;

    //入队
    q.push(3);
    q.push(4);
    q.push(1);

    //通过下标访问元素
    printf("%d\n",q.top());//输出4

    //出队
    q.pop();
    printf("%d\n",q.top());//输出3

    //检测队列是否为空
    if(q.empty() == true) {
        printf("Empty\n");
    } else {
        printf("Not Empty\n");
    }

    //获取长度
    //printf("%d\n",q.size());//输出3
}
```

**基本数据类型的优先级设置**

一般情况下，数字大的优先级更高。（char类型的为字典序最大） 对于基本结构的优先级设置。下面两种优先队列的定义是等价的： 

```c++
priority_queue<int> q; 
priority_queue<int,vector<int>,less<int> > q;
```

如果想让优先队列总是把最小的元素放在队首，需进行以下定义：

```c++
priority_queue<int, vector<int>, greater<int>> q
```

**思路**

**(双顶堆)**   $O(logn)$

我们可以使用两个堆来解决这个问题，使用一个大顶堆保存整个数据流中较小的那一半元素，再使用一个小顶堆保存整个数据流中较大的那一半元素，同时大顶堆堆顶元素 `<=` 小顶堆堆顶的元素。

**具体过程：**

- 1、建立一个大根堆，一个小根堆。大根堆存储小于当前中位数，小根堆存储大于等于当前中位数。
- 2、执行`addNum`操作时，如果新加入的元素`num`小于等于大顶堆`down`堆顶元素，则加入大根堆中，否则加入小根堆中。
- 3、为了维持左右两边数的数量，我们可以让大根堆的大小最多比小根堆大`1`，每次插入后，可能会导致数量不平衡，所以如果插入后哪边的元素过多了，我们将该堆的堆顶元素弹出插入到另一个堆中。
- 4、当数据个数是奇数的时候，中位数就是大根堆堆顶，是偶数的时候就是大根堆与小根堆堆顶的平均值。 

<img src="剑指offer.assets/image-20211114213415034.png" alt="image-20211114213415034" style="zoom:50%;" />

**c++代码**

```c++
class MedianFinder {
public:
    priority_queue<int, vector<int>, greater<int>> up;  //小根堆
    priority_queue<int> down;  // 大根堆

    /** initialize your data structure here. */
    MedianFinder() {

    }

    void addNum(int num) {
        if (down.empty() || num <= down.top()) {
            down.push(num);
            if (down.size() > up.size() + 1) {
                up.push(down.top());
                down.pop();
            }
        } else {
            up.push(num);
            if (up.size() > down.size()) {
                down.push(up.top());
                up.pop();
            }
        }
    }

    double findMedian() {
        if ((down.size() + up.size()) % 2) return down.top();
        return (down.top() + up.top()) / 2.0;
    }
};
```

### [剑指 Offer 56 - II. 数组中数、字出现的次数 II](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/)

**思路**

**(位运算)**   $O(n)$ 

如果一个数字出现`3`次，它的二进制每一位也出现的`3`次。如果把所有的出现`3`次的数字的二进制表示的每一位都分别加起来，那么每一位都能被`3`整除。 我们把数组中所有的数字的二进制表示的每一位都加起来。如果某一位能被3整除，那么这一位对只出现一次的那个数的这一肯定为`0`。如果某一位不能被`3`整除，那么只出现一次的那个数字的该位置一定为`1`。

因此，考虑二进制每一位上出现 `0` 和 `1` 的次数，如果出现 `1` 的次数为 `3k + 1`，则证明答案中这一位是 `1`。

**具体过程：**

- 1、定义`bit`，从`0`枚举到`31`，相当于考虑数字的每一位。
- 2、遍历数组`nums`，统计所有数字`bit`位出现`1`的个数，记录到`cnt`中。
- 3、如果`bit`位`1`出现次数不是`3`的倍数，则说明答案在第`i`位是`1`，否则说明答案的`bit`位是`0`。

**时间复杂度分析：** 仅遍历 `32` 次数组，故时间复杂度为 $O(n)$。 

**c++代码** 

```c++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int n = nums.size();
        int res = 0;
        for(int bit = 0; bit < 32; bit++){
            int cnt = 0; //统计所有数字bit位上1的个数
            for(int i = 0; i < nums.size(); i++){
                if(nums[i] >> bit & 1) cnt++;
            }
            if(cnt % 3 != 0) res += 1 << bit;
        }
        return res;
    }
};
```

### [剑指 Offer 66. 构建乘积数组](https://leetcode-cn.com/problems/gou-jian-cheng-ji-shu-zu-lcof/)

**思路**

**(数组)**   $O(n)$

由题意可知：$B[i]=A[0]×A[1]×…×A[i−1]×A[i+1]×…×A[n−1]$ 。

因此，我们可以通过两边遍历来实现：

- 1、第一遍正向遍历，求出 $B[i]=A[0]×A[1]×…×A[i−1]$。
- 2、第二遍反向遍历，求出$B[i] *= A[n - 1]×A[n - 2]×…×A[i+1]$。  

最后我们返回`B[]`数组即可。 

**时间复杂度分析：** 我们遍历了两次数组，因此时间复杂度为$O(n)$ 

**c++代码**

```c++
class Solution {
public:
    vector<int> constructArr(vector<int>& a) {
        int n = a.size();
        vector<int> res(n);
        for(int i = 0, p = 1; i < n; i++){  // 第一次算出 res[i] = a[0] * a[1] * ... * a[i - 1]
            res[i] = p;
            p *= a[i];
        }
        for(int i = n - 1, p = 1; i >= 0; i-- ){
            res[i] *= p;                   // 第二次算出 res[i] *= a[n - 1] * a[n - 2]*...* a[i + 1]
            p *= a[i];
        }
        return res;
    }
};
```

### [剑指 Offer 67. 把字符串转换成整数](https://leetcode-cn.com/problems/ba-zi-fu-chuan-zhuan-huan-cheng-zheng-shu-lcof/)

**思路**

**(模拟)**  $O(n)$

**先来看看题目的要求：**

- 1、**忽略所有行首空格**，找到第一个非空格字符，可以是 `‘+/−’ `表示是正数或者负数，紧随其后找到**最长的一串连续数字**，将其解析成一个整数。
- 2、整数后可能有**任意非数字字符**，请将其忽略。
- 3、如果整数大于`INT_MAX`，请返回`INT_MAX`；如果整数小于`INT_MIN`，请返回`INT_MIN`；

**具体过程：**

- 1、定义`k = 0`，用`k`来找到第一个非空字符位置。
- 2、使用`flag`记录数字的正负性，`false`表示正号，`true`表示负号。
- 3、使用`res`来存贮结果，当`str[k]`为数字字符时进入`while`循环，执行`res = res * 10 +str[k] - '0'`。
  - 根据`flag`判断，如果`res`大于`INT_MAX`，则返回`INT_MAX`；如果`res * -1`小于`INT_MIN`，则返回`INT_MIN`；
- 4、计算`res `。

**时间复杂度分析：**字符串长度是 `n`，每个字符最多遍历一次，所以总时间复杂度是 $O(n)$。

**c++代码 **

```c++
class Solution {
public:
    int strToInt(string str) {
        int k = 0;
        bool flag = false;

        while (k < str.size() && str[k] == ' ') k++;
        if (str[k] == '-') flag = true, k++;
        else if (str[k] == '+' ) k++;

        long long res = 0;
        while(k < str.size() && str[k] >= '0' && str[k] <= '9'){
            res = res * 10 + str[k] - '0';
            if (res > INT_MAX && !flag)     return  INT_MAX;
            if (res * -1 < INT_MIN && flag) return  INT_MIN;
            k++;
        }
        if(flag) res *= -1;
        return  res;
    }
};
```

