# Git 从入门到企业级实战完整教学文档

> **适用人群**：零基础计算机新手、在校大学生、刚入行的初级开发工程师
> **学习目标**：学完后能独立胜任中小型公司前后端开发日常 Git 工作流，看懂企业规范、规避线上事故
> **文档风格**：口语化、步骤化、全程企业真实场景驱动，拒绝纯理论堆砌

---

## 第一章：Git 底层核心逻辑（先懂原理再敲命令，新手不踩坑）

### 1.1 版本控制是什么？无 Git 开发的企业痛点

在开始敲任何命令之前，我们先来聊一个最本质的问题：**为什么全世界几千万程序员都在用 Git？**

想象一下你在公司里开发一个电商网站项目，团队有 5 个人，大家共用一台服务器上的代码文件夹。没有 Git 的时候，你们会遇到这些灾难：

**痛点一：代码互相覆盖**
小王在改「购物车页面」，小李也在改「购物车页面」，小李后保存，直接把小王的代码覆盖了。小王写了 3 天的逻辑瞬间消失，没有任何备份可以恢复。

**痛点二：文件命名地狱**
为了防止覆盖，大家开始这样命名文件：`购物车_最终版.java`、`购物车_最终版2.java`、`购物车_打死不改版.java`、`购物车_真的最后一次.java`……项目做到第 3 个月，文件夹里有 200 多个文件，根本不知道哪个是最新版。

**痛点三：线上出了 Bug 没法回滚**
今天上线了一个新版本，用户反馈支付功能崩溃了。你想赶紧回到昨天的稳定版本，但昨天的代码已经被今天的覆盖了，你根本找不回昨天那个能正常运行的版本。

**痛点四：多人协作完全靠吼**
「这个文件我改了，你别动！」「你改完跟我说一声！」——全靠口头沟通，一旦有人请假或者忘记说，就出问题。

**版本控制系统（Version Control System）** 就是为了解决上面所有问题而诞生的工具。你可以把它理解为：**一个专门管理代码修改历史的超级管家**。它会自动帮你做以下事情：

- 每次你修改代码并「提交」，它都会帮你**拍一张完整的快照**保存
- 你可以随时**回退到任何一次历史快照**，代码不会丢
- 多个人可以同时修改同一个项目，系统会**自动合并**大家的改动
- 如果有人改了同一行代码，系统会**提醒你冲突了**，让你手动决定保留谁的

**Git 就是目前全世界最流行的版本控制系统**，由 Linux 之父 Linus Torvalds 在 2005 年发明。现在不管是腾讯、阿里、字节跳动这样的国内大厂，还是 Google、Facebook 这样的国外巨头，几乎所有互联网公司都在用 Git。

### 1.2 Git 三大核心区域比喻：工作区、暂存区、本地仓库、远程仓库

这是新手学 Git 最容易懵的地方，但理解了它就等于理解了 Git 的一半。我用一个生活中的例子来讲：

**比喻：Git 就像一个「文档管理流水线」**

想象你是一个作家，正在写一本小说：

**① 工作区（Working Directory）= 你的书桌**

你的书桌上放着正在写的稿子，你可以随意涂改、撕掉重写、加新段落。这就是你平时写代码的地方——你在编辑器（比如 VS Code、IDEA）里打开的项目文件夹，就是工作区。你在里面做的任何修改，Git 都知道，但还没有正式「存档」。

**② 暂存区（Staging Area / Index）= 书桌旁边的「待归档文件夹」**

你在书桌上改了很多页，觉得这一批改得差不多了，就把它们放进旁边的「待归档文件夹」里，意思是：「这些改动我觉得 OK 了，准备正式归档。」暂存区就是一个**过渡区域**，让你先挑选好哪些改动要一起存档，而不是一股脑全存。

**③ 本地仓库（Local Repository）= 你家里的「保险柜」**

当你确认暂存区里的改动都没问题了，就执行一个「归档」操作（Git 里叫 `commit`），把这批改动正式锁进保险柜里。保险柜里的每一份存档都有编号和时间戳，永远不会被意外删除或覆盖。这就是你的本地仓库——**存储在你自己电脑上的完整代码历史**。

**④ 远程仓库（Remote Repository）= 公司的「云端共享网盘」**

你一个人在家写小说，保险柜够用了。但如果你是一个 5 人写作团队呢？每个人家里都有保险柜，互相看不到对方写了什么。这时候就需要一个放在云端的共享网盘（比如 GitHub、Gitee、GitLab），大家把各自保险柜里的存档**推送（push）上去**，也可以把别人推送的存档**拉取（pull）下来**。这样所有人就共享同一份最新的小说内容了。

**数据流转全过程（记住这个顺序！）：**

```
你的代码修改 → [git add] → 暂存区 → [git commit] → 本地仓库 → [git push] → 远程仓库
  （书桌）                （待归档文件夹）              （保险柜）                （云端网盘）
```

反过来，获取别人代码的流程：

```
远程仓库 → [git pull / git fetch] → 工作区（你的书桌自动更新为最新内容）
```

### 1.3 Git 仓库、快照、提交、分支、合并基础概念通俗解读

**仓库（Repository / Repo）**
就是一个被 Git 管理的项目文件夹。你可以把任何一个文件夹变成 Git 仓库。仓库里保存了这个项目从创建到现在的所有修改记录。就像是一个「项目档案室」，里面存放着项目的一切历史。

**快照（Snapshot）**
每次你执行一次「提交」操作，Git 就会给你当前的代码拍一张完整的照片，这就是快照。注意：不是只保存你改了什么，而是保存了整个文件夹在那一刻的完整状态。就像你每次写完一稿都把整本小说复印一份存档，而不是只复印你改过的那几页。

**提交（Commit）**
就是「拍照存档」这个动作本身。每次提交，Git 都会给你一个唯一的编号（叫做 commit hash，类似身份证号），还会记录谁在什么时间做了什么修改，以及你写的一句说明（commit message）。你可以把它理解为游戏里的「存档点」——一旦存档成功，你就永远可以随时读档回到这个状态。

**分支（Branch）**
想象你在写小说的第 5 章，突然想尝试一条完全不同的剧情线，但又不想把现在写好的内容搞乱。怎么办？你可以把当前的稿子**复印一份**，在复印件上随便改，原件完全不受影响。这个「复印件」就是分支。Git 里的分支就是代码的一个独立副本，你可以在上面开发新功能、做实验，不会影响主线代码。

**合并（Merge）**
当你在分支上把新功能开发完了，觉得满意了，就把分支上的改动**合回到主线**上去。就像你把复印件上写得好的新章节，抄回到原件里。这个过程就是合并。

### 1.4 区分 Git 和 SVN，说明现在企业为什么全用 Git

SVN（Subversion）是 Git 之前的老一代版本控制工具，很多传统企业以前都用它。但现在几乎所有互联网公司都已经迁移到 Git 了，原因如下：

| 对比维度 | SVN | Git |
|---------|-----|-----|
| 架构方式 | **集中式**：所有历史存在中央服务器上，断网就没法提交代码 | **分布式**：每个人电脑上都有完整的仓库副本，断网也能提交，有网了再同步 |
| 分支管理 | 分支是一个完整的目录拷贝，创建慢、合并容易冲突 | 分支只是一个轻量指针，创建秒完成，合并智能高效 |
| 速度 | 每次操作都要和中央服务器通信，网络差时很慢 | 大部分操作都在本地完成，速度飞快 |
| 离线工作 | 断网 = 完全无法工作 | 断网也能正常提交代码，联网后再推送 |
| 数据安全 | 服务器挂了，所有历史都没了（除非有备份） | 每个人电脑上都有完整历史，任何一个人的电脑都能恢复 |
| 企业生态 | 工具链老化，CI/CD 支持差 | GitHub/GitLab/Gitee 生态完善，所有 DevOps 工具链都以 Git 为核心 |

**一句话总结**：SVN 就像全班只有老师手里有一份答案册（集中式），老师不在大家啥也干不了；Git 就像每个同学手里都有一份完整答案册（分布式），谁都能独立学习，最后大家再互相核对统一。

---

## 第二章：本地 Git 基础命令（从零搭建本地仓库，单人开发）

> **阅读指引**：从本章开始，你会逐个学习 Git 的核心命令。每个命令都严格按照「企业场景用途 → 通俗比喻 → 语法参数 → 企业场景测验」四段模板讲解，确保你学完立刻能在工作中用。

### 2.1 Git 安装与配置

#### 【企业场景用途】

你入职新公司的第一天，领导让你把项目代码拉下来开始开发。这时候你电脑上必须先安装 Git，然后配置好你的身份信息（姓名和邮箱），这样以后你每次提交代码，Git 才能知道「这行代码是谁改的」。在公司项目里，如果 Git 没配置好你的名字，提交记录会显示 unknown，代码评审的时候没人知道这是你写的，出了问题也不好追溯。

#### 【通俗比喻讲解】

安装 Git 就像给你的电脑装了一个「代码档案管理员」。配置身份信息就像给这个管理员登记你的工牌——以后每次你存档，管理员都会在存档记录上写上你的名字和工号。

#### 【完整操作步骤】

**第一步：下载并安装 Git**

- Windows：访问 https://git-scm.com/download/win ，下载安装包，一路点 Next 默认安装即可
- macOS：打开终端（Terminal），输入 `git --version`，系统会提示你安装，点击确认即可；也可以通过 Homebrew 安装：`brew install git`
- Linux（Ubuntu/Debian）：终端输入 `sudo apt install git`

安装完成后，打开终端（Windows 用户在桌面右键选择「Git Bash Here」），输入以下命令验证安装成功：

```bash
git --version
# 输出类似：git version 2.43.0
```

**第二步：配置用户信息（必须做，每次提交都会用到）**

```bash
# 设置你的名字（显示在提交记录中的作者名）
git config --global user.name "你的名字"
# 示例：git config --global user.name "ZhangSan"

# 设置你的邮箱（显示在提交记录中的作者邮箱，建议用公司邮箱）
git config --global user.email "你的邮箱"
# 示例：git config --global user.email "zhangsan@company.com"
```

> **企业提醒**：`--global` 参数表示这个配置对你电脑上所有 Git 仓库生效。如果你电脑上同时有公司项目和个人项目，需要分别配置不同身份，那就不要用 `--global`，而是在具体项目目录里执行不带 `--global` 的命令。

**第三步：验证配置是否生效**

```bash
git config --list
# 你会看到类似这样的输出：
# user.name=ZhangSan
# user.email=zhangsan@company.com
```

**第四步：配置默认编辑器（可选但推荐）**

```bash
# 企业里很多人习惯用 VS Code 作为 Git 的默认编辑器
git config --global core.editor "code --wait"
```

#### 【企业场景测验题】

**题目 1（实操题）**：你刚入职公司，需要在电脑上配置 Git，你的公司邮箱是 lisi@techcorp.com，英文名是 LiSi。请写出完整的配置命令。

**标准答案**：
```bash
git config --global user.name "LiSi"
git config --global user.email "lisi@techcorp.com"
```

**解析**：企业开发中必须配置正确的用户名和邮箱，否则提交记录无法追溯到人。公司通常要求使用公司邮箱而非个人邮箱，方便在 GitLab/GitHub 上自动关联到公司账号。

---

**题目 2（选择题）**：你同时参与公司的项目 A（用公司邮箱 wangwu@corp.com）和自己的开源项目 B（用个人邮箱 wangwu@gmail.com），以下哪种做法是正确的？

