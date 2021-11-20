## LeetCode 精选 TOP 面试题

### [116. 填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/) *

**思路**

**(BFS，树的遍历)**  $O(n)$

从根节点开始宽度优先遍历，每次遍历一层，遍历时按从左到右的顺序，对于每个节点，先让左儿子指向右儿子，，最后让这一层最右侧的节点指向`NULL`。直到我们遍历到叶节点所在的层为止。

我们举个例子，**如图所示：**

<img src="LeetCode 精选 TOP 面试题.assets/image-20211115212827935.png" alt="image-20211115212827935" style="zoom:50%;" />

对于`2`这个节点：

- 1、先让左儿子指向右儿子，即`2->left->next = 2->right ` 。

- 2、然后让右儿子指向下一个节点的左儿子，即`2->right->next = 2->next->left `。

**具体实现过程如下：**

1、记录根节点`source = root`，从根节点`root`开始遍历。

2、如果`root->lfet`存在，我们遍历该层的所有节点，每层从最左边节点开始，假设当前遍历的节点为`p`。

- 先让`p`的左儿子指向右儿子，即`p->left->next = p->right ` 。
- 如果`p->next`存在，我们就让`p`的右儿子指向下一个节点（即`p->next`节点）的左儿子。

3、每遍历完一层节点，让` root = root->left`。

4、最后返回`source`节点。

**时间复杂度分析：** 每个节点仅会遍历一次，所以总时间复杂度是 $O(n)$。

**c++代码**

```c++
class Solution {
public:
    Node* connect(Node* root) {
        if(!root) return root;
        Node* source = root;
        while(root->left){
            for(Node* p = root; p; p = p->next){
                p->left->next = p->right;
                if(p->next) p->right->next = p->next->left;
            }
            root = root->left;
        }
        return source;
    }
};
```

**c++代码2**

```c++
```

### [127. 单词接龙](https://leetcode-cn.com/problems/word-ladder/)

**思路**

**(最短路，BFS)**  $O(n^2L)$

我们对问题进行抽象：

将单词看做点，如果两个单词可以相互转化，则在相应的点之间连一条无向边。那问题就变成了求从起点到终点的最短路。

然后考虑如何建图，这里我们选择:

- 枚举所有单词对，然后判断是否可以通过改变一个字母相互转化，时间复杂度  $O(n^2L)$；

由于边权都相等，所以可以用BFS求最短路。

**时间复杂度分析：**

-  1、建图，通过上述分析可知，时间复杂度是 $O(n^2L)$；
- 求最短路用的是BFS，每个节点仅会遍历一次，每个点遍历时需要$O(L)$的计算量，所以时间复杂度是 $O(nL)$；

所以总时间复杂度是 $O(26nL2)$。 

**c++代码**

```c++
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        unordered_set<string> S;
        unordered_map<string, int> dist;
        queue<string> q;
        dist[beginWord] = 1;
        q.push(beginWord);
        for (auto word: wordList) S.insert(word);

        while (q.size()) {
            auto t = q.front();
            q.pop();
            string r = t;
            for (int i = 0; i < t.size(); i ++ ) {
                t = r;
                for (char j = 'a'; j <= 'z'; j ++ )
                    if (r[i] != j) {
                        t[i] = j;
                        if (S.count(t) && dist.count(t) == 0) {
                            dist[t] = dist[r] + 1;
                            if (t == endWord) return dist[t];
                            q.push(t);
                        }
                    }
            }
        }
        return 0;
    }
};
```

### [130. 被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/)

**思路**

**(Flood Fill, 深度优先遍历)**  $O(n^2)$ 

边界上的`'O'`不会被包围，所有不在边界上且不与边界相连的`'O'`都会被攻占。因此我们可以逆向考虑，先统计出哪些区域不会被攻占，然后将其它区域都变成`'X'`即可。

**具体过程如下：**

- 1、从外层出发，`dfs`深度搜索，将不被包围的`'O'`变成`'#'`。
- 2、最后枚举整个数组，把`'O'`和`'X'`变成`'X'`，`'#'`变成`'0'`。

**时间复杂度分析：**  $O(n^2)$ 。

**c++代码**

