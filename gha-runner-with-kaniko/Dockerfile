FROM gcr.io/kaniko-project/executor:debug as kaniko

# From https://github.com/Azure-Samples/container-apps-ci-cd-runner-tutorial/blob/main/Dockerfile.github
FROM ghcr.io/actions/actions-runner:2.316.0
# for latest release, see https://github.com/actions/runner/releases

USER root

# install curl and jq
RUN apt-get update && apt-get install -y curl jq && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

COPY gha-runner-with-kaniko/entrypoint.sh ./entrypoint.sh
RUN chmod +x ./entrypoint.sh

# COPY --from=kaniko /kaniko/.config /kaniko/.config
COPY --from=kaniko /kaniko/.docker /kaniko/.docker
COPY --from=kaniko /kaniko/executor /kaniko/executor
COPY --from=kaniko /kaniko/ssl /kaniko/ssl
COPY --from=kaniko /kaniko/warmer /kaniko/warmer

ENV HOME /runner
ENV USER /runner
ENV PATH $PATH:/kaniko
ENV SSL_CERT_DIR /kaniko/ssl/certs
ENV DOCKER_CONFIG /kaniko/.docker/

USER runner

ENTRYPOINT ["./entrypoint.sh"]

# FROM gcr.io/kaniko-project/executor:latest

# WORKDIR /workspace

# COPY . /workspace/

# CMD [ "--dockerfile=Dockerfile.build", "--context=dir:///workspace", "--destination=891377098216.dkr.ecr.us-east-1.amazonaws.com/kaniko:built" ]


# ARG ARTIFACTORY_REGISTRY_DOCKER

# FROM $ARTIFACTORY_REGISTRY_DOCKER/gcr.io/kaniko-project/executor:v1.6.0-debug AS kaniko

# FROM scratch
# FROM alpine

# ENV PACKAGES \
#     git \
#     bash \
#     coreutils

# RUN apk --update-cache add $PACKAGES

# COPY --from=kaniko /kaniko/.config /kaniko/.config
# COPY --from=kaniko /kaniko/.docker /kaniko/.docker
# COPY --from=kaniko /kaniko/executor /kaniko/executor
# COPY --from=kaniko /kaniko/ssl /kaniko/ssl
# COPY --from=kaniko /kaniko/warmer /kaniko/warmer

# ENV HOME /root
# ENV USER /root
# ENV PATH $PATH:/kaniko
# ENV SSL_CERT_DIR /kaniko/ssl/certs
# ENV DOCKER_CONFIG /kaniko/.docker/