<!-- markdownlint-disable -->

# Hardening Report: Trusera--ai-bom/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Trusera--ai-bom/v3.1.0** was hardened automatically. 10 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The 'Run ai-bom scan' step in action.yml directly interpolates multiple ${{ inputs.* }} expressions inside the run: shell script (rule a). Specifically: `ARGS="scan ${{ inputs.path }} --format ${{ inputs.format }} --quiet"`, `if [ -n "${{ inputs.output }}" ]`, `ARGS="$ARGS -o ${{ inputs.output }}"`, `elif [ "${{ inputs.format }}" = "sarif" ]`, `if [ "${{ inputs.scan-level }}" = "deep" ]`, `if [ -n "${{ inputs.fail-on }}" ]`, and `ARGS="$ARGS --fail-on ${{ inputs.fail-on }}"`. YAML template substitution occurs before the shell parses the command, so an attacker who controls any of these inputs can inject arbitrary shell metacharacters (e.g. semicolons, backticks, pipes). All inputs should be passed via env: variables and then referenced as quoted shell variables (e.g. "$INPUT_PATH") instead of being interpolated directly.

Locations:

- `action.yml:50`
- `action.yml:53`
- `action.yml:54`
- `action.yml:55`
- `action.yml:60`
- `action.yml:64`
- `action.yml:65`

### unpinned-uses (severity: high)

Two uses: references in action.yml are pinned to mutable version tags rather than immutable 40-character SHA digests, making the action vulnerable to supply-chain attacks if the upstream tag is moved or compromised. Failing references: `actions/setup-python@v5` (line 38) and `github/codeql-action/upload-sarif@v3` (line 70). These should be replaced with their full SHA commit hashes, e.g. `actions/setup-python@<40-char-sha> # v5`.

Locations:

- `action.yml:38`
- `action.yml:70`

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

1. Pinned actions/setup-python@v5 to SHA a26af69be951a213d495a4c3e4e4022e16d87065 and github/codeql-action/upload-sarif@v3 to SHA 9f759ee644a3e7c15c1390abf49868036c00067b.
2. Moved all ${{ inputs.* }} expressions (path, format, output, scan-level, fail-on) from the run: shell script into an env: block as INPUT_PATH, INPUT_FORMAT, INPUT_OUTPUT, INPUT_SCAN_LEVEL, INPUT_FAIL_ON. All references in the shell script now use quoted environment variables ($INPUT_PATH, etc.) instead of inline template expressions, preventing shell injection attacks.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in the 'Run ai-bom scan' step of action.yml. Replaced the string-based ARGS variable (which concatenated unquoted input variables and was itself unquoted when passed to ai-bom) with a bash array. Each input-derived value ($INPUT_PATH, $INPUT_FORMAT, $INPUT_OUTPUT, $INPUT_FAIL_ON) is now double-quoted when added to the array, and the command is invoked as `ai-bom "${ARGS[@]}"` to preserve argument boundaries and prevent word-splitting or shell metacharacter injection.

