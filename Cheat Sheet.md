## 1. 双指针
链表题:
1. 合并两个有序链表
2. 链表的分解
3. 合并 `k` 个有序链表
4. 寻找单链表的倒数第 `k` 个节点
5. 寻找单链表的中点
6. 判断单链表是否包含环并找出环起点
7. 判断两个单链表是否相交并找出交点
### 1.1 左右指针
本质: 利用题目中的某种 (单调) 性质, 使第二个指针 (数组中一般用`j`) 在第一个指针 (一般用`i`) 移动或者说指向下一元素时, **不需要从头开始** (这里的头不一定是下标`0`, 而是指`j`的初始值), 只需要从上一次到的位置继续移动, 将 $O(n^2)$ 的暴力算法优化到 $O(n)$; 做题时往往可以**先想暴力算法**, 再去寻找 (单调) **性质**优化时间复杂度.
```cpp
for (int i = 0, j = 0; i < n; i++ ) // 初始值因题目而已
{
	while (j < i && check(i, j)) // 不一定是j < i, 也可能是j < n等等
		j++;

  // 具体问题的逻辑
}
```
**常见问题分类:**
1. 对于一个序列, 用两个指针维护一段区间.
2. 对于两个序列, 维护某种次序, 比如归并排序中合并两个有序序列的操作.

最长回文子串, 滑动窗口最值.
### 1.2 快慢指针
## 2. 离散化
本质: 当有些数据因为本身很大或者类型不支持, 自身无法作为数组的下标来方便地保存对应的属性 (不能 or 能但**时间复杂度太高**), 而影响最终结果的只有元素之间的相对属性时, 我们可以将原来的数据**按顺序**映射到小的自然数序列`{1, 2, 3...}`或`{0, 1, 2}`等等 (也就是按顺序编号) 来处理问题, 即离散化. 事实上离散化是一种特殊的 (哈希函数保序的) 哈希.
```cpp
vector<int> alls; // 存储所有待离散化的值
sort(alls.begin(), alls.end()); // 将所有值排序
alls.erase( unique(alls.begin(), alls.end()), alls.end() );// 去掉重复元素

// 二分求出x对应的离散化后的值
int bisect_left(int x) {  // 找到满足alls[y] >= x的y的左边界, 即x离散化后对应的值y
	int l = 0, r = alls.size() - 1;
	while (l < r) {
		int mid = l + r >> 1;
		if (alls[mid] >= x) r = mid;
		else l = mid + 1;
	}
	return r + 1; // 加1是为了映射到1, 2, ... , alls.size()
	// `return r;`是映射到l_init, l_init + 1, ... , r_init
	// ATTENTION: 在具体题目中使用离散化结果时 (例如遍历) , 务必注意离散化结果的取值区间；如 算法题.md 3.区间和 中预处理前缀和时i是从1取到alls.size()
}

// 此二分也可以用STL完成
std::lower_bound(alls.begin(), alls.end(), x)；
//存在这样的x则返回左边界迭代器, 否则返回alls.end()
```
tips: `erase()`是容器的成员函数, 应该用`.
[AcWing: 区间和](https://www.acwing.com/problem/content/804/)
## 3. Trie
```Python
# Trie implementation in Python 

class TrieNode:
	def __init__(self):
		# pointer array for child nodes of each node
		self.childNode = [None] * 26
		self.wordCount = 0
		
def insert_key(root, key):
	# Initialize the currentNode pointer with the root node
	currentNode = root

	# Iterate across the length of the string
	for c in key:
		# Check if the node exist for the current character in the Trie.
		if not currentNode.childNode[ord(c) - ord('a')]:
			# If node for current character does not exist
			# then make a new node
			newNode = TrieNode()
			# Keep the reference for the newly created node.
			currentNode.childNode[ord(c) - ord('a')] = newNode
		# Now, move the current node pointer to the newly created node.
		currentNode = currentNode.childNode[ord(c) - ord('a')]
	# Increment the wordEndCount for the last currentNode
	# pointer this implies that there is a string ending at currentNode.
	currentNode.wordCount += 1
	
def search_key(root, key):
	# Initialize the currentNode pointer with the root node
	currentNode = root

	# Iterate across the length of the string
	for c in key:
		# Check if the node exist for the current character in the Trie.
		if not currentNode.childNode[ord(c) - ord('a')]:
			# Given word does not exist in Trie
			return False
		# Move the currentNode pointer to the already existing node for current character.
		currentNode = currentNode.childNode[ord(c) - ord('a')]

	return currentNode.wordCount > 0

def delete_key(root, word):
	currentNode = root
	lastBranchNode = None
	lastBrachChar = 'a'

	for c in word:
		if not currentNode.childNode[ord(c) - ord('a')]:
			return False
		else:
			count = 0
			for i in range(26):
				if currentNode.childNode[i]:
					count += 1
			if count > 1:
				lastBranchNode = currentNode
				lastBrachChar = c
			currentNode = currentNode.childNode[ord(c) - ord('a')]

	count = 0
	for i in range(26):
		if currentNode.childNode[i]:
			count += 1

	# Case 1: The deleted word is a prefix of other words in Trie.
	if count > 0:
		currentNode.wordCount -= 1
		return True

	# Case 2: The deleted word shares a common prefix with other words in Trie.
	if lastBranchNode:
		lastBranchNode.childNode[ord(lastBrachChar) - ord('a')] = None
		return True
	# Case 3: The deleted word does not share any common prefix with other words in Trie.
	else:
		root.childNode[ord(word[0]) - ord('a')] = None
		return True
```
## 4. 最长连续不重复子序列
```cpp
#include <iostream>
#include <vector>

using namespace std;

int s[100010];

int main() {
	ios::sync_with_stdio(false);
	
	int n;
	vector<int> a;
	cin >> n;
	for (int i = 0; i < n; ++i) {
		int temp;
		cin >> temp;
		a.push_back(temp);
	}
	
	int cnt = 0;
	for (int i = 0, j = 0; i < n; ++i) {
		++s[a[i]];
		while (s[a[i]] > 1) {
			--s[a[j]];
			++j;
		}
		cnt = max(cnt, i - j + 1);
	}
	cout << cnt << endl;
}
```
1. 遍历数组a中的每一个元素a[i], 对于每一个i，找到j使得双指针[j, i]维护的是以a[i]结尾的最长连续不重复子序列，长度为i - j + 1, 将这一长度与r的较大者更新给r。
2. 对于每一个i，如何确定j的位置：由于[j, i - 1]是前一步得到的最长连续不重复子序列，所以如果[j, i]中有重复元素，一定是a[i]，因此右移j直到a[i]不重复为止（由于[j, i - 1]已经是前一步的最优解，此时j只可能右移以剔除重复元素a[i]，不可能左移增加元素，因此，j具有“单调性”、本题可用双指针降低复杂度）。
3. 用数组s记录子序列a[j ~ i]中各元素出现次数，遍历过程中对于每一个i有四步操作：cin元素a[i] -> 将a[i]出现次数s[a[i]]加1 -> 若a[i]重复则右移j（s[a[j]]要减1） -> 确定j及更新当前长度i - j + 1给r。
## 5. 表达式·表达式树·表达式求值
```Python
import operator as op

class Node:
    def __init__(self, x):
        self.value = x
        self.left = None
        self.right = None

def priority(x):
    if x == '*' or x == '/':
        return 2
    if x == '+' or x == '-':
        return 1
    return 0

