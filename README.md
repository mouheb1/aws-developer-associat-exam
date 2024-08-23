# AWS Developer Associate Exam Preparation

## VPC Networking

### NAT Gateway vs NAT Instance
- **NAT Gateway**:
  - Managed by AWS, offering better scalability, availability, and performance compared to NAT Instances.
  - Highly available within a single Availability Zone; can be made regionally redundant by deploying in multiple AZs.
  - Automatically assigned a public IP address and can handle large amounts of traffic.
  - Requires minimal management effort as AWS handles scaling and maintenance.
  - **Use Case**: Allow instances in a private subnet to access the internet while preventing inbound internet traffic.

- **NAT Instance**:
  - Requires manual management, including scaling, updates, and high availability configuration.
  - Can be a cost-effective solution for smaller-scale environments but requires more hands-on maintenance.
  - Must be configured with security groups and source/destination checks disabled.
  - **Use Case**: Customizable, can be used in scenarios requiring specific configurations or where cost savings are prioritized over managed solutions.

### Security Groups vs Network ACLs

- **Security Groups**:
  - Operate at the instance level (virtual firewall).
  - **Stateful**: Return traffic for an outbound request is automatically allowed, even if there is no explicit inbound rule.
  - Support allow rules only; any traffic not explicitly allowed is denied by default.
  - **Use Case**: Fine-grained control over individual instance traffic, ideal for managing access to specific resources within a VPC.

- **Network ACLs (NACLs)**:
  - Operate at the subnet level.
  - **Stateless**: Return traffic must be explicitly allowed by rules; outbound rules must allow return traffic for inbound requests.
  - Support both allow and deny rules, providing a higher level of control at the network boundary.
  - **Use Case**: Broad control over traffic entering and leaving subnets, suitable for baseline security at the subnet level.

### Additional Notes
- **VPC Peering**: Allows private communication between VPCs across accounts and regions without traversing the public internet. It’s non-transitive, meaning if VPC A is peered with VPC B, and VPC B is peered with VPC C, A cannot communicate with C.
- **VPC Endpoints**: Allow you to privately connect your VPC to supported AWS services and VPC endpoint services powered by AWS PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect.
- **VPC Flow Logs**: Capture detailed information about the IP traffic going to and from network interfaces in your VPC, useful for security analysis and troubleshooting.

## API Gateway

### Usage Plans
- **Purpose**: Define who can access one or more deployed API stages and methods and set throttling and quota limits for each user or group of users.
- **Components**:
  - **API Stages**: A usage plan can be associated with one or more API stages.
  - **Throttling**: Controls the rate limit of requests, specifying the maximum number of requests per second.
  - **Quota**: Sets a limit on the total number of requests that a user can make within a specified time period (e.g., day, week, or month).
  - **API Keys**: Users are required to use an API key, which is associated with a usage plan, to access the APIs.

- **Use Case**: Commonly used to manage and monetize API access, ensuring fair usage among users, preventing abuse, and implementing tiered access levels for different user groups.

### Monitoring
- **IntegrationLatency**: Refers specifically to the time that elapses between API Gateway forwarding a request to the backend integration (e.g., Lambda, HTTP endpoint) and receiving a response from that integration.
- **Latency**: Refers to the total time that elapses from when API Gateway receives a request to when it returns a response to the client.

### Integration Types
- **AWS**: Integrate with serverless functions, accessing data from AWS services.
- **AWS_PROXY**: Build flexible and complex APIs with Lambda.
- **HTTP**: Integrate with third-party services or on-premises systems.
- **HTTP_PROXY**: Integrate with external APIs that require full request pass-through.
- **MOCK**: Test API clients, build mock APIs.

### Access Control
- **AWS_IAM**: Secure, controlled access using IAM policies.
- **NONE**: Open access, no authentication.
- **COGNITO_USER_POOLS**: Managed user authentication via Amazon Cognito.
- **CUSTOM**: Flexible, Lambda-based custom authorization logic.

### Notes
- APIs created with Amazon API Gateway expose HTTPS endpoints only (does not support HTTP).
- API keys alone do not grant access to execute an API. They need to be associated with a usage plan that determines which API stages and methods the API key can access.

## Encryption

### AWS KMS vs. AWS STS

#### AWS KMS decrypt
- **Purpose**: Decrypts data that has been encrypted using AWS Key Management Service (KMS).
- **Use Case**: Used to securely decrypt sensitive data, such as database credentials or encrypted files, that were previously encrypted with a KMS key.

