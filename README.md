# heroku-buildpack-timestamps

[![Build Status](https://api.travis-ci.org/edmorley/heroku-buildpack-timestamps.svg?branch=master)](https://travis-ci.org/edmorley/heroku-buildpack-timestamps)

A buildpack that adds timestamps and durations to the logs output by Heroku builds,
as a lightweight means of timing/profiling slow builds.

## Usage

This buildpack does not replace your existing buildpack(s) but instead is added
alongside them. It must be placed **first** in the buildpack list.

To do this:

1. Ensure the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) is installed and up to date.
2. `cd <path/to/your/app>`
3. Check your app has an existing buildpack already configured, using:
   `heroku buildpacks`
4. Insert this buildpack before all others, using:
   `heroku buildpacks:add --index=1 edmorley/timestamps`

The next time your app is built, each line of the build logs will be prefixed with
timestamps, and a duration shown at the end of each buildpack's compile.

For more information on buildpacks and how to configure them, see the Heroku
[buildpack documentation](https://devcenter.heroku.com/articles/buildpacks).

## Example output

```
-----> Timestamp app detected
-----> Node.js app detected
2018-10-01 20:30:59        
2018-10-01 20:30:59 -----> Creating runtime environment
2018-10-01 20:30:59        
2018-10-01 20:30:59        NPM_CONFIG_LOGLEVEL=error
2018-10-01 20:30:59        YARN_PRODUCTION=true
2018-10-01 20:30:59        NODE_ENV=production
2018-10-01 20:30:59        NODE_MODULES_CACHE=true
2018-10-01 20:30:59        NODE_VERBOSE=false
2018-10-01 20:30:59        
2018-10-01 20:30:59 -----> Installing binaries
2018-10-01 20:30:59        engines.node (package.json):  10.11.0
2018-10-01 20:30:59        engines.npm (package.json):   unspecified (use default)
2018-10-01 20:30:59        engines.yarn (package.json):  1.11.1
2018-10-01 20:30:59        
2018-10-01 20:30:59        Resolving node version 10.11.0...
2018-10-01 20:31:00        Downloading and installing node 10.11.0...
2018-10-01 20:31:01        Using default npm version: 6.4.1
2018-10-01 20:31:01        Resolving yarn version 1.11.1...
2018-10-01 20:31:01        Downloading and installing yarn (1.11.1)...
2018-10-01 20:31:02        Installed yarn 1.11.1
2018-10-01 20:31:03        
2018-10-01 20:31:03 -----> Restoring cache
2018-10-01 20:31:03        - node_modules
2018-10-01 20:31:05        
2018-10-01 20:31:05 -----> Building dependencies
2018-10-01 20:31:06        Installing node modules (yarn.lock)
2018-10-01 20:31:07        yarn install v1.11.1
2018-10-01 20:31:08        success Already up-to-date.
2018-10-01 20:31:08        Done in 1.16s.
2018-10-01 20:31:08        
2018-10-01 20:31:08 -----> Caching build
2018-10-01 20:31:08        - node_modules
2018-10-01 20:31:15        
2018-10-01 20:31:15 -----> Pruning devDependencies
2018-10-01 20:31:15        Skipping because YARN_PRODUCTION is 'true'
2018-10-01 20:31:15        
2018-10-01 20:31:15 -----> Build succeeded!
2018-10-01 20:31:15 -----> Buildpack compile completed in 16s
```

## Limitations

This buildpack wraps later buildpack's compile steps, by temporarily shadowing
the real `bash` on `PATH`. As such, the other buildpack's compile steps must use
the shebang `#!/usr/bin/env bash` rather than `#!/bin/bash`, or their log output
will not be wrapped.

Official buildpacks known to be compatible:
* [heroku/clojure](https://github.com/heroku/heroku-buildpack-clojure)
* [heroku/gradle](https://github.com/heroku/heroku-buildpack-gradle)
* [heroku/java](https://github.com/heroku/heroku-buildpack-java)
* [heroku/nodejs](https://github.com/heroku/heroku-buildpack-nodejs)
* [heroku/php](https://github.com/heroku/heroku-buildpack-php)
* [heroku/python](https://github.com/heroku/heroku-buildpack-python)
* [heroku/ruby](https://github.com/heroku/heroku-buildpack-ruby)
* [heroku/scala](https://github.com/heroku/heroku-buildpack-scala)

Official buildpacks that are not yet compatible:
* [heroku/go](https://github.com/heroku/heroku-buildpack-go)

## Alternatives

There is [heroku-buildpack-timings](https://github.com/tt/heroku-buildpack-timings),
however it requires moving the buildpack configuration to the deprecated
[heroku-buildpack-multi](https://github.com/heroku/heroku-buildpack-multi),
rather than being able to use Heroku's native
[multi-buildpack support](https://devcenter.heroku.com/articles/using-multiple-buildpacks-for-an-app).
