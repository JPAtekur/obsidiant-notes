### Lambda
* A function can use up to 5 layers at a time
* Environment variables: 4 KB
* RAM: 128 MB - 10GB
* To enable your Lambda function to access resources inside your private VPC, you must provide **VPC subnet IDs** and **Security Group IDs**. AWS Lambda uses this information to set up ENIs.

### HPC
* **Cluster placement group**
* **Elastic Fabric Adapter (EFA)**
	Enhanced for HPC
	Supported in **Linux** only
- **AWS Batch**
- **AWS Parallel Cluster**
	 - Ability to enable **EFA** on the cluster

### Workflows
* Step Functions
	*  Used to build **serverless workflows** to **orchestrate Lambda functions**
	*  Maximum workflow execution time: 1 year
* Simple Workflow Service (SWF)
	*  Outdated service (step functions are preferred instead)
	*  **Ensures that a task is never duplicated** (could replace standard SQS queues)
	*  **Built-in human intervention step**

### Instance Store
- **Hardware storage directly attached to EC2 instance** (cannot be detached and attached to another instance)
-  **Ephemeral storage** (loses data when the instance is stopped, **hibernated** or terminated)
- You can specify the instance store volumes only when you launch an instance. You can’t attach instance store volumes to an instance after you’ve launched it.

### Elastic Block Storage (EBS)
- **Bound to an AZ**
- `DeleteOnTermination` attribute can be updated for the root EBS volume only from the CLI
- **gp3**
	*  **3,000 lOPS baseline** (max 16,000 - independent of size)
* **gp2**
	*  **Burst IOPS up to 3,000**
* Provisioned IOPS SSD
	*  Max IOPS: **64,000 for Nitro EC2 instances** & **32,000 for non-Nitro**
	*  io2 have more durability and more IOPS per GB (at the same price as io1)
	* **io2 Block Express**
		* Sub-millisecond latency
		- Max IOPS: 256,000
		- 1000 lOPS per GB
- Hard Disk Drives (HDD)
	-  **Cannot be used as boot volume** for an EC2 instance
	- **Throughput Optimized HDD (st1)**
		- Optimized for large sequential reads and writes (Big Data, Data Warehouses, Log Processing)
		- **Max throughput: 500 MB/s**
		-  **Max IOPS: 500**
	- **Cold HDD (sc1)**
	    - For infrequently accessed data
	    - Cheapest
	    - **Max throughput: 250 MB/s**
	    - **Max IOPS: 250**
- Snapshots
	* ***Data Lifecycle Manager (DLM)** can be used to automate the creation, retention, and deletion of snapshots of EBS volumes

### Elastic File System (EFS)
- Can be mounted to multiple EC2 instances **across AZs**
-  Compatible with **Linux** based AMIs (**POSIX** file system)
-  **Uses security group to control access to EFS**
- Lifecycle management feature to move files to **EFS-IA** after N days
-  **POSIX Permissions** to control access from hosts by IAM User or Group

### Simple Storage Service (S3)
- **Max Object Size: 5TB**
-  **SYNC** command can be used to **copy data between buckets**, possibly in **different regions**
-  Force objects to be encrypted at upload by using Bucket policy
- Server Access Logging
	-  Most detailed way of logging access to S3 buckets (better than [CloudTrail](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/CloudTrail))
	- Does not support **Data Events** & **Log File Validation** (use [CloudTrail](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/CloudTrail) for that)
- Replication
	-  **Asynchronous replication**
	-  Supports **cross-region** and **cross-account** replication
-  Can **move data into IA from Standard only after 30 days**
- Analytics
	-  Provides analytics to determine when to transition data into different storage classes
	- **Does not work for ONEZONE_IA & GLACIER**
-  SSE-KMS may create bottleneck in S3 performance
- **Byte-range fetches**
	- Parallelize download requests by fetching specific byte ranges in each request
	- Better resilience in case of failures since we only need to refetch the failed byte range and not the whole file
- **S3 Transfer Acceleration**
    - Speed up **upload and download** for **large objects (>1GB)** for **global users**
- S3 Select
	- Select a subset of data from S3 using **SQL queries** (**server-side filtering**)
- S3 Notification Events
	- Generates events for operations performed on the bucket or objects
	-  Targets
	    - SNS topics
	    - **SQS Standard** queues (not FIFO queues)
	    - Lambda functions

### Relational Database Service (RDS)
* ***Automated Backups** (enabled by default)
	- Daily full backup of the database (during the defined maintenance window)
	- Backup retention: 7 days (max 35 days)
- Auto Scaling
	-  Free storage is less than 10% of allocated storage
	- Low-storage lasts at least 5 minutes
	- 6 hours have passed since last modification
-  **Up to 5 read replicas** (within AZ, cross AZ or cross region)
-  **Asynchronous Replication** (seconds)
- Multi AZ
	-  **Synchronous Replication**
-  RDS events only provide operational events on the DB instance (not the data)
- To capture data modification events, use **native functions** or **stored procedures** to invoke a **Lambda** function.
- Any database engine level upgrade for an RDS DB instance with Multi-AZ deployment triggers both the primary and standby DB instances to be upgraded at the same time. This causes **downtime** until the upgrade is complete. This is why it should be done during the maintenance window.

### Aurora
- Auto-scaling (max 128TB)
- **Backtrack**: restore data at any point of time without taking backups
- Endpoints
	- **Writer Endpoint** (Cluster Endpoint)
	- **Reader Endpoint**
	- **Custom Endpoint**
		- Used to point to a subset of replicas
- **Automated failover**
	- A read replica is promoted as the new master in less than 30 seconds
	- In case **no replica** is available, Aurora will attempt to **create a new DB Instance** in the **same AZ** as the original instance. This replacement of the original instance is done on a **best-effort basis** and may not succeed.
-  Aurora maintains 6 copies of your data across 3 AZ:
    - 4 copies out of 6 needed for writes (can still write if 1 AZ completely fails)
    - 3 copies out of 6 need for reads
- Each Read Replica is associated with a priority tier (0-15). In the event of a failover, Amazon Aurora will promote the Read Replica that has the highest priority (lowest tier). If two or more Aurora Replicas share the same tier, then Aurora promotes the replica that is largest in size. If two or more Aurora Replicas share the same priority and size, then Aurora promotes an arbitrary replica in the same promotion tier.
- EC2 instances should access the DB using [IAM DB Authentication] but they can also do it using credentials fetched from the [SSM Parameter Store]
-  Aurora Serverless
	- Automated database instantiation and auto scaling based on usage
- Aurora Multi-Master
	- Every node (replica) in the cluster can read and write
	- Used for immediate failover for write node (high availability in terms of write). If disabled and the master node fails, need to promote a Read Replica as the new master (will take some time).
	- **Client needs to have multiple DB connections for failover**
- Aurora Global Database
	- Entire database is replicated across regions to recover from region failure
	- Up to 5 secondary (read-only) regions (replication lag < 1 second)
	- Up to 16 Read Replicas per secondary region
	- **RTO of less than 1 minute** (to promote another region as primary)
