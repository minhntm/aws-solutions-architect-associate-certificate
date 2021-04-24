# Route53

About the name: it is a combination of "Route 66" (historical road running connecting the US east and west) and 53 to standard port of DNS servers.

## Domain Name System (DNS)

- Map IP addresses to domain names
- Top-Level Domain (TLD): .com, .uk, .de
- Second-level domain (SLD): **amazon**.com, .**co**.uk
- Sub domain: **console**.amazon.com, **beta**.google.co.uk

### Hosts and Subdomains

Domains can reference individual **computers or services** through a sub-domain

- [example.com](http://example.com) vs www.example.com
- api.example.com, ftp.example.com, files.example.com
- subdomain **extends the parent domain**

### Full Qualified Domain Name (FQDN)

Also referred to as absolute domain name since is specifies all domain levels, including host name (in the subdomain), at least on higher level domain (e.g. a second level-domain) and the top level domain followed by a ".".

![Full Domain Name](images/full-domain.png)

### Name Servers

A computer designated to translate domain names into IP addresses.

Name servers give answers to queries about domains under their control. Otherwise, they point to other servers or serve cached copies of other name server's data

### Zone Files

Simple text file that contains the mappings between domain names and IP.

Stored in Name Servers

### Top-Level Domain (TLD) Name Registrars

An organization that manages the reservation of Internet domain.

E.g.: Vietnam government manages the reservation of **.vn** domain.

### Steps that map domain names with IP address

1. Type a domain name into web browser
2. Computer checks its host file to see if it has that domain name stored locally if not, check DNS cache to see if visited the site before
3. If not, it contacts the DNS server that it is set up to use, e.g. 8.8.4.4
4. DNS is a hierarchical system with root servers at the top. Root servers handle requests for information about TLDs. 

## Route53

### Main function

- Domain registration
- DNS record configuration
- Health checking: sends requests automatically to verify application is reachable, available, functional

### Hosted Zones

- Collection of resource record sets like a traditional DNS zone file
- 2 types of hosted zones:
    - private: hold information about how to route traffic for a domain within VPC
    - public: hold information about how to route traffic for a domain on the Internet
- The resource record sets contained in a hosted zone must share the same top level and high-level domains (amazon.com., aws.amazon.com., api.amazon.com., not amazon.jp.)

### Health Checks

- Can set health checks on individual record sets
- If a record set fails a health check Route53 provides failover, by routing traffic to a different destination (works by adding overlapping weighted wrapping for the same hosted zone)
- Can sent SNS notification if a health check is failed

### Routing Policies

When setting up DNS Records, you can set a Routing Policy and choose from the following:

Simple Routing

![Simple Routing](images/simple-routing.jpeg)

- Allows only record of any name and type within the Hosted Zone.
- If you set up an A Record with several IP addresses, traffic will be distributed randomly leading to a 50-50 split of traffic (see picture above).
- With simple routing alone, Health Check failover won't be supported (since there is no alternative records to divert traffic to).

Weighted Routing

![Weighted Routing](images/weighted-routing.jpeg)

- Split traffic based on different weights assigned

Latency-based Routing

![latency-based Routing](images/latency-based-routing.jpeg)

- Route traffic based on the lowest network latency (fastest response time)

Failover Routing

  ![Failover Routing](images/failover-routing.jpeg)

  - When primary end point fails the health check, route traffic to second end point
  
Geolocation Routing

  ![Geolocation Routing](images/geolocation-routing.jpeg)

  - Route traffic based on the geographic location of your users
    
Geoproximity Routing (Traffic Flow Only)
  - Route traffic to resources based on the geographic location of users and resource
  - Can route more traffic or less to a given resource by specifying a bias value
  - **Must use Route53 traffic flow**
    
Multivalue Answer Routing

  ![Multivalue Answer Routing](images/multivalue-answer-routing.jpeg)

  - Similar to simple routing, however it allows you to put health checks on each record set

### Tips

- ELBs do not have pre-defined IPv4 addresses, use a DNS name to do that (CNAME)
- Common DNS Types:
    - SOA Records
    - NS Records
    - A Records
    - Alias Records (proprietary Amazon Record, e.g. to reference CloudFront Distributions)
    - CNAME Records
    - MX Record
    - PTR Record
- Can buy domain directly with AWS
- It can take up to 3 days to register domain