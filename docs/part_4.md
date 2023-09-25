# Different ways to handle Database conenctions (engines, connections, sessions)

## Engine

This is like a central component of SQLAlchemy Database connections. It is a pool of database connections and is created once per database. Suitable for multithreading. This is database agnostic so you can configure it for your database type (MySQL, MS SQL, Postgressql) and and then make connections to that.

```python
# MS SQL
conn_string = = f"mssql+pyodbc://@{SERVER}/{DATABASE}?driver={DRIVER}&Encrypt=no"

# SQL Lite
conn_string = f"sqlite:///{mydatabase.db}"

# MySQL
conn_string = f"mysql://{username}:{password}@{hostname}:{port}/{database_name}"

engine = create_engine(conn_string)
```

Trade-off : Creating multiple engines for multiple databases can result in increase of resource consumption.

## Connection

A connection represents a single connection to the database. SQLAlchemy automatically manages connections in a connection pool associated with the engine. Connections are used to execute raw SQL queries or commands.

Normally, you don't interact with connections directly in SQLAlchemy. Instead, you use a higher-level component called a session to manage transactions and queries.

Trade-off - Using connections directly can be error-prone, as you need to handle transaction management manually. It's recommended to use sessions instead for more robust transaction handling.

```python
# ASSUMING YOU HAVE ALREADY CREATED AN ENGINE OBJECT
sql_query = "SELECT * FROM myTable"

with engine.connect() as conn:
    result = conn.execute(sql_query)
```

## Transactions and Sessions

#### What is a transaction?
Transactions are used to group one or more database operations into an atomic unit of work. Transactions ensure that a series of operations either all succeed or all fail, maintaining database consistency.


Example -  **bank transfer**, where money is withdrawn from one account and deposited into another. In this case, the transaction might involve two database operations: subtracting funds from one account and adding funds to another. The transaction ensures that both operations are completed successfully or neither is, maintaining the integrity of the accounts and the consistency of the data.

Trade-off: Explicitly managing transactions can be error-prone, especially in multi-user environments
#### What are sessions?
Sessions are the primary way to interact with the database when using SQLAlchemy's ORM or high-level query interface. They handle transaction management and provide a clean API for working with objects. They manage transactions and data changes.

Trade-off: Adds and additional layer of abstraction.

Example:-
```python
from sqlalchemy import create_engine, text
from sqlalchemy.orm import sessionmaker

# Create an SQLAlchemy engine
engine = create_engine("sqlite:///mydatabase.db")

# Create a session
Session = sessionmaker(bind=engine)

# For SQL SELECT
with Session() as session:
    result = session.execute(text("SELECT * FROM my_table WHERE age > 18"))

# For SQL INSERT
with Session() as session():
    values = {"name":"Alice", "age": 30}
    session.execute(text("INSERT INTO users (name, age) VALUES (:name, :age)"), values)
    session.commit()

# You can also have multiple executes within a session block
with Session() as session():
    value_set_1 = {"name":"Alice", "age": 30}
    value_set_2 = {"name":"Bob", "age": 26}
    session.execute(text("INSERT INTO users (name, age) VALUES (:name, :age)"), value_set_1)
    session.execute(text("INSERT INTO users (name, age) VALUES (:name, :age)"), value_set_2)
    session.commit()
```

You can also use ``session.begin()`` to explicitly start a transaction - 
```python
with Session.begin() as session:
    value_set_1 = {"name":"Alice", "age": 30}
    value_set_2 = {"name":"Bob", "age": 26}
    session.execute(text("INSERT INTO users (name, age) VALUES (:name, :age)"), value_set_1)
    session.execute(text("INSERT INTO users (name, age) VALUES (:name, :age)"), value_set_2)
    session.commit()
```

#### Difference between normal ``Session()`` and ``Session.begin()`` -

|Session()|Session.begin()|
|---------|---------------|
|By default in "autocommit" mode|Explicitly start a new transaction within the session
|Each individual SQL statement executed within the session is treated as a separate transaction|All subsequent SQL operations within the session are part of this transaction, and they are treated as a single unit of work
|if you perform multiple SQL operations within a session and an exception occurs during any of them, only the current operation is rolled back, and the others are not affected|If an exception occurs during any of the SQL operations, the entire transaction (including all previous operations) can be rolled back using session.rollback()


#### In Summary
- A normal session (session()) operates in autocommit mode, where each SQL operation is treated as a separate transaction.
- A session created with session.begin() allows you to explicitly start and manage a single transaction for multiple SQL operations, providing better control over the transaction boundaries.


#### There is also ``engine.begin()`` that you can use in the same way and to the same type of things.

Both ``engine.begin()`` and ``Session.begin()`` can help you achive the same goals but they have some differences.

- ``engine.begin()`` is used for low-level, explicit control of database transactions outside of a session context.
- ``session.begin()`` is used for managing transactions within a higher-level SQLAlchemy session context, where transaction management is more automatic and integrated with the session's features.
