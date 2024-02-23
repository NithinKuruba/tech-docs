# Amazon Elastic Cloud Compute (EC2)

## Basics

- When EC2 instance is running, price charged per hour and not when stopped

- 4 dimensions for each instance type
  - vCPUs
  - Memory
  - Storage
  - Network Performance

- The ratio of vCPU to memory is constant and scale linearly as well as the price per hour

- Families
  - c4 compute optimized
  - r3 memory optimized
  - i2 storage optimized
  - G2 graphics optimized

- The network performance increases within family for higher instance types

- Some types support enhanced networking by enabling Single Root I/O virtualization, which results in more packets per second, low latency and less jitter

- Enhanced networking is only available for instances deployed in AWS VPC

- AMIs define initial state (OS, patches and applications) of instance

- All AMIs are based on x86 OSs, Linux or win

## Access

- Every instance gets unique DNS name which dies with the instance

- Public IP is unique in internets gets assigned and cannot be transferred to other instances

- Elastic IP is unique on internet and can be purchased independently and can be assigned or released from instances. It is not tied to lifecycle of instance as in with public IP

## SSH access

- EC2 uses public and private key pair to encrypt and decrypt login information

- Public keys can be stored under `~/.SSH/authorized_keys`

- User can use private key to login

- EC2 instances come with default user ec2-user

- Windows instances generates password encrypted by public key so user has to decrypt it using priv key to get the password

## Virtual Firewalls

- Security groups control inbound and outbound traffic

- If instance launched within VPC, security groups control in and out

- If instance launched within EC2 classic, only out is controlled by classic sec groups

- Every instance must have 1 or more sec groups

- Security group source/destination could contain CIDR block or another sec group

- Security group is state full, which means response is allowed for sent requests

- Security group operates at instance level



 
