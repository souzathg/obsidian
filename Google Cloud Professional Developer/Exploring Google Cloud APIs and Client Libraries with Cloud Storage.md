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
