1. python 的整除`//`是向下取整，与 cpp 的去掉小数部分逻辑不同（`int`除以`int`得`int`时的强转）

2. 注意python 变量的指针本质！！

   - python 中赋值是让左侧变量指向右侧对象（如果右侧是对象）或让左侧变量指向右侧变量指向的对象（如果右侧是变量）

   - 赋值后更改变量导致的行为则和变量类型是否可变有关

   - When you use `=` to assign a variable to another, you are creating a reference to the same object in memory. This is true for both mutable and immutable types. However, the crucial distinction lies in how these types handle modifications.

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

6. 