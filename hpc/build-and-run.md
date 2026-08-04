# SGSim 的 WSL 构建与运行

本文是 SGSim 在本机的**唯一操作手册**。后续配置、编译、运行和调试统一使用 **WSL2 + Ubuntu 22.04**（2026-08-04 迁移，24.04 并存保留），不再以 Windows 构建流程作为日常入口。迁移原因与根因见 [environment.md](environment.md) 与 [log.md](log.md)。

环境安装、内网接入及已验证版本见 [environment.md](environment.md)；第一次参与 C++ 开发时先读 [development-workflow.md](development-workflow.md)。内部仓库地址、凭据与未获公开授权的材料仍只保存在受控系统中。

## 一、先判断当前属于哪一类操作

| 类别 | 什么时候做 | 主要动作 |
| --- | --- | --- |
| 一次性配置 | 新机器、新 WSL 发行版或重建开发环境 | 安装固定工具链，准备环境脚本和本机 preset |
| 每个终端会话 | 每次新开 Ubuntu shell | 进入工程，加载 `sgsim-env.sh`，确认 Intel MPI wrapper |
| 按需同步代码 | 开始新任务或需要远程最新修改 | 依次检查主仓、Artifact、ThirdParty，只快进有更新的仓库 |
| CMake 配置 | 首次构建，或编译器、MPI、依赖、preset、CMake cache 发生变化 | `cmake --preset linux_gcc_debug_intelmpi` |
| 每次代码修改后 | 保存源码后 | 增量编译受影响 target，再运行相关测试 |
| 源码运行验证 | 需要验证刚编译的 `SGFEM` | 检查 `ldd` 后运行 `build/intelmpi-debug/bin/SGFEM` |
| Artifact 对照试算 | 只验证研究院预编译程序和 CPardiso 环境 | 使用隔离的 `LD_LIBRARY_PATH`，不得混入源码构建库 |

配置成功不等于编译成功；编译成功不等于程序能加载；程序启动不等于算例完成。每一层都必须使用本节后续给出的独立验收条件。

## 二、一次性配置

本机已经完成以下配置，正常日常编译无需重复安装：

| 项目 | 固定值或位置 |
| --- | --- |
| WSL 发行版 | **Ubuntu 22.04**（24.04 并存保留） |
| C/C++ 编译器 | GCC/G++ 11 |
| Intel MPI | 2021.14，Build `20241121`（**拷贝制**，位于 `/opt/intel/oneapi/mpi/2021.14`） |
| Artifact / ThirdParty ABI 基线 | `Ubuntu22-gcc11.4` |
| MKL 历史兼容回退 | `~/mkl-extra` 仅存于 24.04；**22.04 侧 env 脚本已移除该路径** |
| 项目环境脚本 | `~/sgsim-env.sh` |
| 本机 CMake preset | `linux_gcc_debug_intelmpi`，保存在被 Git 忽略的 `CMakeUserPresets.json` |
| 构建目录 | `build/intelmpi-debug` |

**22.04 是迁移后宿主**：系统 Boost 1.74、GCC 11 与 Artifact 的 `Ubuntu22-gcc11.4` 基线天然一致，源码端到端验证通过（24.04 上因 Boost ABI 冲突必崩，详见 [environment.md](environment.md) 与 [log.md](log.md)）。Intel MPI 由 tar 拷贝自 24.04，无 APT 包管理，也不需 hold。

一次性配置需要重做时，按 [environment.md](environment.md) 的实际版本与验收记录执行，不从本文临时猜版本。不要把项目的 `LD_LIBRARY_PATH` 写入 `.bashrc`，以免污染其他程序。

## 三、每个新终端都要做

以下操作只影响当前 Ubuntu shell；关闭终端后需要重新执行。

### 3.1 进入工程

```bash
cd /home/brighthe/workspace/SGSim
```

源码必须长期放在 WSL 的 Linux 文件系统中，不在 `/mnt/c` 下编译。

### 3.2 加载源码开发环境

```bash
source "$HOME/sgsim-env.sh"
```

当前脚本为**源码开发环境**：它把 `build/intelmpi-debug/lib` 放在 Artifact 库之前，便于新编译的模块参与测试和运行。这个顺序不适用于 Artifact 对照试算，详见第九节。

