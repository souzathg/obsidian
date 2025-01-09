# Cloud Spanner
- Fully managed, mission critical, relational (SQL), globally distributed database with very high availability (99.999%)
	- Strong transaction consistency at global scale
	- Scales to PBs of data with automatic sharding
- Cloud Spanner **scales horizontally for reads and writes**
	- Just configure the number of nodes
	- [REMEBER] In comparison, [[Cloud SQL]] provides read replicas:
		- BUT you cannot horizontally scale write operations with [[Cloud SQL]]
- Regional and Multi-Regional configurations  
- **Expensive** (compared to [[Cloud SQL]]): Pay for nodes & storage
- **Data Export**: Use Cloud Console to export data
	- Other option is to use Dataflow to automate export

Example script 
```SQL
1. CREATE TABLE Users (
2.   UserId   INT64 NOT NULL,
3.   UserName  STRING(1024)
4. ) PRIMARY KEY(UserId);
```

## Designing Cloud Spanner Tables - Interleaved Tables
```SQL
CREATE TABLE User (
	UserId  INT64 NOT NULL,
	FirstName  STRING(1024),
	LastName  STRING(1024),
) PRIMARY KEY (UserId);

CREATE TABLE Todo (
	UserId  INT64 NOT NULL,
	TodoId  INT64 NOT NULL, 
	Description  STRING(MAX),
) PRIMARY KEY (UserId, TodoId),
INTERLEAVE IN PARENT User ON DELETE CASCADE
```
- Scenario: Every time you access a user, you also access their todos:
	- [DEFAULT] User details and his todos might be stored in different nodes
- How to request Cloud Spanner to store user and his todos together? **INTERLEAVED TABLES**
	- User and todo data might be distributed across nodes BUT a specific user and his todo information will be stored on the same node!
	- Provides the best possible performance to access details form multiple tables

## Exploring Cloud Spanner Queries - UNNEST
Spanner supports ARRAY data type, which is an ordered list consisting of values of the same data type.
`SELECT ['AWS', 'AZURE', 'GCP'] as platform;`  
Sometimes you would want to convert elements in an array to rows in a table

- **UNNEST** is used to perform flattening
- **UNNEST** can be used to execute a single DML statement with an array of data
	- Reduces the need to execute multiple insert or delete statements

Example queries:
```SQL
INSERT INTO Todos (UserId, TodoId, Description)
SELECT * FROM UNNEST ([(1, 1, 'Learn GCP'), 
					   (1, 2, 'Learn AWS'), 
					   (1, 3, 'Learn Azure')])
```

```SQL
DELETE FROM Todos
WHERE (UserId, TodoId)
IN UNNEST ([(1,1), (1,3)])
```

## Understanding Cloud Spanner Client Libraries
- How do you build applications that talk to Cloud Spanner? **Cloud Spanner Client Libraries**
	- Cloud Client Libraries recommended
		- Java:  add to pom.xml or gradle dependencies `compile 'com.google.cloud:google-cloud-spanner'
		- Node.js: `npm install --save @google-cloud/spanner`
		- Python: `pip install --upgrade google-cloud-spanner`
	- Supported for: Compute Engine, App Engine flexible environment, Google Kubernetes Engine, and Cloud Functions
		- Java library provides support for App Engine standard environment as well
- Recommended: Use Service Account for providing access
- Couple things to remember:
	- Cloud Spanner client library retries a transaction automatically
	- You can **divide a query into smaller pieces** and execute it in parallel
		- Example: txn.partitionQuery(..., Statement.of("SELECT TodoId, Description FROM Todos))
		- Recommended when reading large amounts of data

## Exploring Cloud Spanner Transactions
**Trasaction** is a set of atomic operations. Either all operations are successful or none of them are successful.  
Cloud Spanner supports **three transaction modes:**

- **Locking read-write**: When you wnat to perform write transactions
	- Provide ACID transaction properties of typical relational databases
	- RECOMMENDED: If you are doing single or multiple writes as a result of reads, do all the writes and reads in a Locking read-write transaction mode
- **Read-only**: Provides consistency across multiple reads
	- Does not allow writes
- **Partitioned DML**: Recommended for Bulk updates (cleanup and backfilling)
	- Example: Fill a default value instead of NULL
	- Partitioned DML would partition the key space and execute multiple independent read-write transactions
		- Avoid locking an entire table

## Best Practices
- Use Cloud Spanner **multi-region configuration** when you have users from multiple geographic locations
	- [REMEMBER] Provides **faster reads** at the **cost of a small increase in write latency**
	- Available multi-region-configurations:
		- One continent: asia1 (Asia), eur6 (Europe), nam12 (North America), etc
		- Three Continents: nam-eur-asia1
- Design your primary key to distribute data across servers
	- Avoid monotonically increasing values as the first key part
		- Your inserts and queries might be slow
		- Example: Avoid timestamp-based primary keys
	- If you need to have a timestamp (or a monotonically increasing value) in the primary key
		- Have it as a second part (have something else as the first part)
		- Example: Use (UserId, LastAccess) as primary key instead of (LastAcess, UserId)
- Accessing **co-located rows is faster** than accessing rows distributed across multiple nodes
	- Make sure that related data is co-located (interleaved tables)
- Use stale reads (as compared to strong read) when your application is latency sensitive but can live with a bit of stale data
- Have Compute Instance(s) and Cloud Spanner instance in the same region
	- Provides the best performance
- Ensure that high priority total CPU utilization always remains under 65%
	- Increase number of nodes as needed
- Use Batch DML to send multiple DMLs in a single request