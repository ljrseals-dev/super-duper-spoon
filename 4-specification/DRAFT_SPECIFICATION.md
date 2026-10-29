# 4. Draft Provisional Specification

*Draft. Practitioner should edit for accuracy, consistency, and antecedent basis before filing.*

---

**TITLE:** FORENSIC LOUDNESS NORMALIZATION PIPELINE WITH DECOUPLED MEASUREMENT, RENDER, AND POST-RENDER VERIFICATION STAGES AND DETERMINISTIC AUDIT REPORTING

**FIELD:** Digital audio processing; loudness normalization; audio quality assurance; machine-generated audit reporting for media mastering workflows.

**BACKGROUND:** Loudness normalization tools typically measure an input, apply a gain, and write an output. The resulting file is generally not re-measured by the same tool, so the operator has no machine-verifiable assurance that the requested target and peak ceiling were actually achieved after limiting, dithering, bit-depth reduction, or sample-rate conversion. Where reporting exists, it commonly interleaves input measurements, applied actions, and output measurements into a single flat record, which makes automated diffing, regression testing, and audit comparison unreliable. Batch workflows frequently abort or lose per-item provenance when a single item fails. There is a need for a normalization pipeline in which the rendered result is verified as a distinct, first-class stage, and in which the audit record is structurally separated by stage and deterministic across runs.

**SUMMARY:** In one embodiment, a method comprises: receiving an input audio signal; computing a first metric set from the input signal using one or more standardized loudness and peak measurement primitives; determining a gain value from a target loudness value and the first metric set, and clamping the gain value so that a predicted peak does not exceed a true-peak ceiling; rendering a normalized output signal by applying the gain value and, optionally, a limiter; computing a second metric set from the rendered output signal; comparing the second metric set to the target loudness value and the true-peak ceiling under a tolerance; conditionally re-rendering when the comparison fails; and generating a report comprising three structurally distinct sections respectively containing the first metric set, a record of render actions, and the second metric set, wherein the report is deterministic with respect to the input signal and the parameter set.

## DETAILED DESCRIPTION

1. **System architecture.** The system comprises a decode/ingest module, a measurement module, a render module, a verification module, a remediation controller, a report generator, and a batch orchestrator. The modules may execute in a single process, in multiple processes, or across hosts. The measurement module is invoked at least twice per item: once on the input and once on the rendered output.

2. **Ingest.** The ingest module decodes the input into a working sample format and records an input digest over the decoded samples (or over the source bytes, or both). The ingest module also records the source sample rate, channel count, bit depth, and duration.

3. **Measurement primitives as known steps.** The measurement module computes metrics using published measurement primitives. In one embodiment the primitives comprise K-weighted integrated loudness, momentary loudness over a 400 ms window, short-term loudness over a 3 s window, loudness range, sample peak, and true peak obtained by oversampled interpolation. In one embodiment the oversampling factor is 4. In another embodiment the oversampling factor is configurable. These primitives are treated as known and are not themselves the subject of the claimed improvement; the improvement lies in how their outputs are sequenced, verified, and recorded.

4. **Channel weighting.** For multichannel input, the measurement module applies per-channel weights and sums channel energies. In one embodiment up to 8 channels are supported with explicit weights. In another embodiment the weights are read from a configuration profile associated with a delivery specification.

5. **First measurement stage.** The measurement module computes the first metric set from the input signal, optionally after a high-pass filter. The first metric set is stored as an immutable record tagged with a stage identifier. In one embodiment the first metric set is computed on the unmodified input; in another embodiment it is computed after a high-pass filter and the filter parameters are recorded in the render-parameter digest.

6. **Gain determination.** The render module computes a candidate gain as the difference between the target loudness value and the measured integrated loudness of the first metric set. The candidate gain is then clamped: the render module predicts the post-gain true peak and, if the prediction exceeds the true-peak ceiling, reduces the gain and/or engages the limiter such that the ceiling is respected.

