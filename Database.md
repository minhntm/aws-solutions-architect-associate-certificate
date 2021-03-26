# Database

## DB Security (RDS, Redshift, Aurora)

- Infrastructure resources (IAM)
- Database level (set up permissions for database's user)
- Record level (encrypt to protect data at rest (KMS...), use SSL/HTTPS to protect data in transit )
- Network level (Use VPC, security group for databases deployed with VMs, e.g. RDS)

## RDS

- RDS runs on virtual machines
- Cannot log in to these operating systems
- RDS is not serverless, however **Aurora is Serverless**
- Online Transaction Processing (OLTP) (OLAP Online Analytics Processing - Redshift)
- Once RDS instances are encrypted (use KMS - Amazon Key Management Service or TDE - Transparent Data Encryption), automated backups, read replicas, snapshots is also encrypted
- **DB parameter groups**: act as a container for engine configuration, can apply to one or more DB instances (can change db parameter groups of instance but reboot is required)
- **DB option groups**: act as a container for engine features
- **RPO - Recovery Point Objective** the maximum period of data loss that is acceptable in the event of failure
- **RTO - Recovery Time Objective** the maximum amount of downtime that is permitted to recover from backup and to resume processing
- **License Included:** Oracle Standard One, SQL Server Express, SQL Server Web
- **Bring You Own License:** Oracle all edition (standard one, standard, enterprise), SQL Server Standard, SQL Server Enterprise

### Backups

- Automated backups
    - Enabled by default
    - Backup data is stored in s3 and storage space is free
    - Is deleted after delete the original RDS instance
- Database snapshots
    - Do manually
    - Is stored even after delete the original RDS instance
- While backup the database, storage I/O may be suspended. Use Multi-AZ deployment to eliminate I/O suspension and minimize latency spikes during backups.
- When restore the database, we will get a new RDS instance with a new DNS endpoint
- When restore the database, only the default **DB parameter** and **security groups** are associated with the restored instance (can be changed after the restore is completed)

### Multi-AZ

![Multi-AZ](images/read-replica.png)

- Copy database to replica in another AZ
- **For Disaster Recovery only** (availability, failover) (For improved performance use Read Replicas)
- Can force a failover from 1 AZ to another by rebooting rds instance

### Read Replica

![Read Replicas](images/multi-az.png)

- **Used for scaling**, increase performance, not for Disaster Recovery
- Must have automatic backups turned on
- Can have up to 5 read replica copies of any database
- Can have read replicas of read replicas (but watch out for latency)
- Each read replica has its own DNS end point
- Can have read replicas that use Multi-AZ deployment
- Can create read replicas of source DBs with Multi-AZ deployment enabled
- Can be promoted to master. This breaks the replication
- **Can have a read replica in different regions**
- Replica can be MySQL, Postgres, MariaDB, Oracle, Aurora (only exception is Microsoft SQLServer)
- Use case:
    - For read-heavy workloads
    - Handle read traffic while the source DB instance is unavailable (E.g. due to I/O suspension for backups or scheduled maintenance)
    - Handle offload reporting or data warehouse scenarios instead of the primary DB Instance

## DynamoDB

- Stored on SSD storage
- Spread across 3 geographically distinct data centers
- Eventually Consistent Reads is default (read data after write within 1 second)
- Can use strongly consistent read (read data after write less than 1 second), but not recommend due to higher latency, lower throughput and lower availablility
- Each table in DynamoDB is limited to 20 global secondary indexes (default limit) and 5 local secondary indexes.
- The maximum item size in DynamoDB is 400 KB

## Redshift

![Redshift](images/redshift.png)

- Backups enabled by default with 1 day retention period (maximum is 35 days)
- Always maintain at least 3 copies of your data (the original, a replica on the compute nodes and a backup in S3)
- Can asynchronously replicate your snapshots to S3 in another region for disaster recovery
- Backups:
    - Automatic backup
    - Manual backup

## Aurora

- 2 copies of your data are contained in each AZ with a minimum of 3 AZs, which makes a minimum of 6 copies of your data
- Can share Aurora Snapshots with other AWS accounts
- 2 types of replicas available
    - Aurora Replicas ⇒ automated failover
    - MySQL replicas
- hHve up to 15 Aurora Replicas
- Automated backups turned on by default
- Migrate from MySQL to Aurora
    - Create read replica and promote it
    - Create a snapshot and restore from that snapshot

## ElastiCache

- Use to increase database and web application performance
- 2 types
    - Redis
        - Multi-AZ
        - Can be backed up and restored
    - Memcached
        - Multi-threaded