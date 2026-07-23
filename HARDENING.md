<!-- markdownlint-disable -->

# Hardening Report: github-community-projects--contributors/v2.0.15

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github-community-projects--contributors/v2.0.15** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses a Docker image referenced by a mutable tag (`v2`) instead of an immutable SHA digest. This means the action could silently pull a different (potentially malicious) image on future runs. The failing reference is: `image: "docker://ghcr.io/github-community-projects/contributors:v2"`. It should be replaced with a SHA-digest reference such as `image: "docker://ghcr.io/github-community-projects/contributors@sha256:<64-hex-char-digest>"`

Locations:

- `action.yml:7`

### script-injection (severity: high)

Sub-rule (a): Two `run:` steps in python-ci.yml directly interpolate `${{ matrix.python-version }}` inside shell command strings. Although `matrix` values are defined in the same workflow file, any `${{ ... }}` expression interpolated directly into a `run:` block is a script-injection risk because the YAML template substitution occurs before the shell ever sees the value, allowing shell metacharacters to be injected. Offending lines:
- Line 41: `run: uv python install ${{ matrix.python-version }}`
- Line 43: `run: uv sync --frozen --python ${{ matrix.python-version }}`
Fix: move the value into an `env:` variable and reference it as a quoted shell variable, e.g. `env: { PYTHON_VERSION: "${{ matrix.python-version }}" }` then `run: uv python install "$PYTHON_VERSION"`.

Locations:

- `.github/workflows/python-ci.yml:41`
- `.github/workflows/python-ci.yml:43`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. action.yml: Pinned Docker image from mutable tag `docker://ghcr.io/github-community-projects/contributors:v2` to immutable digest `docker://ghcr.io/github-community-projects/contributors:v2@sha256:8891b833aca32ce26fc88ec563ebc74c6113788fd7a41b4eab6a57c56998e2a4`, preserving the docker:// scheme and :v2 tag inline. 2. .github/workflows/python-ci.yml: Fixed script injection on lines 41 and 43 by moving `${{ matrix.python-version }}` into an `env:` block as `PYTHON_VERSION` and referencing it as `"$PYTHON_VERSION"` in both `uv python install` and `uv sync --frozen --python` run steps.

