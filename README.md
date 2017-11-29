**.NET Core SDK** Docker image with **libglib2.0-0**
====================================================

Docker image for building .NET Core applications including libglib2.0-0 for building executable [AppImage](https://appimage.org/) packages.

Supported tags and respective `Dockerfile` links
------------------------------------------------

- [`1.1.5-sdk-jessie`, `1.1.5-sdk`, `1.1-sdk`, `1-sdk` (1.1/jessie/Dockerfile)](https://github.com/philippgille/docker-dotnet-libglib/blob/master/1.1/jessie/Dockerfile)
- [`2.0.3-sdk-stretch`, `2.0-sdk-stretch`, `2.0.3-sdk`, `2.0-sdk`, `2-sdk`, `sdk`, `latest` (2.0/stretch/amd64/Dockerfile)](https://github.com/philippgille/docker-dotnet-libglib/blob/master/2.0/stretch/amd64/Dockerfile)
- [`2.0.3-sdk-jessie`, `2.0-sdk-jessie`, `2-sdk-jessie` (2.0/jessie/amd64/Dockerfile)](https://github.com/philippgille/docker-dotnet-libglib/blob/master/2.0/jessie/amd64/Dockerfile)
- [`2.1.0-preview1-sdk-stretch`, `2.1-sdk-stretch`, `2.1.0-preview1-sdk`, `2.1-sdk` (2.1/stretch/amd64/Dockerfile)](https://github.com/philippgille/docker-dotnet-libglib/blob/master/2.1/stretch/amd64/Dockerfile)
- [`2.1.0-preview1-sdk-jessie`, `2.1-sdk-jessie`, (2.1/jessie/amd64/Dockerfile)](https://github.com/philippgille/docker-dotnet-libglib/blob/master/2.1/jessie/amd64/Dockerfile)

> Note: The `2.1` images are preview versions based on the [`microsoft/dotnet-nightly`](https://hub.docker.com/r/microsoft/dotnet-nightly/) images, so don't use them in production.

Usage
-----

Assuming you have a build script that builds the .NET Core app as well as the AppImage:

```bash
docker run --rm \
    -v /path/to/source/:/app \
    -w /app \
    philippgille/dotnet-libglib:2.0-sdk \
    bash -c "./build.sh"
```

> Note: In the build script you can't just run the `appimagetool-x86_64.AppImage` to create your own AppImage. Instead, you need to extract that AppImage first and then you can run the `AppRun` executable to build your own AppImage. See the below *Details* section for more information.

Details
-------

When you build your .NET Core applications in a Docker container and also want to create executable AppImage packages for Linux, you need to install *libglib2.0-0* in your container. The reason for this is that AppImages require *FUSE* to be installed (see [AppImage/AppImageKit/wiki/FUSE](https://github.com/AppImage/AppImageKit/wiki/FUSE)), but FUSE doesn't work in Docker containers, so you can't just run the `appimagetool-x86_64.AppImage`. Instead, you need to extract the AppImage with `appimagetool-x86_64.AppImage --appimage-extract` and then you can run `./squashfs-root/AppRun /path/to/your/AppDir/ /path/to/your/app.AppImage`. *This extraction* is what requires libglib2.0-0. Now, if your build container is ephemeral (`--rm`), you have to install libglib2.0-0 *each time* you create a new container.

For example, in one of my other repositories I had a build script called `build-with-docker.sh`. Its content was:

```bash
#!/bin/bash

set -euxo pipefail

SCRIPTDIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"

docker run --rm \
    -v $SCRIPTDIR/../:/app \
    -w /app \
    microsoft/dotnet:2.0-sdk \
    bash -c "apt update && apt install -y --no-install-recommends libglib2.0-0 && ./scripts/build.sh"
```

Each time I ran `build-with-docker.sh`, the `apt update` and `apt install` commands took a lot of time and lead to unnecessary data traffic.

That's why I built this Docker image, where libglib2.0-0 is **already installed**. Nothing else gets installed, so it's the original [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) .NET Core SDK images, just with an added libglib2.0-0.
