# git npm yarn

## npm yarn 修改镜像源

**为什么需要修改镜像源？**

> 执行 yarn,npm各种命令的时候，默认是去npm/yarn官方（服务器在国外）镜像源获取需要安装的具体软件信息,国内有时候访问很慢，所以要将镜像源修改为阿里旗下维护的一个完整的 npm 镜像源https://registry.npm.taobao.org （同样适用于 yarn）

**淘宝镜像以10分钟一次的频率同步官网**

### 安装cnpm

```js
npm install cnpm -g --registry=https://registry.npm.taobao.org
```

`cnpm -v`测试是否安装成功

### 改变镜像源

**查看当前使用的镜像源**

```arduino
yarn config get registry
npm config get registry

还原的时候可以设置
npm默认地址：https://registry.npmjs.org/
yarn默认地址：https://registry.npm.taobao.org
```

**修改镜像源的方式**

1. 临时修改镜像源

```arduino
npm install 软件名 --registry=https://registry.npm.taobao.org
```

2. **全局修改**

```js
npm config set registry https://registry.npm.taobao.org
```

4. 设置下载镜像站点
   1. 打开.npmrc文件（nodejs\node_modules\npm\npmrc，没有的话可以使用git命令行建一个( touch .npmrc)，用cmd命令建会报错）
   2. 增加 registry =https://registry.npm.taobao.org  即可。

```js
// .npmrc文件
registry=https://registry.npmmirror.com
```

### 安装依赖

``` bash
#局部
$ npm install package
$ yarn add package

#全局
$ npm install -g package
$ yarn global add package
```

### 移除

```bash
#局部
$ npm uninstall package
$ yarn remove package

#全局
$ npm uninstall -g package
$ yarn global remove package
```

### 升级

```bash
#局部
$ npm update package
$ yarn upgrade package

#全局
$ npm update -g package
$ yarn global upgrade package
```



## nrm/yrm

 npm registry manager是npm的镜像源管理工具，有时候国外资源太慢，那么我们可以用这个来切换镜像源

### 安装nrm

nrm是node模块，所以通过npm来安装，它是一个命令行模块，需要使用--global参数进行全局安装，命令如下：

```js
npm install --global nrm
```

此时执行`nrm -h`，会显示command not found，出现这个问题的原因是没有配置环境变量。在终端中输入`vi ~/.bash_profile`打开bash_profile文件，如果系统里不存在该文件，该命令会自动生成一个。手动添加系统变量后，按esc，输入`:wq`保存并退出，输入`source ~/.bash_profile`，使刚刚的配置生效。再次输入`nrm -h`,出现nrm的帮助信息

### 测试速度(nrm test)

对源进行网络测速，通过测速结果来决定具体使用哪个源，`*`代表当前正在使用的源

```bash
$ nrm test

  npm ------ 929ms
  yarn ----- 996ms
  tencent -- 1334ms
  cnpm ----- 1225ms
* taobao --- 266ms
  npmMirror - 2360ms
```

### 查看源(nrm ls)

```bash
$ nrm ls

npm ---------- https://registry.npmjs.org/
yarn --------- https://registry.yarnpkg.com/
tencent ------ https://mirrors.cloud.tencent.com/npm/
cnpm --------- https://r.cnpmjs.org/
taobao ------- https://registry.npmmirror.com/
npmMirror ---- https://skimdb.npmjs.com/registry/
```

表示是当前使用的源，左边是源的名称，可以在nrm里切换

### 切换源

使用源的名称可以快速切换源`nrm use <registry name>`

```bash
$ nrm use taobao
                        
Registry has been set to: https://registry.npmmirror.com/
```

### 增加源

为了开发效率，企业内部可能会在内网部署一套私有的npm源。因为内网安装速度快；有些私有模块，是仅供企业内部使用，更安全；适合多团队开发。使用命令`nrm add yourcompany http://registry.npm.yourcompany.com/`

### 删除源

