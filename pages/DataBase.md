# 从零开始DBA之路

真正的大师，永远怀着一颗学徒的心✋

## 2022.4.6

### 基础命令

基础命令无非就是增删查改，以下给出范例：

#### 一、简单的mysql的语法
每条语句都以分号结尾。

#### 创建一个数据库

格式为：create database 数据库名;

例：create database test;

#### 创建一个表

格式为：create table 表名(内容 类型);

例：create table student(number int , name char(32), score double);

#### 向表中插入信息

格式为：insert into 表名 values(表中的内容);

例：insert into student values(1, 'xiaoming', 99);

#### 从表格中删除信息

格式为：delete from 表名 where 列名 = 条件;

例：delete  from student where name = 'xiaoming';

#### 查询表中的内容

格式为：select 列名 from 表名 where 列名 = 条件;

例：select score from student where name = 'xiaoming';

#### 我们可以用这样的语句查询所有的信息

select * from student;

#### 修改表中的数据

格式为：update 表名 set 列名 = 新内容 where 列名 = 条件;

例：update student set score = 66 where name = 'xiaoming';

#### 从数据库中删除一个表

格式为：drop table 表名;

例：drop table student;


  
  

  
  
