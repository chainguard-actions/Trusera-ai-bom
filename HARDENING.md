<!-- markdownlint-disable -->

# Hardening Report: Trusera--ai-bom/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Trusera--ai-bom/v3.1.0** was hardened automatically. 10 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The 'Run ai-bom scan' step in action.yml directly interpolates multiple `inputs.*` expressions inside the `run:` shell script (sub-rule a). Before the shell executes, GitHub's template engine substitutes these values verbatim, allowing a calling workflow to supply values containing shell metacharacters (`;`, `|`, `$(...)`, etc.) that execute arbitrary commands. Affected interpolations:
- Line 52: `ARGS="scan ${{ inputs.path }} --format ${{ inputs.format }} --quiet"`
- Line 55: `if [ -n "${{ inputs.output }}" ]`
- Line 56: `ARGS="$ARGS -o ${{ inputs.output }}"`
- Line 57: `elif [ "${{ inputs.format }}" = "sarif" ]`
- Line 63: `if [ "${{ inputs.scan-level }}" = "deep" ]`
- Line 67: `if [ -n "${{ inputs.fail-on }}" ]`
- Line 68: `ARGS="$ARGS --fail-on ${{ inputs.fail-on }}"`

Fix: Move all inputs into `env:` variables and reference them as quoted shell variables (e.g., `"$INPUT_PATH"`) instead of using `${{ ... }}` directly inside the `run:` block.

Locations:

- `action.yml:52`
- `action.yml:55`
- `action.yml:56`
- `action.yml:57`
- `action.yml:63`
- `action.yml:67`
- `action.yml:68`

### unpinned-uses (severity: high)

Two `uses:` references in action.yml are pinned to mutable version tags rather than immutable 40-character SHA digests. If the tag is moved (intentionally or via a supply-chain compromise), the action will silently execute different code.
- Line 38: `uses: actions/setup-python@v5` (tag `v5`, not a SHA)
- Line 73: `uses: github/codeql-action/upload-sarif@v3` (tag `v3`, not a SHA)

Fix: Pin each reference to its full commit SHA, e.g. `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v5`.

Locations:

- `action.yml:38`
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

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all findings in action.yml:
1. Pinned actions/setup-python@v5 to SHA a26af69be951a213d495a4c3e4e4022e16d87065 and github/codeql-action/upload-sarif@v3 to SHA ce5e7eeac396d720fe2d802714996c8ce561e311.
2. Moved all ${{ inputs.* }} expressions (path, format, output, scan-level, fail-on) from the run: shell block into an env: map (INPUT_PATH, INPUT_FORMAT, INPUT_OUTPUT, INPUT_SCAN_LEVEL, INPUT_FAIL_ON) and replaced all inline template expressions in the shell script with references to these environment variables.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script-injection vulnerability in the 'Run ai-bom scan' step of action.yml. Replaced the string-based ARGS variable (where INPUT_PATH, INPUT_FORMAT, INPUT_OUTPUT, and INPUT_FAIL_ON were expanded unquoted) with a bash array. Each input value is now a properly double-quoted separate array element: ARGS=("scan" "$INPUT_PATH" "--format" "$INPUT_FORMAT" "--quiet"), with conditional elements appended via ARGS+=(...). The final command uses `ai-bom "${ARGS[@]}"` which expands each element as a separate, properly-quoted argument, preventing shell metacharacter injection from attacker-controlled input values.

