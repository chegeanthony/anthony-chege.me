---
title: An In-Depth Exploration of Data Analysis - Mechanisms of Data Ingestion
date: '2023-06-04'
tags: ['Python', 'Data analysis', 'Pandas' 'Data ingestion', 'Data Engineering']
draft: false
summary: 'technical overview of data ingestion from diverse sources, such as file systems and databases, employing the Python data manipulation library, pandas.'
---

Originally posted on [creds ~ DevElie](https://twitter.com/dev_elie). [Lux Tech Academy blog.](https://dev.to/luxacademy/intro-to-data-analysis-data-reading-2ncp)

In our modern era characterized by continuous technological evolution, data indisputably reigns supreme as the primary asset for institutions, organizations, and diverse entities. Consequently, the exigency to harness and manipulate the troves of data accessible has become paramount to drive meaningful changes.

Data analytics, an integral subset of data science, is a discipline that concentrates on the transformation and statistical analysis of existing data sets. It places an emphasis on the establishment of robust methods for data capture and organization, with the overarching aim to extrapolate actionable insights that could address prevailing issues, and to identify the most effective means to present and communicate the derived data.

Data analysis is a specific branch of data analytics that is routinely applied in commercial settings to scrutinize data and infer valuable conclusions. The methodology underpinning data analysis involves several stages: data gathering, data cleaning, data examination, and data interpretation, all orchestrated to ensure comprehensive understanding of the messages relayed by the data.

> Source — [Stack Overflow](https://stackoverflow.com/a/57657369/12943692)

To provide a technical introduction to data analysis, this post will elucidate on the methods to ingest data presented in various formats such as CSV, JSON, and database files using Python's Pandas library.

## Table of Contents

1. [Data from a CSV file](#reading-data-from-a-csv-file)
2. [Data in SQL flavour](#reading-data-in-sql-flavour)
3. [JSON files](#reading-data-from-json-files)

## Reading data from a CSV file

Ingestion of data from a comma-separated values (CSV) file into a pandas DataFrame is performed via the `pandas.read_csv` function.

The [read_csv](https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html) function accepts a multitude of parameters, the choice of which hinges on the unique attributes of your dataset or the objective of your analysis.
Some of the most commonly employed parameters, excluding the compulsory `filepath_or_buffer`, encompass `sep, delimiter, header, index_col` and so forth.

### Read comma separated file

The `sep` parameter, which is short for separator, essentially tells the interpreter how to separate the data items in our CSV file.The interpreter assumes that the delimiter used is a comma by default if the sep parameter is not given.

```python
from pyforest import *
df = pd.read_csv("cereal.csv")
df.head()
```

### Read tab separated file

```python
from pyforest import *
df = pd.read_csv("cereal_tab.csv",sep='\t')
df.head()
```

### Read semicolon separated file

```python
from pyforest import *
df = pd.read_csv("cereal_semicolon.csv",sep=';')
df.head()
```

## Reading Data in SQL flavour

This section involves reading data from various SQL relational databases using pandas.

### MySQL database

```python
from pyforest import *
from sqlalchemy import create_engine

# provide a connection string/URL
db_connection_str = "mysql+mysqlconnector://mysql_username:mysql_user_password@localhost/mysql_db_name"
# produce an Engine object based on a URL
db_connection = create_engine(db_connection_str)
# read SQL query or database table into a DataFrame.
df = pd.read_sql('SELECT * FROM table_name', con=db_connection)
# return the first 5 rows of the dataframe
df.head()
```

> Source — [Stack Overflow](https://stackoverflow.com/a/37730334/12943692)

### PostgreSQL

```python
from pyforest import *
from sqlalchemy import create_engine
# produce an Engine object based on a postgresql database URL
engine = create_engine("postgresql:///psql_dbname")
# read SQL query or database table into a DataFrame.
df = pd.read_sql('select * from "user"',con=engine)
# return the first 5 rows of the dataframe
df.head()
```

### SQlite database

```python
from pyforest import *
from sqlalchemy import create_engine

# connect to a database
engine = create_engine("sqlite:///database.db")
# read database data into a pandas DataFrame
df = pd.read_sql('select * from user', engine)
# return the first 5 rows of the dataframe
df.head()
```

## Ingestion of Data from JSON Files

The ingestion of data from a JSON (JavaScript Object Notation) file is as straightforward as reading data from a CSV file. The `pandas.read_json` function facilitates the transformation of a JSON string into a pandas object with relative ease. The primary argument this function requires is `path_or_buf`, which ought to be a valid JSON string, path object, or file-like object. This [function](https://pandas.pydata.org/pandas-docs/version/1.1.3/reference/api/pandas.read_json.html) also accepts a multitude of other parameters.

```python
from pyforest import *
df = pd.read_json('cereal_default.json')
df.head()
```

If you enjoyed this article, please leave a comment, like it, share it, and follow me on Twitter [@Tonycodh](https://twitter.com/Tonycodh).

Credit [@dev_elie](https://twitter.com/dev_elie).

## Reference(s)

1. [analyticsvidhya](https://www.analyticsvidhya.com/blog/2021/04/delimiters-in-pandas-read_csv-function/)
2. [pandas](https://pandas.pydata.org/)