#### AWS STS decode-authorization-message
- **Purpose**: Decodes authorization failure messages returned by AWS services when an action is denied due to insufficient IAM permissions.
- **Use Case**: Used to understand and troubleshoot IAM permission errors by decoding encoded messages that explain why a specific action was unauthorized.

### S3 Encryption
- **Default Encryption**: Use AWS KMS Keys (SSE-KMS).
- **Permissions**: Uploading a large file to S3 requires `kms:Decrypt` and `kms:GenerateDataKey`.

### Encryption Algorithms
- **SSE-KMS**: `x-amz-server-side-encryption`.
- **SSE-C**: 
  - `x-amz-server-side-encryption-customer-algorithm`: This header specifies the encryption algorithm. The header value must be **AES256**
  - `x-amz-server-side-encryption-customer-key`: This header provides the 256-bit, base64-encoded encryption key for Amazon S3 to use to encrypt or decrypt your data.

  - `x-amz-server-side-encryption-customer-key-MD5`: This header provides the base64-encoded 128-bit MD5 digest of the encryption key according to RFC 1321. Amazon S3 uses this header for a message integrity check to ensure the encryption key was transmitted without error.


## Compute and Development

### EC2 / On-Premises
- **Permissions**:
  - **EC2**: Create an IAM role with the appropriate permissions.
  - **On-Premises**: Create the credentials file at `~/.aws/credentials`.

### Cloud9
- **Definition**: A cloud-based IDE for developing and coding applications within AWS infrastructure.

## Infrastructure as Code (IaC)

### AWS CloudFormation

#### Functions
- **Fn::FindInMap**: Retrieve values from a defined map.
- **Fn::ImportValue**: Access values from other stacks.
- **Fn::GetAtt**: Get specific attributes of resources.
- **Ref**: Reference the logical ID of a resource.

#### Template Components
- **Transform**: Declares the use of AWS SAM or another macro.
  ```
  Transform: 'AWS::Serverless-2016-10-31'
  ```

- **Mappings**: Define static key-value pairs for reuse within the template.
  ```
  Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-0ff8a91507f77f867
    us-west-1:
      AMI: ami-0bdb828fd58c52235
  ```

- **Parameters**: Allow dynamic input to customize stack deployment.
  ```
  Parameters:
  InstanceType:
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - t2.small
    Description: EC2 instance type
  ```
    
- **Format Version**: Specifies the CloudFormation template version.
  ```
  AWSTemplateFormatVersion: '2010-09-09'
  ```

#### StackSets
- Extends stack functionality across multiple accounts and regions with a single operation.

#### Deployment
- **Packaging**: Artifacts must be packaged and uploaded to S3 before deployment.

#### Python Helper Scripts
- **cfn-init**: Retrieve and interpret resource metadata, install packages, create files, and start services.
- **cfn-signal**: Signal the creation of resources.
- **cfn-get-metadata**: Retrieve metadata for a resource.
- **cfn-hup**: Check for updates to metadata and execute custom hooks.

#### Notes
- CloudFormation is for building and managing your AWS infrastructure.
- **CDK Synth**: A command-line tool that converts your AWS CDK code into a CloudFormation template.
- **AWS Systems Manager Parameter Store**: Store sensitive data like database passwords or API keys in the Parameter Store, using the **SecureString** type with encryption enabled.

### AWS SAM
- **Definition**: Uses AWS CloudFormation as the underlying deployment mechanism.
- **Deploy**: Zips your code artifacts, uploads them to S3, and produces a packaged AWS SAM template file for deployment.

#### Sam init
- Initializes a serverless application with an AWS SAM template.
- **Functionality**:
  - Provides a folder structure for your Lambda functions.
  - Connects to event sources such as APIs, S3 buckets, or DynamoDB tables.
  - Includes everything you need to get started and to eventually extend it into a production-scale application.

#### Sam build
- Builds any dependencies that your application has.
- **Functionality**:
  - Copies your application source code to folders under `.aws-sam/build`.
  - Prepares the application to be zipped and uploaded to Lambda.

#### Sam deploy
- Performs the functionality of `sam package` and more.
- **Functionality**:
  - Directly packages and deploys your application.
  - 
#### Sam local invoke
- Works with **CloudFormation template** ( use **cdk synth** to convert CDK constructs )  

## Step Functions

