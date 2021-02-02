---
title: 'Github Pages配置 + 域名注册 + 绑定'
tags: life Y2021
---
> 我真的服了，为了这个东西忙了一天，做个总结记录一下

## Github Pages

注册和访问部分没什么好讲的，网上很多[教程](https://sspai.com/post/54608 "教程")，在这里主要分享一下关于网站模板的应用。

github自带的模板特别简单，除了一些css样式剩下的就是一个面板，如果你想有个花里胡哨的Blog页面的话就要自己写或者伸手拿一个大佬做好的模板。

我这里用的是[TeXt](https://github.com/kitian616/jekyll-TeXt-theme "TeXt")，步骤简单总结就是：

- fork 到自己的目录（star 一下大佬）
- 把名字改成 `username.github.io`（这里一定要是自己的 username，否则无法访问）
- 下载github desktop，改一改大佬的设置（_config.yml），然后在 _posts 上传自己的文章，命名方式为 `year-month-day-namexx.md`
- 等个几十秒就可以看到自己的文章啦

## 域名注册

这里的 motivation 主要是原来的链接好长，正好现在`.me`域名好火就想着自己注册一个简单域名顺便绑定到 GitHub page 上，这里最好不要在国内的平台购买域名（阿里云或者是腾讯云）。

我选择的平台是[狗爹](https://sg.godaddy.com/ "狗爹")，当然还有一个`name.com`也可以进行选择比价。

最后花了大概 200RMB 购买了域名 + 基础虚拟机，没舍得买ssl（狗爹卖的好贵啊，我服了），准备在国内买个便宜的弄一下，后面弄好了会更新。

## 绑定

分为 GitHub 端和自己域名端：

### GitHub端

- 新建一个文件，命名为 CNAME（一定大写），其内容为你的域名： xx.xx
- 之后将 Setting 中的 Custom domain 同样设置为自己的域名： xx.xx

### 域名端

这里以我购买的平台GoDaddy为例

- 首先 ping username.github.io 得到ip地址并记住
- 打开域名 DNS 配置，设置如下：

![DNS](https://i.loli.net/2021/02/02/r1k6xCGUVyFLP23.png)

- 之后就是等待大概十分钟，就可以正常访问了~


