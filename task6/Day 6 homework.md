#### 1. 各部门工资最高的员工

```sql
-- Create Table 'Employee'.
CREATE TABLE Employee (
    -> Id CHAR(2) NOT NULL,
    -> name VARCHAR(100) NOT NULL,
    -> salary VARCHAR(10) NOT NULL,
    -> department_id VARCHAR(2),
    -> PRIMARY KEY (Id));
    
-- Insert values.
insert into employee values ('1', 'Joe', '70000', '1');
insert into employee values ('2','Henry','80000','2');
insert into employee values ('3','Sam','60000','2');
insert into employee(Id, name, Salary, Department_id)  values ('4','Max','90000','1');

-- Check data.
select * from employee;

--Create Table 'Department'.
CREATE TABLE Department(
    -> Id CHAR(2) NOT NULL,
    -> Department_name VARCHAR(100) NOT NULL);

ALTER TABLE Department ADD PRIMARY KEY (Id);

insert into department values('1','IT');
insert into department values('2','Sales');

-- Check data.
SELECT * FROM department;

-- Query: Search the Highest Salary of each department.
select d.Department_name, employee, salary from (select name as employee, salary, department_id from employee where salary in (select max(salary) from employee group by department_id)) as ee
    -> inner join department as d
    -> on ee.department_id = d.Id;
```

#### 2.  换座位（难度：中等）

小美是一所中学的信息科技老师，她有一张 seat 座位表，平时用来储存学生名字和与他们相对应的座位 id。其中纵列的**id**是连续递增的, 小美想改变相邻俩学生的座位。

**示例：**

```nohighlight
+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Abbot   |
|    2    | Doris   |
|    3    | Emerson |
|    4    | Green   |
|    5    | Jeames  |
+---------+---------+
```

假如数据输入的是上表，则输出结果如下：

```nohighlight
+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Doris   |
|    2    | Abbot   |
|    3    | Green   |
|    4    | Emerson |
|    5    | Jeames  |
+---------+---------+
```

**注意：**
如果学生人数是奇数，则不需要改变最后一个同学的座位。

```sql
-- Create Table.
CREATE TABLE student(
id INT(4) NOT NULL,
student VARCHAR(100) NOT NULL);

INSERT INTO STUDENT VALUES (1,'Abbot');
INSERT INTO STUDENT VALUES (2,'Doris');
INSERT INTO STUDENT VALUES (3,'Emerson');
INSERT INTO STUDENT VALUES (4,'Green');
INSERT INTO STUDENT VALUES (5,'James');

-- Alter table (change the name of column 'student' to 'name').
ALTER TABLE student CHANGE COLUMN student name VARCHAR(100);
    
-- QUERY.
SELECT ROW_NUMBER() OVER(ORDER BY id) AS ID, name
FROM (SELECT CASE WHEN MOD(id, 2) = 1 THEN id + 1 ELSE id - 1 END AS ID, name
      FROM student) AS A;
```

#### 3. 分数排名（难度：中等）

编写一个 SQL 查询来实现分数排名。如果两个分数相同，则两个分数排名（Rank）相同。请注意，平分后的下一个名次应该是下一个连续的整数值。换句话说，名次之间不应该有“间隔”。

```sql
-- Create Table
CREATE TABLE score( id INT(2) NOT NULL PRIMARY KEY, score VARCHAR(10));

INSERT INTO score VALUES ('1', '3.50');
INSERT INTO score VALUES ('2', '3.65');
INSERT INTO score VALUES ('3', '4.00');
INSERT INTO score VALUES ('4', '3.85');
INSERT INTO score VALUES ('5', '4.00');
INSERT INTO score VALUES ('6', '3.65');

-- Query
SELECT score, (DENSE_RANK() OVER (ORDER BY score DESC)) AS 'Rank' from score;
-- 'Rank' 不加引号会报错

+-------+------+
| score | Rank |
+-------+------+
| 4.00  |    1 |
| 4.00  |    1 |
| 3.85  |    2 |
| 3.65  |    3 |
| 3.65  |    3 |
| 3.50  |    4 |
+-------+------+
6 rows in set (0.00 sec)

```

#### 4.连续出现的数字

编写一个 SQL 查询，查找所有至少连续出现三次的数字。

