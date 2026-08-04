# WSL/C++ 开发流程

本文面向第一次参与 SGSim C++ 开发的人员，说明如何判断构建结果、管理分支、阅读和修改代码、运行测试、调试以及收尾。实际配置、编译、动态库检查和算例运行的命令统一见 [build-and-run.md](build-and-run.md)；项目环境事实见 [environment.md](environment.md)。

> **操作命令的唯一来源**：本文不再复制三仓库同步、环境加载、CMake 配置、编译、`ldd`、源码试算或 Artifact 对照命令。需要执行这些操作时直接按 [build-and-run.md](build-and-run.md) 对应章节操作。

内部源码、仓库地址、模型原件和凭据只在受控系统中保存。本仓库只记录可公开的开发方法、环境结论和脱敏路径。

## 一、先建立正确的判断标准

### 1.1 从源码到结果的五层门禁

```text
源码和头文件
    ↓ 编译
目标文件和动态库
    ↓ 链接
可执行文件
    ↓ 动态加载
进程启动
    ↓ 功能运行
日志和结果文件
    ↓ 结果验证
可信结论
```

| 层级 | 成功判据 | 常见失败 |
| --- | --- | --- |
| 配置 | CMake 退出码为 `0`，生成构建系统 | 依赖、编译器或 preset 错误 |
| 编译与链接 | 构建退出码为 `0`，没有 `error:` | 头文件、符号、ABI 或库路径错误 |
| 动态加载 | `ldd` 没有 `not found`，MPI 未混用 | `LD_LIBRARY_PATH`、运行库版本错误 |
| 功能运行 | 进程退出码为 `0`，主流程完整结束 | 初始化、输入、内存或算法错误 |
| 结果验证 | 输出存在且与参考或不变量一致 | 数值错误、回归或测试口径错误 |

不能用上一层代替下一层。例如源码编译通过，不能证明主程序能够启动；Artifact 算例成功，也不能证明刚编译的源码正确。

### 1.2 CMake 在工程中的作用

CMake 读取 `CMakeLists.txt` 和 preset，生成构建系统，再由编译器完成编译与链接：

```text
CMakeLists.txt + CMake preset
        ↓
build/intelmpi-debug/ 中的构建系统
        ↓
GCC/G++ 11 + Intel MPI wrappers
        ↓
build/intelmpi-debug/bin 和 build/intelmpi-debug/lib
```

项目公共配置在 `CMakePresets.json`，本机 Intel MPI 配置在被 Git 忽略的 `CMakeUserPresets.json`。当前源码开发 preset 为 `linux_gcc_debug_intelmpi`；触发重新配置的条件由 [build-and-run.md](build-and-run.md) 第五节统一规定。

### 1.3 Debug 与 Release

| 配置 | 主要用途 | 特点 |
| --- | --- | --- |
| Debug | 日常开发、断点和错误定位 | 保留调试信息，优化少，运行较慢 |
| Release | 交付验证和正式性能测试 | 开启优化，变量可能被优化，单步困难 |

第一次修改只使用 Debug。功能正确后再单独验证 Release；不要在同一个构建目录中来回切换配置，也不要把本机功能试算当成正式性能数据。

## 二、当前能力与边界

截至 2026-08-04（迁移到 Ubuntu 22.04）本机状态如下：

| 工作 | 状态 |
| --- | --- |
| WSL、内网和 GitLab 访问 | 已通过 |
| Intel MPI 与 Artifact ABI | 已匹配，未混用 OpenMPI |
| 源码配置和编译 | 已通过，产出主程序和 5 个测试程序 |
| 单元测试基线 | 468 项运行、464 项通过、4 项 skipped、0 failed |
| 源码主程序运行 | 端到端通过（22.04 迁移后；后向误差 `7.595360e-17`） |
| PETSc / Hypre 迭代求解 | 最小闭环已验证（`TPetscKsp` / `THypreKsp`，见 [build-and-run.md](build-and-run.md) 8.2/8.3） |
| Artifact CPardiso 对照 | 隔离动态库后通过 |
| 正式性能基线 | 不在本机工作站（含 WSL）产生 |

