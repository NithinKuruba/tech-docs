# Amazon Relational Database Service (RDS)

## Databases

- Two categories: relational and non-relational (NoSQL)
- Relational database can be categorized into two types
  - Online Transaction Processing (OLTP)
  - Online Analytical Processing (OLAP)

### Data Warehouses

- A single store that collects data from various sources and used as OLAP
- OLAP get updates at most few times a day compared to many a times with OLTP
- Amazon red shift is popular OLAP solution

### NoSQL

- Improved performance compared to relational
- Key values stores or documents
- No rigid schemas like relational
- Amazon DynamoDB is a great option that enables distributed cluster spanning multi AZ

### RDS

- It handles ops like backups, patching, scaling and replication
- It replicates data across AZs, improve durability, or scale up or beyond a single database instance for read heavy database workloads