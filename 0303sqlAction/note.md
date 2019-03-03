# MySQL 实战

## 学习内容

数据导入导出 (见附件)

### 将Excel文件导入MySQL表

在navica运行“导入向导”

为源定义一些附加选项。

* 栏位名行：数据表字段所在的行位置；

* 第一个数据行：所导入源数据从第几行开始；

* 最后一个数据行：所导入源数据到第几行结束。

选择目标表，也可以新建一个表。

定义源栏位和目标栏位的对应关系，如果目标栏位设置了主键，在这一步中一定要勾选，否则也将无法正常导入数据。可设置主键

### MySQL导出表到Excel文件

在navica运行“导出向导”，设定输出位置

### 注

连接语句的条件 (SELECT... FROM... INNER JOIN... ON...)

```mysql
SELECT a.runoob_id, a.runoob_author, b.runoob_count FROM runoob_tbl a INNER JOIN tcount_tbl b ON a.runoob_author = b.runoob_author;
```

等价于

```mysql
SELECT a.runoob_id, a.runoob_author, b.runoob_count FROM runoob_tbl a, tcount_tbl b WHERE a.runoob_author = b.runoob_author;
```

我们使用 GROUP BY 语句 将数据表按名字进行分组

```mysql
SELECT column_name, function(column_name)
FROM table_name
WHERE column_name operator value
GROUP BY column_name;
```



## 作业

### 项目七: 各部门工资最高的员工（难度：中等）
创建Employee 表，包含所有员工信息，每个员工有其对应的 Id, salary 和 department Id。
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
+----+-------+--------+--------------+
创建Department 表，包含公司所有部门的信息。
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
编写一个 SQL 查询，找出每个部门工资最高的员工。例如，根据上述给定的表格，Max 在 IT 部门有最高工资，Henry 在 Sales 部门有最高工资。
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| Sales      | Henry    | 80000  |
+------------+----------+--------+

```mysql
CREATE TABLE Employee
(
Id INT UNSIGNED AUTO_INCREMENT, 
Name VARCHAR(100) NOT NULL,
Salary INT UNSIGNED,
DepartmentId INT UNSIGNED,
PRIMARY KEY (Id)
) ;

INSERT INTO employee
(Name, Salary, DepartmentId)
VALUES
('Joe', 70000, 1),
('Henry', 80000, 2),
('Sam', 60000, 2),
('Max', 90000, 1);

CREATE TABLE Department
(
Id INT UNSIGNED AUTO_INCREMENT, 
Name VARCHAR(100),
PRIMARY KEY (Id)
);

--查询各部门工资最高
    SELECT d.Name AS Department, e1.Name AS Employee, e1.Salary
      FROM employee e1 
INNER JOIN employee e2
        ON e1.DepartmentId = e2.DepartmentId
INNER JOIN department d
        ON e1.DepartmentId = d.Id
       AND e1.Salary > e2.Salary
```

### 项目八: 换座位（难度：中等）

小美是一所中学的信息科技老师，她有一张 seat 座位表，平时用来储存学生名字和与他们相对应的座位 id。
其中纵列的 id 是连续递增的
小美想改变相邻俩学生的座位。
你能不能帮她写一个 SQL query 来输出小美想要的结果呢？
请创建如下所示seat表：
示例：
+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Abbot   |
|    2    | Doris   |
|    3    | Emerson |
|    4    | Green   |
|    5    | Jeames  |
+---------+---------+
假如数据输入的是上表，则输出结果如下：
+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Doris   |
|    2    | Abbot   |
|    3    | Green   |
|    4    | Emerson |
|    5    | Jeames  |
+---------+---------+
注意：
如果学生人数是奇数，则不需要改变最后一个同学的座位。

创建表格并插入数据

```mysql
Create table If Not Exists seat(id int, studentvarchar(255));

insert into seat (id, student) values ('1','Abbot');
insert into seat (id, student) values ('2','Doris');
insert into seat (id, student) values ('3','Emerson');
insert into seat (id, student) values ('4','Green');
insert into seat (id, student) values ('5','Jeames')

```

换座位

针对id进行更改交换，最后进行排序

分三类id进行处理，奇数+1，偶数-1，最后一位不变，然后连接

UNION 操作符用于连接两个以上的 SELECT 语句的结果组合到一个结果集合中。多个 SELECT 语句会删除重复的数据

组合成一个新表设定别名进行计算

```mysql
SELECT * FROM 
(
SELECT id-1 as id, student FROM seat WHERE id%2 = 0
UNION
SELECT id+1 as id, student FROM seat WHERE id%2 = 1 AND id != (SELECT COUNT(*) FROM seat)
UNION 
SELECT id as id, student FROM seat WHERE id = (SELECT COUNT(*) FROM seat)
) AS a
ORDER BY a.id
```

```mysql
SELECT (CASE 
WHEN id % 2 != 0 and id != (SELECT COUNT(*) FROM seat) THEN id+1
WHEN id % 2 != 0 and id = (SELECT COUNT(*) FROM seat) THEN id
ELSE id - 1 END) as id,student
FROM seat 
ORDER BY id;
```

### 项目九:  分数排名（难度：中等）

编写一个 SQL 查询来实现分数排名。如果两个分数相同，则两个分数排名（Rank）相同。请注意，平分后的下一个名次应该是下一个连续的整数值。换句话说，名次之间不应该有“间隔”。
创建以下score表：
+----+-------+
| Id | Score |
+----+-------+
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |
+----+-------+
例如，根据上述给定的 Scores 表，你的查询应该返回（按分数从高到低排列）：
+-------+------+
| Score | Rank |
+-------+------+
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |
+-------+------+

```mysql
CREATE TABLE score (
Id INT UNSIGNED AUTO_INCREMENT,
Score FLOAT NOT NULL,
PRIMARY KEY (Id)
)

INSERT INTO score
(Score)
VALUES
(3.50),
(3.65),
(4.00),
(3.85),
(4.00),
(3.65);
```

查询分数排序

```mysql
SELECT Score FROM score ORDER BY Score DESC
```

```mysql
SELECT Score, (SELECT COUNT(DISTINCT Score) FROM score WHERE Score >= S.Score)  
FROM score s ORDER BY Score DESC
```

 (SELECT COUNT(DISTINCT Score) FROM score WHERE Score >= S.Score) 意为在score表中，当其值大于S.Score时，统计还剩多少条参数（当前行分数大于等于同表的分数的count数量），S.Score为导入的分数，统计大于这个分数的数量