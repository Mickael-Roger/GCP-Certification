 # Organization and project
 ## Hierarchy

1. Organization node (example.com)    -> Not available to free account
2. Folders -> Not available to personnal account. Organize projects into groups
3. Projects (example-test; example-dev; example-prod)
4. Ressources

Like file system, each ressource can only have one and only one project attached to and a project is attached to only one organization

## Folders

Group of projects that share common IAM policies
Role granted to folders apply to ressources inside
Managed in : IAM & Admin - Manage Ressources

## Projects

There are the core of GCP.
Identified by 3 ways :
- Project Name ("My super project" for instance)
- Project ID (Unique on all google cloud plateform)
- Project Number : Not directly used

Project is really deleted 30 days after the deletion has been submitted

## Quotas

3 types of quotas:
- Ressources per project
- API rate limit per project
- Per region

Managed through support ticket or self service form (In the console -> Summit a request ticket to GCP support team)
Can be viewed in console : IAM & Admin - Quotas

## Labels
Almost any ressources can be labeled
Up to 64 labels per ressource

A label is a key/value pair. Example : env:prod; owner:matt, tier:front; tier:middle; state:unused

Different from tags that is used only for network and impact ressources operations.


# IAM
## Account

2 types : personal account / service account
Service account is identified with an email address : project_id@developper.gserviceaccount.com

## Role and permissions
### Permission

Is formatted by <service.ressource.verb>
Exemple : compute.instances.delete

### Roles
User is assigned to role that is a collection of permissions
Example : user is assigned to compute.instance admin. This role is formed by differents permissions like compute.instances.get, compute.instances.delete, ...

2 types of roles : primitives / predefined

#### Primitive role
Applied to project level : Viewer / Editor / Owner

#### Predefined role (and custom role)
Defined at ressource level

### IAM Policy
Full list of roles granted to a member or a ressource (Organization, folders, projects, services)

### Policy hierarchy
Order : Organization (example.com) -> Project (example-dev) -> Ressources (Compute Engine)
Children inherit parent role /!\

You can have a more permissive parent role and a more restrictive child role


# Accessing gcloud
3 ways :
 - gcloud SDK / cloud shell
    - cloud shell : only $HOME (5GB) is persistent
 - GCP Console (Web interface)
 - RestFUL API

# Compute Engine
## Compute options
 - Compute Engine : instances
 - Container Enginer : Run container powered by Kubernetes
 - App Engine : PaaS
 - Cloud Functions : (Beta)

## gcloud sample
gcloud compute instances create --project "gcp-linuxacademy" "instance-2" --zone "us-east1-b" --machine-type "n1-standard-1" --subnet "default" --no-scopes --tags "http-server" --image "centos-7-v20171213" --image-project "centos-cloud" --boot-disk-size "10"


## Snapshots
Not shareable between projects
Snapshots work as differential backup.
 - First snapshot is a full snapshot
 - Second is differential from the full
 - Third is differential from the second
 - ...

If a snapshot is destroyed, link is not broken, the next snapshot is modified to included data differences between the previous one

## Preemptible VM
Max life of 24h

## Instances group
Group of instances. Can be managed or unmanaged
Managed groups
 - Auto scaling
 - Work with LB
 - If an instance crashes, it is auto recreated

Can be mono or multi zones (but not multi regions)
Auto scaling can be based on : CPU usage, HTTP LB usage, Stackdriver metrics or multi criteria

## SSH key management
Global SSH key management is in the Compute/Meta Data menu for console or through gcloud cli

# Network
## VPC
A VPC can exist in many regions
Virtual Private Cloud Network. Works as a physical network
Each VPC has its own managed firewall
It's possible to manage network routes
Each VPC contain one or many subnet. Can be configured automatic or custom. Only automatic (Automatic allocated subnet) can be convert to custom.

## Subnet
One subnet can only exist in one region (but in all zones of this region)

## External IP adresses
Can be ephemeral (Change every time the instance is restarted) or static (Reserved and attached to an instance)

