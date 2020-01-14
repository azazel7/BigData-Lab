## Set up guide for Ubuntu

This document is intended to provide you with the details for how to install and
configure conda, python, pip, pytest, pyspark and dask.
Note: these instructions were tested on Ubuntu 18.04.3.


### Installing Conda

Conda is an open-source package management system and environment management system that runs on Windows, macOS, and Linux. We will use Conda to create a virtual environment with python3.5, PySpark, Dask and PyTest.

1. Download Miniconda for Linux by executing the following command:

```
    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-$(uname -i).sh
```
2. Grant execution rights to the installer with the command `chmod +x Miniconda3-latest-Linux-$(uname -i).sh`.
3. Execute the installer by executing the command `./Miniconda3-latest-Linux-$(uname -i).sh`. Press ENTER, then if you accept the license agreement type `yes` and press ENTER. Enter the path where you want to install anaconda on your computer (for example `~/.condainstallation`). Finally, type `yes` to let the installer initialize conda.
4. Reinitialize your shell by executing the command `source ~/.bashrc`.
5. To prevent conda from activating the default environment whenever you open a shell, use the following command: `conda config --set auto_activate_base false`.
6. Execute the command `conda update -y -n base -c defaults conda` to update conda to its latest version.
7. Congrats! You have successfully installed conda.

### Creating a virtual environment

Conda can be used to create environments. A conda environment is a directory that contains a specific collection of conda packages. They make it possible to have different versions of python and python packages installed on the same system in different environments.

1. Create a virtual environment with python 3.5 by executing the command: `conda create -n bigdata-lab python=3.5 -y`
2. Activate your virtualenv by executing the command: `conda activate bigdata-lab`


### Installing PySpark inside your virtual environment

PySpark is the Python API in Apache Spark. It is required to complete the course assignments.
In this section, we will install Apache Spark into the virtual environment that we
have just created.

1. With your venv activated, we will execute the command `conda install -y pyspark`.
   You have just installed PySpark, however, Apache Spark needs the Java Platform to run.
2. You can check if java is installed on your system with the command `java -version`.
   If it is not installed, you can install it by executing the command `sudo apt install -y openjdk-8-jre`.
3. Execute the command `pyspark` to start the PySpark interpreter.
4. Run the following commands:

```
data = [i for i in range(10)]
rdd = spark.sparkContext.parallelize(data)
rdd.filter(lambda x: x%2 == 0).collect()
```
5. The output of the last command should be *[0, 2, 4, 6, 8]*. Use exit() or Ctrl-D to exit the interpreter.
6. Congrats! You have successfully installed PySpark.


### Installing Dask inside your virtual environment

Dask is a Big Data framework just like Apache Spark. However, unlike Spark, it is
entirely Python-based and does not require a JVM to execute its Python pipelines.

1. With your venv activated, we will execute the command `conda install -y dask` in the Command Prompt.
2. If no errors have occurred during the process, Dask has been successfully installed.


### Installing Pytest inside your virtual environment

Pytest is a Python framework that allows you to write test cases for Python applications.
We will be using Pytest to evaluate your assignment solutions.

1. With your venv activated, well will execute the command `conda install -y pytest` in the Command Prompt.
2. Execute the command `pytest --version` to ensure pytest has successfully been installed.


Congrats! Your system is now configured for the course. Make sure that your virtual environment is always activated to
access the installed resources, otherwise, you will find that they are missing.

If you would like to exit your virtual environment, entering the command `conda deactivate` into your Command Prompt will do it.
