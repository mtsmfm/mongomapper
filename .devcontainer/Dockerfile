FROM ruby:2.7.2

ARG USERNAME=app
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN apt-get update && apt-get install -y socat

ENV ENTRYKIT_VERSION 0.4.0

RUN wget https://github.com/progrium/entrykit/releases/download/v${ENTRYKIT_VERSION}/entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz \
  && tar -xvzf entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz \
  && rm entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz \
  && mv entrykit /bin/entrykit \
  && chmod +x /bin/entrykit \
  && entrykit --symlink

RUN groupadd --gid $USER_GID $USERNAME \
  && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
  && apt-get update \
  && apt-get install -y sudo \
  && echo "$USERNAME ALL=(root) NOPASSWD:ALL" > /etc/sudoers.d/$USERNAME \
  && chmod 0440 /etc/sudoers.d/$USERNAME

ENV BUNDLE_PATH=/app/vendor/bundle
RUN mkdir -p /app $BUNDLE_PATH /home/app/.config
RUN chown -R $USERNAME /app $BUNDLE_PATH /home/app

ENV SHELL=/bin/bash

USER $USERNAME

WORKDIR /app

ENTRYPOINT ["codep", "socat TCP-LISTEN:27017,fork TCP:mongo:27017", "--"]
