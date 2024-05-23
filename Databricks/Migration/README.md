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

### Data Migration from On-Prem Hadoop Cluster to Azure Databricks for both full and incremental loads

To achieve data migration from an on-premises Hadoop cluster to Azure Databricks with Unity Catalog and Delta Tables for both full and incremental loads, follow these steps:

### Architecture Overview
1. **On-premises Hadoop Cluster**: Source data stored in HDFS.
2. **Azure Blob Storage/ADLS**: Intermediate storage in Azure.
3. **Azure Databricks**: For data processing and storage using Delta Tables.
4. **Unity Catalog**: For data organization and management.
5. **Azure Data Factory**: For orchestrating the data migration process.

### Step-by-Step Process

#### Full Load (Initial Load)

1. **Extract Data from Hadoop Cluster**
   - Use `hdfs dfs -copyToLocal` to extract data from HDFS to a local directory.
   - Example:
     ```bash
     hdfs dfs -copyToLocal /path/on/hdfs /local/path
     ```

2. **Transfer Data to Azure Blob Storage/ADLS**
   - Use tools like Azure Storage Explorer, AzCopy, or Azure CLI to upload the data to Azure Blob Storage or Azure Data Lake Storage (ADLS).
   - Example with AzCopy:
     ```bash
     azcopy copy '/local/path' 'https://<storage-account-name>.blob.core.windows.net/<container-name>'
     ```

3. **Mount Azure Storage in Databricks**
   - Mount the Azure Blob Storage or ADLS to your Databricks workspace.
   - Example for mounting ADLS:
     ```python
     configs = {
         "fs.azure.account.auth.type": "OAuth",
         "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
         "fs.azure.account.oauth2.client.id": "<application-id>",
         "fs.azure.account.oauth2.client.secret": "<application-secret>",
         "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<directory-id>/oauth2/token"
     }

     dbutils.fs.mount(
         source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/",
         mount_point = "/mnt/<mount-name>",
         extra_configs = configs
     )
     ```

4. **Read Data into Databricks**
   - Use Spark to read the data files from the mounted storage into Databricks DataFrames.
   - Example:
     ```python
     df = spark.read.csv("/mnt/<mount-name>/data", header=True, inferSchema=True)
     df.show()
     ```

5. **Write Data to Delta Lake**
   - Save the data into Delta Lake for optimized storage and querying.
   - Example:
     ```python
     df.write.format("delta").mode("overwrite").save("/mnt/<mount-name>/delta/table_name")
     ```

6. **Register Delta Table in Unity Catalog**
   - Use SQL commands to create and register the Delta table in Unity Catalog.
   - Example:
     ```sql
     CREATE CATALOG IF NOT EXISTS ecomm_app_insights;
     CREATE SCHEMA IF NOT EXISTS ecomm_app_insights.bronze;

     CREATE TABLE IF NOT EXISTS ecomm_app_insights.bronze.table_name (
       id STRING,
       name STRING,
       value DECIMAL(10, 2)
     )
     USING delta
     LOCATION 'abfss://<storage-account-name>@<container-name>.dfs.core.windows.net/delta/table_name';
     ```

#### Incremental Loads

1. **Identify Changes in Hadoop Cluster**
   - Use timestamp columns or other mechanisms to identify new or updated records.
   - Example query to extract incremental data:
     ```sql
     SELECT * FROM table WHERE last_modified >= '2022-01-01 00:00:00'
     ```

2. **Export Incremental Data**
   - Use `hdfs dfs -copyToLocal` to export the incremental data.
   - Example:
     ```bash
     hdfs dfs -copyToLocal /path/on/hdfs/incremental /local/path/incremental
     ```

3. **Transfer Incremental Data to Azure**
   - Use AzCopy or another method to upload the incremental data files to Azure Blob Storage or ADLS.
   - Example with AzCopy:
     ```bash
     azcopy copy '/local/path/incremental' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/incremental'
     ```

4. **Read Incremental Data into Databricks**
   - Use Spark to read the incremental data files from the mounted storage into Databricks DataFrames.
   - Example:
     ```python
     incremental_df = spark.read.csv("/mnt/<mount-name>/incremental", header=True, inferSchema=True)
     incremental_df.show()
     ```

5. **Merge Incremental Data into Delta Table**
   - Use Delta Lake’s `MERGE INTO` statement to upsert the incremental data into the Delta table.
   - Example:
     ```python
     from delta.tables import DeltaTable

     # Define the Delta table
     delta_table = DeltaTable.forPath(spark, "/mnt/<mount-name>/delta/table_name")

     # Merge incremental data into Delta table
     delta_table.alias("target").merge(
         incremental_df.alias("source"),
         "target.id = source.id"  # Use the appropriate key for matching records
     ).whenMatchedUpdateAll().whenNotMatchedInsertAll().execute()
     ```

### Automate the Process

1. **Scheduling with Databricks Jobs**
   - Create Databricks jobs to automate the full and incremental data load processes. Schedule the full load job to run once and the incremental load job to run at regular intervals.

2. **Data Pipelines with Azure Data Factory**
   - Use Azure Data Factory (ADF) to orchestrate the data extraction, transfer, and load processes. ADF can manage the entire ETL pipeline, including triggering Databricks notebooks for processing.
  