### 3.3 确认 Intel MPI

```bash
command -v mpicxx
```

验收：输出必须为 `/opt/intel/oneapi/mpi/2021.14/bin/mpicxx`。若指向 `/usr/bin` 或 OpenMPI，停止配置和编译，先修复当前 shell 的环境。

## 四、编译前按需同步三个仓库

SGSim 的 WSL 开发目录包含三个独立 Git 仓库。每次准备使用远程最新代码时都检查三者，但只有远程 `dev` 领先本地时才合并。

| 顺序 | 仓库 | 目录 | 作用 |
| --- | --- | --- | --- |
| 1 | SGSim 主仓 | `.` | 可见源码、CMake 文件和测试 |
| 2 | Artifact | `Artifact/Ubuntu22-gcc11.4` | 预编译程序、头文件和动态库 |
| 3 | ThirdParty | `ThirdParty/Ubuntu22-gcc11.4` | 第三方依赖 |

### 4.1 确认工作树干净

```bash
git status --short --branch
git -C Artifact/Ubuntu22-gcc11.4 status --short --branch
git -C ThirdParty/Ubuntu22-gcc11.4 status --short --branch
```

验收：三者均在 `dev` 并跟踪 `origin/dev`，除分支状态行外没有文件记录。发现本地改动或提交时先停止；不要覆盖、清理或顺手合并这些内容。

### 4.2 获取并审阅远程变化

主仓执行：

```bash
git fetch origin
git log --oneline --decorate HEAD..origin/dev
git diff --stat HEAD..origin/dev
```

Artifact 和 ThirdParty 分别执行同一组检查：

```bash
git -C Artifact/Ubuntu22-gcc11.4 fetch origin
git -C Artifact/Ubuntu22-gcc11.4 log --oneline --decorate HEAD..origin/dev
git -C Artifact/Ubuntu22-gcc11.4 diff --stat HEAD..origin/dev
```

```bash
git -C ThirdParty/Ubuntu22-gcc11.4 fetch origin
git -C ThirdParty/Ubuntu22-gcc11.4 log --oneline --decorate HEAD..origin/dev
git -C ThirdParty/Ubuntu22-gcc11.4 diff --stat HEAD..origin/dev
```

`HEAD..origin/dev` 没有输出表示该仓库已经最新，不需要合并。主仓 `fetch` 可能提示无法访问未开放源码的封闭子模块；只要远程引用更新成功且没有 `fatal`，该提示不表示主仓获取失败。不要执行递归 submodule 更新或自行修复这些目录。

### 4.3 只快进有更新的仓库

仅对上一小节确认远程领先的仓库执行相应命令：

```bash
git merge --ff-only origin/dev
git -C Artifact/Ubuntu22-gcc11.4 merge --ff-only origin/dev
git -C ThirdParty/Ubuntu22-gcc11.4 merge --ff-only origin/dev
```

验收：实际执行的合并出现 `Fast-forward`，没有冲突。随后重新执行 4.1 的三条状态检查，确认三者均与 `origin/dev` 对齐且工作树干净。若 `--ff-only` 拒绝合并，停止并检查分叉，不改用强制命令。

任一仓库发生更新后，都按第五节重新配置、按第六节编译并执行相应动态库和运行门禁；不能只凭 Git 同步成功认定新基线可用。

## 五、什么时候需要重新执行 CMake 配置

只有以下情况需要重新配置：

- 首次创建 `build/intelmpi-debug`；
- `CMakeLists.txt`、preset 或 CMake 选项发生变化；
- 编译器、Intel MPI、Artifact、ThirdParty 或其他依赖版本/路径发生变化；
- 需要排除旧 cache 对依赖探测的影响。

普通 `.cpp` / `.h` 修改不需要每次重新配置，直接进入第六节增量编译。

### 5.1 确认 preset

```bash
cmake --list-presets
```

验收：输出包含 `linux_gcc_debug_intelmpi`。

### 5.2 配置 Debug 构建

```bash
cmake --preset linux_gcc_debug_intelmpi
```

验收：退出码为 `0`，末尾出现 `Configuring done`、`Generating done`，实际目录为 `build/intelmpi-debug`。