def infix_trans(infix):
    postfix = []
    op_stack = []
    for char in infix:
        if char.isalpha():
            postfix.append(char)
        else:
            if char == '(':
                op_stack.append(char)
            elif char == ')':
                while op_stack and op_stack[-1] != '(':
                    postfix.append(op_stack.pop())
                op_stack.pop()
            else:
                while op_stack and priority(op_stack[-1]) >= priority(char) and op_stack[-1] != '(':
                    postfix.append(op_stack.pop())
                op_stack.append(char)
    while op_stack:
        postfix.append(op_stack.pop())
    return postfix

def build_tree(postfix):
    stack = []
    for item in postfix:
        if item in '+-*/':
            node = Node(item)
            node.right = stack.pop()
            node.left = stack.pop()
        else:
            node = Node(item)
        stack.append(node)
    return stack[0]

def get_val(expr_tree, var_vals):
    if expr_tree.value in '+-*/':
        operator = {'+': op.add, '-': op.sub, '*': op.mul, '/': op.floordiv}
        return operator[expr_tree.value](get_val(expr_tree.left, var_vals), get_val(expr_tree.right, var_vals))
    else:
        return var_vals[expr_tree.value]

def getDepth(tree_root):
    left_depth = getDepth(tree_root.left) if tree_root.left else 0
    right_depth = getDepth(tree_root.right) if tree_root.right else 0
    return max(left_depth, right_depth) + 1
'''
首先，根据表达式树的值和深度信息构建第一行，然后构建第二行，该行包含斜线和反斜线，
用于表示子树的链接关系。接下来，如果当前深度为0，表示已经遍历到叶子节点，直接返回该节点的值。
否则，递减深度并分别获取左子树和右子树的打印结果。最后，将左子树和右子树的每一行拼接在一起，
形成完整的树形打印图。
    
打印表达式树的函数。表达式树是一种抽象数据结构，它通过树的形式来表示数学表达式。在这段程序中，
函数printExpressionTree接受两个参数：tree_root表示树的根节点，d表示树的总深度。
首先，函数会创建一个列表graph，列表中的每个元素代表树的一行。第一行包含根节点的值，
并使用空格填充左右两边以保持树的形状。第二行显示左右子树的链接情况，使用斜杠/表示有左子树，
反斜杠\表示有右子树，空格表示没有子树。

接下来，函数会判断深度d是否为0，若为0则表示已经达到树的最底层，直接返回根节点的值。否则，
将深度减1，然后递归调用printExpressionTree函数打印左子树和右子树，
并将结果分别存储在left和right中。

最后，函数通过循环遍历2倍深度加1次，将左子树和右子树的每一行连接起来，存储在graph中。
最后返回graph，即可得到打印好的表达式树。
'''
def printExpressionTree(tree_root, d):  # d means total depth
    graph = [" "*(2**d-1) + tree_root.value + " "*(2**d-1)]
    graph.append(" "*(2**d-2) + ("/" if tree_root.left else " ")
                 + " " + ("\\" if tree_root.right else " ") + " "*(2**d-2))

    if d == 0:
        return tree_root.value
    d -= 1
    '''
    应该是因为深度每增加一层，打印宽度就增加一倍，打印行数增加两行
    '''
    #left = printExpressionTree(tree_root.left, d) if tree_root.left else [
    #    " "*(2**(d+1)-1)]*(2*d+1)
    if tree_root.left:
        left = printExpressionTree(tree_root.left, d)
    else:
        #print("left_d",d)
        left = [" "*(2**(d+1)-1)]*(2*d+1)
        #print("left_left",left)

    right = printExpressionTree(tree_root.right, d) if tree_root.right else [
        " "*(2**(d+1)-1)]*(2*d+1)

    for i in range(2*d+1):
        graph.append(left[i] + " " + right[i])
        #print('graph=',graph)
    return graph

infix = input().strip()
n = int(input())
vars_vals = {}
for i in range(n):
    line = input().split()
    vars_vals[line[0]] = int(line[1])
'''
infix = "a+(b-c*d*e)"
#infix = "a+b*c"
n = 5
vars_vals = {'a': 2, 'b': 7, 'c': 5, 'd':1, 'e':1}
'''
postfix = infix_trans(infix)
tree_root = build_tree(postfix)
print(''.join(str(x) for x in postfix))
expression_value = get_val(tree_root, vars_vals)

for line in printExpressionTree(tree_root, getDepth(tree_root)-1):
    print(line.rstrip())

print(expression_value)
"""分割线, 以下为补充内容, 用于把没空格分隔的中序表达式s做成列表expr"""
expr = [""]
for i in range(len(s)):
	c = s[i]
	if c in "+-*/()":
		expr.append(c)
		expr.append("")
	else:
		expr[-1] += c
expr = [c for c in expr if c != '']
```
```cpp
#include <iostream>

using namespace std;

int itemv();
int factorv();

int expv() {
    int v = itemv();
    while (true) {
        char c = cin.peek();
        if (c == '+') {
            cin.get();
            v += itemv();
        }
        else if (c == '-') {
            cin.get();
            v -= itemv();
        }
        else break;
    }

    return v;
}

int itemv() {
    int v = factorv();
    while (true) {
        char c = cin.peek();
        if (c == '*') {
            cin.get();
            v *= factorv();
        }
        else if (c == '/') {
            cin.get();
            v /= factorv();
        }
        else break;
    }

    return v;
}

int factorv() {
    int v;
    char c = cin.peek();
    if (c == '(') {
        cin.get();
        v = expv();
        cin.get();
    }
    else cin >> v;

    return v;
}

int main() {
    printf("%d", expv());
}
```
```Python
def expr():
    value = item()
    while s:
        if s[-1] == '&':
            s.pop()
            value = item() and value
        elif s[-1] == '|':
            s.pop()
            value = item() or value
        else:
            break
        #print(s)

    return value

def item():
    if s[-1] == '!':
        s.pop()
        return not item()

    if s[-1] == '(':
        s.pop()
        value = expr()
        s.pop()
        return value
    value = toBool[s.pop()]
    return value

while True:
    try:
        toBool = {'V': True, 'F': False}
        s = [x for x in input() if x != ' '][::-1]
        print('V' if expr() else 'F')
    except EOFError:
        break
"""分割线"""
while True:
    try:
        s=input()
    except EOFError:
        break
    s=s.replace('V','True').replace('F','False')
    s=s.replace('&',' and ').replace('|',' or ').replace('!',' not ')
    if eval(s):
        print('V')
    else:
        print('F')
```
## 6. 快排与快速选择算法
```Python
def quick_sort(arr, l, r):
	'''对[l, r]闭区间排序'''

    # base case
    if l == r:
        return
    
    # 第一步: 分成子问题
    i, j = l - 1, r + 1  # 双指针
    pivot = arr[l + r >> 1]
    while i < j:
        i += 1
        while arr[i] < pivot:
            i += 1
        j -= 1
        while arr[j] > pivot:
            j -= 1
        if i < j:
            arr[i], arr[j] = arr[j], arr[i]
    
    # 第二步: 递归处理子问题
    quick_sort(arr, l, j)
    quick_sort(arr, j + 1, r)
    
    # 第三步: 子问题合并 (快排这一步不需要操作, 但归并排序的核心在这一步骤) 
```
```Python
def quick_select(arr, l, r, k):
	'''选择出[l, r]区间中排序后的第k个数并返回'''
	if l == r:
		return arr[l]

	i, j = l - 1, r + 1
	pivot = arr[l + r >> 1]
	while i < j:
		i += 1
		while arr[i] < pivot:
			i += 1
		j -= 1
		while arr[j] > pivot:
			j -= 1
		if i < j:
			arr[i], arr[j] = arr[j], arr[i]

	if k <= j - l + 1:
		return quick_select(arr, l, j, k)
	return quick_select(arr, j + 1, r, k - j + l - 1)
	# 如果k > j - l + 1, 当前序列第k个数就是右边子序列第k - j + l - 1个数
