# scikit-learn
The goal of this laboratory is to show you that using classifier in python is straightforward.

## Install scikit-learn
To install scikit-learn you can use `pip`. Note that Numpy and SciPy are dependencies of scikit-learn.
```
pip install -U numpy
pip install -U scipy
pip install -U scikit-learn
```
or `conda`:
```
conda install scikit-learn
```

## On orwell
All packages are installed on Orwell, all you need to do is load python module.
```
module load python/3.5.1
python
>>> import sklearn
```


## First Classification
Let's set-up the python environment and have a look at the data:
```python
import numpy as np
from sklearn import datasets
iris = datasets.load_iris()
print(iris.feature_names)
print(iris.data)
print(iris.target)
print(iris.target_names)
```

Then we create the training set and the test set:
```python
from sklearn.model_selection import train_test_split
X = iris.data
y = iris.target
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.4) # 60% training and 40% test
```

The classifier is initiated then trained.
```python
from sklearn.ensemble import RandomForestClassifier
rf = RandomForestClassifier(n_estimators=10, random_state=123456)
rf = rf.fit(X_train, y_train)
```

Finally we test our model:
```python
from sklearn.metrics import accuracy_score
predicted = rf.predict(X_test)
accuracy = accuracy_score(y_test, predicted)
print(f'Mean accuracy score: {accuracy:.3}')
```

Using the KFold split instead:
```python
from sklearn.model_selection import KFold
kf = KFold(n_splits=3)
for train, test in kf.split(X):
	print("%s %s" % (train, test))
```

## Second classification
Dowload the dataset [here](https://archive.ics.uci.edu/ml/machine-learning-databases/00240/UCI%20HAR%20Dataset.zip) then extract it.

Your goal is to train a model on this dataset.
- Load the sets (training and testing sets).
- Initialize your classifier.
- Train your classifier.
- Test your classifier.
- Check the accuracy of your classifier.


This a solution:
```python
import numpy as np
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score
from io import StringIO 

X_train = np.loadtxt(StringIO(open("train/X_train.txt", "r").read()))
y_train = np.loadtxt(StringIO(open("train/y_train.txt", "r").read()))
X_test = np.loadtxt(StringIO(open("test/X_test.txt", "r").read()))
y_test = np.loadtxt(StringIO(open("test/y_test.txt", "r").read()))

rf = RandomForestClassifier(n_estimators=10, random_state=123456)
rf = rf.fit(X_train, y_train)

predicted = rf.predict(X_test)
accuracy = accuracy_score(y_test, predicted)
# accuracy -> 0.9107567017305734
```
## Sources
- A [tutorial](https://www.datacamp.com/community/tutorials/random-forests-classifier-python#building) with scikit-lean.
- Another [tutorial](https://www.blopig.com/blog/2017/07/using-random-forests-in-python-with-scikit-learn/) with scikit-lean.
- [Scikit-Learn installation guide](https://scikit-learn.org/stable/install.html).
- [Scikit-Learn guide](https://scikit-learn.org/stable/tutorial/basic/tutorial.html#machine-learning-the-problem-setting) on supervised learning.
