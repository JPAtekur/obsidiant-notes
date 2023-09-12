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

