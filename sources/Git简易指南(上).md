# Git 简易指南(上)

> BY 童仲毅（[geeeeeeeeek@github](https://github.com/geeeeeeeeek/git-recipes/)）
>
> 除非另行注明，页面上所有内容采用知识共享-署名（[CC BY 2.5 AU](http://creativecommons.org/licenses/by/2.5/au/deed.zh)）协议共享。[git-guide](https://github.com/rogerdudler/git-guide/) 项目对本文亦有贡献。

这节是完全面向入门者的，我假设你从零开始创建一个项目并且想用 Git 来进行版本控制，因此本文会避开分支这些相对复杂的概念。

在这节中，我会介绍如何在你的个人项目中使用 Git，我们会讨论 Git 最基本的操作——如何初始化你的项目，如何管理新的或者已有的文件，如何在远端仓库中储存你的代码。

## 安装 Git

- Mac 用户：Xcode Command Line Tools 自带 Git（`xcode-select --install`）

- Linux 用户：`sudo apt-get install git`

- Windows 用户：下载 [Git SCM](git-for-windows.github.io)

  ```
  - 对于 Windows 用户，安装后如果希望在全局的 cmd 中使用 Git，需要把 git.exe 加入 PATH 环境变量中，或在 Git Bash 中使用 Git。
  ```

## 检出仓库

执行如下命令以创建一个本地仓库的克隆版本：

`git clone /path/to/repository`

如果是远端服务器上的仓库，你的命令会是这个样子：

`git clone username@host:/path/to/repository` （通过 SSH）

或者：

`git clone https:/path/to/repository.git` （通过 https）

比如说 `git clone https://github.com/geeeeeeeeek/git-recipes.git` 可以将 git 教程 clone 到你指定的目录。

## 创建新仓库

创建新文件夹，打开，然后执行 `git init` 以创建新的 git 仓库。

> 下面每一步中，你都可以通过 `git status` 来查看你的git仓库状态。

## 工作流

你的本地仓库由 Git 维护的三棵「树」组成。第一个是你的 `工作目录`，它持有实际文件；第二个是 `缓存区（Index）`，它像个缓存区域，临时保存你的改动；最后是 `HEAD`，指向你最近一次提交后的结果。

![enter image description here](http://www.bootcss.com/p/git-guide/img/trees.png)

> 事实上，第三个阶段是 commit history 的图。HEAD 一般是指向最新一次 commit 的引用。现在暂时不必究其细节。

## 添加与提交

你可以计划改动（把它们添加到缓存区），使用如下命令：

```
git add < filename >
git add *
```

这是 Git 基本工作流程的第一步。使用如下命令以实际提交改动：

```
git commit -m "代码提交信息"
```

现在，你的改动已经提交到了 HEAD，但是还没到你的远端仓库。

> 在开发时，良好的习惯是根据工作进度及时 commit，并务必注意附上有意义的 commit message。创建完项目目录后，第一次提交的 commit message 一般为「Initial commit」。

## 推送改动

你的改动现在已经在本地仓库的 HEAD 中了。执行如下命令以将这些改动提交到远端仓库：

```
git push origin master
```

可以把 master 换成你想要推送的任何分支。

如果你还没有克隆现有仓库，并欲将你的仓库连接到某个远程服务器，你可以使用如下命令添加：

```
git remote add origin <server>
```

如此你就能够将你的改动推送到所添加的服务器上去了。

> - 这里 origin 是 &lt;server&gt; 的别名，取什么名字都可以，你也可以在 push 时将 &lt;jserver&gt; 替换为 origin。但为了以后 push 方便，我们第一次一般都会先 remote add。
> - 如果你还没有 Git 仓库，可以在 Github 等代码托管平台上创建一个空（不要自动生成 README.md）的仓库，然后将代码 push 到远端仓库。

##### 至此，你应该可以顺利地提交你的项目了。在下一节中，我们将涉及更多的命令，来完成更有用的操作。比如从远端的仓库拉取更新并且合并到你的本地，如何通过分支多人协作，如何处理不同分支的冲突等等。

> 这篇文章是[**「git-recipes」**](https://github.com/geeeeeeeeek/git-recipes/)的一部分，点击 [**目录**](https://github.com/geeeeeeeeek/git-recipes/wiki/) 查看所有章节。
>
> 如果你觉得文章对你有帮助，欢迎点击右上角的 **Star** :star2: 或 **Fork** :fork_and_knife:。
>
> 如果你发现了错误，或是想要加入协作，请参阅 [Wiki 协作说明](https://github.com/geeeeeeeeek/git-recipes/issues/1)。
