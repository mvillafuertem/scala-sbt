#
# Scala and sbt Dockerfile
#
# https://github.com/hseeberger/scala-sbt
#

# Pull base image
ARG BASE_IMAGE_TAG
FROM openjdk:${BASE_IMAGE_TAG:-8u212-b04-jdk-stretch}

# Env variables
ARG SCALA_VERSION
ENV SCALA_VERSION ${SCALA_VERSION:-2.13.1}
ARG AMM_VERSION
ENV AMM_VERSION ${SBT_VERSION:-2.0.4}
ARG USER_ID
ENV USER_ID ${USER_ID:-1001}
ARG GROUP_ID
ENV GROUP_ID ${GROUP_ID:-1001}

# Install aliases
RUN \
  apt-get update && \
  apt-get upgrade && \
  apt-get install curl -y && \
  curl -L -o /root/.bash_aliases https://raw.githubusercontent.com/mvillafuertem/dotfiles/master/bash_aliases

# Install ammonite
RUN \
  curl -L -o /usr/share/amm https://github.com/lihaoyi/ammonite/releases/download/$AMM_VERSION/2.13-$AMM_VERSION-bootstrap && \
  chmod +x /usr/share/amm && \
  ln -s /usr/share/amm /usr/local/bin/amm


# Install Scala
## Piping curl directly in tar
RUN \
  curl -fsL https://downloads.typesafe.com/scala/$SCALA_VERSION/scala-$SCALA_VERSION.tgz | tar xfz - -C /usr/share && \
  mv /usr/share/scala-$SCALA_VERSION /usr/share/scala && \
  chown -R root:root /usr/share/scala && \
  chmod -R 755 /usr/share/scala && \
  ln -s /usr/share/scala/bin/scala /usr/local/bin/scala

# Add and use user ammuser
RUN groupadd --gid $GROUP_ID ammuser && useradd --gid $GROUP_ID --uid $USER_ID ammuser --shell /bin/bash
RUN chown -R ammuser:ammuser /opt
RUN mkdir /home/ammuser && chown -R ammuser:ammuser /home/ammuser
RUN mkdir /logs && chown -R ammuser:ammuser /logs

# Switch working directory back to root
## Users wanting to use this container as non-root should combine the two following arguments
## -u ammuser
## -w /home/ammuser
WORKDIR /root