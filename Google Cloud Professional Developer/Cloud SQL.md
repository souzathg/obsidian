# Cloud SQL
Fully Managed Relation Database service 
Configure your needs and do NOT worry about managing the database.  
Supports MySQL, PostgreSQL and SQL Server  
Regional Service providing High Availability (99.95%)  
Use SSDs or HDDs (for best performance: use SSDs)  
Up to 416 GB of RAM and 30 TB of data storage  

**Use Cloud SQL for simple relational use cases**
- To migrate local MySQL, PostgreSQL and SQL Server databases
- To reduce your maintenence cost for a simple relational database
- [REMEMBER] Use Cloud Spanner (which is very expensive compared to Cloud SQL) instead of Cloud SQL if:
  - You have huge volumes of relational data (TBs);
  - You need infinite scaling for a growing application (to TBs);
  - You need a Global (distirbuted across multiple regions) Database;
  - You need higher availability (99.999%)

## Playing with Cloud SQL
Connecting to an instance via Cloud Shell or `gcloud`:
```bash
gcloud sql connect INSTANCE_NAME --user=root --quiet
```

Creating and querying a table in MySQL:
```sql
CREATE table user (id integer, username varchar(30));
DESCRIBE user;
INSERT INTO user values (1, 'Thiago');
SELECT * FROM user;
```

## Cloud SQL Features
- Automatic encryption, maintenance and updates
- High availability and failover:
  - Create a standby with automatic failover
  - Pre requisites: Automated backups and Binary logging
- Read replicas for read workloads
  - Options: Cross-zone, Cross-region and External (NON Cloud SQL DB)
  - Pre requisites: Automated backups and Binary logging
- Automatic storage increase without downtime (for newer versions)
- Point-in-time recovery: Enable binary logging
- Backups (Automated and on-demand backups)
- Supports migrations from other sources
  - Use Database Migration Service (DMS)
- You can export data from UI (console) or gcloud with formats:
  - SQL and CSV

## High Availability
- Create a High Availability (HA) Configuration
  - Choose Primary and Secondary zones within a region
  - You will have two instances: Primary and Secondary instances
- Changes from primary are replicated **synchronously** to secondary
- In case of **Zonal** failure, automatic failover to secondary instance:
  - If **Primary zone** becomes available, failover does not rever automatically
- [REMEBER] **High Availability** setup CANNOT be used as a Read Replica
