ARG REGISTRY_PREFIX_GHCR
ARG REGISTRY_PREFIX_DOCKER_HUB

FROM ${REGISTRY_PREFIX_GHCR}cirruslabs/flutter:3.41.2 AS builder

WORKDIR /app

# Mirrors for faster download in restricted networks
ENV PUB_HOSTED_URL=https://pub.flutter-io.cn
ENV FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn

# Flutter warns about running as root, but this is safe in a build-only container.
# Resolve dependencies first for layer caching.
COPY pubspec.yaml pubspec.lock ./
# Use proxy for pub get if provided via build-arg
ARG http_proxy
ARG https_proxy
RUN --mount=type=cache,target=/root/.pub-cache flutter pub get

COPY . .
RUN --mount=type=cache,target=/app/.dart_tool \
    flutter build web --base-href /flutter/

FROM ${REGISTRY_PREFIX_DOCKER_HUB}alpine:3.21
COPY --from=builder /app/build/web /flutter-web
CMD ["cp", "-r", "/flutter-web/.", "/output/"]
