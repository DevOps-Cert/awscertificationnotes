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
- scale up(memory) or out(more nodes)

## reddis
- not multithreaded
- take 90/cpu cores
- uses reserved memories
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
- 
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

```
example EXAM Q - how does a web app, connect to aws and get temp access to s3

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



