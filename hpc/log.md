# 进度日志 · HPC

> Append-only：新条目加在最上面，格式 `## [YYYY-MM-DD] <简述>`；只增不改历史条目。

## [2026-08-21] 「Antigravity 必须开 Clash TUN」是误判；根因是代理环境变量从未持久化

- **结论：Antigravity 不需要开虚拟网卡模式（TUN）**。它的语言服务器 `language_server.exe` / `language_server_windows_x64.exe` 是 Go 二进制（二进制内命中 `net/http` 与 `HTTP_PROXY`/`https_proxy`/`no_proxy`），走 `http.ProxyFromEnvironment`——**只认代理环境变量，不认 Windows 系统代理（WinINET）**。其扩展未贡献任何 `*.proxy` 设置项，`settings.json` 里的 `http.proxy` 只作用于 Electron 外壳。
- **根因：用户级持久环境变量里只有 `NO_PROXY`，没有 `HTTP_PROXY`/`HTTPS_PROXY`**。机器级环境变量与两个 PowerShell profile 均无 proxy 相关项。此前在终端里之所以「看起来有」，是 Claude Code 自行注入给子 shell 的——其进程由 Explorer 拉起，本身只能继承到用户级环境，注入的变量出了该进程即失效。而 `NO_PROXY` 是当初修内网 GitLab 502 时加的：那次的需求是「把内网排除掉」而非「指定代理在哪」，所以只加了排除项。
- **为什么恰好卡住 Antigravity**：系统代理覆盖浏览器与 Electron 外壳，环境变量覆盖终端内启动的进程；「从开始菜单启动的 GUI 应用，且核心子进程是只认环境变量的 Go 二进制」这一类两边都够不着。Antigravity 是本机第一个落进该缝隙的程序，因此该缺口此前一直不显形。
- **处置与验证**：在用户级持久化 `HTTP_PROXY` / `HTTPS_PROXY` 指向本机代理端口 `http://127.0.0.1:7897`（`NO_PROXY` 已含内网域名，无需改动），然后完全退出并重启 Antigravity。实测在 **TUN 关闭、VPN 断开、默认路由只剩物理网卡**的干净状态下，语言服务器的 10 条 ESTABLISHED 连接全部指向 `127.0.0.1:7897`，0 条直连；重启前则是直连 Google 地址、无一条走代理。环境变量改动必须完全重启目标应用才生效。
- **更正 TUN 抢占默认路由的机制描述**：本机 Clash Verge 的 TUN 网卡（`Meta`）加的是 **`0.0.0.0/0`、RouteMetric 0**，不是常见的 `0.0.0.0/1 + 128.0.0.0/1` 分割路由。所以它压过 VPN 默认路由靠的是 **metric 更低，而不是前缀更长**——此前按「前缀更长」给出的解释不准确，以本条为准。修复方向不受影响：给内网网段补**明细路由**仍然稳赢，因为明细前缀长于 `0.0.0.0/0`，与 metric 无关。
- **TUN 与 VPN 冲突的其余实测事实**：VPN 为全隧道且**不下发任何内网明细路由**，内网仅靠默认路由可达，故 TUN 一开内网即全断；而 VPN 服务器的 `/32` 主机路由由 Windows RAS 自动维护、始终走物理网卡，隧道本身不会断——症状表现为「VPN 显示已连接但内网打不开」，容易误判成掉线。另需注意 TUN 模式下 `NO_PROXY` 与系统代理绕过列表**完全失效**（TUN 工作在路由层，应用根本不知道有代理），[environment.md](environment.md) 3.1 记录的两层绕过配置仅对系统代理模式有效。
- **后续**：既然 TUN 可以常关，VPN 与 Antigravity 不再冲突，按需切换即可满足日常使用。若今后需要内网与外网代理同时可用，仍需把 VPN 改为分流并补内网明细路由；该方案连同本机具体网段、服务器地址等参数一并见本地未入库的 `dev-access.md` 5.1，**不进入本 Public 仓库**。

## [2026-08-04] Hypre KSP 最小闭环走通；env 脚本补 Intel MPI include 路径

- **Hypre 最小闭环在本机源码构建走通**：切换机制与 PETSc（8.2）完全相同——`Resource/cmake/SGConfig.cmake` 的 `SGSIM_LINEAR_REAL_SOLVER` 改为 `THypreKsp<Real_t>`，`source ~/sgsim-env.sh` 后重配 `build/petsc-minimal` + 编译，运行 `celas2.bdf`。输出：`Selected linear solver for real-valued problems: THypreKsp<Real_t>`、`Attempting to create linear solver of type: THypreKsp<Real_t>`、`Iterations = 1`、`Job celas2_hypre Finish (0.478 s)`、退出码 `0`、`.h5` 564 KB。验证后已恢复 `SGConfig.cmake` 默认。完整步骤见 [build-and-run.md](build-and-run.md) 8.3。
- **要点**：`THypreKsp` 实现封在闭源 `libAlgebra.so`（依赖 Artifact 的 `libHYPRE-3.1.0.so`），编译时不直接需要 `HYPRE.h` 头文件（Artifact 中也没有）；`SG_USE_HYPRE` 只是惰性 `option()`，全仓无 CMake 消费它，真正决定求解器的是 `SGConfig.cmake` 那一个字符串。
- **env 脚本加固（CPATH）**：`~/sgsim-env.sh` 在加载 `vars.sh` 后统一把 Intel MPI 的 include 路径去重写入 `CPATH`（重复 source 不累积），单次/二次 source 实测均为一份。**教训**：干净 shell 直接 `cmake` 配置/编译会因缺 `CPATH` 报 `mpi.h: No such file or directory`；配置前必须先 `source ~/sgsim-env.sh`。
- **勘误（以本条为准）**：此前临时判断「vars.sh 在非交互 shell 下漏设 CPATH」不准确——`sgsim-env.sh` 用 `. vars.sh`（source）加载，vars.sh 会正常设置 CPATH；初判受嵌套 `bash -lc` 中变量被外层展开为空的假象误导。env 脚本的 CPATH 补齐行仍保留，作去重加固。

