# VPC

## Features

- launch instances into a subnet
- assign custom IP ranges in each subnet
- configure route tables between subnets
- attach internet gateway to VPC
- better security control over aws resources
- instance security groups
- subnet network access control lists (ACLS)
- consists of IGWs (Virtual Private Gateways), Route Tables, Network Access Control Lists, Subnets, Security Groups
- Security Groups are Statefull, Network Access Control Lists are Stateless
- 10.0.0.0/16 ⇒ 65536 IP
10.0.0.0/24 ⇒ 256 IP
10.0.1.0/28 ⇒ 16 IP
- create a VPC ⇒ default Route Table, Network ACL, Security Group is automatically created. (subnet, internet gateway isn't created)
- us-east-1a in your AWS account is different to us-east-1 in another account (the AZ's are randomized)
- AWS reserve 5 IP within a subnet (you cannot use those)
- n Subnet → 1 AZ (~~n AZ → 1 subnet~~)
- 1 Internet Gateway ↔ 1 VPC
- n Security Group → 1 VPC  (~~1 Security Group → n VPC~~ )

## Default VPC vs Custom VPC

- default VPC is user friendly, allowing you to immediately deploy instances
- All Subnets in default VPC have a route out to the internet
- Each EC2 instance has both public and private IP address

## VPC Peering

- allow to connect 1 VPC with another via a direct network route using private IP addresses
- instances behave as if they were on the same private network
- can peer VPC's with other AWS account or other VPCs in the same account
- 1 central VPC peers with 4 others. **NO TRANSITIVE PEERING**
- can peer between regions

## Network Address Translation (NAT)

### NAT Instance

- when creating NAT Instance must disable Source/Destination Check on Instance
- NAT instances must be in a public subnet
- must route traffic of private subnet to NAT instance
- amount of traffic that NAT instance can support depends on instance spec (CPU, RAM, storage...)
- can create high availability using Autoscaling Groups, multiple subnets in different As, and a script to automate failover
- Behind a Security Group

### Nat Gateways

- 1 NAT Gateway → 1 AZ (n NAT Gateway → 1 AZ ?)
- preferred by the enterprise
- start with throughput at 5Gbps, scale currently to 45Gbps
- No need to patch
- Not associated with security groups
- assigned a public ip address by ElasticIP
- remember to update route tables
- no need to disable Source/Destination Checks
- if you have resource in multiple AZs and they share one NAT gateway, if NAT Gateway's AZ is down, resources in the other AZs lose internet access. To create an AZ-independent architecture, create a NAT gateway in each AZ and configure your routing to ensure that resources use the NAT gateway in the same aZ

## Network Access Control List (ACL)

### Ephemeral Port

[Ephemeral Port](https://en.wikipedia.org/wiki/Ephemeral_port): Client decide the port to communication with server (TCP, UDP, SCTP). The server allocate automatically from a predefined range. The allocations are temporary and only valid for the duration of the communication session. After completion of the communication session, the ports become available for reuse.

- if a request comes into a web server in your VPC from a client on the internet, your network ACL must have an outbound rule to enable traffic destined for ephemeral ports.
- If an instance in your VPC is the client initiating a request, your network ACL must have an inbound rule to enable traffic destined for the ephemeral ports.

The range varies depending on the client's operating system.

- Many Linux kernels (including the Amazon Linux kernel) use ports 32768-61000.
- Requests originating from Elastic Load Balancing use ports 1024-65535.
- Windows operating systems through Windows Server 2003 use ports 1025-5000.
- Windows Server 2008 and later versions use ports 49152-65535.
- A NAT gateway uses ports 1024-65535.
- AWS Lambda functions use ports 1024-65535.
- In practice, to cover the different types of clients that might initiate traffic to public-facing instances in your VPC, you can open ephemeral ports **1024-65535.**

[Read more](https://docs.aws.amazon.com/vpc/latest/userguide//vpc-network-acls.html#nacl-ephemeral-ports)
