----------Multi-Region Disaster Recovery-----------

📌 Objective

To design and implement a multi-region disaster recovery solution using AWS services, where infrastructure is deployed in two AWS regions. The solution will replicate data across regions, ensure high availability, and implement a failover mechanism for disaster recovery. The project involves using Terraform to provision infrastructure, and CI/CD pipelines to ensure both regions remain synchronized for disaster recovery and failover.

🌐 Architecture Overview

The architecture involves provisioning AWS resources across two regions to create a highly available and resilient infrastructure. Key components include Amazon VPC for networking, Amazon S3 for cross-region data replication, Amazon RDS for multi-region database deployment, and Route 53 for DNS failover routing. The solution ensures that both regions are synchronized and can automatically switch to the backup region if needed.

🧩 AWS Services Used

- Amazon VPC (Virtual Private Cloud) for networking
- Amazon S3 for data replication across regions
- Amazon RDS for relational database deployment and cross-region replication
- Amazon Route 53 for DNS failover routing
- Terraform for infrastructure provisioning
- Jenkins / AWS CodePipeline for CI/CD automation
 
Brief about the services used:

🏗️ Amazon VPC

A Virtual Private Cloud (VPC) lets us launch AWS resources in a logically isolated portion of the AWS Cloud, giving us control over IP addressing, subnets, routing, and connectivity 

•	🔗 https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html

•	We will create two VPCs (one for Active, one for Pilot Light), each with public/private subnets, IGWs, NAT gateways, and route tables to manage traffic flow and network segmentation.
________________________________________

🖥️ Amazon EC2 (Elastic Compute Cloud)

Amazon EC2 provides secure, resizable compute capacity in the cloud. It allows you to launch virtual servers (instances), configure networking, and attach storage to run your applications.

📘 Docs:

🔗https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html

•	We'll set up ASGs in both regions to scale EC2 capacity based on load and ensure resilience of compute services.
________________________________________

📈 Auto Scaling Groups (ASG)

EC2 Auto Scaling Groups manage a group of EC2 instances. You define policies for scaling in or out based on metrics like CPU utilization, allowing your infrastructure to automatically adapt to load while maintaining availability and minimizing cost.

📘 Docs:

🔗 https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html

•	We'll set up ASGs in both regions to scale EC2 capacity based on load and ensure resilience of compute services.
________________________________________

🗂️ S3 (with CRR)

Amazon S3 is a highly durable object storage service, supporting features like versioning, cross-region replication (CRR), and high performance 

•	We'll enable versioning and configure CRR to asynchronously replicate objects from the Active bucket to the Pilot Light bucket, protecting against regional failure.

•	🔗https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html
________________________________________

📶 Application Load Balancer (ALB)

While not directly cited above, ALB is part of Elastic Load Balancing, offering flexible routing and TLS termination for HTTP/HTTPS traffic.

•	We'll deploy ALBs in each region, forwarding traffic only to healthy EC2 instances managed by ASGs.

•	Reference - https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html
________________________________________

💾 Amazon RDS

Amazon Relational Database Service (RDS) is a managed relational database service with automation for patching, backups, and high availability.

•	📘 Reference Documentation:

🔗https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html

•	We'll deploy a Multi-AZ primary in Active region and a cross-region read replica in Pilot Light for DR and failover.
________________________________________

🌐 Route 53 (DNS Failover)

Amazon Route 53 provides highly available DNS with health checks and traffic failover capabilities.

•	We'll configure DNS failover: Route 53 will route to the Active ALB by default and switch to the Pilot Light ALB if health checks fail.

•	📘 Reference Docs:

🔗https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html

In AWS, "Pilot Light" is a disaster recovery (DR) strategy where a minimal, essential version of your application infrastructure is kept running in the cloud, ready to be scaled up quickly in the event of a disaster. 
________________________________________

✅ Project Implementation Plan 

•	We will provision independent VPCs in both the Active and Pilot regions. Each VPC will include public and private subnets, internet gateways (IGWs), NAT gateways, and properly configured route tables.

•	We will create S3 buckets in both regions with versioning enabled. Cross-region replication will be configured to replicate data from the Active region to the Pilot region.

•	We will deploy a Multi-AZ RDS instance in the Active region to serve as the primary database. A read replica will be set up in the Pilot region for disaster recovery.

•	We will configure security groups to allow secure and expected communication. ALBs will allow HTTP/HTTPS traffic, and RDS will permit MySQL access only from EC2 instances in the Auto Scaling Group.

•	We will deploy Application Load Balancers (ALBs) in public subnets in both regions. These ALBs will use instance-based target groups to route traffic to EC2 instances.

•	We will create EC2 Auto Scaling Groups (ASGs) in private subnets, and attach them to the ALB target groups to ensure scalable application compute.

•	We will configure Route 53 to implement DNS failover. Health checks will monitor the Active region ALB, and if it becomes unhealthy, Route 53 will automatically route traffic to the Pilot region ALB.
