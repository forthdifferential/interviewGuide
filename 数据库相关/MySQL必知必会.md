MySQL必知必会

mysql 本地机 root 密码

## 1.  概述

数据库：保存有组织的数据的容器

1. DBMS：数据库管理系统
2. 表：某种特定类型数据的结构化清单
3. 表名：唯一性，
4. 模式：关于数据库和表的布局及特性的信息；
5. 列：表的一个字段；
6. 数据类型：每个表列都有相应的数据类型；
7. 行：表中的一个记录；
8. 主键：一列，其值能区别表的每个行；一个总是定义主键；唯一性；非空；不更新，不重用，常量
9. SQL(Structured Query Language)：机构化查询语言，专门用来与数据库通信的语言；

## 15.联结

1. 外键：表的一列，包含另一个表的逐渐值，定义了两个表之间的关系

2. 关系数据库的可伸缩性好：能适应不断增加的工作量而不失败

3. 联结：机制，用来在一条select语句中关联表，存在在查询的执行中

4. 笛卡尔积 没有联结条件的表关系返回结果为笛卡尔积，没有where限定联结关系

5. **内部联结**(等值联结)
   SELECT vend_name,prod_name,prod_price
   FROM vendors,products
   WHERE vendors.vend_id = products.vend_id
   ORDER BY vend_name,prod_name;


   FROM vendors INNER JOIN products
   ON vendors.vend_id = products.vend_id;

6. 联结代替子查询

   SELECT cust_name,cust_contact
   FROM customers
   WHERE cust_id IN(SELECT cust_id
   								FROM orders
   								WHERE order_num IN (SELECT order_num

   ​																		FROM oderitems
   ​																		WHERE prod_id ='TNT2'));


   SELECT cust_name,cust_contact
   FROM customers ,orders,oderitems
   WHERE oderitems.prod_id='TNT2'
   	AND oderitems.order_num =orders.order_num 
   	AND order.cuts_id = customers.cust_id;

## 16.创建高级联结

1. 别名用在表上：缩短sql语句，单条SELECT语句中不 止一次引用相同的表
2. **自联结** ：不然要用子查询了
   SELECT p1.prod_id ,p1.prod_name
   FROM products As p1,products As p2
   WHERE p1.vend_id = p2.vend_id
       AND p2.prod_id = 'DTNTR';
3. **自然联结**：需要联结则至少有一列出现在不止一个表中，自然连接防止相同列的多次出现：具体是让一个表通配，其他表罗列，自己控制；
   目前基本都是自然联结
4. **外部联结**：不仅是两个表中关联的行，还要列出包含没有关联行的哪些行：
   对课话下了多少订单进行计数，包括那些从没下过订单的客户
   SELECT custmers.cust_id,orders.order_num
   FROM customers LEFT OUTER JION orders
   ON customers.cust_id=orders.cust_id;
   其中LEFT 表示指定左边表的所有行
5. 带有聚集函数的链接：
   这里复习一下聚集函数count（sum、avg等）和group by联合使用来返回group by后面条件列的值的条数（合、均值）；
   这里就是多了个INNER JOIN

## 17.组合查询

1. 单个查询中从不同的表返回类似结构的数据；单个表执行多个查询，按照按个查询返回数据
2. 用UNION连接两个SELECT语句，可多个组合
3. UNION和where一样，自动去处重复的行，也就是当做一次查询返回了；也可以用UNION ALL保留重复
4. UNION的一堆只能定义一次ORDER BY

## 18. 全文本搜索

1. 在创建时启动FULLTEXT(note_text)；定义之后，MySQL自动维护缩影，增加、更新、删除行随时更新；
2. 索引时执行全文搜索WHERE Match(note_text) Against('rabbit')
   也可以用link实现 WHERE note_text LINCK '%rabbit%';
3. 全文搜索的返回顺序,按照被搜索词出现的前后顺序
4. 也可以在select时候按照搜索值进行返回，其中rank是一个等级值，文本中词靠前的行的等级值比词靠后的 行的等级值高。SELECT note_text,Match(note_text) Against('rabbit') As rank
5. 查询扩展 WITH QUERY EXPANSION
6. 布尔文本搜索：没有FULLTEXT索引也可以使用 IN BOOLEAN MODE

## 19.数据插入

