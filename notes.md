## data types

### bytes

Any weird encoding of characters can be treated as bytes, or a 'byte array': since Python2 used ASCII internally, characters like asian ideograms where not considered a string data type but, more generically, bytes. Python3's main update was the introduction of Unicode as the set of characters used internally, so now special characters (from non-latin alphabets) are also strings.

Bytes data type is important for data transfer in and out of Python or the local environment: data sent to a web server (like a request) must be encoded (using the `.encode()` method) to UTF-8 (no arguments required for `.encode()`, since UTF-8 is default) and data coming from the Internet must be decoded (`.decode()` method).

## modules or libraries

Like other programming languages, like Perl and R, Python can be extended by importing libraries: packages containing more functions.

Some bigger modules may be subdivided into subsets, which can be imported separately to avoid import of the full library if its other functions are not needed.

Examples:

    matplotlib
        matplotlib.pyplot
        matplotlib.colors
        matplotlib.legend
        ...

When importing a library, it can be "aliased", *i.e.* an **alias** can be created for it, so that the module can be called upon using its alias in place of the full name, in the rest of the script. 

This is especially useful with libraries with long names, or when there is the need to call functions from a library subset.



Examples:

```py
import pandas as pd
import matplotlib
import matplotlib.pylot as plt

... # object creation (omissis)
plt.savefig('dataset_barplot.png')  # equivalent to matplotlib.pylot.savefig()
```

> It is not necessary to call the full library before a subset, if functions from its other subets are not needed. If they are needed though, it still is convenient (and good practice) to create an alias for functions with a long name we know we'll be using, as for `matplotlib.pylot` subset, which contains the `matplotlib.pylot.savefig()` function.

### `socket`

The `socket` module provides socket operations to allow Python scripts to communicate with another machine/server through the internet. It supports the portocols IP and Unix domain sockets.

To prepare for a connection from client, it's necessary to create a socket object first, which is done by assigning to a variable the output of the `socket` function from the `socket` module:

```py  
mysock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```

> In the example above, the 2 arguments represent the standard which usually doesn't need to be changed. It basically instructs to create a INET (*i.e.* IPv4) STREAM socket. 

After the client socket has been created, it can be used to connect to a web server, which requires the usage of the `.connect()` method (from the `socket` module) on the socket object.

```py
mysock.connect("www.python.org", 80)
```

> The example above connects to "www.python.org" using the client socket "mysock", on port 80 (*i.e.* the normal http port).
> When the `connect` command completes, the socket "mysock" can be used to send in a request for the text of the page. The same socket will read the reply, and then be destroyed: client sockets are normally only used for one exchange (or a small set of sequential exchanges).

The example below shows the commands to send a request to a web server and to receive data back, effectively creating a little web browser:

```py
mysock.connect(('www.python.org', 80))
cmd = 'GET http://www.python.org/something.txt HTTP/1.0\n\n'.encode()
mysock.send(cmd)

while True:
    data = mysock.recv(512)
    if len(data) < 1:
        break
    print(data.decode())
mysock.close()
```

> The command uses the `GET` keyword for the http request of the test file at the specified url, followed by 2 hard returns. The command has to force the encoding of the source file or page in UTF-8 through the `.encode()` method. The string containing the command is sent to the web server, to the piece of software that makes a listening server socket, this software sends back the requested data, which we can choose to receive.
> A loop it's used to receive data, 512 characters each time. If the data is of 0 characters, the stream is closed (end of the stream). Data will have to be decoded through the `.decode()` method, then the socket is closed.

From the web server side, the Python code creates a “server socket”:

```py
# create an INET, STREAMing socket
serversocket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# bind the socket to a public host, and a well-known port
serversocket.bind((socket.gethostname(), 80))

# become a server socket
serversocket.listen(5)
```

> NOTE 1: low number ports are usually reserved for "well known" services (HTTP).

> NOTE 2: the argument for `listen` tells the socket library to queue up as many as 5 connect requests (the normal max) before refusing outside connections.

Now that a “server” socket has been created, listening on port 80, the main loop of the web server can be entered:

```py
while True:
    # accept connections from outside
    (clientsocket, address) = serversocket.accept()
```

> Use `help()` and then state `socket` to obtain the list of functions in the module.

### `urllib`

The `urllib` library allows connection to Internet resources without dealing with sockets. It is basically a library that takes care of encoding, sending and receiving data to and from a web server or Internet resource. Received data is stored in a variable that works just like a filehandle, so it can be looped through. It still is necessary to decode received data. The example code below gives a demonstration of the necessary steps to build a little web browser with Python's `urllib` module. 

```py
import urllib

fhand = urllib.request.urlopen('http://data.pr4e.org/romeo.txt')

for line in fhand:
    print(line.decode().strip())
```


### `BeautifulSoup`, *i.e.* web scraping/spidering with Python

Python can be extended with the `BeautifulSoup` library, which is used to parse and HTML and XML documents.

All the important abilities and functions of BeautifulSoup are well described in its documentation page: <https://www.crummy.com/software/BeautifulSoup/bs4/doc/>.

Below is a small code example on how to use BeautifulSoup in combination with urllib to parse a web page (HTML document) and extract all hyperlinks (href).

