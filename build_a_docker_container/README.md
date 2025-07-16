# Example of how to build  a docker container

source: https://github.com/spurin/cmatrix/blob/master/compilation_steps_in_alpine_container.md

## 1. Build container manually to test it works as expected

1. Source a small Docker image to build with - e.g. Alpine linux (usually < 10MB)
```
docker pull alpine
Using default tag: latest
latest: Pulling from library/alpine
Digest: sha256:8a1f59ffb675680d47db6337b49d22281a139e9d709335b492be023728e11715
Status: Image is up to date for alpine:latest
docker.io/library/alpine:latest

...

docker images | grep alpine
alpine                                    latest                                                                        8a1f59ffb675   6 weeks ago     13.3MB
```

2. Create a Dockerfile (see 'cmatrix' directory)

3. Build a container and test manual setup prior to automation (from directory containing the Dockerfile):
```
[+] Building 0.1s (5/5) FINISHED                                                                                  docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                              0.0s
 => => transferring dockerfile: 387B                                                                                              0.0s
 => [internal] load metadata for docker.io/library/alpine:latest                                                                  0.0s
 => [internal] load .dockerignore                                                                                                 0.0s
 => => transferring context: 2B                                                                                                   0.0s
 => [1/1] FROM docker.io/library/alpine:latest@sha256:8a1f59ffb675680d47db6337b49d22281a139e9d709335b492be023728e11715            0.0s
 => => resolve docker.io/library/alpine:latest@sha256:8a1f59ffb675680d47db6337b49d22281a139e9d709335b492be023728e11715            0.0s
 => exporting to image                                                                                                            0.0s
 => => exporting layers                                                                                                           0.0s
 => => exporting manifest sha256:3bc1c531ceb019ab7854f03f9c9eff56c3286a081ed8c9c36f1133953e982b34                                 0.0s
 => => exporting config sha256:732d455fdec457ccdc470bc387be476d8c12edf086997403666c99196edd0d2f                                   0.0s
 => => exporting attestation manifest sha256:9581c9dc2acb051aa8d3d28db17b437dc04d5444a9b625eb0c360b4f227c6be3                     0.0s
 => => exporting manifest list sha256:d15450ea1e7cdd24122a15cca8d7bb828a6c844859ce26b4f9bdfa197f7b07e4                            0.0s
 => => naming to docker.io/mmcarthy/cmatrix:latest                                                                                0.0s
 => => unpacking to docker.io/mmcarthy/cmatrix:latest                                                                             0.0s
```
4. Connect to container (Alpine doesn't include Bash. Use 'sh' instead):
```
docker run --rm -it mmcarthy/cmatrix sh
```
- -it = interactive terminal
- --rm = will automatically remove container on exit

5. Clone the  source code. Alpine distro won't contain Git by default:
```
apk update
apk add git
git clone https://github.com/spurin/cmatrix.git

# Change to the cmatrix source code and view the code
cd cmatrix/
```
- APK = Alpine Package Keeper

6. Also to be run inside the container:
```
# Install autoconf
apk add autoconf

# Install automake
apk add automake

# Install dependencies and create missing directories
apk add ncurses-dev ncurses-static
mkdir -p /usr/lib/kbd/consolefonts /usr/share/consolefonts

# Install compiler
apk add alpine-sdk

# Prepare compilation
autoreconf -i

# build a static binary
./configure LDFLAGS="-static"

# make to compile
make

# run
./cmatrix
```

## 2. Package steps in a Dockerfile

- v1: Included initial Dockerfile (Dockerfile_13_layers) that's far from optimized (too many layers and file is very large)
```
docker images
REPOSITORY                                TAG                                                                           IMAGE ID       CREATED              SIZE
mmcarthy/cmatrix                          latest                                                                        1f6a70f49634   About a minute ago   450MB
```
- v2: Optimized to 3 layers (Dockerfile_3_layers)
      - Still running as root user:
      ```
      docker run --rm -it mmcarthy/cmatrix whoami
      root
      ```
- v3: Dockerfile_multi: Multi-step build process to reduce container size
      - and --no-cache for apk to further  reduce container size
      - added non-root user (-D to disable password, -H to not create a home directory)
      ```
      $ docker images
      REPOSITORY                                TAG                                                                           IMAGE ID       CREATED          SIZE
      mmcarthy/cmatrix                          latest                                                                        3f2822acfcdd   13 minutes ago   20.1MB
      ```

- v4: Allow for parameters (Dockerfile)

## 3. Build container from Dockerfile
```
docker build . -t mmcarthy/cmatrix
```

# 4.Running the container

Default:
```
docker run --rm -it mmcarthy/cmatrix
```

Using parameters (v4):
```
 docker run --rm -it mmcarthy/cmatrix --help
 Usage: cmatrix -[abBcfhlsmVxk] [-u delay] [-C color] [-t tty] [-M message]
 -a: Asynchronous scroll
 -b: Bold characters on
 -B: All bold characters (overrides -b)
 -c: Use Japanese characters as seen in the original matrix. Requires appropriate fonts
 -f: Force the linux $TERM type to be on
 -l: Linux mode (uses matrix console font)
 -L: Lock mode (can be closed from another terminal)
 -o: Use old-style scrolling
 -h: Print usage and exit
 -n: No bold characters (overrides -b and -B, default)
 -s: "Screensaver" mode, exits on first keystroke
 -x: X window mode, use if your xterm is using mtx.pcf
 -V: Print version information and exit
 -M [message]: Prints your message in the center of the screen. Overrides -L's default message.
 -u delay (0 - 10, default 4): Screen update delay
 -C [color]: Use this color for matrix (default green)
 -r: rainbow mode
 -m: lambda mode
 -k: Characters change while scrolling. (Works without -o opt.)
 -t [tty]: Set tty to use
```

Run with custom parameters:
```
docker run --rm -it mmcarthy/cmatrix -ab -u 2 -C magenta
```
