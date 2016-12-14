# aws system operations - associate certification notes
_Notes to self for AWS sysops cerification grokking. Consume at own risk._


# metrics

## cloudwatch
- EC2 - cpu, network, storage, status
- RAM is custom own script
- default is poll at 5minutes
- metrics stored for 2weeks

## rds metrics
- can use rds or cloudwath for metrics

## ELB metrics
- monitors every 60 **seconds**
- if no data, nothing shows up
- surge queue length - is requests pending to get to an instance
- spillover count - number of requests being rejected by a full queue
- 2XX http code is success
- 3XX http code is pending user action
- 4XX http code is client error
- 5XX http code is server error

# elastic cache

## memcached
- multi-threaded - can handle loads up to 90%, so add more nodes if this happens
- should be 0MB most of time for swap but add more if >50 MB
- can scale up(memory) or out(more nodes)

## reddis
- not multithreaded
- so to work out usage its load/#of cores in processor
- scale out only(more nodes)

## evictions
- when memory is full and another item needs to be moved out

## concurrent connections
- can mean either a traffic spike or application is not releasing connections

# billing

- consolidated billing allows a master account to be created for collecting costs from multiple AWS accounts

# ec2 types

- spot price - bid for spare instances e.g. a uni runs a big data job on 4am on saturday then write results to s3 to keep the data as instances terminated once price goes below spot price
- like a rug - can be terminated anytime
- on-demand - paid hourly, no commitment, great for auto-scaling spiky trafffic
- reserved - up to 75% off, for steady state needs, e.g for a website single instance, then augement with scaling group of on-demand to meet spiky traffic
- reserved types are light(1year), medium(3 year) and heavy(3 year) - cheaper according to usage

# costing
mix of reserved, medium and heavy utilisation as well as auto-scaled group - reserved for min, medium for m-f traffic and auto-scaled for 6 crazy traffic a year
- standard, convertible(change attributes) and scheduled instances 


# elasticity and scalability
- elasticity - is short term demand(e.g. hours or days)
- scalability - is long term demand(weeks, days, months, years)

e.g. ec2 scalability - increase size vs elastic - add instances


# RDS AZ failover

- backup and restores happen from the secondary 
- can force failover by rebooting an instance via console or api
- for high availability not scaling/elasticitiy
- NOTE! need to have a backup enabled/created/snapshot in place before a read replica can be created
- create read -only replica in other AZ but not multiple however...
- can have backups of read-replicas to be put in mult AZ
- promote read-only replica to make it the primary(make it read-write)
- reboot of db has option to reboot to failover
- up to 5 read replicas for elastic read heavy apps
- multi-az is for failover

# troubleshooting auto-scaling

- associated key pair not there .e.g deleted after creation, or compromised
- security group deleted
- check the auto-scaling config
- invalid EBS device mapping
- attaching ebs to marketplace ami that uses instance store(non-persistent storage)

# services with root/admin access

- elastic bean stalk
- elastic mapreduce
- opsworks
- ec2


## ELB
- for balancing across AZ
- health check interval x (healthy, unhealthy) interval = total time
- stick session ensures a user is routed to the same web server for a session(not enabled by default)
- duration based session cookie created by the load balancer
- or application generated cookie stickiness
- question, why ASG adds servers but web server still having same load - stickiness set too long
- can pre-configure ELB by requesting from AWS for .e.g load tests, with start and end dates

# backup and DR
services - regions, storage(s3, glacier, ebs, direct connect, storage gateway)
storage gateway - cached, stored or virtual tape library to s3 or glacier
- recovery time objective(rto) - time to recover
- recovery point objective(rpo) - acceptable level of data loss

### trad backup/restore - use s3 and glacier
- copy to s3, ensure to set access, retention and encryption

### pilot light infrastruture is rds db, maybe directory services or exchange servers
- light up ami's, enis( for static ips), elb for app and web servers to light up
- essentially have a read-only db, then promote it, turn on the app/web servers, re-direct route 53 cname entries



# AWS services and automated backups
- RDS - yes
- following have auto backup
- elastic cache - yes
- redshift - yes

ec2 - not auto, can take manual snapshots
- snapshots are incremental!

# upgrading ebs volume types
- can take a snapshot, then restore it as a volume as a diff type, then attach it to an ec2 instance

# storing logfiles and backups
- store it in s3, then to glacier
- cloudwatch
- s3 logging

# identity terms
- federation - combine users from one service(active dir e.g) to another face book
- identity broker - connect identities from one to another - need to write your own
- identity store - id repo
- identify - the id key

### example EXAM Q - how does a web app, connect to aws and get temp access to s3
```
answer 1- identity broker which we need to code goes to active dir first, then STS, then app gets temp access to s3

answer 2 - identity broker which we need to code goes to active dir first, then app assumes a role which then gets a STS token, then app gets temp access to s3
```


