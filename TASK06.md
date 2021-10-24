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

# 练习题4
数据来源：https://tianchi.aliyun.com/dataset/dataDetail?dataId=1074

请使用A股上市公司季度营收预测中的数据集《Macro&Industry.xlsx》中的sheet-INDIC_DATA，请计算全社会用电量:第一产业:当月值在2015年用电最高峰是发生在哪月？并且相比去年同期增长/减少了多少个百分比？

## 回答

```
SELECT indic_id,
       SUM(DATA_VALUE) AS SUM_VALUE
  FROM `macro industry`
  GROUP BY indic_id WITH ROLLUP
  ORDER BY SUM_VALUE DESC;
       
SELECT PERIOD_DATE,
       MAX(DATA_VALUE) FianlValue
  FROM `macro industry`
 WHERE INDIC_ID = '2020101522'
   AND YEAR(PERIOD_DATE) = 2015
 GROUP BY PERIOD_DATE
 ORDER BY FianlValue DESC
 LIMIT 1;
 SELECT BaseData.*,
     (BaseData.FianlValue - YoY.FianlValue) / YoY.FianlValue  YoY
  FROM (SELECT PERIOD_DATE,
         MAX(DATA_VALUE) FianlValue
      FROM `macro industry`
       WHERE INDIC_ID = '2020101522'
       AND  YEAR(PERIOD_DATE) = 2015
     GROUP BY PERIOD_DATE
     ORDER BY FianlValue DESC
         LIMIT 1) BaseData
  LEFT JOIN -- YOY
     (SELECT PERIOD_DATE,
       MAX(DATA_VALUE) FianlValue
    FROM `macro industry`
     WHERE INDIC_ID = '2020101522'
     AND  YEAR(PERIOD_DATE) = 2014
     GROUP BY PERIOD_DATE ) YoY
      ON YEAR(BaseData.PERIOD_DATE) = YEAR(YoY.PERIOD_DATE) + 1
     AND MONTH(BaseData.PERIOD_DATE) = MONTH(YoY.PERIOD_DATE);
```
这一题找不到每个列名说明，有些地方搞的不是很明白

# 练习题5
 数据来源：https://tianchi.aliyun.com/competition/entrance/231593/information

使用Coupon Usage Data for O2O中的数据集《ccf_online_stage1_train.csv》，试统计在2016年6月期间，线上总体优惠券弃用率为多少？并找出优惠券弃用率最高的商家。

弃用率 = 被领券但未使用的优惠券张数 / 总的被领取优惠券张数

## 回答
```
SELECT SUM(CASE WHEN Date IS NULL AND Coupon_id IS NOT NULL
                THEN 1 ELSE 0 END)/
       SUM(CASE WHEN Coupon_id IS NOT NULL 
                THEN 1 ELSE 0 END) AS discard_rate
  FROM ccf_online_stage1_train
 WHERE Date_received BETWEEN '2016-06-01' AND '2016-06-30';
```
```
SELECT Merchant_id,
       SUM(CASE WHEN Date IS NULL AND Coupon_id IS NOT NULL
                THEN 1 ELSE 0 END)/
       SUM(CASE WHEN Coupon_id IS NOT NULL 
                THEN 1 ELSE 0 END) AS discard_rate
  FROM ccf_online_stage1_train
 WHERE Date_received BETWEEN '2016-06-01' AND '2016-06-30'
 GROUP BY Merchant_id
 ORDER BY discard_rate DESC;
```

# 练习题6
数据来源：https://tianchi.aliyun.com/dataset/dataDetail?dataId=44

请使用 Wine Quality Data 数据集《winequality-white.csv》，找出 pH=3.63的所有白葡萄酒，然后，对其 residual sugar 量进行英式排名（非连续的排名）

## 回答

```
SELECT pH,
       `residual sugar`,
       RANK() OVER (ORDER BY `residual sugar`)
  FROM winequality-white
 Where pH = 3.63 ;
```
复习一下RANK
* RANK() 1,1,1,4
* DENSE_RANK() 1,1,1,2
* ROW_NUMBER() 1,2,3,4

# 练习题7
数据来源：https://tianchi.aliyun.com/dataset/dataDetail?dataId=1074

请使用A股上市公司季度营收预测中的数据集《Market Data.xlsx》中的sheet-DATA，

计算截止到2018年底，市值最大的三个行业是哪些？以及这三个行业里市值最大的三个公司是哪些？（每个行业找出前三大的公司，即一共要找出9个）

