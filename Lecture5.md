Lecture5.md
# Lecture 5 - SQL

### Recap
- Aggregation
  - Sum
  - Count
  - Avg
  - Min
  - Max
  - Group By
  - Having
- Nested Subquery
  - In, Some, All, Exists
- With Clause



### Modification of the Database
- Deletion of tuples from a given relation
- Insertion of new tuples into a given relation
- Updating of values in some tuples in a given relation
- CRUD (four basic operations of persistent storage)
  - Create
  - Read
  - Update
  - Delete
### Deletion
- Delete all instructors
  - `DELETE FROM instructor;`
  - this will delete every instructor from the table
  - Do not do this
- Delete all instructors from the finance department
  - `DELETE FROM instructor`
  - `WHERE dept_name = 'finance';` (remember to use single quotes for string literals)

- Delete all tuples in the instructor relation for those instructors associated with a department located in the Watson Building
  - `delete from instructor where dept_name = 'Biology' or dept_name = 'Physics'`
  - Easier way
    - `delete from instructor where dept_name in ('Biology', 'Physics')`

- To write a subquery, we have to understand what departments are present.
- Subquery:
  - `SELECT dept_name FROM department WHERE building = 'Watson'`
- Parent:
  - `DELETE FROM instructor WHERE dept_name IN (subquery here)`
You can also use a `WITH` clause:
- `WITH` clause defines 'variables'.
- Define the subquery with `WITH` clause and then use it in the `DELETE`.

Q: Why don't we just delete by the building?
A: `dept_name` is the key that is present in both tables, therefore we can delete by that.
- Let's say we want to delete all instructors whose salary is less than the average salary of instructors.
  - `DELETE FROM instructor`
  - `WHERE salary < (SELECT AVG(salary) FROM instructor);`
  - There is a problem since deleting an entry changes the average.
- We can compute the average salary and find all tuples to delete, and then delete the tuples without recomputing the average or retesting the tuples.
- Let's say we want to delete all instructosrs whose salary is less than the average salary of instructors
  - delete from instructor
  - where salary < (select avg(salary) from instructor);
  - There is a problem since deletung an entry changes the average

- We dcan compute the average salaray and find all tuples to delete, and then delete te tuples without recomputing avg or retesting the tuples

### Insertion
We can add a new tuple to 'course'
- insert into course
- values ('CS-437', 'Database Systems', 'Comp.SCi', 4) 
We can also use this:
- insert into course (course_id, title, dept_name, credits)
- values ('CS-437', 'Database Systems', 'Comp.SCi', 4)
 

- Pros:
  - More readable and self-documenting (you know exactly which value goes where).
  - Safer — won’t break if table structure changes (unless one of these columns is removed).
  - Easier to maintain in production code.
- Cons:
  - Slightly longer to type.


- Make each student in the Music department who has earned more than 144 credit hours an instructor in the Music department with a salary of 18,000
  - `INSERT INTO instructor`
    - `SELECT ID, name, dept_name, 18000`
    - `FROM student`
    - `WHERE dept_name = 'Music' AND total_cred > 144;`
- The `SELECT FROM WHERE` statement is evaluated fully before any of its results are inserted into the relation.

### Updates
- Give a 5 percent salary raise to all instructors
  - `UPDATE instructor`
    - `SET salary = salary * 1.05`
- Give a salary raise of 5 percent to those instructors who earn less than 70,000
    - `WHERE salary < 70000;`
- Give a 5 percent salary raise to instructors whose salary is less than average
    - `UPDATE instructor`
    - `WHERE salary < (SELECT avg(salary) FROM instructor);`
    - set salary = salary * 1.05
    - where salary < (select avg (salary) from instructor)