可用系统自带的 `grep` 核对 cache：

```bash
grep -nE 'ONEAPI_MPI_VERSION|MPI_(C|CXX)_COMPILER|MPI_.*LIBRARY' build/intelmpi-debug/CMakeCache.txt
```

MPI wrappers 和库必须指向 Intel MPI 2021.14，不得出现 `/openmpi/`、`libmpi.so.40` 或 `opal_wrapper`。单独的 `_ONEAPI_MPI_CXX_LIBRARY-NOTFOUND` 不构成当前阻塞；当前项目使用 `mpicxx` wrapper 和 Intel `libmpi.so`，且配置已经验证成功。

## 六、每次修改后的编译流程

### 6.1 增量编译

```bash
cmake --build build/intelmpi-debug --parallel 16
```

验收：退出码为 `0`，没有 `error:`，并确认输出中确实出现预期 target。没有源码变化时命令很快结束是正常现象。

产物位置：

- 主程序和测试程序：`build/intelmpi-debug/bin`；
- 本次源码构建的动态库：`build/intelmpi-debug/lib`。

### 6.2 确认可执行文件

```bash
find build/intelmpi-debug/bin -maxdepth 1 -type f -executable -printf '%f\n'
```

当前应看到主程序 `SGFEM`，以及 `BeamSecPropCalculatorTest`、`ElementCalculatorTest`、`DataStructureTest`、`FrameworkTest`、`UtilityTest` 五个测试程序。

### 6.3 动态库门禁

```bash
ldd build/intelmpi-debug/bin/SGFEM
```

验收：

- 没有 `not found`；
- MPI 解析到 Intel `libmpi.so.12`，而不是 `libmpi.so.40`；
- 没有 OpenMPI 路径；
- 参与本次源码构建的模块应从 `build/intelmpi-debug/lib` 加载。

测试执行方式和当前 468 项基线见 [development-workflow.md](development-workflow.md) 第六节。只运行与本次修改直接相关的测试；当前工程没有把 GoogleTest 注册给 CTest，`ctest -N` 的 `Total Tests: 0` 不表示测试通过。

## 七、禁止安装

日常开发**禁止执行**：

```text
cmake --install <构建目录>
```

公共 Linux preset 的安装前缀会指向 Artifact，可能覆盖其中的已跟踪预编译文件。本机 preset 虽把前缀隔离到 `out/intelmpi-debug`，当前开发、测试和试算也不需要 install。源码产物始终从 `build/intelmpi-debug` 使用。

## 八、运行源码主程序

只有第六节的动态库门禁通过后，才运行刚编译的程序：

```bash
source "$HOME/sgsim-env.sh"
cmake -E chdir build/intelmpi-debug/bin cmake -E env I_MPI_PRINT_VERSION=1 ./SGFEM -i /home/brighthe/workspace/SGSim/TestData/SGFem/Pre/StaticLin-LoadNested_fixed3.bdf -w /home/brighthe/workspace/SGSim/workspace -j source-smoke
```

目标状态是依次完成 Intel MPI 初始化、BDF 导入、装配、求解、HDF5 导出，并以退出码 `0` 结束。

**当前基线（2026-08-04 起）**：源码主程序端到端通过。最小算例后向误差 `7.595360e-17`，全流程 ~0.4 s，退出码 `0`。

### 8.1 受控对照验证矩阵

24.04 上「源码库优先 + workspace 非空」必崩（Boost ABI 冲突，见 [environment.md](environment.md) 4.2 与 [log.md](log.md)）。22.04 迁移后该关键格通过。**以下矩阵为源码端到端验收标准，每格建议连跑 ≥5 次确认稳定**：

| # | 库顺序 | workspace | 期望 |
| --- | --- | --- | --- |
| 1 | 源码库优先 | 空 | PASS |
| 2 | **源码库优先** | **非空（预置任意文件）** | **PASS**（24.04 上做不到的关键格） |
| 3 | Artifact 优先（隔离） | 任意 | PASS（CPardiso 对照） |

