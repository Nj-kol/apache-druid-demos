
# Python Client for Druid : pydruid

## Install

```bash
pip install pydruid
```

## Query

```python
from sqlalchemy.engine import create_engine

druid_url = 'druid://localhost:30282/druid/v2/sql'
engine = create_engine(druid_url)
conn = engine.connect()

query = 'select * from network_flow limit 10'

result = conn.execute(query)

for row in result:
    print(row)

conn.close()
```

# Notes

* Here the host and port is that of druid broker