### Data Migration from On-Prem Kafka Cluster to Azure Databricks

To perform data migration from an on-premises Kafka cluster to Azure Databricks with both incremental and full loads using Unity Catalog and Delta Tables, you'll follow these steps:

1. **Set Up Connectivity**
2. **Full Load (Initial Load)**
3. **Incremental Loads**
4. **Data Organization with Unity Catalog and Delta Tables**
5. **Automation**

### Detailed Steps

#### 1. Set Up Connectivity

**Azure Event Hubs as Kafka Endpoint:**

1. **Create Azure Event Hubs Namespace and Event Hub**:
   - In the Azure portal, create an Event Hubs namespace.
   - Inside the namespace, create an Event Hub.

2. **Set Up Kafka Connect to Mirror Kafka Topics to Event Hubs**:
   - Use Kafka Connect to replicate data from your on-premises Kafka cluster to Azure Event Hubs.
   - Example Kafka Connect configuration:

   ```json
   {
     "name": "kafka-to-eventhubs-connector",
     "config": {
       "connector.class": "io.confluent.connect.replicator.ReplicatorSourceConnector",
       "key.converter": "org.apache.kafka.connect.storage.StringConverter",
       "value.converter": "org.apache.kafka.connect.storage.StringConverter",
       "src.kafka.bootstrap.servers": "localhost:9092",
       "src.consumer.group.id": "replicator-group",
       "src.kafka.topic.whitelist": "your_topic",
       "dest.kafka.bootstrap.servers": "your-eventhubs-namespace.servicebus.windows.net:9093",
       "dest.kafka.security.protocol": "SASL_SSL",
       "dest.kafka.sasl.mechanism": "PLAIN",
       "dest.kafka.sasl.jaas.config": "org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"your-eventhubs-connection-string\";",
       "dest.kafka.replication.factor": "3",
       "confluent.topic.replication.factor": "3"
     }
   }
   ```

#### 2. Full Load (Initial Load)

1. **Extract Historical Data**:
   - Use a Kafka consumer to read historical data from the beginning of the Kafka topic.

2. **Transfer Data to Azure Blob Storage or ADLS**:
   - Save the extracted data to files and upload them to Azure Blob Storage or Azure Data Lake Storage (ADLS) using tools like AzCopy.

   Example:
   ```bash
   azcopy copy '/local/path/to/historical_data.json' 'https://<storage-account-name>.blob.core.windows.net/<container-name>'
   ```

3. **Load Data into Azure Databricks**:
   - Mount the Azure storage in Databricks and read the data files into Spark DataFrames.

   Example:
   ```python
   from pyspark.sql import SparkSession

   # Initialize Spark session
   spark = SparkSession.builder \
       .appName("Full Load from Kafka to Databricks") \
       .getOrCreate()

   # Define the ADLS mount path
   mount_path = "/mnt/<mount-name>"

   # Read full data from JSON file
   df = spark.read.json(f"{mount_path}/historical_data.json")
   df.show()

   # Write data to Delta Lake
   df.write.format("delta").mode("overwrite").save(f"{mount_path}/delta/historical_data")
   ```

4. **Register Delta Table in Unity Catalog**:
   - Use SQL commands to create and register the Delta table in Unity Catalog.

   Example:
   ```sql
   CREATE CATALOG IF NOT EXISTS ecomm_app_insights;
   CREATE SCHEMA IF NOT EXISTS ecomm_app_insights.bronze;

   CREATE TABLE IF NOT EXISTS ecomm_app_insights.bronze.historical_data (
     key STRING,
     value STRING,
     timestamp TIMESTAMP
   )
   USING delta
   LOCATION 'abfss://<storage-account-name>@<container-name>.dfs.core.windows.net/delta/historical_data';
   ```

#### 3. Incremental Loads

1. **Stream Data from Kafka to Azure Event Hubs**:
   - Use Kafka Connect to replicate real-time data from on-premises Kafka topics to Azure Event Hubs.

