# Example of how to build  a docker container

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
4. Connect to container (Alpine doesn't include Bash. Use 'sh' instead:
```
docker run --rm -it mmcarthy/cmatrix sh
```
- -it = interactive terminal
- --rm = will automatically remove container on exit

5. We need to clone source code. Alpine won't contain Git by default:
```
apk update
apk add git
git clone https://github.com/spurin/cmatrix.git
```
- APK = Alpine Package Keeper

6. Other things that are needed:
```
apk add autoconf
apk add automake
```
