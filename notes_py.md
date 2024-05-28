
top-down, bottom-up
https://www.zhihu.com/question/410196236/answer/1373722221?utm_psn=1756480534099050496

递归第一步就是把问题拆成“子”问题, 子问题的规模更小, base case 是规模最小的子问题 (可能有多个) , 所以一定是 top-down 的；如果有一个问题可以由更大的问题解决, 也可以定义一个递归调用, 使得递归是按照bottom-up的逻辑进行的, 比如数学里的演绎推理, 但就计算机领域, 算法题而言, 我们从不会为了解决一个问题, 先去考虑规模更大的问题, 永远是将其拆解为子问题, 即便最后实现上可能是bottom-up, 例如非记忆化搜索的dp, 但这里up到最顶层也就是我们的原问题, 而非更大的问题. 所以递归和dp都是把问题转化为更小的子问题, 前者top-down地求解, 后者bottom-up地求解. 所以就算法题而言, 递归 (函数自我调用) 一定top-down, bottom-up一定不能用递归 (函数自我调用) 实现, 当你想到一个buttom-up的思路时, 不要试图用递归实现, 一定需要先把子问题“存”起来, 像bottom-up的dp一样. 
https://en.wikipedia.org/wiki/Recursion

递归就是函数自我调用, 是一种程序运行方式；
dp是一种high-level的想法, 可以有不同的实现方式, 狭义dp是bottom-up的, 但top-down的递归+cache也是几乎等价的 (时间复杂度一样, 常数大一些) 

#### 1. Python 变量的指针本质
广义地，可以把 python 中一切可赋值的东西都称为变量，而所有的变量都是指针（或者说引用），包括列表的元素，字典的值。其指针本质体现在赋值、函数形参、函数返回值、存放元素到容器中等处。**脑子里时时刻刻要有类似 pythontutor 里那种从 identifier 指向对象的箭头！**

- python 中赋值是让左侧变量指向右侧变量指向的对象（如果右侧是变量）或让左侧变量指向右侧对象（如果右侧是对象）

- 函数形参相当于用实参赋值得到，即形参指向实参变量指向的对象（如果实参是变量）或指向实参这个对象（如果实参是对象）

- 函数调用得到的返回值（如果有）相当于`return x`语句中`x`赋值给它，即返回值指向`x`指向的对象（如果`x`是变量）或返回值指向`x`这个对象（如果`x`是对象）：

  ```python
  a = [1, 2]
  def f():
      return a
  f()[0] = 0
  print(a)
  # output: [0, 2]
  ```

- 存放元素到容器中也一样，容器可赋值的地方都是广义的变量，也就都是指针：

  ```python
  a = []
  x = [0, 1]
  a.append(x) # a[-1]指向x指向的对象，不会copy一份x
  x[1] = 2
  print(a[0][1])
  # output: 2
  ```

`for item in comtainer`循环中，`item`也是指向`contain`的一个元素指向的对象。

赋值（其他情形也一样）后更改变量导致的行为则和变量类型是否可变有关：

> A class is immutable if each **object** of that class has a fixed value upon instantiation that cannot subsequently be changed. For example, the float class is immutable. Once an instance has been created, its value cannot be changed (although an **identifier** referencing that object can be reassigned to a different value).
> 
> *Data Structures and Algorithms in Python*

immutable or mutable 是针对 object 而言的，而非 identifier，immutable 指的是 identifier 指向的对象不可 mutate，赋值只是重新指向，不是 mutate。

在使用`*`运算符重复列表时，重复的元素是原始列表中相同对象的引用。这一点在处理可变对象（如列表）时尤其重要，因为修改一个地方会影响到其他引用相同对象的地方。

##### 补充例子
建 BST 时`insert_node`函数的错误例子：
```Python
def insert_node(root, key):
    if not root:
        root = Node(key)
        return
    if key < root.key:
        insert_node(root.left, key)
    else:
        insert_node(root.right, key)
    return
```
例如第一次执行到第6行递归调用`insert_node(root.left, key)`时，进入下一层函数后，这层函数的`root`和上一层函数的`root.left`这两个identifier只是指向了同一个对象（函数形参与实参的关系），在执行这层函数的第3行`root = Node(key)`时，赋值，只会将此时的`root`指向新的对象，不影响上一层的`root.left`，这个引用不是 C++ 那种引用（引用后二者就完全等价，改其中之一一定影响另一）。
#### 2. 批量处理列表元素

