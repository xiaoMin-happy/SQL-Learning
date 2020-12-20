#### 3.1 

```sql
create view viewpractice5_1(product_name, sale_price, regist_date)
    -> as
    -> select product_name, sale_price, regist_date from product
    -> where sale_price >= 1000 and regist_date = '2009-09-20';
```

![图1](https://user-images.githubusercontent.com/62495140/102713715-2cc5f380-4305-11eb-803e-4f3267754203.png)

#### 3.2

```sql
INSERT INTO ViewPractice5_1 VALUES (' 刀子 ', 300, '2009-11-02');
ERROR 1423 (HY000): Field of view 'shop.viewpractice5_1' underlying table doesn't have a default value
```

Reason: 插入视图的数据也会改变表中的数据，但这行代码没有定义原表中一些含有非空约束的字段（如product_type）。

#### 3.3

```sql
select product_id, product_name, product_type, sale_price, (select AVG(sale_price) from product) as sale_price_all from product;
```

#### 3.4

```sql
create view AvgPriceByType(product_id, product_name, product_type, sale_price, avg_price_type)
    -> as
    -> select product_id,product_name,product_type,sale_price,
    -> (select AVG(sale_price) from product as P2 where P2.product_type = product.product_type
    -> group by P2.product_type) as avg_price_type
    -> from product;

```

![图2](https://user-images.githubusercontent.com/62495140/102713717-32bbd480-4305-11eb-8bff-6f8d14bf853f.png)

#### 3.5 运算或者函数中含有 NULL 时，结果全都会变为NULL ？（判断题）

题意不明

#### 3.6 

对本章中使用的 product（商品）表执行如下 2 条 SELECT 语句，能够得到什么样的结果呢？

①

```sql
SELECT product_name, purchase_price
  FROM product
 WHERE purchase_price NOT IN (500, 2800, 5000);
```

![图3](https://user-images.githubusercontent.com/62495140/102713721-394a4c00-4305-11eb-8865-fc4dfc5b5b1b.png)

②

```sql
SELECT product_name, purchase_price
  FROM product
 WHERE purchase_price NOT IN (500, 2800, 5000, NULL);
```

![图4](https://user-images.githubusercontent.com/62495140/102713725-3e0f0000-4305-11eb-85d2-f805a94c1ee1.png)

#### 3.7

```sql
select sum(case when sale_price <= 1000 then 1 else 0 end) as low_price,
    -> sum(case when sale_price between 1001 and 3000 then 1 else 0 end) as mid_price,
    -> sum(case when sale_price > 3000 then 1 else 0 end) as high_price 
    -> from product;
```

![图5](https://user-images.githubusercontent.com/62495140/102713728-423b1d80-4305-11eb-9477-01634adc1658.png)