1. insert 插入完整的行，在表名括号给出列明，VALUES括号给出所有的值；
2. 可以省略列，该列为NULL或定义时给出默认值
3. INSERT IN单条insert语句多组值，每组用圆括号，逗号分割
4. INSERT SELECT语句，插入检索出来的数据；不管名字，按照列插入，这对不同的表之间的互换有好处

## 20.更新和删除数据

1. UPTATE语句,别忘了WHERE限制
   UUPTATE customers
   SET cust_email = 'el@fudd.com'

   SET cust_name = 'fudd'
   WHERE cust_id = 1005;

2. 可以把值设置为NULL以删除某个列的值

3. DELETE FROM删除整行

4. 删除所有行 TRUNCATE TABLE

## 21.创建和操纵表

1. CREATE TABLE创建，用PRIMARY KEY()指定主键列；可以指定多个主键
2. 可以使用 IF NOT EXISTS
3. 每个列的数据类型，NOT NULL情况
4. AUTO_INCREMENT，自动对该列增量；每个表只允许一个AUTO_INCREMENT列，而且他必须被缩影；
5. last_insert_id()函数获得最后一个自增的值；
6. DEFAULT 1给出该行的默认值是1；注意该值必须是常量；
7. MySQL支撑多种引擎，具有不同特性和功能，一般使用默认（很可能是MyISAM）;用ENGINE = 语句显示指出使用哪种引擎；
   InnoDB是事务处理引擎，不支持全文搜索
   MEMORY功能差不多，但是数据存储在内存中，速度快
   MyISAM是性能极高的引擎，支持全文内搜索，但不支持事务处理
8. 更改表的结构 ALTER TABLE，增加和删除列 ADD和DROP
9. 删除DROP DABLE customer
10. 重命名表 RENAME TABLE    TO    

## 22. 使用视图

1. 虚拟的表，不包含数据，只包含使用时动态检索数据的查询；
2. 操作视图 CREATE VIEW;SHOW CREATE VIEW;DROP VIEW;CREATE OR REPLACE VIEW
3. 利用视图简化复杂的联结，创建课重用的视图
4. 用视图重新格式化检索出的数据Concat()之后转化为VIEW
5. 用视图过滤不想要的数据
6. 使用视图与计算字段
7. 视图的更新，实际上是更新基表的数据吗，有限制的使用

## 23.存储过程

1. 通过吧处理封装在容易使用的单元中，简单安全高性能

2. 没有创建存储过程的安全访问权限，就不能创建，

3. 调用存储过程

   ~~~mysql 
   CALL productpricing(@pricelow,@pricehigh,@priceaverage);
   然后调用返回显示
   SELECT @pricelow,@pricehigh,@priceaverage;
   ~~~

4. 创建存储过程 参数前缀IN OUT INOUT

5. 无参

   ~~~mysql
   DELIMITER//
   CREATE PROCEDURE productpricing()
   BEGIN
   SELECT Avg(prod_price) AS 
   FROM products;
   END;
   DELIMITER;
   ~~~

   有参

   ~~~mysql
   DELIMITER//
   CREATE PROCEDURE productpricing(
   	OUT p1 DECIMAL(8,2)
   	OUT ph DECIMAL(8,2)
   	OUT pa DECIMAL(8,2))
   BEGIN
   	SELECT Min(prod_price)
   	INTO p1
   	FROM products;
   	SELECT Max(prod_price)
   	INTO ph
   	FROM products;
   	SELECT Avg(prod_price)
   	INTO pa
   	FROM products;
   DELIMITER;
   ~~~

5. 删除存储过程
   DROP PROCEDURE  IF EXISTS priveaverage;
6. 高级的存储过程，相对于封装函数啊
7. 检查存储过程

~~~mysql
SHOW CREATE PROCEDURE ordertotal;
SHOW PROCEDURE SRARUS LIKE 'ordertotal';
~~~

## 24.使用游标

1. 游标用于交互式应用，滚动数据，对数据进行浏览或做出更改；
2. MySQL游标只能用于存储过程和和函数
3. 创建游标 

~~~mysql
BEGIN
DECLARE ordernumbers CURSOR
For 
SELECT order_num FROM orders;
END;
~~~

4. 打开和关闭游标 OPEN 和 CLOSE
5. 使用游标 FETCH ordernumbers INTO o;

