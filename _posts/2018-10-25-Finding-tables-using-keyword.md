---
title: "Finding tables in a database that have keyword in table name"
categories:
  - Data
tags:
  - sql
  - data
toc: true
header:
  teaser: "assets/images/post_images/findingdata.jpg"
  caption: "Credit: www.exterro.com"
excerpt: Need help finding a table lost in a huge database? No Problem.
---

After taking 2 courses on Coursera, and spent several days working on problem sets on W3School, Hackerrank, and Leetcode, I thought I had everything I need to be successful at my new job, which involves a lot of data <abbr title="Extract, Transform, and Load">ETL</abbr> at multiple databases, each contains tens of schemas and hundreds/thousands of tables. Given my unfamiliarity with the database, trying to find a table can be difficult. However, this snippet became a life saver and turned out super helpful for me to navigate through the complexity of the database.

## Why this can be useful

Because nobody knows every single table by its name. In general, database in real life are much bigger and more comoplicated than ones you deal with in tutorials or classes.

Tutorials/Classes|Real life
---|---
Small database, simple schema, very few tables|Multiple large databases, hundreds/thousands of tables
All tables follow a naming convention that is consistent and self-explanatory|Depends
Instructor knows what he/she has in the database|"Legacy stuff? Nobody knows what's in there"

## How to do it

### In SQL server

```sql
SELECT *, LOWER(table_catalog + '.' + table_schema + '.' + table_name) 
FROM INFORMATION_SCHEMA.TABLES 
WHERE table_name LIKE '%keyword%' 
ORDER BY table_name
```

### In Postgres SQL

```sql
SELECT CONCAT(table_schema, '','', table_name) AS table_name
FROM information_schema.tables
WHERE table_name LIKE '%keyword%'
ORDER BY table_schema,table_name;'
```

### In MySQL (untested)

```sql
SELECT table_name
FROM INFORMATION_SCHEMA.tables 
WHERE table_name LIKE '%keyword%'
```

### In Oracle (untested)

```sql
SELECT table_name 
FROM all_tab_columns 
WHERE table_name = '%keyword%';
```

If you have DBA privileges, you can try this command instead:

```sql
SELECT table_name 
FROM dba_tab_columns 
WHERE table_name = '%keyword%';
```