## 回答
```
SELECT TYPE_NAME_CN,
       SUM(MARKET_VALUE) AS mak_val
  FROM `market data`
WHERE YEAR(END_DATE) = 2018
GROUP BY TYPE_NAME_CN
ORDER BY mak_val DESC
LIMIT 3;

SELECT Basedata.TYPE_NAME_CN,
       Basedata.TICKER_SYMBOL
  FROM (SELECT TYPE_NAME_CN,
               TICKER_SYMBOL,
               MARKET_VALUE,
               ROW_NUMBER() OVER (PARTITION BY TYPE_NAME_CN ORDER BY MARKET_VALUE) AS CompanyRanking
          FROM `market data`) AS Basedata
LEFT JOIN
       (SELECT TYPE_NAME_CN,
               SUM(MARKET_VALUE)
          FROM `market data`
           WHERE YEAR(END_DATE) = 2018
           GROUP BY TYPE_NAME_CN
           ORDER BY SUM(MARKET_VALUE) DESC
           LIMIT 3) AS top3Type
          ON Basedata.TYPE_NAME_CN = top3Type.TYPE_NAME_CN
    WHERE CompanyRanking <= 3
      AND top3Type.TYPE_NAME_CN IS NOT NULL
```
PARTITON BY是用来分组，即选择要看哪个窗口，类似于GROUP BY 子句的分组功能，但是PARTITION BY 子句并不具备GROUP BY 子句的汇总功能，并不会改变原始表中记录的行数。

# 练习题8
数据来源：https://tianchi.aliyun.com/competition/entrance/231593/information

使用Coupon Usage Data for O2O中的数据集《ccf_online_stage1_train.csv》和《ccf_offline_stage1_train.csv》，试找出在2016年6月期间，线上线下累计优惠券使用次数最多的顾客。

## 回答
```
SELECT User_id,
       SUM(couponCount) AS couponCount
  FROM (SELECT User_id,
               count(*) AS couponCount
          FROM `ccf_online_stage1_train`
         WHERE (Date IS NOT NULL AND Coupon_id IS NOT NULL)
               AND DATE BETWEEN '2016-06-01' AND '2016-06-30'
         GROUP BY User_id
             UNION ALL
         SELECT User_id,
                    COUNT(*) AS couponCount
          FROM `ccf_offline_stage1_train`
          WHERE (Date IS NOT NULL AND Coupon_id IS NOT NULL)
               AND DATE BETWEEN '2016-06-01' AND '2016-06-30'
         GROUP BY User_id) AS BaseData
   GROUP BY User_id
   ORDER BY couponCount DESC;
        
```

# 问题9
数据来源：https://tianchi.aliyun.com/dataset/dataDetail?dataId=1074

请使用A股上市公司季度营收预测数据集《Income Statement.xls》中的sheet-General Business和《Company Operating.xlsx》中的sheet-EN。

找出在数据集所有年份中，按季度统计，白云机场旅客吞吐量最高的那一季度对应的净利润是多少？（注意，是单季度对应的净利润，非累计净利润。）

## 回答
```
SELECT *
  FROM (SELECT TICKER_SYMBOL,
               YEAR(END_DATE) AS YEAR,
               QUARTER(END_DATE) AS QUARTER,
               SUM(VALUE) AS Amount_p
          FROM `company operating`
         WHERE INDIC_NAME_EN = 'Baiyun Airport:Passenger throughput'
         GROUP BY TICKER_SYMBOL,YEAR(END_DATE),QUARTER(END_DATE)
         ORDER BY SUM(VALUE) DESC
         LIMIT 5) AS BaseData
 LEFT JOIN
       (SELECT TICKER_SYMBOL,
               YEAR(END_DATE) AS YEAR,
               QUARTER(END_DATE) AS QUARTER,
               SUM(N_INCOME) AS Amount_income
          FROM `income statement`
         GROUP BY TICKER_SYMBOL,YEAR(END_DATE),QUARTER(END_DATE)) AS Income
    ON BaseData.TICKER_SYMBOL = Income.TICKER_SYMBOL
    AND BaseData.Year = Income.Year
    AND BaseData.QUARTER = Income.QUARTER;
```
YEAR/QUARTER/MONTH/WEEK(DATE)

# 问题10
数据来源：https://tianchi.aliyun.com/competition/entrance/231593/information

使用Coupon Usage Data for O2O中的数据集《ccf_online_stage1_train.csv》和《ccf_offline_stage1_train.csv》，试找出在2016年6月期间，线上线下累计被使用优惠券满减最多的前3名商家。

比如商家A，消费者A在其中使用了一张200减50的，消费者B使用了一张30减1的，那么商家A累计被使用优惠券满减51元。

## 回答
```
SELECT Merchant_id,
       SUM(discount_amount) as Discount_amount
  FROM (SELECT Merchant_id,
               SUM(SUBSTRING_INDEX(`Discount_rate`,':',-1)) AS discount_amount
          FROM `ccf_online_stage1_train`
         WHERE (Date IS NOT NULL AND Coupon_id IS NOT NULL)
           AND Date BETWEEN '2016-06-01' AND '2016-06-30'
         GROUP BY Merchant_id
             UNION ALL
        SELECT Merchant_id,
               SUM(SUBSTRING_INDEX(`Discount_rate`,':',-1)) AS discount_amount
          FROM `ccf_offline_stage1_train`
         WHERE (Date IS NOT NULL AND Coupon_id IS NOT NULL)
           AND Date BETWEEN '2016-06-01' AND '2016-06-30'
         GROUP BY Merchant_id) AS BaseData
 GROUP BY Merchant_id
 ORDER BY Discount_amount DESC
 LIMIT 5;
      
      
```
