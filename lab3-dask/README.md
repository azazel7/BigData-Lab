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

All Dask objects have a `dask` attribute that stores the calculations necessary to produce the result in a dictionary
