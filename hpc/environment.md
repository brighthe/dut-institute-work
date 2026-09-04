# SGSim 项目环境事实

本文只记录 SGSim 在本机 WSL 中依赖的项目级环境、ABI 约束和已验证边界。日常配置、编译、动态库检查与算例运行统一见 [build-and-run.md](build-and-run.md)；开发方法见 [development-workflow.md](development-workflow.md)。

WSL 安装、DNS、VPN、Intel MPI/MKL 的机器级复现步骤由 `workstation:wsl/README.md` 维护；账号和仓库权限的开通过程见 [log.md](log.md)。内部 IP、VPN 网段、SSH 端口、内部仓库路径和凭据不进入本 Public 仓库。

## 一、当前状态

| 项目 | 当前状态 |
| --- | --- |
| 开发平台 | WSL2 + **Ubuntu 22.04**（2026-08-04 迁移，源码位于 Linux 文件系统；24.04 并存保留） |
| C/C++ 编译器 | GCC/G++ 11 |
| CMake | 3.22.1（22.04 默认） |
| Intel MPI | 2021.14，Build `20241121`（**拷贝制**，非 APT 包，无需 hold） |
| Artifact / ThirdParty ABI | `Ubuntu22-gcc11.4` |
| 当前已验证三仓基线 | 主仓 `98038b6`、Artifact `08d3cfa`、ThirdParty `b0b6f19` |
| 本机 preset | `linux_gcc_debug_intelmpi` |
| 源码配置与编译 | 已通过，产出 `SGFEM` 和 5 个测试程序 |
| 单元测试 | 468 项运行、464 项通过、4 项 skipped、0 failed |
| 源码主程序 | **源码端到端通过**（验证矩阵 5 轮 × 3 格全 PASS，后向误差 `7.595360e-17`） |
| Artifact CPardiso 对照 | 隔离动态库后通过 |
| 正式性能基线 | 不在本机工作站（含 WSL）产生 |

当前可以准确表述为：**WSL 源码配置与编译通过、单元测试通过、源码端到端运行通过、Artifact CPardiso 对照通过。**任务状态见 [plan.md](plan.md)，时间线见 [log.md](log.md)。

> 2026-08-04 迁移背景：24.04 上源码主程序在 workspace 非空时必崩，根因是 **Boost ABI 冲突**（系统 Boost 1.83 vs Artifact 1.74，非 PETSc 误判），详见 [log.md](log.md)。22.04 系统 Boost 1.74 与 Artifact 基线一致，消除冲突。

## 二、一次性项目配置

### 2.1 目录布局

| 路径 | 用途 | 规则 |
| --- | --- | --- |
| `/home/brighthe/workspace/SGSim` | 主源码根目录 | 日常开发入口 |
| `build/intelmpi-debug` | Debug 构建目录 | 可重新生成，不提交 |
| `build/intelmpi-debug/bin` | 主程序和测试程序 | 源码验证入口 |
| `build/intelmpi-debug/lib` | 本次源码构建的动态库 | 源码开发环境优先加载 |
| `build/petsc-minimal` | 独立 PETSc 验证构建目录 | 可重新生成，不提交；见 2.3「运行时求解器选择」 |
| `Artifact/Ubuntu22-gcc11.4` | 研究院预编译产物 | 只读，不安装覆盖 |
| `ThirdParty/Ubuntu22-gcc11.4` | 第三方依赖 | 只读 |
| `workspace` | 本地日志和结果 | 不提交 |
| `~/mkl-extra` | 历史 MKL 兼容回退 | 仅存于 24.04；22.04 侧 env 脚本已移除该路径（版本混装隐患） |

源码长期放在 WSL 的 Linux 文件系统中，不在 `/mnt/c` 下编译。`Artifact`、`ThirdParty`、build 和 workspace 均不进入业务提交。

### 2.2 Ubuntu 22.04 与 Artifact ABI（2026-08-04 迁移）

