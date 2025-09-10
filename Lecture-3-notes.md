Lecture-3-notes.md


# Lecture 3 - SQL

### Creating tables
- SQL relation schema is defined using the `CREATE TABLE` command:
- `CREATE TABLE` r (A1 D1, ..., An Dn)
  - r is the name of [the] relation
  - Ai is an attribute name in the schema of relation r
  - Di is the data type of attribute Ai, along with optional constraints that restrict the set of allowed values for Ai
  - Ex [Example]:
    - `CREATE TABLE` department (dept_name `VARCHAR`(20), building `VARCHAR`(15))
    - *   (`VARCHAR` is a variable-length string data type. The number in the parentheses specifies the maximum length.)
- *   General Form: `CREATE TABLE table_name ( column1 datatype, column2 datatype, column3 datatype, .... );`
    - create table department(dept_name varchar(20), building varchar(15))
  
### Basic Types
- CHAR(size) A FIXED length string (can contain letters, numbers, and special characters). The size parameter specifies the column length in characters - can be from 0 to 255. Default is 1
- VARCHAR(size) A VARIABLE length string (can contain letters, numbers, and special characters). The size parameter specifies the maximum string length in characters - can be from 0 to 65535
- BINARY(size) Equal to CHAR(), but stores binary byte strings. The size parameter specifies the column length in bytes. Default is 1
- VARBINARY(size) Equal to VARCHAR(), but stores binary byte strings. The size parameter specifies the maximum column length in bytes.
- TINYBLOB For BLOBs (Binary Large Objects). Max length: 255 bytes
- TINYTEXT Holds a string with a maximum length of 255 characters
- TEXT(size) Holds a string with a maximum length of 65,535 bytes
- BLOB(size) For BLOBs (Binary Large Objects). Holds up to 65,535 bytes of data
- MEDIUMTEXT Holds a string with a maximum length of 16,777,215 characters
- MEDIUMBLOB For BLOBs (Binary Large Objects). Holds up to 16,777,215 bytes of data
- LONGTEXT Holds a string with a maximum length of 4,294,967,295 characters
- LONGBLOB For BLOBs (Binary Large Objects). Holds up to 4,294,967,295 bytes of data
- ENUM(val1, val2, val3, ...) A string object that can have only one value, chosen from a list of possible values. You can list up to 65535 values in an ENUM list. If a value is inserted that is not in the list, a blank value will be inserted. The values are sorted in the order you enter them
- SET(val1, val2, val3, ...) A string object that can have 0 or more values, chosen from a list of possible values. You can list up to 64 values in a SET list

### Foreign Key Primary Key
- define primary key (attributeName)
- define foreign key (attributeName2) references (tablename)
    *  (A foreign key links a column or columns in one table to a primary key in another table, establishing a link between the data.)
    *   (Primary keys uniquely identify each record in a table and cannot contain null values.)
    *  (Referential integrity ensures that relationships between tables remain consistent.)
   *  (SQL syntax for defining primary and foreign keys.)

### Drop Tables and Alter Tables
- Deletes all the tuples [rows] and the table itself
- Alter tables
  - Add columns
  - Modify structure
    -   (e.g., change data type of a column)
- Modern SQL engines might not support drop or similar things
    *   (Consider using views or soft deletes instead in these cases)
- Write more notes about altering and dropping and how it works:
    *   **DROP TABLE**: Removes the table definition and all its data [permanently].
        *   (Syntax: `DROP TABLE table_name;`)
    *   **ALTER TABLE**: Modifies the existing table structure.
        *   (Syntax: `ALTER TABLE table_name ADD column_name datatype;`, `ALTER TABLE table_name MODIFY column_name datatype;`)
        * **Formula for Calculating total number of columns after ALTER TABLE with ADD**
            *   Let $N_c$ be the number of columns before the ALTER operation.
            *   Let $N_{added}$ be the number of columns added during the ALTER operation.
            *   The total number of columns $N_{total}$ after the ALTER operation is:
            *   $N_{total} = N_c + N_{added}$