A. 只配置 `--global` 为公司邮箱，所有项目都用公司邮箱
B. 只配置 `--global` 为个人邮箱，所有项目都用个人邮箱
C. `--global` 配置公司邮箱作为默认，在项目 B 目录下单独配置个人邮箱（不加 `--global`）
D. 每次提交前手动修改全局配置

**标准答案**：C

**解析**：`--global` 是全局默认配置，在特定项目目录下不带 `--global` 的 `git config` 命令只会影响该仓库，优先级高于全局配置。这样项目 A 自动用公司邮箱，项目 B 自动用个人邮箱，互不影响。选项 D 太麻烦且容易忘记改回来。

---

### 2.2 git init — 初始化本地仓库

#### 【企业场景用途】

当你开始一个全新的项目，或者接手一个还没有用 Git 管理的旧项目时，第一件事就是执行 `git init`，把一个普通文件夹变成 Git 仓库。比如领导说「你新建一个前端项目，用 Git 管理起来」，你就需要先创建文件夹，然后执行 `git init`。

#### 【通俗比喻讲解】

`git init` 就像给你的文件夹**请了一个档案管理员入驻**。在这之前，文件夹就是个普通文件夹，里面的文件改了就改了，没人帮你记历史。执行 `git init` 之后，这个文件夹就被 Git「接管」了，之后你做的每一次改动都可以被记录和追溯。

#### 【完整语法 + 企业常用参数】

```bash
# 在当前文件夹初始化 Git 仓库
git init

# 在指定路径创建新文件夹并初始化为 Git 仓库
git init 项目文件夹名
```

**实际示例**：
```bash
# 场景：你要新建一个电商前端项目
mkdir ecommerce-frontend
cd ecommerce-frontend
git init
# 输出：Initialized empty Git repository in /Users/zhangsan/ecommerce-frontend/.git/
```

执行完后，你会发现文件夹里多了一个隐藏的 `.git` 文件夹——这就是 Git 存放所有历史记录和管理数据的地方。**永远不要手动修改或删除 `.git` 文件夹**，否则整个仓库的历史记录都会丢失。

#### 【企业场景测验题】

**题目 1（实操题）**：领导让你在 D 盘 projects 目录下创建一个新项目 `order-service`，并用 Git 管理。请写出完整的操作命令。

**标准答案**：
```bash
cd D:/projects
mkdir order-service
cd order-service
git init
```

**解析**：先创建目录再进入，然后执行 `git init`。注意 Windows 上 Git Bash 使用正斜杠 `/` 作为路径分隔符。

---

### 2.3 git config — 查看与修改配置

#### 【企业场景用途】

在公司里，你可能需要查看当前 Git 配置是否正确、修改默认编辑器、设置提交时自动签名、或者在切换项目时调整局部配置。比如你发现提交记录里的邮箱写错了，需要用 `git config` 检查并修正。

#### 【通俗比喻讲解】

`git config` 就像你查看和修改**档案管理员的工作手册**。你可以告诉管理员：「以后存档的时候，作者名字写张三，邮箱写 zhangsan@company.com，用 VS Code 当记事本。」

#### 【完整语法 + 企业常用参数】

```bash
# 查看当前仓库的所有配置
git config --list

# 查看某个具体配置项
git config user.name
git config user.email

# 修改当前仓库的用户名（只影响当前项目）
git config user.name "新名字"

# 修改全局配置（影响所有项目）
git config --global user.name "新名字"

# 删除某个配置项
git config --global --unset user.name
```

**企业常用配置速查表**：

| 配置命令 | 作用 | 企业场景 |
|---------|------|---------|
| `git config --global user.name "Name"` | 设置提交者名字 | 入职第一天必配 |
| `git config --global user.email "email"` | 设置提交者邮箱 | 入职第一天必配 |
| `git config --global core.editor "code --wait"` | 设置默认编辑器为 VS Code | 很多公司统一用 VS Code |
| `git config --global init.defaultBranch main` | 新仓库默认分支名改为 main | 现代企业普遍使用 main 而非 master |
| `git config --list` | 查看所有配置 | 检查配置是否正确 |

#### 【企业场景测验题】

**题目 1（选择题）**：你发现最近几次提交记录显示的名字是 "admin" 而不是你的真名 "WangWu"，你应该怎么做？

A. 用 `git config --global user.name "WangWu"` 修改全局配置
B. 不用管，之前的提交记录会自动更新
C. 用 `git config user.name "WangWu"` 修改当前仓库配置，但之前错误的提交记录不会自动修改
D. A 和 C 都可以修改后续提交的名称，但已经提交的记录不会自动改变

**标准答案**：D

**解析**：修改 config 只会影响**之后的提交**，已经提交的历史记录不会自动更新。如果你只是想让以后的提交用正确名字，全局改或当前仓库改都行。选项 B 错误，Git 不会自动修改历史记录。

---

### 2.4 git add — 将修改添加到暂存区

#### 【企业场景用途】

你在公司写完了一段「购物车结算」功能的代码，准备提交了。但你这次同时改了 5 个文件，其中 3 个是购物车相关的，另外 2 个是你顺手修的其他模块 Bug。领导要求你**每次提交只做一件事**，这时候你需要用 `git add` 先把购物车相关的 3 个文件放进暂存区，单独提交；然后再把 Bug 修复的 2 个文件放进暂存区，单独提交。

#### 【通俗比喻讲解】

`git add` 就像你在书桌上改了一堆稿子，现在要**挑选**哪些改动放进「待归档文件夹」。你不是把桌上所有东西一股脑全塞进去，而是有选择地挑出相关的一批放进去。比如「这次只归档购物车相关的稿子，其他的下次再说」。

#### 【完整语法 + 企业常用参数】

```bash
# 添加指定文件到暂存区（企业最常用，推荐！）
git add 文件名
git add src/cart.js src/cart.css src/cart.html

# 添加当前目录下的所有修改（包含新增、修改、删除）
git add .

# 查看暂存区当前状态（配合 git add 使用，确认哪些文件已暂存）
git status
```

**企业真实操作示例**：
```bash
# 你改了 3 个购物车文件 + 2 个其他文件
# 第一步：只暂存购物车相关文件
git add src/cart.js src/cart.css src/cart.html

# 第二步：确认暂存区状态
git status
# 输出中你会看到：
# Changes to be committed:（已暂存，准备提交的）
#   modified: src/cart.js
#   modified: src/cart.css
#   modified: src/cart.html
# Changes not staged for commit:（未暂存，还没加入这次提交的）
#   modified: src/user.js
#   modified: src/login.css
```

> **企业规范提醒**：很多新手习惯直接 `git add .` 把所有改动全加进去，这在企业里是**不推荐**的。因为你可能会不小心把调试用的临时代码、本地配置文件、甚至密码密钥一起提交上去。建议先 `git status` 看一下改了哪些文件，确认没问题再有针对性地 `git add`。

#### 【企业场景测验题】

**题目 1（实操题）**：你在开发「用户注册」功能，同时顺手修改了 `README.md` 加了一行说明。领导要求功能代码和文档说明分成两次提交。你应该怎么操作？

**标准答案**：
```bash
# 第一次提交：只提交注册功能代码
git add src/register.js src/register.css
git commit -m "feat: 完成用户注册功能"

# 第二次提交：提交文档修改
git add README.md
git commit -m "docs: 更新项目说明文档"
```

**解析**：企业开发中「一次提交只做一件事」是基本规范，方便代码评审和后续回滚。如果功能和文档混在一起提交，以后想单独回滚功能或文档就很麻烦。

---

**题目 2（选择题）**：你修改了 `config/database.yml`（包含数据库密码）和 `src/api.js`（接口代码），准备提交接口代码。以下哪种做法是正确的？

A. `git add .` 全部添加，一起提交
B. `git add src/api.js`，只暂存接口代码文件，不暂存配置文件
C. `git add config/database.yml src/api.js`，都暂存
D. 先把数据库密码改成测试密码，再 `git add .`

**标准答案**：B

**解析**：`database.yml` 包含数据库密码等敏感信息，**绝对不能**提交到 Git 仓库。在企业里，这类文件应该写进 `.gitignore`（后面章节会讲），从源头上防止提交。选项 A、C、D 都有泄露密码的风险。

---

### 2.5 git status — 查看仓库当前状态

#### 【企业场景用途】

在公司开发中，`git status` 是你**用得最频繁的命令之一**，没有之一。你每次提交前要看一下改了哪些文件、每次拉取代码后要看一下有什么变化、切换分支后要看一下当前在哪个分支上。养成随时 `git status` 的习惯，能帮你避免 80% 的低级错误。

#### 【通俗比喻讲解】

`git status` 就像你**环顾一下你的书桌**，看看当前情况：哪些稿子改完了还没归档（已修改未暂存）、哪些已经放进待归档文件夹了（已暂存）、有没有新写的稿子还没被管理员注意到（未跟踪文件）。

#### 【完整语法 + 企业常用参数】

```bash
# 查看完整状态（显示详细信息）
git status

# 查看精简状态（一行一个文件，适合文件多的时候快速浏览）
git status -s
# 输出示例：
#  M src/cart.js     ← 已修改，未暂存（M 在右边）
# M  src/user.js     ← 已修改，已暂存（M 在左边）
# ?? new-file.js     ← 新文件，Git 还没跟踪（??）
# A  src/new-api.js  ← 新文件，已暂存（A 表示 Added）
# D  old-file.js     ← 文件被删除（D 表示 Deleted）
```

**企业真实操作示例**：
```bash
# 开发完一个功能后，准备提交前先检查状态
git status

# 输出：
# On branch feature/cart          ← 当前在购物车功能分支上
# Changes to be committed:         ← 以下文件已暂存，会包含在下一次提交中
#   (use "git restore --staged <file>..." to unstage)
#         modified:   src/cart.js
#         new file:   src/cart.test.js
#
# Changes not staged for commit:   ← 以下文件有修改但还没暂存
#   (use "git add <file>..." to update what will be committed)
#         modified:   src/utils.js
#
# Untracked files:                 ← 以下文件是新创建的，Git 还没跟踪
#   (use "git add <file>..." to include in what will be committed)
#         debug.log
```

#### 【企业场景测验题】

**题目 1（选择题）**：你执行 `git status -s` 后看到以下输出：
```
 M src/order.js
M  src/payment.js
?? temp-debug.log
A  src/refund.js
```
以下说法正确的是？

A. `src/order.js` 已暂存，会在下次提交中包含
B. `src/payment.js` 已暂存，会在下次提交中包含
C. `temp-debug.log` 已被 Git 跟踪
D. `src/refund.js` 是一个新文件且已暂存

**标准答案**：B、D

**解析**：在 `-s` 精简输出中，左边列代表暂存区状态，右边列代表工作区状态。`M ` 表示 M 在左边（已暂存的修改）；` M` 表示 M 在右边（未暂存的修改）；`??` 表示未跟踪文件；`A ` 表示新增且已暂存。`temp-debug.log` 是调试日志文件，不应该提交（应该加入 `.gitignore`）。

---

### 2.6 git commit — 提交快照到本地仓库

#### 【企业场景用途】

`git commit` 是你写完一段代码后**最关键的存档动作**。在公司里，你每完成一个小功能（比如写完登录接口、做完表单验证），就要执行一次 commit。每次 commit 都会生成一个存档点，以后如果需要回退代码、查看谁改了什么、 cherry-pick 某个修复，全靠这些 commit 记录。

#### 【通俗比喻讲解】

`git commit` 就是**给暂存区里的所有改动拍一张照片，永久存档到保险柜里**。拍完照之后，就算你把书桌上的稿子全撕了，你也可以从保险柜里把这张照片取出来恢复。每次拍照你都要写一句说明（commit message），告诉自己以后回来看的时候知道这次改了什么。