## [2026-08-04] PETSc 最小闭环在源码构建走通；SGConfig 恢复默认收尾

- **PETSc KSP 求解在本机源码构建走通**：独立构建目录 `build/petsc-minimal` 由本机 preset 继承链自带 `SG_USE_PETSC=ON` 配置编译。运行最小算例 `TestData/SGFem/Task/Input/celas2.bdf`（1 单元、1 约束、1 载荷），关键输出：`Selected linear solver for real-valued problems: TPetscKsp<Real_t>`；带 KSP 验证参数（`-ksp_type cg -pc_type jacobi -ksp_rtol 1e-9 -ksp_converged_reason`）时 `Linear solve converged due to CONVERGED_ATOL iterations 1`；导出 HDF5；`Job celas2_petsc Finish (0.397 s)`（带参数 0.375 s）；退出码 0。这对应宋维豪文档的「PETSc 最小闭环」，且用**本机源码构建**复现（此前其文档口径为自编 exe + Artifact 库的变体 A）。
- **运行时求解器切换机制确认**：`SG_USE_PETSC=ON` 只控制编译链接；顶层求解器由 `Resource/cmake/SGConfig.cmake` 的 `SGSIM_LINEAR_REAL_SOLVER` 决定，配置时写入各构建目录 `bin/solver.conf`，默认 `CPardiso<Real_t>`。切 PETSc：改为 `TPetscKsp<Real_t>`；切 Hypre：`THypreKsp<Real_t>`（Hypre 需同时 `SG_USE_PETSC=ON`）。改后必须重新配置 + 编译；该文件为主仓已跟踪文件，不得 commit，恢复用 `git checkout -- Resource/cmake/SGConfig.cmake`。完整步骤见 [build-and-run.md](build-and-run.md) 8.2。
- **收尾**：验证完成后已恢复 `SGConfig.cmake` 为 `CPardiso<Real_t>`，三仓工作树干净；默认基线 `build/intelmpi-debug/bin/solver.conf` 仍为 `CPardiso<Real_t>`，不受影响；`build/petsc-minimal` 保留为已验证的 PETSc 构建目录（不提交、可重新生成）。

## [2026-08-04] 源码主程序 SIGSEGV 根因定为 Boost ABI 冲突；并装 Ubuntu 22.04 迁移验证全绿

- **根因不是 PETSc，是 Boost ABI 冲突**。此前多次记录的「源码主程序在 PETSc 初始化阶段 SIGSEGV（退出码 15）」系误判：PETSc 只是安装了 SIGSEGV 信号处理器、抢先打印 `[0]PETSC ERROR`，崩溃与 PETSc 无关。真实因果链（gdb backtrace 钉死）：`SGLogger::Init` 初始化 → Boost.Log 1.83 `scan_for_files` 扫描 workspace → `directory_iterator` 符号解析到 Artifact 的 Boost.Filesystem 1.74 → 两版本迭代器内存布局不兼容 → `__libc_free` 把字符串当指针 → SIGSEGV。
- **触发条件（20/20 确定性复现，无随机性）**：崩溃 = 源码构建库优先加载 **且** workspace（`-w`）目录非空，两条件缺一不崩。历来必崩原因：`~/sgsim-env.sh` 源码库优先，`-w` 指向堆满历史产物的 workspace。
- **为何 24.04 必然踩到**：系统 Boost 1.83 vs Artifact 基线（22.04）1.74；`find_package(Boost)` 无版本约束，CMake 静默选中系统 Boost；Artifact 只有 1.74 的 `.so`、无头文件，源码无法用 Artifact Boost 编译。
- **决策并装 Ubuntu 22.04**：兼容靠构造而非补丁。22.04 系统 Boost=1.74、默认 GCC=11，与 Artifact 基线天然对齐；24.04 发行版保留不动，随时可回退。
- **迁移完成，源码端到端首次稳定通过**：22.04 上完成工具链（gcc/g++ 11.4、boost 1.74.0.3ubuntu7、cmake 3.22）、Intel MPI（**拷贝路线**：tar 直传 2021.14 Build `20241121`，SHA 与 Artifact 一致，无需 APT hold）、SSH、三仓库 tar 直传（基线 `98038b6`/`08d3cfa`/`b0b6f19`）、env/preset（移除 `~/mkl-extra`）后，配置+编译成功；验证矩阵 **5 轮 × 3 格全 PASS**，含「源码库优先 + 非空 workspace」关键格（24.04 上做不到），后向误差恒 `7.595360e-17`、退出码 0、全流程 ~0.4s。**可写「源码端到端通过」**。运行时 Boost 解析到 Artifact 的 1.74 副本（非系统），但同为 1.74、仅一套，无冲突。
- **勘误（按 append-only 规则以本条为准）**：
  - 2026-07-31、08-02 多条「PETSc 初始化阶段 SIGSEGV」表述为误判。
  - environment.md 2.2「Ubuntu 24.04 无需降级到 22.04」结论被推翻：Boost 是当时未查到的第四个 ABI 约束点（前三个：GCC 11、Intel MPI 精确 Build、Artifact 库组）。
  - environment.md 2.5「外置 mkl-extra 仍可能优先解析」订正为「确实解析到」；`~/mkl-extra` 是版本混装隐患（oneMKL 2025.0.0 vs Artifact 内其余 MKL），22.04 侧 env 脚本已移除该路径。
  - 宋维豪编译文档的「PETSc 最小闭环」= 我们的变体 A（自编 exe + Artifact 库），不等于源码端到端；引用其结论须带此前提。

## [2026-08-02] 三仓库同步并完成新 Artifact 的独立 MKL 验证

- **三仓库 `dev` 已同步并锚定新基线**：主仓由 `82a9c27` 快进到 `98038b6`，Artifact 由 `69aa30b` 快进到 `08d3cfa`，ThirdParty 保持 `b0b6f19`；三者均与各自 `origin/dev` 对齐且工作树干净。主仓获取时出现未开放子模块的访问提示，但远程引用和主仓快进均成功，无需递归修复子模块。
- **更新后配置与编译复核通过**：继续使用 GCC/G++ 11、Intel MPI 2021.14 Build `20241121` 和 `linux_gcc_debug_intelmpi`；CMake 重新配置成功，增量编译到 100%，产出 `SGFEM` 与 5 个测试程序。源码 `SGFEM` 的动态库检查无 `not found`、OpenMPI 或 `libmpi.so.40`。
- **源码端已知边界未变化**：更新后的源码主程序仍在 BDF 导入前、PETSc 初始化阶段触发 SIGSEGV，退出码 `15`，因此不能表述为源码端到端通过。
- **Artifact 的 MKL 缺失问题已由新版本闭合**：`08d3cfa` 新增 `libmkl_sequential.so.2`。明确从 `LD_LIBRARY_PATH` 排除 `~/mkl-extra` 和 `build/intelmpi-debug/lib` 后，`ldd` 将该库解析到 Artifact 自身 `lib/`；CPardiso 对照完成 BDF 导入、装配、分解、求解和 HDF5 导出，退出码 `0`，全流程 0.461 s，后向误差 `7.595360e-17`。
- **本次脱敏产物**：`cpardiso-artifact-update-20260802.h5` 为 1,461,752 bytes，`cpardiso-artifact-update-20260802_0.log` 为 10,637 bytes；原件保留在本机 SGSim `workspace/`，不进入本 Public 仓库。后续三仓库同步与更新后门禁统一见 [build-and-run.md](build-and-run.md)。

## [2026-08-02] 本机 WSL 完成源码编译与 Artifact CPardiso 隔离试算

- **本人按 WSL 流程完成配置与编译复核**：在 Ubuntu 24.04 中加载项目环境，确认 `mpicxx` 指向 Intel MPI 2021.14；`linux_gcc_debug_intelmpi` preset 配置成功，`build/intelmpi-debug` 增量编译到 100%，产出 `SGFEM` 主程序和 5 个测试程序。源码主程序的 `ldd` 无 `not found`、OpenMPI 或 `libmpi.so.40`，PETSc、HYPRE、MKL 和 Intel MPI 均解析到预期位置。
- **源码运行阻塞再次稳定复现**：刚编译的 `SGFEM` 打印 Intel MPI 2021.14 Build `20241121` 后，在 PETSc 初始化阶段触发 SIGSEGV，退出码为 `15`，尚未进入 BDF 导入与 CPardiso 求解。这与 2026-07-31 基线一致，说明配置和编译通过，但源码端到端运行仍未闭合。
- **定位 Artifact 首次对照失败为动态库混装**：`~/sgsim-env.sh` 是源码开发环境，会让 Artifact 的预编译 `SGFEM` 优先加载 `build/intelmpi-debug/lib` 中的 `Framework`、`DataStructure`、`Utility` 等模块；该组合在 MPI 初始化后报 `free(): invalid pointer`。临时隔离 `LD_LIBRARY_PATH` 后，所有项目库统一来自 `Artifact/Ubuntu22-gcc11.4/lib`，不再混入源码构建目录。
- **Artifact CPardiso 对照试算通过**：隔离环境下完成 BDF 导入、装配、`CPardiso<Real_t>` 分解与求解、HDF5 导出，进程退出码为 `0`，全流程 0.464 s，迭代精化后向误差 `7.595360e-17`。生成 `cpardiso-artifact-isolated-20260802.h5`（1,461,752 bytes）与 `cpardiso-artifact-isolated-20260802_0.log`（10,645 bytes）；原件留在本机 SGSim `workspace/`，不进入本 Public 仓库。
- **结论边界**：Ubuntu 24.04 可在固定 GCC/G++ 11 和匹配 ABI 的前提下使用 `Ubuntu22-gcc11.4` Artifact；当前可以表述为「WSL 源码配置与编译通过、Artifact CPardiso 对照通过」，不能表述为「源码端到端通过」。可复现分类与命令见 [build-and-run.md](build-and-run.md)。

## [2026-07-31] Hypre 编译及最小计算流程走通

