---
layout: docu
railroad: statements/insert.js
redirect_from:
- docs/archive/0.9.2/sql/statements/insert
- docs/archive/0.9.1/sql/statements/insert
- docs/archive/0.9.0/sql/statements/insert
title: Insert Statement
---

The `INSERT` statement inserts new data into a table.

### Examples

```sql
-- insert the values (1), (2), (3) into "tbl"
INSERT INTO tbl VALUES (1), (2), (3);
-- insert the result of a query into a table
INSERT INTO tbl SELECT * FROM other_tbl;
-- insert values into the "i" column, inserting the default value into other columns
INSERT INTO tbl(i) VALUES (1), (2), (3);
-- explicitly insert the default value into a column
INSERT INTO tbl(i) VALUES (1), (DEFAULT), (3);
-- assuming tbl has a primary key/unique constraint, do nothing on conflict
INSERT OR IGNORE INTO tbl(i) VALUES(1);
-- or update the table with the new values instead
INSERT OR REPLACE INTO tbl(i) VALUES(1);
```

### Syntax

<div id="rrdiagram"></div>

`INSERT INTO` inserts new rows into a table. One can insert one or more rows specified by value expressions, or zero or more rows resulting from a query.

## Insert Column Order

It's possible to provide an optional insert column order, this can either be `BY POSITION` (the default) or `BY NAME`.
Each column not present in the explicit or implicit column list will be filled with a default value, either its declared default value or `NULL` if there is none.

If the expression for any column is not of the correct data type, automatic type conversion will be attempted.

### BY POSITION

The order that values are inserted into the columns of the table is determined by the order that the columns were declared in.
This can be overridden by providing column names as part of the target, for example:
```sql
CREATE TABLE tbl(a INTEGER, b INTEGER);
INSERT INTO tbl(b, a) VALUES (5, 42);
```
This will insert `5` into `b` and `42` into `a`.
The values supplied by the VALUES clause or query are associated with the column list left-to-right.

### BY NAME

The names of the column list of the SELECT statement are matched against the column names of the table to determine the order that values should be inserted into the table, even if the order of the columns in the table differs from the order of the values in the SELECT statement.
For example:
```sql
CREATE TABLE tbl(a INTEGER, b INTEGER);
INSERT INTO tbl BY NAME (SELECT 42 AS b);
```
This will insert `42` into `b` and insert `NULL` (or its default value) into `a`.

It's important to note that when using `INSERT INTO <table> BY NAME`, the column names specified in the SELECT statement must match the column names in the table.  
If a column name is misspelled or does not exist in the table, an error will occur.  

This is not a problem however if columns are missing from the SELECT statement, as those will be filled with the default value.

## ON CONFLICT Clause

An `ON CONFLICT` clause can be used to perform a certain action on conflicts that arise from `UNIQUE` or `PRIMARY KEY` constraints.

A `conflict_target` may also be provided, which is a group of columns that an Index indexes on, or if left out, all `UNIQUE` or `PRIMARY KEY` constraint(s) on the table are targeted.
The `conflict_target` is optional unless using a `DO UPDATE` (see below) and there are multiple unique/primary key constraints on the table.

When a conflict target is provided, you can further filter this with a `WHERE` clause, that should be met by all conflicts.
If a conflict does not meet this condition, an error will be thrown instead, and the entire operation is aborted.

Because we need a way to refer to both the **to-be-inserted** tuple and the **existing** tuple, we introduce the special `excluded` qualifier.
When the `excluded` qualifier is provided, the reference refers to the **to-be-inserted** tuple, otherwise it refers to the **existing** tuple
This special qualifier can be used within the `WHERE` clauses and `SET` expressions of the `ON CONFLICT` clause.

There are two supported actions:

1. `DO NOTHING`  
Causes the error(s) to be ignored, and the values are not inserted or updated.

2. `DO UPDATE`  
Causes the `INSERT` to turn into an `UPDATE` on the conflicting row(s) instead.  
The `SET` expressions that follow determine how these rows are updated.  
Optionally you can provide an additional `WHERE` clause that can exclude certain rows from the update.  
The conflicts that don't meet this condition are ignored instead.

`INSERT OR REPLACE` is a shorter syntax alternative to `ON CONFLICT DO UPDATE SET (c1 = excluded.c1, c2 = excluded.c2, ..)`.  
It updates every column of the **existing** row to the new values of the **to-be-inserted** row.
  
`INSERT OR IGNORE` is a shorter syntax alternative to `ON CONFLICT DO NOTHING`.


### RETURNING Clause

The `RETURNING` clause may be used to return the contents of the rows that were inserted. This can be useful if some columns are calculated upon insert. For example, if the table contains an automatically incrementing primary key, then the `RETURNING` clause will include the automatically created primary key. This is also useful in the case of generated columns.

Some or all columns can be explicitly chosen to be returned and they may optionally be renamed using aliases. Arbitrary non-aggregating expressions may also be returned instead of simply returning a column. All columns can be returned using the `*` expression, and columns or expressions can be returned in addition to all columns returned by the `*`.

```sql
-- A basic example to show the syntax
CREATE TABLE t1(i INT);
INSERT INTO t1 
    SELECT 1 
    RETURNING *;
```


| i |
|---|
| 1 |

```sql
-- A more complex example that includes an expression in the RETURNING clause
CREATE TABLE t2(i INT, j INT);
INSERT INTO t2 
    SELECT 2 AS i, 3 AS j 
    RETURNING *, i * j AS i_times_j;
```


| i | j | i_times_j |
|---|---|---|
| 2 | 3 | 6         |

This example shows a situation where the `RETURNING` clause is more helpful. First, a table is created with a primary key column. Then a sequence is created to allow for that primary key to be incremented as new rows are inserted. When we insert into the table, we do not already know the values generated by the sequence, so it is valuable to return them. For additional information, see the [`CREATE SEQUENCE` documentation](create_sequence).

```sql
CREATE TABLE t3(i INT PRIMARY KEY, j INT);
CREATE SEQUENCE 't3_key';
INSERT INTO t3 
    SELECT nextval('t3_key') AS i, 42 AS j 
    UNION ALL
    SELECT nextval('t3_key') AS i, 43 AS j
    RETURNING *;
```


| i | j |
|---|---|
| 1 | 42 |
| 2 | 43 |