<!-- markdownlint-disable -->

# Hardening Report: Trusera--ai-bom/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Trusera--ai-bom/v3.0.0** was hardened automatically. 12 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses action refs that are tags, not commit SHAs: 'uses: actions/setup-python@v5' and 'uses: github/codeql-action/upload-sarif@v3'.

Locations:

- `action.yml:52`
- `action.yml:79`

### script-injection (severity: high)

Rule (a) is violated. The 'Run ai-bom scan' step puts expressions straight into its run: script. Examples: 'ARGS="scan ${{ inputs.path }} --format ${{ inputs.format }} -o ${{ inputs.output }} --quiet"', plus ${{ inputs.severity }}, ${{ inputs.fail-on }} and ${{ inputs.policy-file }} in if-tests and assignments. Rule (b) is also violated: the input values are expanded unquoted through 'ai-bom $ARGS', so the shell splits them on spaces and expands glob characters.

Locations:

- `action.yml:65`
- `action.yml:75`

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

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 5369d80..13fb8da 100644
--- a/action.yml
+++ b/action.yml
@@ -1,16 +1,13 @@
 name: "AI-BOM Scan"
 description: "Discover and inventory AI/LLM components in your codebase"
 author: "Trusera"
-
 # Required permissions in calling workflow:
 #   permissions:
 #     security-events: write   # For SARIF upload to GitHub Code Scanning
 #     contents: read           # For checkout
-
 branding:
   icon: "shield"
   color: "blue"
-
 inputs:
   path:
     description: "Path to scan"
@@ -44,38 +41,58 @@ inputs:
     description: "Python version to use"
     required: false
     default: "3.12"
-
 runs:
   using: "composite"
   steps:
     - name: Set up Python
-      uses: actions/setup-python@v5
+      uses: actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v5
       with:
         python-version: ${{ inputs.python-version }}
         cache: 'pip'
-
     - name: Install ai-bom
       shell: bash
       run: pip install ai-bom
-
     - name: Run ai-bom scan
       shell: bash
-      continue-on-error: true  # Scan findings should not fail the workflow
+      continue-on-error: true # Scan findings should not fail the workflow
+      env:
+        INPUT_PATH: ${{ inputs.path }}
+        INPUT_FORMAT: ${{ inputs.format }}
+        INPUT_OUTPUT: ${{ inputs.output }}
+        INPUT_SEVERITY: ${{ inputs.severity }}
+        INPUT_FAIL_ON: ${{ inputs.fail-on }}
+        INPUT_POLICY_FILE: ${{ inputs.policy-file }}
       run: |
-        ARGS="scan ${{ inputs.path }} --format ${{ inputs.format }} -o ${{ inputs.output }} --quiet"
-        if [ -n "${{ inputs.severity }}" ]; then
-          ARGS="$ARGS --severity ${{ inputs.severity }}"
+        args=(scan "$INPUT_PATH" --format "$INPUT_FORMAT" -o "$INPUT_OUTPUT" --quiet)
+        if [ -n "$INPUT_SEVERITY" ]; then
+          args+=(--severity "$INPUT_SEVERITY")
         fi
-        if [ -n "${{ inputs.fail-on }}" ]; then
-          ARGS="$ARGS --fail-on ${{ inputs.fail-on }}"
+        if [ -n "$INPUT_FAIL_ON" ]; then
+          args+=(--fail-on "$INPUT_FAIL_ON")
         fi
-        if [ -n "${{ inputs.policy-file }}" ]; then
-          ARGS="$ARGS --policy ${{ inputs.policy-file }}"
+        if [ -n "$INPUT_POLICY_FILE" ]; then
+          args+=(--policy "$INPUT_POLICY_FILE")
         fi
-        ai-bom $ARGS
-
+        ai-bom "${args[@]}"
     - name: Upload SARIF to GitHub Code Scanning
       if: ${{ inputs.upload-sarif == 'true' && inputs.format == 'sarif' }}
-      uses: github/codeql-action/upload-sarif@v3
+      uses: github/codeql-action/upload-sarif@4187e74d05793876e9989daffde9c3e66b4acd07 # v3
       with:
         sarif_file: ${{ inputs.output }}

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection, prior-fix

**Notes:**

Pinned actions/setup-python@v5 to a26af69b and github/codeql-action/upload-sarif@v3 to 9418214d (the current v3 SHA). Moved the inputs path, format, output, severity, fail-on and policy-file into env vars in the scan step. The ai-bom command is now built as a quoted bash array, so these values can't be split on spaces or globbed. This is the same change the previous harden made.

### Iteration 2

**Fixes applied:** unpinned-uses

**Notes:**

Pinned actions/setup-python@v5 to a26af69be951a213d495a4c3e4e4022e16d87065 (# v5) in action.yml. The other uses: was already pinned to a SHA.

