<!-- markdownlint-disable -->

# Hardening Report: github-community-projects--contributors/v2.0.20

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github-community-projects--contributors/v2.0.20** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses a Docker action with a mutable image tag (`docker://ghcr.io/github-community-projects/contributors:v2`) instead of a SHA digest. A mutable tag can be silently updated to point to a different (potentially malicious) image, creating a supply-chain risk. The `image:` field should reference a specific SHA digest, e.g. `docker://ghcr.io/github-community-projects/contributors@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:6`

### script-injection (severity: high)

Rule (a) violation: Two `run:` steps in python-ci.yml directly interpolate `${{ matrix.python-version }}` — a `matrix.*` context value — into shell command strings. Although the matrix values are hardcoded in this workflow, any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value flows through YAML template substitution before the shell sees it. An attacker who can influence the matrix (e.g. via a reusable workflow call) could inject arbitrary shell commands. The values should be passed via an `env:` variable and then referenced as a quoted shell variable.

Offending lines:
- `run: uv python install ${{ matrix.python-version }}`
- `run: uv sync --frozen --python ${{ matrix.python-version }}`

Locations:

- `.github/workflows/python-ci.yml:33`
- `.github/workflows/python-ci.yml:35`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. action.yml: Pinned the Docker image from `docker://ghcr.io/github-community-projects/contributors:v2` to `docker://ghcr.io/github-community-projects/contributors:v2@sha256:a3f28ca0b947267a7ba20df098f740f784fc808afa68ba5aa959d88ac3266e13`, preserving the `docker://` scheme and `:v2` tag inline.
2. .github/workflows/python-ci.yml: Fixed two script-injection vulnerabilities by moving `${{ matrix.python-version }}` out of `run:` blocks and into `env:` blocks as `PYTHON_VERSION`, then referencing it as `"$PYTHON_VERSION"` in the shell commands.