```c++
class Solution {
public:
    vector<vector<char>> board;  //全局变量
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
    int n, m;
    void solve(vector<vector<char>>& _board) {
        board = _board;
        n = board.size(), m = board[0].size();
        if(!n) return ;

        for(int i = 0; i < n; i++){  // 左右边界
            if(board[i][0] == 'O')     dfs(i, 0);
            if(board[i][m - 1] == 'O') dfs(i, m - 1);
        }

        for(int i = 0; i < m; i++){ //上下边界
            if(board[0][i] == 'O')     dfs(0, i);
            if(board[n - 1][i] == 'O') dfs(n - 1, i);
        }
        
        for(int i = 0; i < n; i++)
            for(int j = 0; j < m; j++)
                if(board[i][j] == '#')  board[i][j] = 'O';
                else board[i][j] = 'X';  
        _board =  board;          
    }        
    void dfs(int x, int y){
        board[x][y] = '#';
        for(int i = 0; i < 4; i++){
            int a = x + dx[i], b = y + dy[i];
            if(a >= 0 && a < n && b >= 0 && b < m && board[a][b] == 'O'){
                dfs(a, b);
            } 
        }
    }    
};
```

### [140. 单词拆分 II](https://leetcode-cn.com/problems/word-break-ii/)*

**思路**

**(回溯 + DP)**

我们先来看看`LeetCode139` 单词拆分 的思路:

**状态表示：**  `f[i]`表示字符串`s`的前`i`个字符是否可以拆分成`wordDict`，其值有两个`true` 和`false`。

**状态计算：** 依据最后一次拆分成的字符串`str`划分集合，最后一次拆分成的字符串`str`可以为`s[0 ~ i - 1]`，`s[1 ~ i - 1]`，，，`s[j ~ i - 1]`。

**状态转移方程：**  `f[i] = ture   `   的条件是 ：`f[j] = ture`并且`s[j, i - 1]`在`hash`表中存在。

**初始化：** `f[0] = true`，表示空串且合法。

**时间复杂度分析：** 状态枚举$O(n^2)$，状态计算$O(n)$，因此时间复杂度为$O(n^3)$。

```c++
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> hash;   //存贮单词
        vector<bool> f(s.size() + 1, false);  
        f[0] = true;  //初始化
        for(string word : wordDict){
            hash.insert(word);
        }
        for(int i = 1; i <= s.size(); i++){
            for(int j = 0; j < i; j++){  //for(int j = 1; j <= i; j++)
                if(f[j] && hash.find(s.substr(j, i - j)) != hash.end()){
                    f[i] = true;
                    break;  //只要有一个子集满足就ok了
                }
            }
        }
        return f[s.size()];
    }
};
```

我们通过`DP`可以判断出一个字符串是否可以被合法的拼接，如果其可以合法拼接，则进行`dfs`，找出所有的情况。

**`dfs`函数设计**

```c++
void dfs(string s, int u, string path) 
```

- `u`表示下一个单词开始的位置。
- `path`用来记录路径。

`dfs(s, u, path)`表示，从`s[u]`开始拼接下一个单词`s[u, i]`。如果`s[u, i]`出现在`wordBreak`中，并且`f[u] = true`，即`s`的前`u`个字符可以被合法的拼接，则我们将`s[u, i]`拼接到`path`中，并从`i + 1`位置继续递归到下一层。

**c++代码**

```c++
class Solution {
public:
    vector<string> res; //记录答案
    unordered_set<string> hash;  //哈希表
    vector<bool> f;
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        int n = s.size();
        f.resize(n + 1);
        f[0] = true;
        for(string word : wordDict){
            hash.insert(word);
        }
        for(int i = 1; i <= n; i++)
            for(int j = 0; j < i; j++){
                if(f[j] && hash.count(s.substr(j, i - j))){
                    f[i] = true;
                    break;
                }
            }
        if(!f[n]) return res;
        dfs(s, 0, ""); 
        return res;   
    }

    void dfs(string& s, int u, string path){
        if(u == s.size()){
            path.pop_back(); // 去除多余的空格
            res.push_back(path);
            return;
        }
        for(int i = u; i < s.size(); i++){  //[u, i]
            if(f[u] && hash.count(s.substr(u, i - u + 1))){
                dfs(s, i + 1, path + s.substr(u, i - u + 1) + ' ');
            }
        }
    }
};
```

