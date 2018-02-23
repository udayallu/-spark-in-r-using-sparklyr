# Spark in r using **sparklyr**
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
## pre requs..
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
## Exer 2
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
## Exer3
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
## Exer 3
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
## Exer 4

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
## Exer5
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
## Exer 6

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
## Exer 6
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
## Chapter 2
- When working with the reomte data such as spark data frames an imporant technique is managing where the results are calcualted.
- We learn to use the compute function to store results in spar.
-  We learn to use collect fucntion to pull results back to the r.

## Exer 7

A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

- Select all columns from track_metadata_tbl starting with "artist".
- Select all columns from track_metadata_tbl ending with "id".

```
# track_metadata_tbl has been pre-defined
track_metadata_tbl

track_metadata_tbl %>%
# Select columns starting with artist
select(starts_with("artist"))

track_metadata_tbl %>%
 # Select columns ending with id
select(ends_with("id"))
```
 ## Exer 8
 A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

- Select all columns from track_metadata_tbl containing "ti".
- Select all columns from track_metadata_tbl matching the regular expression "ti.?t".
 
 ```
# track_metadata_tbl has been pre-defined
track_metadata_tbl

track_metadata_tbl %>%
  # Select columns containing ti
select(contains("ti"))

track_metadata_tbl %>%
  # Select columns matching ti.?t
select(matches("ti.?t"))
 ```
 ### exer 9
 A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

- Find the distinct values of the artist_name column from track_metadata_tbl
 ```
 # track_metadata_tbl has been pre-defined
track_metadata_tbl

track_metadata_tbl %>%
  # Only return rows with distinct artist_name
  distinct(artist_name)
 ```
 
### Exer 10

A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

- Count the values in the artist_name column from track_metadata_tbl.
- Pass sort = TRUE to sort the rows by descending popularity.
- Restrict the results to the top 20 using top_n()
```
# track_metadata_tbl has been pre-defined
track_metadata_tbl

track_metadata_tbl %>%
  # Count the artist_name values
  count(artist_name,sort=TRUE) %>%
  # Restrict to top 20
 top_n(20)
```

## Exer 11
A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

- Filter the rows of track_metadata_tbl where artist_familiarity is greater than 0.9, assigning the results to results.
- Print the class of results, noting that it is a tbl_lazy (used for remote data).
- Collect your results, assigning them to collected.
- Print the class of collected, noting that it is a tbl_df (used for local data).

```
# track_metadata_tbl has been pre-defined
track_metadata_tbl

results <- track_metadata_tbl %>%
  # Filter where artist familiarity is greater than 0.9
  filter(artist_familiarity > 0.9)

# Examine the class of the results
 class(results)

# Collect your results
collected <- results %>%
  collect()

# Examine the class of the collected results
class(collected)
```
## Exer 12

A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

- Filter the rows of track_metadata_tbl where artist_familiarity is greater than 0.8.
- Compute the results using compute().
- Store the results in a Spark data frame named "familiar_artists".
- Assign the result to an R tibble named computed.
- See the available Spark datasets using src_tbls().
- Print the class() of computed. Notice that unlike collect(), compute() returns a remote tibble. The data is still stored in the Spark cluster.

```
# track_metadata_tbl has been pre-defined
track_metadata_tbl

computed <- track_metadata_tbl %>%
  # Filter where artist familiarity is greater than 0.8
  filter(artist_familiarity>0.8) %>%
  # Compute the results
  compute()

# See the available datasets
src_tbls(spark_conn)

# Examine the class of the computed results
class(computed)
```
## Exer 13
```
# track_metadata_tbl has been pre-defined
track_metadata_tbl

computed <- track_metadata_tbl %>%
  # Filter where artist familiarity is greater than 0.8
  filter(artist_familiarity>0.8) %>%
  # Compute the results
  compute("familiar_artists")

# See the available datasets
src_tbls(spark_conn)

# Examine the class of the computed results
class(computed)
```
## Exer 14
A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

- Group the contents of track_metadata by artist_name, then:
- Summarize the groupwise mean of duration as a new column, mean_duration.
- Assign the results to duration_by_artist.
- Find the artists with the shortest songs by arranging the rows in ascending order of mean_duration.
- Likewise, find those with the longest songs by arranging in descending order of mean_duration.

```
# track_metadata_tbl has been pre-defined
track_metadata_tbl

duration_by_artist <- track_metadata_tbl %>%
 
  group_by(artist_name) %>%
  # Calc mean duration
  summarize(mean_duration=mean(duration))

duration_by_artist %>%
  # Sort by ascending mean duration
  arrange(mean_duration)

duration_by_artist %>%
  # Sort by descending mean duration
  arrange(desc(mean_duration))

```

## Exer 15
A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

- Group the contents of track_metadata by artist_name.
- Add a new column named time_since_first_release.
- Make this equal to the groupwise year minus the first year (that is, the min() year) that the artist released a track.
- Arrange the rows in descending order of time_since_first_release.
```
# track_metadata_tbl has been pre-defined
track_metadata_tbl

track_metadata_tbl %>%
  # Group by artist
  group_by(artist_name,year) %>%
  # Calc time since first release
  mutate(time_since_first_release=year-min(year)) %>%
  # Arrange by descending time since first release
  arrange(desc(time_since_first_release))
```
## Exer 16