## VPN and routers
VPN IPSec point to point are supported
Routers can be implemented

## Firewall
By default everything is blocked. Think to add firewall rules to new VPC or everything gonna be blocked!
Firewall act like Security Groups and Firewall

# App Engine
Is the PAAS solution of GCP
Provides managed : Firewalls, DOS, viruses, patch, network, Failover, LB, capacity planning, security, ...
New app Engine can only be deployed through gcloud cli

Exists in 2 versions : Standard and Flexible

## Standard
Can be used with Java, Python, PHP and Go
Can be auto-scaled to 0
Cannot write on local filesystems or modify the runtime environnement
Charges on instance hours (how often it's used)

## Flexible
Based on compute Engine
Auto scale up and down
Native for Java, Python, NodeJs, Ruby, .Net, ... or provide our own runtime
Charged by CPU, memory and disk usage

### App deployment
Need 3 files :
- app.yml contains deployment configuration and is used by gcloud app deploy
- config.py contains the application configuration (Storage, Database, ...)
- main.py imports code and loads configuration data


### Requirements installation
Edit requirements.txt file to insert required dependencies
For python : use pip install -r requirements.txt -t lib (install all dependencies into the lib directory)

### Deploy
In the project directory, use : gcloud app deploy
To list all app deployed : gcloud app instances list

### Mutliple versions
Traffic can be split through multiple versions of the same app. It is configured through the version menu (split traffic) and can be based on IP addresses, cookie or random

# Google Cloud Endpoints
Create, deploy, monitor, protect, analyze and serve our API

# Google Cloud Storage
5 types of storage
- BigTable
- Datastore
- Storage
- SQL
- Spanner

## Database breakdown
- SQL : SQL
- NoSQL : Datastore or BigTable (NoSQL)
- New Category : Spanner

## SQL
Host MySQL or PostgreSQL instance
vertical scale on read/write
horizontal scale on read only

Create an instance and price is the same than compute engine
Cannot connect through ssh to the instance but connection to mysql cli is possible through the gcloud cli :
 - gcloud sql connect SQL_INSTANCE_NAME --user=root

## Datastore
NoSQL but with some SQL aspects
Scale from 0 to terabytes of data
Cost efficient
Support ACID transaction

## Bigtable
Terabytes to petabytes of data
Apache HBase is born from Bigtable
Pricier than Datastore and charged whether using it or not (At least 3 instances except for developpement type which use 1 instance)
Use HBase Shell or scripts (google provides sample samples) to interact with HBase

## Cloud Spanner
Relational database like SQL but with horizontal scale


## Cloud Storage
Object storage
Integrated with:
- Compute engine : startup scripts, images and oject storage
- SQL : Import and export tables
- BigQuery : Import and export tables
- App Engine : Object storage, logs, Datastore backups

Pay per usage
Data encrypted in transit and at rest

Organisation
Bucket : A basic container (Buckets cannot be nested)
For performance, it's better to have fewer buckets and more objects in each bucket
Bucket name must be unique in all GCP Platform
Objects : Can be up to 5TB. Stored in a bucket

A bucket can have one of theses storage class :
- Multi regional : Geo-redundant (inside a continent - Europe, Asia or US)
- Regional : Redondant inside a geographical region
- Nearline : Used to store rarely accessed document (less than once a month)
- Coldline : Used to store very rarely accessed document (less than a year)
Each storage class has the same throughput, latency and durability. Differences are from the availability (From 99% to 99.95%) and pricing for storage and access

Use gsutil command line
- Create : gsutil mb  gs://BUCKET_NAME
- Change rights : gsutil defacl ch -u AllUsers:R gs://BUCKET_NAME

# GKE : Google Container Engine
It's a fully managed environment for containerized application deployment
It uses compute engine ressources with kubernetes and a special customized OS (Container-Optimized OS)
It's a solution between Compute Engine and App Engine

## When to choose Container Engine over App Engine
- Hybrid or multicloud
- Other protocols than HTTP/HTTPS
- Multicontainer solution (need orchestration)
- Want to use Kubernetes

## When ti choose Compute Engine over Container Engine
- Need GPU
- Non Kubernetes container solution
- Migrating legacy on premised to the cloud
- Custom OS

## GKE Components
### Container cluster
Group of instances. It contains at least 1 node instance

### Kubernetes master
Manage the cluster

### Pods
Group of one or more containers
Share storage and configuration data among containers
Pod can contain multiple containers

### Node
Individual instance that runs one or more pod

### Replication controller
Ensures the number of pod replicas are always available and automatically adds or remove pods

### Services
Define a logical set of pods accross nodes and a way to access them using a single IP en port

### Container registry
Not part of GKE, but a separate service for private storage of our own Docker images

### Configure an application on kubernetes
- gcloud config set container/cluster CLUSTER-NAME : The cluster name to use
- docker build -t gcr.io/PROJECT_NAME/IMAGE_NAME . : Build the docker image
- gcloud docker -- push gcr.io/PROJECT_NAME/IMAGE_NAME : Push docker image to the GCP image hub
- gcloud container clusters get-credentials CLUSTER-NAME : Get an id for the Kubernetes cluster
- kubectl get APP : Obtain informations and the external IP of the application
- kubectl create -f APP.yaml : Deploy the application on Kubernetes
  APP.yaml sample :
```YAML
    apiVersion: v1
    kind: ReplicationController
    metadata:
      name: APP
    spec:
      replicas: 3
      template:
        metadata:
          labels:
            app: APP_NAME
            tier: frontend
        spec:
          containers:
          - name: APP
            image: gcr.io/PROJECT_NAME/IMAGE_NAME
            imagePullPolicy: Always
            env:
            - name: PROCESSES
              value: IMAGE_NAME
            ports:
            - containerPort: 8080
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: APP
      labels:
        app: APP_NAME
        tier: frontend
    spec:
      type: LoadBalancer
      ports:
      - port: 80
        targetPort: 8080
      selector:
        app: APP_NAME
        tier: frontend
```

# Big Data services

## BigQuery
Stores and queries massive dataset
Use SQL syntax
Real time analysis

BigQuery organization:
- GCP Project -> Can be shared
- Dataset (group of tables) -> Can be shared (lowest level of access control)
- Tables -> row/column structures
- Jobs -> queuing large request

## Dataflow
Data processing service based on Apache Beam
Can process data with 2 modes : Stream or Batch

## Dataproc
Scalable clusters of Apache Spark and Apache Hadoop
Preemtible instances for batch processing recommanded

## Datalab
Interactive tool for data exploration, analysis, visualization and machine learning
Build on Jupyter

## Dataprep
Visually explore, clean and prepare data for analysis
Runs on top of Dataflow

## Pub/Sub
Send and receive messages many to many (asynchronous)
Ideal for stream processing
Apps publish and subscribre to topics

# Machine Learning
Based on neuronal network (TensorFlow) already trained

## Cloud Vision API
Detect and extract text
Classify image (Car, boat, lion, ...)
Detect inapropriate content
Detect celebrities, logos, ...

## Cloud natural language API
Reveal the structure and meaning of text
Extract informations about people, place, ...
Understand sentiment, ...

## Cloud Translate API
Language detection and translation

## Cloud Speech API
Convert audio to text and vice versa

## Cloud Video Intelligence
In beta for now
Video analysis, detect object, content, ...


# Global commands
- gcloud config set compute/zone (ZONE)
- gcloud auth list
- gcloud config list project

- gcloud compute instances create NAME --labels contact=matt,state=inuse,env=prod
- gcloud compute instances update NAME --update-labels contact=matt
- gcloud compute instances update NAME --remove-labels label_name

- gcloud source repos clone NAME

- gcloud datastore create-indexes FILE_DESCRIPTOR.yaml

- gcloud debug logpoints list
- gcloud beta debug logpoints create FILE.py:NUM_LINE "Message log"
