FROM microsoft/dotnet-nightly:2.1-sdk-jessie

LABEL maintainer "Philipp Gille"

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        libglib2.0-0 \
    && rm -rf /var/lib/apt/lists/*
