# `	SQL` 语法

`SQL`语句按照功能作用主要分为一下几类：

1. `DQL` 数据查询 select
2. `DML` 数据操作 update、delete、insert
3. `DDL` 数据定义 create、alter、drop、truncate
4. `TCL` 事务控制 commit、rollback 事务的提交及回滚
5. `DCL` 数据控制 grant、revoke 数据授权及撤销

# 表的创建与删除--`DDL`

## create table

```
create table if not exists table_name (
	field1 data_type constraint, # 列级约束
	field2 data_type constraint,
	field3 data_type constraint,
	PRIMARY KEY(field1), # 表级约束，多个字段联合
	AUTO_INCREMENT(dield2),# 自增长
	CHECK(field2>5)  # 值检查
);
```

## drop table

```
drop table if exists table_name;
```

## 常用数据类型-datatype

- varchar(最大255)，可变长字符串类型，节省存储空间但效率较低
- char(最大255)，定长字符串 
- int，整型
- bigint
- float，单精度浮点型
- double， 双精度浮点型
- clob， Char Large OBject 字符大对象，超过255个字符的使用clob存储
- blob，Binary Large OBject 二进制大对象，常用于视频、图片等的存储。需要I/0流才能存储

## 建表约束-constraint

1. NOT NULL：非空约数, 该字段所在列不能为空值；
2. UNIQUE：, 唯一性约束，该字段所在列不能含重复值，**但是可以为NULL**；
3. PRIMARY KEY：主键约束，not null和unique的结合，每张表仅有一个主键；
4. FOREIGN KEY： 外键约束,，用于关联另外一个表的主键（一对多的关系中，外键总是定义'多'的那张表。如：student、class表，外键定义在student中，因为一个班级对应多个学生）；
5. CHEK(field >0) 值检查，内部定义值的限定条件，必须满足该条件方可插入；
6. auto_increment 自增长，插入每条数据时，该字段自动+1；
7. default  字段默认值： 为字段设置缺省值

### 联合唯一



## 案例

`create table t_student (
    id int(8) auto_increment , # 自动增长
    no int(8) unique not null, # 字段值不为null
    name varchar(24),
    age int(3),
    date_of_attendance date default '2016-09-01',
    college varchar(255) default 'chaohu university', # 设置字段默认值
    primary key (id),  # 设置id为主键
    CHECK(no>12035000)  # 学号需要大于12035000
);`

```
mysql> create table t_student (
    ->     id int(8) auto_increment ,
    ->     no int(8) unique not null,
    ->     name varchar(24),
    ->     age int(3),
    ->     date_of_attendance date default '2016-09-01',
    ->     college varchar(255) default 'chaohu university',
    ->     primary key (id),
    ->     CHECK(no>12035000)
    -> );
Query OK, 0 rows affected, 3 warnings (0.04 sec)

mysql> desc t_student;
+--------------------+--------------+------+-----+-------------------+----------------+
| Field              | Type         | Null | Key | Default           | Extra          |
+--------------------+--------------+------+-----+-------------------+----------------+
| id                 | int          | NO   | PRI | NULL              | auto_increment |
| no                 | int          | NO   | UNI | NULL              |                |
| name               | varchar(24)  | YES  |     | NULL              |                |
| age                | int          | YES  |     | NULL              |                |
| date_of_attendance | date         | YES  |     | 2016-09-01        |                |
| college            | varchar(255) | YES  |     | chaohu university |                |
+--------------------+--------------+------+-----+-------------------+----------------+
6 rows in set (0.01 sec)
```

# 数据查询-`DQL`

```
select
    field name
from 
	table name
where
	expresion
group by
	field 分组字段
having
	对分组后的字段重新筛选
order by
    field排序字段
```

## `sql`查询语句的执行顺序

1. from 子句

2. where 子句

3. group having 子句

4. select 子句

5. order by 子句

## 案例表

