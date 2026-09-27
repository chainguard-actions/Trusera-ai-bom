<!-- markdownlint-disable -->

# Hardening Report: Trusera--ai-bom/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Trusera--ai-bom/v3.1.0** was hardened automatically. 10 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The 'Run ai-bom scan' step (action.yml) directly interpolates multiple user-controlled `inputs.*` expressions inside the `run:` shell script via YAML template substitution, enabling command injection. An attacker who controls these inputs can inject arbitrary shell commands. Offending lines include:
- `ARGS="scan ${{ inputs.path }} --format ${{ inputs.format }} --quiet"` (sub-rule a)
- `if [ -n "${{ inputs.output }}" ]; then` (sub-rule a)
- `ARGS="$ARGS -o ${{ inputs.output }}"` (sub-rule a)
- `elif [ "${{ inputs.format }}" = "sarif" ]; then` (sub-rule a)
- `if [ "${{ inputs.scan-level }}" = "deep" ]; then` (sub-rule a)
- `if [ -n "${{ inputs.fail-on }}" ]; then` (sub-rule a)
- `ARGS="$ARGS --fail-on ${{ inputs.fail-on }}"` (sub-rule a)
- `ai-bom $ARGS` (unquoted expansion of attacker-controlled ARGS, sub-rule b)

Fix: move all inputs into `env:` variables and reference them as double-quoted shell variables (e.g., `"$INPUT_PATH"`), never interpolating `${{ ... }}` directly inside a `run:` block.

Locations:

- `action.yml:51`
- `action.yml:54`
- `action.yml:55`
- `action.yml:56`
- `action.yml:62`
- `action.yml:65`
- `action.yml:68`
- `action.yml:71`

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable version tags instead of pinned 40-character commit SHA digests, making the action vulnerable to supply-chain attacks if the upstream tag is moved or compromised:
- `uses: actions/setup-python@v5` (line 39) — should be pinned to a full SHA, e.g. `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v5`
- `uses: github/codeql-action/upload-sarif@v3` (line 73) — should be pinned to a full SHA, e.g. `github/codeql-action/upload-sarif@45775bd8235c68ba998cffa5171334d58593da47 # v3`

Locations:

- `action.yml:39`
- `action.yml:73`

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

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses

**Notes:**

Fixed all findings in hardened/action/action.yml:

1. script-injection / static-inline-injection: Moved all ${{ inputs.* }} expressions from the run: block into the step's env: map (INPUT_PATH, INPUT_FORMAT, INPUT_OUTPUT, INPUT_SCAN_LEVEL, INPUT_FAIL_ON). Shell script now references these as double-quoted env vars. Replaced the unquoted $ARGS string with a bash array (ARGS=(...) with += appends) and invokes ai-bom with "${ARGS[@]}" to preserve argument boundaries.

2. unpinned-uses: Pinned both action references to full 40-character commit SHAs:
   - actions/setup-python@v5 → @a26af69be951a213d495a4c3e4e4022e16d87065 # v5
   - github/codeql-action/upload-sarif@v3 → @1190a975f95ce23525efb6a3fc21ea29567c1b52 # v3

The if: condition and sarif_file: input on the upload step retain ${{ }} expressions as they are in YAML expression context (not run: shell scripts) and are not subject to shell injection.

