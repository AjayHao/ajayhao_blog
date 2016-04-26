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

还在脱密期，已感受到了新东家的热(hou)情(ji)，包括工作邮箱、vpn、git等环境配置邮件已邮件告知，可以远程办公这点比起现在倒是人性化了些，要是能不上班在家干活就更好了

（老婆：纳尼！干毛活！在家就是我的！！）
（老板：周末在家干活行，平时？想多了吧！）

走http协议拉了个代码，每次切换分支都要输入口令进行验证，后续写起代码提交的勤快些还得了？。。由于家中已配置了GitHub的SSH key，如何集成公司的GitLab环境的SSH key，使我同时用GitHub与公司GitLab时能走不同的key，免去频繁的口令验证呢？下面介绍两种管理多套环境的key的方法...

<!-- more --><!-- indicate-the-source -->

友情提示：Windows平台下的童鞋，以下操作请到Cygwin/msysGit等支持git bash的环境下执行。

以下结合GitHub官方参考指引和相关资料，介绍两种方法:
### 方法一  使用ssh-agent
#### 1.  [检查已有的SSH key](https://help.github.com/articles/generating-an-ssh-key/)
#### 2.  [生成key并加入ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)
这里插一小段，此处是方法一的关键，度娘上的答案大多只提到怎么生成key，却没有提到要加入ssh-agent，还有就是在执行ssh-add时遇到
 “Could not open a connection to your authentication agent”，查到的解法基本都是执行

    $ ssh-agent bash
    $ ssh-add
  然后呢？还是不行，反正我试下来不行，stackoverflow中也中一篇解答给了关键，这在2中也有所体现。

    $ eval `ssh-agent -s`
问题解决~，查看ssh-agent中的密钥

    $ ssh-agent -l

#### 3. [加到Git账户中](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)
不单单适用于GitHub，其他Git仓库也类似

### 方法二. 在config中配置host
第1步、第3步和方法一相同，主要差别在第2步
#### 2. 生成key并配置config文件
生成key的方式也和方法一的第2步一样。生成后在~/.ssh 目录下创建config文件，配置私钥对应的服务器，假设为公司GitLab生成的私钥为id_rsa_work，则config文件内容如下：

    Host gitlab.xxx.com
        HostName gitlab.xxx.com
        User git
        Port 22
        IdentityFile ~/.ssh/id_rsa_work

 可以延用公司给的gitlab域名，也可以自定义域名，但后续git链接中的地址需要相应调整

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

另外在失败时，可以通过

     $ ssh -v -T git@github.com
查看具体的处理过程，进行更精确的定位。
![带-v参数](/images/posts/git-sshkey/debug.png)




