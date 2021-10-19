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
结合GROUPBY子句使用内连结，需要根据分组位列于哪个表区别对待。最简单的情形，是在内连结之前就使用GROUOPBY子句。但是如果分组列和被聚合的列不在同一张表，且二者都未被用于连结两张表，则只能
先连结，再聚合。
### 自连结(SELF JOIN)
一张表与自身连结
### 内连结与关联子查询
```
SELECT p1.product_id,
       p1.product_name,
       p1.product_type,
       p1.sale_price,
       p2.avg_price
  FROM product AS p1
INNER JOIN
  (SELECT product_type, AVG(sale_price) AS avg_price
     FROM product
     GROUP BY product_type) AS p2
   ON p1.product_type = p2.product_type
 WHERE p1.sale_price > p2.avg_price；
```
### 自然连结(NATURAL JOIN)
自然连结是内连结的一种特例-当两个表进行自然连结时，会按照两个表中都包含的列名来进行等值内连结，此时无需使用ON来指定连结条件。
```
SELECT = FROM shopproduct NATURAL JOIN product
```
使用自然连结可以求出两张表或子查询的公共部分，类似INTERSECT。
### 使用连结求交集
尝试使用连结来求交集
```
SELECT p1.*
  FROM product AS p1
INNER JOIN product2 AS p2
    ON (p1.product_id = p2.product_id
    AND p1.product_name = p2.product_name
    AND p1.product_type = p2.product_type
    AND p1.sale_price = p2.sale_price
    AND p1.regist_date = p2.regist_date)
```
注意，如果其中存在缺失值NULL，则不能用等号相连接。
## 4.2.2外连结(OUTER JOIN)
内连结会丢弃两表中不满足ON条件的行，和内连结相对的就是外连结。外连结会根据外连结的种类有选择地保留无法匹配到的行。其中分为左连结，右连结和全外连结。
### 左连结与右连结
左连结会保存左表中无法按照ON子句匹配的行，此时对应右表的行均为缺失值；右连结则会保存右表中无法按照ON子句匹配到的行，此时对应左表的行均为缺失值；全外连结则会同时保存
两个表中无法按照ON子句匹配到的行，相应的另一张表中的行用缺失值填充。
### 使用左连结从两个表获取信息
```
SELECT SP.shop_id,
       SP.shop_name,
       SP.product_id,
       P.product_name,
       P.sale_price,
  FROM product AS P
LEFT OUTER JOIN shopproduct AS SP
    ON SP.product_id = P.product_id;
```
### 结合WHERE字句使用左连结
```
SELECT p.product_id,
       p.product_name,
       p.sale_price,
       sp.shop_id,
       sp.shop_name,
       sp.quantity
  FROM product AS p
LEFT OUTER JOIN --先用子查询筛选出quantity < 50的商品
 (SELECT *
    FROM shopproduct
   WHERE quantity < 50) AS sp
  ON sp.product_id = p.product_id
```
### 在MYSQL中实现全外连结
MYSQL 8.0还不支持全外连结
## 4.2.3多表连结
多表连结指多张表连结的情况，原则上连结表的数量并没有限制
### 多表进行内连结
```
SELECT SP.shop_id,
       SP.shop_name,
       SP.product_id,
       P.product_name,
       P.sale_price,
       IP.inventory_quantity
  FROM shopproduct AS SP
INNER JOIN product AS P
    ON SP.product_id = P.product_id
INNER JOIN Inventoryproduct AS IP
    ON SP.product_id = IP.product_id
WHERE IP.inventory_id = 'P001'
```
连结第三张表的时候，也是通过ON子句指定连结条件，由于product表和shopproduct表已经进行了连结。因此就无需再对product表和inventoryproduct表进行连结。
### 多表进行外连结
多表可以进行外连接，能够比内连结给出更多关于主表的信息
## 4.2.4 ON字句进阶-非等值连结
除了用'='来作等值连结，也可以用比较运算符
### 非等值自左连结(SELF JOIN)
对product表中的商品按照售价赋予排名
```
SELECT product_id,
       product_name,
       sale_price,
       COUNT(p2_id) AS rank_id
  FROM (--使用左连结对每种商品找出价格不低于它的商品
       SELECT  p1.product_id,
               p1.product_name,
               p2.product_id AS p2_id
               p2.product_name AS p2_name
               p2.sale_price AS p2_price
          FROM product AS p1
          LEFT OUTER JOIN product AS p2
            ON p1.sale_price <= p2.sale_price) AS x
  GROUP BY product_id, product_name, sale_price
  GROUP BY rank_id
       
```
### 交叉连结 -- CROSS JOIN(笛卡尔积)
如果没有ON来指定连结条件，那CROSS JOIN就是两张表的笛卡尔积。
实际上，用笛卡尔积得到表，再用WHERE进行限定，可以得到相同的内连结。
### 连结的特定语法和过时语法
因为一些特定的语法和历史遗留问题。一部分人仍然使用WHERE而不是ON，所以希望这部分的语法结构也能掌握。



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
* 外连结的要点
  * 选取出单张表中的全部信息
  * 使用LEFT，RIGHT来指定主表

# 问题练习
## 4.1
```
SELECT *
  FROM product
WHERE sale_price > 500
 UNION
SELECT *
  FROM product2
WHERE sale_price > 500;
```
## 4.2
```
SELECT *
  FROM product
WHERE product_id NOT IN(SELECT product_id
                          FROM product
                         WHERE product_id NOT IN (SELECT product_id
                                                    FROM product2))
 UNION
SELECT*
  FROM product 2
WHERE product_id NOT IN(SELECT product_id
                          FROM product2
                         WHERE product_id NOT IN (SELECT product_id
                                                    FROM product2));
```

## 4.3
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
INNER JOIN (SELECT product_type
                   MAX(P.sale_price) AS maxp
              FROM product
            GROUP BY product_type) AS mp
   ON mp.product_type=P.product_type and mp.maxp=P.sale_price;
            
```
## 4.4 
内连结
```
SELECT p.sale_price,
       p.product_id,
       p.product_name,
       p.product_type
  FROM product AS p
INNER JOIN (SELECT product_type,
                   MAX(sale_price) AS maxp
              FROM product
            GROUP BY product_type) AS mp
    ON mp.product_type=p.product.type and mp.maxp = p.sale_price;
```
关联子查询
```
SELECT p.sale_price,
       p.product_id,
       p.product_name,
       p.product_type
  FROM product AS p
 WHERE sale_price = (SELECT MAX(sale_price)
                       FROM product AS p1
                      WHERE p.product_type = p1.product_type
                      GROUP BY product_type);
```
## 4.5
```
SELECT p.product_id, 
       p.product_name, 
       p.product_type, 
       p.sale_price,
	    (SELECT SUM(sale_price) 
         FROM product as p1
	     where p.sale_price > p1.sale_price
	        OR (p.sale_price = p1.sale_price AND p.product_id >= p1.product_id)
	      ) as 'acc_sum'
  FROM product as p 
 ORDER BY sale_price;
```

# 学习总结
本周内容较多，主要学习了表的集合运算和连结。其中表的运算中因为MYSQL8.0还不支持EXCEPT和INTERSECT操作，需要对表的集合运算有较为熟悉的掌握。<br>
连结分为内连结和外连结，外连结可以保留主表较多的信息。
         