#### State Definition

- **Task**: Represents a single unit of work, such as invoking an AWS Lambda function or calling another AWS service.

- **Pass**: Passes its input to its output or injects some fixed data without performing any work. Useful for testing and debugging.

- **Choice**: Adds conditional branching to your workflow. Based on the input, the workflow can proceed down different paths.

- **Wait**: Delays execution for a specific time or until a certain time is reached.

- **Parallel**: Enables the execution of multiple branches of a workflow simultaneously.

- **Map**: Iterates over a collection of items and applies the same processing steps to each item.

- **Succeed**: Terminates the state machine execution successfully.

- **Fail**: Terminates the state machine execution and marks it as a failure.

- **Retry and Catch**: Handle error conditions by retrying failed operations or transitioning to a fallback state.
  
### Task States
- **waitForTaskToken**: Pause a task until an external system signals completion.
- **SendTaskSuccess**: Mark a task as successfully completed.
- **SendTaskFailure**: Mark a task as failed.

### Notes
- **Catch** and **Retry** fields are designed for error handling within Step Functions state machines. They centralize error handling logic, making it easier to manage.

### Permissions Management

#### IAM Role vs. IAM User

- **IAM User**: Represents a specific individual or service with long-term credentials (passwords, access keys). Typically used for consistent, long-term access to AWS resources.

- **IAM Role**: Provides temporary access to AWS resources by being assumed by users, services, or applications. Roles are ideal for secure, temporary access and cross-account permissions without long-term credentials.

### AssumeRole
- **Purpose**: Provides temporary security credentials by assuming a role with different permissions, commonly used for cross-account access or delegation.
- **Use Cases**:
  - **Cross-Account Access**: Access resources in another account.
  - **Delegation**: Temporarily assume a different set of permissions.
- **Features**:
  - Temporary credentials valid for 15 minutes to 12 hours.
  - Supports session policies to restrict permissions further.

### GetSessionToken
- **Purpose**: Generates temporary credentials for an IAM user or root account without changing permissions, often used with MFA.
- **Use Cases**:
  - **MFA-Enabled Users**: Generate credentials after MFA authentication.
  - **Security**: Short-lived credentials for existing permissions.
- **Features**:
  - Credentials valid for 15 minutes to 36 hours.
  - Permissions are the same as the IAM user or root account.

### Key Differences
- **AssumeRole**: Grants different permissions by assuming a role; typically used for cross-account access.
- **GetSessionToken**: Retains existing permissions; often used with MFA for enhanced security.

### API Gateway

- **API Keys**: Identify trusted calling parties.
- **AWS_IAM**: Grant permission to the specified IAM role to invoke the `execute-api:Invoke` action.
- **Lambda Authorizers**: 
  - **Token-Based Lambda Authorizer (TOKEN)**: Receives the caller’s identity in a bearer token, such as a JSON Web Token (JWT) or an OAuth token.
  - **Request Parameter-Based Lambda Authorizer (REQUEST)**: Receives the caller’s identity in a combination of headers, query string parameters, stage variables, and `$context` variables.

## Containers and Orchestration

### ECS (Elastic Container Service)

#### Configuration
- **Task Definition**: Configure containers to access ports on the host container instance.
- **Service Scheduler**: Provides the ability to run tasks manually.
- **Container Instance**: An Amazon EC2 instance running the ECS container agent.
- **Container Agent**: Allows container instances to connect to your cluster.

#### Notes
- **Deregistration**: 
  - **RUNNING State**: Automatic deregistration occurs when the instance is terminated.
  - **STOPPED State**: Manual deregistration is necessary using the Amazon ECS Console, AWS CLI, or an API call. A deregistered instance will no longer appear as a resource in your ECS cluster.


### ECS Cluster Strategies
- **binpack**: Place tasks based on the least available amount of CPU or memory, minimizing the number of instances in use.
- **random**: Place tasks randomly.
- **spread**: Place tasks evenly based on specified values like attribute key-value pairs, instanceId, or host.

### AWS Fargate
- **Definition**: A technology that allows you to run containers without managing servers or EC2 instances.

## Monitoring and Logging

### CloudWatch

#### Configuration
- **Period**: Time length to evaluate the metric or expression for each data point.
- **Evaluation Period**: Number of recent periods or data points to evaluate for alarm state.
- **Datapoints to Alarm**: Number of data points within the evaluation period that must be breaching to trigger an alarm.

