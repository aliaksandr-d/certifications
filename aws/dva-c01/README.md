Links:
* [Free Code Camp Course](https://www.youtube.com/watch?v=RrKRN9zRBWs)
* [How I Passed the AWS Certified Developer Associate Exam](https://www.freecodecamp.org/news/how-i-passed-the-aws-certified-developer-associate-exam/)
* [AWS Developer Learning Path](https://aws.amazon.com/training/path-developing/)

Distribution:
* Deployment
   * CI/CD
   * Elastic beanstalk
   * Build packages
   * Deploy serverless

* Security
   * Cognito
   * KMS
   * IAM

* Development
   * Lambda
   * Scenarios
   * SDK, CLI, API

* Refactoring
   * Optimize
   * Migration to AWS

* Monitoring and troubleshoot
   * CloudWatch
   * CloudTrail
   * Investigate errors

Whitepapers:
* [AWS Well-Architected Framework](https://d1.awsstatic.com/whitepapers/architecture/AWS_Well-Architected_Framework.pdf)
* [Practicing CI and CD on AWS accelerating software delivery for Devops](https://d0.awsstatic.com/whitepapers/DevOps/practicing-continuous-integration-continuous-delivery-on-AWS.pdf)
* [Architecture for the cloud: AWS Best practices](https://d1.awsstatic.com/whitepapers/AWS_Cloud_Best_Practices.pdf)

Elastic Beanstalk:
* Web (ec2 asg elb):
   * Load balanced env (ec2, elb, asg)
   * Single Instance env (ec2, asg (n=1))
* Worker (ec2, asg, sqs) env
* Deployment policies:
   * All at once: 
      * Full redeployment for rollback
      * Cons: downtime
   * Rolling (can't be used for Single-Instance env, requires ELB): 
      * Remove a part of existing servers
      * Create new servers
      * Rollback requires roll update   
   * Rolling with additional batch:
      * Create new few servers
      * Remove old
      * Pros: never reduce capacity
      * Rollback requires roll update 
   * Immutable: 
      * Create new servers+asg inside an Environment (In-Place deploment)
      * Switch ELB to a new ASG 
      * Pros: safest for critical apps
   * Blue/green: 
      * Similar to Immutable but it creates a new EB Environment
      * Switch DNS to a new ELB
      * Cons: slow switch
      * Requires that your DB is out of an Environment

* Configuration files for customization:
   * .ebextensions - hidden folder that contains config files (can define custom image)
   * .config - extension for config files
   * Config files can config:
      * Option settings
      * Linux/Windows server config
      * Custom resources
   * Linux config:
      * Packages in yum
      * Users
      * Groups
      * Files with chmod
      * Commands (execute on ec2)
      * Services
      * Container commands that affects source code
      
* Environment manifest:
   * env.yml
   * Environment name
   * Choose the stack solution
   * ASsociate env links
   * Default config of services
   
* CLI:
   * Install from Github
   * Commands:
      * eb init
      * eb create - create env
      * eb status - env status
      * eb helath - instances health
      * eb events
      * eb logs - view logs
      * eb open - open in browser
      * eb deploy
      * eb config
      * eb terminate
      
* Custom Image: If you need to install a lot of software
   * Take an original ImageId
   * Create a EC2 from image
   * Install custom packages
   * Create a new AMI
   * Update an ENV image
   
* Configure RDS:
   * Inside EB Env:
      * If env is terminated - RDS is terminated
   * Outside EB Env:
      * RDS is remained if EB env is terminated
      
* Notes:
   * Pay only for resources
   * Not recommended for Production
   * Many preconfigured platforms

AWS Cloud9:
* Requires SG update to connect from local PC
* EB CLI is not installed, can be installed from Github
* eb init:
   * select regio
   * select platform
   * use codecommit?
   * enter name
   * default branch for Codecommit
   * setup ssh for instances?
   * config.yml is created
* EB Config:
   * *.ebextensions/001_envvar.config*:
      * *aws:elasticbeanstalk:environment:variables*
   * .ebextensions/002_node_command.config:
      * *aws:elasticbeanstalk:container:nodejs* - container version and npm start
* EB Create:
   * *eb create --single* - for development
      * enter name
      * cname
      * use spot fleet?
   * *eb status* - check status:
      * aws-eb2-elasstibeanstalk-role is created
      * Status: Ready
      * Health: Green
      * Branch
      * Repository
   * *eb logs*
   * *eb events* - datetime, level, description
* Immutable deployment:
   * Update *.ebextensions/deploy.config*:
      * DeploymentPolicy: Immutable
      * HealthCheckSuccessThreshold
      * IgnoreHealthCheck
   * *eb deploy* - update application with a new config
   * *eb abort* - abort deployment
* Blue-green deployment:
   * Uses *Actions/Swap URL* and change *Environment name*
   * *eb clone* to clone an environment with suffix *-clone*
   * *eb deploy study-sync-prod-clone* - deploy a clone environment
   * *eb swap study-sync-prod --destination_name study-sync-prod-clone*
   * *eb terminate study-sync-prod*
* In Docker container:
   * put a Dockerfile in root
   * *eb create --single*
   * *Dockerrun.json* file:
      * AWSEBDockerrunversion
      * Image/Name

ECS (Elastic Container Server):
* *ECS cluster* = EC2's = EC2 container
* Task definition = JSON File (<=10 containers)
   * Wizard in console
   * image = ECR or Docker
   * 1+ essential container in a task
   * Env values or Env valueFrom parameter storage
* *Container Agent* is running on EC2
* New Cluster: 
   * type = Fargate or ECS
   * Number of instances
   * EBS Storage volume
   * OS Amazon Linux 2 or 1
* ECS Instance Role: Create *ecsInstanceRole* role
* ECS Instance Role: Create *ecsTaskExecutionRole* role for *Task Role*:
   * Elastic Container Service
   * *AmazonECSTaskExecutionRolePolicy*
* *Task* - lauches a container, not running when container is complete
* *Service* - number of tasks and scheduling with LB

ECR:
* Docker images for ECS, K8s, Fargate, EC2

Fargate / ECS Fargate:
* Serverless containers
* Empty ECS Cluster, to create choose *Networking only*:
* Charged at least *one minute* and after *by second*, based on duration and consumption
* Fargate Task:
   * memory and vCPU
   * resources for each container
   * SG to per task and service
   * VPC and subnet per task and service
   * IAM Role per task and service
* Fargate vs Lambda:
   * duration: Lambda <15mins
   * memory: fargate <30GB, Lambda <3GB
   * container: fargate own containers
   * integration: manual work for fargate integration
   * price: fargate per 1sec >1min, lambda per 100ms

X-Ray:
* Distribute tracing system
* Debug, analyze and log microservices
* Anatomy of X-Ray:
   * X-Ray Daemon - buffer of requests to API in Queue and upload to X-Ray in batches (UDP, port 2000)
   * X-Ray API - 
   * X-Ray SDK - send JSON segment doscs to Daemon with UDP
      * Inretceptor
      * Client handler
      * HTTP Client
      * Support of instrumenting to SQL Calls
         * Instrucmenting - open and close segments to send logs to X-Ray
* X-Ray Concepts:
    * Service graphs: flowchart visualization: client, frontend, backend
    * Segments: details of work on compute
    * Subsegments: granular timing info, downstream calls
    * Traces: segments generated by a single segments
    * Sampling: record a 1st request each second and 5% of any requests (to reduce costs), not all request is traced
    * Tracing Header: X-Amzn_Trace-id is reqest id to downstream services
    * Filter expressions: helps to narrow down specific paths or users (http.url, http.method, etc)
    * Groups: create based on filter expression
    * Annotations, Metadata: add other info to segments:
       * annotations: up to 50 annotations per trace - indexes
       * metadata: key-value pair not indexed, not for searching
    * Error, Faults:
       * error - client error 400/4xx
       * fault - server fault 500/5**
       * throttling - 429 too many requests
* services: Lambda, elb, sns, api gateway, sqs
* languages: go, nodejs, java. python, ruby, asp.net, php

ACM (amazon cert manager):
* manges ssl/tls certificates
* public (free) or private (400$/month)
* handle subdomains: *.example.com
* use with ELB/ALB, Cloudfront, API Gateway

Route53:
* register and maange domains
* create various record sets
* traffic flow: blue/green deployment, failover
* continous monitor and health check
* resolve vpc's outside of aws
* route traffic to:
  * elb
  * ec2
  * gateway ip
  * cloudfront
  * ip
  * s3 website
  * vpc endpoint
* Alias option - can select aws resource directly
* Routing policies: traffic flow policy, support versioning,
  * Simple routing policy: Default policy, randomly select from a list of IPs
  * Weighted routed policy: several endpoints with ID and weight 
  * Latency based routing: several endpoints chose based on region
  * Failover routing policy: primary and secondary in different locations, chose based on healthches
  * Geolocation routing policy: choose Location (e.g. north america)
  * Geoproximity routing policy: can set exact coordinate and bias
  * Multi-value answer policy: simple routing + healthcheck
* Healthchecks:
  * check every 10s-30s
  * Can create CloudWatch alarm
  * supports non-aws endpoints
* Route53 Resolver (.2 resolver):
  * route dns queries between VPC and user network (hybrid)

AWS CLI & SDK:
* CLI:
   * switch profile with *--profile*
   * change output with *--output* with json, table, text
* SDK:
   * set of lib integrate AWS SDK
   * Languages: C++, Go, JS, .NET, NodeJs, PHP, Python, Ruby
   * Path: *~/.aws/credentials* to configure profiles and keys
 
KMS:
* Create and control, rotate encryption key, stores Master keys
* multi-tenant
* Can be used with CloudTrail
* Integrated with many services
* Hardware security module:
   * multitenant
   * stores keys in memory
   * storing encryptions keys
   * compliense FIPS 140-2 Level 3
* KMS complient FIS 140-2 Level 2
* Master key - encrypt all other keys
* Envelope encryption - encrypt other keys
* CMK (customer master key) includes key ID, creation date, description, key state
* AWS KMS supports symmetric (s3 buket key AES-256) and asymmetric (two keys, ssh keys) CMKs
* CLI Commands:
   * aws kms create-key
   * aws kms encrypt
   * aws kms descrypt
   * aws kms re-encrypt
   * aws kms enable-key-rotation - automatic rotation
* It's possible to use CloudTrail to find requests to KMS from DynamoDB
* API:
   * Encrypt
   * Descrypt

Amazon Cognito - decentralized wuth system:
* Functions:
   * User pools - wuth using OAuth (FB, Google and etc)
      * Sign-up Sign-in, Acount reconvery, Account confirmation
      * Specify primary id, password restriction, user attributes, MFA
      * Integrate with PinPoint for user campaigns
      * Can trigger Lambda after signup
   * Identity Pools
      * Provide temporary aws creds for aws services
      * choose auth provider
      * use SDK to get temp creds
   * Sync - sync data across all devises
      * uses push syncronization for updates
      * key-value
* Web Identity Federation and Identity Provider
   * IdP: use trusted provider to auth to other services: Facebook, Amazon, Google Twitter, Github
   * Types: SAML, OpenID, OIDC

Simple Notification Service (SNS)
* sends messages in text email (only plain text), lambda, sms, push 
* messages in topics are stored redundantly across multi-AZ
* Uses Pub/sub for application integration
* publishers for sns topic: aws sdk, aws cli, cloudwatch, aws services
* subscribers for sns topic: lambda, sqs, email, http/s, sms
* sns topics: group subscriptions, can encrypt with kms, multiple protocols
* sns subscriptions: per topic, 
   * Application as subsriber: amazon device, movile push
   
SQS (simple queue service):
* to decouple systems/processes via messages, like Sidekiq, RabbitMQ
* Queuing systems - not real time, have to pull messages (SQS)
* Streaming systems - realtime, no pull (Kinesis)
* Queuing is for connecting isolated apps. 
* Use case:
   * app publishes message to the queue
   * other app pulls, finds a message and does smth
   * other app completes work and marks msg as processed
   * app see tha messages is not in queue and processed
* to extend message size, it's stored in s3. standart size 1 byte .. 256kb
* Retansion: 4days - default, 60sec .. 40days
* Guarantees AT LEAST Once
* Standart queue - unlimited message rate
* If order is required: FIFO queue support ordered message group, 300 msgs limit
* Visibility Time-out - message is invisible in SQS Queue, message is deleted only after job is processed, 30sec-default, 0sec..12hours
* Short vs Long polling, *Short* - return immediately even if no messages. *Long* polling - waits until message arrives, reduce number of empty receives, with SDK

Kinesis - scalable, durable, realtime streaming processing:
* usecase: stock prices, game analytics
* Data Streams:
   * Producers : ec2,mobile, client
   * shard: 
      * pay per running shard
      * from 24h to 168h
      * data is ordered
   * consumers: redshift, dynamodb, s3, emr, can have multiple consumers
* Firehose delivery stream:
   * data immediately disappears ine data is consumed
   * support convertion of data, compress, secure
   * pay for data that is ingested
* Video streams:
   * ingest video and audio from various devices
   * output video data to ML or video processing (SageMakers, Recognition)
* Data analytics:
   * input/output streams: Data streams or Firehose streams
   * run custom sql 
* KPL - kineses produce library (Java lib)

SSM Parameter store:
* Secure, hierarchical storage
* password, db strings, license codes,
* hierarchies (by using forward slashes, e.g */exam/app/prod*) and track versions
* types: string, stringlist, secure list
* tears - can convert standard parameter to advanced but not revert:
   * standard 10000 params, 4kb, free
   * advances 100k params, 8kb, param policy
* Policies: force update/delete password , enforce, multiple ppolicies
   * timestamp
   * after days
* *aws ssm put-parameter --name "..." --value ... --type String*
* *aws ssm get-prarameters-by-path --path /planet/vulcan*

Secret manager:
* store, rotate db credentials: RDS, Redshift, DocumentDB, Other DBs, Key/value
* For db credentialss ned to enter server address, db name, port
* Enforces encryption at-rest using KMS
* $0.4 per secret per month, 0.05$ per 10000api calls
* support CloudTrail
* Automatic rotation for any DB credentials:
   * rotation interval up to 365days
   * creates Lambda
   * choose to rotate Superuser or developer password
* *aws secretmanager describe-secret --secret-id ...*   - VersionIds
* *aws secretmanager get-secret-value --secret-id ... --version-stage ...* - returns SecretString
* use case:
   * Rotate password, run lambda to change password in RDS

DynamoDB:
* key-value and document database, guarantee consistent reads and writes and any scale
* nosql, key/value, document db
* specify read/writes capacity Units
* 3+ AZs on SSD
* Tables contain rows(items) & columns (attributes), keys (column name), value (cell)
* Read consistency:
   * Eventual consistent read - possible inconsistent copy, reads are fast, become generally consistent wihin a second
   * strongly consistent read - wait for consistent, slower
* Parsitions: 
   * slice if table into smaller chunk in SSD and replicated. 
   * speed up read
   * Create additional partition:
      * every 10GB
      * when exceeds RCUs or WCUs for a single partision, maximum 3000 RCUs and 1000 WCUs
      *  see a split pattern
* Primary key:
   * determends where and how data is stored in partitions
   * PK can't be changed later (called HASH)
   * sort key - determends stor on a partition (called RANGE)
   * Simple PK:
      * Only with a partition key, no sort key
      * DynamoDB internal hash function hash(PK)
   * Composite PK:
      * PK+Sort key should be unique, hash(PK+SK)
      * Sorting is for fater access
   * PK Design:
      * Simple PK:
         * No two items can have same Partition Key
      * Composite PK:
         * Partition+sort should be unique
      * Distinct - unique
      * uniform
* Query and scan:
   * in console you can query and scan
   * query:
      * find items based on PK value
      * Query table or secondary index
      * Reads eventually consistent, or ConsistenRead=True
      * By default returns all attributes, or set attributes in ProjectExpression
      * By default ascending order, or ScanIndexForward=False
   * scan:
      * scan all items, return 1+ items
      * by default return all attributes
      * scan on tables and secondary indexes
      * Much less efficient than query
      * On big tables, scan is longer
      * on large table , you can use all provisioned thrughput
      * avoid scans if possible
* Provisioned capacity:
   * Provisioned throughput capacity - maximum amount of capacity
   * Autoscaling: target utilization (%), minimum provisioned capacity, maximum provisioned capacity
* On-demand capacity:
   * better on new tables with unknow load
   * unpredictable trffic
   * pay only what you use
   * default limit: 40000RCU and 40000WCU per table
   * no hard limit (unlimit on traffic), can be expensive
* Calculating:
   * RCU:
      * one strongly consistency read per second up to 4kb per item
         * 50 reads at 40kb per item for strong: `40/4*50=500RCUs`
         * 10 reads at 6kb per item for strong: `(6->8)/4*10=20 RCUs`
      * two eventually consistens reads per second up to 4kb per item
         * 50reads at 40kb per item: `40/4/2*50=250 RCUs`
         * 11 reads at 9kb per item: `12/4/2*11=17 RCUs`
    * WCU:
      * one write per second, item up to 1KB:
         * 50 writes at 40kb per item: `40*50=2000 WCUs`
* Global tables:
   * multi-region
   * required:
      * use kms cmk
      * enable streams
      * stream type of new and old image
* Transactions:
   * ACID:
     * Atomicity
     * Consistency
     * Isolation
     * Durability
   * No additional cost
   * Two underlying reads: before and after transaction
   * via TransactWriteItmes and TransactGetItems API Calls
* TTL
   * Items expiration (deletion)
   * case: session data, logs
   * define an attribute name (everything is a string in DynamoDB, use Epoch format)
* Streams:
   * when item is changed, changes are sent to Lambda
   * Don't cosume RCUs
* Errors:
   * Throttlingexception:
      * rate of requests exceeds
   * ProvisionThroughputExceedException:
      * exceeds maximum allowed number of Throughput, use Exponential backoffs
* Secondary indexes:
   * LSI - local secondary index (only with initial table) - strong consistency
      * base parition, same as PK
      * can't exceed 10gb
      * shares provisioned throughput
      * up to 5 per table
      * can't add, modify, delete
      * Checkbox "Create as LSI"
      * sort key required
      * can request attributes that not in index
   * GSi - global secondary table - no strong consistency
      * can span all of the data in base table across all partitions
      * no size limits
      * own throughput settions
      * up to 20 per table
      * can add, delete, modify any time
      * PK can be different
      * can only request attributes in index
* Accelerator (DAX)
   * in-memory cache
   * miliseconds
   * good for:
     * fastest possible response time
     * read small number of times
     * repeated reads
   * bad for:
     * strong consistent
     * for write-intensive apps
     * microseconds response
     * apps that using other caching solution
* Cheatsheet:
   * datatypes: binary, number, string
* CLI:
   * `aws dynamodb get-item` - return a set of attributes
   * `aws dynamodb put-item` - cerate or replace item
   * `aws dynamodb update-item` - update or create item
   * `aws dynamodb batch-get-item` - return16Mb of data, 100items
   * `aws dynamodb batch-write-item` - 16MB, 25
   * `aws dynamodb create-table`
   * `aws dynamodb update-table`
   * `aws dynamodb deleta-table`
   * `aws dynamodb transact-get-item`  - return objects from multiple tables
   * `aws dynamodb transact-write-items`
   * `aws dynamodb query` - find based on primary key
   * `aws dynamodb scan` - find item by accssing every item
   
EC2:
* Resizable compute capacity
* Choose:
   * OS: Re
   * Instance type
   * Volume: EBS, EFS
   * Configure: SG, Key Pairs, Userdata
* Instance Types:
   * General purpose: A1, T3, M5, 
   * Compute optimized (more computing): C5, C4
   * Memory optimized: R5, X1
   * Accelerated Optimized (GPU): P3, P2, G3
   * Storage optimized (local large datasets): I3, D2, H1
* EC2 Instances sized generally double in price and key attributes
* Instance profile: Attach a role to an instance (IAM Role)
* Placement groups:
   * Cluster - close inside AZ
   * Partition - logical partitions, in separate hardware
   * Spread - each instance in separate hardware, up to 7 instances, multi-AZ
* Userdata: 
   * script is run when lauching an EC2, install packages
   * Check userdata in http://169.254.169.254/latest/user-data
* Metadata - get ec2 info from API http://169.254.169.254/latest/meta-data/

VPC (virtual private cloud):
* Logically isolated section of AWS Cloud, give compelte control over virtual network env
* Key features:
   * Region specific
   * Up to 5 VPCs per region
   * Every region has default VPC
   * 200subnets per VPC
   * Cost nothing: 
   * Cost some money: NAT Gateway, VPC Endpoints, VPN Gateway, Customer Gateway
   * DNS Hostnames can be disabled in VPC
* Default VPC:
   * size /16 VPC
   * size /20 subnet per AZ
   * InternetGateway to default VPC
   * Default security group
   * Default NACL
   * Default DHCP
* VPC Peering:   
   * Connect VPCs over direct network route using private IPs
   * Diffent accounts and regions
   * Peering uses Star (1 central VPC and secondary VPCs)
   * No overlapping CIRD
* Route tables:
   * Used to determine where network traffic is directed
   * Multiple subnets can be connected to a single route table, 
* Internet gateway:
   * VPC access to internet
   * Perform NAT (netword, address translation)
* Bastions/Jumpbox:
   * bastion host (ec2 in public subnet) is public that has access to private host (ec2 in private subnet)
   * Session Manager replaces Bastions
* Direct Connect:
   * Dedicated network connection from on-premises to AWS
   * Low bandwidth (50MB-500MB) to High (1GB-10GB)

EC2 Auto Scaling Groups:
* ASG contains a colelction of EC2 with automatic scaling and maangement
* Settings:
   * Desired Capacity - ideally run
   * Min - minimum ec2 , ASG always adds EC2 to fit Min
   * Max - maximum ec2
* Healthcheck: 
   * EC2 type: checks EC2
   * ELB type: check HTTP request
* Scaling policies - 
   * Metric at a target value:
      * CPU utilization
      * Network in
      * Network out
   * Simple Scaling when alarm is triggered (legacy)
   * Scaling with steps:
      * Alam is creached
      * Scales based on alarm value
* ELB Integration:
   * CLB is associated with ASG
   * ALB, NLB associated with a Target group
* Launch Configuration:
   * Cannot be edited, clone or create a new one
   * Create Launch configuration same as EC2 creation
   * Launch template - Launch Config with vesioning

VPC Endpoints:
* Privately connect VPC with AWS Services without public IP
* Traffic doesn't leave AWS Network
* horisontally scaledm redundant, HA
* Interface endpoints:
   * ENI with Private IP
   * Powered by AWS PrivateLink
   * paid
* Gateway endpoint:
   * Specific eroute in route table traffic to AWS Service
   * free
   * only support DynamoDB and S3

ELB:   
* Distributes traffic to multi tarets 
* ALB, NLB, CLB
* Rules of Traffic:
   * Listeners - posrts that accept traffic
   * Rules - Listeners invoke rules to decide what to do with traffic, and pass to target group
   * EC2 instances are registered in Target group
* ALB and NLB:
   * Check Listener
   * check ports
   * check rules: what to do, e.g. redirect ALB
   * sends traffic to target group
   * Target group distribute traffic to E2s
* ALB:
   * Layer 7 (HTTP/HTTPS)
   * Request Routing
   * WAF
   * For web app
* NLB:
   * Layer 4 (TCP/UDP)
   * Millions req/sec
   * Cross-zone LB
* CLB:
   * Check listeners
   * send to registered targets
   * HTTP/TCP
   * Sticky sessions
   * will respond 504 if underlying app not respond (web app or db)
   * not recommended
   * at least 2 AZ, it's not cross-region
* Sticky sessions:
   * bind user session to same ec2
   * only CLB or ALB (to target group)
* Header `X-Forwarded-For` contains client IP 
* SSL certificate from Amazon Cert Manager
* Healthchecks:
   * ALB and NLB have Healthcheck on target group
   * OutOfService -  unhealthy instance, traffic are not routet
* Cross-zone load balancing (CLB and NLB)
   * requests distributed accross all instances in differented AZ
* Request routing: 
   * Forward or redirect traffic based on: Host header, source ip, path, http header, http header method, query string

Security Groups:
* virtual Firewall at the instance level
* contains inbound and outbound
* contains EC2 instances
* All traffic is blocked by default (no 'deny' rules)
* Instances from multiple Subnets can be assigned to SG
* Immediate chang effect
* Can't block specific IP addtess (use NACL)
* Stateful
* Use cases:
   * Source inbound rule can be IP range or IP/32
   * Source can be another SG
   * Instance ca belong to multiple SGs, groups are permissive (add allows)
* Limits
   * upto 10000SG in a Region (default 2500)
   * 60 Inbound and 60 outbound per sg
   * 16 SG per ENI (default 5)

NACL (network access control list)
* Virtual firewall at Subnet level
* Allow and deny inbound/outbound traffic
* Rules are ordered with a number (0..32766) from lowest to highest
* Can block specific IP
* Default NACL allow all Inbound/outbound
* SG is assigned to a single NACL
* Stateless (inbound rul doesn't allow outbound)
* use case:
   * Block specific IP
   * Deny port SSH

IAM (Identity Access management):
* Core components:
  * user - log into console or programmatic access
  * group - group of users
    * Group of users with same permissions
  * roles - associate permissions to roles
    * multiple policies are attached to role
* Policies - and Role can be assigned to group of users
  * Types of policies:
    * managed - can't edit, managed by aws
    * customer managed - created by customer
    * inline policy - atatched directly to user/resource, only to a single object
  * Structure:
    * Version
    * statement - container for policy elements
      * Sid - (optional) label of statement
      * effect - allow / deny
      * principal - account, user, role, federated user
      * action - list of actions
      * resource - resource to which action applies
      * condidtion - (optional)
  * Password policy - rotate and rules
* Access keys:
  * for users with programmatic access
  * up to 2
* MFA: 
  * turned on per user
  * admin cannot enforse MFA
  * admin can restrict access without MFA
* Temporary credentials:
  * identity federation
  * delegation
  * cross - account access
  * IAM Role
* Identity Federation:
  * users can exists on a different platform (e.g. Google, FB)
  * Two types:
     * Enterprise
        * SAML
        * Custom federation broker
     * Web identity fedetaion
        * Amazon
        * FB
        * Google
        * openID Conenct 2.0 (OICD) - on top of OAuth 2.0
* STS (Security Token Service)
   * *Temporary limited priviledge credentials* for IAM users or for federated users
   * global service
   * STS returns:
      * AccessKeyID
      * SecretAccessKey
      * SessionToken
      * Expiration
   * API
      * AssumeRole
      * AssumeRoleWithSAML
      * AssumeRoleWithWebIdentity
         * It's required to identify in FB, get JWT
         * Call  AWS CLI - ASsumeRoleWebIdentity
         * Get Temporary credentials
         * Use temp creds
   * Cross acccount roles - to grant access for users from different account to resources in your account
      * Policy: give access to action=sts:AssumeRole to resource in dofferent acc

CloudFront:
* CDN is a distributed network of server which delivers content to end users based on geo location
* distribute static, dynamic and streaming
* Core component:
   * Origin - where files are lcoated: S3, EC2, ELB, Route53
   * Edge location - where content will be cached (different than regio or az (readonly)
   * Distribution - colelction of Edge Location:
      * Behavious (redirect to HTTPs, redirect to HTTP, set ttl)
      * Incalidations - manually invalidate cache on specific files via invalidations:
         * files replacement in s3 doesn't change file in CLoudfront
         * to invalidate :
            * Create invalidation
            * Past file paths (with wildcard)
            * Takes time
      * Error Pages - custom error pages
      * Restrictions - Blacklist or whitelist of countries
      * Two types of distributions: Web, RTMP
* Lambda@Edge - pass request and response to lambda and can process:
   * Viewer request
   * origin request
   * origin response
   * Viewer response
* CF Protection:
   * Restrict Viewer Access (used Signed URL or Cookies)
   * Original identity Access to create Signed URL ot Signed Cookies (not sames as s3)
      * used to acess private s3 bucket
   
Cloudtrail - Logs API calls to AWS, enables governance, compliance, operation auditing, risk auditing
* Where (IP), when(EventTime),  who (User, useragent), what(region, resource, action)
* Default last 90days
* Of >90days , create Custom Trail to output to S3
* To analyze trail, use Athena
* Options:
   * Log to all regions
   * Across all accounts in an Org
   * Encrypt logs with KMS
   * Log file validation
* CloudTrail can deliver events to CloudWatch logs
* events:   
   * Management event:
      * configure security
      * register devices
   * Data events: turned off by default
      * S3
      * Lambda
* Trail:
   * Choose events: data, management
   * Choose storage: s3 bucket, path prefix
   * KMS
   * Validation

CloudFormation:
* Infrastructure as a Code - manage aws based on defintion files
* Template formats: JSON and YAML
* Tempalte anatomy:
   * MetaData - details about template
   * Description - what tempalte should do
   * Parameters - pass to teplate at runtime
   * Mappings - lookup table - map key to values
   * Coditions - wether resources are created
   * Transform - applies macros to change the anatomy to be custom
   * Resource - resources to create
   * Outputs - result/values that are returned
* CF template require at least one resource
* Stack Update:
   * CF can intelligently change or recreate resources
   * Ways:   
      * Direct updates - update-stack
      * Change set - can preview changes to CF, good to check changes
   * Stack updates:
      * Update with no interruption
         * ID not changes
         * without disrupting
      * Update with some interruption:
         * some interruption
         * same ID
      * Replacement:
         * recreates
         * regenerates ID
   * Prevent update:
      * Stetement: Deny -> Update:Reokace
* NestedStack (define TempalteURL)
   * create modular templates (reusability)
   * assemble large templates (reduce complexity)
* Drift Detection:
   * CF detects that resources deleed or modified
   * CF doesn't detect drift in nested stacks
   * When actual configuration differs in aws
* Rollbacks:
   * When create/update/delete:
      * template has syntax error
      * delete respurce that doesn't exists
      * *--ignore-rollback*
      * Rollback can fail
* Pseudo parameters - predefined by AWS CF, you don't need to declare them:
   * AWS:: Parition
   * AWS::Region
   * AWS::StackId
* Resource attributes:
   * CreationPolicy - not success until number of positive signals
   * DeletionPolicy - reserve or backup resource when stack is deelted
   * UpdatePolicy - Handle an updaye for asg, ElastiCache Lambda alias
   * UpdateReplcaePolicy - backup the existing physical instancewhen it's replaces during update operation
   * DependsOn - Resource is created only after resource in DependsOn
* Intrinsic Functions - get values that are not available until runtime
   * Fn::Base64
   * Fn::Cidr
   * Fn::FindInMap
   * Fn::And Fn::Equals Fn::if
   * Fn::Transform
   * Fn::GetAZs
   * Fn:ImportValue - return a value from other stack
   * Fn:Join - join a list of strings
   * Fn::Select - get from list by index
   * Fn:Split
   * Fn::GetAttr
* Ref - returns parameter of a resource
* Fn:GetAttr - returns an attribute from a resource
* CLI:
   * *aws cloudformation create-stack* - create a stack
   * *aws cloudformation update-stack* - update a stack
* Wait condition, use cases:
   * coordinate stack resources creation
   * track the status of a configuration process
* Large templates (>0.05MB) must be uploaded to s3

Cloud Development Kit:
* write IaC using imperative paradigm
* transpiler - one source code into another: imperative to Declarative
* Imperative:
   * More flexible, less certain, write less
* Declarative:
   * less flxible, very certail, write more
* Languages: nodejs, typescript (support most apis), python, java, asp.net

Serverless Application Model (SAM):
* extension for AWS CLI and CloudFormation Macro
* SAM CLI run/package/deploy Serverless apps or Lambdas
  * sam build
  * sam deploy - upload package to artifactory and deploy
  * sam init - create sample project
  * sam local generate-event
  * sam local invoke - single lambda call locally
  * sam local start-api
  * sam local start-lambda - tun a lambda locally (easier)
  * sam logs - logs for Lambda
  * sam package - creates a zip and copy to s3
  * sam publish - publish SAM app to AWS serverless app repo
  * sam validate - validate an aws sam template
* CI/CD pipeline: Code, Build, Integrate, Test Release, Deploy
  * CI = Code-Test - integrate code changes in a single project:
     * each commit triggers a pipeline : CodeCommit, Lambda, CodeBuild, Artifact
  * Continous Delivery = Code-Release - prepare a code for release:
     * CI + Artifact + Lambda + Create PR + manual merge to master
  * Continous Deployment = Code-Deploy - automaticaly deploys changes to prod:
     * CD + CodeDeploy (deploy from master to Prod servers)

CodeCommit:
* Host Git-based repositories
* VCS / Source Control
* Can push/pull code
* Same like Github, Bitbucket, Gitlab
* Key Features:
   * Compliance programs, e.g. HIPAA
   * encrypted
   * Large number of files and ..
   * No limit
   * Repo is closed to AWS Cloud
   * Use IAM to control users

Docker:
* compare to EC2
* dockerfile:
   * FROM - base docekr image
   * RUN - execute any command
   * WORKINGDIR - default folder
   * COPY - copy files from local pc
   * ENTRYPOINT - can't be 
   * EXPOSE - entrypoint command, can't be changed
   * CMD - pass parameters to entrypoint
* CLI:
   * docker build
   * docker ps
   * docker images
   * docker run - run a command in a new contaner
   * docker push 
   * docker pull - pull from repo

CodeBuild - compiles code, run unit tests, produces artifacts
* Support Maven, Gradle
* Customize build env
* Workflow:
   * Trigger codebuild: Console, cli, sdk, CodePipeline
   * Setup build env: Managed images or Custom image (docker image):
      * Managed: Ubuntu, Amazon Linux,
      * Custom image in ECR
   * Source code in buildspec.yml: Github, Bitbucker, AWS CodeCommit, S3
* Cases:
   * Trigger in AWS Console, CodeCommit, CodeBuild, Render static files to S3
   * Push to Github, Webhook, CodeBuild
* Buildspe.yml:
   * Build instructions in root of proejct
   * Structure:
      * version: 0.2 - all build commands in a same instance, 0.1 - each command in separate instance
      * phases: commands in each build phase
         * install - install packages in build env
         * pre_build
         * build
         * Post_build
      * artifact: build output
      
CodeDeploy - deploypipeline to deploy on prod, stagng env
* can deploy ec2, on-prem, lambda, ecs
* update lambda version
+ avoid downtime
* integrateds with CI/CD tools: jenkins, CodePipeline
* integrates with config management: puppet, chef, ansible
* Core components:
   * Application - unique id for app being deployed
   * deployment group - a set of ec2/lambda
   * deployment - process to apply a new app revision
   * deployment configuration - deployment rules
   * AppSpec file - deployment actions that CodeDeploy exec for deployment
   * Revision - Everything for deploy new version AppSpec, app files, config files, executables
* Deployments:
   * In-place deployments - updates deployment group with a latest app revisions
      * only EC2/on-prem, support, not lambda
   * Blue/green - creates new ec2 isntances, deploy, switch to new ASG, remove old EC2s
* appspec.yml:
   * version: 0.0
   * os: linux
   * files: { source, destination }
   * permissions: 
   * lifecycle hooks - different depending on ec2/lambda and in-place/blue-green deployment:
      * ApplciationStop - how to stop ap
      * BeforeInstall
      * AfterInstall
      * ApplicationStart
* CodeDeploy agent and ServiceRole:
   * not installed on Amazon linux
   * binary that checked CodeDeploy
   * ServiceRole AWSCodeDeployRole - create EC2/ELB and ect
   
CodePipeline - managed ci/cd pipeline:
* Anatomy:
   * Pipeline
   * Stage - e.g. source/build/deploy, has a group of actions
   * Action group = group several acrtions
   * Action - do smth, group code, build , lambda
   * Artifact - zip retuned by action and stored in s3
   * Stage transitions - links stages
* Action:
   * Custom actions: 
      * Source , 
      * Test (AWS Code build, Jenkins)
      * Deploy: Cloudformation, Codedeploy, s3
      * Invoke: lambda
* Example:
   * Source: Pull from Github
   * Stage transit,
   * Deploy: CodeDeploy
* Use Cases:
   * Monolith application: Github, CodeBuild, CodeDeploy
   * Satic S3 website: Bitbucket, CodeBuild, Lambda
   * Serverless function: Codecommit, CloudFormation (deploy)
   
CodeStar:
* Deployment pipeline, access management, project dashboard
* how to
   * Choose tempalte
   * Creaye a mew git
   * CodePipeline

RDS:
* Relational db service, many engines
* Aurora, MySQL, MariaDB, oracle, MSSQL, Postgres
* Encryption:
   * Old versions can non-support
   * KMS
* Backups:
   * Automated:
      * Retantion perion 1-35days
      * In s3
      * Backup window
      * Storage i/o may be suspended
   * Manual - Database snaphot
      * Manually 
      * backup persists even if rds is deleted
* Restoring backup:
   * Restore in point-in-time
   * never restored overtop an existance instance (creates new instance)
* Multi-AZ - standby copy:
   * Syncs from master to standby db
   * Automatic failover protection if master goes down
* Read replicas - multiple copies that allow only read:
   * Multiple copies of DB - only read
   * async 
   * <=5 replicas
   * Multi-az replicas, cross-region replicas
   * mast have auto backup
* Multi-az vs read replicas:
   * Sync - async
   * only master is availabl - all available
   * auto backup - no backup
   * two az within region - az, cross-az, cross regio
   * db update
   * auto failover - manual failover
* Templates for running: prod, dev, free tier
* Performance insight - rich analytics dashboard
   * view performance per query
   * rich analytics
* Reserved instances:
   * pay upfront 1-3years
* Snapshots:
   * Restore snapshot
      * Create a new instance
      * to change a size of instance
      * change multi-az
   * Migrate snapshot - migrate to a different engine (e.g. to aurora), engine version
   * Copy - move snapshot to other region

Aurora Serverless:
* Cheeper, for development
* Choose capacity: choose capasity units
* Data API - SQL HTTP API
* Can't turn off Backup, choose retention period
* Support force scaling
* Cloud9 supports 

S3:
* Object-based storage
  * not file system (files and hyerarchy)
  * not block storage (sector and tracks)
* Unlimited storage
* Object:
   * key
   * value - 0bytes .. 5TB
   * version id
   * meradata
* bucket:
   * hold object
   * unique name within all AWS
* Storage classes:
   * Standard: 99.99% availability, 11 9s durability, across 3 AZs
   * Intelligent tiering - use ML to choose appropriate class
   * Standard IA - access files less once month
   * One zone IA - retrival fee, data could be destroyed
   * Glacier - long term, retrieve takes minutes to hours
   * Glacier deep archive - 12 hours to retrieve
* Storage class compare:
   * 11 9s durability
   * Availability 99.9% but 99.5% in one AZ
   * only One-zone has 1 AZ
   * min capacity  change
   * min duration charge: 128kb for Standard IA and one-zone IA, 40kb for Glacier
   * retrieval fee: paid per GB for Standard IA, One-zone IA, Glacier
* S3 Securiy:
   * Private by default
   * Bucket policies - with json, flexible:
      * Allow/deny
      * Principal: path
      * Action: PutObject, PutObjectAcl
      * S3 bucket ARN
   * Access COntrol Lists - (legacy feature), control access to bucket and objects
* S3 Encryption:
   * When upload SSL/TLS 
   * Server side encryption:
      * SSE-AES - encryption algorithm
      * SSE-KMS - using KMS to encrypt
      * SSE-C - customer provided key
      * Client side encryption - before upload
   * After enable, existing files are not encrypted
   * Files are accesible, they are encrypted on server side
* Data consistency:
   * Puts - new object, Read after Write consistency - when object is created, it's available
   * Overwrite or delete - takes time to replicate
* Cross region replication - replicate to another region:
   * Higher durability,
   * Support other account
   * Copy from a bucket to another bucket, destination bucket needs to be created before
   * *Versioning* is required
   * Storage class be changed in replication
* Versioning:
   * Store all version of an s3 object
   * Cannot be disabled, only suspended
   * MFA Delete feature
   * Track object changes
   * To enable: properties - versioning. 
   * Not able to *Turn off* but *Suspend*
   * *Version ID = null* for files that uploaded when *Versioning is off*.
   * New upload doesn't inherit properties of previous versions (e.g. Public access)
   * Protect from deletion of files
   * When object is deleted, previos version is restored
* Lifecycle:
   * Can be used with vesioning
   * Change storage class depending on conditions
   * Delete on schedule
   * Automate a process of changing storage class:
      * *Add lifecycle rule*
      * Storage class transition: current version/previos
      * Minimum 30 days after creation
      * Expiration - delete version after N days
* Transfer acceleration:
   * CloudFront uses Edge Locations
   * Users upload data to Edge location
* Presigned url:
   * aws s3 presign s3://... --expires-in 300
* MFA Delete:
   * users cannot delete without MFA Code
   * Versioned must be turned on
   * aws s3api pre-bucket-versioning 
   * only bucket owner logged in as Root can delete 
   * not delete object by acident
* Public:
   * *Make public* is disabled by default
   * Permissions - untick *Block public access*
* S3 CLI:
   * aws s3 ls
   * aws s3 ls s3://...
   * aws s3 cp
   * aws s3 presign s3://... --expires-in
   
ElastiCache:
* Cache - trmporary storage area, non drable
* In-Memory Data Store - very fast, risk of loss
* Only within same VPC
* ElastiCache support:
   * Memached
      * key/value store
      * very fast and simple
      * preferable for HTML
   * Redis:
      * different operations on data
      * leaderboards, notifications
      * not fast as memcache
      * Supprots Snapshots, transactions

Lambda:
* Run code wihout provision or managing servers, serverless
* Scales automaticaly
* Pay for compute time and invocation
* Supports: Ruby, Python, Java, Go, Powershell, NodeJs, C#
* Support custom runtime
* Use case: Processing Thumbnails, Contact email form
* Triggers:
   * AWS SDK
   * AWS Services: Gateway, Alex, CloudFront, CloudWatch, Kinesis, S3, DynamoDB and etc
   * External partners
* Pricing: 1milion requests are free per month, then $0.2 per 1M requests
* Interface:
   * Choose runtime
   * Upload code: inline, zip files, choose in s3
   * Choose trigger
   * Grant permission for outputs via IAM Role
* Defaults and limits:
   * 1000 concurrently runs (but can be increased per request)
   * /tmp up to 500MB
   * No VPC by default, but can cahnge VPC
   * Timeout maximum 15Min, *or use Fargate*
   * memory 128MB..3008MB at increment 64MB (more memory - more expensive)
* Cold Starts:
   * Delay to copy code
* Versioning:
   * To deploy a version beta not for prod
   * To revert version
   * *Publish new version*
   * After a publish each version has its own unique ARN
   * Two initial versions:
      * Quilified ARN: $LATEST or any version 
      * Unqualified ARN - alwayspoint latest version, cannot create Aliases
* Aliases:
   * Friendlier name to a version
   * *Create Alias*
* Layers:
   * Pull in additional code and content
   * Zip that contains libraries, custom runtime, dependencies
   * Modular
   * Up to 5 layes
   * <250MB
   * e.g. python library
   
API Gateway:
* Create secure APIs at any scale
* Front door for applications
* Pass request to API Gateway cache, CloudWatch, AWS Services
* Key Features:
   * All tasks involving, up to 100.000 of concurrent api calls
   * prevent attacks
   * HTTPS
   * Highly scalable
   * Send each API to a different target
   * Multiple versions of API
* Configuration:
   * Resources: URL, can have child resources (e.g. /project/-id-/edit)
   * Methods on Resources, can have Multiple: Get, Post, Delete and etc.
   * Stages - versions of API: prod, qa and ect
   * Invoke URL - URL for each stage, custom domain
   * Deploy API - after each change, changes must be deployed *Deploy API*
   * Integration: HTTP, Lambda and etc
* Cache:
   * Caches responses for TTL
   * Looking for responses in cache
   * reduce load
* CORS:
   * CORS request is made to:
      * different domain
      * different subdomain
      * different port
      * different protocol
   * For simple requests (non-CORS), response needs to include *Access-Control-Allow-Origin*
   * Preflight request for CORS:
      * *Origin* header
      * method *OPTION*
      * includes *Access-Control-Request-Method* , *Access-Control-Request-Headers*
   * API must enable:   
      * Access-Control-Allow-Methods
      * Access-Control-Allow-headers
      * Access-Control-Allow-Origin
      * Support *OPTION* method
   * By default not enabled
   * CORS is always enforsed by a client
* Same Origin Policy (XSS):
   * Execute script from another website on your site
* Can *require auth* with AWS Cognito or custom lambda
* Throttle API:
   * Rate - Steady-state rate, requests per second
   * Burst - maximum bucket size of requests
   * When requests rate > rate and burst limit, API Gateway fails *429 Too many Requests*
* Cache:
   * it is enabled per Stage
   * only GET is cached
   * CloudWatch metrics: *CacheHitCount* and *CacheMissCount*

Step functions:
* multiple aws services into pipeline
* State machine - sequence of states, decide how state goes a different, Flow-chart:
   * *Standard* - durable, etl jobs, long-duration jobs
   * *Express* - short duration high-event-rate workflows
* Step functions - serverless workflow, trigger each step, retries on failure and etc
* Steps can run in parallel
* Steps are defined in JSON using *Amazon States Language*: Comment, Version, States, Resource, parameters, 
* use cases:
   * Manage a batch job or fargate container - Submit a job to AWS Batch, if fail - notify in SNS
   * Transfer Data Records:
      * Load up DynamoDB table
      * Add each item to a queue
      * When item is processed - remove from queue
      * Send a report
* States:
   * Pass state - input to it's output without performing work, useful for debugging, does nothing
      * Parameters, Result, Resultpath
   * Task State - single unit of work performed by a state machine
      * performs work by Lambda
      * AWS service - Lambda, AWS Batch, SNS, SQS and etc
      * Actions - doing work outside of AWS: EC2, ECS, mobile phone
         * Waits for an activity worker poll for a task
   * Choise state - add branching logic to a state machine
   * Wait state - delay for a specified time (for period and till datetime)
   * Succeed state - stops an execution - good for branching
   * Fail state - mark a failure of a state machine: Cause, Error
   * Parallel states - to run states in parallel: Branches
   * map state - run a set of steps for each element of an input array
