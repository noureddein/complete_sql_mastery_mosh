# Retrieving Data From a Single Table

## Operators

-   Arithmetic Operators:
    -   The order of operators matter
    -   To override the default order we use parentheses
    -   The order of the operators are:
        -   `* /`
        -   `- +`

```
+ - * / %
```

-   Comparing Operators:
    -   The order of operators matter
    -   To override the default order we use parentheses

```sql
< <= > >= = !=
```

-   **Logical Operators**: Logical operators test for the truth of some condition. Logical operators, like comparison operators, return a Boolean data type with a value of TRUE, FALSE, or UNKNOWN.
    -   The order of operators matter
    -   To override the default order we use parentheses
    -   The order of the operators are:
        -   `AND`
        -   `OR`

```
AND
OR
NOT
IN
BETWEEN
LIKE
REGEXP
IS NULL
IS NOT NULL
```

-   **LIKE** operator has several patterns:

    -   `%b` select all the strings that end with letter `b`
    -   `b%` select all the strings that starts with letter `b`
    -   `%b%` select any strings that have the letter `b` with any number of characters before or after `b` letter
    -   `__y` select the string that has three characters and the last character is `y`, the underscore represent the number of characters

-   **REGEXP** operator has several patterns:

    -   `^field` select all the strings that starts with `field`
    -   `field$` select all the strings that end with `field`
    -   `field|mac` select all the strings that have `field` or `mac`
    -   `^field|mac|rose` select all the strings that start with `field` or have `mac` or `rose`
    -   `[gim]e` select any string that have in it on of these patters `ge`, `ie`, or `me`

    -   `[a-h]e` select any string with the range from `a` to `h` pattern, `ae`, `be`, `ce`, `de`, ... `he`

## **ORDER BY** Clause

-   The default order of any table depends on the primary key and the default order is `ASC`
-   The table can be order by multiple columns

```sql
SELECT
    *
FROM
    customers
ORDER BY state , first_name DESC;
```

-   MySQL database allow the user to order the table with the columns that are not in the select statement

```sql
SELECT
    first_name, last_name
FROM
    customers
ORDER BY state , first_name DESC;
```

## **LIMIT** Clause

-   Limit clause used to limit the retrieved rows.
-   Limit clause always must be at the end of the statement.

```sql
SELECT
    customer_id ,first_name, last_name, points
FROM
    customers
ORDER BY points desc limit 3
-- This statement will ony return the first 3 records
```

```sql
SELECT
    *
FROM
    customers
LIMIT 6, 3
-- This statement will escape the first 6 records and retrieve the three records after 6
```

## Examples

```sql
select * from customers where state <> 'VA';
select * from customers where state <> 'va';
-- upper case or lower case is not matter
```

```sql
select * from customers where birth_date > '1990-01-01';
-- The default format for representing dates in database is 'YYYY-MM-DD'
```

```sql
SELECT * FROM customers where phone is null;

SELECT * FROM customers where phone is not null;
```
