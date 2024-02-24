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

- Security group is statefull, which means response is allowed for sent requests

- Security group operates at instance level and its default state is to deny all incoming traffic and allow all outgoing traffic

## Lifecycle of Instances

- Instances can be bootstrapped using a unencrypted string called `UserData` during the launch process; It contains code that shall be processed by OS

- VM import/export allows to import images as AMI's to create new instances from existing environment and also allows for exporting it back

- Instance contains metadata and is accessible to OS. `http://<ip>:<port>/latest/meta-data` provides it

- Instances can be tagged with key value pairs. They can be monitored with Amazon Cloud Watch

- Instance type can be changed after the launch and sec groups can be changed for ones launched inside VPC

- Instances can be terminated but when `termination protection` is enabled, it cannot be terminated through Console, CLI and API. However, it has no effect on terminations occurring through OS, AutoScaler or Spot Price Changes

## Pricing

- EC2 instances are charged every hour and the price varies for types of instances: On-Demand, Reserved and Spot

- On-Demand is suitable for unpredictable workload

- Reserved ones save 75% of price of On-Demand; The saving is determined by term commitment (1-3 yrs) and payment (upfront, partial upfront, no upfront)

- Spot are ideal for non-critical workloads and tolerant to terminations. They are allocated if price of it drops below customer's bid price

## Tenancy

- It allows for achieving policy and compliance needs

- Shared tenancy is default, whereas dedicated tenancy provides dedicated hardware for a custom to run their instances. Dedicated host assigns a EC2 physical server to customer

## Placement Groups

- Logical grouping of instances within single AZ for enhanced networking needs

## Storage

- Instance store is a temporary storage solution for EC2

- Instance store gets deleted when host disk drive fails, instance stops, or instance terminates

### EBS (Elastic Block Storage)

- It is a block level storage and offers a persistent solution to store data

- The data stored in EBS is auto replicated within AZ

- Multiple EBS can be mounted into an EC2 but one EBS can be assigned to only one EC2

- DIfferent types of EBS exist:

  - Magnetic Volumes (1GB - 1TB): Low price, low workloads, 100s IOPS, billed based on space allocated, upto 90MB throughput, ideal for cold data
  - General Purpose SSD (1GB - 16TB): Low price, medium workloads, upto 10000 IOPS (depends on space allocated), IOPS can be accumulated and used during high needs, upto 160MB throughput, ideal for dev and test/small to meduim
  - Provisioned IOPS SSD (4GB - 16TB): Expensive, IO intensive workloads, high performance, upto 20000 IOPS, combine/stripe multiple volumes, upto 320MB throughput, ideal for critical apps and large databases
  - Throughput optimized HDD: low cost, frequent access, upto 16TB, throughput upto 500MB
  - Cold HDD: upto 16TB, 250 IOPS, upto 250MB throughput

- EBS Optimized volumes provide best performance by reducing the contention but cost extra per hour

#### Data Protection

- Point in time snapshots are possible and are saved to S3 techology. Priced by storage space and can be transferred to a different region

- A volume can be created from snapshot but data is loaded lazily, i.e. it is restored at first request

- `DeleteOnTermination` flag should be set to false for volume to be detached before instance is terminated

- EBS support encryption, where communication between volume and instance and data at rest are encrypted using AES 256 algo. This does not impact IOPS
