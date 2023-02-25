---
title: Python 项目的最佳实践
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
date: 2023-02-25 19:52:56
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

通常来说该步是可以省略的，setuptools 会自动识别出 src layout 并添加其下所有 package，但如果有特殊情况需要控制打包的 package，也可以在此进行单独配置。

### 为你的项目添加类型检查支持

自从 Python 引入类型注解后，对于新项目都建议尽可能完善地添加类型注解。类型注解可以让 IDE 提供更完善的补全和提示支持，也可以减少因为类型不匹配产生的错误。

根据 [PEP 561](https://peps.python.org/pep-0561/)，如果类型注解是以 inline 的形式直接添加在 Python 代码中的，则需要在支持类型检查的包下添加 `py.typed` 文件（本文示例中则是 `src/dt` 目录下）

需要注意的是在添加后还需要编辑 `MANIFEST.in` 文件以确保 `py.typed` 能被正确地打包进发行版

```
# MANIFEST.in
include src/dt/py.typed
```

### 为项目编写单元测试

单元测试是代码质量的基本保障，越早进行测试就可以越早发现隐含的问题并进行解决。本文使用 `pytest` 作为单元测试的框架。

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

即可执行单元测试。pytest 会按照文件名匹配测试模块并且将其导入，然后按照方法名匹配执行测试用例。目前 pytest 使用三种导入方式，分别是

* `prepend`：将测试模块路径添加到 `sys.path` 的*前方*
* `append`：将测试模块路径添加到 `sys.path` 的*后方*
* `importlib`：不修改 `sys.path`，而是使用 `importlib` 导入测试模块

通常来说建议使用 `importlib` 作为默认的导入方式，由于不修改 `sys.path`，因此被测试的库代码一定要预先安装在 python 环境中，这也确保了被测试的是安装的代码而非开发中的代码

pytest 可以使用 pyproject.toml 进行配置，只需在其中添加

```toml
[tool.pytest.ini_options]
addopts = "--import-mode=importlib"
```

### 为函数添加 docstring 与 doctest

[PEP 257](https://peps.python.org/pep-0257/) 中提出，docstring 是一个*字符串字面量*，出现在模块/函数/类/方法定义的第一条语句。对于包，则可以在其 `__init__.py` 中添加 docstring。正确编写的 docstring 其内容会成为被注释的对象的 `__doc__` 属性。

永远使用三个连续的双引号来定义 docstring。现在有许多插件可以一键生成符合指定格式的 docstring，本文使用 Google 的格式。现在为 `ask` 函数添加对应的 docstring

```python
# src/dt/func.py
def ask(_: str) -> int:
    """ask deep thought

    Args:
        _ (str): the question

    Returns:
        int: answer
    """
    return 42
```

执行 repl，可以看到定义的 docstring 被保存至运行时

```python-repl
>>> import dt
>>> dt.ask.__doc__
'ask deep thought\n\n    Args:\n        _ (str): the question\n\n    Returns:\n        int: answer\n    '
>>> help(dt.ask)
Help on function ask in module dt.func:

ask(_: str) -> int
    ask deep thought

    Args:
        _ (str): the question

    Returns:
        int: answer

>>>
```

可以使用内置的 `help` 函数查看方法的 docstring

doctest 则是 python 内置的测试模块，可以用于测试类似 repl 环境代码的文本。对于不依赖外部服务的函数或方法来说，可以在 docstring 中加入 repl 代码来演示使用场景，并使用 doctest 验证功能与文档是否保持一致。接下来为 `ask` 函数添加对应的 repl 代码。

```python
# src/dt/func.py
def ask(_: str) -> int:
    """ask deep thought

    >>> ask("some question")
    42

    Args:
        _ (str): the question

    Returns:
        int: answer
    """
    return 42
```

可以通过在命令行直接调用 doctest 模块进行测试，但推荐使用 pytest 整合 doctest 的功能进行测试。首先在 pyproject.toml 中进行配置

```toml
[tool.pytest.ini_options]
addopts = [
    "--import-mode=importlib",
    "--doctest-modules"
]
```

然后运行

```console
$ pytest
collected 2 items

src\dt\func.py .                                  [ 50%]
tests\test_dt\test_func.py .                      [100%]
```

即可看到位于源代码 docstring 中的测试和位于 `tests` 目录下的单元测试都被收集并执行了

### 使用 pytest-cov 统计测试覆盖率

代码覆盖率是软件测试中的一种重要度量标准，通常我们并不追求 100% 的覆盖率，但覆盖率也能一定程度上反映出测试代码是否考虑周全。可以使用 pytest 的插件 pytest-cov 来统计代码覆盖率

```console
$ pip install pytest-cov
$ pytest --cov=src --cov-report=xml
```

通过在调用 pytest 时指定统计覆盖率的源代码目录以及输出的报告格式，即可在测试后生成对应的覆盖率报告。一般来说生成 xml 格式的报告后可以配合编辑器的插件直接在源代码界面展现覆盖率，也可以生成 html 格式的报告后在浏览器中查看。

上述选项同样可以在 pyproject.toml 中配置，以在每次运行时都更新覆盖率报告。

```toml
[tool.pytest.ini_options]
addopts = [
    "--import-mode=importlib",
    "--doctest-modules",
    "--cov=src",
    "--cov-report=xml"
]
```

### 规范代码风格

可读性高且风格统一的代码可以提升开发效率，也有助于多人开发时的团队合作。python 的代码风格一般是指由 [PEP 8](https://peps.python.org/pep-0008/) 所规定的风格。在规范代码风格时会使用到两类工具：

* formatter：用于将代码按照一定标准进行格式化
* linter：用于检查代码是否满足标准（通常是 PEP 8）

本文选择 [black](https://black.readthedocs.io/en/stable/)，[isort](https://pycqa.github.io/isort/) 作为 formatter，[flake8](https://flake8.pycqa.org/en/latest/) 作为 linter。要使用这些工具，首先需要将其安装在虚拟环境中

```console
$ pip install black isort flake8
```

black 以其开箱即用而闻名，通常使用默认配置即可。需要注意的是 isort 与 flake8 需要针对 black 进行一些调整。对于 isort 可以直接在 pyproject.toml 中进行配置

```toml
[tool.isort]
profile = "black"
```

而 flake8 尚不支持通过 pyproject.toml 配置，因此需要新建 `.flake8` 文件并在其中配置

```ini
# .flake8
[flake8]
max-line-length = 88
extend-ignore = E203
```

在完成一定阶段的代码编写之后（比如 git 提交前），可以运行 isort 与 black 对代码进行格式化，然后运行 flake8 检查是否符合标准。这一步可以手动在命令行进行，也可以配置为 git 的 hook 执行。

### 更严格的类型检查

通常如果你使用 vscode 开发 python，那么 Pylance 自带的类型检查在大多数时候都足够了（当你依赖的第三方库有良好的类型注解时可以使用更严格的模式，如 `strict`，一般情况下使用 `basic` 即可）。但如果你希望使用更严格也更灵活的类型检查，则可以使用 [mypy](https://mypy.readthedocs.io/en/stable/index.html)

```console
$ pip install mypy
```

通常情况下对于新开发的项目，可以直接在 pyproject.toml 中开启 mypy 的严格模式

```toml
[tool.mypy]
strict = true
```

如果依赖的某些第三方库缺少类型注解，则可以单独配置忽略其类型检查

```toml
[[tool.mypy.overrides]]
module = [
    "somelib"
]
ignore_missing_imports = true
```

也可以渐进式地添加 mypy 的规则以减少迁移成本。本文常用的配置如下

```toml
namespace_packages = false
disallow_any_generics = true
disallow_untyped_defs = true
no_implicit_optional = true
check_untyped_defs = true
warn_return_any = true
warn_unused_ignores = true
```

而 strict 默认开启的规则可以在命令行通过 `mypy -h` 查看

### 确定 python 最小依赖版本

在 pyproject.toml 中的 `project` 表中有一项 `requires-python`，用于确定发布的包所支持的 python 版本。但通常情况下当项目规模上升，我们并不能轻松地确认当前代码能正确运行的最低 python 版本。一个方法是使用 [tox](https://tox.wiki/en/latest/) 这样的工具进行测试，本文给出另一种通过静态分析代码的方式确定最低支持版本的方法。

使用工具 [vermin](https://github.com/netromdk/vermin)，先将其安装到虚拟环境

```console
$ pip install vermin
$ vermin src
```

即可输出项目最低支持版本。也可以使用配置文件来输出更详细的内容

```ini
# vermin.ini
[vermin]
quiet = yes
targets = 3.7
backports =
    typing
parse_comments = no
eval_annotations = yes
only_show_violations = yes
```

### 为项目添加可选依赖

上述小节提到的工具都是在开发时需要用到，但并不会成为项目依赖的一部分进行打包发布的库，此时可以使用 pyproject.toml 中可选依赖的功能对这些工具的依赖信息进行控制

```toml
[project.optional-dependencies]
test = [
    "pytest >= 7.0.0",
    "pytest-cov"
]
lint = [
    "black",
    "isort",
    "flake8",
    "mypy"
]
```

该表项的每一项都是符合如下正则表达式的可选依赖集名称，内容则是一个列表，其中每一项是满足 [PEP 508](https://peps.python.org/pep-0508/) 的字符串

```regexp
^([a-z0-9]|[a-z0-9]([a-z0-9-](?!--))*[a-z0-9])$
```

在通过 pip 安装时，则可以用方括号指明需要的可选依赖

```console
$ pip install somepackage[optional_dependency]
```

可选依赖在进行本地安装时也是有效的。如果有多个可选依赖集要同时安装，则在方括号中使用逗号分隔（注意不能有空格）

```console
$ pip install -e .[test,lint]
```

### 为项目添加命令行入口

当你的项目是一个库时，完成上面的步骤基本足够了，但如果你的项目希望能同时提供命令行服务（就像上文提到的 black/isort 等工具一样），那么还需要提供命令行入口。

命令行入口通过在 pyproject.toml 的 `project.scripts` 表中添加表项实现。其中键是生成的可执行文件名称，值是形如 `module_name:function_name` 的字符串

在 `func.py` 中添加 `main` 函数

```python
# src/dt/func.py

def main():
    question = input("提出问题：")
    print(f"答案：{ask(question)}")
```

然后修改 pyproject.toml，加入

```toml
[project.scripts]
dt = "dt.func:main"
```

需要注意的是不同于源代码的变更，对 pyproject.toml 做出修改时需要卸载并重新安装当前项目才能生效。生成的命令行入口程序位于当前 Python 环境的 `Scripts` 目录下

```console
$ dt
提出问题：生命、宇宙以及任何事情的终极答案
答案：42
```

一个更好的方案是在当前包下创建 `__main__.py` 并将命令行的入口放入该文件下

```python
# src/dt/__main__.py
from dt.func import ask


def main():
    question = input("提出问题：")
    print(f"答案：{ask(question)}")

main()
```

然后修改 pyproject.toml 为

```toml
[project.scripts]
dt = "dt.__main__:main"
```

此时不仅可以通过 `dt` 直接调用程序，也可以通过

```console
$ python -m dt
提出问题：生命、宇宙以及任何事情的终极答案
答案：42
```

的方式进行调用。在某些情况下 `Scripts` 目录可能并不在路径中，此时提供第二种等价的调用方案便很有帮助了