# SQL Select Statement

## Learning Goals

- Define a query as an SQL SELECT statement that retrieves data from a database.
- Use a comma-separated column list to display a subset of columns for a query.
- Use the DISTINCT keyword to select unique values within a column.
- Use the WHERE clause to select rows that match a predicate.
- Use the IN keyword to retrieve rows that contain a value within a list of values.
- Use the BETWEEN modifier to retrieve rows that contain a value that is within a given range.
- Use the NULL data type keyword to retrieve rows containing a null value.
- Use the ORDER BY modifier to order tables by specific SELECT statements.
- Use the ASC and DESC modifiers to sort queries in ascending or descending order.
- Use the LIMIT modifier to determine the number of rows to retrieve.

## Introduction

The SQL SELECT statement retrieves one or more table rows based on matching
a predicate.  

The general syntax for selecting rows from a single table is:

```sql
SELECT [comma-separated list of columns]
FROM [table_name]
WHERE [row selection predicate]
ORDER BY [comma-separated list of columns]
LIMIT [count];
```

- The `WHERE` clause is optional.  The clause is used to select a subset of rows that match
  the predicate.  If omitted, all rows are retrieved.
- The `ORDER BY` clause is optional.  The clause is used to sort the result set based on
  the order of one or more columns.  If omitted, the row order is determined by the DBMS.
  While some use  a default order based on the primary key, PostgreSQL displays rows in
  chronological order of insertions and updates.
- The `LIMIT` clause is optional. The clause restricts the number of rows displayed in the result.


## Table Setup  (Code Along)

We will work with the same `pet` table from the previous lesson.  
Copy and execute the following SQL statements in the **pgAdmin**
query tool to ensure we are starting with the same of rows.
Note that we've added two additional dogs, a Poodle and a YorkiePoo.
We will do some pattern matching using their breed names.

```sql
DROP TABLE IF EXISTS pet;

CREATE TABLE pet (
        id  INTEGER PRIMARY KEY,
        name TEXT,
        species TEXT,
        breed TEXT,
        age INTEGER
);

INSERT INTO pet (id, name, species, breed, age) 
VALUES (1, 'Moe', 'cat', 'Tabby', 10);

INSERT INTO pet (id, name, species, breed, age) 
VALUES (2, 'Hana', 'cat', 'Tabby', 1);

-- Use two single quotes '' to escape a single quote character in the name
INSERT INTO pet (id, name, species, breed, age) 
VALUES (3, 'Lil'' Bub', 'cat', 'American Shorthair', 5);

INSERT INTO pet (id, name, species, breed, age) 
VALUES (4, 'Snuggles', 'dog', 'Bichon', 5);

--unknown age
INSERT INTO pet (id, name, species, breed)
VALUES (5, 'Honey', 'dog', 'Cavachon');

-- unknown breed
INSERT INTO pet (id, name, species, age)
VALUES (6, 'Herbie', 'fish', 4);

INSERT INTO pet (id, name, species, breed, age) 
VALUES (7, 'Fifi', 'dog', 'Poodle', 1);

INSERT INTO pet (id, name, species, breed, age) 
VALUES (8, 'Lulu', 'dog', 'Yorkiepoo', 12);
```

