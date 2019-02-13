---
title: selenium自动化测试入门篇
categories: 
- selenium
- python
tags: 
- selenium
- 自动化测试
---

## 前言
在使用selenium+python实践过自动化测试项目一段时间后，决定将此实践过程记录下来，以便后来的学习者提供借鉴。这里的例子基于h5archetype项目
> 自动化测试的工具有很多，selenium+python只是其中的一种组合方式，不必纠结于使用什么测试工具，关键看你怎么利用好这些工具

## 1.环境准备
1-1）selenium 测试框架（必须）
1-2）python36 编程语言（必须）
1-3）pycharm 编辑器（必须）
1-4）chromedriver.exe 浏览器引擎（必须）
1-5）sqlalchemy 数据库连接引擎（可选，需要连接数据库必须）
1-6）cx_Oracle 数据库连接库（可选，需要连接Oracle数据库必须）

## 2.安装环境
2-1）[安装python36](https://www.jianshu.com/p/7a0b52075f70) 编程语言  

2-2）[安装pycharm](https://www.jetbrains.com/pycharm/download/) 编辑器 
> 下载社区版即可，不需要专业版（为什么选择pycharm编辑器，难道其他的编辑器就不行吗？别急往下看）

2-3）[安装chromedriver.exe](https://chromedriver.storage.googleapis.com/index.html?path=2.30/) 浏览器引擎（仅针对chrome61版本，不同版本请自行选择下载）
> 谷歌浏览器版本与chromedriver.exe版本映射地址：https://blog.csdn.net/qq_40027987/article/details/79544489
> **下载完成后，放入C:\Python36\Scripts目录中，然后将C:\Python36\Scripts配置到系统环境变量path中**

2-4）在pycharm编辑器中引入其他环境依赖包
**打开pycharm编辑器，新建项目或引入h5archetype/frontend项目**
打开配置：File->settings→
![](/assets/selenium1-1.png)

添加python36
![](/assets/selenium1-2.png)

添加selenium
![](/assets/selenium1-3.png)

按照添加selenium的方式，依次添加cx_Oracle，sqlalchemy， 如下图
![](/assets/selenium1-4.png)

如上图，所有环境依赖准备就绪后，点击OK即可

至此，环境已经安装完成！接下来，即可编写自动化编程

**最后解答下前面提出的一个问题，为什么选择pycharm编辑器？**
答：***从上面也可注意到，pycharm编辑器提供安装项目依赖环境，方便安装各种依赖包，使用系统window的python环境会出现目录文件找不着的情况。其他编辑器试了都是这种情况，如Intellij IDEA，vscode，eclise等。这是由于全局中的python查找目录文件的默认方式造成的，当然，你可以在引用其他目录文件时修改它的默认查找方式，但还是建议你使用pycharm编辑，既方便又省事。***

## 3.运行项目
将/frontend 自己的项目引入pycharm编辑器中
进入test_auto/test/test_suite/sample目录，选择suite_sample_create.py或suite_sample_manage.py点击运行即可

如下图，运行Sample Manage页面的自动化测试
![](/assets/selenium1-5.png)

自动打开登录页面
![](/assets/selenium1-6.png)

自动打开Sample Manage页面，并自动测试设定的场景（如查询场景，编辑场景）
![](/assets/selenium1-7.png)

执行完成后，结果如下：
![](/assets/selenium1-8.png)

打开HTML结果报告
![](/assets/selenium1-9.png)

***扩展：***
生成更好看的测试报告可以使用allure工具，[详情点击](https://www.kancloud.cn/guanfuchang/python_selenium/714653)

## 4.项目解析
![](/assets/selenium1-10.png)

自动化测试的作用：特别是在回归测试中作用发挥巨大，能帮助我们节省很多的时间和精力，节约成本。
**其实一个好的自动化测试项目应该有一个好的项目结构，相比于自动化测试工具，我认为项目结构更为重要，项目结构设计的好坏直接影响项目的可扩展性和维护性，也就是测试对象无论怎么变化，作为对应的自动化测试维护工作都应该尽可能少，且不影响测试结果的正确性。**
**（自动化测试项目其实应该是个独立的项目，与各分支无关，不应跟在项目的某个分支下面，这里为了图方便与分支搅在了一起。）**

以下是我们的项目结构：
- [ ] .
- [ ] ├── /common/ # 公共函数目录
- [ ] ├── /config/ # 系统配置
- [ ] ├── /db/ # 数据库连接目录
- [ ] ├── /log/ # 日志函数目录
- [ ] ├── /test/ # 测试目录
- [ ] │ ├── /test_data/ # 测试数据
- [ ] │ ├── /test_page/ # 测试逻辑
- [ ] │ ├── /test_suite/ # 测试入口

其中/test作为项目的源码，将测试的数据，逻辑，运行入口进行分离。以页面作为测试对象，以页面操作场景作为最小测试单位（如新建场景，修改场景，查询场景等）

### 1）/test_data 存放查找页面元素css和各页面的测试用例
![](/assets/selenium1-11.png)

### 2）/test_page 存放各页面操作场景的逻辑
![](/assets/selenium1-12.png)

### 3）/test_suite 存放各页面可运行用例
![](/assets/selenium1-13.png)

## 5.相关技术介绍

### 5-1）python
python作为最流行的编程语言之一，适合编写应用程序的高级编程语言，推荐教程：
http://www.runoob.com/python/python-intro.html
***重点关注Python unittest***

### 5-2）selenium
作为最流行的自动化测试框架之一，它非常容易上手且相关社区较为活跃，遇到问题很容易就找到解决方法，推荐教程：
http://selenium-python-zh.readthedocs.io/en/latest/getting-started.html

举个栗子（注意先准备好环境）
```python
#coding:utf-8
from selenium import webdriver
import time
 
 
browser = webdriver.Chrome()
browser.get('https://www.baidu.com/')
# time.sleep(2)
assert "百度一下，你就知道" in browser.title
browser.find_element_by_id('kw').send_keys('selenium')
browser.find_element_by_id('su').click()
time.sleep(3)
# browser.close() # 关闭一个tab标签页
browser.quit() # 关闭整个浏览器
```
说白了就是提供个web驱动，模拟用户的各种行为，如打开浏览器，输入度娘的网址，输入要查询的内容"selenium", 然后点击查询，浏览3秒后关闭浏览器，看是不是很（hen）简（hai）单（sen）!

### 5-3）sqlalchemy
作用：提供DB连接引擎，连接数据库，执行SQL
官网地址：http://docs.sqlalchemy.org/en/latest/core/tutorial.html

举个栗子（注意先准备好环境）
```python
# coding:utf-8
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base
 
 
class db_help:
    engine = create_engine('oracle://db_username:db_password@sql_address:sql_port/db_name', echo=False) #请先替换自己的oracle数据库对应的数据库名，登录名，密码
    #构造函数
    def __init__(self):
        Session = sessionmaker(bind=db_help.engine, autocommit=False)
        self.session = Session()
        print ('create session:')
        print (self.session)
     
    def getSession(self):
        return self.session
 
    #析构函数
    def __del__(self):
        if self.session != None:
        print ('delete session')
        self.session.close()
        self.session = None
 
 
db = db_help()
session = db.getSession()
result = session.execute('select * from crm_user where username =:user_name', {'user_name': 'admin'}).fetchall()
if len(result) > 0:
    for item in result:
        print(item)
```
功能：查询crm_user表中admin的数据并打印出来