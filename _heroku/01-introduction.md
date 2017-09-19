---
title: Introduction
permalink: "/guide/heroku/"
---

Heroku and Convox are both Platforms-as-a-Service designed around [The Twelve-Factor App methodologies](https://12factor.net/).

Convox is open-source and built entirely on AWS cloud services. It enables you to deploy your apps to your own AWS account for maximum control. Depending on your application and engineering team, migrating an app to Convox could unlock security, reliability, performance, cost and/or operational improvements.

Many parts of the Heroku platform map directly to Convox:

<table>
  <thead>
    <tr>
      <th></th>
      <th>Heroku</th>
      <th>Convox</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Codebase</td>
      <td>Git Repo</td>
      <td>Git Repo</td>
    </tr>
    <tr>
      <td>Config</td>
      <td>
        <pre>$ heroku config</pre>
      </td>
      <td>
        <pre>$ convox env</pre>
      </td>
    </tr>
    <tr>
      <td>Build / Release / Run</td>
      <td>
        <pre>$ git push heroku master</pre>
        <pre>$ heroku releases</pre>
      </td>
      <td>
        <pre>$ convox deploy</pre>
        <pre>$ convox releases</pre>
      </td>
    </tr>
    <tr>
      <td>Processes</td>
      <td>
        <pre>$ heroku ps</pre>
      </td>
      <td>
        <pre>$ convox ps</pre>
      </td>
    </tr>
    <tr>
      <td>Concurrency</td>
      <td>
        <pre>$ heroku scale</pre>
      </td>
      <td>
        <pre>$ convox scale</pre>
      </td>
    </tr>
    <tr>
      <td>Databases</td>
      <td>
        <pre>$ heroku addons</pre>
      </td>
      <td>
        <pre>$ convox resources</pre>
      </td>
    </tr>
    <tr>
      <td>Logs</td>
      <td>
        <pre>$ heroku logs</pre>
      </td>
      <td>
        <pre>$ convox logs</pre>
      </td>
    </tr>
    <tr>
      <td>Admin Processes</td>
      <td>
        <pre>$ heroku run</pre>
      </td>
      <td>
        <pre>$ convox run</pre>
        <pre>$ convox exec</pre>
      </td>
    </tr>
  </tbody>
</table>

Other parts are similar, but represent more significant changes to your apps:

<table>
  <thead>
    <tr>
      <th></th>
      <th>Heroku</th>
      <th>Convox</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Manifest</td>
      <td>
        <div>Procfile</div>
      </td>
      <td>
        <div>docker-compose.yml</div>
      </td>
    </tr>
    <tr>
      <td>Ports</td>
      <td>
        <div>Port Assignment</div>
        <div>$PORT environment variable</div>
      </td>
      <td>
        <div>Port Mapping</div>
        <pre>ports:
  - 80:8000
</pre>
      </td>
    </tr>
    <tr>
      <td>Development</td>
      <td>
        <pre>$ heroku local</pre>
        <div>Homebrew</div>
      </td>
      <td>
        <pre>$ convox start</pre>
        <div>Docker</div>
      </td>
    </tr>
    <tr>
      <td>Production</td>
      <td>
        <div>Cedar Stack Image</div>
        <div>PaaS</div>
      </td>
      <td>
        <div>Docker Base Image</div>
        <div>IaaS / AWS</div>
      </td>
    </tr>
  </tbody>
</table>

This guide explains the differences of the platforms, and walks you through the steps required to migrate an app.

## Prerequisites

Let's start with a simple Python and Postgres Heroku app. The codebase is in [a GitHub repo](https://github.com/heroku/python-getting-started.git).

First, let's create an empty Convox app:

<pre class="terminal">
<span class="command">convox apps create</span>
Creating app python-getting-started... CREATING
</pre>

If you don't have Convox set up in your AWS account, refer to the [Getting Started](http://localhost/docs/getting-started/) doc.

Convox uses Heroku's buildpacks to build images for apps. The `convox init` command will generate a Dockerfile to describe how to build your app with the appropriate buildpack.

To get started, run:

<pre class="terminal">
<span class="command">convox init</span>
Updating convox/init... OK
Initializing a python app
Building app metadata. This could take a while... OK
Writing docker-compose.yml... OK
Writing Dockerfile... OK
Writing .dockerignore... OK
</pre>

## Manifest

<table class="vs">
  <thead>
    <tr>
      <th>Heroku</th>
      <th>Convox</th>
    </tr>
  </thead>
  <tr>
    <td>
      Heroku uses a <b>Procfile</b> to define process types and commands.
    </td>
    <td>
      Convox uses a <b>docker-compose.yml file</b> to define service types and commands.
    </td>
  </tr>
</table>

The previous `convox init` command generated a `docker-compose.yml` file for our Convox app. Every process type and command in `Procfile` is added as a service and command to `docker-compose.yml`.

<pre class="file yaml" title="docker-compose.yml">
version: "2"
services:
  database:
    image: convox/postgres
    ports:
    - 5432/tcp
    volumes:
    - /var/lib/postgresql/data
  web:
    build:
      context: .
    command: gunicorn gettingstarted.wsgi --log-file -
    environment:
    - PORT=4001
    labels:
      convox.port.443.protocol: tls
    links:
    - database
    ports:
    - 80:4001/tcp
    - 443:4001/tcp
</pre>

## Deploying

Now our app can be deployed to Convox:

<pre class="terminal">
<span class="command">convox deploy</span>
$ convox deploy
Deploying python-getting-started
Creating tarball... OK
Uploading: 9.56 KB / 9.39 KB [===========================] 101.84 % 0s
Starting build... OK
running: docker build -f /tmp/407003531/Dockerfile -t python-getting-started/web /tmp/407003531
Sending build context to Docker daemon 46.08 kB
Step 1 : FROM heroku/cedar
...
Successfully built d9c7f075f169

running: docker tag 
running: docker push 

web.BXMCXIIFZSQ: digest: sha256:62815bd42414f508c7ce326a42dbc7df484beaf175ee5581ef7c0fda36dad21a size: 1994
Release: RRYXNKQRAPD
Promoting RRYXNKQRAPD... UPDATING

<span class="command">convox apps info</span>
$ convox apps info python-getting-started
Name       python-getting-started
Status     running
Release    RRYXNKQRAPD
Processes  database web
Endpoints  internal-python-getting-started-AT3UMZ4-i-500316325.us-east-1.elb.amazonaws.com:5432 (database)
           python-getting-started-w-IC7MLNI-1004687141.us-east-1.elb.amazonaws.com:80 (web)
           python-getting-started-w-IC7MLNI-1004687141.us-east-1.elb.amazonaws.com:443 (web)
</pre>

Sure enough, our app is available at the endpoint.

## Databases

<table class="vs">
  <thead>
    <tr>
      <th>Heroku</th>
      <th>Convox</th>
    </tr>
  </thead>
  <tr>
    <td>
      Heroku uses <b>addons</b> to provision database services, and sets app <b>config</b> with service connection information.
    </td>
    <td>
      Convox uses <b><a href="/docs/about-resources/">resources</a></b> to provision database services, and sets app <b><a href="/docs/environment/">environment</a></b> with service connection information.
    </td>
  </tr>
</table>

The previous step deployed a database container. A containerized database is good for development and verification purposes, but in production we'll want a "real" hosted database. There are two strategies.

#### Reuse Heroku Addons

Both Heroku and Convox run in the AWS cloud. As long as the Heroku addons and Convox app are in the same region, access between them is fast. So the simplest strategy is to connect the Convox app to the Heroku addons by copying over the config.

<pre class="terminal">
# copy Heroku config to Convox app environment

<span class="command">heroku config -s | convox env set</span>
Updating environment... OK
To deploy these changes run `convox releases promote RWGFPGSELVA`

<span class="command">convox releases promote RWGFPGSELVA</span>
Promoting RWGFPGSELVA... OK

# verify the Convox environment, database connection and data

<span class="command">convox run web python manage.py migrate</span>
Running migrations:
  No migrations to apply.
</pre>

#### Migrate Data

The other strategy is to create new databases and migrate data. This has the security advantage of moving your database and data into a VPC that is not acessable to anything but your Convox app.

To do this, we will backup the Heroku database, and restore it into a new, private Postgres database.

<pre class="terminal">
# create the Convox resource and open a local proxy

<span class="command">convox resources create postgres</span>
Creating postgres-2098 (postgres)... CREATING

<span class="command">convox resources proxy postgres-2098</span>
proxying 127.0.0.1:5432 to dev-east-postgres-2098.cyzckls48pd3.us-east-1.rds.amazonaws.com:5432

# stop writing data and capture a backup

<span class="command">heroku maintenance:on</span>

<span class="command">heroku pg:backups:capture</span>
Backing up DATABASE to b001... done

<span class="command">heroku pg:backups:download</span>
Getting backup from ⬢ pure-basin-53177... done, #1
Downloading latest.dump...

# restore the Convox database from the backup

<span class="command">pg_restore -Ov -d app -h localhost -n public -U postgres latest.dump</span>
pg_restore: connecting to database for restore
Password: 
pg_restore: creating TABLE "public.auth_group"
pg_restore: creating SEQUENCE "public.auth_group_id_seq"
...
</pre>

### Scale Down Database Containers

Now that we've got a real database set up, we'll want to scale down the database container so that we're not running unnecessary resources. Scaling the container to a count of -1 will also deprovision its load balancer, saving us money on our AWS bill.

<pre class="terminal">
<span class="command">convox scale database --count=1</span>
</pre>

### Next Steps

Our first deploy to Convox is just the beginning. From here you can explore:

* [Developing the app with `convox start`](/docs/running-locally/)
* [Configuring HTTPS, TCP and private load balancing](/docs/load-balancers/)
* [Attaching to a running container](/docs/one-off-commands/)
* [SSHing to an instance](/docs/debugging/)
* [Automating builds, tests and releases](/docs/workflows/)
