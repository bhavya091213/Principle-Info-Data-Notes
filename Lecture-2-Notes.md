Lecture-2-Notes.md

# Lecture 2 - Relational Databases


## Recap
- What is a database
- Database application examples
- Purpose of databse
  - Why not using file system to manage data
- View of data
- The architecture of a database

## Lecture Outline
- Structure of Rel DB
- Schema
- Key
- Schema Diagrams
- The Relational Algebra

Relational databases can be used because of relational algebra, which you can express connections in mathematical ways. New data representation means.


### Example of an Instructor Relation

- A relational database consists of a collection of tables (or relation)
- You can query the tables, and insert/delete/update tuples

The column headers are the attributes and the tuples are the rows

| ID    | Name | dept_name |
| -------- | ------- | ------ |
| 1001010  |  Jon   | Finance |
| 122134 | Sarah  | Comp Sci |
| 3214134 | Wu | History |

- Table and the mathematical concept of a relation have close correspondance



### Data Schema

Logical structure of the database or table

A database instance is a snapshot of the data in the database at a given instant time

Example:
- Relational Schema: instructor(ID,name,dept_name,salary)
- Relational Instance: *table of instructor*


### Keys
- let K in R where R is a relation
- K is a **superkey** of R if values for K are sufficient to identify a unique tuple of each possible relation of r(R)
  - Example: {ID} and {ID, name} are both superkeys of *instructor*
- A superkey K is a candidate key if K is minimal
  - Example: {ID} is a candidate key of *instructor*
  - A superkey is any set of attributes that can uniquely identify a row in a table, even if it has extra unnecessary attributes.
  - A key (candidate key) is a minimal superkey with no redundant attributes.
- One of the candidate keys is selected to be the *primary key*
- Foreign key constraint: Value in one relation ust appear in another
  - Referencing relation vs Referenced relation
  - Example: dept_name in instructor is a foreign key from instructor referencing department
  - In a foreign key relationship, the referencing relation is the table that contains the foreign key, and the referenced relation is the table that the foreign key points to, which contains the primary or candidate key.
  - Referenced key is the one that everything will reference. Referencing table is the one that contains the foriegn key

