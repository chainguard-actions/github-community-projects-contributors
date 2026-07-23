<!-- markdownlint-disable -->

# Hardening Report: github-community-projects--contributors/v2.0.12

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github-community-projects--contributors/v2.0.12** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image referenced by a mutable tag (`v2`) instead of an immutable SHA digest. This means the action could silently pull a different (potentially malicious) image on future runs. The failing reference is: `image: "docker://ghcr.io/github-community-projects/contributors:v2"`. It should be replaced with a SHA-pinned form such as `image: "ghcr.io/github-community-projects/contributors@sha256:<64-hex-char-digest> # v2"`

Locations:

- `action.yml:7`

### script-injection (severity: high)

Sub-rule (a): Two `run:` steps in python-ci.yml directly interpolate `${{ matrix.python-version }}` inside shell commands. The `matrix.*` context flows through YAML template substitution before the shell ever sees the value, so a specially crafted matrix value (e.g. via a workflow_dispatch or repository fork) could inject arbitrary shell commands. Offending lines:
- `run: uv python install ${{ matrix.python-version }}`
- `run: uv sync --frozen --python ${{ matrix.python-version }}`
Fix: move the value into an `env:` variable and reference it as a quoted shell variable, e.g. `env: { PY_VER: "${{ matrix.python-version }}" }` then `run: uv python install "$PY_VER"`.

Locations:

- `.github/workflows/python-ci.yml:37`
- `.github/workflows/python-ci.yml:39`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. action.yml: Pinned the Docker image 'ghcr.io/github-community-projects/contributors:v2' to its immutable SHA256 digest (sha256:8891b833aca32ce26fc88ec563ebc74c6113788fd7a41b4eab6a57c56998e2a4), preserving the docker:// scheme and :v2 tag inline. 2. .github/workflows/python-ci.yml: Fixed script injection in two run: steps by moving ${{ matrix.python-version }} into an env: block as PY_VER and referencing it as "$PY_VER" in the shell commands, preventing shell command injection via matrix values.

