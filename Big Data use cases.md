# Flowlogistic

## Main themes

- Transition existing infrastructure to the Cloud
- Reproduce existing workload -> First step into cloud transition

## Primary cloud objectives

- Use proprietary inventory system
   - Many IoT devices
   - Kafka stack unable to handle data ingest
   - Interact with both SQL and NoSQL databases
   - Map to Pub/Sub - Dataflow
- Hadoop analytics in the cloud
   - Dataproc
   - Apply analytics and ML

## Technical considerations

- Stream and batch data ingest
- Migrate existing workload to manage services
   - SQL -> Cloud SQL (Spanner if more than 10TB and/or globally available)
   - Cassandra - No SQL -> BigTable
   - Kafka -> Pub/Sub - Dataflow - BigQuery
- Store data in a data lake
   - Further transition one in the cloud
   - Storage -> Cloud Storage / BigTable / BigQuery
   - Migrate from HDFS

## Migration scenario
### Inventory tracking system

![flowlogistic](http://www.mickael-roger.com/tmp/flowlogistic1.png)

### Analytics system

![flowlogistic](http://www.mickael-roger.com/tmp/flowlogistic2.png)


# MJTelco

## Main themes

- No legacy
- Global data ingest

## Primary cloud objectives

- Accept massive data ingest and processing on a global scale
   - Need no-ops
   - Pub/Sub accepts inputs from many host globally
- Use ML to improve their topology model

## Technical considerations

- Isolated environnment from Dev, Int and production -> Use separate projects
- Granting access to data -> IAM roles
- Analyze up to 2 years of telemetry data -> Store in GCS or BigQuery

## Migration scenario

![mjtelco](http://www.mickael-roger.com/tmp/mjtelco1.png)