### [150. 逆波兰表达式求值](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)

**思路**

**(栈操作)** $O(n)$

遍历所有元素。如果当前元素是整数，则压入栈；如果是运算符，则将栈顶两个元素弹出做相应运算，再将结果入栈。
最终表达式扫描完后，栈里的数就是结果。

**时间复杂度分析：** 每个元素仅被遍历一次，且每次遍历时仅涉及常数次操作，所以时间复杂度是 $O(n)$。

**c++代码**

```c++
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        stack<int> stk;
        for(string t : tokens){
            // 遇到运算符，则将栈顶两个元素弹出做相应运算，再将结果入栈
            if(t == "+" || t == "-" || t == "*" || t == "/"){
                int b = stk.top(); stk.pop();
                int a = stk.top(); stk.pop();
                if (t == "+") a += b;
                else if (t == "-") a -= b;
                else if (t == "*") a *= b;
                else a /= b;
                stk.push(a);
            }else{
            // 当前元素是整数，则压入栈    
                stk.push(stoi(t));
            }
        }
        return stk.top();
    }
};
```

### [166. 分数到小数](https://leetcode-cn.com/problems/fraction-to-recurring-decimal/)

**思路**

**(模拟)**  $O(n)$

模拟手工计算除法的过程，每次将余数乘`10`再除以除数，当同一个余数出现两次时，我们就找到了循环节。所以我们用一个哈希表 `unordered_map<int,int>` 记录所有余数所对应的商在小数点后第几位，当计算到相同的余数时，上一次余数的位置和当前位置之间的数，就是该小数的循环节。

**实现细节：**

当分子取`-2^31`，分母取`-1`是，结果`2^31`会超出`int = 2^31 - 1`的范围，因此我们用`long long`来存贮中间计算结果。

**具体过程如下：**

1、假设分子为`x`，分母为`y`，如果`x % y == 0`，则直接返回`x / y`。

2、根据`x`和`y`的正负性，判断结果的正负。为了方便计算，`x`取 `abs(x)`，`y`取`abs(y)`。

3、如果`x % y != 0`，我们先将商`x / y`存入结果字符串`res`中，并将余数`x % y`记录到`x `中。

4、定义`hash`表，如果当前余数`x`不为`0`，则我们进行下述循环操作：

- 用`hash`表记录每个余数的位置。
- 将余数`x *= 10`，作为下一次除法的被除数。
- 将商`x / y`存入结果字符串`res`中，并计算下一次的余数`x %= y`。
- 如果我们发现计算到了相同的余数`x`，则将`[0,hash[x]]`和`[hash[x],res.size()]`加入结果字符串`res`中。

5、最后返回`res`。

<img src="LeetCode 精选 TOP 面试题.assets/image-20211117215955785.png" alt="image-20211117215955785" style="zoom:50%;" />

**时间复杂度分析：** 计算量与结果长度成正比，是线性的。所以时间复杂度是 $O(n)$。

**c++代码**

```c++
class Solution {
public:
    string fractionToDecimal(int numerator, int denominator) {
        typedef long long LL;
        LL x = numerator, y = denominator;
        if(x % y == 0) return to_string(x / y);
        string res;  //记录答案
        if((x < 0 && y > 0) || (x > 0 && y < 0))  res += '-';
        x = abs(x), y = abs(y);
        res += to_string(x / y) + '.', x %= y;
        unordered_map<int, int> hash;
        while(x){
            hash[x] = res.size();
            x *= 10;
            res += to_string(x / y);
            x %= y;
            if(hash.count(x)){
                res = res.substr(0, hash[x]) + '(' + res.substr(hash[x]) + ')';
                break;
            }
        }
        return res;
    }
};
```

### [208. 实现 Trie (前缀树)](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)

**思路**

**字典树** 

字典树，顾名思义，是关于“字典”的一棵树。即：它是对于字典的一种存储方式（所以是一种数据结构而不是算法）。这个词典中的每个“单词”就是从根节点出发一直到某一个目标节点的路径，路径中每条边的字母连起来就是一个单词。

<img src="LeetCode 精选 TOP 面试题.assets/image-20211118195003075.png" alt="image-20211118195003075" style="zoom:50%;" />

