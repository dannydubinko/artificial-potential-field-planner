# Python Quick Reference & Useful Documentation

This guide provides a reference for core Python syntax, design patterns, and built-in features.

---

## Table of Contents

* [1. Data Structures & Built-in Types](#1-data-structures--built-in-types)
* [2. Comprehensions](#2-comprehensions)
* [3. Functions & Functional Tools](#3-functions--functional-tools)
* [4. Object-Oriented Programming (OOP)](#4-object-oriented-programming-oop)
* [5. Context Managers & Resource Management](#5-context-managers--resource-management)
* [6. Exception Handling](#6-exception-handling)
* [7. Essential Standard Library Modules](#7-essential-standard-library-modules)
* [8. Python Libraries](#8-python-libraries)

---

## 1. Data Structures & Built-in Types

<details>
<summary><b>Click to expand: Lists, Dictionaries, Sets, and Tuples</b></summary>

### Lists (Mutable, Ordered)
```python
fruits = ["apple", "banana"]
fruits.append("cherry")      # Add to end
fruits.insert(1, "orange")   # Insert at index 1
last = fruits.pop()          # Remove and return last element
```

### Dictionaries (Key-Value, Unique Keys)
```python
person = {"name": "Alice", "age": 30}
age = person.get("age", 25)  # Safe retrieval with default fallback
person["email"] = "alice@example.com"

# Iterating
for key, val in person.items():
    print(f"{key}: {val}")
```

### Sets (Mutable, Unique, Unordered)
```python
s = {1, 2, 3, 3}            # s is {1, 2, 3}
s.add(4)
s.discard(2)                # Safe remove (does not raise error if missing)

# Set Operations
a = {1, 2, 3}
b = {3, 4, 5}
union = a | b               # {1, 2, 3, 4, 5}
intersection = a & b        # {3}
difference = a - b          # {1, 2}
```

### Tuples (Immutable, Ordered)
```python
point = (10, 20)
x, y = point                # Unpacking
```
</details>

---

## 2. Comprehensions

<details>
<summary><b>Click to expand: List, Dict, and Set Comprehensions</b></summary>

Syntactic sugar to construct collections concisely.

### List Comprehensions
```python
# Format: [expression for item in iterable if condition]
squares = [x**2 for x in range(10) if x % 2 == 0]
# [0, 4, 16, 36, 64]
```

### Dictionary Comprehensions
```python
# Format: {key_expr: value_expr for item in iterable}
square_dict = {x: x**2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

### Set Comprehensions
```python
unique_lengths = {len(word) for word in ["apple", "banana", "pear", "apple"]}
# {4, 5, 6}
```
</details>

---

## 3. Functions & Functional Tools

<details>
<summary><b>Click to expand: args, kwargs, lambdas, and generators</b></summary>

### Position-independent and Keyword Arguments
```python
def print_data(*args, **kwargs):
    # args is a tuple of positional arguments
    for arg in args:
        print(arg)
    # kwargs is a dictionary of keyword arguments
    for key, value in kwargs.items():
        print(f"{key} = {value}")

print_data(1, 2, 3, user="Alice", active=True)
```

### Lambda Functions (Anonymous functions)
```python
square = lambda x: x**2
print(square(5))  # 25

# Sort list of tuples by second element
pairs = [(1, 'one'), (2, 'two'), (3, 'three')]
pairs.sort(key=lambda pair: pair[1])
```

### Generators (Lazy iteration)
```python
def fibonacci(limit):
    a, b = 0, 1
    while a < limit:
        yield a
        a, b = b, a + b

for num in fibonacci(10):
    print(num)  # Prints 0, 1, 1, 2, 3, 5, 8
```
</details>

---

## 4. Object-Oriented Programming (OOP)

<details>
<summary><b>Click to expand: Classes, Inheritance, and Magic Methods</b></summary>

```python
class Animal:
    def __init__(self, name):
        self.name = name  # Instance variable

    def speak(self):
        raise NotImplementedError("Subclass must implement speak")

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name) # Call parent constructor
        self.breed = breed

    def speak(self):
        return f"{self.name} says Woof!"

    def __str__(self):
        # Representation string when printed
        return f"Dog: {self.name} ({self.breed})"

my_dog = Dog("Rex", "German Shepherd")
print(my_dog)         # Dog: Rex (German Shepherd)
print(my_dog.speak()) # Rex says Woof!
```
</details>

---

## 5. Context Managers & Resource Management

<details>
<summary><b>Click to expand: with statements & Custom Context Managers</b></summary>

Ensures resources are cleaned up promptly.

### Standard usage
```python
with open("test.txt", "w") as f:
    f.write("Hello World")
# File is automatically closed here even if exceptions occurred
```

### Custom Context Manager (using contextlib)
```python
from contextlib import contextmanager

@contextmanager
def simple_timer(label):
    import time
    start = time.time()
    try:
        yield
    finally:
        end = time.time()
        print(f"[{label}] Elapsed: {end - start:.4f}s")

with simple_timer("My Code Block"):
    # Perform operations
    sum(x*x for x in range(1000000))
```
</details>

---

## 6. Exception Handling

<details>
<summary><b>Click to expand: try/except/finally blocks</b></summary>

```python
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"Error: {e}")
except TypeError:
    print("Invalid Types")
else:
    print(f"Success! Result: {result}")
finally:
    print("Execution completed.") # Always runs
```
</details>

---

## 7. Essential Standard Library Modules

<details>
<summary><b>Click to expand: OS, Sys, JSON, Collections, Itertools</b></summary>

### `os` & `sys` (System & Filesystem)
```python
import os
import sys

current_dir = os.getcwd()
files = os.listdir(current_dir)
script_args = sys.argv  # CLI parameters
```

### `json` (Serialization)
```python
import json

data = {"name": "Bob", "verified": True}
json_str = json.dumps(data)         # Dict to string
parsed_dict = json.loads(json_str)  # String to dict
```

### `collections` (Specialized containers)
```python
from collections import defaultdict, Counter

# defaultdict: automatically assigns default value to missing keys
dd = defaultdict(list)
dd["fruits"].append("apple")

# Counter: counts hashable objects
counts = Counter(["apple", "orange", "apple"])
print(counts["apple"]) # 2
```

### `itertools` (Iterator tools)
```python
import itertools

# Cartesion product
pairs = list(itertools.product([1, 2], ['a', 'b']))
# [(1, 'a'), (1, 'b'), (2, 'a'), (2, 'b')]

# Permutations & Combinations
perms = list(itertools.permutations([1, 2, 3], 2))
```
</details>

---

## 8. Python Libraries

<details>
<summary><b>Click to expand: Dataclasses</b></summary>

### Dataclasses
Dataclasses (introduced in Python 3.7) provide a decorator and functions for automatically adding generated special methods (like `__init__()` and `__repr__()`) to user-defined classes.

```python
from dataclasses import dataclass, field

@dataclass
class InventoryItem:
    name: str
    unit_price: float
    quantity_on_hand: int = 0
    # Use field() for mutable default values or complex initialization
    features: list[str] = field(default_factory=list)

    def total_cost(self) -> float:
        return self.unit_price * self.quantity_on_hand

# Automatically generates constructor and representation
item = InventoryItem("Widget", 2.5, 10, ["metal", "heavy"])
print(item)  # InventoryItem(name='Widget', unit_price=2.5, quantity_on_hand=10, features=['metal', 'heavy'])
```

### Frozen Dataclasses (Read-only / Hashable)
Setting `frozen=True` makes the instance immutable, automatically generating `__hash__()`.
```python
@dataclass(frozen=True)
class ImmutablePoint:
    x: float
    y: float

p = ImmutablePoint(1.0, 2.0)
# p.x = 3.0  # Raises dataclasses.FrozenInstanceError
```
</details>
