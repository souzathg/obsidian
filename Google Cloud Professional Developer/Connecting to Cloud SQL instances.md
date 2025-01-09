# Connecting to Cloud SQL instances

## Getting Started

How do you simplify connection management and ensure secure connection (HTTPS) between clients and Cloud SQL?

- Cloud SQL Auth proxy  

Communication encrypted with TLS 1.2 with a 128-bit AES cipher

- Do not worry about certificates

Easier connection management
Needs installation of Proxy Client on the client machine

- Can be used to connect from local environment

Works with both private and public IP addresses

Cloud SQL Auth Proxy is automatically supported on some GCP services

- Example: App Engine Standard, Cloud Run, etc...

## Configuring Cloud SQL Auth Proxy

- Enable Cloud SQL Admin API
- Install Cloud SQL Auth Proxy on Client

```bash
wget https://dl.google.com/cloudsql/cloud_sql_proxy.linux.amd64 -O cloud_sql_proxy
```

- Start Cloud SQL Auth Proxy
- Give execute permission to the script

```bash
chmod +x ./cloud_sql_proxy
```

- Run Cloud SQL Auth Proxy script

```bash
./cloud_sql_proxy -instances=INSTANCE_CONN_NAME=tcp:3306
```

On GKE, you can run Cloud SQL Auth Proxy as a side car container.  
On AppEngine and Cloud Run, you can enable it with simple configuration.

- Connect to the Cloud SQL Auth Proxy (using the local IP)

```bash
mysql -u USERNAME -p --host 127.0.0.1
```

## Configuring Authorization for Cloud SQL Auth Proxy

Cloud SQL Auth Proxy needs to connect to Cloud SQL:

- **Recommended Role**: Cloud SQL Client
  - Other Roles: Cloud SQL Editor or Cloud SQL Admin
  - Or, assign these IAM permissions manually to your role:
    - *cloudsql.instances.connect*, *cloudsql.instances.get*
- **Assign the role** to your compute env. service account
  - Or create a new service account (with key) and use it to launch Cloud SQL Auth Proxy. `./cloud_sql_proxy -instances=INSTANCE_CONN_NAME=tcp:3306 -credential_file=PATH_TO_KEY_FILE`

Your application needs to connect to the database

- Configure credentials in application config:
  - db_user = os.environ["DB_USER"]
  - db_pass = os.environ["DB_PASS"]
  - db_name = os.environ["DB_NAME"]
  - In Kubernetes, you can use Kubernetes Secrets for storing credentials

## Private Service Connection

How to connect from a VM instance **inside a VPC** to a Cloud SQL instance **using private address**?

- Create a "**Private Service Connection**"
- A **Private Connection** between your VPC and network owned by Google:
  - Enables VM instances in your VPC network to talk to GCP services using internal UP addresses.
  - Example: Connect from your VM instance to a Cloud SQL instance using private address
  - Examples of supported GCP Services: Memorystore, Tensorflow and Cloud SQL.

### Creating a Private Service Connection

1. Allocate IP address range for subnet
2. Create private conn. to service producer

## Serverless VPC Access

**REMEMBER**: Serverless environments **automatically** use Cloud SQL proxy when using public IP addresses!

- Example: Cloud Functions, Cloud Run and App Engine standard environment
- But things are different when using Cloud SQL private IP adresses
  - How do you connect from your serverless environments to a resource in a VPC network using private IPs? **Serverless VPC Access**

**Serverless VPC Access** allows Cloud Fcuntions, Cloud Run (fully managed) services and App Engine standard environment apps to access resources in a VPC network using those resources' private IPs

You can connect to these resrouces using internal IP addresses:

- GCE VMs
- Cloud SQL instances
- Cloud Memorystore instances
- Kubernetes Pods/Services (on GKE public or private clusters)
- Internal Load Balancers

## How to connect to Cloud SQL instance

- **Prefer private IP addresses** to public IP addresses
- Use Serverless VPC Access for private IP connections from Serverless Environments
  - App Engine, Cloud Functions, Cloud Run
- **Use Cloud SQL Auth proxy** for these scenarios:
  - Connecting with Public IP from Serverless Environments (App Engine, Cloud Functions, Cloud Run)
  - Connecting with Public / Private IPs from other environments (GCE VMs, GKE, etc)
- If you are NOT using Cloud SQL Auth proxy, it is recommended to use self managed SSL/TLS certificates

## Cloud SQL Best Practices

- Use Cloud SQL Proxy when possible
- Use connection pooling & exponential backoff
- Keep transactions short and small
- Use internal IP as much as possible
- Understand Scalability:
  - Enable HA configuration for high availability
    - Primary instance and a standby instance created in the same Region (Remember - Regional)
  - Read replicas help you offload read workloads (reporting, analytics, etc)
    - [REMEBER] Read replicas do NOT increase availability.
  - Prefer a number of small Cloud SQL instances to having one large instance
    - Cloud SQL connot scale horizontally for writes
