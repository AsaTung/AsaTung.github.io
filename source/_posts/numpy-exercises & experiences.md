---
title: numpy exercises and experience
data: 2019-8-1
tags: numpy
---

最近在读机器学习深度学习代码时，时常发现自己读不懂，原因除了不熟悉算法原理之外，还有不知道某个函数实现了什么功能，例如numpy有无数种创建数组和矩阵的方式，为什么这个算法的实现要用到这种方式等等。Numpy数组有许多极其优良的性能，在对大量数据进行数学和其他类型的操作是，我们通常会选择numpy数组而不是python原生的list。因此，本文主要记录入门numpy的一些容易混淆的概念和常见的使用技巧，一方面在记录的过程中理清思路，在脑海里留下一些实现方法的印象，一方面便于日后复习。
# Table

+ import
+ The N-dimensional array (class ndarray)
    + Preface
    + Momory layout
        + Item layout
        + Flattened item layout
        + Memory layout
    + Copies & Views
        + No Copy
        + View or Shallow Copy
        + Deep Copy
+ numpy experience
+ Refrence

# import 

		import numpy as np

# The N-dimeensional array (class ndarray)

## Preface

The ndarray class is the core of numpy. The numpy documentation defines the ndarray class very clearly: An instance of class ndarray consists of a contiguous one-dimensional segment of computer memory (owned by the array, or by some other object), combined with an indexing scheme that maps N integers into the location of an item in the block.

Said differently, an array is mostly a contiguous block of memory whose parts can be accessed using an indexing scheme. 

## Memory layout
Such indexing scheme is in turn defined by a **shape** and a **data type** and this is precisely what is needed when you define a new array:

		Z = np.arange(9).reshape(3,3).astype(np.int16)

Here, we know that Z itemsize is 2 bytes (int16), the shape is (3,3) and the number of dimensions is 2 (len(Z.shape)).

		print(Z.itemsize)	#2
		print(Z.shape)		#(3, 3)
		print(Z.ndim)		#2

> The number of dimensions (axes) of the array. In the Python world, the number of dimensions is referred to as **rank**. The rank of array Z is np.ndim(Z). 这里的 rank 不是线性代数中的 rank（秩），它指代的是**维数**（number of dimensions）

### Item layout

		               shape[1]
		                 (=3)
		            ┌───────────┐
		
		         ┌  ┌───┬───┬───┐  ┐
		         │  │ 0 │ 1 │ 2 │  │
		         │  ├───┼───┼───┤  │
		shape[0] │  │ 3 │ 4 │ 5 │  │ len(Z)
		 (=3)    │  ├───┼───┼───┤  │  (=3)
		         │  │ 6 │ 7 │ 8 │  │
		         └  └───┴───┴───┘  ┘
axis的计数方式是**从外到内**计数的，例如矩阵Z在numpy中表示为：

		Z = [[0,1,2],
		     [3,4,5],
		     [6,7,8]]
对应的轴为：

		    axis = 0
			|
		    | axis = 1
		    | |
		    ↓ ↓
		Z = [ [0,1,2],
			  [3,4,5],
			  [6,7,8] ]

在指定axis上求和，即对于np.sum(Z, **axis=0**)和np.sum(Z, **axis=1**)会有不同的结果

### Flattened item layout

		┌───┬───┬───┬───┬───┬───┬───┬───┬───┐
		│ 0 │ 1 │ 2 │ 3 │ 4 │ 5 │ 6 │ 7 │ 8 │
		└───┴───┴───┴───┴───┴───┴───┴───┴───┘
		
		└───────────────────────────────────┘
		               Z.size
		                (=9)