#### Purpose
- Collects and tracks metrics, logs, and events from AWS resources and applications.

#### Functionality
- Monitors AWS resources (e.g., EC2 instances, Lambda functions).
- Collects and stores logs for analysis.
- Generates alarms based on metric thresholds.
- Provides dashboards for visualization.

#### CloudWatch Synthetics
- **Purpose**: Actively monitor the availability, performance, and functionality of external web applications and APIs.
- **Functionality**:
  - Create synthetic tests (canaries) to simulate user behavior.
  - Monitor endpoints and APIs for performance issues and errors.
  - Capture screenshots and collect performance metrics.
  - Integrate with CloudWatch for centralized monitoring and alerting.

#### Notes
- CloudWatch does not track memory utilization by default.

### AWS X-Ray

#### Key Components
- **Daemon**: Listens for traffic on UDP port 2000.
- **Subsegments**: Instrument specific functions or lines of code in your application.

#### Environment Variables
- **AWS_XRAY_CONTEXT_MISSING**: Determines behavior if X-Ray data is recorded without a tracing header.
- **_X_AMZN_TRACE_ID**: Contains the tracing header with sampling decision, trace ID, and parent segment ID.
- **AWS_XRAY_DAEMON_ADDRESS**: Exposes the X-Ray daemon’s address.

#### Data Recording
- **Metadata**: Store additional context or detailed information not suitable for searching.
- **Sampling**: Determines the percentage of requests that are traced, reducing the overhead of X-Ray by only capturing a subset of requests. It doesn't allow you to focus on specific blocks of code.
- **Annotations**: Structured data used for filtering and analysis.
- **Subsegments**: Break down a segment into smaller units to measure performance.

#### Notes
- **GetTraceSummaries**: Identify traces with high latency or error rates.
- **BatchGetTraces**: Retrieve full trace data for selected traces.
- **GetServiceGraph**: Visualize the impact of identified issues on the overall system.

## Identity and Access Management (IAM)

### Cognito

#### Identity Pool
- **Purpose**: AWS access management, grants AWS access based on user identity, and authorization.

#### User Pool
- **Purpose**: User management and authentication; stores user information.
- **ID**: Used to reference the user pool when configuring other AWS services (e.g., API Gateway authorizers).

## Storage

### S3

#### Cross-Region Replication (CRR)
- **Requirements**: 
  - Source and destination buckets must have versioning enabled.
  - Buckets must be in different AWS regions.
  - S3 must have permissions to replicate objects on your behalf.

#### Transfer Acceleration
- Enables fast, easy, and secure file transfers over long distances between your client and S3 bucket.

#### S3 Select
- Use SQL statements to filter contents of S3 objects and retrieve only the necessary data.

#### Athena
- More complex and expensive than S3 Select, used for querying data in S3.

#### S3 Retention
- Stores a timestamp in the object version's metadata to indicate when the retention period expires.

## Serverless

### Lambda

#### Invocation Types
- **RequestResponse (default)**: Invoke the function synchronously; API response includes the function response.
- **Event**: Invoke the function asynchronously; failed events are sent to a dead-letter queue if configured.
- **DryRun**: Validate parameter values and verify permissions without invoking the function.

#### Notes
- **AWSLambdaBasicExecutionRole**: Provides Lambda permissions to upload logs to CloudWatch.
- **Concurrency Limits**: The unreserved account concurrency cannot go below 100.
- **Scaling Considerations**: Indefinite scaling is not always desirable.

### Lambda@Edge

#### Notes
- **Viewer Request**: After CloudFront receives a request from a viewer.
- **Origin Request**: Before CloudFront forwards the request to the origin.
- **Origin Response**: After CloudFront receives the response from the origin.
- **Viewer Response**: Before CloudFront forwards the response to the viewer.

![Alt text](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2018/01/23/cloudfront-events-that-trigger-lambda-functions.png)

## Databases

### DynamoDB

#### ReturnValues
- **Purpose**: Determines whether to return item attributes in a response, and if so, which ones.
- **Options**:
  - `NONE`: No attributes are returned.
  - `ALL_OLD`: Returns the attributes of the item as it was before the operation.
  - `UPDATED_OLD`: Returns only the updated attributes, as they were before the operation.
  - `ALL_NEW`: Returns all attributes of the item after the operation.
  - `UPDATED_NEW`: Returns only the updated attributes, as they are after the operation.

