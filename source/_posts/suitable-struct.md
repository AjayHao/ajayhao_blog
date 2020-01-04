title: J2EE代码结构推荐
tags:
  - J2EE
categories:
  - 技术
thumbnail: 'http://ajayhao.github.io/images/thumbnails/struct.jpg'
comments: true
date: 2016-05-29 12:09:00
original: true
---

J2EE项目一般都有比较类似的目录层次，有了maven后，项目的大框架已经有了一种默认规约，而一个清晰良好的目录结构，能够为后续项目的“健康发展”提供很重要的支持。在一个结构良好的代码框架下，既能快速定位资源，便于日常维护，提升开发效率，又能使新的开发成员快速熟悉模块关联，便于开发团队新老交替，技术传承。
<!-- more -->

传统SSH类项目的分层结构已经老生常谈，主要是Action-Service-DAO，可以参考[吴毅](http://my.csdn.net/yuanhenglizhen110)先生的博文

[JAVA中Action层, Service层 ，modle层 和 Dao层的功能区分](http://blog.csdn.net/yuanhenglizhen110/article/details/9744669)


![简单的分层](https://s2.ax1x.com/2020/01/04/lwRGY6.png)

下图是典型的基于maven的SpringMVC+Mybatis的项目结构

![](https://s2.ax1x.com/2020/01/04/lwRlwR.png)

从图中看已经做了比较好的分层，controller负责与前端交互，service负责模块业务逻辑，dao负责数据访问，mapper层为对象模型。

实践下来，一般规模的项目基于这个分层模型，应该已经足够，但是如果项目足够大，这样的划分就显得“不够友好”了。随着系统逐步庞大，模块间的业务逻辑调用势必藤蔓相连，会显得越来越“重”。我们尝试做下优化:

![](https://s2.ax1x.com/2020/01/04/lwR1T1.png)

#### 优化1：单一服务层->服务依赖隔离

红框中的服务层，很可能出现负载过重的情况。因为服务接口作为对外的调用约定，具体业务逻辑均体现在ServiceImpl中，实际上就包括了两部分内容：

    1. 组装业务模块DAO的返回POJO数据，并完成相应处理，实现自身业务模块的逻辑（“自身”指对于无需依赖外部接口的场景）
    2. 整合调用外部服务接口（需要与外部系统/外部模块交互）

这样的话，service层就会变得比较臃肿，比较好的做法是把1与2拆分开。在service中抽出facade层与intergration层。facade层作为留给外部模块调用的接口层，而intergration层作为一个访问其他服务的代理，继而将模块的内部逻辑与外部服务依赖加一层隔离。

![](https://s2.ax1x.com/2020/01/04/lwR8Fx.png)

如图所示，integration中的代理类负责内外交互，facade的实现类可以访问唯一的访问代理，而不用直接访问外部服务，IUserClientProxy起到了良好的“内外层服务隔离”效果。

#### 优化2：单一模型层->DO + DTO

![](https://s2.ax1x.com/2020/01/04/lwRJfK.png)

如图所示，dao层返回的POJO, 若直接穿透到controller层与viewer交互，无疑会出现实体对象的跨层耦合，这样便无法做到后台的改动对于前台透明。 为避免这样的情况发生，可以考虑在facade层增加DTO层，在DO数据需要被“跨层访问”时，进行适当的转换。

![](https://s2.ax1x.com/2020/01/04/lwRNlD.png)

#### 优化3: 把业务模块做成maven子模块

![](https://s2.ax1x.com/2020/01/04/lwRaOH.png)

核心思想是把业务层的逻辑，能按照业务模块维度单独打包成jar包，便于更灵活的版本发布、弹性扩容以及缺陷管理。
web的打包形式为war，业务层的maven module一律打包成jar，带上版本号，标识成web的依赖。后续可以针对单独的业务模块jar包进行管理。

这样调整后，可以作为较为理想的j2ee分层目录结构分层参考。 在我们团队中，电商项目及运营后台均沿用这样一种分层结构风格，或许对于一些小型项目，也不用划分的那么细。 可从长远的可扩展角度看，这种目录组织方式在模块解耦合上效果很好，使整个项目“能屈能伸”。
