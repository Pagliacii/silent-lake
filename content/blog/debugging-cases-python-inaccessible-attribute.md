+++
title = 'Python 之“无法访问”的属性'
date = 2023-12-07T22:38:40+08:00
draft = false
toc = true
math = false
tags = ["Python", "Debugging Cases"]
+++

最近碰到一个 Python 中的奇怪现象：明明在类定义里有着一个属性，但是在实例化后的对象上却无法访问到它，会抛出 `AttributeError`。

虽然 root cause 很简单，但是我觉得这个案例可以作为 debug 的例子，于是就有了这篇文章。

<!--more-->

## 示例代码

```python
class Parent:
    _fields = {}

    def __getattr__(self, name):
        try:
            return self._fields[name]
        except KeyError:
            raise AttributeError(
                f"'{self.__class__.__name__}' object has no attribute '{name}'",
            )


class Unrelated:
    @property
    def name(self):
        return "Unrelated"


class Child(Unrelated, Parent):
    @property
    def name(self):
        # access some variable in other module
        return OtherModule.name


child = Child()
print("name" in dir(child)) # True
print(child.name) # AttributeError: 'Child' object has no attribute 'name'
```

## 问题描述

问题出现在访问 `Child` 实例属性 `name` 时。虽然 `"name" in dir(child)` 返回 `True`，表明属性 `name` 存在，但是直接反问 `child.name` 却抛出了 `AttributeError`。

这乍一看上去很奇怪，明明属性存在却无法访问，这是为什么呢？

## 分析过程

