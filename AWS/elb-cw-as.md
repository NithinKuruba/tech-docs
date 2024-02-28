# Elastic Load Balancing, CloudWatch and Auto Scaling

## Elastic Load Balancing

- It automatically distributes traffic across multiple EC2s in one or more AZs enabling high availability
- ELB supports HTTP, HTTPS, SSH, TCP
- Support CNAME and internal application facing load balancers
- Supports health checks
- It can scale automatically based on metrics
- It can integrate with Auto Scaler to spin multiple EC2 instances behind load balancer
- It also support integrated certificate management and SSL termination
- They support encrypted connections
- Types:
  - Internet Facing
  - Internal