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

**1.** Created and setup a repository using the **AWS Elastics Container Registry** for storing **docker** images of the application pushed from the AWS CodeBuild project.

**2.** Setup a build project pipeline using **AWS Codebuild** with relevant ECR role data attached to it so that the build job can successfuly push the build image to a registry. 
The project is triggered by webhook event "pull_request_merged" in the selected ready github project repository.

**3.** I have then Setup a cluster using **Amazon Elastic Kubernetes Service** with a node group which consists of **2 nodes of Amazon Linux 2 (arm64) with Instance type m6g.large and 20GiB disk size.** 
This are suitable hardware and software components for this application, considering its workload. 
Furthermore, scaling up/down can be advantageous and beneficial as per business requirements/needs.

**4.** Initialize communication between the AWS EKS service and the VS Studio termianal in order to be able to access and work on the created cluster.
From the VS Studios Workspace terminal by running > **aws eks update-kubeconfig --name <cluster-name> --region <region>**

**5.** I have configured a database for this Microservice using Helm Charts as follows:
 - > **helm repo add bitnami https://charts.bitnami.com/bitnami** 
- > **helm install analyticsapi bitnami/postgresql --set primary.persistence.enabled=false** 
- First command creates a repository for this database service. 
- Then the second command installs the PostgreSQL Helm Chart.
- This should set up a Postgre deployment at `<SERVICE_NAME>-postgresql.default.svc.cluster.local` in your Kubernetes cluster. You can verify it by running `kubectl svc`
- Followed command two output instructions on multiple ways to connect to the database from within or outside the cluster
- We will need to run the seed files in `db/` in order to create the tables and populate them with data.

**6.** Therefore, I have the database service running which can be accessed by the deployed container running an image of the business analysts api.
This Microservice is now running as a pod in a Kubernetes cluster and it can be destroyed and redeployed automatically whenever a merged code occurs in a GitHub repo.
For high availabilty purposes, I have also deployed a Load Balancer targeting the pod running the container/image of the Business Analyts application.
For troubleshooting purposes, the logs for the container/application running as a kubernetes pod are directed to a log group which can be accessed using from AWS CloudWatch, under log groups.

#### Best Practices
* Dockerfile uses an appropriate base image for the application being deployed. Complex commands in the Dockerfile include a comment describing what it is doing.
* The Docker images use semantic versioning with three numbers separated by dots, e.g. `1.2.1` and versioning is visible in the  screenshot. See [Semantic Versioning](https://semver.org/) for more details.
