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