每格用 `mpiexec -n 1` 运行，源码库优先时 `LD_LIBRARY_PATH="$BLD/lib:$I_MPI_ROOT/lib:$ART/lib"`，Artifact 优先时 `LD_LIBRARY_PATH="$ART/lib:$I_MPI_ROOT/lib"`；全程 `I_MPI_FABRICS=shm`。2026-08-04 实测：三格各 5 次共 15 次全 PASS，后向误差恒 `7.595360e-17`、退出码 `0`。

### 8.2 切换到 PETSc 求解（最小闭环教程）

`SG_USE_PETSC=ON` 只控制编译链接；**顶层求解器由 `Resource/cmake/SGConfig.cmake` 的 `SGSIM_LINEAR_REAL_SOLVER` 决定**，配置时写入各构建目录的 `bin/solver.conf`，默认 `CPardiso<Real_t>`。切换须在**独立构建目录**重新配置 + 编译，避免污染默认基线 `build/intelmpi-debug`。

#### 8.2.1 原理

| 求解器 | `SGSIM_LINEAR_REAL_SOLVER` | 前置 |
| --- | --- | --- |
| 默认（直接法） | `CPardiso<Real_t>` | — |
| PETSc KSP | `TPetscKsp<Real_t>` | `SG_USE_PETSC=ON` |
| Hypre KSP | `THypreKsp<Real_t>` | `SG_USE_PETSC=ON`（Hypre 需同时开 PETSc） |

`TPetscKsp<Real_t>` / `THypreKsp<Real_t>` 定义在闭源 `libAlgebra.so`（Artifact）中，由 `GeneralProducer` 工厂创建。本机 preset `linux_gcc_debug_intelmpi` 的继承链 `linux_default` 自带 `SG_USE_PETSC=ON`，故 PETSc 构建无需额外开关，只需指定独立构建目录。

#### 8.2.2 配置独立 PETSc 构建（2026-08-04 实测通过）

```bash
source ~/sgsim-env.sh
cmake --preset linux_gcc_debug_intelmpi -B build/petsc-minimal
cmake --build build/petsc-minimal --parallel 16
```

> `-B build/petsc-minimal` 覆盖 preset 自带的构建目录，与默认基线隔离。公共 preset 的 `CMAKE_PREFIX_PATH=/data/3rdparty/intel-opt-zmo/` 在本机不存在，靠 preset 的 `CXXFLAGS=-I .../Artifact/.../include` 与链接 flags `-L .../Artifact/.../lib` 兜底（PETSc 头文件与库来自 Artifact），实测配置编译成功。构建目录不提交，可随时删除重新生成。
>
> **配置前必须先 `source ~/sgsim-env.sh`**：干净 shell 直接配置/编译会因缺少 Intel MPI 的 `CPATH`（`/opt/intel/oneapi/mpi/2021.14/include`）而报 `mpi.h: No such file or directory`。env 脚本已把该路径去重写入 `CPATH`（`sgsim-env.sh` 在加载 `vars.sh` 后统一补齐，重复 source 不累积）。2026-08-04 实测：source 后无需手动 `CPATH` 即可重配编译通过。

#### 8.2.3 运行 PETSc 最小闭环

```bash
cmake -E chdir build/petsc-minimal/bin cmake -E env \
  LD_LIBRARY_PATH=/home/brighthe/workspace/SGSim/build/petsc-minimal/lib:/opt/intel/oneapi/mpi/2021.14/lib:/home/brighthe/workspace/SGSim/Artifact/Ubuntu22-gcc11.4/lib \
  I_MPI_FABRICS=shm \
  /opt/intel/oneapi/mpi/2021.14/bin/mpiexec.hydra -n 1 \
  ./SGFEM -i /home/brighthe/workspace/SGSim/TestData/SGFem/Task/Input/celas2.bdf \
  -w /home/brighthe/workspace/SGSim/workspace -j celas2_petsc
```

成功标准：

- 输出 `Selected linear solver for real-valued problems: TPetscKsp<Real_t>`；
- 出现 `Job celas2_petsc Finish`，退出码 `0`，`workspace/celas2_petsc.h5` 存在且非零。

验证 KSP 实际收敛时追加参数：