使用命令`nrm del <registry name>`

## nvm

nodejs 版本管理工具

### 安装nvm

可借鉴https://github.com/creationix/nvm/blob/master/README.md

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash
```

+ 解决GitHub的raw.githubusercontent.com无法连接问题

  > 部分人也许会出现
  >
  > 1. https://site.ip138.com/raw.Githubusercontent.com/输入raw.githubusercontent.com查询IP地址
  >
  > 2. 修改hosts Ubuntu，macOS直接在终端输入，打开hosts修改编辑器
  >
  >    ```
  >    sudo vi /etc/hosts
  >    ```
  >
  > 3. 例如查询到ip为，则在hosts添加一行185.199.109.133 raw.githubusercontent.com

重新打开终端，输入nvm验证一下是否安装成功，当出现“Node Version Manager”，则说明安装成功。

如果在新的终端输入 nvm 时提示：command not found:nvm，可能原因是，没有执行下面的脚步

```
=> Close and reopen your terminal to start using nvm or run the following to use it now:

export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

把下面的脚本复制到终端，回车之后执行nvm命令，发现可以正常使用了

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

我这边还有一个问题就是，每次关掉终端，再重新打开的时候执行nvm又会提示 command not find。执行上面的脚本之后又可正常使用，如此反复。这时候需要.zshrc文件(touch .zshrc)，然后将上面的脚本复制进去。

### nvm 常用命令

```bash
nvm install stable ## 安装最新稳定版 node
nvm install <version> ## 安装指定版本
nvm uninstall <version> ## 删除已安装的指定版本
nvm use <version> ## 切换使用指定的版本node,要先安装（如果没有该版本的话），再使用
nvm ls ## 列出所有安装的版本
nvm ls-remote ## 列出所有远程服务器的版本
nvm current ## 显示当前的版本
nvm alias <name> <version> ## 给不同的版本号添加别名
nvm unalias <name> ## 删除已定义的别名
nvm reinstall-packages <version> ## 在当前版本 node 环境下，重新全局安装指定版本号的 npm 包
nvm alias default [node版本号] ##设置默认版本
```



| 指令                    | 解释                                                         |
| ----------------------- | ------------------------------------------------------------ |
| nvm ls                  | 查看所有安装的node版本                                       |
| nvm ls-remote           | 查看网络上可以安装的版本                                     |
| nvm install stable      | 安装最新版 node                                              |
| nvm install latest      | 安装最新稳定版 node                                          |
| nvm install <version>   | 安装指定版 node，如安装10.24.1版本可以运行 nvm install 10.24.1 |
| nvm uninstall <version> | 卸载指定版 node，如卸载10.24.1版本可以运行 nvm uninstall 10.24.1 |
| nvm use <version>       | 指定运行node的版本                                           |
| nvm current             | 查看当前运行的node版本                                       |



### 修改全局安装目录

为什么要修改

1. 因某些特殊原因无法使用管理员权限

2. 使用nvm来安装node之后，安装npm i -g cnpm yarn nrm yrm时，我们需要统一修改npm的全局安装目录，单独管理，这样才好配置环境变量问题

   > node node v14.21.1其安装目录默认在在/Users/lizhanpeng/.nvm/versions/node/v14.21.1
   >
   > node node v18.12.1其安装目录默认在在/Users/lizhanpeng/.nvm/versions/node/v18.12.1
   >
   > 这就导致不好配置环境变量而且相关依赖也需要多处node版本重复安装

因此安装nvm后需要修改全局安装目录。例如将默认的全局安装路径修改到当前用户的home目录下

1. 打开终端，新建一个文件夹

```bash
mkdir ~/.npm-global
```

2. 配置npm使用新的路径

```arduino
npm config set prefix '~/.npm-global'
```

3. 打开或新建~/.bash_profile文件，在末尾加入

```bash
# 新建命令 touch ~/.bash_profile
export PATH=~/.npm-global/bin:$PATH
复制代码
```

