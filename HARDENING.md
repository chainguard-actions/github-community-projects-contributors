<!-- markdownlint-disable -->

# Hardening Report: github-community-projects--contributors/v2.0.18

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github-community-projects--contributors/v2.0.18** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml Docker image reference uses a mutable version tag (`v2`) instead of an immutable SHA digest. This means the action could silently pull a different (potentially malicious) image on future runs if the tag is moved. The reference `docker://ghcr.io/github-community-projects/contributors:v2` should be replaced with a SHA-pinned form such as `docker://ghcr.io/github-community-projects/contributors@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:7`

### script-injection (severity: high)

Sub-rule (a): `${{ matrix.python-version }}` is interpolated directly inside `run:` shell command strings in python-ci.yml. Although `matrix.*` values are defined in the same workflow file, they still flow through YAML template substitution before the shell sees them — any special shell characters in the value would be interpreted by the shell. The value should be passed via an `env:` variable and referenced as a quoted shell variable (e.g. `"$PYTHON_VERSION"`) instead. Offending lines:
- `run: uv python install ${{ matrix.python-version }}`
- `run: uv sync --frozen --python ${{ matrix.python-version }}`

Locations:

- `.github/workflows/python-ci.yml:40`
- `.github/workflows/python-ci.yml:42`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. action.yml: Pinned the Docker image reference from `docker://ghcr.io/github-community-projects/contributors:v2` to `docker://ghcr.io/github-community-projects/contributors:v2@sha256:8891b833aca32ce26fc88ec563ebc74c6113788fd7a41b4eab6a57c56998e2a4`, preserving the docker:// scheme and tag inline. 2. python-ci.yml: Fixed two script injection instances by moving `${{ matrix.python-version }}` out of `run:` shell strings into `env:` blocks as `PYTHON_VERSION`, then referencing it as `"$PYTHON_VERSION"` in the shell commands.

