# Git 钩子：自定义你的工作流

> BY 童仲毅（[geeeeeeeeek@github](https://github.com/geeeeeeeeek/git-recipes/)）
>
> 这是一篇在[原文（BY atlassian）](https://www.atlassian.com/git/tutorials/git-hooks)基础上演绎的译文。除非另行注明，页面上所有内容采用知识共享-署名（[CC BY 2.5 AU](http://creativecommons.org/licenses/by/2.5/au/deed.zh)）协议共享。

Git 钩子是在 Git 仓库中特定事件发生时自动运行的脚本。它可以让你自定义 Git 内部的行为，在开发周期中的关键点触发自定义的行为。

![enter image description here](https://wac-cdn.atlassian.com/dam/jcr:ac22adee-d740-4216-a92a-33c14b5623e5/01.svg)

Git 钩子最常见的使用场景包括推行提交规范，根据仓库状态改变项目环境，和接入持续集成工作流。但是，因为脚本可以完全定制，你可以用 Git 钩子来自动化或者优化你开发工作流中任意部分。

在这篇文章中，我们会先简要介绍 Git 钩子是如何工作的。然后，我们会审视一些本地和远端仓库使用最流行的钩子。

# 概述

Git 钩子是仓库中特定事件发生时 Git 自动运行的普通脚本。因此，Git 钩子安装和配置也非常容易。

钩子在本地或服务端仓库都可以部署，且只会在仓库中事件发生时被执行。在文章后面我们会具体地研究各种钩子。接下来所讲的配置对本地和服务端钩子都起作用。

### 安装钩子

钩子存在于每个 Git 仓库的 `.git/hooks` 目录中。当你初始化仓库时，Git 自动生成这个目录和一些示例脚本。当你观察 `.git/hooks` 时，你会看到下面这些文件：

```
applypatch-msg.sample       pre-push.sample
commit-msg.sample           pre-rebase.sample
post-update.sample          prepare-commit-msg.sample
pre-applypatch.sample       update.sample
pre-commit.sample
```

这里已经包含了大部分可用的钩子了，但是 `.sample` 拓展名防止它们默认被执行。为了安装一个钩子，你只需要去掉 `.sample` 拓展名。或者你要写一个新的脚本，你只需添加一个文件名和上述匹配的新文件，去掉 `.sample` 拓展名。

比如说，试试安装一个 `prepare-commit-msg` 钩子。去掉脚本的 `.sample` 拓展名，在文件中加上下面这两行：

```
#!/bin/sh

echo "# Please include a useful commit message!" > $1
```

钩子需要能被执行，所以如果你创建了一个新的脚本文件，你需要修改它的文件权限。比如说，为了确保 `prepare-commit-msg` 可执行，运行下面这个命令：

```
chmod +x prepare-commit-msg
```

接下来你每次运行 `git commit` 时，你会看到默认的提交信息都被替换了。我们会在「准备提交信息」一节中细看它是如何工作的。现在我们已经可以定制 Git 的内部功能，你只需要坐和放宽。

内置的样例脚本是非常有用的参考资料，因为每个钩子传入的参数都有非常详细的说明（不同钩子不一样）。

### 脚本语言

内置的脚本大多是  shell和 PERL 语言的，但你可以使用任何脚本语言，只要它们最后能编译到可执行文件。每次脚本中的 `#!/bin/sh` 定义了你的文件将被如何解释。比如，使用其他语言时你只需要将 path 改为你的解释器的路径。

比如说，你可以在 `prepare-commit-msg` 中写一个可执行的 Python 脚本。下面这个钩子和上一节的 shell 脚本做的事完全一样。

```
#!/usr/bin/env python

import sys, os

commit_msg_filepath = sys.argv[1]
with open(commit_msg_filepath, 'w') as f:
    f.write("# Please include a useful commit message!")
```

注意第一行改成了 Python 解释器的路径。此外，这里用 `sys.argv[1]` 而不是 `$1` 来获取第一个参数（这个也后面再讲）。

这个特性非常强大，因为你可以用任何你喜欢的语言来编写  Git 钩子。

### 钩子的作用域

对于任何 Git 仓库来说钩子都是本地的，而且它不会随着 `git clone` 一起复制到新的仓库。而且，因为钩子是本地的，任何能接触得到仓库的人都可以修改。

对于开发团队来说，这有很大的影响。首先，你要确保你们成员之间的钩子都是最新的。其次，你也不能强行让其他人用你喜欢的方式提交——你只能鼓励他们这样做。

在开发团队中维护钩子是比较复杂的，因为 `.git/hooks` 目录不随你的项目一起拷贝，也不受版本控制影响。一个简单的解决办法是把你的钩子存在项目的实际目录中（在 `.git` 外）。这样你就可以像其他文件一样进行版本控制。为了安装钩子，你可以在 `.git/hooks` 中创建一个符号链接，或者简单地在更新后把它们复制到 `.git/hooks` 目录下。

![enter image description here](https://wac-cdn.atlassian.com/dam/jcr:e068ea71-a552-4d07-9917-49104f4d382e/02.svg)

作为备选方案，Git 同样提供了一个模板目录机制来更简单地自动安装钩子。每次你使用 `git init` 或 `git clone` 时，模板目录文件夹下的所有文件和目录都会被复制到 `.git` 文件夹。

所有的下面讲到的本地钩子都可以被更改或者彻底删除，只要你是项目的参与者。这完全取决于你的团队成员想不想用这个钩子。所以记住，最好把 Git 钩子当成一个方便的开发者工具而不是一个严格强制的开发规范。

也就是说，用服务端钩子来拒绝没有遵守规范的提交是完全可行的。后面我们会再讨论这个问题。

## 本地钩子

本地钩子只影响它们所在的仓库。当你在读这一节的时候，记住开发者可以修改他们本地的钩子，所以不要用它们来推行强制的提交规范。不过，它们确实可以让开发者更易于接受这些规范。

在这一节中，我们会探讨 6 个最有用的本地钩子：

- pre-commit
- prepare-commit-msg
- commit-msg
- post-commit
- post-checkout
- pre-rebase

前四个钩子让你介入完整的提交生命周期，后两个允许你执行一些额外的操作，分别为 `git checkout` 和 `git rebase` 的安全检查。

所有带 `pre-` 的钩子允许你修改即将发生的操作，而带 `post-` 的钩子只能用于通知。

我们也会看到处理钩子的参数和用底层 Git 命令获取仓库信息的实用技巧。

### pre-commit

`pre-commit` 脚本在每次你运行 `git commit` 命令时，Git 向你询问提交信息或者生产提交对象时被执行。你可以用这个钩子来检查即将被提交的代码快照。比如说，你可以运行一些自动化测试，保证这个提交不会破坏现有的功能。

`pre-commit` 不需要任何参数，以非0状态退出时将放弃整个提交。让我们看一个简化了的（和更详细的）内置 `pre-commit` 钩子。只要检测到不一致时脚本就放弃这个提交，就像 `git diff-index` 命令定义的那样（只要词尾有空白字符、只有空白字符的行、行首一个 tab 后紧接一个空格就被认为错误）。

```
#!/bin/sh

# 检查这是否是初始提交
if git rev-parse --verify HEAD >/dev/null 2>&1
then
    echo "pre-commit: About to create a new commit..."
    against=HEAD
else
    echo "pre-commit: About to create the first commit..."
    against=4b825dc642cb6eb9a060e54bf8d69288fbee4904
fi

# 使用git diff-index来检查空白字符错误
echo "pre-commit: Testing for whitespace errors..."
if ! git diff-index --check --cached $against
then
    echo "pre-commit: Aborting commit due to whitespace errors"
    exit 1
else
    echo "pre-commit: No whitespace errors :)"
    exit 0
fi
```

使用 `git diff-index` 时我们要指出和哪次提交进行比较。一般来说是 HEAD，但 HEAD 在创建第一次提交时不存在，所以我们的第一个任务是解决这个极端情形。我们通过 `git rev-parse --verify` 来检查 HEAD 是否是一个合法的引用。`>/dev/null 2>&1` 这部分屏蔽了 `git rev-parse` 任何输出。HEAD 或者一个新的提交对象被储存在 `against` 变量中供 `git diff-index` 使用。`4b825d...` 这个哈希字串代表一个空白提交的 ID。

`git diff-index --cached` 命令将提交和缓存区比较。通过传入 `-check` 选项，我们要求它在更改引入空白字符错误时警告我们。如果它这么做了，我们返回状态1来放弃这次提交，否则返回状态 0，提交工作流正常进行。

这只是 `pre-commit` 的其中一个例子。它恰好使用了已有的 Git 命令来根据提交带来的更改进行测试，但你可以在 `pre-commit` 中做任何你想做的事，比如执行其它脚本、运行第三方测试集、用 Lint 检查代码风格。



### prepare-commit-msg

`prepare-commit-msg` 钩子在 `pre-commit` 钩子在文本编辑器中生成提交信息之后被调用。这被用来方便地修改自动生成的 squash 或 merge 提交。

`prepare-commit-msg` 脚本的参数可以是下列三个：

- 包含提交信息的文件名。你可以在原地更改提交信息。
- 提交类型。可以是信息（`-m` 或 `-F` 选项），模板（`-t` 选项），merge（如果是个合并提交）或 squash（如果这个提交插入了其他提交）。
- 相关提交的 SHA1 哈希字串。只有当 `-c`、`-C` 或 `--amend` 选项出现时才需要。

和 `pre-commit` 一样，以非0状态退出会放弃提交。

我们已经看过一个修改提交信息的简单例子，现在我们来看一个更有用的脚本。使用 issue 跟踪器时，我们通常在单独的分支上处理 issue。如果你在分支名中包含了 issue 编号，你可以使用 `prepare-commit-msg` 钩子来自动地将它包括在那个分支的每个提交信息中。

```
#!/usr/bin/env python

import sys, os, re
from subprocess import check_output

# 收集参数
commit_msg_filepath = sys.argv[1]
if len(sys.argv) > 2:
    commit_type = sys.argv[2]
else:
    commit_type = ''
if len(sys.argv) > 3:
    commit_hash = sys.argv[3]
else:
    commit_hash = ''

print "prepare-commit-msg: File: %s\nType: %s\nHash: %s" % (commit_msg_filepath, commit_type, commit_hash)

# 检测我们所在的分支
branch = check_output(['git', 'symbolic-ref', '--short', 'HEAD']).strip()
print "prepare-commit-msg: On branch '%s'" % branch

# 用issue编号生成提交信息
if branch.startswith('issue-'):
    print "prepare-commit-msg: Oh hey, it's an issue branch."
    result = re.match('issue-(.*)', branch)
    issue_number = result.group(1)

    with open(commit_msg_filepath, 'r+') as f:
        content = f.read()
        f.seek(0, 0)
        f.write("ISSUE-%s %s" % (issue_number, content))
```

首先，上面的 `prepare-commit-msg` 钩子告诉你如何收集传入脚本的所有参数。接下来，它调用了 `git symbolic-ref --short HEAD` 来获取对应 HEAD 的分支名。如果分支名以 `issue-` 开头，它会重写提交信息文件，在第一行加上 issue 编号。比如你的分支名 `issue-224`，下面的提交信息将会生成：

```
ISSUE-224

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch issue-224
# Changes to be committed:
#   modified:   test.txt
```

有一点要记住的是即使用户用 `-m` 传入提交信息，`prepare-commit-msg` 也会运行。也就是说，上面这个脚本会自动插入 `ISSUE-[#]` 字符串，而用户无法更改。你可以检查第二个参数是否是提交类型来处理这个情况。

但是，如果没有 `-m` 选项，`prepare-commit-msg` 钩子允许用户修改生成后的提交信息。所以脚本的目的是为了方便，而不是推行强制的提交信息规范。如果你要这么做，你需要下一节所讲的 `commit-msg` 钩子。

### commit-msg

`commit-msg` 钩子和 `prepare-commit-msg` 钩子很像，但它会在用户输入提交信息之后被调用。这适合用来提醒开发者他们的提交信息不符合你团队的规范。

传入这个钩子唯一的参数是包含提交信息的文件名。如果它不喜欢用户输入的提交信息，它可以在原地修改这个文件（和 `prepare-commit-msg` 一样），或者它会以非 0 状态退出，放弃这个提交。

比如说，下面这个脚本确认用户没有删除 `prepare-commit-msg` 脚本自动生成的 `ISSUE-[#]` 字符串。

```
#!/usr/bin/env python

import sys, os, re
from subprocess import check_output

# 收集参数
commit_msg_filepath = sys.argv[1]

# 检测所在的分支
branch = check_output(['git', 'symbolic-ref', '--short', 'HEAD']).strip()
print "commit-msg: On branch '%s'" % branch

# 检测提交信息，判断是否是一个issue提交
if branch.startswith('issue-'):
    print "commit-msg: Oh hey, it's an issue branch."
    result = re.match('issue-(.*)', branch)
    issue_number = result.group(1)
    required_message = "ISSUE-%s" % issue_number

    with open(commit_msg_filepath, 'r') as f:
        content = f.read()
        if not content.startswith(required_message):
            print "commit-msg: ERROR! The commit message must start with '%s'" % required_message
            sys.exit(1)
```

虽然用户每次创建提交时，这个脚本都会运行。但你还是应该避免做检查提交信息之外的事情。如果你需要通知其他服务一个快照已经被提交了，你应该使用 `post-commit` 这个钩子。

### post-commit

`post-commit` 钩子在 `commit-msg` 钩子之后立即被运行 。它无法更改 `git commit` 的结果，所以这主要用于通知用途。

这个脚本没有参数，而且退出状态不会影响提交。对于大多数 `post-commit` 脚本来说，你只是想访问你刚刚创建的提交。你可以用 `git rev-parse HEAD` 来获得最近一次提交的SHA1哈希字串，或者你可以用 `git log -l HEAD` 获取完整的信息。

比如说，如果你需要每次提交快照时向老板发封邮件（也许对于大多数工作流来说这不是个好的想法），你可以加上下面这个 `post-commit` 钩子。

```
#!/usr/bin/env python

import smtplib
from email.mime.text import MIMEText
from subprocess import check_output

# 获得新提交的git log --stat输出
log = check_output(['git', 'log', '-1', '--stat', 'HEAD'])

# 创建一个纯文本的邮件内容
msg = MIMEText("Look, I'm actually doing some work:\n\n%s" % log)

msg['Subject'] = 'Git post-commit hook notification'
msg['From'] = 'mary@example.com'
msg['To'] = 'boss@example.com'

# 发送信息
SMTP_SERVER = 'smtp.example.com'
SMTP_PORT = 587

session = smtplib.SMTP(SMTP_SERVER, SMTP_PORT)
session.ehlo()
session.starttls()
session.ehlo()
session.login(msg['From'], 'secretPassword')

session.sendmail(msg['From'], msg['To'], msg.as_string())
session.quit()
```

你虽然可以用 `post-commit` 来触发本地的持续集成系统，但大多数时候你想用的是 `post-receive` 这个钩子。它运行在服务端而不是用户的本地机器，它同样在任何开发者推送代码时运行。那里更适合你进行持续集成。

### post-checkout

`post-checkout` 钩子和 `post-commit` 钩子很像，但它在你用 `git checkout` 查看引用的时候被调用。这是用来清理你的工作目录中可能会令人困惑的生成文件。

这个钩子接受三个参数，它的返回状态不影响 `git checkout` 命令。

- HEAD 前一次提交的引用
- 新的 HEAD 的引用
- 1 或 0，分别代表是分支 checkout 还是文件 checkout。

Python 程序员经常遇到的问题是切换分支后那些之前生成的 `.pyc` 文件。解释器有时使用 `.pyc` 而不是 `.py` 文件。为了避免歧义，你可以在每次用 `post-checkout` 切换到新的分支的时候，删除所有 `.pyc` 文件。

```
#!/usr/bin/env python

import sys, os, re
from subprocess import check_output

# 收集参数
previous_head = sys.argv[1]
new_head = sys.argv[2]
is_branch_checkout = sys.argv[3]

if is_branch_checkout == "0":
    print "post-checkout: This is a file checkout. Nothing to do."
    sys.exit(0)

print "post-checkout: Deleting all '.pyc' files in working directory"
for root, dirs, files in os.walk('.'):
    for filename in files:
        ext = os.path.splitext(filename)[1]
        if ext == '.pyc':
            os.unlink(os.path.join(root, filename))
```

钩子脚本当前的工作目录总是位于仓库的根目录下，所以 `os.walk('.')` 调用遍历了仓库中所有文件。接下来，我们检查它的拓展名，如果是 `.pyc` 就删除它。

通过 `post-checkout` 钩子，你还可以根据你切换的分支来来更改工作目录。比如说，你可以在代码库外面使用一个插件分支来储存你所有的插件。如果这些插件需要很多二进制文件而其他分支不需要，你可以选择只在插件分支上 build。

### pre-rebase

`pre-rebase` 钩子在 `git rebase` 发生更改之前运行，确保不会有什么糟糕的事情发生。

这个钩子有两个参数：fork 之前的上游分支，将要 rebase 的下游分支。如果 rebase 当前分支则第二个参数为空。以非 0 状态退出会放弃这次 rebase。

比如说，如果你想彻底禁用 rebase 操作，你可以使用下面的 `pre-rebase` 脚本：

```
#!/bin/sh

# 禁用所有rebase
echo "pre-rebase: Rebasing is dangerous. Don't do it."
exit 1
```

每次运行 `git rebase`，你都会看到下面的信息：

```
pre-rebase: Rebasing is dangerous. Don't do it.
The pre-rebase hook refused to rebase.
```

内置的 `pre-rebase.sample` 脚本是一个更复杂的例子。它在何时阻止 rebase 这方面更加智能。它会检查你当前的分支是否已经合并到了下一个分支中去（也就是主分支）。如果是的话，rebase 可能会遇到问题，脚本会放弃这次 rebase。

# 服务端钩子

服务端钩子和本地钩子几乎一样，只不过它们存在于服务端的仓库中（比如说中心仓库，或者开发者的公共仓库）。当和官方仓库连接时，其中一些可以用来拒绝一些不符合规范的提交。

这节中我们要讨论下面三个服务端钩子：

- pre-receive
- update
- post-receive

这些钩子都允许你对 `git push` 的不同阶段做出响应。

服务端钩子的输出会传送到客户端的控制台中，所以给开发者发送信息是很容易的。但你要记住这些脚本在结束完之前都不会返回控制台的控制权，所以你要小心那些长时间运行的操作。

### pre-receive

`pre-receive` 钩子在有人用 `git push` 向仓库推送代码时被执行。它只存在于远端仓库中，而不是原来的仓库中。

这个钩子在任意引用被更新前被执行，所以这是强制推行开发规范的好地方。如果你不喜欢推送的那个人（多大仇 = =），提交信息的格式，或者提交的更改，你都可以拒绝这次提交。虽然你不能阻止开发者写出糟糕的代码，但你可以用 `pre-receive` 防止这些代码流入官方的代码库。

这个脚本没有参数，但每一个推送上来的引用都会以下面的格式传入脚本的单独一行：

```
<old-value> <new-value> <ref-name>
```

你可以看到这个钩子做了非常简单的事，就是读取推送上来的引用并且把它们打印出来。

```
#!/usr/bin/env python

import sys
import fileinput

# 读取用户试图更新的所有引用
for line in fileinput.input():
    print "pre-receive: Trying to push ref: %s" % line

# 放弃推送
# sys.exit(1)
```

这和其它钩子相比略微有些不同，因为信息是通过标准输入而不是命令行传入的。在远端仓库的 `.git/hooks` 中加上这个脚本，推送到 master 分支，你会看到下面这些信息打印出来：

```
b6b36c697eb2d24302f89aa22d9170dfe609855b 85baa88c22b52ddd24d71f05db31f4e46d579095 refs/heads/master
```

你可以用 SHA1 哈希字串，或者底层的 Git 命令，来检查将要引入的更改。一些常见的使用包括：

- 拒绝将上游分支 rebase 的更改
- 防止错综复杂的合并（非快速向前，会造成项目历史非线性）
- 检查用户是否有正确的权限来做这些更改（大多用于中心化的 Git 工作流中）
- 如果多个引用被推送，在 `pre-receive` 中返回非 0 状态，拒绝所有提交。如果你想一个个接受或拒绝分支，你需要使用 `update` 钩子

### update

`update` 钩子在 `pre-receive` 之后被调用，用法也差不多。它也是在实际更新前被调用的，但它可以分别被每个推送上来的引用分别调用。也就是说如果用户尝试推送到4个分支，`update` 会被执行 4 次。和 `pre-receive` 不一样，这个钩子不需要读取标准输入。事实上，它接受三个参数：

- 更新的引用名称
- 引用中存放的旧的对象名称
- 引用中存放的新的对象名称

这些信息和 `pre-receive` 相同，但因为每次引用都会分别触发更新，你可以拒绝一些引用而接受另一些。

```
#!/usr/bin/env python

import sys

branch = sys.argv[1]
old_commit = sys.argv[2]
new_commit = sys.argv[3]

print "Moving '%s' from %s to %s" % (branch, old_commit, new_commit)

# 只放弃当前分支的推送
# sys.exit(1)
```

上面这个钩子简单地输出了分支和新旧提交的哈希字串。当你向远程仓库推送超过一个分支时，你可以看到每个分支都有输出。

### post-receive

`post-receive` 钩子在成功推送后被调用，适合用于发送通知。对很多工作流来说，这是一个比 `post-commit` 更好的发送通知的地方，因为这些更改在公共的服务器而不是用户的本地机器上。给其他开发者发送邮件或者触发一个持续集成系统都是 `post-receive` 常用的操作。

这个脚本没有参数，但和 `pre-receive` 一样通过标准输入读取。

## 总结

在这篇文章中，我们学习了如果用 Git 钩子来修改内部行为，当仓库中特定的事件发生时接受消息。钩子是存在于 `git/hooks` 仓库中的普通脚本，因此也非常容易安装和定制。

我们还看了一些常用的本地和服务端的钩子。这使得我们能够介入到整个开发生命周期中去。我们现在知道了如何在创建提交或推送的每个阶段执行自定义的操作。有了这些简单的脚本知识，你就可以对 Git 仓库为所欲为了 :]

> 这篇文章是[**「git-recipes」**](https://github.com/geeeeeeeeek/git-recipes/)的一部分，点击 [**目录**](https://github.com/geeeeeeeeek/git-recipes/wiki/) 查看所有章节。
>
> 如果你觉得文章对你有帮助，欢迎点击右上角的 **Star** :star2: 或 **Fork** :fork_and_knife:。
>
> 如果你发现了错误，或是想要加入协作，请参阅 [Wiki 协作说明](https://github.com/geeeeeeeeek/git-recipes/issues/1)。
