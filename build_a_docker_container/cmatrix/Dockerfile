
# Muti-stage build process

# Build and compile in first container, but only package what's needed in 'cmatrix' container

# build container image
FROM alpine AS cmatrixbuilder

WORKDIR cmatrix

RUN apk update --no-cache && \
    apk add git autoconf automake alpine-sdk ncurses-dev ncurses-static && \
    git clone https://github.com/spurin/cmatrix.git . && \
    autoreconf -i && \
    mkdir -p /usr/lib/kbd/consolefonts /usr/share/consolefonts && \
    ./configure LDFLAGS="-static" && \
    make

# cmatrix container image
FROM alpine

LABEL org.opencontainers.image.authors="Mick McCarthy <email@address.com>" \
      org.opencontainers.image.description="Container image for https://github.com/abishekvashok/cmatrix"

RUN apk update --no-cache && \
    apk add ncurses-terminfo-base && \
    adduser -g "Thomas Anderson" -s /usr/sbin/nologin -D -H thomas

COPY --from=cmatrixbuilder /cmatrix/cmatrix /cmatrix

USER thomas
ENTRYPOINT ["./cmatrix"]
CMD ["-b"]
