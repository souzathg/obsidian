Google Cloud's Serverless CI/CD Platform
- Deploy to multiple clouds
## Typical steps in a Cloud Build workflow
- **Build**: Build a deployable artifact (jar, war or docker image) for your application - Java, Go, Node.js, Python or
	- Pickup code from Cloud Source Repositories, GitHub, Bitbucket
	- Push images to Container Registry
- **Test**: Run automated unit tests or system tests
- **Static Analysis**: Perform static analysis (coding standards, security standards, etc) of your code
- **Deploy**: Deploy to VMs, GKE, App Engine, Cloud Functions, Cloud Run, etc.
## How does Cloud Build work?
### Trigger
- Push to a branch
- New Pub/Sub message
- Manual invocation
### Configuration: 2 Options
1. Cloud Build configuration file (YAML or JSON)
	1. Build file name: Default - cloudbuild.yaml
	2. Or choose your custom build file name in the trigger
	3. Also present: Option to write inline YAML
2. Dockerfile
	1. Cloud Build will automatically build a Docker Image and push it to Artifact Registry
## Understanding Build Steps
```yaml 
steps:
- name: first
- name: second
- name: third
```
**Build configuration**: Define the tasks that should be done in Cloud Build
Each build is a series of build steps
- Each build step is run in a separate Docker container
By default, all steps are executed one after the other!
You have **flexibility to customize**:
- Builder image for each step
- Order of step execution (and parallelization)
## Controlling Order of Cloud Build Steps
```yaml
steps:
- name: first
  id: A
- name: second
  id: B
  waitFor: ['-']
- name: third
  waitFor: B
- name: fourth
```
Default: If `waitFor` is not specified, a step waits for all the previously defined steps to complete
- If you specify `waitFor: ['-']` the step will start immediately  at the start of the build
- `waitFor: SPECIFIC-ID` starts after the execution of specific step
	- Step third starts only after second step completes execution
## Choosing Builder Image for Each Cloud Build Step
Choose between:
- Google Cloud Build official builder images:
	- curl, docker, gcs-fetcher, git, gke-deploy, go, gradle, gsuitl, kubectl, mvn, npm, wget, yarn...
- Google Cloud Build [community images](https://github.com/GoogleCloudPlatform/cloud-builders-community)
	- To use one of theser, you must first build the image and push it to Container Registry in your project
- Create your cusom builder
	- When you want to perform a task (or use a build tool) not supported by official or community builders
	- Create a Dockerfile, build the image and push it to Container Registry in your project
## Understanding Flexibility of Cloud Build Configuration
- Important Configuration Options
	- **env**: Environment variables
	- **dir**: Working directory (`/workspace/<dir>`)
	- **timeout**: Max execution time for the step
	- **waitFor**: Which steps should complete before this step is executed?
	- **logsBucket**: Which Cloud Stroage bucket should logs be written to?
	- **machineType**: Default machine type is 1 CPU
	- **logging**: Where should your logs go to?
		- **Default**: both Cloud Logging and Cloud Storage
		- **GCS_ONLY**: Store only to Cloud Storage
	- **images**: Docker images to be pushed
	- **artifacts**: Non-container artifacts to store in Cloud Storage
	- **serviceAccount**: Use a custom service account
## Exploring Cloud Build Configuration Substitutions
Some of the supported substitutions are:
- **$PROJECT_ID**: ID of your Cloud project
- **$BUILD_ID**: ID of your build
- For trigger based builds:
	- **$TRIGGER_NAME**: the name associated with your build
	- **$COMMIT_SHA**: the commit ID associated with your build
	- **$REVISION_ID**: the revision ID associated with your build
	- **$REPO_NAME**: the name of your repository
	- **$BRANCH_NAME**: the name of your branch
	- **$TAG_NAME**: the name of your tag

You can define your own substitutions: 
```yaml
substitutions:
	_NODE_VERSION_1: v6.9.1
	_NODE_VERSION_2: v6.9.2
```
It can be used like this: `node_version=${_NODE_VERSION_1}`
**REMEMBER:** User defined substitutions must begin with an underscore!
## Sharing Files between Cloud Build Steps
Containers are discarded after each step
What if you want to share files between steps?
Source for a build execution is mounted under the directory: `/workspace`
Use `/workspace` folder to share files between build steps:
- Files created in `/workspace` are available to next build steps
- (ALTERNATIVE) Create and use Docker Volumes
## Managing Permissions for Cloud Build Service Account
Service account used to execute your Cloud Build builds:
- Email: <PROJECT_NUMBER>@cloudbuild.gserviceaccount.com
Its default permissions are a collection of permissions to ensure monitoring of the build, trigger based on commits, push updates to Pub/Sub, among others
You can add additional permissions on the Cloud Build Settings page
- Give access to Cloud Functions, Cloud Run, App Engine, Kubernetes Engine, Compute Engine, Cloud KMS, Secret Manager, etc.
## Deploying to GKE
1. Build Container Image for your app
2. Push Container image to Artifact Registry
3. Deploy to Kubernetes Cluster
	1. **kubernetes-resource-file**: K8s resource file or folder path with resource files
	2. **cluster**: which cluster do you want to deploy to?
	3. **location**: which zone/region is your cluster in?
	4. Ensure that Kubernetes Engine Developer role is Enabled in Cloud Build Settings
## Best Practices
- Use/build lean container images (builder/application)
	- You ned to pay for storage and it takes time to pull and push images
- When a cached image can be used for subsequent builds, use `--cache-from` argument (only for Docker images)
- **Use GCS to cache files** (other than container images) between builds
	- Recommended when your build takes a long time and produces a small number of files (copying to/from GCS does not take long time)
- **Use custom virtual machine sizes** to increase speed of your builds
- Consider **Kaniko cache** for caching build artifacts
	- Stores and indexes intermediate layers within a container image registry
- Exclude files not needed for build by creating a `.gcloudignore` file
### Features to remember
- You can build custom VM images using Packer
	- Use Packer community builder image
- You can schedule builds
	- Create a build with manual trigger
	- Scheduler it usign Cloud Scheduler
- Run your build locally using the `cloud-build-local` tool