```py
import urllib
import BeautifulSoup

url = 'https://www.crummy.com/software/BeautifulSoup/bs4/doc/'
html = urllib.request.urlopen(url).read()   # for the sake of this example we'll pretend this is a very small page and that there's no harm in loading it all at once in memory

soup = BeautifulSoup(html, 'html.parser')   # BeautifulSoup takes the html document apart and returns a "soup object"

tags = soup('a')

for tag in tags:
    print(tag.get('href', None))
```

### `sqlite3`

The `sqlite3` package allows Python to connect to a relational database and run SQL queries through a Cursor.

> The [`pandas` module](#pandas) can import data from SQL databases into dataframes, which can then be organised and cleaned ("[Import data from relational databases](#data-from-relational-databases)").

Below is noted down how to go through a basic workflow using Python to connect to databases. 

- In order to work with a SQLite database, the Python script first has to connect to it using the `connect()` function, which returns a Connection object.

```py
import sqlite3

conn = sqlite3.connect('example.db')
```

- Another object from this library, the Cursor, allows to execute SQL queries against the database (the Connection object). The Cursor object possesses the `.execute()` method, which accepts as argument a string, containing the SQL query and parameters. To fetch the results of the query, a second method of the Cursor object, the `.fetchall()` method, shall be used:

```py
cur = conn.cursor()

# the following SQL code extracts the first 5 rows from a table called 'simpson_characters'
cur.execute('SELECT * FROM simpson_characters LIMIT 5;')

# the .fetchall() method is used to fetch the query's results and assign them to a variable (a list of tuples)
results = cur.fetchall()
```
 
- After finishing work with a relational database, it's good practice to close Connection and Cursor objects, just like when opening files for reading or writing:

```py
cur.close()
conn.close()
```

### `NumPy`

NumPy is a number management and math optimisation library: it focuses on optimising how numbers and Python objects are stored into memory to allow quicker access, shorter calculation times and lower impact on memory.

Usually storing a single digit into a variable with Python does not require 8 bits (*i.e.* 1 byte), but 20 bytes, because of all the associated information, data type, Python methods and features related to the integer, float or list classes.

In addition, elements in lists and dictionaries are not stored in memory cells adjacent to one another, so access to such variables is slower than it could be. This is because Python is a high level programming language, written to be easy to understand, human-readable and object-oriented. To take care of performance, Python can use the `NumPy` library, which allows control on how memory is allocated.

Usually NumPy is not used directly, since [`pandas`](#pandas) and [`matplotlib`](#matplotlib) libraries both use the `numpy` library to manage numbers and calculations. However it can come in handy to use it in cases which overlap (and replace) matlab's use cases.

> Use `help()` and then state `numpy` to obtain the list of functions in the module.

> Note that, due to `pandas` being built on top of `numpy`, `pandas` inherit many functions and behaviors, but [some (like the `.inan()` `numpy` method), can have a different corresponding function(s) in `pandas`](#data-cleaning), since the latter's functions and objects mimic those of R.

#### Main properties of an array

**Mathematical operations** on arrays performed with standard Python syntax or with `NumPy`'s own math functions **are vectorised (or element-wise)**, meaning that `arr + 10` will per perform the same addition on all elements of the array.

Specific functions of the `numpy` module allow to perform math on whole arrays or between arrays (as long as they have same dimensions, at least along the considered axis).

#### Creation of arrays, dimensions of an array

`NumPy`'s arrays can have multiple dimensions, which are passed as lists inside a list.

```py
import numpy as np

a = np.array([1,2,3]) # 1D numpy array
b = np.array([[1.0,2.0,3.0],[3.0,4.0,5.0]]) # 2D numpy array
c = np.array([[[1,2],[1.0,2.0]], [[3,4],[3.0,4.0]], [[5,6],[5.0,6.0]]]) # 3D numpy array
```

The sample code above shows how `numpy` arrays are created and how dimensions are managed (lists within lists). To create a `numpy` array, simply assign to a variable the ouput of the `numpy.array()` function, passing a list as argument.

#### Control over array memory usage (`'dtype'`)

To control the amount of memory allocated to the numbers' storage, `numpy.array()`'s `dtype=` argument can be passed a string in the format `'int8'`, `'int16'`, `'int32'`, *etc.* (multiples of 8). `'int32'` is default, but it can be changed to a lower value if working with small numbers, like integers <10:

```py
arr = np.array([1,2,3], dtype='int16')
```

> In case of floats, the `dtype` argument will be passed the format like `dtype='float32'`.

#### Array attributes

A `NumPy` array's attributes can be returned by consulting the appropriate property.

```py
arr.ndim     # numpy array dimensions
arr.shape    # like pandas' shape, returns sizes of the dimensions
arr.dtype    # returns the array's dtype
arr.size     # total number of elements
arr.itemsize # number of bytes of single elements in the array
arr.nbytes   # total number of bytes of the whole array (arr.itemsize * arr.size)
```

#### Get elements from an array

**Array dimensions are listed and accessed in the order 'row, column'**, so `.shape` returns dimensions as `[r, c]` and accessing specific elements (or slices) requires to use dimensions `[r, c]`.

Furthermore, `NumPy`'s arrays follow Python's index rules: they start from `0`, for all of their dimensions.

Examples:

```py
>>> arr = np.array([[1,2,3,4,5],[6,7,8,9,10]]) 
>>> print(arr[1, 3]) # extract a single element
9
>>> print(arr[:, 3]) # extract elements from a specific columns for all rows
[4 9]
>>> print(arr[0, :]) # get a whole row (all colums)
[1 2 3 4 5]
# Using the syntax [start:end:step] allows to add a step to a slice:
>>> print(arr[1, 1:-1:2])
[7 9]
```

> Extraction from arrays is performed with slices and indices like for nested lists. Just as for normal Python lists, slices on `NumPy` arrays do not include the element at the end of the slice.

Replacement of elements in an array is done with an assignment to a specific index, like for normal lists. However, a value can also be used to replace all elements of a column, for example; furthermore a number of values equal to the number of columns/rows extracted can be used to replace multiple elements with the values in the order passed: 

```py
>>> arr[:, 3] = 5   # replace whole column
>>> print(arr)
[[ 1  2  3  5  5]
 [ 6  7  8  5 10]]
>>> arr[0, 0] = 20  # replace single element
>>> print(arr)
[[20  2  3  5  5]
 [ 6  7  8  5 10]]
>>> arr[:, 1] = [50, 42]    # replace elements with different values
>>> print(arr)
[[20 50  3  5  5]
 [ 6 42  8  5 10]]

# examples with 3D arrays:
>>> arr2 = np.array([[[0,1],[2,3]],[[4,5],[6,7]]])
>>> print(arr2)
[[[0 1]
  [2 3]]

 [[4 5]
  [6 7]]]
>>> print(arr2[0,1,:])
[2 3]
>>> arr2[:,:,1] = 9
>>> print(arr2)
[[[0 9]
  [2 9]]

 [[4 9]
  [6 9]]]
```

#### Functions to initialise arrays

```py
# to create a matrix of 0s (takes as argument the dimensions of the array, plus optional dtype)
arr1 = np.zeros((2,2))
# to create a matrix of 1s (takes as argument the dimensions of the array, plus optional dtype)
arr2 = np.ones((4,3))
# for any value: (takes as arguments the dimensions, the value to fill the matrix with, plus the optional dtype)
arr3 = np.full((4,3,2), 42)
# an array of specified dimensions filled with random
arr4 = np.random.rand(4,2)  # floats
arr4 = np.random.randint(4,9, size=(2,2))   # integers. Takes range values plus array size.

## dimensions can be passed also as:
arr3 = np.full(arr1.shape, 42) # those returned from another array's .shape
arr3 = np.full_like(arr1, 42) # just the array with the function full_like() (same as above)
```

#### Copying arrays (`.copy()` method)

Differently from how it usually happens in Python and many other languages, copies of an array inherit changes:

```py
>>> arr = np.array([1,2,3])
>>> print(arr)
[1 2 3]
# issue with copying arrays:
>>> copy = arr
>>> print(copy)
[1 2 3]
>>> copy[0] = 20
>>> print(copy)
[20  2  3]
>>> print(arr)
[20  2  3]
# solution:
>>> copy = arr.copy()
>>> copy[0] = 100
>>> print(copy)
[100   2   3]
>>> print(arr)
[20  2  3]
```

When assigning `copy = arr`, `numpy` is not actually creating a copy: it's creating a variable that points to the same memory blocks. For this reason, changes to one array will affect the "copied one" too. To circumvent this issue and perform an actual copy of the array, the `.copy()` method must be used, as shown in the example above.

#### Reshaping arrays

The shape of an array can be changed using the `.reshape()` method. `.reshape()` accepts a tuple as argument (in the format `(r, c)`) and it only works if the total number of elements of the output array will match with that of the original one.

```py
arr1 = np.ones((2, 4))
arr2 = arr1.reshape((4, 2))
arr3 = arr1.reshape((8, 1))
arr4 = arr1.reshape((2, 2, 2))
```

Arrays can also be stacked:

- vertically (becoming 2 subsequent columns of a 2D array, for example): **`vstack()`**
- horizontally (concatenating arrays into one): **`hstack()`**

Arrays can be vertically stacked only if they have the same number of values.

```py
>>> arrA = np.array([0,1,2,3,4])
>>> arrB = np.array([5,6,7,8,9])
>>> arrV = np.vstack([arrA, arrB])
>>> print(arrV)
[[0 1 2 3 4]
 [5 6 7 8 9]]
>>> arrH = np.hstack([arrA, arrB])
>>> print(arrH)
[0 1 2 3 4 5 6 7 8 9]
```

#### Loading data

The `NumPy` module can load data from a text file with the `genfromtext()` function:

```py
np.genfromtext('data.csv', delimiter=',')
```

The method `.astype()` accepts a dtype and converts data of an array to that format (*e.g.*: an array that's `'float32'` can be converted to `'int32'`).

#### Boolean masking and advanced indexing

- `NumPy` arrays support boolean operations: any comparison done with the array will return an array of `True` and `False` values.

```py
arr > 50
((arr > 50) & (arr < 100))
```

- Boolean operations can also be used to index the array, returning only the values for which the result of the comparison is `True`.

```py
arr[arr > 50]
```

- `NumPy` arrays support indexing with lists:

```py
arr[[0, 3, 7]]
arr[[0,1,2,3],[1,2,3,4]]
```

- The `any()` function returns an array of `True` and `False` values, where the condition is evaluated to `True` if any value in the specified axis meets the requirement. The `all()` function returns `True` if all values in the specified axis meet the condition.

```py
>>> arr = np.array([[23,654,46,765,243,4,526],[4,754,43,76,24,6532,3]])

>>> np.any(arr > 50, axis=0)
array([False,  True, False,  True,  True,  True,  True])
>>> np.any(arr > 50, axis=1)
array([ True,  True])

>>> np.all(arr > 50, axis=1)
array([False, False])
>>> np.all(arr > 50, axis=0)
array([False,  True, False,  True, False, False, False])

```

### `matplotlib`

Includes functions and methods to create and output graphs and plots. `pandas` rely on it to produce graphs. More information and examples are listed in the [`pandas` Plotting section](#plotting).

### `pandas`

The `pandas` library is used to read and build table-like files, organise and extract data with functions and syntax that are inherited from R.

> This library can be used in place of R, since it's basically a "pythonisation" of R functions and data types, or it's possible to simply switch to R, which also has RStudio to visualise the produced graphs. In addition, if R features are not needed (and no graph is to be produced), it's not necessary to use Pandas at all in Python (all managed through plain Python).

Like R, the main data structures used by Pandas are the the [**DataFrame**](#dataframes) and the [**Series**](#series).

> **Note:** taking from R, while in the Python interpreter, `pandas` allows to print function manuals by appendig a `?` to the function name. Example: `pandas.read_csv?`. 

#### Series

A Pandas' series looks just like R's vectors: it's a sequence of elements, all indexed (by line number, when thinking it as R object representations, or by position, when thinking of Python list indices). This means that a series corresponds to a standard Python list, except it has an associated data type, as managed by NumPy for memory optimisation (**note:** NumPy arrays and Pandas series cannot store variables of different types).

```py
>>> pandas.Series([18.25, 486.21, 7896.20, 2.0])
0      18.25
1     486.21
2    7896.20
3       2.00
dtype: float64
```

```py
ser_1 = pandas.Series([18.25, 486.21, 7896.20, 2.0])
ser_1.name = 'My_series'
```

A series is created as showed in the sample code above. Pandas series can have a name (secon line of code in example).

> Names are important because they represent a column's name in a dataframe, just like in R.

```py
>>> ser_1
0      18.25
1     486.21
2    7896.20
3       2.00
Name: My_series, dtype: float64
```

More useful methods associated to a Pandas series are listed in the following block of code:

```py
ser_1.dtype	# returns the data type of the underlying NumPy array
ser_1.values	# returns the underlying NumPy array
ser_1.name	# returns the series' name
ser_1.index # returns the series' indices
```

Just like normal Python lists, elements are accessed through indices:

```py
ser_1[2]
```

However, just like in R and in contrast with Python lists, indices can be defined explicitly and can be returned.

```py
ser_1.index	# returns the indices as a list
ser_1.index = ['sample_1', 'sample_2', 'sample_3', 'sample_4',] # changes index labels
```

> This is important because, like in R, indices represent the row or labels in a dataframe.

These features make a series similar to a dictionary too, but in contrast to them, series are **ordered**. Elements can still be accessed by their index numbers or by their labels.

```py
>>> ser_1['sample_3']
7896.2
```

> Series still support selections and slicing, but **as opposed to plain Python, series slicing does return the last element of the slice**.

#### Boolean arrays

Series support mathematical and logical operations. In both cases **those operations are vectorised, like in R**. Boolean operation will return a boolean array/series as a result of the operation vectorisation on the series elements.

**Row extraction by conditional selection** can be performed with index syntax:

```py
ser_1[ser_1 > 100.00]
```
> Mathematical operations, operators (also logical, `|` `&` `~`) are all supported. Use the `help()` function to list available operation functions in the module.

A series can be modified by changing elements by label, index or boolean selection:

```py
ser_1['sample_1'] = 100.20
ser_1.iloc[-1] = 1.00
ser_1[ser_1 > 100.00] = 99.9
```

#### Dataframes

Pandas dataframes are really just R dataframes. They can be built from a list of series, from a dictionary or from a list of lists, but they also have associated functions to import data from files.

    >>> import pandas as pd
    >>> dc = {'col_1': [15789, 852, 6325],
            'col_2':  [0.90, 0.75, 0.32]}
    >>> df = pd.DataFrame(dc, index=['row_1', 'row_2', 'row_3'])

Below are listed some available methods to consult dataframe attributes:

```py
.index
.columns
.info
.size
.shape
.describe   # similar to .info,  but gives a summary of statistics (only for numeric columns)
.dtypes
.dtypes.value_counts()
```

Data is extracted from dataframes by row or column:

```py
df.loc['row_name_here'] # extracts the row through index name (like keys for dictionaries)
df.iloc[row_number_here]    # extracts the row through index number (like list indices)
df['column_name_here']  # extracts the column through column name (series name)
```

> Extracted data is returned as a series. The **`.to_frame()`** method will convert a series to dataframe (just like R's `.as_dataframe()` function).

When extracting columns or rows (with `.loc` or `.iloc`), is possible to:
    
- use slices and multi-indexing
    ```py
    df[['col2', 'col4']]
    df.loc['row1':'row3']
    ```
- add second dimension
    ```py
    df.loc['row1':'row3', 'col1']
    df.loc['row1':'row3', ['col1', 'col3']]
    ```
##### Conditional selection

As for conditional extraction of data from series, selection can be performed conditionally on dataframes too. The output will be boolean arrays.

Examples:

```py
df['col_1'] > 85    # filters values in a column and returns a boolean array

df.loc[df['col_1'] > 85] # filters rows based on condition (value >85 in col_1)
df.loc[df['col_1'] > 85, ['col_1', 'col_3']]    # same as above, but with column selection
df.loc[df['col_1'] > 85, 'col_3']
```

##### Conditional selection: `.query()`

The last 2 of the above examples demonstrate how to conditionally select rows, extracting values from columns based on the corresponding value of another column. Below are more examples on that useful topic:

```py
>>> import pandas as pd # manage libraries
>>> dc = {'A': ['r1', 'r1', 'r3', 'r2'], 'B': [1, 2, 3, 4]} # prepare dictionary
>>> df = pd.DataFrame(dc) # create dataframe from dc
>>> df.info # print dataframe summary
<bound method DataFrame.info of     A  B
0  r1  1
1  r1  2
2  r3  3
3  r2  4>
>>> df.loc[df['B'] == 3, 'A'] # extract value from column 'A' based on corresponding value in 'B' 
2    r3
Name: A, dtype: object
>>> df.loc[df['B'] == 3, 'A'].iloc[0] # same as above, but with selection of first element
'r3' # (returns a series, in this case of len 1. To isolate the value we need selection through index)
>>> df.query('B==3')['A'] # same as above, but less verbose, thanks to .query() method
2    r3
Name: A, dtype: object
>>> df.query('B!=3')['A']
0    r1
1    r1
3    r2
```

In the example above, the **`.query()`** Dataframe method allows to perform conditional selection avoiding `pandas` horrible syntax: condition is the function's argument, while column(s) is/are selected from its output Dataframe through column(s) name.

##### Conditional selection: `.drop()`

The method **`.drop()`**, on the other hand, allows to **remove** elements based on a condition. It generates a new dataframe, lacking the column, rows or values that have been dropped:

```py
# drop rows
df.drop('row_2')
df.drop(['row_2', 'row_3'])

# drop columns
df.drop(columns=['col_1', 'col_4'])
```

#### Operations

Operations with series and dataframes work at column level: math is "broadcasted" on all values in a column from dataframe_1, with the second member being the value in dataframe_2/series_2

Example:

    >>> df
           col_1  col_2
    row_1  15789   0.90
    row_2    852   0.75
    row_3   6325   0.32
    >>> s1 = pd.Series([350, 0.5], index=['col_1', 'col_2'])
    >>> df - s1
             col_1  col_2
    row_1  15439.0   0.40
    row_2    502.0   0.25
    row_3   5975.0  -0.18

#### Modifying dataframes

Values in columns or rows can be replaced using a new element or series. New columns can be added with a new series or can be created as output of an operation on other columns:

    >>> df['col_3'] = df['col_1'] / df['col_2']
    >>> df
        col_1  col_2         col_3
    row_1  15789   0.90  17543.333333
    row_2    852   0.75   1136.000000
    row_3   6325   0.32  19765.625000

The method `.rename()` allows to rename columns (series names) or rows (indices). Methods, like the string method `.upper()`, can be used as arguments (Ex.: `df.rename(index=str.upper())`).

To rename columns or rows:

```py
# to rename one or more columns or rows using .rename()
df.rename(columns={'old_col_name_1': 'new_col_name_1', 'old_col_name_2': 'new_col_name_2'}, inplace=True)
new_df = df.rename(index={'old_ix_name_1': 'new_ix_name_1', 'old_ix_name_2': 'new_ix_name_2'}, inplace=False)
df.rename(columns={'old_col_name_1': 'new_col_name_1'}, index={'old_ix_name_2': 'new_ix_name_2'}, inplace=True)

# to rename all columns/indices directly
df.columns = ['N_of_something', 'Percentage_of_something']
df.index = ['index_1', 'index_2']
```

> **Note 1:** `.rename()`'s `inplace=` argument allows to modify the dataframe in place, without the need to reassign the output to a variable, if it's set to `True`.

> **Note 2:** the direct renaming of columns or indices works by assigning a new list of names to the dataframe's corresponding attribute. **It requires a list of the same length** of that returned by the attribute invocation, so if a dataframe has 12 columns, a list of 12 names must be provided.

#### Read data from external table

The following functions perform import of external data as dataframes in Python:

```py
df = pandas.read_csv('path/to/file.csv')   # argument `header=None` after path if the table has no header
df.set_index('col_1', inplace=True) # uses the specified column as the indices of the dataframe
```

The **`pandas.read_csv()`** function allows customisation through parameters to set every feature of the dataframe when the `csv` is being imported.

Example:

```py
df = pandas.read_csv('path/to/file.csv',   # path to csv
    header=None,    # don't infer the header
    names=['ID', 'Value', 'Date'],  # use list as column names (header)
    sep=',' # just an example, since default is already ','
    index_col=0,    # use column with index 0 as index names
    parse_dates=True    # parse dates as datetime objects
    )
```

Reading `tsv` tables works in the same way: it's sufficient to replace the separator character passed to the `sep` argument. A `pandas.read_tsv()` functions is also available (it's an alias for the same function but with "`\t`" as default separator).

#### Data from relational databases

**Reading from database:**

The `pandas` module can read data from relational databases in 2 main ways:

- If data has already been retrieved using the [`sqlite3` module](#sqlite3), it will be stored in a variable as a list of tuples (Cursor object's `.fetchall()` method). This means that the `pandas.DataFrame()` function can be used to create a DataFrame object from that list:

    ```py
    df = pd.DataFrame(results)
    ```

- As an alternative to creating a Cursor object with a Connection object's `.cursor()` method, `pandas` can use its own `pandas.read_sql()` function to directly read the results of a SQL query into a DataFrame object:

    ```py
    import pandas as pd
    import sqlite3

    conn = sqlite3.connect('example.db')

    df = pd.read_sql('SELECT * FROM simpson_characters;', conn)
    ```

The end result for the 2 options is the same and both need to import the `sqlite3` library. The main difference of using `pandas.read_sql()` are that:

- it creates a dataframe without creating a list object first;
- it automatically reads names of headers from the database table;
- it replaces both the creation of a Cursor object and the function call to `.fetchall()`.

**Exporting to databases:**

The Dataframe object's `.to_sql()` method allows to export a dataframe as a database table (`.db` file). The `.to_sql()` method requires 2 arguments: a string to use as file name, plus the Connection object to dump into it.

```py
df.to_sql('mydatabase', conn)
```

In addition, `.to_sql()`'s `if_exist` parameter can be used to specify the behaviour in case the same database already exists. Possible values for this argument are: `if_exist='replace'`, `if_exist='append'` and `if_exist='fail'`.

#### Reading data from HTML tables and spreadsheets

The `pandas` module also include more functions to read from tabular files, like spreadsheets or HTML tables.

- `pandas.read_html()` is the main function to import data from an HTML table, which can be extracted from a HTML page using other libraries, like [`BeautifulSoup`](#beautifulsoup-ie-web-scrapingspidering-with-python).

> Usually HTML tables can contain cells that span more than one column or row, or other features to make contents more human readable. Such characteristics should be filtered out from the resulting dataframe, through data cleaning procedures.

- `pandas.read_excel()` and `.to_excel()` are used to read from a spreadsheet and to save as a spreadsheet, respectively. They include arguments to manage the sheet to import/export in multi-sheet files.

> Considering that spreadsheets are easily converted to `csv` or `tsv` and that spreadsheet readers support such files natively, these functions are actually of little use.

#### Data cleaning

##### `.isnull()`/ `.isna()` and `.notnull()`/ `.notna()`

`pandas` inherit many functions and methods from the `numpy` library. Among them, there is a set of utilities to identify missing values:

```py
import pandas

# for series objects
## with sr as series:
## sr = pandas.Series([25, None, 145, None, 964])
pandas.isna(sr)
pandas.isnull(sr)

# for DataFrame objects
## with `df` as dataframe:
## df = pandas.read_csv('cgMLST_human.csv', index_col=0)
df.isna()
df.isnull()
```

> The `.isnull()` function for series and the `.DataFrame.isnull()` method for dataframe objects are aliases for the corresponding `.isna()` function/method. All of them perform the same task: they identify missing values (the first 2 for series and the last 2 for dataframes).

The corresponding opposite functions and methods are also inerited by `pandas`:

```py
pandas.notna(sr)
pandas.notnull(sr)
df.notna()
df.notnull()
```

> `pandas` offer aliases for the same series function and dataframe method because it tries to mimic the R language, where NA and NULL values are different things (the first a logical, not-number value with its own functions, the second R's `None` value). However, since `pandas` are built on `numpy`, which only deals `NaN` values ("Not a Number") whatever the nature of such not-number object is, there is no difference between the two and `isna` and `isnull` variants only have the purpose of keeping `pandas` function names close to R's.

> Being `pandas` a Python module, missing values are `None` values (*i.e.* the `False` boolean value and all values that evaluate to `False`, like empty strings or 0, are not considered missing values).

Functions and methods that identify missing values in an object all return an object of the same type (series/dataframe) and dimensions of the input, containing logical values (`True`/`False`) depending on the corresponding element in the original object being a missing value or not.

Example:

```py
>>> import pandas
>>> sr = pandas.Series([25, None, 145, None, 964])
>>> pandas.isnull(sr)
0    False
1     True
2    False
3     True
4    False
dtype: bool
```

Missing values can also be generated using `numpy`'s `.nan` method (which is conceptually more correct when working with `pandas`). Using a `.notnull()`/`.isnull` coupled with the `.sum()` method is a perfect way to **identify the number of non-missing or missing values**:

```py
import pandas, numpy

s = pandas.Series(['Luke', 3, numpy.nan, 1, numpy.nan])
print(s.notnull().sum())
```

The methods `.any()` and `.all()` can also be used to check if a series or dataframe contains missing values: `.any()` checks if there's any `True` value in a series (or dataframe), while `.all()` checks if all values are `True`. Thus, by pairing it with the `.isnull()` function, they check if the series returned by `.isnull()` contains any `True`, *i.e.* if `.isnull()` found any missing values.

```py
s.isnull().any()
```

The `.notnull()`/`.isnull` functions (and aliases) also work as methods:

```py
>>> import pandas
>>> sr = pandas.Series([25, None, 145, None, 964])
>>> sr.isnull()
0    False
1     True
2    False
3     True
4    False
dtype: bool
```

**`.notnull()`/`.isnull` (and aliases) can be used for logical extraction from series and dataframe objects:**

```py
>>> sr[pandas.notnull(sr)]
0     25.0
2    145.0
4    964.0
dtype: float64

>>> missing_subagente = df_sort_dt[df_sort_dt['subagente'].isnull()]

```

##### `.dropna()`

To simply remove `NA` values use the **`.dropna()`** method on a series or dataframe object.

```py
clean_sr = sr.dropna()  # drop NAs from series and assign clean series to a new variable
df.dropna() # drop all rows containing at least one NA
df.dropna(axis=1)   # drop all columns with at least one NA (parameter `axis='columns'` also works)
```

> 1. `.dropna()`, as well as the other data-cleaning functions and methods (`.notnull()`/`.isnull`) do not modify the original dataframe. They create a new dataframe, so it's necessary to assign it to a variable in order to access it again (line 1 of the examples above).
> 2. `.dropna()` can also be used on dataframes (lines 2 and 3 of the examples above), but works in a different way on them: **it drops the whole row (or column) if it contains at least one `NA` value**.

The `.dropna()` method can also be used to subset data using a threshold value:

```py
df.dropna(how='any')  # default: drop all rows containing at least one NA
df.dropna(how='all') # drop rows containing only NAs in them
df.dropna(thresh=3) # keep rows if they have at least 3 non-NA values and drop the rest
df.dropna(thresh=3, axis='columns') # same as above, but for columns
```

##### `.fillna()`

The `.fillna()` method replaces all missing values with the specified value.

Example:

```py
df.fillna(0)
```

> In the example above, all missing values are replaced with 0. As for the other methods, `.fillna()` does not changes the original data.

`.fillna()` has the following parameters:

* `axis` - switches between acting on rows (default) or columns ('colums' or 1 argument)
* `method` - replaces main argument and fills the missing values using one of 2 methods:
    * `ffill` - "forward fill": replaces NAs with the last preceding non-NA value
    * `bfill` - "backward fill": replaces NAs with the first following non-NA value

##### `.unique()`

`pandas` also inherit `numpy`'s **`.unique()`** method, which works similarly to `Bash`'s `uniq` command: it returns a `pandas` series (or `numpy`'s array) which consists of only the unique values from the original series:

```py
sr.unique()   # .unique() method applied to a series
```

```py
>>> df['Stages'].unique()   # .unique() method applied to a dataframe's column
array(['In-Training', 'Mega', 'Rookie', 'Champion', 'Ultimate'],
      dtype=object)
>>> df['Stages'].unique().tolist()  # with conversion to list with .tolist() method
['In-Training', 'Mega', 'Rookie', 'Champion', 'Ultimate']
```

> `pandas`' `.uniq()` is a series method: it can only be applied to series/array or **a single** dataframe column.

In order to get the unique values of multiple dataframe columns, `pandas` use the [`.drop_duplicates()`](#duplicated-and-drop_duplicates) method.

##### `.duplicated()` and `.drop_duplicates()`

`.duplicated()` is a series method which returns a new series of Boolean values depending on whether the value is considered a duplicate. By default it treats as duplicates all occurrences of a value **after the first one**.

The parameter `keep` lets the user specify whether to keep the last occurrence instead or if all occurrences that are present more than once should be marked as duplicates, as an override on the default behavior:

```py
my_series = pd.Series([
    'In-Training',
    'Rookie',
    'Champion',
    'Rookie',
    'Rookie'
], index=[
    'Koromon',
    'Agumon',
    'Angemon',
    'Gabumon',
    'Tentomon'
])

my_series.duplicated()
my_series.duplicated(keep='last')   # keep the last occurrence
my_series.duplicated(keep=False)    # all occurrences should return True (keep none of them)
my_series.name = 'Stage'
my_series.duplicated(subset='Stage')    # subsets on the specified column
```

`.drop_duplicates()` uses the same rules as `.duplicated()` to return a dataframe of all the unique combinations:

```py
>>> df  # obviously it can have more than 2 columns
        Stages       Digimon
0  In-Training       Koromon
1         Mega    Wargreymon
2       Rookie       Gabumon
3     Champion       Angemon
4       Rookie      Tentomon
5     Ultimate  Skullgreymon
6       Rookie        Agumon
7     Champion       Angemon

>>> df[['Stages', 'Digimon']].drop_duplicates() # in this case also as df.drop_duplicates()
        Stages       Digimon
0  In-Training       Koromon
1         Mega    Wargreymon
2       Rookie       Gabumon
3     Champion       Angemon
4       Rookie      Tentomon
5     Ultimate  Skullgreymon
6       Rookie        Agumon
```

##### `.value_counts()`

The `.value_counts()` method counts occurrences of unique values in a `pandas` dataframe column:

```py
>>> df['Digimon'].value_counts()
Angemon         2
Wargreymon      1
Skullgreymon    1
Agumon          1
Gabumon         1
Koromon         1
Tentomon        1
Name: Digimon, dtype: int64
```

##### `.replace()`

Not to be confused with core Python's [`.replace()`](#replace) **string method**, `pandas`'s own `.replace()` is a **dataframe and series method**. It replaces a given item with a provided value; the value to replace can be a regex, list, dictionary, series, integer, float, or `None` value. Values of the Series/DataFrame are replaced with other values dynamically (with no need to specify indices or locations).

Examples:

```py
# <series_or_dataframe>.replace(<value(s)_to_replace>, <what_to_replace_with>)
s.replace(1, 5) # replace all 1s in the series (or dataframe) with 5s
df.replace([0, 1, 2, 3], 4) # replace all elements in list with 4s
df.replace([0, 1, 2, 3], [4, 3, 2, 1])  # replace each item from list 1 with corresponding item in list 2
s.replace([1, 2], method='bfill')   # replace values in list applying the .bfill() method
df.replace({0: 10, 1: 100}) # replace each element equal to a key in provided dictionary with its corresponding value
df.replace({'A': 0, 'B': 5}, 100)   # replace all 0s from column A and all 5s in column B with 100
df.replace({'A': {0: 100, 4: 400}}) # from column A replace 0s with 100s and 4s with 400s
df.replace(to_replace=r'^ba.$', value='new', regex=True)    # syntax for regex. Can be mixed with other kinds of subsitution
```

##### `.iterrows()`

The dataframe method `.iterrows()` is used to iterate over a dataframe rows; it returns a dictionary which includes the index of the row and data as a series. Since it returns a dictionary, it requires a paired iteration variable syntax in a for loop:

```py
for index, row in missing_subagente.iterrows():
    my_value = row['col3']
```

#### Data attributes

Data stored in a series or dataframe columns preserve their data type as attribute. Main data types in these scenarios are strings (`.str` attribute), datetime (`.dt`) and categorical (`.cat`). Methods associated with certain data type are accessible and usable on the series or column, by using the method preceded by the corresponding attribute:

```py
dc = {'Sample_ID': ['NC_00254', 'JB_12345', 'JM_98700'],
      'Date':  [datetime.date(2022, 12, 25), datetime.date(2015, 9, 2), datetime.date(2019, 3, 18)],
      'Label':  ['meat', 'dairy', 'vegetables']}
df = pd.DataFrame(dc)

df['Sample_ID'] = df['Sample_ID'].astype('str')
df['Date'] = pd.to_datetime(df['Date'])
df['Label'] = df['Label'].astype('category')

df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3 entries, 0 to 2
Data columns (total 3 columns):
Sample_ID    3 non-null object
Date         3 non-null datetime64[ns]
Label        3 non-null category
dtypes: category(1), datetime64[ns](1), object(1)
memory usage: 283.0+ bytes

df['Sample_ID'].str.split('_')   # string method
df['Date'].dt.strftime("%Y")   # datetime object method
df['Label'].cat.remove_categories('dairy')   # categorical data method
```

#### Plotting

`Pandas` integrates with `matplotlib`, so it can produce graphs with the **`df.plot()`** function, just like R with the `ggplot` library.

To save a plot, it is necessary to use the **`.pyplot.savefig()`** method after returning the plot (like in R). The `.pyplot.savefig()` method is available in the `matplotlib.pyplot` subset of functions and is therefore called as `matplotlib.pyplot.savefig()` (or, more simply, by using an alias).

```py
import pandas
import matplotlib.pyplot as plt # import the .pyplot subset of functions of the matplotlib library as an alias

... # creation of a dictionary to build a dataframe later (omissis)
df_2 = pandas.DataFrame(dic.values(), index=dic.keys()) # creation of pandas dataframe
df_2.columns = ['population']   # renaming the (only) column

barplot = df_2.plot(kind='bar') # creation of a plot object
plt.savefig('dataset_barplot.png')  # saving the plot with matplotlib.pyplot.savefig()
```



### `Datetime`

Library with functions to parse objects as date and time ("datetime objects"). Use the `.to_datetime()` method to convert an objecto to a datetime object.



<!--

### `.insert() method`

>>> df.insert(3, "value_1", [23.25, 58, 0.00])
>>> df
  Sample_ID        Date       Label  value_1
0  NC_00254  2022-12-25        meat    23.25
1  JB_12345  2015-09-02       dairy    58.00
2  JM_98700  2019-03-18  vegetables     0.00
>>> df.insert(4, "value_2", [0.25, 159.47, 96])
>>> df
  Sample_ID        Date       Label  value_1  value_2
0  NC_00254  2022-12-25        meat    23.25     0.25
1  JB_12345  2015-09-02       dairy    58.00   159.47
2  JM_98700  2019-03-18  vegetables     0.00    96.00


-->


## `lambda` functions

https://realpython.com/python-lambda/



```py
# Function to add quotes to a string
def add_quotes(s):
    return f"'{s}'"

# Applying the function to the column
df['col1'] = df['col1'].apply(add_quotes)
```