### Day 5.

#### 5.1 OLAP Function (窗口函数)

###### 5.1.1 OLAP (Online Analytical Processing)

窗口函数也称为**OLA\**\*\*P\*\**\*函数**。OLAP 是OnLine AnalyticalProcessing 的简称，意思是对数据库数据进行实时分析处理。

为了便于理解，称之为窗口函数。常规的SELECT语句都是对整张表进行查询，而窗口函数可以让我们有选择的去某一部分数据进行汇总、计算和排序。

窗口函数的通用形式：

```sql
<窗口函数> OVER ([PARTITION BY <列名>]
                     ORDER BY <排序用列名>)  
```

*[]中的内容可以省略。
窗口函数最关键的是搞明白关键字**PARTITON BY**和**ORDER BY**的作用。

**PARTITON BY**是用来分组，即选择要看哪个窗口，类似于GROUP BY 子句的分组功能，但是PARTITION BY 子句并不具备GROUP BY 子句的汇总功能，并不会改变原始表中记录的行数。

**ORDER BY**是用来排序，即决定窗口内，是按那种规则(字段)来排序的。

#### 5.2 窗口函数种类

大致来说，窗口函数可以分为两类。

一是 将SUM、MAX、MIN等聚合函数用在窗口函数中

二是 RANK、DENSE_RANK等排序用的专用窗口函数

##### 5.2.1 专用窗口函数

- **RANK函数**

计算排序时，如果存在相同位次的记录，则会跳过之后的位次。

例）有 3 条记录排在第 1 位时：1 位、1 位、1 位、4 位……

- **DENSE_RANK函数**

同样是计算排序，即使存在相同位次的记录，也不会跳过之后的位次。

例）有 3 条记录排在第 1 位时：1 位、1 位、1 位、2 位……

- **ROW_NUMBER函数**

赋予唯一的连续位次。

例）有 3 条记录排在第 1 位时：1 位、2 位、3 位、4 位

运行以下代码直观显示差异：

```sql
SELECT  product_name
       ,product_type
       ,sale_price
       ,RANK() OVER (ORDER BY sale_price) AS ranking
       ,DENSE_RANK() OVER (ORDER BY sale_price) AS dense_ranking
       ,ROW_NUMBER() OVER (ORDER BY sale_price) AS row_num
  FROM product  
```

##### 5.2.2 聚合函数在窗口函数上的使用

聚合函数在开窗函数中的使用方法和之前的专用窗口函数一样，只是出来的结果是一个**累计**的聚合函数值。

运行以下代码：

```sql
SELECT  product_id
       ,product_name
       ,sale_price
       ,SUM(sale_price) OVER (ORDER BY product_id) AS current_sum
       ,AVG(sale_price) OVER (ORDER BY product_id) AS current_avg  
  FROM product;  
```

可以看出，聚合函数结果是，按我们指定的排序，这里是product_id，**当前所在行及之前所有的行**的合计或均值。即累计到当前行的聚合。

#### 5.3 窗口函数的的应用 - 计算移动平均

在上面提到，聚合函数在窗口函数使用时，计算的是累积到当前行的所有的数据的聚合。 实际上，还可以指定更加详细的**汇总范围**。该汇总范围成为**框架(frame)。**

 语法

```sql
<窗口函数> OVER (ORDER BY <排序用列名>
                 ROWS n PRECEDING )  
                 
<窗口函数> OVER (ORDER BY <排序用列名>
                 ROWS BETWEEN n PRECEDING AND n FOLLOWING)
```

PRECEDING（“之前”）， 将框架指定为 “截止到之前 n 行”，加上自身行

FOLLOWING（“之后”）， 将框架指定为 “截止到之后 n 行”，加上自身行

BETWEEN 1 PRECEDING AND 1 FOLLOWING，将框架指定为 “之前1行” + “之后1行” + “自身”

执行以下代码：

```sql
SELECT  product_id
    ->        ,product_name
    ->        ,sale_price
    ->        ,AVG(sale_price) OVER (ORDER BY product_id
    ->                                ROWS 2 PRECEDING) AS moving_avg
    ->        ,AVG(sale_price) OVER (ORDER BY product_id
    ->                                ROWS BETWEEN 1 PRECEDING 
    ->                                         AND 1 FOLLOWING) AS moving_avg  
    ->   FROM product;
    
    --图note—1。
```

**执行结果：**

注意观察框架的范围。

![note-1](https://user-images.githubusercontent.com/62495140/103126197-8133ef00-46c8-11eb-9a80-198a1d25fdd9.png)

##### 5.3.1 窗口函数适用范围和注意事项

- 原则上，窗口函数只能在SELECT子句中使用。
- 窗口函数OVER 中的ORDER BY 子句并不会影响最终结果的排序。其只是用来决定窗口函数按何种顺序计算。

#### 5.4 GROUPING运算符

##### 5.4.1 ROLLUP - 计算合计及小计

常规的GROUP BY 只能得到每个分类的小计，有时候还需要计算分类的合计，可以用 ROLLUP关键字。

```sql
SELECT  product_type
       ,regist_date
       ,SUM(sale_price) AS sum_price
  FROM product
 GROUP BY product_type, regist_date WITH ROLLUP  
```

### 练习题

#### 5.1 请说出针对本章中使用的 product（商品）表执行如下 SELECT 语句所能得到的结果。

```sql
SELECT  product_id
       ,product_name
       ,sale_price
       ,MAX(sale_price) OVER (ORDER BY product_id) AS Current_max_price
  FROM product
  --答案： 按id顺序逐行显示当前的最高售价。
```

#### 5.2 继续使用product表，计算出按照登记日期（regist_date）升序进行排列的各日期的销售单价（sale_price）的总额。排序是需要将登记日期为NULL 的“运动 T 恤”记录排在第 1 位（也就是将其看作比其他日期都早）

```sql
select regist_date, product_id, product_name, sale_price, sum(sale_price) over (order by regist_date) as sumprice from product;

```

![2](https://user-images.githubusercontent.com/62495140/103126201-85600c80-46c8-11eb-8a1d-ec0ea8c7af09.png)

#### 5.3 思考题

#### ① 窗口函数不指定PARTITION BY的效果是什么？

```sql
-- Set Partiton by
select product_id, product_name, product_type, sale_price, 
    -> sum(sale_price) over (partition by product_type 
    -> order by product_id) as sum_price
    -> from product;
    
-- Without partition by
select product_id, product_name, product_type, sale_price, 
    -> sum(sale_price) over (
    -> order by product_id) as sum_price
    -> from product;
```

不设置 PARTITION BY 就没有小窗口，整个表都是一个大窗口，设置了以后优先级会高于order by，具体看代码运行结果。

#### ② 为什么说窗口函数只能在SELECT子句中使用？实际上，在ORDER BY 子句使用系统并不会报错。

