## Views

-   **Creating Views**
    -   In SQL, a view is a virtual table based on the result-set of an SQL statement.
    -   A view contains rows and columns, just like a real table. The fields in a view are fields from one or more real tables in the database.
    -   You can add SQL statements and functions to a view and present the data as if the data were coming from one single table.
    -   A view is created with the CREATE VIEW statement.
    -   Views do not store data.

```sql
CREATE VIEW sales_by_client AS
    SELECT
        c.client_id, c.name, SUM(i.invoice_total) AS total_sales
    FROM
        clients c
            JOIN
        invoices i USING (client_id)
    GROUP BY c.client_id , c.name;
```

```sql
CREATE VIEW clients_balance AS
    SELECT
        c.client_id,
        c.name,
        SUM(i.invoice_total) AS total_sales,
        SUM(i.payment_total) AS payment_total,
        SUM(i.invoice_total - i.payment_total) AS balance
    FROM
        clients c
            JOIN
        invoices i USING (client_id)
    GROUP BY c.client_id , c.name;
```

-   **Altering or Dropping Views**

    -   To change the view :
        -   Use the drop clause `DROP VIEW  clients_balance;`
        -   Replace the view use (preferable) `CREATE OR REPLACE VIEW clients_balance`

-   **Update Views**
    -   Views can use update, delete, and insert statement but only under certain circumstances, if the view does not have these clauses, we can consider that view as an updatable view:
        -   DISTINCT
        -   Aggregate functions (MIN, MAX, SUM, AVG, ...)
        -   GROUP BY / HAVING
        -   UNION

```sql
-- This called as an updatable view
CREATE OR REPLACE VIEW invoices_with_balance AS
    SELECT
        i.invoice_id,
        number,
        client_id,
        invoice_total,
        payment_total,
        invoice_total - payment_total AS balance,
        invoice_date,
        due_date,
        payment_date
    FROM
        invoices i
    WHERE
        (invoice_total - payment_total) > 0
        order by balance desc;
```

```sql
-- View can be modified
DELETE FROM invoices_with_balance
WHERE invoice_id = 1;
```

-   **The WITH OPTION CHECK Clause**

    -   WITH CHECK OPTION is an optional clause on the CREATE VIEW statement. It specifies the level of checking when data is inserted or updated through a view. If WITH CHECK OPTION is specified, every row that is inserted or updated through the view must conform to the definition of the view.

```sql
-- This called as an updatable view
CREATE OR REPLACE VIEW invoices_with_balance AS
    SELECT
        i.invoice_id,
        number,
        client_id,
        invoice_total,
        payment_total,
        invoice_total - payment_total AS balance,
        invoice_date,
        due_date,
        payment_date
    FROM
        invoices i
    WHERE
        (invoice_total - payment_total) > 0
    ORDER BY balance DESC WITH CHECK OPTION;
```

-   **Other Benefits of Views**
    -   Views can help us with:
        -   Simplify queries
        -   Reduce the impact of changes.
        -   Restrict access to the data
