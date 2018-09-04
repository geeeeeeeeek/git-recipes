# Git log 高级用法

> BY 童仲毅（[geeeeeeeeek@github](https://github.com/geeeeeeeeek/git-recipes/)）
>
> 这是一篇在[原文（BY atlassian）](https://www.atlassian.com/git/tutorials/git-log)基础上演绎的译文。除非另行注明，页面上所有内容采用知识共享-署名（[CC BY 2.5 AU](http://creativecommons.org/licenses/by/2.5/au/deed.zh)）协议共享。

每一个版本控制系统的出现都是为了让你记录代码的变化。你可以看到项目的历史记录——谁贡献了什么、bug 是什么时候引入的，还可以撤回有问题的更改。但是，首先你得知道如何使用它。这也就是为什么会有 `git log` 这个命令。

到现在为止，你应该已经知道如何用 `git log` 命令来显示最基本的提交信息。但除此之外，你还可以传入各种不同的参数来获得不一样的输出。

`git log` 有两个高级用法：一是自定义提交的输出格式，二是过滤输出哪些提交。这两个用法合二为一，你就可以找到你项目中你需要的任何信息。

## 格式化 Log 输出

首先，这篇文章会展示几种 `git log` 格式化输出的例子。大多数例子只是通过标记向 `git log` 请求或多或少的信息。

如果你不喜欢默认的 `git log` 格式，你可以用 `git config` 的别名功能来给你想要的格式创建一个快捷方式。

### Oneline

`--oneline` 标记把每一个提交压缩到了一行中。它默认只显示提交ID和提交信息的第一行。`git log --oneline` 的输出一般是这样的：

```
0e25143 Merge branch 'feature'
ad8621a Fix a bug in the feature
16b36c6 Add a new feature
23ad9ad Add the initial code base
```

它对于获得项目的总体情况很有帮助。

### Decorate

很多时候，知道每个提交关联的分支或者标签很有用。`--decorate` 标记让 `git log` 显示指向这个提交的所有引用（比如说分支、标签等）。

这可以和另一个配置项一起使用。比如，执行 `git log --oneline --decorate` 会将提交历史格式化成这样：

```
0e25143 (HEAD, master) Merge branch 'feature'
ad8621a (feature) Fix a bug in the feature
16b36c6 Add a new feature
23ad9ad (tag: v0.9) Add the initial code base
```

在这个例子中，你（通过HEAD标记）可以看到最上面那个提交已经被 checkout 了，而且它还是 master 分支的尾端。第二个提交有另一个 feature 分支指向它，以及最后那个提交带有 v0.9 标签。

分支、标签、HEAD 还有提交历史是你 Git 仓库中包含的所有信息。因此，这个命令让你更完整地观察项目结构。

### Diff

`git log` 提供了很多选项来显示两个提交之间的差异。其中最常用的两个是 `--stat` 和 `-p`。

`--stat` 选项显示每次提交的文件增删数量（注意：修改一行记作增加一行且删去一行），当你想要查看提交引入的变化时这会非常有用。比如说，下面这个提交在 hello.py 文件中增加了 67 行，删去了 38 行。

```
commit f2a238924e89ca1d4947662928218a06d39068c3
Author: John <john@example.com>
Date:   Fri Jun 25 17:30:28 2014 -0500

    Add a new feature

 hello.py | 105 ++++++++++++++++++++++++-----------------
 1 file changed, 67 insertion(+), 38 deletions(-)
```

文件名后面+和-的数量是这个提交造成的更改中增删的相对比例。它给你一个直观的感觉，关于这次提交有多少改动。如果你想知道每次提交删改的绝对数量，你可以将 `-p` 选项传入`git log`。这样提交所有的删改都会被输出：

```
commit 16b36c697eb2d24302f89aa22d9170dfe609855b
Author: Mary <mary@example.com>
Date:   Fri Jun 25 17:31:57 2014 -0500

    Fix a bug in the feature

diff --git a/hello.py b/hello.py
index 18ca709..c673b40 100644
--- a/hello.py
+++ b/hello.py
@@ -13,14 +13,14 @@ B
-print("Hello, World!")
+print("Hello, Git!")
```

对于改动很多的提交来说，这个输出会变得又长又大。一般来说，当你输出所有删改的时候，你是想要查找某一具体的改动，这时你就要用到 `pickaxe` 选项。

### Shortlog

`git shortlog` 是一种特殊的 `git log`，它是为创建发布声明设计的。它把每个提交按作者分类，显示提交信息的第一行。这样可以容易地看到谁做了什么。

比如说，两个开发者为项目贡献了 5 个提交，那么 `git shortlog` 输出会是这样的：

```
Mary (2):
      Fix a bug in the feature
      Fix a serious security hole in our framework

John (3):
      Add the initial code base
      Add a new feature
      Merge branch 'feature'
```

默认情况下，`git shortlog` 把输出按作者名字排序，但你可以传入 `-n` 选项来按每个作者提交数量排序。

### Graph

`--graph` 选项绘制一个 ASCII 图像来展示提交历史的分支结构。它经常和 `--oneline` 和 `--decorate` 两个选项一起使用，这样会更容易查看哪个提交属于哪个分支：

```
git log --graph --oneline --decorate
For a simple repository with just 2 branches, this will produce the following:

*   0e25143 (HEAD, master) Merge branch 'feature'
|\  
| * 16b36c6 Fix a bug in the new feature
| * 23ad9ad Start a new feature
* | ad8621a Fix a critical security issue
|/  
* 400e4b7 Fix typos in the documentation
* 160e224 Add the initial code base
```

星号表明这个提交所在的分支，所以上图的意思是 `23ad9ad` 和 `16b36c6` 这两个提交在 topic 分支上，其余的在 master 分支上。

虽然这对简单的项目来说是个很好用的选择，但你可能会更喜欢 gitk 或 SourceTree 这些更强大的可视化工具来分析大型项目。

### 自定义格式

对于其他的 `git log` 格式需求，你都可以使用 `--pretty=format:"<string>"` 选项。它允许你使用像 printf 一样的占位符来输出提交。

比如，下面命令中的 `%cn`、`%h` 和 `%cd` 这三种占位符会被分别替换为作者名字、缩略标识和提交日期。

```
git log --pretty=format:"%cn committed %h on %cd"
This results in the following format for each commit:

John committed 400e4b7 on Fri Jun 24 12:30:04 2014 -0500
John committed 89ab2cf on Thu Jun 23 17:09:42 2014 -0500
Mary committed 180e223 on Wed Jun 22 17:21:19 2014 -0500
John committed f12ca28 on Wed Jun 22 13:50:31 2014 -0500
```

完整的占位符清单可以在文档中找到。

除了让你只看到关注的信息，这个 `--pretty=format:"<string>"` 选项在你想要在另一个命令中使用日志内容是尤为有用的。

## 过滤提交历史

格式化提交输出只是 `git log` 其中的一个用途。另一半是理解如何浏览整个提交历史。接下来的文章会介绍如何用 `git log` 选择项目历史中的特定提交。所有的用法都可以和上面讨论过的格式化选项结合起来。

### 按数量

`git log` 最基础的过滤选项是限制显示的提交数量。当你只对最近几次提交感兴趣时，它可以节省你一页一页查看的时间。

你可以在后面加上 `-<n>` 选项。比如说，下面这个命令会显示最新的 3 次提交：

```
git log -3
```

### 按日期

如果你想要查看某一特定时间段内的提交，你可以使用 `--after` 或 `--before` 标记来按日期筛选。它们都接受好几种日期格式作为参数。比如说，下面的命令会显示 2014 年 7 月 1 日后（含）的提交：

```
git log --after="2014-7-1"
```

你也可以传入相对的日期，比如一周前（`"1 week ago"`）或者昨天（`"yesterday"`）：

```
get log --after="yesterday"
```

你可以同时提供`--before` 和 `--after` 来检索两个日期之间的提交。比如，为了显示 2014 年 7 月 1 日到 2014 年 7 月 4 日之间的提交，你可以这么写：

```
git log --after="2014-7-1" --before="2014-7-4"
```

注意 `--since`、`--until` 标记和 `--after`、`--before` 标记分别是等价的。

### 按作者

当你只想看某一特定作者的提交的时候，你可以使用 `--author` 标记。它接受正则表达式，返回所有作者名字满足这个规则的提交。如果你知道那个作者的确切名字你可以直接传入文本字符串：

```
git log --author="John"
```

它会显示所有作者叫 John 的提交。作者名不一定是全匹配，只要包含那个子串就会匹配。

你也可以用正则表达式来创建更复杂的检索。比如，下面这个命令检索名叫 Mary 或 John 的作者的提交。

```
git log --author="John\|Mary"
```

注意作者的邮箱地址也算作是作者的名字，所以你也可以用这个选项来按邮箱检索。

如果你的工作流区分提交者和作者，`--committer` 也能以相同的方式使用。

### 按提交信息

按提交信息来过滤提交，你可以使用 `--grep` 标记。它和上面的 `--author` 标记差不多，只不过它搜索的是提交信息而不是作者。

比如说，你的团队规范要求在提交信息中包括相关的issue编号，你可以用下面这个命令来显示这个 issue 相关的所有提交：

```
git log --grep="JRA-224:"
```

你也可以传入 `-i` 参数来忽略大小写匹配。

### 按文件

很多时候，你只对某个特定文件的更改感兴趣。为了显示某个特定文件的历史，你只需要传入文件路径。比如说，下面这个命令返回所有和 `foo.py` 和 `bar.py` 文件相关的提交：

```
git log -- foo.py bar.py
```

`--` 告诉 `git log` 接下来的参数是文件路径而不是分支名。如果分支名和文件名不可能冲突，你可以省略 `--`。

### 按内容

我们还可以根据源代码中某一行的增加和删除来搜索提交。这被称为 pickaxe，它接受形如 `-S"<string>"` 的参数。比如说，当你想要知道 `Hello, World!` 字符串是什么时候加到项目中哪个文件中去的，你可以使用下面这个命令：

```
git log -S "Hello, World!"
```

如果你想用正则表达式而不是字符串来搜索，你可以使用 `-G"<regex>"` 标记。

这是一个非常强大的调试工具，它能让你定位到所有影响代码中特定一行的提交。它甚至可以让你看到某一行是什么时候复制或者移动到另一个文件中去的。

### 按范围

你可以传入范围来筛选提交。这个范围由下面这样的格式指定，其中 `<since>` 和 `<until>` 是提交的引用：

```
git log <since>..<until>
```

这个命令在你使用分支引用作为参数时特别有用。这是显示两个分支之间区别最简单的方式。看看下面这个命令：

```
git log master..feature
```

其中的 `master..feature` 范围包含了在 feature 分支而不在 master 分支中所有的提交。换句话说，这个命令可以看出从 master 分支 fork 到 feature 分支后发生了哪些变化。它可以这样可视化：

![enter image description here](https://wac-cdn.atlassian.com/dam/jcr:b443a307-2df4-4080-948b-f5b9a1f8fd40/01.svg)

注意如果你更改范围的前后顺序（feature..master），你会获取到 master 分支而非 feature 分支上的所有提交。如果 `git log` 输出了全部两个分支的提交，这说明你的提交历史已经分叉了。

### 过滤合并提交

`git log` 输出时默认包括合并提交。但是，如果你的团队采用强制合并策略（意思是 merge 你修改的上游分支而不是将你的分支 rebase 到上游分支），你的项目历史中会有很多外来的提交。

你可以通过 `--no-merges` 标记来排除这些提交：

```
git log --no-merges
```

另一方面，如果你只对合并提交感兴趣，你可以使用 `--merges` 标记：

```
git log --merges
```

它会返回所有包含两个父节点的提交。

## 总结

你现在应该对使用 `git log` 来格式化输出和选择你要显示的提交的用法比较熟悉了。它允许你查看你项目历史中任何需要的内容。

这些技巧是你 Git 工具箱中重要的部分，不过注意 `git log` 往往和其他 Git 命令连着使用。当你找到了你要的提交，你把它传给 `git checkout`、`git revert` 或是其他控制提交历史的工具。所以，请继续坚持 Git 高级用法的学习。

> 这篇文章是[**「git-recipes」**](https://github.com/geeeeeeeeek/git-recipes/)的一部分，点击 [**目录**](https://github.com/geeeeeeeeek/git-recipes/wiki/) 查看所有章节。
>
> 如果你觉得文章对你有帮助，欢迎点击右上角的 **Star** :star2: 或 **Fork** :fork_and_knife:。
>
> 如果你发现了错误，或是想要加入协作，请参阅 [Wiki 协作说明](https://github.com/geeeeeeeeek/git-recipes/issues/1)。