#### 【完整语法 + 企业常用参数】

```bash
# 提交暂存区的改动，附带一句说明（最常用）
git commit -m "说明这次改了什么"

# 提交并跳过暂存步骤（自动把所有已跟踪文件的修改加入暂存区再提交）
# 注意：不会包含新创建的未跟踪文件！企业里不推荐用，容易漏文件
git commit -am "说明"

# 打开编辑器写更详细的提交说明（企业里做大改动时推荐用）
git commit

# 修改上一次的提交说明（写错了 commit message 时用）
git commit --amend -m "新的说明"
```

**企业 commit message 规范（必须掌握！）**：

在正规企业里，commit message 不是随便写的，有一套通用前缀规范：

| 前缀 | 含义 | 示例 |
|------|------|------|
| `feat:` | 新功能 | `feat: 新增用户注册接口` |
| `fix:` | 修复 Bug | `fix: 修复购物车金额计算错误` |
| `docs:` | 文档修改 | `docs: 更新 API 接口文档` |
| `style:` | 代码格式调整（不影响逻辑） | `style: 统一缩进为 4 空格` |
| `refactor:` | 代码重构（不改功能） | `refactor: 提取公共工具函数` |
| `test:` | 测试相关 | `test: 补充用户登录单元测试` |
| `chore:` | 构建/工具链等杂项 | `chore: 升级 webpack 到 5.x` |

**企业真实操作示例**：
```bash
# 场景：你刚完成购物车页面的「全选/取消全选」功能
git add src/cart/selectAll.js src/cart/selectAll.css
git commit -m "feat: 购物车页面新增全选/取消全选功能"

# 场景：你发现刚才的 commit message 写错了
git commit --amend -m "feat: 购物车页面新增全选/取消全选功能，支持跨页选中状态保持"

# 场景：你修复了一个线上 Bug
git add src/payment/calculate.js
git commit -m "fix: 修复优惠券金额计算精度丢失导致多扣用户余额的问题"
```

#### 【企业场景测验题】

**题目 1（实操题）**：你刚完成了「用户头像上传」功能，需要提交代码。请写出完整的 git add + git commit 命令，commit message 要符合企业规范。

**标准答案**：
```bash
git add src/user/avatar-upload.js src/user/avatar-upload.css
git commit -m "feat: 新增用户头像上传功能，支持裁剪和压缩"
```

**解析**：commit message 使用 `feat:` 前缀表示新功能，说明清晰具体。企业里好的 commit message 应该让同事一看就知道这次改了什么，不需要去翻代码。

---

**题目 2（选择题）**：以下哪条 commit message 符合企业规范？

A. `git commit -m "改了点东西"`
B. `git commit -m "update"`
C. `git commit -m "fix: 修复用户列表分页参数传递错误导致的翻页白屏问题"`
D. `git commit -m "今天写了购物车功能，还修了个bug，另外改了样式"`

**标准答案**：C

**解析**：A、B 太空泛，完全看不出改了什么，以后排查问题根本找不到线索。D 虽然写了内容但没有规范前缀，且一次提交混了太多事情。C 使用了 `fix:` 前缀，清晰描述了修复的具体问题和影响，是标准的优秀 commit message。

---

### 2.7 git log — 查看提交历史

#### 【企业场景用途】

在公司里，你经常需要查看提交历史：比如排查线上 Bug 时想知道「这段代码是谁什么时候改的」、代码评审时想看「这个分支总共提交了哪些内容」、交接项目时想了解「项目的开发历程」。`git log` 就是你的「历史档案馆查询工具」。

#### 【通俗比喻讲解】

`git log` 就像翻看保险柜里的**存档记录册**。每一页记录着：这次存档的编号、谁存的、什么时间存的、存的时候写了一句什么说明。你可以从最新的一条往前翻，看看这个项目经历了哪些修改。

#### 【完整语法 + 企业常用参数】

```bash
# 查看完整提交历史（默认从最新到最旧排列）
git log

# 精简模式：一行一条记录（企业最常用！）
git log --oneline
# 输出示例：
# a3b5c2d feat: 新增购物车全选功能
# 8f1e4a6 fix: 修复登录验证码过期问题
# c92d3b1 feat: 完成用户注册接口

# 显示最近 N 条记录
git log --oneline -5     # 只看最近 5 条

# 显示提交历史的图表形式（可视化分支合并关系，企业调试时常用）
git log --oneline --graph --all

# 查看某个文件的所有修改历史
git log --oneline -- src/cart.js

# 查看某个人的提交记录（比如只看张三提交了什么）
git log --oneline --author="ZhangSan"

# 查看某个时间段的提交
git log --oneline --since="2025-01-01" --until="2025-02-01"
```

#### 【企业场景测验题】

**题目 1（实操题）**：线上用户反馈「订单页面」有 Bug，你怀疑是最近一周有人改了 `src/order/list.js` 这个文件引入的。请写出命令，查看这个文件最近 10 条修改记录。

**标准答案**：
```bash
git log --oneline -10 -- src/order/list.js
```

**解析**：通过 `--` 加上文件路径可以精确定位某个文件的修改历史，配合 `-10` 只看最近 10 条，快速排查是谁在什么时候改了什么导致的问题。

---

### 2.8 git restore — 撤销工作区的修改

#### 【企业场景用途】

你在公司改了一段代码，改着改着发现改坏了，想**放弃这次修改，恢复到上一次提交的状态**。比如你在调试的时候加了一堆 `console.log` 和临时代码，调试完了想把这些调试代码全清掉，恢复到干净状态，就可以用 `git restore`。

#### 【通俗比喻讲解】

`git restore` 就像你把书桌上**改坏的稿子扔掉，从保险柜里拿出上次的存档重新抄一份**。你书桌上的改动消失了，但保险柜里的存档完好无损。

#### 【完整语法 + 企业常用参数】

```bash
# 撤销工作区中某个文件的修改（恢复到上次提交的状态）
git restore src/cart.js

# 撤销工作区中多个文件的修改
git restore src/cart.js src/user.js src/order.js

# 撤销工作区中所有文件的修改（⚠️ 危险！所有未暂存的修改都会丢失！）
git restore .

# 把文件从暂存区移回工作区（取消 git add，文件修改还在）
git restore --staged src/cart.js
```

**重要区分（新手必记）**：

| 命令 | 作用 | 你的代码修改会怎样 |
|------|------|------------------|
| `git restore 文件名` | 撤销**工作区**的修改 | 修改**永久丢失**，恢复到上次提交的状态 |
| `git restore --staged 文件名` | 取消**暂存区**的文件（撤回 git add） | 修改**还在**，只是从暂存区移回了工作区 |

#### 【企业场景测验题】

**题目 1（选择题）**：你修改了 `src/api.js`，已经执行了 `git add src/api.js`，但突然意识到这个改动还不想提交。你应该怎么做？

A. `git restore src/api.js` — 撤销修改
B. `git restore --staged src/api.js` — 从暂存区移出，修改保留在工作区
C. 直接删掉 `src/api.js` 重新写
D. `git commit -m "撤销"` 提交后再改回来

**标准答案**：B

**解析**：文件已经 `git add` 进了暂存区，用 `git restore --staged` 可以把它从暂存区移出来，但你的代码修改还保留在工作区（书桌上），没有丢失。如果选 A，你的修改就彻底没了（因为这个文件在暂存区，`git restore` 不带 `--staged` 是恢复暂存区的版本）。

---

### 2.9 git reset — 回退提交（⚠️ 企业高危命令）

#### 【企业场景用途】

`git reset` 在企业里是一个**高风险命令**，用不好会导致代码丢失甚至覆盖远程仓库的公共代码。它的典型使用场景是：你在本地连续提交了 3 次，发现方向搞错了，想把这 3 次提交全部撤销，重新来过。**注意：`git reset` 只在本地分支使用，绝对不能对已经 push 到远程的公共分支使用！**

#### 【通俗比喻讲解】

`git reset` 就像把保险柜里的**存档记录撕掉**，假装这些存档从来没有发生过。你可以选择撕掉存档后稿子怎么处理：是保留在书桌上继续改（soft）、放到待归档文件夹里（mixed）、还是直接扔进垃圾桶（hard）。

#### 【完整语法 + 企业常用参数】

```bash
# ⚠️ 软重置：撤销 commit，但改动保留在暂存区（保险柜存档撕了，稿子还在待归档文件夹里）
git reset --soft HEAD~1        # 撤销最近 1 次提交

# ⚠️ 混合重置（默认）：撤销 commit + 取消暂存，改动保留在工作区（稿子在书桌上，但没归档）
git reset HEAD~1               # 等同于 git reset --mixed HEAD~1
git reset --mixed HEAD~1

# ⚠️⚠️⚠️ 硬重置：撤销 commit + 取消暂存 + 丢弃所有改动（稿子直接扔垃圾桶，彻底没了！）
git reset --hard HEAD~2        # 撤销最近 2 次提交，所有改动永久丢失！
```

> **HEAD~N 解释**：`HEAD` 代表当前最新提交，`HEAD~1` 代表往前回退 1 步（上一次提交的前一个），`HEAD~3` 代表回退 3 步。你也可以用具体的 commit 编号（hash）：`git reset --soft a3b5c2d`。

**三种模式对比表（必须牢记！）**：

| 模式 | commit 记录 | 暂存区的改动 | 工作区的改动 | 企业风险等级 |
|------|------------|------------|------------|------------|
| `--soft` | ❌ 撤销 | ✅ 保留在暂存区 | ✅ 保留 | 低：改动不会丢 |
| `--mixed`（默认） | ❌ 撤销 | ❌ 移出暂存区 | ✅ 保留在工作区 | 中：改动还在但需要重新 add |
| `--hard` | ❌ 撤销 | ❌ 清除 | ❌ 清除 | **🔴 极高：改动永久丢失，无法恢复！** |

**企业红线**：
> **🚨 绝对禁止**：`git reset --hard` 对已经 push 到远程的公共分支使用！这会导致你的本地历史和远程历史不一致，如果其他人已经基于远程代码开发，他们的代码也会出问题。
>
> **🚨 绝对禁止**：`git push --force`（强制推送）配合 `git reset` 覆盖远程公共分支！这会直接覆盖同事的代码，属于「开除级事故」。

#### 【企业场景测验题】

**题目 1（实操题）**：你在本地连续提交了 3 次（还没 push 到远程），发现整体方向搞错了，想撤销这 3 次提交，但保留代码改动在工作区重新整理。你应该用什么命令？

**标准答案**：
```bash
git reset --mixed HEAD~3
# 或者
git reset HEAD~3
```

**解析**：`--mixed` 模式会撤销 commit 记录，把改动退回工作区（书桌上），代码不会丢失，你可以重新整理后再分次提交。因为是本地还没 push 的提交，所以用 reset 是安全的。

---

**题目 2（选择题）**：以下哪种场景可以使用 `git reset --hard`？

A. 远程 main 分支上最近一次提交有 Bug，需要回退
B. 本地 dev 分支连续提交了 5 次（还没 push），全部写错了，想彻底丢弃
C. 同事已经基于你 push 的代码继续开发了，你想撤销你上次的提交
D. 线上生产环境的代码需要回滚到上个版本

**标准答案**：B

**解析**：只有**本地且还没 push 的提交**才能安全使用 `git reset --hard`。A 和 D 涉及远程/线上代码，应该用 `git revert`（后面章节会讲）。C 的提交已经 push 且别人在用，`--hard` 会导致同事代码冲突。

---

### 2.10 git diff — 查看代码差异

#### 【企业场景用途】