### Case Statement for conditional Updates
- Same query as before but with `CASE` statement
  - `UPDATE instructor`
    - `SET salary = CASE WHEN salary <= 100000 THEN salary * 1.05`
      *  (This part of the `CASE` statement specifies that if the current `salary` is less than or equal to 100,000, the new `salary` will be the current `salary` increased by 5%.)
      *  (The calculation $1.05 \times \text{salary}$ represents the salary after a 5% increase.)
    - `ELSE salary * 1.03`
      *  (This `ELSE` clause handles all other cases where the `salary` is greater than 100,000. The new `salary` will be the current `salary` increased by 3%.)
      *  (The calculation $1.03 \times \text{salary}$ represents the salary after a 3% increase.)
    - `END`
  - Can stack multiple `WHEN`s and then have to define an `END`
    *  (The `CASE` statement allows for multiple `WHEN` conditions to be evaluated sequentially. If a `WHEN` condition is met, the corresponding `THEN` action is executed, and the `CASE` statement terminates.)
    *  (If no `WHEN` condition is met, the `ELSE` clause is executed if present. If there is no `ELSE` clause and no `WHEN` condition is met, the result of the `CASE` statement is `NULL`.)



### Exercises
- Delete all students whose dept_name is that [most] ost frequent department
  - where mostFreq as (
    - select dept_name
    - from students
    - group by dept_name
    - order by count(*) desc
    - limit 1
  - )
  - delete from students
    - where dept_name = (select dept_name from mostFreq)
- Find the median of the column tot_cred
  - [The median is the middle value in a dataset that has been ordered from least to greatest. If the dataset has an even number of values, the median is the average of the two middle values.]
  - To find the median of `tot_cred`, you would typically use a SQL query that ranks the `tot_cred` values and selects the middle one. The exact query can vary depending on the specific SQL dialect, but a common approach involves using window functions.
  - For example, in PostgreSQL or MySQL (version 8+), you might use:
    - SELECT tot_cred
    - FROM (
        - SELECT
        -     tot_cred,
        -     ROW_NUMBER() OVER (ORDER BY tot_cred) as row_num,
        -     COUNT(*) OVER () as total_rows
        - FROM students
    - ) as ranked_students
    - WHERE row_num IN (FLOOR((total_rows + 1) / 2.0), CEIL((total_rows + 1) / 2.0));
    *   `ROW_NUMBER() OVER (ORDER BY tot_cred)` assigns a unique sequential integer to each row based on the ordered `tot_cred` values.
    *   `COUNT(*) OVER ()` calculates the total number of rows in the `students` table.
    *   The `WHERE` clause selects the row(s) corresponding to the median position. For an odd number of rows, `FLOOR` and `CEIL` will yield the same value, selecting the single middle row. For an even number of rows, they will yield two adjacent numbers, and selecting both will allow for averaging if needed (though the example returns both values which can then be averaged).


-- My solution
SELECT tot_cred  -- Selects the total credit hours for each student.
FROM student      -- Specifies the table to retrieve data from, which is 'student'.
ORDER BY tot_cred DESC -- Sorts the results in descending order based on the 'tot_cred' column, so the highest credit hours appear first.
LIMIT 1 OFFSET (SELECT COUNT(*) FROM student) / 2; -- Limits the output to a single row and offsets the results.
    -- (The OFFSET clause calculates the middle point of the sorted student list and retrieves the student at that exact middle position.
    -- For example, if there are 10 students, COUNT(*) will be 10. (10)/2 = 5. The query will return the 6th student after sorting in descending order.)

- This doesnt account for even number of rows

### Joined Relations
- Join operations take two relations and return as a result another relation.
- A join operation is a Cartesian product [**Cartesian product** (also known as the cross product) is a mathematical operation that returns the product of two sets. In the context of relational databases, it combines every row from the first relation with every row from the second relation.] which requires that tuples [**Tuples** (also known as records or rows) are ordered sets of values, where each value corresponds to an attribute in a relation.] in the two relations match under some condition.
- It also specifies the attributes that are present in the result of the join.
- The join operations are typically used in the FROM clause.
- Three types of joins:
  - Natural Join
- students (ID, name, dept_name, tot_dcred)
- takes (ID, course_ID, sec_ID, semester, year, grade)
- list the name of students along with course ID of the courses they took
- Natural join matches tuples with the same values for all common attributes.
    - A natural join on two relations R and S performs a join based on all attributes that are common to both R and S. The result of a natural join contains only one copy of each common column.
- Retains only one copy of each common column.
- Same query in SQL with "natural join" construct:

- SELECT T1.name, T2.course_ID
- FROM students AS T1
- NATURAL JOIN takes AS T2;
  - Inner Join
  - Outer Join