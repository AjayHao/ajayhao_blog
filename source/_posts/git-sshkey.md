title: 应对多套git环境的ssh-key管理方案
tags:
  - git
categories:
  - 技术
thumbnail: '/images/thumbnails/git.png'
cc: true
comments: true
date: 2016-04-26 19:09:00

---

还在脱密期，已感受到了新东家的热(hou)情(ji)，包括工作邮箱、vpn、git等环境已邮件告知，能远程办公这点比起现在倒是人性化了些，要再能不上班在家干活就更好了

（老婆：纳尼！干毛活！在家就是我的！！）
（老板：周末在家干活行，平时？想多了吧！）

走http协议拉了个代码，每次切换分支都要输入口令进行验证，后续写起代码提交勤快了还了得？。。由于家中已配置了GitHub的SSH key，如何集成公司的GitLab环境的SSH key，使同时用GitHub与公司GitLab时能走不同的key，免去频繁的口令验证呢？下面介绍两种管理多套环境的key的方法...

<!-- more --><!-- indicate-the-source -->

友情提示：Windows平台下的童鞋，以下操作请到Cygwin/msysGit等支持git bash的环境下执行。

以下结合GitHub官方参考指引和相关资料，介绍两种方法:
### 方法一  使用ssh-agent
#### 1.  [检查已有的SSH key](https://help.github.com/articles/checking-for-existing-ssh-keys/)
#### 2.  [生成key并加入ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)
此处是方法一的关键，度娘上的答案大多只提到怎么生成key，却没有提到要加入ssh-agent，另外在执行ssh-add时遇到
 “Could not open a connection to your authentication agent”，查到的解法基本都是执行

    $ ssh-agent bash
    $ ssh-add
  然后呢？还是不行，反正我试下来不行，stackoverflow中也中一篇解答给了关键，这在2中也有所体现。

    $ eval `ssh-agent -s`
问题解决~，查看ssh-agent中的密钥

    $ ssh-add -l

#### 3. [加到Git账户中](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)
不单单适用于GitHub，其他Git仓库也类似

### 方法二. 在config中配置host
第1步、第3步和方法一相同，主要差别在第2步
#### 2. 生成key并配置config文件
生成key的方式也和方法一的第2步一样。生成后在~/.ssh 目录下创建config文件，配置私钥对应的服务器，假设为公司GitLab生成的私钥为id_rsa_work，则config文件内容如下：

    Host gitlab.xxx.com
        HostName gitlab.xxx.com
        User git
        PreferredAuthentications publickey
        IdentityFile ~/.ssh/id_rsa_work

 第一行的 Host 只是一个名字，第三行的 Hostname 才是对应的真实地址，但是两者最好保持一致，这样后续git链接中的地址就不需要转换。

### 最后进行SSH连接测试
执行以下命令

    #测试GitHub以及公司链接
    $ ssh -T git@github.com
    $ ssh -T git@gitlab.xxx.com

结果如下：
![测试结果](/images/posts/git-sshkey/result1.png)

验证通过后，GitHub的key状态还会发生变化
![验证前](/images/posts/git-sshkey/pic1.png)

![验证后](/images/posts/git-sshkey/pic2.png)

另外在失败时，可以通过在ssh中加上-v参数，查看具体的处理过程，进行更精确的定位.

     $ ssh -v -T git@github.com

![带-v参数](/images/posts/git-sshkey/debug.png)

### 小结

两个方法都能实现多套key的存储，但方法一有个缺陷

ssh-add 这个命令不是用来永久性的记住你所使用的私钥的。实际上，它的作用只是把你指定的私钥添加到 ssh-agent 所管理的一个 session 当中。所以ssh-agent 是一个用于**存储私钥的临时性的 session 服务**，当你重启之后，ssh-agent 服务也就重置了，不过我们完全可以用脚本自动化这件事，以便启动终端的时候完成。

方法二的操作方式是使用ssh config的配置做定向管理，作用和Mac OS上的Keychain类似，相比方法一，易用性更佳。