```
mysql> select * from emp;
+-------+--------+-----------+------+------------+---------+---------+--------+
| EMPNO | ENAME  | JOB       | MGR  | HIREDATE   | SAL     | COMM    | DEPTNO |
+-------+--------+-----------+------+------------+---------+---------+--------+
|  7369 | SMITH  | CLERK     | 7902 | 1980-12-17 |  800.00 |    NULL |     20 |
|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 | 1600.00 |  300.00 |     30 |
|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 | 1250.00 |  500.00 |     30 |
|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 | 2975.00 |    NULL |     20 |
|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 | 1250.00 | 1400.00 |     30 |
|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 | 2850.00 |    NULL |     30 |
|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 | 2450.00 |    NULL |     10 |
|  7788 | SCOTT  | ANALYST   | 7566 | 1987-04-19 | 3000.00 |    NULL |     20 |
|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 | 5000.00 |    NULL |     10 |
|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 | 1500.00 |    0.00 |     30 |
|  7876 | ADAMS  | CLERK     | 7788 | 1987-05-23 | 1100.00 |    NULL |     20 |
|  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 |  950.00 |    NULL |     30 |
|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 | 3000.00 |    NULL |     20 |
|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 | 1300.00 |    NULL |     10 |
+-------+--------+-----------+------+------------+---------+---------+--------+
14 rows in set (0.00 sec)

mysql> select * from dept;
+--------+------------+----------+
| DEPTNO | DNAME      | LOC      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
+--------+------------+----------+
4 rows in set (0.00 sec)

mysql> select * from salgrade;
+-------+-------+-------+
| GRADE | LOSAL | HISAL |
+-------+-------+-------+
|     1 |   700 |  1200 |
|     2 |  1201 |  1400 |
|     3 |  1401 |  2000 |
|     4 |  2001 |  3000 |
|     5 |  3001 |  9999 |
+-------+-------+-------+
5 rows in set (0.00 sec)
```



## 分组函数

- max，求最大值
- min，求最小值
- avg，求平均值
- count,   count(\*) 计算表的总条数数，count(field)计算field值不为null的条数
- sum，求和

所有分组函数自动忽略数值中含`NULL`的值

select字段后仅能跟**分组字段**、**分组函数**

分组函数不能直接使用在`WHERE`子句中

**案例**：分组后重新筛选

```
mysql> select job, round(avg(sal),1) as avgsal from EMP group by job having avgsal > 1400;
+-----------+--------+
| job       | avgsal |
+-----------+--------+
| MANAGER   | 2758.3 |
| ANALYST   |   3000 |
| PRESIDENT |   5000 |
+-----------+--------+
3 rows in set (0.00 sec)
```



## 单行处理函数

### substr(field, startIndex, endIndex)

取子字符串

### case...when...then..when...then...else...

### round(field, n)

取约数

### trim

去除字符串两边空格

### str_to_date(str，format) -> date

字符串转换成日期格式

转换规则如下：

- %Y: 年

- %m: 月

- %d: 日

- -%h: 时

- %i: 分

- %s: 秒

### date_format(date, format) -str

日期转换成字符串

## 内连接   inner join

关联查询时，其查询次数满足**笛卡尔积**规则

关联查询：两张表的数据同时匹配

```
select
    ...
from 
	table a..
	join
	table b...
	on 
	关联条件conditon...
where
	筛选条件...
group by
	...
having
	...
order by
    ...
```

### 等值连接

关联查询

**案例：**查询关联的部门位置

```
mysql> select e.ename, e.job,d.loc from emp e join dept d
    -> on e.deptno=d.deptno;
+--------+-----------+----------+
| ename  | job       | loc      |
+--------+-----------+----------+
| SMITH  | CLERK     | DALLAS   |
| ALLEN  | SALESMAN  | CHICAGO  |
| WARD   | SALESMAN  | CHICAGO  |
| JONES  | MANAGER   | DALLAS   |
| MARTIN | SALESMAN  | CHICAGO  |
| BLAKE  | MANAGER   | CHICAGO  |
| CLARK  | MANAGER   | NEW YORK |
| SCOTT  | ANALYST   | DALLAS   |
| KING   | PRESIDENT | NEW YORK |
| TURNER | SALESMAN  | CHICAGO  |
| ADAMS  | CLERK     | DALLAS   |
| JAMES  | CLERK     | CHICAGO  |
| FORD   | ANALYST   | DALLAS   |
| MILLER | CLERK     | NEW YORK |
+--------+-----------+----------+
14 rows in set (0.00 sec)
```