7. **Render.** The render module applies the clamped gain and, where engaged, the limiter, and writes a rendered output signal. In one embodiment the render module also applies bit-depth conversion with dither and, optionally, sample-rate conversion. In one embodiment the render module optionally produces an output at an integer multiple of the input sample rate; in a specific embodiment the multiple is 8 (for example, 48 kHz to 384 kHz), and the multiple, the output rate, the bit depth, and the dither configuration are recorded as render actions.

8. **Second measurement stage (verification).** After rendering completes, the verification module re-reads the rendered output file and computes a second metric set using the same measurement primitives and the same channel weighting as the first stage. Computing the second metric set on the written file, rather than on an in-memory intermediate, is a deliberate feature: it captures effects introduced by limiting, dither, bit-depth reduction, sample-rate conversion, and file encoding.

9. **Tolerance test.** The verification module compares the second metric set against the target loudness value and the true-peak ceiling under a configured tolerance. In one embodiment the loudness tolerance is expressed in loudness units and the peak tolerance in decibels relative to full scale. The comparison produces a pass/fail result and a residual value for each checked metric.

10. **Remediation loop.** When the comparison fails, the remediation controller computes a correction from the residual, updates the gain and/or limiter parameters, and causes the render and verification stages to execute again. In one embodiment the loop is bounded by a maximum iteration count; in another embodiment it terminates when the residual falls below the tolerance or when the iteration count is exhausted. Each iteration appends a new render record and a new verification record to the report; prior iterations are retained rather than overwritten.

11. **Deterministic report.** The report generator emits a structured report comprising three structurally distinct sections: a measurement section containing the first metric set; a render section containing the render actions (gain applied, limiting engaged, bit depth, dither, output rate, output path, iteration index); and a verification section containing the second metric set, the tolerance test result, and residuals. The sections are non-interleaved. The report is made deterministic by: (a) canonical ordering of keys and array elements; (b) fixed numeric formatting; (c) exclusion or quarantine of non-deterministic fields such as wall-clock timestamps, hostname, and elapsed time into a separate provenance block that is excluded from the determinism guarantee; and (d) binding the report to an input digest and a parameter digest. Two executions with the same input digest and parameter digest produce byte-identical measurement, render, and verification sections.

12. **Batch orchestration.** The batch orchestrator dispatches items to worker processes. Each item produces its own report. A failure in one item is captured as a failure record in that item’s report and does not terminate the batch. In one embodiment the orchestrator emits an index report that references each per-item report by digest.

13. **Configuration.** A configuration profile specifies the target loudness value, true-peak ceiling, loudness and peak tolerances, channel weights, high-pass parameters, limiter parameters, output bit depth, dither, output rate multiple, maximum remediation iterations, and report determinism options.

14. **Alternative embodiments.** The measurement primitives may be any standardized loudness and peak measurement scheme. The report may be serialized as JSON, XML, YAML, CSV with fixed column order, or a binary format. Verification may be selectable as required, optional, or skipped, with the selection recorded in the report. The remediation loop may be omitted and the tolerance test used only as a pass/fail gate. The render stage may operate in the same or a different process from the verification stage. The pipeline may be exposed as a command-line interface, a library call, or a network service.

15. **Advantages.** The pipeline provides machine-verifiable assurance that the written file meets the requested target, surfaces discrepancies introduced by limiting or format conversion, retains a per-iteration audit trail, and produces reports that can be byte-compared across runs for regression testing and forensic review.

## FIGURES (to be prepared)

1. System architecture (ingest, measurement, render, verification, remediation, report, batch).
2. Three-stage pipeline with feedback path from verification to render.
3. Tolerance test and remediation-loop decision flow.
4. Deterministic report schema showing three non-interleaved sections plus a quarantined provenance block.
5. Batch orchestration with per-item isolation and an index report.
6. Optional high-rate output path.

## ABSTRACT

A loudness normalization pipeline computes a first metric set from an input audio signal, determines and clamps a gain against a true-peak ceiling, renders a normalized output, re-measures the rendered output file to produce a second metric set, tests the second metric set against the target within a tolerance, conditionally re-renders in a bounded remediation loop, and emits a deterministic report having three structurally distinct, non-interleaved sections for measurement, render actions, and verification results, bound to input and parameter digests.
