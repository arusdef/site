---
title: ALB Is Ready
date: 2017-05-25 00:00:00 Z
author: Noah Zoschke
twitter: nzoschke
---

A load balancer is the most important cloud service. A great load balancing strategy means the front door to your application is always online and routing traffic to backends that are healthy and can scale out to meet the load. No load balancing strategy means a single point of failure and overloaded backends.

Seven months ago I covered the AWS [Application Load Balancer (ALB) announcement](https://convox.com/blog/alb/). The [ALB GitHub issue](https://github.com/convox/rack/issues/1373) is one of the most popular feature requests for the Convox platform.

It’s clear that the Elastic Load Balancer (ELB) is old and busted, and ALB is the new hotness.

So I’m happy to announce that Convox now has an ALB path. Check out this new [Getting Started Guide](https://github.com/convox/praxis/blob/master/docs/getting-started.md) to learn how to set up a new version of the platform with a single ALB routing traffic to many apps.

This improves deploy speeds. ALB allows us to launch and register many new containers into the load balancer at once, much faster than rolling new containers out one or two at a time behind an ELB.

It also simplifies scaling on all clusters, letting us run our workloads on fewer, bigger instances. This makes autoscaling of clusters easier than ever and could also mean big cost savings by reducing license fees for agents like NewRelic and Datadog.

Ultimately this brings the cost of a small private, production-ready container cluster down to **$35/mo** for a handful of apps.

<!--more-->

## ELB Port Mapping → ALB Target Groups

At Convox we've been using ELBs to load balance traffic to apps on the EC2 Container Service (ECS) for years. While the reliability of ELB to ECS has been impeccable, there is one clear flaw in how it works.

An ELB is configured to forward traffic from an internet-facing port to a single EC2 instance port. For example a common configuration is to forward the load balancer port 80 to instance port 8080, and the load balancer port 443 to instance port 4443. Since we can register many instances behind this load balancer, ELB port mapping is sufficient for horizontal scaling.

However this means to add an additional backend we need to add an additional instance. In the container universe this is less than ideal.

If we want to scale one web service out to 10 backends, we need 10 instances. Furthermore, when it comes time to a new version of the web service, if we want to always maintain at least 10 backends, we need one or more spare instances to place a new container before we can stop an old one. So we might be looking at a 12 instance cluster just to support one service type.

This also impacts deployment speed. In the above scenario, we can only place one or two new containers at a time onto the spare instances. When these containers come up and pass health checks, ECS will stop old containers. This roll out is automatic but in practice can take 5 or 10 minutes.

So the August ALB announcement was exciting. It's clear that ALB was designed to directly address this flaw in ELB by introducing [target groups](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html).

A backend from any instance and port can register itself into an target group and the ALB will start load balancing traffic to it. This means that two or more web backends on a single instance can share in load balancing.

We no longer need 12 instances to support 10 web backends. In fact a single large instance could work (but of course we want two instances for availabilty).

We also don't need to roll out new containers a few at a time. We can launch 10 new containers into the cluster at once, then stop all the old ones at once. This should take only a minute or two.

## ALB Path Based Routing: Not Quite There

ALB’s content-based routing showed that a single ALB could dynamically forward requests to more than one group of containers dynamically, even two or more containers on the same instance, based on the HTTP request.

But more research and development around ALB showed it wasn’t really ready for total adoption.

ALB routing was path based, meaning different target groups of backends needed to be sub-paths under a single domain:

* [https://myapp.com/](https://myapp.com/) → Jekyll app

* [https://myapp.com/blog](https://myapp.com/blog) → WordPress app

* [https://myapp.com/api](https://myapp.com/api) → Rails app

On top of this, ALB had a limit of **10 URL-based rules**.

The reality of this implementation is that using ALB could require serious changes to our deployments to map everything under a single domain, and that we’d still need multiple ALBs for deployments with more than 10 service types.

Because of these limitations of ALB, we took a conservative approach for Convox and persisted with an ELB strategy, where every set of containers gets it’s own ELB and own hostname. All those ELBs are annoying on the monthly bill, but at least we didn’t have to rewrite our apps.

## ALB Host Based Routing: YES!

Fast forward to the April 2017 announcement: [ALB Adds Support for Host-based Routing and Increased Rules](https://aws.amazon.com/about-aws/whats-new/2017/04/elastic-load-balancing-adds-support-for-host-based-routing-and-increased-rules-on-its-application-load-balancer/).

A single ALB can now route requests to a target group based on the HTTP Host header. On top of this, ALB now supports **75 URL-based rules.** Now we’re talking! A single ALB in a container cluster can do it all...

* [https://myapp.com/](https://myapp.com/) → Nginx app

* [https://blog.myapp.com/](https://myapp.com/blog) → WordPress app

* [https://api.myapp.com/](https://myapp.com/api) → Rails app

* [https://balancer.us-east-1.elb.amazonaws.com](https://balancer.us-east-1.elb.amazonaws.com)/system → Cluster management API

With this enhancement, it’s easy to see how a single ALB can handle the most complex microservice deployments.

## The ALB Plan

The final question for us at Convox is how to migrate the platform from many ELBs to a single ALB. Unfortunately there’s no easy way.

Load balancers and their host names are mission-critical components of any production service. To avoid downtime, changes in this layer require careful maintenance windows for adding a new load balancer, changing DNS, then removing the old load balancer.

It’s literally impossible to do this automatically with zero downtime during an app deploy.

That means ALB represents a breaking architectural change.

So at Convox we’re looking at ALB as this as opportunity to improve the entire platform architecture:

* 1 ALB

* 1 ECS Cluster

* 2+ EC2 Instances

* 75 service types you deploy 

This is one of the biggest improvements to the platform we have ever made. A single ALB can replace countless ELBs, reducing the number of cloud services needed to run our apps.

## Cost Savings

This is also one of the biggest cost improvements to the platform we have ever made.

### Convox 1.0

* t2.small instance ($15 / mo) * 3 → $45 / mo

* 1 management API ELB → $18 / mo

* 1 management Dynamo DB table → $10 / mo

* ELB  * 5 apps → $90 / mo

Total: $163 / mo for 5 apps

### Convox 2.0

* t2.micro instance ($15 / mo) * 2→ $17 / mo

* 1 ALB → $16 / mo

* 1 management SimpleDB table → $1 / mo

Total: $34 / mo for 5 apps

## Try It Out

We’re hard at work finishing up and testing the new ALB architecture as we speak. If you’d like to help us test it, you can check out the [“Praxis” GitHub project](https://github.com/convox/praxis) and follow the [Getting Started Guide](https://github.com/convox/praxis/blob/master/docs/getting-started.md) to deploy your first app behind an ALB.

## Conclusion

AWS is constantly working hard to provide the component services we want to run our apps. With the host-based routing feature, it’s clear that ALB is now the best and cheapest way to use AWS.

Convox is constantly evaluating the entire landscape of cloud services so we can leverage the best ones for our platform. It took a bit of patience, but we are now confident that an ALB architecture is the way to go.

It gives us great pleasure to bring these massive architecture and price improvements to all our customers with no additional work on their part.

They get to stay focused on apps and leave the infrastructure work to us.

Have questions about ALB? Reach out on [Twitter](https://twitter.com/goconvox) or [Slack](http://invite.convox.com/).

--

Thanks to [Michael Warkentin](https://github.com/mwarkentin) for feedback!
