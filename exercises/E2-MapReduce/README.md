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
