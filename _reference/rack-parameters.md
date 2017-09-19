---
title: Rack Parameters
---

# Setting Parameters

Parameters can be set using the following command.

    convox rack params set Foo=bar

You can also set multiple parameters at once.

    convox rack params set Foo=bar Baz=qux

# Available Parameters

The following parameters can be used to configure your Convox Rack:

* [Ami](#ami)
* [ApiCount](#apicount)
* [ApiCpu](#apicpu)
* [ApiMemory](#apimemory)
* [Autoscale](#autoscale)
* [BuildCpu](#buildcpu)
* [BuildImage](#buildimage)
* [BuildInstance](#buildinstance)
* [BuildMemory](#buildmemory)
* [ClientId](#clientid)
* [ContainerDisk](#containerdisk)
* [Development](#development)
* [EncryptEbs](#encryptebs)
* [Encryption](#encryption)
* [ExistingVpc](#existingvpc)
* [HttpProxy](#httpproxy)
* [InstanceBootCommand](#instancebootcommand)
* [InstanceCount](#instancecount)
* [InstanceRunCommand](#instanceruncommand)
* [InstanceType](#instancetype)
* [InstanceUpdateBatchSize](#instanceupdatebatchsize)
* [Internal](#internal)
* [Key](#key)
* [OnDemandMinCount](#ondemandmincount)
* [Password](#password) **(required)**
* [Private](#private)
* [PrivateApi](#privateapi)
* [SpotInstanceBid](#spotinstancebid)
* [Subnet0CIDR](#subnet0cidr)
* [Subnet1CIDR](#subnet1cidr)
* [Subnet2CIDR](#subnet2cidr)
* [SubnetPrivate0CIDR](#subnetprivate0cidr)
* [SubnetPrivate1CIDR](#subnetprivate1cidr)
* [SubnetPrivate2CIDR](#subnetprivate2cidr)
* [SwapSize](#swapsize)
* [Tenancy](#tenancy)
* [Version](#version) **(required)**
* [VolumeSize](#volumesize)
* [VPCCIDR](#vpccidr)

## Ami

Which [Amazon Machine Image](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/launch_container_instance.html) should be used.

## ApiCount

How many Rack API containers to run. Setting this higher than 2 will guarantee better Rack API availability for mission critical clusters.

| Default value | `2` |

## ApiCpu

How much CPU should be reserved by the API web process.

| Default value | `128` |

## ApiMemory

How much memory should be reserved by the API web process.

| Default value | `128` |

## Autoscale

Autoscale rack instances. See our [Scaling doc](/docs/scaling#autoscale) for more information.

| Default value  | `Yes`       |
| Allowed values | `Yes`, `No` |

## BuildCpu

How much CPU should be allocated to builds.

| Default value | `0` |

## BuildImage

Override the default build image.

This parameter is used for local [development on Rack](https://github.com/convox/rack/blob/master/Development.md#build-image). This is primarily used for development purposes only. General users should not set this parameter (which is not related to `BuildInstance` below).

| Default value | "" |

## BuildInstance

EC2 instance type to create and use as the Rack's [dedicated build instance](/docs/builds/#dedicated-build-instance).

Note: the build instance will also use the [`InstanceBootCommand`](/docs/rack-parameters#instancebootcommand) and [`InstanceRunCommand`](/docs/rack-parameters#instanceruncommand) Rack params, if defined.

| Default value  | ""                                                               |
| Allowed values | [EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/) |

See also the [InstanceType](#instancetype) Rack parameter and our page on [AWS Instance Types](/docs/aws-instance-types/).

## BuildMemory

Defines the amount of memory (in MB) that the instance should allocate to build containers for each build.

| Default value  | 1024 |

<div class="alert alert-info">
Getting build errors like <b>Starting build... ERROR: not enough memory available to start process</b>? You should either reduce this parameter, or change the <a href="#instancetype">InstanceType</a> parameter to an <a href="https://aws.amazon.com/ec2/instance-types/">instance type</a> with more memory.
</div>

<div class="alert alert-warning">
Note: If you set BuildMemory to an amount that's more than half of the total memory available to the build instance, you'll only be able to run one build at a time. If this value is too high, builds may fail.
</div>


## ClientId

Anonymous identifier.

| Default value  | `dev@convox.com` |

## ContainerDisk

<div class="alert alert-info">
Getting errors like <b>No space left on device</b>? You can extend the space on the device by increasing this parameter.
</div>

Default container disk size in GB.

| Default value | `10` |

## Development

Development mode.

| Default value  | `No`        |
| Allowed values | `Yes`, `No` |

## EncryptEbs

Enable encryption at rest for EBS volumes.

| Default value  | `No`        |
| Allowed values | `Yes`, `No` |

## Encryption

Encrypt secrets with KMS.

| Default value    | `Yes`       |
| Permitted values | `Yes`, `No` |

## ExistingVpc

Existing VPC ID (if blank, a VPC will be created).

## HttpProxy

HTTP proxy for outbound HTTP connections (for network-restricted Racks).

Set this value to the hostname (or IP address) and port number of an HTTP proxy to use for the ECS agent to connect to the internet.

| Default value  | "" |

For more information, see [HTTP Proxy Configuration](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/http_proxy_config.html) in the AWS docs.

## InstanceBootCommand

A single line of shell script to run (as root) as a cloud-init command early during instance boot.

For more information about using cloud-init with EC2, see the AWS doc [Running Commands on Your Linux Instance at Launch](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html#user-data-cloud-init). For cloud-init specifics, see "bootcmd" in the doc [Run commands on first boot](http://cloudinit.readthedocs.io/en/latest/topics/examples.html#run-commands-on-first-boot).

The `InstanceBootCommand` will also apply to any [build instance](/docs/rack-parameters#buildinstance) associated with the Rack.

| Default value | "" |

#### Example: increase virtual memory

Tell the host EC2 instance to set operating system limits on mmap to `262144` (useful for ElasticSearch):

```
$ convox rack params set 'InstanceBootCommand="sysctl -w vm.max_map_count=262144"'
Updating parameters... OK
```

(Note the surrounding single quotes in the above command.)

## InstanceCount

The number of EC2 instances in your Rack cluster.

| Default value | `3`    |
| Minimum value | `3`    |

## InstanceRunCommand

A single line of shell script to run as a cloud-init command late during instance boot.

For more information about using cloud-init with EC2, see the AWS doc [Running Commands on Your Linux Instance at Launch](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html#user-data-cloud-init). For cloud-init specifics, see "runcmd" in the doc [Run commands on first boot](http://cloudinit.readthedocs.io/en/latest/topics/examples.html#run-commands-on-first-boot).

The `InstanceRunCommand` will also apply to any [build instance](/docs/rack-parameters#buildinstance) associated with the Rack.

| Default value | "" |

## InstanceType

The type of EC2 instance to run in your Rack cluster.

| Default value  | `t2.small`                                                       |
| Allowed values | [EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/) |

See also the [BuildInstance](#buildinstance) Rack parameter and our page on [AWS Instance Types](/docs/aws-instance-types/).

## InstanceUpdateBatchSize

The number of instances to update in a batch.

| Default value | `1`    |
| Minimum value | `1`    |

## Internal

Make all new apps created in the Rack use Internal ELBs by default, i.e. make them accessible only from within the VPC and unreachable from the internet. See the [Internal Racks](/docs/internal-apps#internal-racks) section of our Internal Apps doc for more information.

| Default value  | `No`        |
| Allowed values | `Yes`, `No` |

## OnDemandMinCount

If using spot instances through the [SpotInstanceBid](#spotinstancebid) parameter, this configures the minimum number of on demand instances. This should be set to a value that will guarantee the minimum acceptable service availbility.

| Default value | `3`    |

## Key

SSH key name for access to cluster instances.

## Password

(REQUIRED) API HTTP password.

| Minimum length  | 1  |
| Maximum length  | 50 |

## Private

Have the Rack create non-publicly routable resources, i.e. in a private subnet. See our [Private Networking doc](/docs/private-networking/) for more information.

| Default value  | `No`        |
| Allowed values | `Yes`, `No` |

## PrivateApi

Put Rack API Load Balancer in a private network, i.e. have the Rack API use an Internal ELB, making it unreachable from the internet.

| Default value  | `No`        |
| Allowed values | `Yes`, `No` |

## SpotInstanceBid

A value, in dollars, that you want to pay for spot instances. If spot instances are available for the bid price, the Rack instances will use spot instances instead of on demand instances, resulting in significant cost savings. If the parameter is empty, spot instances will not be utilized. This should be used with the [OnDemandMinCount](#ondemandmincount) parameter to guarantee some on demand instances are running if spot instances are not available.

| Default value  | "" |

## Subnet0CIDR

Public Subnet 0 CIDR Block.

| Default value | `10.0.1.0/24` |

## Subnet1CIDR

Public Subnet 1 CIDR Block.

| Default value | `10.0.2.0/24` |

## Subnet2CIDR

Public Subnet 2 CIDR Block.

| Default value | `10.0.3.0/24` |

## SubnetPrivate0CIDR

Private Subnet 0 CIDR Block.

| Default value | `10.0.4.0/24` |

## SubnetPrivate1CIDR

Private Subnet 1 CIDR Block.

| Default value | `10.0.5.0/24` |

## SubnetPrivate2CIDR

Private Subnet 2 CIDR Block.

| Default value | `10.0.6.0/24` |

## SwapSize

Default swap volume size in GB.

| Default value | `5` |

## Tenancy

Dedicated hardware.

| Default value  | `default`              |
| Allowed values | `default`, `dedicated` |

## Version

(REQUIRED) Convox release version.

| Minimum length | 1 |

## VolumeSize

Default disk size (in gibibytes) of the EBS volume attached to each EC2 instance in the cluster.

| Default value | `50` |

## VPCCIDR

VPC CIDR Block. Note that changing this has no effect since VPC CIDR ranges cannot be changed after they're created.

| Default value | `10.0.0.0/16` |

## Reference

* [rack.json](https://github.com/convox/rack/blob/b7d9a114d5cc5f0a2843b89413067466aa41bc94/provider/aws/dist/rack.json#L176) on GitHub
