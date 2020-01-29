# Dask Introduction
The purpose of this lab is to give you a brief introduction on the functioning of Dask. Most examples and content have been taken from [Dask Tutorial](https://github.com/dask/dask-tutorial).

## Dask delayed
Delayed functions are lazily evaluated functions (i.e. only evaluated when result is required / when `compute()` is called).

```python
from dask import delayed
```

Python functions can be declared as they normally would. A `@delayed` decorator can be added to a function to specify a delayed function.

```python
def inc(x):
    return x + 1

@delayed
def add(x, y):
	return x + y
```

Or a function can be passed to the `dask.delayed()` function
```python
x = delayed(inc)(1)
y = delayed(inc)(2)
z = add(x,y)
```

Because `z` is still a delayed object, the `compute()` function needs to be called.

```python
z.compute()
```
```
5
```

The `visualize` function can be used to visualize the graph

```python
z.visualize()
```
![](figures/visualize.png)

All Dask objects have a `dask` attribute that stores the calculations necessary to produce the result in a dictionary.


```python
dict(z.dask)
```
```
{'inc-badf625d-fc77-49c3-a3a8-520ebc7ce0d2': (<function __main__.inc(x)>, 1),
 'inc-cbe2d881-46da-49e4-8e12-9e2a756f4133': (<function __main__.inc(x)>, 2),
 'add-2d84e90c54d3a13bd4fff48a7cc67afd': (<function _operator.add(a, b, /)>,
                                          'inc-badf625d-fc77-49c3-a3a8-520ebc7ce0d2',
                                          'inc-cbe2d881-46da-49e4-8e12-9e2a756f4133')}
```

### Important notes
- Not all functions should be delayed. It may be desirable to immediately execute fast functions which allow us to determine which slow functions need to be called.
- Methods and attribute access on delayed objects work automatically, so if you have a delayed object you can perform normal arithmetic, slicing, and method calls on it and it will produce the correct delayed calls.

```python
x = delayed(np.arange)(10)
y = (x + 1)[::2].sum()  # everything here was delayed
```

## Dask Bags
Dask-bag excels in processing data that can be represented as a sequence of
arbitrary inputs. We'll refer to this as "messy" data, because it can contain
complex nested structures, missing fields, mixtures of data types, etc. The
functional programming style fits very nicely with standard Python iteration,
such as can be found in the itertools module.

### Creation
Dask bags can be created from a Python sequence, files, etc.
```python
# each element is an integer
import dask.bag as db
b = db.from_sequence([1, 2, 3, 4, 5, 6, 7, 8, 9, 10], npartitions=2)
b.take(3)
```
```
(1, 2, 3)
```

### Manipulation
Bag objects hold the standard functional API found in projects like the Python standard library, toolz, or pyspark, including `map`, `filter`, `groupby`, etc..

Operations on Bag objects create new bags. Call the `compute()` method to trigger execution, as we saw for Delayed objects.

```python
def is_even(n):
    return n % 2 == 0

b = db.from_sequence([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
c = b.filter(is_even).map(lambda x: x ** 2)
c
```
```
dask.bag<map-lam..., npartitions=10>
```

```python
c.compute()
```
```
[4, 16, 36, 64, 100]
```

### Groupby and Foldby
Often we want to group data by some function or key. We can do this either with
the `groupby` method, which is straightforward but forces a full shuffle of the
data (expensive) or with the harder-to-use but faster `foldby` method, which
does a streaming combined groupby and reduction.

- `groupby`: Shuffles data so that all items with the same key are in the same key-value pair.
- `foldby`: Walks through the data accumulating a result per key.

Note: the full groupby is particularly bad. In actual workloads you would do well to use foldby or switch to DataFrames if possible.

#### Groupby
Groupby collects items in your collection so that all items with the same value under some function are collected together into a key-value pair.

```python
b = db.from_sequence(['Alice', 'Bob', 'Charlie', 'Dan', 'Edith', 'Frank'])
b.groupby(len).compute()  # names grouped by length
```
```
[(7, ['Charlie']), (3, ['Bob', 'Dan']), (5, ['Edith', 'Alice', 'Frank'])]
```

```python
b = db.from_sequence(list(range(10)))
is_even = lambda x: x % 2
b.groupby(is_even).starmap(lambda k, v: (k, max(v))).compute()
```
```
[(0, 8), (1, 9)]
[(0, 8), (1, 9)]
```
#### Foldby
When using foldby you provide:

- A key function on which to group elements
- A binary operator such as you would pass to reduce that you use to perform reduction per each group
- A combine binary operator that can combine the results of two reduce calls on different parts of your dataset.

Your reduction must be associative. It will happen in parallel in each of the partitions of your dataset. Then all of these intermediate results will be combined by the combine binary operator.

```python
b.foldby(is_even, binop=max, combine=max).compute()
```
```
[(0, 8), (1, 9)]
```

#### Limitations
Bags provide very general computation (any Python function.) This generality comes at cost. Bags have the following known limitations

- Bag operations tend to be slower than array/dataframe computations in the same way that Python tends to be slower than NumPy/Pandas
- Bag.groupby is slow. You should try to use Bag.foldby if possible. Using Bag.foldby requires more thought. Even better, consider creating a normalised dataframe.

## Dask Arrays
Dask array provides a parallel, larger-than-memory, n-dimensional array using blocked algorithms. Simply put: distributed Numpy.

### Create dask.array object
You can create a dask.array Array object with the da.from_array function. This function accepts

- data: Any object that supports NumPy slicing
- chunks: A chunk size to tell us how to block up our array, like (1000000,)


```python
import dask.array as da
x = np.random.rand(100000000) # create random numpy array
y = da.from_array(x, chunks=(1000000,))
```

### Manipulate dask.array objct as you would a numpy array
Now that we have an Array we perform standard numpy-style computations like arithmetic, mathematics, slicing, reductions, etc..


```python
result = y.sum()
result.compute()
```
```
49993551.54938029
```
```python
y[0:4].compute()
```
```
array([0.54890611, 0.84501265, 0.0433674 , 0.4306912 ])
```
### Limitations
Dask.array does not implement the entire numpy interface. Users expecting this will be disappointed. Notably dask.array has the following failings:

- Dask does not implement all of np.linalg. This has been done by a number of excellent BLAS/LAPACK implementations and is the focus of numerous ongoing academic research projects.
- Dask.array does not support any operation where the resulting shape depends on the values of the array. In order to form the Dask graph we must be able to infer the shape of the array before actually executing the operation. This precludes operations like indexing one Dask array with another or operations like np.where.
- Dask.array does not attempt operations like sort which are notoriously difficult to do in parallel and are of somewhat diminished value on very large data (you rarely actually need a full sort). Often we include parallel-friendly alternatives like `topk`.
- Dask development is driven by immediate need, and so many lesser used functions, like `np.full_like` have not been implemented purely out of laziness. These would make excellent community contributions.

## Dask Dataframes
The dask.dataframe module implements a blocked parallel DataFrame object that mimics a large subset of the Pandas DataFrame. One Dask DataFrame is comprised of many in-memory pandas DataFrames separated along the index. One operation on a Dask DataFrame triggers many pandas operations on the constituent pandas DataFrames in a way that is mindful of potential parallelism and memory constraints.


```python
import dask.dataframe as dd

# Note: datatypes are inferred by reading the first couple of lines
# and may be incorrect and therefore need to be supplied
df = dd.read_csv('frenepublicinjection2015.csv',
dtype={"No_Civiq": str, "Nom_parc": str})
# load and count number of rows
df.head()
```
![](figures/head.png)

```python
# len is applied to each individual pandas dataframe
# and then the subtotals are combined by Dask
len(df)
```
```
21914
```
```python
df.latitude.max().visualize()
```
![](figures/visualize_df.png)


### Limitations
Dask.dataframe only covers a small but well-used portion of the Pandas API. This limitation is for two reasons:

- The Pandas API is huge
- Some operations are genuinely hard to do in parallel (e.g. sort)

Additionally, some important operations like `set_index` work, but are slower than in Pandas because they include substantial shuffling of data, and may write out to disk.
