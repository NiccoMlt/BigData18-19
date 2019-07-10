# E3 Exercises on Spark File

## Using Spark

Spark supports three languages (Scala, Java, Python) and provides two approaches to run applications

Spark-shell
  - **Interactive** shell that enables ad-hoc data analysis
  - _Automatically initializes the `SparkContext`_

Standalone applications
  - Submit the application (jar file or Python script) using `spark-submit`
  - _The `SparkContext` must be initialized by the application_
  - Three deployment modes: cluster mode, client mode, local mode

## Creating RDDs

RDDs can be created either from:
  - Objects (e.g., an array of Integer)
  - External datasets (e.g., a list of files in a directory of HDFS)

When creating RDDs, one important parameter is the number of
partitions to cut the dataset into: **_Spark runs one task for each partition_**
  - Too few partitions: some CPU cores will not be used
  - Too many partitions: Excessive overhead in managing many small tasks
  - Good practice: **2-4 partitions for each CPU core** in the cluster

If not specified, Spark sets the number of partitions automatically
  - For objects, it is based on the cluster
  - For external datasets, it is one partition for each block (128MB in HDFS)

### Creating RDDs from objects

Existing collections of data can be copied to an RDD, in order to enable parallel computations
  - Requires the entire collection in memory on one machine
    ```scala
    val data = Array(1, 2, 3, 4, 5)
    val rddData = sc.parallelize(data)
    ```

The number of partitions can be set manually by passing it as a second parameter to parallelize

```scala
sc.parallelize(data, 10)
```

A synonym for parallelize is `makeRDD`

### Creating RDDs from external datasets

Spark can create RDDs from any storage source supported by Hadoop
  - Local file system, HDFS, Cassandra, HBase, Amazon S3, etc.
  - Spark supports text files, SequenceFiles, and any other Hadoop InputFormat

Text file RDDs can be created using SparkContext’s textFile method

```scala
val rddFile = sc.textFile("data.txt")
```

The first argument can be a file or a directory; wildcards are supported

```scala
sc.textFile("/my/directory")
sc.textFile("/my/directory/*.gz")
```

The second (optional) parameter is the number of partitions
  - By default, Spark creates one partition for each block (128MB in HDFS)
  - It is not allowed to have fewer partitions than blocks
