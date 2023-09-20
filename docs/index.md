# What is the difference between SQLAlchemy ORM and SQLAlchemy Core?

### SQLAlchemy provides two components for interacting with databases - ORM and Core.

### ORM (Object-Relational Mapping)
- Enables developers to work with relational databases using the object-oriented approach
- Uses python classes to define the database model
- Provides a higher-level OO approach, has abstraction and therefore has overhead

### SQLAlchemy Core
- Focuses on constructing and executing SQL queries while giving developers full control over the SQL generation process
- Uses SQL constructs to build and execute SQL statements
- Lower level and provides a more direct and SQL-centric way to interact with DB's giving devs more control over the queries, therefore is lightweight, no overhead, and fast


### Conclusion
- ORM - Objects and Classes, high-level approach, rapid development and less SQL knowledge requirements because of abstraction

- Core - Control over SQL queries, performance, optimization, complex SQL statements, multiple databases, 

Can also take on a hybrid approach - ORM for standard CRUD operations and Core for more optimized and complex SQL

# s