### Basic Query Structure
- Typical query:
  - `select` A1, A2, ..., An
  - `from` r1, r2, ..., rn
  - `where` P
    - Ai represents attribute
    - ri represents relation
    - P is a predicate
      *   (A predicate is a condition that evaluates to true or false)
  - This results in an SQL Query
    *   **Example 1: Simple Selection**
        *   Table: `Customers`
            *   `CustomerID` (INT, Primary Key)
            *   `FirstName` (VARCHAR)
            *   `LastName` (VARCHAR)
            *   `City` (VARCHAR)
        *   Query:
            SELECT FirstName, LastName
            FROM Customers
            WHERE City = 'New York';
            *   (This query selects the first name and last name of all customers who live in New York.)
    *   **Example 2: Joining Tables**
        *   Tables:
            *   `Orders`
                *   `OrderID` (INT, Primary Key)
                *   `CustomerID` (INT, Foreign Key referencing Customers.CustomerID)
                *   `OrderDate` (DATE)
            *   `Customers` (as defined above)
            SELECT Orders.OrderID, Customers.FirstName, Customers.LastName, Orders.OrderDate
            FROM Orders
            JOIN Customers ON Orders.CustomerID = Customers.CustomerID
            WHERE Customers.City = 'Los Angeles';
            *   (This query joins the `Orders` and `Customers` tables on the `CustomerID` column and retrieves the order ID, customer's first name, customer's last name, and order date for all orders placed by customers living in Los Angeles. The `JOIN` keyword combines rows from two or more tables based on a related column between them.)
    *   **Example 3: Using Aggregate Functions**
        *   Table: `Products`
            *   `ProductID` (INT, Primary Key)
            *   `ProductName` (VARCHAR)
            *   `Price` (DECIMAL)
            *   `CategoryID` (INT)
            SELECT CategoryID, AVG(Price) AS AveragePrice
            FROM Products
            GROUP BY CategoryID;
            *   (This query calculates the average price for each category of products.  The `AVG()` function calculates the average, and the `GROUP BY` clause groups the results by `CategoryID`.)
    *   **Example 4: Using Subqueries**
        *   Tables: `Orders`, `Customers` (as defined above)
            WHERE CustomerID IN (SELECT CustomerID FROM Orders WHERE OrderDate > '2023-01-01');
            *   (This query finds the first name and last name of all customers who have placed an order after January 1, 2023.  The subquery `(SELECT CustomerID FROM Orders WHERE OrderDate > '2023-01-01')` returns a list of `CustomerID` values, and the outer query selects the customer information for those IDs.)
    *   **Example 5: Selecting all attributes**
        *   Table: `Employees`
            *   `EmployeeID` (INT, Primary Key)
            *   `Department` (VARCHAR)
            *   `Salary` (DECIMAL)
            SELECT *
            FROM Employees
            WHERE Department = 'Sales' AND Salary > 60000;
            *   (This query retrieves all attributes for Employees in the Sales department who have a salary greater than 60000.)
    *   **General SQL Query Equation**
        *  The general form of an SQL query can be conceptually represented (though not executable) as:
        $$
            \text{Result} = \sigma_{P}(\Pi_{A1, A2, ..., An}(r1 \times r2 \times ... \times rn))
            *   Where:
                *   `σ` [sigma] represents the selection operation (WHERE clause).
                *   `Π` [Pi] represents the projection operation (SELECT clause).
                *   `P` is the predicate.
                *   `A1, A2, ..., An` are the attributes to be selected.
                *   `r1, r2, ..., rn` are the relations (tables).
                *   `×` represents the Cartesian product (joining tables without a specific condition, generally replaced by JOIN ON for efficiency).


### The 'select' clause
Select clause lists the attributes desired in the result of a query.
- Corresponds to the projection operation of the relational algebra.
  - (Relational algebra is a theoretical language used to describe database operations)
  - The projection operation can be represented mathematically as:
    -  π<sub>attribute1, attribute2, ...</sub>(Relation)  
     * (Where π is the projection operator, attribute1, attribute2, etc. are the attributes to be selected, and Relation is the table or relation being projected)
- Example:
  - Finds the names of all [the] instructors
    - `select name` [Corrected "ame" to "name"]
    - `from instructor`
*  When using the keyword `ALL`, it implies no removal of duplicates.

*  The keyword `DISTINCT` returns no duplicates.
*  A statement like `SELECT '437'` simply returns a column named '437' containing the value '437'.
    *   (This is a literal value selection. The database engine treats the string '437' as a single value, and the `SELECT` statement then outputs this value as if it were data from a column. The column header will typically be the value itself if no alias is provided, or a default name assigned by the database system. This is often used for testing or for returning a constant value within a query.)
*  Arithmetic expressions are also allowed, using `+`, `-`, `*`, or `/`, and operating on constants or attributes of tuples.
    *   (Tuples in this context often refer to rows in a database table or elements within a structured data type.)
    *   **Examples of Arithmetic Expressions:**
        *   **Adding:** `SELECT price + tax FROM products;`
            *   (This would calculate the total price by adding the `tax` amount to the `price` for each product.)
        *   **Subtracting:** `SELECT stock_quantity - reorder_level FROM inventory;`
            *   (This could be used to determine how many items are currently available below a certain reorder point.)
        *   **Multiplying:** `SELECT quantity * unit_price FROM order_items;`
            *   (This is a common way to calculate the subtotal for each item in an order.)
        *   **Dividing:** `SELECT total_sales / number_of_transactions FROM sales_summary;`
            *   (This would calculate the average sale value.)
    *   **Real-World Use Cases:**
        *   **E-commerce:** Calculating final prices after discounts and taxes, determining profit margins by subtracting costs from revenue.
        *   **Finance:** Calculating interest accruals, determining loan amortization schedules, analyzing stock performance by calculating percentage changes.
        *   **Inventory Management:** Calculating stock levels after sales or returns, determining reorder points based on sales velocity.
        *   **Data Analysis:** Creating new derived metrics from existing data, such as calculating ratios, percentages, or differences between time periods.
    *   **Explanation:** These expressions allow you to perform calculations directly within your database queries. Instead of retrieving raw data and performing calculations in an application layer, you can leverage the database's computational power. This can lead to more efficient data processing, especially for large datasets, as the calculations are performed close to the data itself. When operating on attributes of tuples (columns), the operation is applied row by row.
*   