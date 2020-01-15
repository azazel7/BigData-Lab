## Set up guide for Archlinux

This document is intended to provide you with the details for how to install and
configure
python, pip, venv, pytest, pyspark and dask.
Note: these instructions may vary depending on your configuration.

### Installing Python version 3.5.7

To install Python 3.5.7, there are two possibilities:

0. Install  from AUR repository ([here][https://aur.archlinux.org/packages/python35/]).

### Creating a virtual environment

Venv provides an isolated environment for all Python project packages to reside.
It is a preinstalled module in Python versions 3.5+, and therefore, does not need to be installed.

1. Create a virtual environment by executing the command: `python3.5 -m venv <your venv directory path/name>.
(e.g. `python3.5 -m venv soen499venv`)
2. Activate your virtualenv by executing the command: `source <venv directory path>/bin/activate`
(e.g. `source soen499venv/bin/activate`)


### Installing PySpark inside your virtual environment

PySpark is the Python API in Apache Spark. It is required to complete the course assignments.
In this section, we will install Apache Spark into the virtual environment that we
have just created.

1. With your venv activated, we will execute the command `pip install pyspark`.
2. Restart your Command Prompt, activate your virtual environment and execute the command `pyspark` to start the pyspark interpreter.
3. Run the following commands:

```
data = [i for i in range(10)]
rdd = spark.sparkContext.parallelize(data)
rdd.filter(lambda x: x%2 == 0).collect()
```
4. The output of the last command should be *[0, 2, 4, 6, 8]*
5. Congrats! You have successfully installed PySpark.


### Installing Dask inside your virtual environment.

Dask is a Big Data framework just like Apache Spark. However, unlike Spark, it is
entirely Python-based and does not require a JVM to execute its Python pipelines.

1. With your venv activated, we will execute the command `pip install "dask[complete]"` in the Command Prompt.
2. If no errors have occurred, during the process, Dask has been successfully installed.


### Installing Pytest

Pytest is a Python framework that allows you to write test cases for Python applications.
We will be using Pytest to evaluate your assignment solutions.

1. With your venv activated, well will execute the command `pip install pytest` in the Command Prompt.
2. Execute the command `pytest --version` in your Command Prompt` to ensure pytest has successfully been installed.

Congrats! Your system is now configured for the course. Make sure that your virtual environment is always activated to
access the installed resources, otherwise you will find that they are missing.

If you would like to exit your virtual environment, entering the command `deactivate` into your Command Prompt will do it.