```
## 7. 求逆序数
```Python
def merge_sort(arr: list, l, r):
    '''将arr[l: r]排好, 并返回该切片 (左闭右开) 内的逆序数'''
    if r - l == 1:
        return 0
    
    mid = l + r >> 1
    inv = 0
    inv += merge_sort(arr, l, mid)
    inv += merge_sort(arr, mid, r)

    temp = []
    i = l
    j = mid
    while i < mid and j < r:
        if arr[i] <= arr[j]:
            temp.append(arr[i])
            i += 1
        else:
            temp.append(arr[j])
            j += 1
            inv += mid - i # 此步计算逆序数
    
    while i < mid:
        temp.append(arr[i])
        i += 1
    while j < r:
        temp.append(arr[j])
        j += 1

    for k in range(l, r):
        arr[k] = temp[k - l]

    return inv
```
## 8. 单调栈和单调队列
单调栈/单调队列只是**在加入元素前先弹出一些元素**, 使得栈/队列中元素单调严格递增/严格递减/不增/不减, 从而降低时间复杂度或维护出某些性质 (其用途十分单一, 后文会进一步说明). 所以这其实是很朴素的东西, 完全没有必要怕.

具体做题时分两种情况:
1. 题目明确给了单调栈/单调队列 tag 提示: 一方面可以考虑先用栈/队列暴力 (穷举) 模拟, 然后挖掘一些性质 (**加入元素前先弹出一些不会有用的元素**), 使得栈/队列有单调性, 降低时间复杂度; 另一方面可以考虑将题目条件"强行"转化成单调栈/单调队列能实现的几个**单一用途**.
2. 没有 tag 提示: 只能在平时训练对单调栈/单调队列能实现的几个**单一用途**的敏感度.
### 8.1 单调栈
如上所述, 单调栈的用途十分单一, 具体来说它只用来**维护数组的所有前缀 (子串) 的后缀最值 (或其下标)**, 这个最值可以是最大/最小值, 也可以是严格 (或者说唯一) 最大/最小值. 具体来说单调栈中就按原数组顺序存储了当前前缀的所有后缀最值 (或其下标). 另外, 单调栈的"单调"当然指的是其存储数组元素的值时是单调的, 但因为它按原数组顺序来存储, 所以它在存储下标时也是单调的 (常常利用这一点来做**二分**, 例如[[Problems#92. 奶牛排队]]).

当然单调栈也会有一些变形和等价的用途, 常见的有:
1. 将数组颠倒后再入栈, 显然也就可以**维护数组的所有后缀的前缀最值 (或其下标)**.
2. 找数组中每个数左/右边第一个大于/小于/不大于/不小于它的数 (或其下标). 以找每个数左边第一个大于它的数为例, 根据后缀最值的定义就可以发现, 每个数 (设下标为`i`) 左边第一个大于它的数, 其实就是前缀`arr[:i + 1]` (切片右边是开区间) 的后缀严格最大值中除`arr[i]`以外的最右边一个, 或者说是后缀严格最大值栈在加入`arr[i]`前的栈顶元素.
***
变形一下也可以处理子序列的后缀最值, 例如:
上升子序列每个元素都是子序列中的后缀最小值,
最长上升子序列 (LIS) 除 dp 的  $O(n^2)$ 做法外, 也可以用单调栈 + 二分以 $O\log(n)$ 解决. [证明及代码见此题解](https://blog.csdn.net/weixin_63001635/article/details/136486681?spm=1001.2101.3001.6650.5&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-5-136486681-blog-123542640.235%5Ev43%5Epc_blog_bottom_relevance_base7&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-5-136486681-blog-123542640.235%5Ev43%5Epc_blog_bottom_relevance_base7&utm_relevant_index=10)

子串的情况弹完后只需要在后面加, 弹掉的就不要了 (因为要弹出, 所以直接二分查到位置也没用, 不如直接 while 循环, 每个元素最多入栈出栈一次,还是 O(n); 子序列的情况弹掉后也还要加进来, 更好的做法显然是所以用二分+替换, 后面的元素不要动, 显然这时其实和栈就没太大关系了, 因为单纯的栈结构肯定只支持在栈顶增删元素, 替换这个逻辑是不行的, 所以这个时候叫单调队列也可以. **严格说来这就不是单调"栈"也不是单调"队列", 只能叫做单调动态数组.**
***
具体实现也很简单, 以维护数组的所有前缀的后缀严格最大值下标 (顺便记录每个数左边第一个大于它的数) 为例, 显然这需要维护一个严格递减栈:
```Python
stack = []  # 存储当前前缀的后缀严格最大值下标
res = []  # 记录每个数左边第一个大于它的数, 没有记为 None
for i in range(n):  # 前缀右端点 i 每次加 1
	while stack and arr[stack[-1]] <= arr[i]:
		stack.pop()
		
	# 此时栈顶是 arr[i] 左边第一个比它大的数的下标
	res.append(arr[stack[-1]] if stack else None)
	stack.append(i)
	# 此时栈中是前缀 arr[:i + 1] 的所有后缀严格最大值下标
```
### 8.2 单调队列
```Python
from collections import deque

# 滑动窗口最大值, 维护递减队列
n, k = [int(x) for x in input().split()]
arr = [int(x) for x in input().split()]
q = deque([0])
out = []
for i in range(1, k):
    while q and arr[i] >= arr[q[-1]]:
        q.pop()
    q.append(i)
out.append(arr[q[0]])

for i in range(k, n):
    if i - k >= q[0]:
        q.popleft()
    #if not q or arr[i] > arr[q[0]]:
        #while q:
            #q.popleft()
    while q and arr[q[-1]] <= arr[i]:
        q.pop()
    q.append(i)
    out.append(arr[q[0]])

print(*out)
```
### 8.3 求区间所有后缀最大值的位置
```cpp
#include <vector> 
#include <array> 
#include <queue> 
#include <iostream> 
int n, k; 
std::deque<int> q; 
std::array<unsigned long long, 1000005> a; 
int main() { 
	std::ios::sync_with_stdio(false); 
	std::cin.tie(nullptr); 
	std::cin >> n >> k; 
	for (int i = 1; i <= n; ++i) { 
		std::cin >> a.at(i); 
		if (q.size() && q.front() + k - 1 < i) q.pop_front(); 
		while (q.size() && (a.at(i) >= a.at(q.back()))) { 
		q.pop_back(); 
		} 
		q.push_back(i); 
		if (i >= k) std::cout << q.size() << '\n'; 
	} 
}
```
## 9. 链表
## 10. 字符串哈希-前缀哈希
核心思想: 将字符串看成 P 进制数, P 的经验值是 131 或 13331 , 取这两个值的**冲突概率低**.

作用: 判断字符串是否相等.

tips: 
-   取模的数用$2^{64}$, 这样直接用`unsigned long long`存储, 溢出的结果就是取模的结果
-   一般情况下, 不要把某个字符映射到 $0$
-   此方法不考虑冲突的处理, 我们认为按经验参数基本上不会遇到冲突
```cpp
int P = 131; // 或13331
typedef unsigned long long ULL;
ULL h[N], p[N]; 
// h[k]存储字符串前k个字符的哈希值, p[k]存储 P^k mod 2^64
// 一般让字符串从下标1开始, 而h[0]赋为0, 方便初始化前缀哈希

