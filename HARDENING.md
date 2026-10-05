<!-- markdownlint-disable -->

# Hardening Report: advanced-security--filter-sarif/v1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **advanced-security--filter-sarif/v1.1** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a) violation: Three `inputs.*` expressions are directly interpolated inside the `run:` shell script, allowing an attacker-controlled workflow to inject arbitrary shell commands via metacharacters. Offending expressions on line 26: `${{ inputs.input }}`, `${{ inputs.output }}`, `${{ inputs.patterns }}`; and the same three expressions repeated on line 28. Note that `${{ inputs.severity }}` is correctly routed through the `env:` block as `$INPUT_SEVERITY` and used quoted, but the other three inputs are not. Fix: move `inputs.input`, `inputs.output`, and `inputs.patterns` into `env:` variables (e.g. `INPUT_INPUT`, `INPUT_OUTPUT`, `INPUT_PATTERNS`) and reference them as double-quoted shell variables (`"$INPUT_INPUT"`, etc.) in the `run:` block.

Locations:

- `action.yml:26`
- `action.yml:28`

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

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Moved inputs.input, inputs.output, and inputs.patterns from inline ${{ }} expressions in the run: block to the env: block as INPUT_INPUT, INPUT_OUTPUT, and INPUT_PATTERNS. These are now referenced as double-quoted shell variables in the run: script, eliminating all script injection vectors. The inputs.severity was already correctly handled via env: and was left unchanged.

