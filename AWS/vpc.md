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

- Horizontally scaled, highly available and redundant that allow traffic from all the instances inside VPC/Subnet to internet
- Instances inside subnet of a VPC are only aware of their private IP address so when public subnet routes instances traffic to IGW, the IGW translates reply field to public IP of the instances so the response is sent to public IPs via IGW. The IGW upon receiving the response, translates public IP to private IP and forwards traffic to public subnet and finally reaches the instance
- For instances to communicate with Internet, ensure ACLs and Security Groups are configured correctly
- EC2 instances should have public or EIP to connect to internet

## DHCP Option Sets

- These are set of configurations available to be linked with a VPC
- The configuration includes set of key value pairs like `domain name`, `domain name server` and `netbios-node-type`
- AWS auto creates on config and assigns it to VPC with domain name defaulted to region and domain name server defaulted to `AmazonProvidedDNS`

## Elastic IP Address

- It's a static IP available from Amazon's pool of public IP addresses
- They are specific to a region
- one to one relationship between ENI and EIPs
- They are charged even when not associated with any resource
- They can be moved to different instances
- They are allocated to AWS account so need to release them explicitly to get rid of them

## Elastic Network Interface

- They can be attached to EC2 instances
- They are associated to subnet upon creation
- Multiple ENIs can be associated with one instance (multiple private IPs but only one public IP) to enable network presence in multiple subnets
- If attached instance dies, the ENI can be attached to a different instance, this way private IP address is preserved

## Endpoints

- They enable connection between VPC and other AWS services
- Access polices can be set uniquely in each route table targetting one endpoint or multiple connecting to same service

## Peering

- Network connection between VPCs in a single region
- VPC Peering request expires in one week
- Transitive peering is not supported
- If VPCs have overlapping CIDRs then they cannot be peered
- No more than one peering connection between two VPCs

## Security Groups

- Virtual stateful (allows responses for outgoing requests) firewall for EC2 instances
- Default security group is assigned to instances when launching if not explicitly assigned to custom security group
- Default security group allows are internal communication, outbound traffic and denies all inbound traffic
- 500 max security groups per VPC
- 50 max inbound and 50 max outbound per security group
- Can change security group after EC2 is launched