在公司里，`git diff` 是你的「代码对比放大镜」。提交之前你想确认一下自己到底改了哪些行、代码评审时你想看看同事的 PR 具体改了哪些内容、排查 Bug 时你想知道两次提交之间代码发生了什么变化——这些都要用 `git diff`。

#### 【通俗比喻讲解】

`git diff` 就像把你书桌上的稿子和某个存档版本**并排放在一起，用荧光笔标出所有不同的地方**：新增了哪些行、删掉了哪些行、修改了哪些行，一目了然。

#### 【完整语法 + 企业常用参数】

```bash
# 查看工作区（书桌）和暂存区（待归档文件夹）的差异（你改了但还没 add 的内容）
git diff

# 查看暂存区和上一次提交的差异（你 add 了但还没 commit 的内容）
git diff --staged
# 或者
git diff --cached

# 查看两个提交之间的差异
git diff commit_id_1 commit_id_2

# 查看某个文件的具体差异
git diff -- src/cart.js

# 查看某个分支和当前分支的差异
git diff main..feature/cart
```

**企业真实操作示例**：
```bash
# 提交前确认自己改了哪些内容
git diff --staged
# 输出示例：
# diff --git a/src/cart.js b/src/cart.js
# --- a/src/cart.js
# +++ b/src/cart.js
# @@ -15,6 +15,10 @@
#  function calculateTotal(items) {
# -    return items.reduce((sum, item) => sum + item.price, 0);
# +    return items.reduce((sum, item) => {
# +        const discount = item.coupon ? item.coupon.discount : 1;
# +        return sum + item.price * discount;
# +    }, 0);
#  }
# 解读：- 号开头的行是被删掉的旧代码，+ 号开头的行是新增的代码
```

#### 【企业场景测验题】

**题目 1（实操题）**：你已经 `git add` 了几个文件，提交前想最后确认一下暂存区里的改动内容是否正确。应该用什么命令？

**标准答案**：
```bash
git diff --staged
```

**解析**：`git diff` 不带参数只能看工作区和暂存区的差异（还没 add 的改动）。已经 add 进暂存区的改动，需要用 `git diff --staged` 或 `git diff --cached` 才能看到。这是企业里提交前「最后一道检查」的标准操作。

---

## 第三章：远程仓库协作（GitHub / Gitee / GitLab，企业多人协作基础）

> **前置条件**：在开始本章之前，你需要先在 GitHub（https://github.com）、Gitee（https://gitee.com）或 GitLab 上注册一个账号。

### 3.1 SSH 密钥配置（企业必用）

#### 【企业场景用途】

在公司里，你每天要 push 和 pull 代码几十次。如果用 HTTPS 方式连接远程仓库，每次都要输入用户名和密码（或者 token），非常烦。SSH 密钥就像给你的电脑和远程仓库之间建立了一条**免密通道**，配置一次之后，以后所有操作都不需要再输密码了。

#### 【通俗比喻讲解】

SSH 密钥就像你给自己配了一把**专属指纹锁**。你在远程仓库那边登记你的指纹（公钥），以后你的电脑（私钥）每次去仓库取东西或放东西，系统自动验证指纹，不用每次都输密码。

#### 【完整操作步骤】

```bash
# 第一步：生成 SSH 密钥对（包含一个私钥和一个公钥）
ssh-keygen -t ed25519 -C "你的公司邮箱"
# 如果系统不支持 ed25519，用 RSA 也行：
# ssh-keygen -t rsa -b 4096 -C "你的公司邮箱"

# 执行后会提示你选择保存路径和设置密码，直接回车用默认值即可
# 生成成功后会显示：
# Your identification has been saved in /Users/xxx/.ssh/id_ed25519      ← 私钥（绝对不能给别人！）
# Your public key has been saved in /Users/xxx/.ssh/id_ed25519.pub      ← 公钥（放到远程仓库上的）

# 第二步：查看并复制公钥
cat ~/.ssh/id_ed25519.pub
# 复制输出的全部内容（以 ssh-ed25519 开头的一整行）

# 第三步：在远程仓库平台添加公钥
# GitHub：Settings → SSH and GPG keys → New SSH key → 粘贴公钥
# Gitee：设置 → SSH公钥 → 粘贴公钥
# GitLab：Preferences → SSH Keys → 粘贴公钥

# 第四步：测试 SSH 连接是否成功
ssh -T git@github.com
# 成功输出：Hi 你的用户名! You've successfully authenticated...
```

> **企业安全提醒**：私钥文件（`id_ed25519`）就是你的「身份证」，**绝对不能分享给任何人、不能提交到 Git 仓库、不能上传到任何公开平台**。如果私钥泄露，别人可以冒充你访问所有你有权限的代码仓库。

#### HTTPS 与 SSH 企业选型区别

| 对比维度 | HTTPS | SSH |
|---------|-------|-----|
| 连接地址示例 | `https://github.com/user/repo.git` | `git@github.com:user/repo.git` |
| 认证方式 | 用户名 + 密码/Token | SSH 密钥对 |
| 是否需要每次输密码 | 是（除非配置了 credential helper） | 否（配置一次永久免密） |
| 企业推荐度 | 低（临时用、CI/CD 场景） | **高（日常开发标配）** |
| 防火墙穿透 | 好（走 443 端口） | 可能被公司防火墙拦截（走 22 端口） |

### 3.2 git clone — 克隆远程仓库到本地

#### 【企业场景用途】

你入职公司的第一天，领导给你的第一件事就是：「把项目代码 clone 下来。」`git clone` 是把远程仓库的**完整代码和所有历史记录**下载到你电脑上，同时在本地自动创建一个和远程仓库一模一样的 Git 仓库。

#### 【通俗比喻讲解】

`git clone` 就像把公司云端网盘里的**整个项目保险柜连同里面所有存档**复制到你的电脑上。你不仅拿到了最新的代码，还拿到了从项目创建以来的所有历史记录。以后你断网也能查看历史、提交代码。

#### 【完整语法 + 企业常用参数】

```bash
# 克隆远程仓库（SSH 方式，企业推荐）
git clone git@github.com:company/project-name.git

# 克隆到指定文件夹
git clone git@github.com:company/project-name.git my-project

# 只克隆最近 1 次提交的历史（浅克隆，项目历史很长时节省时间和空间）
git clone --depth 1 git@github.com:company/project-name.git

# 克隆指定分支
git clone -b dev git@github.com:company/project-name.git
```

**企业真实操作示例**：
```bash
# 入职第一天，克隆公司的后端项目
git clone git@gitlab.company.com:backend/order-service.git
cd order-service
# 此时你已经有了完整的项目代码和 Git 历史，可以直接开始开发
```

#### 【企业场景测验题】

**题目 1（实操题）**：公司 GitLab 上有一个项目，SSH 地址是 `git@gitlab.company.com:frontend/admin-dashboard.git`，你需要克隆到本地并开始开发。写出完整命令。

**标准答案**：
```bash
git clone git@gitlab.company.com:frontend/admin-dashboard.git
cd admin-dashboard
```

---

### 3.3 git remote — 管理远程仓库连接

#### 【企业场景用途】

`git clone` 下来的项目已经自动连接了远程仓库，但有时候你需要查看连接的是哪个远程地址、或者项目迁移了仓库地址需要更新、或者你需要同时关联多个远程仓库（比如一个公司仓库 + 一个开源镜像仓库）。

#### 【通俗比喻讲解】

`git remote` 就像管理你的**云端网盘地址簿**。你可以查看当前连接了哪些网盘、添加新的网盘地址、修改旧地址、删除不用的地址。

#### 【完整语法 + 企业常用参数】

```bash
# 查看当前关联的远程仓库名称（一般 clone 下来默认叫 origin）
git remote

# 查看远程仓库的详细地址（推荐，能看到完整的 URL）
git remote -v
# 输出示例：
# origin  git@github.com:company/project.git (fetch)
# origin  git@github.com:company/project.git (push)

# 添加一个新的远程仓库
git remote add upstream git@github.com:original-author/project.git

# 修改远程仓库地址（比如项目迁移了新仓库）
git remote set-url origin git@github.com:company/new-project.git

# 删除一个远程仓库连接
git remote remove upstream
```

> **企业知识补充**：`origin` 是你自己仓库的默认名字（你自己 fork 的或者 clone 的），`upstream` 通常指上游原始仓库（比如你 fork 了别人的开源项目，`upstream` 指向原作者的仓库）。这个命名约定在团队协作中很重要。

#### 【企业场景测验题】

**题目 1（选择题）**：你执行 `git remote -v` 后看到：

```
origin  git@github.com:zhangsan/company-project.git (fetch)
origin  git@github.com:zhangsan/company-project.git (push)
```
这说明什么？

A. 你的本地仓库没有连接任何远程仓库
B. 你的本地仓库连接了一个名为 origin 的远程仓库，地址是 zhangsan 的 fork 仓库
C. 你的代码已经推送到了公司主仓库
D. origin 是公司主仓库的默认名称

**标准答案**：B

**解析**：`origin` 是远程仓库的别名，这里指向 `zhangsan` 的 GitHub 仓库（很可能是从公司主仓库 fork 出来的个人副本）。在企业协作中，你通常需要先 push 到自己的 origin，再通过 MR/PR 合并到公司主仓库。

---

### 3.4 git push — 推送本地代码到远程仓库

#### 【企业场景用途】

你在本地写完代码、commit 之后，需要把代码**推送到远程仓库**，这样团队其他成员才能看到你的代码、拉取你的改动。`git push` 是让你的代码「上线」到共享仓库的关键一步。

#### 【通俗比喻讲解】

`git push` 就像把你保险柜里的存档**复制到公司的云端共享网盘上**。推送成功后，团队里所有人都能在网盘上看到你新存的内容了。

#### 【完整语法 + 企业常用参数】

```bash
# 推送当前分支到远程仓库（最常用）
git push

# 第一次推送新分支时，需要关联远程分支（设置上游跟踪）
git push -u origin feature/cart
# -u 等同于 --set-upstream，意思是「把本地的 feature/cart 分支和远程的 feature/cart 分支关联起来」
# 设置一次之后，以后在这个分支上直接 git push 就行了

# 推送指定分支到远程
git push origin feature/cart

# 推送所有本地分支到远程
git push --all

# 推送所有标签到远程
git push --tags
```

> **🚨 企业红线 — 绝对禁止的命令**：
>
> ```bash
> git push --force        # 或 git push -f
> git push --force-with-lease
> ```
> 强制推送会用你本地的代码**直接覆盖远程仓库的代码**，同事推送到远程的改动会被你抹掉！这在企业里属于**开除级事故**，只有在极少数场景（比如个人 fork 的分支、rebase 后自己的 feature 分支）才可以使用，**绝对禁止在 main/master/dev 等公共分支上使用**。

#### 【企业场景测验题】

**题目 1（实操题）**：你在本地创建了一个新功能分支 `feature/user-login`，开发完并 commit 后，需要推送到远程仓库并建立关联。请写出命令。

**标准答案**：
```bash
git push -u origin feature/user-login
```

**解析**：新分支第一次推送时需要 `-u` 参数建立上下游关联，之后就可以直接 `git push` 了。

---

**题目 2（选择题）**：同事告诉你他推送了新代码到 dev 分支，你也改了 dev 分支的代码并 commit 了。你直接执行 `git push` 会怎样？

A. 你的代码会自动和远程的代码合并
B. 推送成功，但会覆盖同事的代码
C. 推送被拒绝（rejected），Git 提示你先 pull 再 push
D. 你的代码会自动创建一个新分支

**标准答案**：C

