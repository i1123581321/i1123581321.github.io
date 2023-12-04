---
title: Python 元类
tags:
  - Python
categories:
  - computer science
  - python
mathjax: false
documentclass: ctexart
classoption: UTF8
geometry:
  - margin=1in
  - a4paper
---

# Python 元类

## 前言

元类是 Python 元编程中的一个重要功能。类的方法可以控制实例的创建、初始化与行为，相应的，元类也可以控制其实例（也就是一般的类）的创建、初始化与行为。

## 面向对象与 magic methods

Python 是一门支持面向对象范式的编程语言，其所有类型都有一个共同的基类：`object`。类型同样也是对象，通过 `class` 关键字定义的类都是 `type` 的实例，且 `type` 对象也是 `type` 的实例，而 `type` 同样有 `object` 作为其基类，`object` 则是 `type` 的实例。通过 REPL 即可验证上述关系

```python-repl
>>> class A:
...     pass
...
>>> a = A()
>>> issubclass(A, object)
True
>>> isinstance(a, A)
True
>>> isinstance(A, type)
True
>>> isinstance(type, type)
True
>>> issubclass(type, object)
True
>>> isinstance(object, type)
True
```