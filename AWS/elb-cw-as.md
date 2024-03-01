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
    - Elastic load balancers in VPC support only IPV4 but the ones in EC2 classic support IPV6 as well
  - Internal: can be used to distribute the traffic to EC2 instances sitting in private subnet
  - HTTPS:
    - It uses TLS for encryption purposes
    - It requires SSL certs for performing termination
    - A single cert with multip subject alternative name (SAN) can support multiple websites running inside EC2s

### Listeners

- Listens for the incoming requests
- It support HTTP, HTTPS, TCP and SSL

### ELB Configuration

#### Idle Connection Timeout

- ELB maintains two connections for every request, one with sender and other with destination. Post idle time, it closes the connection
- We can increase the idle time with this setting
- Enable `keep-alive` in EC2 webserver settings so that ELB reuses existing connections reducing CPU utilization

#### Cross Zone Load Balancing

- This enables traffic distribution irrespective of EC2 instaces across different AZs

#### Connection Draining

- To stop ELB from sending traffic to deregistering or unhealthy instances
- This setting enables maximum time ELB can keep the connections open before terminating them
- Default is 300 secs and max can be 3600 secs

#### Proxy Protocol

- It is an internet protocol used to carry connection information from the source to the destination
- It uses human readable header format
- Example `PROXY TCP4 198.51.100.22 203.0.113.7 35646 80\r\n`

#### Sticky Sessions

- It is also known as session affinity, where ELB binds user session to a specific instances and forwards all the requests from the user to same instance
- This is useful if your application does not maintain session cookie

#### Health Checks

- A health check is a ping, a connection attempt, or a page that is checked periodically

## CloudWatch

- A service to monitor AWS resources and applications in real time
- It can collect metrics, create alarms that send notifications and make changes to resources being monitored based on the rules set
