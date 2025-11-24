



1.IAM User Setup: Create an IAM user with the necessary permissions for EKS, S3, DynamoDB, ECR, and EC2.
2.Terraform & AWS CLI Installation: Install Terraform and AWS CLI on your local machine and configure AWS CLI with IAM credentials.
3.S3 Bucket and DynamoDB Table: Set up an S3 bucket for Terraform state files and a DynamoDB table for state locking.
4.Infrastructure as Code (IaC): Use Terraform and AWS CLI to set up the Jenkins server (EC2 instance) on AWS.
5.Jenkins Server Configuration: With shell script & terraform install Jenkins, Docker, Docker Compose, Terraform, Kubectl, AWS CLI, Trivy, and SonarQube.
6.Installing Plugins on Jenkins: Install Jenkins plugins, including AWS Credentials, AWS Steps, Docker plugins, Eclipse Temurin installer, NodeJS, OWASP Dependency-Check, and SonarQube Scanner.
7.SonarQube Setup: Integrate SonarQube for code quality analysis within the Jenkins pipeline.
8.Amazon ECR Repositories: Create Amazon ECR repositories for frontend and backend images and configure Jenkins to push Docker images to ECR.
9.EKS Cluster Deployment: Deploy the EKS cluster with Terraform, including VPC, subnets, security groups, and IAM roles.
10.Prometheus and Grafana Installation and Configuration: Use Helm to install Prometheus and Grafana on the EKS cluster and set up monitoring dashboards.
11.Jenkins Pipelines (Frontend & Backend): Set up Jenkins pipelines for frontend and backend applications with stages for building, testing, scanning, and deploying.
12.ArgoCD Installation & Application Deployment: Install ArgoCD on the EKS cluster and configure it to deploy applications automatically from a Git repository.
13.DNS Configuration: Configure DNS to point to the EKS load balancer or Ingress endpoint, and to make applications available on a subdomain using Cloudflare.
14.Data Persistence: Implement persistent volume and persistent volume claims for database pods to ensure data persistence.
15.Conclusion: Test the end-to-end deployment, verify integrations, and ensure that monitoring, alerting, and scaling settings are in place for production readiness.






Terraform & AWS CLI Installation
To install Terraform, You can visit Terraform official website.

To install AWS CLI, You can Visit AWS installtion page.


After installation, you can verify installation with these two commands

terraform --version
aws --version
Then configure aws CLI using the given blow commands, Use Access and secret key from the CSV file you download in the IAM User step.

aws configure












# Three-Tier DevSecOps Full-Stack Web Application Deployment on AWS EKS using ArgoCD, Prometheus, Grafana, and Jenkins
[![LinkedIn](https://img.shields.io/badge/Connect%20with%20me%20on-LinkedIn-blue.svg)](https://www.linkedin.com/in/muhammad-rashid-daha/)
[![Youtube](https://img.shields.io/youtube/channel/subscribers/UC1HEefoqUWmztGZ_Laq28sw)](https://youtube.com/@codewithmuh)
[![Medium](https://img.shields.io/badge/Medium-12100E?style=for-the-badge&logo=medium&logoColor=white)](https://medium.com/@codewithmuh)
[![GitHub](https://img.shields.io/github/stars/codewithmuh.svg?style=social)](https://github.com/codewithmuh)
[![Terraform](https://img.shields.io/badge/Terraform-%E2%9C%A8-lightgrey)](https://www.terraform.io)
[![AWS](https://img.shields.io/badge/AWS-%F0%9F%9B%A1-orange)](https://aws.amazon.com)


### Youtube Video Url: https://youtu.be/UNF5JdUEfh8
### Linkedin Article: https://www.linkedin.com/pulse/deploying-three-tier-devsecops-web-application-aws-eks-rashid-x7f9f/

Learn how to deploy a complete three-tier DevSecOps project on AWS EKS! This step-by-step guide covers setting up Kubernetes clusters, implementing CI/CD with Jenkins, managing GitOps with ArgoCD, and monitoring with Prometheus and Grafana. Perfect for mastering secure and scalable application deployment.
![1i (6)](https://github.com/user-attachments/assets/f69e8abc-c703-4604-bb14-f3876ddc08f8)


This Repo is your blueprint for leveraging AWS services like ECR, S3, DynamoDB, and EC2, with Terraform as the backbone for Infrastructure as Code (IaC). From setting up an IAM user with proper permissions to configuring pipelines for building, scanning, and deploying frontend and backend applications, you'll discover how to create a production-grade system designed for scalability and security.

#### Highlights include:

**Pipeline Automation:** Build, scan, and deploy Docker images using Jenkins, Trivy, and SonarQube.

**Cluster Monitoring:** Install Prometheus and Grafana to monitor application health with visual dashboards.

**GitOps with ArgoCD:** Achieve seamless application deployment and updates via Git repositories.

**End-to-End DevSecOps Workflow:** Incorporate security scans, monitoring, and scalable infrastructure for a production-ready deployment.


To make learning even easier, a detailed YouTube video accompanies this guide, walking you through each step visually. Whether you're a DevOps enthusiast or a seasoned cloud professional, this tutorial will help you bridge the gap between theory and practical implementation.


