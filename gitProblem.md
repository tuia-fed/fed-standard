## git 简介

> Git 是一个内容寻址文件系统。Git 的核心部分是一个简单的键值对数据库（key-value data store）。 你可以向该数据库插入任意类型的内容，它会返回一个键值，通过该键值可以在任意时刻再次检索（retrieve）该内容。

上面是《[Git Book](https://git-scm.com/book/zh/v2/Git-%E5%86%85%E9%83%A8%E5%8E%9F%E7%90%86-Git-%E5%AF%B9%E8%B1%A1)》在介绍 git 内部原理时对 git 的解释。

作为 git 使用者，我们没必要深入了解 git 的内部原理，对它的模型有个大致的概念就好。

git 中的 commit 可以理解为数据结构中的节点对象，它储存了提交者的各种信息，文件的快照，还有父节点的引用。所有的 commit 节点组合成了一棵树，每个 commit 节点是不可变的，一旦生成就不会被删除、修改和移动。

分支就是一个指针，它储存了某个 commit 的引用。`reset` 操作、切换分支、添加/删除分支都不会改变 commit 节点，本质其实就是分支内引用的改变和分支的添加删除。

因此，只要我们不破坏 `.git` 文件，我们就可以在 commit 树上自由穿梭，达到我们想要的任何状态。

## git 常见问题及解决方法

#### 代码 commit 到错误的分支上怎么办

使用软重置，将 commit 代码退回到暂存区。

```shell
$ git reset --soft HEAD^
```

然后切到正确的分支上，提交代码。

```shell
$ git checkout rightBranchName
$ git commit -m 'some commit message'
```

#### 修改文件名称/移动文件

如果直接修改文件名称或移动文件会导致之前的提交信息丢失。

使用 `mv` 命令可以防止以上情况发生。

```shell
$ git mv oldName newName
```

#### git 大小写问题

开启大小写敏感

```shell
$ git config core.ignorecase true
```

#### 找回被删除后的分支

利用 `reflog` 找到分支被删除之前所指向的 commit ID

```shell
$ git reflog -g
```

使用`branch`命令，创建被删除掉的分支

```shell
$ git branch recoverBranch commitID
```

#### 撤销 `git reset --hard`

其实 git 并没有撤销的操作，但还是有办法回到硬重置前的状态。

利用 `reflog` 找到硬重置操作前的那个 commit ID

```shell
$ git reflog -g
```

使用 `checkout` 检出到那个 commit

```shell
$ git checkout commitID
```

#### 如何 revert 中间的某个 commit

例子：`revert` 掉 master 分支中间的 c 节点

![revert-example.png](http://yun.dui88.com/Pictures/2019-7-18/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-18%20%E4%B8%8A%E5%8D%889.35.15.png)

```shell
$ git revert a3bf7
```

这样很可能会有冲突，需要解决冲突后再提交。

如果 revert 过程中有冲突，你想放弃当前的 `revert` 操作，可以使用 `--abort` 选项。

```shell
$ git revert --abort
```

在 `merge` `revert` `rebase` `cherry-pick` 操作的过程中发生冲突，都可以使用 `--abort` 选项来放弃操作。

#### 如何 `revert` 一个 merge 节点

如图所示，想要 `revert` 掉从 dev 分支合并进来的 commit g,该如何操作呢？

![revert-merge-commit.png](http://yun.dui88.com/Pictures/2019-7-18/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-18%20%E4%B8%8A%E5%8D%8810.37.56.png)

当我们直接 `revert`,会出现以下的提示

![cant-revert-directly.png](http://yun.dui88.com/Pictures/2019-7-18/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-18%20%E4%B8%8A%E5%8D%8810.40.20.png)

大致意思是：`a8d08 commit` 是一个合并节点，在 `revert` 操作时需要带上 `-m` 选项。

正确的操作应该是这样：

```shell
$ git revert -m 1 a8d08
```

为什么 `revert` 一个合并节点需要 `-m` 的选项，并且还要在后面加个 `1` 呢？

从前一个图片可以看到，如果 master 分支要 `revert` 掉 commit g,那它将面临两个选择，到底是保留上面那个分支的内容还是保留下面那个分支的内容。对我们来说，很明显要保留上面那个分支，但是 git 不知道要保留哪个，所以需要我们使用 `-m` 选项给它指定。`1` 表示上面那个分支，`2` 表示合并进来的那个分支，也就是下面那个分支。

#### 恢复被 revert 掉的 commit

恢复被 `revert` 掉的 commit 其实很简单，就是 `revert` 那个 revert commit。

#### 谨慎使用 `reset --hard` 与 `push -f` 的组合

已经 `push` 到远程的分支，如果要撤销某个 commit 一般需要使用 `revert`。

还有一种方法就是使用 `reset --hard` 撤销某个 commit，然后用 `push -f` 强推到远程分支。

**注意：** 后面的那个方法在多人合作的分支上会有很大的风险。例如你撤销了某个带 bug 的 commit,在你强推前你的同事已经将那个带有 bug 的 commit 拉到本地。你即便是将硬重置的分支强推到远程，你的同事还是会将带 bug 的 commit 推到远程分支。

**总结：** 已 `push` 到远程的分支使用 `revert` 操作。如果真要使用 `reset --hard` 加 `push -f` 的方式，一定要确保该分支仅自己一个人在使用。
