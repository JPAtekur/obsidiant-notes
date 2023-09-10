### Lambda
* A function can use up to 5 layers at a time
* - Environment variables: 4 KB
* - RAM: 128 MB - 10GB
* To enable your Lambda function to access resources inside your private VPC, you must provide **VPC subnet IDs** and **Security Group IDs**. AWS Lambda uses this information to set up ENIs.

### HPC
* **Cluster placement group**
* **Elastic Fabric Adapter (EFA)**
	 - Enhanced for HPC
	- Supported in **Linux** only
- **AWS Batch**
- **AWS Parallel Cluster**
	- - Ability to enable **EFA** on the cluster

### Workflows
* Step Functions
	* - Used to build **serverless workflows** to **orchestrate Lambda functions**
	* - Maximum workflow execution time: 1 year
* Simple Workflow Service (SWF)
	* - Outdated service (step functions are preferred instead)
	* - **Ensures that a task is never duplicated** (could replace standard SQS queues)
	* - **Built-in human intervention step**

### Instance Store
- **Hardware storage directly attached to EC2 instance** (cannot be detached and attached to another instance)
- - **Ephemeral storage** (loses data when the instance is stopped, **hibernated** or terminated)
- You can specify the instance store volumes only when you launch an instance. You can’t attach instance store volumes to an instance after you’ve launched it.

### Elastic Block Storage (EBS)
- **Bound to an AZ**
- `DeleteOnTermination` attribute can be updated for the root EBS volume only from the CLI
- **gp3**
	*  **3,000 lOPS baseline** (max 16,000 - independent of size)
* **gp2**
	* - **Burst IOPS up to 3,000**
* Provisioned IOPS SSD
	* - Max IOPS: **64,000 for Nitro EC2 instances** & **32,000 for non-Nitro**
	* - io2 have more durability and more IOPS per GB (at the same price as io1)
	* **io2 Block Express**
		* Sub-millisecond latency
		- Max IOPS: 256,000
		- 1000 lOPS per GB
- Hard Disk Drives (HDD)
	- - **Cannot be used as boot volume** for an EC2 instance
	- **Throughput Optimized HDD (st1)**
		- Optimized for large sequential reads and writes (Big Data, Data Warehouses, Log Processing)
		- **Max throughput: 500 MB/s**
		- - **Max IOPS: 500**
	- **Cold HDD (sc1)**
	    - For infrequently accessed data
	    - Cheapest
	    - **Max throughput: 250 MB/s**
	    - **Max IOPS: 250**
- Snapshots
	* ***Data Lifecycle Manager (DLM)** can be used to automate the creation, retention, and deletion of snapshots of EBS volumes

### Elastic File System (EFS)
- Can be mounted to multiple EC2 instances **across AZs**
- - Compatible with **Linux** based AMIs (**POSIX** file system)
- - **Uses security group to control access to EFS**
- Lifecycle management feature to move files to **EFS-IA** after N days
- - **POSIX Permissions** to control access from hosts by IAM User or Group

### Simple Storage Service (S3)
- **Max Object Size: 5TB**
- - **SYNC** command can be used to **copy data between buckets**, possibly in **different regions**
- - Force objects to be encrypted at upload by using Bucket policy
- Server Access Logging
	- - Most detailed way of logging access to S3 buckets (better than [CloudTrail](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/CloudTrail))
	- Does not support **Data Events** & **Log File Validation** (use [CloudTrail](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/CloudTrail) for that)
- Replication
	- - **Asynchronous replication**
	- - Supports **cross-region** and **cross-account** replication
- - Can **move data into IA from Standard only after 30 days**
- Analytics
	- - Provides analytics to determine when to transition data into different storage classes
	- **Does not work for ONEZONE_IA & GLACIER**
- - SSE-KMS may create bottleneck in S3 performance
- **Byte-range fetches**
	- - - Parallelize download requests by fetching specific byte ranges in each request
	- Better resilience in case of failures since we only need to refetch the failed byte range and not the whole file
- **S3 Transfer Acceleration**
    - Speed up **upload and download** for **large objects (>1GB)** for **global users**
- S3 Select
	- Select a subset of data from S3 using **SQL queries** (**server-side filtering**)
- S3 Notification Events
	- Generates events for operations performed on the bucket or objects
	- - Targets
	    - SNS topics
	    - **SQS Standard** queues (not FIFO queues)
	    - Lambda functions

### Relational Database Service (RDS)
* ***Automated Backups** (enabled by default)
	- Daily full backup of the database (during the defined maintenance window)
	- Backup retention: 7 days (max 35 days)
- Auto Scaling
	- - Free storage is less than 10% of allocated storage
	- Low-storage lasts at least 5 minutes
	- 6 hours have passed since last modification
- - **Up to 5 read replicas** (within AZ, cross AZ or cross region)
- - **Asynchronous Replication** (seconds)
- Multi AZ
	- - **Synchronous Replication**
- - RDS events only provide operational events on the DB instance (not the data)
- To capture data modification events, use **native functions** or **stored procedures** to invoke a **Lambda** function.
- Any database engine level upgrade for an RDS DB instance with Multi-AZ deployment triggers both the primary and standby DB instances to be upgraded at the same time. This causes **downtime** until the upgrade is complete. This is why it should be done during the maintenance window.

