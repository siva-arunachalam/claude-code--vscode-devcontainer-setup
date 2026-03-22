# ── DEV CONTAINER ONLY ────────────────────────────────────────────────────────
# This image is for local development inside VS Code Dev Containers.
# It is NOT a production image — it includes dev tools (zsh, sudo, git, etc.)
# and a non-root `vscode` user that are not appropriate for production.
# For a production image, create a separate Dockerfile at the project root.
#
# ── Base image ────────────────────────────────────────────────────────────────
# python:3.13-slim-bookworm — recent stable Python on Debian 12 (slim = smaller
# image, bookworm = Debian 12). Change the tag here to pin a different version.
FROM python:3.13-slim-bookworm

# ── System dependencies ────────────────────────────────────────────────────────
# curl / git / zsh / sudo are useful in a dev container.
# build-essential is needed for packages with C extensions (e.g. psycopg2-binary).
# Clean up apt cache to keep the image lean.
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    git \
    sudo \
    zsh \
    && rm -rf /var/lib/apt/lists/*

# ── Non-root user (matches VS Code Dev Containers convention) ──────────────────
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=1000

RUN groupadd --gid ${USER_GID} ${USERNAME} \
    && useradd --uid ${USER_UID} --gid ${USER_GID} -m ${USERNAME} -s /bin/zsh \
    && echo "${USERNAME} ALL=(root) NOPASSWD:ALL" > /etc/sudoers.d/${USERNAME} \
    && chmod 0440 /etc/sudoers.d/${USERNAME}

# ── Python tooling ─────────────────────────────────────────────────────────────
# Upgrade pip and install uv (fast installer) and common dev tools.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir uv

# ── Application dependencies ───────────────────────────────────────────────────
# Copy only requirements first so Docker can cache this layer independently of
# your source code changes.
WORKDIR /workspace
COPY requirements.txt* ./

RUN if [ -f requirements.txt ]; then \
        pip install --no-cache-dir -r requirements.txt; \
    fi

# ── Claude config directory (persisted via a named volume in devcontainer.json) ─
RUN mkdir -p /home/${USERNAME}/.claude \
    && chown -R ${USERNAME}:${USERNAME} /home/${USERNAME}/.claude

# ── Switch to non-root user ────────────────────────────────────────────────────
USER ${USERNAME}

# ── Default shell for interactive sessions ─────────────────────────────────────
ENV SHELL=/bin/zsh
CMD ["zsh"]