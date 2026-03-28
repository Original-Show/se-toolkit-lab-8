ARG REGISTRY_PREFIX_DOCKER_HUB
FROM ${REGISTRY_PREFIX_DOCKER_HUB}astral/uv:python3.14-bookworm-slim AS builder

WORKDIR /app

# Copy workspace config from the workspace context
COPY --from=workspace pyproject.toml uv.lock ./
# Copy nanobots own pyproject.toml
COPY pyproject.toml ./nanobot/
# Copy dependencies needed for nanobot from workspace context
COPY --from=workspace mcp/ ./mcp/
COPY --from=workspace nanobot-websocket-channel/ ./nanobot-websocket-channel/

# Use proxy for uv sync if provided via build-arg
ARG http_proxy
ARG https_proxy

# Sync dependencies for the nanobot package within the workspace
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --frozen --no-install-workspace --package nanobot

# Copy nanobot source
COPY . ./nanobot/

# Final sync to install the package itself
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --frozen --package nanobot

# Stage 2: Final
ARG REGISTRY_PREFIX_DOCKER_HUB
FROM ${REGISTRY_PREFIX_DOCKER_HUB}python:3.14.2-slim-bookworm

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
ENV LANG=C.UTF-8

# Setup a non-root user if not root
ARG APP_UID=1000
ARG APP_GID=1000
RUN if [ "$APP_UID" != "0" ]; then \
        groupadd --system --gid ${APP_GID} nonroot || true && \
        useradd --system --gid ${APP_GID} --uid ${APP_UID} --create-home nonroot || true; \
    fi

# Copy the application from the builder
COPY --from=builder /app /app

# Place executables in the environment at the front of the path
ENV PATH="/app/.venv/bin:$PATH"

WORKDIR /app/nanobot

CMD ["python", "entrypoint.py"]
