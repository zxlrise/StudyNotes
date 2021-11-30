## 剑指offfer2

### [剑指 Offer 45. 把数组排成最小的数](https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)

**思路**

**(排序) ** $O(nlogn)$

自定义排序规则，如果拼接`ab` 可以比拼接 `ba`更小的话，我们选择拼接`ab  `。

**时间复杂度分析:**  排序的时间复杂度为$O(nlogn)$。

**c++代码**

```c++
class Solution {
public:

    // 自定义排序规则，如果拼接ab 可以比拼接 ba更小的话，我们选择拼接ab  
    static bool cmp(int a, int b)
    {
        string as = to_string(a), bs = to_string(b);
        return as + bs < bs + as;
    }
    string minNumber(vector<int>& nums) {
        sort(nums.begin(), nums.end(), cmp);
        string res;
        for(auto x : nums)
            res +=  to_string(x);
        return res;    
    }
};
```

### [剑指 Offer 46. 把数字翻译成字符串](https://leetcode-cn.com/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/)

**思路**

**(动态规划)**  $O(logn)$

给定我们一个数字`num`，按照题目所给定的规则将其翻译成字符串，问一个数字有多少种不同的翻译方法。

**样例：** 

<img src="剑指offer.assets/image-20211006215819974.png" alt="image-20211006215819974" style="zoom:50%;" />

我们先来理解一下题目的翻译规则，如样例所示，`num = 12258`，可以分为两种情况：

- 1、将每一位单独翻译，因此可以翻译成`"bccfi"`。
- 2、将相邻两位组合起来翻译（组合的数字范围在`10 ~ 25`之间），因此可以翻译成`"bwfi"`, `"bczi"`, `"mcfi"和"mzi"`。

两种情况是或的关系，互不影响，将其相加，那么`12258`共有`5`种不同的翻译方式。为了可以很方便的将数字的相邻两位组合起来，我们可以先将数字`num`转化成字符串数组`s[]`，下面来讲解动态规划的做法。

**状态表示：** 

我们定义`f[i]`表示前`i`个数字一共有多少种不同的翻译方法。那么，`f[n]`就表示前`n`个数字一共有多少种不同的翻译方法，即为答案。

**状态计算:**    

假设字符串数组为`s[]`，对于第`i`个数字，分成两种决策：

- 1、单独翻译`s[i]`。由于求的是方案数，如果确定了第`i`个数字的翻译方式，那么翻译前`i`个数字和翻译前`i - 1`个数的方法数就是相同的，即`f[i] = f[i - 1]`。(`s[]`数组下标从`1`开始)

  <img src="剑指offer.assets/image-20211006224400252.png" alt="image-20211006224400252" style="zoom:50%;" />

- 2、将`s[i]`和`s[i - 1]`组合起来翻译(组合的数字范围在`10 ~ 25`之间)。如果确定了第`i`个数和第`i - 1`个数的翻译方式，那么翻译前`i`个数字和翻译前`i - 2`个数的翻译方法数就是相同的，即`f[i] = f[i - 2]`。(`s[]`数组下标从`1`开始)

  <img src="剑指offer.assets/image-20211006224445222.png" alt="image-20211006224445222" style="zoom:50%;" />

最后将两种决策的方案数加起来，**因此，状态转移方程为：** `f[i] = f[i - 1] + f[i - 2]`。

**初始化：** 

`f[0] = 1`，翻译前`0`个数的方法数为`1`。

**为什么一个数字都没有的方案数是`1`？** 

`f[0]`代表翻译前`0`个数字的方法数，这样的状态定义其实是没有实际意义的，但是`f[0]`的值需要保证边界是对的，即`f[1]`和`f[2]`是对的。比如说，翻译前`1`个数只有一种方法，将其单独翻译，即`f[1] = f[1 - 1] =  1`。翻译前两个数，如果第`1`个数和第`2`个数可以组合起来翻译，那么`f[2] = f[1] + f[0] = 2` ，否则只能单独翻译第`2`个数，即`f[2] = f[1] = 1`。因此，在任何情况下`f[0]`取`1`都可以保证`f[1]`和`f[2]`是正确的，所以`f[0]`应该取`1`。

**实现细节:**

我们将数字`num`转为字符串数组`s[]`，在推导状态转移方程时，假设的`s[]`数组下标是从`1`开始的，而实际中的`s[]`数组下标是从`0`开始的，为了一 一对应，在取组合数字的值时，要把`s[i - 1]` 和 `s[i]`的值往前错一位，取`s[i - 1]`和`s[i - 2]`，即组合值`t = (s[i - 2] - '0') * 10 + s[i - 1] - '0'`。

在推导状态转移方程时，一般都是默认数组下标从`1`开始，这样的**状态表示**可以和实际数组相对应，理解起来会更清晰，但在实际计算中要错位一下，希望大家注意下。

**时间复杂度分析：** $O(logn)$，计算的次数是`nums`的位数，即`logn​`，以`10`为底。

**c++代码**

```c++
class Solution {
public:
    int translateNum(int num) {
        string s = to_string(num);
        int n = s.size();
        vector<int> f(n + 1);
        f[0] = 1;  // 初始化
        for(int i = 1; i <= n; i++){
            f[i] = f[i - 1];
            if(i > 1){
                int t = (s[i - 2] - '0') * 10 + s[i - 1] - '0';
                if(t >= 10 && t <= 25)
                    f[i] += f[i - 2];
            }
        }
        return f[n];
    }
};
```

### [剑指 Offer 47. 礼物的最大价值](https://leetcode-cn.com/problems/li-wu-de-zui-da-jie-zhi-lcof/)

**思路**

**(动态规划)**   $O(m*n)$ 

**状态表示：** `f[i,j]`表示从`(0,0)`走到`(i,j)`可以拿到的礼物最大价值。那么，`f[n - 1][m - 1]`就表示从棋盘左上角走到棋盘右下角可以拿到的礼物最大价值，即为答案。

**状态转移：**   

<img src="剑指offer.assets/image-20210729102449459.png" alt="image-20210729102449459" style="zoom: 50%;" /> 

由于限制了只会**向下走**或者**向右走**，因此到达`(i,j)`有两条路径 

- 从上方转移过来，`f[i][j] = f[i-1][j] + grid[i][j]`
- 从左方转移过来，`f[i][j] = f[i][j-1] + grid[i][j]` 

因此，**状态计算方程为：** `f[i][j] = max(f[i - 1][j], f[i][j - 1]) + grid[i][j]`， 从向右和向下两条路径中选择礼物价值最大的转移过来，再加上`grid[i][j]`的值。

**初始化：** `f[0][0] = grid[0][0]`。

**c++代码**

```c++
class Solution {
public:
    int maxValue(vector<vector<int>>& grid) {
        int n = grid.size(), m = grid[0].size();
        vector<vector<int>>f(n + 1, vector<int>(m + 1));
        f[0][0] = grid[0][0];
        for(int i = 0; i < n; i++)
            for(int j = 0; j < m; j++){
                if(!i && !j) continue;
                if(i) f[i][j] = max(f[i][j], f[i - 1][j] + grid[i][j]);
                if(j) f[i][j] = max(f[i][j], f[i][j - 1] + grid[i][j]);
            }
        return f[n - 1][m - 1];    
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

