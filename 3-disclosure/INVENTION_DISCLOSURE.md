# 3. Invention Disclosure Summary

**Working title:** Forensic Loudness Normalization Pipeline with Decoupled Measurement, Render, and Post-Render Verification Stages and Deterministic Audit Reporting

## One-paragraph disclosure

A loudness-normalization system processes an input audio file through three structurally distinct stages. A first measurement stage computes a set of loudness and peak metrics on the input. A render stage computes a gain from a target and the first measurement, clamps the gain against a true-peak ceiling, optionally applies a limiter, and writes a rendered output file. A verification stage re-measures the rendered output and compares the result against the target within a configured tolerance. If the tolerance is exceeded, a remediation loop recomputes and re-renders. The system emits a deterministic report in which measurement-stage data, render-stage actions, and verification-stage results are stored in three separate, non-interleaved sections, each bound to provenance data (input digest, parameter digest, tool version, ordering keys) so that two runs on the same input and parameters produce byte-identical reports.

## Explicitly excluded from claimed scope

The K-weighting filter coefficients, the −70 LUFS absolute gate, the −10 LU relative gate, the 400 ms / 3 s window definitions, LRA per EBU Tech 3342, and 4× oversampled true-peak detection. These are treated as known measurement primitives invoked by the pipeline.
