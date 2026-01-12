# AWS RDS MySQL Read Replica with Monitoring and Alerts

## Project Overview

This project demonstrates the deployment of a secure, production-style Amazon RDS MySQL architecture using a Single-AZ primary database, a same-region read replica, private networking, monitoring, and alerting.

The entire setup is deployed inside a private VPC with no public database access. Connectivity is validated using a private EC2 instance accessed via a bastion host. Monitoring is implemented using Amazon CloudWatch and SNS to provide real-time alerts.

---

## Objectives

- Deploy a MySQL 8.0 RDS instance in Single-AZ mode
- Create a same-region read replica to demonstrate read scaling
- Implement a private-only database architecture
- Configure custom parameter groups for slow query logging
- Enable CloudWatch monitoring and SNS-based alerts
- Validate database connectivity and replication
- Perform controlled CPU load testing to trigger alarms

---

## Architecture Overview

The architecture follows production best practices for private database deployments:

- Custom VPC with public and private subnets
- Bastion Host for secure SSH access
- Private EC2 instance for database connectivity testing
- Primary RDS MySQL instance (Single-AZ)
- RDS Read Replica in the same region
- Custom DB parameter group
- CloudWatch CPU alarm with SNS notifications
- NAT Gateway for controlled outbound access

All database resources remain inaccessible from the public internet.

---

## AWS Services Used

- Amazon RDS (MySQL 8.0)
- Amazon EC2
- Amazon VPC
- Security Groups
- NAT Gateway
- AWS CloudWatch
- Amazon SNS
- IAM
- Amazon Linux 2

---

## Implementation Summary

### 1. Network Setup
- Created a custom VPC with public and private subnets
- Configured Internet Gateway and NAT Gateway
- Associated correct route tables for controlled traffic flow

### 2. EC2 Setup
- Launched a public bastion host for SSH access
- Launched a private EC2 instance for database testing
- Installed MariaDB client on private EC2

### 3. RDS Primary Instance
- Engine: MySQL 8.0
- Deployment: Single-AZ
- Storage: gp3
- Public access: Disabled
- Backups enabled
- Enhanced monitoring enabled

### 4. Read Replica
- Created a same-region read replica
- Verified replication and read-only behavior

### 5. Parameter Group Configuration
- Enabled slow query logging
- Configured long query execution threshold
- Rebooted RDS instance to apply static parameters

### 6. Security Group Design
- Bastion SG: SSH from trusted IP only
- Private EC2 SG: SSH from Bastion SG
- RDS SG: MySQL (3306) allowed only from Private EC2 SG

### 7. Monitoring and Alerts
- Created CloudWatch alarm for CPU utilization > 70% for 5 minutes
- Integrated alarm with SNS email notifications
- Confirmed alert delivery via email

### 8. Testing and Validation
- Verified EC2 to primary RDS connectivity
- Performed write operations on primary DB
- Confirmed read replica synchronization
- Verified write attempts fail on replica
- Generated CPU load to trigger CloudWatch alarm

---

## Testing Results

- Primary database accepted write operations
- Read replica reflected replicated data
- Read replica blocked write attempts
- Replication lag observed as zero
- Slow query logging enabled successfully
- CloudWatch alarm triggered as expected
- SNS email notification received

---

## Challenges and Solutions

### Connectivity Timeouts
- Issue: EC2 could not connect to RDS
- Cause: Incorrect subnet placement and conflicting security groups
- Solution: Launched EC2 inside RDS private subnet and cleaned SG configuration

### Parameter Group Not Applied
- Issue: Slow query parameters not taking effect
- Cause: RDS reboot required
- Solution: Rebooted RDS instance

### Alarm Not Triggering
- Issue: CPU usage did not exceed threshold long enough
- Cause: Insufficient load
- Solution: Executed parallel benchmark queries to sustain CPU load

---

## Cleanup Strategy

All AWS resources were deleted in the correct order to avoid unnecessary charges, including:

- Read Replica
- Primary RDS instance
- Parameter and subnet groups
- EC2 instances
- NAT Gateway and Internet Gateway
- Route tables, subnets, and VPC

---

## Key Learnings

- Designing private-only RDS architectures
- Implementing read replicas for scalability
- Managing subnet groups and routing for RDS
- Configuring security group to security group access
- Applying and validating DB parameter groups
- Monitoring databases using CloudWatch
- Implementing alerting with SNS
- Troubleshooting real-world AWS connectivity issues

---

## Author

**Shloka Kamdar**  
Date: 04-12-2025  
