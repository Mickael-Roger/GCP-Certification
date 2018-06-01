# Perimeter
 - Datalab
 - Dataproc
   - Hadoop
   - hdfs
   - Yarn
   - Hive
   - Pig
   - Spark
 - BigQuery
 - GCS
 - Datastore
 - BigTable*
 - Pub/Sub*
 - Dataflow*
 - Machine Learning*
   - Tensor Flow*
   - ML Engine*
 - Machine learning API : Vision, Speech, Jobs, Translation and Natural Language
 

# Datalab
## Description
Based on Jupyter

Docker image

## Notes


## Scenario
- Deploy Datalab on cloud shell
- Deploy Datalab on Dataproc Master node
- Analyze connexions between Datalab and Master and Worke node in Dataproc (Using python, java, ...)


# Dataproc
## Components
![Hadoop_ecosystem](https://www.safaribooksonline.com/library/view/hadoop-essentials/9781784396688/graphics/3765_02_06.jpg)

### Hadoop
Consists of Haddop common, HDFS, MapReduce and Yarn
### HDFS
#### Architecture

Master / Slave architecture. 

Namenode acted as a master and manage the filesystem and regulates access by clients. It makes all decisions about the replication and maintain the filesystem Metadata (in the file FsImage sotred locally).
Namenode is a SPOF.

Datanode acted as a slave and manage storage attached to the node. Datanode manage block creation, deletion and replication. They send Heartbeat periodically to the Namenode.

Support hierarchical file organization but do not support link.

For replicas placement HDFS cares about the rack the node is in.
Files are not delete immediatly. Deleted files are plaed in /trash and have been removed later (default policy : 6 hours)

#### Command
```bash
hadoop fs -ls /
hdaoop fs -mkdir /test
hadoop fs -cp file /dir/
```


### YARN
#### Architecture
Composed of a global ressource manager (RM) and a per-application master (AM) - two separate daemons

A container in YARN is a gorup of ressources (CPU, RAM, Disk)

- Ressource manager : Arbitrate ressources among all applications in the cluster. 
Additionally, on each node runs a NodeManager. It's responsible for containers, monitoring their ressources usage and reporting to the RM.
- Per-application master : Negociate ressources with the RM and work with the nodemanager to execute and monitore tasks

Ressource manager is composed of 2 components:
- Scheduler : Allocate ressources to various running applications
- Application manager : Accept job submission, negotiate the first container to Applicaiton Master

```bash
yarn application -list
yarn node -list
```

### Hive
#### Definition
It's a datawarehouse built on top of Hadoop MapReduce framework.

It provides:
- Datawarehouse tasks like ETL, data summarization, query and analysis
- Can access files stored in HDFS or other mechanism like HBase
- SQL-like interface called HiveQL
- Command line interface 

Tables and Databases are created first, then data is loaded into them.
Hive engine compiles HiveQL into MapReduce jobs.


### Pig
#### Definition
Provides script capabilities as an alternative to MapReduce java jobs. 

Pig is composed of 2 components:
- Pig Latin : Language to write scripts
- Runtime environnement : Convert script to MapReduce programs


While Hive is for querying data, pig is for preparing data to make it suitable for querying


### Spark
#### Architecture
Spark stack 
![Spark_stack](http://www.mickael-roger.com/tmp/sparkarchitecture.png)

Use a master (driver) / slave (worker) architecture

##### Spark Core
- Distributes workload
- Monitor applications accross the cluster
- Schedule tasks
- Memory management
- Fault recovery
- Interact with storage system
- Houses API that defines RDD

Spark cluster manager supports Standalone, Mesos or Yarn.

Use in-memory storage for RDD as well as disk storage

SparkContext : 
- Main entry point
- Define the main driver program
- Tell spark how and where to access cluster
- Connects to cluster manager
- Coordinates spark processes
- Used to creates RDDs and shares variables accross the cluster

##### Spark API librairies
- Spark SQL : Structured data processing
- Spark streaming : Process livre stream of data
- Spark MLlib : Common Machine Learning functionality
- GraphX : Manipulates and performs graphs
- SparkR : Ligthweight frontend for use spark from R

##### Resilient Distributed Datasets
###### RDD definitions
- RDD is the core data API. 
- DataFrame API : Introduced since Spark 1.3 is more suitable for querying building because it uses a schema to describe data.
- Dataset API : Released with Spark 2.0, it combines the best of both RDD and DataFrame API

RDD is the core concept in Spark. It can be simply described as ditributed collection of read-only elements

There is also a special RDD called : Key Value pair RDD. Can be created by using directly key/value data or by creting it with the map operation. Shuffle (transfert the same key to the same node) works only with Key Value pair RDD.


###### Creation

- Distributiong an existing collection.
```python
lines = sc.parallelize(["Word1", "Word2", "Word3"])
```
- Loading an external dataset 
```python
lines = sc.testFile("MY FILE")
```
- Opeations on existing RDD
```python
newLines = lines.transform(...)
```

###### Operations
- Transformation : Create a new RDD from an existing one. Relationship between transformations are recorded in a linear graph which is a DAG
```python
inputRDD = sc.textFile("input.txt")
errorRDD = inputRDD.filter(lambda x : "error" in x)
warningRDD = inputRDD.filter(lambda x : "warning" in x)
badlinesRDD = errorRDD.union(warningRDD)
```
! At this point, none of the transformation have been executed. Only a DAG (Direct Acyclic Graph) is created. RDD transformation will be executed later when action is called

- Operation : Execute operations on RDD. When action is called, entire RDD is performed from scratch. Best practive is to persist intermediate results.
```python
print "Bas lines : " + badlinesRDD.count()
badlinesRDD.take(10)
```

###### Operations flow
1. Driver : Create RDD and distribute to executor
2. Driver : Lazily evaluates transformation and creates lineage graph
3. Driver : On an action, schedule tasks on the executor
4. Executor : Execute transformation on RDD
5. Executor : Cache RDD if applicable
6. Executor : Perform actions and return results
7. Driver : Run action on result on returned results from executors 



#### Flow of execution
- User submit an application
- Driver program contacts cluster manager to ask for ressources
- Cluster manager launch executors
- Driver program divides the progam into tasks and send them to executors
- Executors run the tasks and return the results to the driver
- At the end of the application, executors are terminated and cluster manager release the ressources



## Notes
Preemptibles VM only function as processing nodes and do not store data for the HDFS cluster

## Scenario
- Create an hadoop cluster
- Put a datafile in hdfs
- Parse it
- Analyze it with Hive
- Analyze it with Pig
- Analyze it with spark, pyspark and sparksql
- Split and store a file in GCS
- Process it in parralel



# BigQuery
## Architecture
- A project (tenant) is ocmposed of one or more Dataset.
- A dataset (ACL for reader/writer/owner at this level) contains one or more table
- A table is a collection of columns

It's a colum storage (Each column is stored i a separate, compressed and encrypted file)

## Advanced SQL processing
- Subqueries is supported:
```SQL
SELECT
   xxx
FROM
   (SELECT yy FROM ....)
WHERE
   ccc
```

- Multitable queries are supported
```SQL
SELECT
   xxx
FROM
   Table1,
   Table2,
   Table3
```
Even with a Wildcard
```SQL
SELECT
   xxx
FROM
   TABLE_DATE_RANGE(myproject:applogs.events_, TIMESTAMP(2018-01-01), TIMESTAMP(2018-03-30))
```

- Join on multiple tables are supported

- WITH, ARRAY and STRUCT are supported
```SQL
WITH res1 AS (
SELECT x, y, z ...
)
SELECT a,
  ARRAY( SELECT AS STRUCT c, d FROM ...)
FROM res1
```


- UNNEST : The UNNEST operator takes an ARRAY and returns a table, with one row for each element in the ARRAY
    - Example


| Name | email | array_of_x |
| ---- | ----- | ---------- |
| per1 | mail1 | blablabla1 |
|      |       | blablabla2 |
      
      
Become


| Name | email | array_of_x |
| ---- | ----- | ---------- |
| per1 | mail1 | blablabla1 |
| per1 | mail1 | blablabla2 |

    
    
    
```SQL
SELECT
  author.email,
  diff.new_path AS path,
  author.date
FROM
  `bigquery-public-data.github_repos.commits`,
  UNNEST(difference) diff
WHERE
  EXTRACT(YEAR
  FROM
    author.date)=2016
LIMIT 10
```
- Replace author.email by author and give all author's information : author.name, author.email, author.address, ...
- diff.new_path can be replace by difference[OFFSET(0)].new_path -> because difference is an array inside a row



## Storage and loading data
Data used by Bigquery can be stored in:
- BigQuery storage (table) using
  - bq command line
  - WebUI
  - API
- GCS
  - gsutil

## Pricing and optimization
Price is based on :
 - Storage
     - Amount of data in table
     - Ingest rate of streaming data
     - Automatic discount for old data
 - Processing (1TB/month for free)
 - Free operations
     - Loading
     - Exporting
     - Query on metadata
     - Queriy in error
     - Cached query (! per user cache)

Optimization through
- Dont' process unnecessary columns
- Do biggest JOIN first
- Built-in function are faster than javacript UDF (use APPROX function if possible - example : use APPROX_COUNT_DISTINCT instead of COUNT)


## Partitioning
2 ways :
- Use wildcard table : FROM table_*
- Use partitioned table : --time_partitioning_type (--time_partitioning_expiration to delete old data)



## Commands
- bq load --source_format=NEWLINE_DELIMITED_JSON air_dataset.new_tbl gs://xxx/yy*.json schema.json



# GCS
## Description
Object storage :
- Multi regional
- Regional
- Nearline
- Coldline


## Notes
Manage ACL rights: 
```console
gsutil acl ch -u AllUsers:R gs://my-awesome-bucket/kitten.png   # Grant read access to AllUsers
gsutil acl ch -d AllUsers gs://my-awesome-bucket/kitten.png     # Remove theses accesses
```

## Scenario
- Create a bucket
- Put file in the bucket
- Manage acl of a file


# Datastore
## Description
NoSQL managed service build upon Bigtablee and Megastore

- Support SQL like queries
- RESTFul API
- Transaction and Indexes
- Strong consistency and eventual consistency

Structure

| Concept 	                     | Cloud Datastore | Relational database |
| ----------------------------- | --------------- | ------------------- |
| Category of object 	          | Kind 	          | Table               |
| One object 	                  | Entity 	        | Row                 |
| Individual data for an object |	Property 	      | Field               |
| Unique ID for an object 	     | Key 	Primary    | key                 |

### Consistency
- Global queries : Eventual consistent
- Key queries : Strong consistent
- Ancestor queries : Strong consistent

Only one write per second is recommanded inside each entity group. By default each entity belongs to its own entity group


## Notes
### Entity group creation
Create an entity (For example a company). Declare the entity reference as the parent of all entities you want to group inside this entity group.


## Scenario
- Create 4 entity group (For 4 differents companies) in a company kind
- Create tens of users in each entity group
- Create thousands of users linked to no entity group



# Machine Learning : API
## Description
- Vision : Analyze pictures. Multiple features (text_detection, landmark_detection, web_detection, ...)
- Text to speech
- Speech : Analyze audio (gRPC used for streaming)
- Translation : Translate text (Translate or detect)
- Natural Language : Analyze text


## Notes
API access for each componennt has to be activated. Use JSON for response

Accessible through gcloud : ex : gcloud ml vision detect-text gs://img.jpg

Or REST API : curl -v -s -H "Content-Type: application/json" https://vision.googleapis.com/v1/images:annotate?key=API_KEY --data-binary @req.json

```json
{
  "requests": [
    {
      "image": {
        "source": {
          "gcsImageUri": "gs://cpb100-201619/tmp/speech.jpg"
        }
      },
      "features": [
        {
          "type": "TEXT_DETECTION"
        }
      ]
    }
  ]
}
```

## Scenario
- Authorize access to API
- Analyze a picture
- Translate a text
- Translate an audio
- Translate an audio stream
- Analyze a text



# DataFlow
## Architecture
Based on Apache Beam. It's a unified model for defining both stream and batch data-parallel processing pipelines.

The programm that defines the pipeline is built on of the supported beam SDK (mainly Java and Python).

Then, the pipeline is executed by a distributed processing backends (Apex, Spark, Flink or DataFlow). Dataflow is the execution environnment on GCP.

It's particularly efficient for data processing task in which the problem can be split into many smaller bundle of data that can be processed independently and in parallel. Beam can be used as an ETL (Extract Transform and Load) and pure data integration.


## Usage
For streaming data, you have to applying a sliding window (for group, average, ... for instance)

A pipeline is a direct graph of steps.

Use PCollection - Each step generates a PCollection

Prefer combine over GroupByKey

### Java

```java
Pipeline p = Pipeline.create(PipelineOptionsFactory.fromAgrs(arg))

p.apply(TestIO.Read.from("gs://..."))
 .apply(new CountWords())
 .ParDo.of(...
 .apply(TestIO.Write.to("gs://..."));
 
p.run();
```

### Python

- For 1 to 1 relation between input and output : Map function
- For non 1 to 1 relation : FlatMap function

```python
p = beam.Pipeline(argv=sys.argv)

(p
   | 'GetLine' >> beam.io.ReadFromText(input)
   | 'Grep' >> beam.FlatMap(lambda line: my_grep(line, searchTerm) )
   | 'write' >> beam.io.WriteToText(output_prefix)
)
```



