## Microservices at Scale using Kubernetes and AWS
This project focuses on deploying and managing microservices at scale using Kubernetes on AWS. The goal is to build a resilient, scalable, and highly available architecture that can support microservices in a cloud environment.

### Dependencies:
#### Local Environment
- Python Environment - run Python 3.6+ applications and install Python dependencies via `pip`
- Docker CLI - build and run Docker images locally
- `kubectl` - run commands against a Kubernetes cluster
- `helm` - apply Helm Charts to a Kubernetes cluster

#### Remote Resources
- AWS CodeBuild - build Docker images remotely
- AWS ECR - host Docker images
- Kubernetes Environment with AWS EKS - run applications in k8s
- AWS CloudWatch - monitor activity and logs in EKS
- GitHub - pull and clone code
- Docker - containerize the app

### Project Objectives:
- Set up a Postgres database with a Helm Chart
- Create a `Dockerfile` for the Python application. Use a base image that is Python-based.
- Write a simple build pipeline with AWS CodeBuild to build and push a Docker image into AWS ECR
- Create a service and deployment using Kubernetes configuration files to deploy the application
- Check AWS CloudWatch for application logs

### Deploying an Analytics API as a Microservice to Kubernetes using AWS:

1. Set up a private repository using AWS Elastic Container Registry (ECR) to store Docker images of the application, which are pushed from the AWS CodeBuild project.
   
2. Set up a build pipeline using AWS CodeBuild and grant the necessary ECR permissions to ensure the build job can successfully push the Docker image to the Elastic Container Registry (ECR). The pipeline will be triggered by a webhook event when a "pull_request_merged" action occurs in the specified GitHub repository containing the application code. In this scenario, the `analytics-api` folder should exist as an independent repository. The `buildspec.yml` file is essential in AWS CodeBuild projects, as it defines the commands and settings required to build, test, and package your application.
   
3. I then set up a cluster using Amazon Elastic Kubernetes Service (EKS) with a node group consisting of 2 nodes running Amazon Linux 2 (ARM64), with m6g.large instance types and 20 GiB disk sizes. These hardware and software components are well-suited for the microservice's workload. Additionally, the ability to scale up or down offers flexibility to meet evolving business requirements.

4. Establish communication between the AWS EKS service and the Visual Studio terminal to access and work on the created cluster. From the Visual Studio workspace terminal, run the command:
   > aws eks update-kubeconfig --name "your-cluster-name" --region "region"

5. I configured a database for this microservice using Helm Charts as follows:

   Add the Bitnami repository:
   > helm repo add bitnami https://charts.bitnami.com/bitnami

   Install the PostgreSQL Helm Chart:
   > helm install analyticsapi bitnami/postgresql --set primary.persistence.enabled=false

- This sets up a PostgreSQL deployment at <SERVICE_NAME>-postgresql.default.svc.cluster.local in your Kubernetes cluster. You can verify it by running kubectl get svc.

- Follow the output instructions from the second command for multiple ways to connect to the database, either from within or outside the cluster.

- Finally, run the seed files located in the `db/` directory in numerical order to create the tables and populate them with data.

**At this stage, your database service should be running and accessible by the deployed container hosting the analytics-api. This microservice operates as a pod within a Kubernetes cluster, with the ability to be automatically destroyed and redeployed whenever a code merge occurs in the GitHub repository. To ensure high availability, a Load Balancer has been deployed to direct traffic to the pod running the analytics-api microservice.** 

For troubleshooting, logs from the container or application running as a Kubernetes pod are sent to a log group that can be accessed through AWS CloudWatch under Log Groups.

#### Best Practices
* Dockerfile uses an appropriate base image for the application being deployed. Complex commands in the Dockerfile include a comment describing what it is doing.
* The Docker images use semantic versioning with three numbers separated by dots, e.g. `1.2.1` and versioning is visible in the  screenshot. See [Semantic Versioning](https://semver.org/) for more details.
