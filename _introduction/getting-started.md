---
title: Getting Started
order: 400
---

Getting started with Convox is easy. The instructions below guide you through:

* [Signing up for a Convox account](https://convox.com/signup)
* Launching a Convox Rack, your private PaaS
* Installing the Convox CLI
* Deploying an example application

This guide takes around 30 minutes to go from zero to your first production deployment.

## Sign Up

First, [sign up for Convox Console](https://convox.com/signup), a web UI for managing your Organizations, Integrations and Racks.

### Create an Organization

Next, set up your organization. All the Racks and integrations you set up for this organization are only visible to you. Later you can invite members of your team and assign them [roles](/docs/access-control) that control what they can access.

### Connect an AWS Account

Next, click the **Setup** button then **Connect an AWS account**, and give Convox an AWS access key. This grants Convox access and permission to help manage resources in your AWS account.

See [AWS Integration](/docs/aws-integration) for more details.

## Launch your private PaaS

Next, click on the **Add Rack** button, followed by **Install New** in the top navigation bar. Enter a descriptive Rack name such as `production` if you plan to deploy production services, or `development` if this is for testing.

See [Installing a Rack](/docs/installing-a-rack) for more details.

## Install the Convox CLI

The `convox` command line tool offers:

* `convox start` - A single command to start your development environment
* `convox deploy` - A single command to deploy your application to a Rack
* `convox rack update` - A single command to deliver API and infrastructure improvements to your Rack

along with numerous other utilities that make building, configuring, scaling and securing your apps easy.

Click the **Setup** button then **Connect the Convox CLI** to get your API key.

Next, [install the Convox CLI](/docs/installation/) for your platform.

<pre id="install-mac">
$ curl -Ls https://convox.com/install/osx.zip > /tmp/convox.zip
$ unzip /tmp/convox.zip -d /usr/local/bin
</pre>

<pre id="install-linux" class="hidden" >
$ curl -Ls https://convox.com/install/linux.zip > /tmp/convox.zip
$ unzip /tmp/convox.zip -d /usr/local/bin
</pre>

<p id="install-windows" class="hidden">
On Windows, download and run <a href="https://dl.equinox.io/convox/convox/stable">Windows Installer</a>. Read the <a href="https://convox.com/docs/windows/">Windows Reference</a> for full details.
</p>

Finally, use the `convox login` command with your API key:

<pre id="login">
$ convox login
API Key:
Logged in successfully.
</pre>

See [Installing the Convox CLI](/docs/installation/) and [API Keys](/docs/api-keys/) for more details.

## Deploy to Convox

#### Clone a sample application

    $ git clone https://github.com/convox-examples/rails5
    $ cd rails5

#### Create an app in your Rack

Before deploying, create a new app in your Rack.

    $ convox apps create

<div class="block-callout block-show-callout type-info" markdown="1">
Convox infers the app name from the current directory name. If you'd like to specify another name use `convox apps create <name>` and specify `--app <name>` to each of the following commands.
</div>
    
Wait for the underlying components to be created by watching for the app status to change to `running`:

    $ convox apps info
    
Deploy the application

    $ convox deploy
    
Continue to watch `convox apps info` to find the load balancer hostnames for the application.

    $ convox apps info
    
<div class="block-callout block-show-callout type-info" markdown="1">
When a load balancer is first created it can take 5-10 minutes for its hostname to become available in DNS.
</div>

## Next Steps

Now that you've deployed your first application you can:

* Create a production database like [Postgres](/docs/postgresql/) and link it to your app
* [Prepare and deploy more of your own apps](/docs/preparing-an-application/)
* Grant your team members [access](/docs/access-control) to your organization
* Set up Continuous Delivery [Workflows](/docs/workflows)
* Install another Rack for isolated development or staging deployments

Or you can easily [uninstall everything](/docs/uninstalling-convox/) you just experimented with.

<script>
$(document).ready(function() {
  if (navigator.platform.indexOf('Win') > -1) {
    $('#install-windows').removeClass('hidden')
    $('#install-mac').addClass('hidden')
    $('#install-linux').addClass('hidden')
  }

  if (navigator.platform.indexOf('Linux') > -1) {
    $('#install-linux').removeClass('hidden')
    $('#install-mac').addClass('hidden')
    $('#install-windows').addClass('hidden')
  }
});
</script>
