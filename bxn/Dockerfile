FROM ghcr.io/bxnlabs/containers/py-devtools:20251118.1_99b0b3f@sha256:474324e91045a066761dc77cd8398774860dc7aa9e3631e06b81869ac0516656 AS deps

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


FROM ghcr.io/bxnlabs/containers/py-base:20251120.1_8898cd3@sha256:c203c1bac6883ba78e976cf403233275aff67f921fb6956fcbbda7611fde4237

COPY --from=build ${VIRTUAL_ENV}/ ${VIRTUAL_ENV}/
COPY bxn/config.yaml /etc/litellm/config.yaml