因此，本机已经可以稳定执行“阅读 → 修改 → 编译 → 相关单元测试 → diff 检查 → 源码最小验证”。迁移前的 SIGSEGV 阻塞（Boost ABI 冲突）已在 22.04 解除，详见 [log.md](log.md)；Artifact 对照仍只证明预编译程序与本机运行环境可用。

## 三、目录与修改边界

源码根目录为：

```text
/home/brighthe/workspace/SGSim
```

日常开发始终在 WSL 的 Linux 文件系统中进行，不在 `/mnt/c` 下长期编译。

| 目录 | 用途 | 处理规则 |
| --- | --- | --- |
| 主源码目录 | 可见源码、CMake 文件和测试 | 在本地开发分支上修改 |
| `build/intelmpi-debug/` | CMake cache、目标文件和本次构建产物 | 可重新生成，不提交 |
| `workspace/` | 本地算例日志和结果 | 可生成，不提交 |
| `Artifact/` | 研究院预编译头文件、库和程序 | 只读，不安装覆盖、不提交 |
| `ThirdParty/` | 第三方依赖 | 只读，不提交 |

部分子模块目录为空是权限与交付方式决定的正常状态；其头文件和预编译库由 Artifact 提供。不要把空目录自动视为克隆失败，也不要自行递归修复子模块。

### 3.1 禁止事项

- 不执行 `cmake --install`；安装安全边界见 [build-and-run.md](build-and-run.md) 第七节。
- 不在 `dev` 上直接开发。
- 不执行可能清理本地改动的 VS Code 任务，如 `Clear Local Branch`。
- 不提交 Artifact、ThirdParty、build、workspace、本机 preset 或本地调试配置。
- 不使用 `git add -A`；提交前只暂存本任务明确涉及的文件。
- 不在本机输出或汇报正式性能基线。

## 四、开始一次开发任务

先按 [build-and-run.md](build-and-run.md) 第三节完成当前 WSL 会话准备，再处理 Git 状态。

### 4.1 确认分支和工作树

```bash
git status --short --branch
```

先区分已有改动、未跟踪文件和当前分支。已有改动默认属于用户，不覆盖、不丢弃，也不顺手纳入当前任务。

### 4.2 同步三个仓库基线

只有需要同步内网 GitLab 时才连接 VPN。主仓、Artifact 和 ThirdParty 的检查、审阅与快进命令统一按 [build-and-run.md](build-and-run.md) 第四节执行，不在本文维护第二套命令。

三个仓库都要检查，但只有远程领先时才合并。若任一仓库工作树不干净或无法快进，停止并处理分叉；不要强制覆盖，也不要递归修复未开放源码的子模块。Artifact 和 ThirdParty 只用于同步依赖，不建立业务开发分支。

### 4.3 建立本地主仓开发分支

```bash
git switch -c local/<任务名>
```

任务名应能说明修改目的。`local/` 只作为本地示例；需要 push 时按团队规范确认远端分支名。

### 4.4 从同一环境打开 VS Code

在已经完成会话准备的 WSL shell 中打开工程：

```bash
code .
```

验收：VS Code 左下角显示 WSL/Ubuntu，工程路径位于 `/home/brighthe/workspace/SGSim`，而不是 Windows 文件系统窗口。

## 五、阅读和修改代码

### 5.1 阅读顺序

面对一个功能点时，按以下顺序定位：

1. 从测试或主程序调用点找到入口。
2. 查看对应头文件，理解类型、接口、所有权和约束。
3. 查看实现文件，确认实际控制流和数据流。
4. 搜索所有调用点，判断影响范围。
5. 查看相关 `CMakeLists.txt`，确认源码属于哪个 target、受哪些选项控制。
6. 查找相邻测试，确认现有行为和可复用测试夹具。