`Ubuntu22-gcc11.4` 表示 Artifact 的构建和 ABI 基线。**2026-08-04 决策：宿主机使用 Ubuntu 22.04**，而非此前的 24.04——24.04 上源码主程序因 Boost ABI 冲突必崩（系统 Boost 1.83 vs Artifact 1.74，详见 [log.md](log.md)）。22.04 系统 Boost 1.74、默认 GCC 11，与 Artifact 基线天然一致，源码端到端验证通过。24.04 发行版保留，随时可回退。

Artifact 自带 `libmpi.so.12.0.0` 与 Intel 2021.14 的 SHA-256 和 Build ID 完全一致，对应 Build `20241121`。较新的 `2021.14.2-7` 是 Build `20250213`，不能仅凭主版本相同替换。**22.04 侧的 Intel MPI 由 tar 拷贝自 24.04（`/opt/intel/oneapi/mpi`，415M），版本天然钉死在 2021.14/Build `20241121`，无 APT hold 需求**（拷贝制，`mpiexec --version` 实测确认）。

系统 OpenMPI 保留供其他软件使用，但 SGSim 的 wrapper、CMake cache 和构建产物不得解析到 OpenMPI、`libmpi.so.40` 或 `opal_wrapper`。

### 2.3 本机 CMake preset

被 Git 忽略的 `CMakeUserPresets.json` 定义 `linux_gcc_debug_intelmpi`，继承公共 `linux_gcc_debug`，但隔离本机构建：

| 项目 | 本机值 |
| --- | --- |
| 构建目录 | `build/intelmpi-debug` |
| 安装前缀 | `out/intelmpi-debug` |
| C/C++ 编译器 | GCC/G++ 11 |
| MPI wrappers | `/opt/intel/oneapi/mpi/2021.14/bin/mpicc`、`mpicxx` |
| MPI runtime | `/opt/intel/oneapi/mpi/2021.14/lib/libmpi.so.12` |

源码树中的多数子模块目录为空，因此构建仍需只读引用 Artifact 的头文件和库。本机 preset 显式提供这些后备路径，不修改公共 preset，也不需要执行 `cmake --install`。

> **2026-08-21 fork 后新增的构建陷阱**：`Resource/cmake/Tools.cmake` 的 `add_subdir` 宏以「子目录下是否存在 `CMakeLists.txt`」决定走源码还是预编译，这是一个隐式开关。`Algebra`、`DBManager`、`Partition` 一旦 init 出源码，构建即从「引用 Artifact 预编译库」切换为「源码编译」，而 Artifact 只发布运行期 `.so`，不含 PETSc/SLEPc/HYPRE/ARPACK 的头文件与 CMake 配置，本机不具备源码编译这三个模块的条件。**该判断基于依赖清点，重新配置尚未实测。**若要沿用原有预编译构建线，需先 `git submodule deinit` 这三个目录。

**运行时求解器选择**：`SG_USE_PETSC=ON` 只控制编译链接；顶层求解器由 `Resource/cmake/SGConfig.cmake` 的 `SGSIM_LINEAR_REAL_SOLVER` 决定（默认 `CPardiso<Real_t>`，可切 `TPetscKsp<Real_t>` / `THypreKsp<Real_t>`），配置时写入各构建目录 `bin/solver.conf`。本机 preset 继承链自带 `SG_USE_PETSC=ON`，独立 PETSc 构建用 `-B build/petsc-minimal` 指定目录即可，避免污染默认基线 `build/intelmpi-debug`。切换与恢复步骤见 [build-and-run.md](build-and-run.md) 8.2。

### 2.4 项目环境脚本

`~/sgsim-env.sh` 是**源码开发环境**，每个新 WSL shell 按 [build-and-run.md](build-and-run.md) 加载。它负责固定：

- Intel MPI 2021.14 的命令与运行库；
- Intel MPI 的 include 路径（去重写入 `CPATH`，干净 shell 配置/编译缺它会报 `mpi.h` 找不到）；
- `I_MPI_FABRICS=shm`；
- `build/intelmpi-debug/lib`、Artifact 和外置 MKL 的库路径；
- 源码运行和测试需要的其他项目变量。

