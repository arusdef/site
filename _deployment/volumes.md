---
title: Volumes
order: 600
---

You can use Docker volumes to make data persist between runs of a given service's containers across restarts and instance replacements. This is useful for applications like WordPress or Jenkins that need to store data on the filesystem.

<div class="block-callout block-show-callout type-warning" markdown="1">
<b>Note:</b> It's not currently possible to share volumes between containers of different services in the same app.
</div>

## Shared Filesystem

Convox uses a network filesystem backed by [Amazon EFS](https://aws.amazon.com/efs/) that is shared among all of the instances in your Rack.

<div class="block-callout block-show-callout type-warning" markdown="1">
### Supported Regions

Shared volumes are only possible in AWS regions that support Elastic File System (EFS). As of the time of this writing, the following regions are supported:

- EU West 1 (Ireland)
- US East 1 (N. Virginia)
- US East 2 (Ohio)
- US West 2 (Oregon)

To check whether EFS is available in a given region, select your region from the dropdown in [the EFS section in the AWS console](https://console.aws.amazon.com/efs/home).

Volumes used in regions without EFS will not persist or be shared across instances.
</div>

## Sharing Data

You can mount a shared volume by specifying a container path in the `volumes:` section of your `docker-compose.yml`: 

```
services:
  web:
    volumes:
      - /my/shared/data
```

If you specify your volume path this way, Convox will persist data on your Rack instances in an application-namespaced path under `/volumes`.

<div class="block-callout block-show-callout type-info" markdown="1">
  By default, volumes are located at `/volumes/<rack>-<app>/<service>/*`.
  You can also specify the volume in the more explicit `host:container` format, e.g. `/host/path:/container/path`. This allows you to set exactly where on the host instance to persist the data.
</div>

## Permissions

It's not currently possible to mount EFS volumes as read-only with Convox. If this option would be useful to you, feel free to open a support ticket so we can track it as a feature request.

## Persistence

When your `docker-compose.yml` declares a volume with the single-path format, the volume will be persisted across container runs of the same process type. The path where these volumes persist on the Docker host differs depending on whether you are running your apps locally or deployed in a Rack.

### Persistence for local containers

Local apps running via `convox start` mount [their volumes](/docs/docker-compose-file/#volumes) from the local file system. More specifically, the volumes are persisted at `~/.convox/volumes/`.

The path structure looks like this:

```
~/.convox/volumes/<app-name>/<service-name>/<container-path>
~/.convox/volumes/example-app/web/my/shared/data
```

If a volume seems corrupted or doesn't behave as expected (and you're sure you don't need the data), you can try deleting the directory above.

### Persistence for deployed containers

Deployed apps running in a Rack mount their volumes from the file system of EC2 instances in the Convox cluster. More specifically, the volumes are persisted at `/volumes/` on the EC2 instances, which have mounted that volume from EFS to share it across the cluster. Going back in the other direction, the persisted volume is mounted from EFS to cluster instances, and then from those cluster instances into the relevant containers.

The path structure looks like this:

```
/volumes/<rack-name>-<app-name>/<service-name>/<container-path>
/volumes/production-example-app/web/my/shared/data
```

## Example: WordPress

[WordPress](/docs/wordpress) is a popular PHP blogging platform. It expects a persistent filesystem for storing themes, plugins, and media uploads. You can persist this data by specifying a shared volume at `/var/www/html`:

```
services:
  web:
    image: wordpress:4.5.2-apache
    ports:
      - 80:80
    volumes:
      - /var/www/html
```

This configuration will work with both `convox start` and `convox deploy`. Files written to `/var/www/html` will be persisted on the Docker host.

## Cleanup

Occasionally volumes become corrupted. If this happens, note that you can delete volumes on a Rack via `convox instances ssh`, e.g.:

```
$ convox instances ssh <instance-id> "sudo rm -rf /volumes/myrack-myapp/myservice/myvolume"
```

You can get the `instance-id` via `convox instances`.

Note that you need to keyroll at least once before using `convox instances ssh`. For details, see [SSH keyroll](/docs/ssh-keyroll/).
