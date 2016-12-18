# cloudwatch
secure logging - requires creation of a role with cloudwatch rights, then assign it to ASG so each EC2 instance will use it

# EBS
- PIOPS - Provisioned Input Output Per Second
- snapshots occur asynchronously & can be taken while read/write is still happening

# DyanamoDB
- managed noSQL service
- table --has--> item --consists of--> attributes
- partition key - primary key
- composite key - consists of partition key to group, then a sort key attribute to sort the item in a group
- create tables and scale up/down throughput to the tables
- a partition is stored on SSD storage
- on creation allocate read-write throughput
- auto replicated across AZ


# SQS
- standard queues - all in messages, process at least once
- fifo - sequential messages, processed once
- delay seconds - delays how long a message will be invisible once received on a queue
- visibility timeout - delays how long a message will be invisible after being picked up from queue
- short polling is default with wait time of 0 and polls a subset of servers
- long polling polls all servers and returns message as soon as available
- long polling can reduce cost by reducing amount of empty messages received
- set the wait time longer to have long polling

## messages on queues
- can use message timers to delay individual messages
- state of "inflight" when its taken by a consumer but not deleted from the queue
- by default messages are retained for 4 days

# EMR
- runs on top of ec2

## architecture
- master node - distributes work to data and tasks to nodes
- core node(slave node to run tasks and store data)
- task node - only runs tasks


# AWS well architectured framework
## TL;DR
The AWS Well-Architected Framework is based on five pillars—security,
reliability, performance efficiency, cost optimization, and operational
excellence.

## general design principles of cloud
- don't guess capacity, scale up and down as needed
- test at product capacity
- automate provisioning, so can run tests with different parameters
- allow for evolutioning architecture - evolve it as required to meet biz needs
- measure and evolve - make decision based on metrics collected on usage, performance
- have game days to simulate prod situations to exercise organisational experience

### security
- have hardened AMIs used for each
- have a capability to spin up forensic environments
- log access and changes with cloudtrail and config
- have a data classification to decide on encryption, retention times for data in transit/rest
- key tools are AWS config, cloudtrail, iam(least privilege), VPC, data protection in EBS, RDS, S3, ELB, KMS

### reliability
- ability to recover from incidents, scale up/down as needed, survive misconfiguration or transient network issues
- use many smaller instances rather than a single large instance to avoid single points of failure
- monitor using cloudtrail to added/remove resources a needed
- how is it integrated with existing on-premise resources
- in failure, don't fix, spin up new instance, then diagnose broken instance separately
- or spin up a whole new instance and diagnose it there
- keep testing for failure to measure time to recover and acceptable data loss limits

### performance
#### compute considerations
- what type or architecture e.g. ETL, pipeline, event
- use appropriate compute - CPU vs memory intensive
- also what type of compute - server, container, serverless

#### storage considerations
*The optimal storage solution for a particular system will vary based on the kind
of access method (block, file, or object), patterns of access (random or
sequential), throughput required, frequency of access (online, offline, archival),
frequency of update (WORM, dynamic), and availability and durability
constraints. Well-architected systems use multiple storage solutions and enable
different features to improve performance.
- can use snowball for archival*

#### database considerations
- RDBMS, noSQL, or warehouse
- online processing or analytics?
- use read-replicas for read type transactions
- multi-AZ for high availiabilty failover
- what availability, consisterncy and partition tolerance being used?

#### networking considerations
- performance vs consistency - e.g use cloudfront caching or elasticcache
- network helpers include optimised Ec2, s3 transfer acceleration, direct connect, latency route 53 rules, regions/availability zones, VPC endpoint

#### monitoring considerations
- cloudfront is the dashboard
- ingets information from s3, kinesis, SQS, lambda and have alarms to respond to events

### cost optimisation
- design for usage e.g dev/test can be turned off out of hours/weekends
- tag all resources to provide cost tracking and identification
- instead of heavy upfront planning and design, adopt a evolve as needed model - focus on speed
- cost optimisation can be done later, don't design heavily upfront for overprovisioned/under utilised resources
- have a model to adopt new AWS services, retire old/orphaned services
- ec2 can be provisioned with mix of on-demand reserved and spot instances - based on demand
- is demand steady or spiky, predictable, unpredictable?
- can setup SNS message to monitor usage
- also cross AWS region transfer


#### operational excellence

*operations metrics, unexpected anomalies, failed deployments, system
failures, and ineffective or inappropriate responses to failures. System
and architecture documentation should also be captured and updated
using automation as environments and operations evolve.*

- runbooks - for standard operations
- playbooks - for responses to unexpected events
- make small, incremental changes, not big batches, rollback/recovery automate
- test responses to unexpected events
- learn from ops/failures
- cloudformation, CI/CD - small steps
- automate routine tasks
- have in place responses for alerting, mitigation, remediation, rollback, and recovery.
