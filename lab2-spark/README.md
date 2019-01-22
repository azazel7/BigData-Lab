# Spark Introduction

## RDDs
RDDs can be seen as lists of elements that will be manipulated by applying functions over each element.
A more complete documentation is available [here](http://spark.apache.org/docs/latest/rdd-programming-guide.html).

```python
filename = "trees2016.csv"
lines = spark.sparkContext.textFile(filename, minPartitions=8)
data = [i for i in range(10)]
rdd = spark.sparkContext.parallelize(data)
```
`lines` can be seen as a list of independant strings and `rdd` as a list of integer ranging from 0 to 9.

- **collect**() : This function will collect the RDD into a python list.
- **take**(k) : This function create an RDD from `k` element within the RDD.
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
- **reduceByKey**(func) : 	When called on a dataset of (K, V) pairs, returns a dataset of (K, V) pairs where the values for each key are aggregated using the given reduce function func, which must be of type (V,V) => V. Like in groupByKey, the number of reduce tasks is configurable through an optional second argument.
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
- groupBy
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
trees_rdd = parts.map(lambda p: Row(id=int(p[1]), park_name=p[1], x=p[2], y=p[3]))
trees_df = spark.createDataFrame(trees_rdd)
```
- **join**() : Combine dataframe based on common column.
```python
df = df1.join(df2, df2.park_name == df1.park_name)
```
