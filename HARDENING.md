<!-- markdownlint-disable -->

# Hardening Report: github-community-projects--contributors--/v2.0.18

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **github-community-projects--contributors--/v2.0.18** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image with a mutable tag `:v2` instead of a SHA digest. The reference `docker://ghcr.io/github-community-projects/contributors:v2` can be silently updated to point to a different (potentially malicious) image without any change to the action definition. It should be pinned to a specific SHA digest, e.g. `docker://ghcr.io/github-community-projects/contributors@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:7`

### script-injection (severity: high)

Two `run:` steps in python-ci.yml directly interpolate `${{ matrix.python-version }}` into shell commands (sub-rule a). Although the matrix values are hardcoded literals in this workflow, the `${{ ... }}` template substitution happens before the shell processes the string, meaning any special characters in the value would be interpreted by the shell before quoting can occur. The safe pattern is to pass the value via an `env:` variable and reference it as a quoted `"$ENV_VAR"` in the shell script. Offending lines: `run: uv python install ${{ matrix.python-version }}` and `run: uv sync --frozen --python ${{ matrix.python-version }}`.

Locations:

- `.github/workflows/python-ci.yml:35`
- `.github/workflows/python-ci.yml:37`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. action.yml: Pinned the Docker image from mutable tag 'docker://ghcr.io/github-community-projects/contributors:v2' to immutable digest 'docker://ghcr.io/github-community-projects/contributors@sha256:8891b833aca32ce26fc88ec563ebc74c6113788fd7a41b4eab6a57c56998e2a4' with a # v2 comment for readability. 2. .github/workflows/python-ci.yml: Fixed two script injection instances by moving ${{ matrix.python-version }} out of run: shell strings into env: blocks as PYTHON_VERSION, then referencing it as "$PYTHON_VERSION" in the shell commands for both 'uv python install' and 'uv sync --frozen --python' steps.

