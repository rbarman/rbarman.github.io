Title: SQL Queries in Pandas
Date: 2023-09-10
Tags: Pandas, DuckDB

I spend a lot of development time in PySpark applications and like to use SparkSQL for transformations. It is very easy to run SparkSQL queries on a Spark DataFrame. 

```python
source_df = spark.read.format(format).load(path)
source_df.createOrReplaceTempView(source)
```

```sql
SELECT *
FROM source
LIMIT 10
```

I have several projects that use Pandas and I want a similar workflow. Why bother learning the Pandas API for transformations when SQL is more elegant and evergreen? Many resources online recommend to use [pandassql](https://pypi.org/project/pandasql/). Unfortunately it has not been updated in over 7 years and it does not work. 

I learned that [DuckDB](https://duckdb.org/2021/05/14/sql-on-pandas.html) is a better approach. 

```python

import pandas as pd
import duckdb

source = pd.read_csv(path)

def sql(query: str) -> pd.DataFrame:
    return duckdb.query(query).to_df()

sql("""
    SELECT *
    FROM source
    LIMIT 10
""")

```
It is able to recognize existing dataframes by name and execute plain SQL. The result of my `sql` function returns a Pandas DataFrame that also be referenced by future `sql` calls. 

```python
clean_source = sql("""
    SELECT *
    FROM source
    WHERE id > 0
""")

sql("""
    SELECT *
    FROM clean_source
    LIMIT 10
""")
```