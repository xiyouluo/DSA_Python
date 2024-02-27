1. **quick sort**

   - ```cpp
     void quick_sort(type arr[], int l, int r) {
         // 递归的终止情况
         if (l >= r) return;
         
         // 第一步：分成子问题
         int i = l - 1, j = r + 1;
         auto x = arr[l + r >> 1];
         while (i < j) {
         	do i ++ ; while (arr[i] < x);
         	do j -- ; while (arr[j] > x);
         	if (i < j) swap(arr[i], arr[j]);
         }
         
         // 第二步：递归处理子问题
         quick_sort(arr, l, j);
         quick_sort(arr, j + 1, r);
         
         // 第三步：子问题合并.快排这一步不需要操作，但归并排序的核心在这一步骤
     }
     ```
   
   - 排序算法是否稳定的定义：原序列中相等的元素是否交换位置
   
2. **merge sort**

   - ```cpp
      void merge_sort(int arr[], int l, int r) {
          // 递归的终止情况
          if (l >= r)
              return;
          
          // 第一步：分成子问题
          int mid = l + r >> 1;
          
          // 第二步：递归处理子问题
          merge_sort(arr, l, mid);
          merge_sort(arr, mid + 1, r);
          
          // 第三步：合并子问题
          int k = 0, i = l, j = mid + 1, temp[r - l + 1];
          while (i <= mid && j <= r)
              if arr[i] <= arr[j]; temp[k++] = arr[i++];
              else temp[k++] = arr[j++];
          
          while (i <= mid) temp[k++] = arr[i++];
          while (j <= r) temp[k++] = arr[j++];
          
          for (k = 0, i = l; i <= r; ++k, ++i) arr[i] = temp[k];
      
      }
      ```
   
3. **bisect, binary search**

   - **本质：找到某范围的有序整数区间中 满足某性质的值 的最值（i.e. 边界），且时间复杂度仅有 $\Omicron (logn)$ ！**

   - ```cpp
      bool check(int x); 
      // 定义check(x)函数，使得x满足该性质时返回true，不满足时返回false
      
      int bsearch_left(int l, int r) 
      // 找满足check(x)的x的最小值（左边界）
      // 常用于找最大值的最小值（最大值的左边界），check(x)即检验x能否为最大值
      {
          while (l < r) {
              int mid = l + r >> 1;
              if (check(mid)) r = mid; 
              else l = mid + 1;
          }
          
          return l; 
      }
      ```
      
   - ```cpp
      bool check(int x); 
      // 定义check(x)函数，使得x满足该性质时返回true，不满足时返回false
      
      int bsearch_right(int l, int r) 
      // 找满足check(x)的x的最大值（右边界）
      // 常用于找最小值的最大值（最小值的右边界），check(x)即检验x能否为最小值
      {
          while (l < r) {
              int mid = l + r + 1>> 1; // 注意要加1
              if (check(mid)) l = mid; 
              else r = mid - 1;
          }
          
          return l;
      }
      ```
      
   - ```cpp
      // 浮点数二分，比较简单
      
      double bsearch_left(double l, double r) {
          while (r - l > 精度) {
              double mid = (l + r) / 2;
              if (check(mid)) l = mid;
              else r = mid;
          }
          
          return l;
      }
      
      double bsearch_right(double l, double r) {
          while (r - l > 精度) {
              double mid = (l + r) / 2;
              if (check(mid)) r = mid;
              else l = mid;
          }
          
          return l;
      }
      ```
   
4. **prefix sum & adjacent difference**

   - ```cpp
     // 一维前缀和
     
     // 初始化S[0] = 0;
     S[i] = a[1] + a[2] + ... a[i];
     // 作用：快速求出数组a（下标）在区间[l, r]内的部分和
     a[l] + ... + a[r] = S[r] - S[l - 1];
     ```

   - ```cpp
     // 一维差分
     
     // 作用：给数组a（下标）在区间[l, r]内的每个数加上c; 下面的b是a的差分（即a是b的前缀和）
     b[l] += c;
     b[r + 1] -= c;
     // 从a得到b：
     // 假设初始状态a[i]全为0，则易算得b[i]也全为0；此时每一步在[i, i]区间内给a[i]（此时为0）加上a[i]（实际的取值），使用上面的式子即可更新b
     ```

   - ```cpp
     // 二维前缀和
     
     // S[i][j] = 第i行j列元素a[i][j]及左上部分所有元素的和
     //初始化S[0][j] = s[i][0] = 0;
     S[i][j] = S[i - 1][j] + S[i][j - 1] - S[i - 1][j - 1] + a[i][j];
     // 作用：以(x1, y1)为左上角，(x2, y2)为右下角的子矩阵的和为：
     S[x2, y2] - S[x1 - 1, y2] - S[x2, y1 - 1] + S[x1 - 1, y1 - 1]
     ```

   - ```cpp
     // 二维差分
     
     // 作用：给以(x1, y1)为左上角，(x2, y2)为右下角的子矩阵中的所有元素加上c：
     b[x1, y1] += c;
     b[x2 + 1, y1] -= c;
     b[x1, y2 + 1] -= c;
     b[x2 + 1, y2 + 1] += c;
     // 从a得到b：
     // 假设初始状态a[i][j]全为0，则易算得b[i][j]也全为0；此时每一步给以(i, j)为左上角，(i,j)为右下角的子矩阵中的所有元素加上实际应取的a[i][j]，使用上面的式子即可更新b
     ```

5. **双指针**

   - 本质：利用题目中的某种（单调）性质，使第二个指针（一般用`j`）在第一个指针（一般用`i`）移动或者说指向下一元素时，**不需要从头开始**（这里的头不一定是下标`0`，而是指`j`的初始值），只需要从上一次到的位置继续移动，将$O(n^2)$的暴力算法优化到$O(n)$；做题时往往可以**先想暴力算法**，再去寻找**性质**优化时间复杂度

   - ```cpp
     for (int i = 0, j = 0; i < n; i++ ) // 初始值因题目而已
     {
         while (j < i && check(i, j)) // 不一定是j < i，也可能是j < n等等
             j++;
     
         // 具体问题的逻辑
     }
     ```

   - **常见问题分类：**
         (1) 对于一个序列，用两个指针维护一段区间
         (2) 对于两个序列，维护某种次序，比如归并排序中合并两个有序序列的操作

6. **位运算**

   - c++中负数是补码，对于正整数`n`有`-n = ~n + 1`

   - ```cpp
     // 求n二进制下的第k位数字:
     // 最右边是第0位
     n >> k & 1; // 先右移再与
     
     int lowbit(int n) // 返回 n的最后一位1及之后可能有的0 这个二进制数字（的十进制表示）
     {
     	return n & -n; // 即n & (~n + 1);  
     }
     
     ```

7. **离散化**

   - 本质：当有些数据因为本身很大或者类型不支持，自身无法作为数组的下标来方便地保存对应的属性（不能or能但**时间复杂度太高**），而影响最终结果的只有元素之间的相对属性时，我们可以将原来的数据**按顺序**映射到小的自然数序列`{1, 2, 3...}`或`{0, 1, 2}`等等（也就是按顺序编号）来处理问题，即离散化；离散化是一种特殊的（哈希函数保序的）哈希

   - ```cpp
     vector<int> alls; // 存储所有待离散化的值
     sort(alls.begin(), alls.end()); // 将所有值排序
     alls.erase( unique(alls.begin(), alls.end()), alls.end() );// 去掉重复元素
     
     // 二分求出x对应的离散化后的值
     int bisect_left(int x) // 找到满足alls[y] >= x的y的左边界，即x离散化后对应的值y
     {
         int l = 0, r = alls.size() - 1;
         while (l < r)
         {
             int mid = l + r >> 1;
             if (alls[mid] >= x)
                 r = mid;
             else 
                 l = mid + 1;
         }
         return r + 1; // 加1是为了映射到1, 2, ... , alls.size()
         	// `return r;`是映射到l_init, l_init + 1, ... , r_init
         	// ATTENTION: 在具体题目中使用离散化结果时（例如遍历），务必注意离散化结果的取值区间；如 算法题.md 3.区间和 中预处理前缀和时i是从1取到alls.size()
     }
     
     // 此二分也可以用STL完成
     std::lower_bound(alls.begin(), alls.end(), x)；
     	//存在这样的x则返回左边界迭代器，否则返回alls.end()
     ```
     
   - tips: `erase()`是容器的成员函数，应该用`.`调用

8. **区间合并**

   - 首先把区间按左端点排序，排完序后，当前维护区间和下一个区间的关系只有三种：

     - 下一个区间左右端点在当前区间内：当前区间不用更新
     - 下一个区间左端点在当前区间内，右端点在当前区间外：更新当前区间的右端点
     - 下一个区间左端点在当前区间外：当前区间维护完毕，当前区间更新为下一个区间

   - 注：当前维护区间初始化为`[-inf, -inf]`，其中`inf`是一个在本题中足够大的数字（`-inf`小于区间最小的左端点）

   - ```cpp
     // 将所有存在交集的区间合并
     void merge(vector<PII> &segs) {
         vector<PII> res; //合并后的结果
     
         sort(segs.begin(), segs.end());
     
         int st = -inf, ed = -inf;
         for (auto seg : segs) {
             if (ed < seg.first) {
                 if (st != -2e9)
                     res.push_back({st, ed});
                 st = seg.first, ed = seg.second;
             }
             else
                 ed = max(ed, seg.second);
         }
         if (st != -2e9)
             res.push_back({st, ed}); //最后一段区间直接放到结果里，但加个if判断，避免segs为空还加了一段区间到结果
     }
     ```

   - tip: 在`sort`函数中，对应`pair<T1, T2>`类型默认先按`first`再按`second`排序

