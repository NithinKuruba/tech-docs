# Amazon Virtual Private Cloud (VPC)

## Introduction

- Virtual network with custom IP address range, subnets, route tables, network gateways and security settings
- IP address range can be CIDR `/16` (65536 IPs) to CIDR `/28` (16 IPs) and be non overlapping
- Every AWS account would have access to default VPC (created with 172.31.0.0/16) created in each region and a public subnet in each AZ within that region, with a netmask of `/20` (20 bits turned on)
- Amazon VPC consists of
  - Subnets
  - Route Tables (RTs)
  - Dynamic Host Configuration Protocol option sets
  - Network Access Control Lists (ACLs)
- Amazon VPC Optional Components:
  - Internet Gateways (IGWs)
  - Elastic IPs
  - Elastic Network Interfaces
  - Endpoints
  - Peering
  - Network Address Translation (NAT) instances and NAT gateways
  - Virtual Private Gateway (VPG), Virtual Private Network (VPN), Customer Gateway (CGW)

## Subnets

- They live in one AZ but cannot span zones
- Smallest subnet can contain CIDR `/28` (16 IP addresses)
- 5 IP addresses are reserved for AWS internal communication
- Public subnet - RT has a route to IGW
- Private subnet - RT has no route to IGW
- VPN only subnet - RT has route to only VPG

## Route Tables

- RTs allows EC2 instances in different subnets to communicate
- They decide which subnet would be public or private
- Ever RT has a default route for internal communication and it cannot be changed or removed
- VPC comes with default RT (replaceable) but additional ones can be created
- If subnet is not associated with new RT, it uses main RT of VPC
- Every route will have destination CIDR and target

## Internet Gateway

- Horizontally scaled, highly available and redundant that allow traffic from all the components inside VPC/Subnet to internet
- It does NAT for devices assigned with public IPs
-
