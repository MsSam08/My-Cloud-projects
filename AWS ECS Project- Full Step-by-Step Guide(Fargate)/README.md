## AWS ECS Fargate Deployment Project ##

This project demonstrates deploying a Dockerized web application on AWS ECS using Fargate. Fargate allows you to run containers without managing EC2 instances. The project includes building and pushing Docker images, creating ECS task definitions, clusters, services, scaling, testing replacements, and cleaning up resources.
Project Goal

The goal of this project is to demonstrate a complete containerized application deployment using AWS ECS Fargate:

- Build Docker image and push it to Amazon ECR

- Create an ECS Task Definition specifying container, CPU, memory, networking, and logging

- Deploy an ECS Cluster and Service with Fargate launch type

- Verify tasks are running and logs are accessible

- Test task replacement by stopping one task

- Scale service to more instances

- Clean up all AWS resources to avoid costs

# Prerequisites #

Before starting, ensure you have:

- AWS CLI installed and configured with IAM permissions

- Docker installed and running locally

- AWS Account ID and preferred region

- A basic application with a Dockerfile ready for deployment

  Set Variables

Set environment variables for convenience:
```AWS_REGION="us-east-1"
AWS_ACCOUNT_ID="123456789012"
REPO_NAME="my-app"
IMAGE_TAG="v1"
ECR_URI="${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${REPO_NAME}:${IMAGE_TAG}"
CLUSTER_NAME="my-app-cluster"
SERVICE_NAME="my-app-service"
TASK_FAMILY="my-app-task"
DESIRED_COUNT=2
SUBNETS="subnet-aaa,subnet-bbb"
SECURITY_GROUP="sg-0123456789abcdef0"
```
*These variables will be reused throughout commands to simplify the workflow.*

# Step 1: Build & Push Docker Image to ECR #

Why: ECS pulls images from Amazon ECR. You need to build your application image and push it so Fargate can run it.

CLI Steps

Create the ECR repository:
