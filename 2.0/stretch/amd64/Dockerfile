FROM microsoft/dotnet:2.0-sdk

LABEL maintainer "Philipp Gille"

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        libglib2.0-0 \
    && rm -rf /var/lib/apt/lists/*
