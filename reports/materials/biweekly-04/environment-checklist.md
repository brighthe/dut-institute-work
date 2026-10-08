---
title: "编译环境与验证流程"
type: material
tags:
  - reports
  - hpc
status: draft
cycle: 2026-09-07..2026-09-18
origin: "Environment_Checklist.md"
author: 宋维豪（算海）
origin_date: 2026-09-09
date_added: 2026-09-10
date_update: 2026-09-14
---

> 来源：宋维豪（算海），2026-09-09 发于飞书「（内部）大连理工项目算海内部群」（`Environment_Checklist.md`，10.9 KB），附言「大致编译流程，后续我再优化一版」，**非定稿**。原文照录归档，未作改写。

# 编译环境与验证流程

## 1. 221节点基本配置

| 组件 | 配置 |
| --- | --- |
| Intel C/C++编译器 | 2025.0.4 |
| Intel Fortran编译器 | 2025.0.4 |
| Intel MPI | 2021.14，Build 20241121 |
| Intel MKL | 2025.0 |
| 服务器glibc | 2.35，`libc6 2.35-0ubuntu3.14` |
| 服务器X11运行库 | `libX11.so.6` |
| PETSc标量 | complex、double |
| PETSc索引 | 64位 |
| BLAS/LAPACK索引 | 64位，MKL ILP64 |
| PETSc并行配置 | MPI、OpenMP |
| PETSc求解器 | MKL PARDISO、MKL CPARDISO |
| PETSc目录 | `/data/3rdparty/intel-opt-zmo` |

本地后续采用LP64构建。上传服务器验证时需要同时上传本地LP64依赖，不能加载221节点现有的ILP64 PETSc。

本地主机保持现有Ubuntu版本，不要求修改操作系统。服务器交付物统一在本机Docker的Ubuntu 22.04兼容环境中构建。当前容器与221节点均为glibc 2.35、`libc6 2.35-0ubuntu3.14`；其余编译器和依赖版本按221节点基线配置。


## 2. 从零配置编译环境

### 2.1 解压第三方依赖

先准备更新后的 `intel32-petsc64-opt-zmo.zip`。压缩包内部已经包含 `data/3rdparty`，因此应解压到根目录：

```bash
sudo unzip intel32-petsc64-opt-zmo.zip -d /
```

解压后确认目录存在：

```bash
ls /data/3rdparty/intel32-petsc64-opt-zmo
```

该压缩包包含本地LP64依赖，可以直接用于本文流程；它与221节点现有的ILP64 PETSc不是同一接口。

### 2.2 安装基础工具

```bash
sudo apt update
sudo apt install -y ca-certificates wget gpg build-essential cmake pkg-config unzip rsync docker.io
```

### 2.3 添加Intel软件源

```bash
sudo mkdir -p /usr/share/keyrings
wget -O- https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS.PUB | gpg --dearmor | sudo tee /usr/share/keyrings/oneapi-archive-keyring.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/oneapi-archive-keyring.gpg] https://apt.repos.intel.com/oneapi all main" | sudo tee /etc/apt/sources.list.d/oneAPI.list
sudo apt update
```

### 2.4 安装指定版本

```bash
sudo apt install --no-install-recommends \
  intel-oneapi-compiler-dpcpp-cpp-2025.0=2025.0.4-1519 \
  intel-oneapi-compiler-fortran-2025.0=2025.0.4-1519 \
  intel-oneapi-mkl-devel-2025.0=2025.0.1-14 \
  intel-oneapi-mpi-2021.14=2021.14.1-5 \
  intel-oneapi-mpi-devel-2021.14=2021.14.1-5
```

必须明确指定 MPI 的 `2021.14.1-5`，否则APT可能安装仓库中较新的2021.14补丁版本。

### 2.5 配置终端环境

编辑用户环境文件：

```bash
vim ~/.bashrc
```

在文件末尾加入：

