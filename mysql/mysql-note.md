# mysql note

[一天学会MySQL数据库](https://www.bilibili.com/video/BV1Vt411z7wy/)视频课笔记

---

mysql - 关系型数据库

# 1 基础操作

## 登录退出数据库服务器

```sql
-- 登陆mysql服务器
mysql -u root -p
-- 退出服务器
exit;
```

## 基本操作

```sql
--查询数据库服务器中所有数据库
SHOW DATABASES;

-- 对某一数据库进行操作
USE databaseName

-- 查看数据库中所有表
SHOW TABLES;

-- 查询表中所有数据
SELECT * FROM tableName;

-- 创建新数据库
CREATE DATABASE databaseName;

-- 创建数据表
CREATE TABLE pet(
	name VARCHAR(20),
	owner VARCHAR(20),
	species VARCHAR(20),
	sex CHAR(1),
	birth DATE,
	death DATE
);

-- 查看数据表结构
DESCRIBE tableName;
DESC tableName;
说明:
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
 Field : 字段的名称
 Type : 字段的类型
 Null : 是否可以为空 
 Key : 是否是关键字 如可以定义为:  primary key 或者 unique key   ...
 Default : 若是该字段没有主动设置值的时候,该字段的默认值

-- 向表中插入记录
INSERT INTO pet 
VALUES ('puffball', 'Diane', 'hamster', 'f', '1990-03-30', NULL);

-- 修改数据
UPDATE pet SET name = 'squirrel' where owner = 'Diane';

-- 删除数据
DELETE FROM pet where name = 'squirrel';

-- 删除表
DROP TABLE myorder;
```

- 虽然关键词用大小写都可以，但最好用大写来区分
- 定义最后一个字段时不要加 ','，语句结束时需要加';'
- NULL代表的是空,表示该字段还没有数据.千万不要主动填写'NULL',这代表你的字段有一个值叫做'null'.
- VARCHAR( ), CHAR( )区别
    1. char和varchar最大的不同就是一个是固定长度,一个是可变长度.

    char(M)类型的数据列里，每个值都占用M个字节，如果某个长度小于M，MySQL就会在它的右边用空格字符补足。（在检索操作中那些填补出来的空格字符将被去掉）

    由于varchar是可变长度,因此存储的是实际字符串再加上一个记录字符串长度的字节。（L+1)

    如果分配给char或varchar列的值超过列的最大长度,则对值进行裁剪.

    2. varchar(M)和char(M),M都表示字符数.

    varchar的最大长度为65535个字节,不同的编码所对应的最大可存储的字符数不同.

    char最多可以存放255个字符,不同的编码最大可用字节数不同.

## 常用数据类型

mysql支持多种数据类型， 大致可分为三类：

- 数值