**解析**：当远程分支有你本地没有的新提交时，Git 会拒绝你的 push，提示你先 `git pull` 拉取远程的最新代码、解决可能的冲突后再 push。这是 Git 的安全机制，防止你无意中覆盖别人的代码。

---

### 3.5 git pull — 拉取远程代码并合并到本地

#### 【企业场景用途】

在公司里，你每天早上到工位第一件事就是 `git pull`，把同事们昨天推送的新代码拉到你电脑上。不然你基于旧代码开发，最后合并的时候冲突会非常多。`git pull` = `git fetch`（下载）+ `git merge`（合并），一步完成下载和合并。

#### 【通俗比喻讲解】

`git pull` 就像你打开公司的云端共享网盘，**把同事新上传的文件下载到你电脑上，并自动合并到你的工作文件夹里**。如果有文件你和同事都改了，系统会提醒你「这里冲突了，你来看看保留谁的」。

#### 【完整语法 + 企业常用参数】

```bash
# 拉取当前分支的远程更新并合并（最常用）
git pull

# 拉取指定远程仓库的指定分支
git pull origin dev

# 用 rebase 方式拉取（保持提交历史整洁，企业推荐！）
git pull --rebase origin dev
```

> **企业建议**：很多团队推荐用 `git pull --rebase` 代替普通的 `git pull`，因为它不会产生多余的合并提交记录，让提交历史更干净。但这个看团队规范，有的团队要求用普通 `git pull`，有的要求用 `--rebase`。

#### 【企业场景测验题】

**题目 1（实操题）**：你正在 `feature/cart` 分支开发购物车功能，同事提醒你 dev 分支有更新，你需要把 dev 的最新代码拉下来合并到你的分支。写出操作步骤。

**标准答案**：
```bash
# 确保当前在 feature/cart 分支
git checkout feature/cart

# 拉取 dev 分支的最新代码并合并到当前分支
git pull origin dev
# 如果有冲突，解决冲突后再 commit 和 push
```

**解析**：在功能分支上定期拉取 dev 的最新代码，可以提前发现和解决冲突，避免最后合并时冲突堆积。企业里建议每天至少拉取一次 dev 分支。

---

### 3.6 git fetch — 仅下载远程更新（不合并）

#### 【企业场景用途】

`git fetch` 和 `git pull` 的区别是：`git fetch` **只下载不合并**。在企业里，当你想先看看远程有什么新变化、但不想立刻合并到你的代码里时，用 `git fetch`。比如你想先 review 一下同事推送的代码再决定是否合并。

#### 【通俗比喻讲解】

`git pull` 是把网盘的新文件下载下来**直接塞进你的文件夹**（下载 + 合并）。`git fetch` 是把网盘的新文件下载到你电脑的一个**临时文件夹**里，你可以先看看都有什么，确认没问题了再手动合并到你的文件夹。

#### 【完整语法 + 企业常用参数】

```bash
# 下载所有远程仓库的更新
git fetch

# 下载指定远程仓库的更新
git fetch origin

# 下载指定分支的更新
git fetch origin dev

# fetch 后查看远程分支和本地分支的差异（先看再决定是否合并）
git diff HEAD..origin/dev

# 确认没问题后，手动合并
git merge origin/dev
```

**git pull vs git fetch 对比表**：

| 对比维度 | git pull | git fetch |
|---------|----------|-----------|
| 本质 | fetch + merge（下载 + 自动合并） | 只下载，不合并 |
| 对工作区的影响 | 直接修改你的工作区代码 | 不影响你的工作区 |
| 安全性 | 可能产生冲突，需要立刻处理 | 完全安全，你可以慢慢看 |
| 企业使用场景 | 日常开发，确定要合并时用 | 想先 review 远程变化再决定是否合并 |

---

## 第四章：分支管理（企业开发核心，重中之重）

### 4.1 分支底层原理比喻

在前面的章节中，我们已经用「复印草稿」来比喻过分支。这里再深入理解一下：

Git 的分支其实就是一个**指向某次提交的标签（指针）**。它不是一个完整的代码副本（SVN 才是），所以 Git 创建和切换分支的速度极快——本质上就是移动一个标签而已。

```
主线（main）:  A → B → C → D（最新提交）
                              ↑ main 指针在这里

你在 C 点创建 feature 分支：
主线（main）:  A → B → C → D
                              ↑ main

功能分支:      A → B → C → E → F
                                  ↑ feature/cart 指针在这里
```

C 是分支创建的起点，E 和 F 是你在功能分支上新的提交，D 是主线上的新提交。两条线互不干扰。

### 4.2 企业标准分支规范（GitFlow 简易版）

在正规企业里，不是所有代码都直接往 main 分支上写的。一套标准的分支规范通常包含以下 5 种分支：

| 分支名称 | 生命周期 | 企业用途 | 谁有权限推送 |
|---------|---------|---------|------------|
| `main` / `master` | 永久 | **生产环境代码**，始终保持可上线的稳定状态 | 只有 Tech Lead / 运维通过 MR 合并 |
| `dev` / `develop` | 永久 | **开发主分支**，所有人日常开发合并到这里 | 通过 MR 合并，不允许直接 push |
| `feature/xxx` | 临时 | **功能分支**，从 dev 创建，开发完合并回 dev | 开发者个人分支，自由 push |
| `hotfix/xxx` | 临时 | **紧急修复分支**，从 main 创建，修复完合并回 main 和 dev | 高级开发者处理线上紧急 Bug |
| `release/x.x` | 临时 | **上线准备分支**，从 dev 创建，做上线前最后测试 | 测试团队 / 运维 |

**企业分支工作流程简述**：

```
1. 你从 dev 分支创建 feature/xxx 分支 → 开发新功能
2. 功能开发完 → 合并回 dev 分支 → 测试团队在 dev 上测试
3. 测试通过 → 从 dev 创建 release 分支 → 做上线前检查
4. release 确认没问题 → 合并到 main → 打 tag → 部署上线
5. 线上出 Bug → 从 main 创建 hotfix 分支 → 修完合并回 main 和 dev
```

### 4.3 git branch — 查看、创建、删除分支

#### 【企业场景用途】

领导给你分配了一个新需求「开发购物车模块」，你第一件事就是用 `git branch` 创建功能分支。开发完了合并后，用 `git branch -d` 删掉功能分支保持仓库整洁。日常也要经常用 `git branch` 看看当前在哪个分支上、有哪些分支。

#### 【通俗比喻讲解】

`git branch` 就像管理你的**草稿副本**。你可以列出所有草稿（查看分支列表）、新建一份草稿来写新内容（创建分支）、写完了把草稿销毁（删除分支）。

#### 【完整语法 + 企业常用参数】

```bash
# 查看本地所有分支（当前分支前面会有 * 标记）
git branch
# 输出示例：
#   dev
# * feature/cart     ← 星号表示当前所在分支
#   main

# 查看所有分支（包括远程分支）
git branch -a

# 创建新分支（不切换过去）
git branch feature/cart

# 删除已合并的分支（安全删除，如果分支没合并会报错阻止你）
git branch -d feature/cart

# 强制删除分支（⚠️ 即使没合并也删掉，改动会丢失！）
git branch -D feature/cart
```

### 4.4 git checkout / git switch — 切换分支

#### 【企业场景用途】

你正在 `feature/cart` 分支开发购物车，领导突然说线上有个紧急 Bug 要你修。你需要立刻切换到 `hotfix/urgent-bug` 分支去修 Bug，修完再切回 `feature/cart` 继续开发。`git checkout` 和 `git switch` 就是用来切换分支的。

#### 【通俗比喻讲解】

切换分支就像**换一张书桌工作**。你从「购物车书桌」站起来，走到「紧急修复书桌」继续工作。每张书桌上的稿子都保持着你上次离开时的样子，互不影响。

#### 【完整语法 + 企业常用参数】

```bash
# 切换到已有分支（老命令）
git checkout feature/cart

# 切换到已有分支（新命令，Git 2.23+ 推荐）
git switch feature/cart

# 创建并切换到新分支（一步完成创建+切换，企业最常用！）
git checkout -b feature/cart
# 或者（推荐新写法）
git switch -c feature/cart

# 切回上一个分支（类似"后退"按钮）
git switch -
# 或
git checkout -
```

> **checkout vs switch 区别**：`git checkout` 是个老命令，既能切换分支又能恢复文件，功能混杂容易误操作。`git switch` 是 Git 2.23 引入的新命令，专门用来切换分支，更安全清晰。新项目中推荐用 `git switch`，但老项目中很多人还是习惯用 `git checkout`，两个都要会。

#### 【企业场景测验题】

**题目 1（实操题）**：你正在 dev 分支上，现在要开发「用户评论」功能。请写出从创建功能分支到切换过去的完整命令（用推荐的新写法）。

**标准答案**：
```bash
git switch -c feature/user-comment
```

**解析**：`git switch -c` 一步完成创建分支 + 切换分支，是最高效的做法。分支命名遵循企业规范 `feature/功能名`。

---

**题目 2（选择题）**：你在 `feature/login` 分支上改了 3 个文件但还没提交，领导让你紧急切到 `hotfix/payment` 分支修 Bug。你应该怎么做？

A. 直接 `git switch hotfix/payment`，Git 会自动处理
B. 先 `git stash` 暂存改动，再 `git switch hotfix/payment`，修完 Bug 后切回来 `git stash pop` 恢复
C. 先把没写完的代码 commit 了再切换
D. 关掉当前项目，重新 clone 一份

**标准答案**：B（最佳实践）

**解析**：如果改动的文件和目标分支没有冲突，A 也能成功（Git 会带着未提交的改动一起切换）。但这不是好的做法，容易混乱。B 的 `git stash` 是标准操作——把没完成的改动临时「存起来」，切完分支修完 Bug 再「取出来」继续写。C 的问题是把没写完的代码提交了会污染提交历史。

---

### 4.5 git merge — 合并分支

#### 【企业场景用途】

你在 `feature/cart` 分支上把购物车功能开发完了、测试通过了，现在需要把这些改动合并回 `dev` 分支，让团队其他人也能用到你的代码。这就是 `git merge` 的用途。

#### 【通俗比喻讲解】

`git merge` 就像把你**草稿上写得好的新章节，正式抄写到主稿里**。抄完之后，主稿就包含了你的新内容。如果主稿和草稿有同一段落的不同版本，系统会提醒你冲突了，让你自己决定保留哪个版本。

#### 【完整语法 + 企业常用参数】

```bash
# 把指定分支合并到当前分支
# 操作步骤：先切到目标分支，再执行 merge
git switch dev
git merge feature/cart

# 合并时保留分支合并记录（产生一个合并提交节点，企业推荐）
git merge --no-ff feature/cart

# 如果合并出现冲突，解决冲突后继续合并
git add .
git commit -m "merge: 合并购物车功能分支到 dev"
```

> **`--no-ff` 说明**：不加这个参数时，如果目标分支没有新提交，Git 会做「快进合并」（fast-forward），直接把指针移过去，看不出曾经有过分支。加了 `--no-ff` 会强制创建一个合并提交，保留分支的痕迹，方便以后追溯。企业里一般推荐加 `--no-ff`。

### 4.6 git rebase — 变基合并（保持历史整洁）

#### 【企业场景用途】

`rebase` 是另一种合并分支的方式。和 `merge` 不同的是，`rebase` 会把你分支上的提交「摘下来」，放到目标分支的最新提交后面，让提交历史变成一条直线，没有分叉。很多团队偏好 `rebase`，因为提交历史更干净。

#### 【通俗比喻讲解】

`merge` 是把两条河流汇合，交汇处有个分叉记录；`rebase` 是把你的河流**整体平移**到另一条河的下游，看起来就像你一直在同一条河上走，没有分叉。

