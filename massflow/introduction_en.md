# Introduction

This image is designed for the high-performance computing Massflow framework. It is built upon the official NVIDIA CUDA image and integrates the next-generation Python package management tool uv, as well as the R language environment.

## Included Toolset

### System Development and Compilation Tools

- **Compilation Chain**: GCC, GFortran, pkg-config, libc6-dev.
- **Math and Science Libraries**: BLAS, LAPACK, FFTW3 (Fast Fourier Transform).
- **Image and Database**: LibTIFF, LibJPEG, LibPNG, LibXML2, LibCurl (OpenSSL).
- **Version Control**: Git (pre-configured with Git LFS to support large file version control).
- **Remote Connection**: OpenSSH Server (configured to allow Root login for convenient remote development).
- **Common Tools**: `vim`, `wget`, `curl`, `htop`, `less`, `bzip2`, `sudo`.

### Programming Languages and Package Management

1. Integrated **uv** (an extremely fast Python package manager and resolver), designed to replace the traditional pip/virtualenv workflow.

2. **R Language Environment**:

- Version: R 4.5.2+
- Core Library: Integrated BiocManager and Cardinal

### Image Tag Naming Convention

The tags for this image are dynamically generated to intuitively display version information for key components.

**Tag Format:** `ub<Ubuntu Version>_cuda<CUDA Major.Minor Version>_cardinal<Cardinal Major.Minor Version>`

| Component    | Meaning      | Description                            |
| ------------ | ------------ | -------------------------------------- |
| `ub24`       | Ubuntu 24.x  | Built on Ubuntu 24.04                  |
| `cuda131`    | CUDA 13.1    | CUDA compilation toolchain version     |
| `cardinal312` | Cardinal 312 | Major and minor version of the R Cardinal package |

### Usage Instructions

- **Working Directory**: Defaults to `/home/Share_Space`. The directory permissions are set to `777` to facilitate external volume mounting.
- **SSH Service**: The image has the SSH service installed and the run directory `/var/run/sshd` created. You can connect for remote development via port mapping.
- **Version Check**: There is a `/version.txt` file in the image root directory, which records the specific version snapshot at build time.

```bash
docker run -it \
  --gpus all \
  -v "${HOME}/Share_Space:/home/Share_Space" \
  -p 8001:22 \
  --rm \
  neonexusx/massflow:latest
```

After connecting via SSH, please restart the SSH service and change the password first:

```bash
service ssh restart && passwd 
```

### Software Test Code

Install Cupy and verify functionality:

https://github.com/NeoNexusX/Example/tree/main/Python/cupy

Numba-CUDA test (Note: only supports CUDA 12 and above versions):

https://github.com/NeoNexusX/Example/blob/main/Python/numba-cuda/numba-cuda.py

```bash
root@1340ea303917:/home/Share_Space/cupy_test# uv run main.py 
Using device: 0 - NVIDIA GeForce RTX 4060 Laptop GPU
device.compute_capability : 89
Total Memory (MiB): 7805.06 MiB
Hello from cupy-test!
Free Memory: 6541.12 MiB
Allocating array with 285786112 elements (approx 2180.38 MiB)...
assert x_huge.nbytes == target_bytes : True
Norm calculated: 16905.209611241145
Press Enter to free memory and exit...
```