```bash
cmake -E chdir build/petsc-minimal/bin cmake -E env \
  LD_LIBRARY_PATH=/home/brighthe/workspace/SGSim/build/petsc-minimal/lib:/opt/intel/oneapi/mpi/2021.14/lib:/home/brighthe/workspace/SGSim/Artifact/Ubuntu22-gcc11.4/lib \
  I_MPI_FABRICS=shm \
  /opt/intel/oneapi/mpi/2021.14/bin/mpiexec.hydra -n 1 \
  ./SGFEM -i /home/brighthe/workspace/SGSim/TestData/SGFem/Task/Input/celas2.bdf \
  -w /home/brighthe/workspace/SGSim/workspace -j celas2_petsc_ksp \
  -ksp_type cg -pc_type jacobi -ksp_rtol 1e-9 -ksp_converged_reason
```

期望输出：`Linear solve converged due to CONVERGED_ATOL iterations 1`。2026-08-04 实测：全流程 0.375 s，退出码 `0`。

> 用 `mpiexec.hydra` 完整路径：`/opt/intel/oneapi/mpi/2021.14/bin/mpirun` 是指向 `/etc/alternatives/mpirun` 的符号链接（解析到 OpenMPI），不能用。

#### 8.2.4 切回 CPardiso 默认

```bash
git checkout -- Resource/cmake/SGConfig.cmake   # 恢复 SGSIM_LINEAR_REAL_SOLVER=CPardiso<Real_t>
```

需要让默认构建 `build/intelmpi-debug` 重新生成 `solver.conf` 时，重新配置 + 编译该目录即可。注意 `SGConfig.cmake` 是主仓已跟踪文件，切换测试后**不得 commit**；`git status` 确认工作树干净。

### 8.3 切换到 Hypre 求解

机制与 PETSc（8.2）完全相同：`SGSIM_LINEAR_REAL_SOLVER` 改成 `THypreKsp<Real_t>`，重新配置 + 编译。本机前提已核实：Artifact 带 `libHYPRE-3.1.0.so`，`libAlgebra.so` 内含 `THypreKsp` 实现并依赖该库；默认构建 `ldd` 已解析到 Hypre。`SG_USE_HYPRE` 开关只是惰性 `option()`，没有 CMake 消费它，真正决定求解器的是 `SGConfig.cmake` 那一个字符串；`THypreKsp` 实现封在闭源 `libAlgebra.so` 中，编译时不直接需要 `HYPRE.h` 头文件（Artifact 中也没有）。运行时 `THypreKsp` 经 `libAlgebra` 依赖 PETSc/Hypre 库，preset 继承链已开 `SG_USE_PETSC`，沿用默认构建即可。

#### 8.3.1 切换并编译（与 8.2.2 同构）

```bash
source ~/sgsim-env.sh
sed -i 's/"CPardiso<Real_t>"/"THypreKsp<Real_t>"/' Resource/cmake/SGConfig.cmake
cmake --preset linux_gcc_debug_intelmpi -B build/petsc-minimal
cmake --build build/petsc-minimal --parallel 16
```

配置后 `build/petsc-minimal/bin/solver.conf` 的 `Solver.LinearReal.Value` 应为 `THypreKsp<Real_t>`。

#### 8.3.2 运行 Hypre 最小闭环

```bash
cmake -E chdir build/petsc-minimal/bin cmake -E env \
  LD_LIBRARY_PATH=/home/brighthe/workspace/SGSim/build/petsc-minimal/lib:/opt/intel/oneapi/mpi/2021.14/lib:/home/brighthe/workspace/SGSim/Artifact/Ubuntu22-gcc11.4/lib \
  I_MPI_FABRICS=shm \
  /opt/intel/oneapi/mpi/2021.14/bin/mpiexec.hydra -n 1 \
  ./SGFEM -i /home/brighthe/workspace/SGSim/TestData/SGFem/Task/Input/celas2.bdf \
  -w /home/brighthe/workspace/SGSim/workspace -j celas2_hypre
```

成功标准：

- 输出 `Selected linear solver for real-valued problems: THypreKsp<Real_t>` 与 `Attempting to create linear solver of type: THypreKsp<Real_t>`；
- 出现 `Iterations = 1`（Hypre 迭代收敛输出，格式与 PETSc KSP 不同）；
- 出现 `Job celas2_hypre Finish`，退出码 `0`，`workspace/celas2_hypre.h5` 存在且非零。

