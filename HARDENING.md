<!-- markdownlint-disable -->

# Hardening Report: Trusera--ai-bom/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Trusera--ai-bom/v3.1.0** was hardened automatically. 11 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) is broken: the 'Run ai-bom scan' step puts ${{ inputs.* }} expressions straight into the run: script. Examples: `ARGS="scan ${{ inputs.path }} --format ${{ inputs.format }} --quiet"`, `if [ -n "${{ inputs.output }}" ]; then`, `ARGS="$ARGS -o ${{ inputs.output }}"`, `if [ "${{ inputs.scan-level }}" = "deep" ]`, `ARGS="$ARGS --fail-on ${{ inputs.fail-on }}"`. The caller controls these inputs, so a crafted value can run shell commands. The script also runs `ai-bom $ARGS` with $ARGS unquoted, so the shell splits and glob-expands the input values. Fix: pass the inputs through env: variables and build the arguments as a quoted bash array.

Locations:

- `action.yml:54`
- `action.yml:57`
- `action.yml:58`
- `action.yml:66`
- `action.yml:71`
- `action.yml:72`
- `action.yml:76`

### unpinned-uses (severity: high)

Two composite steps use a version tag instead of a full commit SHA: `uses: actions/setup-python@v5` and `uses: github/codeql-action/upload-sarif@v3`.

Locations:

- `action.yml:41`
- `action.yml:81`

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

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 50a4c60..e6d958d 100644
--- a/action.yml
+++ b/action.yml
@@ -36,8 +36,25 @@ inputs:
 runs:
   using: "composite"
   steps:
     - name: Set up Python
       uses: actions/setup-python@v5
       with:
         python-version: "3.12"
         cache: "pip"
@@ -49,34 +66,40 @@ runs:
     - name: Run ai-bom scan
       id: scan
       shell: bash
+      env:
+        INPUT_PATH: ${{ inputs.path }}
+        INPUT_FORMAT: ${{ inputs.format }}
+        INPUT_OUTPUT: ${{ inputs.output }}
+        INPUT_SCAN_LEVEL: ${{ inputs.scan-level }}
+        INPUT_FAIL_ON: ${{ inputs.fail-on }}
       run: |
         # Build the base command
-        ARGS="scan ${{ inputs.path }} --format ${{ inputs.format }} --quiet"
+        ARGS=(scan "$INPUT_PATH" --format "$INPUT_FORMAT" --quiet)
 
         # Output file
-        if [ -n "${{ inputs.output }}" ]; then
-          ARGS="$ARGS -o ${{ inputs.output }}"
-        elif [ "${{ inputs.format }}" = "sarif" ]; then
+        if [ -n "$INPUT_OUTPUT" ]; then
+          ARGS+=(-o "$INPUT_OUTPUT")
+        elif [ "$INPUT_FORMAT" = "sarif" ]; then
           # Default SARIF output path for upload step
-          ARGS="$ARGS -o ai-bom-results.sarif"
+          ARGS+=(-o ai-bom-results.sarif)
           echo "sarif_file=ai-bom-results.sarif" >> "$GITHUB_OUTPUT"
         fi
 
         # Scan level
-        if [ "${{ inputs.scan-level }}" = "deep" ]; then
-          ARGS="$ARGS --deep"
+        if [ "$INPUT_SCAN_LEVEL" = "deep" ]; then
+          ARGS+=(--deep)
         fi
 
         # Fail-on severity threshold
-        if [ -n "${{ inputs.fail-on }}" ]; then
-          ARGS="$ARGS --fail-on ${{ inputs.fail-on }}"
+        if [ -n "$INPUT_FAIL_ON" ]; then
+          ARGS+=(--fail-on "$INPUT_FAIL_ON")
         fi
 
-        echo "Running: ai-bom $ARGS"
-        ai-bom $ARGS
+        echo "Running: ai-bom ${ARGS[*]}"
+        ai-bom "${ARGS[@]}"
 
     - name: Upload SARIF to GitHub Code Scanning
       if: ${{ inputs.format == 'sarif' && (steps.scan.outputs.sarif_file != '' || inputs.output != '') }}
-      uses: github/codeql-action/upload-sarif@v3
+      uses: github/codeql-action/upload-sarif@4bfa242aa49c5f7c6770b98670d354a7dfac05f8 # v3
       with:
         sarif_file: ${{ steps.scan.outputs.sarif_file || inputs.output }}

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses, prior-fix

**Notes:**

I fixed every finding in action.yml. Nothing was run or tested after the edits.

- **Script injection:** In the 'Run ai-bom scan' step, the five inputs (path, format, output, scan-level, fail-on) are now passed in through env: (INPUT_*) instead of being pasted into the script. The command is built as a bash array, and each input is one quoted argument, so the shell no longer splits or glob-expands them. This re-applies what the previous harden did, so it also covers the prior-fix finding.
- **Unpinned actions:** actions/setup-python is pinned to a26af69be951a213d495a4c3e4e4022e16d87065 (# v5), and github/codeql-action/upload-sarif to 9418214df1f2c4b8128c770156c409013b07d6b9 (# v3). Both SHAs came from looking up the tags. The v3 commit is newer than the one the previous harden used (4bfa242a…) because the tag has moved since.

One behaviour change: upstream left these inputs unquoted, so a `path` with several space-separated directories used to reach ai-bom as separate arguments. Now it arrives as a single argument. I treated `path` as a single directory because its description is "Directory to scan" and its default is `.`, and the previous harden made the same choice.

