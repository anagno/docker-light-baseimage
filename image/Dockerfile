FROM golang:1.11-stretch as builder

WORKDIR /go/src/github.com/cloudflare/cfssl

# Download cfssl
RUN \
    curl -o \
        /tmp/cfssl.tar.gz \
        -SL https://github.com/cloudflare/cfssl/archive/1.3.3.tar.gz && \
    tar -xf /tmp/cfssl.tar.gz -C /go/src/github.com/cloudflare/cfssl --strip-components=1

# Build the cfssl. We are not using the make file in the source code
# because we need to activate the verbose mode in the compiling
# otherwise travis will think that the procedure has stalled
# and cancel it.
RUN cd /go/src/github.com/cloudflare/cfssl && \
    CGO_ENABLED=0 GOOS=linux go build -v -o bin/cfssl ./cmd/cfssl && \
    CGO_ENABLED=0 GOOS=linux go build -v -o bin/cfssljson ./cmd/cfssljson


FROM debian:buster-slim

COPY --from=builder /go/src/github.com/cloudflare/cfssl/bin/cfssl /usr/sbin/cfssl
COPY --from=builder /go/src/github.com/cloudflare/cfssl/bin/cfssljson /usr/sbin/cfssljson

COPY . /container
RUN /container/build.sh

ENV LANG="en_US.UTF-8" \
    LANGUAGE="en_US:en" \
    LC_ALL="en_US.UTF-8"

ENTRYPOINT ["/container/tool/run"]
