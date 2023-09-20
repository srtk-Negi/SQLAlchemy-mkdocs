## What is the difference between SQLAlchemy ORM and SQLAlchemy Core?

#### SQLAlchemy provides two components for interacting with databases - ORM and Core.

#### ORM (Object-Relational Mapping)
- Enables developers to work with relational databases using the object-oriented approach
- Uses python classes to define the database model
- Provides a higher-level OO approach, has abstraction and therefore has overhead

#### SQLAlchemy Core
- Focuses on constructing and executing SQL queries while giving developers full control over the SQL generation process
- Uses SQL constructs to build and execute SQL statements
- Lower level and provides a more direct and SQL-centric way to interact with DB's giving devs more control over the queries, therefore is lightweight, no overhead, and fast


#### Conclusion
- ORM - Objects and Classes, high-level approach, rapid development and less SQL knowledge requirements because of abstraction

- Core - Control over SQL queries, performance, optimization, complex SQL statements, multiple databases, 

Can also take on a hybrid approach - ORM for standard CRUD operations and Core for more optimized and complex SQL

## What does SQLAlchemy core does and how can you build SQL queries with it?
- Provides a Pythonic SQL language that allows you to construct SQL queries using Python objects and functions that closely resemble SQL syntax.
- provides an Engine object that represents a database connection pool and a Connection object for executing queries and managing transactions
- Database agnostic, works with different DB systems
- Build complex queries (with conditions, joins, etc.)

To build SQL Queries using SQLAlchemy Core, you first need to define the structure of your table.

```python
from sqlalchemy import Table, Integer, String, Column, MetaData

meta_data = MetaData(schema="dbo")

table = Table(
    name="user_table",
    meta_data,
    Column("index", Integer)
    Column("name", String)
    Column("grade", String)
)
```

Some examples on how to build SQL queries using this: 
#### Basic Select Query

```python
from sqlalchemy import select

query = select(table)
print(query)
```
```
Output
SELECT dbo.user_table.index, dbo.user_table.name, dbo.user_table.grade
FROM dbo.user_table
```

#### Select Query with desired columns specified
```python
from sqlalchemy import select

query = select(table.c.name, table.c.grade)
print(query)
```
or
```python
query = select(table.c["name","grade"])
print(query)
```
```
Output
SELECT dbo.user_table.name, dbo.user_table.grade 
FROM dbo.user_table
```