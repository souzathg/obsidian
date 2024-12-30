## Understanding the need for a Virtual Private Cloud
In a corporate network or an on-premises data center: Can anyone on the internet see the data exchange between the application and the database? **No**.
Can anyone from internet directly connect to your database? Typically **no**. You need to connect to your corporate network and then access your applications or databases.
Corporate network provides a secure internal network protecting your resources, data and communication from external users.
How do you create your own private network in the cloud? Google Cloud Virtual Private Cloud (VPC).
## Google Cloud VPC
Your own isolated network in GCP Cloud
Network traffic within a VPC is isolated (not visible) from all other Google Cloud VPCs.
You control all the traffic coming in and going outside a VPC
**BEST PRACTICE**: Create all your GCP resources (compute, storage, databases, etc) within a VPC
- Secure resources from unauthorized access;
- Enable secure communication between your cloud resources
VPC is a global resource and contains subnets in one or more region. **REMEMBER:** not tied to a region or a zone. VPC resources can be in any region or zone.
## Need for VPC Subnets
### Different types of resources are created on cloud - databases, compute, etc.
- Each type of resource has its own access needs
- Load Balancers are accessible from internet (**public** resources)
- Databases or VM instances should NOT be accessible from internet
	- Only applications within your network (VPC) should be able to access them (**private** resources)
### Separating public resources from private resources inside a VPC
- Create separate subnets! In the subnet where public resources are present, you can allow incoming traffic from the internet. In the subnet where there are private resources, you only allow traffic from the other subnets.
- Additional Reason: You want to distribute resources across multiple regions for high availability
- **Solution:** Create different subnets for public and private resources
	- Resources in public subnet **CAN** be accessed from internet
	- Resources in a private subnet **CANNOT** be accessed from internet
	- But resources in public subnet can communicate with resources in private subnet
- Each subnet is created in a region
	- Example: VPC - demo-vpc => Subnets - region us-central1, europe-west1 or us-west1
### Creating VPCs and Subnets
By default, every project has a default VPC.
You can create your own VPCs:
1. Auto mode VPC network:
	1. Subnets are automatically created in each region
	2. Default VPC created automatically in the project uses auto mode!
2. Custom mode VPC network:
	1. No subnets are automatically created
	2. You have complete control over subnets and their IP ranges
	3. Recommended for Production
Options when you create a subnet:
- Enable **Private Google Access** - Allows VMs to connect to Google APIs using private IPs
- Enable **FlowLogs** - To troubleshoot any VPC related network issues
## CIDR (Classless Inter-Domain Routing) Blocks
Resources in a network use continuous IP addresses to make routing easy:
- Example: Resources inside a specific network can use IP addresses from 69.208.0.0 to 69.208.0.15
How do you express a range of addresses that resources in a network can have? **CIDR Block**
- Example: CID bloc **69.208.0.0/28** represents addresses from 69.208.0.0 to 69.208.0.15. A total of 16 addresses.
- Quick tip: **/28** indicated that the first 28 bits (out of 32) are **fixed**. Last 4 bits can change, so $2^4 = 16$ 
- A good site to understand how this works is [CIDR.xyz](https://cidr.xyz).
## Firewall rules
Configure firewall rules to control traffic going in or out of the network
- Stateful, if incoming traffic is allowed, outgoing traffic is automatically allowed
- Each firewall rule has priority (0-65535) assigned to it
- 0 has highest priority, 65535 has least priority.
- Default implied rule with lowest priority (65353)
	- Allow all egress
	- Deny all ingress
	- Default rules can't be deleted
	- You can override default rules by defining new rules with priority 0-65534.
- Default VPC has 4 additional rules with priority 65534
	- Allow incoming traffic from VM instances in same network: **default-allow-internal**
	- Allow incoming TCP traffic on port 22 (SSH): **default-allow-ssh**
	- Allow incoming TCP traffic on port 3389 (RDP): **default-allow-rdp**
	- Allow incoming ICMP from any source on the network: **default-allow-icmp**
### Ingress and Egress Rules
Ingress Rules: incoming traffic from outside to GCP targets
- Target (defines the destination): All instances or instances with TAG/SA
- Source (defines where the traffic is coming from): CIDR or All instances/instances with TAG/SA.
Egress Rules: outgoing traffic to destination from GCP targets
- Target (defines the source): All instances or instances with TAG/SA
- Destination: CIDR Block
Along with each rule, you can also define:
- Priority - Lower the number, higher the priority
- Action on match - Allow or Deny traffic
- Protocol - TCP or UDP or ICMP
- Port - Which port?
- Enforcement status - Enable or disable the rule
## Shared VPC
Your organization has multiple projects. You want resources in different projects to talk to each other. How to allow this scenario to happen securely and efficiently? **Shared VPC**.
Created at organization or shared folder level (Access Needed: Shared VPC Admin)
Allows VPC network to be shared between projects in same organization
Shared VPC contains one host project and multiple service projects.
- Host project: Contains shared VPC network
- Service projects: Attached to host projects
Helps you achieve separation of concerns:
- Network administrators responsible for Host projects and Resource users use Service Project
## VPC Peering
How to connect VPC networks across different organizations? **VPC Peering**
Networks in same project, different projects and across projects in different organizations can be peered.
All communication happens using internal IP addresses.
- This is highly efficient because all communication happens inside Google network
- Highly secure because not accessible from Internet
- No data transfer charges for data transfer between services.
**Remember:** network administration is NOT changed. 
- Admin of one VPC do not get the role automatically in a peered network.
## Cloud VPN
Connect on-premise network to the GCP network
- Implemented using IPSec VPN Tunnel
- Traffic through internet (public)
- Traffic encrypted using **Internet Key Exchange** protocol
Two types of Cloud VPN solutions:
- High Availability VPN
	- Only support dynamic routing (BGN) supported
- Classic VPN
	- Supports static routing and dynamic routing
## Cloud Interconnect
High speed physical connection between on-premise and VPC networks
- High available and high throughput
- Two types of connections possible
	- Dedicated Interconnect - 10Gbps or 100Gbps configurations
	- Partner Interconnect - 50 Mbps to 10 Gbps configurations
Data exchange happens through a private network
- Communicate using VPC network's internal IP addresses from on-premise network
- Reduces egress costs
	- As public internet is NOT used
**Feature:** Supported Google APIs and services can be privately accessed from on-premise
Use only for high bandwidth needs. For low bandwidth, Cloud VPN is recommended.
## Direct Peering
Connect customer network to google network using network peering
- Direct patch from on-premises network to Google services.
NOT RECOMMENDED.