脚本让源码构建库优先于 Artifact，以便本次修改真正参与运行。这些变量不写入 `.bashrc`，避免污染其他项目。Artifact 对照不能直接继承该库顺序，必须使用操作手册中的临时隔离环境。

### 2.5 MKL 历史回退与 WSL 通信约束

早期 Ubuntu Artifact 缺 `libmkl_sequential.so.2`，本机曾从同版本 oneMKL 2025.0.0 的官方包取得该文件并放在 `~/mkl-extra`。Artifact `08d3cfa` 已将该库补入自身 `lib/`；2026-08-02 在明确排除 `~/mkl-extra` 的隔离环境中，`ldd` 和 CPardiso 对照均通过。**勘误**：实测确认源码环境**确实解析到**外置副本（`ldd` 显示解析到 `~/mkl-extra/libmkl_sequential.so.2`），非「仍可能」。该外置目录是版本混装隐患（oneMKL 2025.0.0 vs Artifact 内其余 MKL）。**22.04 侧 `sgsim-env.sh` 已移除该路径**；`~/mkl-extra` 仅存于 24.04 侧保留。

WSL 中 Intel MPI 默认尝试 OFI，而本机没有可用的 libfabric provider；未设置 `I_MPI_FABRICS=shm` 时会在 MPI 初始化阶段失败。该变量属于本机单节点 WSL 运行条件，不代表目标 Linux 集群的通信配置。

## 三、接入与历史环境摘要

> 接入的操作细节与排障（VPN 连接、密码初始化、邮件问题、代理干扰等）见本地未入库的 `dev-access.md`；本文件只保留状态结论。

### 3.1 内网与代理

VPN、GitLab 账号和任务仓库权限均已开通。内网 GitLab 在浏览器、Windows 命令行和 WSL 中均验证可达。

本机 Clash Verge 曾将内网域名代理到外部节点产生 502，需同时覆盖系统代理绕过列表（浏览器）与 `NO_PROXY`（命令行）两层。机器级的代理机制、TUN 规范与排错方法见 `workstation:network/README.md`，本机具体地址与研究院特有配置见本地未入库的 `dev-access.md`。

### 3.2 SSH 与源码获取

内网 GitLab 使用独立 SSH 密钥，公钥注册和 `ssh -T` 已验证；WSL 与 Windows 侧共享同一身份，一处吊销或到期会同时失效。三个任务仓库已经克隆；同步顺序和门禁见 [build-and-run.md](build-and-run.md) 第四节，已验证源码基线见 [plan.md](plan.md)。

**仓库访问范围**（2026-08-21 研究院 fork 到 `SuanHai` 分组）：主仓与 `Artifact` 转为可写，`Algebra`、`DBManager`、`Partition` 三个子模块新开放源码，`ThirdParty` 失去访问；原命名空间已全面撤权，旧路径均不可达，因此迁移是强制的，不是可选优化。**完整访问矩阵与对应的源码可见性结论见 [gitlab-migration.md](gitlab-migration.md)**，本文不重复。

对本机环境的三点直接影响：

- 其余 10 个子模块的空目录仍是正常交付状态，对应接口和预编译库由 Artifact 提供，无需递归修复或申请源码权限；
- `ThirdParty` 在 SuanHai 分组下没有对应仓库，本机副本停留在撤权前的 `b0b6f19`（2026-05-19），无法再取更新；
- 三个新开放模块 init 出源码后会改变构建模式，见 2.3。

经 VPN 克隆速度较慢属于当前接入链路特征。拉内网仓库必须连接 VPN；大批量公网下载宜断开 VPN，避免全隧道路由显著降低速度。

### 3.3 Windows 构建线

Windows MinGW-w64 GCC 13.2.0、CMake 3.28.3 和对应 Artifact/ThirdParty 曾完成 ABI、编译和 CPardiso 算例验证。该路径作为历史事实保留在 [log.md](log.md)，不再是当前操作入口；后续项目配置、编译、运行和调试统一使用 WSL。

## 四、已验证边界

### 4.1 源码构建与测试

`linux_gcc_debug_intelmpi` 已从空目录完成配置和 `--parallel 16` 编译，生成主程序与 5 个 GoogleTest 程序。所有构建产物的动态加载检查均无 `not found` 或 OpenMPI 混用。

5 个测试程序固定从 `build/intelmpi-debug/bin` 运行，稳定基线为 468 项运行、464 项通过、4 项 skipped、0 failed。CTest 没有注册这些测试，`Total Tests: 0` 不能作为通过证据。

### 4.2 源码主程序端到端（2026-08-04 已闭合）

**24.04 上的 SIGSEGV 根因是 Boost ABI 冲突，非 PETSc**（详见 [log.md](log.md) 2026-08-04 条目）：源码库优先 + workspace 非空时，Boost.Log 1.83 `scan_for_files` 的 `directory_iterator` 符号解析到 Artifact 的 Boost.Filesystem 1.74，两版本迭代器内存布局不兼容导致崩溃。PETSc 只是安装了 SIGSEGV 处理器、抢先打印错误，被误判为 PETSc 问题。

**22.04 迁移后已闭合**：系统 Boost 1.74 与 Artifact 一致，单一版本无冲突。源码 `SGFEM` 在验证矩阵三格（源码库优先+空/非空 workspace、Artifact 优先隔离）各 5 次共 15 次运行全 PASS，后向误差恒 `7.595360e-17`、退出码 `0`，HDF5 导出正常。**可写「源码端到端通过」**。

### 4.3 Artifact 动态库隔离

源码环境会把 `build/intelmpi-debug/lib` 放在 Artifact 之前。直接启动 Artifact 的 `SGFEM` 会混入源码构建的 `Framework`、`DataStructure`、`Utility` 等模块，实测在 MPI 初始化后报 `free(): invalid pointer`。

临时隔离 `LD_LIBRARY_PATH` 后，项目依赖统一来自 `Artifact/Ubuntu22-gcc11.4/lib`。Artifact `08d3cfa` 在不包含 `build/intelmpi-debug/lib` 和 `~/mkl-extra` 的环境中完成同一最小算例的 BDF 导入、`CPardiso<Real_t>` 求解和 HDF5 导出，退出码为 `0`，全流程 0.461 s，后向误差 `7.595360e-17`。固定命令和验收条件只在 [build-and-run.md](build-and-run.md) 第九节维护。

该成功只证明 Artifact 与本机运行环境可用，不能证明源码产物正确。

### 4.4 性能口径

本机 CPU 为大小核混合架构，MPI rank 可能落在不同类型核心上，扩展性数据不可比。正式性能基线必须在固定源码、依赖和硬件条件的 Linux 目标机产生；WSL 只用于开发、单元测试和功能摸底。

## 五、何时回查本文

出现以下变化时，先核对本文件，再按 [build-and-run.md](build-and-run.md) 重新配置和验证：

- Ubuntu、GCC/G++、Intel MPI 或 CMake 版本变化；
- Artifact、ThirdParty 或闭源模块版本变化；
- `CMakeUserPresets.json`、`~/sgsim-env.sh` 或外置 MKL 路径变化；
- CMake cache 再次发现 OpenMPI；
- Artifact 对照加载到 `build/intelmpi-debug/lib`；
- 系统 Boost 版本变化（当前 1.74，若升级需重查 ABI 一致性）；
- 源码主程序的运行行为发生变化。

## 参考

- 唯一操作手册：[build-and-run.md](build-and-run.md)
- 开发方法：[development-workflow.md](development-workflow.md)
- 账号与权限过程：[log.md](log.md)
- 任务状态：[plan.md](plan.md)
- 历史进展：[log.md](log.md)
- 机器级 WSL 配置：`workstation:wsl/README.md`