// 初始化, 预处理前缀的哈希
h[0] = 0;
p[0] = 1;
for (int i = 1; i <= n; i ++ ) {
    h[i] = h[i - 1] * P + str[i - 1];
    // 这里减1是因为我们使用的字符串是从下标0开始的, 下标i - 1即第i个字符
    // 另一种等价的处理方式是把字符串读到str + 1这个指针里 (使用char str[]型字符串) 
    p[i] = p[i - 1] * P;
}

// 计算子串 str[l ~ r] 的哈希值
ULL get(int l, int r) {
    return h[r] - h[l - 1] * p[r - l + 1];
}
```
## 11. 树
遍历 or 分解问题?

### 11.1 Generic Tree and Binary Tree
上述表示方法事实上把多叉树转化成了二叉树, 转换的代码如下: 
```Python
class Node:
	"""多叉树节点"""
    def __init__(self):  
        self.children = []  
        self.next_sibling = None  

class Node2:
	"""二叉树节点"""
    def __init__(self):  
        self.left = None  
        self.right = None

def convert(root: Node) -> Node2:
	"""将以root为根的多叉树转换为二叉树, 并返回该二叉树的根节点"""
	# base case
    if not root:  
        return None  

	# step 1: 建根
    binary_root = Node2()  
    # step 2: 将原多叉树的第一个子树 (转换出的二叉树) 连接为二叉树的左子树
    if root.children:  
        binary_root.left = convert(root.children[0])  
	# step 3: 将原多叉树的其他子树 (转换出的二叉树) , 按顺序 (类似链表) 连接为前一棵子树 (转换出的二叉树) 的右子树
    curr = binary_root.left  
    for i in range(1, len(root.children)):  
        curr.right = convert(root.children[i])  
        curr = curr.right  
	# step 4: return
    return binary_root

def convert(root: Node2) -> Node:  
	"""将以root为根的二叉树还原为多叉树, 并返回该多叉树的根节点"""
	# step 1: 建根
    new_root = Node(root.val)  
    left = root.left  
    if left:  
	    # step 2: 将二叉树的左子树 (转换出的多叉树) 连接为多叉树的第一个子树
        new_root.children.append((convert(left)))  
		# step 3: 从将二叉树的左子节点的右子节点开始向右按链表遍历, 每次将以当前节点为根的子树 (转换出的多叉树) 连接到根
        cur = left.right  
        while cur:  
            new_root.children.append((convert(cur)))  
            cur = cur.right    
    # step 4: return
    return new_root
```
### 11.2 Summary of Tree Building
1. 梳理树的结构, **找到根和子树**. 可能需要先递归定义xxx, 再将其对应到树而得到 (例如表达式树, [[Problems#[21. OpenJudge 文件结构“图”](http//cxsjsx.openjudge.cn/hw202313/B/)|文件结构“图”]]) , 也可能直接从“位置”获取信息, 例如根据前中后序遍历序列之二建树, 或者是综合的信息, [[Problems#74. 树的镜面映射|例如]]给出满二叉树 (要么是叶节点要么有两个子节点) 的前序遍历序列, 其中叶节点有标记. 
2. 按照两类方法之一建树. 
   1) Top-down: 做出root, **递归**建子树, 连接子树, **返回root**. Base case是leaf或空树. 
   2) Bottom-up: 做出root, 从**栈**中弹出子树根节点, 连接子树, 存到栈中；如果是leaf, 直接存到栈中. 最后栈中唯一元素即root. [例题及参考代码](http://dsbpython.openjudge.cn/dspythonbook/solution/44430461/)
3. 有时也通过每次插入一个节点 (到合适位置) 的方式建树, 例如建 BST, 以及堆 (不过建堆有更好的方式, 见[[#3.7.2 Implementation|堆的实现]]). 

#todo ==重新总结==
注: 题目中的树可能本身就不是递归定义的, 此时也不应该用递归去建树. 显然的道理是, 树是如何定义的 (题中的输入如何描述出一棵树) 就如何去建树. 例如[森林的带度数层次序列存储](http://cs101.openjudge.cn/2024sp_routine/07161/)中给的序列是层次遍历的结果, 此时自然就应该用层次遍历框架来建树.
### 11.3 Huffman Algorithm
#### 11.3.1 Steps
Huffman 算法用于构造一棵 Huffman 树, 算法步骤如下: 
1. **初始化**: 由给定的 $n$ 个权值构造 $n$ 棵只有一个根节点的二叉树, 得到一个二叉树森林 $F$. 
2. **选取与合并**: 从二叉树集合 $F$ 中选取根节点权值**最小**的两棵二叉树分别作为左右子树构造一棵新的二叉树, 这棵新二叉树的根节点的权值为其左、右子树根结点的权值和. 
3. **删除与加入**: 从 $F$ 中删除作为左、右子树的两棵二叉树, 并将新建立的二叉树加入到 $F$ 中.
4. 重复2, 3步, 当森林中只剩下一棵二叉树时, 这棵二叉树就是霍夫曼树. 
![](https://raw.githubusercontent.com/xiyouluo/md_images/main/images/huffman_tree_202403301515337.png)
#### 11.3.2 Properties
树的带权路径长度: 设二叉树具有 $n$ 个带权叶节点, 从根节点到各叶节点的路径长度与相应叶节点权值的乘积之和称为树的带权路径长度 (Weighted Path Length, WPL) , 也称带权外部路径长度 (Weighted External Path Length) . 

对于给定一组具有确定权值的叶节点, 可以构造出不同的二叉树, 其中, **WPL 最小的二叉树称为Huffman 树.**
对于霍夫曼树来说, 其叶节点权值越小, 离根越远, 叶节点权值越大, 离根越近, 此外其仅有叶结点的度为 $0$, **其他结点度均为 $2$** (从算法过程的“合并”可以看出) . 
#### 11.3.3 Huffman Code
在进行二进制编码时, 假设所有的代码都等长, 那么表示 $n$ 个不同的字符需要 $\left \lceil \log_2 n \right \rceil$ 位, 称为等长编码. 

如果每个字符的使用频率相等, 那么等长编码无疑是空间效率最高的编码方法, 而如果字符出现的频率不同, 则可以让频率高的字符采用尽可能短的编码, 频率低的字符采用尽可能长的编码, 来构造出一种不等长编码, 从而获得更好的空间效率. 

在设计不等长编码时, 要考虑解码的唯一性, 如果一组编码中任一编码都不是其他任何一个编码的前缀, 那么称这组编码为前缀编码, 其保证了编码被解码时的唯一性. 

Huffman 树可用于构造**最短的前缀编码**, 即 Huffman 编码, 其构造步骤如下: 

1. 设需要编码的字符集为: $d_1,d_2,\dots,d_n$, 他们在字符串中出现的频率为: $w_1,w_2,\dots,w_n$. 
2. 以 $d_1,d_2,\dots,d_n$ 作为叶节点, $w_1,w_2,\dots,w_n$ 作为叶节点的权值, 构造一棵 Huffman 树. 
3. 规定哈夫曼编码树的左分支代表 $0$, 右分支代表 $1$, 则从根结点到每个叶结点所经过的路径组成的 $0,1$​ 序列即为该叶节点对应字符的编码. 
```Python
from heapq import heapify, heappush, heappop
from functools import lru_cache

class Node:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None
    def __lt__(self, o):
        return self.val < o.val

#@lru_cache(None)
def minLen(root, d):
    if not root.left:
        return root.val * d
    return minLen(root.left, d + 1) + minLen(root.right, d + 1)

n = int(input())
forest = [Node(int(x)) for x in input().split()]
heapify(forest)
while len(forest) > 1:
    a = heappop(forest)
    b = heappop(forest)
    c = Node(a.val + b.val)
    c.left = a
    c.right = b
    heappush(forest, c)

