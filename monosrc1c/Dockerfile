ARG mono_version=6.14.1
FROM alpine:3.21 AS base
RUN apk --no-cache add libgcc ca-certificates

FROM base AS build
ARG mono_version
RUN apk --no-cache add curl git
RUN mkdir /build
WORKDIR /build
RUN git clone --depth 1 --branch mono-${mono_version} https://gitlab.winehq.org/mono/mono.git
WORKDIR /build/mono
RUN apk --no-cache add gcc g++ make python3 xz bash autoconf automake libtool musl-dev cmake linux-headers gdb strace linux-headers
RUN ./autogen.sh --prefix=/usr/local --with-mcs-docs=no --with-sigaltstack=no --disable-nls
RUN ln -s /usr/bin/python3 /usr/bin/python
RUN mkdir -p /usr/include/sys && touch /usr/include/sys/sysctl.h
RUN sed -i 's/HAVE_DECL_PTHREAD_MUTEXATTR_SETPROTOCOL/0/' mono/utils/mono-os-mutex.h
RUN make -j16
RUN make install
RUN apk del gcc g++ make python3 xz autoconf automake libtool musl-dev cmake linux-headers gdb strace linux-headers curl git bash
RUN rm -rf /usr/local/include && find /usr/local -name \*.a | xargs rm

FROM base AS runtime
COPY --from=build /usr/local/ /usr/local/
RUN cert-sync /etc/ssl/certs/ca-certificates.crt && mono --version