# NumPy Quick Reference & Useful Documentation

NumPy is the fundamental package for scientific computing in Python. It provides a high-performance multidimensional array object, and tools for working with these arrays.

---

## 1. Array Creation

```python
import numpy as np

# Creating arrays from Python lists
a = np.array([1, 2, 3])                 # 1D array
b = np.array([[1.5, 2, 3], [4, 5, 6]])  # 2D array (matrix)

# Creating arrays with placeholders
zeros   = np.zeros((3, 4))             # 3x4 array of zeros
ones    = np.ones((2, 3), dtype=int)   # 2x3 array of ones with integer type
empty   = np.empty((2, 2))             # Uninitialized array (values depend on memory state)
arange  = np.arange(10, 30, 5)         # [10, 15, 20, 25] (start, stop, step)
linspace = np.linspace(0, 2, 9)        # 9 values from 0 to 2 (inclusive)
eye     = np.eye(3)                    # 3x3 identity matrix
```

---

## 2. Array Attributes

```python
a = np.array([[1, 2, 3], [4, 5, 6]])

print(a.ndim)   # 2          (Number of array dimensions)
print(a.shape)  # (2, 3)     (Tuple of array dimensions)
print(a.size)   # 6          (Number of elements in the array)
print(a.dtype)  # dtype('int64') (Data type of elements)
```

---

## 3. Basic Operations & Broadcasting

Operations on arrays are element-wise by default:

```python
a = np.array([20, 30, 40, 50])
b = np.arange(4)  # [0, 1, 2, 3]

c = a - b         # [20, 29, 38, 47]
d = b**2          # [0, 1, 4, 9]
e = 10 * np.sin(a)

# Matrix Multiplication (Dot Product)
A = np.array([[1, 1], [0, 1]])
B = np.array([[2, 0], [3, 4]])

product1 = A @ B        # Modern matrix multiplication operator
product2 = A.dot(B)     # Equivalent dot function
```

### Broadcasting Rules
Broadcasting allows operations on arrays of different shapes under certain rules:
1. If the arrays do not have the same number of dimensions, prepend 1s to the shape of the smaller array.
2. If size in a dimension is 1 in one array and $N > 1$ in the other, the array behaves as if it were copied along that dimension.

---

## 4. Indexing, Slicing, and Iterating

```python
a = np.arange(10)**3
# [0, 1, 8, 27, 64, 125, 216, 343, 512, 729]

# Slicing: [start:stop:step]
print(a[2:5])     # [8, 27, 64]
print(a[:6:2])    # [0, 8, 64]
a[:6:2] = 1000    # Equivalent to a[0]=1000, a[2]=1000, a[4]=1000

# Multidimensional slicing
b = np.array([[ 0,  1,  2,  3],
              [10, 11, 12, 13],
              [20, 21, 22, 23]])

print(b[0:2, 1])  # Rows 0 & 1, Column 1 -> [1, 11]
print(b[:, 1:3])  # All rows, Columns 1 & 2
print(b[-1])      # Last row -> [20, 21, 22, 23]
```

---

## 5. Shape Manipulation

```python
a = np.floor(10 * np.random.random((3, 4)))

print(a.shape)   # (3, 4)
print(a.ravel()) # Flattened 1D representation
print(a.reshape(6, 2))  # Reshapes to 6x2 (does not modify original array)
print(a.T)       # Transpose of array (4x3)
```

---

## 6. Mathematical & Statistical Functions

NumPy offers universal functions (`ufunc`) that run fast compiled C code over arrays.

```python
a = np.array([[1, 2], [3, 4]])

print(np.sum(a))          # 10
print(np.sum(a, axis=0))  # Column sums -> [4, 6]
print(np.sum(a, axis=1))  # Row sums -> [3, 7]
print(np.mean(a))         # 2.5
print(np.std(a))          # 1.118... (Standard deviation)
print(np.min(a))          # 1
print(np.max(a))          # 4
print(np.argmax(a))       # Index of maximum element -> 3
```

---

## 7. Useful Submodules

### Linear Algebra (`np.linalg`)
```python
import numpy as np

a = np.array([[1.0, 2.0], [3.0, 4.0]])

inv_a = np.linalg.inv(a)       # Matrix inverse
solve = np.linalg.solve(a, y)  # Solve linear equations system a * x = y
eigvals, eigvecs = np.linalg.eig(a) # Eigenvalues and eigenvectors
```

### Random Number Generation (`np.random`)
Using the modern `Generator` API (recommended over legacy `np.random.rand`):
```python
rng = np.random.default_rng(seed=42)

rand_floats = rng.random((3, 2))      # Uniform [0.0, 1.0)
rand_normal = rng.normal(0, 1, (3, 3)) # Normal mean=0, std=1
rand_ints   = rng.integers(0, 10, 5)   # 5 random integers from [0, 10)
shuffled    = rng.shuffle(my_array)    # Shuffling array in-place
```