2026-08-04 实测：全流程 0.478 s，退出码 `0`，`.h5` 564 KB。

#### 8.3.3 切回 CPardiso 默认

与 8.2.4 相同：`git checkout -- Resource/cmake/SGConfig.cmake`。

## 九、Artifact 的 CPardiso 对照试算

Artifact 试算只证明研究院预编译程序、依赖和本机运行环境可用，**不能证明刚编译的源码正确**。

### 9.1 为什么不能直接复用源码环境

`source ~/sgsim-env.sh` 后，普通 `ldd Artifact/Ubuntu22-gcc11.4/bin/SGFEM` 会优先解析到 `build/intelmpi-debug/lib` 中的部分模块，形成预编译主程序与源码构建库混装。2026-08-02 实测该组合在 MPI 初始化后报 `free(): invalid pointer` 并异常终止。

因此 Artifact 对照必须在单条命令内临时覆盖 `LD_LIBRARY_PATH`；不要修改当前 shell，也不要把该值写入 `.bashrc`。

### 9.2 隔离后的动态库门禁

```bash
cmake -E env LD_LIBRARY_PATH=/home/brighthe/workspace/SGSim/Artifact/Ubuntu22-gcc11.4/lib:/opt/intel/oneapi/mpi/2021.14/lib ldd Artifact/Ubuntu22-gcc11.4/bin/SGFEM
```

验收：所有 SGSim、Boost、PETSc、HYPRE、MKL、VTK 等项目依赖来自 `Artifact/Ubuntu22-gcc11.4/lib`，不得出现 `build/intelmpi-debug/lib`、`~/mkl-extra` 或 `not found`。Artifact `08d3cfa` 已补入 `libmkl_sequential.so.2`，2026-08-02 实测不再需要外置 MKL 回退。

### 9.3 执行最小 CPardiso 算例

```bash
cmake -E chdir Artifact/Ubuntu22-gcc11.4/bin cmake -E env LD_LIBRARY_PATH=/home/brighthe/workspace/SGSim/Artifact/Ubuntu22-gcc11.4/lib:/opt/intel/oneapi/mpi/2021.14/lib I_MPI_FABRICS=shm I_MPI_PRINT_VERSION=1 ./SGFEM -i /home/brighthe/workspace/SGSim/TestData/SGFem/Pre/StaticLin-LoadNested_fixed3.bdf -w /home/brighthe/workspace/SGSim/workspace -j cpardiso-artifact-smoke
```

成功标准：

- Intel MPI 2021.14 Build `20241121` 初始化成功；
- BDF 导入完成并选择 `CPardiso<Real_t>`；
- 完成矩阵分解、求解与 HDF5 导出；
- 出现 `Job cpardiso-artifact-smoke Finish`；
- 退出码为 `0`，对应 `.h5` 和 `_0.log` 文件存在且大小非零。

2026-08-02 本机实测同一路径成功：12 个方程、20 个非零元，16 个 OpenMP 线程，后向误差 `7.595360e-17`，全流程 0.464 s。

## 十、结果口径

| 结果 | 可以得出的结论 | 不能得出的结论 |
| --- | --- | --- |
| CMake 配置成功 | preset、编译器和依赖探测可用 | 源码可编译或程序可运行 |
| 源码编译和 `ldd` 通过 | 构建与动态加载门禁通过 | 源码端到端算例通过 |
| Artifact CPardiso 成功 | 预编译程序和本机 CPardiso 环境可用 | 新编译的源码正确 |
| 源码最小算例成功（8.1 矩阵） | 本次源码产物完成最小功能链路、无 ABI 冲突 | 数值正确、性能达标或规模测试完成 |

正式性能基线仍不在本机 WSL 上产生；本机 CPU 为大小核混合架构，MPI rank 落核差异会破坏扩展性数据的可比性。

## 参考

- 环境事实与一次性配置：[environment.md](environment.md)
- 开发、测试和调试流程：[development-workflow.md](development-workflow.md)
- 工作安排与当前状态：[plan.md](plan.md)
- 进度日志：[log.md](log.md)
