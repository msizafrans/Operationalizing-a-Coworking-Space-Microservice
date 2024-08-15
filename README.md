## Coworking Space Service Extension
The Coworking Space Service is a set of APIs that enables users to request one-time tokens and administrators to authorize access to a coworking space. This service follows a microservice pattern and the APIs are split into distinct services that can be deployed and managed independently of one another.

For this project, you are a DevOps engineer who will be collaborating with a team that is building an API for business analysts. The API provides business analysts basic analytics data on user activity in the service. The application they provide you functions as expected locally and you are expected to help build a pipeline to deploy it in Kubernetes.

## Dependencies
#### Local Environment
1. Python Environment - run Python 3.6+ applications and install Python dependencies via `pip`
2. Docker CLI - build and run Docker images locally
3. `kubectl` - run commands against a Kubernetes cluster
4. `helm` - apply Helm Charts to a Kubernetes cluster

#### Remote Resources
1. AWS CodeBuild - build Docker images remotely
2. AWS ECR - host Docker images
3. Kubernetes Environment with AWS EKS - run applications in k8s
4. AWS CloudWatch - monitor activity and logs in EKS
5. GitHub - pull and clone code

## Project Objectives:
1. Set up a Postgres database with a Helm Chart
2. Create a `Dockerfile` for the Python application. Use a base image that is Python-based.
3. Write a simple build pipeline with AWS CodeBuild to build and push a Docker image into AWS ECR
4. Create a service and deployment using Kubernetes configuration files to deploy the application
5. Check AWS CloudWatch for application logs

## Setup - Deploying a Business Analysts API as a Microservice to Kubernetes using AWS

#### How the project works:

**1.** Set up a private repository using **AWS Elastic Container Registry** (ECR) to store Docker images of the application, which are pushed from the AWS CodeBuild project.

**2.** Set up a build pipeline using **AWS CodeBuild**, attaching the necessary ECR role permissions to enable the build job to successfully push the **Docker** image to a registry. The pipeline is triggered by a webhook event when a "pull_request_merged" action occurs in the specified GitHub repository.

**3.** I then set up a cluster using **Amazon Elastic Kubernetes Service** (EKS) with a node group consisting of 2 nodes running Amazon Linux 2 (ARM64), with m6g.large instance types and 20 GiB disk sizes. These hardware and software components are well-suited for the application's workload. Additionally, the ability to scale up or down offers flexibility to meet evolving business requirements.

**4.** Establish communication between the AWS EKS service and the Visual Studio terminal to access and work on the created cluster. From the Visual Studio workspace terminal, run the command:
> aws eks update-kubeconfig --name <cluster-name> --region <region>

**5.** I configured a database for this microservice using Helm Charts as follows:

Add the Bitnami repository:
> helm repo add bitnami https://charts.bitnami.com/bitnami
Install the PostgreSQL Helm Chart:

Installs the PostgreSQL Helm Chart.
> helm install analyticsapi bitnami/postgresql --set primary.persistence.enabled=false

- This sets up a PostgreSQL deployment at <SERVICE_NAME>-postgresql.default.svc.cluster.local in your Kubernetes cluster. You can verify it by running kubectl get svc.

- Follow the output instructions from the second command for multiple ways to connect to the database, either from within or outside the cluster.

- Finally, Run the seed files located in the db/ directory to create the tables and populate them with data.

**6.** Therefore, I have the database service running which can be accessed by the deployed container running an image of the business analysts api.
This Microservice is now running as a pod in a Kubernetes cluster and it can be destroyed and redeployed automatically whenever a merged code occurs in a GitHub repo.
For high availabilty purposes, I have also deployed a Load Balancer targeting the pod running the container/image of the Business Analyts application.
For troubleshooting purposes, the logs for the container/application running as a kubernetes pod are directed to a log group which can be accessed using from AWS CloudWatch, under log groups.

#### Best Practices
* Dockerfile uses an appropriate base image for the application being deployed. Complex commands in the Dockerfile include a comment describing what it is doing.
* The Docker images use semantic versioning with three numbers separated by dots, e.g. `1.2.1` and versioning is visible in the  screenshot. See [Semantic Versioning](https://semver.org/) for more details.
