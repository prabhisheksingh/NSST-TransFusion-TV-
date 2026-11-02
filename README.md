# NSST-TransFusion-TV

**Deterministic cross-modal medical image fusion using the non-subsampled shearlet transform (NSST), bidirectional patch-token cross-attention, and total-variation refinement.**

This repository contains a **MATLAB research prototype** for fusing two already-registered images (for example, MRI with PET or SPECT). Its core implementation is in [`src/NSST_TransFusion_TV.m`](src/NSST_TransFusion_TV.m).

> **Terminology note:** despite the project's historical name, the current implementation uses *deterministic, transformer-style cross-attention*, **not** a trained Transformer. It has no learned query/key/value projections, pretrained model, training procedure, or learned weights. Do not describe it as a trained deep-learning model.

## Pipeline

```text
Registered modality 1 + registered modality 2
                    |
     Convert to luminance (resize 256 x 256)
                    |
             NSST decomposition
                    |
       +------------+-------------+
       |                          |
   Low-frequency               High-frequency
   coefficients                directional bands
       |                          |
   Bidirectional               Bidirectional
   patch-token                  patch-token
   cross-attention              cross-attention
   + local activity             + local activity
       |                          |
       +------------+-------------+
                    |
              Inverse NSST
                    |
             TV dual/FISTA
                    |
        Optional detail reinjection
                    |
      Optional chroma from modality 2
                    |
         Final fused image + metrics
```

## Requirements

- MATLAB **R2021b or newer** (specified by the original source; **not verified by executing MATLAB in this package**).
- MATLAB **Image Processing Toolbox**.
- A **third-party NSST implementation** providing `nsst_dec2.m`, `nsst_rec2.m`, and their supporting functions, with the API used in the code. **Not included**: obtain it separately and check its license and compatibility.
- Two **spatially registered** image files; supported file types include PNG, JPG, BMP, and TIFF.
- No GPU, model weights, training data, or Python environment is required by the supplied script.

See [Installation and setup](docs/INSTALLATION.md).

## Quick start

1. Download/extract this folder, and open MATLAB with the project root as the **Current Folder**.
2. Make the NSST toolbox available on the MATLAB path, e.g.:

   ```matlab
   addpath(genpath('C:\path\to\your\NSST_toolbox'));
   ```

   You can also let the main script prompt you to select the toolbox folder if the two entry-point functions are missing.
3. Run the included dependency checks:

   ```matlab
   run('scripts/check_environment.m')
   ```

4. Run the main script:

   ```matlab
   run('src/NSST_TransFusion_TV.m')
   ```

5. Select **first/structural** and **second/functional** image when prompted. These images must be registered beforehand.
6. Inspect the figures and the default output directory `NSST_TransFusion_TV_Output/` in the MATLAB **current working directory**.

**For a no-patient-data demonstration:** run `run('examples/generate_synthetic_demo_inputs.m')`, then select the generated sample images. These are *synthetic technical test images*, not a clinical dataset or a performance benchmark.

## Repository layout

```text
NSST-TransFusion-TV_GitHub/
├── README.md
├── CITATION.cff
├── CHANGELOG.md
├── CONTRIBUTING.md
├── LICENSE_GUIDANCE.md
├── .gitignore
├── src/
│   └── NSST_TransFusion_TV.m        # Original supplied 877-line MATLAB source
├── scripts/
│   ├── check_environment.m          # Dependency report
│   └── smoke_test_nsst.m            # NSST reconstruction check
├── examples/
│   ├── README.md
│   └── generate_synthetic_demo_inputs.m
├── data/
│   └── README.md                    # Dataset and privacy guidance; no data shipped
└── docs/
    ├── INSTALLATION.md
    ├── PARAMETERS.md
    ├── OUTPUTS_AND_METRICS.md
    ├── REPRODUCIBILITY.md
    └── RESEARCH_LIMITATIONS.md
```

## Default configuration (in the supplied script)

| Setting | Default | Notes |
|---|---|---|
| `P.resizeTo` | `[256 256]` | Both modalities are resized to this size |
| `P.pfilt` | `'maxflat'` | NSST filter name; toolbox-dependent |
| `P.dcomp` / `P.dsize` | `[3 3 4]` / `[8 8 16]` | Three directional levels |
| `P.lowPatch` / `P.highPatch` | `16` / `8` | Patch-token edge lengths |
| `P.tvLambda` | `0.018` | TV regularization used in THIS code snapshot |
| `P.tvIterations` / `P.tvStep` | `60` / `0.12` | Iteration count and dual step |
| `P.detailReinjection` | `0.08` | Can partly reintroduce noise |
| `P.preserveColorFromImg2` | `true` | Chroma taken from second image when RGB |
| `P.saveIntermediate` | `true` | Produces images, metrics CSV, and MAT file |

For the remaining settings, see [Parameter reference](docs/PARAMETERS.md).

## Generated files

The program creates separate folders for luminance inputs, low- and high-frequency NSST coefficients, attention weights, and final fused images, as well as `Fusion_Metrics.csv` and a MATLAB workspace file. See [Outputs and metrics](docs/OUTPUTS_AND_METRICS.md). Generated outputs are excluded from Git by `.gitignore`.

## Scientific use and limitations

- **Image registration is assumed**, not performed; misaligned images can invalidate fusion and comparisons.
- Reported scores are **no-reference / input-referenced image statistics** and **do not establish diagnostic accuracy** or clinical utility. The script contains no tumor segmentation stage or clinical validation.
- Parameters and preprocessing need to be frozen before unbiased test-set assessment. The default `P.tvLambda=0.018` may differ from earlier manuscript experiments; reconcile paper, code, and tables before publishing results.
- NSST reconstruction behavior depends on the **exact external toolbox version and filters**. Save that information with experimental results.
- Outputs are resized to 256 x 256, not original resolution. Quantitative results depend on this resizing and on each input's luminance normalization.
- No claim of execution-based validation is made for this packaged snapshot; run both checks and an end-to-end test using MATLAB in your own environment.

See [Research limitations](docs/RESEARCH_LIMITATIONS.md) and [Reproducibility checklist](docs/REPRODUCIBILITY.md).

## Citation

GitHub can display a **Cite this repository** widget using [`CITATION.cff`](CITATION.cff). The citation metadata here describes the software only; add your final paper DOI **only after** it is assigned, and confirm author metadata before publication.

## License and external dependencies

**No software license has been chosen or granted by this package.** Read [`LICENSE_GUIDANCE.md`](LICENSE_GUIDANCE.md) and add a suitable `LICENSE` file before making this repository available for reuse. The external NSST toolbox has its own licensing conditions and must be credited separately; do not copy it into this repository without permission.

## Contributions

Please open an issue with MATLAB version, Image Processing Toolbox version, NSST implementation/version, input dimensions, parameters, and steps to reproduce. See [`CONTRIBUTING.md`](CONTRIBUTING.md).
