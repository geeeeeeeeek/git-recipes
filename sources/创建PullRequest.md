# 创建Pull Request

> BY 童仲毅（[geeeeeeeeek@github](https://github.com/geeeeeeeeek/git-recipes/)）
>
> 这是一篇在[原文（BY atlassian）](https://www.atlassian.com/git/tutorials/making-a-pull-request)基础上演绎的译文。除非另行注明，页面上所有内容采用知识共享-署名（[CC BY 2.5 AU](http://creativecommons.org/licenses/by/2.5/au/deed.zh)）协议共享。
>
> 原文以 Bitbucket 为例，考虑到[git-recipes](https://github.com/geeeeeeeeek/git-recipes/)主要面向 GitHub 用户，因此例子替换成了 GitHub。Pull Request 在 GitLab 等平台上也有，用法和本教程基本一致。

Pull Request 是开发者使用 GitHub 进行协作的利器。这个功能为用户提供了友好的页面，让提议的更改在并入官方项目之前，可以得到充分的讨论。

![qq20160127-0](https://cloud.githubusercontent.com/assets/7262715/12608331/8ece897e-c516-11e5-91c0-f7a44434478f.png)

最简单地来说，Pull Request 是一种机制，让开发者告诉项目成员一个功能已经完成。一旦 feature 分支开发完毕，开发者使用 GitHub 账号提交一个 Pull Request。它告诉所有参与者，他们需要审查代码，并将代码并入 `master` 分支。

但是，Pull Request 不只是一个通知，还是一个专注于某个提议功能的讨论版。 如果更改导致了任何问题，团队成员可以在 Pull Request 下发布反馈，甚至推送后续提交来修改这个 Pull Request。所有的活动都在这个 Pull Request里之间追踪。

![Git Workflows: Activity inside a Pull Request](https://www.atlassian.com/git/images/tutorials/collaborating/making-a-pull-request/02.svg)

和其他协作模型相比，这种共享提交的解决方案形成了更加线性的工作流。SVN 和 Git 都能通过一个简单的脚本发送通知邮件；但是，如果要讨论更改，开发者不得不在邮件里回复。这会变得愈发杂乱无章，尤其是后续提交出现时。Pull Request 将所有这些功能放入了一个友好的网页，在每个 GitHub 仓库上方都能找到。

### 剖析一个 Pull Request

当你提交一个 Pull Request 的时候，你做的事情是 *请求（request）* 另一个开发者（比如项目维护者）来 *拉取（pull）* 你仓库中的一个分支到他们的仓库。也就是说你需要提供 4 个信息来完成一个 Pull Request：源仓库、源分支、目标仓库、目标分支。

![Git Workflows: Pull Requests](https://www.atlassian.com/git/images/tutorials/collaborating/making-a-pull-request/03.svg)

GitHub 会机智地帮你将一些值设为默认值。但是，取决于你的协作工作流，你的团队可能需要设置不同的值。上图显示了一个请求从 feature 分支合并到官方  master分支的一个 Pull Request，但除此之外还有好多种使用 Pull Request 的方式。

## Pull Request是如何工作的

Pull Request 可以和 feature 分支工作流、GitFlow 工作流或者 Fork 工作流一起使用。但 Pull Request 需要两个不同的分支或是两个不同的仓库，因此它们不能和中心化的工作流一起使用。在不同的工作流中使用 Pull Request 有些不同，但大致的流程如下：

1. 开发者在他们的本地仓库中为某个功能创建一个专门的分支。
2. 开发者将分支推送到公共的 GitHub 仓库。
3. 开发者用 GitHub 发起一个 Pull Request。
4. 其余的团队成员审查代码，讨论并且做出修改。
5. 项目维护者将这个功能并入官方的仓库，然后关闭这个 Pull Request。

下面的章节讨论 Pull Request 在不同的协作工作流中有哪些不同。

### Feature 分支工作流中的 Pull Request

Feature 分支工作流使用共享的 GitHub 仓库来管理协作，开发者在单独的 feature 分支中添加功能。开发者在将代码并入主代码库之前，应该发起一个 Pull Request 来启动这个功能的讨论，而不是直接将它们合并到 `master`。

![Pull Request: Feature Branch workflow](https://www.atlassian.com/git/images/tutorials/collaborating/making-a-pull-request/04.svg)

在 Feature 分支工作流中只有一个公共的仓库，因此 Pull Request 的目标和源仓库永远是同一个。一般来说，开发者会将他们的  feature分支作为源分支，`master` 作为目标分支。

在收到 Pull Request 之后，项目维护者将会做出决定。如果这个功能可以立即发布，他们只需要将代码合并进 `master`，然后关闭 Pull Request 即可。但是，如果提议的更改有一些问题，他们可以在 Pull Request 下发布反馈。后续提交将会显示在相关评论的下方。

你也可以发布一个未完成功能的 Pull Request。例如，如果开发者在实现一个特殊的需求时遇到了问题，同样可以发布一个包含工作进展的 Pull Request。其他开发者可以在这个 Pull Request 后面提供建议，甚至自己发布后续的提交来解决这个问题。

### GitFlow 工作流中的 Pull Request

GitFlow 工作流和 Feature 分支工作流类似，但定义了围绕项目发布的一个严格的分支模型。在 GitFlow 工作流之上添加 Pull Request 使得开发者方便地讨论发布分支或是所在的维护分支。

![Pull Requests: Gitflow Workflow](https://www.atlassian.com/git/images/tutorials/collaborating/making-a-pull-request/05.svg)

![Pull Requests: Gitflow Workflow 2](https://www.atlassian.com/git/images/tutorials/collaborating/making-a-pull-request/06.svg)

在 GitFlow 工作流中的 Pull Request 和上一节中的完全一致：开发者只需在功能、发布或是快速修复分支需要审查时发布一个 Pull Request，GitHub 会通知到其余的团队成员。

功能一般都会合并到 `develop` 分支，而发布和快速修复分支会被同时合并到 `develop` 和 `master` 当中。 Pull Request 可以用来妥善管理这些合并。

### Fork 工作流中的 Pull Request

在 Fork 工作流中，开发者将一个完成的功能推送到 *他们自己的* 仓库，而不是公共的仓库。在这之后，他们发布一个 Pull Request，告诉项目维护者代码需要审查了。

在这个工作流中，Pull Request 的通知作用显得非常有用，因为项目维护者无法获知其他开发者什么时候向他们自己的 GitHub 仓库中添加了提交。

![Pull Requests: Forking workflow](https://www.atlassian.com/git/images/tutorials/collaborating/making-a-pull-request/07.svg)

因为每个开发者都有他们自己的公共仓库，Pull Request 的源仓库和目标仓库不是同一个。源仓库是开发者的公开仓库，源分支是包含提议更改的那一个。如果开发者想要将功能合并到主代码库，目标仓库便是官方的项目仓库，目标分支为 `master`。

Pull Request 还可以用来和官方项目之外的开发者进行协作。比如说，一个开发者正在和同事一起开发一个功能，他们可以向 *同事的* GitHub 仓库发起一个 Pull Request，而不是官方仓库。他们将 feature 分支同时作为源分支和目标分支。

![Pull Requests: Forking workflow](https://www.atlassian.com/git/images/tutorials/collaborating/making-a-pull-request/08.svg)

两个开发者可以在 Pull Request 中讨论和开发分支。当功能完成时，其中一位可以发起另一个 Pull Request，请求将功能合并到官方的 master 分支中去。这种灵活性使得 Pull Request 成为了 Fork 工作流中尤为强大的协作工具。

## 例子

下面的例子演示了如何将 Pull Request 用在 Fork 工作流中。小团队中的开发和向一个开源项目贡献代码都可以这样做。

在这个例子中，Mary 是一位开发者，John 是项目的维护者。他们都有自己公开的 GitHub 仓库，John 的仓库之一便是下面的官方项目。

### Mary fork了官方项目

![Pull Requests: Fork the project](https://www.atlassian.com/git/images/tutorials/collaborating/making-a-pull-request/09.svg)

为了参与这个项目，Mary 首先要做的是 fork 属于 John 的 GitHub 仓库。她需要注册登录 GitHub，找到 John 的仓库，点击 Fork 按钮。

> 下图显示的是 geeeeeeeeek 的 WeChatLuckyMoney 仓库。

![qq20160127-1](https://cloud.githubusercontent.com/assets/7262715/12614812/232fb7f2-c53d-11e5-80cb-68d2af29d6e9.png)

选好 fork 的目标位置之后，她在服务端就有了一个项目的副本。

### Mary 克隆了她的 GitHub 仓库

![Pull Request: Clone the Bitbucket repo](https://www.atlassian.com/git/images/tutorials/collaborating/making-a-pull-request/11.svg)

接下来，Mary 需要将她刚刚 fork 的 GitHub 仓库克隆下来。她在本地会有一份项目的副本。她需要运行下面这个命令：

```
git clone https://github.com/user/repo.git
```

请记住，`git clone` 自动创建了一个名为 `origin` 的远端连接，指向 Mary 所 fork 的仓库。

### Mary 开发了一个新功能

![Pull Requests: develop a new feature](https://www.atlassian.com/git/images/tutorials/collaborating/making-a-pull-request/12.svg)

在她写任何代码之前，Mary 需要为这个功能创建一个新的分支。这个分支将是她随后发起 Pull Request 时要用到的源分支。

```
git checkout -b some-feature
# 编辑一些代码
git commit -a -m "新功能的一些草稿"
```

为了完成这个新功能，Mary 想创建多少个提交都可以。如果 feature 分支的历史有些乱，她可以使用交互式的 rebase 来移除或者拼接不必要的提交。对于大项目来说，清理 feature 的项目历史使得项目维护者更容易看清楚 Pull Request 的所处的进展。

### Mary将feature分支推送到了她的GitHub仓库

![Pull Requests: Push feature to Bitbucket repository](https://www.atlassian.com/git/images/tutorials/collaborating/making-a-pull-request/13.svg)

在功能完成后，Mary 使用简单的 `git push` 将 feature 分支推送到了她自己的 GitHub 仓库上（不是官方的仓库）：

```
git push origin some-branch
```

这样她的更改就可以被项目维护者看到了（或者任何有权限的协作者）。

### Mary创建了一个Pull Request

![Pull Request: Create Pull Request](https://www.atlassian.com/git/images/tutorials/collaborating/making-a-pull-request/14.svg)

GitHub 上已经有了她的 feature 分支之后，Mary 可以找到被她 fork 的仓库，点击项目简介下的 *New Pull Request* 按钮，用她的 GitHub 账号创建一个 Pull Request。Mary 的仓库会被默认设置为源仓库（head fork），询问她指定源分支（compare）、目标仓库（base fork）和目标分支（base）。

Mary 想要将她的功能并入主代码库，所以源分支就是她的 feature 分支，目标仓库就是 John 的公开仓库，目标分支为 `master`。她还需要提供一个 Pull Request 的标题和简介。

> 下图展示的是将0492wzl/WeChatLuckyMoney(源仓库)的stable(源分支)合并到geeeeeeeeek/WeChatLuckyMoney(目标仓库)的stable(目标分支)。

![qq20160127-2](https://cloud.githubusercontent.com/assets/7262715/12615530/3088775a-c541-11e5-914e-d8232037e741.png)

在她创建了 Pull Request 之后，GitHub 会给 John 发送一条通知。

### John审查了这个Pull Request

![qq20160127-3](https://cloud.githubusercontent.com/assets/7262715/12615618/b1373fd0-c541-11e5-9b91-47d40f8083ba.png)

John 可以在他自己的 GitHub 仓库下的 *Pull Request* 选项卡中看到所有的 Pull Request。点击 Mary 的 Pull Request 会显示这个 Pull Request 的简介、feature 分支的提交历史，以及包含的更改。

如果他认为 feature 分支已经可以合并了，他只需点击 *Merge Pull Request* 按钮来通过这个 Pull Request，将 Mary 的  feature分支并入他的 `master` 分支。

但是，在这里例子中，假设 John 发现了 Mary 代码中的一个小 bug，需要她在合并前修复。他可以评论整个 Pull Request，也可以评论 feature 分支中某个特定的提交。

![qq20160127-4](https://cloud.githubusercontent.com/assets/7262715/12615732/67c8c872-c542-11e5-9734-71751b83f63c.png)

### Mary 添加了一个后续提交

如果 Mary 对这个反馈感到困惑，她可以在 Pull Request 后回复，把这里当做是她的功能的讨论版。

为了修复错误，Mary 在她的 feature 分支后面添加了另一个提交，并将它推送到了她的 GitHub 仓库，就像她之前做的一样。这个提交被自动添加到原来的 Pull Request 后面，John 可以在他的评论下方再次审查这些修改。

### John 接受了 Pull Request

最后，John 接受了这些修改，将 feature 分支并入了 master 分支，关闭了这个 Pull Request。功能现在已经整合到了项目中，其他在 master 分支上工作的开发者可以使用标准的 `git pull` 命令将这些修改拉取到自己的本地仓库。

> 如果你希望实践一下，可以按照上面的流程向这个项目发起一个 Pull Request，修改任何你发现的错误 :smile:

## 接下来怎么做？

你现在应该已经掌握了如何将你的 Pull Request 整合到你的工作流。记住，Pull Request 不是替代任何 Git 工作流的万金油，而是一种让队员间协作锦上添花的工具。

> 这篇文章是[**「git-recipes」**](https://github.com/geeeeeeeeek/git-recipes/)的一部分，点击[**目录**](https://github.com/geeeeeeeeek/git-recipes/wiki/)查看所有章节。
>
> 如果你觉得文章对你有帮助，欢迎点击右上角的 ***Star***:star2: 或 ***Fork***:fork_and_knife:。
>
> 如果你发现了错误，或是想要加入协作，请参阅[Wiki协作说明](https://github.com/geeeeeeeeek/git-recipes/issues/1)。