#### 【完整语法 + 企业常用参数】

```bash
# 在当前分支上，把你的提交变基到目标分支的最新位置
# 操作步骤：在你的功能分支上执行 rebase
git switch feature/cart
git rebase dev

# 如果 rebase 过程中有冲突
# 1. 手动解决冲突文件
# 2. git add 解决后的文件
# 3. git rebase --continue 继续
# 如果搞砸了想放弃 rebase
git rebase --abort
```

**merge vs rebase 企业选型对比（重要！）**：

| 对比维度 | git merge | git rebase |
|---------|-----------|------------|
| 提交历史 | 保留分叉，有合并记录 | 直线历史，没有分叉 |
| 安全性 | 不改写历史，安全 | **改写提交历史**，有风险 |
| 冲突处理 | 一次性解决所有冲突 | 可能需要逐个提交解决冲突 |
| 适用场景 | 合并功能分支到 dev/main | 更新功能分支（把 dev 的最新代码同步到你的分支） |
| **企业红线** | 安全，无限制 | **🚨 绝对禁止对已 push 的公共分支执行 rebase！** |

> **🚨 企业红线**：`git rebase` 会改写提交历史（改变 commit hash），如果你对已经 push 到远程的公共分支（如 dev、main）执行 rebase，其他人拉取代码时会冲突严重。**rebase 只应该用在本地还没 push 的分支上，或者只有你自己在用的 feature 分支上。**

#### 【企业场景测验题】

**题目 1（选择题）**：你在 `feature/cart` 分支上开发了 3 天，期间 dev 分支上同事也推送了很多新代码。你想把 dev 的最新代码同步到你的分支，同时保持提交历史整洁。以下哪种做法最合适？

A. `git merge dev` — 在自己的分支上合并 dev
B. `git rebase dev` — 把自己的分支变基到 dev 最新位置
C. `git push --force` 覆盖 dev
D. 删除自己的分支重新创建

**标准答案**：B

**解析**：在自己的 feature 分支上执行 `git rebase dev`，可以把你的 3 天提交「平移」到 dev 的最新位置后面，历史变成一条直线。选项 A 也可以，但会产生一个合并提交，历史不够干净。C 和 D 都是危险操作或浪费工作。

---

### 4.7 git tag — 版本标签管理

#### 【企业场景用途】

当项目上线一个正式版本时，企业里会在 Git 上给这个版本打一个标签（tag），比如 `v1.0.0`、`v2.1.3`。以后如果需要回滚到这个版本、或者查看某个版本发布时的代码状态，直接通过 tag 就能找到。这就像游戏的「存档点命名」——`v1.0.0` 就是「第一版上线存档」。

#### 【通俗比喻讲解】

`git tag` 就像给保险柜里的某次存档**贴上一个标签**。标签不是新的存档，只是给已有存档起了一个好记的名字。以后你一看标签就知道：「v1.0.0 就是第一次上线的那个版本」。

#### 【完整语法 + 企业常用参数】

```bash
# 查看所有标签
git tag

# 在当前提交上创建轻量标签
git tag v1.0.0

# 创建附注标签（企业推荐！包含作者、日期、说明信息）
git tag -a v1.0.0 -m "Release v1.0.0: 首个正式版本上线"

# 给历史提交打标签（用 commit hash）
git tag -a v0.9.0 -m "Beta 测试版本" a3b5c2d

# 推送标签到远程仓库
git push origin v1.0.0          # 推送单个标签
git push --tags                  # 推送所有标签

# 删除标签
git tag -d v1.0.0               # 删除本地标签
git push origin --delete v1.0.0  # 删除远程标签

# 查看某个标签对应的代码状态
git show v1.0.0
```

**企业版本号规范（语义化版本 SemVer）**：

| 版本号格式 | 含义 | 示例 |
|-----------|------|------|
| `v主版本.次版本.修订号` | 主版本：不兼容的 API 变更；次版本：新增功能；修订号：Bug 修复 | `v2.1.3` 表示第 2 大版的第 1 个新功能版本，修了 3 个 Bug |

#### 【企业场景测验题】

**题目 1（实操题）**：项目上线了 v2.0.0 版本，你需要在当前提交上创建一个附注标签并推送到远程仓库。写出完整命令。

**标准答案**：
```bash
git tag -a v2.0.0 -m "Release v2.0.0: 全新架构重构版本"
git push origin v2.0.0
```

---

## 第五章：冲突处理、代码回滚（企业保命技能，防止线上事故）

### 5.1 代码冲突产生原因与标准解决流程

#### 什么是冲突？

当两个人（或你自己两次操作）修改了**同一个文件的同一行代码**时，Git 不知道该保留哪个版本，就会暂停合并，把决定权交给你——这就是冲突（Conflict）。

**比喻**：你和同事都在改公司宣传册的第 3 页标题。你改成了「科技引领未来」，同事改成了「创新驱动发展」。打印机不知道该印哪个，就把两份都放在你面前说：「你俩自己商量用哪个。」

#### 企业多人开发冲突标准解决流程

```
第一步：拉取最新代码
git pull origin dev
# Git 提示：CONFLICT (content): Merge conflict in src/cart.js

第二步：打开冲突文件，找到冲突标记
# 你会在文件中看到这样的标记：
<<<<<<< HEAD
你的代码写在这里（你本地的版本）
=======
同事的代码写在这里（远程拉下来的版本）
>>>>>>> origin/dev

第三步：手动编辑，决定保留哪个版本（或合并两者的代码）
# 删掉 <<<<<<< ======= >>>>>>> 这些标记，只保留最终要用的代码

第四步：标记冲突已解决
git add src/cart.js

第五步：完成合并提交
git commit -m "merge: 解决购物车模块代码冲突"

第六步：推送到远程
git push origin dev
```

> **企业提醒**：解决冲突时要和改那段代码的同事沟通，确认保留谁的版本、或者怎么合并。不要自己擅自删掉别人的代码，否则可能引入 Bug。

### 5.2 git revert — 创建反向提交来撤销更改（✅ 企业线上安全命令）

#### 【企业场景用途】

线上代码出了问题，你需要撤销某次提交引入的改动。`git revert` 是最安全的方式——它不会删除历史，而是**创建一个新的提交，内容刚好和你想撤销的那次提交相反**。就像你写了一封信寄出去了（已经 push），你不能假装没写过（reset），但你可以再写一封信说「上一封信的内容作废」（revert）。

#### 【通俗比喻讲解】

`git revert` 就像你在档案记录里**追加一条「撤销声明」**：「第 XX 号存档的内容作废，以下是正确的版本。」原来的存档还在记录册里（历史不丢），但你新增了一条纠正记录。

#### 【完整语法 + 企业常用参数】

```bash
# 撤销某次提交（创建一个新的反向提交）
git revert commit_hash
# 示例：git revert a3b5c2d

# 撤销某次提交但不自动生成提交（让你手动检查后再提交）
git revert --no-commit commit_hash
# 检查无误后手动提交：
git commit -m "revert: 撤销 a3b5c2d 提交的错误修改"

# 撤销最近一次提交
git revert HEAD

# 撤销一个合并提交（需要指定保留哪个分支的代码，-m 1 表示保留主分支）
git revert -m 1 merge_commit_hash
```

### 5.3 git reset vs git revert — 企业使用边界（必须区分！）

| 对比维度 | git reset | git revert |
|---------|-----------|------------|
| 原理 | 把提交历史往回**倒退**，仿佛那些提交不存在 | 在历史末尾**追加**一个反向提交来抵消 |
| 历史记录 | **改写历史**，被撤销的提交从记录中消失 | **保留历史**，所有提交都在 |
| 对远程的影响 | 如果已 push，会导致本地和远程不一致 | 安全，推送后远程也正常 |
| 企业使用场景 | **仅限本地未 push 的提交** | **线上代码回滚的标准方式** |
| 风险等级 | 🔴 高（可能丢失代码） | 🟢 低（安全可追溯） |

> **企业铁律**：
> - 本地还没 push 的提交搞砸了 → 用 `git reset`
> - 已经 push 到远程的提交要撤销 → **必须用 `git revert`**，绝对不用 `git reset`

### 5.4 git cherry-pick — 摘取指定提交（精准补丁）

#### 【企业场景用途】

在 dev 分支上有很多提交，但其中只有某一个提交修复的 Bug 需要紧急上线到 main 分支。你不需要把整个 dev 合并过去（太多没测完的功能），只需要把那一个提交「摘」过来应用到 main 上。这就是 `cherry-pick`（樱桃采摘）——从一堆提交中精准挑出你需要的那一颗「樱桃」。

#### 【通俗比喻讲解】

`cherry-pick` 就像你同事在 dev 分支上写了一堆代码（10 个提交），其中只有第 7 个提交修了一个紧急 Bug。你只需要把第 7 个提交**单独复制**到 main 分支上，其他的都不要。

#### 【完整语法 + 企业常用参数】

```bash
# 摘取指定提交到当前分支
git cherry-pick commit_hash
# 示例：git cherry-pick a3b5c2d

# 摘取多个提交
git cherry-pick commit_hash_1 commit_hash_2

# 摘取一个提交范围（不包含 start，包含 end）
git cherry-pick start_hash..end_hash

# 如果 cherry-pick 有冲突
# 解决冲突后：
git add .
git cherry-pick --continue
# 想放弃：
git cherry-pick --abort
```

#### 【企业场景测验题】

**题目 1（实操题）**：线上 main 分支发现支付金额计算有 Bug。同事在 dev 分支的提交 `f8e2a1b` 中已经修复了这个问题，但 dev 分支还有很多其他没测完的功能不能合并到 main。你该怎么操作？

**标准答案**：
```bash
# 切换到 main 分支
git switch main

# 拉取最新代码
git pull origin main

# 精准摘取修复 Bug 的那个提交
git cherry-pick f8e2a1b

# 推送到远程 main 分支
git push origin main
```

**解析**：`cherry-pick` 是企业里处理「只需要某个提交，不需要整个分支合并」场景的标准操作。这在线上紧急修复时非常常见。

---

**题目 2（选择题）**：线上版本 v1.2.0 上线后发现某个功能有严重 Bug，需要回滚。该版本是 3 天前通过一次 merge 合并到 main 的。以下哪种做法最安全？

A. `git reset --hard HEAD~5` 回退 5 次提交
B. `git revert -m 1 merge_commit_hash` 创建反向合并提交
C. 手动把有问题的代码删掉再提交
D. `git push --force` 强制覆盖远程 main

**标准答案**：B

**解析**：线上代码回滚必须用 `git revert`，它会保留完整历史并创建一条新的撤销记录。`-m 1` 是因为 revert 合并提交时需要指定保留哪一侧的代码。A 和 D 在线上环境是绝对禁止的，C 手动操作容易出错且不可追溯。

---

## 第六章：企业完整标准开发工作流（端到端实战流程）

本章完整模拟一个公司从接到需求到上线的标准 Git 操作全流程，假设你接到了一个「开发商品收藏功能」的需求。

### 6.1 完整流程演示

**第一步：确保本地代码是最新的**

```bash
# 切到 dev 分支
git switch dev

# 拉取最新代码
git pull origin dev
```

**第二步：从 dev 创建功能分支**

```bash
# 创建并切换到功能分支（命名规范：feature/功能描述）
git switch -c feature/product-favorite
```

**第三步：开发功能，频繁小步提交**