![img](https://github.com/janneys/DatabaseNotes/blob/master/mysql/img/Untitled.png)

- 日期/时间

![img](https://github.com/janneys/DatabaseNotes/blob/master/mysql/img/Untitled%201.png)

- 字符串（字符）

![img](https://github.com/janneys/DatabaseNotes/blob/master/mysql/img/Untitled%202.png)

- 日期选择按照格式， 数值和字符串选择按照大小

# 2 约束

## 主键约束 primary key

唯一确定一张表中的一条记录

通过给某个字段添加约束， 就可以使该字段不重复且不为空

```sql
-- 使某个字段不重复且不得为空，确保表内所有数据的唯一性。
CREATE TABLE usertable (
    id INT PRIMARY KEY,
    name VARCHAR(20)
);

-- 添加主键约束
-- 如果忘记设置主键，还可以通过SQL语句设置（两种方式）：
ALTER TABLE usertable ADD PRIMARY KEY(id);
ALTER TABLE usertable MODIFY id INT PRIMARY KEY;

-- 删除主键
ALTER TABLE usertable drop PRIMARY KEY;
```

### 联合主键

由多个字段组成的主键，所有字段都不能为空，字段中至少有一个不相同

```sql
-- 联合主键中的每个字段都不能为空，并且加起来不能和已设置的联合主键重复。
CREATE TABLE user (
    id INT,
    name VARCHAR(20),
    password VARCHAR(20),
    PRIMARY KEY(id, name)
);
```

## 自增约束 auto_increment

```sql
-- 自增约束的主键字段值由系统自动递增分配。
CREATE TABLE user (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(20)
);
```

## 唯一约束 unique

约束修饰的字段的值不可以重复, 可以为空

```sql
-- 建表时创建唯一约束
CREATE TABLE user (
    id INT,
    name VARCHAR(20),
    UNIQUE(name)
);

-- 添加唯一约束
-- 如果建表时没有设置唯一建，还可以通过SQL语句设置（两种方式）：
ALTER TABLE user ADD UNIQUE(name);
ALTER TABLE user MODIFY name VARCHAR(20) UNIQUE;

-- 删除唯一约束
ALTER TABLE user DROP INDEX name;
```

主键约束(primary key)中包含了唯一约束

场景:业务需求:设计一张用户注册表,用户姓名必须要用手机号来注册,而且手机号和用户名称都不能为空,那么:

```sql
CREATE TABLE user_test(
    id INT PRIMARY KEY AUTO_INCREMENT COMMENT'主键id',
    name VARCHAR(20) NOT NULL COMMENT'用户姓名,不能为空',
    phone_number VARCHAR(20) UNIQUE NOT NULL COMMENT'用户手机,不能重复且不能为空'
);

-- 运行 DESCRIBE user_test;
+--------------+-------------+------+-----+---------+----------------+
| Field        | Type        | Null | Key | Default | Extra          |
+--------------+-------------+------+-----+---------+----------------+
| id           | int(11)     | NO   | PRI | NULL    | auto_increment |
| name         | varchar(20) | NO   |     | NULL    |                |
| phone_number | int(11)     | NO   | UNI | NULL    |                |
+--------------+-------------+------+-----+---------+----------------+
-- 这样的话就达到了每一个手机号都只能出现一次,达到了每个手机号只能被注册一次.
-- 用户姓名可以重复,但是手机号码却不能重复,复合正常的逻辑需求
```

## 非空约束 not_null

修饰的字段不能为空 NULL

```sql
CREATE TABLE user (
    id INT,
    name VARCHAR(20) NOT NULL
);
```

## 默认约束 default

插入字段值时如果没有传值，就会使用默认值

```sql
-- 约束某个字段的默认值
CREATE TABLE user2 (
    id INT,
    name VARCHAR(20),
    age INT DEFAULT 10
);
```

业务需求:找正常的用户,对这些正常用户进行发放优惠卷或者积分之类的东西,而被禁封的用户我们不让其参加多动.我们想要封用户只要将status的值从0改为1就行了,当然我们取用户的时候必须要先判断status是否是0.若是1.说明该用户已经被禁封.

```sql
-- 先封手机号为'1234'的用户:
UPDATE user6 SET status = 1 WHERE phone_number= '1234';
SELECT * FROM user6;
+----+------+--------------+--------+
| id | name | phone_number | status |
+----+------+--------------+--------+
|  1 | aa   | 123          |      0 |
|  2 | bb   | 1234         |      1 |
|  3 | cc   | 1263456      |      0 |
+----+------+--------------+--------+
status为1,说明用户已经被封,该用户不可以参加活动

-- 我们取用户的时候加上status的判断,如:
SELECT * FROM user6 WHERE status = 0;
+----+------+--------------+--------+
| id | name | phone_number | status |
+----+------+--------------+--------+
|  1 | aa   | 123          |      0 |
|  3 | cc   | 1263456      |      0 |
+----+------+--------------+--------+
```

## 外键约束 foreign key

涉及两个表： 父表、子表 （主表、副表）

```sql
-- 班级
CREATE TABLE classes (
    id INT PRIMARY KEY,
    name VARCHAR(20)
);

-- 学生表
CREATE TABLE students (
    id INT PRIMARY KEY,
    name VARCHAR(20),
    -- 这里的 class_id 要和 classes 中的 id 字段相关联
    class_id INT,
    -- 表示 class_id 的值必须来自于 classes 中的 id 字段值
    FOREIGN KEY(class_id) REFERENCES classes(id)
);

-- 1. 主表（父表）classes 中没有的数据值，在副表（子表）students 中，是不可以使用的；
-- 2. 主表中的记录被副表引用时，主表不可以被删除。
```

- 主表中没有的数据,在附表中,是不可以使用的
- 主表中记录的数据现在正在被附表所引用,那么主表中正在被引用的数据不可以被删除
- 若要想删除,先将附表中的数据删除在删除主表数据
- 对于外键约束大家可以联想 省,市 来进行联想 (市必须要依赖于省,只要省还有一个市在引用,那么就不可以删除省,要不然市就没有省了. 那么我们想删除省,必须要将该省下所有的市全部删除之后,才可以删除这个省)

# 3 范式

## 第一范式 1NF

数据表中的所有字段都是不可分割的原子值

只要字段值还可以继续拆分，就不满足第一范式。

eg: 地址 → 省，市，区...

范式设计得越详细，对某些实际操作可能会更好，但并非都有好处，需要对项目的实际情况进行设定。

## 第二范式 2NF

必须满足第一范式的前提下，第二范式要求，除主键外的每一列都必须完全依赖于主键。

如果出现不完全依赖，只可能发送在联合主键的情况下。

```sql
create table myorser(
	product_id int,#产品号
	customer_id int,#用户号
	product_name varchar(20),
	customer_name varchar(20),
	primary key(product_id,customer_id)#产品号和用户号形成联合主键
);
-- 产品的名字只和产品号有关、用户的名字只和用户号有关
-- 除主键外的其他列，只依赖于主键的部分字段
-- 不完全依赖于主键，不满足第二范式

-- 拆表
CREATE TABLE myorder (
    order_id INT PRIMARY KEY,
    product_id INT,
    customer_id INT
);

CREATE TABLE product (
    id INT PRIMARY KEY,
    name VARCHAR(20)
);

CREATE TABLE customer (
    id INT PRIMARY KEY,
    name VARCHAR(20)
);
-- myorder 表中的 product_id 和 customer_id 完全依赖于 order_id 主键，而 product 和 customer 表中的其他字段又完全依赖于主键。满足了第二范式的设计
```

## 第三范式 3NF

在满足第二范式的前提下，除了主键列之外，其他列之间不能有传递依赖关系。

传递依赖 → 冗余

```sql
CREATE TABLE myorder (
    order_id INT PRIMARY KEY,
    product_id INT,
    customer_id INT,
    customer_phone VARCHAR(15)
);
-- 表中的 customer_phone 有可能依赖于 order_id 、 customer_id 两列

-- 拆表
CREATE TABLE myorder (
    order_id INT PRIMARY KEY,
    product_id INT,
    customer_id INT
);

CREATE TABLE customer (
    id INT PRIMARY KEY,
    name VARCHAR(20),
    phone VARCHAR(15)
);
--修改后就不存在其他列之间的传递依赖关系，其他列都只依赖于主键列，满足了第三范式的设计
```

# 4 查询练习

## 数据准备

```sql
-- 创建数据库
CREATE DATABASE select_test;
-- 切换数据库
USE select_test;

-- 创建学生表
CREATE TABLE student (
    no VARCHAR(20) PRIMARY KEY,
    name VARCHAR(20) NOT NULL,
    sex VARCHAR(10) NOT NULL,
    birthday DATE, -- 生日
    class VARCHAR(20) -- 所在班级
);

-- 创建教师表
CREATE TABLE teacher (
    no VARCHAR(20) PRIMARY KEY,
    name VARCHAR(20) NOT NULL,
    sex VARCHAR(10) NOT NULL,
    birthday DATE,
    profession VARCHAR(20) NOT NULL, -- 职称
    department VARCHAR(20) NOT NULL -- 部门
);

-- 创建课程表
CREATE TABLE course (
    no VARCHAR(20) PRIMARY KEY,
    name VARCHAR(20) NOT NULL,
    tno VARCHAR(20) NOT NULL, -- 教师编号
    -- 表示该 tno 来自于 teacher 表中的 no 字段值
    FOREIGN KEY(t_no) REFERENCES teacher(no) 
);

-- 成绩表
CREATE TABLE score (
    sno VARCHAR(20) NOT NULL, -- 学生编号
    cno VARCHAR(20) NOT NULL, -- 课程号
    degree DECIMAL,	-- 成绩
    -- 表示该 sno, cno 分别来自于 student, course 表中的 no 字段值
    FOREIGN KEY(sno) REFERENCES student(no),	
    FOREIGN KEY(cno) REFERENCES course(no),
    -- 设置 sno, cno 为联合主键
    PRIMARY KEY(sno, cno)
);

-- 查看所有表
SHOW TABLES;

-- 添加学生表数据
INSERT INTO student VALUES('101', '曾华', '男', '1977-09-01', '95033');
INSERT INTO student VALUES('102', '匡明', '男', '1975-10-02', '95031');
INSERT INTO student VALUES('103', '王丽', '女', '1976-01-23', '95033');
INSERT INTO student VALUES('104', '李军', '男', '1976-02-20', '95033');
INSERT INTO student VALUES('105', '王芳', '女', '1975-02-10', '95031');
INSERT INTO student VALUES('106', '陆军', '男', '1974-06-03', '95031');
INSERT INTO student VALUES('107', '王尼玛', '男', '1976-02-20', '95033');
INSERT INTO student VALUES('108', '张全蛋', '男', '1975-02-10', '95031');
INSERT INTO student VALUES('109', '赵铁柱', '男', '1974-06-03', '95031');

-- 添加教师表数据
INSERT INTO teacher VALUES('804', '李诚', '男', '1958-12-02', '副教授', '计算机系');
INSERT INTO teacher VALUES('856', '张旭', '男', '1969-03-12', '讲师', '电子工程系');
INSERT INTO teacher VALUES('825', '王萍', '女', '1972-05-05', '助教', '计算机系');
INSERT INTO teacher VALUES('831', '刘冰', '女', '1977-08-14', '助教', '电子工程系');

-- 添加课程表数据
INSERT INTO course VALUES('3-105', '计算机导论', '825');
INSERT INTO course VALUES('3-245', '操作系统', '804');
INSERT INTO course VALUES('6-166', '数字电路', '856');
INSERT INTO course VALUES('9-888', '高等数学', '831');

-- 添加添加成绩表数据
INSERT INTO score VALUES('103', '3-105', '92');
INSERT INTO score VALUES('103', '3-245', '86');
INSERT INTO score VALUES('103', '6-166', '85');
INSERT INTO score VALUES('105', '3-105', '88');
INSERT INTO score VALUES('105', '3-245', '75');
INSERT INTO score VALUES('105', '6-166', '79');
INSERT INTO score VALUES('109', '3-105', '76');
INSERT INTO score VALUES('109', '3-245', '68');
INSERT INTO score VALUES('109', '6-166', '81');

-- 查看表结构
SELECT * FROM course;
SELECT * FROM score;
SELECT * FROM student;
SELECT * FROM teacher;
```

## 基础

```sql
-- 查询 student 表的所有行
SELECT * FROM student;

-- 查询 student 表中的 name、sex 和 class 字段的所有行
SELECT name, sex, class FROM student;

-- 查询95033和95031班全体学生的记录
SELECT * FROM student WHERE class in ('95033','95031')

-- 查询 teacher 表中不重复的 department 列
-- distinct: 去重查询
SELECT DISTINCT department FROM teacher;

-- 查询 score 表中成绩在60-80之间的所有行（区间查询和运算符查询）
-- BETWEEN xx AND xx: 查询区间, AND 表示 "并且"
SELECT * FROM score WHERE degree BETWEEN 60 AND 80;
SELECT * FROM score WHERE degree > 60 AND degree < 80;

-- 查询 score 表中成绩为 85, 86 或 88 的行
-- IN: 查询规定中的多个值
SELECT * FROM score WHERE degree IN (85, 86, 88);

-- 查询 student 表中 '95031' 班或性别为 '女' 的所有行
-- or: 表示或者关系
SELECT * FROM student WHERE class = '95031' or sex = '女';

-- 以 class 降序的方式查询 student 表的所有行
-- DESC: 降序，从高到低
-- ASC（默认）: 升序，从低到高
SELECT * FROM student ORDER BY class DESC;
SELECT * FROM student ORDER BY class ASC;

-- 以 c_no 升序、degree 降序查询 score 表的所有行
SELECT * FROM score ORDER BY cno ASC, degree DESC;

-- 查询 "95031" 班的学生人数
-- COUNT: 统计
SELECT COUNT(*) FROM student WHERE class = '95031';

-- 查询 score 表中的最高分的学生学号和课程编号（子查询或排序查询）。
-- (SELECT MAX(degree) FROM score): 子查询，算出最高分
SELECT sno, cno FROM score WHERE degree = (SELECT MAX(degree) FROM score);

--  排序查询
-- LIMIT r, n: 表示从第r行开始，查询n条数据
SELECT sno, cno, degree FROM score ORDER BY degree DESC LIMIT 0, 1

-- 多段排序
-- 以 class 和 birthday 从大到小的顺序查询 student 表
SELECT * FROM student ORDER BY class DESC, birthday;
```

## 分组

```sql
-- 查询每门课的平均成绩
SELECT cno, AVG(degree) FROM score GROUP BY cno;

-- 查询score表中至少有两名学生选修并以3开头的课程平均成绩
-- 分组条件与模糊查询
-- 1 查询每门课的平均成绩
SELECT cno, AVG(degree) FROM score GROUP BY cno;
-- 2 至少有2名学生选修
HAVING COUNT(cno) >= 2
-- 3 课程号3开头
-- LIKE 表示模糊查询，"%" 是一个通配符，匹配 "3" 后面的任意字符。
AND cno LIKE '3%'
-- 整理一下
SELECT cno, AVG(degree), COUNT(*) 
FROM score 
GROUP BY cno
HAVING COUNT(cno) >= 2 AND c_no LIKE '3%';
+-------+-------------+----------+
| cno  | AVG(degree) | COUNT(*) |
+-------+-------------+----------+
| 3-105 |     85.3333 |        3 |
| 3-245 |     76.3333 |        3 |
+-------+-------------+----------+
```

## 多表

```sql
-- 查询所有学生的name，以及该学生在score表中对应的cno和degree
SELECT name, cno, degree
FROM student, score
WHERE student.no = score.sno

-- 查询所有学生的no、课程名称(course表中的name)和成绩(score表中的degree)列
SELECT sno, name as cname, degree
FROM score, course
WHERE score.cno = course.no

-- 查询所有学生的name、课程名称(course表中的name)和成绩(score表中的degree)列
SELECT student.name as sname, course.name as cname, degree
FROM student, course, score
where student.no = score.sno AND course.no = score.cno

-- 查询某选修课程多于2个同学的教师姓名
-- 1 得到选课人数大于2的课程号
-- 2 根据课程号查询教师编号
-- 3 根据教师编号查询教师姓名
SELECT name
FROM teacher
WHERE no IN (
	SELECT tno FROM course WHERE no IN(
		SELECT cno FROM scores GROUP BY cno HAVING count(*)>2
	)
);
```

## 子查询

```sql
-- 查询95031班学生每门课程的平均成绩
-- 1 95031班学生信息
SELECT no FROM student WHERE class='95031';
-- 2 95031班学生成绩
SELECT sno, cno, degree
FROM score
WHERE sno IN (SELECT no FROM student WHERE class='95031');
-- 3 计算平均值并分组
SELECT cno, AVG(degree)
FROM score
WHERE sno IN (SELECT no FROM student WHERE class='95031')
GROUP BY cno;
+-------+-------------+
| cno  | AVG(degree) |
+-------+-------------+
| 3-105 |     82.0000 |
| 3-245 |     71.5000 |
| 6-166 |     80.0000 |
+-------+-------------+

-- 查询在 3-105 课程中，所有成绩高于 102 号同学的记录
SELECT * 
FROM score
WHERE cno = '3-105'
AND degree > (SELECT degree FROM score where cno='3-105' AND sno='102')

-- 查询所有成绩高于 102 号同学的 3-105 课程成绩记录
SELECT *
FROM score
WHERE degree > (SELECT degree FROM score where cno='3-105' AND sno='102')
-- 最终结果不再限制课程号

-- 查询 '张旭' 教师任课的学生成绩表
-- 1 找到教师编号
SELECT no FROM teacher WHERE name = '张旭';
-- 2 找到该教师任课的课程号
SELECT no FROM course WHERE tno IN (SELECT no FROM teacher WHERE name = '张旭');
-- 3 得到学生成绩表
SELECT * FROM score
WHERE cno IN (SELECT no FROM course 
	WHERE tno IN (SELECT no FROM teacher WHERE name = '张旭')
);
-- 用IN比=好， 因为可能对应多个结果

-- 查询 “计算机系” 课程的成绩表
-- 1 查询“计算机系”任课教师编号
-- 2 根据教师编号查询课程号
-- 3 根据课程号查询成绩表
SELECT * FROM score WHERE cno IN (
    SELECT no FROM course WHERE tno IN (
        SELECT no FROM teacher WHERE department = '计算机系'
    )
);

-- 查询某课程成绩比该课程平均成绩低的 score 表
-- 将表 b 作用于表 a 中查询数据, 相同表的声明
SELECT * FROM score a WHERE degree < (
    (SELECT AVG(degree) FROM score b WHERE a.cno = b.cno)
);

-- 查询所有任课(在course表里有课程)教师的name和department
SELECT name, department
FROM teacher
WHERE no IN (
	SELECT tno FROM course
);

-- 查询student表中至少有2名男生的class
SELECT class 
FROM student
WHERE sex = '男'
GROUP BY class
HAVING COUNT(*)>=2;

-- 查询 "男" 教师及其所上的课程
SELECT * FROM course
WHERE tno IN (SELECT no FROM teacher WHERE sex = '男');

-- 查询最高分同学的 score 表
-- 找出最高成绩（该查询只能有一个结果）
SELECT MAX(degree) FROM score;
-- 根据上面的条件筛选出所有最高成绩表，
-- 该查询可能有多个结果，假设 degree 值多次符合条件。
SELECT * FROM score WHERE degree = (SELECT MAX(degree) FROM score);

-- 查询和 "李军" 同性别且同班的同学 name
SELECT name, sex, class 
FROM student 
WHERE sex = (
    SELECT sex FROM student WHERE name = '李军'
) AND class = (
    SELECT class FROM student WHERE name = '李军'
);
```

## 函数与关键字

```sql
-- YEAR 函数与带 IN 关键字查询
-- 查询所有和 101 、108号学生同年出生的no 、name 、birthday列
SELECT no, name, birthday 
FROM student
WHERE YEAR(birthday) IN (
	SELECT YEAR(birthday) FROM student WHERE no IN (101, 108)
);

-- UNION 和 NOT IN 的使用
-- 查询 计算机系 与 电子工程系 中的不同职称的教师
-- NOT: 代表逻辑非
SELECT * FROM teacher WHERE department = '计算机系' AND profession NOT IN (
    SELECT profession FROM teacher WHERE department = '电子工程系'
)
-- 合并两个集
UNION
SELECT * FROM teacher WHERE department = '电子工程系' AND profession NOT IN (
    SELECT profession FROM teacher WHERE department = '计算机系'
);

--  查询课程 3-105 且成绩 至少 高于 3-245 的 score 表
-- ANY: 符合SQL语句中的任意条件。
-- 在 3-105 成绩中，只要有一个大于从 3-245 筛选出来的任意行就符合条件，
-- 最后根据降序查询结果。
SELECT * FROM score
WHERE cno = '3-105' AND degree > ANY(
    SELECT degree FROM score WHERE cno = '3-245'
) ORDER BY degree DESC;

-- 查询课程 3-105 且成绩高于 3-245 的 score 表
-- ALL: 符合SQL语句中的所有条件。
-- 在 3-105 每一行成绩中，都要大于从 3-245 筛选出来全部行才算符合条件。
SELECT * FROM score 
WHERE cno = '3-105' AND degree > ALL(
    SELECT degree FROM score WHERE cno = '3-245'
);

-- 查询 student 表中不姓 "王" 的同学记录
-- NOT LIKE 模糊查询取反
SELECT * FROM student WHERE name NOT LIKE '王%';

-- 查询 student 表中每个学生的姓名和年龄
-- 使用函数 YEAR(NOW()) 计算出当前年份，减去出生年份后得出年龄。
SELECT name, YEAR(NOW()) - YEAR(birthday) as age FROM student;

-- 查询 student 表中最大和最小的 birthday 值
-- MIN MAX
SELECT MAX(birthday), MIN(birthday) FROM student;

-- 建立一个 grade 表代表学生的成绩等级. 按等级查询
CREATE TABLE grade (
    low INT(3),
    upp INT(3),
    grade char(1)
);

INSERT INTO grade VALUES (90, 100, 'A');
INSERT INTO grade VALUES (80, 89, 'B');
INSERT INTO grade VALUES (70, 79, 'C');
INSERT INTO grade VALUES (60, 69, 'D');
INSERT INTO grade VALUES (0, 59, 'E');

SELECT * FROM grade;
+------+------+-------+
| low  | upp  | grade |
+------+------+-------+
|   90 |  100 | A     |
|   80 |   89 | B     |
|   70 |   79 | C     |
|   60 |   69 | D     |
|    0 |   59 | E     |
+------+------+-------+

SELECT s_no, c_no, grade FROM score, grade 
WHERE degree BETWEEN low AND upp;
+------+-------+-------+
| s_no | c_no  | grade |
+------+-------+-------+
| 101  | 3-105 | A     |
| 102  | 3-105 | A     |
| 103  | 3-105 | A     |
| 103  | 3-245 | B     |
| 103  | 6-166 | B     |
| 104  | 3-105 | B     |
| 105  | 3-105 | B     |
| 105  | 3-245 | C     |
| 105  | 6-166 | C     |
| 109  | 3-105 | C     |
| 109  | 3-245 | D     |
| 109  | 6-166 | B     |
+------+-------+-------+
```

## 连接

```sql
-- 数据准备
CREATE DATABASE testJoin;

CREATE TABLE person (
    id INT,
    name VARCHAR(20),
    cardId INT
);

CREATE TABLE card (
    id INT,
    name VARCHAR(20)
);

INSERT INTO card VALUES (1, '饭卡'), (2, '建行卡'), (3, '农行卡'), (4, '工商卡'), (5, '邮政卡');
SELECT * FROM card;
+------+-----------+
| id   | name      |
+------+-----------+
|    1 | 饭卡      |
|    2 | 建行卡    |
|    3 | 农行卡    |
|    4 | 工商卡    |
|    5 | 邮政卡    |
+------+-----------+

INSERT INTO person VALUES (1, '张三', 1), (2, '李四', 3), (3, '王五', 6);
SELECT * FROM person;
+------+--------+--------+
| id   | name   | cardId |
+------+--------+--------+
|    1 | 张三   |      1 |
|    2 | 李四   |      3 |
|    3 | 王五   |      6 |
+------+--------+--------+

-- 内连接 inner join  AB交集
SELECT * FROM person INNER JOIN card on person.cardId = card.id;
+------+--------+--------+------+-----------+
| id   | name   | cardId | id   | name      |
+------+--------+--------+------+-----------+
|    1 | 张三   |      1 |    1 | 饭卡      |
|    2 | 李四   |      3 |    3 | 农行卡    |
+------+--------+--------+------+-----------+
-- inner 可省略

-- 左(外)连接 left join   A + AB交集
SELECT * FROM person LEFT JOIN card on person.cardId = card.id;
+------+--------+--------+------+-----------+
| id   | name   | cardId | id   | name      |
+------+--------+--------+------+-----------+
|    1 | 张三   |      1 |    1 | 饭卡      |
|    2 | 李四   |      3 |    3 | 农行卡    |
|    3 | 王五   |      6 | NULL | NULL      |
+------+--------+--------+------+-----------+
-- 无匹配时填充NULL

-- 右(外)连接 right join   AB交集 + B
SELECT * FROM person RIGHT JOIN card on person.cardId = card.id;
+------+--------+--------+------+-----------+
| id   | name   | cardId | id   | name      |
+------+--------+--------+------+-----------+
|    1 | 张三   |      1 |    1 | 饭卡      |
|    2 | 李四   |      3 |    3 | 农行卡    |
| NULL | NULL   |   NULL |    2 | 建行卡    |
| NULL | NULL   |   NULL |    4 | 工商卡    |
| NULL | NULL   |   NULL |    5 | 邮政卡    |
+------+--------+--------+------+-----------+

-- 全连接 full(out) join = union  AB并集
SELECT * FROM person LEFT JOIN card on person.cardId = card.id
UNION
SELECT * FROM person RIGHT JOIN card on person.cardId = card.id;
+------+--------+--------+------+-----------+
| id   | name   | cardId | id   | name      |
+------+--------+--------+------+-----------+
|    1 | 张三   |      1 |    1 | 饭卡      |
|    2 | 李四   |      3 |    3 | 农行卡    |
|    3 | 王五   |      6 | NULL | NULL      |
| NULL | NULL   |   NULL |    2 | 建行卡    |
| NULL | NULL   |   NULL |    4 | 工商卡    |
| NULL | NULL   |   NULL |    5 | 邮政卡    |
+------+--------+--------+------+-----------+

-- MySQL不支持 full join 语法的全外连接
-- SELECT * FROM person FULL JOIN card on person.cardId = card.id;
-- 出现错误：
-- ERROR 1054 (42S22): Unknown column 'person.cardId' in 'on clause'
```

# 5 事务

## 概念

在 MySQL 中，事务其实是一个最小的不可分割的工作单元。事务能够保证一个业务的完整性。

比如银行转账：

```sql
-- user a transfer 100: 
update user set money = money-100 name = 'a';
-- user b get 100:
update user set money = money+100 name = 'b';
```

如果只有一条语句执行成功了， 而另一条没有执行成功 → 数据前后不一致

因此，在执行多条有关联 SQL 语句时，事务可能会要求这些 SQL 语句要么同时执行成功，要么就都执行失败。

## 事务控制

### commit_rollback

在 MySQL 中，事务的自动提交状态默认是开启的。

可以看到，在执行插入语句后数据立刻生效，原因是 MySQL 中的事务自动将它提交到了数据库中。

事务回滚的意思就是，撤销执行过的所有 SQL 语句效果，使其回滚到最后一次提交数据时的状态。

```sql
SELECT @@autocommit
+--------------+
| @@autocommit |
+--------------+
|            1 |
+--------------+
```

默认事务开启的作用：当我们执行一个sql语句的时候，效果会立即体现出来，且不能回滚。

```sql
CREATE DATABASE bank;

USE bank;

CREATE TABLE user (
    id INT PRIMARY KEY,
    name VARCHAR(20),
    money INT
);

INSERT INTO user VALUES (1, 'a', 1000);

SELECT * FROM user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
+----+------+-------+

-- 回滚到最后一次提交
ROLLBACK;

-- rollback并没有生效
SELECT * FROM user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
+----+------+-------+

-- 设置mysql自动提交为false
set autocommit = 0;

-- 查询自动提交状态
SELECT @@AUTOCOMMIT;
+--------------+
| @@AUTOCOMMIT |
+--------------+
|            0 |
+--------------+

INSERT INTO user VALUES (2, 'b', 1000);

-- 关闭 AUTOCOMMIT 后，数据的变化是在一张虚拟的临时数据表中展示，
-- 发生变化的数据并没有真正插入到数据表中。
SELECT * FROM user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
|  2 | b    |  1000 |
+----+------+-------+

-- 数据表中的真实数据其实还是：
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
+----+------+-------+

-- 由于数据还没有真正提交，可以使用回滚
ROLLBACK;

-- 再次查询
SELECT * FROM user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
+----+------+-------+

-- 如何将虚拟的数据真正提交到数据库中？使用 COMMIT :

INSERT INTO user VALUES (2, 'b', 1000);
-- 手动提交数据（持久性），
-- 将数据真正提交到数据库中，执行后不能再回滚提交过的数据。
COMMIT;

-- 提交后测试回滚
ROLLBACK;

-- 再次查询（回滚无效了）
SELECT * FROM user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
|  2 | b    |  1000 |
+----+------+-------+
```

- 事务的持久性
1. **自动提交**
    - 查看自动提交状态：`SELECT @@AUTOCOMMIT` ；
    - 设置自动提交状态：`SET AUTOCOMMIT = 1` 。
2. **手动提交**

    `@@AUTOCOMMIT = 0` 时，使用 `COMMIT` 命令提交事务。

3. **事务回滚**

    `@@AUTOCOMMIT = 0` 时，使用 `ROLLBACK` 命令回滚事务。

### 手动开启事务

begin/start_transaction

事务的默认提交被开启 ( @@AUTOCOMMIT = 1 ) 后，此时就不能使用事务回滚了。但是我们还可以手动开启一个事务处理事件，使其可以发生回滚：

```sql
-- 使用 BEGIN 或者 START TRANSACTION 手动开启一个事务
-- START TRANSACTION;
BEGIN;
UPDATE user set money = money - 100 WHERE name = 'a';
UPDATE user set money = money + 100 WHERE name = 'b';

-- 由于手动开启的事务没有开启自动提交，
-- 此时发生变化的数据仍然是被保存在一张临时表中。
SELECT * FROM user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |   900 |
|  2 | b    |  1100 |
+----+------+-------+

-- 测试回滚
ROLLBACK;

SELECT * FROM user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |  1000 |
|  2 | b    |  1000 |
+----+------+-------+
```

仍然使用 COMMIT 提交数据，提交后无法再发生本次事务的回滚。

```sql
BEGIN;
UPDATE user set money = money - 100 WHERE name = 'a';
UPDATE user set money = money + 100 WHERE name = 'b';

SELECT * FROM user;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | a    |   900 |
|  2 | b    |  1100 |
+----+------+-------+

-- 提交数据
COMMIT;

-- 测试回滚（无效，因为表的数据已经被提交）
ROLLBACK;
```

## ACID特征与使用

### 事务的四大特征：

- **Atomicity 原子性**：事务是最小的单位，不可以再分割；
- **Consistency 一致性**：要求同一事务中的 SQL 语句，必须保证同时成功或者失败；
- **Isolation 隔离性**：事务1 和 事务2 之间是具有隔离性的；
- **Durability 持久性**：事务一旦结束 ( `COMMIT` ) ，就不可以再返回了 ( `ROLLBACK` ) 。

事务开启：

- 修改默认提交 set autocommit=0;
- begin;
- start transaction

事务手动提交：

- commit;

事务手动回滚:

- rollback;

## 隔离性

**事务的隔离性可分为四种 ( 性能从低到高 )** ：

1. **READ UNCOMMITTED ( 读取未提交 )**

    如果有多个事务，那么任意事务都可以看见其他事务的**未提交数据**。

2. **READ COMMITTED ( 读取已提交 )**

    只能读取到其他事务**已经提交的数据**。

3. **REPEATABLE  READ ( 可被重复读 )**

    如果有多个连接都开启了事务，那么事务之间不能共享数据记录，否则只能共享已提交的记录。

4. **SERIALIZABLE ( 串行化 )**

    所有的事务都会按照**固定顺序执行**，执行完一个事务后再继续执行下一个事务的**写入操作**。

查看当前数据库的默认隔离级别：

```sql
-- MySQL 8.x, GLOBAL 表示系统级别，不加表示会话级别。
SELECT @@GLOBAL.TRANSACTION_ISOLATION;
SELECT @@TRANSACTION_ISOLATION;
+--------------------------------+
| @@GLOBAL.TRANSACTION_ISOLATION |
+--------------------------------+
| REPEATABLE-READ                | -- MySQL的默认隔离级别，可以重复读。
+--------------------------------+

-- MySQL 5.x
SELECT @@GLOBAL.TX_ISOLATION;
SELECT @@TX_ISOLATION;
```

修改隔离级别：

```sql
-- 设置系统隔离级别，LEVEL 后面表示要设置的隔离级别 (READ UNCOMMITTED)。
SET GLOBAL TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;

-- 查询系统隔离级别，发现已经被修改。
SELECT @@GLOBAL.TRANSACTION_ISOLATION;
+--------------------------------+
| @@GLOBAL.TRANSACTION_ISOLATION |
+--------------------------------+
| READ-UNCOMMITTED               |
+--------------------------------+
```

### 脏读

测试 READ UNCOMMITTED ( 读取未提交 ) 的隔离性：

如果有事务a和事务b，a 事务对数据进行操作，在操作过程中事务没有被提交，但是b可以看见a操作的结果

```sql
NSERT INTO user VALUES (3, '小明', 1000);
INSERT INTO user VALUES (4, '淘宝店', 1000);

SELECT * FROM user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
+----+-----------+-------+

-- 开启一个事务操作数据
-- 假设小明在淘宝店买了一双800块钱的鞋子：
START TRANSACTION;
UPDATE user SET money = money - 800 WHERE name = '小明';
UPDATE user SET money = money + 800 WHERE name = '淘宝店';

-- 然后淘宝店在另一方查询结果，发现钱已到账。
SELECT * FROM user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |   200 |
|  4 | 淘宝店    |  1800 |
+----+-----------+-------+
```

由于小明的转账是在新开启的事务上进行操作的，而该操作的结果是可以被其他事务（另一方的淘宝店）看见的，因此淘宝店的查询结果是正确的，淘宝店确认到账。但就在这时，如果小明在它所处的事务上又执行了 ROLLBACK 命令，会发生什么？

```sql
-- 小明所处的事务
ROLLBACK;

-- 此时无论对方是谁，如果再去查询结果就会发现：
SELECT * FROM user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
+----+-----------+-------
```

在READ-UNCOMMITTED 条件下

如果两个不同的地方，都在进行操作，如果事务a开启后，他的数据可以被其他事务读取到，这样就会出现脏读
对应淘宝店主其实也是在事务，只是他的每一个事务都执行了

脏读：一个事务读到了另一个事务没有提交的数据，就叫做脏读。在实际开发中是不允许出现的

### 不可重复读

首先修改隔离级别为read committed:

```sql
SET GLOBAL TRANSACTION ISOLATION LEVEL READ COMMITTED;
SELECT @@GLOBAL.TRANSACTION_ISOLATION;
+--------------------------------+
| @@GLOBAL.TRANSACTION_ISOLATION |
+--------------------------------+
| READ-COMMITTED                 |
+--------------------------------+
```

这样，再有新的事务连接进来时，它们就只能查询到已经提交过的事务数据了。但是对于当前事务来说，它们看到的还是未提交的数据，例如：

```sql
-- bank database, user table
-- 小张是银行的会计，他查看资产
-- 正在操作数据事务（当前事务）
START TRANSACTION;
UPDATE user SET money = money - 800 WHERE name = '小明';
UPDATE user SET money = money + 800 WHERE name = '淘宝店';

-- 虽然隔离级别被设置为了 READ COMMITTED，但在当前事务中，
-- 它看到的仍然是数据表中临时改变数据，而不是真正提交过的数据。
SELECT * FROM user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |   200 |
|  4 | 淘宝店    |  1800 |
+----+-----------+-------+

-- 假设此时在远程开启了一个新事务，连接到数据库。
$ mysql -u root -p12345612

-- 此时远程连接查询到的数据只能是已经提交过的
SELECT * FROM user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
+----+-----------+-------+
```

但是这样还有问题，那就是假设一个事务在操作数据时，其他事务干扰了这个事务的数据。例如：

```sql
-- 小张在查询数据的时候发现：
SELECT * FROM user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |   200 |
|  4 | 淘宝店    |  1800 |
+----+-----------+-------+

-- 在小张求表的 money 平均值之前，小王做了一个操作：
START TRANSACTION;
INSERT INTO user VALUES (5, 'c', 100);
COMMIT;

-- 此时表的真实数据是：
SELECT * FROM user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
|  5 | c         |   100 |
+----+-----------+-------+

-- 这时小张再求平均值的时候，就会出现计算不相符合的情况：
SELECT AVG(money) FROM user;
+------------+
| AVG(money) |
+------------+
|  820.0000  |
+------------+
```

虽然我只能读到已经提交的数据，但还是会出现问题就是在读取同一个表的数据时，可能会发生前后不一致的情况。

**在read committed情况下会出现不可重复读现象**

⇒ 加锁

### 幻读

将隔离级别设置为 REPEATABLE READ ( 可被重复读取 ) :

```sql
SET GLOBAL TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SELECT @@GLOBAL.TRANSACTION_ISOLATION;
+--------------------------------+
| @@GLOBAL.TRANSACTION_ISOLATION |
+--------------------------------+
| REPEATABLE-READ                |
+--------------------------------+
```

测试 REPEATABLE READ ，假设在两个不同的连接上分别执行 START TRANSACTION :

```sql
-- 小张 - 成都
START TRANSACTION;
INSERT INTO user VALUES (6, 'd', 1000);

-- 小王 - 北京
START TRANSACTION;

-- 小张 - 成都
COMMIT;
```

当前事务开启后，没提交之前，查询不到，提交后可以被查询到。但是，在提交之前其他事务被开启了，那么在这条事务线上，就不会查询到当前有操作事务的连接。相当于开辟出一条单独的线程。

无论小张是否执行过 `COMMIT` ，在小王这边，都不会查询到小张的事务记录，而是只会查询到自己所处事务的记录：

```sql
SELECT * FROM user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
|  5 | c         |   100 |
+----+-----------+-------+
```

这是**因为小王在此之前开启了一个新的事务 ( `START TRANSACTION` ) ，那么在他的这条新事务的线上，跟其他事务是没有联系的**，也就是说，此时如果其他事务正在操作数据，它是不知道的。

然而事实是，在真实的数据表中，小张已经插入了一条数据。但是小王此时并不知道，也插入了同一条数据，会发生什么呢？

```sql
INSERT INTO user VALUES (6, 'd', 1000);
-- ERROR 1062 (23000): Duplicate entry '6' for key 'PRIMARY'
```

报错了，操作被告知已存在主键为 6 的字段。这种现象也被称为幻读，一个事务提交的数据，不能被其他事务读取到。

事务a和事务b同时操作一张表，事务a提交的数据也不能被事务b看到，就会造成幻读

### 串行化

顾名思义，就是所有事务的写入操作全都是串行化的。什么意思？把隔离级别修改成 SERIALIZABLE :

```sql
SET GLOBAL TRANSACTION ISOLATION LEVEL SERIALIZABLE;
SELECT @@GLOBAL.TRANSACTION_ISOLATION;
+--------------------------------+
| @@GLOBAL.TRANSACTION_ISOLATION |
+--------------------------------+
| SERIALIZABLE                   |
+--------------------------------+
```

```sql
-- 小张 - 成都
START TRANSACTION;

-- 小王 - 北京
START TRANSACTION;

-- 开启事务之前先查询表，准备操作数据。
SELECT * FROM user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | a         |   900 |
|  2 | b         |  1100 |
|  3 | 小明      |  1000 |
|  4 | 淘宝店    |  1000 |
|  5 | c         |   100 |
|  6 | d         |  1000 |
+----+-----------+-------+

-- 发现没有 7 号王小花，于是插入一条数据：
INSERT INTO user VALUES (7, '王小花', 1000);
-- sql 语句会卡住
-- 当user表被另一个事务操作时，其他事务里面的写操作是不可以进行的
-- 直到另一个事务结束之后，写操作才会执行。（前提没有等待超时）
```

此时会发生什么呢？由于现在的隔离级别是 **SERIALIZABLE ( 串行化 )** ，串行化的意思就是：假设把所有的事务都放在一个串行的队列中，那么所有的事务都会按照**固定顺序执行**，执行完一个事务后再继续执行下一个事务的**写入操作** ( **这意味着队列中同时只能执行一个事务的写入操作** ) 。

根据这个解释，小王在插入数据时，会出现等待状态，直到小张执行 `COMMIT` 结束它所处的事务，或者出现等待超时。

**串行口的问题是，性能太差**

**隔离级别越高，性能越差:**

read uncommitted>read committed>repeatable read>serializable

**mysql默认隔离级别是repeatable read**



#### 参考资料：

[一天学会 MySQL 数据库](https://www.bilibili.com/video/BV1Vt411z7wy/)

[MySQL 数据类型](https://www.runoob.com/mysql/mysql-data-types.html)
