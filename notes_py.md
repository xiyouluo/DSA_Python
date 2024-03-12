1. python 的整除`//`是向下取整，与 cpp 的去掉小数部分逻辑不同（`int`除以`int`得`int`时的强转）

2. 注意 python 变量的指针本质！！

   - 广义地，可以把 python 中一切可赋值的东西都称为变量，而所有的变量都是指针（或者说引用），包括列表的元素，字典的值。其指针本质体现在赋值、函数形参、函数返回值、存放元素到容器中等处。

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

   - 赋值（函数形参等情形也一样）后更改变量导致的行为则和变量类型是否可变有关：When you use `=` to assign a variable to another, you are creating a reference to the same object in memory. This is true for both mutable and immutable types. However, the crucial distinction lies in how these types handle modifications.

     - When you have immutable types, like integers (`int`) or strings (`str`), modifications result in the creation of a new object.

       ```python
       a = 42
       b = a  # b now references the same integer object as a
       
       a = 99
       print(b)  # Output: 42
       ```

       In this case, after changing `a`, `b` still references the original immutable object (42). This is because integers are immutable, and when you change the value of `a`, it creates a new object.

     - With mutable types, like lists (`list`), modifications to the object are reflected in all references because they point to the same underlying object.

       ```python
       list1 = [1, 2, 3]
       list2 = list1  # list2 now references the same list object as list1
       
       list1[0] = 99
       print(list2)  # Output: [99, 2, 3]
       ```

       Here, modifying `list1` affects `list2` because both variables reference the same mutable list object in memory.

3. 在使用`*`运算符重复列表时，重复的元素是原始列表中相同对象的引用。这一点在处理可变对象（如列表）时尤其重要，因为修改一个地方会影响到其他引用相同对象的地方

4. `join`的用法：`str.join(Iterable[str])`

5. 结合例子进一步理解 python 变量的指针本质：

   ```python
   ls = ['A', 'B']
   for ch in ls:
       ch = ch.lower() # 不会改变ls中每个元素的值
       # ch = 'b'也不会改变
   ls[0] = 'E' # 可以改变ls中ls[0]的值
   ```

   - 在这段 snippet 中，`for`循环遍历 `ls`时，变量`ch`与`ls[i]`指向同一个对象（例如第一次循环时都指向对象`'A'`），或者说都是同一个对象的引用，但注意不是 c++ 中说的引用（也就是说不是变量`ch`引用了`ls[i]`），在`ch = ch.lower()`中，右侧创建了一个新的`str`类型的对象，并赋值给变量`ch`，而此时`ls[i]`仍然指向之前的对象（例如第一次循环后`ls[0]`仍然指向对象`'A'`）；而在`ls[0] = 'E'`中，就是使`ls[0]`指向新的对象`'E'`，并没有改变字符串，这与`str`类型 immutable 不矛盾。
   - 事实上此处`for`循环的变量`ch`也相当于是用`ls[i]`赋值得到的，那么它是一个 mutable type 时行为也就可以预测了。

6. 当需要在 for 循环遍历列表时增删列表元素时（如果这样操作可能引起循环时索引混乱），可以考虑：

   - **拷贝**一个新列表，遍历一个修改一个
   - 列表推导式（更适用于删元素）如`[i for i in ls if xxx]`
   - 用`filter()`函数删除特定元素

7. python 中字符串不可以直接修改某索引处的字符，但有`.replace()`方法可以帮助替换和删除（替换为空串即可）

8. `str(lst)`和`"string.join(lst)"`效果不同，前者是把一个列表作为参数传给`str`类的构造函数，这样构造出来的字符串是包含列表的方括号和逗号的，例如：

   ```python
   my_list = ['apple', 'banana', 'orange']
   result_str = str(my_list)
   print(result_str)
   
   # output: "['apple', 'banana', 'orange']"
   ```

9. `print`函数的第一个参数为`*values`，数目不定，因此在需要指定`sep`或`end`时需要显式地指出`sep=`或`end=`，无法仅从位置判断；即不显式地指定时，所有字符串参数都传给了`*values`。

10. 格式化输出保留两位小数

    - ```python
      "%.2f %(expr)""
      "{.2f}".format(expr)
      f"{expr:.2f}"
      ```

    - 其他更细节的用法（考试时）可以在 Spyder 的 Console 中使用 `help(format)`查询

