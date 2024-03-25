## Data Types

-   **String Types**

    -   Strings have many data types, but most common ones are:
        -   CHAR(X): used for fixed-length, like stats
        -   VARCHAR(X) use for variable length strings, like username, passwords, email, and address. max: 65,535 cha.(~64KB)
        -   MEDIUMTEXT: used for JSON, csv, and short to medium length quotes. max 16 million char. (16MB)
        -   LONGTEXT: used for text books, years of log files. max (4GB)
        -   TINYTEXT: max (255 Bytes)
        -   TEXT : Like VARCHAR, max (64KB)
        -   Character Bytes
            -   English take 1 byte
            -   European/Middle-eastern 2 bytes
            -   Asian 3 bytes

-   **Integer Types**
    -   We use integers to store whole numbers that don't have a decimal point.
    -   Types
        -   TINYINT 1B [-128, 127]
        -   UNSIGNED TINYINT 1B [0, 255]
        -   SMALLINT 2B [-32K, 32K]
        -   MEDIUMINT 3B [-8M, 8M]
        -   INT 4B [-2B, B]
        -   BIGINT 8B [-9Z, 9Z]
        -   ZEROFILL: display the numbers with fixed length e.g. 0001, 0002, 0234,

> For mor information [Link](https://dev.mysql.com/doc/refman/8.3/en/integer-types.html "MySQL Integer Types")

-   **Fixed-point anf Floating-point Types**

    -   We have 3 types of storing decimal type
        -   Relational
            -   DECIMAL(p, s): Used to store fixed point numbers, the numbers that have fixed number of digits after the decimal point like Monetary values.
                -   p: precision, specifies the maximum number of digits and this can be between 1 - 65
                -   s: scale, the scale determine the number of digits after the decimal point
                -   e.g. DECIMAL(9, 2): 7 digit before decimal and 2 digit after decimal 1234567.89
                -   DECIMAL type have a few synonyms DEC, NUMERIC, and FIXED
            -   FLOAT: Used with scientific calculations 4byte
            -   DOUBLE: Used with scientific calculations 8byte

-   **Boolean Types**

    -   BOOL or BOOLEAN: This datatype is a synonym for TINYINT
    -   TRUE and FALSE represented as 1 or 0

-   **Enum and Set Types**

    -   ENUMS and SET : Used to restrict the values for a column to a limited list of strings e.g. we need to have a column called size that have the values of ('small', 'medium', 'large')
    -   ENUM is bad because
        -   To change the members of enum can be expensive, because if we have million of records and we decided to add or remove a value, this will rebuild the whole table
        -   If we need to show these values to user as a dropdown its not easy to do
        -   ENUMS are not reusable, if I want to use the same column with another table I have to redefined these values.
    -   A better approach is to have a separate table called sizes, and store all the sizes and any additional attributes like their measurements, we refer to this table as a lookup table.
    -   Set is used to store multiple values

-   **Date and Time Types**

    -   We have 4 data types for storing date and time values
        -   DATE: To store date without the time component
        -   TIME for storing the time value
        -   DATETIME
        -   TIMESTAMP use to keep tracking of when the row is inserted or last updated
        -   YEAR to store for digit year
    -   Difference between DATETIME and TIMESTAMP
        -   DATETIME: 8bytes used to store dates goes beyond year 2038
        -   TIMESTAMP: 4bytes (up to 2038) this is called the year 2038 problem

-   **Blobs Types**

    -   Blobs used to store large amounts of binary data like images, PDFs, videos, word files.
    -   We have 4 blob types and they defer based on the max amount of data they can store.
        -   TINYBLOB: to store binary data up to 255 bytes
        -   BLOB: to store binary data up to 65 KB
        -   MEDIUMBLOB: to store binary data up to 16MB
        -   LONGBLOB: to store binary data up to 4GB
    -   Generally best approach is to store files outside database, because relational databases designed for working with structured relational data not binary data.
    -   Problems with storing files in database:
        -   Increased database size very quickly
        -   Slower backups
        -   Performance problems
        -   Writing more code to read/write data

-   **JSON Types**
    -   JSON is a lightweight format for storing and transferring data over the internet.
    -   To use JSON with table, set the column data type to JSON

```SQL
-- To update a column with JSON object
UPDATE products
SET properties = '
{
	"dimensions": [1,2,4],
    "weight": 10,
    "manufacturer":{ "name": "sony" }
}'
WHERE product_id = 1;

-- Another way to create a JSON object with MySQL JSON functions
UPDATE products
SET properties = JSON_OBJECT(
    'weight', 20,
    'dimensions', JSON_ARRAY(1,2,3),
    'manufacturer', JSON_OBJECT('name', 'sony')
)
WHERE product_id = 1;
```

```SQL
-- Extract individual  key value from JSON object
--  $ refer to the current document
SELECT
	product_id ,
    JSON_EXTRACT(properties, '$.weight') AS weight
FROM sql_store.products WHERE product_id = 1;

SELECT
	product_id ,
    properties -> '$.dimensions[0]'
FROM sql_store.products WHERE product_id = 1;

-- The double greater than sign to get rid of the double quotes
SELECT
	product_id ,
    properties ->> '$.manufacturer.name'
FROM sql_store.products WHERE product_id = 1;

-- To search in JSON
SELECT
	product_id ,
    properties ->> '$.manufacturer.name'
FROM sql_store.products WHERE  properties ->> '$.manufacturer.name' = 'sony';
```

```SQL
-- To update a JSON
UPDATE products
SET
    properties = JSON_SET(properties, '$.weight', 22, '$.age', 10)
WHERE product_id = 1;

-- To remove a key from JSON
UPDATE products
SET
    properties = JSON_REMOVE(properties, '$.age')
WHERE product_id = 1;
```
