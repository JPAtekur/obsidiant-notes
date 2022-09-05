# Elastic File System
Create and configure shared file systems simply and quickly for AWS compute services—no provisioning, deploying, patching, or maintenance required.

Scale your file system automatically as files are added, removed, and burst to higher throughput levels when necessary.

Pay only for the storage you use and reduce costs up to 92 percent by automatically moving infrequently accessed files.

## How it works
Amazon Elastic File System (Amazon EFS) automatically grows and shrinks as you add and remove files with no need for management or provisioning.

![[efs-img.png]]

## Use cases
- Simplify DevOps
- Modernize application development
- Enhance content management systems
- Accelerate data science

## Mount a directory on EFS
- Create security group
	- Add NFS inbond rule and allow security group of EC2 instances
- Create EFS file system with security group
- Create Access point
- Install  amazon-efs-utils
	 ```
	  sudo yum install -y amazon-efs-utils
	```
- Add following commad in /etc/fstab(replace with actual file-system-id, efs-mount-point and access-point-id)
	```
	 file-system-id efs-mount-point efs _netdev,tls,accesspoint=access-point-id 0 0
	```
- Execute following cmd
	```
	 mount -fav
	```