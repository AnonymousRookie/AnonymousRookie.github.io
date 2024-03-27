---
layout: post
title:  "jekyll github blog搭建"
categories: jekyll github blog
tags: jekyll github blog
author: ZhangJie
---

* content
{:toc}
jekyll github 博客搭建。




## jekyll github搭建博客主页

https://pages-themes.github.io/midnight/

http://www.pchou.info/ssgithubPage/2014-07-04-build-github-blog-page-08.html


leopardpan.github.io-master


### 本地环境搭建

1. 安装ruby(rubyinstaller-2.3.3.exe)
```sh
E:\>ruby -v
ruby 2.3.3p222 (2016-11-21 revision 56859) [i386-mingw32]

C:\>gem -v
2.5.2
```

2. 安装DevKit(DevKit-mingw64-32-4.7.2-20130224-1151-sfx.exe)

```sh
C:\DevKit>ruby dk.rb init

C:\DevKit>ruby dk.rb install
```

3. 安装jekyll
```sh
gem install jekyll

C:\>jekyll -v
jekyll 3.8.4
```

4. 安装bundle
```sh
gem install bundle
```


5. 安装jekyll-paginate
```sh
gem install jekyll-paginate
```

打开服务:

```sh
bundle exec jekyll serve

或者：

jekyll serve
```


```sh
$ bundle exec jekyll serve
Configuration file: D:/MyGithub/local_home_page/_config.yml
            Source: D:/MyGithub/local_home_page
       Destination: D:/MyGithub/local_home_page/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
                    done in 0.857 seconds.
  Please add the following to your Gemfile to avoid polling for changes:
    gem 'wdm', '>= 0.1.0' if Gem.win_platform?
 Auto-regeneration: enabled for 'D:/MyGithub/local_home_page'
    Server address: http://127.0.0.1:4000
  Server running... press ctrl-c to stop.
[2017-12-31 23:33:44] ERROR `/favicon.ico' not found.
```

