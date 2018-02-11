---
date: 2018-02-10
title: Transfer Large Files
---
We are working with a client to evaluate performance measures and are using the NPMRDS as a data source. One of the issues we face is that since we are looking at multiple year data, transferring the data (via CSV files) from the client's servers to our servers involves either:

1. Using portable devices to copy the files over and ship it to us; or
2. Figure out a way to compress the file into binary for transport.

What's the fun in transferring files in portable devices. So I thought I would be interesting to see if the files can be compressed into a smaller size and then sent via FTP. For testing I used a CSV file I have that has 1.6M records and 7 variables and has a size of 935MB. 

So as a first step, I thought I would use HDF5 since I recall reading it is very efficient for large files (https://stackoverflow.com/questions/16639877/hdf5-taking-more-space-than-csv and https://github.com/pandas-dev/pandas/issues/3651). Below is my python code for converting CSV to HDF. 

- Input File: 935 MB CSV File
- Output File: 1.35 GB HDF5 File

```python 

# System information - Python 3.6.1
# Import numpy, pandas, and time and all the functions from pandas

import numpy as np
import pandas as pd
from pandas import *

# Create a hdf5 and call it testdata.h5

hdf = HDFStore('testdata.h5')

# Read in CSV file - In this example it vtrans_int14.csv & this file has 
# all headers

df1=pd.read_csv("vtrans_int14.csv")

# Since hdf5 can handle multiple tables, we give it 
# a name d1 and ask it to read the table df1 created above 
# and put it in hdf5 format

hdf.put('d1', df1, format='table', data_columns=True)

# Close the hdf file
hdf.close()

```
The next step was to try R and use the RDS format for saving the CSV file. The one concern I have with R is whether it might crash when reading in a large CSV file because of memory limitations. Went to my trusty data.table library to read the CSV file in and then saved it as a RDS file. It compressed it to 17% of the original CSV file (impressive).

- Input File: 935 MB CSV File
- Output File: 160 MB RDS File

```R

# Open library data.table
# If library data.table is not available, install from 
# cran.rstudio.com or so some other CRAN mirror

library(data.table) 

# Read the CSV file
# If the CSV files are huge and RAM is limited, then 
# R can crash

vtrans_int14 <- fread("vtrans_int14.csv",sep=",",header=T)

# Save the file to RDS format file
saveRDS(vtrans_int14,"testdata.rds")

```