### 非等值连接

```
mysql> select e.ename, e.job, s.grade from emp as e join salgrade as s on e.sal between s.losal and s.hisal;
+--------+-----------+-------+
| ename  | job       | grade |
+--------+-----------+-------+
| SMITH  | CLERK     |     1 |
| ALLEN  | SALESMAN  |     3 |
| WARD   | SALESMAN  |     2 |
| JONES  | MANAGER   |     4 |
| MARTIN | SALESMAN  |     2 |
| BLAKE  | MANAGER   |     4 |
| CLARK  | MANAGER   |     4 |
| SCOTT  | ANALYST   |     4 |
| KING   | PRESIDENT |     5 |
| TURNER | SALESMAN  |     3 |
| ADAMS  | CLERK     |     1 |
| JAMES  | CLERK     |     1 |
| FORD   | ANALYST   |     4 |
| MILLER | CLERK     |     2 |
+--------+-----------+-------+
14 rows in set (0.00 sec)
```

### 自连接

表自身查询

案例：查询每个员工的领导名称

```
mysql> select a.empno, a.ename, a.job, a.mgr, b.ename as 'mgr name' from emp a join emp b
    -> on a.mgr = b.empno;
+-------+--------+----------+------+----------+
| empno | ename  | job      | mgr  | mgr name |
+-------+--------+----------+------+----------+
|  7369 | SMITH  | CLERK    | 7902 | FORD     |
|  7499 | ALLEN  | SALESMAN | 7698 | BLAKE    |
|  7521 | WARD   | SALESMAN | 7698 | BLAKE    |
|  7566 | JONES  | MANAGER  | 7839 | KING     |
|  7654 | MARTIN | SALESMAN | 7698 | BLAKE    |
|  7698 | BLAKE  | MANAGER  | 7839 | KING     |
|  7782 | CLARK  | MANAGER  | 7839 | KING     |
|  7788 | SCOTT  | ANALYST  | 7566 | JONES    |
|  7844 | TURNER | SALESMAN | 7698 | BLAKE    |
|  7876 | ADAMS  | CLERK    | 7788 | SCOTT    |
|  7900 | JAMES  | CLERK    | 7698 | BLAKE    |
|  7902 | FORD   | ANALYST  | 7566 | JONES    |
|  7934 | MILLER | CLERK    | 7782 | CLARK    |
+-------+--------+----------+------+----------+
13 rows in set (0.00 sec)
```

## 外连接 outer join

外连接有主次之分，以其中一个表为主。输出主表中的所有数据，即使右表中无匹配数据。

外连接的查询结果 >= 内连接的查询结果

### 左连接

以左表为主进行查询

```
mysql> select a.ename as ename, ifnull(b.ename, 'BOSS') as 'MGR NAME' from emp as a left join emp b
    -> on a.mgr = b.empno;
+--------+----------+
| ename  | MGR NAME |
+--------+----------+
| SMITH  | FORD     |
| ALLEN  | BLAKE    |
| WARD   | BLAKE    |
| JONES  | KING     |
| MARTIN | BLAKE    |
| BLAKE  | KING     |
| CLARK  | KING     |
| SCOTT  | JONES    |
| KING   | BOSS     |
| TURNER | BLAKE    |
| ADAMS  | SCOTT    |
| JAMES  | BLAKE    |
| FORD   | JONES    |
| MILLER | CLARK    |
+--------+----------+
14 rows in set (0.00 sec)
```

### 右连接

以右表为主进行查询

```
mysql> select a.ename as ename, ifnull(b.ename, 'BOSS') as 'MGR NAME' from  emp as b right join emp a
    -> on a.mgr = b.empno;
+--------+----------+
| ename  | MGR NAME |
+--------+----------+
| SMITH  | FORD     |
| ALLEN  | BLAKE    |
| WARD   | BLAKE    |
| JONES  | KING     |
| MARTIN | BLAKE    |
| BLAKE  | KING     |
| CLARK  | KING     |
| SCOTT  | JONES    |
| KING   | BOSS     |
| TURNER | BLAKE    |
| ADAMS  | SCOTT    |
| JAMES  | BLAKE    |
| FORD   | JONES    |
| MILLER | CLARK    |
+--------+----------+
14 rows in set (0.00 sec)
```