#### Select
- **Purpose**: Determines the select behavior of a query or scan operation.
- **Options**:
  - `ALL_ATTRIBUTES`: Returns all attributes.
  - `ALL_PROJECTED_ATTRIBUTES`: Returns only the attributes that are projected into the index.
  - `SPECIFIC_ATTRIBUTES`: Returns only the specified attributes.
  - `COUNT`: Returns the number of matching items, rather than the items themselves.

#### Attributes
- **Purpose**: Refers to the individual data elements within a DynamoDB item.
- **Types**:
  - **Scalar Types**: String, Number, Binary, Boolean, Null.
  - **Document Types**: List, Map.
  - **Set Types**: String Set, Number Set, Binary Set.

#### LeadingKeys
- **Purpose**: Refers to the primary key attributes (Partition Key and Sort Key) that are used to uniquely identify items in a DynamoDB table.
- **Importance**: The choice of leading keys is critical for efficient data retrieval and performance optimization in DynamoDB.

#### Locking Mechanisms
- **Pessimistic Locking**: Locks records immediately.
- **Optimistic Locking**: Locks records only during commit.

#### ReturnConsumedCapacity
- **TOTAL**: Returns the total number of write capacity units consumed.
- **INDEXES**: Returns the total number of write capacity units consumed, with subtotals for the table and any secondary indexes.
- **NONE**: No write capacity details returned (default).

#### Lambda Integration
- Configure Lambda to read from DynamoDB Streams.
- **AWSLambdaDynamoDBExecutionRole**: Permissions include `dynamodb:DescribeStream`, `dynamodb:GetRecords`, `dynamodb:GetShardIterator`, `dynamodb:ListStreams`.

#### Global Secondary Indexes (GSIs)
- Support only eventual consistency.
- Queries or scans consume capacity units from the index, not the base table. ( to prevent throttling: GSI WCU > Table WCU )

#### Notes
- **Write Capacity Units (WCU)**: 1 WCU can handle 1 write per second for an item up to 1KB.
- **Read Capacity Units (RCU)**: 1 RCU can handle 1 strongly consistent read or 2 eventually consistent reads for an item up to 4KB.
- **Lambda Triggers**: Only Lambda functions can be used as triggers for DynamoDB Streams.
- **Global Tables**: Use a “last writer wins” conflict resolution strategy.


| **Metric**                             | **Mode**           | **Rule**                                                                                     | **Handling Larger Items**                                            |
|----------------------------------------|--------------------|----------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| **Read Request Units (RRU)**           | On-Demand          | 1 RRU = 1 strongly consistent read of up to 4 KB/s                                           | `(Total Item Size / 4 KB)` rounded up to the nearest whole number    |
|                                        |                    | 1 RRU = 2 eventually consistent reads of up to 4 KB/s                                        |                                                                      |
|                                        |                    | 2 RRUs = 1 transactional read request for items up to 4 KB/s                                 |                                                                      |
| **Write Request Units (WRU)**          | On-Demand          | 1 WRU = 1 write of up to 1 KB/s                                                              | `(Total Item Size / 1 KB)` rounded up to the nearest whole number    |
|                                        |                    | 2 WRUs = 1 transactional write request for items up to 1 KB/s                                |                                                                      |
| **Write Capacity Units (WCU)**         | Provisioned        | 1 WCU = 1 write of up to 1 KB/s                                                              | `(Total Item Size / 1 KB)` rounded up to the nearest whole number    |
|                                        |                    | 2 WCUs = 1 transactional write request for items up to 1 KB/s                                |                                                                      |
| **Read Capacity Units (RCU)**          | Provisioned        | 1 RCU = 1 strongly consistent read of up to 4 KB/s                                           | `(Total Item Size / 4 KB)` rounded up to the nearest whole number    |
|                                        |                    | 1 RCU = 2 eventually consistent reads of up to 4 KB/s                                        |                                                                      |
| **Calculating Required RCUs**          | Provisioned        | Step 1: Round up average item size to nearest 4 KB                                           | Example: Item size of 3.5 KB rounds up to 4 KB                       |
|                                        |                    | Step 2: Divide average item size by 8 KB to determine RCU per item                           | Example: 4 KB / 8 KB = 0.5 RCU per item                              |
|                                        |                    | Step 3: Multiply RCU per item by the number of items to be read per second                   | Example: 150 items x 0.5 RCU per item = 75 RCUs for eventually consistent read requests |