```bash
# 开发完「收藏按钮 UI」
git add src/favorite/FavoriteButton.vue
git commit -m "feat: 新增商品收藏按钮组件"

# 开发完「收藏接口对接」
git add src/favorite/favoriteApi.js src/api/index.js
git commit -m "feat: 对接商品收藏/取消收藏后端接口"

# 开发完「收藏列表页面」
git add src/favorite/FavoriteList.vue src/router/index.js
git commit -m "feat: 新增我的收藏列表页面及路由配置"
```

**第四步：定期拉取 dev 分支的最新代码（防止最后合并时冲突堆积）**

```bash
# 方式一：rebase 方式（推荐，历史更干净）
git pull --rebase origin dev

# 方式二：merge 方式
git pull origin dev

# 如果有冲突 → 解决冲突 → git add → git rebase --continue（或 git commit）
```

**第五步：推送功能分支到远程仓库**

```bash
# 第一次推送需要 -u 关联远程分支
git push -u origin feature/product-favorite

# 后续每次开发完直接 git push 即可
```

**第六步：提交 MR/PR 合并申请（代码评审 CR）**

在 GitLab/GitHub 上操作：
1. 进入项目页面 → 点击「New Merge Request」（GitLab）或「New Pull Request」（GitHub）
2. 源分支选择 `feature/product-favorite`，目标分支选择 `dev`
3. 填写 MR 标题和描述：标题如「feat: 新增商品收藏功能」，描述包含改了什么、怎么测试、有无风险点
4. 指定 Code Reviewer（代码评审人，通常是你的 Tech Lead 或同事）
5. 等待评审通过 → 评审人提出修改意见 → 你修改后再 push → 评审人再次审核 → 通过

> **企业提醒**：MR/PR 是企业开发中**必不可少的流程**。没有经过代码评审的代码不允许合并到公共分支。评审不仅检查代码质量，也是团队知识共享的方式。

**第七步：合并到 dev 分支，测试团队测试**

```bash
# MR 评审通过后，由评审人或你在 GitLab/GitHub 页面上点击「Merge」
# 或者命令行操作：
git switch dev
git merge --no-ff feature/product-favorite
git push origin dev

# 合并后删除功能分支（保持仓库整洁）
git branch -d feature/product-favorite
git push origin --delete feature/product-favorite    # 删除远程功能分支
```

**第八步：测试通过后，合并到 main 分支上线**

```bash
git switch main
git pull origin main
git merge --no-ff dev
git push origin main

# 打版本标签
git tag -a v1.3.0 -m "Release v1.3.0: 新增商品收藏功能"
git push origin v1.3.0
```

**第九步：线上出现 Bug，紧急修复（hotfix 流程）**

```bash
# 从 main 创建 hotfix 分支
git switch main
git pull origin main
git switch -c hotfix/favorite-crash

# 修复 Bug
git add src/favorite/FavoriteList.vue
git commit -m "fix: 修复收藏列表在 iOS Safari 上的崩溃问题"

# 合并回 main
git switch main
git merge --no-ff hotfix/favorite-crash
git push origin main
git tag -a v1.3.1 -m "Hotfix v1.3.1: 修复 iOS 收藏列表崩溃"
git push origin v1.3.1

# 同步合并回 dev（确保 dev 也有这个修复）
git switch dev
git merge --no-ff hotfix/favorite-crash
git push origin dev

# 删除 hotfix 分支
git branch -d hotfix/favorite-crash
```

### 6.2 企业常见违规操作提醒

| 违规操作 | 后果 | 正确做法 |
|---------|------|---------|
| 直接在 main 分支上开发 | 未测试的代码直接影响生产环境 | 创建 feature 分支开发 |
| 不提交 MR 直接 merge 到 dev | 代码质量无人把关，Bug 率高 | 必须走 MR/PR 评审 |
| 一周才提交一次 | 提交粒度太粗，出问题难以定位 | 每天至少提交一次，小步提交 |
| 不拉取 dev 就合并 | 冲突堆积，最后合并时大量冲突 | 每天拉取 dev 同步 |
| 功能分支用完不删 | 仓库分支越来越多，混乱 | 合并后及时删除本地和远程分支 |
| commit message 乱写 | 排查问题时找不到线索 | 遵循 `feat/fix/docs` 前缀规范 |

---

## 第七章：企业 Git 强制规范 & 高危红线（工作必看，避免开除级事故）

### 7.1 Commit 提交信息规范

企业统一使用以下规范模板，每条 commit 必须包含**类型前缀**和**简洁描述**：

```
<类型>(<作用域>): <简短描述>

<可选的详细说明>
```

| 类型前缀 | 含义 | 正确示例 | 错误示例 |
|---------|------|---------|---------|
| `feat` | 新功能 | `feat: 新增商品收藏功能` | `加了个功能` |
| `fix` | 修复 Bug | `fix: 修复登录验证码60秒倒计时不生效` | `修了个bug` |
| `docs` | 文档修改 | `docs: 更新部署说明文档` | `改了文档` |
| `style` | 代码格式（不影响逻辑） | `style: 统一缩进风格为4空格` | `调了下格式` |
| `refactor` | 重构（不改功能） | `refactor: 提取公共请求工具函数` | `重构` |
| `perf` | 性能优化 | `perf: 优化列表查询SQL减少50%耗时` | `优化了一下` |
| `test` | 测试相关 | `test: 补充用户注册接口单元测试` | `加了测试` |
| `chore` | 构建/工具链 | `chore: 升级 Node 版本至 18.x` | `更新依赖` |
| `revert` | 回滚提交 | `revert: 撤销 feat: 新增商品收藏功能` | `撤回` |

**反面案例**：
```
# ❌ 这些 commit message 在企业里会被 Tech Lead 打回来重写：
git commit -m "update"
git commit -m "fix bug"
git commit -m "..."
git commit -m "111"
git commit -m "今天的代码"
git commit -m "asdfasdf"
```

### 7.2 绝对禁止的高危操作（红线中的红线）

**🚨 红线一：禁止直接 push 到 main/master 分支**

```bash
# ❌ 绝对禁止：
git push origin main

# 正确做法：通过 MR/PR 合并
```
**原因**：main 分支是生产环境代码，直接 push 绕过了代码评审，可能导致未测试的代码上线。

**🚨 红线二：禁止强制推送覆盖远程代码**

```bash
# ❌ 绝对禁止（在公共分支上）：
git push --force origin dev
git push --force origin main
git push -f origin dev

# 后果：你本地的代码会直接覆盖远程仓库，同事推送的代码全部丢失！
# 这是最常见的「开除级事故」之一。
```
**唯一允许场景**：你个人的 feature 分支（只有你一个人在用的分支），在 rebase 后可以 `git push --force-with-lease`（比 `--force` 更安全，会检查远程是否有你不知道的更新）。

**🚨 红线三：禁止在公共分支上使用 reset**

```bash
# ❌ 绝对禁止：
git checkout main
git reset --hard HEAD~3
git push --force origin main

# 后果：main 分支的最近 3 次提交被你从历史上抹掉了，所有基于这些提交的工作全部受影响。
```

### 7.3 .gitignore 企业标准模板

`.gitignore` 文件告诉 Git 哪些文件**不要跟踪、不要提交**。每个项目根目录必须有一个 `.gitignore` 文件。

**前后端通用 .gitignore 标准模板**：

```gitignore
# ==================== 操作系统文件 ====================
.DS_Store
Thumbs.db
Desktop.ini

# ==================== 编辑器/IDE 配置 ====================
.idea/
.vscode/
*.swp
*.swo
*~

# ==================== 依赖目录（必须忽略！太大且有安全风险） ====================
node_modules/
vendor/
__pycache__/
*.pyc

# ==================== 编译输出目录 ====================
dist/
build/
target/
*.class
*.jar
*.war

# ==================== 日志文件 ====================
*.log
logs/
npm-debug.log*
yarn-debug.log*

# ==================== 环境变量和敏感配置（🚨 最重要！绝对不能提交） ====================
.env
.env.local
.env.*.local
*.pem
*.key
config/database.yml
config/secrets.yml
application-local.yml

# ==================== 临时文件 ====================
tmp/
temp/
*.tmp

# ==================== 打包文件 ====================
*.zip
*.tar.gz
*.rar
```

> **企业提醒**：`.gitignore` 只能忽略**还没被 Git 跟踪**的文件。如果某个文件之前已经被 commit 过了，再加到 `.gitignore` 里是无效的。需要先把它从 Git 跟踪中移除：`git rm --cached 文件名`。

### 7.4 敏感信息防范

**绝对不能提交到 Git 仓库的内容**：

| 类型 | 示例 | 泄露后果 |
|------|------|---------|
| 数据库密码 | `password: MyDBPass123` | 黑客直接连接你的数据库 |
| API 密钥 / Token | `Authorization: Bearer xxxxx` | 别人可以冒充你的服务调用接口 |
| 云服务密钥 | AWS Secret Key、阿里云 AccessKey | 黑客用你的账号开服务器挖矿 |
| SSL 证书私钥 | `*.pem`、`*.key` 文件 | HTTPS 加密被破解 |
| 内部服务器 IP | `192.168.1.100` | 暴露内网结构 |

**万一不小心提交了敏感信息怎么办？**

```bash
# 如果只是本地 commit 了还没 push：
git reset HEAD~1     # 撤销这次提交，代码保留在工作区
# 修改代码去掉敏感信息后重新 commit

# 如果已经 push 到远程了：
# 1. 立刻用 git revert 撤销那次提交
git revert commit_hash
git push origin dev

# 2. 立刻更改泄露的密码/密钥！（这步最重要！）
#    因为即使你 revert 了，Git 历史中仍然可以看到旧的密码
#    需要用 git filter-branch 或 BFG 工具彻底清除历史记录中的敏感数据

# 3. 通知安全团队评估影响范围
```

### 7.5 多人协作沟通规范

1. **开始开发前**：在群里/站会上告知团队「我开始开发 XXX 功能了，会动 YYY 模块」，避免撞车
2. **每天下班前**：push 当天的代码到远程，不要只在本地提交（万一电脑坏了代码全丢）
3. **修改了公共文件**（如路由配置、全局样式、公共组件）：必须在群里通知，说明改了什么
4. **MR 评审**：认真写描述、附上截图/录屏、说明测试情况；评审人要在 24 小时内完成评审
5. **解决冲突后**：跑一遍完整的功能测试，确保没有引入新 Bug

### 7.6 生产环境上线 Git 操作禁忌

| 禁忌操作 | 为什么危险 |
|---------|----------|
| 周五下午 5 点后上线 | 出了问题周末加班修，人员不齐响应慢 |
| 未经测试直接合并到 main | 可能引入未发现的 Bug |
| 一次上线合并太多功能 | 出问题难以定位是哪个功能引起的 |
| 上线后不观察日志 | 可能有问题在用户使用时才暴露 |
| 没有回滚预案就上线 | 出问题不知道怎么快速恢复 |
| 上线后不更新版本号/tag | 以后无法定位某个版本的代码状态 |

---

## 第八章：企业高频疑难问题排错手册（工作踩坑急救）

### 8.1 push 被拒绝、代码冲突无法合并

**报错信息**：
```
! [rejected]        dev -> dev (fetch first)
error: failed to push some refs to 'git@github.com:company/project.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally.
```

**原因**：远程分支有你本地没有的新提交，Git 拒绝你的推送以防止覆盖别人的代码。

**解决方案**：
```bash
# 第一步：拉取远程最新代码
git pull origin dev
# 或者推荐用 rebase 方式：
git pull --rebase origin dev

# 第二步：如果有冲突，解决冲突
# 编辑冲突文件 → git add → git commit（或 git rebase --continue）

# 第三步：重新推送
git push origin dev
```

### 8.2 忘记拉取代码提交，覆盖同事代码

