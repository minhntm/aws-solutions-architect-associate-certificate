# EC2

## EC2 Pricing Models

### On Demand
Allows you to pay a **fixed rate** by the **hour** (or by the **second**) with no commitment

⇒ suitable for use cases where you dont want any long term commitment like testing and POCs, spiky, unpredictable workloads that cannot be interrupted

### Reserved
Provides you with a capacity reservation, and offer a significant discount on the hourly charge for an instance. Contract Terms are **1 Year** or **3 Year** Terms

- Standard Reserved Instances
- Convertible Reserved Instances
- Scheduled Reserved Instances

### Spot
These are unused EC2 instances that can bid for. Once your bid exceeds the current spot price the instance is launched. The instance **can be shuted down anytime** the spot price becomes greater than your bid price.

⇒ providing for even greater saving if your app have flexible start and end times, very low compute prices, urgent computig needs for large amounts of additional capacity

### Dedicated Hosts
Physical EC2 server. reduce costs by allowing to use your existing server-bound **software licenses** (oracle license...) 

- can be purchased on-demand (hourly)
- can be purchased as a reservation

## EBS
![EBS](./images/ebs.png)

- Termination Protection is turned off by default, you must turn it on
- On an EBS-backed instance, the **default action is for the root EBS volume to be deleted** when the instance is terminated but **any additional volumes by default won't be deleted**
- EBS Root Volumes of your DEFAULT AMI's (amazone machine image) **CAN be encrypted** by use a third party tool or creating AMI's in the aws console or using the API
- Additional volumes can be encrypted

## EC2  Security Groups
- All **Inbound** traffic is **blocked by default**
- All **Outbound** traffic is **allowed by default** (outbound = response of inbound traffic + request to external services)
- Security Groups are **STATEFUL** ⇒ an inbound rule allowing traffic in, that traffic is automatically allowed back out again
- Changes to Security Groups take effect immediately
- n EC2 instances ↔ 1 security group
- 1 EC2 instances ↔ n security group
- cannot block specific IP address using Security Groups (instead use Network Access Control Lists - VPC)
- can specify allow rules, but not specify deny rules