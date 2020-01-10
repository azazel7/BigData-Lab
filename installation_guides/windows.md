## Set up guide for Windows

This document is intended to provide you with the details for how to install
python, pip, virtualenv, pytest, pyspark and dask.
Note: these instructions were tested on Windows 7 Enterprise with Java 1.8.0_211 
installed. Please submit a PR to update this document for alternate versions of Windows.


### Installing Python version 3.5.4

0. Check if Python is installed on your computer by opening a Command Prompt and typing `python --version`.
   If Python is installed and you have a version of Python3+, you may skip the following steps and move on to the next section.
1. Download the Python 3.5.4 embeddable zip file from [here](https://www.python.org/downloads/release/python-354/)
2. Extract the files to an appropriate directory (e.g. `C:\Users\v_hayots\Documents\BigData\Software\python-3.5.4-embed-amd64`)
3. Add directory to your Windows path:
  1. Start menu
  2. Control Panel
  3. User Accounts
  4. Change my environment variables (last option on the lefthand-side menu)
  5. Click on `New...` under `User variables for <your username>
  6. Set variable name to `PYTHONHOME` and variable value to the path of your newly extracted 
     Python directory (e.g. https://www.python.org/downloads/release/python-354/)
  7. Press `Ok`
  8. Select `PATH` under `User variables for <your username>` and click `Edit...`. If it doesn't exist, create it by clicking `New...`!
  9. Add the previously created PYTHONHOME variable to your PATH along with its Scripts folder
     (e.g. `%PYTHONHOME%;%PYTHONHOME%\Scripts`). Note: Don't erase the other values
     in the PATH variable, just append to the list using a semi-colon before entering the Python executable path.
  10. Press OK on both screens to finalize the changes.
4. You should now be able to open your Command Prompt and enter `python` to access the Python interpreter.
5. Congrats! You have successfully installed Python 3.5.4 on your Windows machine.


### Installing pip
Pip is the Python package installer that will help us install virtualenv, pytest, PySpark and Dask.

0. Check if pip is installed by typing `pip --version` into your Command Prompt. If it is installed,
and the Python version matches your target Python version, you may move to the next section.
1. Download the following file to your computer: [get-pip.py](bootstap.pypa.io/get-pip.py)
2. Open your Command Prompt and execute the following:
```
python <filepath to get-pip.py>
```
3. Read the warning message, it will likely tell you that pip has been installed, but has not been added to your PATH. It will also tell you the location of the pip installation
4. Add pip to your Windows PATH variable following the steps in step 3 of `Installing Python version 3.5.4`.
5. Open the Command Prompt and execute the `pip` command to obtain the pip usageinstructions.
6. Congrats! You have successfully installed pip.


### Installing Virtualenv and creating a virtual environment

Virtualenv provides an isolated environment for all project packages to reside.
Note: This step is not necessary. I normally create virtual environments to help me manage
projects using different versions of Python. If you do not want a virtual environment, you
can skip this part.

0. Check if virtualenv is installed by entering `virtualenv --version` into your Command Prompt. If it is installed,
you may move onto the next section.
1. Download virtualenv via `pip` (e.g. `pip install virtualenv`)
2. Create a virtual environment (be sure to be in the desired directory first):
```
virtualenv <environment name>
```
e.g. `virtualenv bigdataenv`






 