root = forest[0]
print(minLen(root, 0))
```

### 11.4 二叉堆
建堆的说明: 
1. `insert`每个元素建堆: 每次 $\log n$, 总复杂度 $O(n\log n)$, 不好；
2. Top-down 建堆: 按层次遍历顺序, `percUp`每个节点, 从根开始一层一层构建堆 (保持堆性质) ；注意到第 $k$ 层节点需要操作 $\log k$ 次, 总时间复杂度为 $\log 1 + \log 2 + \cdots + \log n = O(n \log n)$, 其中用到了 $\text{Stirling}$ 公式. 
3. Bottom-up 建堆: 从最底层向上, `percDown`每个节点, 有堆性质的子树逐渐合并；第 $k$ 层需要操作 $\log n - k$ 次, 总复杂度 $n\log n - \log 1 - \log 2 - \cdots -\log n = O(n)$, 同样用到 $\text{Stirling}$ 公式；另外注意到叶节点不需要`percDown`, 所以可以从`n // 2`开始, 节约一些检查是否需要`percDown`的时间. 

注意: 删除最小值, 直接pop底层最右再加到顶, 需要注意原堆只有一个元素的情形下这样操作会加到None, RE.

其他的注意事项在注释中. 
```Python
class BinHeap:
    def __init__(self):
        self.heapList = [0] # 用一个元素占住`0`索引位置, 便于用列表表示完全二叉树. 
        self.currentSize = 0

    def percUp(self, i): # percolate
        while i // 2 > 0:
            if self.heapList[i] < self.heapList[i // 2]:
                self.heapList[i], self.heapList[i // 2] = self.heapList[i // 2], self.heapList[i]
            i = i // 2

    def insert(self, k):
        self.heapList.append(k)
        self.currentSize = self.currentSize + 1
        self.percUp(self.currentSize)

    def percDown(self, i):
        while (i * 2) <= self.currentSize:
            mc = self.minChild(i)
            if self.heapList[i] > self.heapList[mc]:
                self.heapList[i], self.heapList[mc] = self.heapList[mc], self.heapList[i]
            i = mc

    def minChild(self, i):
        if i * 2 + 1 > self.currentSize:
            return i * 2
        else:
            if self.heapList[i * 2] < self.heapList[i * 2 + 1]:
                return i * 2
            return i * 2 + 1

    def delMin(self):
        retval = self.heapList[1]
        self.heapList[1] = self.heapList[self.currentSize]
        self.currentSize -= 1
        self.heapList.pop() # 不pop掉的话insert时调用append就加错位置了！
        self.percDown(1)
        return retval

    def buildHeap(self, alist):
        i = len(alist) // 2
        self.currentSize = len(alist)
        self.heapList = [0] + alist[:] # copy一份！
        while (i > 0):
            self.percDown(i)
            i = i - 1
```
### 11.5 BST
建 BST 的一般方法是逐个加入节点；且 **BST 的新加入节点一定是叶节点**. 

