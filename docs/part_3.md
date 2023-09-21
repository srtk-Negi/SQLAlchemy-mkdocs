# Examples on how to create a Database connection and execute queries

```python
from sqlalchemy import create_engine

conn_string = = f"mssql+pyodbc://@{SERVER}/{DATABASE}?driver={DRIVER}&Encrypt=no"
engine = create_engine(conn_string)

select_query = select(table_1).where(table_1.c.name == "John")
insert_query = insert(table_1).values(index=10, name="Adam", grade="A")

## To execute a SELECT query
with engine.connect() as conn:
    result = conn.execute(select_query)
    for row in result:
        print(row)

## To execute an INSERT query
with engine.connect() as conn:
    conn.execute(insert_query)
    conn.commit()

```