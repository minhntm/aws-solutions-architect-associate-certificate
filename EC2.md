# EC2, CloudWatch, CloudTrail

## EC2

SSH to EC2 instance and request to the [instance meta-data URL](http://169.254.169.254/latest/meta-data) for instance's information (public, private IP...)

- By default, AWS has a limit of 20 instances per region
- Termination Protection (deactivation of termination of EC2 instance via API) is turned off by default

## Things that can be modified after launch

- Instance type (size, memory, network...)
- Security groups: can be changed if the instance is running in an VPC (default when launch instance)
- Termination protection. Note that termination protection protects from termination calls from AWS Console, CLI, API (human errors), not prevent termination triggered by an OS shutdown command termination from an Auto Scaling group, termination of a Spot Instance (due to Spot price changes)

### EC2 Pricing Models

#### On Demand

Allows you to pay a **fixed rate** by the **hour** (or by the **second**) with no commitment

- Suitable for use cases where you don't want any long term commitment like testing and POCs, **spiky**, unpredictable workloads that cannot be interrupted

#### Reserved

Provides you with a capacity reservation, and offer a significant discount on the hourly charge for an instance. Contract terms are **1 Year** or **3 year** terms

- Standard Reserved Instances
    - can change Availability Zone, instance size (for Linux OS), networking type
- Convertible Reserved Instances
    - can change Availability Zone, instance size (for Linux OS), networking type
    - can change instance families, operating system, tenancy, and payment option
- Scheduled Reserved Instances

#### Spot

These are unused EC2 instances that you can bid for. Once your bid exceeds the current spot price, the instance is launched. The instance **can be shut down anytime**, once the spot price becomes greater than your bid price.

⇒ Provides for even greater savings, if your applications have flexible start and end times
- Very low compute prices, thus can satisfy urgent computing needs for large amounts of additional capacity 
- E.g. Spot Instances can be used for workloads with several Reserved Instances reading from a queue, in order to alleviate heavy traffic in a cost-effective way

#### Dedicated Hosts

Physical EC2 server. Reduce costs by allowing to use your existing server-bound **software licenses** (oracle license...) 

- Can be purchased on-demand (hourly)
- Can be purchased as a reservation
- In order to change Dedicated Hosting back to default hosting, use CLI, SDK or API. Stop instances before change

## Security Groups

- All **Inbound** traffic is **blocked by default**. Security Groups selectively open traffic routes, e.g. for certain ports, ips addresses and sources.
- All **Outbound** traffic is **allowed by default**
- Security Groups are **stateful**: if an Inbound Rule allows traffic in, that traffic is automatically allowed back out again
- Changes to Security Groups take effect immediately
- Assignment of Security Groups is very flexible: you can assign one Security Group to several EC2 instances and assign several Security Groups to one EC2 instance.
- Security Groups can't block specific IP address. Instead use Network Access Control Lists (ACLs) that are defined at your VPC
- **Combine of multiple Security Group: (or)** if an instance has multiple Security Groups, it has the sum of all rules in the various groups

### EBS (Elastic Block Storage)

![EBS](images/ebs.png)

- On an EBS-backed instance, the **default action is for the root EBS volume to be deleted** when the instance is terminated but **any additional volumes by default won't be deleted**
- EBS Root Volumes of your default AMI's (Amazon machine image) **can be encrypted** by using a third party tool, creating AMI's in the aws console or using the API
- Additional volumes can be encrypted

## Volumes, Snapshots

- Volumes exist on EBS - the storage technology is block storage. Volumes can be considered virtual hard disks and are effectively a chunk of a gargantuan physical device that appears and works like a separate disk.
    - Volumes are **always** be in the same AZ as the EC2 instance, since they require storage devices that are physically connected to the servers hosting the 
    - Can change EBS volume sizes and storage type even when it is attached to a running instance. To make use of extra capacity, the file system will need to get extended via operating system configuration. 
- Snapshots are stored on S3 and can be considered a point in time copy of the current state of your disk
    - Can take a snapshot while the instance is running, **however** should stop the instance before taking the snapshot for volumes that serve as root devices to ensure that snapshots will be consistent
    - Snapshots are incremental: only the blocks that have change since your last snapshot are moved to S3
        - t1 ⇒ snapshot1
        - create file a.txt ⇒ t2 ⇒ snapshot2
        - S3 2 files is storaged: snapshot1, (snapshot2 -snapshot1)
        - first snapshot may take some time to create
- AMI (Amazone Machine Image) can be created from both Volumes and Snapshots

### **Move a Volumes from one AZ to another**

1. Take a snapshot
2. Create an AMI
3. Launch the EC2 instance in a new AZ (choose Subnet of AZ)

### **Move a Volumes from one region to another**

1. Take a snapshot
2. Create an AMI
3. Copy AMI to other region
4. Launch the EC2 instance by the copied AMI

### Encryption on Volumes and Snapshots

- Snapshots of encrypted volumes are encrypted automatically
- Volumes restored from encrypted snapshots are encrypted automatically
- Can share snapshots but only if they are unencrypted
- Can encrypt root device volumes when create EC2 instance

### Encrypt an unencrypted root device volume

1. Take a snapshot of the unencrypted root device volume
2. Copy snapshot and enable encryption
3. Create an AMI from the encrypted Snapshot
4. Use that AMI to launch new encrypted instances

### Snapshot ⇒ AMI ⇒ instance

- Copy a snapshot can enable **encryption**
- Create AMI from a snapshot can only change **virtualization type** (HVM, PV)
- Copy AMI can change **region**
- Launch instance from an AMI can choose **AZ**

## EBS vs Instance Store

- Instance Store Volumes are also called Ephemeral Storage
    - Use case: temporary storage such as buffers, caches, scratch data or data that is replicated across a fleet of instances such as load-balanced pool
    - Very cost effective, very high IOPS
    - **Require provision** but charge is based on the **hourly cost of instance**
    - Instance store volumes **can't be stopped**. If the instance fails they lose their data
- EBS:
    - EBS backed instances **can be stopped**. If instance is stopped there is not loss of data
    - **Require provision**, charge is based on the total **amount of storage provisioned**
- Instances can be rebooted with both storage types without data loss (a reboot does not constitute an instance stop)
- By default, both storage types will be deleted on termination if used as root volumes. For EBS root volumes, can choose option that retains the volume after termination.

## IAM Roles With EC2

- Attaching roles to instances is more secure and easier than storing access key and secret access key with applications.
- Roles can be assigned to an EC2 instance after it is created
- Roles are globally visible and can be used in any region

## EFS (Elastic File System)

- Supports NFSv4 protocol
- Only **pay as you use**. Scales up elastically (**no pre-provisioning of set capacity** required)
- Can scale up to petabytes
- Supports thousands of concurrent NFS connections
- Stores data across multiple AZ's within a region
- **Read After Write Consistency**

## EC2 Placement Groups

- Clustered Placement Group
    - AWS recommends to use uniform instances within this groups
    - Can't span multiple AZ
    - Use case: low network latency / high network throughput
- Spread Placement Group
    - Can span multiple AZ
    - Maximum of 7 running instances per AZ per group
    - Use case: place a relatively small number of EC2 instances on district hardware to reduce failure correlation
- Partitioned
    - Can span multiple AZ
    - Maximum of 7 logical partitions (deployment environments with segregated hardware, e.g. server rack, power source, network connection) per AZ
    - The number of instances that can be launched into a partition placement group is limited only by the limits of your account
    - Use case: Multiple EC2 instances HDFS, HBase, Cassandra
- Only certain types of instances can be launched in a placement group (compute optimied, gpu, memory optimied, storage optimized)
- Can't merge placement groups
- Can't move an existing instance into a placement group (can create AMI from existing instance and launch new instance into a placement group)
- No charge for creating a placement group
- Name of placement group must be unique within your AWS account


## Bootstrapping

- Allows to run a script to initialize your instance with OS configurations, applications