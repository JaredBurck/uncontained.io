# https://github.com/jojomi/docker-hugo
# Use Alpine Linux as our base image so that we minimize the overall size our final container, and minimize the surface area of packages that could be out of date.
FROM alpine:3.7@sha256:7df6db5aa61ae9480f52f0b3a06a140ab98d427f86d8d5de0bedab9b8df6b1c0
#FROM ruby:2.4.0-alpine

LABEL description="Docker container for building static sites with the Hugo static site generator."
LABEL maintainer="https://github.com/redhat-cop/uncontained.io"

ENV HUGO_VERSION=0.39 \
    USER_UID=1001 \
    HOME=/home/hugo

ADD https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_${HUGO_VERSION}_Linux-64bit.tar.gz /tmp
ADD root /

RUN tar -xf /tmp/hugo_${HUGO_VERSION}_Linux-64bit.tar.gz -C /tmp \
    && mkdir -p /usr/local/sbin \
    && mv /tmp/hugo /usr/local/sbin/hugo \
    && rm -rf /tmp/hugo_${HUGO_VERSION}_linux_amd64 \
    && rm -rf /tmp/hugo_${HUGO_VERSION}_Linux-64bit.tar.gz \
    && rm -rf /tmp/LICENSE.md \
    && rm -rf /tmp/README.md \
    && apk add --update asciidoctor curl git make gcc libc-dev libcurl python py-pygments \
    && apk upgrade \
    && apk add --no-cache ca-certificates \
    && chmod +x /usr/local/bin/entrypoint \
    && chmod +x /usr/local/bin/run \
    && chmod +x /usr/local/bin/user_setup \
    && /usr/local/bin/user_setup

EXPOSE 1313

USER ${USER_UID}

WORKDIR ${HOME}

ENTRYPOINT [ "/usr/local/bin/entrypoint" ]

CMD ["/usr/local/bin/run"]
