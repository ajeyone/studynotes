# macOS 安装 node

## 注意
> 不要使用可执行安装包程序安装。
> 不要使用可执行安装包程序安装。
> 不要使用可执行安装包程序安装。

使用可执行安装包程序安装，会有权限问题，应直接使用 nvm 安装。

## nvm 安装

直接去github主页找安装脚本：https://github.com/nvm-sh/nvm

截止至2021年12月18日是 `v0.39.1` 版本：

```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
```

## nvm 用法

```bash
nvm install stable ## 安装最新稳定版 node
nvm install <version> ## 安装指定版本
nvm uninstall <version> ## 删除已安装的指定版本
nvm use <version> ## 切换使用指定的版本node
nvm ls ## 列出所有安装的版本
nvm ls-remote ## 列出所有远程服务器的版本
nvm current ## 显示当前的版本
nvm alias <name> <version> ## 给不同的版本号添加别名
nvm unalias <name> ## 删除已定义的别名
nvm reinstall-packages <version> ## 在当前版本 node 环境下，重新   全局安装指定版本号的 npm 包
nvm alias default [node版本号] ##设置默认版本
```