当需要，可以考虑：

- **拷贝**一个新列表，遍历一个修改一个
- 列表推导式（更适用于删元素）如`[i for i in ls if xxx]`
- 用`filter()`函数删除特定元素
- 在 for 循环遍历列表时增删元素时可能引起循环时索引混乱，不要这样做



#### 3. 字符串修改

Python 中字符串不可以直接修改某索引处的字符，但有`.replace()`方法可以帮助替换和删除（替换为空串即可）。



#### 4. 列表转字符串

`str(lst)`和`"string.join(lst)"`效果不同，前者是把一个列表作为参数传给`str`类的构造函数，调用`list`的`__str__`方法，这样构造出来的字符串是包含列表的方括号和逗号的，例如：

```python
my_list = ['apple', 'banana', 'orange']
result_str = str(my_list)
print(result_str)

# output: "['apple', 'banana', 'orange']"
```



#### 5. `print`函数

`print`函数的第一个参数为`*values`，数目不定，因此在需要指定`sep`或`end`时需要显式地指出`sep=`或`end=`，无法仅从位置判断；即不显式地指定时，所有字符串参数都传给了`*values`。

此外，多次调用`print()`函数可能花费较多不必要的时间，尝试一次性输出。



#### 6. 格式化输出保留两位小数

```python
"%.2f %(expr)""
"{.2f}".format(expr)
f"{expr:.2f}"
```

其他更细节的用法（考试时）可以在 Spyder 的 Console 中使用 `help(format)`查询



#### 7. 列表推导式进一步用法

- 用`if`进行条件筛选
  ```Python
  new_list = [expression for item in iterable if condition]
  ```

- 用`if-else`进行条件转换
```Python
new_list = [expression_if_true if condition else expression_if_false for item in iterable]
```

- 多重循环

  ```python
  # 生成一个包含 (x, y) 形式的坐标对的列表
  coordinates = [(x, y) for x in range(3) for y in range(3)]
  # 输出: [(0, 0), (0, 1), (0, 2), (1, 0), (1, 1), (1, 2), (2, 0), (2, 1), (2, 2)]
  ```

- 嵌套列表推导式

  ```python
  # 生成一个包含 2 维矩阵的列表
  matrix = [[i * j for j in range(3)] for i in range(3)]
  # 输出: [[0, 0, 0], [0, 1, 2], [0, 2, 4]]
  ```

此外，类似列表推导式，还有字典推导式, 集合推导式, 元组推导式：
```python
{key: value for (key, value) in iterable}
{expr for x in iterable}
tuple(expr for x in iterable)
```
但是元组推导式中要写`tuple`不能只写括号, 否则是生成器, 而生成器是一次性的, 一旦迭代完就耗尽 (exhausted) 了.
#### 8. 自定义排序

