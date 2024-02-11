## Retrieving Data From Multiple Tables

![Database Joins!](/imgs/SQL-JOINS.png "Database Joins")

-   **Inner Join**
    -   Inner joins combine records from two tables whenever there are matching values in a field common to both tables.

```sql
SELECT
    o.order_id, c.customer_id
FROM
    orders o
        INNER JOIN
    customers c ON c.customer_id = o.customer_id
```

-   **Joining across databases**

```sql
select
	*
from
	sql_store.order_items oi
inner join sql_inventory.products p on
	p.product_id = oi.product_id;
```

-   **Self-Join**

    -   self-join is same as inner join, the table have a primary key and a pointer to that key in the same tale.For example each employee has a manager, but the manager himself is a employee, so we create a column in the employee table called manager_id.

```sql
select
    e.first_name,
    e.last_name,
    e.job_title,
    m.first_name as manager
from
    employees e
inner join
    employees m on
    e.reports_to = m.employee_id ;
```

-   **Joining Multiple Tables**

```sql
select
	o.order_id ,
	o.order_date,
	c.first_name ,
	c.last_name ,
	os.name as Status
from
	orders o
join customers c on
	c.customer_id = o.customer_id
join order_statuses os on
	os.order_status_id = o.status
```

-   **Compound Join Conditions**
    -   **order_items** table have a composed primary keys, which mean the table have more than one primary key.

```sql
SELECT
	*
FROM
	order_items oi
join
	order_item_notes oin on
	oi.order_id = oin.order_Id
	AND oi.product_id = oin.product_id ;
```

-   **Implicit Join Syntax**
    -   Should avoid using the implicit join syntax because if the where clause forgotten, the database will join every records in the two tables.

```sql
-- Explicit Join Syntax
select * from orders o join customers c on c.customer_id =o.customer_id ;

-- Implicit Join Syntax
select * from orders o , customers c WHERE  c.customer_id =o.customer_id ;
```

-   **Outer Join**
    -   An outer join is a method of combining two or more tables so that the result includes unmatched rows of one of the tables, or of both tables.

```sql
select
	c.customer_id ,
	c.first_name ,
	o.order_id
from
	orders o
right join customers c on
	c.customer_id = o.customer_id  ;
```

-   **Outer Join Between Multiple Tables**

```sql
select
	o.order_date ,
	o.order_id,
	c.first_name ,
	s.name as shipper,
	os.name as status
from
	customers c
join orders o on
	c.customer_id = o.customer_id
left join shippers s on
	 o.shipper_id = s.shipper_id
join order_statuses os on
	o.status = os.order_status_id
```

-   **Self Outer Join**

```sql
select
	e.employee_id ,
	e.first_name ,
	e.reports_to,
	m.first_name  as manager,
	e.job_title
from
	employees e
left join employees m on
	e.reports_to = m.employee_id
order by e.employee_id;
```

-   **The USING Clause**
    -   If the primary key and the pointer key have the same name, then we can use the _USING_ clause. `USING (column_id)`.

```sql
select
	e.employee_id ,
	e.first_name ,
	e.reports_to,
	m.first_name  as manager,
	e.job_title
from
	employees e
left join employees m
	USING  (employee_id)
order by e.employee_id;
----------------
select
	*
from
	order_items oi
join order_item_notes oin
		using (order_id, product_id );

-- on
-- 	oin.order_Id = oi.order_id
-- 	and oin.product_id = oi.product_id
```

-   **Natural Joins**

    -   The _Natural Join_ give the responsibility to the database to decide which columns are the primary key and th pointer key, but the two tables must have the same columns name.

-   **Cross Joins**
    -   The **CROSS JOIN** is used to generate a paired combination of each row of the first table with each row of the second table. This join type is also known as _cartesian_ join.
    -   AxB ={(a,1), (a,2), (a,3), (b,1), (b,2), (b,3)}

![Database Cross Joins!](/imgs/cross-join.png "Database Cross Joins")

```sql
-- Syntax 1: Explicit syntax
select * from customers c cross join orders o order by c.first_name

-- Syntax 2: Implicit syntax
select * from customers c , orders o order by c.first_name
```

-   **Union Clause**
    -   The UNION operator is used to combine the result-set of two or more SELECT statements.
    -   Every SELECT statement within UNION must have the same number of columns
    -   The columns must also have similar data types
    -   The columns in every SELECT statement must also be in the same order
