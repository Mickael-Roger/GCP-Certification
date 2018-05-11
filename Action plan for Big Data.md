# Perimeter
 - Datalab
 - Dataproc
   - Hadoop
   - hdfs
   - Yarn
   - Hive
   - Pig
   - Spark
 - BigQuery*
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

### Hive
### Pig
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


