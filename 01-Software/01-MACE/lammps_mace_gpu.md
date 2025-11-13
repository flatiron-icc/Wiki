# 🧩 LAMMPS with MACE (Working Setup)

Hi all — Niamh and I had to wrangle a bit to get **LAMMPS with MACE** working, so I thought it might be useful to document the final setup that worked.

---

## 🧱 Load the Following Modules

```bash
module purge
module load modules/2.4-20250724
module load gcc/13.3.0 openmpi/4.1.8 intel-oneapi-mkl/2024.2.2 hdf5/1.12.3 boost/1.87.0 python/3.11.11 cmake/3.31.6 cuda/12.5.1 nccl/2.23.4-1
```

---

## 🐍 Create a New Environment (No System Packages)

```bash
python -m venv mace-torch-lammps
```

---

## 📦 Install Required Python Packages

```bash
pip install torch==2.5.1 torchvision==0.20.1 torchaudio==2.5.1 --index-url https://download.pytorch.org/whl/cu121
pip install --upgrade pip
pip install mace-torch
pip install cuequivariance-torch
pip install cuequivariance
pip install cuequivariance-ops-torch-cu12
pip install cupy-cuda12x
```

---

## ⚙️ Use This `kokkos-cuda.cmake` File

*(Instead of the one from the [MACE LAMMPS instructions](https://mace-docs.readthedocs.io/en/latest/guide/lammps_mliap.html))*:

```cmake
set(PKG_KOKKOS ON CACHE BOOL "" FORCE)
set(Kokkos_ENABLE_SERIAL ON CACHE BOOL "" FORCE)
set(Kokkos_ENABLE_CUDA   ON CACHE BOOL "" FORCE)
set(Kokkos_ARCH_AMPERE80 ON CACHE BOOL "" FORCE)
get_filename_component(NVCC_WRAPPER_CMD ${CMAKE_CURRENT_SOURCE_DIR}/../lib/kokkos/bin/nvcc_wrapper ABSOLUTE)
set(CMAKE_CXX_COMPILER ${NVCC_WRAPPER_CMD} CACHE FILEPATH "" FORCE)

# If KSPACE is also enabled, use CUFFT for FFTs
set(FFT_KOKKOS "CUFFT" CACHE STRING "" FORCE)

# Hide deprecation warnings temporarily for stable release
set(Kokkos_ENABLE_DEPRECATION_WARNINGS OFF CACHE BOOL "" FORCE)
```

---

## 🏗️ Final Steps

Then follow the remaining steps in the official MACE documentation to **compile and install**:  
👉 [https://mace-docs.readthedocs.io/en/latest/guide/lammps_mliap.html](https://mace-docs.readthedocs.io/en/latest/guide/lammps_mliap.html)