标橙色的节点是“目标节点“，即根节点到这个目标节点的路径上的所有字母构成了一个单词。

**作用：**

- 1、维护字符串集合**（字典）** 
- 2、向字符串集合中插入字符串**（建树）**
- 3、查询字符串集合中是否有某个字符串**（查询）**
- 4、查询字符串集合中是否有某个字符串的前缀**（查询）** 

**具体操作：**

**定义字典树节点**

```c++
struct Node {
    bool is_end;    // 表示是否存在以这个点为结尾的单词
    Node *son[26];  // 26个小写字母子结点
    Node() {        // 初始化
        is_end = false;
        for (int i = 0; i < 26; i ++ )   
            son[i] = NULL;
    }
}*root;
```

向字典树中插入一个单词`word`

从根结点出发，沿着字符串的字符一直往下走，若某一字符不存在，则直接把它创建出来，继续走下去，走完了整个单词，标记最后的位置的`is_end = true`。

```c++
void insert(string word) {
    auto p = root;
    for (auto c: word) {
        int u = c - 'a';
        if (!p->son[u]) p->son[u] = new Node();
        p = p->son[u];
    }
    p->is_end = true;
}
```

查找字典树中是否存在单词`word`

从根结点出发，沿着字符串的字符一直往下走，若某一字符不存在，则直接`return false`，当很顺利走到最后的位置的时候，判断最后一个位置的`is_end`即可。

```c++
bool search(string word) {
    auto p = root;
    for (auto c: word) {
        int u = c - 'a';
        if (!p->son[u]) return false;
        p = p->son[u];
    }
    return p->is_end;
}
```

查找字典树中是否有以`prefix`为前缀的单词

从根结点出发，沿着字符串的字符一直往下走，若某一字符不存在，则直接`return false`，如果顺利走到最后一个位置，则返回`true`。

```c++
bool startsWith(string word) {
    auto p = root;
    for (auto c: word) {
        int u = c - 'a';
        if (!p->son[u]) return false;
        p = p->son[u];
    }
    return true;
}
```

**时间复杂度分析：** $O(n)$，`n`表示单词操作字符串长度。

**c++代码**

```c++
class Trie {
public:

    struct Node{
        bool is_end;
        Node* son[26];
        Node(){
            is_end = false;
            for(int i = 0; i < 26; i++)
                son[i] = NULL;
        }
    }*root;

    Trie() {
        root = new Node();
    }
    
    void insert(string word) {
        auto p = root;
        for(auto c : word){
            int u = c - 'a';
            if(!p->son[u]) p->son[u] = new Node();
            p = p->son[u];
        }
        p->is_end = true;
    }
    
    bool search(string word) {
        auto p = root;
        for(auto c : word){
            int u = c - 'a';
            if(!p->son[u]) return false;
            p = p->son[u];
        }
        return p->is_end;
    }
    
    bool startsWith(string prefix) {
        auto p = root;
        for(auto c : prefix){
            int u = c - 'a';
            if(!p->son[u]) return false;
            p = p->son[u];
        }
        return true;
    }
};
```

### [210. 课程表 II](https://leetcode-cn.com/problems/course-schedule-ii/)

**思路**

**拓扑排序：**   $O(n+m)$ 

对一个有向无环图`G`进行拓扑排序，是将`G`中所有顶点排成一个线性序列，使得图中任意一对顶点`u`和`v`，若`<u，v> ∈E(G)`，则`u`在线性序列中出现在`v`之前。

<img src="LeetCode 精选 TOP 面试题.assets/image-20211118203550909.png" alt="image-20211118203550909" style="zoom:50%;" />

一个合法的选课序列就是一个拓扑序，拓扑序是指一个满足有向图上，不存在一条边出节点在入节点前的线性序列，如果有向图中有环，就不存在拓扑序。可以通过拓扑排序算法来得到拓扑序，以及判断是否存在环。

**判断拓扑排序步骤：** 

- 1、建图并记录所有节点的入度。
- 2、将所有入度为`0`的节点加入队列。
- 3、取出队首的元素`now`，将其加入拓扑序列。
- 4、访问所有`now`的邻接点`nxt`，将`nxt`的入度减`1`，当减到`0`后，将`nxt`加入队列。
- 5、重复步骤`3`、`4`，直到队列为空。 
- 6、如果拓扑序列个数等于节点数，代表该有向图无环，且存在拓扑序。

