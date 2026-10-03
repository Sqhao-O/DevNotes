---
title: Python 基础
tags:
  - Python
  - 搜广推算法
  - 学习笔记
aliases:
  - 20分钟学完一遍Python基础
---

# Python 基础

> 来源：[B 站《20分钟学完一遍 Python 基础》](https://www.bilibili.com/video/BV1Sz4y1U77N)。原笔记标注作者为岳同学，演示环境为 Python 3.7.2、PyCharm。
>
> 本文依据提供的视频笔记整理，合并重复讲解、修正代码与概念，并补充必要的边界说明。适合入门和复习，不是完整的 Python 教程。

学习主线：**变量与类型 → 输入输出与运算 → 条件与循环 → 字符串和容器 → 函数**。

## 1. 注释、变量与缩进

### 1.1 注释与文档字符串

`#` 开始单行注释；多行注释通常逐行使用 `#`。三引号创建多行字符串，放在模块、类或函数体开头时可作为文档字符串（docstring），不应直接等同于注释语法。

```python
# 记录用户年龄
age = 20

def greet():
    """打印一句问候。"""
    print("hello")
```

### 1.2 变量与命名

`=` 表示赋值：先计算右侧表达式，再将结果绑定到左侧变量名。变量不需要预先声明类型，同一变量名可以重新绑定到不同类型的对象。

```python
count = 1
user_name = "Alice"
count = "one"
```

- 变量名可使用字母、下划线和数字，但不能以数字开头；Python 也支持符合规则的 Unicode 标识符。
- 区分大小写：`name` 和 `Name` 是不同变量。
- 不能使用 `if`、`for` 等关键字；尽量不要覆盖 `list`、`str`、`sum` 等内置名称。
- 普通变量和函数名通常使用 `snake_case`，例如 `user_name`、`is_even`。

### 1.3 缩进与代码块

Python 使用缩进划分代码块。`if`、`for`、`while`、`def` 等语句头以冒号 `:` 结束，下一层通常缩进 **4 个空格**；同一代码块必须保持一致。

不要混用 Tab 和空格，也不要把“1 个 Tab”理解为语法上等于“4 个空格”。

## 2. 常见数据类型

### 2.1 数值、字符串与布尔值

| 类型 | 名称 | 示例 |
| --- | --- | --- |
| `int` | 整数 | `12`、`-3` |
| `float` | 浮点数 | `1.2`、`-0.5` |
| `str` | 字符串 | `"hello"`、`'hello'` |
| `bool` | 布尔值 | `True`、`False` |

补充：`None` 表示没有值；可用 `type(value)` 查看对象类型。

### 2.2 四种常用容器

| 类型 | 示例 | 顺序与访问方式 | 是否可变 | 重复规则 |
| --- | --- | --- | --- | --- |
| 列表 `list` | `[1, 2, 3]` | 有序，支持索引与切片 | 可变 | 元素可重复 |
| 元组 `tuple` | `(1, 2, 3)` | 有序，支持索引与切片 | 不可变 | 元素可重复 |
| 集合 `set` | `{1, 2, 3}` | 无序，不支持位置索引 | 可变 | 元素不重复 |
| 字典 `dict` | `{"name": "Alice"}` | 按键访问，保留插入顺序 | 可变 | 键唯一，值可重复 |

字典保留插入顺序是 Python 3.7 起的语言保证。集合元素和字典键必须**可哈希**，如整数、字符串、所有元素均可哈希的元组；列表、字典和集合本身不可哈希。

```python
empty_list = []
empty_tuple = ()
empty_dict = {}       # {} 是空字典
empty_set = set()     # 空集合必须这样创建
single_tuple = (1,)   # 单元素元组需要逗号；(1) 只是整数 1
```

## 3. 输入、输出与类型转换

### 3.1 输出 print

```python
age = 20
print("hello world")
print(age)
print(f"年龄：{age}")          # f-string：在字符串中嵌入表达式
print("年龄：%d" % age)        # % 格式化；%d 为整数占位符
print("A\tB")                 # \t 是制表符，显示宽度与位置有关
print("第一行\n第二行")        # \n 是换行符
print("hello", end=" ")       # 默认 end="\n"；这里改为空格
print("world")                # 与上一条共同输出 hello world
```

### 3.2 输入 input

`input()` 读取一行输入，返回值是 **字符串**。需要数值运算时，应先转换类型。

```python
name = input("请输入姓名：")
age = int(input("请输入整数年龄："))
print(f"{name} 明年 {age + 1} 岁")
```

### 3.3 类型转换

| 写法 | 结果 | 说明 |
| --- | --- | --- |
| `int("12")` | `12` | 整数字符串转整数 |
| `float("1.2")` | `1.2` | 数字字符串转浮点数 |
| `str(15)` | `"15"` | 转字符串 |
| `int(3.9)` | `3` | 向零截断，不是四舍五入 |
| `bool(0)` | `False` | 零值为假 |
| `bool("")` | `False` | 空字符串为假 |
| `bool("False")` | `True` | 非空字符串为真，不解析其中的文字 |

转换有条件限制，例如 `int("abc")`、`int("1.2")` 都会抛出 `ValueError`。`None`、零值、空字符串和空容器通常为假。

## 4. 运算符与随机数

### 4.1 算术运算

| 运算符 | 含义 | 示例 |
| --- | --- | --- |
| `+`、`-`、`*` | 加、减、乘 | `2 + 3 == 5` |
| `/` | 真除法 | `5 / 2 == 2.5`，`4 / 2 == 2.0` |
| `//` | 向下取整除法（补充） | `5 // 2 == 2`，`-5 // 2 == -3` |
| `%` | 取余 | `5 % 2 == 1` |
| `**` | 幂运算 | `2 ** 3 == 8` |

对普通整数、浮点数运算，`/` 返回浮点数。括号可改变计算顺序。

```python
count = 1
count += 1       # 对这里的整数，等价于 count = count + 1
count *= 5
count **= 2
result = (count + 15) / (20 * 15 + 15)
```

### 4.2 比较、逻辑与成员判断

- 比较：`==`、`!=`、`<`、`<=`、`>`、`>=`。
- 逻辑：`not`（非）、`and`（与）、`or`（或）；这三者的优先级依次降低，复杂表达式建议加括号。
- 成员判断：`in`、`not in`；对字典判断的是键。

```python
age = 20
is_adult = age >= 18
print(is_adult and age < 60)    # True
print(not is_adult)            # False
print("a" in "apple")         # True
print(2 not in [1, 3, 5])      # True
```

**`=` 是赋值，`==` 是判断是否相等。** `and`、`or` 会短路求值，返回值可能是某个操作数，不一定是 `bool`，例如 `"" or "默认值"` 返回 `"默认值"`。

### 4.3 字符串拼接与重复

```python
print("O" + "H")       # OH
print("H" * 2)         # HH
print("O" + "H" * 3)   # OHHH
```

### 4.4 random 模块

```python
import random

a = random.randint(1, 100)   # 整数，包含 1 和 100
b = random.uniform(1, 100)   # 指定范围内的浮点数
c = random.random()         # 浮点数，范围为 [0.0, 1.0)
```

`uniform(a, b)` 的末端 `b` 是否可能出现取决于浮点舍入，不宜将它简单记成严格的左闭右开区间。

## 5. 条件与循环

### 5.1 if / elif / else

同一条条件链从上到下判断，只执行第一个满足条件的分支；都不满足时执行 `else`（若存在）。

```python
score = 75
if score < 0 or score > 100:
    print("分数不合法")
elif score < 60:
    print("不合格")
elif score < 80:
    print("合格")
else:
    print("优秀")

number = 11
if number % 2 == 0:
    print("是偶数")
else:
    print("是奇数")
```

### 5.2 while：按条件重复

```python
count = 0
while count < 5:
    count += 1
    print(count)    # 依次输出 1、2、3、4、5
```

循环必须有可达的退出方式，例如更新条件、执行 `break` 或从函数中 `return`。并非每个 `while` 都必须使用递增计数器；`while True` 也可以配合 `break` 正常退出。

### 5.3 for：遍历可迭代对象

```python
for item in [1, 3, 5]:
    print(item)

for char in "ABC":
    print(char)

for i in range(3):
    print(i)        # 0、1、2
```

`range(start, stop, step)` 表示整数序列，包含起点，不包含终点；默认起点为 `0`，步长为 `1`，步长不能为 `0`。

| 写法 | 遍历得到的值 |
| --- | --- |
| `range(5)` | `0, 1, 2, 3, 4` |
| `range(-1, 3)` | `-1, 0, 1, 2` |
| `range(1, 6, 2)` | `1, 3, 5` |
| `range(3, 0, -1)` | `3, 2, 1` |

`range` 本身不是列表，若需要列表，可使用 `list(range(5))`。

### 5.4 break / continue / 循环 else

- `break`：立即退出最近一层循环。
- `continue`：跳过本轮剩余语句，进入最近一层循环的下一轮。
- 循环的 `else`：在迭代耗尽或 `while` 条件变为假时执行；被 `break` 中断时不执行。`continue` 不会单独阻止 `else` 执行。

```python
for number in range(1, 8):
    if number == 3:
        continue
    if number == 6:
        break
    print(number)    # 1、2、4、5
else:
    print("遍历完成")  # 本例遇到 break，不执行
```

嵌套循环中，内层的 `break` 不会退出外层。写 `while` 时还要留意：`continue` 是否跳过了更新循环条件的语句。

## 6. 字符串：索引、切片与方法

### 6.1 索引与切片

字符串、列表和元组均支持索引与切片。索引从 `0` 开始，`-1` 表示最后一个元素。

```python
text = "my name is xxx"
print(text[0])       # m
print(text[-1])      # x
print(text[1:5])     # y na
print(text[:5])      # my na
print(text[1:5:2])   # yn
print(text[::-1])    # xxx si eman ym
```

切片格式是 `sequence[start:stop:step]`，不包含终点；省略边界时，默认边界由步长方向决定。

- `text[1:]`：从下标 `1` 取到结尾。
- `text[:]`：取得完整字符串内容，不保证创建新的字符串对象。
- `text[-5:-1]`：从倒数第 5 个字符取到倒数第 2 个字符。
- `text[::-1]`：反转序列。

单个索引越界会报 `IndexError`；切片边界越界通常会自动截断。

### 6.2 常用字符串方法

字符串**不可变**；这些方法返回处理后的字符串或其他结果，不会原地修改字符串。

```python
text = "my name is xxx"
replaced = text.replace("xxx", "Alice")  # my name is Alice
words = text.split(" ")                 # ['my', 'name', 'is', 'xxx']
joined = "-".join(words)                 # my-name-is-xxx
```

| 方法 | 含义 | 示例结果 |
| --- | --- | --- |
| `capitalize()` | 首字符大写，其余小写 | `"hELLO"` → `"Hello"` |
| `title()` | 按方法的单词边界规则转标题式大小写 | `"hello world"` → `"Hello World"` |
| `lower()` | 转小写 | `"Hello"` → `"hello"` |
| `upper()` | 转大写 | `"Hello"` → `"HELLO"` |
| `lstrip()` | 去掉左侧空白 | `"  hello"` → `"hello"` |
| `rstrip()` | 去掉右侧空白 | `"hello  "` → `"hello"` |
| `strip()` | 去掉两侧空白 | `"  hello  "` → `"hello"` |

补充：`split()` 不传分隔符时，按连续空白切分；`split(" ")` 按单个空格切分，连续空格可能产生空字符串。`join()` 要求待拼接的元素都是字符串。

## 7. 列表 list

### 7.1 访问与嵌套

列表有序、可变，可以存放不同类型的对象，也可以嵌套。

```python
items = [1, False, "happy", [2, 3]]
print(items[0])        # 1
print(items[3][1])     # 3
items[0] = 123         # 修改元素

matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
print(matrix[1][2])    # 6：第 2 行、第 3 列
for row in matrix:
    for value in row:
        print(value)
```

更深的嵌套使用连续索引，例如 `cube[2][1][0]`；遍历时可对应增加循环层数。

### 7.2 增删改查

```python
items = [1, 2, 3]
items.append(4)        # [1, 2, 3, 4]：末尾追加一个元素
items.insert(1, 9)     # [1, 9, 2, 3, 4]：在下标 1 处插入
removed = items.pop(0)  # removed 为 1，items 为 [9, 2, 3, 4]
items.remove(3)        # [9, 2, 4]：删除第一个等于 3 的元素
items[0] = 8           # [8, 2, 4]
print(2 in items)      # True
print(len(items))      # 3
items.clear()          # []：清空列表，但变量仍存在
del items              # 删除变量绑定，此后不能直接使用这个名称
```

`pop()` 不传索引时删除并返回最后一个元素；索引越界会报错。`remove(value)` 找不到对应值时会报 `ValueError`。

### 7.3 赋值、浅拷贝与深拷贝

**`b = a` 不会复制列表，而是让两个变量指向同一个对象。** 对共享列表的原地修改会通过两个变量体现；重新给其中一个变量赋值不会重新绑定另一个变量。

```python
a = [1, 2]
b = a
b.append(3)
print(a)              # [1, 2, 3]

c = a.copy()          # 创建新的外层列表
c.append(4)
print(a)              # [1, 2, 3]
print(c)              # [1, 2, 3, 4]
```

`copy()` 和列表切片 `a[:]` 都是**浅拷贝**：外层独立，内部元素仍是原对象的引用。

```python
import copy

a = [[1, 2], [3, 4]]
b = a.copy()
b[0][0] = 99
print(a)              # [[99, 2], [3, 4]]：内部列表仍共享

c = copy.deepcopy(a)  # 对这种嵌套列表递归复制
c[0][0] = 100
print(a[0][0])        # 99
```

### 7.4 排序

```python
numbers = [4, 2, 1, 3]
ascending = sorted(numbers)     # 返回新列表 [1, 2, 3, 4]
print(numbers)                 # [4, 2, 1, 3]，原列表未变
numbers.sort(reverse=True)     # 原地修改为 [4, 3, 2, 1]
```

| 写法 | 是否修改原列表 | 返回值 |
| --- | --- | --- |
| `numbers.sort()` | 是 | `None` |
| `sorted(numbers)` | 否 | 新的已排序列表 |

不要写 `numbers = numbers.sort()`，否则变量会被赋为 `None`。

## 8. 元组 tuple 与集合 set

### 8.1 元组

元组支持索引、切片、遍历，但不能替换其中的元素引用，也不能增删元素。

```python
point = (10, 20)
print(point[0])         # 10
for value in point:
    print(value)

for i in range(len(point)):
    print(i, point[i])  # 通过索引遍历

# point[0] = 30        # TypeError：不能给元组元素赋值
```

元组不可变不代表其内部对象一定不可变。例如 `data = ([1, 2],)` 中的列表仍可通过 `data[0].append(3)` 修改。

### 8.2 集合

集合用于去重和成员判断，元素必须可哈希，不保证遍历顺序，不支持 `s[0]` 这类位置索引。

```python
values = {1, 3, 3, 4}
print(len(values))    # 3：重复的 3 只保留一份
print(3 in values)   # True
```

补充常用操作：

```python
values = {1, 3, 4}
values.add(5)
values.discard(3)     # 不存在时也不报错
values.remove(4)      # 不存在时会报 KeyError
```

## 9. 字典 dict

### 9.1 键值对与增删改查

字典通过键定位值。键必须可哈希，值可以是任意类型；重复给同一个键赋值会覆盖旧值。

```python
user = {"name": "Alice", "age": 20}
print(user["name"])                # Alice
user["city"] = "Shanghai"          # 新增
user["age"] = 21                   # 修改
print("age" in user)               # True：判断键是否存在
print(user.get("score", 0))        # 0：键不存在时使用默认值
del user["city"]                   # 删除键值对
```

`user[key]` 访问不存在的键会报 `KeyError`；`get()` 可提供默认值。`user.clear()` 清空内容；`del user` 删除变量绑定。

### 9.2 keys / values / items 与遍历

```python
user = {"name": "Alice", "age": 20}
print(list(user.keys()))     # ['name', 'age']
print(list(user.values()))   # ['Alice', 20]
print(list(user.items()))    # [('name', 'Alice'), ('age', 20)]

for key in user:
    print(key)              # 默认遍历键

for key, value in user.items():
    print(key, value)
```

`keys()`、`values()`、`items()` 返回的是**视图对象**，不是列表；确实需要列表时再用 `list(...)` 转换。

## 10. 函数

### 10.1 定义、调用、参数与返回值

函数把一段逻辑封装起来，便于复用。`def` 创建函数，调用时才执行函数体。通常把与类或对象关联的函数称为“方法”，两者不宜完全混用。

```python
def is_even(number):
    return number % 2 == 0

value = 15
if is_even(value):
    print(f"{value} 是偶数")
else:
    print(f"{value} 不是偶数")
```

- **形参**：定义中的 `number`。
- **实参**：调用时传入的 `value`。
- **返回值**：`return` 后的结果；执行 `return` 会立即结束当前函数调用。
- 没有执行带值的 `return`，或只写 `return`，返回值都是 `None`。
- `print()` 负责输出，不能代替 `return` 把计算结果交给调用者。

### 10.2 默认参数、位置参数与关键字参数

```python
def greet(name, age=18):
    return f"{name}, {age}"

print(greet("Alice"))               # Alice, 18
print(greet("Alice", 20))           # Alice, 20
print(greet(age=20, name="Alice"))   # Alice, 20
```

同一组普通位置参数中，无默认值的参数应放在有默认值的参数之前。不要为同一个参数重复传值。

### 10.3 不定长参数 *args 与 **kwargs

定义函数时，`*args` 收集额外的位置实参，内部是元组；`**kwargs` 收集额外的关键字实参，内部是字典。名称可以改变，星号才决定其含义。

```python
def show(name, age=18, *args, **kwargs):
    print(name, age)
    print(args)
    print(kwargs)

show("Alice", 20, 2, 3, city="Shanghai", score=95)
# Alice 20
# (2, 3)
# {'city': 'Shanghai', 'score': 95}
```

常见定义顺序为：普通参数 → 带默认值的参数 → `*args` → `**kwargs`。

但这不是完整规则：`*args` 后还可以定义**仅限关键字参数**，调用时必须写参数名；`**kwargs` 应放在最后。

```python
def collect(*args, limit=10, **kwargs):
    return args[:limit], kwargs

print(collect(1, 2, 3, limit=2, source="demo"))
# ((1, 2), {'source': 'demo'})
```

补充：调用函数时，`*sequence` 展开位置实参，`**mapping` 展开关键字实参，与定义时的“收集”方向相反。

### 10.4 多返回值与解包

```python
def get_pair():
    return 1, 2          # 实际返回元组 (1, 2)

pair = get_pair()
left, right = get_pair()
print(pair)             # (1, 2)
print(left, right)      # 1 2
```

可迭代对象通常都能解包。普通解包时左右数量要匹配；字典直接解包得到键，集合的解包顺序不应依赖。

```python
first, second = [10, 20]
head, *tail = [1, 2, 3]  # head 为 1，tail 为 [2, 3]
```

### 10.5 局部变量与 global

函数内赋值的名称通常属于局部变量。若要在函数内**重新绑定模块级全局变量**，需要声明 `global`。

```python
day_count = 0

def next_day():
    global day_count
    day_count += 1
    return day_count

print(next_day())        # 1
```

只读取全局变量，或通过方法原地修改全局列表等对象，通常不需要 `global`。为使输入输出更清晰，优先考虑传参并返回结果。

### 10.6 文档字符串与类型提示

原笔记用图片处理函数演示这两个概念；这里换成不依赖图片文件和第三方库的例子。

```python
def rectangle_area(width: float, height: float) -> float:
    """计算矩形面积。

    width：矩形宽度。
    height：矩形高度。
    返回宽度与高度的乘积。
    """
    return width * height

print(rectangle_area(3.0, 4.0))  # 12.0
```

`: float` 标注参数类型，`-> float` 标注返回值类型。类型提示便于阅读和工具检查，Python 默认不会据此强制检查或转换实参。

### 10.7 文件执行顺序与 __name__

Python 文件的顶层语句按顺序执行，**不是从 `if __name__ == "__main__":` 才开始运行**。

```python
def main():
    print("程序开始")

if __name__ == "__main__":
    main()
```

- 直接运行这个文件：`__name__` 为 `"__main__"`，执行判断块中的 `main()`。
- 被其他模块导入：`__name__` 通常为模块名，不执行这个判断块；但其他顶层语句仍可能在导入时执行。
- `main` 是约定俗成的函数名，不是特殊关键字。

### 10.8 递归

递归是函数直接或间接调用自身。需要明确的终止条件，并确保每次调用都向终止条件推进。

```python
def factorial(n: int) -> int:
    """计算非负整数 n 的阶乘。"""
    if n < 0:
        raise ValueError("n 必须为非负整数")
    if n <= 1:
        return 1
    return n * factorial(n - 1)

print(factorial(5))  # 120
```

此例假定输入为整数。即使有终止条件，递归层数过深也可能触发 `RecursionError`；无终止条件的递归通常会在达到递归深度限制时报错。

## 11. 复习速查与易错点

| 场景 | 关键点 |
| --- | --- |
| 判断相等 | 用 `==`，赋值用 `=` |
| 读取数字 | `input()` 返回字符串，按需用 `int()` / `float()` 转换 |
| 判断偶数 | `number % 2 == 0` |
| 使用缩进 | 每层通常 4 个空格，不混用 Tab |
| 循环边界 | `range` 不包含终点，步长不能为 0 |
| 循环控制 | `break` 退出最近一层；`continue` 跳过本轮剩余代码 |
| 循环 else | 正常耗尽或条件变假时执行；遇到 `break` 不执行 |
| 修改字符串 | 字符串不可变，不能用 `text[0] = ...` 修改 |
| 复制列表 | `b = a` 共享对象；`a.copy()` 只浅拷贝外层 |
| 列表排序 | `sort()` 原地排序并返回 `None`；`sorted()` 返回新列表 |
| 创建空容器 | `{}` 是空字典，`set()` 是空集合 |
| 字典键与集合元素 | 必须可哈希，列表不能直接作为键或集合元素 |
| 字典视图 | `keys()` / `values()` / `items()` 不直接返回列表 |
| 函数返回 | 无显式返回值时返回 `None`；多个返回值通常打包为元组 |
| 参数顺序 | `*args` 后允许仅限关键字参数，`**kwargs` 放在最后 |
| 程序入口 | 顶层语句顺序执行，`__main__` 判断控制入口代码是否执行 |

本笔记覆盖视频整理稿中的主要入门内容；异常处理、文件读写、类与对象、推导式、模块与包等主题需另行学习。
