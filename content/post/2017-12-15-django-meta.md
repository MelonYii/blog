---
title: "python meta 探索"
tags: ["python", "meta", "flask", "django"]
date: 2017-12-15
---

最开始是想了解一下Django框架的ORM是如何实现的。发现其实涉及到的一个重要知识点就是meta。本人python并没有非常系统的学习，于是决定探一探python的meta。

## 引子

Python 中的一切都是对象，包括类也是对象。大家有没有好奇，那类的类是什么呢？代码告诉你。

```python
a = 1
a.__class__  # int
type(a)  # int

class A(object):
    pass

A.__class__  # type
A.__class__.__class__  # type
type(A)  # type
```

控制台中输出的`type`和查看类型的`type`的功能可不一样。类的类其实就是元类。

## type 能力

`type`除了我们常用的返回对象的类型外，还有一个功能。它能动态的创建类。

```python
type(类名, 父类的元组（针对继承的情况，可以为空），包含属性的字典（名称和值）)
```

以下两段代码是等价的，

```python
class MyShinyClass(object):
    pass

MyShinyClass = type('MyShinyClass', (), {})
```

当我们使用 class 来创建类的时候，Python 实际上就是用 type 来创建类的，这种方式主要是通过元类(meta class)来实现的。

## 什么是 meta class

在创建类的时候，有一个特殊的属性(`__metaclass__`)，可以用函数或者类来指定元类。

```python
class Foo(object):
    __metaclass__ = something

```

上述代码会使用`something`来创建类。

```python
class Foo(Bar):
    pass
```

如果是上述代码创建类，python将做如下动作：

1. Foo中有`__metaclass__`这个属性吗？如果是，Python会在内存中通过`__metaclass__`创建一个名字为Foo的类对象（我说的是类对象，请紧跟我的思路）。
2. 如果Python没有找到`__metaclass__`，它会继续在Bar（父类）中寻找`__metaclass__`属性，并尝试做和前面同样的操作。
3. 如果Python在任何父类中都找不到`__metaclass__`，它就会在模块层次中去寻找`__metaclass__`，并尝试做同样的操作。
4. 如果还是找不到`__metaclass__`,Python就会用内置的type来创建这个类对象。

## 自定义元类

我们自定义元类的主要目的就是为了当创建类时能够自动地改变类。通常，你会为API做这样的事情。假想一个很傻的例子，你决定在你的模块里所有的类的属性都应该是大写形式。有好几种方法可以办到，但其中一种就是通过在模块级别设定`__metaclass__`。采用这种方法，这个模块中的所有类都会通过这个元类来创建，我们只需要告诉元类把所有的属性都改成大写形式就万事大吉了。

```python
# 请记住，'type'实际上是一个类，就像'str'和'int'一样
# 所以，你可以从type继承
class UpperAttrMetaClass(type):
    # __new__ 是在__init__之前被调用的特殊方法
    # __new__是用来创建对象并返回之的方法
    # 而__init__只是用来将传入的参数初始化给对象
    # 你很少用到__new__，除非你希望能够控制对象的创建
    # 这里，创建的对象是类，我们希望能够自定义它，所以我们这里改写__new__
    # 如果你希望的话，你也可以在__init__中做些事情
    # 还有一些高级的用法会涉及到改写__call__特殊方法，但是我们这里不用
    def __new__(upperattr_metaclass, future_class_name, future_class_parents, future_class_attr):
        attrs = ((name, value) for name, value in future_class_attr.items() if not name.startswith('__'))
        uppercase_attr = dict((name.upper(), value) for name, value in attrs)
        return type(future_class_name, future_class_parents, uppercase_attr)


__metaclass__ = UpperAttrMetaClass  #  这会作用到这个模块中的所有类
 
class Foo(object):
    # 我们也可以只在这里定义__metaclass__，这样就只会作用于这个类中
    bar = 'bip'


print hasattr(Foo, 'bar')
# 输出: False
print hasattr(Foo, 'BAR')
# 输出:True
```

使用到元类的代码比较复杂，这背后的原因倒并不是因为元类本身，而是因为你通常会使用元类去做一些晦涩的事情，依赖于自省，控制继承等等。确实，用元类来搞些“黑暗魔法”是特别有用的，因而会搞出些复杂的东西来。但就元类本身而言，它们其实是很简单的：

1. 拦截类的创建
2. 修改类
3. 返回修改之后的类


在实际中，最典型使用元类的例子就是ORM了。这边推荐看一下[编写ORM-廖雪峰](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014323389656575142d0bcfeec434e9639a80d3684a7da000)，这就是一个很好的运用元类的例子。

## 究竟为什么要使用元类？

为什么你会去使用这样一种容易出错且晦涩的特性？好吧，一般来说，你根本就用不上它：

>> “元类就是深度的魔法，99%的用户应该根本不必为此操心。如果你想搞清楚究竟是否需要用到元类，那么你就不需要它。那些实际用到元类的人都非常清楚地知道他们需要做什么，而且根本不需要解释为什么要用元类。”  —— Python界的领袖 Tim Peters

## 结语

虽然实际在工作中很少会用到这么高级的特性，但其实挺多框架都有用到，学习一下，在阅读这些源码的时候也能够比较顺利的进行下去。

### 参考
[深刻理解Python中的元类(metaclass)](http://blog.jobbole.com/21351/)
[使用元类-廖雪峰](https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386820064557c69858840b4c48d2b8411bc2ea9099ba000#0)
[Python 的 type 和 meta class 简介](http://icejoywoo.github.io/2017/03/28/python-intro-of-meta-class-and-type.html#%E4%BB%80%E4%B9%88%E6%98%AF-meta-class)