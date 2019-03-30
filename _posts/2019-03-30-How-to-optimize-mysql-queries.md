---
layout: post
title: How to Optimize MySQL Queries for Speed and Performance
gh-badge: [star, fork, follow]
tags: [performance, optimize, database, mysql, ]
comments: true
---

### Tip #1: Using Index 
#### Index All Columns Used in `where`, `order by`, and `group by` Clauses
An index is also very useful when it comes to sorting records.  
Check the explain statement of query from the table with 500 rows without index  
```mysql
mysql> explain select customer_id, customer_name from customers where customer_id='140385';
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table     | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | customers | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  500 |    10.00 | Using where |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
```
First, it is clear that MySQL will conduct a full table scan because key column is `NULL`.  
Second, MySQL server has clearly indicated that it's going to conduct a full scan on the 500 rows in our database.  
To optimize the above query, we can just add an index to the `customer_id` field 
```mysql
mysql> Create index customer_id ON customers (customer_Id);
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> Explain select customer_id, customer_name from customers where customer_id='140385';
+----+-------------+-----------+------------+------+---------------+-------------+---------+-------+------+----------+-------+
| id | select_type | table     | partitions | type | possible_keys | key         | key_len | ref   | rows | filtered | Extra |
+----+-------------+-----------+------------+------+---------------+-------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | customers | NULL       | ref  | customer_id   | customer_id | 13      | const |    1 |   100.00 | NULL  |
+----+-------------+-----------+------------+------+---------------+-------------+---------+-------+------+----------+-------+
```
You can clearly see that the number of rows to scan will be 1  
#### Use Index Prefixes 
Indexing only a prefix of column values in this way can make the index file much smaller.  
When you index a BLOB or TEXT column, you must specify a prefix length for the index.  
```mysql
CREATE TABLE test (blob_col BLOB, INDEX(blob_col(10)));
```
Read more at [Column-Indexes](https://dev.mysql.com/doc/refman/8.0/en/column-indexes.html)  

#### Multiple-Column Indexes
[Multiple-Column Indexes](https://dev.mysql.com/doc/refman/8.0/en/multiple-column-indexes.html)

### Tip #2: Optimize Like Statements With Union Clause
When want to select with like statment in 2 fields
Don't use `like` statement with `or`
```mysql
mysql> select * from students where first_name like  'Ade%'  or last_name like 'Ade%' ;
```
Use `union` instead
```mysql
mysql> select  from students where first_name like  'Ade%'  
union all select  from students where last_name like  'Ade%' ;
```
In fact, we should avoid Like Expressions With Leading Wildcards to search text in table, 
MySQL is not able to utilize indexes when there is a leading wildcard in a query.  
With example at tip #2, a search `like` this will cause MySQL to perform *full table scan* even  
if you have indexed the 'first_name' field on the students table 
```mysql
mysql> explain select * from students where first_name like  '%Ade'  ;
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | students | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  500 |    11.11 | Using where |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
```
Better way is using search engine ( elastic, redis, sphinx, ... ).

### Tip #3: Take Advantage of MySQL Full-Text Searches
Using MySQL full-text search (FTS) because it is far much faster than queries using wildcard characters  
To add a full-text search index to the students sample table, we can use the below MySQL command  
```mysql
mysql>Alter table students ADD FULLTEXT (first_name, last_name);
mysql> explain Select * from students where match(first_name, last_name) AGAINST ('Ade');
+----+-------------+----------+------------+----------+---------------+------------+---------+-------+------+----------+-------------------------------+
| id | select_type | table    | partitions | type     | possible_keys | key        | key_len | ref   | rows | filtered | Extra                         |
+----+-------------+----------+------------+----------+---------------+------------+---------+-------+------+----------+-------------------------------+
|  1 | SIMPLE      | students | NULL       | fulltext | first_name    | first_name | 0       | const |    1 |   100.00 | Using where; Ft_hints: sorted |
+----+-------------+----------+------------+----------+---------------+------------+---------+-------+------+----------+-------------------------------+
```

### Tip #4: Optimize Your Database Schema
#### Normalize Tables  
Design table with following normal forms :  
- First Normal Form (1NF)
- Second Normal Form (2NF)
- Third Normal Form (3NF)
- Boyce and Codd Normal Form (BCNF)
- Fourth Normal Form (4NF)

Always use the same data type for storing similar values even if they are on different tables  
Example : the data type of field `customer_id` should be same as INT type on both tables `customer` and `order` 

#### Use Optimal Data Types
Use right data type for each fields and shorter is always better
For instances, if use design field to store item type that having value less than 255  
Using `TINYINT` is better than `INT`
And use `DATETIME` for the fields expects a date value 

#### Avoid `Null` Values
Should avoid this kind of values whenever possible because they can harm your database results.  
Take a look at [Problem with NULL](https://dev.mysql.com/doc/refman/8.0/en/problems-with-null.html)  
Sometime, we will be confused and meet problem when use `count`, `sum`, `min` with `NULL` value.  
Below query will output different values when select table having rows with `NULL` value  
```mysql
create table `tb_user` (
  user_id INT AUTO_INCREMENT,
  age TINYINT Unsigned NULL,
  PRIMARY KEY (user_id)
);
insert into tb_user(user_id, age) values (1, 30), (2, 20), (3, null), (4, 0);
select count(*), count(age) from tb_user;
count(*)	count(age)
4	3
```
Another problems
```mysql
SELECT NULL, 1+NULL, CONCAT('Invisible',NULL);
NULL	1+NULL	CONCAT('Invisible',NULL)
(null)	(null)	(null)
```
Finaly, to avoid `NULL` value, should use `NOT NULL` and `DEFAULT` for field when designing table

#### Avoid Too Many Columns
Wide tables can be extremely expensive and require more CPU time to process.  
Instead of creating one wide table, consider splitting it apart in to logical structures.  
[Supporting Wide Table in Mysql](https://medium.com/build-acl/supporting-wide-tables-in-mysql-18248e4e3b0a)  

#### Optimize Joins
Always include fewer tables in your join statements.  
An SQL statement with poorly designed pattern that involves a lot of joins may not work well.  
A rule of thumb is to have utmost a dozen joins for each query.  

### Tip #5: MySQL Query Caching
Should take advantage of MySQL query caching feature.  
This will speed up performance when read operations are conducted  
Can check if your MySQL server has query cache enabled by running the command below  
```mysql
mysql> show variables like 'have_query_cache';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| have_query_cache | YES   |
+------------------+-------+
1 row in <b>set</b> (0.00 sec)
```



---
Referenced link :  
[How to Optimize MySQL Queries for Speed and Performance](https://dzone.com/articles/how-to-optimize-mysql-queries-for-speed-and-perfor)  
[Problem with NULL](https://dev.mysql.com/doc/refman/8.0/en/problems-with-null.html)  
[Supporting wide tables in MYSQL](https://medium.com/build-acl/supporting-wide-tables-in-mysql-18248e4e3b0a)  

