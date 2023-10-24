+++
title = 'Python 列表推导式里变量未定义的问题'
date = 2023-10-15T23:29:26+08:00
draft = false
tags = ['python', 'scope']
toc = true
+++

之前在[捕蛇者说](https://pythonhunter.org/)的听众群里有人问了下面这个问题：

> 为什么 `eval('''exec("d={};[d for i in range(1)]")''',{},{})` 会报错 NameError
> 而 `eval('''exec("d={};[d for i in range(1)]",None,{})''')` 和 `eval('''exec("d={};[d]",None,{})''')` 都不会报错？

挺有趣的，为什么在有 `d={}` 的情况下还会出现 `NameError` 呢？下面就来分析看看。

<!--more-->

| :information_source: INFO | 使用的 Python 版本为 **3.11.2** |
| :------------------------ | :-----------------------------: |

### TL;DR

`exec` 在执行 Python 语句时有自己的局部作用域，同时列表推导式也有自己的局部作用域。在全局作用域里没有 `d` 这个变量的情况下，列表推导式并不能够获取到 `exec` 执行时的局部作用域里的 `d`。

## 简化问题

首先可以把这个问题拆分成以下三个部分：

1. 最外层的 `eval` 函数的作用。
2. 中间的 `exec` 函数的作用。
3. 最里层的列表推导式。

## `eval` 函数

在 repl 里输入 `help(eval)` 可以得到下面的这段输出：

```text {linenos=false}
eval(source, globals=None, locals=None, /)
    Evaluate the given source in the context of globals and locals.

    The source may be a string representing a Python expression
    or a code object as returned by compile().
    The globals must be a dictionary and locals can be any mapping,
    defaulting to the current globals and locals.
    If only globals is given, locals defaults to it.
```

从上面的 help 文档，我们可以知道 `eval` 函数会去评估 (evaluate) 一条 Python 表达式 (_expression_)，并支持通过参数来指定 **globals** 和 **locals** 两个作用域[^1]。

接着看回原来的问题，可以看到包含了两种情况：

1. `eval(source, {}, {})`: 在 `globals` 和 `locals` 都为空的条件下评估 `source`
2. `eval(source, None, {})`: 在默认的 `globals` 以及空的 `locals` 下评估 `source`

那么接下来看看 `source` 是什么。

## `exec` 函数

需要被评估的代码是 `exec("d={};[d for i in range(1)]")` 和 `exec("d={};[d]")`，所以还是先看看 `exec` 函数的文档：

```text {linenos=false}
exec(source, globals=None, locals=None, /, *, closure=None)
    Execute the given source in the context of globals and locals.

    The source may be a string representing one or more Python statements
    or a code object as returned by compile().
    The globals must be a dictionary and locals can be any mapping,
    defaulting to the current globals and locals.
    If only globals is given, locals defaults to it.
    The closure must be a tuple of cellvars, and can only be used
    when source is a code object requiring exactly that many cellvars.
```

也就是说 `exec` 函数会在给定的 `globals` 和 `locals` 下执行 Python 语句 (_statements_)。

那么原来的问题也就可以简化为以下 4 种情况：

1. `exec("d={};[d for i in range(1)]", {}, {})`
2. `exec("d={};[d for i in range(1)]", None, {})`
3. `exec("d={};[d]", {}, {})`
4. `exec("d={};[d]", None, {})`

所以什么情况下会出现 `NameError` 的异常呢？原因就在于 Python 中很常用的列表推导式身上。

## 列表推导式

`exec` 函数执行的语句分别由两条语句组成，前者为赋值语句，后者则是构造一个列表，只不过构造方式有所差异。

`[d for i in range(1)]` 是一个列表推导式，而 `[d]` 则是一个列表字面量。

第一眼看上去 `d={}` 这条赋值语句会在当前作用域添加一个变量 `d`，而后构造列表时应该可以访问到 `d` 才对，就像以下代码一样：

```python
d = {}
print([d for i in range(1)])  # => [{}]
```

既然现在出现了 `NameError` 的异常，也就说明列表推导式执行时找不到 `d` 这个变量，而 `d={}` 明显给 `d` 赋值了。`d` 到哪里去了？

首先可以确定的是 `d` 是一定存在的，因为有赋值语句，只是现在找不到它而已。而 `d` 会存在的地方有两个，就是 **globals** 和 **locals** 这两个作用域的其中一个。

原问题里是传了两个字典字面量，这不方便后续访问，我们可以用两个变量来替代：

```python
my_globals = {}
my_locals = {}
exec("d = {}; [d for i in range(1)]", my_globals, my_locals)
# NameError: name 'd' is not defined
print(my_globals)  # => {}
print(my_locals)  # => {'d': {}}
```

`d` 出现了，在 **locals** 里。那么问题来了，既然在 **locals** 里，为啥列表推导式里会访问不了 `d` 呢？我们可以在列表推导式里获取 **locals**，比较一下有什么差别：

```python
my_locals = {}
exec("d = {}; c = [locals() for i in range(1)]", None, my_locals)
print(my_locals)
# Result:
{'d': {}, 'c': [{'.0': <range_iterator at 0x1f65cbccd10>, 'i': 0}]}
```

可以看到 `my_locals["c"]` 的值并不等于 `my_locals`。那么问题原因基本上也就可以确定了，就是**列表推导式有自己的局部作用域**。

而 `exec` 在执行语句的时候，也存在着自己的局部作用域，即我们传入的 `my_locals`。

列表推导式只能访问它自己的局部作用域和全局作用域，但是默认的全局作用域里并没有 `d` 存在，所以会出现 `NameError`。

那么如果我们分别在传入的 `my_globals` 和 `my_locals` 里保存一个不同值的 `d`，最终生成的列表是怎样的呢？

```python
my_globals = {"d": 1}
my_locals = {"d": 2}
exec("a = 1; c = [d for i in range(1)]", my_globals, my_locals)
print(my_globals["d"]) # => 1
print(my_locals)
# Result:
{'d': 2, 'a': 1, 'c': [1]}
```

## 等价情况

在 [Python 文档](https://docs.python.org/3.11/library/functions.html#exec)里关于 `exec` 函数的说明里有这么一句话：

> If exec gets two separate objects as globals and locals, the code will be executed as if it were embedded in a class definition.

那么上述代码就可以等价于以下代码：

```python
d = 1
class T:
  a = 1
  d = 2
  c = [d for _ in range(1)]
  # print(locals()) => {'__module__': '__main__', '__qualname__': 'T', 'a': 1, 'd': 2, 'c': [1]}

print(T.c[0] == 1) # => True
```

## Disassembly

当时群里大佬提了一下可以用 [`dis`](https://docs.python.org/3/library/dis.html) 来查看 CPython 的 [bytecode](https://docs.python.org/3/glossary.html#term-bytecode)，就可以看到列表推导式里使用了 [LOAD_GLOBAL](https://docs.python.org/3/library/dis.html#opcode-LOAD_GLOBAL) 指令从全局作用域里加载变量。

```plaintext {linenos=false,hl_lines=[22],linenostart=1}
>>> dis.dis("[a for _ in range(1)]")
  0           0 RESUME                   0

  1           2 LOAD_CONST               0 (<code object <listcomp> at 0x000002BD5774D0D0, file "<dis>", line 1>)
              4 MAKE_FUNCTION            0
              6 PUSH_NULL
              8 LOAD_NAME                0 (range)
             10 LOAD_CONST               1 (1)
             12 PRECALL                  1
             16 CALL                     1
             26 GET_ITER
             28 PRECALL                  0
             32 CALL                     0
             42 RETURN_VALUE

Disassembly of <code object <listcomp> at 0x000002BD5774D0D0, file "<dis>", line 1>:
  1           0 RESUME                   0
              2 BUILD_LIST               0
              4 LOAD_FAST                0 (.0)
        >>    6 FOR_ITER                 9 (to 26)
              8 STORE_FAST               1 (_)
             10 LOAD_GLOBAL              0 (a)
             22 LIST_APPEND              2
             24 JUMP_BACKWARD           10 (to 6)
        >>   26 RETURN_VALUE
```

[^1]: https://en.wikipedia.org/wiki/Scope_(computer_science)
