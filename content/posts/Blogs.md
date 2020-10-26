---
title: "AWS Certified Solutions Architect Exam Notes - 2020"
date: 2020-06-25T16:41:12+02:00
draft: true
---

S3
---
- S3 is **Object** based
- **Unlimited** storage
- S3 Buckets are in a universal namespace, names must be **unique** globally
- Files can be from **0 bytes** to **5TB**
- Upload an object to S3 receive a **HTTP 200 Code**


S3 Consistency model
--------------------
- Reads after Write consistency for PUTS of new objects
- Eventual consistency for overwrite PUTS and DELETES. This means that if you try a GET operation **immediately** after a PUT or DELETE
operation of a object, the returned object might be the previous one (old). It will eventually get consistent with the newer intentended object state, this delay is due to the propagation of objects in S3.

S3 Types
--------

S3 Standard 
- Active data, frequently used
- Milisecond access
- >= 3 AZs

S3 Intelligent Tiering 
- Data with changing access patterns
- Milisecond access
- >= 3 AZs

S3 Standard-IA 
- Inactive data, infrequently accessed 
- Milisecond access
- >= 3 AZs
- 128KB min. object

S3 One Zone - IA 
- Inactive data
- Data that can be lost
- Milisecond access
- 1 AZ
- 128KB min. object

S3 Glacier
- Archive data
- Access data from minutes to hours 
- 90 days min. duration
- >= 3 AZs
- 99.9% availability 99.999999999% durability

- Retrieval options: Expedited (1-5 minutes), Standard (3-5 hours), Bulk (5-12 hours)

S3 Glacier Deep Archive
- Archive data
- Access data from hours
- 180 min. duration
- >= 3 AZs
- 99.9% availability 99.999999999% durability

- Retrieval options: Standard (within 12 hours), Bulk (within 48 hours)

S3 Security
-----------

- By default a Bucket is **Private**
- S3 Buckets can be configured to create **Acccess Logs**, which log all requests made to the S3 Bucket. This can be sent to another bucket and even to another bucket in another account.
- Control access using :
    
    - Bucket Policies - applied at bucket level
    - Bucket ACL (Access Control Lists) - applied at an object level

S3 Encryption
-------------

Encryption in transit is achieved by 
- SSL/TLS

Encryption At Rest (Server Side) is achieved by
- SSE-S3  - AWS S3 Server-Side Encryption Managed Keys
- SSE-KMS - AWS Key Management Service
- SSE-C   - Server-Side Encryption With Customer Provided Keys

Client Side Encryption: client encrypt files and uploads them into S3.

- Prevent unencrypted files from being uploaded by using a policy which only allows requests which include **x-amz-server-side-encryption** parameter in the request header.

S3 Versioning
-------------

- Stores all versions of an object (including all writes and even if you delete a object)
- Great Backup tool
- Once enabled **Versioning cannot be disabled** only **paused**
- Integrates with **Lifecycle** events
- Versioning has **MFA Delete** capability, which uses mult-factor authentication, can be used to provide addittional layer of security

- Update of a file, automatically becames **private**, rollback to previous version, sets the file access that was defined for that version

S3 Lifecycle Mgmt rules
-----------------------

- Automates transition of objects between different storage tiers
- Can be used with versioning

S3 Cross-Account Access
-----------------------
 - Trust relationship
 - Account (A) with Role that allow trust relationship to Account B and ACL to access Bucket
 - Account B will has a policy that allow to assume the Role 

S3 Cross Origin Resource Sharing (CORS)
---------------------------------------


S3 Cross Region Replication (CRR)
---------------------------------

- Cross Region replication can be enabled any time but only replicates new objecs
- Versioning is a prerequisite to Enable cross region replication

S3 Pre-Signed URL
-----------------

- Avoids unwanted access to files, the pre signed URLs are valid only for the specified duration.

S3 Origin Access Identity (OAI)
-------------------------------

- AWS Cloudfront Origin Access Identity (OAI) is used to maintain secure access to files in S3 that you serve with CloudFront.

S3 Gateway Endpoint
-------------------

- Connect directly S3 without using internet.
- You must define the region of S3
- creates a route with target to vpce of S3

Glacier
-------
- Retrieve in 12 hours
- Faster retrieval will cost extra

ELB - Elastic Load Balancers
----------------------------

Application (Layer 7): 

- Application-aware: Use of Listners that contain rules (/orders,/images)
- Supports HTTP and HTTPS 
- Redirects (ex. HTTP to HTTPS)
- Fixed Response (Auto respond based on criteria)
- Slow start (lets instances warm up and cache)

Security

- Integrates with WAF (Web Application Firewall) and CSM (Certificates Security Manager)
- SNI - Server Name Indication - unique cert per application
- Authentication
- Resource level and tag based permissions

Availability

- Cross-zone load balancing

Network: High performance, more expensive



CLassic : Most cost effective (Layer 4 and Layer 7)

- Content Base routing




- 504 Error message means that the gateway has timed out. This means that the application not responding within the idle timeout period.