- Aurora Events
	- Invoke a **Lambda** function from an **Aurora MySQL-compatible DB cluster** with a **native function** or a **stored procedure** (same as [RDS](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Relational%20Database%20Service%20(RDS)#rds-events))

### DynamoDB
- **Maximum size of an item: 400 KB**
-  Supports TTL (automatically delete an item after an expiry timestamp)
- **Provisioned Mode** (default)
	-  Provision read & write capacity
	-  Auto-scaling option (eg. set RCU and WCU to 80% and the capacities will be scaled automatically based on the workload)
- **On-demand Mode**
	-  Capacity auto-scaling based on the workload
- DynamoDB Accelerator (DAX)
	-  Caches the queries and scans of DynamoDB items
	-  Solves read congestion (`ProvisionedThroughputExceededException`)
	-  5 minutes TTL for cache (default)
* DynamoDB Streams
	*  Ordered stream of notifications of item-level modifications (create/update/delete) in a table
	- Destination can be
	    - Kinesis Data Streams
	    - AWS Lambda
	    - Kinesis Client Library applications
	- Data Retention for up to 24 hours
- Global Tables
	-  **Must enable DynamoDB Streams as a pre-requisite**

### ElastiCache
- **In-memory key-value store** with **sub-millisecond latency**
- Redis -> In-memory data store | Memcached -> **Distributed** memory object cache
	- Read Replicas (for scaling reads & HA)  |   No replication
	- Backup & restore  |  No backup & restore
	- **Single-threaded**  |  **Multi-threaded**
	- **HIPAA compliant** | **Not HIPAA compliant**
	- Multi-AZ support with automatic failover (disaster recovery) | -
-  Network security is managed using **Security Groups** (only allow EC2 security group for incoming requests)

### FSx
- Allows us to launch **3rd party high-performance file systems** on AWS
- **Can be accessed from your on-premise infrastructure**
- FSx for Windows
	-  Supports **SMB** protocol, Windows **NTFS**, Microsoft **Active Directory** integration, ACLs, user quotas
	-  Data is backed-up daily to S3
	-  **Does not integrate with S3** (cannot store cold data)
- FSx for Lustre
	- **Seamless integration with S3**
	    - Can read S3 buckets as a file system (through FSx)
	    - Can write the output back to S3 (through FSx)
- FSx Deployment Options
	- **Scratch File System**
	    - Temporary storage (cheaper)
	    - Data is not replicated (data lost if the file server fails)
	    - High burst (6x faster than persistent file system)
	    - Usage: short-term processing
	- **Persistent File System**
	    - Long-term storage (expensive)
	     Data is replicated within same AZ
	    - Failed files are replaced within minutes
	    - Usage: long-term processing, sensitive data
<<<<<<< HEAD

### Redshift
- Based on **PostgreSQL**
- Used for **Online Analytical Processing (OLAP)** and high performance querying
- **Columnar storage** of data with **massively parallel query execution** in **SQL**
- Need to provision instances as a part of the Redshift cluster (pay for the instances provisioned)
- **No multi-AZ support** (all the nodes will be in the same AZ)
- Auto-healing feature
- Loading data into Redshift
	- Use **COPY command** to load data from an S3 bucket into Redshift
	- **Kinesis Data Firehose**
	    - **Sends data to S3** and issues a **COPY** command to load it into Redshift
	- **EC2 Instance**
		- Using **JDBC driver**
- Snapshots
	- Stored internally in **S3**
	- **Incremental** (only changes are saved)
	- Can be restored into a new Redshift cluster
	- Automated
	    - based on a schedule or storage size (every 5 GB)
	    - set retention
	- Manual
	    - retains until you delete them
	- Feature to **automatically copy snapshots into another region**
- Redshift Spectrum
	- Query data present in S3 without loading it into Redshift
	- Need to have a Redshift cluster to use this feature
	- Query is executed by 1000s of Redshift Spectrum nodes

### Neptune
- AWS managed **graph database**
- Used for **high relationship data** (eg. social networking)
- Highly available across **3 AZ** with up to **15 read replicas**
- **Point-in-time recovery** due to continuous backup to S3
- Support for KMS encryption at rest + HTTPS for in-flight encryption
- **Need to provision nodes in advance** (pay for the provisioned nodes)

### ElasticSearch
- Used in combination with a database to perform **search operations on the database**
- Can search on any field, even supports **partial matches**
- **Need to provision a cluster of instances** (pay for provisioned instances)
- Supports **Multi-AZ**
- Used in Big Data
- Comes with **Kibana** (visualization) & **Logstash** (log ingestion) - **ELK stack**
- Integrated with [Cognito](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Cognito) for access control

### Snow Family
- **Takes around 2 weeks to transfer the data**
- **Snowball cannot import to Glacier directly** (transfer to S3, configure a lifecycle policy to transition the data into Glacier)
* ***Snowcone**
	- 2 CPUs, **4GB RAM**, wired or wireless access
	- 8 TB storage
	- **DataSync Agent** is preinstalled
	- Does not support **Storage Clustering**
- **Snowball Edge**
	- **Compute Optimized**
	    - 52 vCPUs, 208 GB of RAM
	    - 42 TB storage
	    - **Optional GPU** (useful for video processing or machine learning)
	    - Supports **Storage Clustering**
	- **Storage Optimized**
	    - Up to 40 CPUs, 80 GB of RAM
	    - 80 TB storage
	    - Supports **Storage Clustering** (up to 15 nodes)
	    - Transfer up to petabytes
* ***Snowmobile**
    - 100 PB storage
    - Used when transferring **> 10PB**
    - Transfer up to exabytes
    - Does not support **Storage Clustering**
- Devices for edge computing
    - Snowcone
    - Snowball Edge
- Can run **EC2 Instances & AWS Lambda functions locally on Snow device** (using **AWS loT Greengrass**)

### Database Migration Service (DMS)
- The source database remains available during migration
- **Continuous Data Replication** using **CDC (change data capture)**
- Requires **EC2 instance running the DMS software** to perform the replication tasks. If the amount of data is large, use a large instance. If multi-AZ is enabled, need an instance in each AZ.
- Migrating using Snow Family
	1. Use the Schema Conversion Tool (SCT) to extract the data locally and move it to the Edge device
	2. Ship the Edge device or devices back to AWS
	3. After AWS receives your shipment, the Edge device automatically loads its data into an Amazon S3 bucket.
	4. AWS DMS takes the files and migrates the data to the target data store (eg. DynamoDB)

### Storage Gateway
* S3 File Gateway
	- Used to expand on-premise NFS by leveraging S3
	- Configured S3 buckets are accessible on premises using the **NFS** and **SMB** protocol
	- **Data is cached at the file gateway** for low latency access
	- Can be mounted on many servers on-premises
	- Integrated with **Active Directory (AD)** for user authentication
* Volume Gateway
	- Used for on-premise storage volumes
	- Uses **iSCSI protocol**
	- Two kinds of volumes:
	    - **Cached volumes**: storage extension using S3 with caching at the volume gateway
	    - **Stored volumes**: entire dataset is on premise, scheduled backups to S3 as **EBS snapshots**
- Tape Gateway
	- Used to backup on-premises data using tape-based process to S3 as Virtual Tapes
	- Uses **iSCSI protocol**
- FSx File Gateway
	- Used to expand on-premise Windows-based storage by leveraging FSx for Windows

### DataSync
- Move **large amounts of data** from your **on-premises NAS or file system** via **NFS** or **SMB** protocol to AWS over the **public internet using TLS**
- Can synchronize to:
    - S3 (all storage classes)
    - EFS
    - FSx for Windows
- **Scheduled Replication** (not continuous)
* Can also be used to transfer between two EFS in different regions
- Suitable in automating and accelerating online data transfers to a variety of AWS storage services (over [Storage Gateway](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Storage%20Gateway) which only works with S3)
* Perfect to move large amounts of historical data from on-premises to S3 Glacier Deep Archive (directly).

### AppSync
- Store and **sync data across mobile and web apps** in **real-time**
- Makes use of **GraphQL** (mobile technology from Facebook)
- **Offline data synchronization** (replaces Cognito Sync)

### Route 53
- Affected by client's DNS caching (not suitable for [Blue-Green Deployment](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Blue-Green%20Deployment) if the client caches DNS queries)
* Hosted Zone
	- A container for DNS records that define how to route traffic to a domain and its subdomains.
* Record Types
* A - maps a hostname to IPv4
* AAAA - maps a hostname to IPv6
* CNAME - maps a hostname to another hostname
	* The target is a domain name which must have an A or AAAA record
	- **Cannot point to root domains (Zone Apex)** Ex: you can’t create a CNAME record for `example.com`, but you can create for `something.example.com`
- **Alias** - maps a hostname to an AWS resource
	- Can point to root (zone apex) and non-root domains
	- **Alias Record is of type A or AAAA** (IPv4 / IPv6)
	- Targets can be
	    - Elastic Load Balancers
	    - CloudFront Distributions
	    - API Gateway
	    - Elastic Beanstalk environments
	    - S3 Websites
	    - VPC Interface Endpoints
	    - Global Accelerator accelerator
	    - Route 53 record in the same hosted zone
	- **Target cannot be EC2**
- Routing Policies
* Simple
	- Route to one or more resources
	- If multiple values are returned, client chooses one at random (client-side load balancing)
	- No health check (if returning multiple resources, some of them might be unhealthy)
* Weighted
	- Route a fraction of request to multiple resources
	- Use case: testing a new application version by sending a small amount of traffic
	- Can be used for **Active-Active failover** strategy
- Latency-based
	- Redirect to the resource that has the lowest latency
	- Health checks
	- Can be used for **Active-Active failover** strategy
* Failover
	- Primary & Secondary Records (if the primary application is down, route to secondary application)
	- Health check must be associated with the primary record
	- Used for **Active-Passive failover** strategy
* Geolocation
	- Routing based on the client's location
	- Should create a “Default” record (in case there’s no match on location)
	- Use cases: restrict content distribution & language preference
- Geoproximity
	- Route traffic to your resources based on the proximity of clients to the resources
	- Ability to shift more traffic to resources based on the defined bias.
	    - To expand (bias: 1 to 99) → more traffic to the resource
	    - To shrink (bias: -1 to-99) → less traffic to the resource
	- Resources can be:
	    - AWS resources (specify AWS region)
	    - Non-AWS resources (specify Latitude and Longitude)
	- Uses **Route 53 Traffic Flow**
- Multi-value
	- Route traffic to multiple resources (max 8)
	- Health Checks (only healthy resources will be returned)

### API Gateway
- Serverless REST APIs
- Invoke Lambda functions using REST APIs (API gateway will proxy the request to lambda)
- Supports **WebSocket** (stateful)
- Cache API responses
- Transform and validate requests and responses
* We can use an API Gateway REST API to directly access a DynamoDB table by creating a proxy for the DynamoDB query API
* Endpoint Types
	- **Edge-Optimized** (default)
	    - For global clients
	    - Requests are routed through the [CloudFront](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/CloudFront) edge locations (improves latency)
	    - The API Gateway lives in only one region but it is accessible efficiently through edge locations
	- **Regional**
	    - For clients within the same region
	    - Could manually combine with your own CloudFront distribution for global deployment (this way you will have more control over the caching strategies and the distribution)
	- **Private**
	    - Can only be accessed within your VPC using an **Interface VPC endpoint** (ENI)
	    - Use resource policy to define access
- Access Management
	* IAM Policy
		- Create an IAM policy and attach to User or Role to allow it to call an API
		- Good to provide **access within your own AWS account**
		- Leverages **Sig v4** where lAM credential are in the request headers
	- Lambda Authorizer
		- Uses a Lambda function to validate the token being passed in the header and return an lAM policy to determine if the user should be allowed to access the resource.
		- Option to **cache result of authentication**
		- For **OAuth / SAML / 3rd party type of authentication**
		- Good to provide access outside your AWS account if you have an **existing IDP**
	- Cognito User Pools (CUP)
		- **Seamless integration with CUP** (no custom lambda implementation required)
		- **Only supports authentication** (authorization must be implemented in the backend)
		- The client (user) first authenticates with Cognito and gets the access token which it passes in the header to API gateway. API gateway validates the token using Cognito and then hits the backend if the token is valid.

# Virtual Private Cloud (VPC)[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#virtual-private-cloud-vpc "Permanent link")

- **Regional resource**
- Soft limit of 5 VPCs per region
- Only the Private IPv4 ranges are allowed

> New EC2 instances are launched into the default VPC if no subnet is specified

## Classless Inter-Domain Routing (CIDR)[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#classless-inter-domain-routing-cidr "Permanent link")

- Way to define a range of IP addresses
    - ![attachments/Pasted image 20220512094258.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512094258.jpg)
- Two parts
    - Base IP - 192.168.0.0
    - Subnet Mask (defines how many bits are frozen from the left side) - /16
- Private IP ranges:
    - 10.0.0.0 - 10.255.255.255 (10.0.0.0/8) ⇒ used in big networks (24 bits can change)
    - 172.16.0.0 - 172.31.255.255 (172.16.0.0/12) ⇒ AWS default VPC
    - 192.168.0.0 - 192.168.255.255 (192.168.0.0/16) ⇒ home networks
- Rest of the IP ranges are Public
- **Max 5 CIDR ver VPC**
    - Min. size is /28 (16 IP addresses)
    - Max. size is /16 (65536 IP addresses)

## Subnets[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#subnets "Permanent link")

- Sub-ranges of IP addresses within the VPC
- **Each subnet is bound to an AZ**
- Subnets in a VPC cannot have overlapping CIDRs
- **Default VPC only has public subnets** (1 public subnet per AZ, no private subnet)
- **AWS reserves 5 IP addresses (first 4 & last 1) in each subnet**. These 5 IP addresses are not available for use. Example: if CIDR block 10.0.0.0/24, then reserved IP addresses are 10.0.0.0, 10.0.0.1, 10.0.0.2, 10.0.0.3 & 10.0.0.255

> To make the EC2 instances running in private subnets accessible on the internet, place them behind an internet-facing (running in public subnets) Elastic Load Balancer.
> 
> **There is no concept of Public and Private subnets.** Public subnets are subnets that have: - “Auto-assign public IPv4 address” set to “Yes” - The subnet route table has an attached Internet Gateway
> 
> This allows the resources within the subnet to make requests that go to the public internet. **A subnet is private by default.**
> 
> Since the resources in a private subnet don't have public IPs, they need a NAT gateway for address translation to be able to make requests that go to the public internet. NAT gateway also prevents these private resources from being accessed from the internet.

## Internet Gateway (IGW)[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#internet-gateway-igw "Permanent link")

- Allows resources in a VPC to connect to the Internet
    - ![attachments/Pasted image 20220512100002.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512100002.jpg)
- **Attached to the VPC**
- Should be used to **connect public resources to the internet** (use NAT gateway for private resources since they need network address translation)
- Route table of the public subnets must be edited to allow requests destined outside the VPC to be routed to the IGW
    - ![attachments/Pasted image 20220512222218.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512222218.jpg)

> IGW performs network address translation (NAT) for a public EC2 instance

## Bastion Hosts[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#bastion-hosts "Permanent link")

- A EC2 instance running in the public subnet (accessible from public internet), to allow users to SSH into the instances in the private subnet.
    - ![attachments/Pasted image 20220512100455.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512100455.jpg)
- Security groups of the private instances should only allow traffic from the bastion host.
- Bastion host should only allow port 22 traffic from the IP address you need (**small instances are enough**)

#### High Availability[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#high-availability "Permanent link")

- HA options for Bastion Host
    - Run 2 Bastion Hosts across 2 AZ
    - Run 1 Bastion Host across 2 AZ with ASG 1:1:1
- Routing to the bastion host
    - If 1 bastion host, use an elastic IP with EC2 user-data script to access it
    - If 2 bastion hosts, use a public-facing NLB (layer 4) deployed in multiple AZ. Bastion hosts can live in the private subnet (more secure)
- Can’t use ALB as it works in layer 7 (HTTP protocol) and SSH works with TCP
- Diagram
    - ![attachments/Pasted image 20220513222559.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220513222559.jpg)

## Network Address Translation (NAT) Instance[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#network-address-translation-nat-instance "Permanent link")

- An [EC2](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Elastic%20Compute%20Cloud%20(EC2)) instance **launched in the public subnet** which performs network address translation to enable private instances to use the public IP of the NAT instance to access the internet. This is exactly the same as how routers perform NAT. This also prevents the private instances from being accessed from the public internet.
    - ![attachments/Pasted image 20220512101152.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512101152.jpg)
- **Must disable EC2 setting: source / destination IP check on the NAT instance** as the IPs can change.
- **Must have an Elastic IP attached to it**
- Route Tables for private subnets must be configured to route internet-destined traffic to the NAT instance (its elastic IP)
- **Can be used as a Bastion Host**
- Disadvantages
    - Not highly available or resilient out of the box. Need to create an ASG in multi-AZ + resilient user-data script
    - Internet traffic bandwidth depends on EC2 instance type
    - You must manage [Security Groups](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Elastic%20Compute%20Cloud%20(EC2)#security-groups) & rules:
        - Inbound:
            - Allow HTTP / HTTPS traffic coming from Private Subnets
            - Allow SSH from your home network (access is provided through Internet Gateway)
        - Outbound:
            - Allow HTTP / HTTPS traffic to the Internet
- Architecture
    - ![attachments/Pasted image 20220512101803.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512101803.jpg)

## NAT Gateway[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#nat-gateway "Permanent link")

- AWS managed NAT with **bandwidth autoscaling** (up to 45Gbps)
- Preferred over NAT instances
- **Uses an Elastic IP** and Internet Gateway behind the scenes
- **Created in a public subnet**
- **Bound to an AZ**
- **Cannot be used by EC2 instances in the same subnet** (only from other subnets)
- **Cannot be used as a Bastion Host**
- Route Tables for private subnets must be configured to route internet-destined traffic to the NAT gateway
    - ![attachments/Pasted image 20220512222148.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512222148.jpg)
- No Security Groups to manage
- Pay per hour
- Architecture
    - ![attachments/Pasted image 20220512204306.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512204306.jpg)
- High Availability
    - Create NAT gateways in public subnets bound to different AZ all routing outbound connections to the IGW (attached to the VPC)
    - No cross-AZ failover needed because if an AZ goes down, all of the instances in that AZ also go down.
    - ![attachments/Pasted image 20220512204520.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512204520.jpg)

## DNS Resolution in VPC[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#dns-resolution-in-vpc "Permanent link")

- Two settings need to be enabled to allow DNS resolution within a VPC:
    - **DNS Support** (enableDnsSupport)
        - Allows the resources within the VPC to query the DNS provided by Route 53 Resolver
        - **Enabled by default**
        - If disabled, we need to provide a custom DNS server otherwise we won’t be able to reach hostnames
        - Diagram
            - ![attachments/Pasted image 20220512210247.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512210247.jpg)
    - **DNS Hostnames** (enableDnsHostnames)
        - Assigns **public hostname** to EC2 instances in our VPC if they have a public IPv4
        - **Doesn't work until `enableDnsSupport=true`**
        - By default
            - Default VPC - Enabled
            - Custom VPC - Disabled
        - When disabled, instances in the VPC will have a public IP but no public DNS
- If you use custom domain names in a Private Hosted Zone in Route 53, you must enable both of these settings
    - ![attachments/Pasted image 20220512210613.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512210613.jpg)

## Network Access Control List (NACL)[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#network-access-control-list-nacl "Permanent link")

- NACL is a firewall at the subnet level
- One NACL per subnet but a NACL can be attached to multiple subnets
- **New subnets are assigned the Default NACL**
- **Default NACL allows all inbound & outbound requests**
    - ![attachments/Pasted image 20220512215745.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512215745.jpg)
- NACL Rules
    - Based only on IP addresses
    - Rules number: 1-32766 (lower number has higher precedence)
    - First rule match will drive the decision
    - The last rule denies the request (only when no previous rule matches)
- NACL vs [Security Group](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Elastic%20Compute%20Cloud%20(EC2)#security-groups)
    - NACL
        - Firewall for subnets
        - Supports both Allow and Deny rules
        - Stateless (both request and response will be evaluated against the NACL rules)
        - Only the first matched rule is considered
    - Security Group:
        - Firewall for EC2 instances
        - Supports only Allow rules
        - Stateful (only request will be evaluated against the SG rules)
        - All rules are evaluated
    - ![attachments/Pasted image 20220512220220.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512220220.jpg)
- NACL with Ephemeral Ports
    - In the example below, the client EC2 instance needs to connect to DB instance. Since the ephemeral port can be randomly assigned from a range of ports, the Web Subnets’s NACL must allow inbound traffic from that range of ports and similarly DB Subnet’s NACL must allow outbound traffic on the same range of ports.
    - ![attachments/Pasted image 20220512220647.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512220647.jpg)

## VPC Peering[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#vpc-peering "Permanent link")

- Connect two VPCs (could be in **different region or account**) using the AWS private network
    - ![attachments/Pasted image 20220512221728.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512221728.jpg)
- Participating VPCs must have **non-overlapping CIDR**
- VPC Peering connection is **non-transitive** (A - B, B - C != A - C) because it works based on route-table rules.
- Must update route tables in each VPC’s subnets to ensure requests destined to the peered VPC can be routed through the peering connection
    - ![attachments/Pasted image 20220512221946.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512221946.jpg)
    - ![attachments/Pasted image 20220512221954.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512221954.jpg)
- You can reference a security group in a peered VPC across account or region. This allows us to use SG instead of CIDR when configuring rules.

> VPC Peering does not facilitate centrally-managed VPC like [VPC Sharing](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Resource%20Access%20Manager%20(RAM)#vpc-sharing)

## VPC Endpoints[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#vpc-endpoints "Permanent link")

- **Private endpoints** within your VPC that allow AWS services to privately connect to resources within your VPC without traversing the public internet (cheaper)
    - ![attachments/Pasted image 20220512222517.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512222517.jpg)
- Powered by **AWS PrivateLink**
- **Route table is updated automatically**
- **Bound to a region** (do not support inter-region communication)
- Two types:
    - **Interface Endpoint**
        - Provisions an **ENI** (private IP) as an entry point per subnet
        - Need to **attach a security group to the interface endpoint** to control access
        - Supports most AWS services
    - **Gateway Endpoint**
        - Provisions a gateway
        - Must be used as a target in a route table
        - Supports only **S3** and **DynamoDB**

## VPC Flow Logs[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#vpc-flow-logs "Permanent link")

- Captures information about **IP traffic** going into your **interfaces**
- Three levels:
    - **VPC** Flow Logs
    - **Subnet** Flow Logs
    - **ENI** Flow Logs
- Can be configured to show accepted, rejected or all traffic
- Flow logs data can be sent to **S3** (bulk analytics) or **CloudWatch Logs** (near real-time decision making)
- Query VPC flow logs using **Athena** in S3 or **CloudWatch Logs Insights**

## IPv6 Support[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#ipv6-support "Permanent link")

- IPv4 cannot be disabled for your VPC
- Enable IPv6 to operate in **dual-stack mode** in which your EC2 instances will get at least a **private IPv4** and a **public IPv6**. They can communicate using either IPv4 or IPv6 to the internet through an Internet Gateway.
    - ![attachments/Pasted image 20220513005218.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220513005218.jpg)
- If you cannot launch an EC2 instance in your subnet, It’s not because it cannot acquire an IPv6 (the space is very large). It’s because there are no available IPv4 in your subnet. **Solution: Create a larger IPv4 CIDR for the subnet**

#### Egress-only Internet Gateway[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#egress-only-internet-gateway "Permanent link")

- Allows instances in your VPC to initiate outbound connections over IPv6 while preventing inbound IPv6 connections to your private instances.
    - ![attachments/Pasted image 20220513005755.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220513005755.jpg)
- Similar to [NAT Gateway](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Virtual%20Private%20Cloud%20(VPC)#nat-gateway) but for IPv6
- Must update Route Tables

## VPC Console Wizard[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/virtual%20private%20cloud%20(vpc)/#vpc-console-wizard "Permanent link")

> - Supported Configurations:
> - VPC with a single public subnet
> - VPC with public and private subnets (NAT)
> - VPC with public and private subnets and AWS Site-to-Site VPN access
> - VPC with a private subnet only and AWS Site-to-Site VPN access

### PrivateLink
- Used to expose **services** in one VPC to multiple other VPCs, possibly in **different accounts**
- Should not use VPC peering as we only want to expose a few services
- Requires a **NLB (common) or GWLB in the service VPC** and **ENI in the consumer VPC**
- Use multi-AZ NLB and ENIs in multiple AZ for fault-tolerance

![attachments/Pasted image 20220512235655.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512235655.jpg)

## Exposing ECS tasks[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/privatelink/#exposing-ecs-tasks "Permanent link")

- ECS tasks require an ALB. So, we can connect the ALB to the NLB for PrivateLink.
- Corporate Data Centers will still connect through the VPN or Direct Connect.

![attachments/Pasted image 20220513000308.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220513000308.jpg)


### Site-to-Site VPN
- Easiest and most cost-effective way to connect a VPC to an on-premise data center
- **IPSec Encrypted** connection through the public internet
- **Virtual Private Gateway (VGW)**: VPN concentrator on the VPC side of the VPN connection
- **Customer Gateway (CGW)**: Software application or physical device on customer side of the VPN connection
- If you need to ping EC2 instances from on-premises, make sure you add the **ICMP protocol** on the inbound rules of your security groups
* VPN CloudHub
	- **Low-cost hub-and-spoke model** for **network connectivity between a VPC and multiple on-premise data centers**
	- Every participating network can communicate with one another through the VPN connection

# Direct Connect (DX)[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/direct%20connect%20(dx)/#direct-connect-dx "Permanent link")

- Dedicated private connection from an on-premise data center to a VPC
    - ![attachments/Pasted image 20220512233459.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512233459.jpg)
- **Data in transit is not-encrypted** but the connection is private (secure)
- More stable and secure than Site-to-Site VPN
- Access public & private resources on the same connection using **Public & Private Virtual Interface (VIF)** respectively
- Connection to a data center is made from a **Direct Connect Location**
- Connects to a Virtual Private Gateway (VGW) on the VPC end
- Supports both IPv4 and IPv6
- Supports **Hybrid Environments** (on premises + cloud)
- **Lead time > 1 month**

## Connection Types[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/direct%20connect%20(dx)/#connection-types "Permanent link")

- **Dedicated Connection**
    - **1 Gbps and 10 Gbps** (fixed capacity)
    - Physical ethernet port dedicated to a customer
- **Hosted Connection**
    - **50Mbps, 500 Mbps, up to 10 Gbps**
    - **On-demand capacity scaling** (more flexible than dedicated connection)

## Encryption[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/direct%20connect%20(dx)/#encryption "Permanent link")

- For encryption in flight, use AWS Direct Connect + VPN which provides an **IPsec-encrypted private connection**
    - ![attachments/Pasted image 20220512234026.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512234026.jpg)
- Good for an extra level of security

## Resiliency[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/direct%20connect%20(dx)/#resiliency "Permanent link")

- **Best way** (redundant direct connect connections)
    - ![attachments/Pasted image 20220512234246.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512234246.jpg)
- **Cost-effective way** (VPN connection as a backup)
    - Implement an **IPSec VPN** connection and use the **same BGP prefix**. Both the Direct Connect connection and IPSec VPN are active and being advertised using the Border Gateway Protocol (BGP). The **Direct Connect link will always be preferred** unless it is unavailable.

## Direct Connect Gateway[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/direct%20connect%20(dx)/#direct-connect-gateway "Permanent link")

- Used to setup a Direct Connect to multiple VPCs, possibly in **different regions** but **same account**
- Using DX, we will create a Private VIF to the Direct Connect Gateway which will extend the VIF to Virtual Private Gateways in multiple VPCs (possibly across regions).
    - ![attachments/Pasted image 20220512234818.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220512234818.jpg)

# Transit Gateway[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/transit%20gateway/#transit-gateway "Permanent link")

- **Transitive peering** between thousands of VPCs and on-premise data centers using **hub-and-spoke (star) topology**
- Works with **Direct Connect Gateway**, **VPN Connection** and **VPC**
- **Bound to a region**
- Transitive peering between VPCs in **same region & account**
- **Route Tables** to control communication within the transitive network
- Supports **IP Multicast** (not supported by any other AWS service)
- Diagram
    - ![attachments/Pasted image 20220513002130.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220513002130.jpg)

## Increasing BW of Site-to-Site VPN connection[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/transit%20gateway/#increasing-bw-of-site-to-site-vpn-connection "Permanent link")

- **ECMP** (equal-cost multi-path) routing is a routing strategy to allow to forward a packet over multiple best path
- To increase the bandwidth of the connection between Transit Gateway and corporate data center, create multiple site-to-site VPN connections, each with 2 tunnels (2 x 1.25 = 2.5 Gbps per VPN connection).
    - ![attachments/Pasted image 20220513002301.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220513002301.jpg)
- Only one VPN connection to a VPC having 2 tunnels out of which only 1 is used (1.25 Gbps)

## Share DX between multiple Accounts[¶](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/transit%20gateway/#share-dx-between-multiple-accounts "Permanent link")

Share Transit Gateway across accounts using [Resource Access Manager (RAM)](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Resource%20Access%20Manager%20(RAM)) connection between VPCs in the **same region but different accounts**

![attachments/Pasted image 20220513003853.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220513003853.jpg)


### Simple Queue Service (SQS)
- Unlimited number of messages in queue
- Max 10 messages received per batch (configured using `MaxNumberOfMessages` parameter in the ReceiveMessage API)
- **Max message size: 256KB**
- **Default message retention: 4 days (max: 14 days)**
- **Consumers could be EC2 instances or Lambda functions**
* Standard Queue
	- Unlimited throughput (publish any number of message per second into the queue)
	- Low latency (<10 ms on publish and receive)
	- Can have duplicate messages (at least once delivery)
	- Can have out of order messages (best effort ordering)
- FIFO Queue
- Limited throughput
    - 300 msg/s without batching (batch size = 1)
    - 3000 msg/s with batching (batch size = 10)
- **Message De-duplication**
    - De-duplication interval: 5 min (duplicate messages will be discarded only if they are sent less than 5 mins apart)
    - De-duplication methods:
        - **Content-based de-duplication**: computes the hash of the message body and compares
        - **Using a message de-duplication ID**: messages with the same de-duplication ID are considered duplicates
- **Message Grouping**
    - Group messages based on `MessageGroupID` to send them to different consumers
    - Same value for `MessageGroupID`
        - All the messages are in order
        - Single consumer
    - Different values for `MessageGroupID`
        - Messages will be ordered for each group ID
        - Ordering across groups is not guaranteed
        - Each group ID can have a different consumer (parallel processing)
        - Max number of consumers = number of unique group IDs
* Access Management
- **IAM Policies** to regulate access to the SQS API
- **SQS Access Policies** (resource based policy)
    - Used for cross-account access to SQS queues
        - The example policy allows any resource in the account 111122223333 to poll the SQS queue for messages. ![attachments/Pasted image 20230220113656.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020230220113656.jpg)
    - Used for allowing other AWS services to send messages to an SQS queue
        - The example policy allows any AWS account to send messages to the queue as long as it is coming from the desired S3 bucket and the source account is the one owning the bucket. ![attachments/Pasted image 20230220113819.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020230220113819.jpg)
* Dead Letter Queue (DLQ)
	- An SQS queue used to store failing to be processed messages in another queue
	- After the `MaximumReceives` threshold is exceeded, the message goes into the DLQ
	- **Redrive to Source** - once the bug in the consumer has been resolved, messages in the DLQ can be sent back to the queue (original queue or a custom queue) for processing
	- Prevents resource wastage
	- Recommended to set a high retention period for DLQ (14 days)
- Queue Delay
	- Consumers see the message after some delay
	- Default: 0 (Max: 15 min)
	- Can be set at the queue level
	- Can override the default queue delay for a specific message using the `DelaySeconds` parameter in the `SendMessage` API
- Long Polling
	- Poll the queue for longer
	- Decreases the number of API calls made to SQS (**cheaper**)
	- Reduces latency (incoming messages during the polling will be read instantaneously)
	- **Polling time: 1 sec to 20 sec**
	- Long Polling is preferred over Short Polling
	- Can be enabled at the queue level or at the consumer level by using `WaitTimeSeconds` parameter in `ReceiveMessage` API.
- Handling Priority
> Use two SQS queues, one for low priority (ex. free) and the other for high priority (ex. paid). Configure your consuming application to only poll the low priority queue if the high priority queue is empty.


### Simple Notification Service (SNS)
- Used to broadcast messages
- Pub-Sub model (publisher publishes messages to a topic, subscribers listen to the topic)
- Instant message delivery (does not queue messages)
* Access Management
	- **lAM policies** to regulate access to the SNS API
	- **SNS Access Policies** (resource based policy)
	    - Used for cross-account access to SNS topics
	    - Used for allowing other AWS services to publish to an SNS topic
- Standard Topics
	- Highest throughput
	- At least once message delivery
	- Best effort ordering
	- Subscribers can be:
	    - SQS queues
	    - HTTP / HTTPS endpoints
	    - Lambda functions
	    - Emails (using SNS)
	    - SMS & Mobile Notifications
	    - Kinesis Data Firehose (KDF) to send the data into S3 or Redshift
* FIFO Topics
	- Guaranteed ordering of messages in that topic
	- Publishing messages to a FIFO topic requires:
	    - **Group ID**: messages will be ordered and grouped for each group ID
	    - **Message deduplication ID**: for deduplication of messages
	- **Can only have SQS FIFO queues as subscribers**
	- **Limited throughput (same as SQS FIFO)** because only SQS FIFO queues can read from FIFO topics
	- **The topic name must end with** `.fifo`
* SNS + SQS Fanout Pattern
	- Fully decoupled, no data loss
	- SQS allows for: data persistence, delayed processing and retries of work
	- Make sure your SQS queue access policy allows for SNS to write
	- Diagram
    - ![attachments/Pasted image 20220509205738.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220509205738.jpg)

* Message Filtering
	- JSON policy used to filter messages sent to SNS topic’s subscriptions
	- Each subscriber will have its own filter policy (if a subscriber doesn’t have a filter policy, it receives every message)
	- Ex: filter messages sent to each queue by the order status
* SNS + Lambda + DLQ
	- Lambda retries each failed message 3 times after which it is **sent to the DLQ by lambda**
    - ![attachments/Pasted image 20220513220228.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220513220228.jpg)


### Kinesis
* Kinesis Data Stream (KDS)
	- Real-time data streaming service
	- **Used to ingest data in real time directly from source**
	- **Capacity Modes**
	    - Provisioned
	        - Publishing: 1MB/sec per shard or 1000 msg/sec per shard
	        - Consuming:
	            - 2MB/sec per shard (throughput shared between all consumers)
	            - **Enhanced Fanout**: 2MB/sec per shard per consumer (dedicated throughput for each consumer)
	        - Throughput scales with shards (**manual scaling**)
	        - Pay per shard provisioned per hour
	    - On-demand
	        - No need to provision or manage the capacity
	        - Default capacity provisioned - 4 MB/sec or 4000 records/sec
	        - Scales automatically based on observed throughput peak during the last 30 days
	        - Pay per stream per hour & data in/out per GB
	- **Not Serverless**
	- **Data Retention: 1 day (default) to 365 days**
	- A record consists of a **partition key** (used to partition data coming from multiple publishers) and data blob (**max 1MB**)
	- Records will be ordered in each shard
	- Producers use SDK, Kinesis Producer Library (KPL) or **Kinesis Agent** to publish records
	- Consumers use SDK or Kinesis Client Library (KCL) to consume the records
	- **Once data is inserted in Kinesis, it can’t be modified or deleted (immutability)**
	- Ability to reprocess (replay) data
	- Diagram
    - ![attachments/Pasted image 20220509221100.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220509221100.jpg)

* Kinesis Data Firehose (KDF)
	- Used to load streaming data into a target location
	- **Writes data in batches efficiently (near real time)**
	    - Buffer size (size of the batch) - 1 MB to 128MB (default 5MB)
	    - Buffer interval (how long to wait for buffer to fill up) - 60s to 900s (default 300s)
	    - Greater the buffer size, higher the write efficiency, longer it will take to fill the buffer
	- **Can ingest data in real time directly from source**
	- **Auto-scaling**
	- **Serverless**
	- Destinations:
	    - AWS: Redshift, S3, **OpenSearch**
	    - 3rd party: Splunk, MongoDB, DataDog, NewRelic, etc.
	    - Custom HTTP endpoint
	- Pay for data going through Firehose (no provisioning)
	- **Supports custom data transformation using Lambda functions**
	- No replay capability (does not store data like KDS) ![attachments/Pasted image 20230221145737.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020230221145737.jpg)

* Kinesis Data Analytics (KDA)
	- Perform **real-time analytics on Kinesis streams using SQL**
	- Creates streams from SQL query response
	- **Cannot ingest data directly from source** (ingests data from KDS or KDF)
	- **Auto-scaling**
	- **Serverless**
	- Pay for the data processed (no provisioning)
	- Use cases:
	    - Time-series analytics
	    - Real-time dashboards
	    - Real-time metrics
- ![attachments/Pasted image 20230221151731.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020230221151731.jpg)

* Kinesis Video Streams
	- Capture, process and store video streams


### Amazon MQ
- If you have some traditional applications running from on-premise, they may use open protocols such as **MQTT, AMQP, STOMP, Openwire, WSS**, etc. When migrating to the cloud, instead of re-engineering the application to use SQS and SNS (AWS proprietary), we can use Amazon MQ (managed Apache ActiveMQ) for communication.
- Doesn’t “scale” as much as SQS or SNS because it is provisioned
- Runs on a dedicated machine (can run in HA with failover)
- **Has both queue feature (SQS) and topic features (SNS)**

* High Availability
	- High Availability in Amazon MQ works by leveraging MQ broker in multi AZ (active and standby).
	- EFS (NFS that can be mounted to multi AZ) is used to keep the files safe in case the main AZ is down. ![attachments/Pasted image 20220509223723.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220509223723.jpg)

### EventBridge
- Extension of CloudWatch ⮕ Events
- Event buses types:
    - **Default event bus**: events from AWS services are sent to this
    - **Partner event bus**: receive events from external SaaS applications
    - **Custom Event bus**: for your own applications
- Event Rules: how to process the events
- **Event buses support cross-account access**
- **Cron Jobs**: when creating an EB rule, we can select “Schedule” instead of event pattern to trigger an event based on a cron expression.
- Can archive events (all or based on a filter) sent to an event bus to replay later

> EventBridge is recommended for decoupling applications that reacts to events from third-party **SaaS** applications.

* Schema Registry
	- Defines how the data is structured in the event bus
	- Schema can be versioned

* Event Bus Policy
	- Manage permissions for an event bus
	- Useful to allow or deny events from another AWS account or region ![attachments/Pasted image 20230219100242.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020230219100242.jpg)


### Identity & Access Management (IAM)
* Trust Policies
	- Defines which principal entities (accounts, users, roles, federated users) can assume the role
	- An IAM role is both an identity and a resource that supports resource-based policies.
	- You must attach both a trust policy and an identity-based policy to an IAM role.
	- The **IAM service supports only one type of resource-based policy** called a **role trust policy**, which is **attached to an IAM role**.
- Protect IAM Accounts
	- **Password Policy**
	    - Used to enforce standards for password
	        - password rotation
	        - password reuse
	    - Prevents **brute force** attack
	- **Multi Factor Authentication (MFA)**
	    - Both root user and IAM users should use MFA
- Reporting Tools
	- **Credentials Report**
	    - lists all the users and the status of their credentials (MFA, password rotation, etc.)
	    - **account level** - used to audit security for all the users
	- **Access Advisor**
	    - shows the service permissions granted to a user and when those services were last accessed
	    - **user-level**
	    - used to revise policies for a specific user
* Permission Boundaries
	- Set the maximum permissions an IAM entity can get
	- **Can be applied to users and roles (not groups)**
	- Used to ensure some users can’t escalate their privileges (make themselves admin)


### Cognito
Amazon Cognito lets you add user sign-up, sign-in, and access control to your web and mobile apps quickly and easily. Amazon Cognito scales to millions of users and supports sign-in with social identity providers, such as Apple, Facebook, Google, and Amazon, and enterprise identity providers via SAML 2.0 and OpenID Connect.

* Cognito User Pools (CUP)
	- **Serverless** identity provider (provides sign in functionality for app users)
	- Sends back a JSON Web Token (used to verify the identity of the user)
	- **MFA support**
	- **Supports Federated Identities** allowing users to authenticate via third party identity provider like Facebook, Google, SAML, etc.
	- Seamless integration with **API Gateway** & **ALB** for authentication

* Cognito Identity Pools (CIP)
	- Provides temporary credentials (using STS) to users so they can access AWS resources
	- Integrates with CUP as an identity provider
	- Example use case: provide **temporary access to write to an S3 bucket** after authenticating the user via FaceBook (using CUP identity federation)
	    - Can't use S3 pre-signed URL as we need to provide access to a bucket location and not an single object


### AWS Organizations
- **Consolidated Billing** across all accounts (lower cost)
- Pricing benefits from aggregated usage of AWS resources
- API to automate AWS account creation (on demand account creation)
- Establish Cross Account Roles for Admin purposes where the master account can assume an admin role in any of the children accounts
* Service Control Policies (SCP)
	- **Whitelist or blacklist IAM actions at the OU or Account level**
	- **Does not apply to the Master Account**
	- Applies to all the Users and Roles of the member accounts, including the root user. So, if something is restricted for that account, even the root user of that account won’t be able to do it.
	- Must have an explicit allow (**does not allow anything by default**)
	- **Does not apply to service-linked roles**
	- **Explicit Deny** has the highest precedence

### CloudFront
- **Edge Locations are present outside the VPC** so the origin's SG must be configured to allow inbound requests from the list of public IPs of all the edge locations.
- Supports HTTP/RTMP protocol (**does not support UDP protocol**)
- **Caches content at edge locations**, reducing load at the origin
- **Geo Restriction feature**
- Improves performance for both cacheable content (such as images and videos) and dynamic content (such as API acceleration and dynamic site delivery)
- To block a specific IP at the CloudFront level, deploy a [WAF](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Web%20Application%20Firewall%20(WAF)) on CloudFront
- Supports **Server Name Indication (SNI)** to allow SSL traffic to multiple domains
* To restrict access to ELB directly when it is being used as the origin in a CloudFront distribution, create a VPC Security Group for the ELB and use AWS Lambda to automatically update the CloudFront internal service IP addresses when they change.
* Signed URL / Cookies
	- Used to **make a CloudFront distribution private** (distribute to a subset of users)
	- Signed URL ⇒ access to individual files
	- Signed Cookies ⇒ access to multiple files
	- Whenever we create a signed URL / cookie, we attach a policy specifying:
	    - URL / Cookie Expiration (TTL)
	    - **IP ranges** allowed to access the data
	    - Trusted signers (which AWS accounts can create signed URLs)
* Multiple Origin
	- Route to different origins based on the path in the request
	    - ![attachments/Pasted image 20220508161137.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220508161137.jpg)

* Origin Groups
	- Consists of a **primary** and a **secondary** origin (can be in **different regions**)
	- Automatic failover to secondary
	    - ![attachments/Pasted image 20220508161659.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220508161659.jpg)
	- Provides **region-level** High Availability
	- Use when getting 504 (gateway timeout) Error

* Field-level Encryption
	- Sensitive information sent by the user is encrypted at the edge close to user which can only be decrypted by the web server (intermediate services can't see the encrypted fields)
	- **Asymmetric Encryption** (public & private key)
	- Max 10 encrypted field


### Global Accelerator
- **Improves availability of the application for global users**
- **Does not cache anything at the edge location**
- Endpoint could be public or private (could span multiple regions)
    - Elastic IP
    - EC2 instances
    - ALB
    - NLB
- **Endpoint Weights** and **Traffic Dials** are used in [Blue-Green Deployment](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Blue-Green%20Deployment)
- **Not affected by client's DNS caching** because the 2 anycast IPs are static (traffic dials and endpoint weights changes are effective within seconds)
- Good for
    - non-HTTP use cases:
        - Gaming (UDP)
        - IoT (MQTT)
        - Voice over IP (VoIP)
    - HTTP use cases that require static IP addresses or fast regional failover
- Working
	- **2 anycast public IPs (static)** are created for your application globally. Requests from clients hitting these IPs will automatically be routed to the nearest edge location. The Edge locations send the traffic to your application through the private AWS network.
	- **Traffic dials** to control the percentage of traffic that is directed to an endpoint group (an AWS region where your application is deployed)
	- **Endpoint weights** to determine the proportion of traffic that is directed to endpoints in an endpoint group
* Disaster Recovery
	- Global Accelerator performs **health checks** for the application
	- **Failover in less than 1 minute** for unhealthy endpoints
* Security
	- Only 2 static IP need to be whitelisted by the clients
	- Can be integrated with **AWS Shield for DDoS protection**

### CloudWatch
* Metrics
	- Variables to monitor in CloudWatch
	- Dimension is an attribute of a metric (instance id, environment, etc.)
	- Up to 30 dimensions per metric
- Custom Metrics
	- Define and send your own custom metrics to CloudWatch using **PutMetricData API**
	- Metric resolution (StorageResolution API) - frequency of sending metric data
	- **Standard**: **60 seconds**
	- **High Resolution**: 1/5/10/30 seconds (higher cost)
* EC2 Monitoring
	- Must run a **CloudWatch agent** on instance to push system **metrics and logs** to CloudWatch. Instance role (IAM) must allow the instance to push logs to CloudWatch.
	- EC2 instances have metrics **every 5 minutes**
	- With **detailed monitoring** (for a cost), you get metrics **every 1 minute**
	- Use detailed monitoring if you want to react faster to changes (eg. scale faster for your ASG)
	- **Available metrics in CloudWatch**:
	    - CPU Utilization
	    - Network Utilization
	    - Disk Performance
	    - Disk Reads/Writes
	- **Custom metrics**:
	    - Memory utilization (memory usage)
	    - Disk swap utilization
	    - Disk space utilization
	    - Page file utilization
	- CloudWatch agent can be used for logging on premises servers too
	- **CW Unified Agent** can send logs & additional system-level metrics
	    - CPU (active, guest, idle, system, user, steal)
	    - Disk metrics (free, used, total), Disk IO (writes, reads, bytes, iops)
	    - RAM (free, inactive, used, total, cached)
	    - Netstat (number of TCP and UDP connections, net packets, bytes)
	    - Processes (total, dead, bloqued, idle, running, sleep)
	    - Swap Space (free, used, used %)
- Dashboards
	- Setup custom dashboards for quick access to key metrics and alarms
	- **Dashboards are global** (allows to **monitor services across accounts & regions**)
	- Dashboards can be shared with people who don’t have an AWS account (public, email address, 3rd party SSO provider through Cognito)
- Logs
	- Used to store application logs
	- Log Groups represent an application sending logs to CW
	- Log Streams represent instances within applications or log files or containers
	- Logs Expiration: never expire (default), 30 days, etc.
	- Logs can be sent to:
	    - S3 buckets (exports)
	    - Kinesis Data Streams
	    - Kinesis Data Firehose
	    - Lambda functions
	    - ElasticSearch
	- **Metric Filters** can be used to filter expressions and use the count to trigger CloudWatch alarms. They apply only on the incoming metrics after the metric filter was created. Example filters:
	    - find a specific IP in the logs
	    - count occurrences of “ERROR” in the logs
	- **Cloud Watch Logs Insights** can be used to query logs and add queries to CloudWatch Dashboards
	- To stream logs in real-time, apply a **Subscription Filter** on logs
	- Logs can take up to **12 hours to become available for exporting to S3** (not real-time). To store logs in real time in S3, use a subscription filter to publish logs to KDF in real time which will then write the logs to S3.
	- Logs from multiple accounts and regions can be aggregated using subscription filters
	    - ![attachments/Pasted image 20220510222924.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020220510222924.jpg)
	
> 	Metric Filters are a part of CloudWatch Logs (not CloudWatch Metrics)

* Alarms
- Alarms are used to trigger notifications for CW metrics based on **Metric Filters**
- Various options to trigger alarm (sampling, %, max, min, etc.)
- An alarm monitors a single CW metric
- Alarm States:
    - OK
    - INSUFFICIENT_DATA
    - ALARM
- Period:
    - Length of time in seconds to evaluate the metric before triggering the alarm
    - High resolution custom metrics: **10 sec**, 30 sec or multiples of 60 sec
- **Targets**:
    - Stop, Terminate, Reboot, or Recover an EC2 Instance
    - Trigger Auto Scaling Action (ASG)
    - Send notification to SNS
- **Composite Alarms** monitor multiple other alarms with AND/OR conditions to generate a new alarm. This is helpful to reduce alarm noise by creating complex composite alarms. Example: send an SNS notification when both CPU and IOPS are above 90% utilization. ![attachments/Pasted image 20230219094745.jpg](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/attachments/Pasted%20image%2020230219094745.jpg)

* EC2 Instance Recovery
	- CloudWatch **alarm** to automatically recover an EC2 instance if it becomes **impaired**
	- **Terminated instances cannot be recovered**
	- After the recovery, the following are retained
	    - Placement Group
	    - Public IP
	    - Private IP
	    - Elastic IP
	    - Instance ID
	    - Instance metadata
	- After the recovery, **RAM contents are lost**
* Events
	- Schedule or Cron to create events on a schedule
	- **Uses default event bus (custom & partner event buses are not supported)**

### CloudTrail
- **Global Service** (a single trail can be applied to multiple regions)
- Provides governance, compliance and audit for the AWS Account
- Enabled by default
- Records the API calls made within the AWS account
- **Event retention: 90 days**
- Export CloudTrail logs into
    - [CloudWatch Logs](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/CloudWatch#logs)
    - [S3](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Simple%20Storage%20Service%20(S3)) (encrypted by default using **SSE-S3**)
- CloudTrail logs up to the last 90 days can be analyzed in CloudTrail Console. Older logs should be present in S3 and can be analyzed using Athena.

> Modifications to log files can be detected by enabling **Log File Validation** on the logging bucket

* Event Types
	* Management Events
		- Events of operations that modify AWS resources. Ex:
		    - Creating a new IAM user
		    - Deleting a subnet
		- **Enabled by default**
		- Can separate Read Events (that don’t modify resources) from Write Events (that may modify resources)
	* Data Events
		- Events of operations that modify data
		    - S3 object-level activity
		    - Lambda function execution
		- **Disabled by default** (due to high volume of data events)
	* Insight Events
		- Enable **CloudTrail Insights** to detect unusual activity in your account
		    - inaccurate resource provisioning
		    - hitting service limits
		    - bursts of AWS IAM actions
		    - gaps in periodic maintenance activity
		- CloudTrail Insights analyzes normal management events to create a baseline and then continuously analyzes write events to detect unusual patterns. If that happens, CloudTrail generates insight events that
		    - show anomalies in the Cloud Trail console
		    - can can be logged to S3
		    - can trigger an EventBridge event for automation

* Encryption
> 	CloudTrail logs are encrypted by default using SSE-S3
> 	A single KMS key can be used to encrypt log files for trails applied to all regions


### Config
- Regional service
- Can be aggregated across regions and accounts
- Record configurations changes over time
- **Evaluate compliance of resources using config rules**
- **Does not prevent non-compliant actions** from happening (no deny)
- Evaluate config rules
    - for each config change (ex. configuration of EBS volume is changed)
    - at regular time intervals (ex. every 2 hours)
- Can make custom config rules (must be defined in Lambda functions) such as:
    - Check if each EBS disk is of type gp2
    - Check if each EC2 instance is t2.micro
- **Can be used along with CloudTrail** to get a timeline of changes in configuration and compliance overtime.
- **Integrates with EventBridge or SNS** to trigger notifications when AWS resources are non-compliant

* Remediation
	- Automate remediation of non-compliant resources using **SSM Automation Documents**
	    - AWS-Managed Automation Documents
	    - **Custom Automation Documents**
	        - to invoke a Lambda function for automation
	- You can set Remediation Retries if the resource is still non-compliant after auto remediation
	- Ex. if IAM access key expires (non-compliant), trigger an auto-remediation action to revoke unused IAM user credentials.


### X-Ray
- Provides an **end-to-end view of requests as they travel through your application**, and shows a map of your application’s underlying components.
- AWS X-Ray helps developers analyze and debug production, distributed applications, such as those built using a **micro-services architecture**.
- Helps understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors.
- Can collect data across AWS Accounts. The **X-Ray agent** can **assume a role** to publish data into an account different from the one in which it is running. This enables you to publish data from various components of your application into a **central account**.

### Trusted Advisor
- Service that analyzes your AWS accounts and provides **recommendations** on:
    - **Cost Optimization**
        - low utilization EC2 instances, EBS volumes, idle load balancers, etc.
        - Reserved instances & savings plans optimizations
    - **Performance**
        - High utilization EC2 instances, CloudFront CDN optimizations
        - EC2 to EBS throughput optimizations, Alias records recommendations
    - **Security**:
        - MFA enabled on Root Account, IAM key rotation, exposed Access Keys
        - S3 Bucket Permissions for public access, security groups with unrestricted ports
    - **Fault Tolerance**:
        - EBS snapshots age, Availability Zone Balance
        - ASG Multi-AZ, RDS Multi-AZ, ELB configuration, etc.
    - **Service Limits**
        - whether or not you are reaching the service limit for a service and suggest you to increase the limit beforehand
- **No installation needed**
- **Weekly email notifications**

### CostExplorer
- Visualize and manage your costs and service usage over time
- Create **custom reports** that analyze cost and usage data
- **Recommendations** to choose an optimal savings plan
- View usage for the **last 12 months & forecast up to 12 months**

> AWS Cost Explorer helps you identify under-utilized EC2 instancess