### DynamoDB Streams

#### Definition
- An ordered flow of information about changes to items in an Amazon DynamoDB table.

#### Notes
- All data in DynamoDB Streams is subject to a 24-hour lifetime.

### RDS

#### Encryption
- **RDS Encryption**: Encrypts storage volumes (physical layer).
- **Database Engine Encryption**: Encrypts data within the database before being written to storage.

#### Notes
- **Transparent Data Encryption (TDE)**: Used for encrypting stored data on DB instances running Microsoft SQL Server.

### Kinesis Data Streams

#### Definition
- A serverless streaming data service for capturing, processing, and storing data streams at any scale.
- A set of shards, each containing a sequence of data records.

#### Notes
- **Split Shards**: Increases capacity.
- **Instance Scaling**: Launching more instances than the number of open shards will not improve processing efficiency.
- **KCL Worker**: Each shard is processed by exactly one KCL worker, with one corresponding record processor.
    one worker can process any number of shards, so it’s fine if the number of shards exceeds the number of instances

### Amazon Redshift

#### Definition
- Uses SQL to analyze structured and semi-structured data across data warehouses, operational databases, and data lakes, delivering optimal price performance at any scale.

### AWS Elastic MapReduce (EMR)

#### Definition
- is a cloud-based big data platform provided by Amazon Web Services (AWS) that enables you to process vast amounts of data using popular open-source frameworks like Apache Hadoop, Apache Spark, Apache HBase, Apache Flink, and Presto. EMR simplifies running big data frameworks on dynamically scalable clusters, making it easier to analyze large datasets quickly and cost-effectively.

#### Usage
- Troubleshoot cluster issues, such as failures or errors.

### Amazon GuardDuty

#### Definition
- A threat detection service that monitors for malicious activity and anomalous behavior to protect AWS accounts, workloads, and data.

### AWS CodeCommit

#### Authentication
- Set up a Git credential helper using your access key credentials specified in your AWS credential profile.
- Generate HTTPS Git credentials for AWS CodeCommit and specify them in the Git Credential Manager.

#### Notes
- AWS credentials must be configured before cloning the CodeCommit repository using the HTTPS clone URL.

### AWS CodeArtifact

#### Definition
- A secure, highly scalable, managed artifact repository service that helps organizations store and share software packages for application development.

### AWS CodeDeploy

#### Deployment Algorithms
- **Canary**: Traffic is shifted in two increments.
- **Linear**: Traffic is shifted in equal increments with equal time between each increment.

#### Deployment Types
- **In-Place Deployment**: Applicable only for EC2/On-Premises.
- **Blue/Green Deployment**:
  - **EC2/On-Premises**: Applicable only for EC2 instances.
  - **AWS Lambda**: All Lambda deployments are blue/green.
  - **Amazon ECS**: Applicable for ECS compute platform.

#### Files
- **appspec.yml**: Manage each application deployment as a series of lifecycle event hooks.

#### Notes
- The CodeDeploy agent is required only if deploying to an EC2/On-Premises compute platform.
- Stores application packages (e.g., zip files) in S3 for deployment.

### AWS Data Pipeline

#### Data Pipeline Concepts
- A web service that helps you reliably process and move data between different AWS compute and storage services, as well as on-premises data sources, at specified intervals.

#### Pipeline Definition
- **Functionality**:
  - A JSON document that specifies the business logic of your data management.
  - Defines the source data, the data transformations, and the destination where the data should be stored.

#### Activities
- **Functionality**:
  - The work performed on the data in the pipeline, such as copying, running a query, or performing a custom application.
  - Examples include EMR activity, ShellCommandActivity, and more.

#### Scheduling
- **Functionality**:
  - Defines when your pipeline activities should run.
  - You can specify intervals or specific times for when each activity should be executed.

#### Resources
- **Functionality**:
  - The compute resources (like EC2 instances or EMR clusters) that your pipeline uses to perform activities.
  - Includes instance types, regions, and other details about the infrastructure.

#### Monitoring and Logging
- **Functionality**:
  - AWS Data Pipeline provides built-in monitoring and logging to track the progress and health of your pipeline.
  - Logs are available in Amazon CloudWatch.

### Elastic Beanstalk

#### Configuration Files
- **cron.yaml**: Configure a worker daemon.
- **Dockerrun.aws.json**: Configure multi-container Docker environments.
- **env.yaml**: Configure environment name, solution stack, and environment links.

