FROM ghcr.io/bxnlabs/containers/py-devtools:20250725.1_bee4041@sha256:bb79e0cabeedd22388b1fc52e40d73dc1f44058ebe9a5b50028064ee60f1e4a4 AS deps

# Dependencies
COPY pyproject.toml [p]oetry.lock ./
RUN --mount=type=cache,target=/var/cache/pypoetry,sharing=locked \
    POETRY_CACHE_DIR=/var/cache/pypoetry uvx poetry sync --only=main --no-root --extras=proxy --compile


FROM deps AS dev

# Install dev dependencies
RUN --mount=type=cache,target=/var/cache/pypoetry,sharing=locked \
    POETRY_CACHE_DIR=/var/cache/pypoetry uvx poetry sync --no-root --extras=proxy --compile

# Source
COPY README.md ./
COPY enterprise ./enterprise
COPY litellm ./litellm
RUN uvx poetry install --only-root --compile


FROM deps AS build

# Source
COPY README.md ./
COPY enterprise ./enterprise
COPY litellm ./litellm
RUN uv pip install --no-deps --compile .


FROM ghcr.io/bxnlabs/containers/py-base:20250730.1_8e63e9c@sha256:4324c95914670faff656d8b01eff87704dc960b5eb30d87d21334f8f34997b2a

COPY --from=build ${VIRTUAL_ENV}/ ${VIRTUAL_ENV}/
COPY bxn/config.yaml /etc/litellm/config.yaml
