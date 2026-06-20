<!-- markdownlint-disable -->

# Hardening Report: Trusera--ai-bom/v0.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **Trusera--ai-bom/v0.1.0** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The 'Run ai-bom scan' step in action.yml directly interpolates multiple user-controlled inputs inside a `run:` shell script via `${{ ... }}` expressions. Specifically, `${{ inputs.path }}`, `${{ inputs.format }}`, `${{ inputs.output }}`, and `${{ inputs.severity }}` are all embedded directly into the shell command string. An attacker who controls these inputs (e.g. via a calling workflow) can inject arbitrary shell commands. Sub-rule (a) violation: direct expression interpolation in a run: block.

Locations:

- `action.yml:56`

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable tag refs instead of full 40-character commit SHA digests, making the action vulnerable to supply-chain attacks if the referenced tags are moved or compromised. Failing references: `actions/setup-python@v5` and `github/codeql-action/upload-sarif@v3`.

Locations:

- `action.yml:48`
- `action.yml:68`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.path }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:57`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.format }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:57`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.output }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:57`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.severity }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:58`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.severity }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:59`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

1. Pinned actions/setup-python@v5 to full SHA a26af69be951a213d495a4c3e4e4022e16d87065 and github/codeql-action/upload-sarif@v3 to full SHA dd903d2e4f5405488e5ef1422510ee31c8b32357.
2. Moved all ${{ inputs.path }}, ${{ inputs.format }}, ${{ inputs.output }}, and ${{ inputs.severity }} expressions from the run: block into an env: block (INPUT_PATH, INPUT_FORMAT, INPUT_OUTPUT, INPUT_SEVERITY). The shell script was refactored to use a bash array (args=(...)) so each argument stays properly separated and double-quoted, preventing both shell injection and argument-boundary issues.