需要注意由于python变量的“指针”特性, 需要让`insert_node`函数有返回值才能对实参进行修改, 详见[[notes_py#补充例子|此处]]. 

 BST 删除操作: 与右子树最小节点 (即最左节点) 互换, 再移除.
### 11.6 并查集
最后问哪些杯子还有可乐, 需要扫一遍`p[]`, 找到`p[i] == i`的`i`. 
#### 11.6.1 Union by size
显然每次把`size`小的树接到大的树更快, 因为有路径压缩, 每个点都要接到根上, 把`size`小的树接到大的上接的点少一些. 
```Python
def unionBySize(x, y):
    root_x = find(x)
    root_y = find(y)

    if root_x == root_y:
        return

    if size[root_x] < size[root_y]:
        p[root_x] = root_y
        size[root_y] += size[root_x]
    else:
        p[root_y] = root_x
        size[root_x] += size[root_y]
```
需要询问连通块中点的数量也可以维护`size`, 例如[连通块中点的数量](https://www.acwing.com/problem/content/description/839/).
#### 11.6.2 带权并查集
在这种并查集中, 两个点在同一棵树中并不说明他们是一类, 只说明我们对这两个点“有信息”. 

最简单的情形是: 已知一共只有两个集合, 现在给定一些信息, 这些信息只说明两个点**不在**一个集合, 然后需要判断询问的两个点的关系, 即在同一集合、在不同集合或不确定. 这时, 我们可以维护一个到根节点的距离, 如果两个点的这个距离在模 2 的意义下相等, 就说明在同一集合, 不等就说明在不同集合, 如果两个点的根节点不同就说明根据当前信息还不确定二者的关系. [例题](http://cs101.openjudge.cn/2024sp_routine/01703/)

最后实现上, 我们只存储到父节点的距离, 在调用`find`连接到根上后才是到根节点的距离. 与普通并查集类似, 带权并查集的**核心**是`p[x]`永远代表`x`的**父节点**, `d[x]`永远代表`x`到**父节点**的距离, 明确这一点就容易在`union`和`find`的过程中正确**维护**这两个信息, 而当需要使用其根节点及到根节点的距离时, 简单地调用`find(x)`将其连接到根上再访问`p[x]`及`d[x]`即可.
```Python
p = [i for i in range(n + 1)]
d = [0] * (n + 1)

def find(x):
    """将x及x所有祖先节点(不含根)连接到它们共同的根上, 且更新距离；最后返回这个根节点"""
    if p[x] == x:
        return x
    u = find(p[x])
    d[x] += d[p[x]]
    p[x] = u
    return u

def union(a, b):
	"""将a的根连接到b的根, 并更新a的根的距离"""
	root_a = find(a)
	root_b = find(b)
	p[root_a] = root_b
	d[root_a] = f(d[a], d[b])
```


## 12. 图
### 12.1 图的遍历
值得一提的是, "找所有解"可以用**生成器**来优美、高效地完成, 注意其中递归调用需要改为`yield from`. [[#3.4.3 Implementation|CSPs Implementation]]中就用生成器实现.
#### Framework 1: Recursion
常用的 DFS 事实上就是**搜索树的前序遍历**, 但**不访问已访问过的点** (到过的点就不再去调用 dfs).
```python
def dfs(node):
    """
    前序遍历以node为根的搜索树, 但不访问已访问过的点;
    没有 base case, 靠搜完来终止.
    """
    # step1: 前序位置访问根
	visited.add(node)
    
	# step2: 扩展节点, 遍历子树
	for nb in node.nbs:
		if nb not in visited:
			dfs(nb)


visited = set()
dfs(startNode)
```
#### Framework 2: Iteration
##### 通用写法
```python
def search(startNode):
	container = Container([startNode])
	# stack -> dfs, queue -> bfs, heap -> ucs / A*
    expanded = set()
    
    while container:
        node = container.pop()
        # 在这里访问节点可以重复访问
        if node not in expanded: # 1. 找到下一个要扩展的节点
	        # 在这里访问节点就只访问一次
	        # 2. 扩展
	        for nb in node.nbs: 
		        # if nb not in expanded:
	            container.push(nb)

			# 3. 扩展完做标记
			expanded.add(node) 
```
注:
6. 如果节点中要存储 mutable 类型的变量, 务必**给每个节点拷贝一份, 避免其指向同一对象**.
7. 在 UCS 及堆优化 $\text{Dijkstra}$ 算法中, 因为需要用到堆, 所以需要节点支持`<`比较, 我们常常把节点写为一个元组, 且把需要排序的属性放到最前面, 从而借助元组的比较方式实现这一点. 但这个方法中需要注意**不能把无法比较大小的类型也写进元组**, 如[Saving Tang Monk](http://cs101.openjudge.cn/2024sp_routine/solution/44871932/)中, 每个节点保存了蛇的状态`st`, 这是一个字典, 把它放到节点元组中导致两个元组比较时出现比较字典的行为, 导致`TypeError: '<' not supported between instances of 'dict' and 'dict'`. 且使用元组会导致排序时多比较其他属性, 这也不太高效. 综合下来更宜**写类**并重载`__lt__`.
##### BFS 常见写法
```python
def bfs():
	queue = deque([startNode])
    visited = {startNode} # 改个名比较好, 因为其含义是是否已入过队
    
    while queue:
        node = queue.popleft()
        
        for nb in node.nbs:
            if nb not in visited:
                queue.append(nb)
                visited.add(nb)
```
如上所述, 这个写法对 BFS 来说是正确的, 但仅将队列换成栈后不是 DFS, 也不能套用到 UCS.
### 12.2 搜索枚举算法
#### 12.2.1 问题分类
dimension 1: 一个终点/所有点, 这是容易处理的, 前者就是图的遍历加一个走到终点时终止.

dimension 2: 一种走法/所有走法, 后者是 **NP-hard** 问题, 解决方法: dfs + 回溯或存储路径为状态. 如果求无权最短路的走法数就可以用不做任何标记的 BFS, 不做标记就是多了有环的路径, 而有环的走法肯定不是最短的, 所以这样也对, 只需在步数达到最短路 + 1 时终止即可, 例题[我是最快的马](http://cs101.openjudge.cn/2024sp_routine/07206/).

此外, 需要注意有些问题中编号相同的顶点由于其他约束的限制, 需要在**以"更优"的属性到达该点时重复扩展顶点** (例如[[Problems#[37. OpenJudge Saving Tang Monk](http //cxsjsx.openjudge.cn/hw202317/A/)|Saving Tang Monk]], [[Problems#[50. OpenJudge ROADS](http //bailian.openjudge.cn/practice/1724)|ROADS]]等). 

另外注意一件很平凡的事情, 判断两点/整个图是否 (强) 连通显然不用去暴搜所有走法, 只需要做一遍图的遍历即可. 如[电话线路](http://cs101.openjudge.cn/2024sp_routine/21515/) 二分壳子里的`check`函数是要检验删去一些边后`1`能不能到`N`, 这是线性时间 (图的遍历) 就可以解决的, 不要想成 NP 问题.
#### 12.2.2 优化时间复杂度
##### 重整约束
##### Grouping/Bucketing
另一方面, 如果 $V$ 中的状态可以根据某个"性质"归为一些类 (或者说装到一些**桶**中), 每一类里的方案虽然不同, 但其对本题的"影响"相同, 我们就可以以一个状态代替一类状态, 从而**减小状态空间**.

具体来说, "影响"相同可以这样定义:

只要该类中一个状态符合要求, 这一类就都符合要求; 反过来, 只要其中一个不符合要求, 这一类就都不符合要求, 且从这个不符合要求的类中任选一个状态用来**转移** (即从搜索树的当前节点得到子节点) 都是等价的. 换句话说就是本题只和状态的这一"性质"有关.

事实上这也就是闫氏 dp 分析法中状态表示的思路: 将一群方案**化零为整**归为一个集合.

例题及题解: [[Problems#78. Find The Multiple]].

如果二类约束是二元或多元的, 如词梯, 桶. #todo ==总结==
##### 调整扩展顺序
Warnsdorf's rule: 在同一点的子节点中优先扩展度数少的子节点 (统计度数时不计算已访问过的节点), 这一 heuristics 也可应用于其他问题.
##### 发现约束
只需要枚举一个局部, (因为存在约束) 整个状态就确定了. 例如熄灯问题.
##### 剪枝
**可行性剪枝**事实上就是提前使用二类约束, 可以在[[#重整约束]]中处理;

需要**留意** (重要的是往这方面想, 因为这类剪枝一般都很简单) 的是最优化问题中的**最优性剪枝**. 如果要求的性质简单, 也可以先[[#调整扩展顺序]]来保证搜索到的第一个解就是最优解, 然后终止.
### 12.3 CSPs
```Python
def backtrackingSearch():
    """
    从assignment这个状态开始, 按顺序给所有未赋值variables赋值, 直到找到解或者发现无解;
    也就是前序遍历以assignment为根的树.
    """
    # step1: 访问根
    # 因为按照顺序赋值, 不会有重复, 不需要visited集合
	if isSolution(assignment): # 只需检查是否赋值完
		yield solutionResult(assignment)  # 注意避免每一个解都指向同一个对象
		return  # 完全赋值后就不应该再有状态转移了, 此即 base case

	# steps: 遍历子树
    var = nextVariable(assignment)
	for value in var.domain:
		if check(assignment, var, value): 
			assign(assignment, var, value)
			yield from backtrackingSearch()
			undoAssign(assignment, var)  # "恢复", 但不一定需要
```
### 12.4 拓扑排序, 环检测
#### 12.4.1 拓扑排序
```Python
def dfs(u):
	# 做一般图的 dfs, 后序位置加入点
    st[u] = True

    for v in graph[u]:
        if st[v] is False:
            dfs(v)

    sq.appendleft(u)
    
def topo_sort():
	q = deque()
	topo_sq = []
	for node in graph:
		if indegree[node] == 0:
			q.append(node)

	while q:
		node = q.popleft()
		topo_sq.append(node)
		for nb in node.nbs:
			indegree[nb] -= 1  
			if indegree[nb] == 0:
				q.append(nb)
```
#### 12.4.2 环检测
```Python
def dfs(u):
    st[u] = 1 # 访问中

    for v in adj[u]:
        if st[v] == 0: # 未访问
	        # prev[v] = u  # 无向图要记录父节点
	        if dfs(v):
		        return True
        elif st[v] == 1: # 子节点v在访问中, 说明搜索树中可以从v到v, 有环
	        # if prev[u] != v:  # 无向图中要排除的情况
			return True

    st[u] = 2 # 访问完
	return False
```
$\text{Kahn}$ 算法运行完后拓扑序列中的点就是图中所有可以拓扑排序的点 (入度可能变成 0 的点), 故**若拓扑序列中的点数小于图的总点数就说明存在环, 且环的总点数为图的总点数减去拓扑序列中的点数**.
### 12.5 SCCs
后序序列中的所有点满足: 对每一点, 所有"指向"它的点都在它后面. 所以对每一个块就有, **所有"指向"它的块都在它后面**, 这就满足了前面的要求.

所以算法的流程为:
1. 迭代所有顶点, 若该顶点没有访问过, 就从它开始做强连通图的 DFS, 在 DFS 的**后序位置**将当前节点加入到序列`sq`中.
2. 按`sq`的顺序, 迭代所有顶点, 若该顶点没有访问过, 就从它开始做强连通图的遍历, 每次获得一个 SCC.

$\text{Kosaraju}$ 给出了一个类似的方法, 借助转置 (有向图 $G$ 的转置是指将其所有边反向后得到的图 $G^T$) 完成, 实际上绕了个圈子: 求**转置**图的**逆**后序序列作为`sq`; 另一种实现是求原图的**逆**后序序列作为`sq`, 按`sq`的顺序对**转置**图做遍历.
### 12.6 最短路
[差分约束系统](https://oi-wiki.org/graph/diff-constraints/)可转化为最短路.
#### 朴素 $\text{Dijkstra}$ 算法
```Python
def dijkstra(graph: List[List[int], n: int, k: int]):
	# 稠密图用邻接矩阵graph, 其中存边权, 无边存无穷大
	expanded = set()
	curDist = [float('inf') for _ in range(n)]
	curDist[k] = 0
	
	for _ in range(n - 1): # 确保不重复扩展(显然最后一个点可以不扩展, 优化为n - 1)
		# 1. 找到没有扩展过的点中到起点距离最短的点u
		u = None
		for v in range(n):
			if v not in expanded and (u is None 
					\ or curDist[v] < curDist[u]):
				u = v
		# u到起点的最短距离和对应的路径在此时就确定了

		# 2. 扩展该点
		for v in graph[u]:
			# 扩展意味着搜索树中nb成为(或暂时成为, 取决于搜索树类型)minNode的子节点
			dist[v] = min(dist[v], dist[u] + graph[u][v])
			# 此处可以记录父节点以显式构建出无重复节点型搜索树(而不只是在脑子里)

		#  3. 扩展完标记该点
		expanded.add(u)
```
#### 堆优化的 $\text{Dijkstra}$ 算法
```Python
def dijkstra(graph: List[List[int]], n: int, k: int):
	# 稀疏图用邻接表graph, 其中graph[node]存储以(nb, w)形式存储邻居和边权
	expanded = [False for _ in range(n)]
	curDist = [float('inf') for _ in range(n)]
	curDist[k] = 0

	h = [(0, k)]
	while h:
		# 1. 找到没有扩展过的点中到起点距离最短的点node
		node = heappop(h)
		if not expanded[node]: # 确保不重复扩展
			# 2. 扩展
			for nb, w in graph[node]:
				# 扩展意味着搜索树中nb(或暂时成为, 取决于搜索树类型)node的子节点
				newDist = curDist[node] + w
				if newDist < curDist[nb]: # 剪枝
					curDist[nb] = newDist
					heappush(h, (newDist, nb))
			# 3. 扩展完标记该点
			expanded[node] = True
```
堆优化版本便于处理 (由于其他约束) 需要重复扩展节点的问题 (例题: [ROADS](http://cs101.openjudge.cn/2024sp_routine/01724/)), 而朴素版本此时不好设定扩展次数.
### 12.7 MST
1. 生成树: 最大无环, 最小连通.
2. If each edge has a distinct weight then there will be only one, i.e., a unique minimum spanning tree.
3. For any cut **C** of the graph, if the weight of an edge **E** in the cut-set of **C** is strictly smaller than the weights of all other edges of the cut-set of **C**, then this edge belongs to [all the MSTs of the graph](https://www.geeksforgeeks.org/total-number-spanning-trees-graph/).
4. For any cycle **C** in the graph, if the weight of an edge **E** of **C** is larger than the individual weights of all other edges of **C**, then this edge cannot belong to an **MST**.
5. 最小生成树是瓶颈生成树 (最大边权最小的生成树) 的充分不必要条件.
注: 整个 MST 部分没有提到**负权**边, 但介绍的两个算法在有负权边时也是正确的.
#### 12.7.1  $\text{Prim}$ 算法
##### 朴素 $\text{Prim}$ 算法
```Python
def prim(graph: List[List[int]], n: int):
	# 稠密图用邻接矩阵graph, 其中存边权, 无边存无穷大
	curDist = [float('inf') for _ in range(n)] # 点到当前树的最小距离, 是边权
	inMST = [False for _ in range(n)] # 标记是否已加入到MST中
	totalWeight = 0

	for _ in range(n): # 每次加一个点一条边到树中(第一次只加点不加边)
		# 1. 通过枚举点找到连接树和树外一点的最短边
		minNode = None
		for node in range(n):
			if not inMST[node] and (minNode is None 
					\ or curDist[node] < curDist[minNode]):
				minNode = node

		# 2. 把最短边及其连接的树外点加入到MST中(第一次循环只加点不加边)
		if i != 0:  # 当然也可将起点的 curDist 初始化为 0, 则此处无需判断
			totalWeight += curDist[minNode]
			# 如果这条最短边为inf, 就代表该树外点与树中任一点都不连通, 即原图是不连通的
		inMST[minNode] = True

		# 3. 更新树外节点到树的最小距离
		for nb in graph[minNode]:
			curDist[nb] = min(curDist[nb], graph[minNode][nb])

	return totalWeight
```
##### 堆优化的 $\text{Prim}$ 算法
```Python
def prim():
    dist = [float('inf')] * n
    dist[0] = 0
    in_mst = set()
    ret = 0
    h = [(0, 0)]
    while h:
        d, u = heappop(h)
        if u not in in_mst:
            ret += d
            in_mst.add(u)
            for v in range(n):
                w = calc_weight(s[u], s[v])
                if w < dist[v]:
                    dist[v] = w
                    heappush(h, (w, v))

    return ret
```

但有时 (用 Python) 会出现朴素 $\text{Prim}$ 和 $\text{Kruskal}$ 都 ac 不了的情况, 要么超内存要么超时, 例如[Truck History](http://cs101.openjudge.cn/2024sp_routine/01798/), 此时可以试试堆优化的 $\text{Prim}$.
##### $\text{Kruskal}$ 算法
```Python
p = [x for x in range(n)]

def union(a, b):
	p[find(a)] = find(b)

def find(x):  
    if p[x] != x:  
        p[x] = find(p[x])  
    return p[x]  
  
def kruskal(edges: List[Tuple[int]]):  
	# 边以edges[i] = (w, a, b)形式存储
    edges.sort()  
    totalWeight = 0  
    for w, a, b in edges:
        if find(a) != find(b):
            union(a, b)
            totalWeight += w
			# 此处可统计添加的边数, 如果添加的总边数小于 n - 1 就说明图不连通

    return totalWeight
```
## 13. 数学
### 13.1 素数定理

$$\pi(n) \sim \frac{n}{\ln n} , n \rightarrow \infty$$

### 13.2 试除法判定质数 $O(\sqrt n)$
```cpp
  bool is_prime(int x) {
      if (x < 2) return false;
      for (int i = 2; i <= x / i; i ++ ) // 优化枚举范围
          if (x % i == 0)
              return false;
      return true;
  }
```
### 13.3 试除法求所有质因子 $O(\sqrt n)$
```cpp
  void get_prime_divisors(int x) {
      for (int i = 2; i <= x / i; i ++ ) // n最多有一个大于sqrt(n)的质因子
          if (x % i == 0) // 只要这里成立, i一定是质数
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
### 13.4 埃氏筛法筛质数 $O(n \log \log n)$
```cpp
  int primes[N], cnt; // primes[]存储所有素数
  bool st[N]; // st[x]存储x是否被筛掉 (被筛掉就是合数) 
  
  void get_primes(int n) {
      for (int i = 2; i <= n; i ++ ) {
          if (st[i]) continue; // 跳过合数
          primes[cnt ++ ] = i;
          for (int j = i + i; j <= n; j += i)
              st[j] = true;
      }
  }
```
### 13.5 欧拉筛法筛质数 $O(n)$
```cpp
  int primes[N], cnt;     // primes[]存储所有素数
  bool st[N];         // st[x]存储x是否被筛掉 (被筛掉就是合数) 
  
  void get_primes(int n) // n会且只会被最小的质因子筛 (所以也可这样推出时间复杂度线性) 
  {
      for (int i = 2; i <= n; i ++ ) {
          if (!st[i]) primes[cnt ++ ] = i;
          for (int j = 0; primes[j] <= n / i; j ++ ) {
              st[primes[j] * i] = true; 
              	// 分类讨论可证明primes[j]一定是primes[j] * i的最小质因子: 
              	// 1. i % primes[j] == 0
              	// 注意到我们的质数是从小到大迭代的, 所以这种情况下primes[j]是i的最小质因子, 那么它也就是primes[j] * i的最小质因子
              	// 2. i % primes[j] ！= 0
              	// 同样, 由于质数是从小到大迭代的, 可以知道primes[j]小于i的最小质因子, 从而推出primes[j]是prime[j] * i的最小质因子
              	// 所以我们每次用最小质因子筛掉一个合数, 且后面保证合数只会被筛一次
              if (i % primes[j] == 0) break;
              	// primes[j]是i的质因子的情况下应该break
              	// 否则下一次for循环迭代时, primes[j + 1]不再是primes[j + 1] * i的最小质因子 (因为primes[j]才是) , 造成重复筛
          }
      }
  }
```
### 13.6 试除法求所有因数
```cpp
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
### 13.7 因数个数与因数之和
- 先将$n$分解质因子: $n = p_ 1^{c_1}p _2^{c_2}...p_k^{c_k}$

  - 则因数个数: $(c_1 + 1)(c_2 + 1)...(c_k + 1)$
  - 因数之和: $(p_1^0 + p_1^1 + ... + p_1^{c_1})...(p_k^0 + p_k^1 + ... + p_k^{c_k})$

```cpp
  // 因数个数
  
  typedef unsigned long long ULL;
  
  ULL get_divisors_cnt(int x) {
      unordered_map<int, int> p_divisors; // key是质因子, value是其指数
  
      for (int i = 2; i <= x / i; i ++ ) {
          while (x % i == 0) {
              x /= i;
              p_divisors[i] ++ ;
          }
      }
      if (x > 1) p_divisors[x] ++ ;
      
      ULL res = 1;
      for (auto p : p_divisors) res = res * (p.second + 1) % mod;
      	// 不能写成res *= (p.second + 1) % mod, 因为要先乘再取模
      	// 每次乘完都取模是为了防止overflow, 在c++中溢出会导致出现预期之外的数字
      
      return ans；
  }
  
```
```cpp
  // 因数之和
  
  typedef unsigned long long ULL;
  
  int get_divisors_sum(int x) {
      unordered_map<int, int> p_divisors; // key是质因子, value是其指数
  
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
### 13.8 最大公因数
- 欧几里得算法 (辗转相除法) 
```cpp
  int gcd(int a, int b) {
      return b ? gcd(b, a % b) : a;
  }
```
### 13.9 扩展欧几里得算法
```Python
def ex_gcd(a, b):
    if b == 0:
        return a, 1, 0
    gcd, x1, y1 = ex_gcd(b, a % b)
    return gcd, y1, x1 - (a // b) * y1

while True:
    a, b, c, k = map(int, input().split())
    if k == 0:
        break
    a, b, c = c, 1 << k, b - a
    gcd, x0, y0 = ex_gcd(a, b)
    if c % gcd != 0:
        print('FOREVER')
    else:
        period = abs(b // gcd)
        if period != 0:
            print((c // gcd * x0) % period)
        else:
            print(0)
```
## 14. 出栈序列
模拟即可, 记模式串为`p`，检查串为`s`，从左向右扫`s`，对其每一个字符`t`，将在`p`中`t`及其左边的（未进过栈的）字符压入栈，并标记已入栈（每个字符只入栈一次），检查当前字符`t`是否为栈顶元素（显然第一个元素一定是满足的，可以参照输出`"NO"`的 case 看一下这个流程），是则检查下一个字符，否则不是合法出栈序列。另外，也需要先检查字符串长度，扫`s`的过程也需要判断`t`是否在`p`中。
```Python
def check(s: str, p: str) -> bool:
    if len(s) != len(p):
        return False
    
    f = {t: i for (i, t) in enumerate(p)}
    v = set()

    stk = [] # 装编号
    for i in range(len(s)):
        t = s[i]
        if t not in f:
            return False
        for j in range(f[t] + 1):
            if j not in v:
                stk.append(j)
                v.add(j)
        if f[t] != stk[-1]:
            return False
        else:
            stk.pop()
        
    return True
```
数量:
```Python
def dfs(n_push, n_pop):
	if n_push == 0 and n_pop == 0:
		return 1

	cnt = 0
	if n_push - 1 >= n_pop:
		cnt += dfs(n_push - 1, n_pop) # 当前状态由一次入栈得到
	if n_pop >= 1:
		cnt += dfs(n_push, n_pop - 1) # 由出栈得到

	return cnt
```
注意到有大量重复子问题，可以加个`lru_cache`或者改自底向上dp。

结果是 $\text{Catalan}$ 数，以下情景结果也是该数：合法括号匹配序列数，BST 的种类数；山形序列数；在一个网格中从左上角到右下角的路径数（只能向右或向下移动）。其解析表达式为：$$C_n = \frac{C_{2n}^n}{n + 1}.$$
Python 的组合数计算：`math.comb(2*n, n)`。
## 15. 动态中位数, 对顶堆
```Python
from heapq import heappush, heappop


T = int(input())
for case in range(T):
    arr = [int(x) for x in input().split()]
    h_max = []
    h_min = [arr[0]]
    res = [arr[0]]
    for i in range(1, len(arr)):
        if arr[i] <= h_min[0]:
            heappush(h_max, -arr[i])
        else:
            heappush(h_min, arr[i])
        if len(h_min) - len(h_max) == 3:
            heappush(h_max, -heappop(h_min))
        if len(h_max) - len(h_min) == 1:
            heappush(h_min, -heappop(h_max))
        if i % 2 == 0:
            res.append(h_min[0])

    print(len(res))
    print(*res)
```
## 16. 输入输出重定向
```Python
import sys

sys.stdin = open('input.txt', 'r')
sys.stdout = open('output.txt', 'w')
```
## 17. 链表
```Python
class Node:
    def __init__(self, data=None):
        self.data = data
        self.next = None

class LinkedList:
    def __init__(self):
        self.head = None

    def append(self, data):
        new_node = Node(data)
        if self.head is None:
            self.head = new_node
            return
        last = self.head
        while last.next:
            last = last.next
        last.next = new_node

    def prepend(self, data):
        new_node = Node(data)
        new_node.next = self.head
        self.head = new_node

    def insert_after_node(self, prev_node, data):
        if not prev_node:
            print("Previous node is not in the list")
            return
        new_node = Node(data)
        new_node.next = prev_node.next
        prev_node.next = new_node

    def delete_node(self, key):
        temp = self.head

        if temp is not None:
            if temp.data == key:
                self.head = temp.next
                temp = None
                return

        while temp is not None:
            if temp.data == key:
                break
            prev = temp
            temp = temp.next

        if temp is None:
            return

        prev.next = temp.next
        temp = None

    def delete_node_at_pos(self, position):
        if self.head is None:
            return

        temp = self.head

        if position == 0:
            self.head = temp.next
            temp = None
            return

        for i in range(position - 1):
            temp = temp.next
            if temp is None:
                break

        if temp is None or temp.next is None:
            return

        next = temp.next.next
        temp.next = None
        temp.next = next

    def length(self):
        count = 0
        current = self.head
        while current:
            count += 1
            current = current.next
        return count

    def print_list(self):
        current = self.head
        while current:
            print(current.data, end=" -> ")
            current = current.next
        print("None")

    def reverse(self):
        prev = None
        current = self.head
        while current is not None:
            next = current.next
            current.next = prev
            prev = current
            current = next
        self.head = prev

    def search(self, key):
        current = self.head
        while current:
            if current.data == key:
                return True
            current = current.next
        return False
```
