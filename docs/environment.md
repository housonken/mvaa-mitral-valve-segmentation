# Environment notes

The research used Python 3.9.23 with PyTorch 2.7.1 and the CUDA 12.8 build. TorchVision 0.22.1 with the matching CUDA build is recorded for the Task 3 delivery environment. Use a separate environment for each task when reproducing the recorded dependency sets. [PyTorch version-specific installation instructions](https://pytorch.org/get-started/previous-versions/)

| Workflow | Recorded versions and dependencies |
| --- | --- |
| Task 1 CT | MONAI 1.5.2, NumPy 1.24.3, PyTorch, NiBabel. |
| Task 2 TEE | nnU-Net v2 2.5.2, dynamic-network-architectures 0.3.1, NumPy 1.26.4, SciPy 1.13.1, NiBabel 5.3.3, SimpleITK 2.5.6, batchgenerators 0.25.3, and batchgeneratorsv2 0.3.5. |
| Task 3 portable inference | NumPy 1.24.3, Pillow 11.1.0, Segmentation Models PyTorch 0.5.0, timm 1.0.22, and safetensors 0.6.2, with the PyTorch and TorchVision builds recorded above. The original training and evaluation code also uses MONAI. |

These are observed settings from the research environment and frozen inference records. The Task 2 workflow used locally frozen preprocessing plans and Windows-specific runtime adjustments. Clean-environment testing of the portable workflows remains pending.

Install external libraries from their upstream distributions. For nnU-Net use the documented `nnUNet_raw`, `nnUNet_preprocessed`, and `nnUNet_results` variables to point to authorized local directories. [nnU-Net installation guide](https://github.com/MIC-DKFZ/nnUNet/blob/master/documentation/installation_instructions.md)
