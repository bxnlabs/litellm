FROM ghcr.io/bxnlabs/containers/py-devtools:20250819.1_f63f77b@sha256:91405ce334c407fee491394b1979d868d452ba61371703e4ef322e89ffb63714 AS deps

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


FROM ghcr.io/bxnlabs/containers/py-base:20250819.1_f63f77b@sha256:df0c51ab3f8cd38b97529a6eb407c9bf47f58a2f5863dbd2e8fdc14c4bd08c58

COPY --from=build ${VIRTUAL_ENV}/ ${VIRTUAL_ENV}/
COPY bxn/config.yaml /etc/litellm/config.yaml
