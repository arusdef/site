---
title: Instance Rolls
order: 525
---

An instance roll is a one-by-one replacement of every instance in your Rack cluster. By replacing instances one at a time, the desired instance count of the Rack can be maintained throughout the operation. When an instance is terminated, it is first drained of any services, which must be rescheduled within the cluster and converge, i.e. run at the desired count in the cluster, before termination can complete. Once an instance is terminated, the process repeats with the next instance. Instance rolls are triggered by some updates of your Rack's CloudFormation stack, including those that occur during the following situations:

1. [SSH Keyrolls](#ssh-keyrolls)
1. [AMI Updates](#ami-updates)
1. [Changes to a CF Resource](#changes-to-a-cf-resource)

Instance rolls should not be confused with one-off instance replacements, which are expected to occur periodically as bad instances in your cluster are detected, replaced, and terminated.

## SSH Keyrolls

An [SSH keyroll](/docs/ssh-keyroll) creates or regenerates the SSH keypair used by the Rack API to access the cluster instances when you `convox instances ssh <instanceID>`. A keyroll triggers a CloudFormation update that performs a rolling replacement of all of the Rack's instances.

## AMI Updates

Convox cluster instances are kept up to date by making sure the CloudFormation template of the Rack cluster references the latest ECS-optimized AMIs released by AWS. When a Rack release includes updated AMIs, updating to that release will result in a rolling replacement of all of the Rack's instances.

## Changes to a CF Resource

Occasionally a change to a specific CF resource requires an instance roll. For example, changes to some properties (e.g. UserData) of the Auto Scaling launch configuration could cause instances to roll.
