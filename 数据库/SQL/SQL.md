# SQL 指令

---

## SQL 入门

### SQL 介绍

SQL 是用于访问和处理数据库的标准的计算机语言。

对于 MySQL、SQL Server、Access、Oracle 等常用数据库，都可以通过使用 SQL 访问和处理数据系统中的数据。

### SQL 使用

计算机安装数据库后，只需要在安装路径打开 CMD 窗口，输入连接指令进入数据库：就可以通过 SQL 指令直接对数据库进行操作。

如果想要在任何路径都能够进入数据库，则需要配置环境变量 Path。

现在主要使用 Navicat 等图形化工具管理数据库，因此直接在控制台书写 SQL 语句的场景变得很少。

### SQL 注意事项

1. SQL 对大小写不敏感。

2. 标识符（库名、表名、字段名、索引、别名）应避免与关键字重名！可用反引号（`）为标识符包裹。

3. 注释：
    单行注释： `# 注释内容`
    多行注释： `/* 注释内容 */`
    单行注释： `-- 注释内容` 

4. 模式通配符：
    `_`   任意单个字符
    `%`   任意多个字符，甚至包括零字符
    `\'`  单引号需要进行转义 

5. 清除已有语句：`\c`

http://c.biancheng.net/mysql/

---

## 基本操作

### 启动/终止服务

- 输入 `net start mysql` 启动本机 MySQL 运行

- 输入 `net stop mysql` 终止本机 MySQL 运行

### 连接/断开服务

MySQL 服务运行时，通过连接指令即可连接 MySQL。

需要输入的属性分别为 (h)IP地址、(P)端口号、(u)用户名、(p)密码。 默认端口号 3306 可省略，密码推荐空缺（在提示时再输入）。

```SQL
-- 本地连接

mysql -h localhost -u root -p 

-- 远程连接

mysql -h 10.0.0.51 -P 3306 -u root -p 123456
```

输入 `exit`、`quit`、`/p` 指令可以断开 MySQL 连接。

---

## 用户管理

MySQL 数据库的全部用户信息保存在 mysql 库中的 user 表内。

- **user 属性**：用户名
- **host 属性**：允许用户登入的网络
- **authentication_string 属性**：密码

### 增删改查

能够对用户进行增删改查操作，需要当前用户拥有非常高的数据库权限。

```SQL
-- 增加用户(CREATE)

mysql> CREATE USER 'boy'@'localhost' IDENTIFIED BY '';              -- 创建用户 boy 允许从本地网络登录
mysql> CREATE USER 'girl'@'10.0.0.%' IDENTIFIED BY '123456';        -- 创建用户 girl 允许从特定网络登录

-- 删除用户(DROP)

mysql> DROP USER 'girl'@'10.0.0.%';

-- 修改用户(ALTER)

mysql> ALTER USER 'boy'@'localhost' IDENTIFIED BY '123456';

-- 重命名用户(RENAME)

mysql> RENAME USER 'boy'@'localhost' TO 'man'@'localhost';

-- 设置密码
mysql> SET PASSWORD = PASSWORD('123456');                            -- 为当前用户设置密码
mysql> SET PASSWORD FOR 'boy'@'localhost' = PASSWORD('密码');        -- 为指定用户设置密码

-- 查询全部用户信息(DESC/SELECT)

mysql> DESC mysql.user;                                            
mysql> SELECT user,host,authentication_string FROM mysql.user     
```


### 权限管理

用户权限分为非常多种，包括全局权限、库权限、表权限、列权限等等。具体情况请在网站查询。

```SQL

-- 赋予权限(GRANT)

mysql> GRANT SELECT,INSERT ON *.*             -- 赋予用户选择插入权限（所有库的所有表）
    -> TO 'boy'@'localhost'                   -- 不存在将新建用户
    -> IDENTIFIED BY '123456'                 
    -> WITH GRANT OPTION;                     -- （可选）允许用户转授权限

-- 撤消权限(REVOKE)

mysql> REVOKE INSERT ON *.*
    -> FROM 'boy'@'localhost';

-- 查看权限

mysql> SELECT Host,User,Select_priv,Grant_priv
    -> FROM mysql.user
    -> WHERE User='testUser';
```

---

## 数据库管理

   
```SQL
-- 查看所有数据库
    show databases;

-- 进入/切换数据库
    use 数据库名;

-- 查看当前数据库
    select database();

-- 创建数据库
    create database [if not exists] 数据库名;


-- 删除数据库
    drop database [if exists] 数据库名;
```

数据库选项信息有以下两种，如果想指定、查看和修改数据库选项信息，请在网站查询。

