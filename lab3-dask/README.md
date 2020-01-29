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

The `visualize` function can be used to visualize the graph

```python
z.visualize()
```
![](figures/visualize.png)

All Dask objects have a `dask` attribute that stores the calculations necessary to produce the result in a dictionary.


```python
dict(z.dask)
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

```python
c.compute()
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

```python
b = db.from_sequence(list(range(10)))
is_even = lambda x: x % 2
b.groupby(is_even).starmap(lambda k, v: (k, max(v))).compute()
```

