---
author: ranjithru
comments: true
date: 2011-07-25 10:15:40+00:00
layout: post
slug: how-to-make-your-application-work-for-both-mysql-and-postgresql
title: How to make your application work for both MySQL and PostgreSQL?
wordpress_id: 73
categories:
- Database
tags:
- database
- MySQL
- PostgreSQL
---


I am using rails 3 and ruby 1.9.2 in my application. 
For development environment, I am using MySQL and for staging environment, I am using PostgreSQL Database.  
After hosting, we have faced some issues.

**1) Quoting styles:**
  MySQL allows you to quote table and column names with backquotes, whereas PostgreSQL uses double quotes.
  For ex: 
  One of our tables has a column in it called when, which must be quoted whenever we use it.
  Rails will of course handle the quoting for you if you do something like 
    
    Meeting.find_by_when(Time.now)


  But if you are constructing your own SQL conditions then you have to handle the quoting problem.
  
  In MySQL, it would be like
  
    
    Meeting.where("`when` < ?", Time.now)


  
  In PostgreSQL, it would be like
  
    
    Meeting.where("\"when\" < ?", Time.now)


  
  Solution:
  
    
    Meeting.where("#{Meeting.connection.quote_column_name(when)} < ?", Time.now)
    


  

**2) Boolean type:**
  MySQL lacks a native BOOLEAN type, so if you create a boolean column in Rails, you will end up with a TINYINT(1) column which has values of 0 and 1 for false and true respectively. PostgreSQL has a native BOOLEAN type, it will accept only false/true unlike MySQL.
  
  In MySQL, it would be like
  
    
    Meeting.where("import=1") OR Meeting.where("import=?", true)


  
  In PostgreSQL, it would be like
  
    
    Meeting.where("import=?", true)


  
  Solution:
  
    
    so replace 0 and 1 with false and true in your all files then it work in both 
    MySQL and PostgreSQL.


  
 
**3) Other differences:**
<ol type='a'>
  <li>Fulltext Search: PostgreSQL is case sensitive. MySQL is not case sensitive.</li>
  <li>To select random records from DB, Mysql has a function called "rand()" and PostgreSQL has a function called "random()".</li>
  <li>PostgreSQL ALTER TABLE supports ADD COLUMN, RENAME COLUMN and RENAME TABLE only. MySQL has all options in ALTER TABLE.</li>
  <li>In PostgreSQL, attribute name starting with numbers, like "360_degree" are not allowed.</li>
</ol>
  
Good luck!

