---
title: Health Checks
order: 350
---

By default, for a deployment to succeed, it must pass certain "health checks."

If the Process does not [expose ports](/docs/port-mapping) it is considered healthy if it starts and doesn't immediately exit or crash.
  
If the Process [exposes ports](/docs/port-mapping) is it considered healthy after it:
  
* Registers behind a load balancer
* Passes a network connection health check

Common causes for not passing health checks are:

* The cluster does not have sufficient memory or CPU resources available to start a new process
* The cluster does not have sufficient instances where a new process port is not already reserved by an older release
* A process crashes immediately after starting due to a problem in the latest code
* A process takes too long to initialize its server and therefore fails a network health check


#### Health Check Options

By default Convox will set up a `tcp` health check to your application, defining that a process must boot and pass the network health check in 3 seconds. If your app takes longer to boot, you may need to increase this to up to 60 seconds by adding a label by adding one or more of the following labels to your `docker-compose.yml` file.

```yaml
version: "2"
services:
  web:
    labels:
        - convox.health.path=/_health
        - convox.health.port=5000
        - convox.health.timeout=3
    ports:
      - "443:5000"
```
<table>
  <tr>
    <th>Label</th>
    <th>Notes</th>
  </tr>
  <tr>
    <td><code>path</code></td>
    <td>The endpoint the load balancer will use to determine the application's health.</td>
  </tr>
  <tr>
    <td><code>port</code></td>
    <td>This is the port that your container is set up to listen on, not the load balancer port.</td>
  </tr>
  <tr>
    <td><code>timeout</code></td>
    <td>The time in seconds after which no response means a failed health check (3 seconds by default; 60 seconds maximum). If the process fails 2 consecutive health checks it will be restarted. By default, the interval between health checks is this value plus 2.</td>
  </tr>
</table>

## See also

* [Rolling Updates](/docs/rolling-updates/)
* [Load Balancers](/docs/load-balancers/)
