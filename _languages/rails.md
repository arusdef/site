---
title: Ruby (Rails)
---

Getting started with Rails on Convox is easy. Your app will need a `Dockerfile`, `docker-compose.yml`, and `.dockerignore` which you can either provide or generate using `convox init`.

## Getting Started

Go to your local source directory and run `convox init`:

    $ cd ~/myapp
    $ convox init
    Updating convox/init... OK
    Initializing a ruby app
    Building app metadata. This could take a while... OK
    Writing docker-compose.yml... OK
    Writing Dockerfile... OK
    Writing .dockerignore... OK
    Writing .gitignore... EXISTS
    Writing .env... OK

## What's Included

The Convox-generated `Dockerfile` and `docker-compose.yml` will give you a good starting configuration that leverages Heroku's open-source [ruby buildpack](https://github.com/heroku/heroku-buildpack-ruby) to build your application.

## Running the Application

See [Running Locally](/docs/running-locally) and [Deploying to Convox](/docs/deploying-to-convox).