Python 中对类的实例排序和 C++ 一样依照`<`进行，因此可以写类，重载`<`运算符，即`__lt__`实现自定义排序；此外（虽然对排序没用），`<=`是`__le__`，`>`是`__gt__`，``>=`是`__ge__`。

此外，有多个排序特征时可以**利用元组的排序特点**：按照元组中的元素顺序从左到右逐个比较，直到找到不相等的元素或者其中一个元组的所有元素都被比较完；再用`lambda`函数传入`sort`的`key`参数中即可。



#### 9. 字典排序

- `.items()`**返回以`(key, value)`元组为元素的列表**，`.keys()`和`.values()`返回`key`或`value`的列表。
- 字典是基于哈希表的一种无序结构，因此没有`.sort()`方法，当需要对字典的键或值或键值对排序时，先用以上方法转为列表。
- 注意`sorted(my_dict)`会被认为是对键排序，类似于`if xxx in my_dict`也会将字典判断为它的键的可遍历列表；总结就是两种情况`my_dict`都被理解成了`my_dict.keys()`。
- 

#### 10. 进制转换

`int()`构造出来的对象是一个十进制的整数，即`int`类型，在这里指定`base=`是为了将括号内用其他进制表示的`str`(还有`bytes`和`bytearray`)转换为十进制整数，例如`int("0b101", base=2)`就会得到一个`int`类型的对象，其值为`5`；`int("101", base=2)`亦可。

需要把十进制转换为其他进制时，可以用内置函数：
- 二进制（binary）: 二进制数字以`0b`或`0B`为前缀，对应函数`bin()`
- 八进制（octal）: 八进制数字以`0o`或`0O`为前缀，对应函数`oct()`
- 十六进制（hexadecimal）: 十六进制数字以`0x`或`0X`为前缀，对应函数`hex()`

或者自己造轮子（短除法，用栈实现）。



#### 11. ASCII 和字符相互转换

- ASCII to char: `chr()`
- char to ASCII: `ord()`, where "ord" stands for ordinal.



#### 12. `sorted()`和`sort()`

- `sorted()`是 Python 内置函数，接收`Iterable[]`返回`List[]`。
- `sort()`是`list`类的方法。



#### 13. 一些函数、方法、模块

- `str`: `replace, find, rindex, .join(Iterable[str])`
- `list`: `index`
- `from itertools import permutations, accumulate`
- `zip()`：将多个`Iterable`以`tuple`形式打包，返回一个`zip`对象，`zip`对象是一个`tuple`的迭代器，需要时（例如要打出来看）可以转为`list`
- 日期相关问题：`import calendar`
- 中位数等统计问题：`import statistics`
- 哈希相关，例如 MD5 加密：`hashlib`



#### 14. 子矩阵截取

`A[i:p+1][j:q+1]`并不会截取矩阵`A`的第`i`行到第`p`行与第`j`列到第`q`列相交形成的子矩阵，显然第一步对行的截取`A[i:p+1]`可以完成，而第二步则不是在截取列，而是在新列表`A[i:p+1]`中继续截取行。





==101的笔记已经挪了前36条?==

#### 15. 切片（slicing）

切片适用于序列型对象（sequence type），如`str`,`list`,`tuple`。

一般语法为`sequence[start:stop:step]`。

- `start`或`stop`越界时，切片操作不会抛出异常，而是进行截断
- `start`比`stop`大时，仍然不会抛出异常，而是直接返回空序列
- `start`, `stop`, `:step`均可缺省，缺省和**写`None`**等效。
- `step`可以为负，代表倒着截取，`start`和`stop`仍然代表开始和结束位置。但是倒着从某位置截取到`0`且包含`0`不能把`stop`写作`-1`，这和`range`不同，因为`-1`这个索引代表最后一个位置，要截取到`0`且包含`0`时`stop`应该不写或写`None`。



#### 16. Python 的队列

Python 列表`list`的`pop(i)`在`i`为非负数索引时是 $O(n - i)$ 的，仅`pop()`或者说`pop(-1)`是 $O(1)$的，所以不宜用`list`模拟 queue，一般用`collections.deque`，其`popleft()`是 $O(1)$ 的；`deque()`可用`Iterable`初始化，一般就用`list`配合推导式初始化`deque`，或者直接`duque()`初始化为空。



#### 17. `for else`



#### 18. Python 的`int`

Python 的`int`支持任意大小的整数，不会溢出；`0xc0c0c0c0`在 python 中不会爆`int`，是一个大的正数，不能用作 $-\infin$​。



#### 19. Python 中也要考虑浮点数的精度问题：

- 一般情况下，由于保留的小数位数比较少，运算步骤也不多，一般`float`的精度够用

- 但是倘若要保留的小数点位数很多，或者运算步骤很多（例如出现**递归**运算），则很有可能**因为精度不够而无法 ac**，例如 [OpenJudge:后序表达式求值](http://cs101.openjudge.cn/2024sp_routine/24588/) 的递归写法中，即便只需要保留两位小数，但因为递归太多次，逐渐累积了很多误差

  - 此题因为先读到的是第二个操作数，所以需要取一次“逆运算”，例如减法需要加个负号，除法要取倒数；显然减法不会引入精度问题，但除法中`b / a`和`1 / (a / b)`就会由于精度逐渐累积误差，导致 WA

  - 解决办法一：先用变量存起来，直接算，不要先算倒数再取倒数

    ```python
    # 1 / (rp() / rp())
    a = rp()
    b = rp()
    return b / a
    ```

  - 解决办法二：浮点数高精度计算，其中`Decimal`型可以和`int`, `float`等直接进行运算且保留精度

    ```python
    import decimal
    return 1 / (decimal.Decimal(str(rp())) / decimal.Decimal(str(rp())))
    #decimal.Decimal()类，可以用int, float, str作为参数构造，运算中保留精度
    ```

- ==警示：`float`运算不要曲线救国！！==



#### 20. Lifetime of variable

各种推导式（list, dictionary, set comprehension）中用于循环的变量是个临时变量，只在推导式内部有效；而`for`循环中，用于循环的变量在循环结束后依然存在。

```python
# 列表推导式中的变量 x 只在列表推导式内部有效
squares = [x**2 for x in range(5)]

