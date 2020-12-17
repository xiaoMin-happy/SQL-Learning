## 一、安装

### 1.1 下载与环境变量配置

官网下载地址：[https://dev.mysql.com/downloads/ ](https://dev.mysql.com/downloads/)

社区版本：[MySQL Community Server](https://dev.mysql.com/downloads/mysql/)  DMG Archive

历史版本：[Download Archives](https://downloads.mysql.com/archives/)的[MySQL Installer](https://downloads.mysql.com/archives/installer/)

一路继续，不选择强密码

打开系统偏好设置，会出现MySQL图标，点击该图标可以设置是否随开机启动，启动或停止MySQL服务

配置环境变量：

```
PATH="$PATH":/usr/local/mysql/bin
mysql -u root -p
mysql> quit
export PATH=$PATH:/usr/local/mysql/bin
source ~/.bash_profile
```

### 1.2 MySQL Workbench

Mac上有哪些SQL数据库开发工具：[https://www.zhihu.com/question/20498949 1](https://www.zhihu.com/question/20498949)

MySQL Workbench是一款强大的数据库管理工具，既可以用于设计数据库，也可以用于连接数据库进行查询

下载地址：https://dev.mysql.com/downloads/workbench/

![界面1](https://user-images.githubusercontent.com/62495140/102097491-f13cac80-3e60-11eb-9c2c-aa31948425db.png)

![界面2](https://user-images.githubusercontent.com/62495140/102097644-23e6a500-3e61-11eb-9280-e9d47490a89a.png)

## 二、 初识数据库

数据库（DataBase，DB），数据库管理系统（Database Management System，DBMS）

### 2.1 DBMS的种类

根据数据的保存形式进行分类：

- 关系数据库（Ralational Database，RDB）
- 层次数据库（Hierarchical Database，HDB）
- 面象对象数据库（Object Oriented Database，OODB）
- XML数据库（XMLDB）
- 键值存储系统（Key-Value Store，KVS）

比较有影响的RDBMS：

- Oracle Database：甲骨文公司的RDBMS
- SQL Server：微软公司的RDBMS
- DB2：IBM公司的RDBMS
- PostgreSQL：开源的RDBMS
- MySQL：开源的RDBMS

### 2.2 RDMS最常见的系统结构

客户端/服务器类型（C/S）

<img src="http://datawhale.club/uploads/default/original/1X/f096eda423f11a0267337508a06ecf379774a656.jpeg" alt="ZS0b6ruwEzagy697"  />



## 三、初识SQL

![4F8lJm8IghrjYaV0](http://datawhale.club/uploads/default/original/1X/98c8218029171ffb8b8ab9c5f816996993fe7b17.jpeg)

数据库中存储的表与exce的行列类似：

- 记录：行，相当于一条记录
- 字段：列，表中存储的数据项目
- 单元格：行和列交汇的地方，一个单元格只能输入一条记录

### 3.1  SQL语句种类

SQL语句可以分为三类：

- DDL：数据定义语言（Data Definition Language） 用来创建或者删除存储数据用的数据库以及数据库中的表等对象。DDL 包含以下几种指令：
  - CREATE ： 创建数据库和表等对象
  - DROP ： 删除数据库和表等对象
  - ALTER ： 修改数据库和表等对象的结构

- DML：数据操纵语言（Data Manipulation Language） 用来查询或者变更表中的记录。DML 包含以下几种指令：
  - SELECT ：查询表中的数据
  - INSERT ：向表中插入新数据
  - UPDATE ：更新表中的数据
  - DELETE ：删除表中的数据
- DCL：数据控制语言（Data Control Language） 用来确认或者取消对数据库中的数据进行的变更。除此之外，还可以对 RDBMS 的用户是否有权限操作数据库中的对象（数据库表等）进行设定。DCL 包含以下几种指令：
  - COMMIT ： 确认对数据库中的数据进行的变更
  - ROLLBACK ： 取消对数据库中的数据进行的变更
  - GRANT ： 赋予用户操作权限
  - REVOKE ： 取消用户的操作权限

实际操作中的SQL语句90%属于DML。

### 3.2 SQL语句的基本书写规则

- 以；结尾
- 关键字不区分大小写，但是插入到表中的数据区分大小写
- win系统默认不区分表名及字段名的大小写
- linux / mac 默认严格区分表名及字段名的大小写
- ‘abc’, 1234, ‘26 Jan 2010’, ‘10/01/26’, ‘2010-01-26’…
- 单词需要**半角空格**或者换行符来分隔

### 3.3  数据库和表的创建

```sql
-- 数据库的创建
CREATE DATABASE < 数据库名称 > ;

-- 表的创建
CREATE TABLE < 表名 >
( < 列名 1> < 数据类型 > < 该列所需约束 > ,
  < 列名 2> < 数据类型 > < 该列所需约束 > ,
  < 列名 3> < 数据类型 > < 该列所需约束 > ,
  < 列名 4> < 数据类型 > < 该列所需约束 > ,
  .
  .
  .
  < 该表的约束 1> , < 该表的约束 2> ,……);
```

举例

```sql
CREATE DATABASE shop;

CREATE TABLE product
(product_id CHAR(4) NOT NULL,
 product_name VARCHAR(100) NOT NULL,
 product_type VARCHAR(32) NOT NULL,
 sale_price INTEGER ,
 purchase_price INTEGER ,
 regist_date DATE ,
 PRIMARY KEY (product_id));
```

### 3.4  命名规则、数据类型的指定、约束的设置

- 只能使用半角英文字母、数字、下划线（_）作为数据库、表和列的名称
- 名称必须以半角英文字母开头
- 四种基本的数据类型
  - INTEGER：整数
  - CHAR ：用来存储定长字符串，当列中存储的字符串长度达不到最大长度的时候，使用半角空格进行补足，由于会浪费存储空间，所以一般不使用。
  - VARCHAR ：用来存储可变长度字符串，即使字符数未达到最大长度，也不会用半角空格补足。
  - DATE：用来指定存储日期（年月日）的列的数据类型
- 约束设置：
  - `NOT NULL`是非空约束，即该列必须输入数据。
  - `PRIMARY KEY`是主键约束，代表该列是唯一值，可以通过该列取出特定的行的数据。

### 3.5  表的删除和更新

```sql
-- 表的删除
DROP TABLE < 表名 > ;     # 需要特别注意的是，删除的表是无法恢复的，只能重新插入

-- 添加列
ALTER TABLE < 表名 > ADD COLUMN < 列的定义 >;

-- 删除列
ALTER TABLE < 表名 > DROP COLUMN < 列名 >;         
-- 注意：ALTER TABLE 语句和 DROP TABLE 语句一样，执行之后无法恢复。误添的列可以通过 ALTER TABLE 语句删除，或者将表全部删除之后重新再创建。

-- 清空表 
TRUNCATE TABLE TABLE_NAME;   # 相比于drop，delet速度更快

-- 数据的更新
UPDATE <表名>
SET <列名> = <表达式> [, <列名2>=<表达式2>...];  
WHERE <条件>;  -- 可选，非常重要。 使用 update 时要注意添加 where 条件，否则将会将所有的行按照语句修改
ORDER BY 子句;  --可选
LIMIT 子句; --可选
```

举例

```sql
DROP TABLE product;

ALTER TABLE product ADD COLUMN product_name_pinyin VARCHAR(100);

ALTER TABLE product DROP COLUMN product_name_pinyin;

-- 修改所有的注册时间
UPDATE product
   SET regist_date = '2009-10-10';  
-- 仅修改部分商品的单价
UPDATE product
   SET sale_price = sale_price * 10
 WHERE product_type = '厨房用具';
-- NULL清空
UPDATE product
   SET regist_date = NULL
 WHERE product_id = '0008'; --和 INSERT 语句一样， UPDATE 语句也可以将 NULL 作为一个值来使用。但是，只有未设置 NOT NULL 约束和主键约束的列才可以清空为NULL。如果将设置了上述约束的列更新为 NULL，就会出错，这点与INSERT 语句相同
--多列更新
UPDATE product
   SET sale_price = sale_price * 10,
       purchase_price = purchase_price / 2  -- SET 子句中的列不仅可以是两列，还可以是三列或者更多。
 WHERE product_type = '厨房用具'; 
```

### 3.6 表的插入

```sql
INSERT INTO <表名> (列1, 列2, 列3, ……) VALUES (值1, 值2, 值3, ……);  
-- 对表进行全列 INSERT 时，可以省略表名后的列清单。这时 VALUES子句的值会默认按照从左到右的顺序赋给每一列。
```

举例

```sql
-- 包含列清单
INSERT INTO ProductIns (product_id, product_name, product_type, 
sale_price, purchase_price, regist_date) 
VALUES ('0005', '高压锅', 
'厨房用具', 6800, 5000, '2009-01-15');

-- 省略列清单
INSERT INTO ProductIns 
VALUES ('0005', '高压锅', '厨房用具', 6800, 5000, '2009-01-15'); 

-- 多行INSERT （ DB2、SQL、SQL Server、 PostgreSQL 和 MySQL多行插入）
INSERT INTO ProductIns VALUES ('0002', '打孔器', 
'办公用品', 500, 320, '2009-09-11'),
('0003', '运动T恤', '衣服', 4000, 2800, NULL),
('0004', '菜刀', '厨房用具', 3000, 2800, '2009-09-20');  

-- Oracle中的多行INSERT
INSERT ALL INTO ProductIns VALUES ('0002', '打孔器', '办公用品', 500, 320, '2009-09-11')
INTO ProductIns VALUES ('0003', '运动T恤', '衣服', 4000, 2800, NULL)
INTO ProductIns VALUES ('0004', '菜刀', '厨房用具', 3000, 2800, '2009-09-20')
SELECT * FROM DUAL;  

-- DUAL是Oracle特有（安装时的必选项）的一种临时表A。因此“SELECT *FROM DUAL” 部分也只是临时性的，并没有实际意义。

-- INSERT 语句中想给某一列赋予 NULL 值时，可以直接在 VALUES子句的值清单中写入 NULL。想要插入 NULL 的列一定不能设置 NOT NULL 约束。
INSERT INTO ProductIns (product_id, product_name, product_type, 
sale_price, purchase_price, regist_date) VALUES ('0006', '叉子', 
'厨房用具', 500, NULL, '2009-09-20');  

-- 还可以向表中插入默认值（初始值）。可以通过在创建表的CREATE TABLE 语句中设置DEFAULT约束来设定默认值。
CREATE TABLE ProductIns
(product_id CHAR(4) NOT NULL,
（略）
sale_price INTEGER
（略）	DEFAULT 0, -- 销售单价的默认值设定为0;
PRIMARY KEY (product_id)); 

-- 将商品表中的数据复制到商品复制表中
INSERT INTO ProductCopy (product_id, product_name, product_type, sale_price, purchase_price, regist_date)
SELECT product_id, product_name, product_type, sale_price, 
purchase_price, regist_date
FROM Product;
```

## 四、练习题

1. 编写一条 CREATE TABLE 语句，用来创建一个包含表 1-A 中所列各项的表 Addressbook （地址簿），并为 regist_no （注册编号）列设置主键约束

![Z4El4zKbbWnaVHro](http://datawhale.club/uploads/default/original/1X/71f46b1e1a52c6504af2f7845d78971d5b47e971.png)

2. 假设在创建练习1.1中的 Addressbook 表时忘记添加如下一列 postal_code （邮政编码）了，请把此列添加到 Addressbook 表中。

列名 ： postal_code

数据类型 ：定长字符串类型（长度为 8）

约束 ：不能为 NULL

3. 编写 SQL 语句来删除 Addressbook 表。

4. 编写 SQL 语句来恢复删除掉的 Addressbook 表。

```sql
--1.
-- 创建表之前要创建数据库
CREATE TABLE Addressbook
(regist_no INTEGER NOT NULL PRIMARY KEY, 
name VARCHAR(128) NOT NULL, 
address VARCHAR(256) NOT NULL, 
tel_no CHAR(10), 
mail_address CHAR(20)
PRIMARY KEY (regist_no));
--2.
ALTER TABLE Addressbook ADD COLUMN postal_code CHAR(8) NOT NULL
--3.
DROP TABLE Addressbook
--4.
删除后的表无法使用命令进行恢复，需要再次创建所需表
ROLLBACK
```

