
# Swiftly - Scalable and Secure E-commerce Platform on AWS

## Project Overview

This project involved designing, developing, and deploying a secure, scalable e-commerce platform using AWS services. The platform was created to handle fluctuating user traffic, protect sensitive customer data, and deliver content efficiently worldwide. Throughout the project, we followed best practices for infrastructure management, security, performance optimization, and monitoring.

### Key AWS Services Utilized:
- **Auto Scaling** for dynamically managing EC2 instances based on demand.
- **RDS** for hosting the MySQL database with Multi-AZ deployment for high availability.
- **WAF** for protection against common web vulnerabilities.
- **CloudFront CDN** to accelerate content delivery globally.
- **Encryption** for securing data both at rest and in transit.

---

## Project Phases

### Phase 1: Infrastructure Setup

In this phase, we set up the core infrastructure required for our e-commerce platform.

#### Key Steps:
1. **EC2 Instances Deployment:**
   - We used a predefined Amazon Machine Image (AMI) configured with a web server and a simple e-commerce application.
   - Set up an Auto Scaling group to automatically adjust the number of instances based on traffic, with a maximum of 3 instances.
   - Configured an Application Load Balancer (ALB) to distribute incoming traffic across the EC2 instances, ensuring a balanced load and high availability.

2. **RDS for MySQL:**
   - We created an RDS MySQL instance to store essential data, including customer information, product details, and order history.
   - Enabled Multi-AZ deployment to ensure high availability and fault tolerance.
   - Used AWS Key Management Service (KMS) to encrypt the database at rest, safeguarding sensitive data.

3. **Auto Scaling Configuration:**
   - Defined Auto Scaling policies to manage the scaling of EC2 instances based on CPU utilization or incoming traffic.
   - Configured CloudWatch Alarms to monitor CPU usage and trigger scaling events when predefined thresholds were met, ensuring efficient resource utilization.

#### Deliverables:
- EC2 instances configured in an Auto Scaling group, with traffic distributed by an ALB.
- A fully functional RDS instance with tables for managing product, user, and order data.
- Basic CRUD operations implemented on the e-commerce platform to interact with the RDS database.

---

### Phase 2: Securing the Application

This phase focused on implementing security measures to protect the application from web vulnerabilities and secure data both in transit and at rest.

#### Key Steps:
1. **AWS WAF Configuration:**
   - Configured AWS WAF (Web Application Firewall) with the Application Load Balancer to filter and block malicious requests.
   - Implemented AWS Managed Rules to protect against common attacks like SQL Injection and Cross-Site Scripting (XSS).
   - Created custom rules for additional security based on our application's specific needs.
   - Monitored blocked requests and rule violations using WAF logs in CloudWatch to gain insights into potential security threats.

2. **Data Encryption in Transit:**
   - Deployed SSL/TLS certificates through AWS Certificate Manager to enable HTTPS for secure communication between clients and the application.
   - Configured encrypted connections between the EC2 instances and the RDS MySQL database to protect data as it moved within the infrastructure.
   - Ensured that sensitive customer data, such as personal information and payment details, was encrypted both in transit and at rest.

#### Deliverables:
- AWS WAF set up with rules to protect against common web threats.
- SSL certificates installed on the ALB to secure client-server communication.
- Encrypted database connections between the e-commerce application and RDS.

---

### Phase 3: Content Delivery and Performance Optimization

In this phase, we focused on accelerating content delivery and optimizing platform performance to enhance the user experience.

#### Key Steps:
1. **CloudFront CDN Setup:**
   - Configured Amazon CloudFront to cache and deliver static assets (e.g., images, CSS, JavaScript) for faster access by users worldwide.
   - Integrated CloudFront with an S3 bucket to serve static content, such as product images, improving load times for users regardless of their geographical location.

2. **Performance Tuning:**
   - Enabled GZIP compression on the web server to reduce the size of assets like HTML, CSS, and JavaScript files, decreasing page load times.
   - Configured CloudWatch to monitor performance metrics, such as latency and request rates, and adjusted configurations to optimize load times and system responsiveness.

3. **Caching Configuration:**
   - Set up caching policies for dynamic content, including product pages, to reduce load on the EC2 instances and improve page load speed.
   - Configured CloudFront and the web server to cache frequently accessed content, reducing the frequency of calls to the backend for commonly requested pages.

#### Deliverables:
- CloudFront distribution configured to accelerate content delivery for global users.
- S3 bucket set up for static assets, integrated with CloudFront.
- Performance tuning and caching implemented to improve user experience and reduce server load.

---

### Phase 4: Testing and Monitoring

During this phase, we conducted testing to ensure the platform could handle traffic fluctuations and implemented monitoring for ongoing operational insights.

#### Key Steps:
1. **Stress Testing:**
   - Used Apache JMeter to simulate high traffic levels, testing the Auto Scaling group’s response to increased demand.
   - Monitored RDS performance under heavy query loads to ensure the database could handle peak periods without degradation.
   - Verified that the Auto Scaling policies functioned as expected, with instances scaling up or down based on CPU utilization.

2. **Monitoring Setup:**
   - Configured CloudWatch dashboards to track critical metrics, including CPU utilization, error rates, database connections, and traffic patterns.
   - Set up AWS CloudTrail to log security events, allowing us to audit actions taken on AWS resources and monitor for potential unauthorized activity.
   - Enabled AWS Config to track changes to our infrastructure and detect any configuration drift from our intended setup.

3. **Cost Optimization:**
   - Used AWS Cost Explorer to analyze the cost of our infrastructure and identified potential areas for cost savings.
   - Recommended Reserved Instances and Savings Plans for long-term cost management, particularly for EC2 instances and RDS, to reduce operational expenses.

#### Deliverables:
- Stress test results showing the platform's ability to scale and handle traffic surges.
- CloudWatch dashboard displaying key performance and security metrics.
- Cost optimization recommendations based on usage analysis to maximize budget efficiency.

---

This README provides a comprehensive overview of our team's work in designing, securing, optimizing, and testing a scalable e-commerce platform on AWS.



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
