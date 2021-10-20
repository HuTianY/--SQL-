本笔记为阿里云天池龙珠计划SQL训练营的学习内容，链接为：https://tianchi.aliyun.com/specials/promotion/aicampsql；

# 窗口函数
窗口函数也称为OLAP函数。OLAP是OnLineAnalyticalProcessing的简称，意思是对数据库数据进行实时分析处理。
## 窗口函数
```
<窗口函数> OVER ([PARTITION BY<列名>]
                     ORDER BY<排序用列名>)
```
[ ]中的内容可以省略<br>
### 窗口函数概念及基本的使用方法
* PARTITION BY 用来分组，选择要看哪个窗口
* ORDER BY 用来排序，决定窗口内，按照哪种规则来排序
## 窗口函数种类
分为两类<br>
一类是将SUM，MAX，MIN等聚合函数用在窗口函数中
二类是将RANK，DENSE_RANK等排序用的专用窗口函数
### 专用窗口函数
* RANK()：1，1，1，4
* DENSE_RANK():1，1，1，2
* ROW_NUMBER():1，2，3，4
### 聚合函数在窗口函数上的使用
使用方法和窗口函数一致，不过得到的是累积的聚合函数值
## 窗口函数的应用 - 计算移动平均
聚合函数在窗口函数使用时，计算的是累积到当前行的所有数据的聚合。实际上，还可以指定更加详细的汇总范围。该汇总范围称为框架(frame)。
```
<窗口函数> OVER (ORDER BY <排序用列名>
                 ROWS n PRECEDING)
```
```
<窗口函数> OVER (ORDER BY <排序用列名>
                 ROWS BETWEEN n PRECEDING AND n FOLLOWING)
```
PRECEDING('之前')，将框架指定为“截止到之前n行”，加上自身行<br>
FOLLOWING('之后')，将框架指定为“截止到之后n行”，加上自身行<br>
BETWEEN 1 PRECEDING AND 1 FOLLOWING，将框架指定为“之前1行”+“之后1行”+“自身”<br>
### 窗口函数适用范围和注意事项
* 原则上，窗口函数只能在SELECT子句中使用
* 窗口函数OVER中的ORDER BY子句并不会影响最终结果的排序。其中只是用来决定窗口函数按何种顺序计算
## GROUPING运算符
常规的GROUPBY只能得到每个分类的小计，有时候还需要计算分类的合计，可以用ROLLUP关键字。
```
SELECT product_type,
       regist_date,
       SUM(sale_price) AS sum_price
  FROM product
 GROUP BY product_type, regist_date WITH ROLLUP
```
### ROLLUP - 计算合计及小计
三层聚合，第一层是GROUPBY的结果，第二层是GROUPBY之后各类别的小计，第三层是第二层中各小计的合计。

# 学习内容
* 窗口函数的例子
```
SELECT product_name,
       product_type,
       sale_price,
       RANK() OVER (PARTITION BY product_type
                        ORDER BY sale_price) AS ranking
 FROM product
```
PARTITION BY能够设定窗口对象的范围。这里设定product_type意味着每一个商品种类就是一个小窗口<br>
ORDER BY能够指定按照哪一列，何种顺序进行排序。所以就是每一个小窗口中按照价格进行升序。
* 计算移动平均的例子
```
SELECT product_id,
       product_name,
       sale_price,
       AVG(sale_price) OVER (ORDER BY product_id
                              ROWS 2 PRECEDING) AS moving_avg,
       AVG(sale_price) OVER (ORDER BY product_id
                              ROWS BETWEEN 1 PRECEDING
                                       AND 1 FOLLOWING) AS moving_avg,
 FROM product                                            
```

# Question
## 5.1
按照product_id升序排列，Current_max_price为当前行为止sale_price的最大值

## 5.2
SELECT regist_time，
       product_name,
       sale_price,
       SUM(sale_price) OVER (ORDERED BY regist_time NULLS FIRST) AS Current_Sum_Price
  FROM product;
  
## 5.3
a.相当于不进行分组，对全局进行排序
b.执行顺序为 FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY <br>
如果在 WHERE, GROUP BY, HAVING 使用了窗口函数，就是说提前进行了一次排序，排序之后再去除记录、汇总、汇总过滤，第一次排序结果就是错误的，没有实际意义。而 ORDER BY 语句执行顺序在
SELECT 语句之后，自然是可以使用的。

# 学习总结
本章内容比较少，但是非常实用。前面所学的一些复杂的语句可以使用窗口函数让语句变简单，再变得简洁的同时也大大增加了可读性。再对窗口函数的分类中需要移动取值的时候可以添加ROWS n BETWEEN
PRECEDING AND n FOLLOWING等来进行操作。ROLLUP则可以计算分类的合计。