### Data Migration from On-Prem DB2 to Azure Databricks for both full and incremental loads

To migrate data from an on-premises DB2 database to Azure Databricks with Unity Catalog and Delta Tables for both full and incremental loads, you need to follow a structured process. This includes extracting data from DB2, transferring it to Azure, processing it in Databricks, and managing it with Unity Catalog and Delta Tables. Here’s a detailed guide:

### Step-by-Step Process

#### 1. Full Load (Initial Load)

1. **Extract Data from DB2**
   - Use DB2 export utilities or SQL queries to extract data from the DB2 database into flat files (e.g., CSV, TSV) or directly into a staging database.
   - Example of exporting a table to a CSV file:
     ```bash
     db2 "EXPORT TO table_data.csv OF DEL MODIFIED BY NOCHARDEL SELECT * FROM schema.table_name"
     ```

2. **Transfer Data to Azure Blob Storage/ADLS**
   - Use tools like Azure Storage Explorer, AzCopy, or Azure CLI to upload the exported data files to Azure Blob Storage or Azure Data Lake Storage (ADLS).
   - Example with AzCopy:
     ```bash
     azcopy copy '/local/path/to/table_data.csv' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/table_data.csv'
     ```

3. **Mount Azure Storage in Databricks**
   - Mount the Azure Blob Storage or ADLS to your Databricks workspace.
   - Example for mounting ADLS:
     ```python
     configs = {
         "fs.azure.account.auth.type": "OAuth",
         "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
         "fs.azure.account.oauth2.client.id": "<application-id>",
         "fs.azure.account.oauth2.client.secret": "<application-secret>",
         "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<directory-id>/oauth2/token"
     }

     dbutils.fs.mount(
         source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/",
         mount_point = "/mnt/<mount-name>",
         extra_configs = configs
     )
     ```

4. **Read Data into Databricks**
   - Use Spark to read the data files from the mounted storage into Databricks DataFrames.
   - Example:
     ```python
     df = spark.read.csv("/mnt/<mount-name>/table_data.csv", header=True, inferSchema=True)
     df.show()
     ```

5. **Write Data to Delta Lake**
   - Save the data into Delta Lake for optimized storage and querying.
   - Example:
     ```python
     df.write.format("delta").mode("overwrite").save("/mnt/<mount-name>/delta/table_name")
     ```

6. **Register Delta Table in Unity Catalog**
   - Use SQL commands to create and register the Delta table in Unity Catalog.
   - Example:
     ```sql
     CREATE CATALOG IF NOT EXISTS ecomm_app_insights;
     CREATE SCHEMA IF NOT EXISTS ecomm_app_insights.bronze;

     CREATE TABLE IF NOT EXISTS ecomm_app_insights.bronze.table_name (
       id STRING,
       name STRING,
       value DECIMAL(10, 2)
     )
     USING delta
     LOCATION 'abfss://<storage-account-name>@<container-name>.dfs.core.windows.net/delta/table_name';
     ```

#### 2. Incremental Loads

1. **Identify Changes in DB2**
   - Use Change Data Capture (CDC) mechanisms, triggers, or timestamp columns to identify new or updated records.
   - Example of querying incremental data using a timestamp column:
     ```sql
     SELECT * FROM schema.table_name WHERE last_modified >= '2022-01-01 00:00:00'
     ```

2. **Export Incremental Data**
   - Export the incremental data from DB2 to a CSV file.
   - Example:
     ```bash
     db2 "EXPORT TO incremental_data.csv OF DEL MODIFIED BY NOCHARDEL SELECT * FROM schema.table_name WHERE last_modified >= '2022-01-01 00:00:00'"
     ```

3. **Transfer Incremental Data to Azure**
   - Use AzCopy or another method to upload the incremental data files to Azure Blob Storage or ADLS.
   - Example with AzCopy:
     ```bash
     azcopy copy '/local/path/to/incremental_data.csv' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/incremental_data.csv'
     ```

4. **Read Incremental Data into Databricks**
   - Use Spark to read the incremental data files from the mounted storage into Databricks DataFrames.
   - Example:
     ```python
     incremental_df = spark.read.csv("/mnt/<mount-name>/incremental_data.csv", header=True, inferSchema=True)
     incremental_df.show()
     ```

5. **Merge Incremental Data into Delta Table**
   - Use Delta Lake’s `MERGE INTO` statement to upsert the incremental data into the Delta table.
   - Example:
     ```python
     from delta.tables import DeltaTable

     # Define the Delta table
     delta_table = DeltaTable.forPath(spark, "/mnt/<mount-name>/delta/table_name")

     # Merge incremental data into Delta table
     delta_table.alias("target").merge(
         incremental_df.alias("source"),
         "target.id = source.id"  # Use the appropriate key for matching records
     ).whenMatchedUpdateAll().whenNotMatchedInsertAll().execute()
     ```

### Automate the Process

1. **Scheduling with Databricks Jobs**
   - Create Databricks jobs to automate the full and incremental data load processes. Schedule the full load job to run once and the incremental load job to run at regular intervals.

2. **Data Pipelines with Azure Data Factory**
   - Use Azure Data Factory (ADF) to orchestrate the data extraction, transfer, and load processes. ADF can manage the entire ETL pipeline, including triggering Databricks notebooks for processing.

