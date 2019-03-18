# Git 提交规范

> 我当年提交的代码记录里只写了 bugfix 这六个字，现在想不起来当时这个 bugfix 的原因。我现在后悔，总之就是非常的后悔
> ————田正男

众所周知，git 提交时必须要有提交信息。

在没有提交信息规范的情况下，提交信息可以写任何东西。
于是就有了这样不雅的情况

```shell
git commit -am 'I have a big dick'
```

**但这样的 commit 信息会在未来寻找过去这个改动的意图时非常困难**

**commit message 应该清晰明了，说明本次提交的目的**
我们代码合并到`develop`或者`master`分支上时，必须启用 squash commit，让我们在其它分支上做的更改压扁为一条更改，合并到对应分支上，这样有助于我们之后更加清晰地排查问题。请各条线项目负责人在 gitlab 的配置中开启 squash commit。

**对于合并到 master 和 develop 分支的代码，merge request 的信息请严格按照下面的规范。**，其它分支阶段性的暂存的改动不硬性要求

提交信息分为 2 部分，header 和 body

## Header

Header 理解为标题，简明扼要地说明这个提交的目的
Header 为一行，分为三部分，格式如下`${type}(${scope}): ${subject}`

### Type

Type 只能为下面的类型

- feature 代表 feature
- bugfix 代表对 bug 的修改
- hotfix 代表对线上问题的修改
- refactor 重构，不是新增功能，也不是修改 bug
- chore 构建工具变动

### scope

影响范围。请写实际的业务模块

### subject

本次修改的主题，简明扼要地说明你做了什么。比如实现了某某需求。另外结尾请不要加句号和逗号

## Body

对本次 commit 的详细描述，可以拆分成多行，使用 Markdown 的列表语法

特别地，**应该说明代码变动的动机，以及与以前行为的对比**
