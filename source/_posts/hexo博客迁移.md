---
title: hexo博客迁移
date: 2019-06-22 16:50:33
tags:
- hexo优化相关
categories:
- hexo优化相关
---



#### 前言

买了台电脑，之前的电脑太卡了，准备弃用，随之进行hexo博客迁移的操作。

## 思路

第一想到的是直接将hexo整个进行迁移，然后将环境进行恢复搭建.

## 执行

因为之前的博客路径就在固态硬盘里，给新电脑装上之前的固态盘，博客整体迁移。

开始进行环境恢复：nodejs(主要使用npm)+git+github

nodejs也是在我固态直接访问路径 执行node -version 正常，但在cmd输入 `npm install -g hexo`

命令无效，随后将整个目录删掉重新安装，执行`npm install -g hexo`   ok。

git直接安装无配置。

之前的博客路径 右键 Git Bash Here 进入git命令行，键入

```
npm install
npm install hexo-deployer-git --save  // 文章部署到 git 的模块
下面为选择安装：
npm install hexo-generator-feed --save  // 建立 RSS 订阅
npm install hexo-generator-sitemap --save // 建立站点地图
```

安装成功 输入 `hexo server` hexo会默认监听4000

直接访问localhost:4000 这个时候应该可以看到自己的博客了。

为了将博客和github建立联系配置sshkey

本地创建sshkey `ssh-keygen -t rsa -C "XXX@XXX.com"`

创建时有几个属于填入的，直接回车跳过，创建的ssh是在C:\Users\用户名\。ssh(英文点号)。

将此目录下**id_rsa.pub**打开记事本即可，这个就是sshkey 将内容复制到自己的博客github，添加sshkey是在setting里，这里不多说；

Title随意起，将内容复制到key里保存，注意此时这个key是灰色的表示这个key还从未使用过。

回到git命令行输入`ssh -T git@github.com`，出现 

`Hi xxx! You've successfully authenticated, but GitHub does not provide shell access.`

代表ssh配置完成，这里我直接`hexo -d -g` 出现警告

```
clean Remove generated files and cache. config Get or set configurations. deploy Deploy your website. generate Generate static files. help Get help on a command. init Create a new Hexo folder. list List the information of the site migrate Migrate your site from other system to Hexo. new Create a new post. publish Moves a draft post from _drafts to _posts folder. render Render files with renderer plugins. server Start the server. version Display version information. Global Options: --config Specify config file instead of using _config.yml --cwd Specify the CWD --debug Display all verbose messages in the terminal --draft Display draft posts --safe Disable all plugins and scripts --silent Hide output on console For more help, you can use 'hexo help [command]' for the detailed information or you can check the docs: http://hexo.io/docs/

释义：清除删除生成的文件和缓存。配置获取或设置配置。部署部署您的网站。生成生成静态文件。帮助获取有关命令的帮助。初始化创建一个新的HEXO文件夹。列表列出站点迁移的信息，将站点从其他系统迁移到HEXO。新建创建新日志。发布将草稿文章从“草稿”文件夹移动到“文章”文件夹。使用渲染器插件渲染渲染文件。服务器启动服务器。版本显示版本信息。全局选项：--config指定配置文件而不是使用config.yml--cwd指定cwd--debug显示终端中的所有详细消息--draft显示草稿发布--safe禁用所有插件和脚本--silent隐藏控制台上的输出有关详细帮助，可以使用“hexo help[command]”获取详细信息，也可以检查do网址：http://hexo.io/docs/
```

这里我会头想，应该是整个hexo里之前的存在配置文件 .git .deploy_git 等等 影响了现在的使用，网上翻文章发现我这样整个的是错误的

参考：<https://blog.csdn.net/m0_37286282/article/details/89496837>

只需要将hexo下的 

```
_config.yml
package.json
scaffolds/
source/
themes/
```

复制就行，随即我将这些文件放入新的文件夹，创建一个新文章进行测试，成功！

另外网上还有另外一种方法，是使用gti clone对hexo文章进行检出备份的这样进行迁移，我并未自己实践

文章：<https://www.jianshu.com/p/beb8d611340a>