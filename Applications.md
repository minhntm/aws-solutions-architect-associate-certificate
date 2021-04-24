# Appications

## Simple Queue Service (SQS)

### SQS standard (offers as the default queue type)

- Nearly-unlimited number of transactions per second
- Message is delivered at least once (due to replication under the hood at massive scale, message duplication is possible on rear occasions)
- Messages are delivered in loose order (due to replication under the hood at massive scale, message out-of-order delivery is possible on rear occasions)

    ![Appications/E6E72E5A-C02B-45F4-83ED-E60EE47F331E.jpeg](images/sqs-standard.jpeg)

### FIFO queue

- Message is delivered once (remain until a consumer processes and deletes it)
- Messages are delivered in order
- 300 transactions per second

    ![Appications/7BF7D8EB-09A6-4418-806D-F66A303C57BF.jpeg](images/sqs-fifo.jpeg)

### Tips

- SQS is pull based, not pushed based
- Messages are 256KB in size
- Messages can be kept in the queue from 1 minute to 14 days, the default retention period is 4 days
- VisibilityTimeout: the amount of time that the message is invisible in the SQS queue after a reader picks up that message. Provided the job is processed before the visibility time out expires, the message will then be deleted from the queue. If the job is not processed within that time, the message will become visible again and another reader will process it. This could result in the same message be delivered twice
    - Visibility timeout maximum is 12 hours
- DelaySeconds: when a new message is added to the SQS queue, it will be hidden from consumer instances for a fixed period
- SQS long polling is a way to retrieve messages from your Amazon SQS queues. While the regular short polling returns messages immediately (even if the message queue being polled is empty), long polling doesn't return a response until a message arrives in the message queue, or the long poll times out ⇒ **save money**
    - WaitTimeSeconds + ReceiveMessageWaitTimeSeconds: when the consumer instance polls for new work, the SQS service will allow it to wait a certain time for one or more message to be available before closing the connection (setting ReceiveMessageWaitTimeSeconds to 0 is equivalent of short polling)

## Simple Work Flow (SWF)

### SQS vs SWF

SWF

- Retention period 1 year
- Task-oriented API
- Task is assigned only once and is never duplicated
- Keeps track of all the task and event

SQS (Standard)

- Retention period 14 days
- Message-oriented API
- Need to handle duplicated messages and ensure that a message is processed only once
- Need to implement your own application-level tracking, especially if you use multiple queues

### SWF Actors

- Workflow starter - initiate workflow
- Decider - control the flow of activity tasks
- Activity worker - carry out the activity tasks

## Simple Notification Service (SNS)

### SNS Benefits

- Push-based delivery (not pull based)
- Simple APIs, easy integration
- Flexible message delivery over multiple transpot protocols
- Inexpensive, pay-as-you-go
- Web-based AWS Management Console offers the simplicity of a point-and-click interface

## Elastic Transcoder

- Media Transcoder in the cloud
- Convert media files from their original source format in to different formats that will play on smartphones, tablets, PCs
- provide transcoding presets for popular output format
- pay based on the minutes that you transcode and th resolution at which you transcode

## API Gateway

- Has caching capabilities to increase performance
- Is low cost and scales automatically
- Can throttle API Gateway to prevent attacks
- Can log results to CloudWatch
- Enable CORS on API Gateway to allow client-based Javascript to communicate with multiple domains. See more: [CORS - Cross Same Origin Policy](https://www.notion.so/CORS-Cross-Same-Origin-Policy-98390b3dd7554abcbad0a53485ee75d9)

## Kinesis

A platform on AWS to stream data to

### Kinesis Streams

- Data is persisted in 24 hour (up to 7days)
- consist of shards:
    - 5 transactions/second for reads, maximum total data read: 2MB/second
    - 1000 records/second for writes, maximum total data write: 1MB/second (including partition keys)
    - total capacity of the stream is the sum of the capacities of itss shards

    ![Appications/4E7D482D-915B-45CB-93E1-D16C88AA98B3.jpeg](images/kinesis-stream.jpeg)

### Kinesis Firehose

- No persistent storage (data has to be processed when it comes in using lambda)

![Appications/CCDBD4CF-923D-4385-88BA-10DF0743F1B8.jpeg](images/kinesis-firehose.jpeg)

### Kinesis Analytics

- Analyze the data on the fly inside either service

![Appications/323883A0-25C7-4680-8BFE-758AB43EB7A0.jpeg](images/kinesis-analytic.jpeg)

## Cognito - Web Identity Federation

- Let your users access to AWS resources after they have authenticated with a identity provider like Amazon, Facebook, Google
- Sign up and sign in to your apps
- Access for guest users
- Act as an Identity Broker between your app and Web ID provider
- Synchronize user data for multiple devices
- Recommend for all mobile app AWS services

### User Pools

- For authentication purpose
- Manage sign-up sign-in functionality for mobile and web app
- Can sign-in directly (use email, password) or use Facebook, Amazon, Google

### Identity Pools

- For authorization purpose
- Provide temporary AWS credentials to access AWS services like S3 or DynamoDB

![Appications/125D7202-28F6-45BE-9451-14B1CCC8A204.jpeg](images/identity-pool.jpeg)

## Key Management Service (KMS)

Compare with other encrypt service 

![Appications/21C61AF5-C9B6-4C57-8F20-DAFA7A6309FC.jpeg](images/rest-encryption.jpeg)