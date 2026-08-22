FROM alpine:3.22

RUN apk add --no-cache \
    curl \
    kcat \
    jq \
    bash

RUN ln -s /usr/bin/kcat /usr/local/bin/kafkacat

ARG WORKSPACE_HOME=/workspace
WORKDIR $WORKSPACE_HOME

COPY source $WORKSPACE_HOME/source
COPY sink $WORKSPACE_HOME/sink

CMD ["bash"]