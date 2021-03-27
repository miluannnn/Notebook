# SQL

## 一、tips

比较散

### 1、SQL查询语句中的 limit 与 offset 的区别：

`limit y` :分句表示: 读取 y 条数据
`limit x, y` :分句表示: 跳过 x 条数据，读取 y 条数据
`limit y offset x` :分句表示: 跳过 x 条数据，读取 y 条数据

### 2、 outer join 与 inner join

- **inner join** A 和 B 获得的是A和B的**交集**(intersect),即韦恩图(venn diagram) 相交的部分.
- **outer join** A和B获得的是A和B的**并集**(union), 即韦恩图(venn diagram)的所有部分. outer join 又有left join, right join, full join。

**示例**

假定有两张表，每张表只有一列，列数据如下：

```sql
A    B
-    -
1    3
2    4
3    5
4    6
```

注意(1,2)是A表独有的，(3,4) 两张共有， (5,6)是B独有的。

**Inner join**

使用等号进行inner join以获得两表的交集，即共有的行。

```sql
select * from a INNER JOIN b on a.a = b.b;
select a.*,b.*  from a,b where a.a = b.b;

a | b
--+--
3 | 3
4 | 4
```

**Left outer join**

 left outer join 除了获得B表中符合条件的列外，还将获得A表所有的列。

```sql
select * from a LEFT OUTER JOIN b on a.a = b.b;
select a.*,b.*  from a,b where a.a = b.b(+);

a |  b  
--+-----
1 | null
2 | null
3 |    3
4 |    4
```

**Full outer join**

full outer join 得到A和B的交集,即A和B中所有的行.。如果A中的行在B中没有对应的部分,B的部分将是 null, 反之亦然。

```sql
select * from a FULL OUTER JOIN b on a.a = b.b;

 a   |  b  
-----+-----
   1 | null
   2 | null
   3 |    3
   4 |    4
null |    6
null |    5
```

## 二、sql小题

### 176 第二高的薪水

编写一个 SQL 查询，获取 Employee 表中第二高的薪水（Salary） 。

```
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

例如上述 Employee 表，SQL查询应该返回 200 作为第二高的薪水。如果不存在第二高的薪水，那么查询应返回 null。

```
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```

**题解**

考察内容：

1. ORDER BY，  DESC降序 ASC升序（默认）
2. limit x offset y; 跳过x个选y个
3. 子查询

```sql
select (select DISTINCT 
        Salary 
    FROM 
        Employee ORDER By Salary DESC limit 1 offset 1) 
AS SecondHighestSalary
```

### 175 组合两个表

表1: Person

```
+-------------+---------+
| 列名         | 类型     |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
```

PersonId 是上表主键
表2: Address

```
+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
```

AddressId 是上表主键


编写一个 SQL 查询，满足条件：无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息：

```
FirstName, LastName, City, State
```

**题解**

考察内容：

​	联表查询: 外连接与内连接的区别