- **Hypre 可用性阻塞已闭合**：研究院发布最新版本后，团队已完成编译并走通计算流程。这订正了本日志 2026-07-29 条目中「Hypre 仍未到位」的状态；按 append-only 规则不修改历史记录，以本条为准。原文见 [外部群增量归档](../chats/feishu-dut-suanhai-external.md)。
- **完成边界**：当前事实只支持版本已获取、编译完成和最小计算流程走通；尚无性能数据，不能据此认定性能基线、规模测试或 Hypre / PETSc KSP 调优已完成。后续状态以 [plan.md](plan.md) 为准。

## [2026-07-31] SGSim 统一到匹配的 Intel MPI，编译与单测通过，源码主程序转为 PETSc 阻塞

- **Intel MPI 开发环境已补齐并与 Artifact 做到字节级匹配**：Intel 官方 APT 仓库中真正对应 Artifact 的是 `2021.14.1-5`（Build `20241121`），其 `libmpi.so.12.0.0` 与 Artifact 的 SHA-256、Build ID 完全相同。最初按大版本判断安装的 `2021.14.2-7` 实际为 Build `20250213`，二进制不匹配，已纠正；runtime/devel 两个包已设置 APT hold。
- **OpenMPI 保留但已从 SGSim 隔离**：本机项目环境固定 `/opt/intel/oneapi/mpi/2021.14` 和 `I_MPI_FABRICS=shm`，Intel wrappers 优先于 `/usr/bin`。忽略的 `CMakeUserPresets.json` 新增 `linux_gcc_debug_intelmpi`，使用 GCC 11、`build/intelmpi-debug` 和安全安装前缀 `out/intelmpi-debug`；公共 preset、源码与 VS Code 配置均未修改。
- **从空目录配置和 `--parallel 16` 编译通过**。因 `Algebra/` 等未开放模块为空，本机 preset 需把只读 Artifact include/lib 显式作为后备；这也解释了为何单纯更改 `CMAKE_INSTALL_PREFIX` 会先后出现 `Algebra/Config.h` 和 `-lAlgebra` 缺失。旧 `build/debug` 保留，早期不匹配版本的失败现场另行保留。
- **动态加载门禁通过**：主程序和五个测试程序均无 `libmpi.so.40`、OpenMPI 路径或 `not found`；有 MPI 依赖的程序全部解析到 `/opt/intel/oneapi/mpi/2021.14/lib/` 下的 `libmpi.so.12`、`libmpicxx.so.12` 和 `libmpifort.so.12`。
- **单元测试基线已建立**：五个 GoogleTest 程序从 `build/intelmpi-debug/bin` 直接执行，进程退出码全部为 `0`；共运行 468 项，464 项通过、4 项 skipped、0 failed。此前 7 个失败不再复现。`ctest -N` 仍显示 `Total Tests: 0`，只说明项目未注册 CTest。
- **源码主程序端到端仍未通过**：最小静力算例能打印 Intel MPI 2021.14 Build `20241121` 并完成 MPI 初始化，但随后在 PETSc 初始化阶段 SIGSEGV，退出码 `15`，未生成 HDF5。对照的 Artifact 预编译主程序在同一环境、同一算例下退出 `0`，完成 CPardiso 求解与 HDF5 导出。故原先的 MPI 混用缺口已闭合，剩余问题应按源码构建产物与 Artifact/PETSc 二进制组合单独定位，本次不扩大为源码修复。
- **平台口径**：WSL 已可承担日常阅读、修改、编译和单元测试；Windows 与 Artifact 预编译程序保留为已验证运行回退。源码主程序问题解决前，不把 WSL 写成完整端到端功能验证平台；正式性能基线仍只在目标 Linux 机器产生。

## [2026-07-30] Linux 线在本机打通：预编译产物试算通过，源码可编译，缺口收窄到 Intel MPI

- **本机 Linux 开发环境（WSL2 + Ubuntu 24.04）全链路打通**：内网访问 → SSH 鉴权 → 三仓库克隆（与远端一致）→ 工具链安装。过程中发现**本机 Windows 映像缺少虚拟化与虚拟网络组件**（`VirtualMachinePlatform` 在映像中根本不存在，非未启用），靠启用 Hyper-V 与 Containers 系列顶替后才可用；可复现步骤见 `workstation:wsl/README.md`，与研究院工作相关的结论见 [environment.md](environment.md) 第六节。
- **Ubuntu 预编译产物试算通过**：静力线性算例走通 BDF 导入 → 装配 → CPardiso 求解 → 导出 HDF5，后向误差 `1e-16` 量级，两个算例 0.395 s / 0.492 s，与 Windows 侧同类验证（0.256 s）链路一致、量级相当。
- **但要跑起来须先补两件事**：① Artifact 的 `lib/` **缺 `libmkl_sequential.so.2`**，导致三个可执行文件全部无法加载，用同版本（oneMKL 2025.0.0）官方库外挂后解决；② 必须设 `I_MPI_FABRICS=shm`，否则 Intel MPI 走 OFI 而本机无 provider，直接 dump core 且报错指不到真正原因。
- **`libmkl_sequential.so.2` 缺失已由组内成员在项目群独立反馈**（2026-07-30），非本机个案。这是继 `libmkl_avx2` / `libmkl_def` 之后同类问题的再次出现，根因同样是研究院开发机装了 oneAPI 而该文件未收进仓库。
- **源码编译通过——推翻此前「Linux 线在本机编不了」的判断**：`linux_gcc_debug` preset 配置与编译均成功，0 错误、31 秒，产出主程序与 5 个单元测试可执行文件。配置阶段需自行补系统 Boost（含 stacktrace）与 GTest；其中 **`import_gtest` 只在 `if(MINGW)` 分支接线，Linux 分支未指向仓库自带的 `googletest-1.14.0`**，疑为预设疏漏。
- **但新编出的主程序运行即崩溃**，崩在 MPI 初始化：Artifact 的闭源库按 Intel MPI（`libmpi.so.12`）编译，本机因缺 Intel MPI 开发文件，CMake 配置时找到了系统 OpenMPI（`libmpi.so.40`），一个进程里混进两套 MPI 实现。链接期已有警告预告。
- **缺口由「整套第三方依赖」收窄到「仅 Intel MPI 开发文件」**——这是今天最重要的结论。实测 `_3RD_PARTY_ROOT` 指向仓库内 `ThirdParty/Ubuntu22-gcc11.4`，轻量库确实来自仓库；PETSc、MKL、HYPRE、ARPACK 的运行时也从仓库 `Artifact/lib` 正常解析。**据此需订正 [plan.md](plan.md)「环境搭建」中「Linux 预设并未使用仓库内的 Ubuntu 第三方依赖」的表述**，该表述曾是「平台已定：Windows 本机」的依据之一。
- **VPN 是全隧道，会拖慢公网访问**：连 VPN 时公网下载约 70 KB/s，断开后约 5 MB/s，相差约 75 倍，Windows 与 WSL 两侧一致。大批量公网下载（apt / pip）前应先断 VPN；拉内网仓库则必须连着。此前记的内网 200 KB/s 属另一回事——内网本就只有 VPN 一条通路。
- **待办与未查明**：单元测试有 7 个用例失败（文件系统与文本/VTK 比对类），疑与测试数据工作目录有关，未定位；Linux 预设的 `CMAKE_INSTALL_PREFIX` 同样指向 Artifact 目录，**执行 install 会覆盖预编译产物并弄脏仓库**，本机至今未执行。

