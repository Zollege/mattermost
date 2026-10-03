# syntax=docker/dockerfile:1.7
# Compiles the patched Mattermost server and drops it into the official
# Team Edition image, which supplies the webapp, mmctl and bundled plugins.
# Built without the enterprise or sourceavailable tags, so none of the
# source-available licensed code is compiled in.
ARG MM_VERSION
ARG GO_VERSION

FROM golang:${GO_VERSION}-bookworm AS build
ARG BUILD_HASH=unknown
ARG BUILD_NUMBER=custom
WORKDIR /src/server
COPY server/ ./
RUN --mount=type=cache,target=/go/pkg/mod \
    --mount=type=cache,target=/root/.cache/go-build \
    go build -buildvcs=false -trimpath \
      -ldflags "-X github.com/mattermost/mattermost/server/public/model.BuildNumber=${BUILD_NUMBER} -X github.com/mattermost/mattermost/server/public/model.BuildHash=${BUILD_HASH} -X github.com/mattermost/mattermost/server/public/model.BuildEnterpriseReady=false" \
      -o /out/mostlymatter ./cmd/mostlymatter

FROM mattermost/mattermost-team-edition:${MM_VERSION}
COPY --from=build --chmod=0755 /out/mostlymatter /mattermost/bin/mattermost
