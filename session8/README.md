# HDFS, Hadoop and Spark

Original texts by Manuel Parra: manuelparra@decsai.ugr.es. 

With contributions by Carlos Cano: carloscano@ugr.es

Content:

- [HDFS, Hadoop](#workshop-material-for-hdfs--hadoop-and-spark)
  * [How to connect](#how-to-connect)
  * [What is hadoop.ugr.es](#what-is-hadoopugres)
  * [Working with HDFS](#working-with-hdfs)
    + [HDFS basics](#hdfs-basics)
    + [HDFS storage space](#hdfs-storage-space)
    + [Usage HDFS](#usage-hdfs)
  * [Exercice](#exercice)
  * [Working with Hadoop Map-Reduce](#working-with-hadoop-map-reduce)
    + [Structure of M/R code](#structure-of-m-r-code)
      - [Mapper](#mapper)
      - [Reducer](#reducer)
      - [Main](#main)
    + [Word Count example](#word-count-example)
    + [WordCount example file](#wordcount-example-file)
    + [Running Hadoop applications](#running-hadoop-applications)
    + [Results](#results)
    + [Datasets](#datasets)
    + [Calculate MIN of a row in Hadoop](#calculate-min-of-a-row-in-hadoop)
    + [Compile MIN in Hadoop](#compile-min-in-hadoop)
    + [References](#references-)
- [Spark](#sparkr)
  * [How to connect](#how-to-connect-1)
  * [Start R shell for Spark](#start-r-shell-for-spark)
  * [Create the Spark Environment](#create-the-spark-environment)
  * [Close the Spark Session](#close-the-spark-session)
  * [Spark Session parameters](#spark-session-parameters)
  * [Creating SparkDataFrames](#creating-sparkdataframes)
    + [From local data frames](#from-local-data-frames)
    + [From Data Sources](#from-data-sources)
    + [How to read/write from/to hdfs](#how-to-read-write-from-to-hdfs)
  * [SparkDataFrame Operations](#sparkdataframe-operations)
  * [Grouping and Aggregation](#grouping-and-aggregation)
  * [Operating on Columns](#operating-on-columns)
  * [SparkSQL](#sparksql)
  * [Machine learning](#machine-learning)
  * [Let see some examples](#let-see-some-examples)
    + [First example](#first-example)

## How to connect

From linux/MacOs machines: 

```
ssh <your account>@hadoop.ugr.es
```
From Windows machine:

```Use Putty/SSH ``` 

Download link: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html

- Host: ``hadoop.ugr.es``
- Port: ``22``
- Click "Open" -> Write your login credentials and password.

## What is hadoop.ugr.es

Hadoop.ugr.es is a computing infrastructure or cluster with 15 nodes and a header node containing the data processing platforms Hadoop and Spark and their libraries for Data Mining and Machine Learning (Mahout and MLLib). It also has HDFS installed for working with distributed data. 


# Working with HDFS


The Hadoop Distributed File System (HDFS) is a distributed file system designed to run on commodity hardware. It has many similarities with existing distributed file systems. However, the differences from other distributed file systems are significant. HDFS is highly fault-tolerant and is designed to be deployed on low-cost hardware. HDFS provides high throughput access to application data and is suitable for applications that have large data sets. HDFS relaxes a few POSIX requirements to enable streaming access to file system data. HDFS was originally built as infrastructure for the Apache Nutch web search engine project. HDFS is now an Apache Hadoop subproject. The project URL is http://hadoop.apache.org/. This material has been composed from the HDFS reference manual: https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/ . 

![HDFS](http://www.glennklockwood.com/data-intensive/hadoop/hdfs-magic.png)

HDFS has a master/slave architecture. An HDFS cluster consists of a single NameNode, a master server that manages the file system namespace and regulates access to files by clients. In addition, there are a number of DataNodes, usually one per node in the cluster, which manage storage attached to the nodes that they run on. HDFS exposes a file system namespace and allows user data to be stored in files. Internally, a file is split into one or more blocks and these blocks are stored in a set of DataNodes. The NameNode executes file system namespace operations like opening, closing, and renaming files and directories. It also determines the mapping of blocks to DataNodes. The DataNodes are responsible for serving read and write requests from the file system’s clients. The DataNodes also perform block creation, deletion, and replication upon instruction from the NameNode.


![HDFS Arch](https://hadoop.apache.org/docs/r1.2.1/images/hdfsarchitecture.gif)

**Data Replication**

HDFS is designed to reliably store very large files across machines in a large cluster. It stores each file as a sequence of blocks; all blocks in a file except the last block are the same size. The blocks of a file are replicated for fault tolerance. The block size and replication factor are configurable per file. An application can specify the number of replicas of a file. The replication factor can be specified at file creation time and can be changed later. Files in HDFS are write-once and have strictly one writer at any time.


![DataNodes](https://hadoop.apache.org/docs/r1.2.1/images/hdfsdatanodes.gif)




## Connecting to Hadoop Cluster UGR

Log in hadoop ugr server with your credentials:

```
ssh ccsa<DNI>@hadoop...
```
you will also need a password from your teacher. 

## HDFS basics

The management of the files in HDFS works in a different way of the files of the local system. The file system is stored in a special space for HDFS. The directory structure of HDFS is as follows:

```
/tmp     Temp storage
/user    User storage
/usr     Application storage
/var     Logs storage
```

## HDFS storage space

Each user has in HDFS a folder in ``/user/`` with the username, for example for the user with login mcc50600265 in HDFS have:

```
/user/CCSA2223/mcc50600265/
```

Attention!! The HDFS storage space is different from the user's local storage space in hadoop.ugr.es

```
/user/CCSA2223/mcc50600265/  NOT EQUAL /home/mcc506000265/
```

## Usage HDFS

```
hdfs dfs <options>
```

Options are (simplified):

```
-ls         List of files 
-cp         Copy files
-rm         Delete files
-rmdir      Remove folder
-mv         Move files or rename
-cat        Similar to Cat
-mkdir      Create a folder
-tail       Last lines of the file
-get        Get a file from HDFS to local
-put        Put a file from local to HDFS
```

List the content of a HDFS folder:

```
hdfs dfs -ls /user/CCSA2223/ccano
```

Create a test file:

```
echo “HOLA HDFS” > fichero.txt
```

Move the local file ``fichero.txt`` to HDFS:

```
hdfs dfs -put fichero.txt /user/CCSA2223/ccano/.
```

List again your folder:

```
hdfs dfs -ls /user/CCSA2223/ccano
```

Create a folder test`:

```
hdfs dfs -mkdir /user/CCSA2223/ccano/test
```

Move ``fichero.txt`` to test folder:

```
hdfs dfs -mv /user/CCSA2223/ccano/fichero.txt /user/CCSA2223/ccano/test/.
```

Show the content:

```
hdfs dfs -cat /user/CCSA2223/ccano/test/fichero.txt
```

Delete file and folder:

```
hdfs dfs -rm /user/CCSA2223/ccano/test/fichero.txt
```

and 

```
hdfs dfs -rmdir /user/CCSA2223/ccano/test
```

Create two files:

```
echo “HOLA HDFS 1” > f1.txt
```

```
echo “HOLA HDFS 2” > f2.txt
```

Store in HDFS:

```
hdfs dfs -put f1.txt /user/CCSA2223/ccano/.
```

```
hdfs dfs -put f2.txt /user/CCSA2223/ccano/.
```

Cocatenate both files:

```
hdfs dfs -getmerge /user/CCSA2223/ccano/ merged.txt
```

## Exercice

- Create 5 files in yout local account with the following names:
  - part1.dat ,part2.dat, part3.dat, part4.dat, part5.dat
- Copy files to HDFS
- Create the following HDFS folder structure:
  - /test/p1/
  - /train/p1/
  - /train/p2/
- Copy part1 in /test/p1/ and part2 in /train/p2/ 
- Move part3, and part4 to /train/p1/
- Finally merge folder /train/p2 and store as data_merged.txt


# References:

- http://www.glennklockwood.com/data-intensive/hadoop/overview.html


## Working with Hadoop Map-Reduce

Hadoop version: 2.9.3

### Structure of M/R code

#### Mapper

Maps input key/value pairs to a set of intermediate key/value pairs.
Maps are the individual tasks which transform input records into a intermediate records. The transformed intermediate records need not be of the same type as the input records. A given input pair may map to zero or many output pairs.

The Hadoop Map-Reduce framework spawns one map task for each InputSplit generated by the InputFormat for the job. Mapper implementations can access the Configuration for the job via the JobContext.getConfiguration().

```
public class TokenCounterMapper 
     extends Mapper<Object, Text, Text, IntWritable>{
    
   private final static IntWritable one = new IntWritable(1);
   private Text word = new Text();
   
   public void map(Object key, Text value, Context context) throws IOException, InterruptedException {
     StringTokenizer itr = new StringTokenizer(value.toString());
     while (itr.hasMoreTokens()) {
       word.set(itr.nextToken());
       context.write(word, one);
     }
   }
 }
```

#### Reducer

Reduces a set of intermediate values which share a key to a smaller set of values.

Reducer has 3 primary phases:

- Shuffle: The Reducer copies the sorted output from each Mapper using HTTP across the network.
- Sort: The framework merge sorts Reducer inputs by keys (since different Mappers may have output the same key). The shuffle and sort phases occur simultaneously i.e. while outputs are being fetched they are merged. A SecondarySort in ordet to achieve a secondary sort on the values returned by the value iterator, the application should extend the key with the secondary key and define a grouping comparator. The keys will be sorted using the entire key, but will be grouped using the grouping comparator to decide which keys and values are sent in the same call to reduce.The grouping comparator is specified via Job.setGroupingComparatorClass(Class). The sort order is controlled by Job.setSortComparatorClass(Class). 
- Reduce In this phase the reduce(Object, Iterable, org.apache.hadoop.mapreduce.Reducer.Context) method is called for each <key, (collection of values)> in the sorted inputs.

The output of the reduce task is typically written to a RecordWriter via TaskInputOutputContext.write(Object, Object).

```
public class IntSumReducer<Key> extends Reducer<Key,IntWritable,
                                                 Key,IntWritable> {
   private IntWritable result = new IntWritable();
 
   public void reduce(Key key, Iterable<IntWritable> values,
                      Context context) throws IOException, InterruptedException {
     int sum = 0;
     for (IntWritable val : values) {
       sum += val.get();
     }
     result.set(sum);
     context.write(key, result);
   }
 }
 
```

#### Main

Main function considering Map and Reduce objects and additional data for the job.

```
...
    Configuration conf = new Configuration();
    Job job = Job.getInstance(conf, "word count");
	job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
...
```

### Word Count example

Full example of Word Count for Hadoop 2.9.3 :

```
import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCount {

  // Mapper
  public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      }
    }
  }

  // Reducer 
  public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
    }
  }

  // Main
  public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    Job job = Job.getInstance(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
}
```


### WordCount example file

Copy source code of WordCount.java from HDFS to your home:

```
hdfs dfs -get /user/CCSA2223/ccano/WordCount.java /home/<yourID>/WordCount.java
```




### Running Hadoop applications


First, create classes folder:

````
mkdir wordcount_classes
````

Compile WordCount Application (from source code WordCount.java):

```
javac -classpath `yarn classpath` -d wordcount_classes WordCount.java
```

Then, (*pay attention in part / . is separated*) 
```
jar -cvf WordCount.jar -C wordcount_classes / .
```

Finally, the execution template is: 

```
hadoop jar <Application> <MainClassName> <Input in HDFS> <Output in HDFS>
```

**Examples of execution**

*Pay attention: Each run require different output folder*

With a file Oddyssey.txt in /tmp (HDFS):

```
hadoop jar WordCount.jar WordCount /tmp/odyssey.txt /user/mp2019/<yourID>/<folder>/
```

With a text file in your HDFS folder:

```
hadoop jar WordCount.jar WordCount /user/mp2019/lorem.txt  /user/mp2019/<yourID>/<folder>/
```




### Results 

Check output folder with:

```
hdfs dfs -ls /user/mp2019/<yourID>/<folder>
```

Return ...:

```
Found 2 items
-rw-r--r--   2 root mapred          0 2019-05-13 17:23 /user/.../_SUCCESS
-rw-r--r--   2 root mapred       6713 2019-05-13 17:23 /user/.../part-r-00000
```

Show the content of ``part-r-00000``:

```
hdfs dfs -cat /user/mp2019/<yourID>/<folder>/part-r-00000
```


### Datasets

Folder ``/user/mp2019/`` contains several samples of a BigData dataset named ECBDL.

```
Rows    Bytes                         Folder and File
------- ---------                     ----------------
5000    177876    2019-05-13 18:14    /user/mp2019/5000_ECBDL14_10tst.data
20000   711174    2019-05-13 18:13    /user/mp2019/20000_ECBDL14_10tst.data
500000  17683919  2019-05-13 18:14    /user/mp2019/500000_ECBDL14_10tst.data
2897918 102747181 2019-05-13 18:14    /user/mp2019/ECBDL14_10tst.data
```


### Calculate MIN of a row in Hadoop

Mapper (old version):


```
public class MinMapper extends MapReduceBase implements Mapper<LongWritable, Text, Text, DoubleWritable> {


        private static final int MISSING = 9999;
        
        // Numero de la Columna del Dataset de donde vamos a buscar el valor mínimo
        public static int col=5;

		public void map(LongWritable key, Text value, OutputCollector<Text, DoubleWritable> output, Reporter reporter) throws IOException {
                
                // Como el fichero de datos cada columna está separada por el caracter , (coma)
				// Usamos el caracter , (coma) para dividir cada línea del fichero en el map en las columnas
                String line = value.toString();
                String[] parts = line.split(",");

                // Hacemos el collect de la key=1 y el valor de la columna (el valor corresponde con el número de columna
                // indicado anteriormente)
                output.collect(new Text("1"), new DoubleWritable(Double.parseDouble(parts[col])));
        }
}
```


Reducer (old version):

````
public class MinReducer extends MapReduceBase implements Reducer<Text, DoubleWritable, Text, DoubleWritable> {
	
		// Funcion Reduce:		
		public void reduce(Text key, Iterator<DoubleWritable> values, OutputCollector<Text, DoubleWritable> output, Reporter reporter) throws IOException {

		// Para extraer el Minimo usamos de valor incial el máximo de JAVA
		Double minValue = Double.MAX_VALUE;
		
		// Leemos cada tupla  y nos quedamos con el menor valor
		while (values.hasNext()) {
			minValue = Math.min(minValue, values.next().get());
		}
		
		// Hacemos el collect con la key el valor mínimo encontrado en esta fase de reducción
		output.collect(key, new DoubleWritable(minValue));
	}
}
````

Main (old version):

```
  public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    Job job = Job.getInstance(conf, "Min");
    job.setJarByClass(Min.class);
    job.setMapperClass(MinMapper.class);
    job.setCombinerClass(MinReducer.class);
    job.setReducerClass(MinReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
```

### Compile MIN in Hadoop

First, create classes folder:

````
mkdir min_classes
````

Compile Min Application (from source code Min.java):

```
javac -classpath `yarn classpath` -d min_classes Min.java
```

Then, (*pay attention in part / . is separated*) 
```
jar -cvf Min.jar -C min_classes / .
```

Finally, the execution template is: 

```
hadoop jar <Application> <MainClassName> <Input in HDFS> <Output in HDFS>
```

**Examples of execution**

*Pay attention: Each run require different output folder*

With a file Oddyssey.txt in /tmp (HDFS):

```
hadoop jar Min.jar Min /user/mp2019/5000_ECBDL14_10tst.data /user/mp2019/<yourID>/<folder>/
```

Check results:

```
hdfs dfs -ls /user/mp2019/<yourID>/<folder>/
```

Show results:

```
hdfs dfs -cat /user/mp2019/<yourID>/<folder>/part-....
```







# References:

- http://www.glennklockwood.com/data-intensive/hadoop/overview.html



cp /tmp/lorem.txt /home/mp<DNI>/lorem.txt
hdfs dfs -put lorem.txt /user/mp2019/mp<DNI>/
hdfs dfs -put /home/mp<DNI>/lorem.txt /user/mp2019/mp<DNI>/

hdfs dfs -ls /user/mp2019/mp<DNI>/lorem.txt
cat lorem.txt
hdfs dfs -cat /user/mp2019/mp<DNI>/lorem.txt




# SparkR

API SparkR: https://spark.apache.org/docs/2.2.0/api/R/

## How to connect


```
ssh <yourID>@hadoop.ugr.es
```

## Start R shell for Spark

Run the next command:

```
R
```

## Create the Spark Environment


```

if (nchar(Sys.getenv("SPARK_HOME")) < 1) {
  Sys.setenv(SPARK_HOME = "/opt/spark-2.2.0/")
}

library(SparkR, lib.loc = c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib")))

sparkR.session(master = "local[*]", sparkConfig = list(spark.driver.memory = "1g"),enableHiveSupport=FALSE)

```



## Close the Spark Session


```
sparkR.session.stop()
```

## Spark Session parameters

```
Property Name                   Property group          spark-submit equivalent
spark.master                    Application Properties  --master
spark.yarn.keytab               Application Properties  --keytab
spark.yarn.principal            Application Properties  --principal
spark.driver.memory             Application Properties  --driver-memory
spark.driver.extraClassPath     Runtime Environment     --driver-class-path
spark.driver.extraJavaOptions   Runtime Environment     --driver-java-options
spark.driver.extraLibraryPath   Runtime Environment     --driver-library-path

```

## Creating SparkDataFrames

With a SparkSession, applications can create SparkDataFrames from a local R data frame, from a Hive table, or from other data sources.

### From local data frames

The simplest way to create a data frame is to convert a local R data frame into a SparkDataFrame. Specifically we can use as.DataFrame or createDataFrame and pass in the local R data frame to create a SparkDataFrame. As an example, the following creates a SparkDataFrame based using the faithful dataset from R.

```
df <- as.DataFrame(faithful)
```

Show data in df:

```
head(df)
```

### From Data Sources

SparkR supports operating on a variety of data sources through the SparkDataFrame interface.

SparkR supports reading JSON, CSV and Parquet files natively, and through packages available from sources like Third Party Projects, you can find data source connectors for popular file formats like Avro.

```
data1 <- read.df("my_file.json", "json")
data2 <- read.df("my_file.csv", "csv")
...
```

### How to read/write from/to hdfs


Read as DataFrame ``/user/mp2019/5000_ECBDL14_10tst.data``:

```
df5000 <- read.df("hdfs://hadoop-master/user/mp2019/5000_ECBDL14_10tst.data", source="csv")

```

Check data:

```
summary(df5000)
```

Explain the data: From _c0 to _c9 (data), class variable: _c10.

## SparkDataFrame Operations

Create the SparkDataFrame

```
df <- as.DataFrame(faithful)
```

Get basic information about the SparkDataFrame

``
df
``

Select only the "eruptions" column

```
select(df, df$eruptions)
``

Show:

```
head(select(df, df$eruptions))
``


Filter the SparkDataFrame to only retain rows with wait times shorter than 50 mins

```
filter(df, df$waiting < 50)
```

Show first results

```
head(filter(df, df$waiting < 50))
```

## Grouping and Aggregation

SparkR data frames support a number of commonly used functions to aggregate data after grouping. 


We use the `n` operator to count the number of times each waiting time appears

```
head(summarize(groupBy(df, df$waiting), count = n(df$waiting)))
```

## Operating on Columns

SparkR also provides a number of functions that can directly applied to columns for data processing and during aggregation.

```
df$waiting_secs <- df$waiting * 60
```

## SparkSQL

```
df5000 <- read.df("hdfs://hadoop-master/user/mp2019/5000_ECBDL14_10tst.data", source="csv")
```

Check summary:

```
summary(df5000)
```

Convert to SparkSQLObject:

```
createOrReplaceTempView(df5000, "df5000sql")

```

Use the next sentence:

```
results <- sql("SELECT _c0  FROM df5000sql")
```

Check results:

```
head(results)
```


```
results <- sql("SELECT max(_c0)  FROM df5000sql")

```

**Question:**

What is the pair of columns more correlated?

Check SQL functions: https://spark.apache.org/docs/2.3.0/api/sql/index.html

**5 minutes**


**How many records of each class are there?**

```
results <- sql("SELECT count(*),_c10  FROM df5000sql group by _c10")
```

## Machine learning

SparkR supports the following machine learning algorithms currently:

- Classification
  - spark.logit: Logistic Regression
  - spark.mlp: Multilayer Perceptron (MLP)
  - spark.naiveBayes: Naive Bayes
  - spark.svmLinear: Linear Support Vector Machine
- Regression
  - spark.survreg: Accelerated Failure Time (AFT) Survival Model
  - spark.glm or glm: Generalized Linear Model (GLM)
  - spark.isoreg: Isotonic Regression
- Tree
  - spark.gbt: Gradient Boosted Trees for Regression and Classification
  - spark.randomForest: Random Forest for Regression and Classification
- Clustering
  - spark.bisectingKmeans: Bisecting k-means
  - spark.gaussianMixture: Gaussian Mixture Model (GMM)
  - spark.kmeans: K-Means
  - spark.lda: Latent Dirichlet Allocation (LDA)
- Collaborative Filtering
  - spark.als: Alternating Least Squares (ALS)
- Frequent Pattern Mining
  - spark.fpGrowth : FP-growth
- Statistics
  - spark.kstest: Kolmogorov-Smirnov Test

Under the hood, SparkR uses MLlib to train the model. Please refer to the corresponding section of MLlib user guide for example code. Users can call summary to print a summary of the fitted model, predict to make predictions on new data, and write.ml/read.ml to save/load fitted models. SparkR supports a subset of the available R formula operators for model fitting, including ‘~’, ‘.’, ‘:’, ‘+’, and ‘-‘.

## Let see some examples

+Info: https://spark.apache.org/docs/2.2.0/ml-classification-regression.html

Before start, check the columns types:

```
summary(df5000)
```

Result:

```
SparkDataFrame[summary:string, _c0:string, _c1:string, _c2:string, _c3:string, _c4:string, _c5:string, _c6:string, _c7:string, _c8:string, _c9:string, _c10:string]
```

ALL TYPES are STRING :(

Solution: Infer Scheme !

```
df5000 <- read.df("hdfs://hadoop-master/user/mp2019/5000_ECBDL14_10tst.data", source="csv",  inferSchema = "true", header="true")
```

Check again:

```
summary(df5000)
```

### First example

```
training <- df5000
test <- df5000
```

```
model = spark.logit(training, f1 ~ class, maxIter = 10, regParam = 0.3, elasticNetParam = 0.8)

```

See the model:

```
summary(model)
```