# 此时尝试输出 x 会导致 NameError
# print(x)


for i in range(5):
    # 在这里，i 是 for 循环中的临时变量
    print(i)

# 可在循环外输出 i，且结果为最后一次循环迭代的值
print(i)
```



#### 21. 关于 Python 的堆：

- `heapq`只能实现小根堆，无法指定比较函数，也没有`key`，当需要大根堆时可以存`-value`，或者重写类的`__lt__`方法
- `heapq.heappop()`弹的是列表的首元素，如果没有`heaping`过的话弹出的不是最小值；注意此方法是不接收列表以外的参数的，所以不能指定弹出某一个元素
- ==Todo:== 复习y总讲的堆（手写堆），思考一下堆中元素怎么删除的问题，目前使用了懒删除（开一个哈希表标记一下每个元素出现的次数，要删除就把次数减1，非正就代表没了）[可参考](https://blog.csdn.net/weixin_39711441/article/details/110165565#:~:text=您可以很容易地从堆中删除第i个元素：h%20%5Bi%5D%20%3D,h%20%5B-1%5Dh.pop%20%28%29heapq.heapify%20%28h%29只需用最后一个元素替换要删除的元素，并删除最后一个元素，然后重新堆叠堆%E3%80%82)



#### 22. Decorator

In Python, a decorator is a special type of function that can be used to modify or extend the behavior of other functions or methods.

`lru_cache()` is one such decorator in `functools` module which helps in reducing the execution time of the function by using memoization technique.

Syntax: `@lru_cache(maxsize=128, typed=False)`

```python
from functools import lru_cache

@lru_cache()
def myFunc():
    pass
```

Python3.9 之后还有`@functools.cache



#### 23. `cmp_to_key`

- Python3 的排序等函数没有`cmp`参数，但有时又觉得写`cmp`方便，可以先写`cmp`函数，再利用`cmp_to_key`将其转换为`key`

- Syntax: `functools.cmp_to_key(callable)`，注意这里是将一个`cmp`函数作为参数传递给`cmp_to_key`，而不是调用`cmp`函数，所以**这里`cmp`函数不写括号和参数**

  ```python
  from functools import cmp_to_key
  def lt(a, b):
      pass
  l.sort(key=cmp_to_key(lt))
  ```

- `cmp`函数的写法要求：

  - Input Parameters:
    - The `cmp` function should take exactly two parameters, typically denoted as `x` and `y`.
  - Return Value:
    - Return `-1` when `x` "lt" `y`.
    - Return `0` when `x`  "eq" `y`.
    - Return `1` when `x`  "gt" `y`.

#### 24. 合并列表的空间复杂度

`list`的`extend`方法把另一个列表加到当前列表，空间复杂度 $O(1)$，而加法（包括`+=`）要创建新列表，空间复杂度 $O(n)$​。



#### 25. 关于条件判断

- Python 中`bool`是`int`的子类。

- `if a`和`if a == True`的区别在于，前者自动调用`a`的`__bool__`或`__len__`方法（对于容器）来给出一个布尔值，后者是检查`a`和`bool`类的实例`True`是否相等，这是有区别的：例如`a = 2`时，前者会执行`if`内的分支，后者则不会；而如果`a = 1`的话二者都会执行，因为`True`和`1`是相等的。

