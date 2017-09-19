---
title: The Convox 2.0 Development Platform
date: 2017-07-25 00:00:00 Z
author: Noah Zoschke
twitter: nzoschke
---

As part of the ongoing [Convox 2.0 effort](https://docs.convox.com/), today I'm excited to offer a preview of the brand new Convox development platform.

These new tools turn your development computer into a platform that you interact with exactly like you do a cloud Platform-as-a-Service. This offers true development and production parity, and makes working on microservices easier than ever.

```console
$ cx apps create
creating docs: OK

$ cx start
build   | building: docs
build   | Step 1/4 : FROM golang:1.8.3
build   | Step 2/4 : RUN go get -v github.com/gohugoio/hugo
build   | Step 3/4 : WORKDIR /app
build   | Step 4/4 : COPY . .
build   | running: docker tag 9836064b convox/docs/web:BFGEZTOEXR
build   | build complete
convox  | promoting RIJBGELKDA
convox  | starting: convox.docs.service.web.1
convox  | starting: convox.docs.service.web.2
web     | syncing: . to /app
web     | Listening on port 1313

$ cx services
NAME  ENDPOINT
web   https://web.docs.convox
```

Here we created, built and deployed an app to our local machine. It is syncing code changes into the web containers and load balancing traffic between them. We can access it at a friendly URL:

![It works!](/assets/images/chrome-secure.png)*It works!*

Try it out yourself by following the [local development walk through](https://docs.convox.com/walkthroughs/local/)...

<!--more-->

## Development Platform

The [Twelve-Factor App](https://12factor.net) methods talk about keeping development and production as similar as possible, aka [dev / prod parity](https://12factor.net/dev-prod-parity).

Convox finally achieves this goal.

The new development platform offers commands like `cx apps create`, `cx deploy`, `cx env`, `cx run` and  to manage apps, environment and processes on your local computer. This experience is what we expect when managing apps in the cloud, and bringing it to the daily development workflow is transformational.

The environment also uses an architecture exactly like a production environment, including a Linux runtime, load balancing to multiple processes, SSL termination, DNS and more.

When you develop apps in this fashion, you need little to no code changes or tooling changes when going to production. You don't have to think about maintaining development docs, scripts and code hacks to make things work on a laptop. You get to focus on shipping changes to apps quickly and safely.

## Microservices

We work with lots of development teams and an extremely common challenge arises around microservices. When you have 5 small apps that interact with each other, running them all together on a laptop can be difficult.

The development platform design addresses this head on. You can now deploy all of these apps to run in the background of your computer with `cx deploy`, then bring one or more to the foreground to work on with `cx start`.

```
$ cx apps create api
creating api: OK

$ cx deploy
build complete
convox  | starting: convox.api.resource.database
convox  | starting: convox.api.service.web.1
convox  | starting: convox.api.service.web.2
convox  | starting: convox.api.service.worker.1

$ cx apps create dashboard
creating dashboard: OK

$ cx start
build complete
starting: convox.dashboard.service.web.1
starting: convox.dashboard.service.web.2
web     | syncing: . to /app
```

Here we run the "api" app and its database in the background, and the "dashboard" app in the foreground. The dashboard code can access the API through the static `https://web.api.convox` URL.

## Try it out

If you'd like to give it a try, check out the [local development tutorial](https://docs-staging.convox.com/walkthroughs/local/) for a full walkthrough.

Stay tuned for more announcements around Convox 2.0!