1. **character set 编码方式**：默认为 utf8mb4
2. **collate 校对规则**：默认为 utf8mb4_general_ci

create database [if not exists] 数据库名 character set utf8mb4 collate;

-- 查看数据库选项信息
    show create database 数据库名;

-- 修改数据库选项信息
    alter database 数据库名 character set utf8;



---

## 表管理

### 表-属性（很少使用）

【**temporary 临时表**： 连接断开时表自动消失】

1. **CHARSET 字符集**
        默认数据库字符集
2. **ENGINE 存储引擎**
        MySQL默认使用InnoDB
3. **DATA DIRECTORY 数据文件目录**

4. **INDEX DIRECTORY 索引文件目录**

5. **COMMENT 表注释**


### 表-列属性

1. **primary key 主键（索引）**
    能唯一标识记录的一个或多个字段。
    表的主键唯一，记录的主键值不能重复，主键字段的值不能为null。

2. **unique 唯一约束（索引）**
    该字段值不能重复。
    
3. **not null 非空约束**
    不允许字段值为空，默认允许。
    
4. **default 默认值**
    设置当前字段的默认值。

5. **auto_increment 自动增长约束**
    用户插入数据时无需为该字段赋值，创建时其值会从一个指定值开始自动增长（默认为1）。

    只能存在一个自动增长字段，且必须为索引。

6. **comment 注释**
    备注信息

7. **foreign key 外键**
    从表通过外键关联到主表的主键，保障数据的一致性和完整性。
    
    update/delete时可选择（默认为restrict）：
    1. cascade： 主表记录更新/删除时，从表相关记录也会被更新/删除。
    2. set null： 主表数据更新/删除时，从表相关记录的外键值被设置为null。
    3. restrict： 存在从表相关记录时，主表不能更新/删除。
   





```SQL
-- 查看所有表
    SHOW TABLES;

-- 创建表
    CREATE [temporary] TABLE [if not exists] Student
    (
        ID int PRIMARY KEY AUTO_INCREMENT=20190001,
        Name varchar(50) NOT NULL,
        Sex int(1) COMMENT 'Male 1，Female 0',
        Access_Time date DEFAULT GETDATE(),
        Major_ID int(10) FOREIGN KEY REFERENCES Major(ID) 
    )

    CREATE TABLE Grade
    (
        Student_ID int,
        Course_ID int,
        Grade int,
        CONSTRAINT pk_Grade PRIMARY KEY (Student_ID,Course_ID),
        CONSTRAINT fk_Student FOREIGN KEY (Student_ID) REFERENCES Student(ID),
        CONSTRAINT fk_Course FOREIGN KEY (Course_ID) REFERENCES Course(ID)
    )

-- 删除表
    DROP TABLE [IF EXISTS] 表名;

-- 查看表结构
    SHOW CREATE TABLE 表名;
    DESC 表名 / DESCRIBE 表名 / EXPLAIN 表名 / SHOW COLUMNS FROM 表名 [LIKE 'PATTERN']
    SHOW TABLE STATUS [FROM db_name] [LIKE 'pattern']
-- 修改表
    -- 修改表本身的选项
        ALTER TABLE 表名 表属性;
        EG:  ALTER TABLE 表名 ENGINE=MYISAM;
    -- 重命名
        RENAME TABLE 原表名 TO 新表名
        RENAME TABLE 原表名 TO 库名.表名（将表移动到另一个数据库）
    -- 修改表的字段机构
        ALTER TABLE 表名
        -- 操作
            ADD [COLUMN] 字段名        -- 增加字段（默认在最后）
                AFTER 字段名            -- 表示增加在该字段名后面
                FIRST                -- 表示增加在第一个

            ADD PRIMARY KEY(字段名)    -- 创建主键
            ADD UNIQUE [索引名] (字段名)-- 创建唯一索引
            ADD INDEX [索引名] (字段名)    -- 创建普通索引

            MODIFY [COLUMN] 字段名 字段属性        --对字段属性进行重定义
            CHANGE [COLUMN] 原字段名 新字段名 字段属性   -- 对字段名修改

            DROP [COLUMN] 字段名        -- 删除字段
            DROP PRIMARY KEY    -- 删除主键(删除主键前需删除其自增属性)
            DROP INDEX 索引名    -- 删除索引
            DROP FOREIGN KEY 外键    -- 删除外键

-- 清空表数据
    TRUNCATE [TABLE] 表名
-- 复制表结构
    CREATE TABLE 表名 LIKE 要复制的表名
-- 复制表结构和数据
    CREATE TABLE 表名 [AS] SELECT * FROM 要复制的表名
-- 检查表是否有错误
    CHECK TABLE tbl_name [, tbl_name] ... [option] ...
-- 优化表
    OPTIMIZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ...
-- 修复表
    REPAIR [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ... [QUICK] [EXTENDED] [USE_FRM]
-- 分析表
    ANALYZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ...
```

