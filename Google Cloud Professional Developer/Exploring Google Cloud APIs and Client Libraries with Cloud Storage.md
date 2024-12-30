# Exploring Google Cloud APIs and Client Libraries with Cloud Storage

## Designing APIs: gRPC vs REST
Two popular approaches: REST and gRPC

### REST
`POST /todos` (username, description) -> todo_URL
- Addressable entities - resources
- Create, delete and modify resources to create desired behavior

### gRPC
Based on Remote Procedure Call (RPC) model
- createTodo(username, description) -> todo_id
  - Addressable entities - procedures
- **Advantage: Hides HTTP details** from developers (uses HTTP/2 in background)
  - Generate gRPC stubs and skeletons 
  - Clients use stubs to make gRPC calls
- **Advantage**: Provides **very good performance** (sometimes up to 10x better)
  - **Use binary payload (more efficient)** - Protobuf (Protocol Buffers) is most commonly used 
  - Efficient management of connections (because of HTTP/2)
  - **REMEMBER:** REST can also use binary payload and HTTP/2 **BUT** these features are built into gRPC by default
- **Disadvantage**: REST API are easiear to consume
  - REST API does not need any special frameworks/library. Just do a cURL or execute a call to a URL from your code

## Exploring Client Libraries
GCP provides a number of services.  
Services can be accessed via console, gcloud, etc.  
How do you make programmatic calls to GCP services?
- Google Cloud APIs are provided for most GCP services
  1. Directly call them (supports JSON REST and gRPC); or
  2. Use client libraries which are provided by Google
    1. Google API Client Libraries
    2. Google Cloud Client Libraries

### Google API Client Libraries vs Google Cloud Client Libraries
- Google API Client Libraries: OLDER
  - Supports JSON REST interface
- Google Cloud Client Libraries: NEWER and RECOMMENDED
  - Available for MOST Google Cloud APIs
  - Supports JSON REST and gRPC
  - Provides better performance and usability

### Things to remember about Google Cloud APIs
- Enable you to make programmatic calls to GCP services
- **Need to be enabled before use**
- Accept only secure requests using TLS encryption
  - Automatically taken care of while using **Client Libraries**
- Google Cloud Console API Dashboard: Provides information about your API usage
  - traffic levels, error rates, and latencies
- [API Library](https://console.cloud.google.com/apis/library)

## Using Cloud Client Libraries (Cloud Storage)
1. Setup Cloud Client Libraries for the Google Cloud Storage API
  - Java: add to pom.xml or gradle dependencies
    - compile 'com.google.cloud:google-cloud-storage'
  - Node.js: npm install --save @google-cloud/storage
  - Python: pip install --upgrade google-cloud-storage
2. Setup Authentication
  - Create service account with the right permissions
  - Make your application use the Service Account
    - If your app is running in Google Cloud: Assign the service account to your compute (App Engine, Cloud Run, Compute Engine, Cloud Functions or GKE)
    - Or Create service-account key and make the service account key available as part of your GOOGLE_APPLICATION_CREDENTIALS
      - `export GOOGLE_APPLICATION_CREDENTIALS="PATH_TO_SERVICE_ACCOUNT_KEY_FILE"`
3. Write the code.
  - API Details:
    - [Java](https://googleapis.dev/java/google-cloud-storage/latest/index.html)
    - [Nodejs](https://googleapis.dev/nodejs/storage/latest/Storage.html)
    - [Python](https://googleapis.github.io/google-cloud-python/latest/storage/client.html)

## Cloud Storage - Flat namespace
Cloud Storage uses a flat namespace. That means internally there is **NO** folder structure.
| Key | Value |
| -------------- | --------------- |
| 2030/course0.png | image-binary-content |
| 2030/10/course1.png | image-binary-content |
| 2030/10/course2.png | image-binary-content |
| 2030/11/course2.png | image-binary-content |
| 2030/11/course3.png | image-binary-content |

How is an empty folder represented?  
- A zero byte object is created as a placeholder

Via `gsutil`: `gsutil cp empty-file gs://your-bucket/empty-folder`

