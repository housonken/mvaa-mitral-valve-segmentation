# MVAA Mitral Valve Segmentation

**Documentation release.** This repository documents the three MVAA research workflows, their data layout, and the software environments used in the thesis.

## Project Overview

This project accompanies the National University of Singapore master's thesis *Evaluating Mitral Valve Segmentation Across Cardiac CT, Three-Dimensional Echocardiography and Surgical Video*, supervised by Professor Li Lei. It develops and evaluates task-specific segmentation workflows for the three imaging modalities in the MVAA challenge.

## Research Objectives

- Adapt training and inference to the differences between volumetric CT, volumetric transesophageal echocardiography (TEE), and surgical video frames.
- Document the preprocessing, checkpoint selection, inference, and export steps needed to reproduce each workflow with authorized data.
- Measure overlap and boundary error using the evaluation split and sampling unit appropriate to each task.

## Three Tasks

| Task | Input | Target |
| --- | --- | --- |
| Task 1 | Cardiac CT volume | Binary mitral valve mask |
| Task 2 | 3D TEE volume | Anterior and posterior leaflet masks with background |
| Task 3 | Intraoperative RGB video frame | Binary mitral valve mask |

## Methodology

- **Cardiac CT.** A MONAI 3D U-Net uses supervised Dice and cross-entropy loss together with an exponential-moving-average teacher for unlabeled volumes. The local training implementation uses full precision for the unsupervised forward pass and loss. Inference uses sliding windows and exports a mask at the input size.
- **3D TEE.** The final workflow uses a five-fold nnU-Net v2 ResEnc-M 3D full-resolution model. The workflow uses mirror test-time augmentation within each fold and averages the five three-class probability maps on the original image grid before assigning labels.
- **Surgical video.** The final workflow averages probabilities from supervised and semi-supervised UNet++ models with EfficientNet-B4 encoders and mirrored views. A frame-level confidence rule can clear the entire mask. Evaluation uses video-level groupings and records false positives on empty reference frames.

The thesis work includes the CT training stability changes and evaluation, the frozen TEE inference and evaluation workflow, and surgical-video fusion, frame filtering, and video-level analysis. The official MVAA Baseline provides the starting point for several source files.

## Project Structure

| Path | Contents |
| --- | --- |
| `README.md` | Research scope and release status |
| `docs/data-layout.md` | Local input organization and access requirements |
| `docs/environment.md` | Dependencies recorded for the original workflows |
| `.gitignore` | Explicit list of reviewed paths |

## Installation

The documentation can be read directly. Consult the [environment notes](docs/environment.md) for the recorded Python, PyTorch, and task-specific environments. The versions describe the local experiments and have yet to be validated as a clean public installation.

## Training and Inference

| Task | Training | Inference |
| --- | --- | --- |
| CT | Pair labeled volumes and masks, keep the labeled validation split fixed, and use eligible unlabeled CT volumes for the teacher branch. | Load an authorized checkpoint, run sliding-window inference, and export NIfTI segmentation masks. |
| 3D TEE | Convert image and label pairs to nnU-Net layout, then plan, preprocess, and train the configured folds. | Run all five checkpoints, restore probabilities to the native grid, and average before assigning labels. |
| Video | Keep entire videos together when assigning labeled training and validation frames. Train with the designated unlabeled frames where applicable. | Fuse the two selected models, apply the frame-level confidence rule, and restore a binary PNG mask to the original frame size. |

These workflows were checked against the original research code. Prepare authorized inputs in accordance with the [data layout](docs/data-layout.md).

## Evaluation Metrics

- **Dice similarity coefficient (DSC)** measures overlap. CT uses foreground Dice, and TEE reports leaflet-specific Dice and the two-leaflet mean.
- **HD100 and ASD** measure boundary error. HD100 is the maximum symmetric Hausdorff distance. ASD is an average surface distance. Volume-based distances must be read with their evaluation geometry and recorded units. HD95 appears in supplementary volume analyses.
- **Video evaluation** reports foreground Dice and distances at the original frame size and aggregates frames within each video before averaging across videos. It also records false-positive empty frames and missed foreground frames. The archived all-frame Dice uses its recorded empty-mask convention.

## Experimental Results

The local research archive records a fixed internal CT holdout, out-of-fold and same-source labeled holdout evaluations for 3D TEE, and internal video-level validation. It also records local mask exports for the official validation split of each task. Reference labels were unavailable locally for those inputs. Quantitative results and figures are held in the thesis research archive.

## Data Availability and Restrictions

Obtain MVAA inputs through the challenge organizers under the applicable access and use conditions. Obtain MVSeg2023 through its gated dataset provider. Medical images, annotations, masks, case-level artifacts, and model checkpoints are held outside this repository. [MVAA challenge record](https://doi.org/10.5281/zenodo.19726755) · [MVSeg2023 dataset card](https://huggingface.co/datasets/pcarnahan/MVSeg2023)

## Acknowledgements and References

- Master's research at the National University of Singapore under Professor Li Lei. The affiliation and supervisor are provided for research attribution.
- Bo Deng and Jieyun Bai, *Baseline Method of the MVAA 2026 Challenge for Multimodal Mitral Valve Segmentation*, MICCAI 2026 satellite events. [Paper](https://papers.miccai.org/miccai-2026-sat/MVAA_035.html) · [Official MVAA Baseline at the reviewed revision](https://github.com/db0725/MVAA/tree/25274d63b6842575e782251968aa0318a168dc73)
- Fabian Isensee et al., *nnU-Net: a self-configuring method for deep learning-based biomedical image segmentation*, Nature Methods, 2021. [Paper](https://doi.org/10.1038/s41592-020-01008-z) · [nnU-Net](https://github.com/MIC-DKFZ/nnUNet)
- The original workflows use MONAI for medical-image training and evaluation and Segmentation Models PyTorch for the surgical-frame models. [MONAI](https://github.com/Project-MONAI/MONAI) · [Segmentation Models PyTorch](https://github.com/qubvel-org/segmentation_models.pytorch)
- Patrick Carnahan et al., *DeepMitral: Fully Automatic 3D Echocardiography Segmentation for Patient Specific Mitral Valve Modelling*, MICCAI 2021. The MVSeg2023 provider requests this citation. [Paper](https://doi.org/10.1007/978-3-030-87240-3_44) · [MVSeg2023 dataset card](https://huggingface.co/datasets/pcarnahan/MVSeg2023)