### 综合案例

```
mysql> select e.ename, f.ename,e.sal, d.loc, s.grade
    -> from
    -> emp as e
    -> join dept as d
    -> on e.deptno=d.deptno
    -> join salgrade as s
    -> on e.sal between s.losal and hisal
    -> left join emp as f
    -> on e.mgr = f.empno;
 
+--------+-------+---------+----------+-------+
| ename  | ename | sal     | loc      | grade |
+--------+-------+---------+----------+-------+
| SMITH  | FORD  |  800.00 | DALLAS   |     1 |
| ALLEN  | BLAKE | 1600.00 | CHICAGO  |     3 |
| WARD   | BLAKE | 1250.00 | CHICAGO  |     2 |
| JONES  | KING  | 2975.00 | DALLAS   |     4 |
| MARTIN | BLAKE | 1250.00 | CHICAGO  |     2 |
| BLAKE  | KING  | 2850.00 | CHICAGO  |     4 |
| CLARK  | KING  | 2450.00 | NEW YORK |     4 |
| SCOTT  | JONES | 3000.00 | DALLAS   |     4 |
| KING   | NULL  | 5000.00 | NEW YORK |     5 |
| TURNER | BLAKE | 1500.00 | CHICAGO  |     3 |
| ADAMS  | SCOTT | 1100.00 | DALLAS   |     1 |
| JAMES  | BLAKE |  950.00 | CHICAGO  |     1 |
| FORD   | JONES | 3000.00 | DALLAS   |     4 |
| MILLER | CLARK | 1300.00 | NEW YORK |     2 |
+--------+-------+---------+----------+-------+
```



## 子查询

### from子句中使用select

from子表后的select子句查询结果，可以看作一个独立的子表。

**案例：**查询每个岗位平局工资的薪资等级信息。如下：from中使用t子表

```
mysql> select t.*, s.grade from (select job, round(avg(sal),0) as avgsal from emp group by job) t join salgrade as s
    -> where t.avgsal between s.losal and s.hisal;
+-----------+--------+-------+
| job       | avgsal | grade |
+-----------+--------+-------+
| CLERK     |   1038 |     1 |
| SALESMAN  |   1400 |     2 |
| MANAGER   |   2758 |     4 |
| ANALYST   |   3000 |     4 |
| PRESIDENT |   5000 |     5 |
+-----------+--------+-------+
5 rows in set (0.00 sec)
```

### where子句中使用select

**案例：**查询大于最低工资的员工信息，如下：from中使用t子表

```
mysql> select ename, sal from emp e
    -> where sal > (select min(sal) from emp);
+--------+---------+
| ename  | sal     |
+--------+---------+
| ALLEN  | 1600.00 |
| WARD   | 1250.00 |
| JONES  | 2975.00 |
| MARTIN | 1250.00 |
| BLAKE  | 2850.00 |
| CLARK  | 2450.00 |
| SCOTT  | 3000.00 |
| KING   | 5000.00 |
| TURNER | 1500.00 |
| ADAMS  | 1100.00 |
| JAMES  |  950.00 |
| FORD   | 3000.00 |
| MILLER | 1300.00 |
+--------+---------+
```

### select 中使用select子句

使用子查询可以代替等值连接

`select 子句`中的查询结果不能为多行（必须是一行）

**案例：**查询每个岗位部门名称

