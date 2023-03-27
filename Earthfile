VERSION 0.7

FROM alpine:3.17

ARG HELM_VERSION="v3.11.2"

RUN apk add curl

RUN curl -fsSL https://get.helm.sh/helm-$HELM_VERSION-linux-amd64.tar.gz |  tar xzf - linux-amd64/helm \
    && install linux-amd64/helm /usr/local/bin/helm

WORKDIR /build

build:
    COPY charts charts
    RUN mkdir build

    FOR chart IN $(ls charts)
        RUN helm package charts/$chart -d ./build
        SAVE ARTIFACT build AS LOCAL build
    END

lint:
    COPY charts charts
    RUN mkdir build

    FOR chart IN $(ls charts)
        RUN helm lint charts/$chart
    END

release:
    ARG REPOSITORY="ghcr.io/paynejacob/charts"

    RUN --secret username=username --secret password=password helm registry login -u $username -p $password $REPOSITORY

    COPY +build/build build

    FOR chart IN $(ls build)
        RUN helm push build/$chart oci://$REPOSITORY
    END