# Security

- shared responsibilities - they look after underlying infrastructure up to host level generally
user responsible for whats on the cloud or connect to the cloud

- AWS manage security of managed services - rds, redshift, elastic map reduce
- IAAS - e.g. ec2, vpc, s3 under user control
- account management (especially root account)- use MFA

- storage decommissioning using these standards
DoD 
NIST

AWS protect against Ddos, packet sniffing, man in middle, ip spoofing, port scanning

- trusted advisor - only assesses IAM really

- can encrypt data but only on higher spec ec2 instances m3, c3, r3

# route53
## failover from a primary to secondary site
### how to setup and test it
- create two ec2 instances behind elb in two different zones
- create a health check on the primary site
- create in route 53 two alias entries setup with the same URL name with one as primary with the health check and the other as secondary
- kill the primary site and watch it switchover to the secondary site

## routing policy: latency routing
- give the best user experience by routing users to closest AZ
### how to setup and test it
- create two alias entries with latency routing, setup to point to a different availability zone
- run a browser to the URL and the closest AZ in the latency routing rules will show route from the closest AZ


# AWS direct connect
*hook straight into AWS*
- lets you connect to private/public resources in AWS

### EXAM Q: exam q why? perche? bakit? instead of VPN?
```
- consistent network performance & bandwidth
- not connecting over internet
- dedicated private connection
- VPN can die
- VPN for low to moderate traffic
- direct connect for serious traffic
- all about bandwidth
- dont' fall for trick saying its about resiliancy! go with the bandwidth answer
```

# networking
**NOTE:** `one subnet = one AZ`

3 addresses reserved by VPC *.*.*.1(router), *.*.*.2(dns),*.*.*.3(future tbd)

## setup a vpc with private and public subnets
- create VPC
- create 2 subnets
- create internet gateway and attach to VPC
- create route table and add a route to the internet 
- then associate a subnet to the route table with internet access
- update the public subnet group to auto assign a public IP so any ec2 instances will be assigned an address

# NAT gateways vs instances 
## NAT gateways
- use in production as they scale
- no worries for patching as AWS does it
- no security groups to manage
- auto assigned IP address
- takes time to spin up
- saves time from having to look after own NAT/DMZ
- preferred use for enterprise


## NAT instances
- disable source/destination check
- must be in public subnet
- must have route from private subnet to the NAT
- bottlenecks can be fixed with bigger instances
- can use auto-scaling and multi-az zones 
- always behind a security group


# NAT and bastion
- NAT provides net access to ec2 instances in private subnet reaching out to internet, while bastion is for providing ssh/rdp access to private ec2 instances from the internet
- bastion is a locked down ec2 which allows access to private resources
- Really lock down bastion server
- NAT instances will always be behind a security group
- NAT gateways does all the patching, has high availability

### EXAM Q: how to set up high availability bastion box
```
- two public subnets in two different availiability zone
- setup auto-scaling group set to 1
- bring up ec2 instance in one availability zone
- setup a route53 with a health check
- in failure, bring up bastion in other availability zone
```

# VPC flow logs
capture all traffic and reports to cloudwatch
## to setup
- select a VPC
- add a flow log
- create IAM role for the flow log via the flow log screen
- go to cloudwatch and create a log group
- go back to to the VPC and create the log
- May need to also add a log stream to the log group

# basic exam tips
## VPC
- VPC is a logical datacentre in VPC
- VPC consists of IGW, route tables(where traffic is to be directed), network ACL, subnet, security groups
- 1 subnet = 1 az
- security groups are stateful, network ACL are stateless
- can peer VPCs, also with other AWS accounts
- no transitive peering e.g. can't go from one AWS VPC to another through another
- security groups act as firewalls at EC2 instances
- ACL act as firewalls at subnet level
- IP range allocated in VPC via CIDR, is then further sub-divided in each subnet 
- 1 route table: many subnets

## NAT instances
- NAT instances must be in public subnet, must have elastic IP address, must be a route from private subnet to the NAT instance
- bottlenecks in NAT instances usually related to instance sizes
- NAT instances high availability can be done using auto-scaling group, multi-az and scripted failover
- behind a security group

## NAT gateway
- use this not NAT instances! 
- autoscaled to 10GB/s, no patching, auto-assigned IP

## Network ACL
- each VPC has a ACL
- default ACL allows all inbound/outbound traffic
- each subnet requires a ACL
- one ACL can be associated to many subnets 
- e.g. one ACL: many subnets
- block IP addresses using ACL

## resilient architecture setup
- have at least 2 public/private subnets in different availability zones
- ELB in two different public subnets
- put bastion with ASG set to 2, with route53 in front of it
- NAT instances with one in each AZ