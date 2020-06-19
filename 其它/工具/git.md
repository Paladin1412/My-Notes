# 版本管理工具

---

##  Git

### 基本概念

**可以对代码进行版本管理和多人协作的代码仓库。**

下载地址：https://git-scm.com/

1. 下载后用户可以直接通过 Git Bash 工具使用 Git 命令。
2. 如果想要直接在控制台输入 git 命令，需要配置环境变量：在 Path 中添加 Git/cmd 文件夹路径。

- **创建仓库**

如果要使用 Git 管理目录内的文件，将目录创建为 Git 代码仓库即可。
 
```bash
git init                             # 在现有目录创建 git 
git init myProject                   # 创建子目录并为子目录创建 git
```

### 数据存储

![git](git.png)

1. **Workspace 工作区** 
  
  即当前开发者正在进行编写的代码。

2. **Index / Stage 暂存区** 

  通过目录树结构，记录要进行版本管理的文件、文件的状态信息以及指向文件的索引。

  使用时间戳、文件长度等状态信息，可以快速判断工作区文件是否被修改。

3. **Repository 仓库区** 
  
  记录本次提交的历史更改，保存在 Git 对象库（.git/objects）中。



- **暂存文件**

需要进行版本管理的代码文件应首先放入暂存区，在执行 add 操作时会自动忽略 `.gitignore` 文件夹中的文件。

```bash
git add README.md                          # 将指定文件放入暂存区
git add .                                  # 将全部文件放入暂存区

git diff                                   # 查看暂存区和工作区文件差异
```

暂存区可能保有历史文件的缓存数据。已提交的文件如果想取消版本管理，则必须首先清除缓存。

```bash
git rm -r --cached .idea                   # 从暂存区删除文件夹
git rm -r --cached .                       # 从暂存区删除全部文件 
```

- **提交版本**

通过执行 commit 操作，将暂存区中托管的文件数据存储到本地仓库保存。

commit 操作执行完毕后，暂存区数据会被清空。每次 commit 操作前都应执行 add 操作。

```bash
git commit -m "Your commit"                # 提交文件，放入 git 仓库保存

git log                                    # 查看当前分支提交历史
git log -p master..origin/master           # 查看两分支之间差异
```

执行 commit 操作后（版本号 A）并尝试 push 到远程仓库，如果远程仓库已经被更新就会遭到拒绝。此时必须通过 pull 获取更新到本地然后合并（版本号 B）才能 push 代码，但是会提交两个版本更新。

此时可以改用 stash 操作对本地更改进行缓存，但不会产生新的提交对象。再执行 pull 操作时会直接读取远程仓库的版本，然后通过 stash pop 操作读取缓存并合并（版本号 B）。之后再进行 commit 和 push 操作，就只会提交一个版本更新。

```bash
git stash                                  # 将更改放入堆栈缓存
git stash pop                              # 读取堆栈缓存中的更改

git stash list                             # 查看堆栈缓存
```


### 分支管理

在创建仓库的时候，默认使用主分支 master。

在开发新功能、修复紧急 bug 时往往会创建分支进行，完成开发后再将它们合并到主分支上。

```bash
git branch                                 # 列出本地分支
git branch -r                              # 列出远程分支
 

git branch test                            # 创建 test 分支（但不切换）
git checkout test                          # 切换到 test 分支
git merge test                             # test 分支合并到主分支
```

### 文件忽略


我们可以在 `.gitignore` 文件中添加一些忽略项，来忽略开发者想忽略掉的文件或目录。在执行 add 操作时会自动跳过。

*暂存区可能保有历史文件的缓存数据。已提交的文件如果想取消版本管理，则必须首先清除缓存。*
 
```bash
*.a               # 忽略所有 .a 结尾的文件
!lib.a            # 但 lib.a 除外

/TODO             # 忽略项目根目录下的 TODO 文件

node_modules      # 忽略指定文件夹
.project
.vscode

build/            # 忽略 build/ 目录下的所有文件
doc/*.txt         # 忽略 doc/ 目录下的 txt 文件
```


---

## GitHub

**以 Git 为基础，允许在线对代码进行版本管理和多人协作的远程代码仓库。**
   
官方网址：https://github.com/


### 账户绑定

GitHub 账号首先需要和本地 Git 进行绑定，从而允许本地仓库对远程代码进行修改：

```bash
$ ssh-keygen -t rsa -b 4096 -C "email@example.com"     # 生成密钥
$ clip < ~/.ssh/id_rsa.pub                             # 显示密钥
```

复制密钥后，在 GitHub 账号 SSH and GPG keys 选项添加。本地仓库即可以该账号的身份修改远程仓库。

### 数据同步

在 GitHub 中储存的项目，都拥有唯一的标识 SSH: 如 `git@github.com:account/project.git`


- **链接仓库**

1. 用户可以直接将 GitHub 上的仓库下载到当前目录，会自动为拷贝的本地仓库创建 git 并链接远程仓库。

```bash
git clone git@github.com:account/project.git                  # 拷贝项目到本地
```

2. 也可以为已创建的本地仓库链接远程仓库。

```bash
git remote add origin git@github.com:account/project.git      # 绑定远程仓库，命名为 origin
git remote rm origin                                          # 移除远程仓库 origin
git remote -v                                                 # 查看绑定的远程仓库
```

- **同步代码**

本地仓库和远程仓库链接后，且本地 Git 绑定的 GitHub 账户具备对远程仓库的操作权限，用户就可以通过以下指令同步远程代码。

```bash
git push -u origin master                                     # master 分支上传到远程仓库 origin
git push -f origin master                                     # master 分支强制上传到远程仓库 origin


git pull origin master                                        # 从远端仓库 origin 获取代码并自动合并到主分支
git fetch origin master                                       # 从远端仓库 origin 获取代码到 origin/master 分支
git log -p master..origin/master                              # 查看分支差异
git merge origin/master                                       # 合并分支
```

### 合作开发

GitHub 支持多人进行协同开发，开发者可以将代码上传到别人的远程仓库中。

1. **Collaborators 模式**

适用于合作开发。

仓库所有者进入仓库设置，在 Collaborators 选项添加合作者。合作者有权限对直接对指定的远程仓库进行修改。

1. **Fork 模式**

适用于开源项目。

开发者选择 Fork 他人仓库，会复制得到自己持有的镜像仓库。开发者对镜像仓库进行修改后，可以发送 Pull Request 询问原仓库拥有者是否想要该修改。


