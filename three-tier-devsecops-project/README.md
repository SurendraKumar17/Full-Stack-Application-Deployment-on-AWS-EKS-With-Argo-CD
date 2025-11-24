



1.IAM User Setup: Create an IAM user with the necessary permissions for EKS, S3, DynamoDB, ECR, and EC2.
2.Terraform - AWS CLI Installation: Install Terraform and AWS CLI on your local machine and configure AWS CLI with IAM credentials.
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



::: Implementation :::


IAM User Setup :

Create an IAM user with the necessary permissions for EKS, S3, DynamoDB, ECR, and EC2.


Terraform - AWS CLI Installation :

To install Terraform, You can visit Terraform official website.
To install AWS CLI, You can Visit AWS installtion page.

#After installation, you can verify installation with these two commands
- terraform --version
- aws --version

#Then configure aws CLI using the given blow commands, Use Access and secret key from the CSV file you download in the IAM User step.
- aws configure


S3 Bucket and DynamoDB Table :

#As S3 Bucket and Dynamo DB are required for Terraform's backend state, you can create them by watching my video.
#S3 bucket can be any, give any unique name to it.
#lock-files name will be DynamoDB table, and the partition key will be LockID.


Jenkins EC2 Server Setup with Terraform :

#Clone the git repo
- git clone ttps://github.com/SurendraKumar17/Full-Stack-Application-Deployment-on-AWS-EKS-With-Argo-CD


#Go to the folder jenkins-server-terraform in the terminal. Just make sure to make changes in backend.tf file. Update the name of the s3 bucket and dynamo db name. Next, we have to run the command to create .pem file for our Jenkins instance. will be used to ssh the server.
- aws ec2 create-key-pair --key-name jenkins_fullstack_app --query "KeyMaterial" --output text > jenkins_fullstack_app.pem

#Now run this command to create a Jenkins server.

- terraform init
- terraform validate
- terraform apply

#Now ssh to your server - Make sure you run these commands from the folder where your pem file exist.
- chmod 400 "jenkins_fullstack_app.pem"
- ssh -i "jenkins_fullstack_app.pem" ubuntu@IP


#configure aws on the Jenkins server(EC2 Instance). As you did for your local machine.
- aws configure


#Installing Plugins on Jenkins - We have to install all these mentioned plugins.

AWS Credentials
AWS Steps
Docker plugins
Eclipse Temurin installer
NodeJS
OWASP Dependency-Check,
SonarQube Scanner.


SonarQube Setup :

Do these - We have to perform 5 Steps on the sonarqube setup.