```sql
-- CREATE TABLE.
CREATE TABLE Q4(
    -> ID INT(2) PRIMARY KEY NOT NULL AUTO_INCREMENT,
    -> NUM VARCHAR(4) NOT NULL);

INSERT INTO Q4(NUM) VALUES('1');
INSERT INTO Q4(NUM) VALUES('1');
INSERT INTO Q4(NUM) VALUES('1');
INSERT INTO Q4(NUM) VALUES('2');
INSERT INTO Q4(NUM) VALUES('1');
INSERT INTO Q4(NUM) VALUES('2');
INSERT INTO Q4(NUM) VALUES('2');

--QUERY.
SELECT DISTINCT QQ1.NUM AS 'ConsecutiveNums' FROM Q4 AS QQ1, Q4 AS QQ2, Q4 AS QQ3 WHERE QQ1.ID = QQ2.ID - 1 AND QQ1.ID = QQ3.ID -2 AND QQ1.NUM = QQ2.NUM AND QQ1.NUM = QQ3.NUM;

+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
1 row in set (0.00 sec)
```

#### 5.树节点

对于**tree**表，*id*是树节点的标识，*p_id*是其父节点的*id*。

```nohighlight
+----+------+
| id | p_id |
+----+------+
| 1  | null |
| 2  | 1    |
| 3  | 1    |
| 4  | 2    |
| 5  | 2    |
+----+------+
```

每个节点都是以下三种类型中的一种：

- Root: 如果节点是根节点。
- Leaf: 如果节点是叶子节点。
- Inner: 如果节点既不是根节点也不是叶子节点。

写一条查询语句打印节点id及对应的节点类型。按照节点id排序。上面例子的对应结果为：

```nohighlight
+----+------+
| id | Type |
+----+------+
| 1  | Root |
| 2  | Inner|
| 3  | Leaf |
| 4  | Leaf |
| 5  | Leaf |
+----+------+
```

**说明**

- 节点’1’是根节点，因为它的父节点为NULL，有’2’和’3’两个子节点。
- 节点’2’是内部节点，因为它的父节点是’1’，有子节点’4’和’5’。
- 节点’3’，‘4’，'5’是叶子节点，因为它们有父节点但没有子节点。

下面是树的图形：

```
    1         
  /   \ 
 2    3    
/ \
4  5
```

**注意**

如果一个树只有一个节点，只需要输出根节点属性。

```sql
--CREATE TABLE
CREATE TABLE tree(
    -> id INT(3) PRIMARY KEY NOT NULL AUTO_INCREMENT,
    -> p_id INT(3));

INSERT INTO tree(p_id) VALUES (NULL);
INSERT INTO tree(p_id) VALUES ('1');
INSERT INTO tree(p_id) VALUES ('1');
INSERT INTO tree(p_id) VALUES ('2');
INSERT INTO tree(p_id) VALUES ('2');

-- QUERY.
SELECT id,  
    -> (CASE WHEN(p_id IS NULL) THEN ('ROOT') 
    -> WHEN (id NOT IN (SELECT id FROM tree WHERE id IN (SELECT p_id FROM tree))) THEN ('LEFT') 
    -> ELSE ('INNER') END) AS TYPE FROM tree;
    
    --left node 嵌套有点多，待优化。
```

#### 6. 至少有五名直接下属的经理 （难度：中等）

**Employee**表包含所有员工及其上级的信息。每位员工都有一个Id，并且还有一个对应主管的Id（ManagerId）。

```nohighlight
+------+----------+-----------+----------+
|Id    |Name 	  |Department |ManagerId |
+------+----------+-----------+----------+
|101   |John 	  |A 	      |null      |
|102   |Dan 	  |A 	      |101       |
|103   |James 	  |A 	      |101       |
|104   |Amy 	  |A 	      |101       |
|105   |Anne 	  |A 	      |101       |
|106   |Ron 	  |B 	      |101       |
+------+----------+-----------+----------+
```

针对**Employee**表，写一条SQL语句找出有5个下属的主管。对于上面的表，结果应输出：

```nohighlight
+-------+
| Name  |
+-------+
| John  |
+-------+
```

**注意:** 没有人向自己汇报。

