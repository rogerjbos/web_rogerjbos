---
title: Postgresql Examples in Python
date: '2019-02-25'
slug: Pytest-post
categories: []
tags: []
description: 'postgresql examples in Python'
---

# Python: SQLAlchemy ORM versus Raw SQL

I was reading this blog post [https://auth0.com/blog/sqlalchemy-orm-tutorial-for-python-developers/] about SQLAlchemy and how to run queries using its Object Relational Mapper (ORM).  It is a very good intruduction to the pythonic ORM way of talking to databases, but I think I still prefer using raw SQL.  In this post I will show you some reasons why I prefer raw SQL.  I will use the same database and tables used in the original tutorial for these examples.


We will import sqlalchemy to connect to the Postgres database and pandas to execute the raw SQL statements.


```python
import sqlalchemy as sqla
from sqlalchemy.orm import sessionmaker
import pandas as pd
from datetime import date

```

If you get any errors, please see the original blog post above for the requirements that need to be installed, such as sqlalchemy.  I am using pandas later on in this blog, so you would need to have that installed too.  The 'psycopg2' module is also used, and has been renamed, so I would recommend something like the following to install it:



```python
pip install psycopg2-binary
```

Again, the original blog post above creates the classes and data for the example tables.  Once they are created, here is the code to import them.


```python

from base import Session
from actor import Actor
from contact_details import ContactDetails
from movie import Movie

```

    /Users/rjbos2/anaconda3/lib/python3.7/site-packages/psycopg2/__init__.py:144: UserWarning: The psycopg2 wheel package will be renamed from release 2.8; in order to keep installing from binary please use "pip install psycopg2-binary" instead. For details see: <http://initd.org/psycopg/docs/install.html#binary-install-from-pypi>.
      """)


The code below shows the connection string for a postgres database named roger with username 'usr' and password 'pass' running on 127.0.0.1 (also knwon as localhost) port 5432 (the default port).


```python
xf = sqla.create_engine('postgresql://usr:pass@localhost:5432/roger')
Session = sessionmaker(bind=xf)
session = Session()
```

Finally, we are able to run our first query.  We are going to query all the movies defined by the Movie class and stored in the movies table.


```python
movies = session.query(Movie).all()
```

The movies object is a class, so you can't just its contents directly.


```python
movies
```




    [<movie.Movie at 0x1177f5be0>,
     <movie.Movie at 0x1177f5c88>,
     <movie.Movie at 0x1177f5908>]



Instead you have to use a for loop to print out each row of data.


```python
for movie in movies:
     print(f'{movie.title} was released on {movie.release_date}')
```

    The Bourne Identity was released on 2002-10-11
    Furious 7 was released on 2015-04-02
    Pain & Gain was released on 2013-08-23


Whereas using the read_sql_query function from pandas with raw SQL gives use hte returned rows in a nice format that prints easily. 


```python
pd.read_sql_query('select * from movies', xf)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>title</th>
      <th>release_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>The Bourne Identity</td>
      <td>2002-10-11</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Furious 7</td>
      <td>2015-04-02</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Pain &amp; Gain</td>
      <td>2013-08-23</td>
    </tr>
  </tbody>
</table>
</div>



Using raw SQL is a quick way to remind yourself of the column names of each table you are using without having to refer back to the class object.  These tables are very small, but with bigger tables you would want to limit the number of rows returned.  For many databases you use the word 'top' to do so, but for Postgres (and MySQL) you use 'limit', as is done in the next few queries.


```python
pd.read_sql_query('select * from actors limit 3', xf)

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>name</th>
      <th>birthday</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Matt Damon</td>
      <td>1970-10-08</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Dwayne Johnson</td>
      <td>1972-05-02</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Mark Wahlberg</td>
      <td>1971-06-05</td>
    </tr>
  </tbody>
</table>
</div>




```python
pd.read_sql_query('select * from movies_actors limit 4', xf)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movie_id</th>
      <th>actor_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>




```python

pd.read_sql_query('select * from contact_details limit 4', xf)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>phone_number</th>
      <th>address</th>
      <th>actor_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>415 555 2671</td>
      <td>Burbank, CA</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>423 555 5623</td>
      <td>Glendale, CA</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>421 444 2323</td>
      <td>West Hollywood, CA</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>421 333 9428</td>
      <td>Glendale, CA</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>



SQLAlchemy also has an inspect method to list the names of all the tables in a database.


```python
inspector = sqla.inspect(xf)
print(inspector.get_table_names())
```

    ['returns', 'actors', 'contact_details', 'movies', 'movies_actors', 'stuntmen']


Now we will look at how to run some more complex queries.  Say we want to find all the movies that have been released since 2015.


```python
# get movies after 15-01-01
movies = session.query(Movie) \
     .filter(Movie.release_date >= date(2015, 1, 1)) \
     .all()

for movie in movies:
     print(f'{movie.title} was released since 2015')

```

    Furious 7 was released since 2015


This can be accomplished in raw SQL as follows


```python
pd.read_sql_query("select * from movies \
where release_date >= '1/1/2015'", xf)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>title</th>
      <th>release_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>Furious 7</td>
      <td>2015-04-02</td>
    </tr>
  </tbody>
</table>
</div>



Continuing with the examples, we can find all the movies featuring 'Dwayne Johnson'


```python
the_rock_movies = session.query(Movie) \
    .join(Actor, Movie.actors) \
    .filter(Actor.name == 'Dwayne Johnson') \
    .all()

for movie in the_rock_movies:
    print(f'The Rock starred in {movie.title}')

```

    The Rock starred in Pain & Gain
    The Rock starred in Furious 7


In the raw SQL version we have to use the movies_actors table to join the ids from the movies and actors tables.  There is more than one way to do this join, but here is an easy and straightforward method.


```python
pd.read_sql_query("select m.title as Movie, a.name as Actor, m.release_date as Released \
                  from movies m, actors a, movies_actors ma \
                  where m.id=movie_id and a.id=actor_id and a.name = 'Dwayne Johnson'", xf)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movie</th>
      <th>actor</th>
      <th>released</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Pain &amp; Gain</td>
      <td>Dwayne Johnson</td>
      <td>2013-08-23</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Furious 7</td>
      <td>Dwayne Johnson</td>
      <td>2015-04-02</td>
    </tr>
  </tbody>
</table>
</div>



As another example of joining and filtering, we can also find all the actors that live in Glendale. SQL uses the '%' sign to indicate a wildcard, so there can be any number of characters before and after 'glendale' and the 'like' parameter will find a match.  If you knew all the addresses started (or ended) with 'glendale', you could use '%glendale' (or 'glendale%').


```python
glendale_stars = session.query(Actor) \
    .join(ContactDetails) \
    .filter(ContactDetails.address.ilike('%glendale%')) \
    .all()

for actor in glendale_stars:
    print(f'{actor.name} has a house in Glendale')
```

    Dwayne Johnson has a house in Glendale
    Mark Wahlberg has a house in Glendale


The following raw SQL query does not work.  Can you spot the problem?  (hint: There are actually two problems.)


```python
pd.read_sql_query("select a.name \
                  from actors a, contact_details c \
                  where a.id=c.actor_id and c.address like '%glendale%'", xf)

```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-17-603009ecee61> in <module>
          1 pd.read_sql_query("select a.name \
          2                   from actors a, contact_details c \
    ----> 3                   where a.id=c.actor_id and c.address like '%glendale%'", xf)
    

    ~/anaconda3/lib/python3.7/site-packages/pandas/io/sql.py in read_sql_query(sql, con, index_col, coerce_float, params, parse_dates, chunksize)
        312     return pandas_sql.read_query(
        313         sql, index_col=index_col, params=params, coerce_float=coerce_float,
    --> 314         parse_dates=parse_dates, chunksize=chunksize)
        315 
        316 


    ~/anaconda3/lib/python3.7/site-packages/pandas/io/sql.py in read_query(self, sql, index_col, coerce_float, parse_dates, params, chunksize)
       1061         args = _convert_params(sql, params)
       1062 
    -> 1063         result = self.execute(*args)
       1064         columns = result.keys()
       1065 


    ~/anaconda3/lib/python3.7/site-packages/pandas/io/sql.py in execute(self, *args, **kwargs)
        952     def execute(self, *args, **kwargs):
        953         """Simple passthrough to SQLAlchemy connectable"""
    --> 954         return self.connectable.execute(*args, **kwargs)
        955 
        956     def read_table(self, table_name, index_col=None, coerce_float=True,


    ~/anaconda3/lib/python3.7/site-packages/sqlalchemy/engine/base.py in execute(self, statement, *multiparams, **params)
       2073 
       2074         connection = self.contextual_connect(close_with_result=True)
    -> 2075         return connection.execute(statement, *multiparams, **params)
       2076 
       2077     def scalar(self, statement, *multiparams, **params):


    ~/anaconda3/lib/python3.7/site-packages/sqlalchemy/engine/base.py in execute(self, object, *multiparams, **params)
        940         """
        941         if isinstance(object, util.string_types[0]):
    --> 942             return self._execute_text(object, multiparams, params)
        943         try:
        944             meth = object._execute_on_connection


    ~/anaconda3/lib/python3.7/site-packages/sqlalchemy/engine/base.py in _execute_text(self, statement, multiparams, params)
       1102             statement,
       1103             parameters,
    -> 1104             statement, parameters
       1105         )
       1106         if self._has_events or self.engine._has_events:


    ~/anaconda3/lib/python3.7/site-packages/sqlalchemy/engine/base.py in _execute_context(self, dialect, constructor, statement, parameters, *args)
       1198                 parameters,
       1199                 cursor,
    -> 1200                 context)
       1201 
       1202         if self._has_events or self.engine._has_events:


    ~/anaconda3/lib/python3.7/site-packages/sqlalchemy/engine/base.py in _handle_dbapi_exception(self, e, statement, parameters, cursor, context)
       1414                 )
       1415             else:
    -> 1416                 util.reraise(*exc_info)
       1417 
       1418         finally:


    ~/anaconda3/lib/python3.7/site-packages/sqlalchemy/util/compat.py in reraise(tp, value, tb, cause)
        247         if value.__traceback__ is not tb:
        248             raise value.with_traceback(tb)
    --> 249         raise value
        250 
        251 else:


    ~/anaconda3/lib/python3.7/site-packages/sqlalchemy/engine/base.py in _execute_context(self, dialect, constructor, statement, parameters, *args)
       1191                         statement,
       1192                         parameters,
    -> 1193                         context)
       1194         except BaseException as e:
       1195             self._handle_dbapi_exception(


    ~/anaconda3/lib/python3.7/site-packages/sqlalchemy/engine/default.py in do_execute(self, cursor, statement, parameters, context)
        507 
        508     def do_execute(self, cursor, statement, parameters, context=None):
    --> 509         cursor.execute(statement, parameters)
        510 
        511     def do_execute_no_params(self, cursor, statement, context=None):


    TypeError: 'dict' object does not support indexing


The interpreter throws a really long error with absolutely no clue as to the actual problem, which is that Python treats '%' as a special character.  So if you want to use the '%' character in a string, you need to double it, like so...


```python
pd.read_sql_query("select a.name \
                  from actors a, contact_details c \
                  where a.id=c.actor_id and c.address like '%%glendale%%'", xf)

```

Now the query runs without error, but it does not return us the two rows we are expecting.  That leads us to the second problem, which is that 'like' is case sensitive.  


```python
pd.read_sql_query("select a.name \
                  from actors a, contact_details c \
                  where a.id=c.actor_id and c.address like '%%Glendale%%'", xf)


```

The problem now is that if some of the city names were capitalized and some were not, the query would not be accurate.  A better fix is to use ilike which is not case sensitive.


```python
pd.read_sql_query("select a.name \
                  from actors a, contact_details c \
                  where a.id=c.actor_id and c.address ilike '%%glendale%%'", xf)

```

That's all I wanted to show you based on the small Movies database.  We spent a lot of time disecting that last query, but I hope it was helpful in your future SQL debugging efforts.  Those debugging steps should be helpful whether you decide to use ORM or raw SQL.  

To show some more examples of raw SQL, we will build a small table of stock prices for GE.

## A Stock Example

The pandas module has a nice function to read in stock prices from Yahoo!


```python
from pandas_datareader import data as wb  
ticker = 'GE' 
GE = wb.DataReader(ticker, data_source='yahoo', start='2007-1-1')['Adj Close']
ret = pd.DataFrame(data = GE)
ret.tail()
```

Lets say we wanted to save this price history into a database.  Using the ORM method, we would have to create a class for the table, but using pandas and raw SQL we can just save the DataFrame to a table and use it right away.  In the first line I drop the table in case it already exists and in the second line the DataFrame 'ret' is saved to a table named 'returns' in the 'xf' database.


```python
xf.execute("drop table returns")
ret.to_sql('returns', xf)
```

To see if the save worked, we can query the table, as we do below.


```python
pd.read_sql_query("select * from returns limit 4", xf)
```

You can see the table output and everything looks fine.  If I ended the post here everything would seem pretty simple and straightforward, but there are many problems which would make it hard for someone new to Python and SQL to work with this table.

In the above query everything worked smoothly because I used the '*' wildcard and didn't specify the column names.  What problems would I encounter if I tried to use the actual column names?  I can predict that Adj Close will be a problem because of the space, so I will quote it.


```python
pd.read_sql_query("select Date, 'Adj Close' from returns limit 4", xf)
```

Python doesn't like the column "Date" either.  It turns out that Postgres SQL doesn't like any column names that are capitalized.  Maybe that's one reason to use ORM, but a simple fix is just to adjust the column names before you save the table.


```python
ret = ret.rename(index=str, columns={"Adj Close": "adj_close"})
ret.index.name = "date"
ret.tail()
```

The above code shows two different methods for modifying column names.  For regular columns you just use the rename function in pandas.  For the index of the dataframe, you have to do something different.  'Date' is an index rather than a column, so we had to use the 'name' function change its name.  Now both column are lower case and do not contain spaces.  We are making everyone happy.  Now lets add a column with the return.


```python
ret['return'] = ret['adj_close'].pct_change()
ret.tail()
```

The DataFrame looks good now.  Lets save this new DataFrame to a table so I can show you what I really want to show you.


```python
xf.execute("drop table returns")
ret.to_sql('returns', xf)
```

Then test out the save and make sure it worked.


```python
out = pd.read_sql_query("select * from returns limit 10", xf)
out.head()
```

Just to verify, we can now specify the column names without a problem.


```python
pd.read_sql_query("select date, adj_close, return from returns limit 10", xf)
```

We can condition on date to get the daily returns for January.


```python
pd.read_sql_query("select date, returns from returns \
                  where date between '2019-01-01' and '2019-02-01'", xf)


```

Now I can finally show you a good example of something you can do in raw SQL.  If you have a table of daily returns, you can calculate cumulative returns for any time period within SQL.  SQL does not have a product function, but you can sum the log returns and then exponentiate them to accomplish the same thing.


```python
pd.read_sql_query("select EXP(SUM(LOG(1+return))) - 1 as cumret from returns \
                  where date between '2019-01-01' and '2019-02-01'", xf)

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cumret</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.136323</td>
    </tr>
  </tbody>
</table>
</div>



We can also calculate the standard deviation of daily returns in the same query as below. Let's expand the window to six months.


```python
pd.read_sql_query("select EXP(SUM(LOG(1 + return))) - 1 as cumret, \
                  SQRT(21 * SUM(return * return) / 126) as volatility \
                  from returns \
                  where date between '2018-06-30' and '2018-12-31'", xf)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cumret</th>
      <th>volatility</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.223921</td>
      <td>0.12785</td>
    </tr>
  </tbody>
</table>
</div>



I hope this tutorial has been helpful.  We looked at a number of queries using with ORM and raw SQL and reviewed some of the pitfalls you may encounter when working with databases in Python.  The ORM approach is probably a good one for large applications with fixed database structions, but raw SQL is a good approach for smaller projects and ad-hoc analysis.
