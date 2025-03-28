# Two-Tier AWS Architecture

A scalable and highly available two-tier architecture deployment with web application servers and RDS database.

## Architecture Components

This project deploys a complete two-tier infrastructure on AWS with the following components:

- VPC with public and private subnets across multiple Availability Zones
- Application Load Balancer for traffic distribution
- EC2 instances for the web application tier
- RDS MySQL database in a private subnet
- Internet Gateway and appropriate routing
- Security Groups for access control

## Infrastructure Details

### Networking
- **VPC**: CIDR block `10.0.0.0/16`
- **Public Subnets**:
  - Public Subnet 1: `10.0.0.0/18` (AZ-1)
  - Public Subnet 2: `10.0.64.0/18` (AZ-2)
- **Private Subnets**:
  - Private Subnet 1: `10.0.128.0/18` (AZ-1)
  - Private Subnet 2: `10.0.192.0/18` (AZ-2)
- **Internet Gateway**: Attached to VPC for public internet access
- **Route Tables**: Configured for public and private subnet routing

### Compute
- **EC2 Instances**: One instance deployed in each public subnet for high availability
- **Elastic IPs**: Associated with EC2 instances for static public addressing

### Database
- **RDS MySQL**: Deployed in private subnets for security
- **Multi-AZ**: Configured for high availability

### Load Balancing
- **Application Load Balancer**: Distributes traffic to EC2 instances across AZs
- **Health Checks**: Monitors EC2 instance health

### Security
- **Security Groups**: Configured for EC2 and RDS to control traffic

## Architecture Diagram

```
                                      +----------------+
                                      | Internet       |
                                      +--------+-------+
                                               |
                                      +--------v-------+
                                      | Internet       |
                                      | Gateway        |
                                      +--------+-------+
                                               |
                          +--------------------+--------------------+
                          |                    |                    |
                  +-------v------+     +-------v-------+           |
                  | Public Route |     | Application   |           |
                  | Table        |     | Load Balancer |           |
                  +-------+------+     +-------+-------+           |
                          |                    |                    |
      +---------+---------+                    +-----------+--------+
      |         |                                          |        |
+-----v------+  |  +--------------+             +---------v--+     |  +------------+
| Public     |  |  | Public       |             | EC2        |     |  | EC2        |
| Subnet 1   <-----+ Subnet 2     |             | Instance 1 |     +--+ Instance 2 |
| 10.0.0.0/18|  |  | 10.0.64.0/18 |             +---------+--+     |  +------+-----+
+------------+  |  +--------------+                       |        |         |
      |         |         |                               |        |         |
      |         |         |                               |        |         |
+-----v------+  |  +------v-------+                       |        |         |
| Private    |  |  | Private      |                       |        |         |
| Subnet 1   <-----+ Subnet 2     |                       |        |         |
|10.0.128.0/18|  |  |10.0.192.0/18|                       |        |         |
+------------+  |  +--------------+                       |        |         |
      |         |         |                               |        |         |
      +---------+---------+                               |        |         |
                |                                         |        |         |
         +------v------+                                  |        |         |
         | RDS MySQL   |<---------------------------------+--------+---------+
         | (Multi-AZ)  |
         +-------------+
```

## Deployment Benefits

- **High Availability**: Multi-AZ deployment for both application and database tiers
- **Scalability**: Easy to scale EC2 instances horizontally behind the load balancer
- **Security**: Database isolated in private subnets, not directly accessible from the internet
- **Performance**: Load balancer distributes traffic efficiently across multiple instances

## Getting Started

1. Clone this repository
2. Configure your AWS credentials
3. Deploy the architecture using the provided CloudFormation template or Terraform code
4. Access your application via the ALB DNS name provided in the outputs

## Prerequisites

- AWS Account
- AWS CLI configured with appropriate permissions
- Basic knowledge of AWS services
