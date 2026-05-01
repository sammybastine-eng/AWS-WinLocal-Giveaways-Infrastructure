# WinLocal Giveaways - AWS Cloud Infrastructure

## Overview
Designed and deployed a scalable, secure AWS architecture for a giveaway platform. Implemented VPC with public/private subnets, Auto Scaling, RDS MySQL, bastion host, and S3 static backup.

## Architecture Components

| Component | Purpose |
|-----------|---------|
| VPC with public/private subnets | Network isolation for different tiers |
| Bastion Host | Secure SSH access to private instances |
| Application Load Balancer | Distribute traffic across Auto Scaling group |
| Auto Scaling Group | Automatic capacity adjustment (1-2 instances, 60% CPU target) |
| RDS MySQL (Single-AZ) | Database for competition entries and user data |
| S3 Static Website | Maintenance/backup page during outages |
| Custom AMI | Pre-configured application image for rapid scaling |

## Security Implementation

### Security Groups (3-Tier Model)

| Tier | Inbound Rules |
|------|---------------|
| Bastion | SSH (port 22) - restricted to single IP |
| Application Server | HTTP from ALB, SSH from Bastion |
| Database | MySQL (3306) from Application Server only |

**Key achievement:** Database completely isolated from public internet - even if application server compromised, direct database access remains blocked.

## Proof of Concept Results

- ✅ VPC with route tables configured (public subnet has IGW route)
- ✅ Bastion host SSH connection successful
- ✅ Custom AMI created and available
- ✅ RDS MySQL connection verified
- ✅ Auto Scaling group operational (desired capacity: 1, max: 2)
- ✅ CPU target tracking policy (60% threshold, 300s warmup)
- ✅ S3 static maintenance page live

## Production Recommendations

| Service | Purpose |
|---------|---------|
| AWS WAF | SQL injection/XSS protection on ALB |
| Multi-AZ RDS | 99.999% availability, automatic failover (60-120 seconds) |

## Sustainability Measures

**Implemented:** Auto Scaling matches capacity to demand, preventing idle server waste

**Recommended:**
- AWS Compute Optimizer for Graviton migration (60% better energy efficiency)
- S3 Lifecycle Policies for infrequent access storage classes
- Scheduled scaling to zero during low-traffic periods
- VPC Endpoints to replace NAT Gateway

## Technologies Used
- AWS VPC, EC2, Auto Scaling, ALB, RDS MySQL, S3, IAM
- Bastion host architecture
- Custom AMI pipeline

## How to Deploy
```bash
# Prerequisites: AWS CLI configured
aws ec2 run-instances --launch-template LaunchTemplateName=project-launch-template
aws autoscaling update-auto-scaling-group --auto-scaling-group-name asg-B00174492 --desired-capacity 2
