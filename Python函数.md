## Python函数

https://mybinder.org/v2/gh/binder-examples/matplotlib-versions/192287ad6789706f858a92c57f4491007727f5e6?filepath=python%E8%BF%9B%E9%98%B6.ipynb

#### 嵌套函数

```python
# 嵌套函数
# 1.能够普保证内部函数的隐私
# 2.合理使用，可以提高程序的运行效率
# 3.嵌套函数中，内部函数可以直接访问外部函数的变量，若要修改，则要使用nonlocal关键字
MIN = 1
MAX = 8
def f1():
    global MIN # 修改全局变量
    MIN = 2
    a = 5
    print(f"{MIN}hello")
    def f2():
        global MAX, MIN
        nonlocal a # 修改外部函数的变量
        a = 6
        MAX = 9
        MIN = 3
        print(f"{MIN}wor{a}ld{MAX}")
    f2()
# 调用函数
f1()

# 输出
2hello
3wor6ld9
```

#### 闭包

```python
# 闭包函数
# 和嵌套函数类似，只是，外部函数返回的是一个函数，而不是一个具体的值
# 返回的函数通常赋予一个变量，这个变量可以在后面被继续执行调用
# 可以简化程序的复杂度，提高可读性
def nth_power(exponent):
    def exponent_of(base):
        return base **exponent
    return exponent_of
# 调用函数
square = nth_power(2)
print(square)
print(square(2))

# 输出
<function nth_power.<locals>.exponent_of at 0x7f3120911b90>
4
```

#### 匿名函数

```python
# 匿名函数
# lambda 参数：表达式，调用
square = lambda x: x**2
print(square(3))
# lambda是一个表达式，不是一个语句，只能写成一行
# 使用场景：程序中需要使用一个函数完成一个简单的共嗯那个，并且该函数只调用一次
# 1.可以用在列表内部
li = [(lambda x: x*x)(x) for x in range(10)]
print(li)
# 2.可以被用作某些函数的参数
# 按列表中元组的第1个元素排序
lis = [(1, 20), (3, 0), (9, 10), (2, -1)]
lis.sort(key=lambda x: x[0]) 
print(lis)
# 对一个字典，根据值进行由高到低的排序
d = {"mike": 10, "luck": 2, "ben": 30}
new_li = sorted(d.items(), key=lambda x: x[1], reverse=True) # 返回的是列表嵌套元组类型
new_d = dict(new_li)
print(d,'\n', new_li, '\n', new_d)
# 3.数据清洗中，常用lambda函数

# 输出
9
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
[(1, 20), (2, -1), (3, 0), (9, 10)]
{'mike': 10, 'luck': 2, 'ben': 30} 
 [('ben', 30), ('mike', 10), ('luck', 2)] 
 {'ben': 30, 'mike': 10, 'luck': 2}
```

#### python函数式编程

```python
# python函数式编程
# 指代码中每一块都是不可变的，都由纯函数的形式组成
# 纯函数，是指函数本身相互独立、互不影响，对于相同的输入，总会有相同的输出
# map(function, iterable)，对序列中的每个元素都运用function这个函数，返回一个迭代器
li = [1, 2, 3, 4, 5]
new_list_1 = map(lambda x: x*2, li)
print(list(new_list_1))
# filter(unction, iterable)，对序列中的每个元素，都使用function判断，并返回True或者False,最后将返回True的元素组成一个新的可遍历的集合，返回迭代器类型
new_list_2 = filter(lambda x: x % 2 == 0, li)
print(list(new_list_2))
# reduce(unction, iterable)，规定有两个参数，表示对序列中的每个元素以及上上一次调用后的结果，运用function进行计算，最后返回的是一个单独的数值
# 计算列表元素的乘积
from functools import reduce
product = reduce(lambda x, y: x * y, li)
print(product)

# 输出
[2, 4, 6, 8, 10]
[2, 4]
120
```

#### 装饰器

**函数核心理解**

- 函数也是对象，可以把函数赋予变量
- 可以把函数当作参数，传入另一个函数中
- 可以在函数里定义函数，函数嵌套
- 函数的返回值也可以是函数对象，闭包

**装饰器**

- @装饰器函数，等价于，原函数名=装饰器函数（原函数名）
- 装饰器就是通过装饰器函数，来修改原函数的一些功能，使得原函数不需要修改，也就是扩展了原函数的功能

```python
# 简单装饰器
def my_decorator(func):
    def wrapper():
        print('wrapper of decorator')
        func()
    return wrapper
# 带参数的装饰器
def my_decorator(func):
    def wrapper(message):
        print('wrapper of decorator')
        func(message)
    return wrapper
# 通用的带参数的装饰器
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print('wrapper of decorator')
        func(*args, **kwargs)
    return wrapper
# 保留原函数的元信息，将原函数的元信息，拷贝到对应的装饰器函数里
import functools
def my_decorator(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        print('wrapper of decorator')
        func(*args, **kwargs)
    return wrapper

@my_decorator
def greet(message):
    print(message)
    
greet('hello world')

# 输出
wrapper of decorator
hello world

# 类装饰器，依赖函数__call__()
class Count:
    def __init__(self, func):
        self.func = func
        self.num_calls = 0

    def __call__(self, *args, **kwargs):
        self.num_calls += 1
        print('num of calls is: {}'.format(self.num_calls))
        return self.func(*args, **kwargs)

@Count
def example():
    print("hello world")

example()

# 输出
num of calls is: 1
hello world
```

##### 装饰器用法实例

- 身份认证
- 日志记录

```python
# 测试某些函数的执行时间
import time
import functools

def log_execution_time(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        res = func(*args, **kwargs)
        end = time.perf_counter()
        print(f"{func.__name__} took {(end - start) * 1000)} ms")
        return res
    return wrapper
    
@log_execution_time
def calculate_similarity(items):
    ...
```

- 输入合理性检查

- 缓存

  python中内置的LRU cache，@lru_cache，会缓存进程中的函数参数和结果，缓存满了之后，会删除访问时间最早的数据

- 工作中，二次开发，在原来的需求基础上做优化，原逻辑不需要修改的化，只需增加新的业务场景的时候