```
# 使用select子句 代替等值内连接 相同的效果
mysql> select e.ename, e.deptno, (select d.dname dname from dept d where e.deptno=d.deptno) dname from emp e;
+--------+--------+------------+
| ename  | deptno | dname      |
+--------+--------+------------+
| SMITH  |     20 | RESEARCH   |
| ALLEN  |     30 | SALES      |
| WARD   |     30 | SALES      |
| JONES  |     20 | RESEARCH   |
| MARTIN |     30 | SALES      |
| BLAKE  |     30 | SALES      |
| CLARK  |     10 | ACCOUNTING |
| SCOTT  |     20 | RESEARCH   |
| KING   |     10 | ACCOUNTING |
| TURNER |     30 | SALES      |
| ADAMS  |     20 | RESEARCH   |
| JAMES  |     30 | SALES      |
| FORD   |     20 | RESEARCH   |
| MILLER |     10 | ACCOUNTING |
+--------+--------+------------+
14 rows in set (0.00 sec)

# 内连接
mysql> select e.ename, e.deptno, d.dname from emp e
    -> join dept d
    -> on e.deptno = d.deptno;
+--------+--------+------------+
| ename  | deptno | dname      |
+--------+--------+------------+
| SMITH  |     20 | RESEARCH   |
| ALLEN  |     30 | SALES      |
| WARD   |     30 | SALES      |
| JONES  |     20 | RESEARCH   |
| MARTIN |     30 | SALES      |
| BLAKE  |     30 | SALES      |
| CLARK  |     10 | ACCOUNTING |
| SCOTT  |     20 | RESEARCH   |
| KING   |     10 | ACCOUNTING |
| TURNER |     30 | SALES      |
| ADAMS  |     20 | RESEARCH   |
| JAMES  |     30 | SALES      |
| FORD   |     20 | RESEARCH   |
| MILLER |     10 | ACCOUNTING |
+--------+--------+------------+
14 rows in set (0.00 sec)
```

## LIMIT

限定结果显示条数

`LIMIT startIndex, length` startIndex从开始

表示显示从startIndex行开始，取长度为length行的数据结果显示

分页显示：`LIMIT  (pageNo-1) * pageSize, PpageSize`

# 数据操作-`DML`

## 插入数据

`insert into table_name (field1,field2,filed3...) values `

`(value1, value2, value3,...), (value1, value2,value3 ...);`

```
mysql> insert into t_student (no,name,age) values (12035001,'chenxiang','22');
Query OK, 1 row affected (0.01 sec)

mysql> insert into t_student (no,name,age) values (12035002,'zhengxiucheng','23');
Query OK, 1 row affected (0.00 sec)

mysql> insert into t_student (no,name,age) values (1203502,'zhengxiucheng','23');
ERROR 3819 (HY000): Check constraint 't_student_chk_1' is violated.
mysql> insert into t_student (no,name,age) values (12035077,'yabin','18');
Query OK, 1 row affected (0.00 sec)
```

当学号不满足`CHECK`要求时，插入失败

## 更新数据

`UPDATE table_name SET field=value, field=value WHERE condition`

```
mysql> select * from t_student;
+----+----------+---------------+------+--------------------+-------------------+
| id | no       | name          | age  | date_of_attendance | college           |
+----+----------+---------------+------+--------------------+-------------------+
|  1 | 12035001 | chenxiang     |   22 | 2016-09-01         | chaohu university |
|  2 | 12035002 | zhengxiucheng |   23 | 2016-09-01         | chaohu university |
|  3 | 12035077 | yabin         |   18 | 2016-09-01         | chaohu university |
+----+----------+---------------+------+--------------------+-------------------+
3 rows in set (0.00 sec)

mysql> update t_student set name='yabin', age = '17' where no=12035077;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from t_student;
+----+----------+---------------+------+--------------------+-------------------+
| id | no       | name          | age  | date_of_attendance | college           |
+----+----------+---------------+------+--------------------+-------------------+
|  1 | 12035001 | chenxiang     |   22 | 2016-09-01         | chaohu university |
|  2 | 12035002 | zhengxiucheng |   23 | 2016-09-01         | chaohu university |
|  3 | 12035077 | yabin         |   17 | 2016-09-01         | chaohu university |
+----+----------+---------------+------+--------------------+-------------------+
3 rows in set (0.00 sec)
```

## 删除数据

`DELETE FROM table_name WHERE conditon`

```
mysql> delete from t_student where id=1;
Query OK, 1 row affected (0.00 sec)

mysql> select * from t_student;
+----+----------+---------------+------+--------------------+-------------------+
| id | no       | name          | age  | date_of_attendance | college           |
+----+----------+---------------+------+--------------------+-------------------+
|  2 | 12035002 | zhengxiucheng |   23 | 2016-09-01         | chaohu university |
|  3 | 12035077 | yabin         |   17 | 2016-09-01         | chaohu university |
+----+----------+---------------+------+--------------------+-------------------+
2 rows in set (0.00 sec)
```