### Memory layout
		
		                         strides[1]
		                           (=2)
		                  ┌─────────────────────┐
		
		          ┌       ┌──────────┬──────────┐ ┐
		          │ p+00: │ 00000000 │ 00000000 │ │
		          │       ├──────────┼──────────┤ │
		          │ p+02: │ 00000000 │ 00000001 │ │ strides[0]
		          │       ├──────────┼──────────┤ │   (=2x3)
		          │ p+04  │ 00000000 │ 00000010 │ │
		          │       ├──────────┼──────────┤ ┘
		          │ p+06  │ 00000000 │ 00000011 │
		          │       ├──────────┼──────────┤
		Z.nbytes  │ p+08: │ 00000000 │ 00000100 │
		(=3x3x2)  │       ├──────────┼──────────┤
		          │ p+10: │ 00000000 │ 00000101 │
		          │       ├──────────┼──────────┤
		          │ p+12: │ 00000000 │ 00000110 │
		          │       ├──────────┼──────────┤
		          │ p+14: │ 00000000 │ 00000111 │
		          │       ├──────────┼──────────┤
		          │ p+16: │ 00000000 │ 00001000 │
		          └       └──────────┴──────────┘
		
		                  └─────────────────────┘
		                        Z.itemsize
		                     Z.dtype.itemsize
		                           (=2)

## Copies & Views

### No Copy (b=a)

Simple assignments do not make the copy of array object. Instead, it uses **the same id()** of the original array to access it. The id() returns a universal identifier of Python object, similar to the pointer in C.

Furthermore, any changes in either gets reflected in the other. For example, the changing shape of one will change the shape of the other too.

#### Example

	```
		a = np.arange(12)
		b = a
		
		print(b is a)  # 返回 True
		b.shape = (3,4)
		print ("a.shape=",a.shape)  # a.shape= (3, 4)
		print (id(a))   # 2493855732640
		print (id(b))   # 2493855732640
	```

### View or Shallow Copy (c = a.view())

NumPy has ndarray.view() method which is a new array object that looks at the **same data** of the original array. Unlike the earlier case, change in dimensions of the new array doesn’t change dimensions of the original.

Slice of an array creates a view.

#### Example

	```
		c = a.view()            # 将a复制给c
		print(c is a)           # False
		print("c=",c)
		        
		"""
		c=array([[0, 1, 2, 3],
		         [4, 5, 6, 7],
		         [8, 9, 10, 11]])
		"""
		        
		c.shape = (2,6)             # 改变c的结构为2行6列，看a是否会变化
		print("c.shape=",c.shape)   # c.shape=(2, 6)
		print ("a.shape=",a.shape)  # a.shape=(3, 4)
		
		c[0,4] = 1234             # 改变矩阵c第0行4列的值，看a的值是否会变化
		print("c=",c)
		print("a=",a)             #结果显示a中相应的值也发生了变化
		
		print(id(a))    # 2493855144416
		print(id(b))    # 2493855144416
	```

### Deep Copy (d=a.copy())

The ndarray.copy() function creates a deep copy. It is a **complete copy** of the array and its data, and doesn’t share with the original array.

#### Example
	
	```
		d = a.copy() 
		print(d is a)  # False
		
		d[0,0] = 9999
		print (d) 
		print (a)     # a中相应的值不会变化
		
		print(id(d))  # 2493855735440
		print(id(a))  # 2493855732640
	```
# numpy experience