9. **linked list 链表**

   - 注：此处用array来模拟、实现链表，也称静态链表；不用`struct`是因为这种实现方式用到了很慢的操作`new`

   - 单链表：实现邻接表，存储图和树

     - 用两个array来模拟，分别是`e[N]`和`ne[N]`，前者存下标`N`处的值，后者存 下标`N`处元素的下一个元素 的下标，最后一个元素的下一个元素的下标定义为`-1`

     - ```cpp
       // head存储头指针，e[]存储节点的值，ne[]存储节点的next指针，idx表示当前用到了哪个节点
       int head, e[N], ne[N], idx;
       
       // 初始化
       // ATTENTION: 记得调用
       void init() {
           head = -1; // 使用链表一定要记得初始化
           idx = 0;
       }
       
       // 在链表头插入一个数
       void insert(int x) {
           e[idx] = x；
           ne[idx] = head；
           head = idx ++ ;
       }
       
       // 将头节点删除，需要保证头节点存在
       void remove() {
           head = ne[head];
       }
       
       // 将x插到下标是k的点后面
       void add(int k, int x) {
           e[idx] = x, ne[idx] = ne[k], ne[k] = idx ++ ;
       }
       
       // 将下标是k的点后面的一个点删掉
       void remove(int k) {
           ne[k] = ne[ne[k]];
       }
       
       //如果需要在链表最后插入元素，则令 最后一个元素的下一个元素 的下标为tail
       ```
     
   - 双链表：优化某些问题

     - ```cpp
       // e[]表示节点的值，l[]表示节点的左指针，r[]表示节点的右指针，idx表示当前用到了哪个节点
       int e[N], l[N], r[N], idx;
       
       // 初始化
       // ATTENTION: 记得调用
       void init()
       {
           // 0是左端点，1是右端点
           r[0] = 1, l[1] = 0;
           idx = 2;
       }
       
       // 在节点 下标是k的点 的右边插入x
       void insert(int k, int x)
       {
           e[idx] = x;
           l[idx] = k, r[idx] = r[k];
           l[r[k]] = idx；r[k] = idx ++ ;
       }
       
       // 删除下标为k的节点
       void remove(int k)
       {
           l[r[k]] = l[k];
           r[l[k]] = r[k];
       }
       ```
   
