FROM ghcr.io/bxnlabs/containers/py-devtools:20250220.1_6525465@sha256:749dc31d994c4ce9bd8f10304ca45970ce2c58cdcd8d84667011e9c84a41ef38 AS devtools

# Dependencies
COPY pyproject.toml [p]oetry.lock ./
RUN --mount=type=cache,target=/var/cache/pypoetry,sharing=locked \
    POETRY_CACHE_DIR=/var/cache/pypoetry uvx poetry sync --no-root --extras=proxy --compile

# Source
COPY README.md ./
COPY enterprise ./enterprise
COPY litellm ./litellm
RUN uvx poetry install --only-root --compile


FROM devtools AS build

# Prune dependencies to only what we need for release
RUN --mount=type=cache,target=/var/cache/pypoetry,sharing=locked \
    POETRY_CACHE_DIR=/var/cache/pypoetry uvx poetry sync --only=main --no-root --extras=proxy --compile
RUN uv pip install --no-deps --compile .


FROM ghcr.io/bxnlabs/containers/py-base:20250225.2_1ccc2a3@sha256:a7b52e1e5fb72d2b5943bd7ccd07405331936920b3bfb63de59bdf4a003784ae

COPY --from=build ${VIRTUAL_ENV}/ ${VIRTUAL_ENV}/
COPY bxn/config.yaml /etc/litellm/config.yaml
