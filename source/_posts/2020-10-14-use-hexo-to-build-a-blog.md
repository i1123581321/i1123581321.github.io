---
title: 使用 Hexo 与 GitHub pages 快速搭建博客
date: 2020-10-14 22:29:42
tags:
- hexo
- github
categories: ["computer science", "other"]
mathjax: true
---

搭建博客以后的第一篇文章就是如何搭建博客，好比垃圾袋拆开以后第一个垃圾就是它的包装纸

## 安装 Hexo 与搭建博客

Hexo 是一个简洁高效的博客框架，可以快速生成静态网页

安装 Hexo 需要先安装

* [node.js](https://nodejs.org/en/)
* [Git](https://git-scm.com/)

安装后需要确保 PATH 中有可执行程序路径，可以通过下述命令检查

```
$ npm -v
$ node -v
$ git --version
```

之后便可以通过 npm 来安装 hexo

```
$ npm install -g hexo-cli
```

安装完成后即可通过下述命令来初始化博客

```
$ hexo init <folder>
$ cd <folder>
$ npm install
```

网站的基本配置均在 `_config.yml` 文件中，具体各字段的含义参见 [Hexo 文档](https://hexo.io/docs/configuration.html)

需要注意的是 yaml 格式的文件要求**冒号后有一个空格**，否则会报错

之后便可以在博客根目录下使用

```
$ hexo server
```

来运行本地服务器并预览博客的效果了

博客的主题默认是 landscape，而这个博客使用的是 [cactus](https://github.com/probberechts/hexo-theme-cactus)，更换主题只需要将主题文件夹放在 themes 下，然后在配置文件中修改对应属性即可。主题的配置位于对应文件夹下的 `_config.yml`

除此之外可以安装 `hexo-filter-mathjax` 来支持 $\LaTeX$ 风格的数学公式，使用如下命令安装即可，具体的配置参见 [hexo-filter-mathjax](https://github.com/next-theme/hexo-filter-mathjax)

```
$ npm install hexo-filter-mathjax
```

创建新的文章可以使用

```
$ hexo new <title>
```

命令行输出会告诉你生成的 markdown 文件位于何处，接下来就可以开始创作了

## 部署至 Github Pages

显然，你需要先有一个 Github 账号

Github Pages 的细节不再赘述，简单来说，如果你希望以 `<username>.github.io` 来访问博客，直接创建一个名称为 `<username>.github.io` 的 repository，至于其余的情况（如该 repo 已占作他用）请参见 [*GitHub pages 文档*](https://pages.github.com/)

然后安装部署插件

```
$ npm install hexo-deployer-git
```

同时配置 `_config.yml`

```yaml
deploy:
  type: git
  repo: <repository url>
  branch: master
  message: # 提交时的信息，默认为提交时间
```

之后便可以通过命令

```
$ hexo deploy -g
```

将内容部署到 Github pages，这里的 `-g` 是指在部署前先运行

```
$ hexo generate
```

这条命令会生成站点的静态文件，然后将静态文件部署至远程仓库。生成的静态文件可以通过

```
$ hexo clean
```

删除

## 持续集成

持续集成（CI, Continuous Integration）可以自动完成部署工作（清理，生成静态文件，部署）

官方网站给出的[持续集成](https://hexo.io/docs/github-pages.html)是通过 Github Actions 实现的，这里使用 [github-actions-hexo](https://github.com/yrpang/github-actions-hexo) 的方案

首先需要做的准备工作是将远程仓库 `<username>.github.io` clone 到本地，然后创建新的分支

```
$ git checkout -b hexo
```

切换到新分支后删除目录下所有文件，将博客的目录下的文件复制过去，然后提交，将该分支推送到远程

```
$ git push -u origin hexo:hexo
```

此时如果 `.gitignore` 文件正常，远程的文件结构应该类似 [hexo-starter](https://github.com/hexojs/hexo-starter) (没有 `.gitmodules`)，仓库的 master 分支为生成内容，而 hexo 分支为博客源代码

然后使用 `ssh-keygen` 生成一对密钥，在项目 - Settings - Deploy keys - Add deploy key 中添加生成的公钥，再在项目 - Settings - Secrets 添加以下环境变量

|    name    | description |
| :--------: | :---------: |
| DEPLOY_KEY | 生成的私钥  |
|   EMAIL    |    邮箱     |
|    NAME    |   用户名    |

此处的 name 可以任意命名，但是要和后文的 action 中的变量对应

然后修改 `_config.yml`

```yaml
deploy:
  type: git
  repo: <repo_url>
  branch: master
  message:
```

需要注意此处的 `repo_url` 必须是 ssh 形式，即协议为 git 而非 https

然后在根目录下创建文件 `.github/workflows/hexo.yml` 并将如下内容添加进去

```yaml
name: Hexo CICD

on: # 监视 `hexo`分支
  push:
      branches:
      - hexo

jobs:
  build:
    runs-on: ubuntu-latest
    if: "!contains(github.event.head_commit.message, '[ci skip]')"
    steps:
      - name: Checkout
        uses: actions/checkout@v1
        with:
          submodules: true
      - name: Cache node modules
        uses: actions/cache@v1
        with:
          path: node_modules
          key: ${{runner.OS}}-${{hashFiles('**/package-lock.json')}}
      - uses: yrpang/github-actions-hexo@master
        with:
          deploykey: ${{secrets.DEPLOY_KEY}}
          username: ${{secrets.NAME}}
          email: ${{secrets.EMAIL}}
```

这样 Github Action 会监控 hexo 分支上的每次提交，只要提交信息不包含 `[ci skip]` 即执行部署。