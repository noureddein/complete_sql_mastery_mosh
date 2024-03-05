## Essential MySQL Functions

-   **Numeric Functions**

    -   ROUND(x, y): **x** is the number, **y**:decimal places
    -   TRUNCATE(x, y): **x** is the number, **y**:decimal places
    -   CEILING(X): **x** is the number, Returns the smallest integer value not less than X. Returns NULL if X is NULL. (e.g. CEILING(1.23): -> 2, CEILING(-1.23): -> -1)
    -   FLOOR(x): **x** is the number, Returns the largest integer value not greater than X. Returns NULL if X is NULL. (e.g. SELECT FLOOR(1.23), FLOOR(-1.23): -> 1, -2)
    -   ABS(x): **x** is the number, Return the absolute value.

    > For more numeric functions [Link](https://dev.mysql.com/doc/refman/8.0/en/numeric-functions.html "MySQL Numeric Functions")

-   **String Functions**
    -   LENGTH('Kindergarten') -> 12
    -   LOWER('Kindergarten') -> kindergarten
    -   UPPER('kindergarten') -> KINDERGARTEN
    -   Remove unnecessary spaces
        -   LTRIM(' kindergarten')
        -   RTRIM('kindergarten ')
        -   TRIM(' kindergarten ')
    -   String splitting
        -   LEFT('kindergarten', 4) -> kind
        -   RIGHT('kindergarten', 6) -> garten
        -   SUBSTRING(string, start_position, length?)
            -   e.g. SUBSTRING('kindergarten', 3, 7) -> ndergar
            -   e.g. SUBSTRING('kindergarten', 4, 40) -> dergarten
            -   e.g. SUBSTRING('kindergarten', 5) -> ergarten
    -   LOCATE(search_string, string): Return the first occurrence of the character, not case sensitive
        -   LOCATE('n', 'kindergarten') -> 3
        -   LOCATE('q', 'kindergarten') -> 0
        -   LOCATE('garten', 'kindergarten') -> 7 -> the search_string starts from position 7
    -   REPLACE(str, from_str, to_str)
        -   REPLACE('kindergarten', 'garten', 'garden') -> kindergarden
    -   CONCAT()
        -   CONCAT('My', 'S', 'QL') -> MySQL

> For more string functions [Link](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html "MySQL String Functions")

-   **Date Functions**
    -   NOW() -> get now date and time -> 2024-03-02 17:42:09
    -   CURDATE() -> get the now date -> 2024-03-02
    -   CURTIME() -> get the now time -> 2024-03-02
    -   DAY(NOW()) -> extract the day -> 2
    -   MONTH(NOW()) -> extract the month -> 3
    -   YEAR(NOW()) -> extract the year -> 2024
    -   HOUR(NOW())
    -   MINUTE(NOW())
    -   SECOND(NOW())
    -   DAYNAME(NOW()) -> Saturday
    -   MONTH(NOW()) -> March
    -   EXTRACT(unit FROM date) -> This function is a part of the standard SQL language
        -   EXTRACT(YEAR FROM NOW()): 2024

```sql
SELECT * FROM
    orders
WHERE
    EXTRACT(YEAR FROM order_date) = YEAR(NOW());
```

-   **Formatting Dates and Times**
    -   DATE_FORMAT(date_value, format_string)
        -   DATE_FORMAT('2020-03-19', '%y') -> 20
        -   DATE_FORMAT('2020-03-19', '%Y') -> 2020
        -   DATE_FORMAT('2020-03-19', '%Y') -> 2020
        -   DATE_FORMAT('2020-03-19', '%m %Y') -> 03 2020
        -   DATE_FORMAT('2020-03-19', '%M %Y') -> March 2020
        -   DATE_FORMAT('2020-03-19', '%d %M %Y') -> 19 March 2020
        -   DATE_FORMAT('2020-03-19', '%D %M %Y') -> 19th March 2020
    -   TIME_FORMAT(date_value, format_string)
        -   TIME_FORMAT(NOW(), '%h:%i %p') -> 09:32 PM

> For more date and time specifiers [Link](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html#function_date-format "Date and Time specifiers")

-   **Calculating Dates and Times**

    -   Date calculation
        -   DATE_ADD('2024-03-02 21:40:25', INTERVAL 1 DAY) -> 2024-03-03 21:40:25
        -   SELECT NOW(), date_add('2024-03-20', interval 1 MONTH) -> 2024-04-20
        -   SELECT NOW(), date_add('2024-03-20', interval -1 YEAR) -> 2023-03-20
        -   SELECT NOW(), date_add('2024-03-20', interval -1 YEAR) -> 2023-03-20
        -   SELECT NOW(), date_sub('2024-03-20', interval 1 year) -> 2023-03-20
        -   SELECT DATEDIFF('2019-01-05', '2019-01-01') -> 4
    -   Time calculation
        -   SELECT TIME_TO_SEC('9:03') - TIME_TO_SEC('9:00') -> 180

-   **The IFNULL and COALESCE Functions**
    -   IFNULL: We can change the value of null for the null cells.
    -   COALESCE: Used to replace the null value with other columns values unless these columns are null,

```sql
-- IFNULL Function
SELECT
    o.order_id, IFNULL(o.shipper_id, 'Not assigned')
FROM
    orders o;

-- COALESCE Function
SELECT
    o.order_id,
    IFNULL(o.shipper_id, 'Not assigned'),
    COALESCE(o.shipper_id, comments, 'Not assigned')
FROM
    orders o;
```

-   **The IF function**
    -   The IF() function returns a value if a condition is TRUE, or another value if a condition is FALSE.

```sql
IF(expression, if_true, if_false)
```

```sql
SELECT
    order_id,
    order_date,
    IF
    (
        YEAR(order_date) = YEAR(NOW()),
        'Active',
        'Archived'
    ) as category
FROM
    sql_store.orders;
```

```sql
SELECT
    p.product_id,
    p.name,
    COUNT(*) AS orders,
    (IF(COUNT(*) > 1, 'Many times', 'Once')) AS frequency
FROM
    products p
        JOIN
    order_items oi USING (product_id)
GROUP BY p.product_id , p.name;
```

-   **The CASE Operator**
    -   The CASE expression goes through conditions and returns a value when the first condition is met (like an if-then-else statement). So, once a condition is true, it will stop reading and return the result. If no conditions are true, it returns the value in the ELSE clause.
    -   If there is no ELSE part and no conditions are true, it returns NULL.

```sql
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    WHEN conditionN THEN resultN
    ELSE result
END;
```

```sql
select
	order_id,
     CASE
		WHEN YEAR(now())     = YEAR(order_date)   THEN 'ACTIVE'
        WHEN YEAR(now()) - 1 = YEAR(order_date)   THEN 'Last Year'
        WHEN YEAR(now()) - 1 < YEAR(order_date)   THEN 'Archived'
        ELSE 'Future'
	END AS category
from
	orders;
SELECT
    p.product_id,
    p.name,
    COUNT(*) AS orders,
    (IF(COUNT(*) > 1, 'Many times', 'Once')) AS frequency
FROM
    products p
JOIN
    order_items oi USING (product_id)
GROUP BY p.product_id , p.name;
```

```sql
SELECT
    CONCAT(c.first_name, ' ', c.last_name) AS full_name,
    c.points,
    CASE
        WHEN points > 3000 THEN 'Gold'
        WHEN points BETWEEN 2000 AND 3000 THEN 'Silver'
        WHEN points < 2000 THEN 'Bronze'
    END AS category
FROM
    sql_store.customers c
ORDER BY points DESC;
```
