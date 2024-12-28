## Software Development Life Cycle (SDLC) evolution
### Waterfall
1. Requirements
2. Design
3. Implementation
4. Testing
5. Deployment
Takes a very long time between requirement phase and deployment. Feedback loop is very slow.

### Spiral
- Start
- Iteration 1
- Iteration 2
- ...
- Iteration N
- Product

### Agile
Agile added principles to the spiral development mode.
- Individuals and interactions over processes and tools.
- Working software over comprehensive documentation.
- Customer collaboration over contract negotiation.
- Responding to change over following a plan.
 
 **Now there are [12 principles](https://agilemanifesto.org/principles.html)**
- Agile is recommended for most software development, but some rigidity from waterfall model is good for critical safety software (Flight navigation software, Medical devices software, etc)

## What is DevOps?
**BUSINESS --- DEVELOPMENT --- OPERATIONS**
Getting better at **Three Elements of Great Software Teams**
- Communication: get teams together
- Feedback: Earlier you find a problem, easier it is to fix
- Automation: Automate testing, infrastructure provisioning, deployment, and monitoring

## DevOps Practices: CI, CD
- **Continuous Integration (CI)**: continuously run your tests and packaging
	- Run unit tests
	- Run integration tests
	- Build docker image, for example
- **Continuous Deployment (CD)**: continuously deploy to environments

### Recommended things to do
#### Static Code Analysis
- Lint, Sonar
- Static Security Checks
#### Runtime Checks
- Run vulnerability scanners
#### Tests
- Unit Tests (JUnit, pytest, Jasmine, etc)
- Integration Tests (Selenium, Robot Framework, Cucumber, etc)
- System Tests (Selenium, Robot Framework, Cucumber, etc)
- Sanity and Regression Tests

## Google's CI and CD Tools
- **Container Registry**: store your docker images
- **Jenkins**: Continuous Integrations
- **Cloud Build**: Build deployable artifacts (jars or docker images) from your source code and configuration
- **Spinnaker**: Multi-cloud continuous delivery platform

## Infrastructure as Code (IaC)
Treat infrastructure **the same way as application code**
Track your infrastructure **changes over time** (version control)
Bring **repeatability** into your infrastructure
Two key parts:
- Infrastructure provisioning
	- Provisioning compute, database, storage and networking
	- Open source cloud neutral - *Terraform*
	- GCP Service - *Google Cloud Deployment Manager*
- Configuration Management
	- Install right software and tools on the provisioned resources
	- Open source tools: *Chef*, *Puppet*, *Ansible* and *SaltStack*

## Google Cloud Deployment Manager
Let's consider an example:
- I want to create a new VPC and a subnet
- I want to provision a Load-Balancer, Instance groups with 5 Compute Engine instances and an Cloud SQL database in the subnet
- I want to setup the right Firewall
- And I want to create 4 environments
	- Dev, QA, Stage and Production

Deployment Manager can help you do all these with a script.
**Advantages**
- Automate deployment and modification of Google Cloud resources with control and predictability.
- Avoid configuration drift
- Avoid mistakes with manual configuration
**IMPORTANT:** always modify the resources created by deployment manager using deployment manager

All configuration is defined in a simple YAML file.
Deployment Manager understands dependencies.
Automatic rollbacks on errors (easier to retry).
Free to use - Pay only for the resources provisioned.
- Bonus: get an automated estimate for your configuration

### Terminology
- Configuration file: YAML file with resource definition of a single deployment
- Templates: Reusable resource definitions that can be used in multiple configuration files
	- Can be defined using Python or JinJa2
- Deployment: Collection of resources that are deployed and managed together
- Manifests: Read-only object containing original deployment configuration

## Container Registry and Artifact Registry
When you've created docker images for your micro-services you can store them in Container Registry and Artifact Registry.
- Container registry uses GCS bucket to store images and support only Container images only
	- Permissions are managed by managing access to GCS buckets
- Artifact Registry: evolution of Container Registry. Manages both container and non-container artifacts.
### Artifact Registry
Support multiple artifact formats.
Does NOT use GCS buckets
Repositories can be regional or multi-regional.
Access is controlled by using Artifact Registry Roles: Artifact Registry Reader, Writer, Administrator, etc...

## Site Reliability Engineering (SRE)
- DevOps++ at Google
- SRE teams focus on every aspect of an application: availability, latency, performance, efficiency, change management, monitoring, emergency response, and capacity planning
### Key Principles
- Manage by Service Level Objectives (SLOs)
- Minimize Toil (manual work that you do)
- Move fast by reducing cost of failure
- Share ownership with developers
### Key Metrics
- Service Level Indicator (SLI): Quantitative measure of an aspect of a service.
	- Availability, latency, throughput, durability, correctness (error rate)
	- Typically aggregated: "Over 1 minute"
- Service Level Objective (SLO): SLI + target
	- 99,99% availability, 99.99999% durability.
	- Response time: p99 - 1 second
	- Choosing an appropriate SLO is complex
- Service Level Agreement (SLA): SLO + consequences (contract)
	- What is the consequence of NOT meeting an SLO? (defined in contract)
	- Have stricter internal SLOs than external SLAs
- Error budgets: (100% SLO)
	- How well is a team meeting their reliability objectives?
	- Used to manage development velocity
### Best Practices
#### Handling Excess Loads
- Load Shedding
	- API Limits
		- Different SLAs for different customers
	- Streaming Data
		- If you are aggregating time series stream data, in some scenarios, you can drop a part of data
- Reduced Quality of Service
	- Instead of talking to a recommendations API, return a hard-coded set of products!
	- Not always possible:
		- Example: if you are making a payment
#### Avoiding Cascading Failures
- Plan to avoid thrashing
	- Circuit Breaker
	- Reduced Quality of Service
#### Penetration Testing (Ethical Hacking)
- Simulate an attack with objective of finding security vulneratibilites
- Should be authorized by project owners
- No need to inform Google
	- Ensure you are only testing your projects and are in compliance with terms of service!
- Can be white or black box
#### Load testing (JMeter, K6, Gatling...)
- Simulate real world traffic as closely as possible
- Test for spiky traffic - suddenly increases in traffic
#### Resilience Testing
"How does an application behaves under stress?"
**Resilience**: Ability of system to proveide acceptable behavior even when one or more parts of the system fail
- Approaches:
	- Chaos Testing: cause one or more layers to fail
	- Add huge stress on one of the layers
	- **Include network in your testing** (VPN, Cloud Interconnect, etc...)
-  DiRT: Disaster Recovery Testing at Google
	- Plan and execute outages for a defined period of time