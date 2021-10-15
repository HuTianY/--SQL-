
# 视图
## 3.1.1什么是视图
视图是一张数据表的虚拟表
## 3.1.2视图与表的区别
视图并不保存真实的数据，视图是基于真实表的一份虚拟表。
## 3.1.3视图的意义
* 通过定义视图可以将频繁使用的SELECT语句保存以提高效率
* 通过定义视图可以使用户看到的数据更加清晰
* 通过定义视图可以不对外公开数据表全部字段，增强数据的保密性
* 通过定义视图可以降低数据的冗余
## 3.1.4如何创建视图
```
CREAT VIEW <视图名称>(<列名1>,<列名2>,...) AS <SELECT语句>
```
SELECT语句中的第一列就是视图中的第一列，第二列就是第二列。视图的列名在视图名称之后的列表中定义.
## 3.1.5如何修改视图结构
```
ALTER VIEW <视图名> AS <SELECT语句>
```
## 3.1.6如何更新视图内容
视图是一个虚拟表，对视图的操作就是对底层基础表的操作，所以在修改时只有满足底层基本表的定义才能成功修改。
对于一个视图来说，如果包含以下结构的任意一种都是不可以被更新的：聚合函数SUM(),MIN(),MAX(),COUNT(),etc.,;DISTINCT关键字;GROUP BY字句;HAVING子句;UNION或UNION ALL运算符;FROM字句中
包含多个表。<br>
注意，通过更新视图来更新原表的话，修改的部分只是被视窗所展示的部分。
## 3.1.7如何删除视图
```
DROP VIEW productSum;
```
# 子查询
## 3.2.1什么是子查询
子查询指一个查询语句嵌套在另外一个查询语句内部的查询，子查询的结果作为外层另外一个查询的过滤条件，查询可以基于一个表或者多个表。
## 3.2.2子查询和视图的关系
子查询就是将用来定义视图SELECT语句直接用于FROM子句当中。其中AS studentSum可以看作是子查询的名称，而且由于子查询是一次性的，所以子查询不会像视图那样保存在存储介质中，而是在SELECT语句执行
之后就消失了
## 3.2.3嵌套子查询
与在视图上再定义视图类似，子查询也没有具体的数量限制。可以在子查询中嵌套子查询。
## 3.2.4标量子查询
标量子查询指返回表中的一个值，即具体的某一行某一列。
## 3.2.5标量子查询有什么用
通过标量子查询得到一个限定性的条件(比如，平均销售单价或者是注册日期最晚等)。标量子查询在几乎任何地方都可以使用
```
SELECT product_id, product_name, sale_price
  FROM product
 WHERE sale_price > (SELECT AVG(sale_price) FROM product);
```
## 3.2.6关联子查询
通过一些标志将内外两层的查询连接起来起到过滤数据的目的
```
SELECT product_type, product_name, sale_price
  FROM product AS p1
 WHERE sale_price > (SELECT AVG(sale_price)
                       FROM product AS p2
                      WHERE p1.product_type = p2.product_type 
  GROUP BY product_type);
```
# 各种各样的函数
## 算数函数
## 字符串函数
## 日期函数
## 转换函数
# 谓词
## LIKE谓词
## BETWEEN谓词
## IN谓词
## 使用子查询作为IN谓词的参数
## EXIST 谓词
# CASE表达式
## 什么是CASE表达式
## CASE表达式的使用方法

# 学习内容
* 创建一个基于单表的视图
```
CREATE VIEW productsum (product_type, cnt_product)
AS
SELECT product_type, COUNT(*)
  FROM product
 GROUP BY product_type ;
```
* 创建一个基于多表的视图(product ; shop_product)
```
CREATE VIEW viw_shop_product(product_type, sale_price, shop_name)
AS
SELECT product_type, sale_type, shop_name
  FROM product,
       shop_product
 WHERE product.id = shop_product.product_id;
```
* 修改一个名为productSum的视图
```
ALTER VIEW productSum
   AS
        SELECT product_type, sale_price
          FROM Product
         WHERE regist_date > '2009-09-11';
```
* 更新视图
```
UPDATE productsum
   SET sale_price = '5000'
 WHERE product_type = '办公用品';
```