2. **Read Streaming Data from Event Hubs in Databricks**:
   - Set up Databricks to read streaming data from Azure Event Hubs.

   Example:
   ```python
   from pyspark.sql import SparkSession
   from pyspark.sql.functions import *

   # Initialize Spark session
   spark = SparkSession.builder \
       .appName("Incremental Load from Kafka to Databricks") \
       .getOrCreate()

   # Event Hubs configuration
   event_hubs_conf = {
       'eventhubs.connectionString': 'Endpoint=sb://<your-eventhubs-namespace>.servicebus.windows.net/;SharedAccessKeyName=<your-shared-access-key-name>;SharedAccessKey=<your-shared-access-key>;EntityPath=<your-eventhub-name>'
   }

   # Read streaming data from Event Hubs
   stream_df = spark.readStream \
       .format("eventhubs") \
       .options(**event_hubs_conf) \
       .load()

   # Parse the JSON data
   stream_df = stream_df.withColumn("body", col("body").cast("string"))

   # Write streaming data to Delta Lake
   query = stream_df.writeStream \
       .format("delta") \
       .outputMode("append") \
       .option("checkpointLocation", "/mnt/<mount-name>/checkpoints/kafka_to_delta") \
       .start("/mnt/<mount-name>/delta/streaming_data")
   ```

3. **Merge Incremental Data into Delta Table**:
   - Use Delta Lake’s `MERGE INTO` statement to upsert the streaming data into the Delta table.

   Example:
   ```python
   from delta.tables import DeltaTable

   # Define the Delta table
   delta_table = DeltaTable.forPath(spark, "/mnt/<mount-name>/delta/streaming_data")

   # Merge streaming data into Delta table
   delta_table.alias("target").merge(
       stream_df.alias("source"),
       "target.key = source.key"  # Use the appropriate key for matching records
   ).whenMatchedUpdateAll().whenNotMatchedInsertAll().execute()
   ```

#### 4. Organize Data within Unity Catalog

- Register Delta Tables within Unity Catalog for both full and incremental data.

#### 5. Automate the Process

1. **Scheduling with Databricks Jobs**:
   - Create Databricks jobs to automate the full and incremental data load processes.

2. **Data Pipelines with Azure Data Factory**:
   - Use Azure Data Factory (ADF) to orchestrate the data extraction, transfer, and load processes.

### Example Workflow

#### Full Load (Initial Load)

```python
from pyspark.sql import SparkSession

# Initialize Spark session
spark = SparkSession.builder \
    .appName("Full Load from Kafka to Databricks") \
    .getOrCreate()

# Define the ADLS mount path
mount_path = "/mnt/<mount-name>"

# Read full data from JSON file
df = spark.read.json(f"{mount_path}/historical_data.json")
df.show()

# Write data to Delta Lake
df.write.format("delta").mode("overwrite").save(f"{mount_path}/delta/historical_data")

# Register the Delta table in Unity Catalog
spark.sql("""
CREATE CATALOG IF NOT EXISTS ecomm_app_insights;
CREATE SCHEMA IF NOT EXISTS ecomm_app_insights.bronze;

CREATE TABLE IF NOT EXISTS ecomm_app_insights.bronze.historical_data (
  key STRING,
  value STRING,
  timestamp TIMESTAMP
)
USING delta
LOCATION 'abfss://<storage-account-name>@<container-name>.dfs.core.windows.net/delta/historical_data';
""")
```

#### Incremental Load

```python
from pyspark.sql import SparkSession
from delta.tables import DeltaTable

# Initialize Spark session
spark = SparkSession.builder \
    .appName("Incremental Load from Kafka to Databricks") \
    .getOrCreate()

# Event Hubs configuration
event_hubs_conf = {
    'eventhubs.connectionString': 'Endpoint=sb://<your-eventhubs-namespace>.servicebus.windows.net/;SharedAccessKeyName=<your-shared-access-key-name>;SharedAccessKey=<your-shared-access-key>;EntityPath=<your-eventhub-name>'
}

# Read streaming data from Event Hubs
stream_df = spark.readStream \
    .format("eventhubs") \
    .options(**event_hubs_conf) \
    .load()

# Parse the JSON data
stream_df = stream_df.withColumn("body", col("body").cast("string"))

# Write streaming data to Delta Lake
query = stream_df.writeStream \
    .format("delta") \
    .outputMode("append") \
    .option("checkpointLocation", "/mnt/<mount-name>/checkpoints/kafka_to_delta") \
    .start("/mnt/<mount-name>/delta/streaming_data")

# Merge streaming data into Delta table
delta_table = DeltaTable.forPath(spark, "/mnt/<mount-name>/delta/streaming_data")
delta_table.alias("target").merge(
    stream_df.alias("source"),
    "target.key = source.key"  # Use the appropriate key for matching records
).whenMatchedUpdateAll().whenNotMatchedInsertAll().execute()
```

By following these steps, you can effectively implement both full and incremental data loads from an on-premises Kafka cluster to Azure Databricks, 
leveraging Delta Lake and Unity Catalog for efficient data management and querying. 

