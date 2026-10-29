# Implementation Notes (relative to current Little-sQrt v2.0.1 source)

These notes are for the inventor / practitioner file. They are **not** part of the specification.

## Present in current repository

- Pre-render measurement (integrated, LRA, momentary/short-term max, sample peak, true peak).
- Gain calculation to target + true-peak ceiling trim.
- Optional 8× high-rate PCM output.
- Post-render analysis producing a stage-separated JSON report (`measurement` / `render` / `verification`).
- Residual error reporting (`loudness_error_lu`) after TP trim.
- Multi-process batch with per-job isolation.
- Optional Numba acceleration with NumPy/SciPy fallback.

## Aspirational relative to current source (described in draft claims but not yet reduced to practice)

- Configured tolerance test that triggers a remediation loop.
- Bounded iteration with retained per-iteration render + verification records.
- Re-reading the *written* output file for verification (current code analyzes the in-memory buffer before `sf.write`).
- Full determinism machinery (canonical ordering, fixed numeric formatting, quarantined provenance block, input/parameter digests guaranteeing byte-identical core sections).

## Recommendation

Either implement the missing closed-loop and determinism features before relying on the stronger claim language, or narrow the provisional claims/specification to the single-pass, stage-separated verification report that currently exists in the code.
