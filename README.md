# Buildstep [![wercker status](https://app.wercker.com/status/c1c25f5d0ff8ea1823063000430114b2/s "wercker status")](https://app.wercker.com/project/bykey/c1c25f5d0ff8ea1823063000430114b2)

Heroku-style application builds using Docker and Buildpacks. Used by [Dokku](https://github.com/progrium/dokku) to make a mini-Heroku.

## Requirements

 * Docker
 * Git

## Supported Buildpacks

Buildpacks should generally just work, but many of them make assumptions about their environment. So Buildstep has a [list of officially supported buildpacks](https://github.com/gliderlabs/herokuish/tree/master/buildpacks) that are built-in and ready to be used.


## Building Buildstep

The buildstep script uses a buildstep base image that needs to be built. It must be created before
you can use the buildstep script. To create it, run:

    $ make build

This will create an image called `progrium/buildstep` that contains all supported buildpacks and the
builder script that will actually perform the build using the buildpacks.

## Building an App

Running the buildstep script will take an application tar via STDIN and the destination image name as an argument.
The tarball is deployed inside a new container based on `progrium/buildstep`, and the `builder` script is run in
the container. The container is then committed to create an image with the specified name.

    $ cat myapp.tar | ./buildstep myapp

If you didn't already have an application tar, you can create one on the fly.

    $ tar cC /path/to/your/app . | ./buildstep myapp

The resulting image contains your built app, ready to go. The builder script also parses the Procfile and produces
a starter script that takes a process type. Run your app with:

    $ docker run -d myapp /bin/bash -c "/start web"

## Custom Buildpacks

Custom buildpacks can be installed by committing a file in the root of your git repository named `.env`
This file should contain a line `export BUILDPACK_URL=<repository>` specifying the git repository providing
the buildpack.

If your buildpack needs extra packages these can be installed by the buildpack using [bin/compile](https://devcenter.heroku.com/articles/buildpack-api#bin-compile).

## License

MIT
