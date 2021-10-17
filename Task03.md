本笔记为阿里云天池龙珠计划SQL训练营的学习内容，链接为：https://tianchi.aliyun.com/specials/promotion/aicampsql；

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
给出一个输入值，按照预的程序定义给出返回值，输入值称为参数。
## 算数函数
* ABS（数值）：绝对值
* MOD（被除数，除数）：求余数
* ROUND（对象数值，保留小数的位数）：四舍五入
## 字符串函数
* 拼接函数：CONCAT（str1,str2,str3）
* 返回字符串长度：LENGTH（字符串）
* 全小写转换：LOWER（字符串）
* 字符串替换：REPLACE（对象字符串，替换前的字符串，替换后的字符串）
* 字符串的截取：SUBSTRING（对象字符串FROM截取前的位置FOR截取的字符数）
* SUBSTRING_INDEX（原始字符串，分隔符，n）<br>
该函数用来获取原始字符串按照分隔符分割，第n个分隔符之前（或之后）的子字符串，支持正向和反向索引，索引起始值分别为1和-1。
## 日期函数
* CURRENT_DATE - 获取当前日期
* CURRENT_TIME - 当前时间
* CURRENT_TIMESTAMP - 当前日期和时间
* EXTRACT - 截取日期元素
## 转换函数
* 类型转换：CAST（转换前的值 AS 想要转换的数据类型）
* COALESCE（数据1，数据2，数据3...）<br>
该函数会返回数据中左侧开始第1个不是NULL的值
# 谓词
返回值为真值的函数。主要有(LIKE, BETWEEN, IS NULL, IS NOT NULL, IN, EXISTS)
## LIKE谓词
LIKE代表匹配查询，例子，比如通过%ddd来查询末尾为ddd的字符串，或者是_ddd%来查询开头第一个字符任意第二个到第四个为ddd结尾为任意的字符串。<br>
_ 代表匹配一个字符串，%代表匹配任意字符串
## BETWEEN谓词
用使BETWEEN可以进行范围查询。内部含三个参数 <br>
```
WHERE sale_price BETWEEN 100 AND 1000
```
BETWEEN的范围是闭区间
## IS NULL IS NOT NULL
用于选取出某些值为NULL的列的数据，而想要选取NULL以外的数据时，需要使用IS NOT NULL。
## IN谓词
```
WHERE purchase_price IN (320,500,5000);
```
反之，希望取出进货价不是320，500，5000的时候可以用NOT IN
## 使用子查询作为IN谓词的参数
* 实际生活中，某个门店的在售商品是不断变化的，使用in谓词就需要经常更新SQL语句，降低了效率，提高了维护成本；
* 实际上，某个门店的在售商品可能有成百上千个，手工维护在售商品编号过于复杂
使用子查询可保持sql语句不变，极大提高了程序的可维护性
## EXIST 谓词
EXIST只关心记录是否存在，因此返回哪些列都没有关系。EXIST只会判断是否存在满足子查询中WHERE字句制定的条件。
# CASE表达式
条件分支
## 什么是CASE表达式
```
CASE WHEN<求值表达式>THEN<表达式>
     WHEN<求值表达式>THEN<表达式>
     WHEN<求值表达式>THEN<表达式>
     .
     .
     .
```
当有WHEN的情况时，表达THEN
## CASE表达式的使用方法
* 根据不同分支得到不同列值
* 实现列方向上的聚合
* 实现行转列

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
* 字符串函数的运用
```
SELECT 
    str1, str2, str3,
    CONCAT(str1,str2,str3) AS str_concat,
    LENGTH(str1) AS len_str,
    LOWER(str1) AS LOW_str,
    REPLACE(str1,str2,str3) AS rep_str,
    SUBSTRING(str1 FROM 3 FOR 2) AS sub_str
FROM
    samplestr;
```
* EXIST的用例
```
WHERE EXISTS （SELECT *
                FROM shopproduct AS sp
               WHERE sp.shop_id = ‘000C’
                 AND sp.shop.product_id = p.product_id）；
```
就像EXISTS可以用来替换IN一样，NOT IN也可以用NOT EXIST来替换
* 根据不同分支得到不同列
```
SELECT product_name,
       CASE WHEN product_type = '衣服' THEN CONCAT('A:',product_type)
            WHEN product_type = '办公用品' THEN CONCAT('B:',product_type)
            WHEN product_type = '厨房用具' THEN CONCAT('C:',product_type)
            ELSE NULL
       END AS abc_product_type
  FROM product;
```
* 实现列方向上的聚合
```
SELECT SUM(CASE WHEN product_type = '衣服'THEN sale_price ELSE 0 END) AS sum_price_clothes,
       SUM(CASE WHEN product_type = '厨房用具'THEN sale_price ELSE 0 END) AS sum_price_kitchen,
       SUM(CASE WHEN product_type = '办公用品'THEN sale_price ELSE 0 END) AS sum_price_office
  FROM product;
```
* 实现行转列
```
SELECT name,
       SUM(CASE WHEN subject='语文' THEN score ELSE null END) as chinese,
       SUM(CASE WHEN subject='数学' THEN score ELSE null END) as math,
       SUM(CASE WHEN subject='外语' THEN score ELSE null END) as english
  FROM score
 GROUP BY name;
```

# Question
## 3.1
```
CREAT VIEW ViewPractice5_1
AS
SELECT product_name, sale_price, regist_date
  FROM product
 WHERE sale_price >= 1000
   AND regist_date = '2009-09-20';
```
## 3.2
报错

## 3.3
```
SELECT product_id, product_name, product_type, sale_price,
       (SELECT AVG(sale_price) FROM product) AS sale_price_all
  FROM product;
```  
## 3.4
```
CREATE VIEW AvgPriceByType AS
SELECT product_id, product_name, product_type, sale_price,
       (SELECT AVG(sale_price)
          FROM product AS p2
         WHERE p1.product_id = p2.product_id
         GROUP BY p1.product_type) AS avg_sale_price
 FROM product AS p1;
```
## 3.5
运算或者函数中含有NULL时，结果全都会变为NULL？ ---正确

## 3.6
a.取出了买入价不为500，2800，5000的产品的产品名和买入价两列
b.NOT IN的查询中不能有NULL，所以不返回记录

## 3.7
```
SELECT SUM(CASE WHEN sale_price < 1000 THEN 1 ELSE 0 END) AS low_price,
       SUM(CASE WHEN sale_price BETWEEN 1001 AND 3000 THEN 1 ELSE 0 END) AS mid_price,
       SUM(CASE WHEN sale_price > 3000 THEN 1 ELSE 0 END) AS high_price
  FROM product;
```

# 学习总结
本章学习了较多内容，主要是视图，子查询以及函数运算和CASE表达式。其中需要注意，改变视图同样会改变原表。子查询的层数尽量不要过于太多导致可读性下降。
CASE表达式是条件分支，使用CASE表达式可以快速得到不同条件表达式下的数据。需要勤加练习掌握。
