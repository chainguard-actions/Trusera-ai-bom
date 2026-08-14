<!-- markdownlint-disable -->

# Hardening Report: Trusera--ai-bom/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Trusera--ai-bom/v3.0.0** was hardened automatically. 11 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Run ai-bom scan' step directly interpolates multiple inputs.* expressions inside a run: shell command string. The values ${{ inputs.path }}, ${{ inputs.format }}, ${{ inputs.output }}, ${{ inputs.severity }}, ${{ inputs.fail-on }}, and ${{ inputs.policy-file }} are all substituted by the GitHub Actions template engine before the shell processes the script, allowing an attacker-controlled value to inject arbitrary shell commands (e.g., a path like ". ; malicious-command" would execute). These must be moved to env: variables and then referenced as double-quoted shell variables (e.g., "$INPUT_PATH") instead of using ${{ }} directly in the run: block.

Locations:

- `action.yml:60`

### unpinned-uses (severity: high)

Two uses: references in action.yml are pinned to mutable tag refs instead of immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the upstream tag is moved or compromised. Failing references: (1) actions/setup-python@v5 — should be pinned to a full SHA, e.g. actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v5; (2) github/codeql-action/upload-sarif@v3 — should be pinned to a full SHA, e.g. github/codeql-action/upload-sarif@ff0a06e83cb2550a134d1a8d7e79f5e4e6e7e9b0 # v3.

Locations:

- `action.yml:49`
- `action.yml:78`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.path }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:65`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.format }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:65`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.output }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:65`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.severity }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:66`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.severity }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:67`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.fail-on }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:69`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.fail-on }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:70`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.policy-file }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:72`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.policy-file }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:73`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses

**Notes:**

Fixed all findings in hardened/action/action.yml: (1) Pinned actions/setup-python@v5 to SHA a26af69be951a213d495a4c3e4e4022e16d87065 and github/codeql-action/upload-sarif@v3 to SHA 4187e74d05793876e9989daffde9c3e66b4acd07, preserving original tags as comments. (2) Moved all six ${{ inputs.* }} expressions from the 'Run ai-bom scan' run: block into an env: block (INPUT_PATH, INPUT_FORMAT, INPUT_OUTPUT, INPUT_SEVERITY, INPUT_FAIL_ON, INPUT_POLICY_FILE). Replaced the string-concatenation ARGS variable with a bash array so each argument token stays separate and properly quoted, preventing shell injection while preserving correct argument boundaries for optional flags.

### Iteration 2

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all unpinned `uses:` references across four workflow files to full 40-character SHA commit hashes, preserving the original tag as a comment:

- `actions/checkout@v6` → `@d23441a48e516b6c34aea4fa41551a30e30af803 # v6` (ci.yml ×4, docker.yml ×1, dogfood.yml ×1, publish.yml ×1)
- `actions/setup-python@v6` → `@ece7cb06caefa5fff74198d8649806c4678c61a1 # v6` (ci.yml ×4, dogfood.yml ×1, publish.yml ×1)
- `github/codeql-action/upload-sarif@v3` → `@4187e74d05793876e9989daffde9c3e66b4acd07 # v3` (ci.yml ×1)
- `github/codeql-action/upload-sarif@v4` → `@e4fba868fa4b1b91e1fdab776edc8cfbe6e9fb81 # v4` (dogfood.yml ×1)
- `docker/setup-buildx-action@v3` → `@8d2750c68a42422c14e847fe6c8ac0403b4cbd6f # v3` (docker.yml ×1)
- `docker/login-action@v3` → `@c94ce9fb468520275223c153574b00df6fe4bcc9 # v3` (docker.yml ×1)
- `docker/metadata-action@v5` → `@c299e40c65443455700f0fdfc63efafe5b349051 # v5` (docker.yml ×1)
- `docker/build-push-action@v6` → `@10e90e3645eae34f1e60eeb005ba3a3d33f178e8 # v6` (docker.yml ×1)
- `pypa/gh-action-pypi-publish@release/v1` → `@ba38be9e461d3875417946c167d0b5f3d385a247 # release/v1` (publish.yml ×1)

All SHAs were resolved using lookup_action_sha. No other findings were present.

