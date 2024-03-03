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
- It support actions like sending notifications or scaling resources through auto scaling policy
- Basic monitoring is free but limited preselected metrics and minimum tracking of 5 minutes
- Detailed monitoring allows for minimum of 1 minute tracking
- It does not aggregate data across regions but across AZs within a region
- AWS does not know of memory, cpu and disk consumption inside EC2 so CloudWatch supports an API endpoint to which apps can make PUT requests to send metrics
- CloudWatch can be used to store, monitor and access logs from applications running inside EC2
- Monitoring can be setup to lookout for errors in log files at app level and send notifications or take necessary actions
- Logs can be stored in S3 or Glacier
- A CloudWatch agent also can be installed in EC2 which can send metrics
- Each AWS account limited to 5000 alarms
- Metric data is retained for two weeks by default but can be moved to S3 for long term storage

## AutoScaling

- This service scales EC2 instances in response to the incoming traffic
- AutoScaling schemes:
  - Maintain Current Instance Levels: This maintains minimum number of running instances at all times. Removes unhealthy ones with new
  - Manual Scaling: Update maximum, minimum and desired capacity and auto scaling group manages the capacity. Useful for Infrequent events
  - Scheduled Scaling: Suitable for predictable schedule of events. Scaling actions are performed automatically as aa function of date and time
  - Dynamic Scaling: Auto Scaling policies dictate AutoScaling process. CloudWatch can monitor some parameters and callout AutoScaling group to update capacity
- Auto scaling components:
  - launch configuration: It is a template which AutoScaling group uses to create new instances, it includes Amazon machine image, EC2 instance type, Security group, and instance key pairs. Only one launch config at a time is allowed per AutoScaling group. 100 is the default number of launch configurations allowed per region. Any attempt to go over limits of other services through AutoScaling is not allowed. Ex. only 20 EC2 are allowed per region by default. A launch config cannot specify both On-demand and spot isntances. Use different launch configuration for on demand and spot instances.
  - AutoScaling Group: it's a collection of EC2 instances. It contains name, min, max and optional desired capacity, which if not specified then default would be the min. It can use default on-demand instances or spot instances. If choosing spot then we need to mention bid price in launch config.
  - Scaling Policy: CloudWatch alarms can be set to dynamically scale instances. The policy is set of instructions that tells AutoScaling group whether to scale out, launching new instances or scale in and terminate insurances. More than one scaling polices can be associated with AutoScaling group. Best practice is scale out quickly and scale in slowly. Cooldown period is where AutoScaling is suspended for short amount of time. When EC2 instance is launched, you are billed per hour so scaling in slowly would remove the need of scaling out for short spikes. Stateless Instances will more gracefully enter or exit the AutoScaling group.