- 所以可以出现这样的代码：

  ```python
  if a == True:
      pass
  elif a == False:
      pass
  else:
      pass # 这里也可以被执行，例如a = 2时
  ```



#### 26. 自定义类的`==`与`!=`

不写`__eq__`的类，`==`就默认比较地址，相当于`is`；同理不写`__ne__`时`!=`就相当于`is not`。



#### 27. Python 中函数也是对象

- 写括号时是调用函数，不写括号时是对象；函数作为对象时和其他对象的使用方式相同，例如可以赋值、作为函数参数、作为函数返回值、存在容器中等。
- 一个类重载`__call__`方法后也就成为一个函数，可以用`()`调用。



#### 28. 作用域 (Scope)
Python follows the principle of "LEGB" for resolving names in different scopes:

- The Local, or function-level, scope, which exists inside functions.
- The Enclosing, or non-local, scope, which appears in nested functions.
- The Global scope, which exists at the module level.
- The Built-in scope, which is a special scope for Python’s built-in names.

You can’t directly **modify** a variable from a high-level scope (like global) in a lower-level scope  (like local).

这里涉及 Python 变量的所谓指针本质，此处说的 **modify** ==只指使此变量指向另一个对象==，例如赋值；但是在低层级对一个高层级的 mutable 变量进行 mutate 是可以的，只是不能更改它地址或者说`id`。

这其实是很好理解的，因为所谓“使变量指向另一个对象”，就是创建了一个新变量，而在低层级创建的变量就属于这个层级，如果在创建这个低层级变量时，已经把这个变量名解释为高层级那个变量了的话，此时就会产生命名冲突；而对一个高层级的 mutable 进行 mutate 不产生新变量，Python已经按照 LEGB 把这一变量理解为高层级的变量，不会产生问题。

当需要在低层级的作用域使高层级的变量名指向另一个对象时，需要在使用这个变量名之前用`global`或`nonlocal`语句声明，此后该低层级作用域的该变量就会被理解成相应的高层级作用域中的变量；或者使用`globals()`函数：

The built-in `globals()` function allows you to access the global scope’s name table, which is a **writable** dictionary containing your current global names and their corresponding values. You can use this function to either access or modify the value of a global variable from within your functions.

**一般`globals()`会更好用（但注意键是`str`类型，不能只写变量名）。**
因为有时候需要在多个地方（比如一些 if-else 中）声明`global`然后改变某变量，而 Python 解释器会在运行前检查，不允许`global`声明出现在对该名称的赋值后（`SyntaxError: name 'xxx' is assigned to before global declaration`），且此处的前后是函数文本位置的前后，不看具体运行后的递归等正常运行顺序。所以有时一个我们人认为如果运行起来能够正常声明作用域的函数也会出现如上bug。

注：OJ的pylint运行前静态检查有时会导致“没道理的”ce：要求在函数内使用不可变类型的全局变量时，将该变量的初始化放到函数前面。可以在代码开始加一行`# pylint: skip-file`跳过其检查避免这个问题。

 
#### 29. 输入输出重定向
```Python
import sys

sys.stdin = open('input.txt', 'r')
sys.stdout = open('output.txt', 'w')
```
好像有问题，经测试spyder中没用?? https://zhuanlan.zhihu.com/p/365033765

试试with open，之前好像能用

#### 30. Nested Function
一般的函数是在全局范围内定义的，称为全局函数。，但Python 还支持在函数体内定义函数，这种函数称为局部函数。

在默认情况下，局部函数对外部是隐藏的，局部函数只能在函数体内调用。函数也可以返回局部函数，以便程序在其他作用域中使用局部函数。

#### 31. 去除重复元素
不需要保持顺序时简单地使用`list(set(myList))`即可。

**需要保持顺序时：**
由于 Python 3.6 之后**字典会保持插入顺序**（集合仍然乱序），可以利用字典去重并保持顺序，如：`sq = list({int(x): None for x in myList}.keys())`，用字典推导式把要去重的列表作为键去重（值随意），再转回`list`。

也可以用一种时间复杂度高一些但很优雅的写法：
`unique_lst = [x for i, x in enumerate(lst) if x not in lst[:i]]`。


