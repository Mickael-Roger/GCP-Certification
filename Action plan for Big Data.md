# Perimeter
 - Datalab
 - Dataproc
   - Hadoop
   - hdfs
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
### Hive
### Pig
### Spark

## Notes


## Scenario
- Create an hadoop cluster
- Put a datafile in hdfs
- Parse it without preemptible VMs
- Parse it with preemptible VMs
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


