<!-- markdownlint-disable -->

# Hardening Report: github-community-projects--contributors/v2.0.16

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github-community-projects--contributors/v2.0.16** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml Docker image reference uses a mutable tag (`v2`) instead of an immutable SHA digest. This means the action could silently pull a different (potentially malicious) image on future runs. The reference `docker://ghcr.io/github-community-projects/contributors:v2` should be replaced with a SHA-pinned form such as `docker://ghcr.io/github-community-projects/contributors@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:7`

### script-injection (severity: high)

Sub-rule (a): Two `run:` steps in python-ci.yml directly interpolate `${{ matrix.python-version }}` — a `matrix.*` context value — into shell command strings. Although the matrix values are defined in the same workflow file, any `${{ ... }}` expression inside a `run:` block is expanded by the Actions template engine before the shell ever sees the string, allowing shell metacharacters to be injected if the value is ever attacker-influenced. The offending lines are:
  - `run: uv python install ${{ matrix.python-version }}` (line 38)
  - `run: uv sync --frozen --python ${{ matrix.python-version }}` (line 40)
Fix: move the value into an `env:` variable and reference it as a quoted shell variable, e.g. `env: { PYTHON_VERSION: "${{ matrix.python-version }}" }` and `run: uv python install "$PYTHON_VERSION"`.

Locations:

- `.github/workflows/python-ci.yml:38`
- `.github/workflows/python-ci.yml:40`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. action.yml: Pinned the Docker image reference from `docker://ghcr.io/github-community-projects/contributors:v2` to `docker://ghcr.io/github-community-projects/contributors:v2@sha256:8891b833aca32ce26fc88ec563ebc74c6113788fd7a41b4eab6a57c56998e2a4`, preserving the docker:// scheme and inline tag. 2. .github/workflows/python-ci.yml: Fixed two script injection vulnerabilities by moving `${{ matrix.python-version }}` out of `run:` shell strings and into `env:` blocks as `PYTHON_VERSION`, then referencing it as `"$PYTHON_VERSION"` in the shell commands.

