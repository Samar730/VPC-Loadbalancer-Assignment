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

![VPC Creation](images/01-vpc-created.png)

### Step 2: Subnet Creation

Subnets were created to logically divide the VPC into public and private network segments across two Availability Zones. This enables high availability, fault tolerance, and controlled exposure to the internet.

**Configuration details:**
- Four subnets were created across two Availability Zones:
  - Two public subnets (one per AZ) for internet-facing resources
  - Two private subnets (one per AZ) for application compute resources
- Each subnet was assigned a `/24` IPv4 CIDR block for clear IP segmentation
- Public and private subnets were distributed evenly across Availability Zones to support high availability
- Private subnets do not assign public IP addresses to resources by default

![Subnet Creation](images/02-subnets-created.png)

### Step 3: Internet Gateway

An Internet Gateway (IGW) enables communication between resources in the VPC and the public internet. It is required for any public subnet that needs inbound or outbound internet access.

**Configuration details:**
- An Internet Gateway was created and attached to the custom VPC
- The IGW enables public subnets to route traffic to and from the internet
- Only public subnets will later be associated with route tables that forward traffic to the IGW

![Internet Gateway Attached](images/03-internet-gateway.png)

### Step 4: Regional NAT Gateway

A NAT Gateway allows instances in private subnets to initiate outbound connections to the internet while remaining unreachable from inbound internet traffic. In this project, a **Regional NAT Gateway** was used to improve availability and simplify multi-AZ network design.

**Configuration details:**
- A Regional NAT Gateway was created within the VPC
- Regional mode allows the NAT Gateway to automatically scale across Availability Zones
- The NAT Gateway was configured as a **public** NAT to provide internet egress for private subnets
- Elastic IP addresses were allocated automatically by AWS for simplified management
- Private subnets will later route outbound traffic to the NAT Gateway

![Regional NAT Gateway](images/04-regional-nat-gateway.png)

### Step 5: Route Tables

Route tables control how network traffic is directed within the VPC. Separate route tables were configured for public and private subnets to enforce secure and predictable traffic flow.

**Public route table configuration:**
- Associated with public subnets in each Availability Zone
- Contains a default route (`0.0.0.0/0`) pointing to the Internet Gateway
- Enables inbound and outbound internet access for public-facing resources

![Public Route Table](images/05-public-route-table.png)

**Private route table configuration:**
- Associated with private subnets in each Availability Zone
- Contains a default route (`0.0.0.0/0`) pointing to the Regional NAT Gateway
- Allows private instances to initiate outbound internet connections while remaining unreachable from the public internet

![Private Route Table](images/06-private-route-table.png)
