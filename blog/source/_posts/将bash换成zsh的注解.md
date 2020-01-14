---
title: 将bash换成zsh的注解
date: 2020-01-14 19:28:59
tags:
---
> ### 版本
> Ubuntu: 16.04LTS WSL
> 参考：https://www.jianshu.com/p/d194d29e488c

## 安装zsh
```bash
sudo apt install zsh
```

## 安装oh-my-zsh
```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
（原本是应该这样的，但由于连接不上，因此使用代理进行下载，假设拥有本地1080端口的socks5代理服务器）  
```bash
sh -c "$(curl --socks5 127.0.0.1:1080 -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## 切换zsh主题
`ZSH_THEME`里面填上你喜欢的主题，内置主题可见：https://github.com/ohmyzsh/ohmyzsh/wiki/Themes  
```bash
vim ~/.zshrc
ZSH_THEME="avit"
```
应用设置  
```bash
source ~/.zshrc
```

## 安装自动补全插件
（这个插件可能有点卡，不过还好用）  
```bash
mkdir ~/.oh-my-zsh/custom/plugins/incr
cd ~/.oh-my-zsh/custom/plugins/incr
wget http://mimosa-pudica.net/src/incr-0.2.zsh
mv incr-0.2.zsh incr.plguin.zsh
vim ~/.zshrc
```
修改  
```bash
plguin = (git incr)
```
应用设置  
```bash
source ~/.zshrc
```

## 修改环境变量
以node为例，发现node命令找不到了，因此需要修改一下环境变量。  
```bash
vim ~/.zshrc
```
将第一行`export PATH=...`取消注释，末尾加上：
``` bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```
应用设置  
```bash
source ~/.zshrc
```
