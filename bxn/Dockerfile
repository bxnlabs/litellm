FROM ghcr.io/bxnlabs/containers/py-devtools:20260721.1_3214b9e@sha256:813563ea33481343463a08a319feb7bc43d7d3d918b85904854dd39365ac266f AS deps

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


FROM ghcr.io/bxnlabs/containers/py-base:20260721.1_3214b9e@sha256:659c6d873fc0861954da11f87505d781ce0b9be0629b385ef3ca6cadd299fdf6

COPY --from=build ${VIRTUAL_ENV}/ ${VIRTUAL_ENV}/
COPY bxn/config.yaml /etc/litellm/config.yaml
