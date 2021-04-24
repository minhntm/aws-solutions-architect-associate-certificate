# VPC
![VPC](images/vpc.jpeg)

## Features

- launch instances into a subnet
- Assign custom IP ranges to each subnet
- Configure route tables for subnets
- Attach internet gateway to VPC
- Security groups can be assigned to EC2 instances for inbound and outbound traffic control
- Network access control lists (ACLS) rules can be associated with subnets for inbound and outbound traffic control.
- Consists of IGWs (Internet Gateways), Route Tables, Network Access Control Lists, Subnets, Security Groups
- Security Groups are stateful (connections that are allowed in are also allowed out), Network Access Control Lists are Stateless
- IP ranges are defined with Cidr notations:
    - 10.0.0.0/16 = 65536 different IPs
    - 10.0.0.0/24 = 256 different IPs
    - 10.0.1.0/28 = 16 different IPs
- When you create a VPC, a default Route Table, Network ACL and Security Group is automatically created. (subnet, internet gateway isn't created)
- us-east-1a in your AWS account is different to us-east-1a in another account (the AZ's are randomized)
- AWS reserve 5 IP within a subnet (you cannot use those)
- 1 AZ can hold multiple AZs
- 1 IGW can only be attached to one VPC
- n Security Group can belong to several VPCs

## Default VPC vs Custom VPC

- The default VPC is pre-setup for your account, allowing you to immediately deploy instances
- All Subnets in default VPC have a route to an IGW and thus are public subnets that can be accessed from the internet.
- Each EC2 instance has both public and private IP address

## VPC Peering

- Allows to connect two VPC with each other via a direct network route using private IP addresses
- Instances behave as if they were on the same private network
- Can peer VPCs with other AWS account or other VPCs in the same account
- 1 central VPC peers with 4 others.
- Transitive peering is not supported (VPC 1 is peered with VPC 2 and VPC 3. This does not mean VPC 2 and VPC 3 are peered)
- Can peer across regions

## Network Address Translation (NAT)

### NAT Instance

- When creating NAT Instance must disable Source/Destination Check on Instance
- NAT instances must be in a public subnet
- Routes traffic of private subnet to NAT instance to allow private Subnets to access the Internet but prevents the Internet from connecting to the those subnets
- Amount of traffic that NAT instance can support depends on instance spec (CPU, RAM, storage...)
- Can create high availability using Autoscaling Groups, multiple subnets in different AZs, and a script to automate failover
- Behind a Security Group
- Official support for NAT Instance ended at the end of 2020. AWS recommends to switcht ot NAT Gateways

### NAT Gateways

- NAT Gateways are allocated to AZs
- Start with throughput at 5Gbps, scale currently to 45Gbps
- No need to patch
- Not associated with Security Groups
- Assigned a public ip address by ElasticIP
- You need to update your Route Table to route internet traffic (0.0.0.0/0) to the NAT Gateway
- No need to disable Source/Destination Checks
- If you have resources in multiple AZs that share the same NAT gateway, if NAT Gateway's AZ is down, resources in the other AZs lose internet access. To create an AZ-independent architecture, create a NAT gateway in each AZ and configure your routing to ensure that resources use the NAT gateway in the same AZ

## Network Access Control List (ACL)

### Ephemeral Port

[Ephemeral Port](https://en.wikipedia.org/wiki/Ephemeral_port): Client decide the port to communication with server (TCP, UDP, SCTP). The server allocate automatically from a predefined range. The allocations are temporary and only valid for the duration of the communication session. After completion of the communication session, the ports become available for reuse.

- If a request comes into a web server in your VPC from a client on the internet, your network ACL must have an outbound rule to enable traffic destined for ephemeral ports.
- If the client resides on instance that sits in your VPC, your network ACL must have an inbound rule to enable traffic destined for the ephemeral ports.

The ephemeral port range varies depending on the client's operating system.

- Many Linux kernels (including the Amazon Linux kernel) use ports 32768-61000.
- Requests originating from Elastic Load Balancing use ports 1024-65535.
- Windows operating systems through Windows Server 2003 use ports 1025-5000.
- Windows Server 2008 and later versions use ports 49152-65535.
- A NAT gateway uses ports 1024-65535.
- AWS Lambda functions use ports 1024-65535.
- In practice, to cover the different types of clients that might initiate traffic to public-facing instances in your VPC, you can open ephemeral ports **1024-65535.**

[Read more](https://docs.aws.amazon.com/vpc/latest/userguide//vpc-network-acls.html#nacl-ephemeral-ports)

### Tips

- Subnets are assigned to default Network ACLs by default. Default Network ACLs allow all outbound and inbound traffic by default
- By default, custom Network ACL denies all in bound and outbound traffic until you add rules
- Block IP Addresses and ports with Network ACLs and not with Security Groups, since those apply to the entire networks rather than individual components
- Multiple Subnets can share the same Network ACL, but a Subnet can only have a single ACL associated with it (When you associate a network ACL with a subnet, the previous association is removed)
- Network ACLs contain a numbered list of rules that is evaluated in order starting with the lowest numbered rule

    ![IP is not denied](images/ip-not-denied.png)

    107.16.110.187 IP is not denied

    ![IP is denied](images/ip-denied.png)

    107.16.110.187 IP is denied

- Network ACLs have separate inbound and outbound rules, each rule can either allow or deny traffic (unlike Security Groups, ACLs are stateless).

## VPC Flow Logs

- Cannot enable flow logs for VPCs that are peered with your VPC unless the peer VPC is in your account
- Cannot tag a flow log
- After you've created a flow log, you cannot change its configuration, for example you can't associate a different IAM role with the flow log
- Not all IP traffic is monitored
    - traffic generated by instances when they contact the Amazon DNS server. If you use your own DNS server, then all traffic to that DNS server is logged
    - traffic generated by a Windows instance for Amazon Windows license activation
    - traffic to and from 169.254.169.254 for instance metadata
    - DHCP traffic
    - traffic to the reserved IP address for the default VPC router

## Bastion host

- NAT Gateway or NAT instance is used to provide internet traffic to EC2 instances in private subnets
- A Bastion is used to securely administer EC2 instances (Using SSH or RDP). Bastion hosts are also called Jump Boxes
- You cannot use a NAT Gateway as a Bastion host

## Direct connect

- With Direct Connect you can directly connect your local data center to AWS
- Useful for high throughput workloads
- Provides a stable and reliable secure connection

## VPC Endpoint

- Enable you to connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without creating a public connection over the Internet
- Instances in the VPC do not require a public IP or a NAT Gateway to communicate with resources in service
- Traffic between your VPC and the other service does not leave the Amazon network
- 2 types:
    - Interface Endpoints
    - Gateway Endpoints (S3, DynamoDB)

## Elastic IP Addresses (EIPs)

- AWS maintains a pool of public IP addresses in each region that you can allocate to your infrastructure, e.g. your EC2 instances
- EIPs are charges for EIPs allocated to your account, even if they are not associated with a resource

## Virtual Private Gateways (VPGs), Customer Gateways (CGWs), Virtual Private Networks (VPNs)

![VPG-CGW](image/../images/vpg-cgw.png)