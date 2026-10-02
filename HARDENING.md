<!-- markdownlint-disable -->

# Hardening Report: advanced-security--filter-sarif/v1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **advanced-security--filter-sarif/v1** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Three `${{ inputs.* }}` expressions are interpolated directly inside the `run:` shell command string. Specifically, `${{ inputs.input }}`, `${{ inputs.output }}`, and `${{ inputs.patterns }}` are embedded in the python3 invocation on line 19. Because GitHub Actions performs template substitution before the shell ever sees the command, an attacker-controlled input value containing shell metacharacters (`;`, `|`, `$(...)`, backticks, etc.) can break out of the quoted context and execute arbitrary commands. All three inputs should be moved to the `env:` block and referenced as double-quoted shell variables (e.g., `"$INPUT"`, `"$OUTPUT"`, `"$PATTERNS"`) instead.

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

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Moved all three ${{ inputs.* }} expressions (${{ inputs.input }}, ${{ inputs.output }}, ${{ inputs.patterns }}) from the run: shell block to an env: block in action.yml. They are now referenced as double-quoted shell variables ($INPUT, $OUTPUT, $PATTERNS) in the python3 invocation, preventing shell injection attacks. The patterns input is passed as a single double-quoted argument since the Python script uses --split-lines to split on newlines internally.

