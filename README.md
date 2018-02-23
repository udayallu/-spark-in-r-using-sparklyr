# spark-in-r-using-sparklyr
1. R is a wonderful tool for the data analysis. 
2. Spark is a cluster computing platform.
3. Sparklyr is an r package that gives access to the spark from R 

## R 
- R lets you write data analysis code quickly. With a bit of care, you can also make your code easy to read, which means that you can easily maintain your code too. In many cases, R is also fast enough at running your code.
- Unfortunately, R requires that all your data be analyzed in memory (RAM), on a single machine. This limits how much data you can analyze using R. There are a few solutions to this problem, including using Spark.

## Spark
- Spark is an open source cluster computing platform. That means that you can spread your data and your computations across multiple machines, effectively letting you analyze an unlimited amount of data. 

**The two technologies complement each other strongly. By using R and Spark together you can write code fast and run code fast!**

## sparklyr
- sparklyr is an R package that lets you write R code to work with data in a Spark cluster. It has a dplyr interface, which means that you can write (more or less) the same dplyr-style R code, whether you are working with data on your machine or on a Spark cluster.
- sparklyr is newer, and doesn't have a full set of features. There are some things that you just can't do with Spark from R right now. The Scala and Python interfaces to Spark are more mature.

## Installing sparklr in r studio that is present in the spark os 
If you wish to install Spark on your local system, simply install the **sparklyr package** and call **spark_install()**


## The connect-work-disconnect pattern
### Working with sparklyr is very much like working with dplyr when you have data inside a database. In fact, sparklyr converts your R code into SQL code before passing it to Spark.

### The typical workflow has three steps:
1. Connect to Spark using **spark_connect()**.
2. Do some work.
3. Close the connection to Spark using **spark_disconnect()**.


## In this exercise, you'll do this simplest possible piece of work: returning the version of Spark that is running, using spark_version().

- spark_connect() takes a URL that gives the location to Spark. For a local cluster (as you are running), the URL should be "local". For a remote cluster (on another machine, typically a high-performance server), the connection string will be a URL and port to connect on.

- spark_version() and spark_disconnect() both take the Spark connection as their only argument.

- One word of warning. Connecting to a cluster takes several seconds, so it is impractical to regularly connect and disconnect. While you need to reconnect for each DataCamp exercise, when you incorporate sparklyr into your own workflow, it is usually best to keep the connection open for the whole time that you want to work with Spark.
## Instricutions
- Load the sparklyr package with library().
- Connect to Spark by calling spark_connect(), with argument master = "local". Assign the result to spark_conn.
- Get the Spark version using spark_version(), with argument sc = spark_conn.
- Disconnect from Spark using spark_disconnect(), with argument sc = spark_conn.

```
# Load sparklyr
library(sparklyr)
# Connect to your Spark cluster
spark_conn <- spark_connect(master="local")

# Print the version of Spark
spark_version(spark_conn)
# Disconnect from Spark
spark_disconnect(spark_conn)
```

## dplyr verbs
Deeplayer provide the grammer of the data transformation. The 5 main data transformation we can apply are 
1. Select the columns using **select() **
2. Filter the rows using **filter() **
3. Arrange the rows using **arrange() **
4. Change and add columns using the mutate using ** mutate()**
5. calculate the summary statistics using the using **summarize() **