- If you need the IPV4 address of your end user, look for the **X-Forwarded-For header

- Instances are monitored: InService, or OutOfService

- Health Checks check the instace helath by talking to it

- Load Balancers have their own DNS name. You are never given an IP address.


Sticky Sessions
No Cross-Zone Load Balancing
Path patterns

## VPC



http://cidr.xyz

- When you create a VPC, a default route table, Network Access Control List (NACL) and a default Security Group is created  
- It will not create any subnets, nor will create a default Internet Gateway
- Availability Zones are randomized, it means the same us-east-1 in different AWS accounts can be completely different
- A,azon always reserves 5 IP addresses within your subnets
- You can only have one Internet Gateway per VPC
- Security groups cannot span VPCs

### VPC Flow Log

- Publish to CloudWatch and S3
- Levels: **EC2**, **VPC** and **Subnet** level
- VPC Flow logs cannot be modified/updated, you must delete and create again


Auto-Scaling
============

Groups



Applications
============

SQS 
===

Message Queue to **decouple** services, 
**Poll/Pull based**

- Standard Queues

- FIFO Queues
 order preserved
 support message groups
 With messages to 256Kb or less
 - Kept in queue for 1 min to 14 days, in average keeps it for 4 days
 - 300 TPS

 - **Visibility timeout** is the amount of time that a message is invisible in the SQS queue after a reader picks up that message. Provided that the job is processed before the visibility timeout expires, the message then will be deleted from the queue. If the job is not processed within that time, the mesasge will become visible again and another reader will process it. This could result in the same message being deleivered twice.
 - Visibility timeout maximum is 12 hours.

 - SQS guarantess that your messages will be processed at least once.

 - SQS **long polling** is a way to retrieve messages from SQS queues. While the regular short polling returns immediately (even if the message queue being polled is empty), long polling does not return a response until a message arrives in the message queue, or the long poll times out.


SWF
===

Amazon Simple Workflow, easy to coordinate work accross distributed application components.

- Tasks represents invocations of various processing steps in an application which can be performed by executable code, web service calls, human actions and scripts.

-Workflow can last to 1 yaer.
-Task oriented API vs SQS message oriented API

SWF actors:
Workflow starters - an application that can start a workflow.
Deciders - Control the flow of activity tasks in a workslow execution.
Activity Workers - perform the tasks

SNS
===
**Push** notifications such as SMS, e-mail
Instantaneous push vased delivery (no polling)
Topics

Elastic Transcoder
==================

-Media Transcoder in the cloud
-Convert media files from their original source format in to different formats that will play on smarthphones, tablets, PCs, etc
- Provides transcoding presets for popular output formats.
. Pay based on minutes and resolution at which you transcode.

API Gateway
===========

Fully managed service that makes easy for developers to develop, mainain , monitor and secure APIs at any Scale.

Expose HTTPS endpoints to define RESTful API
Gateway -> Lambda, EC2, DynamoDB, etc

Uses API Gateway **domain**, by default.

API **caching**, reduce the number of requests and latency for your endpoints. Cached for the TTL defined in seconds.

API Gateway, you may use *throtle*** to prevent attacks,
API Gateway security: Same origin-policy to prevent Cross-Site Scripting (XSS) attacks

Using CORS (Cross Origin Resource Sharing) is one way to the server at the other end (not the client code in the browser) can relax the same-origin policy.

Example: CORS is set for example to fetch fonts from another server.

- Proxy : **No** response transformation takes place at AG level
- Non-Proxy : Response transformation, Headers, status code is set and returned from AG, uses VTL templates to configure

Kinesis
=======

Allows to process and analyze real-time and streaming data, as it arrives, and respond instantly instead of having to wait unitl all your data is collected before the processing can begin.

- Streams are made of Shards
- Each Shard **ingest** data up to **1MB/sec**, and up to 1000 TPS
- Each Shard **emits** up to **2 MB/sec**
- All Data is stored for **24 hours**, and data can be **Replayed** in the 24 hr window
- Scaling Kinesis streams by adding or removing Shards, this is called **Resharding**


**Stremaing Data** generated continuosly.

Kinesis Data Streams:

Producers -> **Shards** -> Consumers (S3, RedShift for example)

- Stored at least 24 hours up to 7 days

Kinesis Firehose:

Producers -> Optional Lambda -> Consumers Elastic

- Analyze on the fly
- No storage

Kinesis Data Analytics  

- Analyze data inside Streams and Firehose
- SQL and Java


Cognito
=======

Web identity federations like Amazon, Facebook, Google

Cognito User Pools

Cognito Federated Id

TODO


Security
=========================

NACL Network Access Control List - Range of IP addresses (Layer 4)
WAF  Inspection to protect from XSS, SQL Injection, etc... (Layer 7)

CloudHSM (Hardware Security Module)

- Manage you own keys, with High Availability
- FIPS 140-2 Level 3, PKCS#1, Java Crypthography Extensions (JCE), Microsoft CryptoNG (CNG)
- Level 2 is KMS
- Runs in its own VPC
- No AWS APIS
- Keep you keys safe, as they are irretrievable if lost.

