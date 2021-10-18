本笔记为阿里云天池龙珠计划SQL训练营的学习内容，链接为：https://tianchi.aliyun.com/specials/promotion/aicampsql；

# 表的加减法
## 4.1.1 集合运算
通过UNION-并集，INTERSECT-交集，EXCEPT-差集 进行表的加减运算。
## 4.1.2 表的加法-union
```
SELECT product_id, product_name
  FROM product;
 UNION
SELECT product_id, product_name
  FROM product2;
```
### UNION与OR谓词
对于同一个表的两个不同筛选结果集使用UNION对两个结果集取并集，和把两个子查询的筛选条件用OR谓词连接，会得到相同的结果，但倘若要将两个不同的表中的结果合并在一起，就不得不用UNION了。
### 包含重复行的集合运算 UNION ALL
```
SELECT product_id, product_name
  FROM product;
 UNION ALL
SELECT product_id, product_name
  FROM product2;
```
### bag模型与set模型
* set:只允许存在不重复的元素
* bag:允许存在重复的元素
### 隐式类型转换
属性不同的列也可以用UNION做运算
## 4.1.3 MYSQL 8.0不支持交运算INTERSECT
```
SELECT product_id, product_name
  FROM product;
 INTERSECT
SELECT product_id, product_name
  FROM product2;
```
### bag的交运算
* 该元素是否同时属于两个bag
* 该元素在两个bag中的最小出现次数
## 4.1.4 差集补集与表的减法
当使用一个集合A减去另外一个集合B的时候，对于只存在于集合B而不存在于集合A的元素，采取直接忽略的策略。因此集合A和B做减法只是将集合A中同时属于集合B的元素减掉。
### MYSQL8.0 还不支持EXCEPT运算
可以用NOT IN来实现表的减法
### EXCEPT与NOT谓词
使用NOT IN谓词基本可以实现和SQL标准语法中的EXCEPT运算相同的效果
### EXCEPT ALL与bag的差
类似于UNION ALL，EXCEPT ALL也是按出现次数进行减法
* 该元素是否属性作为被减数的bag
* 该元素在两个bag中出现的次数
### INTERSECT 与 AND谓词
对于同一个表的两个查询结果而言，他们的交INTERSECT实际上可以等价地将两个查询的检索条件用AND谓词连接来查询
## 4.1.5 对称差
两个集合A，B的对称差是指那些仅属于A或仅属于B的元素构成的集合。这个操作可以用（A-B）并（B-A）实现。
## 4.1.5 对称差
### 借助并集和差集迂回实现交集运算INTERSECT
两个集合的交可以看作是两个集合的并去掉两个集合的对称差。
# 连结(JOIN)
 连结就是用某种关联条件，将其他表中的列添加过来，进行表和表的连结操作。
## 4.2.1 内连结(INNER JOIN)
```
FROM <tb_1> INNER JOIN <tb_2> ON <condition(s)>
```

### 使用内连结从两个表获取信息
* 注意语句中FROM后面要有两张表，并且用INNER JOIN将两张表连结在一起
* 必须使用ON子句来指定连结条件
* SELECT子句中的列最好按照 表明.列名 的格式来使用
### 结合WHERE字句使用内连结
在使用内连结的时候同时使用WHERE字句对检索结果进行筛选，则需要把WHERE子句写在ON字句的后边。
```
SELECT SP.shop_id,
       SP.shop_name,
       SP.product_id,
       P.product_name,
       P.product_type,
       P.sale_price,
       SP.quantity
  FROM shopproduct AS SP
INNER JOIN product AS P
    ON SP.product_id = P.product_id
 WHERE SP.name_name = '东京'
   AND P.product_type = '衣服'；
```
因为 FROM字句--> WHERE字句--> SELECT字句的顺序执行语句。所以只要理解为我们先将两张表连结得到了新表，再对新表使用了WHERE限定条件进行了筛选即可。<br>
也可以先分别在两张表里做WHERE筛选，然后把筛选结果连结。
### 结合GROUP BY子句使用内连结
### 自连结(SELF JOIN)
### 内连结与关联子查询
### 自然连结(NATURAL JOIN)
### 使用连结求交集
## 4.2.2外连结(OUTER JOIN)
### 左连结与右连结
### 使用左连结从两个表获取信息
### 结合WHERE字句使用左连结
### 在MYSQL中实现全外连结
## 4.2.3多表连结
### 多表进行内连结
### 多表进行外连结
## 4.2.4 ON字句进阶-非等值连结
### 非等值自主连结(SELF JOIN)
### 交叉连结 -- CROSS JOIN(笛卡尔积)
### 连结的特定语法和过时语法




# 学习内容（注意点）
* 表的并集运算不仅可以计算表与表之间的数据，同时也可以计算表中数据的并集。UNION运算会通常都会除去重复的记录。
* UNION操作会去掉两个集合中的重复集，为了避免这种情况就可以使用UNION ALL
* 用bag模型来描述数据库。由于bag允许元素重复出现，对于两个bag，他们的并运算会按照
  * 该元素是否至少在一个bag里出现过
  * 该元素在两个bag中的最大出现次数
所以对于A={1,1,1,2,3,5,7}与B={1,1,2,2,4,6,8}两个bag来说，它们的并就等于{1,1,1,2,2,3,4,5,6,7,8}
* 属性不同的数值也可以用UNION作并集运算
* bag的交运算<br>
对于A={1,1,1,2,3,5,7},B={1,1,2,2,4,6,8}两个bag，它们的交运算结果就等于{1,1,2}
* 找出只存在于product表但不存在于product2表的商品
```
SELECT *
  FROM product
 WHERE product_id NOT IN (SELECT product_id
                            FROM product2)
```
* bag的差集运算
```
A = {1,1,1,2,3,5,7}，B = {1,1,2,2,4,6,8} 两个bag，它们的差就等于{1,3,5,7}。
```
* 内连结的例子
```
SELECT SP.shop_id,
       SP.shop_name,
       SP.product_id,
       P.product_name,
       P.product_type,
       P.sale_price,
       SP.quantity
  FROM shopproduct AS SP
INNER JOIN product AS P
    ON SP.product_id = P.product_id;
```
* 先分别对两张表作筛选再连结的例子
```
SELECT SP.shop_id,
       SP.shop_name,
       SP.product_id,
       P.product_name,
       P.product_type,
       P.sale_price,
       SP.quantity
  FROM 
      （SELECT *
        FROM shopproduct
       WHERE shop_name = '东京'） AS SP
INNER JOIN
  (SELECT *
     FROM product
    WHERE product_type = '衣服') AS P
   ON SP.product_id = P.product_id;
```

```


阿斯顿
 
