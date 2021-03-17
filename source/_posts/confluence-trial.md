title: Confluence安装踩坑实录(Docker安装)
tags:
  - 其他
comments: true
date: 2021-03-10 17:00:00
original: true

---
### 1. docker的镜像安装太慢
centos 7下设置国内镜像方法： 
1. 使用阿里云容器服务，登录后首页点击“创建我的容器镜像” 得到一个专属的镜像加速地址
2. 修改/etc/docker/daemon.json中的代理信息，如果没有在目录下创建该文件。
3. 替换原地址，启用加速器。

       sudo mkdir -p /etc/docker
       sudo tee /etc/docker/daemon.json <<-'EOF'
        {
        "registry-mirrors": ["https://km3an9hh.mirror.aliyuncs.com"]
        }
        EOF
        sudo systemctl daemon-reload
        sudo systemctl restart docker


### 2. mysql启动失败
镜像启动后立刻关闭，查询日志报 mysqld: Error on realpath() on '/var/lib/mysql-files' (Error 2 - No such file or directory，
1. 为需要指定/var/lib/mysql-files的外部目录
2. 创建my.cnf并启动容器时直接绑定（参考文档： https://www.cnblogs.com/ybyn/p/13698058.html，勘误：文中datadir不用自定义）

       docker run --restart=unless-stopped -d --name mysql -v /opt/mysql_docker/conf/my.cnf:/etc/mysql/my.cnf -v /opt/mysql_docker/data:/var/lib/mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql

### 3. SSL开启导致的workbench连接报错
关闭ssl方法： 在my.cnf中增加一段配置
    
    skip_ssl
    
重启docker后生效

###4. 连接时报Public Key Retrieval is not allowed
SQLState - 08001
com.mysql.jdbc.exceptions.jdbc4.MySQLNonTransientConnectionException: Public Key Retrieval is not allowed
在连接字符串后面加上

    &allowPublicKeyRetrieval=true
    
###5. 再次报错：mysql ERROR 1045 (28000): Access denied for user ... (using password: YES)
权限问题， 执行以下命令
    
    CREATE USER 'confuser'@'%' IDENTIFIED BY '123456';
    ALTER USER 'confuser'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
    grant all on confdb.* TO 'confuser'@'%';
    flush priviledges;
    
###6. 再再次报错：您的数据库必须使用'READ-COMMITTED'作为默认隔离级别。
1.url连接字符串增加事务级别参数：

    &sessionVariables=transaction_isolation='READ-COMMITTED'
    
2.或者调整整体事务级别

    # confluence要求设置事务级别为READ-COMMITTED
    set global tx_isolation='READ-COMMITTED';