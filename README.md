# Big Data Introduction

## Cheat Sheets
- [Git sheet](https://rogerdudler.github.io/git-guide)
- [Git sheet](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf)
- [Pyspark sheet](https://s3.amazonaws.com/assets.datacamp.com/blog_assets/PySpark_Cheat_Sheet_Python.pdf)
- [Python sheet](https://perso.limsi.fr/pointal/_media/python:cours:mementopython3-english.pdf)
- [Another Python sheet](https://programmingwithmosh.com/python/python-3-cheat-sheet/)

## Git Introduction
[Git Guide](https://rogerdudler.github.io/git-guide)

### Create GitHub account
You can create your GitHub account [here](https://github.com/join).
### Get the repository
The repository for this lab is located [here](https://github.com/azazel7/BigData-Lab1)
#### Fork the repository
#### Clone the repository
`git clone`
### Workflow
#### In a perfect world
`git add <filename>`
`git commit`
`git commit --amend`
`git push`
#### Bug Hunting
`git checkout`
### Pull Request

## Python Introduction
(Python CheatSheet)[]
### Required
- Python 3.5

### Motivations for Python
- Python is very popular in science and engineering: check [SciPy](https://scipy.org), [scikit-learn](http://scikit-learn.org).
- Python is free software (as in freedom)
- Python is portable, available for all major operating systems
- Python is a versatile language, "the second best language for everything"
- Python has a lively online community, active on [Stackoverflow](https://stackoverflow.com) and many other forums

### Other notes
- Python is an object-oriented language
- Python is an interpreted language
- [Google](google.com) and [Stackoverflow](https://stackoverflow.com) are your best friends!

### Hello World
```
print("Hello world")
```
### Variables
Python is dynamically typed.
In this first example, `otter` is set as an integer and its value equal 3.
```
otter = 3
```
Right after assigning 3 to `otter` you can change your mind and assign a string ...
```
otter = "Otters will rule the world."
otter = 'Otters will rule the world.'
```
... or a list.
```
otter = ["Otters", "will", "rule", "Mars", "in", "2037"]
otter = [67, 51, 17, 101, 48]
```

Complex numbers also work:
```
otter = 3 + 2j
```
### Basic operations
Python is able to do all basic operations
```
a = 10
b = 3
a + b
Output: 13
a - b
Output: 7
a / b
Output: 3.33
a * b
Output: 30
a % b
Output: 1
```
### Containers
