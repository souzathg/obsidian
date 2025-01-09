# Asynchronous Communication in Google Cloud
## Need for Asynchronous Communication
- Why do we need asynchronous communication?
### Synchronous Communication
Imagine that you have a Web Server that make synchronous calls to the logging service. What if your logging service goes down?
- Will you applications go down too?
- What is all of sudden, there is high load and there are lot of logs coming in?
	- Log Service is not able to handle the load and goes down very often

### Asynchronous Communication - Decoupled
- Create a topic and have your applications put log messages on the topic
- Logging service picks them up for processing when ready
- Advantages:
	- Decoupling: Publisher (Apps) don't care about who is listening
	- Availability: Publisher (Apps) up even if a subscriber (Logging Service) is down
	- Scalability: Scale consumer instances (Logging Service) under high load
	- Durability: Message is not lost even if subscriber (Logging Service) is down

## Pub/Sub
- Reliable, scalable, fully-managed asynchronous messaging service
- Backbone for **highly available** and **highly scalable solutions**
	- Auto scale to process billions of messages per day
	- Low cost (pay for use)
- Use cases: Event ingestion and delivery for streaming analytics pipelines
- Supports push and pull message deliveries
### How does it work?
#### Publisher
Sender of a message.
- Publishers send messages by making HTTPS requests to pubsub.googleapis.com
#### Subscriber
Receiver of the message
- **Pull**: Subscriber pulls messages when ready
	- Subscriber makes HTTPS requests to pubsub.googleapis.com
- **Push**: messages are sent to subscribers
	- Subscriber provide a web hook endpoint at the time of registration
	- When a message is received on the topic, a HTTPS POST request is sent to the web hook endpoints
#### Flexible Relationships between Publishers and Subscriber
- One to Many
- Many to One
- Many to Many
## Exploring Cloud Pub/Sub - Publishing and Consuming a Message
1. Create a topic
2. Create a subscription
	1. Subscribers register to the topic
	2. Each Subscription represents discrete pull of messages from a topic:
		1. Multiple client pull same subscription => messages split between clients
		2. Multiple client create a subscription each => each client will get every message
3. Publisher sends a message to topic
4. Message **individually** delivery to each and every subscription
	1. Subscribers can receive message either by **push** or **pull**
5. Subscribers send acknowledgements (ack)
6. Messages are removed from subscription message queue
	1. Pub/Sub ensures the message is retained **per subscription** until it is acknowledged
## Cloud Tasks
- Manage execution, dispatch, and delivery of a large number of distributed tasks asynchronously
- Supports tasks targeting any HTTP service running on:
	- Compute Engine
	- Google Kubernetes Engine
	- Cloud Run
	- Cloud Functions
	- On-premises systems
- Difference between Cloud Tasks and Pub/Sub
	- Pub/Sub - Implicit invocation
		- Pull and Push Models
	- Cloud Tasks - Explicit invocation
		- Only Push Model
- Prefer Cloud Tasks to schedule/asynchronously invoke backgroud task
## Cloud Scheduler
- Fully managed, enterprise-grade scheduler
	- Schedule all kinds of jobs
		- Batch, big data jobs, cloud infrastructure operations, etc
	- Uses Unix cron format
- Integrates with App Engine, Cloud Pub/Sub, Cloud Logging and any HTTP endpoint
- Manage all your automation tasks from one place
- Provides **automated retries**
- **Use Case**: Schedule tasks across a fleet of Compute Engine instances
	- Use Cloud Scheduler for scheduling a message on Pub/Sub
	- Compute Engine instances can process messages from Pub/Sub
- [REMEMBER] Needs an App Engine App in the Project