**场景**：你改了 `src/api.js` 并 push 了，但同事在你之前也改了这个文件并 push 了。你的 push 被拒绝后你没有认真解决冲突，而是 `git push --force` 强制推送了，导致同事的代码被覆盖。

**补救方案**：
```bash
# 第一步：立刻通知同事！（最重要的一步，别瞒着）

# 第二步：找到同事被覆盖的提交
git reflog
# reflog 记录了你的每一次操作，包括被覆盖的提交
# 找到同事提交的 hash 值

# 第三步：用 cherry-pick 把同事的提交摘回来
git cherry-pick 同事的commit_hash

# 第四步：推送修正后的代码
git push origin dev

# 第五步：向团队道歉，以后严格遵守 pull → resolve conflict → push 流程
```

> **`git reflog` 是你的救命稻草**：它记录了 Git 的每一次操作（包括 reset、rebase、merge 等），即使你 reset 丢了代码，也可以从 reflog 中找回 commit hash。

### 8.3 误提交敏感文件到远程仓库

**场景**：你不小心把 `.env` 文件（包含数据库密码）提交并 push 到了远程仓库。

**紧急处理方案**：
```bash
# 第一步：从 Git 跟踪中移除文件（不删除本地文件）
git rm --cached .env

# 第二步：把 .env 加入 .gitignore
echo ".env" >> .gitignore

# 第三步：提交并推送
git add .gitignore
git commit -m "chore: 移除误提交的 .env 文件并更新 .gitignore"
git push origin dev

# 第四步（最关键！）：立刻更改数据库密码！
# 因为 Git 历史中仍然可以看到旧的密码内容
# 如果想彻底从历史中清除，需要用 BFG Repo-Cleaner 工具：
# java -jar bfg.jar --delete-files .env repo.git
```

### 8.4 本地多仓库 SSH 密钥切换失败

**场景**：你同时有公司 GitLab 和个人 GitHub 两个账号，配了两套 SSH 密钥，但 push 时提示认证失败。

**解决方案**：在 `~/.ssh/config` 文件中配置多主机密钥映射：

```ssh-config
# 公司 GitLab
Host gitlab.company.com
    HostName gitlab.company.com
    User git
    IdentityFile ~/.ssh/id_ed25519_company

# 个人 GitHub
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal
```

```bash
# 测试连接
ssh -T git@gitlab.company.com
ssh -T git@github.com
```

### 8.5 rebase 操作后代码丢失、提交错乱

**场景**：你在 feature 分支上执行了 `git rebase dev`，冲突太多搞乱了，代码看起来不对。

**解决方案**：
```bash
# 如果 rebase 还没完成：
git rebase --abort    # 直接放弃 rebase，恢复到 rebase 之前的状态

# 如果 rebase 已经完成但结果不对：
git reflog            # 找到 rebase 之前的 commit hash
git reset --hard HEAD@{n}    # n 是 reflog 中 rebase 前的那条记录编号
# 示例：git reset --hard HEAD@{3}

# 恢复后重新尝试 rebase，这次仔细处理每个冲突
git rebase dev
```

### 8.6 远程仓库地址变更、本地无法拉取推送

**场景**：公司把 GitLab 从旧服务器迁移到了新服务器，仓库地址变了，你的 `git pull` 和 `git push` 都报错。

**解决方案**：
```bash
# 查看当前远程地址
git remote -v
# origin  git@old-gitlab.company.com:backend/project.git (fetch)
# origin  git@old-gitlab.company.com:backend/project.git (push)

# 修改远程地址为新地址
git remote set-url origin git@new-gitlab.company.com:backend/project.git

# 验证修改成功
git remote -v
# origin  git@new-gitlab.company.com:backend/project.git (fetch)
# origin  git@new-gitlab.company.com:backend/project.git (push)

# 测试连接
git pull origin dev
```

---

## 附录：企业面试 Git 真题（10 道含答案）

### 题目 1：请简述 Git 的工作区、暂存区、本地仓库、远程仓库的关系和数据流转过程。

**标准答案**：
Git 的四个区域按照数据流转顺序为：工作区（Working Directory）→ 暂存区（Staging Area）→ 本地仓库（Local Repository）→ 远程仓库（Remote Repository）。开发者在工作区修改代码，通过 `git add` 将修改添加到暂存区，通过 `git commit` 将暂存区的内容提交到本地仓库形成快照，通过 `git push` 将本地仓库的提交推送到远程仓库。反向流程是通过 `git pull`（等同于 `git fetch` + `git merge`）从远程仓库拉取最新代码到工作区。

---

### 题目 2：`git merge` 和 `git rebase` 有什么区别？在企业中分别适用于什么场景？

**标准答案**：
`git merge` 会把两个分支的提交历史合并在一起，保留分叉记录，产生一个合并提交节点，不改变已有的提交历史。`git rebase` 会把当前分支的提交「摘下来」放到目标分支的最新提交之后，让提交历史变成一条直线，但会改写提交历史（改变 commit hash）。

在企业中，`merge` 适用于将功能分支合并到 dev/main 等公共分支，因为不改写历史更安全。`rebase` 适用于在个人 feature 分支上同步 dev 的最新代码，保持提交历史整洁。**绝对禁止对已 push 的公共分支执行 rebase**，否则会导致团队其他成员代码冲突。

---

### 题目 3：`git reset` 和 `git revert` 有什么区别？线上代码回滚应该用哪个？

**标准答案**：
`git reset` 是把提交历史往回倒退，被撤销的提交从历史中消失，会改写 Git 历史。`git revert` 是在历史末尾创建一个新的「反向提交」来抵消指定提交的改动，原始提交仍然保留在历史中。

线上代码回滚**必须使用 `git revert`**，因为它不改写历史，不会影响团队其他成员，且保留了完整的操作记录便于追溯。`git reset` 只适用于本地还没 push 的提交，对已 push 的远程分支使用会导致本地和远程历史不一致。

---

### 题目 4：你在 feature 分支开发了一周，期间 dev 分支有很多同事的提交。你准备合并代码时应该怎么做？

**标准答案**：
首先应该在 feature 分支上拉取 dev 的最新代码进行同步：使用 `git pull --rebase origin dev`（或 `git merge dev`）将 dev 的新提交同步到你的分支。如果有冲突，逐个解决冲突后继续。同步完成后，把 feature 分支 push 到远程，提交 MR/PR 请求合并到 dev，等待同事 Code Review 通过后由评审人合并。合并后删除 feature 分支（本地和远程都删除）。

---

### 题目 5：`.gitignore` 文件的作用是什么？如果已经被 Git 跟踪的文件再加到 `.gitignore` 里有效吗？

**标准答案**：
`.gitignore` 的作用是告诉 Git 忽略指定的文件和目录，不将它们纳入版本控制。常用于忽略依赖目录（node_modules）、编译输出（dist/build）、日志文件、环境配置文件（.env）等。

**如果文件已经被 Git 跟踪（之前 commit 过），再添加到 `.gitignore` 是无效的**。需要先用 `git rm --cached 文件名` 将文件从 Git 跟踪中移除（不会删除本地文件），然后再添加到 `.gitignore` 中。

---

### 题目 6：什么是 Git 冲突？你是怎么解决冲突的？

**标准答案**：
当两个人修改了同一个文件的同一部分代码时，Git 无法自动判断该保留哪个版本，就会产生冲突。冲突通常在 `git pull`、`git merge`、`git rebase` 等操作时发生。

解决流程：打开冲突文件，找到 Git 自动标记的冲突区域（`<<<<<<<`、`=======`、`>>>>>>>` 之间的内容），理解两边的修改意图，手动编辑保留正确的代码（可能需要合并两边的改动），删除冲突标记，然后 `git add` 标记为已解决，最后 `git commit` 完成合并。在企业中，解决冲突时应该和产生冲突的同事沟通，确认保留方案，避免误删他人的有效代码。

---

### 题目 7：`git stash` 是什么？你在什么场景下使用它？

**标准答案**：
`git stash` 可以把你工作区中还没提交的修改**临时存储**起来，让工作区恢复到干净状态，方便你切换分支做其他事情。之后可以用 `git stash pop` 把存储的修改恢复回来。

典型使用场景：我正在 feature 分支开发功能（代码还没写完不想 commit），领导让我紧急切到 hotfix 分支修线上 Bug。这时我会先 `git stash` 暂存当前改动，切换到 hotfix 分支修 Bug 并提交，再切回 feature 分支 `git stash pop` 恢复之前的改动继续开发。

---

### 题目 8：如果不小心把包含数据库密码的配置文件 push 到了远程仓库，你应该怎么处理？

**标准答案**：
处理分四步：
1. 立刻用 `git rm --cached` 把文件从 Git 跟踪中移除，把文件路径加到 `.gitignore`，提交并 push。
2. **立刻更改泄露的数据库密码**。因为即使从最新代码中移除了，Git 历史提交中仍然能看到旧密码。
3. 如果需要彻底清除历史中的敏感信息，使用 BFG Repo-Cleaner 或 `git filter-branch` 工具重写历史（需要团队所有人重新 clone 仓库）。
4. 通知安全团队评估影响范围，检查是否有异常访问。

事后要完善 `.gitignore` 文件，确保 `.env`、`config/secrets` 等敏感文件从一开始就被忽略。

---

### 题目 9：请描述一个完整的企业 Git 开发工作流（从接到需求到代码上线）。

**标准答案**：
1. 从 dev 分支拉取最新代码，创建 feature 功能分支（`git switch -c feature/xxx`）
2. 在 feature 分支上开发功能，每天多次小步提交（`git add` + `git commit`），commit message 遵循 `feat/fix` 等前缀规范
3. 每天至少一次拉取 dev 分支的最新代码同步（`git pull --rebase origin dev`），及时解决冲突
4. 功能开发完推送到远程（`git push -u origin feature/xxx`）
5. 在 GitLab/GitHub 上提交 MR/PR，指定 Code Reviewer 进行代码评审
6. 评审通过后合并到 dev 分支，删除 feature 分支
7. 测试团队在 dev 分支上进行测试
8. 测试通过后合并到 main 分支，打版本 tag（`git tag -a v1.x.x`）
9. 部署上线，观察日志
10. 如有线上 Bug，从 main 创建 hotfix 分支修复，修完后合并回 main 和 dev，打 patch 版本 tag

---

### 题目 10：你在企业中使用过哪些 Git 高危操作？如何避免这些操作带来的风险？

**标准答案**：
常见高危操作包括：

- **`git push --force`**：强制推送覆盖远程代码。只在个人 feature 分支 rebase 后使用 `git push --force-with-lease`（更安全的强制推送），绝对不在 main/dev 等公共分支上使用。
- **`git reset --hard`**：硬重置丢弃所有改动。只在本地未 push 的提交上使用，对已 push 的提交改用 `git revert`。
- **`git rebase`**：变基改写历史。只在本地或个人的 feature 分支上使用，绝不对公共分支使用。
- **直接 push 到 main**：绕过代码评审。通过设置 GitLab/GitHub 的分支保护规则（Branch Protection）从系统层面禁止直接 push。

规避风险的核心原则：对本地未 push 的代码可以自由操作（reset/rebase），对已 push 的远程公共代码只能用安全操作（revert/merge）。

---

> **全文完**
>
> 恭喜你完成了 Git 从入门到企业实战的完整学习！记住三个核心原则：
> 1. **勤提交**：小步快跑，每次改动都有记录
> 2. **多拉取**：每天同步远程代码，减少冲突
> 3. **守规范**：commit message 规范、分支规范、MR 评审——这些不是形式主义，是保护你不出事故的护栏