不要只凭文件名猜模块职责，也不要只修改实现而忽略声明、调用点、CMake 接线和测试。

### 5.2 修改原则

- 一次只解决一个问题，保持 diff 小而可解释。
- 修改前写明输入、预期输出和失败表现。
- 优先复用现有接口和测试结构，不顺手重构无关代码。
- 改变公共接口时同步检查全部调用方。
- 修改可选模块时确认对应 `SG_*` 选项确实让本地源码参与构建，避免实际仍链接 Artifact 旧库。
- 新增文件时确认 target 已包含该文件，不能以“文件存在”代替“参与构建”。

### 5.3 修改后的验证顺序

1. 按 [build-and-run.md](build-and-run.md) 第六节增量编译并确认预期 target 被重新构建。
2. 运行与修改直接相关的测试。
3. 具备运行条件时，按操作手册执行源码最小验证。
4. 检查工作树和实际 diff。
5. 保存关键失败的完整 stdout/stderr，不只截取最后一行。

## 六、单元测试

当前工程生成 5 个 GoogleTest 程序，但没有通过 `add_test()` 或 `gtest_discover_tests()` 注册给 CTest。

### 6.1 理解 CTest 状态

```bash
ctest --test-dir build/intelmpi-debug -N
```

当前预期为 `Total Tests: 0`。这只表示 CTest 没有注册测试，不表示所有测试通过。

### 6.2 列出测试用例

例如查看 `UtilityTest`：

```bash
cmake -E chdir build/intelmpi-debug/bin ./UtilityTest --gtest_list_tests
```

测试可能依赖工作目录，因此固定从 `build/intelmpi-debug/bin` 运行。

### 6.3 运行相关测试

```bash
cmake -E chdir build/intelmpi-debug/bin ./UtilityTest
```

修改其他模块时选择对应测试程序：

- `BeamSecPropCalculatorTest`
- `ElementCalculatorTest`
- `DataStructureTest`
- `FrameworkTest`
- `UtilityTest`

验收必须同时记录进程退出码、passed/failed/skipped 数量和失败位置。当前稳定基线为五个程序退出码均为 `0`，共 468 项运行、464 项通过、4 项 skipped、0 failed。

VS Code 中可用 TestMate 浏览和运行测试，但出现差异时以终端完整输出为准。不要为了追求“全绿”而跳过失败用例或改变工作目录；先判断是环境差异、测试数据问题还是真实回归。

## 七、调试与错误分类

### 7.1 VS Code 调试

1. 从已完成会话准备的 WSL shell 打开 VS Code。
2. 在 Run and Debug 中选择 Linux 调试配置。
3. 将 `.vscode/launch.json` 中的占位模型路径换成本地真实算例。
4. 在准备观察的源码行设置断点。
5. 启动前先确认相同输入可以在终端复现；调试器不是环境排错工具。

常用操作：

| 操作 | 快捷键 | 含义 |
| --- | --- | --- |
| Continue | `F5` | 继续到下一断点 |
| Step Over | `F10` | 执行当前行，不进入函数 |
| Step Into | `F11` | 进入当前函数 |
| Step Out | `Shift+F11` | 执行到当前函数返回 |
| Stop | `Shift+F5` | 结束调试 |

当前先做单进程调试。多 rank 调试需要为每个进程建立可区分的调试入口，等进入真实 MPI 问题后再单独设计。

### 7.2 错误分类

| 阶段 | 常见关键词或症状 | 优先检查 |
| --- | --- | --- |
| CMake 配置 | `Could NOT find`、`NOTFOUND` | preset、依赖路径、cache |
| 编译 | `error:`、类型或头文件错误 | 第一条 error、声明与 include |
| 链接 | `undefined reference`、`cannot find -l...` | target、ABI、库版本 |
| 动态加载 | `error while loading shared libraries` | `ldd`、库搜索顺序 |
| MPI 初始化 | `PMPI_Init`、dump core | Intel MPI/OpenMPI、`I_MPI_FABRICS` |
| 运行时 | SIGSEGV、异常、错误返回 | 调用栈、对象生命周期、数组边界 |
| 测试 | assertion、文本或 VTK 差异 | 工作目录、测试数据、真实回归 |

