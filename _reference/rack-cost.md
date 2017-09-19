---
title: Rack Cost
---

The cost of the AWS resources that make up a Rack varies according to your resource usage and deployed applications. This page shows an estimate of the cost of a default Rack.

The _minimum_ monthly cost of a default Rack is about **$99.23**. Each app will incur an additional cost.

You can interactively calculate the base cost in this [Simple Monthly Calculator template](http://calculator.s3.amazonaws.com/calc5.html?key=cloudformation/9d859a10-ec96-4c25-9275-df30f1316423).

<div class="block-callout block-show-callout type-warning" markdown="1">
This costs estimated on this page are per Rack, per month. The amounts listed are approximate, region-dependent and subject to change by AWS at any time.
</div>

## Default AWS resources

Rack creation logs display each AWS resource as it is being created. In each section below, we'll include sample output from these logs, broken down by the AWS resource type they correspond to.

* [CloudWatch](#cloudwatch)
* [DynamoDB](#dynamodb)
* [EBS (Elastic Block Store)](#ebs-elastic-block-store)
* [EC2 instances](#ec2-instances)
* [EC2 Container Service (ECS)](#ec2-container-service-ecs)
* [EFS (Elastic File System)](#efs-elastic-file-system)
* [ELB (Elastic Load Balancer)](#elb-elastic-load-balancer)
* [IAM (Identity and Access Management)](#iam-identity-and-access-management)
* [KMS (Key Management Service)](#kms-key-management-service)
* [Lambda](#lambda)
* [S3 (Simple Storage Service)](#s3-simple-storage-service)
* [Other](#other)
* [Total](#total)

### CloudWatch

```
Created CloudWatch Log Group: test-rack-LogGroup-1IUONPGPH2P6X
```

AWS CloudWatch costs, per month:
* $0.50 per GB ingested
* $0.03 per GB archived

**Cost: about $1.00 per month**


### DynamoDB

```
Created DynamoDB Table: test-rack-builds
Created DynamoDB Table: test-rack-releases
```

Amazon DynamoDB is a fast and flexible NoSQL database service.
Rack builds and releases are stored in DynamoDB tables.

Note: DyanamoDB costs in particular are highly variable.

**Cost: about $5.81 per month**


### EBS (Elastic Block Store)

Amazon Elastic Block Store (Amazon EBS) provides persistent block storage volumes for use with Amazon EC2 instances in the AWS Cloud.

| **Parameter** | **Default value** | **Description** |
| SwapSize      | 5                 | Default swap volume size in GB
| Volume type   | gp2               | |
| VolumeSize    | 50                | Default disk size in GB

Each Rack instance gets an 8GB root volume. Each one then has a 5GB swap volume and another 50GB volume for containers to use.

Therefore, this calculation is based on a formula of `63 GB * 3 instances * 0.10/GB-mo`.

**Cost: about $18.90 per month**

### EC2 instances

```
Created AutoScalingGroup: test-rack-Instances-1C9W9E9CWSOZJ
```

| **Parameter** | **Default value** | **Description** |
| ApiCpu        | 128               | How much cpu should be reserved by the api web process
| ApiMemory     | 128               | How much memory should be reserved by the api web process
| InstanceCount | 3                 | The number of instances in the runtime cluster
| InstanceType  | t2.small          | The type of the instances in the runtime cluster

By default, each Rack includes three EC2 t2.small instances used to run elastic load balancers (EC2-ELB). (Note: each instance gets a 50GB gp2 volume; these volumes are counted in the EBS section above.)

t2.small instances with 2 GiB of memory in the us-east-1 region cost $0.023 per hour, or about $16.56 per month.

This estimate assumes 50 GB of total network data processed by all three instances.

**Cost: about $50.52 per month**

### EC2 Container Service (ECS)

```
Created ECS Cluster: test-rack-Cluster-10I4QARVDTA2D
Created ECS TaskDefinition: RackMonitorTasks
Created ECS TaskDefinition: RackWebTasks
Created Unknown: AWS::ECS::Service: RackWeb
Created Unknown: AWS::ECS::Service: RackMonitor
```

There is no additional charge for Amazon EC2 Container Service. You pay for AWS resources (e.g. EC2 instances or EBS volumes) you create to store and run your application.

**Cost: $0.00 per month**

### EFS (Elastic File System)

```
Created EFS Filesystem: fs-5008c719
```

Amazon Elastic File System (Amazon EFS) provides simple, scalable file storage for use with Amazon EC2 instances in the AWS Cloud.

It costs about $0.30 per GB per month.

On a typical Rack, EFS cost is trivial.

**Cost: <$1.00 per month**

### ELB (Elastic Load Balancer)

```
Created Elastic Load Balancer: test-rack
```

Elastic Load Balancing automatically distributes incoming application traffic across multiple Amazon EC2 instances.

$0.0225 per Application Load Balancer-hour (or partial hour)

**Cost: about $18.00 per month**

### IAM (Identity and Access Management)

```
Created Security Group: sg-d465f9ae
Created IAM User: test-rack-KernelUser-1JTVFJTCHDVDE
Created IAM User: test-rack-RegistryUser-LW33W69G7L1E
Created Security Group: sg-d065f9aa
Created Security Group: sg-cd65f9b7
Created Access Key: AKIAIMZ2YF2MAPYB6YXQ
Created Access Key: AKIAJBJNR4FARZNEIO3A
```

AWS Identity and Access Management (IAM) is a feature of your AWS account offered at no additional charge. 

**Cost: $0.00**

### KMS (Key Management Service)

```
Created KMS Key: EncryptionKey
```

KMS keys are used to encrypt environment variables on your Rack.

**Cost: about $2.00 per month**

### Lambda

```
Created Lambda Function: test-rack-CustomTopic-1SSV97B44N8KN
Created Lambda Function: test-rack-LogSubscriptionFilterFunction-1RS6AARFV7RYL
Created Lambda Function: test-rack-InstancesLifecycleHandler-6RFXW4APGJ5K
```

Three Lambda functions are created for each Rack to handle instance lifecycles and log subscriptions.

The Lambda free tier includes 1M free requests per month and 400,000 GB-seconds of compute time per month.

**Cost: about $1.00 per month**

### S3 (Simple Storage Service)

```
Created S3 Bucket: test-rack-registrybucket-11vpj27tqcy57
Created S3 Bucket: test-rack-settings-14ztaq5q8dct5
```

S3 buckets are created to store metadata about your Rack.

**Cost: <$1.00 per month**

### Other

```
Created VPC Internet Gateway: igw-2213f345
Created VPC: vpc-2ff3a348
Created Routing Table: rtb-d602b7b0
Created VPC Subnet: subnet-11d70b58
Created VPC Subnet: subnet-fe4ea3d3
Created VPC Subnet: subnet-ec8c6eb7
```

Networking resources do not incur charges from AWS.

### Total

```
Created CloudFormation Stack: test-rack
```

| **AWS resource** | **Minimum monthly cost** |
| CloudWatch       | <$1.00                   |
| DynamoDB         |  $5.81                   |
| EBS              | $18.90                   |
| EC2              | $50.52                   |
| ECS              |  $0.00                   |
| EFS              | <$1.00                   |
| ELB              | $18.00                   |
| IAM              |  $0.00                   |
| KMS              |  $2.00                   |
| Lambda           | <$1.00                   |
| S3               | <$1.00                   |
| **Total**        | **$99.23**               |

**Minimum cost:** about $100 per Rack per month


## See also

* [Rack CloudFormation template](https://github.com/convox/rack/blob/master/provider/aws/dist/rack.json#L254) on GitHub
* [CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/)
* [DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing/)
* [EC2 pricing](https://aws.amazon.com/ec2/pricing/)
* [ECS pricing](https://aws.amazon.com/ecs/pricing/)
* [EFS pricing](https://aws.amazon.com/efs/pricing/)
* [ELB pricing](https://aws.amazon.com/elasticloadbalancing/applicationloadbalancer/pricing/)
* [IAM pricing](https://aws.amazon.com/govcloud-us/pricing/iam/)
* [Lambda pricing](https://aws.amazon.com/lambda/pricing/)
