本笔记为阿里云天池龙珠计划SQL训练营的学习内容，链接为：https://tianchi.aliyun.com/specials/promotion/aicampsql；

# 练习题1
数据来源：https://tianchi.aliyun.com/dataset/dataDetail?dataId=1074

请使用A股上市公司季度营收预测数据集《Income Statement.xls》和《Company Operating.xlsx》和《Market Data.xlsx》，
以Market Data为主表，将三张表中的TICKER_SYMBOL为600383和600048的信息合并在一起。只需要显示以下字段。


## 回答
```
SELECT MarketData.TICKER_SYMBOL,
       MarketData.END_DATE,
       MarketData.CLOSE_PRICE,
       IncomeStatement.T_REVENUE,
       IncomeStatement.T_COGS,
       IncomeStatement.N_INCOME,
       CompanyOperating.INDIC_NAME_EN,
       CompanyOperating.VALUE
  FROM (SELECT TICKER_SYMBOL,
               END_DATE,
               CLOSE_PRICE
          FROM `market data`
         WHERE TICKER_SYMBOL IN ('600383','60048')) AS MarketData
 LEFT JOIN (SELECT TICKER_SYMBOL,
                   END_DATE,
                   T_REVENUE,
                   T_COGS,
                   N_INCOME
             FROM `income statement`
            WHERE TICKER_SYMBOL IN('600383', '60048')) AS IncomeStatement
   ON MarketData.TICKER_SYMBOL = IncomeStatement.TICKER_SYMBOL
  AND MarketData.END_DATE = IncomeStatement.END_DATE
 LEFT JOIN (SELECT TICKER_SYMBOL,
                   END_DATE,
                   INDIC_NAME_EN,
                   VALUE
              FROM `company operating`
             WHERE TICKER_SYMBOL IN('600383','60048')) AS CompanyOperating
   ON MarketData.TICKER_SYMBOL = CompanyOperating.TICKER_SYMBOL
  AND MarketData.END_DATE = CompanyOperating.END_DATE
 ORDER BY MarketData.TICKER_SYMBOL, MarketData.END_DATE;
```
注意SQL中的文本引号是``而不是'',注意输入法要英文的标点符号，结尾不要忘记;

# 练习题2

数据来源：https://tianchi.aliyun.com/dataset/dataDetail?dataId=44

请使用 Wine Quality Data 数据集《winequality-red.csv》，找出 pH=3.03的所有红葡萄酒，然后，对其 citric acid 进行中式排名
（相同排名的下一个名次应该是下一个连续的整数值。换句话说，名次之间不应该有“间隔”）

## 回答

```
SELECT pH,
       `citric acid`,
       DENSE_RANK() OVER (ORDER BY `citric acid`) AS rankk
  FROM winequality-red
 WHERE ph = 3.03;
```
不知道为什么把第三列的排序命名为rank会报错。

# 练习题3

数据来源：https://tianchi.aliyun.com/competition/entrance/231593/information

使用Coupon Usage Data for O2O中的数据集《ccf_offline_stage1_test_revised.csv》，试分别找出在2016年7月期间，发放优惠券总金额最多和发放优惠券张数最多的商家。

这里只考虑满减的金额，不考虑打几折的优惠券。

## 回答

```
SELECT Merchant_id,
       SUM(SUBSTRING_INDEX(`Discount_rate`,':',-1)) AS discount_amount
  FROM ccf_offline_stage1_test_revised
 WHERE Date_received BETWEEN '2016-07-01' AND '2016-07-31'
 GROUP BY Merchant_id
 ORDER BY discount_amount DESC
 LIMIT 1;
SELECT Merchant_id,
       COUNT(1) AS cnt
  FROM ccf_offline_stage1_test_revised
 WHERE Date_received BETWEEN '2016-07-01' AND '2016-07-31'
 GROUP BY Merchant_id
 ORDER BY cnt DESC
 LIMIT 1;
```
复习几个语法
* SUMSTRING_INDEX(str,delim,count),delim为分隔符。如果count为负数，则表示从第倒数count个分隔符开始取右边的字符；如果count为正数，则表示从正数第count个分隔符开始取分隔符左边的字符。
* ORDER BY  xxx LIMIT i,n ,返回从i(默认为0)开始的n个数据。这里要重点注意，如果ORDER中存在相同的值的个数>n的话，会返回随机目标，为了避免这种情况发生可以多加一个ORDER条件，一般用ID，因为ID是
唯一值。
* 需要注意区分下''和``


