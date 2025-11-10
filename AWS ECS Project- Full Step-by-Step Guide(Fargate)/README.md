## AWS ECS Fargate Deployment Project ##

This project demonstrates deploying a Dockerized web application on AWS ECS using Fargate. Fargate allows you to run containers without managing EC2 instances. The project includes building and pushing Docker images, creating ECS task definitions, clusters, services, scaling, testing replacements, and cleaning up resources.
Project Goal

The goal of this project is to demonstrate a complete containerized application deployment using AWS ECS Fargate:
** Build Docker image and push it to Amazon ECR
** Create an ECS Task Definition specifying container, CPU, memory, networking, and logging
** Deploy an ECS Cluster and Service with Fargate launch type
** Verify tasks are running and logs are accessible
** Test task replacement by stopping one task
** Scale service to more instances
** Clean up all AWS resources to avoid costs
