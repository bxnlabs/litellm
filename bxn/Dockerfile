FROM ghcr.io/bxnlabs/containers/py-devtools:20251113.1_9234ce3@sha256:329a58140757b26b77bd6f7575ce13407b6cb42fe97e988e39e25214d4e7f6e7 AS deps

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


FROM ghcr.io/bxnlabs/containers/py-base:20251118.1_99b0b3f@sha256:fa7ed1976b75fcf2b1c0ba005ae6df6a2c7f782d76aa0889033bd266aa43ea94

COPY --from=build ${VIRTUAL_ENV}/ ${VIRTUAL_ENV}/
COPY bxn/config.yaml /etc/litellm/config.yaml