## [2026-07-29] Windows 工具链安装完成，内部手册「环境安装」四步走完

- **MinGW-w64 gcc 13.2.0 与 CMake 3.28.3 已安装并验证**，装在 `C:\devtools` 下，`bin` 目录已加入用户级 PATH。加上原有的 Git 2.55.0 与 VSCode，内部手册「环境安装」四步全部完成。
- **MinGW 的 ABI 六项逐项核对通过**：版本 13.2.0、三元组 `x86_64-w64-mingw32`、`posix` 线程、`seh` 异常、`ucrt` 运行时、`rt_v11-rev1` 构建修订。这是能与 `Artifact`、`ThirdParty` 中预编译库链接的前提，任一项不符都会在链接阶段失败。
- **三处偏离手册，均已验证不影响结果**（详见 [environment.md](environment.md) 第三节）：
  - 安装路径 `D:\devtools` → `C:\devtools`，本机无 D 盘。
  - 安装包改从上游官方发布页取，未走内部 `Tools` 仓库——该仓库含大量教学视频，克隆 2 分钟落盘 226 MB 仍未完成，遂中止；上游文件名与手册指定一致并做了字节级完整性校验。
  - CMake 用官方 zip 免安装版而非 msi——msi 报权限不足，提权需交互确认 UAC。结果等价。
- **下一步**：内部手册「获取代码」——克隆主仓与两个依赖仓库，随后编译验证。见 [plan.md](plan.md)。

## [2026-07-29] 环境搭建全流程完成，算例试算跑通

- **从零到求解器出结果的完整闭环已打通**：工具链安装 → 三仓库克隆 → CMake 配置 → 编译 → 安装 → 实际算例试算。试算流程为 BDF 导入（2 单元、1 约束、5 载荷）→ 选定 CPardiso 直接法求解 → 导出 HDF5 结果，**全流程 0.256 s**。可复现步骤见 [environment.md](environment.md)。
- **源码基线已锚定**：三个仓库分支统一为 `dev`，基线提交记于 [plan.md](plan.md)「确认源码基线」。此前 plan.md 标注的「分支名与基线提交尚未记录」缺口就此补齐，后续性能数据可锚定在同一基线上比较。
- **更正：子模块拉取失败不是权限缺口，是设计如此**。13 个子模块全部无访问权限，但其头文件在 Artifact 的 `include/`、导入库在 `lib/`，12 个可逐一对应，空目录即正确终态。据此更正本文件同日前一条目中「已整理成给武兴创的权限申请清单」的处理方向——**无需申请**，克隆时也不必带 `--recurse-submodules`。按 append-only 规则不改历史条目，以本条为准。
- **本机构建产物与研究院 CI 产物等价**：`cmake --install` 覆盖 Artifact 中 15 个文件，其中 14 个字节数完全相同，仅主程序差 0.4 KB（构建路径嵌入差异）。这反证本机 MinGW 工具链的 ABI 配置与研究院 CI 完全一致，构建结果可信。
- **副作用需长期注意**：`CMAKE_INSTALL_PREFIX` 指向 Artifact 目录，每次 install 都会覆盖该 git 仓库的已跟踪文件；**不得在该仓库 commit 或 push**，恢复用 `git checkout -- .`。同理，调试所需的启动配置修改会弄脏主仓工作树，同样不提交。
- **经 VPN 克隆慢是结构性的**：内网地址 RTT 约 54 ms，与公网相当，流量绕公网经 VPN 网关；实测约 200 KB/s。已验证内网 GitLab **无任何公网入口**（域名虽有公网 A 记录但端口全不可达），故关代理、关 VPN 均不可行，只能靠浅克隆减少传输量（实测同为依赖仓库，浅克隆传输量降至约 1/3.6，速率提升近 3 倍）。
  - **组内对照：宋维豪侧约 9 Mb/s**（2026-07-28 16:38 内部群答复），与本机约 1.6 Mb/s 相差数倍。**同一 GitLab、同一 VPN 协议下差异如此之大，说明瓶颈在本机接入环境而非 VPN 机制本身**，值得进一步定位；其具体接入方式（是否在研究院现场）尚未问明。
