# Choosing Databases in Google Cloud

## Database Categories
- Relational (OLTP and OLAP)
- Document
- Key Value (KV)
- Graph
- In Memory
- Vector
Among others

Choosing type of database for your use case is not easy. A few factors: 
- Do you want a fixed schema or do you want flexibility in defining and changing your schema? (schamaless)
- What level of transaction properties do you need? (atomicity and consistency)
- What kind of latency do you want? (seconds, milliseconds or microseconds)
- How many transactions do you expect? (hundreds or thousand or millions of transactions per second)
- How much data will be stored? (MBs, GBs, TBs, or PBs)

## Relational Databases
This was the only option until a decade back. Most popular type of databases.  
Predefined schema with tables and relationships.
Very strong transactional capabilities. You can make updates to multiple tables in a single transaction.
Used for:
  - OLTP (Online Transaction Processing) use cases
  - OLAP (Online Analytics Processing) use cases

### Relational Databases in GCP (OLTP - Online Transaction Processing)
Application where large number of users make large number of small transcations
- Small data read, updates and deletes

Use cases: Most traditional applications, ERP, CRM, e-commerce, banking applications

#### Popular databases
- MySQL
- Oracle
- SQL Server
- PostgresSQL

#### Recommended Google Managed Services
- [[Cloud SQL]]: Supports PostgreSQL, MySQL and SQL Server for regional relational databases (up to a few TBs)
- [[Cloud Spanner]]: Unlimited scale (multiple PBs) and 99.999% availability for global applications with horizontal scaling

### Relational Databases in GCP (OLAP - Online Analytics Processing)
Applications allowing users to analyze petabytes of data  
Examples: Reporting applications, Data warehouses, Business intelligence applications, Analytics systems  
**Sample application**: Decide insurence premiums analyzing data from last hundred years. Data ins consolidated from multiple (transactional) databases.  

#### Recommended GCP Managed Service
- BigQuery: Petabyte-scale distributed data warehouse.

### OLAP vs OLTP
They use similar data structures but they use a very different approach in how data is stored.  
- OLTP databases use row storage
  - Each table row is stored together
  - Efficient for processing small transactions
- OLAP databases use columnar storage
  - Each table column is stored together
  - **High compression**. Store petabytes of data efficiently
  - **Distribute data**. One table in multiple cluster nodes
  - **Execute single query across multiple nodes**: complex queries can be executed efficiently

## NoSQL Databases
New approach to building your databases. NoSQL = Not only SQL.  
Flexible schema
- Structure data the way your application needs it
- Let the schema evolve with time
Horizontally scalte to petabytes of data with millions of TPS
*Not a 100% accurate generalization but a great starting point:*
- Typical NoSQL databases trade-off "Strong consistency and SQL features" to achieve "scalability and high-performance"

### Google Managed Services
- [[Cloud Firestore]] (Datastore)
- Cloud BigTable

### Cloud Firestore vs Cloud BigTable

#### Firestore
- Cloud Datastore is a managed serverless NoSQL document database
  - Provides ACID transactions, SQL-like querires, indexes
    - Designed for transactional mobile and web applications

- Firestore (next version of Datastore) adds:
  - Strong consistency
  - Mobile and Web client libraries

- Recommended for small to medium databases (to a few TBs)

#### Cloud BigTable
- Cloud Big table is a managed, scalable NoSQL wide column database
  - Not serverless (you need to create instances)
  - Recommended for data size: > 10 TB to several PB
  - Recommended for large analytical and operational workloads
    - Not recommended for transactional workloads (Does not sipport multi row transactions, only single-row transactions)

## In-memory Databases
Retrieving data from memory is much faster than retrieving data from disk. In-memory databases like Redis deliver microsecond latency by storing persistent data in memory.  

### Recommended GCP Managed Service
- Cloud Memorystore

Use cases: caching, session management, gaming leader boards, geospatial applications
