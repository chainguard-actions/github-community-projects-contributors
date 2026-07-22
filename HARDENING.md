<!-- markdownlint-disable -->

# Hardening Report: github-community-projects--contributors/v2.0.14

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github-community-projects--contributors/v2.0.14** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses a Docker image referenced by a mutable tag (:v2) instead of an immutable SHA digest. The reference `docker://ghcr.io/github-community-projects/contributors:v2` can be silently replaced by a malicious image without any change to the action source, enabling a supply-chain attack. It should be replaced with a SHA-pinned reference such as `ghcr.io/github-community-projects/contributors@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:7`

### script-injection (severity: high)

python-ci.yml directly interpolates `${{ matrix.python-version }}` inside two `run:` shell command strings (sub-rule a). The matrix context is a workflow-controllable expression that flows through YAML template substitution before the shell sees it, allowing shell metacharacters to be injected. Offending lines: `run: uv python install ${{ matrix.python-version }}` and `run: uv sync --frozen --python ${{ matrix.python-version }}`. These should be routed through an `env:` variable and then referenced as a double-quoted shell variable (e.g., `"$PYTHON_VERSION"`).

Locations:

- `.github/workflows/python-ci.yml:38`
- `.github/workflows/python-ci.yml:40`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. action.yml: Pinned the Docker image reference from `docker://ghcr.io/github-community-projects/contributors:v2` to `docker://ghcr.io/github-community-projects/contributors:v2@sha256:8891b833aca32ce26fc88ec563ebc74c6113788fd7a41b4eab6a57c56998e2a4`, preserving the docker:// scheme and :v2 tag inline. 2. .github/workflows/python-ci.yml: Fixed two script injection instances at lines 38 and 40 by moving `${{ matrix.python-version }}` into an `env:` block as `PYTHON_VERSION` and referencing it as `"$PYTHON_VERSION"` in the shell commands.

