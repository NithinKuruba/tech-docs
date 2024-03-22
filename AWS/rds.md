p by# Amazon Relational Database Service (RDS)

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
- It provides database endpoint for client software to run queries and it does not provide shell access
- RDS provides tools to query, analyze, modify, and administer database
- RDS provides database instance that runs database engine
- The DB instance determines cpu and memory requirements, like db.t2.micro, db.r3.8xlarge
- A DB parameter group acts as container for engine configuration, which is empty by default
- AWS Database Migration Service provides a graphical interface that simplifies the migration of both schema and data between databases. It can also convert databases
- The main aim of RDS is to simplify common tasks required to operate a relational db in a reliable manner
