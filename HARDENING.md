<!-- markdownlint-disable -->

# Hardening Report: Trusera--ai-bom/v0.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Trusera--ai-bom/v0.1.0** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Run ai-bom scan' step directly interpolates GitHub Actions expressions (${{ inputs.path }}, ${{ inputs.format }}, ${{ inputs.output }}, ${{ inputs.severity }}) inside a run: shell script. These values flow through YAML template substitution before the shell ever sees them, allowing an attacker who controls the calling workflow's inputs to inject arbitrary shell commands. All four inputs are required: false with no sanitization. Offending lines:
  ARGS="scan ${{ inputs.path }} --format ${{ inputs.format }} -o ${{ inputs.output }}"
  if [ -n "${{ inputs.severity }}" ]; then
  ARGS="$ARGS --severity ${{ inputs.severity }}"
Fix: move each input into an env: variable and reference it as a quoted shell variable (e.g. "$INPUT_PATH") in the run: block.

Locations:

- `action.yml:55`
- `action.yml:56`
- `action.yml:57`

### unpinned-uses (severity: high)

Two uses: references in action.yml are pinned to mutable tag refs rather than immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the upstream tag is moved or the repository is compromised:
  - uses: actions/setup-python@v5  (line 43)
  - uses: github/codeql-action/upload-sarif@v3  (line 63)
Fix: replace each tag with the full SHA of the intended commit, e.g. uses: actions/setup-python@<40-hex-sha> # v5

Locations:

- `action.yml:43`
- `action.yml:63`

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

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses

**Notes:**

Fixed all findings in hardened/action/action.yml: (1) Moved ${{ inputs.path }}, ${{ inputs.format }}, ${{ inputs.output }}, and ${{ inputs.severity }} out of the run: shell script into an env: block, referencing them as $INPUT_PATH, $INPUT_FORMAT, $INPUT_OUTPUT, $INPUT_SEVERITY. Used a bash array to build the argument list safely. (2) Pinned actions/setup-python@v5 to @a26af69be951a213d495a4c3e4e4022e16d87065 # v5 and github/codeql-action/upload-sarif@v3 to @4187e74d05793876e9989daffde9c3e66b4acd07 # v3.