## 25. 触发器

1. 响应DELETE、INSERT、UPDATE任一语句而自动执行的MySQL语句

2. 创建触发器 

   ~~~mysql
   CREATE TRIGGER newproduct AFTER INSERT ON products
   FOR EACH ROW SELECT 'Product added'
   ~~~

   每个表最多6个触发器，每个触发器和单个事件相关联

3. 删除触发器 `DROP TRIGGER newproduct;`

4. 使用触发器

5. INSERT触发器

   BEFORE用于数据验证和净化（目的是保证插入表中的数据确实是需要的数据）

   可以引用一个名为**NEW的虚拟表**——看起来想是新建new一个不过是按照AUTO_INCREASE

   ~~~mysql
   CREATE TRIGGER neworder AFTER INSERT ON orders
   FOR EACH ROW SELECT NEW.order_num;
   ~~~

6. DELETE触发器
   可以引用一个名为**OLD的虚拟表**——本条本删掉的行的内容

   ~~~mysql
   存档的使用
   CREATE TRIGGER deleteorder BEFORE DELETE ON orders
   FOR EACH ROW
   BEGIN
   	INSERT INTO archive_orders(order_num,order_date,cust_id)
   	VALUES(OLD.order_num,OLD.order_date,OLD.cust_id);
   END;
   ~~~

7. UPDATE触发器
   可以引用OLD虚拟表访问你以前的值，OLD都是只读不能更新
   BEFORE UPDATE触发器中，NEW的值可能被更新
8. MySQL的触发器比较初级，创建需要特殊的安全访问权限；但是执行是自动的；触发器来保证数据的一致性；创建审计跟踪很容器；MySQL触发器不支持CALL，不能调用存储过程

## 26. 管理事务处理

1. 事务处理，维护数据库的完整性，保证操作的原子性
2. 唯物、回退、提交、保留点
3. 事务开始START TRANSACTION
4. 回退只能在事务处理中使用 ROLLBACK，只能对INSERT, UPDATE, DELETE语句，不能回退SELECT， CREATE, DROP语句
5. 事务处理快中，需要显示提交COMMIT
6. 回退到占位符，保留点SAVEPOINT delete1;保留点越多越好
7. 回退和提交处理后，事务自动关闭，保留点自动释放
8. 一般是在自动提交，可以设置autocommit=0

## 27.全球化和本地化

1. 字符集、编码、校对collate
2. 校对顺序在order by子句中很重要，也可以自己select时自己指定校对顺序
3. Cast()和Convert()函数转换串的字符集

## 28. 安全管理

1. 访问控制：用户访问权的设置；

2. 管理用户：除了root账号，需要创建一系列账号，设置权限；有一个user表包含所有用户账号

3. 创建用户账号 CREATE USER ben IDENTIFIED BY 'p@$$w0rd';为了作为散列值指定口 令，使用IDENTIFIED BY PASSWORD

4. 重命名 RENAME USER ben To bforta;

5. 删除 DROP USER ben;

6. 设置访问权限：
   ~~~mysql
   显示权限 SHOW GRANTS FOR；
   设置权限、被授予的数据库或表、用户名
   只读GRANT SELECT,INSERT ON crashcourse.* TP ben;
   撤销权限
   REVOKE SELECT ON crashcourse.* TP ben;
   ~~~

7. 更改用户口令SET PASSWORD =Password('are you  ok')

## 29.数据库维护

1. MySQL数据是基于硬盘的文件，备份的方式: 提前FLUSH TABELS
2. 数据库维护ANALYZE TABLE，用来检查表键是否正确；CHECK TABLE用来针对许多问题对表进行检查；REPAIR TABLE来修复相应的表；如果从一个表中删除大量数据，应该使用OPTIMIZE TABLE来收回所用的空间，从而优化表的性能
3. 诊断启动问题
4. 查看日志文件 错误、查询、二进制、缓慢查询；FLUSH LOGS语句来刷新和重新开始所有日志文 件

## 30. 改善性能

1. 在导入数据时，应该关闭自动提交
2. 为查看当前设置，可使用SHOW VARIABLES;和SHOW STATUS;
3. 决不要检索比需求还要多的数据。换言之，不要用SELECT *（除 非你真正需要每个列）
4. 一般来说，最好是使用FULLTEXT而不是LIKE。