---

## 数据操作

### 增删查改

**插入数据(INSERT)**，如果已有主键值则插入数据失败。

```SQL
mysql> INSERT INTO student
    -> (ID,name,grade)
    -> VALUES(755,'王东浩',80);
```

**插入并替换数据(REPLACE)**，如果已有主键值则先删除再插入（非直接 UPDATE，可能会删除外键）。

```SQL
mysql> REPLACE INTO student
    -> (ID,name,grade)
    -> VALUES(755,'王东浩',80);
```

**更新数据(UPDATE)**，如果没有选择条件则更新全部！

```SQL
mysql> UPDATE student
    -> SET name='孙鹏',grade=60
    -> WHERE id=753;
```


**删除数据(DELETE)**，如果没有选择条件则删除全部！

```SQL
mysql> DELETE FROM student
    -> WHERE id=754;
```


**查询数据(SELECT)**

```SQL
mysql> SELECT id,name FROM student               -- 按条件查询数据
    -> WHERE id BETWEEN 753 and 755;

mysql> SELECT * FROM student;                    -- 查询全部数据
```


### 条件筛选




**数据筛选(WHERE)**：选择性展示数据。

```SQL
mysql> SELECT * FROM student WHERE ID = 100;

mysql> SELECT * FROM student WHERE ID != 100;

mysql> SELECT * FROM student WHERE ID [not] between 30 and 50;

mysql> SELECT * FROM student WHERE ID [not] in (30, 35 ,50);

mysql> SELECT * FROM student WHERE grade is [not] null;
```




**数据分组(GROUP)**：将多条数据合并为一条数据展示。

使用以下分组函数展示合并后的数据，如果不含 GROUP 语句则默认把全部数据合并为一条数据。

- count 求个数
- sum 求和
- max 求最大值
- min 求最小值
- avg 求平均值
- group_concat 返回组内字符串连接

```SQL
-- 查询班级总数，只返回一条数据

mysql> SELECT COUNT(*) FROM class;
```

```SQL
-- 根据年级合并班级数据，查询年级以及年级总人数

mysql> SELECT grade, SUM(class.student_num) AS nums    
    -> FROM class 
    -> GROUP BY grade;                                 
```

*AS 关键字为表或者列起别名，可省略。*


**数据分组筛选(HAVING)**：对分组后的结果再次进行筛选。

```SQL
-- 筛选年级人数大于200 的年级和年级总人数

mysql> SELECT grade, SUM(class.student_num) AS nums 
    -> FROM class 
    -> GROUP BY grade
    -> HAVING SUM(class.student_num) > 200;
```

**数据排序（ASC/DESC）**：数据按指定顺序排列。

```SQL
-- 学生数据按年龄降序排列，年龄相同按学号升序排列

mysql> SELECT * 
    -> FROM student 
    -> ORDER BY age DESC, ID ASC;` 
```

**数据限定条数(LIMIT)**：限定管理数据的条数
```SQL
-- 学生数据按成绩排列，只返回第1-10名

mysql> SELECT * 
    -> FROM student
    -> ORDER BY grade DESC 
    -> LIMIT 0,10;
```


### 多表查询


#### 嵌套查询

查询语句的条件来自另一个查询语句。

**FROM 型**：子语句返回一个表，且必须给子查询结果取别名。

```SQL
mysql> SELECT * 
    -> FROM (SELECT * FROM tb WHERE id > 0) AS subfrom 
    -> WHERE id > 1;
```

**WHERE 型**：子语句返回一个值（不能用于 UPDATE）。

```SQL
mysql> SELECT * 
    -> FROM tb
    -> WHERE money = (SELECT max(money) FROM tb);
```


#### 合并查询(UNION)

多个表查询的字段列表一致，将多个表的查询结果合为一个表返回。

默认为 DISTINCT 形式，不同表查询到的相同数据只展示一个。
    
```SQL
mysql> (SELECT * FROM tb WHERE id < 10) 
    -> UNION
    -> (SELECT * FROM tb WHERE id > 20);
```

设置为 ALL 则不同表查询到的相同结果重复展示。

```SQL
mysql> (SELECT * FROM student1) 
    -> UNION ALL 
    -> (SELECT * FROM student2);
