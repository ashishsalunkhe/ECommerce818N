

# Swiftly -  Scalable and Secure E-commerce Platform on AWS

This project, designed and deployed by our team, builds a **scalable, secure e-commerce platform** leveraging Amazon Web Services (AWS) and Terraform for cloud infrastructure and automation. This README serves as a comprehensive report detailing our architectural choices, AWS services configurations, and load testing strategies.

---

## Table of Contents

1. [Project Objective](#project-objective)
2. [Architecture Overview](#architecture-overview)
3. [AWS Service Configurations](#aws-service-configurations)
4. [Content Delivery Network (CDN) Configuration](#cdn-configuration)
5. [Load Testing with JMeter](#load-testing-with-jmeter)
6. [Security and Compliance](#security-and-compliance)
7. [Cost Optimization](#cost-optimization)
8. [Lessons Learned and Future Improvements](#lessons-learned-and-future-improvements)

---

## Project Objective

Our objective was to create a robust e-commerce platform capable of managing high traffic loads with minimal downtime, safeguarding sensitive data, and optimizing global content delivery. Key goals included:

- **Scalability**: Deploying Auto Scaling Groups (ASG) and an Application Load Balancer (ALB) to efficiently handle traffic peaks and ensure high availability.
- **Security**: Using Amazon Web Application Firewall (WAF) configurations, SSL/TLS encryption, and role-based access control (IAM).
- **Optimized Content Delivery**: Serving static and dynamic assets through Amazon CloudFront for global caching and low-latency access.


---

## Architecture 


![image](https://github.com/user-attachments/assets/e5732214-678a-431e-919e-0813dec380f8)





                              +----------------------------------+
                              |          AWS WAF                |
                              |   (Web Application Firewall)     |
                              +----------------------------------+
                                        |
                                        |
                                        v
                           +-----------------------------+
                           |    Amazon CloudFront        |
                           |       (Content CDN)         |
                           +-----------------------------+
                                        |
                                        |
                                        v
                           +-----------------------------+
                           |       Amazon Route 53       |
                           |        (DNS Routing)        |
                           +-----------------------------+
                                        |
                                        |
                                        v
                           +--------------------------------------+
                           | Application Load Balancer (ALB)      |
                           |     (Distributes Traffic)            |
                           +--------------------------------------+
                                        |
                 +----------------------+--------------------------+
                 |                      |                          |
                 v                      v                          v
         +---------------+      +---------------+        +---------------+
         |   EC2 (App)   |      |   EC2 (App)   |        |   EC2 (App)   |
         | (Auto Scaling)|      | (Auto Scaling)|        | (Auto Scaling)|
         +---------------+      +---------------+        +---------------+
                 |                      |                          |
                 +----------------------+--------------------------+
                                        |
                                        |
                         +-----------------------------+
                         |       Amazon RDS            |
                         |     (MySQL Database)        |
                         |  (Multi-AZ, Private Subnet) |
                         +-----------------------------+
                                        |
                                        |
                          +------------------------------+
                          |       AWS KMS (Encryption)   |
                          +------------------------------+

         +------------------------+               +--------------------------+
         |       S3 Bucket        |               |    NAT Gateway           |
         | (Static Content, Logs) |               | (Public Subnet)          |
         +------------------------+               +--------------------------+
                 |
                 v
          +-----------------------------+
          |  Amazon CloudWatch, Config  |
          |   (Monitoring & Logging)    |
          +-----------------------------+
                 |
                 v
          +-----------------------------+
          |       AWS CloudTrail        |
          | (Audit Logging & Security)  |
          +-----------------------------+


---
This project implements a 3-tier architecture on AWS designed for a production environment. The architecture is optimized for high availability, security, scalability, and efficient monitoring.
---

## Architecture Overview

### 1. Virtual Private Cloud (VPC) Setup

Our VPC design provides isolated network infrastructure with **CIDR block 10.0.0.0/16**, segmented into **public and private subnets** across multiple Availability Zones (AZs) for redundancy and resilience:

- **Public Subnets**: Designed for instances needing internet access, such as web servers, managed via an Internet Gateway attached to the VPC.
- **Private Subnets**: Sensitive resources, like databases, reside in private subnets without internet access, with outbound traffic managed by a NAT Gateway. This setup enhances security while allowing essential services to operate in isolation.
- **Routing**: Each subnet has specific route tables – the public subnets have routes through the Internet Gateway, while the private subnets route through the NAT Gateway for controlled outbound access.

### 2. Auto Scaling Group (ASG) Configuration

The ASG dynamically scales EC2 instances to match traffic demand, supporting cost-effectiveness and high availability:

- **Instance Configuration**: EC2 instances running the application are distributed across AZs in public subnets for reliability.
- **Load Balancer**: An ALB distributes requests evenly across instances, securing traffic with SSL termination and routing requests based on WAF rules.
- **Scaling Policies**: Configured through CloudWatch Alarms based on CPU utilization, the ASG scales out when usage exceeds 50% and scales in at 30%.
- **Ansible Automation**: Ansible playbooks deployed on a bastion host automate the setup of ASG instances, including Apache, PHP, SSL certificates, and CloudWatch agents, ensuring consistency.

### 3. RDS MySQL Database

The MySQL database is hosted on Amazon RDS with Multi-AZ deployment to ensure high availability and fault tolerance:

- **Multi-AZ Deployment**: Ensures automatic failover to a standby instance in another AZ if the primary instance fails.
- **Encryption**: AWS KMS encrypts data at rest, while SSL/TLS encrypts data in transit, enhancing data security.
- **Access Control**: The database is hosted in private subnets, with security groups allowing access only from EC2 instances within the VPC, providing an added layer of protection.

## AWS Service Configurations

### CloudFormation Templates

To automate infrastructure provisioning, we used four primary CloudFormation templates:

1. **IAM Template**: Sets up roles and policies ensuring secure access control across services.

   - **Roles**: EC2 instances, RDS monitoring, VPC flow logging, and Ansible roles.
   - **Policies**: EC2Role for S3 and CloudWatch access; RDSMonitoringRole for enhanced RDS metrics; AnsibleRole for instance management.

2. **VPC Template**: Sets up network infrastructure, including subnets, security groups, and flow logs.

   - **Public and Private Subnets**: Two of each across different AZs.
   - **Routing**: Configures Internet and NAT Gateways for public and private instances respectively.
   - **VPC Flow Logs**: Capture all traffic for security and anomaly detection.

3. **ASG Template**: Manages the Auto Scaling Group, load balancer, and scaling policies.

   - **Launch Template**: Configures instance type, AMI, and security groups for ASG instances.
   - **Scaling Policies**: Defined based on CloudWatch metrics to ensure dynamic scaling.
   - **EventBridge and Lambda Integration**: Automatically configures new ASG instances using Ansible.

4. **RDS Template**: Provisions an RDS instance with necessary security and backup configurations.

   - **Multi-AZ Deployment**: Provides failover capabilities across AZs.
   - **Encryption**: KMS-managed encryption for data at rest.
   - **Backup and Monitoring**: Automated backups and enhanced monitoring enabled.

### Terraform Integration

Terraform was utilized for infrastructure as code, ensuring consistency across deployments:

- **Resource Definitions**: Defined resources like EC2, VPC, and RDS, which were version-controlled for repeatability.
- **IAM Policies**: Implemented through Terraform to manage secure access to each component.
- **Automated Deployments**: Terraform’s declarative syntax supported frequent updates and efficient modifications.

## CDN Configuration

To optimize content delivery and reduce server load, we implemented Amazon CloudFront with specific configurations:

1. **Static Assets Caching**: Configured long TTL (up to 24 hours) for images, CSS, and JavaScript, as these assets change infrequently. This reduces server load and ensures quick access from edge locations.
2. **Cache-Control Headers**: Added headers in the codebase to direct CDN caching behavior. For example, `max-age` was set for specific assets, guiding CloudFront on caching duration.
3. **Origin Configuration**: Used Amazon S3 as the origin for static content, offloading delivery from EC2 instances and leveraging S3’s scalability.
4. **Origin Access Control (OAC)**: Configured CloudFront to exclusively access the S3 bucket, securing assets by making them available only through CloudFront.
5. **Path Patterns and Cache Behavior**: Configured specific path patterns (e.g., `/*.jpg` for images) to route requests and tailor caching policies for different asset types.
6. **Standard Logging**: Enabled CloudFront logging to capture request data, storing logs in S3 for analysis.
7. **Real-Time Monitoring**: Monitored metrics like cache hit ratio, request rates, and latency via Amazon CloudWatch.
8. **Performance Tuning**: Enabled GZIP compression to reduce asset size, enhancing load times and user experience.
9. **Backend Code Changes**: Updated PHP code to serve product images from CloudFront, enhancing load speed and reducing EC2 load.

## Load Testing with JMeter

Our team utilized Apache JMeter to simulate varying levels of traffic on the platform, examining responsiveness, Auto Scaling, and ALB efficiency.

### 1. Environment Setup

- **Endpoint**: ALB DNS was set as the JMeter target.
- **Auto Scaling Configuration**: ASG was configured to scale with thresholds of 50% CPU for scaling out and 30% for scaling in, with a minimum of 1 instance and a maximum of 3.

### 2. JMeter Configuration

#### Thread Group Settings

- **Number of Threads (Users)**: Configured to simulate varying load scenarios.
- **Ramp-Up Period**: Defined the time for JMeter to start all threads, simulating gradual traffic increases.
- **Loop Count**: Specified how often each user repeated the test scenario.

#### HTTP Request Sampler

- **Protocol**: HTTP/HTTPS.
- **Server Name**: ALB DNS name.
- **Path**: Defined paths for specific endpoints.

#### Assertions

- **Response Code Assertion**: Verified HTTP 200 OK responses.
- **Duration Assertion**: Checked response times to be within acceptable limits (under 2 seconds).

### 3. Testing Scenarios

1. **Light Load**: Simulated low traffic with 10 users and a 10-second ramp-up, achieving low response times and minimal CPU load.
2. **Moderate Load**: Tested regular traffic with 50 users, yielding response times under 1 second and some CPU increase.
3. **High Load**: Simulated peak traffic with 100 users, causing ASG scale-out while maintaining response times within 1.5 seconds.
4. **Spike Load**: Tested a sudden surge with 150 users and a rapid ramp-up to observe ASG’s rapid response.
5. **Sustained High Load**: Evaluated performance under continuous high traffic with 300 users, where ASG maintained 2-3 instances with acceptable response times.

## Security and Compliance

### Network Security

- **VPC Design**: Used CIDR 10.0.0.0/16, with separate subnets for public and private resources.
- **Security Groups**: Restricted EC2 instances to only communicate with the load balancer or RDS as needed.
- **Network ACLs**: Configured for additional traffic filtering.

### Application Security

- **WAF Configuration**: Enabled WAF on ALB, using AWS Managed Rules to protect against SQL injection and XSS attacks.
- **SSL/TLS**: Configured HTTPS using ACM for SSL termination at the ALB, enforcing TLS 1.2/1.3.

### Data Security

- **Encryption**: RDS and S3 data encrypted at rest via AWS KMS.
- **Secrets Management**: AWS Secrets Manager secured database credentials.

### Monitoring and Logging

- **CloudWatch Alarms**: Set up for CPU usage, unauthorized API calls, and other key metrics.
- **CloudTrail Logs**: Captured all API activity for audit purposes.

## Cost Optimization

1. **Instance Sizing**: Chose `t2.micro` instances for ASG, keeping costs low while meeting processing needs.
2. **Flow Logs**: Configured VPC flow logs selectively to log all traffic for testing and subsequently narrowed scope to optimize costs.
3. **Cloudflare**: Used Cloudflare for SSL/TLS support and fast DNS resolution, benefiting from reliable, low-cost domain management.

## Lessons Learned and Future Improvements

### Key Takeaways

1. **Infrastructure as Code**: Automating deployments via CloudFormation improved consistency and reduced setup errors.
2. **KMS for Enhanced Security**: Encrypting data at rest proved essential for compliance and security.
3. **Team Collaboration**: Effective teamwork was crucial, and each member’s contribution enhanced project quality.

### Potential Improvements

1. **VPN Instead of Bastion Host**: Replacing the bastion host with VPN access to RDS would further enhance security.
2. **Private Subnets for EC2 Instances**: Moving instances to private subnets, accessed via ALB, would reduce exposure.
3. **CI/CD Pipeline**: Automating code deployment via CI/CD would allow seamless updates and reduce downtime.
4. **Automated WAF Configuration**: Automating WAF rule updates would enable quicker responses to security threats.

This README documents our architecture, choices, and insights, reflecting our commitment to building a secure, scalable, and cost-efficient platform.



<p align="center"><img src="./ui/Ecommerce-Home-Page.png" width="100%" alt="View"></p>
<p align="center"><img src="./ui/Ecommerce-Products.png" width="100%" alt="View"></p>
<p align="center"><img src="./ui/Ecommerce-Cart-Details-Page.png" width="100%" alt="View"></p>
<p align="center"><img src="./ui/Ecommerce-Admin-Login.png" width="100%" alt="View"></p>
<p align="center"><img src="./ui/Ecommerce-Admin-Registration.png" width="100%" alt="View"></p>
<p align="center"><img src="./ui/Ecommerce-Admin-Dashboard.png" width="100%" alt="View"></p>
<p align="center"><img src="./ui/Ecommerce-Admin-Dashboard (1).png" width="100%" alt="View"></p>
<p align="center"><img src="./ui/Ecommerce-Admin-Dashboard (2).png" width="100%" alt="View"></p>
<p align="center"><img src="./ui/Ecommerce-Admin-Dashboard (3).png" width="100%" alt="View"></p>
<p align="center"><img src="./ui/Ecommerce-Admin-Dashboard (4).png" width="100%" alt="View"></p>