进阶: 单调栈? 见labuladong
#### 32. 条件判断
Python 的条件判断也有"短路求值"（short-circuit evaluation）机制，所以`if a and b`以及`if a or b`中`a`和`b`的顺序也影响运行时间，尤其是其一需要大量运算时（例如是个递归函数）。

#### 33. Iterators and Generators
> In Python, the mechanism for iteration is based upon the following conventions:
> - An **iterator** is an object that manages an iteration through a series of values. If variable, `i`, identifies an iterator object, then each call to the built-in function, `next(i)`, produces a subsequent element from the underlying series, with a StopIteration exception raised to indicate that there are no further elements.
> - An **iterable** is an object, `obj`, that produces an iterator via the syntax `iter(obj)`.
>
> It is possible to create multiple iterators based upon the same iterable object, with each iterator maintaining its own state of progress. However, iterators typically maintain their state with indirect reference back to the original collection of elements.
> 
> Python also supports functions and classes that produce an implicit iterable series of values, that is, without constructing a data structure to store all of its values at once. Such a **lazy evaluation** technique has great advantage.

> The most convenient technique for creating iterators in Python is through the use of **generators**. A generator is implemented with a syntax that is very similar to a function, but instead of returning values, a `yield` statement is executed to indicate each element of the series.
> 
> It is illegal to combine yield and return statements in the same implementation, other than a zero-argument return statement to cause a generator to end its execution.
> 
> For each iteration of the loop, Python executes our procedure until a `yield` statement indicates the next value.
> *Data Structures and Algorithms in Python*

#### 34. Chained Comparison
Python 支持链式比较（语法糖），如`a <= b < c`, `a == b == c`，但注意 C++ 不支持，需要用`&&`连接。
#### 35. Recursion Limit  
By default, the **recursion limit** in Python is set to 1,000. If a recursive function exceeds this limit, a `RecursionError` is raised.
```Python
import sys

sys.setrecursionlimit(100000)
```
另外，在C++中，递归调用的深度没有一个固定的默认限制，它受限于栈的大小。
#### 36. `defaultdict`
`defaultdict` 是 Python 标准库 `collections` 模块中的一个类，它是字典（`dict`）的子类，提供了一种方便的方式来设置默认值。

与字典的区别是，`defaultdict` 在创建时需要指定一个默认工厂函数（default factory function）作为参数。该工厂函数用于提供默认值，并在访问不存在的键时自动调用。这样，即使字典中不存在某个键，也能够返回一个默认值，而不是引发 `KeyError` 异常。

default factory function 这个形参要求传递一个 Callable 对象 (即函数, lambda 表达式, 类, 重载了`__call__`方法的类的实例) 实参, 且将该 Callable 对象无参数时的返回值 (或对类来说, 构造的实例) 作为字典的默认值. 注意此处是传参而不是调用函数, 所以只写该 Callable 对象的标识符, 不写圆括号.

#### 37. \* 运算符
`print(*ans if flag else -1)`在 flag 为`False`时会报错:

TypeError: print() argument after * must be an iterable, not int

打印对`ans if flag else -1`这个表达式解包的结果, 而不是打印解包`ans`的结果或`-1`.

故`1`改成`[-1]`即可.

==todo: 整理\*运算符==

#### 38. 内置函数`len()`
调用`len(obj)`时, 会调用`obj`的`__len__()`方法, 由于可迭代的内置类型都实现了`__len__`方法并且在增删元素时维护, 故`len()`是 $O(1)$ 的, 而无需从头统计. 且显然对未实现`__len__`方法的类的实例调用`len()`函数会抛出异常.

#### 39. `__iter__`方法
`__iter__`返回迭代器, 重载该方法除了可以使该类的实例作为迭代器使用 (或者说成为 Iterable) 外, 也使其支持**解包** (unpack) 操作, 如:
```Python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __iter__(self):
        yield self.name
        yield self.age
        # 也可以直接利用元组等的迭代器, 即return iter((self.name, self.age)) 

# Creating an instance of the Person class
person = Person('Alice', 30)
# Unpacking the person object
name, age = person
```

#### 40. 分数幂运算
注意`a ** 1/2`表达的是 $\frac{a^1}{2}$, 且`**`返回的是浮点数.
