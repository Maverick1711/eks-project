Kubernetes Deployment Project: Shopwise App on AWS EKS
Project Overview


This repository contains the infrastructure and deployment configuration for the Shopwise web application, deployed on AWS Elastic Kubernetes Service (EKS). The project demonstrates a professional DevOps workflow including containerization, secret management, CI/CD readiness, and cloud-native deployment best practices.

The application is dynamic, powered by a MySQL database on AWS RDS, and leverages AWS Secrets Manager for secure credential management.


Key Features

Containerized Application: Uses Docker to package the application.

Kubernetes Deployment: Fully configured Deployment, Service, and SecretProviderClass manifests.

AWS EKS Integration: Deploys the app to a managed Kubernetes cluster.

Secrets Management: Securely injects database credentials from AWS Secrets Manager.

Database Migrations: Flyway scripts are integrated for automated database setup.

Resource Management: Kubernetes resource requests and limits defined to ensure cluster stability.

Scalable and Secure: Supports scaling and integrates with IAM service accounts.


Repository Structure
eks-project/
├── deployment.yaml           # Kubernetes Deployment manifest
├── service.yaml              # Kubernetes Service manifest
├── secret-provider.yaml      # SecretProviderClass for AWS Secrets Manager
├── build-image.sh            # Script to build Docker image
├── push-image.sh             # Script to push Docker image to ECR
├── db-migrate-script.sh      # Script to migrate the database
├── Dockerfile                # Docker configuration for the app
├── README.md                 # Project documentation
└── .gitignore


Prerequisites

AWS Account with the following resources:

EKS cluster

RDS MySQL database

ECR repository for Docker images

Secrets Manager secret for DB credentials

kubectl configured for the target EKS cluster

AWS CLI installed and configured

Docker installed

Flyway for database migrations

Setup Instructions
1️⃣ Build and Push Docker Image
chmod +x build-image.sh push-image.sh
./build-image.sh
./push-image.sh


The scripts will build the application image and push it to your AWS ECR repository.

2️⃣ Deploy to Kubernetes
kubectl apply -f secret-provider.yaml -n <namespace>
kubectl apply -f deployment.yaml -n <namespace>
kubectl apply -f service.yaml -n <namespace>


Replace <namespace> with your target Kubernetes namespace.

3️⃣ Verify Deployment
kubectl get pods -n <namespace>
kubectl get svc -n <namespace>
kubectl logs -f <pod-name> -n <namespace>

4️⃣ Database Migrations

Flyway migration scripts are automatically run in the EC2 userdata or can be executed manually inside the pod:

kubectl exec -it <pod-name> -n <namespace> -- ./db-migrate-script.sh

5️⃣ Access the Application

Once the service is running, access your application via:

Load Balancer DNS (if using AWS ELB/ALB)

Port forwarding for local testing:

kubectl port-forward svc/<service-name> 8080:<target-port> -n <namespace>


Open http://localhost:8080 in your browser.

Environment Variables

RDS_ENDPOINT – AWS RDS database endpoint

RDS_DB_NAME – Database name

RDS_DB_USERNAME – DB username

DB_PASSWORD – DB password (injected via SecretManager)

S3_URI – Location of migration SQL scripts in S3

FLYWAY_VERSION – Flyway CLI version

AWS_REGION – AWS region

Kubernetes Resource Management

Requests and limits defined in deployment.yaml:

resources:
  requests:
    cpu: "200m"
    memory: "256Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"


Ensures pods are efficiently scheduled and avoids “noisy neighbor” issues.


Security

Uses AWS IAM Service Accounts with least privilege.

Secrets are injected via SecretProviderClass from AWS Secrets Manager.

Docker images are pulled securely from AWS ECR.



Future Improvements

Implement CI/CD pipeline using Jenkins or GitHub Actions for automated deployment.

Add horizontal pod autoscaling for dynamic scaling.

Add monitoring and logging via CloudWatch or Prometheus/Grafana.



Author

Oladotun David Olanipekun – DevOps Engineer

GitHub: https://github.com/Maverick1711

Expertise: AWS EKS, Kubernetes, Docker, CI/CD, Terraform, Secrets Management
