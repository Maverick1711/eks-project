Shopwise App Kubernetes Deployment

Project Overview

This repository contains the deployment and infrastructure setup for the Shopwise web application on AWS Elastic Kubernetes Service (EKS).

The project demonstrates professional DevOps practices, including:

Containerization with Docker

Kubernetes deployments and service configuration

Secrets management using AWS Secrets Manager

Database migrations using Flyway

CI/CD readiness and cloud-native deployment

This setup ensures scalability, security, and maintainability for dynamic web applications.


Key Features

Containerized App: Dockerfile included for application image build.

Kubernetes Deployment: Deployment and Service manifests ready for EKS.

Secrets Management: Database credentials securely injected via SecretProviderClass.

Database Migrations: Flyway integrated to automate MySQL setup.

Resource Management: CPU and memory limits set to prevent noisy neighbor issues.



AWS Integration: Full support for EKS, RDS, and ECR.

Repository Structure
eks-project/
├── deployment.yaml           # Kubernetes Deployment manifest
├── service.yaml              # Kubernetes Service manifest
├── secret-provider.yaml      # SecretProviderClass for AWS Secrets Manager
├── Dockerfile                # Docker configuration for app
├── build-image.sh            # Script to build Docker image
├── push-image.sh             # Script to push Docker image to ECR
├── db-migrate-script.sh      # Script to migrate database
├── README.md                 # Project documentation
└── .gitignore



Prerequisites

AWS account with EKS cluster, RDS MySQL, ECR repo, and Secrets Manager secret

kubectl configured for the target EKS cluster

AWS CLI installed and configured

Docker installed

Flyway for database migrations

Setup & Deployment
1️⃣ Build & Push Docker Image
chmod +x build-image.sh push-image.sh
./build-image.sh
./push-image.sh

2️⃣ Deploy to Kubernetes
kubectl apply -f secret-provider.yaml -n <namespace>
kubectl apply -f deployment.yaml -n <namespace>
kubectl apply -f service.yaml -n <namespace>

3️⃣ Verify Deployment
kubectl get pods -n <namespace>
kubectl get svc -n <namespace>
kubectl logs -f <pod-name> -n <namespace>

4️⃣ Database Migrations
kubectl exec -it <pod-name> -n <namespace> -- ./db-migrate-script.sh

5️⃣ Access the Application

Via AWS Load Balancer DNS

Or locally using port-forward:

kubectl port-forward svc/<service-name> 8080:<target-port> -n <namespace>


Visit http://localhost:8080 in your browser

Environment Variables
Variable	Description
RDS_ENDPOINT	MySQL RDS endpoint
RDS_DB_NAME	Database name
RDS_DB_USERNAME	Database username
DB_PASSWORD	DB password (from Secrets Manager)
S3_URI	Location of migration SQL scripts
FLYWAY_VERSION	Flyway CLI version
AWS_REGION	AWS region
Resource Management
resources:
  requests:
    cpu: "200m"
    memory: "256Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"


Ensures cluster stability and efficient scheduling.


Security

AWS IAM service account with least privilege

Secrets injected via SecretProviderClass

Docker images pulled securely from AWS ECR


Future Improvements

Implement CI/CD pipeline (Jenkins/GitHub Actions)

Horizontal pod autoscaling

Monitoring with CloudWatch or Prometheus/Grafana


Author

Oladotun David O – DevOps Engineer

GitHub: https://github.com/Maverick1711

Skills: AWS EKS, Kubernetes, Docker, Terraform, CI/CD, Secrets Management
