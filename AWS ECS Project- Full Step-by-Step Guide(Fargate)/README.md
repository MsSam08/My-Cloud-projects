# AWS ECS Fargate Deployment Project ##

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

### Prerequisites 

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

### Step 1: Build & Push Docker Image to ECR #

Why: ECS pulls images from Amazon ECR. You need to build your application image and push it so Fargate can run it.

CLI Steps

1. Create the ECR repository:
```
aws ecr create-repository --repository-name ${REPO_NAME} --region ${AWS_REGION}
```
![WhatsApp Image 2025-11-13 at 12 12 01 PM](https://github.com/user-attachments/assets/1f66676c-bf2e-483c-8088-c9a74e2132a0)


2. Authenticate Docker with ECR:
   ```
   aws ecr get-login-password --region ${AWS_REGION} \
    | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
   ```
![WhatsApp Image 2025-11-13 at 12 14 29 PM](https://github.com/user-attachments/assets/6acc6acb-fc17-4389-b48f-ce9f7d18b6da)

3. Build, tag, and push the image:
   ```
   docker build -t ${REPO_NAME}:${IMAGE_TAG} .
   docker tag ${REPO_NAME}:${IMAGE_TAG} ${ECR_URI}
   docker push ${ECR_URI}
   ```
  ![WhatsApp Image 2025-11-13 at 12 14 30 PM(2)](https://github.com/user-attachments/assets/8fa5f3a6-28c9-48ea-89f0-ccaadb9a7985)
![WhatsApp Image 2025-11-13 at 12 14 31 PM](https://github.com/user-attachments/assets/8ae21f7f-9370-408f-ac6f-e96f578f19c7)

### Step 2: Create ECS Task Execution Role

Why: Fargate tasks need permissions to pull images from ECR and write logs to CloudWatch. The ecsTaskExecutionRole grants this.

1. Create a trust policy (ecs-trust-policy.json):
  ```
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {"Service": "ecs-tasks.amazonaws.com"},
    "Action": "sts:AssumeRole"
  }]
}
  ```
2. Create the role and attach the managed policy:
   ```
   aws iam create-role --role-name ecsTaskExecutionRole --assume-role-policy-document file://ecs-trust-policy.json
   aws iam attach-role-policy \
    --role-name ecsTaskExecutionRole \
    --policy-arn arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy
   ```
*Created mine in the AWS console*
![WhatsApp Image 2025-11-13 at 12 14 36 PM(2)](https://github.com/user-attachments/assets/cda74053-0e95-4c69-ac85-b65ee5a92b8a)

![WhatsApp Image 2025-11-13 at 12 14 35 PM](https://github.com/user-attachments/assets/1bc16641-6a32-4e80-a17b-34a44682e612)

### Step 3: Create ECS Task Definition

Why: Task definitions define how your container runs: which image, CPU/memory, port mappings, logging, and network mode.

1. Create CloudWatch log group:aws logs create-log-group
   ```
   log-group-name /ecs/${REPO_NAME}
   ```

2. Task definition JSON (task-definition.json):
   ```
   {
    "family": "my-app-task",
   "networkMode": "awsvpc",
   "requiresCompatibilities": ["FARGATE"],
   "cpu": "256",
   "memory": "512",
   "executionRoleArn": "arn:aws:iam::123456789012:role/ecsTaskExecutionRole",
   "containerDefinitions": [
    {
      "name": "my-app-container",
      "image": "123456789012.dkr.ecr.us-east-1.amazonaws.com/my-app:v1",
      "essential": true,
      "portMappings": [{ "containerPort": 80, "protocol": "tcp" }],
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/ecs/my-app",
          "awslogs-region": "us-east-1",
          "awslogs-stream-prefix": "ecs"
        }
      }
    }
   ]
   }
   ```
   *Edit the code to fit your account details*
 ![WhatsApp Image 2025-11-13 at 12 14 31 PM](https://github.com/user-attachments/assets/9600e220-a47a-4523-9a04-42fbdad01cf7)

4. Register the task definition:
   ```
   aws ecs register-task-definition --cli-input-json file://task-definition.json
   ```
   Console Alternative: ECS → Task Definitions → Create new → Fargate → paste JSON.
   ![WhatsApp Image 2025-11-13 at 12 14 34 PM](https://github.com/user-attachments/assets/e767a343-8ce3-4e2e-b723-47c2e52ce613)
   
### Step 4: Create ECS Cluster

Why: A cluster is a logical grouping of tasks and services. Fargate handles compute resources automatically.
  ```
  aws ecs create-cluster --cluster-name ${CLUSTER_NAME}
  ```
![WhatsApp Image 2025-11-13 at 12 14 32 PM](https://github.com/user-attachments/assets/f78be5d8-729d-46c9-b2e1-7520dac9599e)
 ![WhatsApp Image 2025-11-13 at 12 14 36 PM(1)](https://github.com/user-attachments/assets/32bf5277-2cfa-4296-84e3-e4a3b46fee9d)
Console Alternative: ECS → Clusters → Create → Networking only (Fargate).

### Step 5: Create ECS Service

Why: Services ensure a desired number of tasks are running, manage replacement, and can integrate with load balancers.
  ```
    aws ecs create-service \
  --cluster ${CLUSTER_NAME} \
  --service-name ${SERVICE_NAME} \
  --task-definition ${TASK_FAMILY} \
  --desired-count ${DESIRED_COUNT} \
  --launch-type FARGATE \
  --network-configuration "awsvpcConfiguration={subnets=[${SUBNETS}],securityGroups=[${SECURITY_GROUP}],assignPublicIp=ENABLED}"
```
Console Alternative: ECS → Cluster → Create → Service → Fargate → Networking → SG.
![WhatsApp Image 2025-11-13 at 12 14 32 PM(1)](https://github.com/user-attachments/assets/70607b65-7f70-469b-b63c-3b6171f9a540)

Step 6: Verify Deployment

- List running tasks:
```
aws ecs list-tasks --cluster ${CLUSTER_NAME} --service-name ${SERVICE_NAME}
```

- Describe task for details and public IP:
  ```
  aws ecs describe-tasks --cluster ${CLUSTER_NAME} --tasks <task-arn>
  ```

 
- View logs in CloudWatch ``` /ecs/my-app```

![WhatsApp Image 2025-11-13 at 12 14 36 PM](https://github.com/user-attachments/assets/1e6cfb92-46c8-4a19-bc44-4af13953b9fa)


### Step 7: Test Task Replacement

Why: ECS services automatically replace failed/stopped tasks. Test this by stopping one:
  ```
TASK_ARN=$(aws ecs list-tasks --cluster ${CLUSTER_NAME} --service-name ${SERVICE_NAME} --query 'taskArns[0]' --output text)
aws ecs stop-task --cluster ${CLUSTER_NAME} --task ${TASK_ARN} --reason "Testing replacement"
```
- ECS should launch a new task automatically.

### Step 8: Scale Service
  ```
aws ecs update-service --cluster ${CLUSTER_NAME} --service ${SERVICE_NAME} --desired-count 3
```
- Verify 3 running tasks using `` list-tasks``

### FURTHER IMPROVEMENTS - OPTIONAL
 **Step 9: Attach an Application Load Balancer**

- Create ALB (internet-facing), target group (IP mode), and health checks.

- Enable ALB in ECS service. ECS registers tasks automatically.

- Security groups must allow HTTP traffic between ALB and tasks.
  
 **Step 10: Autoscaling**

- Set CloudWatch alarms based on CPU or request count.

- Configure target tracking or step scaling on ECS service.

  ### Cleanup

Why: To avoid unnecessary AWS charges.
  ```
# Delete service
aws ecs update-service --cluster ${CLUSTER_NAME} --service ${SERVICE_NAME} --desired-count 0
aws ecs delete-service --cluster ${CLUSTER_NAME} --service ${SERVICE_NAME} --force

# Deregister task definitions
aws ecs list-task-definitions --family-prefix ${TASK_FAMILY}
aws ecs deregister-task-definition --task-definition ${TASK_FAMILY}:1

# Delete cluster
aws ecs delete-cluster --cluster ${CLUSTER_NAME}

# Delete ECR repository
aws ecr delete-repository --repository-name ${REPO_NAME} --force

# Delete IAM role (if created)
aws iam detach-role-policy --role-name ecsTaskExecutionRole --policy-arn arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy
aws iam delete-role --role-name ecsTaskExecutionRole

# Delete ALB, target groups, security groups if created
```
### Quick Checklist

- Build & push Docker image to ECR

- Create ecsTaskExecutionRole (if needed)

- Register task definition

- Create ECS cluster

- Create ECS service (desired count 2)

- Stop a task to verify replacement

- Scale desired count to 3

- Optional: ALB & autoscaling

- Cleanup resources

   

   

