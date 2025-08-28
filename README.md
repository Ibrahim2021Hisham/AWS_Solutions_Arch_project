# GlobalPress CMS - AWS Architecture Overview

## 📌 Overview
GlobalPress is a high-traffic content management system designed for a global audience. This AWS architecture is engineered for high availability, fault tolerance, and scalability. The infrastructure is deployed across multiple Availability Zones (AZs) in a single region, following a three-tier architecture (Web, App, and Data), with additional components for global performance, security, and hybrid connectivity.

## 📌 Architecture Diagram
![Solution Architecture]<img width="1382" height="991" alt="Solution" src="https://github.com/user-attachments/assets/dfc9beff-008a-4aef-badf-aa3309b10216" />


## 🏗️ Infrastructure Design

### 1️⃣ Networking & Subnets
- **VPC**: `10.0.0.0/16`
- **Public Subnets** (2): `10.0.10.0/24` (AZ1) & `10.0.20.0/24` (AZ2)
  - Hosts NAT Gateways and public-facing load balancers.
- **Private Subnets** (2): `10.0.100.0/24` (AZ1) & `10.0.200.0/24` (AZ2)
  - Hosts application EC2 instances and the RDS database.

### 2️⃣ Compute Layer (High Availability)
- **Web/App Tier**: Two EBS-backed EC2 instances, one deployed in each private subnet (`10.0.100.0/24` & `10.0.200.0/24`).
- **Load Balancer**: An Application Load Balancer (ALB) in the public subnets distributes traffic evenly across the EC2 instances in both AZs.

### 3️⃣ Data Tier
- **Amazon RDS (Multi-AZ Deployment)**:
  - Primary instance in one private subnet (e.g., `10.0.100.0/24`).
  - A synchronous standby replica in the other private subnet (`10.0.200.0/24`) for automatic failover.
  - Encryption at rest is enabled (Requirement #9).

### 4️⃣ Global Performance & Decoupling
- **Amazon CloudFront**: A global Content Delivery Network (CDN) is used to cache static content and ensure good performance for users across the globe (Req #6).
- **Amazon SQS**: To decouple the web/app tier from the database tier (Req #8), asynchronous processing tasks are offloaded to queues, preventing the database from being overwhelmed by write requests during traffic spikes.

### 5️⃣ Security & Access Control
- **Security Groups (Layer 4)**: Act as stateful virtual firewalls for EC2 and RDS instances.
- **Network ACLs (Layer 4)**: Provide stateless subnet-level traffic filtering.
- **IAM Roles**:
  - **EC2 Instance Profile**: Grants the application on the EC2 instances programmatic access to required AWS services (SQS, S3, etc.) (Req #4).
  - **IAM Users/Groups**: For administrators and developers requiring programmatic and console access (Req #4).

### 6️⃣ Hybrid Connectivity
- **AWS Direct Connect**: Provides a primary, low-latency, private connection to the corporate data center.
- **IPSec VPN**: Serves as a secure, encrypted internet-based backup connection (Req #11).

### 7️⃣ Domain & DNS
- **Amazon Route 53**: Used for domain registration (Req #5) and DNS routing, which can route users to the CloudFront distribution or the ALB based on the record type.

## 🔧 Deployment
This infrastructure can be deployed using AWS CloudFormation or Terraform. The code for this will be located in the `/infrastructure` directory.

## 📝 Notes
- This design meets all 11 requirements outlined in the project brief.
- Cost optimization considerations (e.g., using Spot Instances for non-critical workloads) can be added in a future iteration.

## 👨💻 Author
Ibrahim Hisham Abdel Azim Alkhouly
