本笔记为阿里云天池龙珠计划SQL训练营的学习内容，链接为：https://tianchi.aliyun.com/specials/promotion/aicampsql；
# 初识数据库与SQL
## 1.1 DBMS的种类
* 层次型数据库（Hierarchical Database，HDB）
* 关系型数据库（Relational Database，RDB）
* 面向对象数据库（Object Oriented Database，OODB）
* XML数据库（XML Database，XMLDB）
* 键值存储系统（Key-Value Store，KVS），举例：MongoDB
## 1.2 RDBMS的常见系统结构
![](https://img.alicdn.com/imgextra/i2/O1CN01kROUDI22ITX6Evayf_!!6000000007097-0-tps-567-333.jpg)
## 1.3 数据库的安装
### 1.3.1 阿里云MySQL服务器使用介绍
[阿里云MySQL服务器使用介绍](http://tianchi-media.oss-cn-beijing.aliyuncs.com/dragonball/SQL/other/阿里云MySQL服务器使用介绍.pdf)
### 1.3.2 本地MySQL环境搭建方法
[本地MySQL环境搭建说明](http://tianchi-media.oss-cn-beijing.aliyuncs.com/dragonball/SQL/other/本地MySQL环境搭建方法介绍.pdf)
# SQL
## 2.1 概念介绍
SQL语句可以分为以下三类
* DDL（Data Definition Language，数据定义语言） 用来创建或者删除存储数据用的数据库以及数据库中的表等对象。DDL 包含以下几种指令。
  * CREATE ： 创建数据库和表等对象
  * DROP ： 删除数据库和表等对象
  * ALTER ： 修改数据库和表等对象的结构
* DML（Data Manipulation Language，数据操纵语言） 用来查询或者变更表中的记录。DML 包含以下几种指令。
  * SELECT ：查询表中的数据
  * INSERT ：向表中插入新数据
  * UPDATE ：更新表中的数据
  * DELETE ：删除表中的数据
* DCL（Data Control Language，数据控制语言） 用来确认或者取消对数据库中的数据进行的变更。除此之外，还可以对 RDBMS 的用户是否有权限操作数据库中的对象（数据库表等）进行设定。DCL 包含以下几种指令
  * COMMIT ： 确认对数据库中的数据进行的变更
  * ROLLBACK ： 取消对数据库中的数据进行的变更
  * GRANT ： 赋予用户操作权限
  * REVOKE ： 取消用户的操作权限
## 2.2 SQL的基本书写规则
  * SQL语句要以分号（ ; ）结尾
  * SQL 不区分关键字的大小写，但是插入到表中的数据是区分大小写的
  * win 系统默认不区分表名及字段名的大小写
  * linux / mac 默认严格区分表名及字段名的大小写

# 重点学习内容-CREATE DATEBASE语句
* 创建
  * 库的创建 CREATE DATABASE XXX; 
  * 表的创建 CREATE TABLE XXX
  (COLUMN1 TYPE NOT NULL,
   COLUMN2 TYPE NOT NULL,
   COLUMN3 TYPE,
   COLUMN4 TYPE,
   PRIMARY KEY(COLUMN1)
  ) ;
  * TYPE
    INTEGER
    CHAR
    VARCHAR
    DATE
  * 只能用半角英文字母，数字，下划线作为数据库，表和列的名称而且名称必须以半角英文字母开头
* 删除
  * 表的删除 DROP TABLE XXX;
  * 添加列 ALTER TABLE XXX ADD COLUMN column2 VARCHAR(100) NOT NULL;
  * 删除列 ALTER TABLE XXX DROP COLUMN column2;
* 插入
  * INSERT INTO TABLE (column1, column2, column3, ...)VALUES('values1', 'values2',...)
  
 # 练习题答案
 ## 3.1
 CREATE TABLE Addressbook(
    regist_no INTEGER NOT NULL,
    name VARCHAR(128) NOT NULL,
    address VARCHAR(256) NOT NULL,
    tel_no CHAR(10),
    mail_address CHAR(20),
    PRIMARY KEY(regist_no)
 ) ;
 ## 3.2 
 ALTER TABLE Addressbook ADD COLUMN postal_code CHAR(8) NOT NULL;
 ## 3.3
 DROP TABLE Addressbook;
 ## 3.4
 被删除的表无法用语句恢复
 
 # 一些感想与思考
 主要掌握了CREATE, DROP, ALTER的语句，同时注意SQL语法的结尾必须补上分号，名称首字母半角英文，名称只能由半角英文数字与下划线组成。
 同时，使用update语句时要添加where条件，不然所有行都要按照语句修改
 
  
