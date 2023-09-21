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

table_1 = Table(
    "user_table",
    meta_data,
    Column("index", Integer)
    Column("name", String)
    Column("grade", String)
)

table_2 = Table(
    "info_table",
    meta_data,
    Column("email", String),
    Column("address", String)
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

#### Select columns from multiple tables
```python
select_query = select(table.c.name, table.c.grade, table_2)
print(select_query)
```
```
Output
SELECT dbo.user_table.name, dbo.user_table.grade, dbo.info_table.email, dbo.info_table.address 
FROM dbo.user_table, dbo.info_table
```

#### Select query with a 'WHERE' clause
```python
select_query = select(table_1).where(table_1.c.name == "John")
print(select_query)
```
```
Output
SELECT dbo.user_table.index, dbo.user_table.name, dbo.user_table.grade 
FROM dbo.user_table 
WHERE dbo.user_table.name = :name_1
```

#### Basic Insert Query
```python
from sqlalchemy import insert

insert_query = insert(table_1).values(index=10, name="Adam", grade="A")
print(insert_query)
```
```
Output
INSERT INTO dbo.user_table (index, name, grade) VALUES (:index, :name, :grade)
```

#### Bulk Insert
```python
data_to_insert = [
    {"index": 1, "name": "John", "grade": "A"},
    {"index": 2, "name": "Jane", "grade": "B"},
    {"index": 3, "name": "Adam", "grade": "A"},
]
    
insert_query = insert(table_1).values(data_to_insert)
print(insert_query)
```
```
Output
INSERT INTO dbo.user_table (index, name, grade) VALUES (:index_m0, :name_m0, :grade_m0), (:index_m1, :name_m1, :grade_m1), (:index_m2, :name_m2, :grade_m2)
```

## Examples on how to create a Database connection and execute queries

```python
from sqlalchemy import create_engine

conn_string = = f"mssql+pyodbc://@{SERVER}/{DATABASE}?driver={DRIVER}&Encrypt=no"
engine = create_engine(conn_string)

select_query = select(table_1).where(table_1.c.name == "John")
insert_query = insert(table_1).values(index=10, name="Adam", grade="A")

with engine.connect() as conn:
    conn.execute()

```