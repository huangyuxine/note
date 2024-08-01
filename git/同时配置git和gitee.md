# 同时配置github和gitee

### 1. 清除 git 的全局设置

若之前对 git 设置过全局的 `user.name` 和 `user.email,`必须删除该设置

```
$ git config --global --unset user.name "你的名字"
$ git config --global --unset user.email "你的邮箱
```

### 2. 生成新的 SSH key

```
ssh-keygen -t rsa -f ~/.ssh/rsa_github -C "xxx@gmail.com"
ssh-keygen -t rsa -f ~/.ssh/rsa_gitee -C "xxx163@gmail.com"
```

#### 完成后会在\~/.ssh / 目录下生成以下文件

rsa\_github.pub

rsa\_github

rsa\_gitee

rsa\_gitee.pub

### 4. 多账号必须配置 config 文件

～/.ssh/config

```
#github
 Host github.com
 HostName github.com
 PreferredAuthentications publickey
 IdentityFile ~/.ssh/rsa_github
 User git
#gitee
 Host gitee.com
 HostName gitee.com
 PreferredAuthentications publickey
 IdentityFile ~/.ssh/rsa_gitee
 User git
```

### 5.在 github 和 gitee网站添加公钥

rsa\_github.pub和rsa\_gitee.pub分别复制到各自的网站

### 6.测试

```
ssh -T git@github.com
ssh -T git@gitee.com
```
