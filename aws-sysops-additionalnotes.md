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
