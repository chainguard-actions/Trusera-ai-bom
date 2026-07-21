<!-- markdownlint-disable -->

# Hardening Report: Trusera--ai-bom/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Trusera--ai-bom/v3.1.0** was hardened automatically. 10 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Run ai-bom scan' step directly interpolates multiple `${{ inputs.* }}` expressions inside the `run:` shell script. Specifically: `${{ inputs.path }}`, `${{ inputs.format }}`, `${{ inputs.output }}`, `${{ inputs.scan-level }}`, and `${{ inputs.fail-on }}` are all embedded directly in shell commands. YAML template substitution occurs before the shell processes the script, so an attacker who controls these inputs can inject arbitrary shell metacharacters (e.g., `;`, `|`, `$(...)`, backticks). The values should be passed via `env:` variables and then referenced as double-quoted shell variables (e.g., `"$INPUT_PATH"`) instead of being interpolated directly with `${{ }}`.

Locations:

- `action.yml:51`

### unpinned-uses (severity: high)

Two `uses:` references in action.yml are pinned to mutable version tags rather than immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the referenced tag is moved or overwritten. Failing references: (1) `uses: actions/setup-python@v5` (line 38) — should be pinned to a full SHA digest; (2) `uses: github/codeql-action/upload-sarif@v3` (line 75) — should be pinned to a full SHA digest.

Locations:

- `action.yml:38`
- `action.yml:75`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.path }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:54`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.format }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:54`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.output }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:57`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.output }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:58`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.format }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:59`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.scan-level }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:66`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.fail-on }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:71`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.fail-on }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:72`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all findings in hardened/action/action.yml:
1. Pinned `actions/setup-python@v5` to SHA `a26af69be951a213d495a4c3e4e4022e16d87065` (# v5)
2. Pinned `github/codeql-action/upload-sarif@v3` to SHA `b7351df727350dca84cb9d725d57dcf5bc82ba26` (# v3)
3. Moved all `${{ inputs.* }}` expressions from the 'Run ai-bom scan' run: block into an env: map (INPUT_PATH, INPUT_FORMAT, INPUT_OUTPUT, INPUT_SCAN_LEVEL, INPUT_FAIL_ON) and updated the shell script to reference them as plain environment variables, eliminating all script injection vectors.

### Iteration 2

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script injection in action.yml by converting ARGS from a string to a bash array with properly double-quoted values, invoking ai-bom with "${ARGS[@]}". Pinned all unpinned action references across 5 workflow files (ci.yml, ai-bom-example.yml, docker.yml, dogfood.yml, publish.yml) to their immutable 40-character SHA digests, preserving the original tag/branch as a comment for readability.

