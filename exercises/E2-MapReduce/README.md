# E2 Exercises on MapReduce File

## MapReduce (command line)

### Create the job and compile

Create a local directory for your mapreduce jobs
  - `mkdir mapreduce`
  - `mkdir mapreduce/wordcount`
  - `cd mapreduce/wordcount`

Get the job file
  - `hdfs dfs –get /bigdata/mapreduce/wordcount/WordCount.java`

Compile the Java source file and create the jar
  - `javac -classpath $(hadoop classpath) -d . WordCount.java`
    - Notice: the command `hadoop classpath` returns the class path(s) needed to get the Hadoop jar and the required libraries
  - `jar cf wc.jar WordCount*.class`

OR: directly get the jar
  - `hdfs dfs –get /bigdata/mapreduce/wordcount/wc.jar mapreduce/wordcount`
 
### Run the jar

```bash
hadoop jar <jar-file> <Main-class> <inputDir> <outputDir> [params]
```

  - `<inputDir>` : existing directory on HDFS that contains the input files
    -  e.g. `/bigdata/dataset/sample-input`
  - `<outputDir>` : directory on HDFS to be created by the job to store the results
    - e.g. `mapreduce/wordcount/output`
  - `[params]` : optional parameters

```bash
hadoop jar wc.jar WordCount /bigdata/dataset/sample-input mapreduce/wordcount/output
```

### Verify

Check the results
  - The output directory is created by the job
  - An empty `_SUCCESS` file is automatically created if the job is completed successfully
  - An output file `part-r-xxxxx` is created for each instantiated reducer
    - "`-r`" stands for "*reducer*"; "`-m`" is used for outputs provided by the *mappers*
    - "`-xxxxx`" is the *number of the reducer*
  - `hdfs dfs -cat mapreduce/wordcount/output/*`
    - Display the output of all the files in the directory
  - `hdfs dfs -cat mapreduce/wordcount/output/* | head –n 30`
    - Display the first 30 lines from the output of all the files in the directory
  - `hadoop fs -getmerge mapreduce/wordcount/output local-wc-output`
    - Merge all the files in the directory and store the result in the local-wc-output file in the local file system
  - `sort local-wc-output | tail`
    - Sort the file and display the last 10 lines

## MapReduce exercises

### Datasets

Reference directory on HDFS:
  - /bigdata/dataset

capra
  - Contains a single file, capra.txt , with the words of the proverb

divinacommedia
  - Contains a single file, divina.txt , with the whole poem

weather ( ftp://ftp.ncdc.noaa.gov/pub/data/noaa/ )
  - Contains weather data of year 2000 in one text file (13 GB)

weather-raw
  - Contains raw weather data of year 2000: more than 10000 text files

weather-sample
  - Contains a sample of the weather dataset
  
### Exercises' code

Get all the Java files:
  - hdfs:///bigdata/mapreduce/mr-src.zip
  - http://isi-vclust0.csr.unibo.it:50070/webhdfs/v1/bigdata/mapreduce/mr-src.zip?op=OPEN

Import the zip file in a Java project in Eclipse or Intellij

### Exercise 1

Modify the source code of the WordCount job to add the use of the Combiner.

Compile and run the job on the `capra` and `divinacommedia` datasets
  - `hadoop jar mr.jar exercise1.WordCount /bigdata/dataset/capra mapreduce/output`
  - `hdfs dfs -cat mapreduce/output/* | head -n 30`
  - How much time does it take to run the jobs?
  - How many mappers and reducers have been instantiated?
  - How is the output sorted?
  - What happens if we enforce 0 reducers?

### Exercise 2

Make a copy of the `WordCount` exercise and turn it to `WordLengthCount`
  - Instead of counting the number of times that a word appears, we want to count how many words are there with a given length
  - If the number of reducers is not given, default it to 1

Compile and run the WordLengthCount job on the `capra` and `divinacommedia` datasets
  - `hadoop jar mr.jar exercise2.WordLengthCount /bigdata/dataset/capra mapreduce/output`
  - `hdfs dfs -cat mapreduce/output/* | head -n 30`
  
### Exercise 3

Input: `weather-sample` dataset (not `weather-raw`, nor `weather`!)

Compile and run the Temperature job
  - `hadoop jar mr.jar exercise3.MaxTemperature /bigdata/dataset/weather-sample mapreduce/output`
  - `hdfs dfs -cat mapreduce/output/* | head -n 30`

Try adding a combiner
  - NOTICE: the combiner is implemented as a reducer, but its output must be of the same type of the map output
  - How does the performance improve?

Modify the job to get the **average** temperature in **each month** of each year
  - Which combiner strategy can we adopt (if any)?

### Exercise 4

Compile and run the `AverageWordLength` and `InvertedIndex` jobs on the `capra` and `divinacommedia` datasets
  - NOTICE: in the input key-value pair, the key is usually the offset with respect to the document (not the row number)

HINTS for `AverageWordLength`:
  - Getting the first letter: `substring()`
  - Getting the length: `getLength()`

HINTS for `InvertedIndex`:
  - Map: cast the key to a `LongWritable`
  - Reduce: use a `TreeSet<Long>` and print it with the `toString()` method
  - In case of `java.io.IOException`: Type mismatch :
    - Verify the type of keys and values in `Mapper` and `Reducer` classes
    - Declare the same types in the job object (`setMapOutputKeyClass`, `setOutputKeyClass`, etc.)
