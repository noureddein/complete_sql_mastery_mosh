## Summarizing Data

-   **Aggregate Functions**
    -   An aggregate function in SQL performs a calculation on multiple values and returns a single value.
    -   SQL provides many aggregate functions that include avg, count, sum, min, max, etc.
    -   An aggregate function ignores NULL values when it performs the calculation, except for the count function.
    -   The COUNT function calculate the duplicated records.
    -   **Functions:**
        -   Count()
        -   Sum()
        -   Avg()
        -   Min()
        -   Max()

```sql
SELECT
    MAX(invoice_total) AS highest,
    MIN(invoice_total) AS lowest,
    AVG(invoice_total) AS average
FROM
    sql_invoicing.invoices;
```

```sql
SELECT
	SELECT
    'First half of 2019' AS data_range,
    SUM(invoice_total) AS total_sales,
    SUM(payment_total) AS total_paymnets,
    SUM(invoice_total - payment_total) AS what_we_expect
FROM
    sql_invoicing.invoices
WHERE
    invoice_date BETWEEN '2019-01-01' AND '2019-06-30'
UNION SELECT
    'Second half of 2019' AS data_range,
    SUM(invoice_total) AS total_sales,
    SUM(payment_total) AS total_paymnets,
    SUM(invoice_total - payment_total) AS what_we_expect
FROM
    sql_invoicing.invoices
WHERE
    invoice_date BETWEEN '2019-07-01' AND '2019-12-31'
UNION SELECT
    'Total' AS data_range,
    SUM(invoice_total) AS total_sales,
    SUM(payment_total) AS total_paymnets,
    SUM(invoice_total - payment_total) AS what_we_expect
FROM
    sql_invoicing.invoices
WHERE
    invoice_date BETWEEN '2019-01-01' AND '2019-12-31';
```

-   **The GROUP BY Clause**
    -   Group by is one of the most frequently used SQL clauses. It allows you to collapse a field into its distinct values.
    -   This clause is most often used with aggregations to show one value per grouped field or combination of fields. We can use an SQL group by and aggregates to collect multiple types of information.

```sql
SELECT
	i.client_id ,
	sum(i.invoice_total) as total_sales
from
	invoices i
where i.invoice_date >= '2019-07-01'
group by i.client_id
ORDER  BY total_sales DESC

-- The Order of clauses are very important
```

-   **GROUP BY Multiple Columns**
    -   Is a combination of unique multiple columns

```sql
select
	c.city ,
	c.state ,
	sum(i.invoice_total)
from
	invoices i
join clients c
		using(client_id)
		group by c.state , c.city
```

```sql
select
	p.`date` ,
	pm.name ,
	sum(p.amount) as total_payment
from
	payments p
	Join payment_methods pm on pm.payment_method_id = p.payment_method
GROUP  BY
	p.`date` ,
	pm.name
ORDER  BY p.`date`
;
```

-   **HAVING Clause**
    -   The HAVING clause was added to SQL because the WHERE keyword cannot be used with aggregate functions.
    -   The HAVING clause differs from WHERE clause when to apply the condition(filter):
        -   WHERE clause applied before rows grouped
            -   With WHERE we can use the columns that are in or not included in the select clause
        -   HAVING clause applied after rows grouped
            -   With HAVING we can not use the columns that are not included in the select clause

```sql
SELECT
	i.client_id ,
	sum(i.invoice_total) as total_sales
from
	invoices i
group by i.client_id
HAVING total_sales > 500;
```

-   **The ROLLUP Operator**
    -   The ROLLUP is an extension of the GROUP BY clause. The ROLLUP option allows you to include extra rows that represent the subtotals, which are commonly referred to as super-aggregate rows, along with the grand total row. By using the ROLLUP option, you can use a single query to generate multiple grouping sets.
    -   The ROLLUP operator not applicable in all databases.

```sql
select
	pm.name as payment_method ,
	sum(p.amount)
from
	sql_invoicing.payments p
inner join sql_invoicing.payment_methods pm on
	p.payment_method = pm.payment_method_id
GROUP BY
	pm.name with ROLLUP
```