```sql
--CREATE TABLE
CREATE TABLE EMPLOYEE_6(
ID INT(4) PRIMARY KEY NOT NULL,
NAME VARCHAR(20) NOT NULL,
DEPARTMENT VARCHAR(4) NOT NULL,
MANAGER INT(4));

INSERT INTO EMPLOYEE_6 VALUES('102','DAN','A',101);
INSERT INTO EMPLOYEE_6 VALUES('103','JAMES','A',101);
INSERT INTO EMPLOYEE_6 VALUES('104','AMY','A',101);
INSERT INTO EMPLOYEE_6 VALUES('105','ANNE','A',101);
INSERT INTO EMPLOYEE_6 VALUES('106','RON','B',101);

--QUERY.
SELECT NAME FROM (SELECT * FROM EMPLOYEE_6 AS E1 LEFT JOIN (SELECT MANAGER AS MANAGER_ID, COUNT(NAME) AS LABEL FROM EMPLOYEE_6 GROUP BY MANAGER) AS E2 ON E1.ID = E2.MANAGER_ID) AS EE WHERE EE.LABEL >= 5; 

+------+
| NAME |
+------+
| JOHN |
+------+
1 row in set (0.00 sec)

```

#### 7. 分数排名 （难度：中等）

练习三的分数表，实现排名功能，但是排名需要是非连续的，如下：

```sql
SELECT score, RANK() OVER (ORDER BY score DESC) AS 'RANK'  FROM score;
+-------+------+
| score | RANK |
+-------+------+
| 4.00  |    1 |
| 4.00  |    1 |
| 3.85  |    3 |
| 3.65  |    4 |
| 3.65  |    4 |
| 3.50  |    6 |
+-------+------+
6 rows in set (0.00 sec)
```

#### 8. 查询回答率最高的问题 （难度：中等）

求出**survey_log**表中回答率最高的问题，表格的字段有：**uid, action, question_id, answer_id, q_num, timestamp**。

uid是用户id；action的值为：“show”， “answer”， “skip”；当action是"answer"时，answer_id不为空，相反，当action是"show"和"skip"时为空（null）；q_num是问题的数字序号。

写一条sql语句找出回答率最高的问题。

**举例：**

**输入**

| uid  | action | question_id | answer_id | q_num | timestamp |
| :--- | :----- | :---------- | :-------- | :---- | :-------- |
| 5    | show   | 285         | null      | 1     | 123       |
| 5    | answer | 285         | 124124    | 1     | 124       |
| 5    | show   | 369         | null      | 2     | 125       |
| 5    | skip   | 369         | null      | 2     | 126       |

**输出**

| survey_log |
| :--------- |
| 285        |

**说明**

问题285的回答率为1/1，然而问题369的回答率是0/1，所以输出是285。

**注意：**最高回答率的意思是：同一个问题出现的次数中回答的比例。

```sql
-- QUERY.
-- CREATE A TEMPORARY VIEW.
CREATE VIEW TEMP_ AS
SELECT QUESID, SUM(CASE WHEN ACTION='ANSWER' THEN 1 ELSE 0 END) AS ANSWER_NUM, SUM(CASE WHEN ACTION='SHOW' THEN 1 ELSE 0 END) AS SHOW_NUM, SUM(CASE WHEN ACTION='SKIP' THEN 1 ELSE 0 END) AS SKIP_NUM FROM SURVEY_LOG GROUP BY QUESID;

SELECT QUESID, ANSWER_NUM/SHOW_NUM AS RATE FROM TEMP_
ORDER BY RATE DESC;

-- RESULT.
+--------+--------+
| QUESID | RATE   |
+--------+--------+
| 285    | 1.0000 |
| 369    | 0.0000 |
+--------+--------+
2 rows in set (0.00 sec)


```

#### 9. 各部门前3高工资的员工（难度：中等）

将项目7中的employee表清空，重新插入以下数据（其实是多插入5,6两行）：

```nohighlight
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
+----+-------+--------+--------------+
```

编写一个 SQL 查询，找出每个部门工资前三高的员工。例如，根据上述给定的表格，查询结果应返回：

```nohighlight
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Randy    | 85000  |
| IT         | Joe      | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |
+------------+----------+--------+
```

此外，请考虑实现各部门前N高工资的员工功能。

```sql
-- EMPTY THE TABLE.
TRUNCATE TABLE EMPLOYEE;
SELECT * FROM EMPLOYEE;
-- RE-INSERT.
insert into employee values ('1', 'Joe', '70000', '1');
insert into employee values ('2','Henry','80000','2');
insert into employee values ('3','Sam','60000','2');
insert into employee(Id, name, Salary, Department_id)  values ('4','Max','90000','1');
insert into employee(Id, name, Salary, Department_id)  values ('5','Janet','69000','1');
insert into employee(Id, name, Salary, Department_id)  values ('6','Randy','85000','1');


-- QUERY.
DROP VIEW IF EXISTS TEMP;
CREATE VIEW TEMP AS
SELECT DD.Department_name AS Dep, EE.name AS Employee, EE.salary AS Salary FROM employee AS EE  
LEFT JOIN department AS DD 
ON DD.Id = EE.department_id;

-- USE 'UNION ALL' & 'LIMIT'.
(SELECT * FROM TEMP WHERE Dep = 'IT' ORDER BY Salary DESC LIMIT 3) 
UNION ALL 
(SELECT * FROM TEMP WHERE Dep = 'Sales' ORDER BY Salary DESC LIMIT 3);

+-------+----------+--------+
| Dep   | Employee | Salary |
+-------+----------+--------+
| IT    | Max      | 90000  |
| IT    | Randy    | 85000  |
| IT    | Joe      | 70000  |
| Sales | Henry    | 80000  |
| Sales | Sam      | 60000  |
+-------+----------+--------+
5 rows in set (0.00 sec)


```

