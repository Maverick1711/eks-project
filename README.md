Shopwise APP on AWS EKS

A full‑stack, containerized web application deployed on AWS EKS, with MySQL on RDS and secure credential handling via AWS Secrets Manager.


🚀 Project Overview

This repository contains the infrastructure, deployment manifests, and automation scripts to run the Shopwise web application on AWS EKS (Kubernetes). It demonstrates a real‑world DevOps workflow including:

Dockerization of the application

Kubernetes Deployment + Service configuration

Secure secret management via AWS Secrets Manager + CSI driver

MySQL database on AWS RDS

Infrastructure-as-code readiness (for CI/CD pipelines)

The goal: provide a reproducible, scalable, and secure deployment environment for a dynamic web app — ideal for production or staging.



📂 Repository Structure
eks-project/
├── deployment.yaml           # Kubernetes Deployment manifest
├── service.yaml              # Kubernetes Service manifest
├── secret-provider.yaml      # SecretProviderClass for AWS Secrets Manager
├── Dockerfile                # Docker config for application image
├── build-image.sh            # Build Docker image script
├── push-image.sh             # Push Docker image to AWS ECR script
├── db-migrate-script.sh      # Script to run DB migrations
├── README.md                 # Project documentation (this file)
└── .gitignore


Note: Adjust names if your files differ (e.g. service.yaml maybe named differently in your project).

🛠️ Prerequisites

Before deploying, make sure you have:

An AWS account with:

EKS cluster configured

RDS MySQL instance (database)

ECR repository for Docker images

AWS Secrets Manager secret storing DB credentials

The following tools installed locally or in a CI environment:

kubectl (configured to target the EKS cluster)

AWS CLI (configured with correct IAM permissions)

Docker

(Optional) Flyway — if using database migration scripts


📦 Deployment Workflow
1. Build & Push Docker Image
chmod +x build-image.sh push-image.sh
./build-image.sh
./push-image.sh


This builds your application’s Docker image and pushes it to your ECR repository.

2. Deploy Kubernetes Resources
kubectl apply -f secret-provider.yaml -n <namespace>
kubectl apply -f deployment.yaml -n <namespace>
kubectl apply -f service.yaml -n <namespace>


Replace <namespace> with your intended namespace. This:

Deploys the application container to EKS

Configures the Service (LoadBalancer / NodePort) for external access

Mounts secrets securely from AWS Secrets Manager

3. Run Database Migrations

If you include migration scripts:

kubectl exec -it <pod-name> -n <namespace> -- /app/db-migrate-script.sh


Or set up your CI/CD to run migrations automatically after deployment.

4. Access the Application

Once the Load Balancer is provisioned and healthy, visit your domain or LB DNS in the browser.
For local testing, you can forward the service port:

kubectl port-forward svc/<service-name> 8080:<service-port> -n <namespace>


Then open http://localhost:8080.

🔐 Configuration & Secrets

Core configuration and credentials (e.g. DB password) are not hard-coded. They are stored in AWS Secrets Manager and mounted into Kubernetes via the SecretProviderClass.

You must ensure:

Secrets exist in AWS Secrets Manager

The secret-provider.yaml references the correct Secret ARN

The Kubernetes ServiceAccount used by the pod has the necessary IAM permissions

Environment variables passed to the application container typically include:

DB_HOST, DB_PORT, DB_DATABASE, DB_USERNAME, DB_PASSWORD, ...


Ensure these match your RDS configuration.

🔧 Kubernetes Resource Limits

Resources are constrained in the Deployment manifest to avoid misuse and ensure cluster stability:

resources:
  requests:
    cpu: "200m"
    memory: "256Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"


Feel free to tune these values to match your workload.

✅ Why This Setup

Production‑ready: Uses managed services (EKS + RDS + Secrets Manager), suitable for real workloads

Secure: Secrets are never exposed in code or config — they live securely in AWS Secrets Manager

Scalable & Maintainable: Docker + Kubernetes + Infrastructure as Code patterns make it easy to scale, version, and automate deployments

Clean Separation: Database, application code, infrastructure, and secrets are cleanly separated — following best practices


📈 Next Steps / Improvements

Add a CI/CD pipeline (GitHub Actions, Jenkins, or AWS CodePipeline) to automate builds, tests, deployments

Add horizontal pod autoscaling for dynamic load handling

Integrate logging/monitoring (e.g. Prometheus/Grafana, AWS CloudWatch)

Add a Helm chart for easier deployment/configuration management

Add docs for staging/production environments, secret rotation, backups


👤 Author / Maintainer

Oladotun — DevOps Engineer
GitHub: Maverick1711


Oladotun — DevOps Engineer
GitHub: Maverick1711
