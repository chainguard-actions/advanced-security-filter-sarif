<!-- markdownlint-disable -->

# Hardening Report: advanced-security--filter-sarif/v1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **advanced-security--filter-sarif/v1.1** was hardened automatically. 8 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) is violated: the composite step 'filter' puts ${{ inputs.input }}, ${{ inputs.output }} and ${{ inputs.patterns }} straight into the run: script. The offending line is: python3 "$GITHUB_ACTION_PATH/filter_sarif.py" --input "${{ inputs.input }}" --output "${{ inputs.output }}" --split-lines --severity "$INPUT_SEVERITY" -- "${{ inputs.patterns }}" (the else branch has the same expressions). GitHub substitutes these values before the shell runs, so double quotes do not protect them. A value containing " or $(...) can run any command. Fix: pass them through env: variables (e.g. INPUT_INPUT, INPUT_OUTPUT, INPUT_PATTERNS) and use them as "$VAR".

Locations:

- `action.yml:25`
- `action.yml:27`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.input }}" appears directly in run: block of step "filter"; move to env: map

Locations:

- `action.yml:25`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.output }}" appears directly in run: block of step "filter"; move to env: map

Locations:

- `action.yml:25`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.patterns }}" appears directly in run: block of step "filter"; move to env: map

Locations:

- `action.yml:25`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.input }}" appears directly in run: block of step "filter"; move to env: map

Locations:

- `action.yml:27`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.output }}" appears directly in run: block of step "filter"; move to env: map

Locations:

- `action.yml:27`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.patterns }}" appears directly in run: block of step "filter"; move to env: map

Locations:

- `action.yml:27`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 02c16af..1ff0bfe 100644
--- a/action.yml
+++ b/action.yml
@@ -16,14 +16,34 @@ inputs:
 runs:
   using: "composite"
   steps:
     - name: filter
       env:
         INPUT_SEVERITY: ${{ inputs.severity }}
+        INPUT_INPUT: ${{ inputs.input }}
+        INPUT_OUTPUT: ${{ inputs.output }}
+        INPUT_PATTERNS: ${{ inputs.patterns }}
       run: |
         unset LD_PRELOAD
         if [ -n "$INPUT_SEVERITY" ]; then
-          python3 "$GITHUB_ACTION_PATH/filter_sarif.py" --input "${{ inputs.input }}" --output "${{ inputs.output }}" --split-lines --severity "$INPUT_SEVERITY" -- "${{ inputs.patterns }}"
+          python3 "$GITHUB_ACTION_PATH/filter_sarif.py" --input "$INPUT_INPUT" --output "$INPUT_OUTPUT" --split-lines --severity "$INPUT_SEVERITY" -- "$INPUT_PATTERNS"
         else
-          python3 "$GITHUB_ACTION_PATH/filter_sarif.py" --input "${{ inputs.input }}" --output "${{ inputs.output }}" --split-lines -- "${{ inputs.patterns }}"
+          python3 "$GITHUB_ACTION_PATH/filter_sarif.py" --input "$INPUT_INPUT" --output "$INPUT_OUTPUT" --split-lines -- "$INPUT_PATTERNS"
         fi
       shell: bash

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, prior-fix

**Notes:**

Moved inputs.input, inputs.output and inputs.patterns into the filter step's env: block (INPUT_INPUT, INPUT_OUTPUT, INPUT_PATTERNS) and referenced them as double-quoted shell variables in both branches. Patterns stays a single argument, as upstream quoted it; filter_sarif.py splits it with --split-lines. This re-applies the prior harden patch.

