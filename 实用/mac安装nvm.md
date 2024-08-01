# MAC安装nvm

如果电脑上已经装上了node，那么你先卸掉

## 卸载node

<pre class="language-bash"><code class="lang-bash"> sudo npm uninstall npm -g
<strong> sudo rm -rf /usr/local/lib/node /usr/local/lib/node_modules /var/db/receipts/org.nodejs.* 
</strong> sudo rm -rf /usr/local/include/node /Users/$USER/.npm  
 sudo rm /usr/local/bin/node  
 sudo rm /usr/local/share/man/man1/node.1 
 sudo rm /usr/local/lib/dtrace/node.d
</code></pre>

## 安装nvm

切换用户目录

```
cd ~
```

用git安装

```bash
git clone https://github.com/nvm-sh/nvm.git
```

装好之后，切换

```bash
cd nvm
```

执行编译

```bash
./install.sh
```

如果出现错误：git使用代理出现LibreSSL SSL\_connect: SSL\_ERROR\_SYSCALL in connection to github.com:443 错误

则执行

```bash
sudo networksetup -setv6off Wi-Fi
```

配置临时变量

```bash
vim ~/.bash_profile
```

.bash\_profile 写入

```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
```

生效

```bash
source ~/.bash_profile
```

完成之后关闭终端如果发现nvm和node无效

我们需要修改zshrc并让.bash\_profile的配置永久生效

```
vim /etc/zshrc
```

添加并保存

```
source ~/.bash_profile
```

## 常用命令

`nvm ls`列出所有已安装的 node 版本&#x20;

`nvm version`查看nvm版本&#x20;

`nvm install 14.17.1`安装指定版本

`nvm install latest` 安装最新版本

`nvm uninstall 14.17.1` 卸载

`nvm use 12.19.0` 版本切换

`nvm current` 当前版本&#x20;

`nvm alias [别名] [node版本号]`给不同的版本号添加别名&#x20;

`nvm unalias [别名]` 删除已定义的别名&#x20;

`nvm alias default[node版本号]`设置默认版本
