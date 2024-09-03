## Microservices at Scale using AWS, Docker, and Kubernetes
This project focuses on deploying and managing microservices at scale using Kubernetes on AWS. The goal is to build a resilient, scalable, and highly available architecture that can support microservices in a cloud environment.

### Dependencies:
#### Local Environment
- Python Environment - run Python 3.6+ applications and install Python dependencies via `pip`
- Docker CLI - build and run Docker images locally
- `kubectl` - run commands against a Kubernetes cluster
- `helm` - apply Helm Charts to a Kubernetes cluster

#### Remote Resources
- AWS CodeBuild - Set up a CI project to build Docker images remotely
- AWS ECR - host Docker images
- Kubernetes/K8s Environment - Deploy applications using AWS EKS
- AWS CloudWatch - monitor AWS EKS activity and logs
- GitHub - store app code, config files and other dependencies
- Docker - to containerize the app
  
### Project Objectives:
- Create a Dockerfile for the Python application. Use a base image that is Python-based.
- Write a simple build pipeline with AWS CodeBuild to build and push a Docker image into AWS ECR.
- Set up a Postgres database with a Helm Chart.
- Create a service and deployment using Kubernetes configuration files to deploy the application.
- Check AWS CloudWatch for application logs.
   
### How Continuous Deployment is Implemented:

1. **Source Code Management with GitHub**:
   - The application code is stored in a GitHub repository. A webhook is configured to trigger actions based on specific events, such as a "pull_request_merged"         action.

2. **Continuous Integration with AWS CodeBuild**:
   - AWS CodeBuild is used to automate the process of building Docker images for the application. Whenever a pull request is merged in the GitHub repository, a 
     webhook event triggers the CI pipeline.
   - The `buildspec.yml` file defines the steps for building the Docker image. This includes logging into AWS Elastic Container Registry (ECR), building the Docker 
     image using the instructions in the Dockerfile, tagging the image, and pushing it to the ECR.

3. **Docker Image Storage in AWS ECR**:
   - The newly built Docker image is pushed to AWS ECR, which acts as a private Docker image repository.

4. **Continuous Deployment with Kubernetes on AWS EKS**:
   - The Kubernetes cluster is set up using Amazon Elastic Kubernetes Service (EKS) with a node group to run the application. The node group consisting of 2 nodes       running Amazon Linux 2 (ARM64), with m6g.large instance types and 20 GiB disk sizes. These hardware and software components are well-suited for the                 microservice's workload. Additionally, the ability to scale up or down offers flexibility to meet evolving business requirements.
   - Establish communication between the AWS EKS service and the Visual Studio terminal to access and work on the created cluster. From the Visual Studio workspace      terminal, run the command:
     
      > aws eks update-kubeconfig --name --region
   - Once the Docker image is pushed to ECR, Kubernetes automatically pulls the updated image based on the deployment configuration.
   - Kubernetes deployment files (YAML configuration files) specify how the application is deployed and managed within the cluster. They define the desired state        for deployments, services, and other resources.
   - When a new Docker image version is available (after a new image is pushed to ECR), Kubernetes can automatically update the running pods with the new image          version. This is facilitated by the Kubernetes Deployment controller, which performs rolling updates to minimize downtime.

5. **Helm for Database Setup and Management**:
   - Helm charts are used to deploy and manage the PostgreSQL database within the Kubernetes cluster. Helm automates the creation and management of Kubernetes           resources, simplifying the deployment process.
     
   - Add the Bitnami repository:
      > helm repo add bitnami https://charts.bitnami.com/bitnami

   - Install the PostgreSQL Helm Chart:
      > helm install <SERVICE_NAME> bitnami/postgresql --set primary.persistence.enabled=false

   - This sets up a PostgreSQL deployment at <SERVICE_NAME>-postgresql.default.svc.cluster.local in your Kubernetes cluster. You can verify it by running kubectl        get svc.

   - Follow the output instructions from the second command for multiple ways to connect to the database, either from within or outside the cluster.

   - Finally, run the seed files located in the `db/` directory in numerical order to create the tables and populate them with data.

6. **Monitoring and Logging with AWS CloudWatch**:
   - Logs from the application pods and containers are sent to AWS CloudWatch. This allows for real-time monitoring and troubleshooting of the application.

### Summary of Continuous Deployment:

In this setup, continuous deployment is achieved through the integration of GitHub, AWS CodeBuild, AWS ECR, and AWS EKS. Code changes in the GitHub repository automatically trigger a build pipeline, which builds and pushes a new Docker image to ECR. Kubernetes then pulls the updated image and redeploys the microservice, ensuring the application is always up-to-date and running the latest code. This approach enables automated, seamless, and efficient deployment of microservices at scale, with minimal manual intervention.
