# 🥡Git 菜单

 *高质量的 Git 中文教程，源于国外社区的优秀文章和个人实践*

🥢🥢🥢🥢🥢🥢

### 第1篇 果壳中的 Git

- **第1章** [什么是 Git](https://github.com/geeeeeeeeek/git-recipes/wiki/1.1-%E6%9E%9C%E5%A3%B3%E4%B8%AD%E7%9A%84-Git)

### 第2篇 从零搭建本地代码仓库

本篇完全面向入门者。我假设你从零开始创建一个项目并且想用 Git 来进行版本控制，我们会讨论如何在你的个人项目中使用 Git，比如如何初始化你的项目，如何管理新的或者已有的文件，如何在远端仓库中储存你的代码。

- **第1章** [快速指南](https://github.com/geeeeeeeeek/git-recipes/wiki/2.1-%E5%BF%AB%E9%80%9F%E6%8C%87%E5%8D%97)
- **第2章** [创建代码仓库](https://github.com/geeeeeeeeek/git-recipes/wiki/2.2-%E5%88%9B%E5%BB%BA%E4%BB%A3%E7%A0%81%E4%BB%93%E5%BA%93)
- **第3章** [保存你的更改](https://github.com/geeeeeeeeek/git-recipes/wiki/2.3-%E4%BF%9D%E5%AD%98%E4%BD%A0%E7%9A%84%E6%9B%B4%E6%94%B9)
- **第4章** [检查仓库状态](https://github.com/geeeeeeeeek/git-recipes/wiki/2.4-%E6%A3%80%E6%9F%A5%E4%BB%93%E5%BA%93%E7%8A%B6%E6%80%81)
- **第5章** [检出之前的提交](https://github.com/geeeeeeeeek/git-recipes/wiki/2.5-%E6%A3%80%E5%87%BA%E4%B9%8B%E5%89%8D%E7%9A%84%E6%8F%90%E4%BA%A4)
- **第6章** [回滚错误的修改](https://github.com/geeeeeeeeek/git-recipes/wiki/2.6-%E5%9B%9E%E6%BB%9A%E9%94%99%E8%AF%AF%E7%9A%84%E4%BF%AE%E6%94%B9)
- **第7章** [重写项目历史](https://github.com/geeeeeeeeek/git-recipes/wiki/2.7-%E9%87%8D%E5%86%99%E9%A1%B9%E7%9B%AE%E5%8E%86%E5%8F%B2)

### 第3篇 远程团队协作和管理

- **第1章** 快速指南
- **第2章** [保持同步](https://github.com/geeeeeeeeek/git-recipes/wiki/3.2-%E4%BF%9D%E6%8C%81%E5%90%8C%E6%AD%A5)
- **第3章** [创建 Pull Request](https://github.com/geeeeeeeeek/git-recipes/wiki/3.3-%E5%88%9B%E5%BB%BA-Pull-Request)
- **第4章** [使用分支](https://github.com/geeeeeeeeek/git-recipes/wiki/3.4-%E4%BD%BF%E7%94%A8%E5%88%86%E6%94%AF)
- **第5章** [常见工作流比较](https://github.com/geeeeeeeeek/git-recipes/wiki/3.5-%E5%B8%B8%E8%A7%81%E5%B7%A5%E4%BD%9C%E6%B5%81%E6%AF%94%E8%BE%83)

### 第4篇 Git 命令详解

- **第1章** [图解 Git 命令](https://github.com/geeeeeeeeek/git-recipes/wiki/4.1-%E5%9B%BE%E8%A7%A3-Git-%E5%91%BD%E4%BB%A4)

  如果你稍微理解 Git 的工作原理，这篇文章能够让你理解的更透彻。

### 第5篇 Git 实用贴士

- **第1章** [代码合并：Merge、Rebase 的选择](https://github.com/geeeeeeeeek/git-recipes/wiki/5.1-%E4%BB%A3%E7%A0%81%E5%90%88%E5%B9%B6%EF%BC%9AMerge%E3%80%81Rebase-%E7%9A%84%E9%80%89%E6%8B%A9)

  `git rebase` 和 `git merge` 都是用来合并分支，只不过方式不太相同。`git rebase` 经常被人认为是一种 Git 巫术，初学者应该避而远之。但如果使用得当，它能省去太多烦恼。在这篇文章中，我们会通过比较找到 Git 工作流中所有可以使用 rebase 的机会。

- **第2章** [代码回滚：Reset、Checkout、Revert 的选择](https://github.com/geeeeeeeeek/git-recipes/wiki/5.2-%E4%BB%A3%E7%A0%81%E5%9B%9E%E6%BB%9A%EF%BC%9AReset%E3%80%81Checkout%E3%80%81Revert-%E7%9A%84%E9%80%89%E6%8B%A9)

  git reset、git checkout 和 git revert 都是用来撤销代码仓库中的某些更改，所以我们经常弄混。在这篇文章中，我们比较最常见的用法，分析在什么场景下该用哪个命令。

- **第3章** [Git log 高级用法](https://github.com/geeeeeeeeek/git-recipes/wiki/5.3-Git-log-%E9%AB%98%E7%BA%A7%E7%94%A8%E6%B3%95)

  任何一个版本控制系统设计的目的都是为了记录你代码的变化——谁贡献了什么，找出 bug 是什么时候引入的，以及撤回一些有问题的更改。`git log` 可以格式化 commit 输出的形式，或过滤输出的 commit 从而找到项目中你需要的任何信息。

- **第4章** [Git 钩子：自定义你的工作流](https://github.com/geeeeeeeeek/git-recipes/wiki/5.4-Git-%E9%92%A9%E5%AD%90%EF%BC%9A%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BD%A0%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%B5%81)

  Git 钩子是在 Git 仓库中特定事件发生时自动运行的脚本。它可以让你自定义 Git 内部的行为，在开始周期中的关键点触发自定义的行为，自动化或者优化你开发工作流中任意部分。

- **第5章** [Git 提交引用和引用日志](https://github.com/geeeeeeeeek/git-recipes/wiki/5.5-Git-%E6%8F%90%E4%BA%A4%E5%BC%95%E7%94%A8%E5%92%8C%E5%BC%95%E7%94%A8%E6%97%A5%E5%BF%97)

  提交是 Git 的精髓所在，你无时不刻不在创建和缓存提交、查看以前的提交，或者用各种 Git 命令在仓库间转移你的提交。在这章中，我们研究提交的各种引用方式，以及涉及到的 Git 命令的工作原理。我们还会学到如何使用 Git 的引用日志查看看似已经删除的提交。

🥢🥢🥢🥢🥢🥢

### 版权说明

- ✍️ [童仲毅 (geeeeeeeeek@github)](https://github.com/geeeeeeeeek)
- 除非另行注明，这个项目中的所有内容采用知识共享-署名（[CC BY 2.5 AU](http://creativecommons.org/licenses/by/2.5/au/deed.zh)）协议共享。
- 不少文章在原基础上翻译或演绎而来，页面上方标注了原作者、原文链接以及原文采用的协议。如有版权疑问，请在 Issue 中提出。
- 欢迎通过 Issue 或者 Pull Request 推荐你认为合适的资料，让这份菜单更充实一些。

🥢🥢🥢🥢🥢🥢

### 为什么要做这份菜单

> 在整理 Git 资料的时候，我发现社区贡献了非常多高质量的博客文章、指南等等。尤其英文的那些资料，除了大家熟知的「Git 图解」，还有好多优秀的文章仍无人翻译。此外，这些资料往往只涉及某些特定的话题，如果能有一份菜单将这些菜谱以特定的方式串起来，那么对于 Git 学习者来说将会是极大的便利。尤其对于我这样热爱查阅社区资料胜过出版物的懒人 :] 随着我的学习节奏还会不断有新的菜谱加入进来，或许不会很频繁，不过也没有确定的终点。
>
> 📅 *写于 2015 年*