Setup of frontend project for code analysis
Setup of backend project for code analysis
Replace the keys in jenkins-pipeline folder, you got from the above two steps. (watch video sonarqube setup)
create a sonar-token, and save it somewhere for later use in Jenkins.
create a webhook on the sonarqube dashboard. (http://<jenkins-ec2-server-public-ip>:8080/sonarqube-webhook/)


Amazon ECR Repositories :
Create two repositories, one for the backend and front end.
Login to ECR on the Jenkins server, using the ECR push command ( check the while creating it)


Add Credentials in Jenkins :

aws-key
GITHUB (username & Password)
github (secret text)
sonar-token
ECR_REPO_FRONTEND
ECR_REPO_BACKEND
ACCOUNT_ID (aws account id)
#Now We have to configure the installed plugins. (important). In Tools, We have to configure JDK, sonar-scanner, nodejs, DP-Check, and docker. Go to Dashboard -> Manage Jenkins -> System  Search for SonarQube installations. Provide the name as it is, then in the Server URL copy the sonarqube public IP (same as Jenkins) with port 9000 select the sonar token that we have added recently, and click on Apply & Save.


EKS Cluster Deployment :

#We have to create EKS Cluster using the below commands.
- eksctl create cluster --name three-tier-k8s-eks-cluster --region us-east-1 --node-type t2.medium --nodes-min 2 --nodes-max 2

Download the policy for the LoadBalancer prerequisite.
- curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json

Create the IAM policy using the below command
- aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json

Create OIDC Provider
eksctl utils associate-iam-oidc-provider --region=us-east-1 --cluster=three-tier-k8s-eks-cluster --approve


Create a Service Account by using below command and replace your account ID with your one
- eksctl create iamserviceaccount --cluster=three-tier-k8s-eks-cluster --namespace=kube-system --name=aws-load-balancer-controller --role-name AmazonEKSLoadBalancerControllerRole --attach-policy-arn=arn:aws:iam::484824099691:policy/AWSLoadBalancerControllerIAMPolicy --approve --region=us-east-1

Run the below command to deploy the AWS Load Balancer Controller
- helm repo add eks https://aws.github.io/eks-charts
  helm repo update eks
  helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=three-tier-k8s-eks-cluster --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller

After 2-3 minutes, run the command below to check whether your pods are running or not.
- kubectl get deployment -n kube-system aws-load-balancer-controller



Prometheus and Grafana Installation and Configuration :

#Grafana is an open-source platform for visualization and analytics. It allows you to query, visualize, alert on, and explore metrics from various data sources.
Steps to Install and Configure Prometheus and Grafana on Kubernetes

#Add Helm Stable Chart Repository
- helm repo add stable https://charts.helm.sh/stable

Add Prometheus Community Helm Repository

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
Step 2: Create a Namespace for monitoring

kubectl create namespace monitoring
Step 3: Install Prometheus with Grafana using Helm

Install the kube-prometheus-stack chart, which includes both Prometheus and Grafana:

helm install stable prometheus-community/kube-prometheus-stack -n monitoring
This command deploys Prometheus and Grafana as part of the kube-prometheus-stack in the prometheus namespace on your EC2 instance.

Step 4: Verify Prometheus Installation

Check the Prometheus pods:

- kubectl get pods -n monitoring
Check the Prometheus services:

- kubectl get svc -n monitoring
Since Grafana is deployed along with Prometheus, there is no need for separate Grafana installation.
Step 5: Expose Prometheus and Grafana to External Access
There are two ways to expose these services:
NodePort
LoadBalancer
Exposing Prometheus via LoadBalancer:
- kubectl edit svc stable-kube-prometheus-sta-prometheus -n monitoring
Save the file. The service should now have a load balancer with an external IP to access Prometheus.
Exposing Grafana via LoadBalancer:
Similarly, edit the Grafana service configuration to change ClusterIP to LoadBalancer:
- kubectl edit svc stable-grafana -n monitoring
Save the changes and use the load balancer IP in your browser to access Grafana.
Step 6: Accessing Grafana
The username will be admin and the password will be prom-operator for your Grafana LogIn.


Jenkins Pipelines (Frontend & Backend) :

....

ArgoCD Installation & Application Deployment :

- kubectl create namespace three-tier
- kubectl create secret generic ecr-registry-secret \
  --from-file=.dockerconfigjson=${HOME}/.docker/config.json \
  --type=kubernetes.io/dockerconfigjson --namespace three-tier
  kubectl get secrets -n three-tier

  #Now, we will install argoCD. To do that, create a separate namespace for it and apply the argocd configuration for installation.
-  kubectl create namespace argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.4.7/manifests/install.yaml
#All pods must be running, to validate run the below command
- kubectl get pods -n argocd

#Now, expose the argoCD server as LoadBalancer using the below command
- kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'


You can validate whether the Load Balancer is created o by going to the AWS Console load balancers.
To access the argoCD, copy the LoadBalancer DNS CNAME record and hit on your favorite browser.
Now, we need to get the password for our argoCD server to perform the deployment.
To do that, we have a pre-requisite which is jq. Install it by the command below.

- sudo apt install jq -y
- export ARGOCD_SERVER=$(kubectl get svc argocd-server -n argocd -o json | jq -r '.status.loadBalancer.ingress[0].hostname') && export ARGO_PWD=$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d) && echo "ARGOCD_SERVER: $ARGOCD_SERVER" && echo "ARGO_PWD: $ARGO_PWD"

#Next will deploy our Three-Tier Application using ArgoCD
As our repository is private. So, we need to configure the Private Repository in ArgoCD.
Then We have to set up applications.
1. database
2. backend
3. frontend
4. frontend-ingress
5. backend-ingress

DNS Configuration : 

Conclusion : 

We have set up a Persistent Volume and a Persistent Volume Claim. This means that if the pods are deleted, the data will not be lost, as it will be stored on the host machine.


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