- Qorum based authentication M of N capability

Use-cases:

- Offload SSL/TLS processing for your web servers.
- Can work as Certify Authority CA
- Enable Transparent Data Encryption

Serverless
==========

Lambda
------

Lambda priced - Number of Requests and Duration

Lambda scales out (not up) automatically
Lambda functions are independent, 1 event = 1 function
Lambda is serverless
Which Services are serverless? (RDS is not serverless with exception of Aurora)
Lambda functions can trigger other functions.
Architectures can get extremely complicated, AWS X-Ray allows you to debug Lambdas through tracing and instrumentation
Lambda can do things globally, you can use it to backup S3 buckets to

Note: EC2 and ECS can run hyperthreading code


AWS Storage Gateway
-------------------

- Backup enterprise data with hybrid cloud storage service that allows local caching (customer side for low latency access).
- AWS Storage appliance is installed On-Prem and data is transfered by SSL to S3 snapshots.

File Gateway

- File sharing, store and access objects in S3 from file-based applications
- SMB and NFS -> Storage Gateway -> S3

Volume Gateway

- Attaches to application servers via iSCSI
- EBS (Block Storage)

Tape Gateway 

- VTL Virtual Tape Library
- S3, S3 Glacier, S3 Glacier Deep Archive


Cloudwatch
----------

- After you enable billing metric data, you may use Cloudwatch to monitor, alltough the alarms
can be deleted in the future the billing metric data cannot be deleted.


AWS Well Architected Framework
------------------------------
TODO

AWS Organizations & Consolidated Billing 
=========================================

- Always enable MFA on root account
- Always use a strong and complex password for root account
- Paying account should be used for billing purposes only. Do not deploy resources into paying account
- Enable/Disable AWS services using **Service Control Policies (SCP)** either on OU (Organization Unit) or on individual accounts

- AWS Pricing Calculator - Evaluate resources pricing and create forecasts 
- Cost Explorer - Investigate resources costs currently in use


Databases
=========

RDS
--------


RDS Multi-AZ
------------

- 


DynamoDB
--------

- Serverless, fully managed, fault tolerante
- DynamoDB does not use SQL for queries
- Queries only limited to indexed data
- Throttling is when DynamoDB will reject your request, because the amount of resources provisioned are not sufficient
- **Adaptive Capacity** allows to reduce request Throttling by borrowing capacity from partitions that are not using their capacity.

DynamoDB Keys
-------------

- Primary Key 
- Partition Key
- Sort Key

DynamoDB Autoscaling
--------------------
GRAPHS (1)

DynamoDB Capacity
-----------------

Measuring capacity:

- Allocate WCU (Write Capacity Units) and RCU (Read Capacity Units)

Provisioned 

- Pay for amount provisioned, even if unused
- Can use AutoScaling to dynamically change provisioned limits, minimize waste, but not perfect
- Pay per hour

On-Demand

- No limits
- Pay per request
- Do't need to worry about Throttling, scaling and monitoring
- Best suited for random unpredicatable patterns

DynamoDB Streams
----------------

- Feature that emits events when record modification occur on a DynamoDB table
- Events can be of types INSERT, UPDATE, and REMOVE
- Events can carry the content of the row(s) being modified
- Events are guaranteed to be in the same order the modifications took place
- Usecases: Real-Time Dashboards, Data Replication for Fuzzy Searches

- Customizable events - Keys Only, New Image, Old Image, New and Old Image
- Batch processing
- No performance impact on source table
- Integrates with Lambda

GRAPHS (2)


DynamoDB Accelerator (DAX)
--------------------------

- Fully managed in-emory cache for DynamoDB that increase performance (microsecond latency)
- Delivers microsecond performance
- Horizontally and vertically scalable
- Supports Get/Query/Scan + Update/Delete/Put + LSI/GSI Operations
- Cache is hosted in nodes (EC2), you pay per node 

DynamoDB Streams
----------------
TODO

DynamoDB Global Tables
----------------------

- Fully managed solution
- Collection of replica tables in other regions, with replication using **DynamoDB Streams** (must be enabled)
- Users interact with closest region for best performance
- Resolves conflicts between regions with **No Cross-Region Strong Consistency**, uses timestamps to determine the latest data, eventual consistency


DynamoDB Backups
----------------
- Point in Time Recovery 
- last **35 days** automatically backed up, with granularaty to **second**
- Restores to a **new** separate table


AWS Well Architected Framework
-------------------------------

5 Pillars:

- Operational Excellence
- Security
- Reliability
- Performance Efficiency
- Cost Optimization


Other Services
--------------

AWS Systems Manager
-------------------

- Centrally manage hybrid cloud resources at any scale
- Operate any AWS or external resource centrally (instance, container, serverless))
- Uses agent for Windows or Linux
- Multi account, multi region automation


References
----------

Knowledge Center
https://aws.amazon.com/premiumsupport/knowledge-center/