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
Service accounts are used to authenticate between services

### Service account
2 types : Google-Managed and User-Managed
- Google-Managed : Represent different google services are like PROJECT_NUMBER@cloudservices.gservicesaccount.com and are invisible to end user
- User-Managed : Creted by and for admins. Are like : PROJECT_NUMER-compute@developper.gserviceaccount.com or PROJECT-ID@appspot.gserviceaccount.com

Service account uses a accountKey and not a password

### Scope
Combine IAM roles with service account to grant per instance permissions to other GCP ressources (declared on the compute instance creation)


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

# Billing
Billing roles are defined in IAM (BillingAccountViewer, ...)
Billing accounts are linked to projects
Each project must be link to a Billing account and you cannot attach a new project to a billing account without thel BillingAccountUser role

Alerts can be set on budget

Billing can be exported into BigQuery or Cloud Storage (using CSV or JSON format)


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
Objects : Can be up to 5TB. Stored in a bucket (Folders are also considered objects)

A bucket can have one of theses storage class :
- Multi regional : Geo-redundant (inside a continent - Europe, Asia or US)
- Regional : Redondant inside a geographical region
- Nearline : Used to store rarely accessed document (less than once a month)
- Coldline : Used to store very rarely accessed document (less than a year)
Each storage class has the same throughput, latency and durability. Differences are from the availability (From 99% to 99.95%) and pricing for storage and access

Cannot change from multi-regional to regional (and vice versa)
Changing class only affect new objects (old objects class can be changed with gsutil)

No retrieval cost when the bucket and the user are in the same region (No Wan access). For instance : GCE in us-east1 and GCS Bucket multi-regioanl in us

### Security concept
#### Access management principles
Two methods : IAM and ACL

##### IAM
Granted to an individual bucket (but not objects)
Possible to gran access to manage bucket but not view/read objects inside

Standard Storage roles work independently from ACL
Legacy roles work with ACL (When a legacy role right is removed, ACL reflects the change)

Standard storage roles (work independently from ACLs) : Storage Admin / Storage Object Admin / Storage Object Viewer / Storage Object Creator
Legacy storage roles (work with ACLs) : Storage Legacy Bucket Owner / Storage Legacy Bucket Reader / Storage Legacy Bucket Writer / Storage Legacy Object Owner / Storage Legacy Object Reader

##### ACL
/!\ Bucket ACL cannot be set through webconsole
Can be applied to bucket or individual objects
Objects inherit ACL from default bucket ACL

Best practice : Use IAM over ACL whenever possible / Use ACL to grant access to an object without granting access to bucket / Use group over individual IAM users

Signed URLs : Times access to object Data
Useful to give a temporarly access to a ressource.
No need for a google account

### Signed URL for temporary access
- Create a service account key in the API menu
- Set permission for 10 minutes : gsutil signurl -d 10m file_key.json gs://BUCKET_NAME/FILE
- Use the returned URL

### Object versioning
Disable by default
When enabled, deleted and overwritten objects are archived
Object keeps the same name but paired with unique identifier number
If versioning disabled, existing versions remain but new ones not created

Archived versions retain own ACL


### Lifecycle management
Applied to bucket level and can only be set through the CLI
Implemented with combination of rules, conditions and actions

If there are multiple conditions in one rule, all conditions must be met before action taken

Conditions can be : Age, CreatedBefore, IsLive, MatchesStorageClass, NumberOfNewerVersions
Actions : Delete, SetStorageClass

Can be configured through console or CLI

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


# Stackdriver
Is a separate project and can monitor AWS and GCP
You can use it with or without agent

## Best practices
- Create a single project for stackdriver monitoring
- Determine monitoring needs in advance
- Separate stackdriver service accounts for data and control isolation

## Monitoring
Monitor metrics, health checks, dashboards and alerts
A group of ressources can be created. This group can be filtered by a tag, a security group, a name, a project, ...

### Uptime check
Check HTTP, HTTPS or TCP and wait for a response code

### Alerting
Based on 4 elements
- Conditions : Uptime check, ...
- Notification : Email, SMS, slack, webhook, ...
- Documentation : Attach a doc to the alert message
- Name : Name of the alert policy

## Logging
Audit of the activity. It's a repository for log data and events

Collect plateform, system and application logs (with agent)

Real time and batch monitoring
Exports logs to other sources for long term storage using "sink" (to big query or GCS or pub/sub)

Activate logging for all services (bucket access, ...) : Add this entries at the beginning of the policy.yaml IAM
```YAML
auditConfigs:
- auditLogConfigs:
  - logType: ADMIN_READ
  - logType: DATA_WRITE
  - logType: DATA_READ
  service: allServices
```

## Error reporting
Identify and understand applications errors

Automatically build into App Engine
In beta for GAE flexible, GCE, GKE and cloud function

CGE and GKE require a stackdriver logging agent

Work with : Java, Python, JavaScript, Ruby, C#, PHP and Go

## Trace
Find bottleneck on App Engine

Automatically build into App Engine
Available on GCE, GKE and GAE flexible with stackdriver trace API or SDK

## Debugger
Find/Fix code errors in production

Inspect application state without stopping or slowing app

Automatically build into App Engine
Available on GCE, GKE and GAE flexible with additional configuration

## Retention

Admin activity logs - 400 days
Data access log, Non-audit logs - 7 days (30 days with premium)


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
- gcloud config list

- gcloud compute instances create NAME --labels contact=matt,state=inuse,env=prod
- gcloud compute instances update NAME --update-labels contact=matt
- gcloud compute instances update NAME --remove-labels label_name

- gcloud source repos clone NAME

- gcloud datastore create-indexes FILE_DESCRIPTOR.yaml

- gcloud debug logpoints list
- gcloud beta debug logpoints create FILE.py:NUM_LINE "Message log"

## IAM Policy management
- gcloud projects get-iam-policy PROJECT_NAME --format json > policy.json
- gcloud projects set-iam-policy PROJECT_NAME policy.json

## gsutil commands
- Create bucket : gsutil mb  gs://BUCKET_NAME
- Change rights : gsutil defacl ch -u AllUsers:R gs://BUCKET_NAME
- gsutil ls -l gs://BUCKET_NAME
- Web access to bucket : https://storage.cloud.google.com/BUCKET_NAME

- gsutil iam ch user:user@gmail.com:objectCreator,objectViewer gs://BUCKET_NAME/
- gsutil iam ch -d user:user@gmail.com:objectCreator,objectViewer gs://BUCKET_NAME/  // Remove specific role
- gsutil iam ch user:user@gmail.com gs://BUCKET_NAME/   // Remove all roles

- gsutil acl ch -u user@gmail.com:[O|R|W] gs://BUCKET_NAME/[FILE|\*.png] (AllUsers can be used to set a public link)
- gsutil acl ch -d user@gmail.com gs://BUCKET_NAME/[FILE]       // Remove ACL rights

- gsutil versioning get gs://BUCKET_NAME/
- gsutil versioning set on gs://BUCKET_NAME/
- gsutil ls -a gs://BUCKET_NAME     // Liste files with version ID (include versioned removed files)
- gsutil rm gs://BUCKET_NAME/file#version_id     // Remove permanently a file even if versioned

- gsutil lifecycle get gs://BUCKET_NAME > policy.json
- gsutil lifecycle set policy.json gs://BUCKET_NAME

- gsutil rm -r gs://BUCKET_NAME     // Delete bucket and content

- gsutil rewrite -r -s NEARLINE gs://BUCKET_NAME/*     // Rewrite file into nearline storage class (-r for recursive)
- gsutil -m ...     // Multiple thread for speed improvement