For more concrete explanation on Superkeys and Minimal Keys: [Gemini Chat Log](https://share.google/aimode/GZD6r4ZidjIQJ5EsA)



### Relational Algebra
- A query language consisting of a set of operations that take one or two relations as an input and produce a new relation as their result
  - Unary operations take one relation as input
  - Binary operations take two relations as input


#### Basic Relational Algebra Operators

- **Select (σ)**  
  - \( \sigma_{\text{condition}}(R) \)  
  - Returns all tuples in relation \( R \) that satisfy a given condition.
  - Selects tuples that satisfy a given predicate
  - *p* is called the selection predicate
  - Example: select those tuples of the instructor relation where the instructor is in the physics department
  - Query
    - select by department name Physics
  - Result
    - You get all tuples that belong with key Physics
  - We allow cmpoarisons using =, !=, >, >=, <, <=, in the selection predicate
  - Combine several predicates into a larger predicate using the connectives AND OR and NOT
    - Find PHYSICS and SALARY >. 90,000 of (instructor)
  - Selector Predicate may include comparisons between two attributes
    - Find all departments whose name is the same as their building name
      - Select dept_name = building (department)
      - In this case we found where dept_name = building
      - Use "" (quotes) for string literal comparisons and the column name to find equality between columns


- **Project (π)**  
  - \( \pi_{\text{attributes}}(R) \)  
  - Returns only the specified attributes (columns) from relation \( R \).
  - Unary operation that returns its argument relation, with certain attributes left out
  - Notation: Project, A1,A32,A3,...,Ak(r)
    - where A1,A2,...,Ak are atttribute names and r is a relation name
    - The result is defined as the relation of k columns obtained by erasing the columns that are not listed
    - Duplicate reows removed from result, since relations are sets
    - SQL query: SELECT Name, Salary FROM Employees;
  - Basically narrows the table down
  - Can select distinct to also get rid of duplicate rows

- **Set Union (∪)**  
  - \( R \cup S \)  
  - Returns all tuples that are in relation \( R \), in relation \( S \), or in both.
  - Combine two relations
  - Notation: r U s
  - For it to be valid
    - r, s must have the same arity, number of attributes
    - The attribue domains must be compatible (example: 2nd column of r deals with the same type of values as the second column of s)
    - Example:
      - Find all courses taught in the Fall 2017 Semester, or in the Spring 2018 Semester, or in both
      - Project COURSE_ID (SELECT semester = Fall and year = 2017(section)) U (or) Project COURSE_ID (SELECT semester = Spring and year = 2018(section))

- **Set Intersection (∩)**  
  - \( R \cap S \)  
  - Returns all tuples that are in both relation \( R \) and relation \( S \).
  - Kind of like the union, but instead what is similar between
  - r N s
  - Assume:
    - r, s have the same arity
    - attribute domains of r and s must be compatible


- **Set Difference (−)**  
  - \( R - S \)  
  - Returns all tuples that are in relation \( R \) but not in relation \( S \).
  - r - s
  - Used to find unique, such that if one tuple exists in relation A, it doesn't exis in relation B
  - Example:
    - Course available in this semester but not in last semester (or vice versa)

- **Cartesian Product (×)**  
  - \( R \times S \)  
  - Returns all possible combinations of tuples from relation \( R \) and relation \( S \).
  - Cartesian product operation denoted by X allows us to combine tables
  - TODO, need ot go back to slides to finish because TA skipped

- **Rename (ρ)**  
  - \( \rho_{S(A_1, A_2, \dots, A_n)}(R) \)  
  - Renames relation \( R \) to \( S \) with attributes \( A_1, A_2, \dots, A_n \).
  - Result of relation expressions do not have a name that we can used to refer to them. We can rename them for that reason
  - the expression roe sub x (E)
    - returns the result of expression E under the name X
  - You can also use roe sub x(a1, a2, a3) (E) in order to rename the actual attributes
  - Can be used to refer the same table multiple times in a query

- **Assignment Operator**
  - Convenient at times to write a relational-algebra expresion by assigning parts of it to temporary relation variables
  - The assignment operation is denoted by <- and works like assignment in a programming language
  - Example:
    - Find all instructor in the "Physics" and Music department
    - Physics <- Select dept_name = "Physics"(instructor)
    - Music <- Select dept_name = "Physics"(instructor)
    - Physics U Music
 
- **Equivalent Queries**
  - There is more than one way to write a query in relational algebra.
  - So we can find things in multiple ways, and two queries are equivalent if they yeild the same data

### Composition of Relational Operations
- The result of relational-algebra operation is relationa and therefore of relational-alebra operations can be composed together into a relational-algebra expression
- Consider the query -- find the names of all instructors in the Physics Department
  - We can use Project name (SELECT dept_name = "Physics(instructor))
  - We can give an expression that evaulates to a relation instead of just giving the name of a relation as the argument of the projection
  - We end up selecting all the names from physics.
  - Why not projet first and then filter for all the Physics instructors?
    - Because if we just have names, we have no attribute dept_name to filter from
  - Why do a projection in the first place?
    - Removing unwanted attributes that we don't care about


### Join Operation
- The Cartesian - Product
- Instructor X teaches
  - Associates every tuple of instructor with every tuple of teaches
  - Most of the resulting rows have information about instructors who did not teach a particular course
- To get only tuples of instructor. teaches that pertain to instructors and the courses that they taught, we write:
  - SELECT instrucor.id = teaches.id (instructor.x teaches)
- Join operation allows us to combine a select operation and cartesian product operation into a single operations
- Let Theta be a predicate on attributes in the schema R union S
  - The join operation r theta s is defined as follows
  - r theta s = select theta (r x s)
  - It is just an easier way to write
  - 