## Resource Hierarchy in GCP
In GCP, we have a well defined hierarchy:
- Organization > Folder > Project > Resources
Resources are created in projects, a folder can contain multiple projects, and an organization can contain multiple folders.
### Recommendation to Enterprises
- Create separate projects for differente environments:
	- Complete isolation between test and proction environments
- Create separate folders for each department
	- Isolate production application of one department from another
	- We can create a shared folder for shared resources
- One project per application (system) per environment
	- Let's consider two apps: "A1" and "A2"
	- Let's assume we need two environments: "DEV" and "PROD"
	- In the ideal world you will create four projects: A1-DEV, A1-PROD, A2-DEV, A2-PROD:
		- Isolates changes environments from each other;
		- DEV changes will NOT break PROD;
		- Grant all developers complete access (create, delete, deploy) to DEV projects
		- Provide production access to operations teams only!
## Billing Accounts
A billing account is mandatory for creating resources in a project:
- Billing Account contains the payment details.
- Every Project with active resources should be associated with a Billing Account
Billing Account can be associated with one or more projects
### Recommendations
- Create billing accounts representing your organization structure:
	- A startup can have just one Billing Account
	- A large enterprise can have a separate billing account for each department
### Types
- **Self Serve**: Billed directly to Credit Card or Bank Account
- **Invoiced**: Generate invoices (Used by large enterprises)
### Managing Billing
Setup a Cloud Billing Budget to avoid surprises:
- Configure **Alerts**
- Default alert thresholds set at 50%, 90% and 100%
	- Send alerts to PubSub (optional)
	- Billing admins and Billing Account users are alerted by e-mail
Billing data can be exported (on a schedule) to:
- Big Query (if you want to query information or visualize it)
- Cloud Storage (for history/archiving)
## IAM Best Practices
### Principle of Least Privilege
Give least possible privilege needed for a role
- Basic Roles are NOT recommended
	- Prefer predefined roles when possible
- Use Service Accounts with minimum privileges
	- Use different Service Accounts for different apps/purposes
### Separation of Duties
Involve atlest 2 people in sensitive tasks:
- Example: Have separate deployer and traffic migrator roles
	- AppEngine provides App Engine Deployer and App Engine Service Admin roles
		- App Engine Deployer can deploy new version but cannot shift traffic
		- App Engine Service Admin can shift traffic but cannot deploy new version!
### Constant Monitoring
Review Cloud Audit Logs to audit changes to IAM policies and access to Service Account keys
- Archive Cloud Audit Logs in Cloud Storage buckets for long term retention
### Use Groups when possible
Imagine you have a team of developers that needs some permissions. Create a group and assign the role to the group. If a new developer comes in, just add him/her to the group. If a developer leaves, just remove them from the group.
Grouping makes managing roles and users easier.

## User Identity Management in Google Cloud
- Email used to create free trial account is a **Super Admin**
	- It has access to everything in your GCP organization, folders and projects
	- Manage access to other users using their Gmail accounts
- However, this is NOT recommended for enterprises
### Options for Enterprises
- Your Enterprises is using Google Workspace
	- Use Google Workspace to manage users (groups, etc)
	- Link Google Cloud Organization with Google Workspace
- Your Enterprise uses an Identity Provider of its own
	- Federate Google Cloud with your Identity Provider
### Corporate Directory Federation
Federate Cloud Identity or Google Workspace with your external identity provider (IdP) such as Active Directory or Azure Active Directory.
Enable Single Sign On
1. Users are redirected to an external IdP to authenticate
2. When users are authenticated, SAML assertion is sent to Google Sign-In
Examples:
- Federate Active Directory with Cloud Identity by using Google Cloud Directory Sync (GCDS) and Active Directory Federation Services (AD FS)
- Federation Azure AD with Cloud Identity
### IAM Members or Identities
- Google Account
	- Represents a person (an email address)
- Service Account
	- Represents an application account (not person)
- Google Group
	- Collection of Google and Service Accounts
	- Has an unique email address
	- Helps to apply access policy to a group
- Google Workspace domain
- Cloud Identity domain
	- Cloud Identity is an Identity as a Service (IDaaS)