---
layout: post
title: How to Optimize MySQL Queries for Speed and Performance
gh-badge: [star, fork, follow]
tags: [performance, optimize, database, mysql, ]
comments: true
---

### Tip #1: Index All Columns Used in 'where', 'order by', and 'group by' Clauses
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
First, it is clear that MySQL will conduct a full table scan because key column is 'NULL'.  
Second, MySQL server has clearly indicated that it's going to conduct a full scan on the 500 rows in our database.  
To optimize the above query, we can just add an index to the 'customer_id' field 
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
Better way is using search engine ( elastic, redis, sphinx, ... ) to search full-text.

### Tip #3: Take Advantage of MySQL Full-Text Searches


---
Referenced link :  
[How to Optimize MySQL Queries for Speed and Performance](https://dzone.com/articles/how-to-optimize-mysql-queries-for-speed-and-perfor)
