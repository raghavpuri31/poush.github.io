---
title: Different Types of SQL Commands
layout: post
categories: intro sql
date:   2017-04-25 13:26:44 +0530
categories: general windows
tag:
- sql
- DDL
- DML
- DCL
star: true
author: poush
---


Structured Query Language (aka "SQL") is created for managing RDBMS ( Relational Database Management Systems )

From Wikipedia, 
> Originally based upon relational algebra and tuple relational calculus, SQL consists of a data definition language, data manipulation language, and data control language. The scope of SQL includes data insert, query, update and delete, schema creation and modification, and data access control. Although SQL is often described as, and to a great extent is, a declarative language (4GL), it also includes procedural elements.

Looks Good!
In simple terms, we are using SQL to operate our RDBMS and this language has seriously easy syntax.

Now there are different types of SQL commands. ( and yeah! We run commands to operate our RDBMS in SQL)
1. DDL  -  Data Definition Language
2. DML - Data Manipulation Language
3. DCL  - Data Control Language

Till my viva in my college, I wasn't aware of any third type :D

## DDL
DDL provides the standard structure of commands that defines the structure of database objects like tables, indexes. Some of the operations/commands are

* CREATE TABLE
* ALTER TABLE
* DROP TABLE
* CREATE INDEX
* ALTER INDEX
* DROP INDEX
* CREATE VIEW
* DROP VIEW

## DML
These commands are used for managing data within schema objects. Some examples:

* INSERT - insert data into a table
* UPDATE - updates existing data within a table
* DELETE - deletes all records from a table, the space for the records remain

## DCL
These commands manage the access to your database. Generally applied to database users so that you can control the access of other users to your database.

* ALTER PASSWORD
* GRANT
* REVOKE
* CREATE SYNONYM

That's all summarized for three different types of SQL commands. 

## Wait!
You see SELECT command anywhere? If you are familiar with SQL then you might know about most used command, The "SELECT" command and if you see the lists above you will not find it in any category.  Well there is another category called  
### DQL - Data Query language

Comprises only one command ***SELECT*** but is most used and most concentrated for SQL users. With different options and clauses, it is used to make queries (inquiries) to the database.

Take the simple example, when you open a website page then the SELECT queries are made to get user data, website configurations, data to shown on web page. Complex and large data require joins and multiple queries. 
And yes number of queries can be reduced by different techniques but we will discuss it later :)