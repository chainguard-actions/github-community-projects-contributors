<!-- markdownlint-disable -->

# Hardening Report: github-community-projects--contributors/v2.0.21

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github-community-projects--contributors/v2.0.21** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml `runs.image:` field references the Docker image `docker://ghcr.io/github-community-projects/contributors:v2` using a mutable tag (`v2`) instead of an immutable SHA digest. This means the image can be silently replaced with a different (potentially malicious) version without any change to the action definition, creating a supply-chain risk. It should be pinned to a specific SHA digest, e.g. `image: ghcr.io/github-community-projects/contributors@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:7`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned the Docker image reference in action.yml from `docker://ghcr.io/github-community-projects/contributors:v2` to `docker://ghcr.io/github-community-projects/contributors:v2@sha256:5537845e70b673b9a20eb14dcc7748e85e08d489052cb08e221027aaa1373f1a`. The `docker://` scheme and `:v2` tag are preserved inline alongside the immutable digest.

