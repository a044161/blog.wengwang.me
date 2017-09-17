---
title: 从GHOST迁移到HEXO（引入 Travis CI）
date: 2017-09-17 20:50:00
photos:
- https://oss.wengwang.me/images/bg/travis_hexo.png
tags:
- CI
- Linux
- Hexo
categories: 
- Others
---

[![](https://badge.juejin.im/entry/59be93ec5188255be81f7dee/likes.svg?style=flat-square)](https://juejin.im/entry/59be93ec5188255be81f7dee/detail)

# 开篇闲扯
上一周，不懂发生了什么误操作，导致我的服务器上的`mysql`跪了，然后通过快照进行恢复，本以为一切顺利，没想到数据库里面的数据全都乱码了，折腾了各种转码，还是不行，找到了一个可以测试各种编码结果的网站[乱码恢复](http://www.mytju.com/classcode/tools/messyCodeRecover.asp)，好在数据不多，通过很挫的手工方式进行一个个恢复。  

<!-- more -->

之后问了一下同事其实可以有更简便，针对多数据进行恢复的方式，先将数据库里面的数据导出，然后写一段转码的脚本，进行转换，然后再导回数据库。  
当时发生了这次事故之后，我选择了更换博客的方案，由原来的`Ghost`换为`hexo`+`oss`+`travis ci`，原先在阿里云上购买了一个100G的硬盘，现在也被释放了，将图片存储到了`oss`。一开始的想法是，博客本身就可以只是一篇篇的`markdown`组成，不需要动用到数据库，`hexo`就是这么的轻量，部署之后，原来的`Ghost`需要占用内存100MB+，现在直接就降到了1MB，之后配合`hexo generate`又替换为了`nginx`静态站点。或许有人看到这里就会想说，为什么不直接使用`gitbug pages`和`hexo delpoy`就可以了，有以下几个理由：

1. 爱折腾
2. 爱折腾
3. 爱折腾
4. 我有服务器
5. 我有服务器
6. 我有服务器

当然通过`gitbug pages`和`hexo delpoy`可以方便很多，也可以自定义域名什么的，但还是放在自己的服务器比较安心，因为国内的墙，你懂的。好了，接下来该进入正文了。  

# Hexo 安装

对于`hexo`的安装其实[官网](https://hexo.io/docs/setup.html)的教程已经很简单了，包括配置信息，都介绍的通俗易懂，我就简单概括一下。  

## 1.安装  

强烈建议大家使用`cnpm`来进行安装，先安装`hexo`的命令行工具
```
cnpm install hexo-cli -g
```

## 2.初始化目录

切换目录到你想要用来存放你`hexo`博客的目录下

```
hexo init blog_name // 会生成初始的目录
cd blog_name // 切换到目录下
cnpm install // 安装依赖文件
```

## 3.目录

```
├── _config.yml // 核心的配置文件
├── package.json
├── scaffolds // 当你新建一篇博文时，会根据里面的定义好的初始结构，生成文件
├── source // 源博文件
|   ├── _drafts // 草稿箱
|   └── _posts // 正式的文章
└── themes // 主题

```

## 4.启动项目

在目录下  

```
hexo server
```
这是访问`http://localhost:4000`就能看见了

到这里位置，就已经完成了全部的安装，还有更多细节的地方，我就不在这里一一讲述，可以自行查阅[官网](https://hexo.io/docs/setup.html)，因为这些不是本篇博文的重点，到了这里，才刚刚开始。

# Travis CI登场

其实按照上面的那些配置，完成可以在自己的服务器上面跑起来了，先在自己的服务器上安装`pm2`进行进程管理，再拉取博客到服务器上，回到博客的目录，再创建一个`server.sh`
```
#!/usr/bin
hexo servr
```
然后通过`pm2`启动服务，`pm2 start server.sh`，之后在`nginx`的`/etc/nginx/sites-available`中新建一个配置`myblog.config`，然后加入以下内容
```
server{
    listen 80;
    server_name: wengwang.me // 你的域名
    location / {
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   Host      $http_host;
        proxy_pass         http://127.0.0.1:4000;
    }
}
```
保存，再将这个文件软连接到`sites-enabled`中，`ln /etc/nginx/sites-available/myblog.config /etc/nginx/sites-enabled/myblog.config`，之后再重启`nginx`，`service nginx restart`即可，直接访问你的域名就能看见啦。  
但是这样怎么能够满足呢，因为这个时候每当你提交一篇博文时，需要进行以下几个操作：

1. 提交博文到`github`
2. 登录到服务器
3. 切换到博客的目录下
4. 拉取

这样就会显得很麻烦，我们就要把步骤简化到只要`git push`就可以了。  

## 1.注册Travis CI  
登录到Travis CI的[官网](https://www.travis-ci.org/)，这里要注意一下，根据你仓库的不同，访问的地址也不同，私有仓库访问的是[https://www.travis-ci.com/](https://www.travis-ci.com/)，公共仓库访问的是[https://www.travis-ci.org/](https://www.travis-ci.org/)，根据里面的提示，一步步完成和`github`的关联即可，简单来说就是，用`github`账号登录`Travis CI`，然后在`Travis CI`里面配置同步你的仓库，找到你的仓库并打开，再回到`Travsi CI`的首页的侧边栏就能看见你的仓库了。  

## 2.添加.travis.yml  
回到你的博客文件夹下，在根目录添加一个`.travis.yml`文件，这个文件用来编写你的流程

## 3.配置.travis.yml

```
language: node_js # 设置语言为nodejs
node_js: stable # 选择nodejs版本
sudo: true # 开启管理员权限
install: 
- npm install # 安装依赖文件
script:
- hexo g # 编译hexo
```
先编写这些内容进行测试，看看是否能够跑通，保存之后，提交代码到`master`分支，这时`Travis CI`就会收到`github`的消息，根据`.travis.yml`文件进行构建。这时回到`Travis CI`就能看到在运行了，一切顺利的话，就会有`passing`的状态，如下图  
![travis-hexo-0](https://oss.wengwang.me/images/wengwang_me/hexo/Others/travis-hexo-0.png)  
这时看`log`的话，其实可以看见`hexo g`生成了好多的文件，其实就在当前的构建的目录`publish`文件夹下  
![travis-hexo-0](https://oss.wengwang.me/images/wengwang_me/hexo/Others/travis-hexo-1.png)  
如果遇到了失败的话，可以查看下面的`log`，再查找相对应的解决方案。  
成功的话，说明`Travis CI`和`github`之间已经成功跑通了，接下来我们就开始折腾服务器了。

## 4.配置`nginx`  

我选择的是静态站点的方式，如果是起服务的话，安装我上面的那种配置就好。
选择静态站点的话，就要对`nginx`的`/etc/nginx/sites-available/myblog.config`，重新进行配置

```
server{
    listen 80;
    server_name: wengwang.me // 你的域名
    location / {
        root /your_site_dir; // 站点的根目录
        index index.html; // 站点的首页
    }
}
```
同样再软连接`ln /etc/nginx/sites-available/myblog.config /etc/nginx/sites-enabled/myblog.config`，在你站点的根目录下可以创建多个`html`文件，写一些不同的内容，然后再重启`nginx`，`service nginx restart`即可，这时访问域名验证以下内容是否正确。  
`nginx`的配置到这里就算结束了，为了安全起见，我们要配置一个专门用来构建的本地账户。  

## 6.创建用户  
可以先创建一个用户组`groupadd deploy`，这个用户组专门用来赋予部署的权限。  
再通过命令`useradd -g deploy travisuser`创建用户，并把它放入用户组，然后 `passwd travisuser`创建密码，这时用户会被默认放在 `/home/travisuser`目录下，一定要记得到 `/home`下看看是否有`travisuser`这个文件夹，不然会影响接下来的操作。  
远程登录一下，看看是否添加成功了。

## 7.免密码登入

因为`Travis CI`是没有交互界面的，所以我们要弄一个免密码登录，这个时候就要建立本地设备和远程服务器的互信。  
在本地设备上，生成一个公钥
```
ssh-keygen -t rsa
```
可以选择一路回车下去，也可以选择在第一个提示时选择自己存放公钥的位置。  
生成完成之后，我们就要把公钥传输到远程服务器，并记录
```
ssh-copy-id travisuser@your_server
```
第一次会要求你进行身份验证，验证通过后，就进行测试
```
ssh travisuser@your_server
```
看看是否以及免密码登入了。

## 8.安装Travis工具  

这时回到你的本地上，安装`Travis`工具，不同的操作系统，有不同的安装方式，直接查阅[文档](https://github.com/travis-ci/travis.rb#installation)，安装完成后登陆`travis login`

## 9.加密关键信息  

这时我们一共要加密两种信息，一种是公钥文件，一种是敏感信息。  
因为我们要与服务器建立互信机制，所以要将密钥文件带入，又不能以铭文的形式存在，所以要进行一次加密，我们在本地上将目录切换到博客的根目录下，然后对密钥进行加密
```
travis encrypt-file /User/xx/.ssh/id_rsa --add
//id_rsa 即刚刚生成的密钥文件
// --add 会自动添加到.travis.yml文件中
```
随后在`.travis.yml`文件中就能看到添加了一下内容  
```
before_install:
- openssl aes-256-cbc -K $encrypted_9d99a4372840_key -iv $encrypted_9d99a4372840_iv
  -in id_rsa.enc -out ~/.ssh/id_rsa -d
- chmod 600 ~/.ssh/id_rsa # 这两行是防止登录时的授权询问
- echo -e "Host 111.111.111.111\n\tStrictHostKeyChecking no\n" >> ~/.ssh/config
```
有的时候会被自动加上`\`进行转义，我们要去掉。  
为了保险起见，我不想被其他人知道我的服务器地址、端口、用户名、远程目录地址，这时我还要对这三个信息进行加密，我个人的习惯是现在`.travis.yml`中先把变量名定好
```
after_success:
- scp -r -P $SSH_PORT ./public/* $SSH_USER@$SSH_IP:$SERVER_PATH # 上传文件

// $SSH_PORT -> ssh端口号
// $SSH_USER -> 用户名
// $SSH_IP -> 服务器ip地址
// $SERVER_PATH -> 远程目录地址
```
要实现这个功能，同样是借助`Travis`的工具
```
travis encrypt SSH_PORT=22 --add
travis encrypt SSH_USER=travisuser --add
travis encrypt SSH_IP=111.111.111.111 --add
travis encrypt SERVER_PATH=/www/blog --add
```
这样就会在`.travis.yml`中多了以下信息
```
env:
  global:
  - secure: xxx
  - secure: xx
  - secure: xx 
  - secure: xx 
```
ok，加密工作完成，这下我们再进行测试一下，先修改一下`.travis.yml`
```
after_success:
- ssh -p $SSH_PORT $SSH_USER@$SSH_IP 'pwd'
```
然后推送到`master`分支，这时看看`Travis`的页面，耐心等待构建过程，之后再看看是否会完成免密码登录，再看看是否有登入后远程服务器的路径，一切顺利的话，就已经离成功不远了。

## 10.通过`scp`上传文件  
一开始我选择的方案是`ftp`方式，期间踩了好多坑，当一切都跑通之后，才发现，`ftp`不能传输整个文件夹，当场就懵逼了，之后才换到了`scp`，具体`scp`是什么可以自行百度，简单来说就是`ssh`版的`cp`，ok，满足需求了，开搞。  
为了保证能够跑的通，我们可以在本地先`hexo g`然后`scp -r  ./public/* traviuser@111.111.111.111:/www/blog`看下是否会免密码上传。可能这里会遇到一些文件夹的权限问题，可以在远程服务器上修改文件夹的权限，一种方式是`chmod -R 777 /www/blog`，一种是用户组`chmod -R root:group_name /www/blog`，根据自己的需求选择。成功上传后，因为之前已经配置过`nginx`了，所以直接访问就好，如果看到了页面了，说明就成功了。  这时我们就能安心的修改 `.travis.yml`了，添加
```
after_success:
- ssh -p $SSH_PORT $SSH_USER@$SSH_IP 'rm -f $SERVER_PATH' # 删除原目录，因为scp无法执行删除命名
- scp -r -P $SSH_PORT ./public/* $SSH_USER@$SSH_IP:$SERVER_PATH # 上传文件
```
大功告成，接下来是完整的`.travis.yml`配置
```
language: node_js # 设置语言为nodejs
node_js: stable # 选择nodejs版本
sudo: true # 开启管理员权限
install: 
- npm install # 安装依赖文件
script:
- hexo g # 编译hexo
before_install:
- openssl aes-256-cbc -K $encrypted_9d99a4372840_key -iv $encrypted_9d99a4372840_iv
  -in id_rsa.enc -out ~/.ssh/id_rsa -d
- chmod 600 ~/.ssh/id_rsa # 防止登录时的授权询问
- echo -e "Host $SSH_IP\n\tStrictHostKeyChecking no\n" >> ~/.ssh/config
after_success:
- ssh -p $SSH_PORT $SSH_USER@$SSH_IP 'rm -f $SERVER_PATH' # 删除原目录，因为scp无法执行删除命名
- scp -r -P $SSH_PORT ./public/* $SSH_USER@$SSH_IP:$SERVER_PATH # 上传文件
env:
  global:
   - secure: xxx
   - secure: xx
   - secure: xx 
   - secure: xx 
```

如果是`hexo server`的方式的话，就只要把`after_success`的进行替换，先登录，然后获取仓库就可以了。  
之后只要简单的`git push`就能完成一次。

# 走了不少弯路

其实没有折腾都不知道，发现自己还是缺少很多的知识，比如`nginx`的配置，`linux`下的用户权限管理，也把`vsftp`玩了一遍，经过了一天的折腾，尝试了各种方案，终于将博客的整个方案搞定了。也想过通过`github`的`Webhooks`自己搭一个`CI`，一切都跑通了，但是还想到了进程该如何的管理，整个方案该怎么制定，最终还是换到了`Travis CI`，配置看着简单，但是也要有很多运维的知识进行支撑，这次也算增加了一点经验，每套方案都要经过各个方面的测试，这是我在这次实践中发现的问题，想要测试各个节点上面的实现，不然某个节点，因为一些无法避免的问题而放弃的话，那前面的折腾算白折腾了（从时间角度来说），折腾总是好，因为，人生本来就是用来折腾的。

# 结语

这篇博文算是一个总结，也希望能够给大家通过不同的思路，方法千千万万，没有最优，只有最合适的。这次折腾过后，也对`CI`有了一定的了解，除了它本身的含义`持续集成`外，我也想到了自己搭建`CI`的想法，希望之后能够开源一个轻量级的`CI`给大家，毕竟，东西在自己手上才最放心。