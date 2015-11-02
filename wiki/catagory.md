**第1篇 Git**

  
**第2篇 从零搭建本地代码仓库**

 - **第1章** [快速指南](https://github.com/geeeeeeeeek/git-recipes/blob/master/Git%E7%AE%80%E6%98%93%E6%8C%87%E5%8D%97(%E4%B8%8A).md)

	这节完全面向入门者。我假设你从零开始创建一个项目并且想用Git来进行版本控制，我们会讨论如何在你的个人项目中使用Git，比如如何初始化你的项目，如何管理新的或者已有的文件，如何在远端仓库中储存你的代码。

 - **第2章** 创建代码仓库

 - **第3章** 保存你的更改

 - **第4章** 查看仓库状态

 - **第5章** 查看以前的提交

 - **第6章** 回滚错误的更改

 - **第7章** 重写项目历史

**第3章 远程团队协作和管理**

 - **第1章** 快速指南 
 
 - **第2章** 同步代码

 - **第3章** 创建Pull Request

 - **第4章** 使用分支

 - **第5章** 几种工作流
  
**第4篇 Git命令详解**

 - 第1章 [图解Git命令](https://github.com/geeeeeeeeek/git-recipes/blob/master/Git%E5%9B%BE%E8%A7%A3.md)

	如果你稍微理解git的工作原理，这篇文章能够让你理解的更透彻。
  
**第5篇 Git实用贴士**

 - **第1章** [代码合并：Merge、Rebase的选择](https://github.com/geeeeeeeeek/git-recipes/blob/master/%E4%BB%A3%E7%A0%81%E5%90%88%E5%B9%B6:Merge%E8%BF%98%E6%98%AFRebase.md)

	`git rebase` 和`git merge` 都是用来合并分支，只不过方式不太相同。`git rebase` 经常被人认为是一种Git巫术，初学者应该避而远之。但如果使用得当，它能省去太多烦恼。在这篇文章中，我们会通过比较找到Git工作流中所有可以使用rebase的机会。
 
 - **第2章** [代码回滚：Reset、Checkout、Revert的选择](https://github.com/geeeeeeeeek/git-recipes/blob/master/%E5%9B%9E%E6%BB%9A%E5%91%BD%E4%BB%A4Reset%E3%80%81Checkout%E3%80%81Revert%E8%BE%A8%E6%9E%90.md)

	git reset、git checkout和git revert都是用来撤销代码仓库中的某些更改，所以我们经常弄混。在这篇文章中，我们比较最常见的用法，分析在什么场景下该用哪个命令。

 - **第3章** [Git log高级用法](https://github.com/geeeeeeeeek/git-recipes/blob/master/Git_log%E9%AB%98%E7%BA%A7%E7%94%A8%E6%B3%95.md)
 
 任何一个版本控制系统设计的目的都是为了记录你代码的变化——谁贡献了什么，找出bug是什么时候引入的，以及撤回一些有问题的更改。`git log` 可以格式化commit输出的形式，或过滤输出的commit从而找到项目中你需要的任何信息。

 - **第4章** [Git钩子：自定义你的工作流](https://github.com/geeeeeeeeek/git-recipes/blob/master/Git%E9%92%A9%E5%AD%90.md)

 Git钩子是在Git仓库中特定事件发生时自动运行的脚本。它可以让你自定义Git内部的行为，在开始周期中的关键点触发自定义的行为，自动化或者优化你开发工作流中任意部分。
 
 - **第5章** Git ref引用

**第6篇 Git应用实践：用GitLab搭建一个课程教学仓库**

 - **第1章** 教师和学生的最佳实践指南

	GitLab本身的权限管理和组织结构已经满足了教学中课程创建、学生管理、收发作业、通知统计等需求。不过，在实践中我们要尤其注意各处的权限和命名规范。因此，我总结了一份教师和学生的最佳实践指南，保证各门课程能够顺畅地进行。
 - **第2章** 在上层搭建一个Classroom应用

	在实践中，我们要手动地导入大量学生、创建分支以及在Gitlab复杂的页面中穿梭。显然我们可以做得更好，那就是在GitLab上再搭建一层Classroom应用。在这章中，我会介绍我们是如何抽取需求，以及构建这个应用的。


