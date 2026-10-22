ARG BASE_TAG=latest
FROM node:22.23.4-bookworm-slim@sha256:de170b39439eb0e5a3444ad7d60b015614a381e10fecae2e30ec9e735122f38d AS patched-node

FROM browseruse/base-system:${BASE_TAG}

# Playwright wheels still bundle vulnerable Node 24.21.0.
COPY --from=patched-node /usr/local/bin/node /usr/local/bin/security-node
COPY scripts/playwright-node /usr/local/bin/playwright-node
ENV PLAYWRIGHT_NODEJS_PATH=/usr/local/bin/playwright-node
RUN apt-get update && apt-get install -y --no-install-recommends libstdc++6 \
    && rm -rf /var/lib/apt/lists/* \
    && chmod 755 /usr/local/bin/playwright-node \
    && /usr/local/bin/playwright-node --version


WORKDIR /tmp
COPY pyproject.toml ./

# Install chromium browser using temporary playwright installation
RUN --mount=type=cache,target=/root/.cache,sharing=locked \
    echo "Installing chromium browser via temporary playwright..." && \
    pip install --no-cache-dir playwright==1.64.0 && \
    PLAYWRIGHT_BROWSERS_PATH=/opt/playwright playwright install chromium --with-deps --no-shell && \
    ln -s /opt/playwright/chromium-*/chrome-linux/chrome /usr/bin/chromium-browser && \
    chmod -R 755 /opt/playwright && \
    pip uninstall playwright -y && \
    rm -f pyproject.toml