```bash
source /opt/intel/oneapi/compiler/2025.0/env/vars.sh
source /opt/intel/oneapi/mpi/2021.14/env/vars.sh
source /opt/intel/oneapi/mkl/2025.0/env/vars.sh

export PETSC_DIR=/data/3rdparty/intel32-petsc64-opt-zmo
export PKG_CONFIG_PATH="$PETSC_DIR/lib/pkgconfig${PKG_CONFIG_PATH:+:$PKG_CONFIG_PATH}"
export LD_LIBRARY_PATH="$PETSC_DIR/lib${LD_LIBRARY_PATH:+:$LD_LIBRARY_PATH}"
export CMAKE_PREFIX_PATH="$PETSC_DIR:/opt/intel/oneapi/mkl/2025.0/lib/cmake/mkl${CMAKE_PREFIX_PATH:+:$CMAKE_PREFIX_PATH}"
```

使配置生效：

```bash
source ~/.bashrc
```

同一个终端不要再加载其他版本的 `setvars.sh`。

### 2.6 修改CMake预设

在项目 `CMakePresets.json` 的Linux预设中确认以下内容：

```json
"CMAKE_PREFIX_PATH": "/data/3rdparty/intel32-petsc64-opt-zmo/",
"MKL_INTERFACE": "lp64"
```

环境变量部分确认：

```json
"LD_LIBRARY_PATH": "/data/3rdparty/intel32-petsc64-opt-zmo/lib/:$penv{LD_LIBRARY_PATH}",
"PETSC_DIR": "/data/3rdparty/intel32-petsc64-opt-zmo"
```

不需要在预设中固定 `MKL_DIR`；加载 `/opt/intel/oneapi/mkl/2025.0/env/vars.sh` 后，CMake可通过 `MKLROOT` 找到MKL。

