#### 4.1.1 分别使用 UNION 或者 OR 谓词,找出毛利率不足 30%或毛利率未知的商品.

```sql
		--or
select product_name from product
    -> where sale_price <  purchase_price * 1.3
    -> or sale_price is null
    -> or purchase_price is null;
    
    --union
 select product_name from product
    -> where sale_price < purchase_price * 1.3
    -> union
    -> select product_name from product
    -> where sale_price/purchase_price is null;

```

参考答案:

```sql
SELECT * 
  FROM product 
 WHERE sale_price / purchase_price < 1.3 
    OR sale_price / purchase_price IS NULL;
  
--union
```

#### 找出 product 和 product2 中售价高于 500 的商品的基本信息.

```sql
select * from product 
    -> where sale_price > 500
    -> union
    -> select * from product2 
    -> where sale_price > 500;

```

#### 练习题:商店决定对product表中利润低于50%和售价低于1000的商品提价, 请使用UNION ALL 语句将分别满足上述两个条件的结果取并集. 

```sql
SELECT * 
  FROM product 
 WHERE sale_price < 1000
 UNION ALL
SELECT * 
  FROM product 
 WHERE sale_price > 1.5 * purchase_price
```

#### 练习题: 使用 SYSDATE()函数可以返回当前日期时间, 是一个日期时间类型的数据, 试测试该数据类型和数值,字符串等类型的兼容性. 例如, 以下代码可以正确执行, 说明时间日期类型和字符串,数值以及缺失值均能兼容.

```sql
SELECT SYSDATE(), SYSDATE(), SYSDATE()
 
 UNION
 
SELECT 'chars', 123,  null
```

运行可兼容。

#### 练习题：找出只存在于product表但不存在于product2表的商品.

```sql
SELECT * 
  FROM product
 WHERE product_id NOT IN (SELECT product_id 
                            FROM product2)
```

#### 练习：使用NOT谓词进行集合的减法运算, 求出product表中, 售价高于2000,但利润低于30%的商品, 结果应该如下表所示.

参考答案:

```sql
SELECT * 
  FROM product
 WHERE sale_price > 2000 
   AND product_id NOT IN (SELECT product_id 
                            FROM product 
                           WHERE sale_price<1.3*purchase_price)
```

参考答案错误，应该是：

```sql
select * from product
    -> where sale_price >= 2000
    -> and product_id not in 
    -> (select product_id from product
    -> where sale_price > purchase_price * 1.3);
    --图1
```

