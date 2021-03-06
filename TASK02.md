本笔记为阿里云天池龙珠计划SQL训练营的学习内容，链接为：https://tianchi.aliyun.com/specials/promotion/aicampsql；

# SELECT语句基础
## 1.1从表中选取数据
SELECT column1，<br>
  FROM  table1；
## 1.2从表中选取符合条件的数据
SELECT column1,column2,<br>
  FROM table1 <br>
 WHERE column2 = 'xx';
## 1.3相关法则
* 星号*代表全部列的意思。
** SELETCT * <br>
     FROM table1;
* SQL中可以随意使用换行符，不影响语句执行(但不可插入空行)。
* 设定汉语别名时需要使用双引号(")括起来。
  * SELECT product_id  AS id, <br>
           purchase_price AS "进货单价" <br>
      FROM product；
* 在SELECT语句中使用DISTINCT可以删除重复行。
  * SELECT DISTINCT product_type <br>
      FROM product;
* 注释是SQL语句中用来标识说明或者注意事项的部分。分为一行注释"--"和多行注释两种"/**/"。
# 算术运算符和比较运算符
## 2.1算术运算符
加法+； 减法-； 乘法*； 除法/；
## 2.2比较运算符
等于=; 不相等<>; 大于等于>=; 大于>; 小于等于<=, 小于<；
## 2.3常用法则
* SELECT子句中可以使用常数或者表达式
* 使用比较运算符时一定要注意不等号和等号的位置
* 字符串类型的数据原则上按照字典顺序进行排序，不能与数字的大小顺序混淆
* 希望选取NULL记录时，需要在条件表达式中使用IS NULL运算符，希望选取不是NULL记录时，需要在条件表达式中使用IS NOT NULL运算符；
# 逻辑运算符
## 3.1NOT运算符
## 3.2AND运算符和OR运算符
## 3.3通过括号优先处理
## 3.4真值表
## 3.5含有NULL的真值
# 对表进行聚合查询
## 4.1聚合函数
* COUNT
* SUM
* AVG
* MAX
* MIN
## 4.2使用聚合函数删除重复值
SELECT COUNT(DISTINCT product_type)<br>
  FROM product; #计算去除重复数据后的数据行数
SELECT SUM(sale_price), SUM(DISTINCT sale_price)
 FROM product;  
## 4.3常用法则
* COUNT函数的结果根据参数的不同而不同。COUNT(*)会得到包含NULL的数据行数，而COUNT（列名）会得到NULL之外的数据行数。
* 聚合函数会将NULL排除在外，但COUNT（*）例外，并不会排除NULL。
* MAX/MIN 函数几乎适用于所有数据类型的列。SUM/AVG函数只适用于数值类型的列。
* 想要计算值的种类时，可以在COUNT函数的参数中使用DISTINCT。
* 在聚合函数的参数中使用DISTINCT，可以删除重复数据
# 对表进行分组
## GROUP BY
# 为聚合结果制定条件
## HAVING
HAVING用于对分组进行过滤，可以使用数字，聚合函数和GROUPBY中指定的列名
# 对查询结果进行排序
## ORDER BY
SQL中的执行结果是随机排列的，当要按照特定顺序排列时，就可以用ORDER BY<br>
SELECT column1, column2, column3,...<br>
  FROM table1 <br>
 ORDER BY column2,column3 #ORDER BY后跟的是排序基准列，默认为升序，降序排列为DESC




# 学习内容
学习了SQL基础查询与排序，大的框架一般为<br>
SELECT 某列<br>
  FROM 某表<br>
 WHERE 条件表达式<br>
算术运算符和比较运算符与其他的语言相近，故不做多余的解释。逻辑运算符中的复杂逻辑运算可以阅读离散数学中的相关内容<br>
聚合查询的函数有(COUNT; SUM; AVG; MAX; MIN)五种<br>
使用的方法写在SELECT 函数之后，<br>
SELECT SUM(purchase_price), AVG(sale_price) <br>
  FROM product; <br>
groupby与pandas中的类似，需要注意使用时的书写顺序<br>
SELECT-FROM-WHERE-GROUP BY <br>
HAVING语句用于对分组进行过滤，可以使用数字，聚合函数和groupby中指定的列名<br>
ORDERBY语句可以对查询结果进行排序，默认为升序，降序排列为DESC <br>
FROM-WHERE-GROUPLE BY-HAVING-SELECT-ORDER BY 这个使用顺序导致了ORDER BY中可以使用别名，因为ORDERBY在SELECT语句之后

# 练习题
## 2.1
SELECT product_name, regist_date <br>
  FROM product<br>
 WHERE regist_date > '2009-04-28'
## 2.2 
a.返回所有买入价为空的单元格<br>
b.返回所有买入价不为空的单元格<br>
c.返回商品名不为空的单元格
## 2.3
```
SELECT product_name, sale_price, purchase_price
  FROM product
 WHERE sale_price - 500 >= purchase_price
```
或者
```
SELECT product_name, sale_price, purchase_price
  FROM product
 WHERE sale_price >= purchase_price + 500
```
## 2.4
```
SELECT product_name, product_type,
       sale_price * 0.9 - purchase_price AS profit
  FROM product
 WHERE sale_price * 0.9 - purchase_price > 100
   AND (product_type = '办公用品' OR product_type = '厨房用具')；
```
## 2.5
name是字符串没有SUM操作<br>
WHERE应该在GROUPBY之前<br>
GROUPBY字段和SELECT字段不同
## 2.6
```
SELECT product_type, SUM(sale_price), SUM(purchase_price)
  FROM product
 GROUP BY product_type
HAVING SUM(sale_price) > 1.5 * SUM(purchase_price);
```
## 2.7
```
ORDER BY regist_date DESC, sale_price;
```

# 思考与总结
通过TASK1与TASK2的学习，基本掌握了SQL的基础操作CREAT,DROP,ALTER,SELECT,INSERT,UPDATE,DELETE<br>
需要注意DML的执行顺序为FROM-WHERE-GROUPBY-HAVING-SELECT-ORDERBY,所以ORDERBY可以使用SELECT中设置的别名，GROUPBY不可以。
还有一点需要注意的是SQL的逻辑运算比较特别，再真假之外有第三种逻辑UNKNOW。<br>
NULL并不代表着空值,
空值没有长度而NULL有长度；NULL的判断要依靠IS NULL或者IS NOT NULL；用COUNT的时候NULL会被忽略但是空值会被统计




 
