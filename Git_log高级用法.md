Git log高级用法
===

> BY 童仲毅(geeeeeeeeek@github)
> 
> 这是一篇在[原文](https://www.atlassian.com/git/tutorials/git-log)基础上演绎的译文。除非另行注明，页面上所有内容采用知识共享-署名([CC BY 2.5 AU](http://creativecommons.org/licenses/by/2.5/au/deed.zh))协议共享。

每一个版本控制系统的目的都是为了记录你代码的变化。你可以看到你项目的历史记录——谁贡献了什么，找出bug是什么时候引入的，还有撤回一些有问题的更改。但是，掌控这些的前提是你知道如何来使用它。这就是为什么会有`git log` 这个命令。

到现在为止，你应该已经知道如何用`git log` 命令来显示最基本的commit的信息。但是，你还可以传入各种不同的参数来获得不一样的输出。

`git log` 有两个高级用法：一是格式化commit输出的方式，二是过滤哪些commit要输出。这两个用法合二为一，你可以找到你项目中你需要的任何信息。

格式化Log输出
---
首先，这篇文章会展示几种`git log` 格式化输出的例子。它们大多数只是通过标记来向`git log` 请求或多或少的信息。

如果你不喜欢默认的`git log` 格式，你可以用`git config` 的别名功能来为任何一种格式创建一个快捷方式。

### Oneline

`--oneline` 标记把每一个commit压缩到了一行中。它默认只显示commit ID和commit信息的第一行。一般来说，`git log --oneline` 的输出是这样的：

```
0e25143 Merge branch 'feature'
ad8621a Fix a bug in the feature
16b36c6 Add a new feature
23ad9ad Add the initial code base
```
这对于获得你项目的大致情况是很有帮助的。

### Decorate

很多时候，知道每个commit关联的分支或者标签是很有用的。`--decorate` 标记让`git log` 显示指向这个commit的所有引用（比如说分支、标签等等）。

这可以和另一个配置项一起使用。比如，执行`git log --oneline --decorate` 会将commit历史格式化成这样：

```
0e25143 (HEAD, master) Merge branch 'feature'
ad8621a (feature) Fix a bug in the feature
16b36c6 Add a new feature
23ad9ad (tag: v0.9) Add the initial code base
```

在这个例子中，你可以看到最上面那个commit已经被checkout了（通过HEAD标记），而且它还是master分支的尾端。第二个commit有另一个feature分支指向它，以及最后那个commit带有v0.9标签。

分支、标签、HEAD还有commit历史是你Git仓库中包含的所有信息。因此，这个命令让你更完整地观察项目结构。

### Diff

`git log` 提供了很多选项来显示两个commit之间的差异。其中最常用的两个是`--stat` 和`-p`。

`--stat` 选项显示每次commit对文件的增删数量（注意修改一行记作增加一行且删去一行）当你想要查看commit产生的变化时这会非常有用。比如说，下面这个commit在hello.py文件中增加了67行，删去了38行。

```
commit f2a238924e89ca1d4947662928218a06d39068c3
Author: John <john@example.com>
Date:   Fri Jun 25 17:30:28 2014 -0500

    Add a new feature

 hello.py | 105 ++++++++++++++++++++++++-----------------
 1 file changed, 67 insertion(+), 38 deletions(-)
```
 
文件名后面+和-号数量是这个commit造成的更改中增删的相对比例。它给你一个直观的感觉，关于每次commit做了什么。如果你想知道每次commit删改的绝对数量，你可以将`-p` 选项传入`git log` 。这样commit所有的删改都会被输出：

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

对于改动很多的commit来说，这个输出会变得又长又大。一般来说，当你输出所有删改的时候，你应该是想要查找某一具体的改动。这种情况你会想用`pickaxe` 选项。

### Shortlog
`git shortlog` 是一种特别的`git log` ，它是为创建发布声明设计的。它把每个commit按作者分类，显示commit信息的第一行。这样可以容易地看到谁做了什么。

比如说，两个开发者为项目贡献了5个commit，那么`git shortlog` 输出会是这样的：

```
Mary (2):
      Fix a bug in the feature
      Fix a serious security hole in our framework

John (3):
      Add the initial code base
      Add a new feature
      Merge branch 'feature'
```
默认情况下，`git shortlog` 把输出按作者名字排序，但你可以传入`-n` 选项来按每个作者commit数量排序。

### Graph
`--graph` 选项绘制一个ASCII图像来展示commit历史的分支结构。它经常和 `--oneline` 和 `--decorate` 两个命令一起使用，这样会更容易查看哪个commit属于哪个分支：

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

星号表明这个commit所在的分支，所以上面这个图的意思是`23ad9ad` 和`16b36c6` 这两个commit在topic分之上，其余的在master分支上。

虽然这对简单的项目来说是个很好用的选项，但你可能会更喜欢gitk或SourceTree这些更强大的可视化工具来分析庞大的项目。

### 自定义格式

对于其他的`git log` 格式需求，你都可以使用`--pretty=format:"<string>"` 选项。它允许你使用像printf一样的占位符来输出commit。

比如，下面命令中的`%cn`、`%h` 和`%cd` 这三种占位符会被分别替换为作者名字、缩略标识和提交日期。

```
git log --pretty=format:"%cn committed %h on %cd"
This results in the following format for each commit:

John committed 400e4b7 on Fri Jun 24 12:30:04 2014 -0500
John committed 89ab2cf on Thu Jun 23 17:09:42 2014 -0500
Mary committed 180e223 on Wed Jun 22 17:21:19 2014 -0500
John committed f12ca28 on Wed Jun 22 13:50:31 2014 -0500
```

完整的占位符清单可以在文档中找到。

除了让你只看到关注的信息，这个`--pretty=format:"<string>"` 选项在你想要在另一个命令中使用日志内容是尤为有用的。

过滤提交历史
---
格式化commit输出只是`git log` 其中一个用途。另一半是理解如何浏览整个提交历史。接下来的文章会介绍如果用`git log` 选择项目历史中的特定的commit。所有的用法都可以和上面讨论过的格式化选项结合起来。

### 按数量
`git log` 最基础的过滤选项是限制显示的commit数量。当你只对最近几次commit感兴趣时，它会节省你一页一页查看的时间。

你可以在后面加上`-<n>`选项。比如说，下面这个命令会显示最新的3次commit：
```
git log -3
```

### 按日期
如果你想要查看某一特定时间段内的commit，你可以使用`--after` 或 `--before` 标记来按日期筛选。它们都接受好几种日期格式作为参数。比如说，下面的命令会显示2014年7月1日后（含）的commit：

```
git log --after="2014-7-1"
```

你也可以传入相对的日期，比如一周前（"`1 week ago`"）或者昨天（"`yesterday`"）：

```
get log --after="yesterday"
```

你可以同时提供`--before` 和 `--after` 来检索两个日期之间的commit。比如，为了显示2014年7月1日到2014年7月4日之间的commit，你可以这么写：

```
git log --after="2014-7-1" --before="2014-7-4"
```

注意`--since` 、`--until` 标记和`--after` 、`--before` 标记是分别相同的。

### 按作者
当你只想看某一特定作者的commit的时候，你可以使用`--author` 标记。它接受正则表达式，返回所有作者名字满足这个规则的commit。如果你知道那个作者的确切名字你可以直接传入文本字符串：

```
git log --author="John"
```

它会显示所有作者叫John的commit。作者名不一定是全匹配，只要包含那个子串就会匹配。

你也可以用正则表达式来创建更复杂的检索。比如，下面这个命令检索名叫Mary或John的作者的commit。

```
git log --author="John\|Mary"
```

注意作者的邮箱地址也算作是作者的名字，所以你也可以用这个选项来按邮箱检索。

如果你的工作流区分提交者和作者，`--committer` 也能以相同的方式使用。

### 按提交信息

按提交信息来过滤commit，你可以使用`--grep` 标记。它和上面的`--author` 标记差不多，只不过它搜索的是提交信息而不是作者。

比如说，你的团队规范要求在提交信息中包括相关的issue编号，你可以用下面这个命令来显示这个issue相关的所有commit：

```
git log --grep="JRA-224:"
```

你也可以传入`-i` 参数来忽略大小写匹配。

### 按文件

很多时候，你只对某个特定文件的更改感兴趣。为了显示某个特定文件的历史，你只需要传入文件路径。比如说，下面这个命令返回所有和`foo.py` 和`bar.py` 文件相关的commit：

```
git log -- foo.py bar.py
```

`--` 参数告诉`git log` 接下来的参数是文件路径而不是分支名。如果分支名和文件名不可能冲突，你可以省略`--`。

### 按内容

我们还可以根据源代码中某一行的增加和删除来搜索commit。这被称为pickaxe，它接受形如`-S"<string>"` 的参数。比如说，当你想要知道`Hello, World!` 字符串是什么时候加到项目中哪个文件中去的，你可以使用下面这个命令：

```
git log -S "Hello, World!"
```

如果你想用正则表达式而不是字符串来搜索，你可以使用`-G"<regex>"` 标记。

这是一个非常强大的调试工具，因为它让你定位所有影响代码中特定一行的commit。它甚至可以让你看到某一行是什么时候复制或者移动到另一个文件中去的。

### 按范围

你可以传入commit的返回来筛选在那个范围内的commit。这个范围由下面这样的格式指定，其中< since > 和< until >是commit的引用：

```
git log <since>..<until>
```

这个命令在你使用分支引用作为参数时特别有用。这是一个显示两个分支之间区别最简单的方式。看看下面这个命令：

```
git log master..feature
```

其中的master..feature范围包含了在feature分支而不在feature分支中所有的commit。换句话说，这个命令可以看出从master分支Fork后feature分支发生了哪些变化。这可以像下面这样可视化：

![enter image description here](https://www.atlassian.com/git/images/tutorials/advanced/git-log/01.svg)

注意如果你更改范围的前后顺序(feature..master)，你会获取到master分支而非feature分支上的所有commit。如果`git log` 输出了全部两个分支的commit，这说明你的提交历史已经分叉了。

### 过滤出merge commit

`git log` 默认会在输出中包括merge commit。但是，如果你的团队采用强制合并策略（意思是merge上游修改你的分支而不是将你的分支rebase到上游分支），你的项目历史中会有很多外来的commit。

你可以通过`--no-merges` 标记来排除这些commit：

```
git log --no-merges
```

另一方面，如果你只对merge commit感兴趣，你可以使用`--merges` 标记：

```
git log --merges
```

它会返回所有有两个父节点的commit。

总结
---

你现在应该对使用`git log` 来格式化输出和选择你要显示的commit的用法比较熟悉了。它允许你查看你项目历史中任何需要的内容。

这些技巧是你Git工具箱中重要的部分，不过注意`git log` 往往和其他Git命令连着使用。当你找到了你要的commit，你把它传给`git checkout` 、`git revert`  或是其他控制你提交历史的工具。所以，请不要停止继续学习Git的高级用法。
