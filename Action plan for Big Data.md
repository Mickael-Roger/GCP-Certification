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
 - Spanner
 - BigTable
 - Pub/Sub
 - Dataprep
 - Data Studio
 - Dataflow
 - Machine Learning
   - Tensor Flow
   - ML Engine
 - Machine learning API : Vision, Speech, Jobs, Translation and Natural Language


---


# Datalab
## Description
Based on Jupyter

Docker image

## Command

On cloud shell : `datalab create dataengvm --zone <ZONE>`


---


# Dataproc
## Components
![Hadoop_ecosystem](https://www.safaribooksonline.com/library/view/hadoop-essentials/9781784396688/graphics/3765_02_06.jpg)

### Hadoop
Consists of Hadoop common, HDFS, MapReduce and Yarn

### HDFS
#### Architecture

Master / Slave architecture.

Namenode acted as a master and manage the filesystem and regulates access by clients. It makes all decisions about the replication and maintain the filesystem Metadata (in the file FsImage stored locally).
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

A container in YARN is a group of ressources (CPU, RAM, Disk)

- Ressource manager : Arbitrate ressources among all applications in the cluster.
Additionally, on each node runs a NodeManager. It's responsible for containers, monitoring their ressources usage and reporting to the RM.
- Per-application master : Negociate ressources with the RM and work with the nodemanager to execute and monitore tasks

Ressource manager is composed of 2 components:

- Scheduler : Allocate ressources to various running applications
- Application manager : Accept job submission, negotiate the first container to Application Master

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

- **Pig Latin** : Language to write scripts
- **Runtime environnement** : Convert script to MapReduce programs


While Hive is for querying data, pig is for preparing data to make it suitable for querying


### Spark
#### Architecture

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
- Spark streaming : Process live stream of data
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

- Distributing an existing collection.
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
- **Transformation** : Create a new RDD from an existing one. Relationship between transformations are recorded in a linear graph which is a DAG
```python
inputRDD = sc.textFile("input.txt")
errorRDD = inputRDD.filter(lambda x : "error" in x)
warningRDD = inputRDD.filter(lambda x : "warning" in x)
badlinesRDD = errorRDD.union(warningRDD)
```
**! At this point, none of the transformation have been executed. Only a DAG (Direct Acyclic Graph) is created. RDD transformation will be executed later when action is called**

- **Operation** : Execute operations on RDD. When action is called, entire RDD is performed from scratch. Best practive is to persist intermediate results.
```python
print "Bad lines : " + badlinesRDD.count()
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

## Command
Dataproc custom machine : `--master-machine type OR --worker-machine-type custom-6-30720` -> 6vCPU, 30Go RAM

## IAM
- Project level only (primitive and predefined roles)
- Roles :
   - Editor : Full access. Create/Delete/Edit Clusters/Jobs/Workflows
   - Viewer : View access
   - Worker : Assigned to service account (R/W GCS, Write to cloud Logging)


## Migrating HADOOP on premise to Google Cloud

Objectives : Separate Data from Compute and consider clusters as ephemeral

- Move Data from HDFS to GCS
   - Possible exception for HBase to BigTable and Impala to BigQuery
- Move a small set of dat to experiment

- HDFS -> GCS
- Hive -> BigQuery
- HBase -> BigTable

Converting from HDFS to GCS

- Copy data to GCS (Manualy or through a connector)
- Update file prefix in script (hdfs:// to gs://)
- Use Dataproc to run against/output to GCS

End of goal could be to migrate to serverless architecture (DataFlow, BigQuery, ...)


## Notes
Preemptibles VM only function as processing nodes and do not store data for the HDFS cluster
When creating a cluster, create a specific bucket if not specified
Access through the Web Console : Port 8088 for Hadoop and 9870 for HDFS



---



# BigQuery
## Architecture
- A project (tenant) is composed of one or more Dataset.
- A dataset (ACL for reader/writer/owner at this level) contains one or more table
- A table is a collection of columns

It's a colum storage (Each column is stored i a separate, compressed and encrypted file)

## Streaming analytics

It provides streaming ingest to unbounded datasets

   - Carry out SQL even when the data continues to be steamed in

-> Best practices is to combined Dataflow and BigQuery
   - Dataflow for processing that is always done on data
   - BigQuery for ad hoc queries, dashboards, ...
      - Use views for common query support

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
     - Query in error
     - Cached query (! per user cache)

Optimization through

- Don't process unnecessary columns
- Do biggest JOIN first
- Built-in function are faster than javacript UDF (use APPROX function if possible - example : use APPROX_COUNT_DISTINCT instead of COUNT)


## Partitioning
2 ways :

- Use wildcard table : FROM table_*
- Use partitioned table : --time_partitioning_type (--time_partitioning_expiration to delete old data)



## Commands
- `bq load --source_format=NEWLINE_DELIMITED_JSON air_dataset.new_tbl gs://xxx/yy*.json schema.json`


---



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


---



# Datastore
## Description
NoSQL managed service build upon Bigtable and Megastore

DATASTORE IS A TRANSACTIONNAL DATABASE

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

## IAM
- Primitive and predefined
- Owner, user, viewer, import/export admin, index admin



---



# Spanner

## Architecture
SQL compatible Database that horizontaly scale
Strong consistency ACID transaction

## IAM
- Project, instance or Database level
- Admin : Full access to Spanner ressources
- Database Admin : Create/Edit/Delete/Grant Database
- Database Reader : Read / Execute
- Viewer : View Instance and Database (Cannot read or modify Database)


---




# BigTable
## Architecture
Used for huge dataset (petabytes of data)
High throughtput analics

![BigTable](http://www.mickael-roger.com/tmp/bigtable.png)

- Front-end server serves client request request to node
- Node process the request. No data is stored on node except the metadata to request the correct tablet
- SSTable : Tablets (BigTable's table sharded into block of rows)

Storage is separated from from the compute node , though each tablet is associated with a node -> Replication and recovery of node data is very fast as only metadata/pointers need to be updated

Not no-ops

Entire BigTable project is called 'instance' -> All nodes and clusters
Nodes grouped into cluster -> 1 or more cluster per instance

Auto-scaling storage

2 types of cluster:

   - Development : single node -> No replication
   - Production : 3+ nodes

Online with no downtime : Cluster resizing (number of nodes)

Interacting with BigTable

- cbt command-line tool
- HBase shell

## Data organisation

Organisation

- One big table
- Can contain thousands of columns and billions of rows
- Table is sharded across tablets

Table components

- Row key
- Columns grouped into column families

![BigTable_data](http://www.mickael-roger.com/tmp/bigtable_data.png)

Indexing and queries

- Only the row key is indexed
- Schema design is necessary for efficient queries
- Field promotion - Move fields from column data to row key


## Schema design

Design best practices

- Keep all entity info in a single row
- Related entities should be in adjacent rows
- Empty column takes no space

Schema efficiency

- Well define row key 
- Multiple values in a row key
- Row key (or prefix) should be sufficient for search
- Goal : Spread load over multiple nodes

Row key best practices

- Good row keys : Distributed load

   - Reverse domain name (com.google.cloud.xxx)
   - String identifier
   - Timestamps reverse (20180725031245) and not a front or only identifier
   
- Bad row keys : hotspotting

   - Domain name (www.free.fr)
   - Sequential ID
   - Timestamp alone or at front



## Command line

- configure cbtrc file
```bash
echo -e "project = [PROJECT_ID]\ninstance = [INSTANCE_ID]" > ~/.cbtrc
```

- cbt createtable table_name
- cbt ls : List tables
- cbt ls table_name : List columns
- cbt deletetable table_name
- cbt read table_name : Read content of the table


## IAM
Project Wide or Instance level
Read/Write/Manage


---

# Pub/Sub

It's a global, multi-tenanted, manage and real time message service.

## Architecture

A message in Pub/Sub subsists for 7 days

Works with topics and subscriptions

It's possible to have multiple subscribers share the work of processing the messages in a single subscription

Delivery garanty :

- A subscriber ACK each message for every subscription
- A message is resent if subscriber takes more than "ackDeadline" to respond
- A subscriber can extend the deadline per message

Pub/Sub deliver at least once, Dataflow deduplicate, order and window -> Work well together

It's a global service where a single namespace is shared around the world :

- The message is store to the closest region of the publisher

Offer push and pull to subscribers

Metadata can be passed to the message with a form of a key-value pair (Ofen used for passing the timestamp of the message)

Message can be publish as batch. Many message can be sent grouped -> Good idea if there are lots of message to send


## PULL vs PUSH

- PULL : Subscribers make API call periodically
- PUSH : Endpoints can only be HTTPs servers accepting webhook


## Commands

- Create a topic : `gcloud pubsub topics create NAME`
- Publish : `gcloud pubsub topics publish NAME --message "MESSAGE"`
- Create a subscription : `gcloud pubsub subscriptions create --topic TOPIC_NAME SUBSCRIPTION_NAME`
- Get message : `gcloud pubsub subscriptions pull --auto-ack SUBSCRIPTION_NAME`


## IAM
- Control access at project, topic or subscription level
- Admin / Editor / Publisher / Subscriber

---


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



---

# Dataprep

A Web tool based on Dataflow to prepare/clean data.

-> Cost : 1.16 times the Dataflow cost

## IAM

- Dataprep user : Run Dataprep in a project
- Dataprep service agent need
   - Access to GCS BUCKET
   - Dataflow developper
   - Big Query user/data editor
   - GCE service account

---

# Data Studio

Tool to build dashboards and report. Dashboards can be shared with other people that can collaborate on it (Like google doc)

Can use lots of GCP services for data : GCS, BigTable, Big Query, Cloud SQL, ...

Part of G Suite -> Do not use IAM, ... but use google drive

Use 2 types of cache :

- Query cache : Cannot be turned off
- Prefetch cache (Smart cache -> Predict what might be requested) : Can be turn off (If the need to see fresh data from rapidely changing data is important)

---

# DataFlow
It's a fully managed, auto-scaling execution environnment for Beam pipelines

## Architecture
Based on Apache Beam. It's a unified model for defining both stream and batch data-parallel processing pipelines.

The programm that defines the pipeline is built on of the supported beam SDK (mainly Java and Python).

Use PCollection - All steps generate a PCollection

Then, the pipeline is executed by a distributed processing backends (Apex, Spark, Flink or DataFlow). Dataflow is the execution environnment on GCP.

It's particularly efficient for data processing task in which the problem can be split into many smaller bundle of data that can be processed independently and in parallel. Beam can be used as an ETL (Extract Transform and Load) and pure data integration.

## Key concept
- **Element** - Single entry of data
- **PCollection** - Distributed dataset. Input and output data
- **PTransform** - Data processing operation
- **ParDo** - Type of transform applied to individual element

### Dealing with late/out of order data
Use 3 concepts:

- Window : Logically divide elements group by time span
   - Based on timestamp (for streaming : time that message was published on the topic or parsed from message)
   - A window can be
      - Fixed : Ex. 9:00 to 9:10
      - Sliding : Last 10 minutes
      - Session : Based on event. Ex. : Mesuring rainfall. Start when rain starts and finish when rain stop
- Watermarks : Timestamp (Event Time - When data is generated or Processing Time - When data is processed)
- Trigger : Late arrived data are reagregate with previously submitted results

## Usage
For streaming data, you have to applying a sliding window (for group, average, ... for instance)

A pipeline is a direct graph of steps.

### Map Reduce Dataflow

ParDo - Act of Map in Map Reduce and use parallelization. Useful for : Filtering, converting, extracting parts of an input, calculating value from different parts of an input

For python only :

- For 1 to 1 relation between input and output : Map function
- For non 1 to 1 relation : FlatMap function

Prefer combine over GroupByKey because GroupByKey explicitly create a key-pair value.
Combine contains many pre-built in functions like sum, mean, ... and can be also applied to a grouped Key-Value pair

### In memory object
Static in memory Data to be used by DataFlow

### Side input
Convert another PCollection to a view and use it as a second PCollection to be conbime with your currently PCollection.

#### Streaming
Use sliding Window and duration. For example : Duration : 2mn and every : 30s

Subsequent groups, aggregations, ... are only compute on Window

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

```python
p = beam.Pipeline(argv=sys.argv)

(p
   | 'GetLine' >> beam.io.ReadFromText(input)
   | 'Grep' >> beam.FlatMap(lambda line: my_grep(line, searchTerm) )
   | 'write' >> beam.io.WriteToText(output_prefix)
)
```

Use DataFlow runner

```bash
python -m my_script_without_extension \
  --project $PROJECT \
  --runner DataflowRunner \
  --staging_location $BUCKET/staging \
  --temp_location $BUCKET/temp \
  --output $BUCKET/results/output
```

## IAM
Project-level only (all pipelines or none)

- Dataflow Admin : Full pipeline access + machine type/storage bucket access
- Dataflow Developper : Full pipeline access
- Dataflow Viewer : View only
- Dataflow worker : Specifically for service accounts

## Dataflow vs Spark vs HADOOP
![Dataflow_comparison](http://mickael-roger.com/tmp/dataflow.png)



---



# Machine Learning

2 types:

- Supervised Learning : Use Labels
- Unsupervised Learning : Data is not labeled

On supervised Learning there are 2 types of models :

- Regression model : When label is a continous number (for exemple predict the weigth of a baby or the tip in a restaurant)
- Classification model : When label is a class (for example predict the sex of a Baby)

Everything that goes to the ML Model has to be numeric

- Images are converted to tensor (red, green, blue)
- Text are converted to tensor using a number for each word. Many, plenty and much for example has to have a number relatively close and very far from car. Theses models for text has already been done
- Numeric values has to have a relation between them : For example customer_id can't be used because customer 2 is not twice more something than customer 1

## Tensor
- 1-Dimension array is a vector
- 2-Dimension array is a matrix
- n-Dimension array is a Tensor

## ML Terms
- **Weights** : Parameters we try to optimize
- **Batch size** : Part of the dataset we use to calculate the result of a new weight
- **Epoch** : One pass through the entire Dataset. For example if the Dataset is 100k and the batch is 1k, the epoch consists of 100 batches or steps
- **Gradient descent** : The process of increasing or decreasing the weight to reduce the errors
- **Evaluation** : Step where we define if the model is good enough
- **Training** : Process of optimizing the weight and evaluate the model

- **Neuron** : One unit of combining inputs
- **Hidden layer** : Set of neurons that operate on the same set of inputs
- **Inputs** :What you feed in a neuron
- **Features** : Transformation of a neuron. For instance x^2

## Dataset

A good Dataset cover all cases and have negative examples and near-misses

Dataset has to be split in :

- Training data
- Validation data
- "Test Data"

![Dataset_Usage](http://www.mickael-roger.com/tmp/dataset.png)


## Error metrics
Get all the errors, square each error value and calculate the mean (avg) value and you get : MSE (Mean Squared Error)

MSE is used for regression model
Cross entropy is used for a classification problem

## Accuracy : Used for a balanced Dataset

To simply define the accuracy, we use a confusion matrix


|           | ML Says: Cat   | ML Says: Not Cat    |
| -------------- | -------------- | -------------- |
|  Truth: Cat      | True positive  | False Negative |
|  Truth: Not Cat  | False positive | True Negative  |



Accuracy is the fraction of correct prediction (True positive and True Negative) divide by all predictions

Accuracy fails if dataset unbalanced. For example : A parking lot with 1000 parking space has only 10 free. The ML model detect only one of the free space but accuracy is 0.991 because it detects all used parking space

## Precision and Recall : Used for an unbalanced Dataset
### Precision

Precision = Accurracy when classifier say "yes"

Precision = Positive predictive value. For example on 5 images (TP + FP = 5), there are only 2 TP.
Precision = TP / (TP + FP) = 2/5 = 40%

### Recall

Recall : Accuracy when the truth is yes

Recall = Fraction of ML finds = TP / (TP + FN )


## TensorFlow

It's an opensource library for numerical computation that uses directed graphs

TF generate a directed graph that represent the computation
Nodes represent mathematical operations (like adding, ...)
Edges connect the nodes

The python API lets build and run directed graph

Build Example -> Only create a directed graph
```python
c = tf.add(a,b)
```

Run Example -> Execute the directed graph
```python
session = tf.Session()
numpy_c = session.run(c, feed_dict=...)
```

TensorFlow does lazy evaluation : You need to run the graph to have the result.
Example:

Build
```python
a = tf.constant([5, 3, 8])
b = tf.constant([3, -1, 2])
c = tf.add(a, b)
print c
```

Result -> Only the directed graph
```
Tensor("Add_7:0", shape=(3,), dtype=int32)
```

Run
```python
with tf.Session() as sess:
   result = sess.run(c)
   print result
```
Result
```
[8, 2, 10]
```

### Estimator API : tf.estimator

Estimator APi : High level "out of the box" API does distributed training

Set up machine learning model : Regression or classification problem; labels; features; ...
Step to define an estimator APi model :

1/ Set up features columns
```python
feat_cols = [tf.feature_column.umeric_column("sq_footage")]
```
2/ Create the model passing in the feature columns
```python
model = tf.estimator.LinearRegressor(featcols, '.file-model-saved')
```
3/ Write input_fn returns features, labels
```python
def train_input_fn():
   features = {"sq_footage": tf.constant([100, 200])}
   labels = tf.constant([50, 100])
   return features, labels
```
4/ Train the model
```python
model.train(train_input_fn, steps = 100)
```
5/ Use train model to predict
```python
def pred_input_fn():
   features = {"sq_footage": tf.constant([150, 180])}
   return features
out = trained.predict(pred_input_fn)
```

#### Different types of regressors

- LinearRegressor
- DNNRegressor : Deep Neureal Network
```python
model = DNNRegressor(feature_columns, hidden_units=[128, 64, 32])       # 5 layers : Input, 128 nodes, 64 nodes, 32 nodes, output
```
- LinearClassifier
- DNNClassifier


### Improving ML
#### Use Big Dataset
Use a function to read shared CSV files instead of using a small set of data that can stand on the memory

```python
dataset = tf.data.TextLineDataset(filnames).map(decode_csv)   #decode_csv is our own function to decode csv file and define used features, labels, ...
```
##### Distributing
For distributed training, it's important to shuffle the data

For distributed operation :

- Distribute the graph
- Share variables
- Evaluate every once in a while
- Handle machine failure
- Create checkpoint files
- Recover from failure
- Save summaries for tensorboard

Use the graphical interface tensorboard to monitor trainning

#### Features engineering
##### Good features
Good features are :

- Related to the objective
- Should be known at production time
   - Temporality of the data is very important
- Has to be numeric with meaningful magnitude
   - For instance employee_id has to be define not has a number but has to be converted has a categorical value
- Has enough examples for each category
   - For example with the credit card fraud detection, you must have lots of no fraud AND fraud transaction samples
- Brings human insight to problem

Bucketizing : Group values in a bucket. For example for house price prediction, do not use the latitude and longitude value but group them in a kind of matrice

#### Model architecture
##### Linear Model
Works very well for sparses features (like categorical employee ID)

##### Deep Neural Network
Works very well for tensors like pictures

##### Wide and Deep Model
Can be used both together because in reality some data are sparsed and some are not
```python
tf.estimator.DNNLinearCombinedClassifier(...)
```

##### Where to do features engineering
- In tensorflow : Advantage : Are made in the same way in both training and serving
- In Dataflow : Ideal for features with time window aggregation
- In a pre processing job : Ideal for scaling preprocessing and use metadata


#### Hyper paramter tuning
Learning rate, batch size, number of bucket, ... are very important to have a good result


### Cloud ML Engine

Manage scalability for training, hyper parameter tuning and even the deployement of the model to give an API access to it

Cloud ML supports batch and online prediction

Steps :

- Use tensorflow to create computation graph and training application
- Package your trainer application
   - task.py : To parse command line parameter and send along to train_and_evaluate
   - model.py : Contains the ML Model in tensorflow (Estimator API)
   - __init__.py : Needed by python (package)
   - Use gcloud command to summit the job locally or to the cloud
- Configure and start Cloud ML jobs
   - Use feature crossing (group features together)
