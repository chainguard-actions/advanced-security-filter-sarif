<!-- markdownlint-disable -->

# Hardening Report: advanced-security--filter-sarif/v1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **advanced-security--filter-sarif/v1** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) is violated: the composite step 'filter' puts inputs.input, inputs.output and inputs.patterns into the run: script as ${{ }} expressions. Offending line: python3 "$GITHUB_ACTION_PATH/filter_sarif.py" --input "${{ inputs.input }}" --output "${{ inputs.output }}" --split-lines -- "${{ inputs.patterns }}". The values are pasted into the script text before bash runs, so the double quotes don't protect them. An input containing a quote or $(...) can run commands. Fix: pass the inputs in through env: variables (for example INPUT_FILE, OUTPUT_FILE, PATTERNS) and use them double-quoted in the script.

Locations:

- `action.yml:19`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.input }}" appears directly in run: block of step "filter"; move to env: map

Locations:

- `action.yml:19`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.output }}" appears directly in run: block of step "filter"; move to env: map

Locations:

- `action.yml:19`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.patterns }}" appears directly in run: block of step "filter"; move to env: map

Locations:

- `action.yml:19`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index b45cfe0..d1b23a4 100644
--- a/action.yml
+++ b/action.yml
@@ -13,8 +13,29 @@ inputs:
 runs:
   using: "composite"
   steps:
     - name: filter
+      env:
+        INPUT: ${{ inputs.input }}
+        OUTPUT: ${{ inputs.output }}
+        PATTERNS: ${{ inputs.patterns }}
       run: |
         unset LD_PRELOAD
-        python3 "$GITHUB_ACTION_PATH/filter_sarif.py" --input "${{ inputs.input }}" --output "${{ inputs.output }}" --split-lines -- "${{ inputs.patterns }}"
+        python3 "$GITHUB_ACTION_PATH/filter_sarif.py" --input "$INPUT" --output "$OUTPUT" --split-lines -- "$PATTERNS"
       shell: bash

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, prior-fix

**Notes:**

In action.yml, the 'filter' step no longer pastes the input, output and patterns inputs into its run: script as ${{ }} expressions. They now come in through env: as INPUT_FILE, OUTPUT_FILE and PATTERNS, and the script uses them double-quoted. This re-applies the previous harden's fix with different variable names. Patterns is still passed as one argument, as upstream did, because filter_sarif.py splits it into lines itself (--split-lines). No tests were run.