Intel软件源配置参考 [Intel官方安装说明](https://www.intel.com/content/www/us/en/docs/oneapi-toolkit/installation-guide-linux/latest/install-oneapi-toolkit-with-apt.html)。

### 2.7 Ubuntu 22.04构建容器

容器内的软件版本参照第1章221节点基本配置，本节仅保留镜像构建和挂载进入命令。

构建镜像：

```bash
docker build -t sgsim-build:ubuntu22-gcc11 /path/to/dockerfile # 修改为Dockerfile所在目录
```

进入容器并挂载编译环境：

```bash
SGSIM_SOURCE=/path/to/SGSim        # 修改为本机实际源码目录
docker run --rm -it \
  --user "$(id -u):$(id -g)" \
  -e HOME=/tmp \
  -v "$SGSIM_SOURCE:/SGSim" \
  -v /opt/intel/oneapi:/opt/intel/oneapi:ro \
  -v /data/3rdparty:/data/3rdparty:ro \
  -w /SGSim \
  sgsim-build:ubuntu22-gcc11 bash
```


## 3. 验证本地环境

按2.7进入容器并按2.5加载oneAPI环境后，只检查必要软件版本：

```bash
getconf GNU_LIBC_VERSION
gcc --version | head -1
cmake --version | head -1
icpx --version | head -1
mpiexec --version | head -1
grep BOOST_LIB_VERSION /usr/include/boost/version.hpp
grep -E '^#define __INTEL_MKL(__|_MINOR__|_UPDATE__)' /opt/intel/oneapi/mkl/2025.0/include/mkl_version.h
grep -E '^#define PETSC_VERSION_(MAJOR|MINOR|SUBMINOR)' /data/3rdparty/intel32-petsc64-opt-zmo/include/petscversion.h
grep '^#define HYPRE_RELEASE_VERSION' /data/3rdparty/intel32-petsc64-opt-zmo/include/HYPRE_config.h
```

版本应为glibc 2.35、GCC 11.4、Boost 1.74、Intel编译器2025.0、Intel MPI 2021.14、MKL 2025.0、PETSc 3.22.1和Hypre 2.33.0。

## 4. 本地构建和服务器验证

### 4.1 本地编译、验证、打包与服务器解压

按2.7进入容器并按2.5加载oneAPI环境后，生成Debug程序：

```bash
cmake --preset linux_gcc_debug
cmake --build build/debug --parallel "$(nproc)"
```

使用SGSim内置算例，并在构建目录保存验证结果：

```bash
CASE_PATH=/SGSim/TestData/SGFem/Task/Input/CELAS1_TwoNodalDofs.bdf
OUTPUT_DIR=/SGSim/build/debug/validation/CELAS1_TwoNodalDofs
mkdir -p "$OUTPUT_DIR"
```

请根据实际需要选择求解器。默认求解器在 `/SGSim/Resource/cmake/SGConfig.cmake` 中设置，配置模板位于 `/SGSim/Resource/config/solver.conf`；修改默认配置后需要重新运行CMake。若算例目录存在同名 `.conf` 文件，可通过该文件设置算例使用的求解器。

使用2个Intel MPI进程运行PETSc CG/Jacobi测试：

```bash
export PETSC_OPTIONS="-ksp_type cg -pc_type jacobi -ksp_rtol 1e-8 -ksp_max_it 10000 -ksp_monitor_short -ksp_converged_reason -ksp_error_if_not_converged"
export OMP_NUM_THREADS=2
export MKL_NUM_THREADS=2
export MKL_DYNAMIC=FALSE
export I_MPI_FABRICS=shm
export LD_LIBRARY_PATH="/SGSim/build/debug/lib:/data/3rdparty/intel32-petsc64-opt-zmo/lib:$LD_LIBRARY_PATH:/SGSim/Artifact/Ubuntu22-gcc11.4/lib"
mpirun -n 2 /SGSim/build/debug/bin/SGFEM \
  -i "$CASE_PATH" \
  -w "$OUTPUT_DIR/" 2>&1 | tee "$OUTPUT_DIR/petsc_cg_console.log"
```

PETSc参数通过 `PETSC_OPTIONS` 传入，不直接追加到SGFEM命令行。当前内置算例使用2个MPI进程，在1次迭代后因 `CONVERGED_ATOL` 收敛，HDF5结果正常导出。

验证成功后，将Debug构建结果、依赖和服务器脚本统一打包：

```bash
ARCHIVE_PATH=/tmp/SGSim_debug_lp64_ubuntu22.tar.gz

SGSIM_SOURCE=/path/to/SGSim          # 修改为本机实际源码目录
DEPLOY_FILES=/path/to/deploy_files   # 修改为服务器脚本所在目录

tar -czf "$ARCHIVE_PATH" \
  -C "$DEPLOY_FILES" \
  set_envs.sh run_multi.sh \
  -C "$SGSIM_SOURCE" \
  build/debug \
  Artifact/Ubuntu22-gcc11.4 \
  ThirdParty/Ubuntu22-gcc11.4 \
  -C /data/3rdparty \
  intel32-petsc64-opt-zmo
```

`ARCHIVE_PATH` 是本地压缩包的输出路径和文件名，可以根据本地磁盘空间自行修改。服务器算例单独存放，不加入程序压缩包。

将压缩包上传服务器后解压：

```bash
ARCHIVE_PATH=/mnt/beegfs/xiangda/SGSim_debug_lp64_ubuntu22.tar.gz
SGSIM_ROOT=/mnt/beegfs/xiangda/SGSim_debug_lp64_ubuntu22

mkdir -p "$SGSIM_ROOT"
tar -xzf "$ARCHIVE_PATH" -C "$SGSIM_ROOT"
```

`ARCHIVE_PATH` 和 `SGSIM_ROOT` 可分别调整；建议解压目录使用压缩包去掉 `.tar.gz` 后的名称。

### 4.2 服务器环境脚本

服务器解压目录示例为：

```bash
SGSIM_ROOT=/mnt/beegfs/xiangda/SGSim_debug_lp64_ubuntu22
```

`SGSIM_ROOT` 可以根据实际部署位置和目录名称自行修改。它不必与压缩包文件名相同，但建议使用压缩包去掉 `.tar.gz` 后的名称，便于管理。

压缩包根目录包含 `set_envs.sh`，内容如下：

```bash
#!/bin/bash

SGSIM_ROOT=/mnt/beegfs/xiangda/SGSim_debug_lp64_ubuntu22 # 修改为实际解压目录
SGSIM_BUILD=$SGSIM_ROOT/build/debug
SGSIM_ARTIFACT=$SGSIM_ROOT/Artifact/Ubuntu22-gcc11.4
THIRD_PARTY_DIR=$SGSIM_ROOT/ThirdParty/Ubuntu22-gcc11.4
PETSC_DIR=$SGSIM_ROOT/intel32-petsc64-opt-zmo
ONEAPI_ROOT=/opt/intel/oneapi

source "$ONEAPI_ROOT/compiler/2025.0/env/vars.sh"
source "$ONEAPI_ROOT/mpi/2021.14/env/vars.sh"
source "$ONEAPI_ROOT/mkl/2025.0/env/vars.sh"

export PETSC_DIR
export LD_LIBRARY_PATH=$SGSIM_BUILD/lib:$PETSC_DIR/lib:$THIRD_PARTY_DIR/PHDF5-1.14.5/lib:$LD_LIBRARY_PATH:$SGSIM_ARTIFACT/lib
export PATH=$SGSIM_BUILD/bin:$PATH
```

该脚本优先运行 `build/debug`，并加载压缩包内的LP64 PETSc和MUMPS，不引用221节点现有的ILP64 PETSc。若所有计算节点没有 `/opt/intel/oneapi`，可将 `ONEAPI_ROOT` 改为共享目录，但必须包含2025.0编译器、2021.14 MPI和2025.0 MKL；不要加载指向2025.1或2021.15的 `latest`。

路径按从左到右的顺序查找。`PATH` 中的 `$SGSIM_BUILD/bin` 位于前面，因此执行 `SGFEM` 时优先使用 `build/debug/bin/SGFEM`；`LD_LIBRARY_PATH` 中的 `$SGSIM_BUILD/lib` 位于 `$SGSIM_ARTIFACT/lib` 前面，因此同名动态库优先使用Debug版本，Artifact中的文件仅作为补充。为避免程序与动态库混用，服务器验证统一使用 `$SGSIM_BUILD/bin/SGFEM`，不要直接运行Artifact中的同名程序。

### 4.3 服务器执行脚本

压缩包根目录包含 `run_multi.sh`，内容如下：

```bash
#!/bin/bash
#SBATCH --job-name=sgsim_verify
#SBATCH --partition=debug
#SBATCH -N 2
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=32
#SBATCH --output=joblog/job_%j.out
#SBATCH --error=joblog/job_%j.err
#SBATCH --time=00:20:00

source "$SLURM_SUBMIT_DIR/set_envs.sh"

export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
export MKL_NUM_THREADS=$SLURM_CPUS_PER_TASK
export MKL_DYNAMIC=FALSE
export PETSC_OPTIONS="-ksp_type cg -pc_type jacobi -ksp_rtol 1e-8 -ksp_max_it 10000 -ksp_monitor_short -ksp_converged_reason -ksp_error_if_not_converged"

mkdir -p "$2"
mpirun -n "$SLURM_NTASKS" SGFEM \
  -i "$1" \
  -w "$2"
```

先使用2个节点验证。验证成功后，再根据正式算例调整节点数、每节点MPI进程数和线程数。

### 4.4 提交验证

```bash
SGSIM_ROOT=/mnt/beegfs/xiangda/SGSim_debug_lp64_ubuntu22
CASE_PATH=/mnt/beegfs/xiangda/testcases/target_case.bdf # 修改为实际算例文件
OUTPUT_DIR=$SGSIM_ROOT/output/target_case               # 修改target_case为实际算例名称

cd "$SGSIM_ROOT"
chmod +x set_envs.sh run_multi.sh
mkdir -p joblog "$OUTPUT_DIR"
source ./set_envs.sh
mpiexec --version | head -1
sbatch run_multi.sh "$CASE_PATH" "$OUTPUT_DIR"
```

将 `target_case.bdf` 和输出目录名替换为实际算例名称。