4. 更新系统环境变量

```bash
source ~/.bash_profile
```

## git

![img](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015120901.png)

下面是我整理的常用 Git 命令清单。几个专用名词的译名如下。

> - Workspace：工作区
> - Index / Stage：暂存区
> - Repository：仓库区（或本地仓库）
> - Remote：远程仓库



### 新建代码库

```bash
# 在当前目录新建一个Git代码库
$ git init

# 新建一个目录，将其初始化为Git代码库
$ git init [project-name]

# 下载一个项目和它的整个代码历史
$ git clone [url]
```

### 配置

Git的设置文件为`.gitconfig`，它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）。

```bash
# 显示当前的Git配置
$ git config --list

# 编辑Git配置文件
$ git config -e [--global]

# 设置提交代码时的用户信息
$ git config [--global] user.name "[name]"
$ git config [--global] user.email "[email address]"
```

例如：没有设置用户信息会提示  Please tell me who you are.Run git config --global user.email "you@example.com" git config --global user.name "Your Name"

**解决办法**:添加Author

```bash
git config --global user.email “你的邮箱”
git config --global user.name “用户名”
```

### 增加/删除文件

```bash
# 添加指定文件到暂存区
$ git add [file1] [file2] ...

# 添加指定目录到暂存区，包括子目录
$ git add [dir]

# 添加当前目录的所有文件到暂存区
$ git add .

# 添加每个变化前，都会要求确认
# 对于同一个文件的多处变化，可以实现分次提交
$ git add -p

# 删除工作区文件，并且将这次删除放入暂存区
$ git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]

# 改名文件，并且将这个改名放入暂存区
$ git mv [file-original] [file-renamed]
```

### 代码提交

```bash
# 提交暂存区到仓库区
$ git commit -m [message]

# 提交暂存区的指定文件到仓库区
$ git commit [file1] [file2] ... -m [message]

# 提交工作区自上次commit之后的变化，直接到仓库区
$ git commit -a

# 提交时显示所有diff信息
$ git commit -v

# 使用一次新的commit，替代上一次提交
# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
$ git commit --amend -m [message]

# 重做上一次commit，并包括指定文件的新变化
$ git commit --amend [file1] [file2] ...
```

### 分支

```bash
# 列出所有本地分支
$ git branch

# 列出所有远程分支
$ git branch -r

# 列出所有本地分支和远程分支
$ git branch -a

# 新建一个分支，但依然停留在当前分支
$ git branch [branch-name]

# 新建一个分支，并切换到该分支
$ git checkout -b [branch]

# 新建一个分支，指向指定commit
$ git branch [branch] [commit]

# 新建一个分支，与指定的远程分支建立追踪关系
$ git branch --track [branch] [remote-branch]

# 切换到指定分支，并更新工作区
$ git checkout [branch-name]

# 切换到上一个分支
$ git checkout -

# 建立追踪关系，在现有分支与指定的远程分支之间
$ git branch --set-upstream [branch] [remote-branch]

# 合并指定分支到当前分支
$ git merge [branch]

# 选择一个commit，合并进当前分支
$ git cherry-pick [commit]

# 删除分支
$ git branch -d [branch-name]

# 删除远程分支
$ git push origin --delete [branch-name]
$ git branch -dr [remote/branch]
```

### 标签

```bash
# 列出所有tag
$ git tag

# 新建一个tag在当前commit
$ git tag [tag]

# 新建一个tag在指定commit
$ git tag [tag] [commit]

# 删除本地tag
$ git tag -d [tag]

# 删除远程tag
$ git push origin :refs/tags/[tagName]

# 查看tag信息
$ git show [tag]

# 提交指定tag
$ git push [remote] [tag]

# 提交所有tag
$ git push [remote] --tags

# 新建一个分支，指向某个tag
$ git checkout -b [branch] [tag]
```

### 查看信息