1. After executing the SQL statements listed above, clear the query panel to remove the statements:  

   ![clear query](https://curriculum-content.s3.amazonaws.com/6036/sql-select-statement/clearquery.png)    

2. Select Yes to discard the changes (this discards changes to the query panel editor, not changes to the table)

   ![discard statements](https://curriculum-content.s3.amazonaws.com/6036/sql-select-statement/confirmdiscard.png)  

3. Confirm the table contains all 8 rows by selecting all rows and columns:

   ![confirm pet table rows](https://curriculum-content.s3.amazonaws.com/6036/sql-select-statement/8pets.png)  

## SELECT Statement (Code Along)

Execute each `SELECT` statement in the query tool to practice writing queries.

### SELECT one or more columns

An asterisk character `*` is used to display all columns.  

We can provide a comma-separated list of columns to display a subset
of columns.  

We can use the keyword `distinct` to display
unique values within a column.


<table>
<tr>
<th>
Query
</th>
<th>
Result
</th>
</tr>

<tr>
<td>
Display all columns and rows:

```sql
SELECT *
FROM pet;
```

</td>
<td>

```text
id  name        species	breed	            age

1   Moe         cat     Tabby	            10
2   Hana        cat     Tabby	            1
3   Lil' Bub    cat     American Shorthair  5
4   Snuggles    dog     Bichon	            5
5   Honey       dog     Cavachon            NULL
6   Herbie      fish    NULL                4
7   Fifi        dog     Poodle              1
8   Lulu        dog     Yorkiepoo           12
```

</td>
</tr>



<tr>
<td>
Display a subset of columns:

```sql
SELECT name, species
FROM pet;
```

</td>
<td>

```text
name        species

Moe         cat
Hana        cat
Lil' Bub    cat
Snuggles    dog
Honey       dog
Herbie      fish
Fifi        dog
Lulu        dog
```

</td>
</tr>

<tr>
<td>
Display unique values within a column:

```sql
SELECT DISTINCT species
FROM pet; 
```

</td>
<td>

```text
species

dog
cat
fish
```

</td>
</tr>

</table>


### WHERE clause

The `WHERE` clause is used to retrieve a subset of rows
that match a given predicate.  The predicate is a boolean
expression that compares column values using:

- Relational operators `=`, `!=`, `<`, `<=`, `>`, `>=`
- Boolean operators `AND`, `OR`, `NOT`
- Shorthand boolean operators `IN`, `NOT IN`
- Shorthand range operator `BETWEEN`
- Null operator `IS NULL` and `IS NOT NULL`
- Pattern matching operator `LIKE`

<table>
<tr>
<th>
Query
</th>
<th>
Result
</th>
</tr>

<tr>
<td>
Display name and age of pets younger than 5:

```sql
SELECT name, age
FROM pet
WHERE age < 5;
```

</td>
<td>

```text
name    age

Hana    1
Herbie  4
Fifi    1
```

</td>
</tr>



<tr>
<td>
Display name, breed, age of all cats:

```sql
SELECT name, breed, age
FROM pet
WHERE species = 'cat';
```

Notice SQL uses only one equal sign to
test for equality.

</td>
<td>

```text
name        breed               age

Moe         Tabby               10
Hana        Tabby               1
Lil' Bub    American Shorthair  5
```

</td>
</tr>


<tr>
<td>
Display name, species of any
pet that is not a cat:

```sql
SELECT name, species
FROM pet
WHERE species != 'cat';
```

</td>
<td>

```text
name        species

Snuggles    dog         
Honey       dog
Herbie      fish
Fifi        dog
Lulu        dog   
```

</td>
</tr>


<tr>
<td>
Display name, breed, and age of
cats younger than 5:

```sql
SELECT name, breed, age
FROM pet
WHERE species = 'cat' AND age < 5;
```

</td>
<td>

```text
name        breed               age

Hana         Tabby               1
```

</td>
</tr>



<tr>
<td>
Display name and age of
pets between 4 and 10 years old (inclusive):

```sql
SELECT name, age
FROM pet
WHERE age >= 4 AND age <= 10;
```

</td>
<td>

```text
name        age

Moe         10
Lil' Bub    5
Snuggles    5
Herbie      4
```

</td>
</tr>


<tr>
<td>
The BETWEEN keyword
can also be used to specify
a range of ages (inclusive):

```sql
SELECT name,age
FROM pet
WHERE age BETWEEN 4 AND 10;
```

</td>
<td>

```text
name        age

Moe         10
Lil' Bub    5
Snuggles    5
Herbie      4
```

</td>
</tr>





<tr>
<td>
Display pets whose breed is
Bichon or Cavachon:

```sql
SELECT name, breed
FROM pet
WHERE breed = 'Bichon' OR breed = 'Cavachon';
```

</td>
<td>

```text
name        breed

Snuggles    Bichon
Honey       Cavachon
```

</td>
</tr>

<tr>
<td>
The IN keyword can also be
used to match a list of possible values:

```sql
SELECT name, breed
FROM pet
WHERE breed IN ( 'Bichon' , 'Cavachon' );
```

</td>
<td>

```text
name        breed

Snuggles    Bichon
Honey       Cavachon
```

</td>
</tr>




<tr>
<td>
The NOT keyword retrieves all rows that
do not match the predicate:

```sql
SELECT name, species
FROM pet
WHERE species NOT IN ( 'dog' , 'cat' );
```

</td>
<td>

```text
name    species

Herbie  fish
```

</td>
</tr>



<tr>
<td>
The NULL keyword is used to
test for missing or unknown values:

```sql
SELECT id, name, age
FROM pet
WHERE age IS NULL;
```

</td>
<td>

```text
id  name    age

5   Honey   null
```

</td>
</tr>


<tr>
<td>
Use NOT NULL to
test for known values:

```sql
SELECT id, name, age
FROM pet
WHERE age IS NOT NULL;
```

</td>
<td>

```text
id  name        age
1   Moe         10
2   Hana        1
3   Lil' Bub    5
4   Snuggles    5
6   Herbie      4
7   Fifi        1
8   Lulu        12   
```

</td>
</tr>

</table>



### Pattern Matching


The `LIKE` keyword is used in a WHERE clause to look for a pattern
in a column.  We can use two wildcards with LIKE:

- `%`  Represents zero, one, or multiple characters   
- `_`  Represents a single character


For example:

- `'%abc'` matches words that end in "abc".
- `'abc%'` matches words that start with "abc".
- `'%abc%'` matches words that contain "abc" as a substring.
- `'a_b'` matches 3 letter words that start with "a" and end with "b".

We can also use functions that convert a string to lowercase or
uppercase to perform case-insensitive matching:

- `LOWER(str)` - return the lowercase version of string `str`.
- `UPPER(str)` - return the uppercase version of string `str`.

<table>
<tr>
<th>
Query
</th>
<th>
Result
</th>
</tr>

<tr>
<td>
Display name and breed
where breed ends in "chon":

```sql
SELECT name, breed
FROM pet
WHERE breed LIKE '%chon';
```

</td>
<td>

```text
name        breed

Snuggles    Bichon
Honey       Cavachon
```

</td>
</tr>


<tr>
<td>
Display name and breed
where breed contains the
substring "poo" (case sensitive).


```sql
SELECT name, breed
FROM pet
WHERE breed LIKE '%poo%';
```

</td>
<td>

```text
name   breed

Lulu    Yorkiepoo
```

</td>
</tr>

<tr>
<td>
Display name and breed
where breed contains the
substring "poo" (case insensitive match).

```sql
SELECT name, breed
FROM pet
WHERE LOWER(breed) LIKE '%poo%';
```

</td>
<td>

```text
name   breed

Fifi    Poodle
Lulu    Yorkiepoo
```

</td>
</tr>

</table>



## ORDER BY

The `ORDER BY` clause sorts the retrieved rows by order of one or more columns.
The optional keywords `ASC` and `DESC` will sort respectively
in ascending or descending order.  Rows are sorted in ascending order by default.

The `ORDER BY` clause follows the `WHERE` clause, if there is one.

<table>
<tr>
<th>
Query
</th>
<th>
Result
</th>
</tr>

<tr>
<td>
Sort in ascending order of age:

```sql
SELECT name, age
FROM pet
WHERE species = 'cat'
ORDER BY age;
```

</td>
<td>

```text
name        age

Hana        1
Lil' Bub    5
Moe         10
```

</td>
</tr>


<tr>
<td>
Sort in descending order of age:

```sql
SELECT name, age
FROM pet
WHERE species = 'cat'
ORDER BY age DESC;
```

</td>
<td>

```text
name        age

Moe         10
Lil' Bub    5
Hana        1
```

</td>
</tr>

<tr>
<td>
Primary sort in ascending order by species.

Secondary sort in descending order by age.

The `ASC` keyword can be omitted:

```sql
SELECT name, species, age
FROM pet
ORDER BY species ASC, age DESC;
```

NOTE: the `null` value is ordered as the largest dog age.
</td>
<td>

```text
name        species age

Moe         cat     10
Lil'Bub     cat     5
Hana        cat     1
Honey       dog     null  
Lulu        dog     12
Snuggles    dog     5
Fifi        dog     1
Herbie      fish    4
```

</td>
</tr>


<tr>
<td>
Omit rows that
contain null values when sorting:

```sql
SELECT name, species, age
FROM pet
WHERE age IS NOT NULL
ORDER BY species ASC, age DESC;
```

</td>
<td>

```text
name        species age

Moe         cat     10
Lil'Bub     cat     5
Hana        cat     1
Lulu        dog     12
Snuggles    dog     5
Fifi        dog     1
Herbie      fish    4
```

</td>
</tr>


<tr>
<td>
The LIMIT keyword restricts the
number of rows displayed.  

View the 
2 oldest pets by limiting the number
of rows to display:

```sql
SELECT name, age
FROM pet
WHERE age IS NOT NULL
ORDER BY age DESC
LIMIT 2;
```

</td>
<td>

```text
name    age

Lulu    12
Moe     10
```

</td>
</tr>




</table>


## Conclusion

The SQL `SELECT` statement is a flexible and powerful language construct
for retrieving rows from a database table.  We can use the `WHERE`
clause to retrieve a subset of rows that match a predicate.  The `ORDER BY`
clause will sort the rows according to one or more columns.

## Resources

[PostgreSQL SELECT](https://www.postgresql.org/docs/current/sql-select.html)