#### 10. 平面上最近距离 (难度: 困难）

**point_2d**表包含一个平面内一些点（超过两个）的坐标值（x，y）。写一条查询语句求出这些点中的最短距离并保留2位小数。

```sql
-- CREATE TABLE.
CREATE TABLE point_2d (
    -> X FLOAT(4) NOT NULL,
    -> Y FLOAT(4) NOT NULL);
 
INSERT INTO point_2d VALUES(-1,-1);
INSERT INTO point_2d VALUES(0,0);
INSERT INTO point_2d VALUES(-1,-2);

-- QUERY.
SELECT MIN(ROUND(SQRT(POW((P1.X - P2.X),2)+POW((P1.Y - P2.Y),2)),2)) AS SHORTDISTANCE FROM point_2d as P1, point_2d as P2 WHERE P1.X != P2.X OR P1.Y != P2.Y;


+---------------+
| SHORTDISTANCE |
+---------------+
|             1 |
+---------------+
1 row in set (0.00 sec)
```

#### 11. 行程和用户（难度：困难）

Trips 表中存所有出租车的行程信息。每段行程有唯一键 Id，Client_Id 和 Driver_Id 是 Users 表中 Users_Id 的外键。Status 是枚举类型，枚举成员为 (‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’)。

| Id   | Client_Id | Driver_Id | City_Id | Status              | Request_at |
| :--- | :-------- | :-------- | :------ | :------------------ | :--------- |
| 1    | 1         | 10        | 1       | completed           | 2013-10-1  |
| 2    | 2         | 11        | 1       | cancelled_by_driver | 2013-10-1  |
| 3    | 3         | 12        | 6       | completed           | 2013-10-1  |
| 4    | 4         | 13        | 6       | cancelled_by_client | 2013-10-1  |
| 5    | 1         | 10        | 1       | completed           | 2013-10-2  |
| 6    | 2         | 11        | 6       | completed           | 2013-10-2  |
| 7    | 3         | 12        | 6       | completed           | 2013-10-2  |
| 8    | 2         | 12        | 12      | completed           | 2013-10-3  |
| 9    | 3         | 10        | 12      | completed           | 2013-10-3  |
| 10   | 4         | 13        | 12      | cancelled_by_driver | 2013-10-3  |

Users 表存所有用户。每个用户有唯一键 Users_Id。Banned 表示这个用户是否被禁止，Role 则是一个表示（‘client’, ‘driver’, ‘partner’）的枚举类型。

```nohighlight
+----------+--------+--------+
| Users_Id | Banned |  Role  |
+----------+--------+--------+
|    1     |   No   | client |
|    2     |   Yes  | client |
|    3     |   No   | client |
|    4     |   No   | client |
|    10    |   No   | driver |
|    11    |   No   | driver |
|    12    |   No   | driver |
|    13    |   No   | driver |
+----------+--------+--------+
```

写一段 SQL 语句查出**2013年10月1日**至**2013年10月3日**期间非禁止用户的取消率。基于上表，你的 SQL 语句应返回如下结果，取消率（Cancellation Rate）保留两位小数。

```nohighlight
+------------+-------------------+
|     Day    | Cancellation Rate |
+------------+-------------------+
| 2013-10-01 |       0.33        |
| 2013-10-02 |       0.00        |
| 2013-10-03 |       0.50        |
+------------+-------------------+
```

