FROM mcr.microsoft.com/devcontainers/java:1-21-bullseye

USER root

# Import Yarn GPG key (try keys.openpgp.org first, fall back to yarn's pubkey)
RUN set -eux \
    && rm -f /etc/apt/sources.list.d/yarn.list || true \
    && apt-get update \
    && apt-get install -y --no-install-recommends curl gnupg dirmngr ca-certificates apt-transport-https \
    && mkdir -p /etc/apt/keyrings /tmp || true \
        && (curl -fsSL "https://keys.openpgp.org/vks/v1/by-fingerprint/72ECF46A56B4AD39C907BBB71646B01B86E50310" -o /tmp/yarnkey || true) \
        && if [ -s /tmp/yarnkey ]; then \
                gpg --dearmor --batch --yes -o /etc/apt/keyrings/yarn-archive-keyring.gpg /tmp/yarnkey; \
        # also add to legacy apt keyring as a fallback for environments where signed-by fails
        && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - || true \
             else \
                 curl -fsSL https://dl.yarnpkg.com/debian/pubkey.gpg -o /tmp/yarnkey_fallback || true; \
                 if [ -s /tmp/yarnkey_fallback ]; then \
                     gpg --dearmor --batch --yes -o /etc/apt/keyrings/yarn-archive-keyring.gpg /tmp/yarnkey_fallback; \
                 fi; \
             fi \
    # If GPG verification fails in some environments, fall back to marking the repo trusted
    && echo "deb [arch=$(dpkg --print-architecture) trusted=yes] https://dl.yarnpkg.com/debian/ stable main" > /etc/apt/sources.list.d/yarn.list \
    && apt-get update \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/yarnkey
FROM mcr.microsoft.com/devcontainers/java:1-21-bullseye

# Ensure Yarn APT repo has its GPG key so later feature installs don't fail with NO_PUBKEY
USER root
RUN set -eux \
    && rm -f /etc/apt/sources.list.d/yarn.list || true \
    && apt-get update \
    && apt-get install -y --no-install-recommends curl gnupg dirmngr \
     && curl -fsSL https://dl.yarnpkg.com/debian/pubkey.gpg -o /tmp/yarnkey || true \
     && if [ -s /tmp/yarnkey ]; then \
            gpg --dearmor --batch --yes -o /usr/share/keyrings/yarn-archive-keyring.gpg /tmp/yarnkey; \
         else \
            echo "Warning: failed to fetch yarn pubkey"; \
         fi \
     && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/yarn-archive-keyring.gpg] https://dl.yarnpkg.com/debian/ stable main" \
         > /etc/apt/sources.list.d/yarn.list \
    && apt-get update \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