```bash
# 显示有变更的文件
$ git status

# 显示当前分支的版本历史
$ git log

# 显示commit历史，以及每次commit发生变更的文件
$ git log --stat

# 搜索提交历史，根据关键词
$ git log -S [keyword]

# 显示某个commit之后的所有变动，每个commit占据一行
$ git log [tag] HEAD --pretty=format:%s

# 显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件
$ git log [tag] HEAD --grep feature

# 显示某个文件的版本历史，包括文件改名
$ git log --follow [file]
$ git whatchanged [file]

# 显示指定文件相关的每一次diff
$ git log -p [file]

# 显示过去5次提交
$ git log -5 --pretty --oneline

# 显示所有提交过的用户，按提交次数排序
$ git shortlog -sn

# 显示指定文件是什么人在什么时间修改过
$ git blame [file]

# 显示暂存区和工作区的差异
$ git diff

# 显示暂存区和上一个commit的差异
$ git diff --cached [file]

# 显示工作区与当前分支最新commit之间的差异
$ git diff HEAD

# 显示两次提交之间的差异
$ git diff [first-branch]...[second-branch]

# 显示今天你写了多少行代码
$ git diff --shortstat "@{0 day ago}"

# 显示某次提交的元数据和内容变化
$ git show [commit]

# 显示某次提交发生变化的文件
$ git show --name-only [commit]

# 显示某次提交时，某个文件的内容
$ git show [commit]:[filename]

# 显示当前分支的最近几次提交
$ git reflog
```

### 远程同步

```bash
# 下载远程仓库的所有变动
$ git fetch [remote]

# 显示所有远程仓库
$ git remote -v

# 显示某个远程仓库的信息
$ git remote show [remote]

# 增加一个新的远程仓库，并命名
$ git remote add [shortname] [url]

# 取回远程仓库的变化，并与本地分支合并
$ git pull [remote] [branch]

# 上传本地指定分支到远程仓库
$ git push [remote] [branch]

# 强行推送当前分支到远程仓库，即使有冲突
$ git push [remote] --force

# 推送所有分支到远程仓库
$ git push [remote] --all
```

### 撤销

```bash
# 恢复暂存区的指定文件到工作区
$ git checkout [file]

# 恢复某个commit的指定文件到暂存区和工作区
$ git checkout [commit] [file]

# 恢复暂存区的所有文件到工作区
$ git checkout .

# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
$ git reset [file]

# 重置暂存区与工作区，与上一次commit保持一致
$ git reset --hard

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
$ git reset [commit]

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
$ git reset --hard [commit]

# 重置当前HEAD为指定commit，但保持暂存区和工作区不变
$ git reset --keep [commit]

# 新建一个commit，用来撤销指定commit
# 后者的所有变化都将被前者抵消，并且应用到当前分支
$ git revert [commit]

# 暂时将未提交的变化移除，稍后再移入
$ git stash
$ git stash pop
```

### .gitkeep

没有别的作用，只是保持文件夹，该文件的存在会让 git 认为这是一个有内容的文件夹

### .gitignore

- 该文件的作用是让 git 忽略一些文件和文件夹
- 里面的内容就是忽略的文件和文件夹的路径
- 该文件请务必放在 .git 文件夹同级，即项目的根目录

## 常用的git命令

缩写其实只要记住对应的英文就会方便很多

```
b -> browse浏览信息
r -> remote
a -> all
l -> list
m -> message
d -> delete

查看分支：git branch
切换分支：git checkout
创建+切换分支：git checkout -b
合并某分支到当前分支：git merge
删除分支：git branch -d
下载远程仓库的所有变动: git fetch 
```

### git init

初始化项目，在项目根目录下创建一个`.git`子目录，用来保存版本信息

### git status

- 该指令用于查看当前的文件状态
- 如果显示红色 表示没有被管理 或者是有过修改
- 如果显示的是绿色 表示在暂存区中

### git add .

将文件区的内容添加到暂存区，添加所有 `git add --all` 或者简化方式 `git add .`

