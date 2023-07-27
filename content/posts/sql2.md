---
title: "两个 SQL 问题"
date: 2023-07-27T22:13:51+08:00
reward: true
draft: false
description: ""
tags: []
categories: [MySQL]
---

<!--more-->

### union、union all、limit 问题

三个子查询，使用两个 `union` 或者两个 `union all` 时，在最后加上 `limit` 就是对整个查询语句的分页

如果使用一个 `union` 和一个 `union all` ，`limit` 就会失效变成对最后一个 SQL 的分页，可以在外层再写一个 `select * ` 然后 `limit` 分页

### or 的内连接问题

查询两个表的数据，错误 SQL 语句如下：

``` mysql
SELECT t.test_name, l.test_cat 
FROM t_test t, t_test_line l 
WHERE (t.test_ID = l.test_ID AND t.IS_DEL = '0' 
       AND l.IS_DEL = '0') OR t.test_no IN ('23453534' )
```

<br>

or 语句后面的条件，即使 t 表和 l 表的关联 id 不相等也会查出来数据，会导致 t 表的 23453534 数据和 l 表的每一行数据关联上都被查询出来	

改成使用 `inner join`，就会是正常的数据：

``` mysql
SELECT t.test_name, l.test_cat 
FROM t_test t INNER JOIN t_test_line l ON t.test_ID = l.test_ID 
WHERE (t.IS_DEL = '0' AND l.IS_DEL = '0') 
or t.test_no = '23453534'
```



