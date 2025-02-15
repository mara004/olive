# Docker Images

Olive uses Docker containers for continuous integration on Linux.
No Docker images are involved for the Windows and macOS CI.

## Overview

`ci-common` is the shared build image with GCC, Clang and packages that are
needed by most dependent images. It is used to compile Olive's dependencies
in a controlled environment. The final CI image `ci-olive` is assembled from
images maintained by the Olive team as well as from
[aswf-docker](https://github.com/AcademySoftwareFoundation/aswf-docker/).

Dependency hierarchy:

1. `ci-common`
2. `ci-otio`, `ci-crashpad`, `ci-ffmpeg`, `ci-ocio`
3. `ci-olive`

## Usage

Pull images from [Docker Hub](https://hub.docker.com/u/olivevideoeditor):

```
docker pull olivevideoeditor/ci-common:2
docker pull olivevideoeditor/ci-package-otio:0.14.1
docker pull olivevideoeditor/ci-package-crashpad
docker pull olivevideoeditor/ci-package-ffmpeg:5.0
docker pull olivevideoeditor/ci-package-ocio:2022-2.1.1
docker pull olivevideoeditor/ci-olive:2022.1
```

Use `ci-olive` image as local build container, by mounting working copy at
`~/olive` into guest system at `/opt/olive/olive`:

```bash
docker run --rm -it -v ~/olive:/opt/olive/olive olivevideoeditor/ci-olive:2022.1
mkdir build
cd build
cmake .. -G Ninja
cmake --build .
```

Rebuild all images locally:

```
cd docker
docker build -t olivevideoeditor/ci-common:2 -f ci-common/Dockerfile .
docker build -t olivevideoeditor/ci-package-otio:0.14.1 -f ci-otio/Dockerfile .
docker build -t olivevideoeditor/ci-package-crashpad -f ci-crashpad/Dockerfile .
docker build -t olivevideoeditor/ci-package-ffmpeg:5.0 -f ci-ffmpeg/Dockerfile .
docker build -t olivevideoeditor/ci-package-ocio:2022-2.1.1 -f ci-ocio/Dockerfile .
docker build -t olivevideoeditor/ci-olive:2022.1 -f ci-olive/Dockerfile .
```

Note that `2022` in `ci-olive:2022.1` stands for the
[VFX Reference Platform](http://vfxplatform.com/) calendar year and `1` for the
build image revision (should be incremented each time a new image is published).

Publish images:

```
docker push olivevideoeditor/ci-common:2
docker push olivevideoeditor/ci-package-otio:0.14.1
docker push olivevideoeditor/ci-package-crashpad
docker push olivevideoeditor/ci-package-ffmpeg:5.0
docker push olivevideoeditor/ci-package-ocio:2022-2.1.1
docker push olivevideoeditor/ci-olive:2022.1
```