11. 列表推导式进一步用法：

    - 添加条件筛选

      ```python
      # 生成一个包含 0 到 9 的偶数的列表
      even_numbers = [x for x in range(10) if x % 2 == 0]
      # 输出: [0, 2, 4, 6, 8]
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

    

12. 类似 list comprehension 列表推导式，有字典推导式和集合推导式：

    ```python
    {key: value for (key, value) in iterable}
    {expr for x in iterable}
    ```

13. python 中对类的实例排序和 c++ 一样依照`<`进行，因此需要重载`<`运算符，即`__lt__`；如果用`sort`的`key`参数，多个排序条件时可以利用元组的排序特点：按照元组中的元素顺序从左到右逐个比较，直到找到不相等的元素或者其中一个元组的所有元素都被比较完。

14. 字典

    - `.items()`**返回以`(key, value)`元组为元素的列表**，`.keys()`和`.values()`返回`key`或`value`的列表
    - 我们知道，字典是基于哈希表一种无序结构，因此没有`.sort()`方法，当需要对字典的键或值或键值对排序时，先用以上方法转为列表
    - 注意`sorted(my_dict)`会被认为是对键排序，类似于`if xxx in my_dict`也会将字典判断为它的键的可遍历列表；总结就是两种情况`my_dict`都被理解成了`my_dict.keys()`

15. 进制转换

    - `int()`构造出来的对象是一个**十进制**的整数，即`int`类型，在这里指定`base=`是为了将括号内用其他进制表示的`str`(还有`bytes`和`bytearray`)转换为十进制整数，例如`int("0b101", base=2)`就会得到一个`int`类型的对象，其值为`5`，`int("101", base=2)`亦可。
    - 需要把十进制转换为其他进制时，可以用内置函数或自己造轮子（短除法）
      - 二进制（binary）: 二进制数字以`0b`或`0B`为前缀，对应函数`bin()`
      - 八进制（octal）: 八进制数字以`0o`或`0O`为前缀，对应函数`oct()`
      - 十六进制（hexadecimal）: 十六进制数字以`0x`或`0X`为前缀，对应函数`hex()`

16. ASCII 和字符相互转换

    - ASCII to char: `chr()`
    - char to ASCII: `ord()`, which stands for ordinal

17. `sorted()`和`sort()`

    - `sorted()`是 python 内置函数，接收`Iterable[]`返回`List[]`
    - `sort()`是`list`类的方法

18. 一些函数、方法、module：

    - `str`: `replace, find, rindex`
    - `list`: `index`
    - `from itertools import permutations, accumulate`
    - `zip()`：将多个`Iterable`以`tuple`形式打包，返回一个`zip`对象，`zip`对象是一个`tuple`的迭代器，需要时（例如要打出来看）可以转为`list`
    - 日期相关问题：`import calendar`
    - 中位数等统计问题：`import statistics`

19. 子矩阵截取：`A[i:p+1][j:q+1]`并不会截取矩阵`A`的第`i`行到第`p`行与第`j`列到第`q`列相交形成的子矩阵，显然第一步对行的截取`A[i:p+1]`可以完成，而第二步则不是在截取列，而是在新列表`A[i:p+1]`中继续截取行。

20. 多次调用`print()`函数可能花费较多不必要的时间，尝试一次性输出。

21. 101的笔记已经挪了前36条

22. 切片（slicing）

    - 适用于序列型对象（sequence type），如`str`,`list`,`tuple`
    - 一般语法为`sequence[start:stop:step]`
    - `start`或`stop`越界时，切片操作不会抛出异常，而是进行截断
    - `start`比`stop`大时，仍然不会抛出异常，而是直接返回空序列
    - `start`,`stop`,`:step`均可缺省

23. python 列表`list`的`pop(i)`一般是 $\Omicron(n)$ 的，仅`pop()`或者说`pop(-1)`是 $\Omicron(1)$的，所以不宜用`list`模拟 queue，一般用`collections.deque`，其`popleft()`是 $\Omicron(1)$ 的；`deque()`可用`Iterable`初始化，一般就用`list`配合推导式初始化`deque`，或者直接`duque()`初始化为空。

24. For else

25. python 的`int`最大值是 $2^{63}-1$，`0xc0c0c0c0`在 python 中不会爆`int`，是一个大的正数，不能用作 $-\infin$

26. 各种推导式（list, dictionary, set comprehension）中用于循环的变量是个临时变量，只在推导式内部有效；而`for`循环中，用于循环的变量在循环结束后依然存在

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

27. 关于 python 的堆：

    - `heapq`只能实现小根堆，无法指定比较函数，也没有`key`，当需要大根堆时可以存`-value`，或者重写类的`__lt__`方法
    - `heapq.heappop()`弹的是列表的首元素，如果没有`heaping`过的话弹出的不是最小值；注意此方法是不接收列表以外的参数的，所以不能指定弹出某一个元素
    - ==Todo:== 复习y总讲的堆（手写堆），思考一下堆中元素怎么删除的问题，目前使用了懒删除（开一个哈希表标记一下每个元素出现的次数，要删除就把次数减1，非正就代表没了）[可参考](https://blog.csdn.net/weixin_39711441/article/details/110165565#:~:text=您可以很容易地从堆中删除第i个元素：h%20%5Bi%5D%20%3D,h%20%5B-1%5Dh.pop%20%28%29heapq.heapify%20%28h%29只需用最后一个元素替换要删除的元素，并删除最后一个元素，然后重新堆叠堆%E3%80%82)

28. python 的`int`支持任意大小的整数，不会溢出

29. python 中也要考虑浮点数的精度问题：

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

30. In Python, a decorator is a special type of function that can be used to modify or extend the behavior of other functions or methods.

    - `lru_cache()` is one such decorator in `functools` module which helps in reducing the execution time of the function by using memoization technique.

    - Syntax: `@lru_cache(maxsize=128, typed=False)`

      ```python
      from functools import lru_cache
      
      @lru_cache()
      def myFunc():
          pass
      ```


    - Python3.9 之后还有`@functools.cache`

31. `cmp_to_key`

    - Python3 的排序等函数没有`cmp`参数，但有时又觉得写`cmp`方便，可以先写`cmp`函数，再利用`cmp_to_key`将其转换为`key`

    - Syntax: `functools.cmp_to_key(callable)`，注意这里是将一个`cmp`函数作为参数传递给`cmp_to_key`，而不是调用`cmp`函数，所以==这里`cmp`函数不写括号和参数==

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

32. `list`的`extend`方法把另一个列表加到当前列表，空间复杂度 $\Omicron(1)$，而加法要创建新列表，空间复杂度 $\Omicron(n)$。
