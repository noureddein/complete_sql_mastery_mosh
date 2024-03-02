## Writing Complex Query

-   **Sub-queries**
    -   sub-query is a query that run before the main query

```sql
    select
	*
from
	employees e
where
	e.salary > (
	select
		avg(e2.salary)
	from
		employees e2
)
```

-   **The IN Operator**

```sql
select
	p.product_id ,
	p.name
from
	products p
where
	p.product_id not in (
	SELECT
		oi.product_id
	from
		order_items oi)
```

-   **Sub-queries vs Joins**
    -   What is sub-query?
        -   A subquery is a query that is nested inside another query. It can be used to filter, aggregate, or calculate data from one or more tables. A subquery can return a single value, a list of values, or a table. For example, you can use a subquery to find the average salary of employees in a department, and then compare it with the salaries of individual employees in the same department.
    -   What is a join?
        -   A join is a query that combines data from two or more tables based on a common column or condition. It can be used to create a new table that contains data from multiple sources. A join can be inner, outer, left, right, or cross, depending on how you want to match the rows from different tables. For example, you can use a join to find the name and address of customers who ordered a specific product.
    -   Sub-query vs join
        -   **Performance**:One of the main factors to consider when choosing between subqueries and joins is performance. Generally speaking, joins are faster than subqueries, because they can use indexes and other optimization techniques. Subqueries, on the other hand, may require more processing and memory, especially if they return large or complex results. However, this is not always the case, and the performance may depend on the database engine, the data size, the query complexity, and the indexes available.
        -   **Readability**: Another factor to consider when choosing between subqueries and joins is readability. Readability refers to how easy it is to understand and maintain the query code. Generally speaking, subqueries are more readable than joins, because they can express complex logic in a simpler way. Subqueries can also avoid duplication and redundancy, by using the same query in different places. Joins, on the other hand, may require more code and conditions, which can make the query harder to follow and debug.
        -   **Flexibility**:A final factor to consider when choosing between subqueries and joins is flexibility. Flexibility refers to how easy it is to modify and adapt the query to different scenarios and requirements. Generally speaking, subqueries are more flexible than joins, because they can handle different types of data and operations. Subqueries can also be used in places where joins are not allowed, such as in the SELECT, HAVING, or ORDER BY clauses. Joins, on the other hand, may have some limitations and restrictions, depending on the type of join and the database engine.

```sql
-- Using Sub-query
select
	p.product_id ,
	p.name
from
	products p
where
	p.product_id not in ( SELECT oi.product_id from order_items oi)

-- Using Join
select
	*
from
	clients c
left join invoices i
		using(client_id)
where
	i.invoice_id is null
```

-   **The All Keyword**
    -   ALL means that the condition will be true only if the operation is true for all values in the range.
        -   Returns a boolean value as a result
        -   Returns TRUE if ALL of the subquery values meet the condition
        -   Is used with SELECT, WHERE and HAVING statements

```sql
select * from
	invoices i
where
	i.invoice_total > (
	select
		max(invoice_total)
	from
		invoices i
	where
		i.client_id = 3
);
-- The sub-query will return a single value, and each row will be compared with the
-- result of sub-query
-- e.g. 189 > 167
```

```sql
select * from
	invoices i
where
	i.invoice_total > ALL (
	select
		invoice_total
	from
		invoices i
	where
		i.client_id = 3
);
-- The sub-query will return an array of values (150, 130, 167, ...) and each
-- row will be compared with all the values of sub-query
-- e.g. 189 > ALL (150, 130, 167)
```

-   **The ANY or SOME Keyword**

    -   ANY means that the condition will be true if the operation is true for any of the values in the range.
        -   Returns a boolean value as a result
        -   Returns TRUE if ANY of the sub-query values meet the condition

```SQL
SELECT * from
	clients c
where
	client_id = ANY (
	select
		i.client_id
	from
		invoices i
	group by
		i.client_id
	HAVING
		count(i.invoice_id) >= 2
)
-- Any value that meet the condition of the sub-query will be returned
```

```sql
SELECT  * from
	clients c
where
	client_id IN (
	select
		i.client_id
	from
		invoices i
	group by
		i.client_id
	HAVING
		count(i.invoice_id) >= 2
)
```

-   **Correlated Sub-queries**
    -   Correlated subqueries are used for row-by-row processing. Each sub-query is executed once for every row of the outer query.

```sql
SELECT * from employees e where e.salary > (
	SELECT AVG(e.salary) from employees e where office_id = e.office_id
)
```

```sql
select * from invoices i1 where i1.invoice_total > (
    select AVG(i.invoice_total) from invoices i where i1.client_id  = client_id
)
```

-   **The EXIST Operator**
    -   The EXISTS operator is used to test for the existence of any record in a sub-query.
    -   The EXISTS operator returns TRUE if the sub-query returns one or more records.

```sql
select * from
	    clients c
    where
        c.client_id in ( SELECT client_id from invoices i )

select * from
	    clients c
    where EXISTS  ( SELECT client_id from invoices i where client_id = c.client_id )
```

```sql
select * from products p where NOT EXISTS (
	select oi.product_id from order_items oi where p.product_id = oi.product_id
)
```

-   **Sub-queries in the SELECT Clause**

```sql
select
	c.client_id,
	c.name,
	(SELECT sum(i.invoice_total) from invoices i where i.client_id = c.client_id) as total_sales,
    (SELECT avg(i2.invoice_total) from invoices i2 ) as average,
    (select total_sales - average) as difference
from clients c
```

-   **Sub-queries in the FORM Clause**
    -   The table must have an alias (e.g. sales_summary)

```sql
SELECT  * FROM
    (SELECT
        c.client_id,
        c.name,
        (SELECT SUM(i.invoice_total) FROM invoices i WHERE i.client_id = c.client_id) AS total_sales,
        (SELECT AVG(i2.invoice_total) FROM invoices i2) AS average,
        (SELECT total_sales - average) AS difference
    FROM
        clients c) AS sales_summary
WHERE
    total_sales IS NOT NULL
```
