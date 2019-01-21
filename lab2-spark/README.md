# Spark Introduction

## RDDs
RDDs can be seen as lists of elements that will be manipulated by applying functions over each element.

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
```
- **flatMap**(func) : Similar to map, but each input item can be mapped to 0 or more output items (so func should return a Seq rather than a single item).
```python
rdd.flatMap(lambda x: [x*x, x*x*x] if x % 2 == 1 else [])
```

- **filter**(func) : Return a new dataset formed by selecting those elements of the source on which func returns true.
```python
rdd.filter(lambda x: x % 3 == 1)
```
- **reduceByKey**(func) : 	When called on a dataset of (K, V) pairs, returns a dataset of (K, V) pairs where the values for each key are aggregated using the given reduce function func, which must be of type (V,V) => V. Like in groupByKey, the number of reduce tasks is configurable through an optional second argument.
```python
data = [(i%3, i) for i in range(10)]
rdd = spark.sparkContext.parallelize(data)
rdd.reduceByKey(lambda x, y: x+y)
```
- **intersection**(dataset) : Return a new RDD that contains the intersection of elements in the source dataset and the argument.
```python
data1 = [i for i in range(10)]
data2 = [i for i in range(5, 15)]
rdd1 = spark.sparkContext.parallelize(data1)
rdd2 = spark.sparkContext.parallelize(data2)
rdd1.intersection(rdd2)
```

- **union**(funct): Return a new dataset that contains the union of the elements in the source dataset and the argument.
```python
data1 = [i for i in range(10)]
data2 = [i for i in range(5, 15)]
rdd1 = spark.sparkContext.parallelize(data1)
rdd2 = spark.sparkContext.parallelize(data2)
rdd1.union(rdd2)
```
- **distinct**() : Return a new dataset that contains the distinct elements of the source dataset.
```python
data = [i for i in range(10)]
data.extend(data)
rdd = spark.sparkContext.parallelize(data)
rdd.distinct()
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

### Operator
- select
- where
- groupBy
- orderBy
	from pyspark.sql.functions import desc
    global_info = global_info.orderBy(desc("size"), desc("interest")).limit(n).select("antecedent", "consequent", "confidence", "items", "freq", "interest")
- limits
- show
- createDataFrame
    plants_rdd = parts.map(lambda p: Row(id=int(p[1]), plant=p[0][0], items=p[0][1:]))
    plants_df = spark.createDataFrame(plants_rdd)
- join
