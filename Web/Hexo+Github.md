# Hexo + Github 静态博客

Hexo 简易静态博客搭建，托管在 Github 上。

推荐使用 VSCode 编辑文件。

关于 Hexo 主题，范围比较大，本文没有涉及具体的操作。

# 步骤

1. `$ hexo init <folder>`
2. `$ cd <folder>`
3. `$ npm install hexo-deployer-git --save`
4. 设置自己的域名
   1. 到阿里云或其他域名机构注册一个自己的域名，注意国内要实名认证。
   2. 需要创建CNAME文件，并放到source目录下
   3. CNAME 文件只有一行没有回车，写要从 username.github.io 跳转到的域名。见注意事项 2。
5. 创建 git 仓库，名称必须为：&lt;username&gt;.github.io，务必初始化的时候创建一个 Readme 文件，内容随意。
6. 修改_config.yml 添加 delopy 信息
```yml
deploy:
  type: git
  repo: git@github.com:username/username.github.io.git
  branch: master
```
7. 检查 git 设置：
   1. 方案1：设置单独的 `sshkey`、`user.name`、`user.email`，如果有多个账号比较麻烦，需要切换 global 设置。
   2. 方案2：使用常用的 github 账号，添加为该 git 仓库的 Collaborators，就可以直接用常用账号提交了，不用切换设置。见注意事项 3。
8. 写文章
9. hexo deploy

## 注意事项

1. Hexo 的 git 目录是个隐藏目录 .deploy_git，不是hexo目录，每次deploy 会在那个隐藏目录下提交新的 commit。
2. CNAME 文件要放在 source 目录下，否则不会提交到 github。如果在github 的设置里创建，还得下载到 source 目录下，不如直接创建一个。
3. 使用其他账号管理的手，需要 owner 账号先提交一个 commit，否则 github pages 设置项无效。初始化的时候创建一个 Readme 文件就可以了。

# 写文章

## Post

这个命令会创建一个新的 Post：
```sh
$ hexo new post <title>
```
Post 目录结构：
```
- source
  - <title>.md
  + <title>
```

## Page

这个命令会创建一个新的页面：
```sh
$ hexo new page <title>
```
Page 目录结构：
```
- source
  - <title>
    + index
    - index.md
```

# 主题

## 安装使用
一般的主题都放在 github 仓库里，clone 或者下载下来
`/themes/` 下

## 配置文件 _config.yml

有两个需要关心的配置文件：
1. 整个网站的配置文件：/_config.yml
   1. 负责全局配置：网站标题、作者名称等
2. 主题的配置文件：/themes/&lt;theme name&gt;/_config.yml
   1. 外观设置：导航菜单、copyright信息等等

## 用过的主题
- Next，可能是用的最多的主题，网上资料丰富
  - [主页](http://theme-next.iissnan.com/)
  - [Github](https://github.com/theme-next/hexo-theme-next)
- Chan，适合当做照片墙，还凑合
  - [Github](https://github.com/denjones/hexo-theme-chan)
  - [示例](http://blog.sprabbit.com/hexo-theme-chan/)