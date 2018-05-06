---
title: 个人安装Hexo总结
date: 2018-05-05 17:21:00
tags:
    - 软件安装
categories:
    - 软件安装
---

<blockquote class="blockquote-center">仅以此篇文章开启我的hexo博客之旅，记录我踩过的坑...</blockquote>

<!--more-->

## 前言
作为一个计算机专业的人，读了那么多年计算机都没有养成写博客总结的习惯，我表示非常后悔......直到最近找实习了才知道平时写写东西总结一下有多么重要（*不仅仅是面试的时候有加分，更重要的是对自己的学习有个总结，以后还可以经常查看复习*）。最近复习的时候看了网上很多博客，然后惊奇的发现很多博客都是一样的模板，然后我还很喜欢这种风格，然后发现都是用hexo搭建的，Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。它主要需要两个辅助工具，一个是git，一个是Node.js。于是乎，好奇宝宝我也想着自己搭建一下个人博客吧，现在开始虽然有点晚，但总比什么都不做好。本文主要只介绍安装过程遇到的一些坑，具体使用方法不做解释，因为我自己也还不熟，具体使用方法可以查看[官方文档](https://hexo.io/zh-cn/docs/index.html).以下便开始正式安装过程了：


## Git安装
首先，安装第一个“助攻”----git。git是一个分布式版本管理系统，用过之后就会发现它的强大。由于我是之前在项目中就有用到过git和github，所以电脑里已经安装好了，这里安装的时候主要参考的是[廖雪峰老师的网站](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)，安装的时候基本也没碰到什么问题。
下面问题来了，划重点！！！我在后面安装好hexo，使用hexo d部署到github的时候就开始报错了:
```
HttpRequestException encountered.
......
bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com': Invalid argument
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Error: bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com': Invalid argument

    at ChildProcess.<anonymous> (G:\blog\minyang.github.io\node_modules\hexo-util\lib\spawn.js:37:17)
    at emitTwo (events.js:106:13)
    at ChildProcess.emit (events.js:191:7)
    at ChildProcess.cp.emit (G:\blog\minyang.github.io\node_modules\cross-spawn\lib\enoent.js:40:29)
    at maybeClose (internal/child_process.js:877:16)
    at Process.ChildProcess._handle.onexit (internal/child_process.js:226:5)
FATAL bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com': Invalid argument
```
大概意思就是说找不到我的github用户名，excuse me???然后我就去 ~~百度~~ google了一下，然后找到了[这篇博客](http://www.cnblogs.com/xuey/p/8474071.html)，博客中说，截至2018年2月22日，GitHub禁用了对弱加密的支持，这意味着许多用户会突然发现自己无法使用Git for Windows进行身份验证（影响版本低于v2.16.0）。不要恐慌，有一个修复。将Git for Windows更新到最新版本（或至少v2.16.0）。问题基本确定了，因为我的git版本太低了（原本是2.14.0），然后我就试图寻找直接update git的方法，遗憾的是，Windows下Git貌似没有自动更新功能，Linux和mac下都有命令行更新命令，好咯，我用Windows怪我咯。最后只能卸载原来的git重新安装了最新的版本（2.17.0）。更新之后其实原本的密钥什么的都不用改，原本的项目什么的也都还在，所以还好。更新之后再次hexo d欣慰的发现一次性成功了，没有报错，这是后话了。至此，git的安装基本完成，进入下一个环节。

## Node.js安装
 Node.js是第二个“助攻”，由于我之前就安装过NodeJS，所以这一步也不详细描述，直接官网下载默认安装就行。

## hexo 安装
下面进入正题，开始安装hexo。直接 `window+R` 打开运行框，输入cmd然后回车，打开Windows命令行工具（我在直接在Gitbash命令行执行下面的操作的），然后在命令行运行下面的命令，在全局环境安装hexo。
```
npm install -g hexo-cli  或者 npm install -g hexo
```
安装完成就可以开始正式创建博客网站了：
> * 首先，这里需要在自己的电脑里新建一个文件夹用来作为博客网站的根目录，以后所有跟博客网站有关的文件都在这个文件夹里了，这里我直接命名为blog。
> * 然后在命令行进入新建的blog文件夹，执行 `hexo init` 初始化博客文件夹，等待几分钟之后我们的博客网站基本就完成一半了，可以执行 `hexo s` 启动本地服务器预览，然后就可以在浏览器打开[localhost:4000](localhost:4000)查看博客的雏形了，这里打开的应该是默认的hello_world博客页面。
> * 接下来我们再稍微熟悉一下hexo的几个主要的命令：
```
npm install hexo -g     #安装Hexo
npm update hexo -g      #升级 
hexo init               #初始化博客

# 下面四种命令左边的为简写格式，右边为完整格式
hexo n  "blog name" / hexo new  "blog name"   #新建文章
hexo g / hexo generate                        #生成
hexo s / hexo server                          #启动服务预览
hexo d / hexo deploy                          #部署

hexo server                    #Hexo会监视文件变动并自动更新，无须重启服务器
hexo server -s                 #静态模式
hexo server -p 5000            #更改端口
hexo server -i 192.168.1.1     #自定义 IP
hexo clean                     #清除缓存，若是网页正常情况下可以忽略这条命令
```
> * 下一步是重头戏了，如何将我本地的hexo blog文件夹发布到网络上，让所有人都可以访问到呢？这里我们就需要用到github了：
>   * 首先，在自己的github下新建一个repository，命名格式为 `xxxxx.github.io` ，这里需要注意的是，一定要在名字后面加上 `.github.io`，而且这里的 `xxx` 一定要跟你的github账户名一致（**很重要，很重要，很重要**）！！！例如，我的github账户名为`ym13349140`，那么我的 `repository` 名字必须为`ym13349140.github.io`。
>   * 打开blog目录下的_config.yml站点配置文件，直接拖到最后，将deploy修改为如下然后保存：
>   ```
>    deploy:
>        type: git
>        repository: https://github.com/ym13349140/ym13349140.github.io.git
>        branch: master
>   ```  
>   * 下一步是安装git部署插件，依然是在命令行blog文件夹下，输入命令：
>   `npm install hexo-deployer-git --save`
>   * 最后，在命令行输入 `hexo g` 重新生成博客内容然后运行 `hexo d` 将博客部署到github上。不出意外的话在浏览器打开 `https://xxx.github.io` 应该可以看到默认的hello world博客界面了，大功告成！

*整个过程中最需要注意的应该就是命名的问题，不要问我怎么知道的，我因为这个问题折腾了半天，原本我想用自己的名字命名，把的repository命名为 `minyang.github.io`，hexo d之后打开 `https://minyang.github.io` ，一直404 Not Found，然后傻傻的重装了很多次之后才发现是因为名字不一致导致的，将repository命名为 `ym13349140.github.io`之后立马就可以了，这也是我记录这篇博客的主要目的。*