```plaintext
.
├── .ebextensions/
│   ├── 01-packages.config
│   ├── 02-environment.config
│   ├── 03-files.config
│   ├── 04-container_commands.config
│   └── 05-commands.config
```

#### Deployment Methods
- **All at Once**: Deploy to all instances simultaneously.
- **Rolling**: Deploy in batches, reducing capacity during deployment.
- **Rolling with Additional Batch**: Launch a new batch of instances before deployment to ensure full capacity.
- **Immutable**: Deploy to a fresh group of instances, minimizing the impact of failed deployments.
- **Blue/Green**: Deploy to a separate environment and swap CNAMEs for zero downtime.

#### Deployment Methods Comparison
| Method                       | Impact of Failed Deployment | Deploy Time  | Zero Downtime | No DNS Change  | Rollback Process        |
|------------------------------|-----------------------------|--------------|---------------|----------------|-------------------------|
| All at once                  | Downtime                    | ⏳           | ✘             | ✓              | Manual Redeploy         |
| Rolling                      | Single batch out of service | ⏳⏳⏳        | ✓             | ✓              | Manual Redeploy         |
| Rolling with additional batch| Minimal                     | ⏳⏳†        | ✓             | ✓              | Manual Redeploy         |
| Immutable                    | Minimal                     | ⏳⏳         | ✓             | ✘              | Terminate New Instances |
| Blue/green                   | Minimal                     | ⏳⏳⏳⏳     | ✓             | ✘              | Swap URL                |

## Networking and Security

### ELB vs. ALB

#### ELB (Elastic Load Balancer)
- **Level**: Network, application, and gateway.
- **Routing**: Based on IP address.
- **Protocol**: TCP, UDP, HTTP.
- **Features**: Basic load balancing.
- **Use Cases**: General-purpose load balancing.

#### ALB (Application Load Balancer)
- **Level**: Application.
- **Routing**: Based on HTTP headers, path, and host.
- **Protocol**: HTTP/HTTPS, WebSocket.
- **Features**: Advanced routing, sticky sessions, health checks.
- **Use Cases**: Web applications, microservices.

## Security Services

### AWS WAF vs AWS Firewall Manager

- **AWS WAF (Web Application Firewall)**:
  - Designed to protect web applications from common web exploits, such as SQL injection, cross-site scripting (XSS), and DDoS attacks.
  - Allows creation of custom rules or use of managed rules to filter specific HTTP/S traffic patterns.
  - Can be integrated directly with Amazon CloudFront, Application Load Balancer (ALB), and API Gateway to protect web applications.
  - Provides real-time visibility into web traffic with detailed logs and metrics.
  - **Use Case**: Protect web applications and APIs from web-based threats, block or allow traffic based on specific patterns like IP addresses, request headers, or country.

- **AWS Firewall Manager**:
  - A security management service that allows centralized configuration and management of firewall rules across multiple AWS accounts and resources.
  - Works in conjunction with AWS WAF, AWS Shield, and AWS VPC security groups to enforce security policies organization-wide.
  - Automatically discovers resources and applies the appropriate security policies, ensuring consistent protection across accounts and regions.
  - Ideal for multi-account and multi-region management, reducing the risk of misconfigurations and maintaining compliance.
  - **Use Case**: Centralized management and enforcement of security policies across multiple AWS accounts, especially in large or complex organizations. Automates protection and ensures consistent security across all resources.

### CloudHSM

#### Purpose
- **CloudHSM (Hardware Security Module)**: Provides a dedicated hardware security module in the cloud that enables you to generate, manage, and store cryptographic keys securely in AWS while maintaining full control over them. It's designed for organizations requiring high-level security for cryptographic operations and compliance with strict regulatory standards.

#### Use Cases
- **Secure Key Storage**: Safely store sensitive cryptographic keys for applications that require strong encryption.
- **Encryption/Decryption Operations**: Perform secure encryption and decryption of data, ensuring that the keys are stored within a hardware-protected environment.
- **Digital Signing**: Sign documents, transactions, or certificates securely with keys that are protected within the HSM.
- **SSL/TLS Offloading**: Offload SSL/TLS processing to CloudHSM to secure web applications while ensuring private keys remain secure.
- **Certificate Authority (CA)**: Manage and store the root of trust for a public key infrastructure (PKI) within the HSM.

