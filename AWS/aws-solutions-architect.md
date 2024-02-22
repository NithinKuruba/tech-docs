# AWS Solutions Architect Notes

## S3

### Introduction

- Common use cases
  - Backup and archive
  - Storage
  - Big Data analytics
  - Static website hosting
  - Disaster recovery
  - Cloud native mobile and internet app hosting

- Storage Classes
  - General Purpose
  - Infrequent Access
  - Archives (Glacier - Cold data)

- S3 can be configured with lifecycle policies(manage data flow), encryption, access management

- Block and file storage are associated with server but not the object storage (accessed over the internet)

- In object storage, data is managed in form of objects using an API

- Objects contain data and metadata; they live inside buckets

- Bucket is a simple flat folder and each object is identified by a filename or key

- Cannot work with portions of object but need to work on it wholly

- Buckets can store unlimited number of objects

- Data is auto replicated across multiple AZs within a region

### Basics

#### Buckets

- Bucket names are global, means they are unique across all AWS accounts

- 100 buckets by default per AWS account

- Data inside bucket in region A can be copied across bucket in region B

- Each can store unlimited number of objects 

#### Objects

- Object can store any kind of data irrespective of format upto 5TB

- Object's metadata is of two types; system (date, last modified, size, MD5 and HTTP metadata content type) and user metadata (tags)

#### Keys

- Key is kind of filename and can be upto 1024 bytes of UTF-8

- Keys must be unique within a bucket

#### Access

- Can use REST API or AWS CLI to perform CRUD ops

- S3 storage offers 99.999999999% durability and 99.99 availability

- Reduced Redundancy Storage (RRS) offers lower durability at lower cost

- When using S3, ensure to setup versioning, cross region replication and MFA delete to prevent accidental deletes

- S3 is eventually consistent system meaning data is available once replicated across all AZs

#### Access Control

- Access to S3 can be managed through ACLs and IAM policies

- IAM policies allow fine grained control and are associated with buckets than IAM principal

#### Static Website Hosting

- Bucket must be named as the desired website hostname

- All files must be public

- Could create a friendly DNS name that resolves to S3 URL

#### Advanced Features

- Support prefixes

#### Storage Classes

- S3 standard for frequent access offers high durability, low latency and high throughput 

- S3 standard in-frequent access offers same as above

- RRS offers slight lower durability - 4 nines

- Glacier offers secure, durable and low cost. Restoring take 3 to 4 hours and it involves copying data to RRS. It offers API for access management

#### Miscellaneous

- Life cycle policies ensure data moves to different storage types and deleted when not needed

- All data to and from S3 is encrypted through SSL

- S3 using AWS KMS can encrypt objects at rest, however even client can encrypt at client side and send to S3 as an alternative

- SSE-S3 is AWS managed keys including rotation and master key

- SSE-KMS where client manages keys and AWS managed key management. It also enables access logging and shows failed attempts