```sql
-- CREATE TABLE.
CREATE TABLE TRIPS (
    -> ID INT(2) PRIMARY KEY NOT NULL AUTO_INCREMENT,
    -> CLIENT_ID INT(2),
    -> DRIVER_ID INT(2),
    -> CITY_ID INT(2),
    -> STATUS VARCHAR(100),
    -> REQUEST_AT DATE);
    
INSERT INTO TRIPS(CLIENT_ID,DRIVER_ID,CITY_ID,STATUS,REQUEST_AT)  VALUES(1,10,1,'completed','2013-10-1');
INSERT INTO TRIPS(CLIENT_ID,DRIVER_ID,CITY_ID,STATUS,REQUEST_AT)  VALUES(2,11,1,'cancelled_by_driver','2013-10-1');
INSERT INTO TRIPS(CLIENT_ID,DRIVER_ID,CITY_ID,STATUS,REQUEST_AT)  VALUES(3,12,6,'completed','2013-10-1');
INSERT INTO TRIPS(CLIENT_ID,DRIVER_ID,CITY_ID,STATUS,REQUEST_AT)  VALUES(4,13,6,'cancelled_by_client','2013-10-1');
INSERT INTO TRIPS(CLIENT_ID,DRIVER_ID,CITY_ID,STATUS,REQUEST_AT)  VALUES(1,10,1,'completed','2013-10-2');
INSERT INTO 
TRIPS(CLIENT_ID,DRIVER_ID,CITY_ID,STATUS,REQUEST_AT)  VALUES(2,11,6,'completed','2013-10-2');
INSERT INTO TRIPS(CLIENT_ID,DRIVER_ID,CITY_ID,STATUS,REQUEST_AT)  VALUES(3,12,6,'completed','2013-10-2');
INSERT INTO TRIPS(CLIENT_ID,DRIVER_ID,CITY_ID,STATUS,REQUEST_AT)  VALUES(2,12,12,'completed','2013-10-3');
INSERT INTO TRIPS(CLIENT_ID,DRIVER_ID,CITY_ID,STATUS,REQUEST_AT)  VALUES(3,10,12,'completed','2013-10-3');
INSERT INTO TRIPS(CLIENT_ID,DRIVER_ID,CITY_ID,STATUS,REQUEST_AT)  VALUES(4,13,12,'cancelled_by_driver','2013-10-3');

-- CREATE TABLE.

CREATE TABLE USERS(
USER_ID INT(2) NOT NULL, 
BANNED VARCHAR(10), 
ROLE VARCHAR(20));

INSERT INTO USERS VALUES(1,'NO','CLIENT');
INSERT INTO USERS VALUES(2,'YES','CLIENT');
INSERT INTO USERS VALUES(3,'NO','CLIENT');
INSERT INTO USERS VALUES(4,'NO','CLIENT');
INSERT INTO USERS VALUES(10,'NO','DRIVER');
INSERT INTO USERS VALUES(11,'NO','DRIVER');
INSERT INTO USERS VALUES(13,'NO','DRIVER');
INSERT INTO USERS VALUES(12,'NO','DRIVER');

-- QUERY.
DROP VIEW IF EXISTS TEMP;
CREATE VIEW TEMP AS 
SELECT T.ID,T.CLIENT_ID,T.DRIVER_ID,T.STATUS,T.REQUEST_AT, U.BANNED AS CLIENT_BAN FROM TRIPS AS T INNER JOIN USERS AS U ON T.CLIENT_ID = U.USER_ID WHERE BANNED = 'NO' AND REQUEST_AT BETWEEN '2013-10-01' AND '2013-10-03';

DROP VIEW IF EXISTS TEMP_;
CREATE VIEW TEMP_ AS 
SELECT T.*, U.BANNED AS DRIVER_BAN FROM TEMP AS T INNER JOIN USERS AS U ON T.DRIVER_ID = U.USER_ID
WHERE BANNED = 'NO' AND REQUEST_AT BETWEEN '2013-10-01' AND '2013-10-03';

SELECT REQUEST_AT, 
SUM(CASE WHEN (STATUS = 'cancelled_by_client' or STATUS = 'cancelled_by_driver') THEN 1 ELSE 0 END) AS CAN_NUM, 
COUNT(STATUS) AS SUM, 
ROUND((SUM(CASE WHEN (STATUS = 'cancelled_by_client' or STATUS = 'cancelled_by_driver') THEN 1 ELSE 0 END) / COUNT(STATUS)),2) AS RATE
FROM TEMP_ GROUP BY REQUEST_AT;

+------------+---------+-----+------+
| REQUEST_AT | CAN_NUM | SUM | RATE |
+------------+---------+-----+------+
| 2013-10-01 |       1 |   3 | 0.33 |
| 2013-10-02 |       0 |   2 | 0.00 |
| 2013-10-03 |       1 |   2 | 0.50 |
+------------+---------+-----+------+
```

