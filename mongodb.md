### mongodb

文档型数据库，是一种NoSQL的数据库

#### 一、基础概念

##### 1、对比mysql

- database 数据库
- collection 集合（mysql table）
- document 数据记录行/文档 (mysql row)
-  field 数据字段/域 (mysql column)
- Index 索引 (mysql index)
-  表连接,MongoDB不支持
-  primary key  (主键,MongoDB自动将_id字段设置为主键)

##### 2、特性

- 1、存储方式：虚拟内存+持久化。
- 2、查询语句：是独特的MongoDB的查询方式。
- 3、适合场景：事件的记录，内容管理或者博客平台等等。
- 4、架构特点：可以通过副本集，以及分片来实现高可用。
- 5、数据处理：数据是存储在硬盘上的，只不过需要经常读取的数据会被加载到内存中，将数据存储在物理内存中，从而达到高速读写。
- 6、成熟度与广泛度：新兴数据库，成熟度较低，Nosql数据库中最为接近关系型数据库，比较完善的DB之一，适用人群不断在增长。

##### 3、优势与劣势

1、在适量级的内存的MongoDB的性能是非常迅速的，它将热数据存储在物理内存中，使得热数据的读写变得十分快。
2、MongoDB的高可用和集群架构拥有十分高的扩展性。
3、在副本集中，当主库遇到问题，无法继续提供服务的时候，副本集将选举一个新的主库继续提供服务。
4、MongoDB的Bson和JSon格式的数据十分适合文档格式的存储与查询。
劣势：
1、 不支持事务操作。MongoDB本身没有自带事务机制，若需要在MongoDB中实现事务机制，需通过一个额外的表，从逻辑上自行实现事务。
2、 应用经验少，由于NoSQL兴起时间短，应用经验相比关系型数据库较少。
3、MongoDB占用空间过大。

##### 4、索引原理

B树

#### 二、集群

##### 1、主从复制

##### 2、副本集

##### 3、分片

- **分片策略**

  根据分片的键的值（Shard Key）选取进行数据分片

  - 基于范围的分片
  - 基于Hash值的分片

