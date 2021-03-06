# Supported tags and respective `Dockerfile` links

-	[`1.3.3`, `1.3` (*1.3/Dockerfile*)](https://github.com/docker-library/golang/blob/1a422afd7db928a821e97906ed27ed606e2f072a/1.3/Dockerfile)
-	[`1.3.3-onbuild`, `1.3-onbuild` (*1.3/onbuild/Dockerfile*)](https://github.com/docker-library/golang/blob/4d4b14164e50c089a09b9364697749dc7f764824/1.3/onbuild/Dockerfile)
-	[`1.3.3-cross`, `1.3-cross` (*1.3/cross/Dockerfile*)](https://github.com/docker-library/golang/blob/acc4ed5ba8dfad17bd484ac858950bc6a6f9acde/1.3/cross/Dockerfile)
-	[`1.3.3-wheezy`, `1.3-wheezy` (*1.3/wheezy/Dockerfile*)](https://github.com/docker-library/golang/blob/1a422afd7db928a821e97906ed27ed606e2f072a/1.3/wheezy/Dockerfile)
-	[`1.4.2`, `1.4`, `1`, `latest` (*1.4/Dockerfile*)](https://github.com/docker-library/golang/blob/1a422afd7db928a821e97906ed27ed606e2f072a/1.4/Dockerfile)
-	[`1.4.2-onbuild`, `1.4-onbuild`, `1-onbuild`, `onbuild` (*1.4/onbuild/Dockerfile*)](https://github.com/docker-library/golang/blob/396f40c6188614c7acd6d8299a0ea71030a056a6/1.4/onbuild/Dockerfile)
-	[`1.4.2-cross`, `1.4-cross`, `1-cross`, `cross` (*1.4/cross/Dockerfile*)](https://github.com/docker-library/golang/blob/396f40c6188614c7acd6d8299a0ea71030a056a6/1.4/cross/Dockerfile)
-	[`1.4.2-wheezy`, `1.4-wheezy`, `1-wheezy`, `wheezy` (*1.4/wheezy/Dockerfile*)](https://github.com/docker-library/golang/blob/1a422afd7db928a821e97906ed27ed606e2f072a/1.4/wheezy/Dockerfile)
-	[`1.5beta2`, `1.5` (*1.5/Dockerfile*)](https://github.com/docker-library/golang/blob/9a3a1ca455a97585ff74267835bea05006833866/1.5/Dockerfile)
-	[`1.5beta2-onbuild`, `1.5-onbuild` (*1.5/onbuild/Dockerfile*)](https://github.com/docker-library/golang/blob/9a3a1ca455a97585ff74267835bea05006833866/1.5/onbuild/Dockerfile)

For more information about this image and its history, please see the [relevant manifest file (`library/golang`)](https://github.com/docker-library/official-images/blob/master/library/golang) in the [`docker-library/official-images` GitHub repo](https://github.com/docker-library/official-images).

# What is Go?

Go (a.k.a., Golang) is a programming language first developed at Google. It is a statically-typed language with syntax loosely derived from C, but with additional features such as garbage collection, type safety, some dynamic-typing capabilities, additional built-in types (e.g., variable-length arrays and key-value maps), and a large standard library.

> [wikipedia.org/wiki/Go_(programming_language)](http://en.wikipedia.org/wiki/Go_%28programming_language%29)

![logo](https://raw.githubusercontent.com/docker-library/docs/master/golang/logo.png)

# How to use this image

## Start a Go instance in your app

The most straightforward way to use this image is to use a Go container as both the build and runtime environment. In your `Dockerfile`, writing something along the lines of the following will compile and run your project:

	FROM golang:1.3-onbuild

This image includes multiple `ONBUILD` triggers which should cover most applications. The build will `COPY . /usr/src/app`, `RUN go get -d -v`, and `RUN
go install -v`.

This image also includes the `CMD ["app"]` instruction which is the default command when running the image without arguments.

You can then build and run the Docker image:

	docker build -t my-golang-app .
	docker run -it --rm --name my-running-app my-golang-app

## Compile your app inside the Docker container

There may be occasions where it is not appropriate to run your app inside a container. To compile, but not run your app inside the Docker instance, you can write something like:

	docker run --rm -v "$PWD":/usr/src/myapp -w /usr/src/myapp golang:1.3 go build -v

This will add your current directory as a volume to the container, set the working directory to the volume, and run the command `go build` which will tell go to compile the project in the working directory and output the executable to `myapp`. Alternatively, if you have a `Makefile`, you can run the `make` command inside your container.

	docker run --rm -v "$PWD":/usr/src/myapp -w /usr/src/myapp golang:1.3 make

## Cross-compile your app inside the Docker container

If you need to compile your application for a platform other than `linux/amd64` (such as `windows/386`), this can be easily accomplished with the provided `cross` tags:

	docker run --rm -v "$PWD":/usr/src/myapp -w /usr/src/myapp -e GOOS=windows -e GOARCH=386 golang:1.3-cross go build -v

Alternatively, you can build for multiple platforms at once:

	docker run --rm -it -v "$PWD":/usr/src/myapp -w /usr/src/myapp golang:1.3-cross bash
	$ for GOOS in darwin linux; do
	>   for GOARCH in 386 amd64; do
	>     go build -v -o myapp-$GOOS-$GOARCH
	>   done
	> done

# Image Variants

The `golang` images come in many flavors, each designed for a specific use case.

## `golang:<version>`

This is the defacto image. If you are unsure about what your needs are, you probably want to use this one. It is designed to be used both as a throw away container (mount your source code and start the container to start your app), as well as the base to build other images off of. This tag is based off of [`buildpack-deps`](https://registry.hub.docker.com/_/buildpack-deps/). `buildpack-deps` is designed for the average user of docker who has many images on their system. It, by design, has a large number of extremely common Debian packages. This reduces the number of packages that images that derive from it need to install, thus reducing the overall size of all images on your system.

## `golang:onbuild`

This image makes building derivative images easier. For most use cases, creating a `Dockerfile` in the base of your project directory with the line `FROM golang:onbuild` will be enough to create a stand-alone image for your project.

While the `onbuild` variant is really useful for "getting off the ground running" (zero to Dockerized in a short period of time), it's not recommended for long-term usage within a project due to the lack of control over *when* the `ONBUILD` triggers fire (see also [`docker/docker#5714`](https://github.com/docker/docker/issues/5714), [`docker/docker#8240`](https://github.com/docker/docker/issues/8240), [`docker/docker#11917`](https://github.com/docker/docker/issues/11917)).

Once you've got a handle on how your project functions within Docker, you'll probably want to adjust your `Dockerfile` to inherit from a non-`onbuild` variant and copy the commands from the `onbuild` variant `Dockerfile` (moving the `ONBUILD` lines to the end and removing the `ONBUILD` keywords) into your own file so that you have tighter control over them and more transparency for yourself and others looking at your `Dockerfile` as to what it does. This also makes it easier to add additional requirements as time goes on (such as installing more packages before performing the previously-`ONBUILD` steps).

# License

View [license information](http://golang.org/LICENSE) for the software contained in this image.

# Supported Docker versions

This image is officially supported on Docker version 1.7.1.

Support for older versions (down to 1.0) is provided on a best-effort basis.

# User Feedback

## Documentation

Documentation for this image is stored in the [`golang/` directory](https://github.com/docker-library/docs/tree/master/golang) of the [`docker-library/docs` GitHub repo](https://github.com/docker-library/docs). Be sure to familiarize yourself with the [repository's `README.md` file](https://github.com/docker-library/docs/blob/master/README.md) before attempting a pull request.

## Issues

If you have any problems with or questions about this image, please contact us through a [GitHub issue](https://github.com/docker-library/golang/issues).

You can also reach many of the official image maintainers via the `#docker-library` IRC channel on [Freenode](https://freenode.net).

## Contributing

You are invited to contribute new features, fixes, or updates, large or small; we are always thrilled to receive pull requests, and do our best to process them as fast as we can.

Before you start to code, we recommend discussing your plans through a [GitHub issue](https://github.com/docker-library/golang/issues), especially for more ambitious contributions. This gives other contributors a chance to point you in the right direction, give you feedback on your design, and help you find out if someone else is working on the same thing.