通用排查顺序：

1. 保存完整 stdout/stderr 和退出码。
2. 找第一条真正错误，不从最后一条连锁报错开始。
3. 先确定属于配置、编译、链接、加载、运行还是结果问题。
4. 一次只改变一个条件，然后重现。
5. 修复后重新执行最小复现，不用“看起来应该好了”代替验证。

源码主程序已在 22.04 端到端通过；PETSc / Hypre 迭代求解切换（`TPetscKsp` / `THypreKsp`）见 [build-and-run.md](build-and-run.md) 8.2 / 8.3。Artifact 只有在隔离动态库后才能作为环境对照，命令见 [build-and-run.md](build-and-run.md) 第九节。

## 八、每日开发闭环

```text
准备 WSL 会话并确认工作树
→ 定义一个小任务和验收标准
→ 阅读接口、实现、调用点、CMake 和测试
→ 小范围修改
→ 按操作手册增量编译
→ 运行相关测试
→ 必要时调试或运行最小算例
→ 检查 diff 和空白
```

### 8.1 每次会话

- [ ] 按 [build-and-run.md](build-and-run.md) 第三节准备 WSL 会话。
- [ ] 需要远程最新修改时，按第四节检查并同步三个仓库。
- [ ] 确认 VPN 状态与本次操作匹配。
- [ ] 检查当前分支和工作树，不覆盖既有改动。
- [ ] 写清本次任务、输入和验收条件。

### 8.2 每次修改后

- [ ] 增量编译成功，并确认预期 target 重新构建。
- [ ] 运行与修改直接相关的测试并记录结果。
- [ ] 具备条件时完成最小功能验证（22.04 源码端到端已通过，见 [build-and-run.md](build-and-run.md) 8.1）。
- [ ] 检查 `git status --short` 和 `git diff`。
- [ ] 执行 `git diff --check`，确认没有尾随空白或冲突标记。
- [ ] 没有修改或暂存 Artifact、ThirdParty、build、workspace 和本地配置。

### 8.3 Artifact 对照

- [ ] 只使用 [build-and-run.md](build-and-run.md) 第九节的隔离命令。
- [ ] `ldd` 不含 `build/intelmpi-debug/lib` 或 `not found`。
- [ ] 日志出现 `CPardiso<Real_t>` 和 `Job ... Finish`，退出码为 `0`，结果文件非空。
- [ ] 结论只写为 Artifact/CPardiso 环境对照通过，不表述为源码端到端通过。

### 8.4 提交前

- [ ] diff 只包含当前任务需要的文件。
- [ ] 测试与验证结果能够对应到本次修改。
- [ ] 只暂存明确文件，不使用宽泛暂存。
- [ ] 未经明确要求，不 commit、不 push、不创建 Merge Request。

## 九、项目必需术语

| 术语 | 含义 |
| --- | --- |
| ABI | 二进制接口约定；编译器、运行时或 MPI 不兼容时可能编译成功但无法链接或运行 |
| target | CMake 管理的构建目标，可以是可执行文件或库 |
| preset | 可复用的 CMake 配置、选项、环境和构建目录 |
| cache | CMake 保存的探测结果；依赖变化后旧 cache 可能误导构建 |
| dynamic library | 运行时加载的 `.so` 文件，路径和版本必须匹配 |
| smoke test | 用最小输入快速确认主链路能否运行 |
| regression | 原本正确的行为被新改动破坏 |
| MPI rank | MPI 并行程序中的一个独立进程编号 |

## 参考

- 唯一操作手册：[build-and-run.md](build-and-run.md)
- 项目环境事实：[environment.md](environment.md)
- 当前任务状态：[plan.md](plan.md)
- 历史进展：[log.md](log.md)
