<!-- markdownlint-disable -->

# Hardening Report: Trusera--ai-bom/vscode-v0.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Trusera--ai-bom/vscode-v0.1.0** was hardened automatically. 15 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple ${{ inputs.* }} expressions are directly interpolated inside run: shell blocks in action.yml, violating rule (a). In the 'Run ai-bom scan' step, inputs.path, inputs.format, inputs.output, inputs.scan-level, and inputs.fail-on are all injected directly into shell command strings (e.g., ARGS="scan ${{ inputs.path }} --format ${{ inputs.format }} --quiet"). In the 'Generate JSON for policy evaluation' step, inputs.path and inputs.scan-level are similarly injected. In the 'Evaluate Cedar policy gate' step, inputs.cedar-policy-file, steps.policy-scan.outputs.policy_results, and github.action_path are injected directly into shell commands (e.g., GATE_ARGS="${{ steps.policy-scan.outputs.policy_results }} ${{ inputs.cedar-policy-file }}" and python3 "${{ github.action_path }}/scripts/cedar-gate.py" $GATE_ARGS). An attacker controlling these inputs can inject arbitrary shell commands.

Locations:

- `action.yml:57`
- `action.yml:60`
- `action.yml:61`
- `action.yml:63`
- `action.yml:68`
- `action.yml:72`
- `action.yml:73`
- `action.yml:87`
- `action.yml:89`
- `action.yml:100`
- `action.yml:102`
- `action.yml:108`

### unpinned-uses (severity: high)

All uses: references across action.yml and every workflow file use mutable tag or branch refs instead of pinned 40-character SHA digests, making the action vulnerable to supply-chain attacks if any referenced action is compromised or its tag is moved. Failing references include: action.yml: actions/setup-python@v5, github/codeql-action/upload-sarif@v3; ai-bom-example.yml: actions/checkout@v4 (×5), trusera/ai-bom@main (×5), actions/upload-artifact@v4; ci.yml: actions/checkout@v6 (×4), actions/setup-python@v6 (×4), github/codeql-action/upload-sarif@v4; docker.yml: actions/checkout@v6, docker/setup-buildx-action@v3, docker/login-action@v3, docker/metadata-action@v5, docker/build-push-action@v6; dogfood.yml: actions/checkout@v6, actions/setup-python@v6, github/codeql-action/upload-sarif@v4; publish-vscode.yml: actions/checkout@v4, actions/setup-node@v4; publish.yml: actions/checkout@v6, actions/setup-python@v6, pypa/gh-action-pypi-publish@release/v1.

Locations:

