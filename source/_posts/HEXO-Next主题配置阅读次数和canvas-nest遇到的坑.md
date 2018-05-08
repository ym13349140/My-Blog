---
title: HEXO-Next主题配置阅读次数和canvas-nest遇到的坑
date: 2018-05-08 12:36:37
tags:
    - 软件安装
    - hexo
categories:
    - 软件安装
---

<blockquote class="blockquote-center">本篇主要记录HEXO-Next主题配置阅读次数和canvas-nest时遇到的坑</blockquote>

<!--more-->
### hexo next主题配置博客阅读次数
前两天把next主题更新到了最新版本之后，发现themes配置文件里的leancloud_visitors属性多了一个security字段，具体security字段安全性的解释，参考[hexo-leancloud-counter-security的github主页](https://github.com/theme-next/hexo-leancloud-counter-security)，为了安全性起见，我就开始配置security选项，具体配置过程请参考[Leancloud访客统计插件重大安全漏洞修复指南](https://leaferx.online/2018/02/11/lc-security/)，这篇博客过程非常详细清楚，按照博客里说的，正常配置完成后，执行 `hexo d` 的时候，插件都会扫描本地source/_posts下的文章并与leanCloud数据库对比，然后在数据库创建没有录入数据库的文章记录。但是我按照博客的流程配置完之后，执行 `hexo d` 之后leanCloud并没有出现新创建的博客的数据记录，然后直接打开新创建的博客时阅读次数就会出现Counter not initialized! See more at console err msg.，查看浏览器的console就会看到一条报错，说是需要安装hexo-leancloud-counter-security，但我其实已经安装好了。我也google了很久，网上也没有相关的问题，只有上面那一篇博客是最详细的，然后我在那篇博客的评论里看到了一个关于deploy缩进的评论，我就抱着试一试的态度改了一下，居然真的可以了，被缩进折磨了一整天，晕死....

问题的关键就在于博客根目录的配置文件中deploy属性的配置，安装上面那篇博客的流程，在我们执行完 npm 安装完 hexo-leancloud-counter-security 之后，需要在配置文件中修改deploy属性，将 hexo-leancloud-counter-security 添加到deploy选项中，问题就出在这里，博客里的流程是这样写的：
{% qnimg 软件安装/hexo-next主题deploy配置security-counter.PNG title:hexo-next主题deploy配置security-counter alt:hexo-next主题deploy配置security-counter示意图 extend:?imageView2/2/w/600 %}
这里问题就来了，图中的type前面是加了缩进的，注意这里一定不要缩进，不要缩进，不要缩进！！！重要的事说三遍！下面是我的deploy属性添加 hexo-leancloud-counter-security 前和添加后的配置内容对比：
```yml
# 添加前type前面是有缩进的，而且没有“-”
deploy:
  type: git
  repository: https://github.com/ym13349140/ym13349140.github.io.git
  branch: master
```

```yml
# 添加leancloud_counter_security_sync配置后，type行一定要顶格写，不要缩进
deploy:
- type: git
  repository: https://github.com/ym13349140/ym13349140.github.io.git
  branch: master
- type: leancloud_counter_security_sync
```
为了形成对比，以下是我之前错误的配置格式：
```yml
# 错误配置，错误配置，错误配置
# 这里在两个type之前都添加了缩进，导致在执行hexo d的时候后面这个type实际没有执行成功，具体原因我也不知道
deploy:
  - type: git
    repository: https://github.com/ym13349140/ym13349140.github.io.git
    branch: master
  - type: leancloud_counter_security_sync
```

这里还有一个需要注意的地方就是：配置deploy的时候，如果只有一个type，则直接缩进type就行，不需要加 `-` 和空格，但是当有多个deploy选项的时候，就需要在每个type前面加 `-` 和空格，而且要顶格开始。

### hexo next 主题开启北京动画canvas-nest效果遇到的问题
也是在next主题更新之后，发现原本的themes配置文件都得重新配，然后在开启canvas-nest为true的时候发现没有效果，查看浏览器console发现是一直报错，找不到 `/lib/canvas-nest/canvas-nest.min.js`，不管我怎么执行 `hexo g` 或者 `hexo d` 都没有办法自动生成这个js文件，我一度以为是自己配置的问题，因为我没更新之前好像就是直接设置了themes配置文件里的 `canvas-nest: true` 就可以呈现效果了，然后我又开始google了，找到一篇博客（[为next主题添加nest背景特效](http://www.jinfang.life/posts/c00b4e03/)），是手动在 `next/layout/_layout.swig` 里面引入 cdn 的canvas-nest.min.js 文件,我试了这种办法确实可行，但是并不能消除掉浏览器console的那个报错，然后我就有点强迫症，非要把这个问题解决了不可。我又去查看 themes 主题的github主页的 readme ,发现有这么一句话：
{% qnimg 软件安装/hexo-themes主题增加plugin方法.png title:hexo-themes主题增加plugin方法 alt:hexo-themes主题增加plugin方法示意图 extend:?imageView2/2/w/600 %}
大概意思就是说，要想增加某项功能，需要将themes配置文件中的相关属性设置为true，然后去到该属性上面的那个github下查看该属性的安装方法，比如说，我这里要安装 canvas-nest.min.js，它的github是这样写的：
{% qnimg 软件安装/hexo-themes主题下canvas-nest安装方法.png title:hexo-themes主题下canvas-nest安装方法 alt:hexo-themes主题下canvas-nest安装方法示意图 extend:?imageView2/2/w/600 %}
然后我按照这个步骤，从命令行进入themes/next文件夹下，执行了git clone操作之后，一切都OK了。

### 总结
强迫症真的是很难受，总是被这样的小问题折磨很久，有时候其实这个问题不管它也不影响什么，比如上面的两个问题，大不了不要特效，不要阅读次数也不影响什么功能，但是一旦我发现了这个问题不去解决，强迫症的心里总有个疙瘩，很不舒服，所以就会耗很多时间在这上面。有时候这种追求答案的精神可能很重要，但是有时候真的是会无端浪费许多时间。
总的来说呢，通过这两个小问题呢，也有些体会：一是，凡事要记得多看官方文档，尤其是github主页或者issues上，一般都会有很多有用的信息；二是，细节真的很重要，要做一个细心的人。