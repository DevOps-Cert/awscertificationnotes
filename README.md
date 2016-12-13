# awscertificationnotes
notes to self on aws certification


# metrics

## cloudwatch
- EC2 - cpu, network, storage, status
- RAM is custom own script
- default is poll at 5minutes
- metrics stored for 2weeks

## rds metrics
- can use rds or cloudwath for metrics

## ELB metrics
- monitors every 60 seconds
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