- `action.yml:48`
- `action.yml:79`
- `.github/workflows/ai-bom-example.yml:24`
- `.github/workflows/ai-bom-example.yml:27`
- `.github/workflows/ai-bom-example.yml:37`
- `.github/workflows/ai-bom-example.yml:40`
- `.github/workflows/ai-bom-example.yml:51`
- `.github/workflows/ai-bom-example.yml:54`
- `.github/workflows/ai-bom-example.yml:58`
- `.github/workflows/ai-bom-example.yml:68`
- `.github/workflows/ai-bom-example.yml:71`
- `.github/workflows/ci.yml:14`
- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:32`
- `.github/workflows/ci.yml:35`
- `.github/workflows/ci.yml:50`
- `.github/workflows/ci.yml:53`
- `.github/workflows/ci.yml:68`
- `.github/workflows/ci.yml:71`
- `.github/workflows/ci.yml:84`
- `.github/workflows/docker.yml:14`
- `.github/workflows/docker.yml:17`
- `.github/workflows/docker.yml:21`
- `.github/workflows/docker.yml:27`
- `.github/workflows/docker.yml:38`
- `.github/workflows/dogfood.yml:14`
- `.github/workflows/dogfood.yml:17`
- `.github/workflows/dogfood.yml:29`
- `.github/workflows/publish-vscode.yml:7`
- `.github/workflows/publish-vscode.yml:8`
- `.github/workflows/publish.yml:17`
- `.github/workflows/publish.yml:21`
- `.github/workflows/publish.yml:34`

### missing-permissions (severity: medium)

The workflow file publish-vscode.yml has no top-level permissions: key and its single job ('publish') also has no job-level permissions: key. Without explicit permissions, the job inherits the default repository permissions (which may include write access to contents and other scopes), violating the principle of least privilege.

Locations:

- `.github/workflows/publish-vscode.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.path }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:66`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.format }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:66`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.output }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:69`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.output }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:70`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.format }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:71`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.scan-level }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:78`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.fail-on }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:83`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.fail-on }}" appears directly in run: block of step "Run ai-bom scan"; move to env: map

Locations:

- `action.yml:84`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.path }}" appears directly in run: block of step "Generate JSON for policy evaluation"; move to env: map

Locations:

- `action.yml:103`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.scan-level }}" appears directly in run: block of step "Generate JSON for policy evaluation"; move to env: map

Locations:

- `action.yml:105`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.cedar-policy-file }}" appears directly in run: block of step "Evaluate Cedar policy gate"; move to env: map

Locations:

- `action.yml:116`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.cedar-policy-file }}" appears directly in run: block of step "Evaluate Cedar policy gate"; move to env: map

Locations:

- `action.yml:118`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all findings across action.yml and 6 workflow files:

1. script-injection/static-inline-injection: Moved all ${{ inputs.* }} and ${{ github.action_path }} expressions from run: shell blocks to env: blocks in action.yml. The three affected steps ('Run ai-bom scan', 'Generate JSON for policy evaluation', 'Evaluate Cedar policy gate') now reference plain environment variables ($INPUT_PATH, $INPUT_FORMAT, $INPUT_OUTPUT, $INPUT_SCAN_LEVEL, $INPUT_FAIL_ON, $INPUT_CEDAR_POLICY_FILE, $POLICY_RESULTS, $ACTION_PATH) in their shell scripts.

2. unpinned-uses: Pinned all mutable tag/branch references to full 40-character SHA digests with tag comments in action.yml and all 6 workflow files (.github/workflows/ai-bom-example.yml, ci.yml, docker.yml, dogfood.yml, publish-vscode.yml, publish.yml). All SHAs were resolved via lookup_action_sha.

3. missing-permissions: Added top-level 'permissions: contents: read' to publish-vscode.yml (minimum needed for checkout; no other permissions required for VS Code extension publishing via VSCE_PAT secret).

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed all three script-injection findings in action.yml by replacing unquoted string-concatenation argument building with bash arrays:

1. 'Run ai-bom scan' step (lines 72, 76, 90, 94): Replaced `ARGS="scan $INPUT_PATH ..."` string concatenation with `ARGS=(scan "$INPUT_PATH" ...)` array syntax. Each user-controlled value (INPUT_PATH, INPUT_FORMAT, INPUT_OUTPUT, INPUT_FAIL_ON) is now double-quoted when appended to the array. Final invocation changed from `ai-bom $ARGS` to `ai-bom "${ARGS[@]}"`.

2. 'Generate JSON for policy evaluation' step (lines 112, 118): Same pattern — replaced string concatenation with `ARGS=(scan "$INPUT_PATH" ...)` array and changed `ai-bom $ARGS` to `ai-bom "${ARGS[@]}"`.

3. 'Evaluate Cedar policy gate' step (lines 131, 138): Replaced `GATE_ARGS="$POLICY_RESULTS $INPUT_CEDAR_POLICY_FILE"` with `GATE_ARGS=("$POLICY_RESULTS" "$INPUT_CEDAR_POLICY_FILE")` array and changed `python3 ... $GATE_ARGS` to `python3 ... "${GATE_ARGS[@]}"`.

All user-controlled values remain in the env: block (already correct) and are now properly double-quoted in array elements, preventing shell metacharacter injection.