#### Features
- **Dedicated Hardware**: Provides single-tenant, dedicated hardware for cryptographic operations, ensuring isolation and security.
- **Full Key Control**: Allows you to retain full control over your cryptographic keys, ensuring that AWS does not have access to them.
- **Compliance**: Meets stringent security standards such as FIPS 140-2 Level 3, making it suitable for regulatory compliance in sectors like finance and healthcare.
- **Scalability**: Supports clustering of multiple HSMs to handle increased cryptographic workloads and ensure high availability.
- **VPC Integration**: HSM appliances are deployed within your Amazon Virtual Private Cloud (VPC), allowing for secure and controlled network access.

#### Key Differences
- **AWS KMS vs. CloudHSM**: AWS KMS is a managed key management service where AWS controls the underlying hardware, while CloudHSM gives you complete control over a dedicated HSM. CloudHSM is preferred when you require full control over your cryptographic keys and need to comply with strict regulatory requirements.


### KMS (Key Management Service)

#### Response Types
- **GenerateDataKey**: Returns a plaintext copy and an encrypted copy of the data key under a KMS key.
- **GenerateDataKeyWithoutPlaintext**: Returns only the encrypted copy of the data key.
- **Encrypt**: Encrypts plaintext into ciphertext using a KMS key.
- **GenerateRandom**: Returns a cryptographically secure random byte string.

#### Server-Side Encryption (SSE)
- **SSE-S3**: Server-side encryption with S3-Managed Keys.
- **SSE-KMS**: Server-side encryption with AWS KMS Keys.
- **SSE-C**: Server-side encryption with Customer-Provided Keys.

#### Client-Side Encryption
- **KMS Key**: Use client-side encryption with AWS KMS Key.
- **Client-Side Master Key**: Use client-side encryption with a client-side master key.

#### Notes
- For large file uploads to S3, the required permissions are `kms:Decrypt` and `kms:GenerateDataKey`.
- **Encryption Pattern**:
  1. Use `GenerateDataKey` to get a data encryption key.
  2. Encrypt data locally using the plaintext data key, then erase the key from memory.
  3. Store the encrypted data key alongside the encrypted data.

## Messaging and Queuing

### SQS (Simple Queue Service)

#### FIFO Queue
- **Enable Content-Based Deduplication**: Use a SHA-256 hash to generate the message deduplication ID using the body of the message.

#### Notes
- Use a FIFO queue to avoid duplicate messages.

### WAF (Web Application Firewall)

#### Definition
- A web application firewall to filter incoming web requests and block malicious traffic.

### AWS Security Token Service (STS)

#### Definition
- A web service that enables you to request temporary, limited-privilege credentials for users.

#### Note
- The policy that will use STS should be created on the account you are delegating access to, which is the development account.

## Edge Computing

### CloudFront

#### Operations
- **Cache Key Normalization**: Transform HTTP request attributes to create an optimal cache key, improving the cache hit ratio.
- **Header Manipulation**: Insert, modify, or delete HTTP headers in the request or response.
- **Status Code Modification and Body Generation**: Evaluate headers and respond with customized content.
- **URL Redirects or Rewrites**: Redirect viewers based on request information or rewrite paths.
- **Request Authorization**: Validate hashed authorization tokens (e.g., JWT) by inspecting authorization headers or other request metadata.

## Development Tools

### AWS Amplify

#### Services
- **Amplify Hosting**: Provides a Git-based workflow for hosting full-stack serverless web apps with continuous deployment.
- **Amplify Studio**: A visual development environment for creating scalable, full-stack web and mobile apps.

### AWS Amplify DataStore

#### Definition
- Allows frontend web and mobile developers to easily build, connect, and host full-stack applications.

## General Notes
- Configure your application to deliver static content and decrease end-user latency using Amazon S3 and Amazon CloudFront.
- When you invoke a function asynchronously, you don’t wait for a response from the function code.
- **--dry-run**: Parameter checks whether you have the required permissions for the action without making the request, providing an error response.
- You can only specify one launch template for an Auto Scaling group at a time, and you can’t modify a launch template after you’ve created it.

- To detach an Amazon Elastic Block Store (EBS) volume from an EC2 instance, you need to follow a series of steps to ensure that the detachment is done safely and correctly:
  1. Stop Any Applications or Processes Using the Volume
  2. Unmount the Volume from the EC2 Instance
  3. Detach the EBS Volume Using the AWS Management Console or CLI