在这道题中，拓扑排序时用的队列的进队顺序就是一个可行的输出方案。因此我们走一遍拓扑排序，将拓扑排序的元素加入`res`中，如果最后的`res.size() < n`，则表示该图不满足拓扑排序，则返回空数组，否则返回`res`数组。

**时间复杂度分析：**假设 $n$ 为点数，$m$ 为边数，拓扑排序仅遍历所有的点和边一次，故总时间复杂度为 $O(n+m)$。

**c++代码**

```c++
class Solution {
public:
    vector<int> findOrder(int n, vector<vector<int>>& edges) {
        vector<vector<int>> g(n);  // 邻接表
        vector<int> d(n);  // 存贮每个节点的入度
        for(auto edge :edges){
            g[edge[1]].push_back(edge[0]);  //建图
            d[edge[0]]++;
        }

        queue<int> q;
        for(int i = 0; i < n; i++)
            if(d[i] == 0) q.push(i); ////将所有入度为0的节点加入队列

        vector<int> res;
        while(q.size()){
            int t = q.front();
            q.pop();
            res.push_back(t);
            for(int i : g[t]){  //访问t的邻接节点(出边)
                d[i]--;
                if(d[i] == 0)  q.push(i);  // i的入度为0，将其加入队列中
            }
        }    

        if(res.size() < n) return {};
        return res;
    }
};
```

### [212. 单词搜索 II](https://leetcode-cn.com/problems/word-search-ii/)

**思路**

**(DFS+Trie)**  $O(n^23^LL)$ 

**朴素思想：**

暴力搜索出所有单词路径，再判断该路径表示的单词是否出现在单词列表中。枚举所有路径时先枚举起点，再枚举路径延伸的方向。 但朴素`DFS`搜索空间太大，直接搜会超时。所以我们需要剪枝。

**字典树优化：** 

我们先将所有单词存入`Trie`树中，这样我们在搜索时，如果发现当前单词前缀不在`trie`中，那么当前的路径一定不会构成任意一个单词，我们直接`return`。

<img src="LeetCode 精选 TOP 面试题.assets/image-20211118213534509.png" alt="image-20211118213534509" style="zoom:50%;" />

**递归函数设计：**

```c++
void dfs(int x, int y, Node* p) 
```

`x`，`y`是当前搜索到的二维字符网格的横纵坐标。

`p` 是当前在字典树中搜索的节点。

**搜索过程如下：**

- 1、先将所有的单词放在一个字典树中，在二维字符网格中枚举每个单词的起点（这个起点是出现在字典树中的）。
- 2、从该起点出发向四周搜索单词`word`，在搜索过的点需要进行赋值为`'.'`，防止重复使用该字符。
- 3、如果当前搜索的位置`(x,y)`的元素`g[a][b]`在字典树中有子节点，则继续向四周搜索。
- 4、直到搜索到单词`word`的最后一个字母，`p->id != -1`，我们将这个单词的序号记录下来，存贮到`set`中。
- 5、最后根据单词的序号，将单词添加到`res`中。

**c++代码**

```c++
class Solution {
public:
    struct Node {
        int id;  //记录当前单词的序号
        Node *son[26];
        Node() {
            id = -1;
            for (int i = 0; i < 26; i ++ ) son[i] = NULL;
        }
    }*root;
    unordered_set<int> ids;
    vector<vector<char>> g;
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};

    void insert(string& word, int id) {   //向字典树中插入一个单词word
        auto p = root;
        for (auto c: word) {
            int u = c - 'a';
            if (!p->son[u]) p->son[u] = new Node();
            p = p->son[u];
        }
        p->id = id;
    }

    vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
        g = board;
        root = new Node();
        for (int i = 0; i < words.size(); i ++ ) insert(words[i], i);

        for (int i = 0; i < g.size(); i ++ )
            for (int j = 0; j < g[i].size(); j ++ ) {
                int u = g[i][j] - 'a';
                if (root->son[u])
                    dfs(i, j, root->son[u]);
            }

        vector<string> res;
        for (auto id: ids) res.push_back(words[id]);
        return res;
    }

    void dfs(int x, int y, Node* p) {
        if (p->id != -1) ids.insert(p->id);
        char t = g[x][y];
        g[x][y] = '.';
        for (int i = 0; i < 4; i ++ ) {
            int a = x + dx[i], b = y + dy[i];
            if (a >= 0 && a < g.size() && b >= 0 && b < g[0].size() && g[a][b] != '.') {
                int u = g[a][b] - 'a';
                if (p->son[u]) dfs(a, b, p->son[u]);
            }
        }
        g[x][y] = t;
    }
};
```

