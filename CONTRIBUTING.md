# Contributing and reporting issues

Contributions that improve correctness, reproducibility, or clarity are welcome after the repository owner decides how contributions should be handled and selects a software license.

When opening an issue, please provide:

1. MATLAB release and platform/OS.
2. Image Processing Toolbox release.
3. The **source and version / commit** of the third-party NSST implementation.
4. Exact error and full call stack, or a minimal **synthetic or publicly redistributable** reproduction.
5. `P` parameter values, input dimensions and channel counts, and whether images were registered.
6. Expected versus observed behavior and the NSST self-reconstruction RMSE.

**Do not share real patient images, names, dates of birth, DICOM identifiers, or other protected information in public GitHub issues.**

Pull requests should avoid changing experiment-defining defaults without documenting a new experiment configuration and a rationale. Benchmark claims require the tested environment, datasets/splits, metrics, statistical analysis, and independently repeatable scripts.