### Adv SQL
As previously mentioned, when you use the dplyr interface, sparklyr converts your code into SQL before passing it to Spark. Most of the time, this is what you want. However, you can also write raw SQL to accomplish the same task. Most of the time, this is a silly idea since the code is harder to write and harder to debug. However, if you want your code to be portable – that is, used outside of R as well – then it may be useful. For example, a fairly common workflow is to use sparklyr to experiment with data processing, then switch to raw SQL in a production environment. By writing raw SQL to begin with, you can just copy and paste your queries when you move to production.

SQL queries are written as strings, and passed to dbGetQuery() from the DBI package. The pattern is as follows.

query <- "SELECT col1, col2 FROM some_data WHERE some_condition"
a_data.frame <- dbGetQuery(spark_conn, query)
Note that unlike the dplyr code you've written, dbGetQuery() will always execute the query and return the results to R immediately. If you want to delay returning the data, you can use dbSendQuery() to execute the query, then dbFetch() to return the results. That's more advanced usage, not covered here. Also note that DBI functions return data.frames rather than tibbles, since DBI is a lower-level package.

A Spark connection has been created for you as spark_conn. A tibble attached to the track metadata stored in Spark has been pre-defined as track_metadata_tbl.

Complete the query to select all columns from the track_metadata Spark data frame where the year is less than 1935 and the duration is greater than 300 seconds.
Call dbGetQuery() to execute the query, assigning the results to results, then view the output.
```
# Write SQL query
query <- "SELECT * FROM track_metadata WHERE year<1935 AND duration>300"

# Run the query
(results <- dbGetQuery(spark_conn, query))
```
## Exer 16
## Left joins
As well as manipulating single data frames, sparklyr allows you to join two data frames together. A full treatment of how to join tables together using dplyr syntax is given in the Joining Data in R with dplyr course. For the rest of this chapter, you'll see some examples of how to do this using Spark.

- A left join takes all the values from the first table, and looks for matches in the second table. If it finds a match, it adds the data from the second table; if not, it adds missing values. The principle is shown in this diagram.

###Exer

A Spark connection has been created for you as spark_conn. Tibbles attached to the track metadata and artist terms stored in Spark have been pre-defined as track_metadata_tbl and artist_terms_tbl respectively.

- Use a left join to join the artist terms to the track metadata by the artist_id column.
- The table to be joined to, track_metadata_tbl, comes first.
- The table that joins the first, artist_terms_tbl, comes next.
- Assign the result to joined.
- Use dim() to determine how many rows and columns there are in the joined table.
```
# track_metadata_tbl and artist_terms_tbl have been pre-defined
track_metadata_tbl
artist_terms_tbl

# Left join artist terms to track metadata by artist_id
joined <- left_join(track_metadata_tbl, artist_terms_tbl, by = "artist_id")

# How many rows and columns are in the joined table?
dim(joined)
```

## Exer 17
## Anti joins
- In the previous exercise, the joined dataset wasn't as big as you might have expected, since not all the artists had tags associated with them. Anti joins are really useful for finding problems with other joins.

- An anti join returns the rows of the first table where it cannot find a match in the second table. The principle is shown in this diagram.
- Anti joins are a type of filtering join, since they return the contents of the first table, but with their rows filtered depending upon the match conditions.
- The syntax for an anti join is more or less the same as for a left join: simply swap left_join() for anti_join().
```
anti_join(a_tibble, another_tibble, by = c("id_col1", "id_col2"))
```

### EXER 
A Spark connection has been created for you as spark_conn. Tibbles attached to the track metadata and artist terms stored in Spark have been pre-defined as track_metadata_tbl and artist_terms_tbl respectively.

- Use an anti join to join the artist terms to the track metadata by the artist_id column. Assign the result to joined.
- Use dim() to determine how many rows and columns there are in the joined table.
```

```
### Semi joins
Semi joins are the opposite of anti joins: an anti-anti join, if you like.

- A semi join returns the rows of the first table where it can find a match in the second table. The principle is shown in this diagram.
- The syntax is the same as for other join types; simply swap the other join function for semi_join()
```
semi_join(a_tibble, another_tibble, by = c("id_col1", "id_col2"))
```
You may have spotted that the results of a semi join plus the results of an anti join give the orignial table. So, regardless of the table contents or how you join them, semi_join(A, B) plus anti_join(A, B) will return A (though maybe with the rows in a different order).

### Exeer

A Spark connection has been created for you as spark_conn. Tibbles attached to the track metadata and artist terms stored in Spark have been pre-defined as track_metadata_tbl and artist_terms_tbl respectively.

- Use a semi join to join the artist terms to the track metadata by the artist_id column. Assign the result to joined.
- Use dim() to determine how many rows and columns there are in the joined table.
```
# track_metadata_tbl and artist_terms_tbl have been pre-defined
track_metadata_tbl
artist_terms_tbl

# Semi join artist terms to track metadata by artist_id
joined <- semi_join(track_metadata_tbl,artist_terms_tbl,by="artist_id")

# How many rows and columns are in the joined table?
dim(joined)
```