### [289. 生命游戏](https://leetcode-cn.com/problems/game-of-life/)

**思路**

**(位运算 + 模拟)**  $O(nm)$

如何做到不用额外的空间，且把所有位置细胞的状态同时改变呢？因为只有`0`和`1`两个状态，因此我们可以用二进制中的第二位来存储变化后的状态。

- `00`：一开始是死细胞，后来还是死细胞
- `01`：一开始是活细胞，后来变成死细胞
- `10`：一开始是死细胞，后来变成活细胞
- `11`：一开始是活细胞，后来还是活细胞

我们最后把第二位全部右移一位就是结果数组了。

**具体过程如下：**

1、我们`cur`表示当前细胞状态，`live`表示当前细胞周围存活的细胞个数，`next`表示当前细胞的下一个状态。

2、遍历` board`数组，对于每一个` board[i][j]`方格上的细胞，先统计出周围存活的细胞个数`live`。

- 当前细胞状态为存活，即`cur = board[i][j] & 1 = 1`：
  - 如果`live < 2 || live > 3`，则其下一个状态为死亡，即 `next = 0`，
  - 否则`next = 1`。

- 当前细胞状态为死亡，即`cur = board[i][j] & 1 = 0`:
  - 如果`live == 3`，则其下一个状态为存活，即 `next = 1`。
  - 否则`next = 0`。

3、将`next`右移一位，存贮到`board[i][j]`中，即` board[i][j] |= next << 1`。

4、最后将`board`数组中的每一个元素左移一位。

**时间复杂度分析：**  $O(nm)$ 

**c++代码**

```c++
class Solution {
public:
    void gameOfLife(vector<vector<int>>& board) {
        if(board.empty() || board[0].empty()) return ;
        int n = board.size(), m = board[0].size();
        for(int i = 0; i < n; i++){
            for(int j = 0; j < m; j++){
                int live = 0;
                for(int x = i - 1; x <= i + 1; x++)
                    for(int y = j - 1; y <= j + 1; y++)
                    if(x >= 0 && x < n && y >=0 && y < m && !(x == i && y == j) && (board[x][y] & 1)){
                        live++;
                    }
                int cur = board[i][j] & 1, next = 0;
                if(cur){
                    if(live < 2 || live > 3) next = 0;
                    else next = 1;
                }else{
                    if(live == 3) next = 1;
                    else next = 0;
                }  
                board[i][j] |= next << 1;  
            }
            
        }
        for(int i = 0; i < n; i++)
            for(int j = 0; j < m; j++){
                board[i][j] >>= 1;
            }
    }
};
```

### [371. 两整数之和](https://leetcode-cn.com/problems/sum-of-two-integers/)

**思路**

**(位运算)**  $O(1)$ 

异或运算相当于不进位的加法，比如：

<img src="LeetCode 精选 TOP 面试题.assets/image-20211119214842165.png" alt="image-20211119214842165" style="zoom:50%;" />

不进位的加法再加上进位就等于两个数相加，即`a + b == ((a & b) << 1) + (a ^ b)`。 

<img src="LeetCode 精选 TOP 面试题.assets/image-20211119215714833.png" alt="image-20211119215714833" style="zoom: 50%;" />

**具体过程如下：**

1、我们计算出 不进位`sum = a ^ b`, 进位`carry = (unsigned)(a & b) << 1`。

2、递归调用`getSum(carry, sum)`。

3、直到`a == 0`时，我们返回`b`，即为答案。

**时间复杂度分析：** $O(1)$。

**c++代码**

```c++
class Solution {
public:
    int getSum(int a, int b) {
        if(!a) return b;
        int sum = a^b, carry = (unsigned)(a & b) << 1;
        return getSum(carry, sum);         
    }
};
```

