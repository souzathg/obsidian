# Cloud BigTable
- Petabyte scale, wide column NoSQL DB (HBase API compatible). 
	- Designed for huge volumes of analytical and operations data.
		- IOT Streams, Analytics, Time Series Data, etc
	- Handle millions of read/write TPS at very low latency
	- Single row transactions (multi row transactions NOT supported)
- NOT serverless: You need to create a server instance (Use SSD or HDD)
	- Scale horizontally with multiple nodes (No downtime for cluster resizing)
- Cannot export data using cloud console or gcloud:
	- Either use a Java application (java -jar JAR export\import) OR
	- Use HBase commands
- Use cbt command line tool to work with BigTable (NOT gcloud)
	- e.g.: `cbt createtable MY-TABLE`
- **Cloud Dataflow**: Used to export data from BigTable to Cloud Storage

## Designing BigTable Tables
- Two things you should know before starting with BigTable:
	- What data do you want to store? (format, columns, etc)
	- What would your frequently used queries look like (ranked by usage)?
- Design your table: Cloud BigTable is a **key/value store**
	- Each table has ONLY ONE index, the row key
		- You cannot create any other secondary indexes
	- Design your row key based on your frequently used queries
		- Goals when designing row key
			- Avoid full table scan
			- Distribute reads and writes even;y across the row space of a table
### Row Key
- You can have multiple row key segments:
	- Separated by a delimiter (Example: thiago#123456#abcd)
- Avoid sequential row keys
	- Avoid timestamps or sequential numbers as the first key segment
	- If you plat to retrieve data based on a timestamp
		- Use timestamp as second or third key segment
		- Use reversed timestamp (Ex: Long.MAX_VALUE - timestamp) if you frequently query recent data
			- Records will be ordered from most recent to least recent
- Row keys should start with a common value and end with a granular value
	- Example1: america#brazil#sao-paulo
		- Allows you to query by continent or country or city
	- Example2: customer-name#rest-of-the-key
		- Useful in multi-tenant applications
## BigTable best practices
- Recommended for streaming IOT & time series data
- Automatically shards data into multiple tables across nodes in cluster
	- **Goal 1**: Have same amount of data on each node
	- **Goal 2**: Distribute reads and writes equally across all nodes
	- [REMEBER] Pre-test with heavy load for a few minutes before you run your tests
		- Gives BigTable a chance to balance data across your nodes
- Cloud BigTable supports SSD or HDD storage:
	- SSD - For most use cases
	- HDD - For large non latency-sensitive data sets of size >10 TB with very very few reads
### Replication
- You can create a Cloud BigTable instance with more than one cluster to enable replication (Cross Region or Cross Zone)
	- Independent copy of data is sorted in each cluster (in the zone of the cluster)
	- Bigtable automatically replicates changes
	- Replication improves durability and availability of your data
		- Stores separate copies in multiple zones or regions
		- Can automatically failover between clusters if needed
	- Replication helps you to put data closer to your customers
		- Configure an application profile, or app profile with routing policy of multi-cluster routing
			- Automatically route to nearest cluster in an instance