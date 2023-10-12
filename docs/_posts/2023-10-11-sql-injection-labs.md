---
layout: post
title:  SQL Injection Labs
date:   2023-06-01 03:49:10 +0100
categories: sql-injection
post_id: 5
---

What follows is solution to various sql injection labs. The labs are from [Portswigger](https://portswigger.net) and [PentesterLab](https://pentesterlab.com).

## Portswigger Labs

### [Lab 1: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data](https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data)

### Task

***This lab contains a SQL injection vulnerability in the product category filter. When the user selects a category, the application carries out a SQL query like the following:***
```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```
***To solve the lab, perform a SQL injection attack that causes the application to display one or more unreleased products.***

### Solution
