# SQL SELECT Statement

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

After executing the SQL statements listed above, clear the query panel to remove the statements:  

   ![clear query](https://curriculum-content.s3.amazonaws.com/6036/sql-select-statement/clearquery.png)    

Select Yes to discard the changes (this discards changes to the query panel editor, not changes to the table)

   ![discard statements](https://curriculum-content.s3.amazonaws.com/6036/sql-select-statement/confirmdiscard.png)  

Confirm the table contains all 8 rows by selecting all rows and columns:

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

<pre>
<code>
SELECT *
FROM pet;
</code>
</pre>

</td>
<td>

<pre>
id  name        species	breed	            age

1   Moe         cat     Tabby	            10
2   Hana        cat     Tabby	            1
3   Lil' Bub    cat     American Shorthair  5
4   Snuggles    dog     Bichon	            5
5   Honey       dog     Cavachon            NULL
6   Herbie      fish    NULL                4
7   Fifi        dog     Poodle              1
8   Lulu        dog     Yorkiepoo           12
</pre>

</td>
</tr>



<tr>
<td>
Display a subset of columns:

<pre>
<code>
SELECT name, species
FROM pet;
</code>
</pre>

</td>
<td>

<pre>
name        species

Moe         cat
Hana        cat
Lil' Bub    cat
Snuggles    dog
Honey       dog
Herbie      fish
Fifi        dog
Lulu        dog
</pre>

</td>
</tr>

<tr>
<td>
Display unique values within a column:

<pre>
<code>
SELECT DISTINCT species
FROM pet;
</code>
</pre>

</td>
<td>

<pre>
species

dog
cat
fish
</pre>

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

<pre>
<code>
SELECT name, age
FROM pet
WHERE age < 5;
</code>
</pre>

</td>
<td>

<pre>
name    age

Hana    1
Herbie  4
Fifi    1
</pre>

</td>
</tr>



<tr>
<td>
Display name, breed, age of all cats:

<pre>
<code>
SELECT name, breed, age
FROM pet
WHERE species = 'cat';
</code>
</pre>

Notice SQL uses only one equal sign to
test for equality.

</td>
<td>

<pre>
name        breed               age

Moe         Tabby               10
Hana        Tabby               1
Lil' Bub    American Shorthair  5
</pre>

</td>
</tr>


<tr>
<td>
Display name, species of any
pet that is not a cat:

<pre>
<code>
SELECT name, species
FROM pet
WHERE species != 'cat';
</code>
</pre>

</td>
<td>

<pre>
name        species

Snuggles    dog         
Honey       dog
Herbie      fish
Fifi        dog
Lulu        dog   
</pre>

</td>
</tr>


<tr>
<td>
Display name, breed, and age of
cats younger than 5:

<pre>
<code>
SELECT name, breed, age
FROM pet
WHERE species = 'cat' AND age < 5;
</code>
</pre>

</td>
<td>

<pre>
name        breed               age

Hana         Tabby               1
</pre>

</td>
</tr>



<tr>
<td>
Display name and age of pets between<br>
4 and 10 years old (inclusive):

<pre>
<code>
SELECT name, age
FROM pet
WHERE age >= 4 AND age <= 10;
</code>
</pre>

</td>
<td>

<pre>
name        age

Moe         10
Lil' Bub    5
Snuggles    5
Herbie      4
</pre>

</td>
</tr>


<tr>
<td>
The BETWEEN keyword can also be used to<br>specify
a range of ages (inclusive):

<pre>
<code>
SELECT name,age
FROM pet
WHERE age BETWEEN 4 AND 10;
</code>
</pre>

</td>
<td>

<pre>
name        age

Moe         10
Lil' Bub    5
Snuggles    5
Herbie      4
</pre>

</td>
</tr>


<tr>
<td>
Display pets whose breed is
Bichon or Cavachon:

<pre>
<code>
SELECT name, breed
FROM pet
WHERE breed = 'Bichon' OR breed = 'Cavachon';
</code>
</pre>

</td>
<td>

<pre>
name        breed

Snuggles    Bichon
Honey       Cavachon
</pre>

</td>
</tr>

<tr>
<td>
The IN keyword can also be
used to match a list of possible values:

<pre>
<code>
SELECT name, breed
FROM pet
WHERE breed IN ( 'Bichon' , 'Cavachon' );
</code>
</pre>

</td>
<td>

<pre>
name        breed

Snuggles    Bichon
Honey       Cavachon
</pre>

</td>
</tr>




<tr>
<td>
The NOT keyword retrieves all rows that<br>
do not match the predicate:

<pre>
<code>
SELECT name, species
FROM pet
WHERE species NOT IN ( 'dog' , 'cat' );
</code>
</pre>

</td>
<td>

<pre>
name    species

Herbie  fish
</pre>

</td>
</tr>



<tr>
<td>
The NULL keyword is used to
test<br> for missing or unknown values:

<pre>
<code>
SELECT id, name, age
FROM pet
WHERE age IS NULL;
</code>
</pre>

</td>
<td>

<pre>
id  name    age

5   Honey   null
</pre>

</td>
</tr>


<tr>
<td>
Use NOT NULL to
test for known values:

<pre>
<code>
SELECT id, name, age
FROM pet
WHERE age IS NOT NULL;
</code>
</pre>

</td>
<td>

<pre>
id  name        age
1   Moe         10
2   Hana        1
3   Lil' Bub    5
4   Snuggles    5
6   Herbie      4
7   Fifi        1
8   Lulu        12   
</pre>

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

<pre>
<code>
SELECT name, breed
FROM pet
WHERE breed LIKE '%chon';
</code>
</pre>

</td>
<td>

<pre>
name        breed

Snuggles    Bichon
Honey       Cavachon
</pre>

</td>
</tr>


<tr>
<td>
Display name and breed
where breed contains<br> the
substring "poo" (case sensitive).


<pre>
<code>
SELECT name, breed
FROM pet
WHERE breed LIKE '%poo%';
</code>
</pre>

</td>
<td>

<pre>
name   breed

Lulu    Yorkiepoo
</pre>

</td>
</tr>

<tr>
<td>
Display name and breed
where breed contains <br>the
substring "poo" (case insensitive match).

<pre>
<code>
SELECT name, breed
FROM pet
WHERE LOWER(breed) LIKE '%poo%';
</code>
</pre>

</td>
<td>

<pre>
name   breed

Fifi    Poodle
Lulu    Yorkiepoo
</pre>

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

<pre>
<code>
SELECT name, age
FROM pet
WHERE species = 'cat'
ORDER BY age;
</code>
</pre>

</td>
<td>

<pre>
name        age

Hana        1
Lil' Bub    5
Moe         10
</pre>

</td>
</tr>


<tr>
<td>
Sort in descending order of age:

<pre>
<code>
SELECT name, age
FROM pet
WHERE species = 'cat'
ORDER BY age DESC;
</code>
</pre>

</td>
<td>

<pre>
name        age

Moe         10
Lil' Bub    5
Hana        1
</pre>

</td>
</tr>

<tr>
<td>
Primary sort in ascending order by species.

Secondary sort in descending order by age.

The `ASC` keyword can be omitted:

<pre>
<code>
SELECT name, species, age
FROM pet
ORDER BY species ASC, age DESC;
</code>
</pre>

NOTE: the `null` value is ordered as the largest dog age.
</td>
<td>

<pre>
name        species age

Moe         cat     10
Lil'Bub     cat     5
Hana        cat     1
Honey       dog     null  
Lulu        dog     12
Snuggles    dog     5
Fifi        dog     1
Herbie      fish    4
</pre>

</td>
</tr>


<tr>
<td>
Omit rows that
contain null values when sorting:

<pre>
<code>
SELECT name, species, age
FROM pet
WHERE age IS NOT NULL
ORDER BY species ASC, age DESC;
</code>
</pre>

</td>
<td>

<pre>
name        species age

Moe         cat     10
Lil'Bub     cat     5
Hana        cat     1
Lulu        dog     12
Snuggles    dog     5
Fifi        dog     1
Herbie      fish    4
</pre>

</td>
</tr>


<tr>
<td>
The LIMIT keyword restricts the
number of rows displayed.  

View the 
2 oldest pets by limiting the number
of rows to display:

<pre>
<code>
SELECT name, age
FROM pet
WHERE age IS NOT NULL
ORDER BY age DESC
LIMIT 2;
</code>
</pre>

</td>
<td>

<pre>
name    age

Lulu    12
Moe     10
</pre>

</td>
</tr>




</table>


## Conclusion

The SQL `SELECT` statement is a flexible and powerful language construct
for retrieving rows from a database table.  We can use the `WHERE`
clause to retrieve a subset of rows that match a predicate.  The `ORDER BY`
clause will sort the rows according to one or more columns.

## Resources

- [PostgreSQL SELECT](https://www.postgresql.org/docs/current/sql-select.html)     
- [PostgreSQL Practice Exercises](https://pgexercises.com/)  
