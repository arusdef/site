---
title: Preparing an Application
order: 100
---

Convox infers information about your application from the following files:

#### Dockerfile

This file documents the steps necessary to build your application into an executable artifact.

#### docker-compose.yml

This file describes the structure of your application and how the pieces connect to each other.

## Intialization

If you don't yet have these files in your application, Convox can build them for you.

    $ convox init
    
Running this command will create a default `Dockerfile` and `docker-compose.yml` that you can use to get started.

These files are generated to take advantage of Heroku's open-source buildpacks. To read more about how that works, check out the [convox init reference](/docs/command-convox-init/).

If you'd rather write your own files or already use Docker in your application, refer to the [Docker Compose File](/docs/docker-compose-file/) and [Dockerfile](/docs/dockerfile/) references to see how they work with Convox.