10. **stack and queue**

    - stack: 

      - ```cpp
        // tt表示栈顶
        int stk[N], tt = 0;
        
        // 向栈顶插入一个数
        stk[ ++ tt] = x;
        
        // 从栈顶弹出一个数
        tt -- ;
        
        // 栈顶的值
        stk[tt];
        
        // 判断栈是否为空，如果 tt > 0，则表示不为空
        if (tt > 0)
        {
        
        }
        ```

    - 普通队列：

      - ```cpp
        // hh 表示队头，tt表示队尾
        int q[N], hh = 0, tt = -1;
        
        // 向队尾插入一个数
        q[ ++ tt] = x;
        
        // 从队头弹出一个数
        hh ++ ;
        
        // 队头的值
        q[hh];
        
        // 判断队列是否为空，如果 hh <= tt，则表示不为空
        if (hh <= tt)
        ```
      
    - 循环队列：

      - 使用数组模拟队列会导致一个问题：随着时间的推移，整个队列会向数组的尾部移动，一旦到达数组的最末端，即使数组的前端还有空闲位置，再进行入队操作也会导致溢出（这种数组里实际有空闲位置而发生了上溢的现象被称为「假溢出」）。
      
        解决假溢出的办法是采用循环的方式来组织存放队列元素的数组，即将数组下标为 `0` 的位置看做是最后一个位置的后继。（数组下标为 `x` 的元素，它的后继为 `(x + 1) % SIZE`）。这样就形成了循环队列。
      
      - ```cpp
        // hh 表示队头，tt表示队尾的后一个位置
        int q[N], hh = 0, tt = 0;
        
        // 向队尾插入一个数
        q[tt ++ ] = x;
        if (tt == N) tt = 0;
        
        // 从队头弹出一个数
        hh ++ ;
        if (hh == N) hh = 0;
        
        // 队头的值
        q[hh];
        
        // 判断队列是否为空，如果hh != tt，则表示不为空
        if (hh != tt)
    
11. **单调栈和单调队列**

    - 先用栈或队列暴力做，然后挖掘一些性质（**删除一些不会有用的元素**），使得栈或队列变成有单调性（递增或递减）的，降低时间复杂度

    - monotonic stack 单调栈：

      - 常见模型：找出每个数左边离它最近的比它大/小的数

      - ```cpp
        int tt = 0;
        for (int i = 1; i <= n; i++) {
            while (tt && check(stk[tt], i)) tt-- ;
            stk[++tt] = i;
        }
        ```
      
    - monotonic queue 单调队列

      - 常见模型：找出滑动窗口中的最大值/最小值

      - ```cpp
        int hh = 0, tt = -1;
        //队列q[N]存的是下标
        for (int i = 0; i < n; i++) {
            if ( check1(q[hh]) ) hh++;  // 判断队头是否滑出窗口
            while ( hh <= tt && check2(q[tt], i) ) tt--;
            q[++tt] = i;
        }
        ```

12. **KMP 字符串匹配**

    - 思路：先暴力模拟，再优化时间复杂度

    - 可用来求循环节，其余问题（大概）都能用字符串前缀哈希做

    - [参考资料](https://oi-wiki.org/string/kmp/)

    - ```cpp
        // s[]是长文本，p[]是模式串，n是s的长度，m是p的长度
        // s和p均从下标1开始，0不要了
        // next数组即前缀函数prefix function
        
        // 求模式串的next数组：
        // i从2开始，因为i=1时（第一个字符）对应的前缀函数定义为0
        for (int i = 2, j = 0; i <= m; i++) {
            while (j && p[i] != p[j + 1]) j = ne[j];
            if (p[i] == p[j + 1]) j++;
            ne[i] = j;
        }
        
        // 匹配
        for (int i = 1, j = 0; i <= n; i++) {
            while (j && s[i] != p[j + 1]) j = ne[j];
            if (s[i] == p[j + 1]) j++;
            if (j == m) {
                j = ne[j];
                // 匹配成功后的题目要求做的事
            }
        }
        ```

13. **trie**

    -   也叫字典树，高效地存储和查找字符串集合的数据结构

    -   ```cpp
        int son[N][26], cnt[N], idx;
        // 0号点既是根节点，又是空节点
        // son[][]存储树中每个节点的子节点
        // cnt[]存储以每个节点结尾的单词数量
        
        // 插入一个字符串
        void insert(char* str) {
            int p = 0;
            for (int i = 0; str[i]; i++) 
                // 利用字符串以0结尾来控制循环的结束
            {
                int u = str[i] - 'a';
                if (!son[p][u]) son[p][u] = ++ idx;
                p = son[p][u];
            }
            cnt[p]++;
        }
        
        // 查询字符串出现的次数
        int query(char* str) {
            int p = 0;
            for (int i = 0; str[i]; i++) {
                int u = str[i] - 'a';
                if (!son[p][u]) return 0;
                p = son[p][u];
            }
            return cnt[p];
        }
        ```

14. **union-find disjoint sets并查集**

    -   并查集是一种用于管理元素所属集合的数据结构，实现为一个森林，其中每棵树表示一个集合，树中的节点表示对应集合中的元素

    -   顾名思义，并查集支持两种操作：

        -   合并（Union）：合并两个元素所属集合（合并对应的树）
        -   查询（Find）：查询某个元素所属集合（查询对应的树的根节点），这可以用于判断两个元素是否属于同一集合

    -   朴素并查集：

        ```cpp
        int p[N]; //存储每个点的父节点
        
        // 返回x的根节点
        int find(int x) {
        	if (p[x] != x) p[x] = find(p[x]); // 路径压缩优化
        	return p[x];
        }
        
        // 初始化，假定节点编号是1~n
        for (int i = 1; i <= n; i++) p[i] = i;
        
        // 合并a和b所在的两个集合：
        p[find(a)] = find(b);
        ```

    -   维护size的并查集（但是注意使用size这个变量名可能会编译错误）

        ```cpp
        int p[N], size[N];
        // p[]存储每个点的父节点, size[]只有根节点的有意义，表示该集合中的点的数量
        
        // 返回x的根节点
        int find(int x) {
        	if (p[x] != x) p[x] = find(p[x]); // 路径压缩
        	return p[x];
        }
        
        // 初始化，假定节点编号是1~n
        for (int i = 1; i <= n; i++) {
        	p[i] = i;
        	size[i] = 1;
        }
        
        // 合并a和b所在的两个集合：
        if (p[a] != p[b]) {
        	size[find(b)] += size[find(a)];
        	p[find(a)] = find(b);
        }
        ```

    -   维护到根节点距离的并查集

        ```cpp
        int p[N], d[N];
        //p[]存储每个点的父节点, d[x]存储x到p[x]的距离
        
        // 返回x的根节点，并更新x的距离
        int find(int x) // 递归用得特别好！
        {
        	if (p[x] != x) {
        		int u = find(p[x]); // 用临时变量t的原因：要先调用find(p[x])更新p[x]的距离d[p[x]]，又需要暂时不改变p[x]（更新距离时要更新之前的p[x]）
        		d[x] += d[p[x]]; // 到这一句时，find(p[x])已经执行过了，所以p[x]这个节点已接在根上
                	// 更新完的结果是x也接在根上（下条语句执行之后），所以新的d[x]（到根节点的距离）等于旧的d[x]（到p[x]的距离）加上d[p[x]]（已经接在根上的p[x]到根节点的距离，它是之前的调用中一路更新过来的值，它等于路径压缩前p[x]到根节点的距离）
                	// 为了保证判断是否在同一集合的正确性，每一个节点到根节点的距离不能变（严谨来说是在模某个数之后的距离不能变），这也是我们更新距离数组的原则
        		p[x] = u;
        	}
        	return p[x];
        }
        
        // 初始化，假定节点编号是1~n
        for (int i = 1; i <= n; i ++ ) {
        	p[i] = i;
        	d[i] = 0;
        }
        
        // 合并a和b所在的两个集合：
        p[find(a)] = find(b);
        d[find(a)] = xxx; // 根据具体问题更新a的根节点的距离；而a这棵树中的其他节点的距离在调用find()时才会被更新
        ```

15. **heap堆**

    -   堆是一棵完全二叉树，有小根堆和大根堆，此处以最小堆为例

        ```cpp
        // h[N]存储堆中的值, h[1]是堆顶，x的左儿子是2x, 右儿子是2x+1
        int h[N], size;
        
        // 此处的heap_swap比较简单，直接用 <algorithm> 里的swap交换值
        void heap_swap(int a, int b) {
            swap(h[a], h[b]);
        }
        
        // 把下标为u的点向下调整位置
        void down(int u) {
            int t = u;
            if (u * 2 <= size && h[u * 2] < h[t]) t = u * 2;
            if (u * 2 + 1 <= size && h[u * 2 + 1] < h[t]) t = u * 2 + 1;
            if (u != t) {
                heap_swap(u, t);
                down(t);
            }
        }
        
        // 把下标为u的点向上调整位置
        void up(int u) {
            while (u / 2 && h[u] < h[u / 2]) {
                heap_swap(u, u / 2);
                u >>= 1;
            }
        }
        
        // 数组已经装好元素时，以O(n)的时间复杂度建堆
        for (int i = n / 2; i; i--) down(i);
        
        // 插入一个数
        h[++size] = x;
        up(size);
        
        // 求最小值
        h[1];
        
        // 删除最小值
        heap_swap(1, size);
        size--;
        down(1);
        
        // 删除下标为k的元素
        heap_swap(k, size);
        size--;
        down[k]; up[k];
        
        // 修改下标为k的元素
        h[k] = x;
        down[k]; up[k];
        ```

    -   如果需要维护每个点是第几个插入的：

        ```cpp
        // ph[k]存储第k个插入的点在堆中的下标
        // hp[k]存储堆中下标是k的点是第几个插入的
        int ph[N], hp[N];
        
        // heap_swap需要更改
        // 交换两个点及其映射关系
        void heap_swap(int a, int b) {
            swap(ph[hp[a]],ph[hp[b]]);
            swap(hp[a], hp[b]);
            swap(h[a], h[b]);
        }
        ```

16. **一般哈希**

    -   哈希表又称散列表，一种以「key-value」形式存储数据的数据结构。所谓以「key-value」形式存储数据，是指任意的 key 都唯一对应到内存中的某个位置。只需要输入查找的key，就可以快速地找到其对应的 value。可以把哈希表理解为一种高级的数组，这种数组的下标可以是很大的整数，浮点数，字符串甚至结构体

        -   哈希函数：要让key对应到内存中的位置，就要为key计算索引，也就是计算这个数据应该放到哪里。这个根据key计算索引的函数就叫做哈希函数。一般取$f(x) = x \space mod \space M$为哈希函数，其中$M$为一个较大的质数（且离2的幂次尽量远），可以证明，这样取$M$可以让冲突尽量少，$M$也作为哈希表中槽的数量（拉链法）

    -   拉链法：在每个存放数据的地方开一个链表，如果有多个key索引到同一个地方，只需要把他们都放到那个位置的链表里就行了。查询的时候需要把对应位置的链表整个扫一遍。

        ```cpp
        int h[N], e[N], ne[N], idx;
        
        // 向哈希表中插入一个数
        void insert(int x) {
            int k = (x % N + N) % N; // c++中负数模运算结果为负数
            e[idx] = x;
            ne[idx] = h[k];
            h[k] = idx ++ ;
        }
        
        // 在哈希表中查询某个数是否存在
        bool find(int x) {
            int k = (x % N + N) % N;
            for (int i = h[k]; i != -1; i = ne[i])
                if (e[i] == x)
                    return true;
        
            return false;
        }
        
        // 若要实现删除，可以定义一个bool标志该元素被删去，不会真的去删
        ```
        
    -   开放寻址法：如果在`t`处有冲突，就往后找`t+1`, `t+2`，直到找到没有冲突的位置（空位置），如果遍历到数组尾则再从`0` 开始

        设待哈希数据有$M$个，则数组大小大概需要开$2M \sim 3M$（经验）

        ```cpp
        int h[N], null = 0x3f3f3f3f;
        
        // 如果x在哈希表中，返回x的下标；如果x不在哈希表中，返回x应该插入的位置
        int find(int x) {
        	int t = (x % N + N) % N;
        	while (h[t] != null && h[t] != x) {
        		t ++ ;
        		if (t == N) t = 0;
        	}
        	return t;
        }
        ```
        
        tips: 在算法竞赛中，我们常常需要用到设置一个常量用来代表“无穷大”。

        比如对于`int`类型的数，有的人会采用`INT_MAX`，即`0x7fffffff`作为无穷大。但是以`INT_MAX`为无穷大常常面临一个问题，即加一个其他的数会溢出，而这种情况在动态规划，或者其他一些递推的算法中常常出现，很有可能导致算法出问题。所以在算法竞赛中，我们常采用`0x3f3f3f3f`来作为无穷大，这样做主要有如下好处：

        `0x3f3f3f3f`的十进制为`1061109567`，和`INT_MAX`一个数量级，即$10^9$数量级，而一般场合下的数据都是小于$10^9$的
        `0x3f3f3f3f * 2 = 2122219134`，无穷大相加依然不会溢出
        **可以使用`memset(array, 0x3f, sizeof(array))`来为数组每个元素设初值为`0x3f3f3f3f`，因为这个数的每个字节都是`0x3f`**
    
17. **字符串哈希-前缀哈希**

    -   核心思想：将字符串看成P进制数，P的经验值是131或13331，取这两个值的**冲突概率低**

    -   作用：判断字符串是否相等

    -   tips: 

        -   取模的数用$2^{64}$，这样直接用`unsigned long long`存储，溢出的结果就是取模的结果
        -   一般情况下，不要把某个字符映射到 $0$
        -   此方法不考虑冲突的处理，我们认为按经验参数基本上不会遇到冲突

        ```cpp
        int P = 131; // 或13331
        typedef unsigned long long ULL;
        ULL h[N], p[N]; 
        // h[k]存储字符串前k个字符的哈希值, p[k]存储 P^k mod 2^64
        // 一般让字符串从下标1开始，而h[0]赋为0，方便初始化前缀哈希
        
        // 初始化，预处理前缀的哈希
        h[0] = 0;
        p[0] = 1;
        for (int i = 1; i <= n; i ++ ) {
            h[i] = h[i - 1] * P + str[i - 1]; 
            // 这里减1是因为我们使用的字符串是从下标0开始的，下标i - 1即第i个字符
            // 另一种等价的处理方式是把字符串读到str + 1这个指针里（使用char str[]型字符串）
            p[i] = p[i - 1] * P;
        }
        
        // 计算子串 str[l ~ r] 的哈希值
        ULL get(int l, int r) {
            return h[r] - h[l - 1] * p[r - l + 1];
        }
        ```
        

18. **DFS&BFS**

    -   此处的 DFS 和 BFS 都是 cs188 讲的 **graph search**，也就是记录下访问过的点，以后不会再访问

    -   搜索问题强烈建议用 **state space graph** 或 **search tree** 来 formulate

        ==核心：每个状态是图的一个节点，用图/树来想搜索过程==

    -   下面列出的DFS与BFS的性质详见 cs188-fa22-note01

    -   BFS:

        -   complete in both
        -   optimal if all edge costs are equivalent 
        -   time complexity: $O(b^s)$
        -   space complexity: $O(b^s)$ 

    -   DFS: 

        -   complete in graph search and not complete in tree search
        -   not optimal（因为这里指的是找到一种走法就终止；显然可以把所有走法找完，比较出最有走法）
        -   time complexity: $O(b^m)$
        -   space complexity: $O(bm)$（此处是用 stack 实现的基本DFS，不回溯）

    -   pseudocode:

        **注意，某些搜索问题不是要找到一种走法（任意一种走法、最短的走法等等），而是要所有走法的总数**，例题：[走迷宫之二](http://dsbpython.openjudge.cn/2023hw9/003/)；事实上 dfs 既可以用来找一种走法也可以用来找所有走法，实现上的区别是，前者在对子节点调用dfs且找到走法后就返回，后者并不返回，见下面的注释；一般的 dfs 就是找所有走法的，**之前有点误会**，以为一般说的 bfs 只找一种走法

        ```pseudocode
        visited = empty
        container = emptyContainer
        container.push(startNode)
        
        function nonRecursive(node):
            while container not empty:
                currentNode = container.pop()
                visited.add(currentNode)
                if isWin(currentNode):
                	processWin()
                	return
                for neighbor in neighbors(currentNode):
                    if neighbor not in visitedSet:
                       container.push(neighbor)
        		
        	processLose()
        ```
        
        recursive DFS:
        
        ```cpp
        function dfs(node):	
        	if isWin(node):
        		visited.add(node)
        		processWin()
                 return
        	for neighbor in neighbors:
        		if neighbor not in visited:
        			dfs(neighbor) 
                     	# 如果只需要找一个解，在dfs(neighbor)找到解后这里就把目前的函数也返回
                        # 另外，如果dfs有返回值，注意这里要让子节点的返回值传递回来（并派上用场）
        	processLose()
        ```

19. **backtracking search**

    -   一般用于解决 CSP 问题，用递归实现（**在递归调用后**恢复到之前的状态）

    -   **注意，此类问题中`state`往往是一个全局的变量，每个节点的状态都是用（更新后的）这个全局变量`state`来表示的，所以在搜索下一个子节点前（即`for`循环遍历下一个`option`前），需要把`state`复原；而一般的DFS或BFS中，每个节点的状态是一个独立的变量，这时自然就不需要复原**；因而==是否需要复原取决于如何存储和表达每个节点的状态==

        可参考 算法题.md 8.八数码

    -   Backtracking search is an optimization on DFS used specifically for the problem of constraint satisfaction, with improvements coming from two main principles:

        1. Fix an ordering for variables, and select values for variables in this order. This is valid when assignments are commutative.
        2. When selecting values for a variable, **only select values that don’t conflict with any previously assigned values**. If no such values exist, backtrack and return to the previous variable, changing its value.

    - 在 CSP 问题（或一般的 dfs 问题）中，我们往往需要在 dfs 函数的参数中记录一些信息（例如搜索深度），这里一般来说可以按两种方式来记录：

      -   一是记录本次 dfs “已经到达的”，以深度举例：一进到这个 dfs 函数，就代表已经搜到形参里这个深度了
      -   二是记录本次 dfs “要做的”，以深度举例：本次 dfs 函数将要达到形参里这个深度，这个“达到”具体发生在本次 dfs 函数到达下一个节点时，也就是进入下一层 dfs 的递归调用之前
      -   这只是习惯问题，但保持一致的风格之后做题好想一点；**以后就都按第一种来**
    
    - pseudocode:
    
      ```pseudocode
      function backtrack_search(state):
      	if is_solution(state):
      		process_solution(state)
      		return
      
      	for option in available_options(state):
      		if not has_conflict(state, option) and state not in visited:
      			update_state(state, option)
         
      			# 后面的state是更新后的 
      			backtrack_search(state)
      
      			undo_update(state, option)
                  
      ```
      
      1.  `is_solution(state)`: A function that checks if the given state represents a complete solution.
      2.  `process_solution(state)`: A function that processes the solution, such as printing it or adding it to a list.
      3.  `available_options(state)`: A function that returns the options or candidates available for the current state.
      4.  `has_conflict(state, option)`: A function that checks if applying the given option to the current state results in a conflict according to the problem's constraints. If a conflict is detected, the algorithm skips the recursive call to `backtrack_search` (pruning here) and moves to the next iteration of the loop, trying the next available option.
      5.  `update_state(state, option)`: A function that updates the state based on chosen option.
      6.  `undo_update(state, option)`: A function that undoes the chosen option, reverting the state to its previous state before applying the next option in the for-loop. **It ensures the next option in the for-loop starts with the correct state.**
    
20. **图的存储**

    -   树是一种特殊的图，与图的存储方式相同
        对于无向图中的边 a -- b，存储两条有向边 a -> b, b -> a
        因此我们可以只考虑有向图的存储

    -   邻接矩阵 adjacency matrix: 使用一个二维数组 `adj` 来存边，其中 `adj[u][v]` 为 1 表示存在`u`到`v`的边，为 0 表示不存在。如果是带边权的图，可以在 `adj[u][v]` 中存储`u`到`v`的边的边权。
        -   邻接矩阵只适用于没有重边（或重边可以忽略）的情况
        -   其最显著的优点是可以 $\Omicron(1)$ 查询一条边是否存在
        -   由于邻接矩阵在稀疏图上效率很低（尤其是在点数较多的图上，**空间**无法承受)，所以一般只会在稠密图上使用邻接矩阵

    -   邻接表 adjacency list: 使用一个支持动态增加元素的数据结构构成的数组，如 `vector<int> adj[n + 1]` 来存边，其中 `adj[u]` 存储的是点`u`的所有出边的相关信息（终点、边权等)。
        -   存各种图都很适合，一般有特殊需求（如需要快速查询一条边是否存在且点数较少）时才使用邻接矩阵

        -   尤其适用于需要对一个点的所有出边进行排序的场合

        -   也可以使用单链表来存边（**记得初始化！！**）：

            ```cpp
            // 对于每个点节点k，开一个单链表存储k的子节点，h[k]存储这个单链表的头指针
            int h[N], e[N], ne[N], idx, w[N]; // w[N]存边权（如果需要的话）
            
            // 添加一条有向边a->b
            void add(int a, int b, int c) {
                e[idx] = b, ne[idx] = h[a], h[a] = idx ++ ;
                w[idx] = c;
            }
            
            // 初始化
            idx = 0;
            memset(h, -1, sizeof h); // 指向-1代表指向空
            ```

21. **图的遍历**（遍历连通图的所有节点）

    -   用邻接表实现的时间复杂度为 $\Omicron(n+m)$，用邻接矩阵则为 $\Omicron(n^2)$

    - 邻接表实现深度优先遍历

      ```cpp
      // 写法一
      void dfs(int u) {
          st[u] = true; // 标记点u已经被遍历过
      
          for (int i = h[u]; i != -1; i = ne[i]) {
              int j = e[i]; // 注意e[i]才是节点！！
              if (!st[j]) dfs(j);
          }
      }
      
      // 写法二
      st[u] = true; // 函数运行前先把初始节点标记为已遍历
      void dfs(int u) {
          for (int i = h[u]; i != -1; i = ne[i]) {
              int j = e[i]; // 注意e[i]才是节点！！
              if (!st[j]) {
                  st[j] = true; // 标记点j已经被遍历过
                  dfs(j);
      		}
          }
      }
      
      // 写法二好处：回溯时非常清晰好看（标记 -> 递归调用dfs -> 取消标记）
      ```

    -   邻接表实现宽度优先遍历

        ```cpp
        // 写法一
        queue<int> q;
        q.push(1);
        
        while (q.size()) {
            int t = q.front();
            st[t] = true; // 标记点t已经被遍历过
            q.pop();
        
            for (int i = h[t]; i != -1; i = ne[i]) {
                int j = e[i]; // 注意e[i]才是节点！！
                if (!st[j]) q.push(j);
            }
        }
        
        // 写法二
        queue<int> q;
        q.push(1);
        st[1] = true;
        
        while (q.size()) {
            int t = q.front();
            q.pop();
        
            for (int i = h[t]; i != -1; i = ne[i]) {
                int j = e[i]; // 注意e[i]才是节点！！
                if (!st[j]) {
                    st[j] = true; // 标记点j已经被遍历过
                    q.push(j);
                }
            }
        }
        
        // 写法一好处：真正搜到一个点时才把它标记为访问过，这从逻辑上看更加合理；写法二在一些有重边的情形会出错，例题见算法题.md ROADS
        ```

22. **拓扑排序Topological sorting**

    -   在一个有向无环图中，我们将图中的顶点以线性方式进行排序，使得对于任何的顶点$u$到$v$的有向边$(u, v)$, 都有$u$在$v$的前面；拓扑序列可能不唯一

    -   $Kahn$算法：

        初始状态下，集合$S$装着所有入度为$0$的点，$Q$是一个空队列

        每次从$S$中任取出一个点$u$放入$Q$，然后将$u$的所有边$(u, v_1)$, $(u, v_2)$...删除；对于边$(u, v)$，若将该边删除后点$v$的入度变为$0$，则将$v$放入$S$中

        不断重复以上过程，直到集合$S$为空。检查图中是否存在任何边，如果有，那么这个图一定有环，否则返回$Q$，$Q$中节点的顺序就是拓扑排序的结果

    -   y总版本使用数组模拟的队列，优点在于出队（更改队头下标实现）的元素还在数组中，不用再开一个集合，只开一个队列即可

        ```cpp
        bool toposort() {
            int hh = 0, tt = -1;
        
            // d[i] 存储点i的入度
            for (int i = 1; i <= n; i ++ )
                if (!d[i])
                    q[ ++ tt] = i;
        
            while (hh <= tt) {
                int t = q[hh ++ ];
        
                for (int i = h[t]; i != -1; i = ne[i]) {
                    int j = e[i];
                    d[j] -- ; // 不用真的删除边，只需要更新入度
                    if (d[j] == 0) q[ ++ tt] = j;
                }
            }
        
            // 如果所有点都入队了，说明存在拓扑序列；否则不存在拓扑序列
            return tt == n - 1;
        }
        ```

23. **最短路**

    -   考察点在于如何把一个实际问题抽象成最短路问题（如怎么表示点和边，怎么把问题表示成graph）

    - 单源最短路：一个点到其他所有点的最短路

      - 边权全都非负

        - 朴素$Dijkstra$算法: 有向边、无向边、重边、自环、其他环都可以有

          - 时间复杂度$O(V^2)$，与边数无关，一般用于稠密图（$E \sim V^2$）
          
          - ```cpp
            // 该算法不同于常规的DFS与BFS那样往后面的状态去“走”（不记录访问过的节点DFS就可能infinite loop），而是每次更新一些节点到源的最短路，不存在是否要记录访问过的节点这样的问题
              
              int g[N][N];  // 邻接矩阵，存储每条边；如果有重边只要边权最小的（因为要求的是最短路）
              int dist[N];  // 存储起点到每个点的当前最短距离
              bool st[N];   // 存储每个点的最短路是否已经确定
              
              // 求1号点到n号点的最短路，如果不存在则返回-1
              int dijkstra() {
                  memset(dist, 0x3f, sizeof dist);
                  dist[1] = 0; // 注意先后顺序，这句放前面就没用了
              
                  // 每次循环确定1个节点的最短路，可以跑n次循环来确定n个节点，即这里写i < n;
                  // 但是，n - 1次循环后确定了n - 1个节点的最短路，第n次循环的作用是确定最后一个节点的最短路，而第n次循环中事实上只有最后这个节点没有被确定，所以t一定会是这个节点，跑第n次循环的意义只在于把st[最后一个被确定的节点]赋值为true，而不会更新dist[]
                  for (int i = 0; i < n - 1; i ++ ) {
                      int t = -1; // t是还未确定最短路的点中到1号点距离最小的点，初始化为-1代表没有
                      for (int j = 1; j <= n; j ++ ) // 找t
                          if (!st[j] && (t == -1 || dist[t] > dist[j]))
                              t = j;
              
                      st[t] = true; // t节点的最短路已经确定了
                      
                      // 用t更新其他节点的距离
                      for (int j = 1; j <= n; j ++ )
                          dist[j] = min(dist[j], dist[t] + g[t][j]);
                  }
              
                  if (dist[n] == 0x3f3f3f3f) return -1;
                  return dist[n];
              }
            ```
          
        - 堆优化的$Dijkstra$算法：有向边、无向边、重边、自环、其他环都可以有
        
          -   cs188-fa22-note01: "The strategy employed in Uniform Cost Search is identical to that of Dijkstra’s algorithm, and the chief difference is that **UCS terminates upon finding a solution state instead of finding the shortest path to all states**." 
        
          -   时间复杂度$O(ElogV)$，一般用于稀疏图（$E \sim V$）
        
          -   ```cpp
              typedef pair<int, int> PII;
              
              int h[N], w[N], e[N], ne[N], idx; // 稀疏图用邻接表存储
              int dist[N]; // 存储所有点到1号点的距离
              bool st[N]; // 存储每个点的最短距离是否已确定
              
              // 求1号点到n号点的最短距离，如果不存在，则返回-1
              int dijkstra(int n) {
                  memset(dist, 0x3f, sizeof dist);
                  dist[1] = 0;
                  priority_queue<PII, vector<PII>, greater<PII>> heap;
                  heap.push({0, 1});      // first存储距离，second存储节点编号
              
                  while (heap.size()) {
                      auto t = heap.top();
                      heap.pop();
              
                      int ver = t.second, distance = t.first; // vertex
              
                      if (st[ver]) continue; // 处理重复节点
                      	// ver被标记为已找到最短路当且仅当达到堆顶，这次以后重复的ver是没有用的
                      st[ver] = true;
              
                      for (int i = h[ver]; i != -1; i = ne[i]) {
                          int j = e[i];
                          if (dist[j] > distance + w[i]) {
                              dist[j] = distance + w[i];
                              // 这里可以加一句p[j] = ver;
                              // 这样的话就可以记录每个节点在路径中的父节点，最后可以输出路径
                              heap.push({dist[j], j}); // 这样做可能会有相同的节点放进堆中
                              	// 最好的做法是更新堆中j节点的dist，但是STL的堆不支持，需要手写堆
                              	// 所以这里的处理是把重复的节点再加到堆里去
                          }
                      }
                  }
              
                  if (dist[n] == 0x3f3f3f3f) return -1;
                  return dist[n];
              }
              // 如果要输出路径：
              vector<int> path;
              		for (int i = n; i != 1;i = p[i]) path.push_back(i);
              		path.push_back(1);
              		reverse(path.begin(), path.end());
              		for (auto i : path) cout << i << ' ';
              		cout << endl;
              ```
    
      - 存在负权边
    
        - $Bellman-Ford$算法：有向边、无向边、重边、自环、其他环都可以有；不限制边数的最短路不能有负环（这样最短路为负无穷）
    
          - 时间复杂度$O(VE)$
        
          - 可解决有边数限制的最短路问题（且有边数限制时可以有负环，因为这样不会导致最短路为负无穷）
        
          - 可判断负环（不带上不超过k条边的要求，看第n次迭代是否会有松弛操作）；但一般用$SPFA$判断负环
        
          - ```cpp
            int n, m; // n表示点数，m表示边数
            int dist[N]; // dist[x]存储1到x的最短路距离
            int last[N]; // 要求不超过k条边才需要
            // 内层for循环的每一次执行会更新dist[]，但内层for循环紧接着的下一次执行不应该用更新后的dist[]；内层for循环每次执行时要的是同样的dist[]（要上一次内层循环完全执行的结果），所以需要在进入内层for循环前拷贝一份dist[]；last[]是指上一次内层循环完全执行的结果（取名原因）
            // 如果没有不超过k条边的限制就不需要把dist[]拷贝到last[]，直接在dist[]上修改
            
            struct Edge     // 边，a表示出点，b表示入点，w表示边的权重
            {
                int a, b, w;
            } edges[M];
            
            // 求1到n的不超过k条边就可到达的最短路，如果无法不超过k条边就从1走到n，则返回-1
            int bellman_ford(int k) {
                memset(dist, 0x3f, sizeof dist);
                dist[1] = 0;
            	
                // 在最短路存在的情况下，由于一次松弛操作会使最短路的边数至少+1，而最短路的边数最多为n-1，因此整个算法最多执行n-1轮松弛循环（松弛循环指对每条边做一次松弛操作）
                // 第k次松弛循环结束后，dist[n]是从1号点到n号点的不超过k条边就可到达的最短路
                // 如果第n次迭代仍然会有松弛操作，就说明存在一条长度是n+1的最短路，由抽屉原理，路径中至少存在两个相同的点，说明图中存在环，而最短路中有环一定是负环
                for (int i = 0; i < k; i ++ ) // 迭代k次
                {
                    memcpy(last, dist, sizeof(dist))
                    for (int j = 0; j < m; j ++ ) {
                        int a = edges[j].a, b = edges[j].b, w = edges[j].w;
                        dist[b] = min(dist[b], last[a] + w); // 松弛操作 relax
                        	// 用last[a]
                    }
                }
            
                if (dist[n] > 0x3f3f3f3f / 2) return -1; 
                // 因为我们的正无穷不是真的正无穷而是0x3f3f3f3f，会被负权边更新，最后不能用 == 				0x3f3f3f3f来判断，具体值怎么取要参考0x3f3f3f3f会被更新到多小
                return dist[n];
            }
            ```
    
        - $SPFA$算法：有向边、无向边、重边、自环、其他环都可以有；有负环不能求最短路（最短路为负无穷）但可以判断负环，且一般判断负环就用$SPFA$
        
          - 时间复杂度：一般$O(E)$，最坏$O(VE)$
          
          - 做不了有边数限制的最短路，此时需要换$Bellman-Ford$
          
          - ```cpp
            int n; // 总点数
            int h[N], w[N], e[N], ne[N], idx; // 邻接表存储所有边
            int dist[N]; // 存储每个点到1号点的最短距离
            bool st[N]; // 存储每个点是否在队列中
            
            // 求1号点到n号点的最短路距离，如果从1号点无法走到n号点则返回-1
            int spfa() {
                memset(dist, 0x3f, sizeof dist);
                dist[1] = 0;
            
                queue<int> q;
                q.push(1);
                st[1] = true;
            
                while (q.size()) {
                    auto t = q.front();
                    q.pop();
                    st[t] = false; // 注意别忘了st数组的更新
            
                    for (int i = h[t]; i != -1; i = ne[i]) {
                        int j = e[i];
                        if (dist[j] > dist[t] + w[i]) {
                            dist[j] = dist[t] + w[i];
                            if (!st[j]) // 注意！！如果队列中已存在j，则不需要将j重复插入
                            {
                                q.push(j);
                                st[j] = true;
                            }
                        }
                    }
                }
            
                if (dist[n] == 0x3f3f3f3f) return -1;
                return dist[n];
            }
            ```
          
          - 判断负环：
          
          - ```cpp
            int n; // 总点数
            int h[N], w[N], e[N], ne[N], idx; // 邻接表存储所有边
            int dist[N], cnt[N]; // dist[x]存储1号点到x的最短距离，cnt[x]存储1到x的最短路中经过的点数
            bool st[N]; // 存储每个点是否在队列中
            
            // 如果存在负环，则返回true，否则返回false
            // 原理：如果某条最短路径上有n个点（除了自己），那么加上自己之后一共有n+1个点，由抽屉原理一定有两个点相同，所以存在环，最短路中的环一定是负环
            bool spfa() {
                // 不需要（重新初始化）dist数组
                // dist数组是全局的，已经默认初始化为0了
                // 初始化为0的原因见下（等价到新图上了）
                
                queue<int> q;
                for (int i = 1; i <= n; i ++ ) {
                    q.push(i);
                    st[i] = true;
                }
                // 该题是判断是否存在负环，并非判断是否存在从1开始的负环，因此需要将所有的点都加入队列中，更新周围的点
                // 初始时将所有点插入队列中可以按如下方式理解：在原图的基础上新建一个虚拟源点，从该点向其他所有点连一条权值为0的有向边；那么原图有负环等价于新图有负环
                // 此时在新图上做SPFA，将虚拟源点加入队列中。然后进行SPFA的第一次迭代，这时会将所有真实点的距离更新为0（且所有真实点cnt + 1）并将所有点插入队列中，而这就是我们初始化的做法
                // 关于cnt >= n：由于我们实际的做法中并没有进行新图里的第一次迭代，所以按照新图的思路，我们的做法里每个真实点的cnt都少加了一次1；如果做了那一次迭代，最后判断是cnt >= n + 1（因为是在有n + 1个点的图中找负环），所以最后的判断就仍然是判断cnt >= n
            
                while (q.size()) {
                    auto t = q.front();
                    q.pop();
                    st[t] = false;
            
                    for (int i = h[t]; i != -1; i = ne[i]) {
                        int j = e[i];
                        if (dist[j] > dist[t] + w[i]) {
                            dist[j] = dist[t] + w[i];
                            cnt[j] = cnt[t] + 1;
                            if (cnt[j] >= n) return true;       
                            // 如果最短路中包含至少n个点（不包括自己），则说明存在环
                            if (!st[j]) {
                                q.push(j);
                                st[j] = true;
                            }
                        }
                    }
                }
            
                return false;
            }
            ```
          

-   多源汇最短路：任意两节点间的最短路

    - $Floyd$算法
    
      - $O(V^3)$
    
      - 我们定义一个数组 `f[k][x][y]`，表示只允许用节点$1\sim k$作为中间点时节点$x$到节点$y$的最短路长度；很显然，`f[n][x][y]` 就是节点$x$到节点$y$的最短路长度
    
        如何求出 `f` 数组的值？dp！
    
        base case: `f[0][x][y]`：即$x$到$y$的边权，同一点为$0$，没有边为$+\infin$
    
        状态转移：`f[k][x][y] = min(f[k-1][x][y], f[k-1][x][k]+f[k-1][k][y])`
    
        （经过$k$点和不经过$k$点）
    
        空间优化：可以证明数组的第一维对结果无影响，可以省略，于是状态转移可以改成 ：`f[x][y] = min(f[x][y], f[x][k]+f[k][y])`
    
      ```cpp
      // 初始化：
      for (int i = 1; i <= n; i ++ )
          for (int j = 1; j <= n; j ++ )
              if (i == j) d[i][j] = 0;
              else d[i][j] = INF;
      // 再读入边即完成初始化（重边只需要读最短的）
      
      // 算法结束后，d[a][b]表示a到b的最短距离
      void floyd() {
          for (int k = 1; k <= n; k ++ )
              for (int i = 1; i <= n; i ++ )
                  for (int j = 1; j <= n; j ++ )
                      d[i][j] = min(d[i][j], d[i][k] + d[k][j]);
      }
      ```

24. **最小生成树**

    - 树：In graph theory, a tree is an undirected graph in which any two vertices are connected by exactly one path, or equivalently a **connected** **acyclic** undirected graph. [wikipedia]([Tree (graph theory) - Wikipedia](https://en.wikipedia.org/wiki/Tree_(graph_theory))

      即，树是连通无环无向图

    - 生成树：在图论中，无向图 G 的生成树（Spanning Tree）是具有 G 的全部顶点，但边数最少的连通子图

    - 最小生成树：连通加权无向图中总权重最小的生成树

    - 朴素$Prim$算法：稠密图一般选用此算法

      - $O(V^2)$

        ```cpp
        int n; // n表示点数
        int g[N][N]; // 邻接矩阵，存储所有边
        int dist[N]; // 存储其他点到当前最小生成树的距离，这是与朴素dijkstra的区别
        bool st[N]; // 存储每个点是否已经在生成树中
        
        
        // 如果图不连通，则返回INF(值是0x3f3f3f3f), 否则返回最小生成树的树边权重之和
        int prim() {
            memset(dist, 0x3f, sizeof dist);
        
            int res = 0;
            for (int i = 0; i < n; i ++ ) {
                int t = -1;
                for (int j = 1; j <= n; j ++ )
                    if (!st[j] && (t == -1 || dist[t] > dist[j]))
                        t = j;
        
                if (i && dist[t] == INF) return INF;
                	// 只有第一次（i == 0）可以为INF，否则说明图不连通
        
                if (i) res += dist[t]; 
                	// 要先更新res再更新dist
                	// 否则dist更新过程中，j取到t且t有自环时可能会把dist[t]更新，而根据算法的原理，我们在增加res的值时不要这个更新
                st[t] = true;
        
                for (int j = 1; j <= n; j ++ ) dist[j] = min(dist[j], g[t][j]);
            }
        
            return res;
        }
        ```

    - 堆优化的$Prim$算法：不常用

      - $O(ElogV)$

    - $Kruskal$算法：稀疏图一般选用此算法

      - $O(ElogE)$

        ```cpp
        int n, m; // n是点数，m是边数
        int p[N]; // 并查集的父节点数组
        
        struct Edge {
            int a, b, w;
        
            bool operator< (const Edge &W) const {
                return w < W.w;
            }
        } edges[M];
        
        int find(int x) {
            if (p[x] != x) p[x] = find(p[x]);
            return p[x];
        }
        
        int kruskal() {
            sort(edges, edges + m);
        
            for (int i = 1; i <= n; i ++ ) p[i] = i; // 初始化并查集
        
            int res = 0, cnt = 0;
            for (int i = 0; i < m; i ++ ) // 遍历图的所有边
            {
                int a = edges[i].a, b = edges[i].b, w = edges[i].w;
        
                a = find(a), b = find(b);
                if (a != b) // 如果a, b两个点还没有连通（即没有加到生成树中），则把它们连通
                {
                    p[a] = b;
                    res += w; // res记录生成树的总权重
                    cnt ++ ; // cnt记录生成树的边数
                }
            }
        
            if (cnt < n - 1) return INF; // 不存在生成树（图不连通）
            return res;
        }
        ```

        

25. **二分图**

    - In graph theory, a bipartite graph (or bigraph) is a graph whose vertices can be divided into two disjoint and [independent sets](https://en.wikipedia.org/wiki/Independent_set_(graph_theory)) $U$ and $V$, that is **every edge connects a vertex in $U$ to one in $
      V$**.

      **Equivalently**, a bipartite graph is a graph that does not contain any odd-length cycles.

    - 染色法：判定二分图（注意二分图不一定是连通图）

      - 时间复杂度：$O(V + E)$

        ```cpp
        int n; // n表示点数
        int h[N], e[M], ne[M], idx; // 邻接表存储图
        int color[N]; // 表示每个点的颜色，-1表示未染色，0表示白色，1表示黑色
        
        // 参数：u表示当前节点，c表示当前点的颜色
        bool dfs(int u, int c) // 遍历并染色
        {
            color[u] = c; // 和“图的深度优先遍历”处代码一样，可以上来就染色；另一种等价的是在函数运行前先把初始节点染色，然后把染色操作写在for循环中，子节点未被染色时将其染色
            for (int i = h[u]; i != -1; i = ne[i]) {
                int j = e[i];
                if (color[j] == -1) // 如果子节点j没染色，就对j调用dfs来染色
                { // 注意这个大括号不能省略，否则后面的else if就和内层的if绑定（没想到合适的词）了
                    if (!dfs(j, !c)) return false; // 如果对j的dfs返回false，对u的也要返回
                }
                else if (color[j] == c) return false; // 如果j染完色和u一样，就出现了矛盾
            }
        
            return true;
        }
        
        // 初始化和调用函数
        memset(color, -1, sizeof color);
        bool flag = true; 
        for (int i = 1; i <= n; i ++ ) // 如果图是连通的就只需要dfs一个点，但可能不连通
            if (color[i] == -1) // 图不连通就要遍历所有点，每次对没染色的点调用dfs
                if (!dfs(i, 0)) {
                    flag = false;
                    break;
                }
        // 最后flag == true代表是二分图，反之不是
        
        
        // 这种写法要先把初始节点（在main函数中调用dfs函数写的节点）染色
        bool dfs(int v, int c) {
            for (int i = h[v]; i != -1; i = ne[i]) {
                int j = e[i];
                if (color[j] == -1) {
                    color[j] = !c; // 在这里染色，但注意是染子节点，颜色应该是!c
                    if (!dfs(j, !c)) return false;
                }
                else if (color[j] == c) return false;
            }
            return true;
        }
        ```
        
      
    - 匈牙利算法：求二分图的最大[匹配]([Matching (graph theory) - Wikipedia](https://en.wikipedia.org/wiki/Matching_(graph_theory))的边数

      - 最大匹配的边数即在二分图中最多能找到多少条没有公共端点的边，等于最小点覆盖
      
      - 时间复杂度：理论$O(VE)$，但一般远小于$O(VE)$
      
      - ```cpp
        int n1, n2; // n1表示第一个集合中的点数，n2表示第二个集合中的点数
        int h[N], e[M], ne[M], idx; // 邻接表存储所有边，匈牙利算法中只会用到从第一个集合指向第二个集合的边，所以这里只用存一个方向的边
        int match[N]; // 存储第二个集合中的每个点当前匹配的第一个集合中的点
        bool st[N]; // 表示第二个集合中的每个点是否已经被遍历过
        
        bool find(int x) // 找女孩儿，找到（配对）就返回true
        {
            for (int i = h[x]; i != -1; i = ne[i]) {
                int j = e[i];
                if (!st[j]) // 没有完全搞懂这个算法，这里可以类比dfs记住需要判断是否已经搜过
                {
                    st[j] = true;
                    if (match[j] == 0 || find(match[j])) // j女孩儿有对象就让她对象重新找
                    {
                        match[j] = x;
                        return true;
                    }
                }
            }
        
            return false;
        }
        
        // 求最大匹配数，依次枚举第一个集合中的每个点能否匹配第二个集合中的点
        int res = 0;
        for (int i = 1; i <= n1; i ++ ) // 每个哥们找一次，成功找到res就+1
        {
            memset(st, false, sizeof st); // 每个哥们儿开始找之前，每个女孩儿都没被搜过
            if (find(i)) res ++ ;
        }
        ```
      

26. **数论**

    - 素数定理 $\pi(n) \sim \frac{n}{\ln n} , n \rightarrow \infty$

    - 试除法判定质数 $O(\sqrt n)$

      - ```cpp
        bool is_prime(int x) {
            if (x < 2) return false;
            for (int i = 2; i <= x / i; i ++ ) // 优化枚举范围
                if (x % i == 0)
                    return false;
            return true;
        }
        ```

    - 试除法求所有质因子 $O(\sqrt n)$

      - ```cpp
        void get_prime_divisors(int x) {
            for (int i = 2; i <= x / i; i ++ ) // n最多有一个大于sqrt(n)的质因子
                if (x % i == 0) // 只要这里成立，i一定是质数
                {
                    int s = 0;
                    while (x % i == 0) {
                        x /= i;
                        s ++ ;
                    }
                    cout << i << ' ' << s << endl;
                }
            if (x > 1) cout << x << ' ' << 1 << endl; // 单独处理大于sqrt(n)的质因子
            cout << endl;
        }
        ```

    - 埃氏筛法筛质数 $O(n \log \log n)$

      - ```cpp
        int primes[N], cnt; // primes[]存储所有素数
        bool st[N]; // st[x]存储x是否被筛掉（被筛掉就是合数）
        
        void get_primes(int n) {
            for (int i = 2; i <= n; i ++ ) {
                if (st[i]) continue; // 跳过合数
                primes[cnt ++ ] = i;
                for (int j = i + i; j <= n; j += i)
                    st[j] = true;
            }
        }
        ```

    - 欧拉筛法筛质数 $O(n)$

      - ```cpp
        int primes[N], cnt;     // primes[]存储所有素数
        bool st[N];         // st[x]存储x是否被筛掉（被筛掉就是合数）
        
        void get_primes(int n) // n会且只会被最小的质因子筛（所以也可这样推出时间复杂度线性）
        {
            for (int i = 2; i <= n; i ++ ) {
                if (!st[i]) primes[cnt ++ ] = i;
                for (int j = 0; primes[j] <= n / i; j ++ ) {
                    st[primes[j] * i] = true; 
                    	// 分类讨论可证明primes[j]一定是primes[j] * i的最小质因子：
                    	// 1. i % primes[j] == 0
                    	// 注意到我们的质数是从小到大迭代的，所以这种情况下primes[j]是i的最小质因子，那么它也就是primes[j] * i的最小质因子
                    	// 2. i % primes[j] ！= 0
                    	// 同样，由于质数是从小到大迭代的，可以知道primes[j]小于i的最小质因子，从而推出primes[j]是prime[j] * i的最小质因子
                    	// 所以我们每次用最小质因子筛掉一个合数，且后面保证合数只会被筛一次
                    if (i % primes[j] == 0) break;
                    	// primes[j]是i的质因子的情况下应该break
                    	// 否则下一次for循环迭代时，primes[j + 1]不再是primes[j + 1] * i的最小质因子（因为primes[j]才是），造成重复筛
                }
            }
        }
        ```

    - 试除法求所有因数

      - ```cpp
        vector<int> get_divisors(int x) {
            vector<int> res;
            for (int i = 1; i <= x / i; i ++ )
                if (x % i == 0) {
                    res.push_back(i);
                	if (i != x / i) res.push_back(x / i);
                }
            sort(res.begin(), res.end());
            return res;
        }
        ```

    - 因数个数与因数之和

      - 先将$n$分解质因子：$n = p_ 1^{c_1}p _2^{c_2}...p_k^{c_k}$

        - 则因数个数：$ (c_1 + 1)(c_2 + 1)...(c_k + 1)$
        - 因数之和：$ (p_1^0 + p_1^1 + ... + p_1^{c_1})...(p_k^0 + p_k^1 + ... + p_k^{c_k})$

      - ```cpp
        // 因数个数
        
        typedef unsigned long long ULL;
        
        ULL get_divisors_cnt(int x) {
            unordered_map<int, int> p_divisors; // key是质因子，value是其指数
        
            for (int i = 2; i <= x / i; i ++ ) {
                while (x % i == 0) {
                    x /= i;
                    p_divisors[i] ++ ;
                }
            }
            if (x > 1) p_divisors[x] ++ ;
            
            ULL res = 1;
            for (auto p : p_divisors) res = res * (p.second + 1) % mod;
            	// 不能写成res *= (p.second + 1) % mod，因为要先乘再取模
            	// 每次乘完都取模是为了防止overflow，在c++中溢出会导致出现预期之外的数字
            
            return ans；
        }
        
        ```

      - ```cpp
        // 因数之和
        
        typedef unsigned long long ULL;
        
        int get_divisors_sum(int x) {
            unordered_map<int, int> p_divisors; // key是质因子，value是其指数
        
            for (int i = 2; i <= x / i; i ++ ) {
                while (x % i == 0) {
                    x /= i;
                    p_divisors[i] ++ ;
                }
            }
            if (x > 1) p_divisors[x] ++ ;
            
            ULL ans = 1;
            for (auto p : p_divisors) {
                ULL res = 1;
                for (int i = 0; i < p.second; i ++ ) 
                    res = (res * p.first + 1) % mod; // 每次乘完都取模防止overflow
                
                ans = ans * res % mod; // 每次乘完都取模防止overflow
            }
        
        	return ans;
        }
        ```

    - 最大公因数

      - 欧几里得算法（辗转相除法）

      - ```cpp
        int gcd(int a, int b) {
            return b ? gcd(b, a % b) : a;
        }
        ```

28. **dp 的思路**

    - 状态表示 $f(i, j)$ 
      - 化零为整：把一群方案归为一个集合
      - 状态表示其实是一个集合到属性的映射；集合由 $i$, $j$ 确定，属性就是 $f(i, j)$ 这个表达式存的值
      - 集合：例如前 $i$ 件物品放入体积为 $j$ 的背包的放法的集合（在全集中加上 $i$, $j$ 的限制）
      - 属性（**一般为 $max\{x\}$, $min\{x\}$, 元素数量**）：例如上述每个放法中的 **$w$ 的最大总和**就是属性
      - 所以在这个例子中，状态 $f(i, j)$ 就是前 $i$ 件物品放入体积为 $j$ 的背包的放法中 $w$ 的最大总和
    - 状态转移
      - 化整为零：把问题拆解为子问题
      - 从上述的角度来看，状态转移就可以理解为集合划分：把 $i$, $j$ 确定的集合**划分为若干个子集**，用这些子集对应的属性 $f$ 计算出当前集合的 $f$ ；集合划分一般要不重不漏（有时可以重复，例如[最长公共子序列]([AcWing 897. 最长公共子序列 - AcWing](https://www.acwing.com/activity/content/problem/content/1005/))，由于属性是 $max\{ x \}$ ，在划分时有重复也不影响正确性）；必须要保证划分后的集合是子集
      - 例如把集合（此处即放法）划分为放第 $i$ 件物品的放法和不放第 $i$ 件物品的放法
    - 优化：对状态转移方程做等价变形
      - 例如 $01$ 背包问题状态可以优化为一维：因为 $f(i, j)$ 的状态转移对于 $i$ 只用到了 $f(i-1)$ ，所以可以考虑删去 $i$ 这一维，每次 $i$ 的状态直接覆盖 $i-1$的状态即可；删去后再考虑 $j$ 这一维度，调整到和原来**等价**即可

29. **背包问题**

    - 初始化:

      - 不要求恰好装满：$\forall 0\leqslant j\leqslant V,f(0, j) = 0$ 

      - 必须恰好装满： $f(0,0) = 0; \forall 1\leqslant j\leqslant V,f(0,j) = -\infin$

        - ```cpp
          // 类似0x3f，可以用0xc0c0c0c0代表负无穷
          memset(f, 0xc0, sizeof f);
          ```

    -  $01$ 背包问题：每种物品仅有一件

      - $f(i, j) = max\{f(i-1, j), f(i-1, j-v_i)+w_i\}$，$j-v_i\geqslant0$
      - 优化空间：从大到小遍历 $j$

    - 完全背包问题：每种物品有无限件

      - 优化时间： $f(i, j) = max\{f(i-1, j), f(i, j-v_i)+w_i\}$，$j-v_i\geqslant0$
      - 优化空间：从小到大遍历 $j$

    - 多重背包问题：第 $i$ 种物品有 $s_i$ 件

      - 朴素做法：$f(i, j) = max\{f(i-1, j-v_ik)+w_ik\}$，其中 $j-v_ik\geqslant0$ 且 $0\leqslant k\leqslant s_i$

        - 优化空间：同 $01$ 背包，从大到小遍历 $j$

      - 二进制优化：

        - ```cpp
          // 对一个体积为a，价值为b，最大数量为s的物品打包
          // 对第一个物品打包前cnt初始化为0
          int k = 1;
          while (k <= s) {
              cnt ++ ;
              v[cnt] = a * k;
              w[cnt] = b * k;
              s -= k;
              k *= 2;
          }
          if (s > 0) {
              cnt ++ ;
              v[cnt] = a * s;
              w[cnt] = b * s;
          }
          
          n = cnt; // 打包之后改一下n就回到01背包问题
          ```

    - 分组背包问题：物品被划分为 $n$ 组，每组中的物品最多选一件

      -  $f(i, j)$ 现在代表前 $i$ 组物品放入体积为 $j$ 的背包的最大总价值
      -  $f(i, j) = max\{f(i-1, j),f(i-1, j-v_{ik})+w_{ik}\}$, $_{ik}$， 代表第 $i$ 组第 $k$ 个物品， $j-v_{ik} \geqslant0$

    - 二维费用的背包问题

      - 例如 $f(i, j, k)$ 表示前 $i$ 件物品放入体积为 $j$ 且最大承重为 $k$ 的背包的最大总价值
      - $f(i, j, k) = max\{f(i-1, j, k), f(i-1, j - v_i, k - u_i)\} + w_i$，$j\geqslant v_i$ 且 $k\geqslant u_i$
      - 有时“二维费用”的条件是以这样一种隐含的方式给出的：最多只能取 $U$ 件物品；这事实上相当于每件物品多了一种“件数”的费用，且每个物品的件数费用均为 $1$ 

30. **dp tips**

    - 初始化：

        - 显然，对于合法的状态集合，只需将 $f$ 初始值置为其属性；而对于不合法的无效状态集合，其属性应视情况置为一个表示不合法的值，**使得这个状态不会在状态转移时起作用**；

        - 某些边界上的初始状态直接根据题意来看是不合法的，但是当我们在状态转移方程中用到它时，它总对应于一种合法的情况，这种情况也可以**根据需要**把它初始化，以[整数划分](https://www.acwing.com/problem/content/902/)中的（二维）完全背包解法为例：

            - ```cpp
                // dp[i][j]表示把前i个数字（这i种物品）放入容量为j的背包的放法数量
                // 初始化时也许会觉得dp[k][0]是不合法的，但是注意到状态转移：
                dp[i][j] = dp[i - 1][j] + dp[i][j - i];
                // 当我们用到dp[k][0]时，意味着这是在第二项中j == i的情况（因为j >= 1，不会是第一项)
                // 也就是说，用到dp[k][0]时，它对应的集合为把前k个数字放入容量为j(等于k)的背包且放了第k个数字的放法，这就只有只放第k个数字这一种放法，故应该将dp[k][0]置为1
                ```
            
        - **update:** 全面、细致地分析状态转移，**递归地找到最小子状态/最小子问题/base case**，其中合法的置为其属性，不合法的置为更新时不会起作用的值

    - 循环的迭代顺序：只需保证在状态转移时用到的子状态是符合预期（符合状态转移方程）的

        - 线性 dp 一般考虑需要更新前的还是更新后的状态，从而确定从前往后或从后往前迭代
        - 区间 dp 一般按区间长度 -> 区间左端点迭代
    
    - ==集合划分的依据：==
    
        - 一般：“寻找最后一个不同点” （by y总）；具体来说，使状态**达到当前状态**有很多方式，通常根据这些方式中的**最后一步操作方式**来划分
    
        - 在上述的“一般情况”下，我们的 dp 数组代表的是**到达**某个状态后的某个属性，例如背包问题中，`dp[i][j]`表示**到达**把前`i`件物品放入体积为`j`的背包这个状态后的某属性，我们自然就可以去按照最后一步的操作方式来划分集合；在具体的问题中，什么是“最后一步”呢？可以有几个思考角度：
          1. 有些问题中操作具有一定的顺序，此时我们容易从时间顺序上得到“最后一步”是什么
    
          2. 如果操作本身没有时间上的顺序，我们可以人为定一个操作的时间顺序：例如背包问题，把前`i`件物品放入体积为`j`的背包有很多放法，没有要求以怎样的先后顺序去放这些物品，但是我们可以规定一个顺序：先放第`1`件，再放第`2`件，依此类推……（这个顺序对于线性 dp 是很适用的），则最后一步就是放第`i`件；或者看位置顺序：仍以背包问题为例，则最后一步可以是放位置最靠后的一件即第`i`件
          3. 注意这里的顺序也给我们指引了**循环的迭代顺序**
    
        - **但有时**，我们定义的 dp 数组是反过来的，它代表从某个状态**开始**的某属性，例如[OpenJudge: 滑雪](http://bailian.openjudge.cn/practice/1088/)中，`dp[i][j]`表示从`[i][j]`点出发能滑的最大长度，这时自然就可以按照第一步的操作方式来划分集合
    
    - dp 的实现方式之二：记忆化搜索
    
        - 具体来说，用递归的 dfs 实现状态转移，并且记录已经算好的状态，此后再次用到时直接查表，不重复计算
    
        - [如何写记忆化搜索？](https://oi-wiki.org/dp/memo/)这里提供的两种方法刚好把题目大概地分了两类，可以根据具体题目去选择：
            - 适合按第一种方法写的是“更 dp 的”一类问题，这种方法本质上是把一个 dp 问题用递归 + 记忆化数组来实现，其实和用循环写差别不大：
                - 把这道题的 dp 状态和方程写出来
    
                - 根据它们写出 dfs 函数
                - 添加记忆化数组
    
            - 适合按第二种方法写的是“更搜索的”一类问题，这类问题的搜索特征很强，一眼看去就很容易想到搜索（例如[Openjudge: 滑雪](http://bailian.openjudge.cn/practice/1088/)），然后我们发现这类问题**不只是搜索，其问题可以由子问题来转移，且存在大量重复的子问题**，这就想到了用 dp 来处理；然而由于这本质是个搜索问题，用常规写 dp 的方法，即**循环，是不好实现状态转移的**（比如最常见的线性 dp，它具有某种线性，虽然这种线性我目前不知道该怎么表达，但搜索是基于图的，图的拓扑结构比线性复杂）；但我们可以**在搜索（一般是 dfs ）时借助 dp 的思想，添加记忆化数组**记录已经计算好的子状态，避免大量重复计算：
                - 写出这道题的暴搜程序（最好是 dfs ）
    
                - 将这个 dfs 改成「无需外部变量」的 dfs ==原文的用意暂时还没有完全体会到==
                - 添加记忆化数组
            - 我们可以对比[OpenJudge: 滑雪](http://cxsjsx.openjudge.cn/hw202314/A/)和[OpenJudge: 怪盗基德的滑翔翼](http://cxsjsx.openjudge.cn/hw202314/C/)来体会拓扑结构（不知道这词儿用得准不准）对 dp 实现的影响：
                - 在 怪盗基德的滑翔翼 中，我们只有一行线性的点，在两个方向的滑行中，每个点可以往比自己低的点转移，由于这种（可以感觉到但难以描述的）线性的结构，我们容易用两重循环完成状态转移
                - 在 滑雪 中，每个点可以往周围四个比自己低的点转移，这是一件不好用循环描述，但极其容易用 dfs 描述的事情，所以我们选择记忆化搜索，结合 dfs 的形式和 dp 的思想来解决此问题

