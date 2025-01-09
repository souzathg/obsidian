# Cloud Firestore
## Cloud Datastore (old version)
Highly scalable NoSQL Document Database

- Automatically scales and partitions data as it grows
- Recommended for up to a few TBs of data
	- For bigger volumes, BigTable is recommended
- Supports Transactions, Indexes and SQL like queries (GQL)
	- Does NOT support Joins or Aggregate (sum or count) operations
- For use cases needing flexible schema with transactions
	- Examples: User Profile and Product Catalogs
- Structure: Kind => Entity (Use namespaces to group entities)
- You can export data ONLY from gcloud (NOT from cloud console)
	- Export contains a metadata file and a folder with the data
## Cloud Firestore
It's **Datastore++**. Optimized for multi device access.

- Offline mode and data synchronization across multiple devices - mobile, IOT, etc...
- Provides cluent side libraries - Web, iOS, Android and more
- Offers Datastore and Native modes

## Exploring Cloud Firestore - Native mode vs Datastore mode
### Native mode
- RECOMMENDED for new mobile and web apps
- Provides Mobile and Web client libraries
- Real-time updates: Listen to real-time updates on a document
- Offline features: Important for mobile applications

### Datastore mode
- Recommended for new server projects
- Uses firestore's storage layer
- Provides Datastore system behavior with a few additions:
	- Strong consistency (by default)
- Does NOT provide real-time updates and offline features

[REMEMBER] You can use EITHER Native mode or Datastore mode in a single project

## Firestore locations - Regional vs Multi-region
Firestore supports Regional and Multi-region locations

### Regional
- Data is replicated in multiple zones in the same region
- Used when you want lower costs and/or lower write latency
- Availability SLA: 99.99%
### Multi-region
- Data is replicated in multiple regions (and multiple zones with each region)
- Used to maximize availability and durability
- Can withstand loss of a complete region
- Examples: eur3 (Europe), nam5 (United States)
- Availability SLA: 99.999%

## Using Indexes with Firestore

- Single-field indexes automatically created for every field in a document
	- If you do NOT want automatic index on a field, you can create **single-field index exemption**
	- RECOMMENDED: Exempt Large string fields (string descriptions) from indexing
- **Create composite index with multiple fields** as needed by your queries
- [REMEMBER] Indexes DON'T come FREE!
	- Indexes increase write latency and storage costs
	- RECOMMENDED: use indexes judiciously
		- Feel free to create single-field index exemptions if you are NOT going to query on a field
## Exploring Firestore Client Libraries
- How to build server side apps that talk to Cloud Firestore?
	- Cloud Firestore client libraries
		- **Java**: add to pom.xml or gradle dependencies. `compile 'com.google.cloud:google-cloud-firestore'`
		- **Node.js**: `npm install --save @google-cloud/firestore`
		- **Python**: `pip install --upgrade google-cloud-firestore`
	- Recommended: Use Service Account for providing access
- How do you build mobile applications that talk to Cloud Firestore?
	- **Firebase** is a Google platform for creating mobile applications
		- **Firebase Admin SDKs** come built-in with Google Cloud client libraries for Firestore
		- Use Firestore Admin SDKs to talk to Firestore
		- Use Firebase Authentication and Firestore Security Rules to secure your data
- [REMEMBER] Firestore SDKs & client libraries retry failed transactions
	- If your are using the REST or RPC APIs directly, enable your applications to retry failed transactions
## Exploring Transaction with Firestore
**Transaction**: set of atomic operations. Either all operation are successul or none of them are successful.
- Firestore supports two types of transactions:
	- **Read-write transaction**: When you want to perform write transactions
		- If you are doing single or multiple writes as a result of reads:
			- Recommended to do all the writes and reads in a Read-write transaction
	- **Read-only**: Provides consistency across multiple reads
		- Does not allow writes
- You can query or lookup any number of entities in a transaction
	- However a maximum of 500 entities can be created, updated, or deleted in a transaction
## Cloud Firestore Best Practices
- Cloud Firestore/Datastore is a document store with flexible schema
	- Recommended for storing things like user profiles
	- Another Use Case: Index for objects sotred in Cloud Storage
		- You want to allow users to upload their profile pictures:
			- Store objects (pictures) in Cloud Storage
			- Enable quick search by storing metadata (like ids and cloud storage bucket, object details) in Cloud Datastore
- Design your keys and indexes carefully
	- Distribute operations evenly across key range
	- Avoid monotonically increasing values as keys
		- NOT RECOMMENDED - 1, 2, 3, ..., OR "Customer1", "Customer2", ..., OR timestamps
		- RECOMMENDED - Use `allocateIds()` for well-distributed numeric IDs
- Create your database instance in the region nearest to your users and compute resources
- Prefer batch operations (to single read, write or delete operations):
	- More efficient as multiple operations are performed with same overhead as one operation
- Remeber **Firestore soft limits**: 1 write per second per document, 1 million concurrent connections per database
	- Exceeding these limits might affect performance
- **Gradually ramp up traffic** to a new collection: "500/50/5" Rule
	- Start with MAX 500 operations per second
	- Increase by 50% every 5 minutes