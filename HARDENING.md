<!-- markdownlint-disable -->

# Hardening Report: github-community-projects--contributors/v2.0.17

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github-community-projects--contributors/v2.0.17** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses a Docker image referenced by a mutable tag (`v2`) instead of an immutable SHA digest. This means the action could silently pull a different (potentially malicious) image on future runs. The failing reference is: `image: "docker://ghcr.io/github-community-projects/contributors:v2"`. It should be replaced with a SHA-digest reference such as `image: "ghcr.io/github-community-projects/contributors@sha256:<64-hex-char-digest> # v2"`.

Locations:

- `action.yml:7`

### script-injection (severity: high)

Sub-rule (a): Two `run:` steps in python-ci.yml directly interpolate `${{ matrix.python-version }}` inside shell command strings. Per the script-injection check, ANY `${{ ... }}` expression — including `matrix.*` — interpolated directly in a `run:` block is a finding, because the value flows through YAML template substitution before the shell ever sees it. If the matrix were ever made externally configurable (e.g. via `workflow_dispatch` inputs or a calling workflow), this would allow command injection. The offending lines are:
- `run: uv python install ${{ matrix.python-version }}` (line 35)
- `run: uv sync --frozen --python ${{ matrix.python-version }}` (line 37)

Fix: move the value into an `env:` variable and reference it as a quoted shell variable, e.g.:
```yaml
env:
  PYTHON_VERSION: ${{ matrix.python-version }}
run: |
  uv python install "$PYTHON_VERSION"
```

Locations:

- `.github/workflows/python-ci.yml:35`
- `.github/workflows/python-ci.yml:37`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. action.yml: Pinned the Docker image from `docker://ghcr.io/github-community-projects/contributors:v2` to `docker://ghcr.io/github-community-projects/contributors:v2@sha256:8891b833aca32ce26fc88ec563ebc74c6113788fd7a41b4eab6a57c56998e2a4`, preserving the docker:// scheme and tag inline.
2. .github/workflows/python-ci.yml: Fixed two script-injection findings by moving `${{ matrix.python-version }}` out of both `run:` blocks and into `env:` blocks as `PYTHON_VERSION`, then referencing it as `"$PYTHON_VERSION"` in the shell commands.

