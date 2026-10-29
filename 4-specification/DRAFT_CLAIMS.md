# Draft Claims (optional in a provisional; useful for focus)

1. A computer-implemented method comprising:
   (a) receiving an input audio signal and a parameter set comprising a target loudness value, a true-peak ceiling, and a tolerance;
   (b) computing a first metric set from the input audio signal using one or more loudness and peak measurement primitives;
   (c) determining a gain value from the target loudness value and the first metric set, and clamping the gain value such that a predicted peak does not exceed the true-peak ceiling;
   (d) rendering a rendered output signal by applying the gain value;
   (e) subsequent to rendering, computing a second metric set from the rendered output signal;
   (f) comparing the second metric set to the target loudness value and the true-peak ceiling under the tolerance to produce a comparison result; and
   (g) generating a report comprising a measurement section containing the first metric set, a render section containing a record of render actions, and a verification section containing the second metric set and the comparison result, wherein the measurement, render, and verification sections are structurally distinct and non-interleaved, and wherein the report is deterministic with respect to an input digest and a parameter digest.

2. The method of claim 1, further comprising, when the comparison result indicates the tolerance is exceeded, computing a correction from a residual, updating at least one of the gain value and a limiter parameter, and repeating steps (d)–(f), wherein each repetition appends a further render record and a further verification record to the report and prior records are retained.

3. The method of claim 2, wherein the repetition is bounded by a maximum iteration count recorded in the parameter set.

4. The method of claim 1, wherein step (e) comprises re-reading the rendered output signal from a written file, whereby effects of at least one of limiting, dither, bit-depth reduction, sample-rate conversion, and file encoding are captured in the second metric set.

5. The method of claim 1, wherein determinism is achieved by canonical key and array ordering, fixed numeric formatting, and quarantining non-deterministic fields into a provenance block excluded from a determinism guarantee.

6. The method of claim 1, wherein rendering further comprises producing an output at an integer multiple of an input sample rate, and wherein the integer multiple is recorded in the render section.

7. The method of claim 6, wherein the integer multiple is 8.

8. The method of claim 1, further comprising applying per-channel weights to a multichannel input, and recording the per-channel weights in the parameter digest.

9. The method of claim 1, further comprising dispatching a plurality of input audio signals to a plurality of worker processes, wherein a failure of one of the plurality of input audio signals produces a failure record in a corresponding report without terminating processing of remaining ones of the plurality of input audio signals.

10. A system comprising a processor and a memory storing instructions that, when executed, cause the system to perform the method of claim 1.

11. A non-transitory computer-readable medium storing instructions that, when executed, cause a computer to perform the method of claim 1.
