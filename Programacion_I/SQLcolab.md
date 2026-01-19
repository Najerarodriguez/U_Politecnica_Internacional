```python
# CREATE TABLE
import sqlite3
conn = sqlite3.connect(":memory:")
cur = conn.cursor()

cur.execute("""
CREATE TABLE clientes (
    cliente_id INTEGER PRIMARY KEY,
    nombre TEXT,
    edad INTEGER,
    ingreso REAL
);
""")
conn.commit()
```

```python
# INSERT
import sqlite3
conn = sqlite3.connect(":memory:")
cur = conn.cursor()

cur.execute("""
CREATE TABLE clientes (
    cliente_id INTEGER,
    nombre TEXT,
    edad INTEGER,
    ingreso REAL
);
""")

cur.execute("""
INSERT INTO clientes VALUES (1,'Ana',30,1200.50);
""")
conn.commit()
```

```python
# INSERT MULTIPLE
import sqlite3
conn = sqlite3.connect(":memory:")
cur = conn.cursor()

cur.execute("""
CREATE TABLE clientes (
    cliente_id INTEGER,
    nombre TEXT,
    edad INTEGER,
    ingreso REAL
);
""")

cur.executemany(
    "INSERT INTO clientes VALUES (?,?,?,?)",
    [(1,"Ana",30,1200.5),(2,"Luis",40,2200.0)]
)
conn.commit()
```

```python
# SELECT *
import sqlite3, pandas as pd
conn = sqlite3.connect(":memory:")
df = pd.read_sql_query("SELECT * FROM clientes;", conn)
df
```

```python
# SELECT COLUMNAS
import sqlite3, pandas as pd
conn = sqlite3.connect(":memory:")
df = pd.read_sql_query("SELECT nombre, ingreso FROM clientes;", conn)
df
```

```python
# WHERE
import sqlite3, pandas as pd
conn = sqlite3.connect(":memory:")
df = pd.read_sql_query(
    "SELECT * FROM clientes WHERE edad > 35;", conn
)
df
```

```python
# ORDER BY
import sqlite3, pandas as pd
conn = sqlite3.connect(":memory:")
df = pd.read_sql_query(
    "SELECT * FROM clientes ORDER BY ingreso DESC;", conn
)
df
```

```python
# LIMIT
import sqlite3, pandas as pd
conn = sqlite3.connect(":memory:")
df = pd.read_sql_query(
    "SELECT * FROM clientes LIMIT 1;", conn
)
df
```

```python
# UPDATE
import sqlite3
conn = sqlite3.connect(":memory:")
cur = conn.cursor()

cur.execute("""
UPDATE clientes
SET ingreso = ingreso * 1.1
WHERE cliente_id = 1;
""")
conn.commit()
```

```python
# DELETE
import sqlite3
conn = sqlite3.connect(":memory:")
cur = conn.cursor()

cur.execute("""
DELETE FROM clientes
WHERE cliente_id = 2;
""")
conn.commit()
```

```python
# COUNT
import sqlite3, pandas as pd
conn = sqlite3.connect(":memory:")
df = pd.read_sql_query(
    "SELECT COUNT(*) AS total FROM clientes;", conn
)
df
```

```python
# GROUP BY
import sqlite3, pandas as pd
conn = sqlite3.connect(":memory:")
df = pd.read_sql_query(
    "SELECT edad, COUNT(*) total FROM clientes GROUP BY edad;", conn
)
df
```

```python
# HAVING
import sqlite3, pandas as pd
conn = sqlite3.connect(":memory:")
df = pd.read_sql_query(
    "SELECT edad, COUNT(*) total FROM clientes GROUP BY edad HAVING total > 1;", conn
)
df
```

```python
# INNER JOIN
import sqlite3, pandas as pd
conn = sqlite3.connect(":memory:")
cur = conn.cursor()

cur.execute("CREATE TABLE ventas (cliente_id INT, monto REAL);")
cur.execute("INSERT INTO ventas VALUES (1,500);")

df = pd.read_sql_query("""
SELECT c.nombre, v.monto
FROM clientes c
INNER JOIN ventas v ON c.cliente_id = v.cliente_id;
""", conn)
df
```

```python
# DROP TABLE
import sqlite3
conn = sqlite3.connect(":memory:")
cur = conn.cursor()

cur.execute("DROP TABLE IF EXISTS clientes;")
conn.commit()
```
