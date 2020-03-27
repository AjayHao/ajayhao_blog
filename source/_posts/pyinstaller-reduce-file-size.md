title: Python打包exe文件压缩
tags:
  - python
categories:
  - 技术
comments: true
date: 2020-03-22 12:04:00
original : true

---

&emsp;&emsp;前几周给产品组做了个抓OA流程数据算处理时效的需求，爬虫相关的小工具，自然而然就用python啦，等要交付的时候发现打出的exe包实在太大啊，一个小功能将近200M（Orz...）, 于是遍寻瘦身之道，这边推荐CodingDog在知乎上的解答，给了四种“玄学”解法（其实都是很科学的方案，给大神点赞~），直接给传送门：

[Python 打包成 exe，太大了该怎么解决？ by CodingDog](https://www.zhihu.com/question/281858271)

&emsp;&emsp;作为一名python小白，这里着力推荐一下解法3，简洁易懂开箱即用。由于真实的python运行环境中安装的库多而全，pyinstaller默认会把很多没用到底库一起打包进来，实际程序用不到那么多库，打完的程序包就显得很臃肿。 精简打包就需要把不相关的依赖排除掉，人工筛查工作量不小，同时需要对模块的底层依赖和第三方依赖非常清晰，稍有不慎就会出错。**解法3的妙处在于其他方案是做减法，而解法3是从0做加法**， 核心思想是创建一个干净的python虚拟环境，在没有任何依赖的情况下，逐个叠加依赖库。 创建虚拟环境的工具也有很多，如virtualenv、pipenv，下文以pipenv做演示。

&emsp;&emsp;具体操作步骤如下：

1.首先安装pipenv

    
    pip install pipenv

2.创建个目录作为虚拟环境，如命名为newenv，在该目录下创建虚拟环境

    
    #进到newenv目录下
    cd D:\Software\Develop\newenv
    
    #建立虚拟环境
    pipenv install
    
![创建虚拟环境](https://s1.ax1x.com/2020/03/27/GiWk7V.png)    
    
3.进入虚拟环境，通过命令行盘符路径前带括号的环境信息来判断是否进入成功

    
    pipenv shell
 
![进入虚拟环境](https://s1.ax1x.com/2020/03/27/GiWF00.png)  
 
4.逐个安装构建项目需要依赖的模块，如本例中的requests、 pyquery，安装完毕后，最后安装pyinstaller

    
    pip install requests
    pip install pyquery
    ...
    pip install pyinstaller
    
5.开始打包工程文件


    pyinstaller -F D:\GitRepo\helloPy\GtjaOATool\oaprocess.py
    

打包后，找到目录dist中的exe文件（所在目录：D:\Software\Develop\newenv\dist），顺利打开~。

![压缩后的文件](https://s1.ax1x.com/2020/03/27/GiWVtU.png)  

可以看到压缩后的文件只有11M。