要分析这个问题，首先我们需要对 Python 中属性查找的过程有一定了解。在 Python 中，当我们使用形如 `instance.attribute` 来访问实例属性时，会先在该实例对象上查找相应的属性，如果找不到就会抛出 `AttributeError`。而当我们给类加上了 `__getattr__` 方法时，Python 就会在找不到属性时调用这个[方法](https://docs.python.org/3/reference/datamodel.html#object.__getattr__)。

> **object.\_\_getattr\_\_(self, name)**
>
> Called when the default attribute access fails with an [`AttributeError`](https://docs.python.org/3/library/exceptions.html#AttributeError) (either [`__getattribute__()`](https://docs.python.org/3/reference/datamodel.html#object.__getattribute__) raises an [`AttributeError`](https://docs.python.org/3/library/exceptions.html#AttributeError) because name is not an instance attribute or an attribute in the class tree for `self`; or [`__get__()`](https://docs.python.org/3/reference/datamodel.html#object.__get__) of a name property raises [`AttributeError`](https://docs.python.org/3/library/exceptions.html#AttributeError)). This method should either return the (computed) attribute value or raise an [`AttributeError`](https://docs.python.org/3/library/exceptions.html#AttributeError) exception.

当我们执行示例代码中的 `print(child.name)` 这一行时，会有以下 traceback 信息抛出：

```text
---------------------------------------------------------------------------
KeyError                                  Traceback (most recent call last)
Input In [2], in Parent.__getattr__(self, name)
      5 try:
----> 6     return self._fields[name]
      7 except KeyError:

KeyError: 'name'

During handling of the above exception, another exception occurred:

AttributeError                            Traceback (most recent call last)
Input In [4], in <cell line: 0>()
----> 1 c.name

Input In [2], in Parent.__getattr__(self, name)
      6     return self._fields[name]
      7 except KeyError:
----> 8     raise AttributeError(
      9         f"'{self.__class__.__name__}' object has no attribute '{name}'",
     10     )

AttributeError: 'Child' object has no attribute 'name'
> <ipython-input-2-b3781a93b20e>(8)__getattr__()
      6             return self._fields[name]
      7         except KeyError:
----> 8             raise AttributeError(
      9                 f"'{self.__class__.__name__}' object has no attribute '{name}'",
     10             )
```

从 traceback 信息中可以看到，`child.name` 这一次属性访问调用了父类 `Parent` 的 `__getattr__` 方法。但是在子类 `Child` 的定义中，`name` 这个属性是明显存在的，属性值是另一个 module 的属性。由于 `__getattr__` 只有在属性访问抛出 `AttributeError` 时才会被调用，那么问题根源应该就是另一个 module 里没有 `name` 这个属性，才会调用到 `__getattr__`。

事实上也是如此，`return OtherModule.name` 这一行抛出了 `AttributeError`，属性访问的行为 fallback 到了 `__getattr__`。

## 调试方法

接下来说一下有什么方法可以用来调试这个问题。调试的目的就是为了弄清楚程序逻辑上有什么问题，通常来说有以下几种方法：

1. 打断点，使用 [pdb](https://docs.python.org/3/library/pdb.html) 进行单步调试
2. 加[日志](https://docs.python.org/3/howto/logging.html)，使用二分法，找到错误的位置

### 打断点

打断点就是在程序代码中插入 `breakpoint()`，当程序执行到这行代码时，程序就会停止运行，可以和当前内存中的数据进行交互，方便定位问题。如果是在 REPL 里，也可以通过 `import pdb; pdb.run('your code here')` 开始单步调试。

以 [IPython](https://ipython.org/) 为例，进入 REPL 后执行以下代码来进入单步调试：

```python {hl_lines=[11]}
In [1]: import pdb

In [2]: class OtherModule:
   ...:     pass
   ...:
   ...: # Parent/Child class definitions

In [3]: c = Child()

In [4]: pdb.run('c.name')
> <string>(1)<module>()
```

然后输入 **s** 回车来跳进 `<module>()` 里面，输入 **n** 回车来执行下一行，按 **q** 结束调试。

```python {hl_lines=[3,11,21]}
In [4]: pdb.run('c.name')
> <string>(1)<module>()
(Pdb) s
--Call--
> <ipython-input-2-20572dc1ce97>(24)name()
-> @property
(Pdb) n
> <ipython-input-2-20572dc1ce97>(27)name()
-> return OtherModule.name
(Pdb) n
AttributeError: type object 'OtherModule' has no attribute 'name'
> <ipython-input-2-20572dc1ce97>(27)name()
-> return OtherModule.name
(Pdb) n
--Return--
> <ipython-input-2-20572dc1ce97>(27)name()->None
-> return OtherModule.name
(Pdb)
--Call--
> <ipython-input-2-20572dc1ce97>(8)__getattr__()
-> def __getattr__(self, name):
(Pdb)
> <ipython-input-2-20572dc1ce97>(9)__getattr__()
-> try:
(Pdb) q
```

你也可以在上面第 11 行后输入 `p dir(OtherModule)` 来查看 `OtherModule` 对象的属性列表。

```python
(Pdb) n
AttributeError: type object 'OtherModule' has no attribute 'name'
> <ipython-input-2-20572dc1ce97>(27)name()
-> return OtherModule.name
(Pdb) p dir(OtherModule)
['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__',
'__format__', '__ge__', '__getattribute__', '__getstate__', '__gt__',
'__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__',
'__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__',
'__sizeof__', '__str__', '__subclasshook__', '__weakref__']
(Pdb)
```

## Bonus

在 Python [Data Model](https://docs.python.org/3/reference/datamodel.html) 文档里还有这样一个 [magic method](https://docs.python.org/3/reference/datamodel.html#object.__getattribute__): `__getattribute__`。它会在属性访问时无条件调用。

> **object.\_\_getattribute\_\_(self, name)**
>
> Called unconditionally to implement attribute accesses for instances of the class.

我们知道在 Python 里并没有事实上的私有变量，只有约定上的私有属性。其实借助 `__getattribute__` 方法，我们就可以把约定上的私有属性变为“事实上”的私有属性。

{{< details "Bonus" >}}

```python
class Base:

    def __init__(self):
        self.public_attr = 0
        self._private_attr = 1

    def __getattribute__(self, name):
        if name.startswith("_") and not name.startswith("__"):
            if type(self) is not Base:
                raise AttributeError(f"Unaccessible private attribute: {name}")
        val = super().__getattribute__(name)
        if name == "__dict__" and type(self) is not Base:
            val = {k: v for k, v in val.items() if not k.startswith("_")}
        return val


class Sub(Base):
    pass


b = Base()
print(b.public_attr)    # 0
print(b._private_attr)  # 1
s = Sub()
print(s.public_attr)    # 0
print(s._private_attr)  # AttributeError: Unaccessible private attribute: _private_attr
```

{{< /details >}}