内容主要来自：[numpy-100](https://github.com/rougier/numpy-100)

1. Import the numpy package under the name np (★☆☆)

    	import numpy as np

2. Print the numpy version and the configuration (★☆☆)

    	print(np.__veision__)
    	np.show_config()
3. Create a null vector of size 10 but the fifth value which is 1 (★☆☆)

    	Z = np.zeros(10)
    	Z[4] = 1
    	print(Z)
4. Create a vector with values ranging from 10 to 49 (★☆☆)

    	Z = np.arange(10, 50)
    	print(Z)
5. How to find the memory size of any array (★☆☆)

    	Z = np.zeros((10,10))
    	print("%d bytes" % (Z.size * Z.itemsize))
    	#Z.size=10*10=100, Z.itemsize=8
6. Reverse a vector (first element becomes last) (★☆☆)

    	Z = np.arange(50)
    	Z = Z[::-1]
    	print(Z)

7. Create a 3x3 matrix with values ranging from 0 to 8 (★☆☆)

		Z = np.arange(9).reshape(3, 3)
        print(Z)
8. Find indices of non-zero elements from [1,2,0,0,4,0] (★☆☆)

		nz = np.nonzero([1,2,0,0,4,0])
        print(nz)
		#np.nonzero会返回非0值的下标
9. Create a 3x3 identity matrix(单位矩阵) (★☆☆)

        Z = np.eye(3)
		print(Z)
		
		"""
		array([[1., 0., 0.],
		       [0., 1., 0.],
		       [0., 0., 1.]])
		"""
10. Create a 3x3x3 array with random values (★☆☆)

		Z = np.random.random((3,3,3))
		print(Z)
11. Create a 10x10 array with random values and find the minimum and maximum values (★☆☆)

		Z = np.random.random((10,10))
		Zmin, Zmax = Z.min(), Z.max()
		print(Zmin, Zmax)
12. Create a random vector of size 30 and find the mean value (★☆☆)

		Z = np.random.random(30)
		m = Z.mean()		#无axis参数，求所有element的均值. axis=0表示列，axis=1表示行.
		print(m)
13. Create a 2d array with 1 on the border and 0 inside (★☆☆)

		Z = np.ones((10,10))
		Z[1:-1,1:-1] = 0
		print(Z)
14. How to add a border (filled with 0's) around an existing array? (★☆☆)

		Z = np.ones((5,5))
		Z = np.pad(Z, pad_width=1, mode='constant', constant_values=0)
		print(Z)

15. What is the result of the following expression? (★☆☆)

		print(0 * np.nan)                  #nan
		print(np.nan == np.nan)            #False
		print(np.inf > np.nan)             #False
		print(np.nan - np.nan)             #nan
		print(np.nan in set([np.nan]))     #True
		print(0.3 == 3 * 0.1)              #False
16. Create a 5x5 matrix with values 1,2,3,4 just below the diagonal (★☆☆)

		Z = np.diag(1+np.arange(4),k=-1)
		print(Z)
			
		"""
		[[0 0 0 0 0]
		[1 0 0 0 0]
		[0 2 0 0 0]
		[0 0 3 0 0]
		[0 0 0 4 0]]
		"""
17. Consider a (6,7,8) shape array, what is the index (x,y,z) of the 100th element? (★☆☆)

		print(np.unravel_index(99,(6,7,8)))
		#(1, 5, 3)
18. Normalize a 5x5 random matrix (★☆☆)

		Z = np.random.random((5,5))
		Z = (Z - np.mean (Z)) / (np.std (Z))	#np.std()计算标准差
		print(Z)
19. Multiply a 5x3 matrix by a 3x2 matrix (real matrix product) (★☆☆)

		Z = np.dot(np.ones((5,3)), np.ones((3,2)))
		print(Z)
		
		# Alternative solution, in Python 3.5 and above
		Z = np.ones((5,3)) @ np.ones((3,2))
		print(Z)

20. Given a 1D array, negate all elements which are between 3 and 8, in place. (★☆☆)

		Z = np.arange(11)
		Z[(3 < Z) & (Z <= 8)] *= -1
		print(Z)
21. What is the output of the following script? (★☆☆)

		print(sum(range(5),-1))    #9
		from numpy import *
		print(sum(range(5),-1))    #10

22. How to find common values between two arrays? (★☆☆)

		Z1 = np.random.randint(0,10,10)
		Z2 = np.random.randint(0,10,10)
		print(np.intersect1d(Z1,Z2))
23. How to ignore all numpy warnings (not recommended)? (★☆☆)

		# Suicide mode on
		defaults = np.seterr(all="ignore")
		Z = np.ones(1) / 0
		
		# Back to sanity
		_ = np.seterr(**defaults)
24. Is the following expressions true? (★☆☆)

		np.sqrt(-1) == np.emath.sqrt(-1)	#False
25. How to get the dates of yesterday, today and tomorrow? (★☆☆)

		yesterday = np.datetime64('today', 'D') - np.timedelta64(1, 'D')
		today     = np.datetime64('today', 'D')
		tomorrow  = np.datetime64('today', 'D') + np.timedelta64(1, 'D')
26. How to get all the dates corresponding to the month of July 2016? (★★☆)

		Z = np.arange('2016-07', '2016-08', dtype='datetime64[D]')
		print(Z)
27. How to compute ((A+B)*(-A/2)) in place (without copy)? (★★☆)

		A = np.ones(3)*1
		B = np.ones(3)*2
		C = np.ones(3)*3
		np.add(A,B,out=B)
		np.divide(A,2,out=A)
		np.negative(A,out=A)
		np.multiply(A,B,out=A)
28. Create a 5x5 matrix with row values ranging from 0 to 4 (★★☆)

		Z = np.zeros((5,5))
		Z += np.arange(5)
		print(Z)
29. Extract the integer part of a random array using 5 different methods (★★☆)

		print (Z - Z%1)
		print (np.floor(Z))
		print (np.ceil(Z)-1)
		print (Z.astype(int))
		print (np.trunc(Z))
30. Create a random vector of size 10 and sort it (★★☆)

		Z = np.random.random(10)
		Z.sort()
		print(Z)
31. Consider two random array A and B, check if they are equal (★★☆)

		A = np.random.randint(0,2,5)
		B = np.random.randint(0,2,5)
		
		# Assuming identical shape of the arrays and a tolerance for the comparison of values
		equal = np.allclose(A,B)
		print(equal)
		
		# Checking both the shape and the element values, no tolerance (values have to be exactly equal)
		equal = np.array_equal(A,B)
		print(equal)
32. Create random vector of size 10 and replace the maximum value by 0 (★★☆)

		Z = np.random.random(10)
		Z[Z.argmax()] = 0
		print(Z)
33. Print the minimum and maximum representable value for each numpy scalar type (★★☆)

		for dtype in [np.int8, np.int32, np.int64]:
		   print(np.iinfo(dtype).min)
		   print(np.iinfo(dtype).max)
		for dtype in [np.float32, np.float64]:
		   print(np.finfo(dtype).min)
		   print(np.finfo(dtype).max)
		   print(np.finfo(dtype).eps)

		"""
		-128
		127
		-2147483648
		2147483647
		-9223372036854775808
		9223372036854775807
		-3.4028235e+38
		3.4028235e+38
		1.1920929e-07
		-1.7976931348623157e+308
		1.7976931348623157e+308
		2.220446049250313e-16
		"""
34. How to find the closest value (to a given scalar) in a vector? (★★☆)

		Z = np.arange(100)
		v = np.random.uniform(0,100)
		index = (np.abs(Z-v)).argmin()
		print(Z[index])
35. How to convert a float (32 bits) array into an integer (32 bits) in place? (★★☆)

		Z = np.arange(10, dtype=np.float32)
		Z = Z.astype(np.int32, copy=False)
		print(Z)
36. What is the equivalent of enumerate for numpy arrays? (★★☆)

		Z = np.arange(9).reshape(3,3)
		for index, value in np.ndenumerate(Z):
		    print(index, value)
		for index in np.ndindex(Z.shape):
		    print(index, Z[index])
37. How to I sort an array by the nth column? (★★☆)

		Z = np.random.randint(0,10,(3,3))
		print(Z)
		print(Z[Z[:,1].argsort()])
38. How to tell if a given 2D array has null columns? (★★☆)

		Z = np.random.randint(0,3,(3,10))
		print((~Z.any(axis=0)).any())
39. Consider an array of dimension (5,5,3), how to mulitply it by an array with dimensions (5,5)? (★★★)

		A = np.ones((5,5,3))
		B = 2*np.ones((5,5))
		print(A * B[:,:,None])
40. How to swap two rows of an array? (★★★)

		A = np.arange(25).reshape(5,5)
		A[[0,1]] = A[[1,0]]
		print(A)
		#swap 0 row and 1 row

# Refrence

+ [numpy-100](https://github.com/rougier/numpy-100)
+ [http://www.labri.fr/perso/nrougier/from-python-to-numpy/](http://www.labri.fr/perso/nrougier/from-python-to-numpy/)
+ [NumPy - Copies & Views](https://www.tutorialspoint.com/numpy/numpy_copies_and_views.htm)
+ [复制理解](https://www.cnblogs.com/wodexk/p/10311293.html)




