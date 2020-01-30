# Spark Introduction
## Cheat Sheets
- [Pyspark API](https://s3.amazonaws.com/assets.datacamp.com/blog_assets/PySpark_Cheat_Sheet_Python.pdf)
- [Dataframe API](https://s3.amazonaws.com/assets.datacamp.com/blog_assets/PySpark_SQL_Cheat_Sheet_Python.pdf)

## Starting Pyspark
After loading your virtual environment, run the following command:
```bash
pyspark
```

## RDDs
Resilient Distributed Datasets (RDDs) can be seen as lists of elements that
will be manipulated by applying functions over each element.  A more complete
documentation is available
[here](http://spark.apache.org/docs/latest/rdd-programming-guide.html).

```python
filename = "trees2016.csv"
lines = spark.sparkContext.textFile(filename)
data = [i for i in range(10)]
rdd = spark.sparkContext.parallelize(data)
```
`lines` can be seen as a list of independant strings and `rdd` as a list of integer ranging from 0 to 9.

### Action Methods
- **collect**() : This function will collect the RDD into a python list.
- **take**(k) : This function create an RDD from `k` element within the RDD.
- **count**() : This function returns the number of element within the RDD.

### Transformation Methods
- **map**(func) : Return a new distributed dataset formed by passing each element of the source through a function func.
```python
lines = lines.map(lambda l: l.split(','))
rdd.map(lambda x: x*x)
#Output [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```
- **flatMap**(func) : Similar to map, but each input item can be mapped to 0 or more output items (so func should return a Seq rather than a single item).
```python
rdd.flatMap(lambda x: [x*x, x*x*x] if x % 2 == 1 else [])
#Output [1, 1, 9, 27, 25, 125, 49, 343, 81, 729]
```

- **filter**(func) : Return a new dataset formed by selecting those elements of the source on which func returns true.
```python
rdd.filter(lambda x: x % 3 == 1)
#Output [1, 4, 7]
```
- **reduceByKey**(func) : 	When called on a dataset of (K, V) pairs, returns a dataset of (K, V) pairs where the values for each key are aggregated using the given reduce function func, which must be of type (V,V) => V.
```python
data = [(i%3, i) for i in range(10)]
rdd = spark.sparkContext.parallelize(data)
rdd.reduceByKey(lambda x, y: x+y)
#Output [(0, 18), (1, 12), (2, 15)]
```
- **intersection**(dataset) : Return a new RDD that contains the intersection of elements in the source dataset and the argument.
```python
data1 = [i for i in range(10)]
data2 = [i for i in range(5, 15)]
rdd1 = spark.sparkContext.parallelize(data1)
rdd2 = spark.sparkContext.parallelize(data2)
rdd1.intersection(rdd2)
#Output [5, 6, 7, 8, 9]
```

- **union**(funct): Return a new dataset that contains the union of the elements in the source dataset and the argument.
```python
data1 = [i for i in range(10)]
data2 = [i for i in range(5, 15)]
rdd1 = spark.sparkContext.parallelize(data1)
rdd2 = spark.sparkContext.parallelize(data2)
rdd1.union(rdd2)
#Output [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14]
```
- **distinct**() : Return a new dataset that contains the distinct elements of the source dataset.
```python
data = [i%3 for i in range(10)]
rdd = spark.sparkContext.parallelize(data)
rdd.distinct()
#Output [0, 1, 2]
```

- **zipWithIndex**() : Assign an index to each element in the RDD.
```python
data = [i for i in range(10)]
rdd = spark.sparkContext.parallelize(data)
rdd.zipWithIndex()
#Output [(0, 0), (1, 1), (2, 2), (3, 3), (4, 4), (5, 5), (6, 6), (7, 7), (8, 8), (9, 9)]
```
- **groupByKey**() : When called on a dataset of (K, V) pairs, returns a dataset of (K, Iterable<V>) pairs. 
```python
data = [(i%3, i) for i in range(10)]
rdd = spark.sparkContext.parallelize(data)
rdd.groupByKey()
```

## Dataframes
Dataframes are structured like tables in SQL or like Excel spreadsheet.

| Tree | Park Name   | x  | y  |
|------|-------------|----|----|
| 1    | Canada Park | 2  | 3  |
| 2    | Otter Park  | 63 | 21 |
| 3    | Canada Park | 2  | 3  |

To load a csv file into a dataframe:
```python
from pyspark.sql import SparkSession
spark = SparkSession._create_shell_session()
df = spark.read.csv(filename, header=True, mode="DROPMALFORMED")
```
### Operators

- **show**() : Display the first lines of a dataframe.
- **select**(column name, another column name) : Return a dataframe with only the column selected.
```python
df = df.select("Park Name")
```
- **where**("action") : Remove lines that does not fit the action.
```python
df = df.where("x = 2")
```
- **groupBy**(): Group rows together using aggregate function such as min, max or avg.
```python
df.groupBy("x").count()
df.groupBy("x").sum()
df.groupBy("x").mean()
```
- **orderBy**("column1", "column2", ...): Order the dataframe with one or more column.
```python
from pyspark.sql.functions import desc
df.orderBy(desc("Park Name"))
```
- **limit**(number) : Drop all rows after the `number` th row.
```python
df.limit(7)
```
- **createDataFrame**(rdd) : Create a dataframe from an RDD.
```python
from pyspark.sql import Row
parts = spark.sparkContext.textFile("frenepublicinjection2015.csv")
parts = parts.map(lambda x: x.split(","))
trees_rdd = parts.map(lambda p: Row(id=p[0], park_name=p[1], x=p[2], y=p[3]))
trees_df = spark.createDataFrame(trees_rdd)
```
- **join**() : Combine dataframe based on common column.
```python
df = df1.join(df2, df2.park_name == df1.park_name)
```

## Assignment 1
### What to do
The goal of the assignment is to code the empty functions in `answers/answer.py`.

Once you have writen the function you can run the corresponding test as follow:
```
pytest tests/test_count.py 						#Run the test functions in test_count.py
pytest tests/test_count.py tests/test_park.py	#Run the test functions in test_count.py and test_park.py
pytest tests/test_count*						#Run the test functions in test_count.py, test_count_rdd.py and test_count_df.py
pytest tests									#Run all tests
```
### CSV
The CSV format is a format where the data is separated by commas and new lines.
```
name,age,address
Martin,22,"40 Oakland street, Montréal"
Tristan,37,"1024 Ford street, Kuujjuaq"
Ali,26,"23 St-Catherine street, Québec"
```
### Dataset
The dataset for assignment 1 is a CSV file that contains one injection per line.
A tree is treated once a year.

Since the header and its documentation are in French, here is a quick translation of what each column means:

- **Nom_arrond** :  Neighborhood name.
- **Invent** : Type of tree injected (H = out of street, R = in street)
- **No_Civiq** : Number of the building in front of the tree.
- **Rue** : Street name if the tree is in the street (see **Invent**).
- **Rue_De** : Street name of the street that delimit the area where the tree is planted.
- **Rue_a** : Street name of the street that delimit the area where the tree is planted. (Combined with **Rue_De**.)
- **Nom_parc** : Name of the park in which the tree is planted (see **Invent**).
- **Sigle** : The first two letter of the latin name of the tree.
- **Injections** : Year of the injection.
- **x** : The x coordinate based on the Mercator projection of Québec.
- **y** : The y coordinate based on the Mercator projection of Québec.
- **longitude** and **latitude**
