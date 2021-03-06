# 数据库系统

## 第一章

### 数据库(应用)系统

分为四个部分

* 用户
* 应用
* DBMS
* DB

有以下功能

* 管理元数据
* 管理业务数据
* 管理访问规则
* 提供并发访问控制
* 执行数据库的备份和恢复

### 数据模型

描述事物对象的数据组成, 数据关系和数据约束的抽象结构及说明
典型的有

* 层次数据模型
* 网状数据模型
* 关系数据模型

### 数据管理技术演化

* 人工管理阶段 - before 1950s
  无法满足数据管理需求
* 文件系统管理阶段 - 1950s-1960s
  冗余 繁琐 不安全 无并发 数据无联系
* 数据库管理阶段 - after 1960s
  独立于应用 结构化 安全 并发

### 数据库应用系统类型

* 业务处理系统 - 表单

* 管理信息系统 - OA

* 决策支持系统 - 商业智能

### 数据库系统应用结构

针对不同应用场景, 数据库系统的应用(实现)方式也不一样

* 单用户

* 集中式 - 所有逻辑由服务器处理

* C/S - 部分逻辑由客户端处理

* 分布式

## 第二章

### 关系代数

* 每行存储一个实例
* 每列存储一类属性
* 每格只存单个值
* 不允许有重复的行
* 不允许属性重名
* 行列顺序任意

##### 基本概念

* 域Domain  --  属性的取值范围 -- 对应数据库中的数据类型,varchar/integer/date...
* 基数  -- 域中的可取值的个数 
* 笛卡尔积 -- n个域的n元组,每个元素对该域取所有值 
* 关系Relationship -- n个域的笛卡尔积中有意义的部分 -- 对应二维表
  因为笛卡尔积相当于是全集,全集没有意义.
* 元数/度数 -- n --  笛卡尔积元组元素的个数/属性的个数 -- 表的列数/属性数

##### 关系类型
* 基本关系 -- 直接对应表
* 视图关系 -- 虚拟表 -- 对应数据库的View

##### Key
* 主键 Primary Key
  **NOT NULL**
  **唯一**
* 候选键 Candidate Key
  **属性的每一行值皆不同, 即为候选键**
* 复合键 Compound Key
  **多个属性组成主键**
  **性能低**
* 代理键 
  **数据库负责生成的主键**

### 关系模型

#####数据结构(组织)
基于集合论的关系概论,表现为二维表

#####数据操作
***
* 选择 -- 对行进行选择 -- where 列1=....
* 投影 -- 对列进行投影 -- select 列1,列2
* 连接 -- 针对某一列的广义笛卡尔积的投影和选择 -- select 列1,列2  from 表1,表2 where 列3 ......
* 除 -- ? -- 无应用
* 并 -- 域相同
* 交 -- 域相同
* 差 -- 域相同

***
* 插入 insert
* 删除 delete
* 更新 update
***

#####关系约束
* 实体ENTITY完整性 -- 针对主键非空唯一的约束
* 参照REFERENCE完整性 -- 针对外键存在的约束,参照记录修改时的置空/修改(级联更新)
* 自定义完整性 -- 针对属性取值的约束

## 第三章

### 结构化查询语言 Structured Query Language

##### DDL

* CREATE 
* DROP

##### DML

* UPDATE
* INSERT
* DELETE

##### DQL

* SELECT 查询

##### DCL 用于访问权限的控制

* GRANT 授权
* REVOKE 销权

##### TPL Transaction Process 事务控制

* BEGIN-TRANSACTION 开始事务
* ROLLBACK 回滚
* COMMIT 提交

##### CCL

##### 其它

* GROUP BY 子句

## 第四章



## 第五章

### ACID

* 原子性 Atomicity
* 一致性 Consistency
* 隔离性 Isolatoin
* 持续性 Durability

### 关系 规范化 范式

* 第一范式
  属性不可再分
* 第二范式
  消除部分函数依赖(属性完整依赖复合主键)
* 第三范式
  消除属性传递函数依赖(非键属性不能由其中一部分决定)
* BCNF范式
  所有函数依赖决定因子都是候选键
* 第四范式
  消除多值函数依赖(属性依赖于多个值(键)



## 第六章

每一条SQL默认情况下自动构成一个事务
也就是说SQL中的某一个属性或者约束出问题, 整个SQL都回滚

### 并发问题 

* 丢失更新
* 不可重复读取
* 脏读

### 加锁协议

* 一级加锁协议

* 二级加锁协议

* 三级加锁协议

### 避免死锁

* 互斥条件

* 请求保持条件

* 不剥夺条件

* 循环等待条件

### 安全模型



