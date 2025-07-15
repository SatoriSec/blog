---
slug: pythonmodules
title: Basic Python Project - Organizing Code with Modules and Imports
date: 2024-03-02
tags: [python]
---

Simple Python project structure to demonstrate how to use modules, classes, and import them across different files and folders.

<!-- more -->

### Project Structure 

```markdown
my_project/
│
├── main.py
├── utils/
│   ├── __init__.py
│   ├── calculator.py
│   └── greeter.py
└── models/
    ├── __init__.py
    └── person.py
```

### Step-by-Step Explanation 
 
1. **`main.py`** : The main entry point of the application.
 
2. **`utils/`** : A folder containing utility modules (`calculator.py` and `greeter.py`).
 
3. **`models/`** : A folder containing data models (`person.py`).

### File Contents 
`main.py`

```python
from utils.calculator import Calculator
from utils.greeter import Greeter
from models.person import Person

def main():
    # Create instances of the classes
    calc = Calculator()
    greeter = Greeter()
    person = Person("Raj", 40)

    # Use methods from the imported classes
    sum_result = calc.add(5, 7)
    greeting = greeter.greet(person.name)

    print(f"Sum: {sum_result}")
    print(greeting)

if __name__ == "__main__":
    main()
```
`utils/calculator.py`

```python
class Calculator:
    def add(self, a, b):
        return a + b

    def subtract(self, a, b):
        return a - b

    def multiply(self, a, b):
        return a * b

    def divide(self, a, b):
        if b != 0:
            return a / b
        else:
            return "Error: Division by zero"
```
`utils/greeter.py`

```python
class Greeter:
    def greet(self, name):
        return f"Hello, {name}! Welcome to the Python project."
```
`models/person.py`

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        return f"Person(name={self.name}, age={self.age})"
```

### How to Run the Program 
 
1. Open a terminal or command prompt.
 
2. Navigate to the `my_project` directory.
 
3. Run the program using Python:


```bash
python main.py
```

### Expected Output 


```vbnet
Sum: 12
Hello, Raj! Welcome to the Python project.
```

### Explanation 
 
- **`main.py`** : Imports classes from different modules and utilizes them to perform various tasks.
 
- **`calculator.py`** : Contains a `Calculator` class with basic arithmetic methods.
 
- **`greeter.py`** : Contains a `Greeter` class to create a greeting message.
 
- **`person.py`** : Defines a `Person` class to represent a person with a name and age.

This structure demonstrates how to organize code into modules and import them across different files and folders in Python.