```

#### 组合查询

两个表数据相乘形成笛卡尔积，然后在表内查询。（使用场景很少，基本使用连表查询）

```SQL
mysql> SELECT * FROM student, class;
```


#### 连表查询(JOIN)

两个表数据通过相同的公有属性连接，等同于筛选后的组合查询。

**内连接(INNER JOIN)**:  （默认连接方式）可直接用 JOIN 表示。

1. on/where 表示连接条件（可省略）。
2. using 会自动查找相同字段名。

```SQL
mysql> SELECT e.empno,e.ename,d.dname
    -> FROM emp e JOIN dept d
    -> ON e.deptno = d.deptno;
```

**交叉连接(CROSS JOIN)**：没有连接条件的内连接。

```SQL
mysql> SELECT *
    -> FROM tb1 CROSS JOIN tb2;
```


**外连接(OUTER JOIN)**：如果数据不存在，也会出现在连接结果中。
    
- left join 如果数据不存在，左表记录会出现，而右表为null填充
- right join 如果数据不存在，右表记录会出现，而左表为null填充

**自然连接(natural join)**：自动判断连接条件完成连接。

- natural left join
- natural right join



---





## 视图操作


-- 创建视图
CREATE [OR REPLACE] [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}] VIEW view_name [(column_list)] AS select_statement
    - 视图名必须唯一，同时不能与表重名。
    - 视图可以使用select语句查询到的列名，也可以自己指定相应的列名。
    - 可以指定视图执行的算法，通过ALGORITHM指定。
    - column_list如果存在，则数目必须等于SELECT语句检索的列数

-- 查看结构
    SHOW CREATE VIEW view_name 

-- 删除视图
    - 删除视图后，数据依然存在。
    - 可同时删除多个视图。
    DROP VIEW [IF EXISTS] view_name ...

-- 修改视图结构
    - 一般不修改视图，因为不是所有的更新视图都会映射到表上。
    ALTER VIEW view_name [(column_list)] AS select_statement

-- 视图作用
    1. 简化业务逻辑
    2. 对客户端隐藏真实的表结构

-- 视图算法(ALGORITHM)
    MERGE        合并
        将视图的查询语句，与外部查询需要先合并再执行！
    TEMPTABLE    临时表
        将视图执行完毕后，形成临时表，再做外层查询！
    UNDEFINED    未定义(默认)，指的是MySQL自主去选择相应的算法。



## 事务

-- 事务开启
    START TRANSACTION; 或者 BEGIN;
    开启事务后，所有被执行的SQL语句均被认作当前事务内的SQL语句。
-- 事务提交
    COMMIT;
-- 事务回滚
    ROLLBACK;
    如果部分操作发生问题，映射到事务开启前。


-- 保存点
    SAVEPOINT 保存点名称 -- 设置一个事务保存点
    ROLLBACK TO SAVEPOINT 保存点名称 -- 回滚到保存点
    RELEASE SAVEPOINT 保存点名称 -- 删除保存点

-- InnoDB自动提交特性设置
    SET autocommit = 0|1;    0表示关闭自动提交，1表示开启自动提交。
    - 如果关闭了，那普通操作的结果对其他客户端也不可见，需要commit提交后才能持久化数据操作。
    - 也可以关闭自动提交来开启事务。但与START TRANSACTION不同的是，
        SET autocommit是永久改变服务器的设置，直到下次再次修改该设置。(针对当前连接)
        而START TRANSACTION记录开启前的状态，而一旦事务提交或回滚后就需要再次开启事务。(针对当前事务)


/* 锁表 */
表锁定只用于防止其它客户端进行不正当地读取和写入
MyISAM 支持表锁，InnoDB 支持行锁
-- 锁定
    LOCK TABLES tbl_name [AS alias]
-- 解锁
    UNLOCK TABLES


/* 触发器 */ ------------------
    触发程序是与表有关的命名数据库对象，当该表出现特定事件时，将激活该对象
    监听：记录的增加、修改、删除。

-- 创建触发器
CREATE TRIGGER trigger_name trigger_time trigger_event ON tbl_name FOR EACH ROW trigger_stmt
    参数：
    trigger_time是触发程序的动作时间。它可以是 before 或 after，以指明触发程序是在激活它的语句之前或之后触发。
    trigger_event指明了激活触发程序的语句的类型
        INSERT：将新行插入表时激活触发程序
        UPDATE：更改某一行时激活触发程序
        DELETE：从表中删除某一行时激活触发程序
    tbl_name：监听的表，必须是永久性的表，不能将触发程序与TEMPORARY表或视图关联起来。
    trigger_stmt：当触发程序激活时执行的语句。执行多个语句，可使用BEGIN...END复合语句结构

-- 删除
DROP TRIGGER [schema_name.]trigger_name

可以使用old和new代替旧的和新的数据
    更新操作，更新前是old，更新后是new.
    删除操作，只有old.
    增加操作，只有new.
