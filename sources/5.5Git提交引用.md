# Git提交引用和引用日志

> BY 童仲毅（[geeeeeeeeek@github](https://github.com/geeeeeeeeek/git-recipes/)）
>
> 这是一篇在[原文（BY atlassian）](https://www.atlassian.com/git/tutorials/refs-and-the-reflog)基础上演绎的译文。除非另行注明，页面上所有内容采用知识共享-署名（[CC BY 2.5 AU](http://creativecommons.org/licenses/by/2.5/au/deed.zh)）协议共享。

提交是 Git 的精髓所在，你无时不刻不在创建和缓存提交、查看以前的提交，或者用各种Git命令在仓库间转移你的提交。大多数的命令都对同一个提交操作，而有些会接受提交的引用作为参数。比如，你可以给 `git checkout` 传入一个引用来查看以前的提交，或者传入一个分支名来切换到对应的分支。

![引用一次提交的各种方式](https://wac-cdn.atlassian.com/dam/jcr:62440330-6153-4f43-9554-b94c7b205e62/01.svg)

知道提交的各种引用方式之后，Git 的命令就会变得更加强大。在这章中，我们研究提交的各种引用方式，来一窥 `git checkout`、`git branch`、`git push` 等命令的工作原理。

我们还会学到如何使用 Git 的引用日志查看似乎已被删除的提交。

## 哈希字串

引用一个提交最直接的方式是通过 SHA-1 的哈希字串，这是每个提交唯一的 ID。你可以在 `git log` 的输出中找到提交的哈希字串。

```
commit 0c708fdec272bc4446c6cabea4f0022c2b616eba
Author: Mary Johnson <mary@example.com>
Date:   Wed Jul 9 16:37:42 2014 -0500

    一些提交信息
```

在 Git 命令中传递时，你只需要提供足以确定那个提交的哈希子串即可。比如，你可以这样用 `git show` 的命令显示上面的提交：

```
git show 0c708f
```

有时，我们需要把分支、标签或者其他间接的引用转变成对应提交的哈希。`git rev-parse` 命令正是你需要的。下面这个命令返回 master 分支提交的哈希字串：

```
git rev-parse master
```

当你写的自定义脚本中需要将提交引用作为参数时，这个命令非常有用。你可以让 `git rev-parse` 帮你处理转换，而不用手动做这件事。

## 引用

ref 是提交的间接引用。你可以把它当做哈希字串的别名，但对用户更友好。这就是 Git 内部表示分支和标签的机制。

引用以一段普通的文本存在于 `.git/refs` 目录中，就是我们平时说的那个 `.git`。你去 `.git/refs` 文件夹查看仓库中的引用。你可以看到下面这样的结构，但具体的文件取决于你的仓库中有什么分支和标签，以及你的远程仓库。

```
.git/refs/
    heads/
        master
        some-feature
    remotes/
        origin/
            master
    tags/
        v0.9
```

`heads`目录定义了你本地仓库中的所有分支。每一个文件名和你的分支名一一对应，文件中包含一个提交的哈希字串。这个就是分支顶端的所在位置。为了验证这一点，试试在 Git 根目录运行下面这两个命令：

```
# 输出`refs/heads/master`文件内容
cat .git/refs/heads/master

# 查看`master`分支尾端的提交
git log -1 master
```

`cat` 命令返回的哈希字串和 `git log` 命令显示的哈希字串应该是一致的。

如果要改变 master 分支的位置，Git 只需要更改 `refs/heads/master` 的文件内容。同样地，创建新的分支也只需要将当前提交的哈希字串写入到新的文件中。这也是为什么 Git 分支比 SVN 轻量那么多的其中一个原因。

`tags` 目录也是以相同的方式存储，只不过其中存的是标签而不是分支。`remotes` 目录将你之前用 `git remote` 命令创建的所有远程仓库以子目录的形式一一列出。在每个文件夹中，你可以找到所有 fetch 到本地仓库的远程分支。

### 指定引用

当你向 Git 命令传入引用的时候，你既可以指定引用完整的名称，也可以使用缩写，然后让 Git 来寻找匹配。你应该已经对引用的缩写很熟悉了，每次你通过名称引用分支的时候都会这么做。

```
git show some-feature
```

这里的 `some-feature` 参数其实是分支名的缩写。Git 在使用前将它解析成 `refs/heads/some-feature`。你也可以在命令行中指定引用的全称，就像这样：

```
git show refs/heads/some-feature
```

这避免了引用可能产生的所有歧义。这是非常必要的，比如你同时有一个标签和分支都叫 `some-feature`。然而，如果使用正常的命名规范，你不应该有这样的歧义。

我们会在 refspec 一节见到更多引用名称。

## 打包引用目录

对于大型仓库，Git 会周期性地执行垃圾回收来移除不需要的对象，将所有引用文件压缩成单个文件来获得更好的性能。你可以使用这个命令强制垃圾回收来执行压缩：

```
git gc
```

这个命令把 `refs` 文件夹中所有单独的分支和标签移动到了 `.git` 根目录下的 `packed-refs` 文件中。如果你打开这个文件，你会发现提交的哈希字串和引用之间的映射关系：

```
00f54250cf4e549fdfcafe2cf9a2c90bc3800285 refs/heads/feature
0e25143693cfe9d5c2e83944bbaf6d3c4505eb17 refs/heads/master
bb883e4c91c870b5fed88fd36696e752fb6cf8e6 refs/tags/v0.9
```

另一方面，正常的 Git 功能不会受到任何影响。但如果你好奇你的 `.git/refs` 文件夹为什么是空的，这一节告诉你了答案。

## 特殊的引用

除了 `refs` 文件夹外，`.git` 根目录还有一些特殊的引用。如下所示：

- HEAD – 当前所在的提交或分支。
- FETCH_HEAD – 远程仓库中 fetch 到的最新一次提交。
- ORIG_HEAD – HEAD 的备份引用，避免损坏。
- MERGE_HEAD – 你通过 `git merge` 并入当前分支的引用（们）。
- CHERRY_PICK_HEAD – 你 `cherry pick` 使用的引用。

这些引用由 Git 在需要时创建和更新。比如说，`git pull` 命令首先运行 `git fetch`，而 `FETCH_HEAD` 引用随之改变。然后，运行 `git merge FETCH_HEAD` 来将 fetch 到的分支最终并入仓库。当然，你也可以使用其他任何引用，因为我相信你已经对 `HEAD` 很熟悉了。

这些文件包含的内容取决于它们的类型和你的仓库状态。`HEAD` 引用可以包含符号链接（指向另一个引用而不是哈希字串），或是提交的哈希字串。比如说，看看当你在 master 分支上时 `HEAD` 的内容：

```
git checkout master
cat .git/HEAD
```

这个命令会输出 `ref: refs/heads/master`，也就是说 HEAD 指向 `refs/heads/master` 这个引用。这也正是 Git 如何知道现在所在的是 master 分支。如果你要切换分支，`HEAD` 的内容将会被更新到新的分支。但如果你要切换到一个提交而不是分支，`HEAD` 会包含一个提交的哈希而不是符号引用。这就是 Git 如何知道现在 `HEAD` 处于分离状态。

在大多数情况下，`HEAD` 是你唯一用得到的引用。其它引用一般只在写底层脚本，接触到 Git 内部的工作机制时才会用到。



## refspec

refspec 将本地分支和远程分支对应起来。我们可以通过它用本地的 Git 命令管理远程分支，设置一些高级的 `git push` 和 `git fetch` 行为。

refspec 的定义是这样的：`[+]<src>:<dst>`。`<src>` 参数是本地的源分支，`<dst>` 是远程的目标分支。可选的 `+` 号强制远程仓库采用非快速向前的更新策略。

refspec 可以和 `git push` 一起使用，用来指定远程的分支的名称。比如，下面这个命令将 master 分支推送到远程 origin，就像一般的 `git push` 一样，但它使用 qa-master 作为远程仓库中的分支名。对于 QA 团队来说，这个方法非常有用。

```
git push origin master:refs/heads/qa-master
```

你也可以用 refspec 来删除远程分支。feature 分支的工作流经常会遇到这种情况，将 feature 分支推送到远程仓库中（比如说为了备份）。你删除本地的 feature 分支之后，远程的 feature 分支依然存在，虽然现在我们已经不再需要它。你可以 push 一个 `<src>` 参数为空的 refspec 来删除它们，就像这样：

```
git push origin:some-feature
```

这非常方便，因为你不需要登录到你的远程仓库然后手动删除这些远程分支。注意，在 Git v1.7.0 之后你可以用 `--delete` 标记代替上面这个方法。下面这个命令和上面的命令作用相同：

```
git push origin --delete some-feature
```

在 Git 配置文件中增加几行，你就可以更改 `git fetch` 的行为。默认地，`git fetch` 会 fetch 远程仓库中所有分支。原因就是 `.git/config` 文件的这段配置：

```
[remote "origin"]
    url = https://git@github.com:mary/example-repo.git
    fetch = +refs/heads/*:refs/remotes/origin/*
```

fetch 这一行告诉 `git fetch` 从 origin 仓库中下载所有分支。但是，一些工作流不需要所有分支。比如，很多持续集成工作流只关心 master 分支。为了做到这一点，我们需要将 fetch 这行改成下面这样：

```
[remote "origin"]
    url = https://git@github.com:mary/example-repo.git
    fetch = +refs/heads/master:refs/remotes/origin/master
```

你还可以类似地修改 `git push` 的配置。比如，如果你总是将 master 分支推送到 origin 仓库的 qa-master 分支（就像我们之前做的一样），你要把配置文件改成这样：

```
[remote "origin"]
    url = https://git@github.com:mary/example-repo.git
    fetch = +refs/heads/master:refs/remotes/origin/master
    push = refs/heads/master:refs/heads/qa-master
```

refspec 给了你完全的掌控权，可以定制 Git 命令如何在仓库之间转移分支。你可以重命名或是删除你的本地分支，fetch 或是 push 不同的分支名，修改 `git push` 和 `git fetch` 的设置，只对你想要的分支进行操作。

## 相对引用

你还可以通过提交之间的相对关系来引用。`~` 符号让你访问父节点的提交。比如说，下面这个命令显示 `HEAD` 祖父节点的提交：

```
git show HEAD~2
```

但是，面对合并提交（merge commit）的时候，事情就会变得有些复杂。因为合并提交有多个父节点，所以你可以找到多条回溯的路径。对于 3 路合并，第一个父节点是你执行合并时的分支，第二个父节点是你传给 `git merge` 命令的分支。

`~` 符号总是选择合并提交的第一个父节点。如果你想选择其他父节点，你需要用 `^` 符号来指定。比如说，`HEAD` 是一个合并提交，下面这个命令返回 `HEAD` 的第二个父节点：

```
git show HEAD^2
```

你可以使用不止一个 `^` 来查看超过一层的节点。比如，下面的命令显示的是 `HEAD` 的祖父节点，也就是 `HEAD` 第二个父节点的父节点。

```
git show HEAD^2^1
```

为了阐明 `~` 和 `^` 是如何工作的，下面这张图告诉你如何使用相对引用，来指向任意的提交。有的提交可以通过多种方式引用。

![Accessing commits using relative refs](https://wac-cdn.atlassian.com/dam/jcr:cb2ce970-3ef4-4eda-96a9-fe990745f5a7/02.svg)

相对引用在命令中的用法和普通的引用相同。比如，下面所有命令中使用的都是相对引用：

```
# 只列出合并提交的第二个父节点的父节点
git log HEAD^2

# 移除当前分支最新的 3 个提交
git reset HEAD~3

# 交互式rebase当前分支最新的 3 个提交
git rebase -i HEAD~3
```

## 引用日志

引用日志是 Git 的安全网。它记录了你在仓库中做的所有更改，不管你有没有提交。你也可以认为这是你本地更改的完整历史记录。运行 `git reflog` 命令查看引用日志。它应该会打印出像下面这样的信息：

```
400e4b7 HEAD@{0}: checkout: moving from master to HEAD~2
0e25143 HEAD@{1}: commit (amend): 将一些很赞的新特性引入`master`
00f5425 HEAD@{2}: commit (merge): 合并'feature'分支
ad8621a HEAD@{3}: commit: 结束feature分支开发
```

说人话就是：

- 你刚刚切换到 `HEAD~2`
- 你刚刚修改了一个提交信息
- 你刚刚把 feature 分支合并到了 master 分支
- 你刚刚提交了一份缓存

`HEAD{<n>}` 语法允许你引用保存在日志中的提交。这和上一节的 `HEAD~<n>` 引用差不多，不过 `<n>` 指的是引用日志中的对象，而不是提交历史。

你可以用办法回到之前可能已经丢失的状态。比如，你刚刚用 `git reset` 方法粉碎了新的 feature 分支。你的引用日志看上去可能会是这样的：

```
ad8621a HEAD@{0}: reset: moving to HEAD~3
298eb9f HEAD@{1}: commit: 一些提交信息
bbe9012 HEAD@{2}: commit: 继续开发
9cb79fa HEAD@{3}: commit: 开始新特性开发
```

`git reset` 前的三个提交现在都成了悬挂的了，也就是说除了引用日志之外没有办法再引用到它们。现在，假设你意识到了你不应该丢掉你全部的工作。你只需要切换到 `HEAD@{1}` 这个提交就能回到你运行 `git reset` 之前仓库的状态。

```
git checkout HEAD@{1}
```

这会让你处于 `HEAD` 分离的状态。你可以从这里开始，创建新的分支，继续你的工作。

## 总结

你现在对 Git 提交的引用应该已经相当熟悉了。我们知道了分支和标签是如何存在于 `.git` 的子文件夹 refs 中，如何读取打包的引用文件，如何使用 refspec 来进行更高级的 push 和 fetch 操作，如何使用 `~` 和 `^` 符号来遍历分支结构。

我们还了解了引用日志，来引用到其他方式已经不存在的提交。这是一种很好的恢复误删提交的方法。

它的意义在于：在任何开发场景下，你都能找到你需要的特定提交。你很容易就可以把这些技巧用在你一有的 Git 知识中，因为很多常用的命令都接受引用作为参数，包括 `git log`、`git show`、`git checkout`、`git reset`、`git revert`、`git rebase` 等等。

> 这篇文章是[**「git-recipes」**](https://github.com/geeeeeeeeek/git-recipes/)的一部分，点击 [**目录**](https://github.com/geeeeeeeeek/git-recipes/wiki/) 查看所有章节。
>
> 如果你觉得文章对你有帮助，欢迎点击右上角的 **Star** :star2: 或 **Fork** :fork_and_knife:。
>
> 如果你发现了错误，或是想要加入协作，请参阅 [Wiki 协作说明](https://github.com/geeeeeeeeek/git-recipes/issues/1)。
