# AWS VPC Load-Balanced Architecture
## Project Summary
This project demonstrates the design and implementation of a secure, load-balanced AWS Virtual Private Cloud (VPC) architecture deployed across multiple Availability Zones within a single region.

The architecture separates resources into public and private subnets to enforce network isolation. Incoming user traffic is routed through Amazon Route 53 and terminated at an Application Load Balancer (ALB) over HTTPS, using SSL/TLS certificates managed by AWS Certificate Manager (ACM).

Compute resources run on EC2 instances within private subnets and are never exposed directly to the public internet. Outbound internet access for these private instances is provided through a Regional NAT Gateway, allowing system updates and external connectivity while maintaining a strong security posture.

This design follows AWS best practices for high availability, scalability, and security, and reflects patterns commonly used in production environments.

## Architecture Diagram

The diagram below illustrates the overall AWS architecture deployed in this project, including the VPC layout, subnet segmentation, load balancing, and secure ingress and egress design.

![Architecture Diagram](images/architecture-diagram.png)

## Implementation Steps

### Step 1: VPC Creation

A Virtual Private Cloud (VPC) provides an isolated networking environment within AWS, similar to a traditional on-premises network. All resources in this project are deployed within this VPC.

**Configuration details:**
- A custom VPC was created with an IPv4 CIDR block of `10.0.0.0/16`
- This CIDR range provides sufficient address space for multiple subnets across Availability Zones
- The VPC serves as the foundational networking layer for all subsequent components