- **宋维豪对两个环境问题的答复（2026-07-29 16:38 内部群，原文见 [../chats/feishu-dut-suanhai-internal.md](../chats/feishu-dut-suanhai-internal.md)）**：① 子模块「他们没有开放源码，后面两个 clone 命令是拉取子模块的编译后代码……直接调用即可」——与本机文件层面的核实完全一致；② clone 速度他那边正常。
- **禅道问题澄清**：宋维豪本人未登录过禅道，「目前开发没用上禅道」，据此该项不阻塞开发，优先级下调，见 [plan.md](plan.md)。
- **性能专项首条实质发现（宋维豪，2026-07-28）**：函数调用热点图结合 MPI 分配情况显示**当前并行负载不均衡**，可作为性能调优与抽取基线的切入点，已记入 [plan.md](plan.md)。
- **Hypre 仍未到位**：2026-07-29 17:23 宋维豪在外部群再次追问杜阳，杜阳 7-28 承诺的「今天或明天」已过期。该项是月底汇报范围的主要不确定因素。
- **待办**：单元测试（内部手册第四章）尚未运行，构建产物中已含 5 个测试可执行文件；`arpackng_DIR` 未被项目使用的警告暂不影响构建，若日后特征值求解出现链接错误可回查。

## [2026-07-29] 开发环境接入全线打通；确定 Windows 开发、Linux 出基线

- **502 的根因是本机代理，不是网络或服务故障**：内网 GitLab 在浏览器与命令行均返回 502，实为本机代理客户端按域名把内网流量劫持到外部节点。需在**系统代理绕过列表**与 **`NO_PROXY` 环境变量**两处分别配置——前者管浏览器，后者管 `curl`/`git`，只配一处会出现「浏览器能开、命令行仍 502」的假象。完整过程、确诊命令与验证方法见 [environment.md](environment.md)。
- **SSH 接入打通**：为内网 GitLab 单独生成密钥（不复用个人 GitHub 密钥），在 `~/.ssh/config` 单列 Host 段隔离，公钥注册后 `ssh -T` 验证通过。排查中曾误按默认 22 端口测试——该端口可连通但并非 GitLab 服务，**连得通不等于连对了服务**，实际服务在非标准端口，端口藏在内部手册的 clone URL 里。
- **仓库权限核验完成**：账号下 8 个项目角色均为 Developer，Windows 与 Linux 两条线所需的源码、教程、Artifact 与第三方依赖仓库齐备，无缺口。另核实内部手册中两处项目路径已更名，GitLab 保留了重定向，手册原命令仍可用。
- **确定开发平台：Windows 本机**。依据是两条线构建预设的可复现性差异——Windows 预设依赖路径全部相对源码树，克隆后即可构建；Linux 预设依赖指向特定机器上的绝对路径，且未使用仓库内的 Ubuntu 第三方依赖，缺该机器无法照配。详见 [plan.md](plan.md)「环境搭建」。
- **正式性能基线不在本机出**：本机 CPU 为大小核混合架构，MPI 各 rank 落核类型不同导致扩展性数据不可比，且 Windows 非部署目标环境。基线须在 Linux 目标机进行，约束记入 [plan.md](plan.md)「抽取性能基线」。
- **待向宋维豪确认**：Linux 预设所依赖的第三方库目录从何而来、是否需要改预设指向仓库内的 Ubuntu 依赖——该答案决定 Linux 线能否在其他机器复制。此前已向其索取的 Ubuntu 步骤与踩坑清单中一并跟进。
- **环境搭建余项**：编译器与构建工具尚未安装、源码未克隆、未编译验证。禅道凭据仍未收到，状态无变化。

## [2026-07-28] 研究院明确下一步抽性能基线；PETSc 最小闭环走通

- **研究院口径（李宁宁部长，外部群）**：认为算海方已掌握 GitLab 上这套代码与 HPC 相关工具链；**建议下一步着手抽取性能基线**，作为后续性能调测与实施方案制定的依据。原文见 [../chats/feishu-dut-suanhai-external.md](../chats/feishu-dut-suanhai-external.md)。
- **PETSc 最小验证闭环已走通**（宋维豪）。过程中 Artifact 缺 `libmkl_avx2.so.2`、`libmkl_def.so.2` 导致退出计算；根因是研究院本地开发机部署了 oneAPI，重新拉取仓库后解决，对口武兴创。产出《SGSim_GCC11_IntelMPI_PETSc 最小验证说明》（内部文档，未公开授权，本仓不留原件）。
- **Hypre 现状**：研究院侧在早版本已完成集成，但目前运行可能有问题，对口杜阳；杜阳 15:47 答复「大概今天或者明天能提交一版」。当前拉取的库中**没有 hypre 头文件**，算海方已提出需要 PETSc 与 Hypre 算例的测试参考命令。
- **构建路径已被组内验证**：宋维豪在 Ubuntu 上用 `linux_gcc_debug` preset 编译成功；初次 clone 有子模块未拉全，研究院王龙指出这些在 Artifact 中有二进制包。
- **源码范围澄清**：李宁宁所指「已掌握的代码」是 GitLab 上的任务源码仓，本人账号亦有权限；这与 [plan.md](plan.md) 中「DLUTFEM 源码未开放」不矛盾——DLUTFEM 是另一支交付（仅提供无源码 debug 包）。据此更正本文件 2026-07-22 条目「SGFEM 正在重构，暂时无法取得源码」：就 GitLab 任务源码仓而言现已可访问，按 append-only 规则不改历史条目，以本条为准。
- **禅道**：宋维豪此前在内部群同样报「VPN 已连接但账号密码登录不进去，禅道也一样」，田昊伦提示确认是否初始化；其后仅确认 GitLab 登录成功，**禅道是否已解决待向宋维豪确认**。本人禅道凭据仍未收到。
- **飞书群聊原文已入仓归档**：外部群与内部群逐条原文见 [../chats/](../chats/README.md)，本条只记结论。

