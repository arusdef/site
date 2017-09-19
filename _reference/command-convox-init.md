---
title: 'Command: convox init'
---

To get your apps up and running on Convox as fast as possible, we offer the CLI command `convox init`. It inspects a codebase and then, depending on the language or framework detected, it generates files that allow Convox to run your app locally via `convox start` and in a Convox Rack via `convox deploy`. Convox provides this convenience by leveraging the open source [Heroku buildpacks](https://devcenter.heroku.com/articles/buildpacks).

## Supported Buildpacks

The `convox init` command has support for the following languages and frameworks:

* Ruby
* NodeJS
* Clojure
* Java
* Gradle
* Python
* Scala
* PHP
* Go

## Files generated

The `convox init` command will create and populate the following files, configuring your app to run with the appropriate buildpack when possible.

* docker-compose.yml
* Dockerfile
* .dockerignore
* .gitignore
* .env

## An Example

Here's what happens when you run `convox init` in a newly generated Rails codebase.

```
$ convox init
Updating convox/init... OK
Initializing a ruby app
Building app metadata. This could take a while... OK
Writing docker-compose.yml... OK
Writing Dockerfile... OK
Writing .dockerignore... OK
Writing .gitignore... EXISTS
Writing .env... OK

Try running `convox start`
```

If you are curious about how the buildpacks fit in, you can take a look at the generated Dockerfile, where a couple `RUN` commands clone the buildpack and then run its compile script. In this example, the [Heroku Ruby Buildpack](https://github.com/heroku/heroku-buildpack-ruby) is used.

```
FROM heroku/cedar

RUN cd /tmp && git clone https://github.com/heroku/heroku-buildpack-ruby
...
RUN output=$(/tmp/heroku-buildpack-ruby/bin/compile /app /tmp/cache) || echo $output
```
