# E1 - Exercises on Hadoop and HDFS File

## Exercise 1 - Setup

Setup your environment with Putty and WinSCP
  - Create a connection to ISI-VCLUST**N**.csr.unibo.it on Putty
    - Where **N** is the number of the node you have been assigned to
  - Connect the credentials
  - Change your password!!
    - Linux command `passwd`
  - Create a directory in your home called *bigdata*
    - Linux command `mkdir`
  - Create a connection to ISI-VCLUST**N**.csr.unibo.it on WinSCP
  - Check the existence of the directory

## Exercise 2 - HDFS commands

Use the following hdfs commands and understand the output
  - `hdfs dfs -df -h`
  - `hdfs dfs -du -h /`
  - `hdfs dfsadmin -report`
  
## Exercise 3 - Moving files around (part 1)

Explore HDFS directories with –ls

```bash
hdfs dfs -ls /
```

Create a bigdata folder in your HDFS home

```bash
hdfs dfs -mkdir bigdata
```

Create a dummy file in your folder in the local file system

```bash
echo 'This is a dummy file' > dummy.txt
```

Put the dummy file to your bigdata folder in HDFS

```bash
hdfs dfs -put bigdata/dummy.txt
```

## Exercise 3 - Moving files around (part 2)

Use Cloudera Manager to check where the file has been put
  - Go to http://137.204.72.233:7180/cmf/home
  - Go to the HDFS service (left panel), then click on NameNome Web UI
  - Go to Utilities, then to Browse the file system
  - Navigate to your folder and click on your file

Change the replication factor of the dummy file to 5

  - ```bash 
    hdfs dfs -setrep -w 5 bigdata/dummy.txt
    ```
  - Verify that the number of replicas has actually increased

Delete the test folder and the dummy.txt file on HDFS
```bash
hdfs dfs –rm -skipTrash bigdata/dummy.txt
```
