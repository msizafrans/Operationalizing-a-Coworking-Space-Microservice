Deploying a Business Analysts API as a Microservice to Kubernetes using AWS

How the project works:

1. Created and setup a repository using the AWS Elastics Container Registry for storing docker images of the application pushed from the CodeBuild project.

2. Setup a build project pipeline using AWS Codebuild with relevant ECR role data attached to it so that the build job can successfuly push the build image to a registry. 
The project is triggered by webhook event "pull_request_merged" in the selected ready github project repository.

3. I have then Setup a cluster using Amazon Elastic Kubernetes Service with a node group which consists of 2 nodes of Amazon Linux 2 (arm64) with Instance type m6g.large and 20GiB disk size. 
This are suitable hardware and software components for this application, considering its workload. 
Furthermore, scaling up/down can be advantageous and beneficial as per business requirements/needs.

4. Initialize communication between the AWS EKS service and the VS Studio termianal in order to be able to access and work on the created cluster.
From the VS Studios Workspace terminal by running > aws eks update-kubeconfig --name <cluster-name> --region <region>

5. I have configured a database for this Microservice using Helm Charts as follows:
> helm repo add bitnami https://charts.bitnami.com/bitnami
> helm install analyticsapi bitnami/postgresql --set primary.persistence.enabled=false
First command creates a repository for this database service.
Then the second command installs the PostgreSQL Helm Chart.
Followed command two output instructions on multiple ways to connect to the database from within or outside the cluster

6. Therefore, I have the database service running which can be accessed by the deployed container running an image of the business analysts api.
This Microservice is now running as a pod in a Kubernetes cluster and it can be destroyed and redeployed automatically whenever a merged code occurs in a GitHub repo.
For high availabilty purposes, I have also deployed a Load Balancer targeting the pod running the container/image of the Business Analyts application.
For troubleshooting purposes, the logs for the container/application running as a kubernetes pod are directed to a log group which can be accessed using from AWS CloudWatch, under log groups.