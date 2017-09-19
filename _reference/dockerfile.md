---
title: Dockerfile
---

The `Dockerfile` describes the steps you need to build and run your application.

* `FROM` defines the base image for your application.
* `COPY` moves files from the local directory into the image.
* `RUN` executes a command.
* `ENTRYPOINT` defines a command prefix that should be prepended to any command run on this image.
* `CMD` defines the default command to start application.
* `ARG` allows you to specify build-time variables.

### ARG

Convox respects the `ARG` Dockerfile directive, allowing you to specify build-time variables to be populated:
- from the `.env` file or local environment during the build step of `convox start`
- from `convox env` during `convox build` and `convox deploy`

This is useful for creating dynamic build environments, allowing you to do things like:
- building differently in production and development environments
- specifying environment variables that should be used during a build, but should not be present in the `Dockerfile` itself or in the resulting image.

<div class="block-callout block-show-callout type-warning" markdown="1">
Warning: It is not recommended to use build-time variables for passing secrets. Build-time variable values are visible to any user of the image with the `docker history` command.
</div>

You can declare a build argument in the `Dockerfile`, with either a default value or an empty one:

```
ARG BUNDLE_WITHOUT="development:test"
ARG RAILS_ENV
```

### Local builds

You can then send a value to the `ARG` during the local build by defining that variable in your `.env`:

```
$ cat .env
BUNDLE_WITHOUT="none"
```

or by setting it in your host environment, like this:

```
$ BUNDLE_WITHOUT="none" convox start
build    │ running: docker build --build-arg BUNDLE_WITHOUT="none" -f /home/aj/git/convox/convox-examples/rails/Dockerfile -t rails/web /home/aj/git/convox/convox-examples/rails
[...]
```

Note: Build arguments defined in `.env` supersede values in the host environment.

### Remote builds

You can send a value (or set an empty value, as below) to be applied during remote builds on your Rack by setting it with `convox env`:

```
$ convox env set BUNDLE_WITHOUT=none --promote
$ convox deploy
```

Now local builds via `convox start` will include development and testing dependencies. Production builds via `convox deploy` will not.

## See also

- [Builds](/docs/builds)
- [-\-build-arg](https://docs.docker.com/engine/reference/commandline/image_build/#/set-build-time-variables---build-arg)
- [Docker basics: Build your own image](https://docs.docker.com/engine/getstarted/step_four/)
- [Dockerfile reference: ARG](https://docs.docker.com/engine/reference/builder/#arg)
