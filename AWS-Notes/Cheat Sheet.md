## ElastiCache
- **In-memory key-value store** with **sub-millisecond latency**
- Need to provision an underlying EC2 instance
- Redis -> In-memory data store | Memcached -> **Distributed** memory object cache
- Read Replicas (for scaling reads & HA)  |   No replication
- Backup & restore  |  No backup & restore
- **Single-threaded**  |  **Multi-threaded**
- **HIPAA compliant** | **Not HIPAA compliant**
- At rest encryption using KMS
- In-flight encryption using SSL

## Web Application Firewall (WAF)
- Protects your application from common **layer 7 web exploits** such as **SQL Injection** and **Cross-Site Scripting (XSS)**
- - Layer 7 has more data about the structure of the incoming request than layer 4 (used by [AWS Shield](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/AWS%20Shield))
- Can only be deployed on
    - [Application Load Balancer](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Elastic%20Load%20Balancer%20(ELB)#application-load-balancer-alb)
    - [API Gateway](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/API%20Gateway)
    - [CloudFront](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/CloudFront)
-  WAF contains **Web ACL (Access Control List)** containing rules to **filter requests** based on:
    - **IP addresses**
    - HTTP headers
    - HTTP body
    - URI strings
    - Size constraints (ex. max 5kb)
    - **Geo-match** (block countries)
    - **Rate-based rules** (to count occurrences of events per IP) for **DDoS protection**

## AWS Shield
### Shield Standard
- Free service that is activated for every AWS customer
- Provides protection from **SYN/UDP Floods, Reflection attacks** and other **layer 3 & layer 4 attacks**
### Shield Advanced
- **DDoS mitigation** service ($3,000 per month per organization)
- Protect against more sophisticated attacks on
    - EC2 instances
    - Elastic Load Balancing (ELB)
    - CloudFront
    - Global Accelerator
    - Route 53
- 24/7 access to AWS **DDoS Response (DRP)** team
- Get reimbursed for usage spikes due to DDoS

## Storage Gateway
- Bridge between on-premises data and [S3](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Simple%20Storage%20Service%20(S3)) for **Hybrid Cloud**
- Not suitable for one-time sync of large amounts of data (use [DataSync](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/DataSync) instead)
- Optimizes data transfer by sending only changed data
- **Data is cached at the file gateway** for low latency access
- Integrated with **Active Directory (AD)** for user authentication
#### Volume Gateway
- Uses **iSCSI protocol**
- Two kinds of volumes:
    - **Cached volumes**: storage extension using S3 with caching at the volume gateway
    - **Stored volumes**: entire dataset is on premise, scheduled backups to S3 as **EBS snapshots**
#### Tape Gateway
- Used to backup on-premises data using tape-based process to S3 as Virtual Tapes
- Uses **iSCSI protocol**
#### FSx File Gateway
- Windows native compatibility (SMB, NTFS, Active Directory)
- **Data is cached at the file gateway** for low latency access

## Route 53
* AWS managed **Authoritative DNS**
* Affected by client's DNS caching (not suitable for [Blue-Green Deployment](https://notes.arkalim.org/notes/aws%20solutions%20architect%20associate/Blue-Green%20Deployment) if the client caches DNS queries)
#### Hosted Zone
- **Public Hosted Zone**
    - resolves public domain names
    - can be queried by anyone on the internet
- **Private Hosted Zone**
    - resolves private domain names
    - can only be queried from within the VPC
- 