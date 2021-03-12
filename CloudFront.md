# CloudFront

- CloudFront is the AWS Content Delivery Network (CDN) 
- A Content Delivery Network (CDN) is a network of distributed servers that delivers webpages and other web content to users based on their geographic locations, the origin of the webpage, and a content delivery server.
- Objects are cached in line with a configurable TTL (Time To Live). This cache can get cleared at a charge

![CloudFront](./images/cloudfront.png)

## Terminology
- Edge Location - This is the location where content will be cached. Every AWS region has dedicated edge locations. Edge locations are not just read only - you can write to them too (eg: put an object to them).
- Origin - This is the downstream location of all files the CDN will distribute. This is done by providing a domain name that can point to an S3 Bucket, an EC2 Instance, an Elastic Load Balancer, a Route53 managed domain or any other domain-addressable resource.
- Distribution - This is the name given to the CDN which consists of a collection of Edge Locations
- Web Distribution - Typically used for Websites
- RTMP - Used for Media Streaming

## CloudFront vs S3 Transfer Acceleration

While CloudFront improves the delivery of your content (GET requests), S3 Transfer Acceleration improves the performance of **uploads** to S3 buckets (PUT or POST requests). This is done by optimization to the TCP protocol and additional intelligence between client and S3 bucket.