## [2026-07-28] 仓库权限开通，接入链路全部打通

- **仓库权限已授予**：任务相关源码、教程、工具和预编译依赖仓库已可访问，具备开发所需权限；具体项目路径与仓库结构只在内部系统维护。
- **官方工具链**：Windows 与 Ubuntu 均有对应的预编译依赖和构建产物，Linux 是正式支持目标，依赖不必自行从头编译。
- **接入三层全部打通**：VPN（07-27）→ GitLab 账号（07-28）→ 仓库权限（07-28）。工程预备剩余事项为环境搭建、确认源码基线与禅道账号，见 [plan.md](plan.md)。
- **任务范围调整**：DLUTFEM 源码获取与产出文档清单两项转为暂不追踪，理由与恢复条件记于 [plan.md](plan.md)。

## [2026-07-28] GitLab 登录跑通，但仓库权限为空

- **密码初始化完成**：通过账号主邮箱完成密码初始化并登录成功；具体邮件与账号配置只在内部记录中维护。
- **新阻塞：仓库权限为空**。账号下无任何项目，`Tools` 仓库返回 404，Explore 中仅可见两个与本任务无关的公开项目。能登录不等于有仓库权限，需研究院授予相应仓库访问权限。
- **更正**：[plan.md](plan.md) 与本文件 2026-07-27 条目此前记有「SGFEM 源码仓已可通过研究院 GitLab 访问且确认包含源码」，就本人账号而言与实际不符——登录后未见任何 SGFEM 相关项目。按 append-only 规则不修改历史条目，以本条为准，plan.md 已同步更正；若该表述指的是团队其他成员或研究院侧状态，来源待确认。
- 禅道账号凭据仍未收到。

## [2026-07-27] 与魏华祎老师确认汇报、日报与沟通口径

- 双周联合例会初步定于周五下午 4:00；会议时长及首次会议日期仍待确认。
- 对外可汇报当前总体状态、各任务线做过的尝试与实际进展、已形成结果、当前问题、需要大工协助的事项及下一双周计划；不展开算海内部执行策略，避免外部因缺少内部上下文而产生误解。
- PPT、书面提纲、现场演示等汇报形式及本次具体内容范围仍需在会前确定。
- 日报以本人围绕大工项目实际开展的工作为主；协同算海团队推进项目是本人工作的重要组成部分，可以写入日报。
- 涉及研究院的问题在大工项目群中公开沟通，避免单人私聊；算海内部产出、问题和解决过程需结构化沉淀到 `suanhaitech/houzai`，群聊记录和飞书文档不作为长期事实来源。
- 纪要见 [../meetings/2026-07-27-suanhai-discussion.md](../meetings/2026-07-27-suanhai-discussion.md)；上午讨论等待田昊伦提供会议记录后补充。

## [2026-07-27] VPN 接入验证通过；GitLab 与禅道账号仍不可用

- **VPN 已验证可用**：按《账号及网络》的 Windows 连接说明完成 L2TP/IPsec 配置，其中包含 IPsec NAT 穿透所需的注册表项并重启后生效。连接成功后隧道建立、取得内网地址、内网 DNS 可解析研究院系统域名，GitLab 与禅道的服务端口均可达。服务器地址、预共享密钥与账号凭据不入本仓。
- **GitLab 仍无法登录**：初始密码邮件与「忘记密码」重置邮件均未送达个人 Gmail 邮箱（收件箱、推广、社交、垃圾邮件与全部邮件均已检索）。重置页面无论邮箱是否在库都给出相同提示，因此账号登记邮箱是否正确无法由我自行验证。已请武兴创在后台核对登记邮箱，或改绑校内邮箱后重发，或直接设置初始密码。
- **禅道账号未收到**：收到的凭据文件只包含 VPN 账号，禅道用户名与密码从未提供，已一并向武兴创提出。
- **更正**：本文件 2026-07-26 条目与 [plan.md](plan.md) 此前记有「VPN / GitLab / 禅道账号已发放」，该表述系由《账号及网络》中「申请 VPN、Gitlab、禅道账号」一句推断而来，与实际不符。按 append-only 规则不修改历史条目，账号状态以本条为准，plan.md 已同步更正。
- **待确认**：VPN 默认不分流，连接期间全部流量走隧道；是否允许配置分流（split tunneling）以便同时使用外网，需与研究院确认。

## [2026-07-27] 与李宁宁部长确认开发节点与汇报机制

