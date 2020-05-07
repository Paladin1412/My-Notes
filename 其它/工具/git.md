# Git / GitHub


## 什么是 Git

- **Git**: 可以对代码进行版本管理和多人协作的代码仓库.

- **GitHub**: 以 Git 为基础，允许在线对代码进行版本管理和多人协作的远程代码仓库。

## 安装和配置 Git

### Git Bash 工具

下载地址：https://git-scm.com/

如果想要直接在控制台输入 git 命令而不必打开 Git Bash 工具，还需要配置环境变量：

- （windows 系统）在 系统变量 > Path 添加 cmd 文件夹路径（如 D:\Software\Git\cmd）。

### GitHub 网站
   
官方网址：https://github.com/

注册账号后，还需要和本地 Git 进行绑定，从而允许本地仓库对远程代码进行修改：

1. 控制台输入 `ssh-keygen -t rsa -b 4096 -C "email@example.com"` 生成密钥（根据 github 邮箱）。

2. 控制台输入 `$ clip < ~/.ssh/id_rsa.pub` 显示密钥，进行复制。

3. 在 GitHub 账号 SSH and GPG keys 选项 添加密钥。


## 使用 Git

### 本地记录

本地 Git 分为暂存区和 git 仓库。

提交文件时，会将暂存区代码文件保存到 git 仓库并记录版本号。

```bash
git init                                   // 在现有目录创建 git 

git add README.md                          // 指定代码文件放入暂存区
git add .                                  // 全部代码文件放入暂存区

git commit -m "Your commit"                // 提交文件，放入 git 仓库保存
git log                                    // 查看提交历史
```

### 分支管理

在创建仓库的时候，默认使用主分支 master。

在开发新功能、修复紧急 bug 时往往会创建分支进行，完成开发后再将它们合并到主分支上。

```bash
git branch test                            // 创建 test 分支

git checkout test                          // 切换到 test 分支

git merge test                             // test 分支合并到主分支
```

### 上传项目

在 GitHub 账号中创建远程项目，复制项目 SSH: 如 `git@github.com:account/project.git`

```bash
// 链接远程仓库，命名为 origin
git remote add origin git@github.com:accounto/project.git   

git remote rm origin                       // 移除名为 origin 的远程仓库
git remote -v                              // 查看远程仓库

git push -u origin master                  // master 分支上传到远程仓库 origin
git push -f origin master                  // master 分支强制上传到远程仓库 origin
```

### 下载项目

复制项目 SSH: 如 `git@github.com:account/project.git`

```bash   
// 拷贝项目到本地
git clone git@github.com:account/project.git      

git fetch origin                           // 从远端仓库 origin 获取代码文件
git reset --hard origin/master             // 将本地代码更新到远端仓库 origin 的 master 分支
``` 

