git-recipes
====
高质量的Git中文教程

目录
---
**第1篇 Git**

  
**第2篇 Git快速入门**

 - **第1章** [从零搭建本地代码仓库](https://github.com/geeeeeeeeek/git-recipes/blob/master/Git%E7%AE%80%E6%98%93%E6%8C%87%E5%8D%97(%E4%B8%8A).md)

	这节完全面向入门者。我假设你从零开始创建一个项目并且想用Git来进行版本控制，我们会讨论如何在你的个人项目中使用Git，比如如何初始化你的项目，如何管理新的或者已有的文件，如何在远端仓库中储存你的代码。
	 - 安装Git
	 - 检出仓库
	 - 创建新仓库
	 - 工作流
	 - 添加与提交
	 - 推送改动
	
 - **第2章** 多分支上的协作和管理
 
  
**第3篇 Git命令详解**

 - 第1章 [图解Git命令](https://github.com/geeeeeeeeek/git-recipes/blob/master/Git%E5%9B%BE%E8%A7%A3.md)

	如果你稍微理解git的工作原理，这篇文章能够让你理解的更透彻。
	 - 基本用法
	 - 约定
	 - 命令详解
  
**第4篇 Git实用贴士**

 - **第1章** 代码合并：Merge、Rebase的选择
 
 - **第2章** [代码回滚：Reset、Checkout、Revert的选择](https://github.com/geeeeeeeeek/git-recipes/blob/master/%E5%9B%9E%E6%BB%9A%E5%91%BD%E4%BB%A4Reset%E3%80%81Checkout%E3%80%81Revert%E8%BE%A8%E6%9E%90.md)

	git reset、git checkout和git revert都是用来撤销代码仓库中的某些更改，所以我们经常弄混。在这篇文章中，我们比较最常见的用法，分析在什么场景下该用哪个命令。
	 - Commit层面的操作
	 - 文件层面的操作
	 - 总结

 - **第3章** [Git log高级用法](https://github.com/geeeeeeeeek/git-recipes/blob/master/Git_log%E9%AB%98%E7%BA%A7%E7%94%A8%E6%B3%95.md)
 
 任何一个版本控制系统设计的目的都是为了记录你代码的变化——谁贡献了什么，找出bug是什么时候引入的，以及撤回一些有问题的更改。`git log` 可以格式化commit输出的形式，或过滤输出的commit从而找到项目中你需要的任何信息。

	 - 格式化Log输出
	 - 过滤提交历史
	 - 总结

 - **第4章** Git钩子：自定义你的工作流
 
 - **第5章** Git ref引用

**第5篇 部署私有Git服务**

 - **第1章** 最佳实践：搭建一个基于GitLab的教室
 

我为什么要做这份菜单
---
在整理Git资料的时候，我发现社区贡献了非常多高质量的博客文章、指南等等。尤其英文的那些资料，除了大家熟知的《Git图解》，还有好多优秀的文章仍无人翻译。此外，这些资料往往只涉及某些特定的话题，如果能有一份菜单将这些菜谱以特定的方式串起来，那么对于Git学习者来说将会是极大的便利。尤其对于我这样热爱查阅社区资料胜过出版物的懒人 : ) 随着我的学习节奏还会不断有新的菜谱加入进来，或许不会很频繁，不过也没有确定的终点。

版权说明
---
除非另行注明，这个项目中的所有内容采用知识共享-署名([CC BY 2.5 AU](http://creativecommons.org/licenses/by/2.5/au/deed.zh))协议共享，童仲毅(geeeeeeeeek@github)版权所有。

其中不少资料是在原基础上翻译或演绎而来的，我都在页面最上方标明了原作者、原文链接以及原文采用的协议。如仍有版权疑问，请在issue中提出。

欢迎通过issue或者pr推荐你认为合适的资料，让这份菜单更充实一些。