### git commit -m '描述'

该指令用于将暂存区中的内容添加到历史区生成一个版本commit

### git clone

克隆远程仓库 git clone [远程仓库地址]

###  git pull 

拉取远程的所有信息至本地

### git push [branch]

推送分支

### git branch

**查看**或者创建分支

```bash
# 新建一个分支，但依然停留在当前分支
$ git branch [branch-name]

# 列出所有本地分支
$ git branch
* dev #当前分支的前面用`*`标记
 master
 
# 列出所有本地分支和远程分支
$ git branch -a
* dev
 master
 remotes/origin/HEAD -> origin/master
 remotes/origin/master

# 列出所有远程分支，远程分支一般会用红色字体标记出来
$ git branch -r
 remotes/origin/HEAD -> origin/master
 remotes/origin/master
```

### git branch -d [branch]

删除分支

### git checkout -b

```bash
# 新建一个分支，并切换到该分支
$ git checkout -b [branch]

#新建一个分支，并切换到该分支，并和指定的远程分支`release/caigou_v1.0`关联了起来
$ git checkout -b dev origin/release/caigou_v1.0

# 切换到上一个分支
$ git checkout -

```

### git merge [branch]

合并指定分支到当前分支

### git fetch

下载远程仓库的所有变动

### git log

查看日志。记录我们git commit操作

```
由四部分组成
1. commit值：是一个由SHA1计算出来的非常大的数字，用十六进制表示，是此次操作的唯一标识符（每次commit的时候该值不会重复）
2. Author： 提交该版本的作者以及该作者的邮箱;最好在git init之后就立刻设置，这样在你进行git commit的时候就能记录你设置的Author信息
3. Date：提交的日期。
4. commit说明，git commit -m的内容
```

### git log --pretty=oneline

可以让每次的commit在一行上显示，如下所示。每一行就是一个commit，前方是commit所对应的hash值，后方是commit所对应的message。

### git reset --hard 

版本回退

1. git reset --hard HEAD^

   > git reset是版本回退命令，
   >
   > --hard是回退命令的一个参数
   >
   > HEAD代表的是当前版本
   >
   > 每加一个^就表示更前的一个版本，如果想要回到5个版本前，就需要5个^，例如git reset --hard HEAD^^^^^

2. git reset --hard HEAD~1

   > HEAD^换成了HEAD~1，其中1代表回退几个版本，如果需要回退到10个之前的版本，只需要把HEAD~1改为HEAD~10

3. git reset --hard 1e7fac4

   > HEAD换成了commit。正是由于commit字串的唯一性，而不需要查找当前版本和切换版本之间的差值
   >
   > git log获取commit，一般取前8位即可

### git commit --amend -m '描述'

+ 对于修改上一次 commit 备注信息，我们可以使用 `git commit --amend -m 'xxx'` 来修改。但它无法修改上上次提交信息

+ git修改上上次的commit信息

  > 1. 执行 `git rebase -i HEAD~2`，如下图可以看到最近两次提交，进入一个 vim 编辑页面
  >
  > 2. 按ESC, 再按 a 进入INSERT模式, 将上上次提交的信息前的 pick 改为 edit，如下图，按ESC，再按 shift + : 进入命令模式，输入x 或wq保存，不熟悉vim操作的可以搜索下vim教程；保存后，终端里会看到下面 log 信息
  >
  >    ```
  >    guoqzuo-mac:fedemo kevin$ git rebase -i HEAD~2
  >    Stopped at e889c7e...  测试第一次提交
  >    You can amend the commit now, with
  >    
  >      git commit --amend 
  >    
  >    Once you are satisfied with your changes, run
  >    
  >      git rebase --continue
  >    guoqzuo-mac:fedemo kevin$ 
  >    ```
  >
  > 3. 运行  `git commit --amend -m 'xxx'` 会修改上上次的信息
  >
  > 4. 最好运行`git rebase --continue`即可修改成功







