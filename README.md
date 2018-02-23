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

## dplyr verbs
Deeplayer provide the grammer of the data transformation. The 5 main data transformation we can apply are 
1. Select the columns using **select() **
2. Filter the rows using **filter() **
3. Arrange the rows using **arrange() **
4. Change and add columns using the mutate using ** mutate()**
5. calculate the summary statistics using the using **summarize() **


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
## Exercise 2
Copying data into Spark
Before you can do any real work using Spark, you need to get your data into it. sparklyr has some functions such as spark_read_csv() that will read a CSV file into Spark. More generally, it is useful to be able to copy data from R to Spark. This is done with dplyr's copy_to() function. Be warned: copying data is a fundamentally slow process. In fact, a lot of strategy regarding optimizing performance when working with big datasets is to find ways of avoiding copying the data from one location to another.

copy_to() takes two arguments: a Spark connection (dest), and a data frame (df) to copy over to Spark.

Once you have copied your data into Spark, you might want some reassurance that it has actually worked. You can see a list of all the data frames stored in Spark using src_tbls(), which simply takes a Spark connection argument (x).

```
# Load dplyr
library("dplyr")

# Explore track_metadata structure
str(track_metadata)

# Connect to your Spark cluster
spark_conn <- spark_connect(master="local")

# Copy track_metadata to Spark
track_metadata_tbl <- copy_to(spark_conn, track_metadata)

# List the data frames available in Spark
src_tbls(spark_conn)

```
## Exercise 3
A Spark connection has been created for you as spark_conn. The track metadata for 1,000 tracks is stored in the Spark cluster in the table "track_metadata".

1. Link to the "track_metadata" table using tbl(). Assign the result to track_metadata_tbl.
2. See how big the dataset is, using dim() on track_metadata_tbl.
3. See how small the tibble is, using object_size() on track_metadata_tbl.
```
# Link to the track_metadata table in Spark
track_metadata_tbl <- tbl(spark_conn,"track_metadata")

# See how big the dataset is
dim(track_metadata_tbl)

# See how small the tibble is
object_size(track_metadata_tbl)
```
## Exercise 3
A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

1. Print the first 5 rows and all the columns of the track metadata.
2. Examine the structure of the tibble using str().
3. Examine the structure of the track metadata using glimpse().

```
# Print 5 rows, all columns
print(track_metadata_tbl, n = 5, width = Inf)

# Examine structure of tibble
str(track_metadata_tbl)

# Examine structure of data
glimpse(track_metadata_tbl)
```
## Exercise 4

-A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

-Select the artist_name, release, title, and year using select().
Try to do the same thing using square bracket indexing. Spoiler! This code throws an error, so it is wrapped in a call to tryCatch().

```
# track_metadata_tbl has been pre-defined
track_metadata_tbl

# Manipulate the track metadata
track_metadata_tbl %>%
  select(artist_name, release, title,year)

# Try to select columns using [ ]
tryCatch({
    track_metadata_tbl[, c("artist_name", "release", "title", "year")]
  },
  error = print
)
```
## Exercise 5
- A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

- As in the previous exercise, select the artist_name, release, title, and year using select().
Pipe the result of this to filter() to get the tracks from the 1960s.

```
# track_metadata_tbl has been pre-defined
glimpse(track_metadata_tbl)

# Manipulate the track metadata
track_metadata_tbl %>%
  select(artist_name, release, title,year) %>%
  filter(year >= 1960, year < 1970)
```
## Exercise 6

A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

1. Select the artist_name, release, title, and year fields.
2. Pipe the result of this to filter on tracks from the 1960s.
3. Pipe the result of this to arrange() to order by artist_name, then descending year, then title.

```
# track_metadata_tbl has been pre-defined
track_metadata_tbl

# Manipulate the track metadata
track_metadata_tbl %>%
  # Select columns
  select(artist_name, release, title,year) %>%
   filter(year >= 1960, year < 1970) %>%
  arrange(artist_name,desc(year),title)
```
## Exerise 6
A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

 - Select the title, and duration fields. Note that the durations are in seconds.
- Pipe the result of this to mutate() to create a new field, duration_minutes, that contains the track duration in minutes.

```
# track_metadata_tbl has been pre-defined
track_metadata_tbl

# Manipulate the track metadata
track_metadata_tbl %>%
  select(title,duration) %>%
  # Mutate columns
  mutate(duration_minutes=duration/60)

```

## Exer 7
A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

- Select the title, and duration fields.
- Pipe the result of this to create a new field, duration_minutes, that contains the track duration in minutes.
- Pipe the result of this to summarize() to calculate the mean duration in minutes, in a field named mean_duration_minutes.

```
# track_metadata_tbl has been pre-defined
track_metadata_tbl

# Manipulate the track metadata
track_metadata_tbl %>%
  select(title,duration) %>%
  # Mutate columns
   mutate(duration_minutes=duration/60) %>%
  # Summarize columns
  summarize(mean_duration_minutes = mean(duration_minutes))
```
## Exp 8
