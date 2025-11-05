# My-Cloud-projects
Practical cloud engineering projects leveraging AWS ( From Beginner to Advanced).

# 1. AWS Billing Alerts Project

This project demonstrates how to configure **AWS CloudWatch Billing Alarms** and integrate them with **Amazon SNS** for real-time cost monitoring.  
I set up three billing alerts at different thresholds (**$5**, **$25**, and **$100**) to track spending and prevent unexpected charges.

The alarms were configured to send **email notifications** through an SNS topic, and their functionality was verified using a **test alarm state**.  
This project showcases practical AWS cost-management techniques using native monitoring and notification tools.

**Level:** Beginner

**Category:** AWS Cloud Engineering | Cost Monitoring 
---
📘 **Read the full walkthrough on Medium:** [*AWS Billing alerts*](https://medium.com/@euodiasam/monitoring-aws-costs-like-a-pro-setting-up-billing-alarms-with-amazon-cloudwatch-afeb6f159112)

 # 2. AWS CLI MySQL RDS Project

This project demonstrates deploying a **MySQL database on Amazon RDS** entirely via the **AWS CLI**.  
It covers **subnet and security group setup**, **database provisioning**, and **connecting from local clients**.  
The project highlights **cloud automation, infrastructure-as-code, and practical RDS management** skills.

**Level:** Beginner → Intermediate  
**Category:** AWS Cloud Engineering | Databases | Automation
---
📘 **Read the full walkthrough on Medium:** [*Deploying MySQL on AWS RDS with CLI*](https://medium.com/@euodiasam/setting-up-a-mysql-database-instance-on-amazon-rds-using-aws-cli-b113f2403336)

# 3. AWS CloudFormation Project

This project demonstrates deploying AWS resources automatically using **AWS CloudFormation**, a service that enables **Infrastructure as Code (IaC)**.  
The stack provisions an **Amazon S3 bucket** and a **DynamoDB table**, showcasing how to manage cloud infrastructure declaratively rather than manually.  
After deployment, the stack is deleted to confirm CloudFormation’s ability to fully automate both **creation** and **cleanup**.

**Level:** Beginner → Intermediate  
**Category:** AWS Cloud Engineering | Infrastructure as Code | Automation  
---
📘 Read the full walkthrough on Medium: [*Deploying DynamoBD and S3 using Cloudformation*](https://medium.com/@euodiasam/infrastructure-as-code-deploying-aws-resources-with-a-single-cloudformation-template-ab148c9ebfbc)