- 通过腾讯会议再次确认开发时间节点保持不变：迭代法与性能基线 2026 年 8 月底、异构并行方案 2026 年 9 月底、有限元全流程与 matrix-free 2026 年底。
- 双方联合例会按两周一次开展；前两周主要处理代码权限，因此没有召开。会议安排在周五，具体时间待与魏华祎老师进一步确认。
- 日报以本人工作和需要协助的事项为主，具体格式向杜阳老师确认；日报用于保证与李宁宁部长的信息同步。
- 每周五汇总 Debug 版本、新取得的代码或程序材料、编译问题和协助需求；取得并使用相关代码后，进一步汇报代码框架认识、性能专项进展、异构并行初步方案及需要大工继续协助的事项。
- 会议纪要见 [../meetings/2026-07-27-liningning-discussion.md](../meetings/2026-07-27-liningning-discussion.md)。

## [2026-07-26] 账号发放到位，开发环境访问打通（本周进展汇总·续）

- **VPN / GitLab / 禅道账号已发放**：先前「VPN 账号暂时创建不了」的阻塞已解除，账号由田昊伦在算海项目组内统一分发并更新过一版。凭据与网络配置由研究院内部文档承载，本仓不记录。
- **账号需邮箱邮件初始化**：未初始化时会出现「VPN 已连接但系统登录失败」，排查时先确认初始化状态。
- **Linux 路径可行但需自建**：武兴创确认 Ubuntu 亦可，研究院只提供 Windows 连接说明；宋维豪已跑通 Ubuntu（L2TP + `network-manager-l2tp`）并成功登录 GitLab，整理为飞书文档《大连理工 Gitlab 连接步骤》，田昊伦要求补成完整流程供组内参考。
- **环境搭建资料位置**：研究院 GitLab `Tools` 仓库（预配准备 / 软件安装 / 建模实践 / DLUTFEM 简介，README 为 DLUTFEM QuickStart Manual）。
- **任务进展**：MFEM 架构调研（houzai [#3](https://github.com/suanhaitech/houzai/issues/3)）已开始，无阻塞。
- 待办：确认我自己的 VPN、GitLab 登录与账号初始化是否跑通。
- 本条按飞书群截图与内部文档回溯整理，具体日期待确认。

## [2026-07-26] 权限仍未开通，先用 DLUTFEM debug 包过渡（本周进展汇总）

- 「武老师」身份已确认为**武兴创**（微信中称「武工」），即阶段安排表中负责账号开通的人。
- **VPN 账号暂时创建不了**：武兴创答复，与陈院长讨论后需先对仓库结构进行重构，待重构完成后再创建，口径为「本周完成」。
- **仓库权限**：李宁宁部长在「研究院-高性能计算」群答复，代码仓库及权限修改本周能完成，届时给我相应仓库权限；在此之前先看相关技术资料。
- **过渡方案**：经李部长建议，武兴创先提供 DLUTFEM debug 版程序、用户手册和一批算例模型（构建日期标记 2026-07-20，无源码），用于先熟悉程序、对性能进行摸底；已在算海项目组内共享，对应 houzai [#2](https://github.com/suanhaitech/houzai/issues/2)。
- 本条按微信与飞书截图回溯整理；各条消息的具体日期待确认，「本周」按 2026-07-20～26 理解。聊天原文归 `heliangos/wechat`，本仓只留结论。

## [2026-07-22] 算海分工确认，任务落地为 houzai issues（2026-07-26 补记）

- 补记：2026-07-20 与魏华祎老师及算海团队召开内部周会（第一周 7.20–7.26），在当日与杜阳老师讨论的基础上确认了分工结论，纪要见 [../meetings/2026-07-20-suanhai-internal-weekly.md](../meetings/2026-07-20-suanhai-internal-weekly.md)；例会节奏定为每周一上午 10:30。
- 2026-07-22 分工落地为 `suanhaitech/houzai` 的四个任务 issues（Hypre/PETSc 可复现构建与验证、DLUTFEM 兼容性与性能基线、MFEM 多后端与 MPI 架构研究、SGFEM 性能分析工具链）；负责人与排期以内部 issues 为准，指针已收录进 [plan.md](plan.md)。
- 口径更新：SGFEM 正在重构，暂时无法取得源码，开放时间待研究院明确；现阶段相关工作按黑盒/外围方式推进。
- 研究院阶段安排表（时间点与初步负责人：性能专项 2026-08-31 / 2026-12-31，架构设计 2026-09-30）已录入 plan.md。

- 陈玉震老师明确：我在研究院的工作由李宁宁部长负责协调安排。
- 李部长在「研究院-高性能计算」群内发布 HPC 开发任务，明确近期工作内容、协作人和参考资料；我已确认收到。
- 已到研究院，电脑和工位落实；开始对接开发环境、代码权限及协作分工。
- 09:56 已在算海内部群同步任务并提议当天开短会，等魏华祎老师确定时间。
- 上午与杜阳老师讨论（详见 [../meetings/2026-07-20-duyang-discussion.md](../meetings/2026-07-20-duyang-discussion.md)）：明确最紧迫事项为代码权限/开发环境（问武老师）、产出文档与咨询渠道（问李宁宁部长）、Hypre / PETSc KSP 预条件子设计（现有测试算例不收敛）；性能专项定位为熟悉代码、发现问题向大工反馈；缺设备可由浪潮提供。

## [2026-07-09] 阶段计划形成

- 与魏华祎老师、陈玉震老师及研究院其他老师讨论后续工作安排，初步形成阶段计划：工程预备、性能专项、架构设计（详见 [../meetings/2026-07-09-stage-plan-discussion.md](../meetings/2026-07-09-stage-plan-discussion.md)）。
