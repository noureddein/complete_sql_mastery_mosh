# Inserting, Updating, and Deleting Data

-   **Column Attributes**
    -   VARCHAR(50) vs CHAR(50)
        -   VARCHAR(50): Variable Character, use the space that equal to the length of the character. Number 50 sets the max length of the inserted character in the column
        -   CHAR(50): Character, uses the whole space when store a character, so if we want to store a (dog) word, the column will preserve 50 character space.
    -   **Column Attributes shortcuts**
        -   Datatype: Define the column datatype, INT(10), VARCHAR(50), CHAR(50), DATE, TEXT, LONGTEXT, ENUM. (The columns datatype depends on the database used)
        -   PK: Primary key
        -   NN: Not Null
        -   AI: Auto Increment
        -   Default: if the user doesn't supply the value for the column, the database will insert the default value

![Column Attributes!](/imgs/columns-attributes.png "Column Attributes")

-   **Inserting a Single Row**

```sql
insert
	INTO
    customers
values
    (
        default,
        'Nour',
        'Eddein',
        '1994-06-20',
        '404-246-3371',
        '22 Al Aqsa',
        'Amman',
        'AM',
        '1298'
    );
```

```sql
insert
INTO
customers
    (
        first_name,
        last_name,
        birth_date,
        phone,
        address,
        city,
        state,
        points
    )
values
    (
        'Mhd',
        'Al Abbassi',
        '1996-09-20',
        '404-226-3371',
        '22 Al Aqsa',
        'Amman',
        'AM',
        '1298'
    );
```

-   **Inserting a Multiple Rows**

```sql
insert
	INTO
	shippers (name)
values  ('DHL'),
		('Aramex'),
		('TNT'),
		('FedEx');
```

-   **Inserting Hierarchical Rows**
    -   Hierarchical data is defined as a set of data items that are related to each other by hierarchical relationships. Hierarchical relationships exist where one item of data is the parent of another item.
    -   Parent-Child relationship: Which mean one row in the parent table can have one or more children inside the child table

```sql
-- Parent Table
INSERT INTO orders
	(customer_id, order_date, status)
values
	(1, "2019-02-02", 1);

 -- Child table
INSERT INTO order_items
	(order_id, product_id, quantity, unit_price)
values
	(LAST_INSERT_ID(), 2, 1, 2.95),
    (LAST_INSERT_ID(), 1, 1, 5.90),
    (LAST_INSERT_ID(), 5, 1, 3.90);

-- LAST_INSERT_ID():
    -- Is a built-in function.
    -- Refers to the last record inserted in the order table.
```

-   **Creating a Copy of a Table**
    -   Create a copy of a table with all the data inside.
    -   This method will ignore all the attributes of the columns.

```sql
CREATE TABLE orders_archived AS
select * from orders; -- This line called a sub-query of (CREATE TABLE orders_archived AS )
```

```sql
create table invoices_archived as
select
	i.invoice_id,
    i.number,
     c.name,
    i.invoice_total,
    i.payment_total,
    i.payment_date
from invoices i join clients c using (client_id)
where i.payment_date is not null
```

-   **Updating Single Row**

```sql
UPDATE invoices
	SET payment_total=10, payment_date='2019-03-01'
where invoice_id = 1;

UPDATE invoices
	SET payment_total=DEFAULT, payment_date=NULL
where invoice_id = 1;

-- Update the row with the 50% of the invoice_total column
-- and the payment_date equal to due_date column of the same row.
UPDATE invoices
	SET payment_total= invoice_total * 0.5,
    payment_date= due_date
where invoice_id = 3;
```

-   **Updating Multiple Rows**
    -   By default MySQL Workbench reject updating multiple rows, because its running in safe mode.

```sql
update customers
	set points = points + 50
where birth_date < '1990-01-01';
```

-   **Using Sub-queries in Updates**
    -   In SQL a Sub-query can be simply defined as a query within another query. In other words we can say that a Sub-query is a query that is embedded in WHERE clause of another SQL query. Important rules for Sub-queries:
        -   You can place the Sub-query in a number of SQL clauses: WHERE clause, HAVING clause, FROM clause. Sub-queries can be used with SELECT, UPDATE, INSERT, DELETE statements along with expression operator. It could be equality operator or comparison operator such as =, >, =, <= and Like operator.
        -   A sub-query is a query within another query. The outer query is called as **main query** and inner query is called as **sub-query**.
        -   The sub-query generally executes first when the sub-query doesn’t have any co-relation with the main query, when there is a co-relation the parser takes the decision on the fly on which query to execute on precedence and uses the output of the sub-query accordingly.
        -   Sub-query must be enclosed in parentheses.
        -   Sub-queries are on the right side of the comparison operator.
        -   ORDER BY command cannot be used in a Sub-query. GROUP BY command can be used to perform same function as ORDER BY command.
        -   Use single-row operators with single-row Sub-queries. Use multiple-row operators with multiple-row Sub-queries.

```sql
UPDATE orders AS o
SET
    o.comments = 'Gold customer'
WHERE
    customer_id IN (SELECT
            customer_id
        FROM
            customers AS c
        WHERE
            c.points >= 3000)
```

-   **Deleting Rows**

```sql
Delete from invoices -- This will delete all the records from invoices table

delete from invoices where
    client_id = (
        select * from clients where name = "Myworks"
    );
```

-   **Restoring Databases**
    -   File > Open SQL Script > Chose the file > Run Execute