![1](https://user-images.githubusercontent.com/62495140/102898628-c081f600-44a4-11eb-9289-c1a43adad74e.png)

#### 练习题: 使用product表和product2表的对称差来查询哪些商品只在其中一张表。

```sql
SELECT * 
  FROM product
 WHERE product_id NOT IN (SELECT product_id FROM product2)
UNION
SELECT * 
  FROM product2
 WHERE product_id NOT IN (SELECT product_id FROM product)
```

#### INNER JOIN练习题: 找出每个商店里的衣服类商品的名称及价格等信息.

```sql
 select * from shopproduct as sp
    -> inner join product as p
    -> on p.product_id = sp.product_id
    -> where p.product_type = '衣服';
    
 -- 参考答案 2--使用子查询
SELECT  SP.shop_id, SP.shop_name, SP.product_id
       ,P.product_name, P.product_type, P.purchase_price
  FROM shopproduct AS SP 
INNER JOIN --从 product 表找出衣服类商品的信息
  (SELECT product_id, product_name, product_type, purchase_price
     FROM product	
    WHERE product_type = '衣服')AS P 
   ON SP.product_id = P.product_id;

```

#### 练习题: 分别使用连结两个子查询和不使用子查询的方式, 找出东京商店里, 售价低于 2000 的商品信息,希望得到如下结果.

```sql
select * from shopproduct as sp
    -> inner join product as p
    -> on p.product_id = sp.product_id
    -> where sp.shop_id = '000A' and p.sale_price < 2000;
    
```

```sql
--使用子查询，较复杂
select sp.shop_id,sp.shop_name,sp.product_id,sp.quantity from (select * from shopproduct where shop_id = '000A') as sp
    -> inner join
    -> (select * from product where sale_price < 2000) as p
    -> on sp.product_id = p.product_id;

```

#### 练习题: 每类商品中售价最高的商品都在哪些商店有售?

```sql
-- Search the most expensive commodity of each category.
select p.product_id, p.product_name, p.product_type from product as p 
inner join 
(select product_type, max(sale_price) as maxprice from product group by product_type) as mp 
on p.product_type = mp.product_type and p.sale_price = mp.maxprice;
--(结果如图2)

-- Create a temp. view to search which shops have those commodities.
create view tempview (shop_id, shop_name, product_id, quantity)
    -> as
    -> select shop_id, shop_name, product_id, quantity from shopproduct
    -> where product_id in 
    -> (select product_id from product as p1 
    -> inner join
    -> (select product_type, max(sale_price) as max_price from product group by product_type) as p2
    -> on p1.product_type = p2.product_type and p1.sale_price = p2.max_price);
-- Check the temp. view.
select * from tempview;
--(结果如图3)

-- Generate the final result.
select tp.shop_id, tp.shop_name, tp.product_id, tp.quantity, p.product_name, p.product_type, p.sale_price 
from tempview as tp 
inner join 
(select product_id, product_name, product_type, sale_price from product) as p 
on p.product_id = tp.product_id;
--(最终结果如图4)
```

![234](https://user-images.githubusercontent.com/62495140/102898642-c4ae1380-44a4-11eb-8b1a-5036f4baa372.png)

![234](https://user-images.githubusercontent.com/62495140/102898662-cb3c8b00-44a4-11eb-8100-411b50fb0d39.png)

![234](https://user-images.githubusercontent.com/62495140/102898672-cf68a880-44a4-11eb-9f20-df2b367dbf02.png)

#### 练习题: 1.分别使用内连结和关联子查询每一类商品中售价最高的商品.

```sql
--sub-query
select * from product as p1
    -> where sale_price >= 
    -> (select max(sale_price) from product as p2 where p1.product_type = p2.product_type group by product_type);

--inner join
select * from product as p1
    -> inner join
    -> (select product_type, max(sale_price) as maxprice from product group by product_type) as p2
    -> on p1.product_type = p2.product_type and p1.sale_price = p2.maxprice;
```

#### 自然连结练习题：试写出与上述自然连结等价的内连结.

```sql
-- 参考答案
SELECT  SP.product_id,SP.shop_id,SP.shop_name,SP.quantity
       ,P.product_name,P.product_type,P.sale_price
       ,P.purchase_price,P.regist_date  
  FROM shopproduct AS SP 
 INNER JOIN product AS P 
    ON SP.product_id = P.product_id
```

#### 连结求交集：

```sql
SELECT P1.*
  FROM product AS P1
 INNER JOIN product2 AS P2
    ON P1.product_id = P2.product_id

```

#### 练习题: 希望对 product 表中的商品按照售价赋予排名. 一个从集合论出发,使用自左连结的思路是, 对每一种商品,找出售价不低于它的所有商品, 然后对售价不低于它的商品使用 COUNT 函数计数. 例如, 对于价格最高的商品,

```
SELECT  product_id
       ,product_name
       ,sale_price
       ,COUNT(p2_id) AS rank
  FROM (SELECT P1.product_id
               ,P1.product_name
               ,P1.sale_price
               ,P2.product_id AS P2_id
               ,P2.product_name AS P2_name
               ,P2.sale_price AS P2_price 
          FROM product AS P1 
          LEFT OUTER JOIN product AS P2 
            ON P1.sale_price <= P2.sale_price 
        ) AS X
 GROUP BY product_id, product_name, sale_price
 ORDER BY rank; 
```

#### 练习题: 请按照商品的售价从低到高,对售价进行累计求和[注:这个案例缺少实际意义, 并且由于有两种商品价格相同导致了不必要的复杂度, 但示例数据库的表结构比较简单, 暂时未想出有实际意义的例题].

首先, 按照题意, 对每种商品使用自左连结, 找出比该商品售价价格更低或相等的商品

```sql
SELECT  P1.product_id
       ,P1.product_name
       ,P1.sale_price
       ,P2.product_id AS P2_id
       ,P2.product_name AS P2_name
       ,P2.sale_price AS P2_price 
  FROM product AS P1 
  LEFT OUTER JOIN product AS P2 
    ON P1.sale_price >= P2.sale_price
 ORDER BY P1.sale_price,P1.product_id, P2.sale_price, P2.product_id;	
 --结果如图5
```

![5](https://user-images.githubusercontent.com/62495140/102898683-d394c600-44a4-11eb-8e72-f678b083051b.png)

```sql
--如果直接分组求和，会因为条件不清晰出现问题。
select product_id, product_name, sale_price, sum(p2_price) as sum_price
    -> from (
    -> SELECT  P1.product_id        ,P1.product_name        ,P1.sale_price        ,P2.product_id AS P2_id        ,P2.product_name AS P2_name        ,P2.sale_price AS P2_price    FROM product AS P1    LEFT OUTER JOIN product AS P2      ON P1.sale_price >= P2.sale_price  ORDER BY P1.sale_price,P1.product_id, P2.sale_price, P2.product_id) as x
    -> group by product_id, product_name, sale_price
    -> order by sale_price, product_id;
--如图6

--需将同价商品按条件区分清楚，如下
SELECT product_id, product_name, sale_price
    ->        ,SUM(P2_price) AS cum_price 
    ->   FROM
    ->         (SELECT  P1.product_id, P1.product_name, P1.sale_price
    ->                 ,P2.product_id AS P2_id
    ->                 ,P2.product_name AS P2_name
    ->                 ,P2.sale_price AS P2_price 
    ->            FROM product AS P1 
    ->            LEFT OUTER JOIN product AS P2 
    ->              ON ((P1.sale_price > P2.sale_price)
    ->              OR (P1.sale_price = P2.sale_price 
    ->             AND P1.product_id<=P2.product_id))
    ->       ORDER BY P1.sale_price,P1.product_id) AS X
    ->  GROUP BY product_id, product_name, sale_price
    ->  ORDER BY sale_price,cum_price;
--结果正确 图7
```

![6](https://user-images.githubusercontent.com/62495140/102898698-d8f21080-44a4-11eb-8c38-6b808ad4cc9d.png)

![7](https://user-images.githubusercontent.com/62495140/102898720-e14a4b80-44a4-11eb-9d86-a07a05fe3bf1.png)