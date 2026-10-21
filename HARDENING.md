<!-- markdownlint-disable -->

# Hardening Report: Trusera--ai-bom/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Trusera--ai-bom/v3.1.0** was hardened automatically. 10 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): the 'Run ai-bom scan' step puts GitHub Actions expressions straight into its run: script. Offending lines: `ARGS="scan ${{ inputs.path }} --format ${{ inputs.format }} --quiet"`, `if [ -n "${{ inputs.output }}" ]; then`, `ARGS="$ARGS -o ${{ inputs.output }}"`, `elif [ "${{ inputs.format }}" = "sarif" ]; then`, `if [ "${{ inputs.scan-level }}" = "deep" ]; then`, `if [ -n "${{ inputs.fail-on }}" ]; then`, `ARGS="$ARGS --fail-on ${{ inputs.fail-on }}"`. A calling workflow can put shell metacharacters in any of these inputs and run commands. The values should be passed through env: variables and expanded with double quotes. The script should also stop running the unquoted string `ai-bom $ARGS` (use an array instead).

Locations:

- `action.yml:52`
- `action.yml:54`
- `action.yml:57`
- `action.yml:58`
- `action.yml:59`
- `action.yml:66`
- `action.yml:71`
- `action.yml:72`

### unpinned-uses (severity: high)

The composite action references actions by mutable tag instead of a full 40-character commit SHA: `uses: actions/setup-python@v5` and `uses: github/codeql-action/upload-sarif@v3`.

Locations:

- `action.yml:40`
- `action.yml:80`

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

Changed the 'Run ai-bom scan' step in action.yml. It now passes inputs.path, format, output, scan-level and fail-on through env: variables. The command is built as a bash array, so each value stays one quoted argument, and runs as ai-bom "${ARGS[@]}". Each input is treated as one value: none of their defaults or names suggest a list. Pinned actions/setup-python to a26af69be951a213d495a4c3e4e4022e16d87065 (v5) and github/codeql-action/upload-sarif to 4bfa242aa49c5f7c6770b98670d354a7dfac05f8 (v3). Nothing was run to test the changes.

### Iteration 2

**Fixes applied:** unpinned-uses

**Notes:**

The SARIF upload step in action.yml now uses github/codeql-action/upload-sarif@4bfa242aa49c5f7c6770b98670d354a7dfac05f8 (the commit v3 currently points to), with '# v3' after it so the version is still readable. A previous edit had put the SHA into the step's name: field by mistake, which left the uses: line unpinned. I put the original name back: 'Upload SARIF to GitHub Code Scanning'.

