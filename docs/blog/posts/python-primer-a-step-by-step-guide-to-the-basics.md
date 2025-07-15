---
slug: python-primer-a-step-by-step-guide-to-the-basics
title: Python Primer: A Step-by-Step Guide to the Basics
date: 2023-09-17
tags: ['python']
---

In the previous article, I provided some background knowledge of Python and installation of python on different platforms. In this article, I will be covering the basics of the language and delving deeper into the above concepts, and see how to use them in practice.

<!-- more -->




### Interactive shell (REPL)


An interactive shell, also known as a REPL (Read Eval Print Loop) is a way to interact with a programming language by entering commands and immediately seeing the results. In Python interactive shell can be accessed by running the python command in a terminal or command prompt as shown below. Once in the interactive shell you can enter Python commands and see the results immediately. You can also use an interactive shell to test small snippets of code or to experiment with the language before writing a full program.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674285735785/de9e2da1-0336-44b2-bb62-0d6e04b6aafa.png)


### Comments


In Python, comments are used to provide explanations or notes about the code. Comments are ignored by the interpreter and do not affect the execution of the program. There are two types of comments in Python:


* Single-line comments


* Multi-line comments.




A single-line comment is created by placing the pound symbol (#) at the beginning of a line followed by the comment text. For example:



```
# This is single - line comment

```

A multi-line comment also called a block comment is created by using triple quotation marks (''' or """). Everything between the quotation marks will be ignored by the interpreter. For example:



```
"""
This is a
multi line comment
"""

```

It is a good practice to use comments to document your code, making it easier for others to understand and maintain. Comments are also useful for explaining the purpose of a specific block of code, and how to use a function or class that you have defined.


When writing a blog on python it is also advisable to use comments in the code snippets to explain the functionality of the code, this can help the readers understand the code better.


### Variable And Data Type


In Python, a variable is a name that refers to a value. Variables are used to store data in a program. Here are some examples of different data types and how they are used in python:


* Numbers: integers (whole numbers) and floating-point numbers (numbers with decimal places)



```
age = 25
height = 1.75
salary = 45000.50

```

* Strings: sequences of characters, used for text



```
name = "John Doe"
grade = 'A'

```

* Booleans: true/false values



```
is_student = True

```

* Lists: ordered collections of items, which can be of any data type



```
fruits = ["apple", "banana", "mango"]

```

* Arrays: a data structure that stores a fixed-size sequential collection of elements of the same type.



```
from array import array
numbers = array("i", [1, 2, 3, 4, 5])

```

* Tuples: Collection of objects that are ordered and immutable.



```
personal_info = ("John", 25, True)

```

In python, unlike other languages, we don't have to mention the data type of the variable while declaring it. The data type is determined by the value assigned to the variable.


And also lists and arrays are not the same, list are mutable and array are not. Tuples are immutable, once it is created we can't change the values inside it.


### Introspection ( dir, type, isinstance, help, docstring)


Introspection is the ability of a computer program to examine and modify its own structure and behavior. In Python, there are several built-in functions and methods that allow you to introspect the data types, variables, and functions in your code.


* **dir()** function: This function returns a list of all the attributes and methods of an object. For example, **dir(list)** returns a list of all the attributes and methods of the list class.


* **type()** function: This function returns the type of an object. For example, **type(5)** returns **<class 'int'>** and **type([1, 2, 3])** returns **<class 'list'>**.


* **isinstance()** function: This function checks if an object is an instance of a particular class. For example, **isinstance(5, int)** returns **True** and **isinstance([1, 2, 3], list)** returns **True**.


* **help()** function: This function prints the documentation string of an object. For example, **help(list)** prints the documentation string of the list class.


* docstring: A docstring is a string literal that occurs as the first statement in a module, function, class, or method definition. It is used to provide a brief description of what the function or class does. Docstrings are enclosed in triple quotes, for example """ this is a docstring """, Docstrings can be accessed via the **doc** attribute of an object or by using the help() function.




These are some of the most common introspection techniques in Python, but there are many more you can use depending on your needs.


### I/O Python


In Python, there are several built-in functions and methods for performing input and output (I/O) operations from the console.


* **input()** function: This function is used to read input from the user. It reads input as a string, so if you want to read a number, you'll need to convert the string to a number using **int()** or **float()** functions.



```
name = input("What's your name? ")
age = int(input("How old are you? "))

```

* **print()** function: This function is used to print output to the console. You can pass any number of arguments to the function, and they will be printed to the console separated by spaces.



```
print("Hello, ", name) 
print("You are ", age, " years old.")

```

* **sys.stdout.write()** and **sys.stdout.flush()**: This is another way to print the output in python, it has more control over the output, you can use **sys.stdout.write()** to write the output and **sys.stdout.flush()** to flush the buffer of stdout.



```
import sys
sys.stdout.write("Hello, ")
sys.stdout.flush()
sys.stdout.write(name)

```

* **file I/O:** You can also read and write data to a file on disk using Python's built-in **open()** function and related methods like **read()**, **write()**, **readline()**, **writelines()**, etc.



```
# Open the file in write mode
file = open("data.txt", "w")

# Write data to the file
file.write("Name: " + name + "\n")
file.write("Age: " + str(age))

# Close the file
file.close()

```

In the next article, we will continue to explore the basics of Python by looking at loops, functions, modules, and packages.


