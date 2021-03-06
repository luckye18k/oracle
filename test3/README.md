# 实验3：创建分区表

## 软工2班-201810414210-胡西凌
### 实验目的：

掌握分区表的创建方法，掌握各种分区方式的使用场景。

### 实验内容：
- 本实验使用3个表空间：USERS,USERS02,USERS03。在表空间中创建两张表：订单表(orders)与订单详表(order_details)。
- 使用**你自己的账号创建本实验的表**，表创建在上述3个分区，自定义分区策略。
- 你需要使用system用户给你自己的账号分配上述分区的使用权限。你需要使用system用户给你的用户分配可以查询执行计划的权限。
- 表创建成功后，插入数据，数据能并平均分布到各个分区。每个表的数据都应该大于1万行，对表进行联合查询。
- 写出插入数据的语句和查询数据的语句，并分析语句的执行计划。
- 进行分区与不分区的对比实验。

### 实验步骤

授予权限

<img src="pic1.png" style="zoom:67%;" />

<img src="pic2.png" style="zoom:67%;" />

在主表orders和从表order_details之间建立引用分区
在study用户中创建两个表：orders（订单表）和order_details（订单详表），两个表通过列order_id建立主外键关联。orders表按范围分区进行存储，order_details使用引用分区进行存储。

<img src="pic3.png" style="zoom:67%;" />

查询数据

<img src="pic4.png" style="zoom:67%;" />

<img src="pic5.png" style="zoom:67%;" />

查询orders中日期在2017-1-1到2018-6-1间的数据：

<img src="pic6.png" style="zoom:67%;" />

联合查询orders和orders_details中日期在2017-1-1到2018-6-1间的数据：

<img src="pic7.png" style="zoom:67%;" />


### 查看数据库的使用情况

以下样例查看表空间的数据库文件，以及每个文件的磁盘占用情况。

$ sqlplus system/123@pdborcl
```sql
SQL>SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files  WHERE  tablespace_name='USERS';

SQL>SELECT a.tablespace_name "表空间名",Total/1024/1024 "大小MB",
 free/1024/1024 "剩余MB",( total - free )/1024/1024 "使用MB",
 Round(( total - free )/ total,4)* 100 "使用率%"
 from (SELECT tablespace_name,Sum(bytes)free
        FROM   dba_free_space group  BY tablespace_name)a,
       (SELECT tablespace_name,Sum(bytes)total FROM dba_data_files
        group  BY tablespace_name)b
 where  a.tablespace_name = b.tablespace_name;
```
- autoextensible是显示表空间中的数据文件是否自动增加。
- MAX_MB是指数据文件的最大容量。

<img src="pic8.png" style="zoom:67%;" />

### 实验总结

通过本次实验我掌握了分区表的创建方法，掌握了各种分区方式的使用场景。