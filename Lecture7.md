Lecture7.md

# Lecture 7 - Transactions
## Recap

### Referential Integrity
- Let A be a set of attributes. Let R and S be two relations that contain attributes A, and where A is the primary key of S. A is said to be a foreign key of R if for any values of A appearing in R, these values also
- Foreign keys
  - Specified as part of the SQL CREATE TABLE statement
    - `FOREIGN KEY (dept_name) REFERENCES department`
  - By default, foreign key references the primary key attributes
- Cascading Actions in referential integrity
  - When a constraint is violated, the procedure is to reject the action that caused the violation
  - If the action is to cascade, the delete cascades to the course table, deleting the tuple that refers to the department that was deleted
  - `DELETE CASCADE`
  - `UPDATE CASCADE`
  - Instead of `CASCADE`, we can use `SET NULL` or `SET DEFAULT`
- Assigning names to constraints
  - `salary` `numeric(8,2)`, constraint `minsalary` check (`salary` > 2900)
  - To drop a constraint:
    - `alter table instructor drop constraint minsalary`
  - Help keep track and debug with names to constraints
### Assertions
- Assertion is a predicate expressing a condition that we wish the database always to satisfy
  - `create assertion <name> check (<predicate (usually a sql command)>)`
### Built-in data types
- `date`
- `time`
- `timestamp`
- `interval`
  - Period of time
### Large object types
- Photos, videos, CAD files, etc.
  - `Blob`
    - Binary Large Object, uninterpreted binary data
  - `Clob`
    - Character Large Object, large collection of character data
- When query returns a large object, a pointer is returned rather than the large object itself
### Custom types
- `create type Dollars as numeric (12,2) final`
  - (This defines a new data type named `Dollars` which is based on `numeric` with a total of 12 digits, 2 of which are after the decimal point. `final` indicates that this type cannot be further subclassed.)
### Generating unique key values
- Automatically generated unique key
  - `ID numeric(5) generated always as identity`
  - In PostgreSQL:
    - `serial`
  - In MySQL:
    - `auto_increment` in place of `generated always as identity`
### Domains
- `create domain` construct in SQL-92 creates user-defined domain types
  - `create domain person_name char(20) not null`
  - (This creates a domain called `person_name` based on `char(20)` which cannot be null.)
- Types and domains are similar; domains can have constraints
### Index Creation
- Many queries reference only a small proportion of the records in a table
- Inefficient for the system to read every record to find a particular record with a particular value
- An index on an attribute of a relation is a data structure that allows the database system to find those tuples in the relation that have a specified value for that attribute efficiently without scanning through all the tuples of the relation
- We create an index with the `create index` command
  - `create index <name> on <relation-name>(attribute);`
pretty much makes a hash on a certain attribute
  - Lets say you make a table and have an attribute ID
    - create index studentID_index on student(ID)
  - then the query
    - select * from student where ID = '12345'
    - Can be executed using the index to find the required records without looking at all records of student
      - This is an example of how an index can speed up data retrieval by providing a direct lookup mechanism. Instead of scanning the entire `student` table, the database can use the `studentID_index` to quickly locate the row where the `ID` column matches '12345'.

### Authorization
- Assign a user several forms of authorizations on parts of the database.
- Select allows reading but not modification.
- Insert allows writing, but not modifications.
- Update allows writing but not deletion.
- Delete allows deletion of data.
- Each type of authorization is called a privilege.
    * A privilege is a specific permission granted to a user or role, defining what actions they can perform on database objects.
- May authorize the user all, none, or a combination of these types of privileges on specified parts of a database such as a relation or a view.
- grant <privilege list> on <relation/view> to <user>
  - Granting a privilege on a view does not imply granting any privileges on the underlying relations.
### Revoking authorizations
- revoke statement
  - revoke <privilege list> on <relation/view> from user
### Roles
- Distinguish among various users as far as what these users can access and update in the database.
- create a role <name>
  - create role instructor
### Transactions
- Unit of a program execution that accesses and possibly updates various data items
- Transaction to transfer 50 from account A to account B
  - read(A)
  - A:=A-50 [This represents the calculation to decrease the balance of account A.]
  - write(A)
  - read(B)
  - B:=B+50 [This represents the calculation to increase the balance of account B.]
  - write(B)
- Two main issues
  - Failures [This refers to problems that can cause a transaction to not complete successfully.] either hardware or system crashes
  - concurrent execution of multiple transactions [This means more than one transaction is running at the same time.]
  - race cases [A race condition occurs when the outcome of a computation depends on the sequence or timing of uncontrollable events, such as the order in which multiple threads or processes access shared data.]
- Atomicity requirement
  - Transaction must be all or nothing
  - changes have to persist otherwise not do anything at all (has to rollback any changes made)
* Consistency requirement
        * Durability Requirement
        * Isolation requirement
            * can be ensured trivially by running transactions serially (Sequentially, one after another)
            * one after another
        * Slower with isolation
            * Multiple transactions concurrently has significantly benefits (Running multiple transactions at the same time can lead to much faster overall processing, even if individual transactions take longer due to isolation mechanisms.)

### ACID PROPERTIES
- Transaction is a unit of program execution. To preserve integrity of data the database system must ensure
    - Atomicity: (Ensures that all operations within a transaction are completed successfully, or none are. If any part of the transaction fails, the entire transaction is rolled back, leaving the database in its original state.)
    - Consistency: (Guarantees that a transaction brings the database from one valid state to another. It prevents data corruption or invalid states.)
    - Isolation: (Ensures that concurrent transactions do not interfere with each other. Each transaction appears to be executed in isolation, as if it were the only transaction running.)
    - Durability: (Confirms that once a transaction has been committed, its changes are permanent and will survive any subsequent failures, such as power outages or system crashes.)

### Transaction State
- Active
  - Initial state; the transaction remains in this state while it is executing.
- Partially Committed
  - The transaction has completed some operations but not all.
- Failed
- Committed
- Aborted
- Active can transition to Partially Committed or Failed.
- Partially Committed can transition to Committed or Failed.
- Failed can only transition to Aborted.


### Schedule
- Chronological order [the sequence] in which instructions of concurrent transactions are executed
- Completes its execution will have a commit instruction [a command to finalize] as the last statement
- Transaction that fails [is unable] to successfully complete its execution will have an abort instruction [a command to cancel] as the last statement
- Amount of schedules possible are transactions factorial

    *   The number of possible schedules for $n$ transactions is $n!$ (n factorial).
    *   For example, if there are 3 transactions (T1, T2, T3), the number of possible schedules is $3! = 3 \times 2 \times 1 = 6$.