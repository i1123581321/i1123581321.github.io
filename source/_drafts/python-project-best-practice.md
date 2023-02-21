---
title: Python 项目的最佳实践
tags:
- "Python"
categories: ["computer science", "python"]
mathjax: false
documentclass: ctexart
classoption: UTF8
geometry:
- margin=1in
- a4paper
---

# Python 项目的最佳实践

## 前言

[Python](https://www.python.org/) 作为当下最流行的脚本语言之一，其最大的特点就是上手迅速。下载 Python 并将其加入路径，执行

```console
$ echo 'print("Hello world")' > hello.py
$ python hello.py
Hello world
```

恭喜你！你写出了第一个 Python 程序。但是当程序规模越来越大，你会遇到各种各样的情况：

* 你的程序需要引入第三方的依赖
* 你想为代码编写单元测试
* 你想将自己的程序打包发布，以供他人引用或执行
* ……

如果之前有过编程经验，你或许会按照功能将脚本文件进行划分，并将其统一放在项目的根目录下，但仅仅这样做是不够的。以下我将介绍一种组织 Python 项目的方式，可以对中等规模的项目进行高效的开发、测试、发布，同时具有较好的可维护性。

以下假定读者对 Python 中的包（package），模块（module）以及打包发布流程（参见 [Packaging Python Projects — Python Packaging User Guide](https://packaging.python.org/en/latest/tutorials/packaging-projects/)）有基本的了解。

## 基本的项目配置

我们将开发一个简单的 Python 项目：`deep-thought`，从创建一个空目录开始

```console
$ mkdir deep-thought
$ cd deep-thought
```

使用 `_` 或 `-` 全凭个人喜好，但请确保保持一致

### Python 版本与虚拟环境

绝大多数情况下在开发的过程中你不需要同时使用多个不同版本的 Python 发行版（如果需要测试程序在不同版本下的兼容性，可以使用 [tox](https://tox.wiki/en/latest/)）。如果真的有这方面的需求，可以考虑手动安装多个版本的 Python 并管理环境变量或设置别名，或者使用 [pyenv](https://github.com/pyenv/pyenv)/[pyenv-win](https://github.com/pyenv-win/pyenv-win)

强烈建议为你的每一个项目使用独立的虚拟环境，以避免不同依赖间产生冲突。本文使用 [virtualenv](https://virtualenv.pypa.io/en/latest/)

```console
$ virtualenv venv
```

### 使用 src 布局组织源代码

有两种主流的用于组织 Python 项目源代码的方式：src layout 与 flat layout，其具体定义与区别可参见 [src layout vs flat layout — Python Packaging User Guide](https://packaging.python.org/en/latest/discussions/src-layout-vs-flat-layout/)

本文使用 src 布局。在项目根目录下建立名为 `src` 的文件夹，然后在 `src` 目录下建立本项目的顶层包 `dt`（一个好习惯是只使用一个顶层包，如果需要多个顶层包，思考一下是否应当拆分成多个项目），以及实现主要功能的文件 `func.py`。此时的项目结构应当是如下形式：

```console
src
└── dt
    ├── __init__.py
    └── func.py
```

本项目的功能是模仿《银河系搭车客指南》中的超级计算机“深思”，`func.py` 中将定义主要的功能函数：提出问题，回答 42

```python
# src/dt/func.py
def ask(_: str) -> int:
    return 42
```

可以在 `__init__.py` 中控制导出的符号

```python
# src/dt/__init__.py
from dt.func import ask

__all__ = ["ask"]
```

### 使用 pyproject.toml 描述项目

在 [PEP 518](https://peps.python.org/pep-0518/) 引入 `pyproject.toml` 后，随着 [PEP 517](https://peps.python.org/pep-0517/)、[PEP 621](https://peps.python.org/pep-0621/) 和 [PEP 660](https://peps.python.org/pep-0660/) 对其扩展，`pyproject.toml` 已经成为了描述 Python 项目元信息的官方标准。`pyproject.toml` 也得到了大部分构建工具的支持，新的项目应当从 `setup.py`/`setup.cfg` 逐步转向 `pyproject.toml`

在根目录下创建名为 `pyproject.toml` 的文件，并在其中添加构建项目所需的必要信息

```toml
# pyproject.toml
[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.build_meta"

[project]
name = "deep-thought"
version = "0.1.0"
```

只需在 `[build-system]` 表中指明依赖的构建系统以及后端，再在 `[project]` 表中指明项目的名称和版本，即可满足构建的最小需求。本项目使用 `setuptools` 作为构建系统

对于项目名，PyPa 给出了规约：[Package name normalization — Python Packaging User Guide](https://packaging.python.org/en/latest/specifications/name-normalization/)，合法的项目名可以用以下正则表达式验证

```regexp
^([A-Z0-9]|[A-Z0-9][A-Z0-9._-]*[A-Z0-9])$
```

个人推荐使用正则化后的项目名作为根目录名称以及 `pyproject.toml` 中的 `project.name` 字段的值，正则化可以使用如下代码实现

```python
import re

def normalize(name):
    return re.sub(r"[-_.]+", "-", name).lower()
```

对于版本号，则推荐遵循 [PEP 440](https://peps.python.org/pep-0440/) 以及 [语义化版本 2.0.0 | Semantic Versioning](https://semver.org/lang/zh-CN/)

### 使用 pip local install 调试项目

至此为止，项目 `deep-thought` 已经可以进行本地安装了（参见：[Local project installs - pip documentation](https://pip.pypa.io/en/stable/topics/local-project-installs/)），在根目录下运行：

```console
$ pip install .
```

此时可以通过 `pip show` 查看包的元信息

```console
$ pip show deep-thought
Name: deep-thought
Version: 0.1.0
Summary:
Home-page:
Author:
Author-email:
License:
Location: path/to/python/site-packages
Requires:
Required-by:
```

实际上运行 `pip install .` 就和手动运行 `python -m build` 构建 whl 文件再通过 pip 安装是一样的。安装后的包位于 pip 所在 Python 环境的 `site-packages` 中——这也意味着对项目的源代码做出的改动是不会反映在已安装的包上的，显然这并不适合开发中的调试。为此 pip 同样提供了 editable install 的模式。卸载刚刚安装的包后，在根目录下运行

```console
$ pip install -e .
```

此时包并没有安装到 `site-package` 下，而只是将包的路径添加到了 Python 加载模块的路径中。打开 REPL 打印 `sys.path` 即可验证这一点。同样可以通过 REPL 验证 `deep-thought` 是否正常工作：

```python-repl
>>> import dt
>>> dt.ask("生命、宇宙以及任何事情的终极答案")
42
>>>
```

### 打包以及发布

此时项目已经可以通过 `build` 进行打包并通过 `twine` 发布（请确保你已通过 pip 安装这两个工具）。在根目录下运行

```console
$ pyproject-build
```

就会在 `dist` 文件夹下生成 sdist 文件和 wheel 文件。接着运行

```console
$ twine upload dist/*
```

即可上传至 pypi。更多细节可参见：[twine documentation](https://twine.readthedocs.io/en/latest/)

## 让你的项目更加完善

虽然我们的项目已经可以发布了，但是仍有许多可以改进的部分。以下的小节都是可选项，但我强烈推荐按照如下方式完善你的项目

### 更完整的 pyproject.toml

首先是 `project` 表，在该表下除了上文提到的 `name` 与 `version` 字段，还可以加入：

```toml
# pyproject.toml
[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.build_meta"

[project]
name = "deep-thought"
version = "0.1.0"
# 对项目的简短描述
description = "A project that imitates 'Deep Thought'"
# 项目的 README，请确保根目录下有 README.md 文件
# 在不能通过后缀判断内容格式时使用如下形式指定
# readme = {file = "README", content-type = "text/markdown"}
readme = "README.md"
# 需要的最小版本 python
requires-python = ">=3.6"
# 项目的 license，请确保根目录下有 LICENSE 文件
license = { file = "LICENSE" }
# 作者的信息
authors = [{ name = "author name", email = "author email" }]
# 维护者的信息
maintainers = [{name = "maintainer name", email = "maintainer email"}]
# 关键字
keywords = ["Deep Thought", "Computer"]
# 分类符
classifiers = [
    "Programming Language :: Python :: 3",
    "Operating System :: OS Independent",
]

# 项目的相关链接
[project.urls]
Homepage = "https://deep.thought"
Source = "https://github.com/someuser/somerepo"
```

完整项目元信息的定义可以参见 [PEP 621](https://peps.python.org/pep-0621/) 以及 [Declaring project metadata — Python Packaging User Guide](https://packaging.python.org/en/latest/specifications/declaring-project-metadata/)

如果项目有第三方依赖，则可以在 `project` 表下的 `dependencies` 项中指明

```toml
[project]
dependencies = [
    "click",
    "requests"
]
```

其中每一项依赖都是一个满足 [PEP 508](https://peps.python.org/pep-0508/) 的字符串

在 `project` 表以外，根据所选用的构建系统补充额外信息。对于本文使用的 `setuptools`，可以指定如何找到需要被打包的模块和包

```toml
[tool.setuptools.packages.find]
where = ["src"]
```

通常来说该步是可以省略的

### 为你的项目添加类型支持

自从 Python 引入类型注解后，对于新项目都建议尽可能完善地添加类型注解。类型注解可以让 IDE 提供更完善的补全和提示支持，也可以减少因为类型不匹配产生的错误。

根据 [PEP 561](https://peps.python.org/pep-0561/)，如果类型注解是以 inline 的形式直接添加在 Python 代码中的，则需要在支持类型检查的包下添加 `py.typed` 文件（本文示例中则是 `src/dt` 目录下）

需要注意的是在添加后还需要编辑 `MANIFEST.in` 文件以确保 `py.typed` 能被正确地打包进发行版

```
# MANIFEST.in
include src/dt/py.typed
```

### 为项目编写单元测试

单元测试是代码质量的基本保障，越早进行测试就可以越早发现隐含的问题并进行解决。本文使用 `Pytest` 作为单元测试的框架。

在项目根目录下建立名为 `tests` 的文件夹，然后在其中建立 `test_dt` 包。一个好习惯是尽可能使用与源代码目录相同的结构组织测试代码，并且在包名与模块名前加上 `test_` 作为前缀。

在 `test_dt` 包中建立测试文件

```python
# tests/test_dt/test_ask.py
def test_ask():
    assert ask("The question") == 42
```

随后运行

```console
$ pytest
```

即可执行单元测试