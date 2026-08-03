<!-- markdownlint-disable -->

# Hardening Report: github-community-projects--contributors/v2.0.19

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github-community-projects--contributors/v2.0.19** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml Docker image reference uses a mutable tag (`v2`) instead of a SHA digest. This means the action could silently pull a different (potentially malicious) image on future runs. The reference `docker://ghcr.io/github-community-projects/contributors:v2` should be replaced with a SHA-pinned form such as `docker://ghcr.io/github-community-projects/contributors@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:7`

### script-injection (severity: high)

Sub-rule (a): `${{ matrix.python-version }}` is interpolated directly inside two `run:` shell command strings in python-ci.yml. Although `matrix.*` values are typically developer-controlled, they still flow through YAML template substitution before the shell processes them, making any special characters in the value executable shell syntax. The offending lines are:
  - `run: uv python install ${{ matrix.python-version }}`
  - `run: uv sync --frozen --python ${{ matrix.python-version }}`
Fix: move the value into an `env:` variable and reference it as a quoted shell variable, e.g. `run: uv python install "$PYTHON_VERSION"`.

Locations:

- `.github/workflows/python-ci.yml:37`
- `.github/workflows/python-ci.yml:39`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. action.yml: Pinned the Docker image reference from `docker://ghcr.io/github-community-projects/contributors:v2` to `docker://ghcr.io/github-community-projects/contributors:v2@sha256:c8f72951fa069eab2ac73b21ed114a3fde961076245fbf4bac16d651b6a10325`, preserving the `docker://` scheme and `:v2` tag inline.
2. .github/workflows/python-ci.yml: Moved `${{ matrix.python-version }}` out of both `run:` shell strings into `env:` blocks as `PYTHON_VERSION`, then referenced it as `"$PYTHON_VERSION"` in the shell commands to prevent script injection.

