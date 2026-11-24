# Local data layout

Keep authorized inputs, checkpoints, and outputs in a restricted local working directory. Use unique case or video keys only in local manifests. The paths below describe input relationships. Confirm the source-specific naming and access conditions when obtaining data.

| Task | Local input | Label and split conventions |
| --- | --- | --- |
| Task 1 | Cardiac CT volumes in NIfTI format, with separate labeled and unlabeled inputs. | The loader pairs each labeled volume with a corresponding `-seg.nii.gz` file. Preserve the source geometry and the case-level holdout. |
| Task 2 | One 3D TEE NIfTI volume per case. The local conversion reads paired `-US.nii.gz` and `-label.nii.gz` training files. | The prepared nnU-Net layout uses `imagesTr/`, `labelsTr/`, and `imagesTs/`. Label 0 is background, label 1 is the posterior leaflet, and label 2 is the anterior leaflet. Keep the approved fold definitions and held-out datasets locally. |
| Task 3 | RGB frames in video-grouped directories. | The training loader can read paired binary PNG masks or the compatible label archives. Preserve the video-level split. The exporter writes binary masks at the original frame size. |

Keep the official validation inputs separate from training. Evaluate labeled splits with their own labels and treat exports for unlabeled inputs as an inference check. Store input-derived visualizations, probability maps, manifests, logs, and submissions with the restricted working artifacts.

The challenge proposal provides background on the three tasks. The MVSeg2023 provider lists its license and gates access to the TEE files. [MVAA challenge record](https://doi.org/10.5281/zenodo.19726755) · [MVSeg2023 dataset card](https://huggingface.co/datasets/pcarnahan/MVSeg2023)
