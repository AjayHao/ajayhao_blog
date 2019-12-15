title: Python3及Django2.6环境搭建
tags:
  - python
  - django
categories:
  - 技术
comments: true
date: 2019-10-22 22:07:00
original : true

---

Python3及Django2.2环境搭建

1. 安装python，安装后将D:\Software\Common\Anaconda3和D:\Software\Common\Anaconda3\Scripts目录加到系统环境变量path中。

2. 安装django，下载 Django 压缩包，解压并和Python安装目录放在同一个根目录，进入 Django 目录，执行python setup.py install，然后开始安装，Django将要被安装到Python的Lib下site-packages。

3. 在cmd中执行django-admin startproject helloPy，尝试创建新项目。

4. 如果报"pkg_resources.DistributionNotFound: The 'sqlparse' distribution was not found and is required by Django"，则说明缺少 sqlparse 模块。
安装命令：
```
    pip install sqlparse
```
5. 如果报"mysqlclient 1.3.13 or newer is required;"，则需要升级mysqlclient版本
执行以下检查mysqlclient当前版本。
```
    pip show mysqlclient
```

  a. 在D:\Software\Common\Anaconda3\Lib\site-packages\Django-2.2.6-py3.6.egg\django\db\backends\mysql中修改base.py，注释掉以下语句：
```python
  if version < (1, 3, 13):
      raise ImproperlyConfigured('mysqlclient 1.3.13 or newer is required; you have %s.' % Database.__version__)
```

  b. D:\Software\Common\Anaconda3\Lib\site-packages\Django-2.2.6-py3.6.egg\django\db\backends\mysql中修改operations.py，将第146行：
```python
    query = query.decode(errors='replace') 
```
  修改为
```python
    query = query.encode(errors='replace')
```
  