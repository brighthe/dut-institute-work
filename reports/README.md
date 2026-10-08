---
title: "项目报告"
type: index
tags:
  - reports
status: in-progress
date_added: 2026-07-29
date_update: 2026-09-29
---

# 项目报告

本目录统一承载大工项目的日报、双周汇报周期文档，以及各周期内**收到的工作输入材料**与**本人承担任务的相关材料**。

日报与汇报是**派生层**：结论依据留在各任务线的事实源文件里，本目录只做对外汇报的组织与措辞。[materials/](materials/) 与 [my-tasks/](my-tasks/) 是本目录内仅有的两块非派生内容，按周期分收**收到的**与**本人产出的**东西：前者登记算海同事在周期内发出的材料，按收到日期归入对应周期，页面记来源、核心内容摘要与原件指针（周期 01–04 的早期材料为原文照录）；后者收本人在该周期承担任务的相关材料，主要是对外交付稿本体及其撰写与提交状态。两者都不充当事实源——技术结论与交付物状态仍以对应任务线的 `log.md` / `artifacts.md` 为准，本目录不复述。

## 目录结构

| 目录 | 内容 |
| --- | --- |
| [daily/](daily/) | 项目日报；一个双周汇报周期一个文档，按当天实际工作顺序编号记录，协同类与个人类工作混排 |
| [materials/](materials/) | 周期内收到的工作输入材料（来源、摘要与原件指针），按 `biweekly-NN/` 分目录存放 |
| [my-tasks/](my-tasks/) | 周期内本人承担任务的相关材料，按 `biweekly-NN/` 分目录存放 |

未来需要阶段汇报时可新增 `stage/` 同级目录；本轮暂不创建。日报骨架见 [../schema/templates/daily-report.md](../schema/templates/daily-report.md)。

## 汇报周期

前四次双周汇报均在周五进行，周期以汇报日收尾。第五次周期已确认为 2026-09-19 至 2026-10-02；汇报日按算海 2026-09-22 发出的[第五次双周会议预计产出](materials/biweekly-05/suanhai-fifth-biweekly-expected-outputs.md)为 10-09（周五），该材料已于当日经讨论确认，**对外通知尚未发出**。

| 次 | 周期 | 汇报日 | 周期文档 | 收到材料 | 本人任务材料 |
| --- | --- | --- | --- | --- | --- |
| 01 | 2026-07-20 至 2026-07-31 | 07-31 | [biweekly-01](daily/biweekly-01-2026-07-20-to-2026-07-31.md) | 无 | 无 |
| 02 | 2026-08-03 至 2026-08-14 | 08-14，[纪要](../meetings/2026-08-14-heterogeneous-parallel-second-biweekly.md) | [biweekly-02](daily/biweekly-02-2026-08-03-to-2026-08-14.md) | 无 | 无 |
| 03 | 2026-08-17 至 2026-09-04 | 09-04，[纪要](../meetings/2026-09-04-heterogeneous-parallel-third-biweekly.md) | [biweekly-03](daily/biweekly-03-2026-08-17-to-2026-09-04.md) | 4 份 | 无 |
| 04 | 2026-09-07 至 2026-09-18 | 09-18，[纪要](../meetings/2026-09-18-heterogeneous-parallel-fourth-biweekly.md) | [biweekly-04](daily/biweekly-04-2026-09-07-to-2026-09-18.md) | 7 份 | 1 份 |
| 05 | 2026-09-19 至 2026-10-02 | 10-09（预定，对外通知未发） | [biweekly-05](daily/biweekly-05-2026-09-19-to-2026-10-02.md) | 7 份 | 无 |

三处需要说明的空档，均为事实记录，不作推断补全：

- 周期 1 的起始日按双周制自汇报日 07-31 倒推得出，2026-07-20 至 07-24 无日报留档；第一次汇报本身也无纪要留档。
- 第二次与第三次汇报间隔三周，周期 3 相应为三周；其中 2026-08-17 至 08-21 无日报留档。
- 2026-08-08（周六）有日报，归入周期 2。

## 材料清单

材料按**收到日期**归入周期，与内容归属无关：内容归属仍看 [../hpc/README.md](../hpc/README.md) 等任务线索引。收到日期或归属一时确定不了的，仍直接归入最接近的周期目录，在下表「状态」栏标「收到日期待确认」或「归属待确认」，不推断、也不另设暂存区中转。

| 周期 | 文件 | 原文件名 | 来源与内容 | 状态 |
| --- | --- | --- | --- | --- |
| 03 | [cpardiso-mumps-solver-comparison.md](materials/biweekly-03/cpardiso-mumps-solver-comparison.md) | `CPardiso与MUMPS解法器性能比较.md` | 胡凯（算海）2026-08-31 产出，直接解法器对比的精简主文档；2026-08-29 讨论确定的 8-31 节点材料，供 9-4 阶段进展沟通使用 | 已成稿 |
| 03 | [cpardiso-mumps-full-data.md](materials/biweekly-03/cpardiso-mumps-full-data.md) | `完整数据对比.md` | 胡凯（算海）2026-08-31 产出，与上一份主文档配套的完整支撑材料：算例规模、非求解阶段、求解时间、资源与残差、rank 级负载、BLAS 后端对照 | 已成稿 |
| 03 | [preconditioner-integration-design-and-test.md](materials/biweekly-03/preconditioner-integration-design-and-test.md) | `预条件器接入设计与测试.md` | 胡凯（算海）2026-08-31 产出，BDDC 预条件器接入 SGSim/SGFem/Algebra 的架构设计、数学定义与 14 个算例的矩阵性质、MUMPS 直接法基线、BDDC 迭代法验证结果；供 9-4 阶段进展沟通使用 | 已成稿，接入架构一节自标「待修改」 |
| 03 | [mpi-solver-performance-comparison.md](materials/biweekly-03/mpi-solver-performance-comparison.md) | `MPI_Solver_Performance_Comparison.md` | 胡凯（算海）2026-09-04 产出，14 个结构有限元算例在 `16 MPI × 1线程` 与 `24 MPI × 1线程` 下三种线性求解方案（PETSc 调 MUMPS 全局直接法、BDDC 等）的对比 | 已成稿 |
| 04 | [environment-checklist.md](materials/biweekly-04/environment-checklist.md) | `Environment_Checklist.md` | 宋维豪（算海）2026-09-09 发于飞书算海内部群，SGSim 编译环境与验证流程：221 节点编译器/MPI/MKL/PETSc 基线、本地 Docker Ubuntu 22.04 容器从零配置、本地 LP64 构建与 221 节点现有 ILP64 PETSc 的区分、打包上传与 Slurm 验证脚本 | **未定稿**——作者附言「大致编译流程，后续我再优化一版」，待更新版 |
| 04 | [reissner-mindlin-shell-linear-static.md](materials/biweekly-04/reissner-mindlin-shell-linear-static.md) | `壳结构线性静力分析.md` | 胡凯（算海）2026-09-12 发出，四节点 Reissner–Mindlin 壳单元线性静力分析的完整数学推导：中面几何与运动学、壳应变与截面本构、总势能与弱形式、非协调膜应变与静态凝聚、MITC4 横向剪切、钻转稳定化、线性运动约束消元与拉格朗日乘子形式、约束与区域分解求解器的衔接；引用公开文献，不含源码、内部路径与算例数据 | 已成稿；是[第四次对外双周报](materials/biweekly-04/dut-fourth-biweekly-external-report.md) 2.2 节的详细材料（2026-09-18 据报告正文确认，此前记为用途待确认） |
| 04 | [bdf-to-db-partition-dataflow.md](materials/biweekly-04/bdf-to-db-partition-dataflow.md) | `BDF到DBManager与Partition计算数据流.md` | 宋维豪（算海）2026-09-12 发出，`50w-2d` 算例从 BDF 到 db、分区与求解的全链路数据流：BDF 卡片统计与 ID 语义、Service/DataOperate/Repository 分层写入 HDF5、串行 METIS 八分区、PLOAD4 与 RBE 在分区后的处理、自由度分类与优化建议；原文自带「已确认 / 逻辑关系 / 未确认」证据标记 | 已成稿；含 SGSim 接口、源码片段、内部路径与算例数据，**经用户确认已取得算海公开授权**后入仓 |
| 04 | [rd-plan-heterogeneous-sections-draft.md](materials/biweekly-04/rd-plan-heterogeneous-sections-draft.md) | `HPC 研发规划.pdf` | 田昊伦（算海）2026-09-15 提供，研究院《HPC 研发规划（2026-2027）》1.3.2、1.5、2.2 三节的撰写稿：1.3.2 给出「原文 / 改写」两版与多后端异构并行计算架构图、1.5 给出 2027 四季度计划与里程碑（图片表格）、2.2 给出研发目标、技术路线、现状、预期成果、开发清单、协调事项与风险应对；作者即田昊伦（2026-09-16 本人确认，此前记为待确认） | **初稿**——本人在其基础上修改完善后已写入源文档，落稿与本稿有出入，状态与差异见 [my-tasks/biweekly-04/rd-plan-2026-2027-heterogeneous.md](my-tasks/biweekly-04/rd-plan-2026-2027-heterogeneous.md)；PDF 转换归档，两张嵌入图已提取至 `media/`。公开边界：含 SGSim 模块名与目标架构图，不含凭据、内部地址、源码与算例数据，**经用户确认后入仓** |
| 04 | [dut-fourth-biweekly-external-report.md](materials/biweekly-04/dut-fourth-biweekly-external-report.md) | `report.md` | 2026-09-18 提交于 `suanhaitech/houzai`（GitHub 账号 `ChaosTHL`，文内未署作者），第四次对外双周报会前正式用稿：会议议程、本周期三项进展（TensorFem 多后端验证、大工算例数学结构推导、异构并行研发初步方案）、产出与边界表，以及三条待讨论事项（`DBManager` 数据到显存的搬运开销、复杂约束与 ghost 数据交互、架构实现工作量与时间规划） | 已成稿（自标「会前版」）；**作者待确认**。含算例规模与墙钟性能数据、首尾两段内部审查注释，**经用户确认后原样入仓**；文内「汇报周期」自 09-05 起算，与本表 09-07 有出入，原文照录不改 |
| 04 | [tensorfem-multi-backend-validation.md](materials/biweekly-04/tensorfem-multi-backend-validation.md) | `TensorFem多后端验证.md` | 2026-09-18 提交于 `suanhaitech/houzai`（由 GitHub 账号 `ChaosTHL` 提交，文内未署作者，作者为宋维豪），上一份报告 2.1 节的详细材料：TensorFem 两种数据入口（活动 `DBServiceFactorySP` 与独立 `.db` 文件）× 三种后端（xtensor CPU / Torch CPU / Torch CUDA）的编译依赖、构建配置、后端切换入口、运行命令，以及 24 次求解的正确性对照与墙钟时间 | 已成稿；作者宋维豪（算海），2026-09-18 经用户确认。含 `/home/peter/...` 本机路径、Docker 容器名与完整 CMake 构建参数，**经用户确认后原样入仓，未作脱敏** |
| 04 | [rd-plan-heterogeneous-sections-final.md](materials/biweekly-04/rd-plan-heterogeneous-sections-final.md) | `HPC 研发规划.md` | 2026-09-18 提交于 `suanhaitech/houzai`（GitHub 账号 `ChaosTHL`），上述报告 2.3 节的详细材料，《HPC 研发规划（2026-2027）》三节的**落稿版**：1.3.2 只留改写口径并附一张架构图，1.5 仅留一行任务说明（无季度表），2.2 全节展开（研发目标、技术路线、现状、预期成果、开发清单、协调事项、风险应对） | 已成稿；与同周期 `rd-plan-heterogeneous-sections-draft.md`（田昊伦初稿）**不是同一稿**，差异见 [my-tasks/biweekly-04/rd-plan-2026-2027-heterogeneous.md](my-tasks/biweekly-04/rd-plan-2026-2027-heterogeneous.md)；是否直接导出自飞书源文档无依据，**作者待确认**。含人员实名责任分工与 NAS 存放约定，**经用户确认后原样入仓**；嵌入图提取至 `media/rd-plan-heterogeneous-sections-final/fig-01.png` |
| 05 | [problem-dependent-mesh-partitioning.md](materials/biweekly-05/problem-dependent-mesh-partitioning.md) | `work_report.md` | 位于算海 GitLab `meshx` 仓库 `develop` 分支，陈康（算海）产出，文内标注汇报日期 2026-09-17、未署作者，作者 2026-09-21 经用户确认；阶段工作汇报「面向并行有限元的问题依赖网格分区」：METIS 单元对偶图基线、工程信息的两层分类、统一数学模型与处理流程（must-link 精确收缩、超边投影、有限修复、原始模型独立验收），以及单层真实网格（32,594 个 CQUAD4）上半宽 SPC、CONTIG 选项与复合约束的实验结果与两项改进方向 | 已成稿。按 2026-09-21 收到归入周期 05，与文内 09-17 汇报日期所属周期不一致，按收到日期归档不作调整。按用户 2026-09-29 要求，页面只记来源、核心内容与结论摘要和原件指针，不录原文与图片 |
| 05 | [suanhai-fourth-biweekly-meeting-note.md](materials/biweekly-05/suanhai-fourth-biweekly-meeting-note.md) | `meeting_note.md` | 位于 `suanhaitech/houzai` `develop` 分支 `kb/meetings/2026_09_18_dalianligong_fourth_external_biweekly/` 下，2026-09-20 由 GitHub 账号 `ChaosTHL` 首次提交、2026-09-21 更新后续行动项，本件取更新后的版本；算海依据 2026-09-18 会议逐字稿整理的第四次对外双周会议正式纪要：会议信息与参会名单、异构并行验证 Demo 的进展与技术限制、Demo 定位与两套代码协同、`matrix-free` 定位、PETSc/BDDC 与异构并行的关系、八条会议结论，以及按异构并行 Demo（宋维豪、魏华祎）／迭代法性能基线（胡凯、杜阳）／网格分区与任务划分（陈康）三条线重排的后续行动项 | 对方自标「整理稿，待参会各方核对」，故记 `draft`；文内未署作者，**作者待确认**。按 2026-09-21 收到归入周期 05，与会议所属周期 04 不一致，按收到日期归档不作调整。含实名参会名单与人员分工、SGSim 模块名、算例规模与墙钟性能数字，不含凭据、内部地址与源码，**经用户确认后原样入仓**，未作改写或脱敏。本仓同一场会另有一份依据会议自动纪要整理的[纪要](../meetings/2026-09-18-heterogeneous-parallel-fourth-biweekly.md)，两者在参会名单、性能数字口径与人员分工上有出入，**该纪要尚未按本件回填** |
| 05 | [suanhai-fifth-biweekly-expected-outputs.md](materials/biweekly-05/suanhai-fifth-biweekly-expected-outputs.md) | `第五次双周会议预计产出.html` | 田昊伦（算海）2026-09-22 发于飞书算海内部群，标注为「后续要发在外部群里的网页材料」；给出第五次双周会议时间 2026.10.9 与三项预期产出及汇报人——陈康「面向 BDDC 和异构计算的结构感知分区与任务划分方案」、胡凯「面向典型算例的迭代法性能基线与瓶颈分析」、宋维豪「面向多后端与 GPU 的异构并行 Demo 优化与可行性验证」，并说明三者「上游结构—性能证据—架构验证」的关系与共同基础 | 已成稿；文内未署作者，作者田昊伦，2026-09-22 经用户确认，内容同日经用户与算海讨论确认。单页 HTML 转 Markdown 归档，两张页眉 logo 未提取。含三人实名汇报分工与模块名，不含凭据、内部地址、源码与算例数据，**经用户确认后入仓**。本件是上表周期 05 汇报日的依据来源 |
| 05 | [hpc-rd-plan-2027-annual.md](materials/biweekly-05/hpc-rd-plan-2027-annual.md) | `2027_hpc研发计划.html` | 田昊伦（算海）2026-09-22 与上一份同批发于飞书算海内部群，同样标注为对外网页材料；2027 年度异构并行研发计划的五个季度节点与里程碑：Q1 架构边界与核心接口、Q1–Q2 独立有限元内核与 CPU 参考闭环、Q2–Q3 CPU/CUDA 多后端单节点验证、Q3–Q4 分布式异构执行与规模验证、Q4 工程化整理与年度交付，另有「先验证再扩展」「两条路线并行保留」两条边界原则 | 已成稿；文内未署作者，作者田昊伦，2026-09-22 经用户确认，内容同日经用户与算海讨论确认。单页 HTML 转 Markdown 归档，两张页眉 logo 未提取。含 SGSim 模块名与 CPU/MPI、CUDA 路线表述，不含凭据、内部地址、源码与算例数据，**经用户确认后入仓**。与周期 04 [rd-plan-heterogeneous-sections-final.md](materials/biweekly-04/rd-plan-heterogeneous-sections-final.md) 的 1.5 节属同一主题，该落稿版 1.5 无季度表，两者关系与本件能否作为 1.5 待定时间列的落定依据**均待确认** |
| 05 | [sgsim-tensorfem-heterogeneous-progress.md](materials/biweekly-05/sgsim-tensorfem-heterogeneous-progress.md) | `SGSim_TensorFem进展汇报.html` | 宋维豪（算海）2026-09-28 发于飞书算海内部群，12 页网页幻灯片「SGSim × TensorFem 异构有限元计算进展」：GPU 路径把数值装配、PETSc CUDA 求解与反力/应力计算留在设备侧，MPI 1/2/4/8 进程经 Partition 与 Service 供数运行；两档 Tet 算例（96,000 与 165,888 个 CTETRA4）上 42 组首轮结果与单进程直接法逐值对照通过，另有五条路径的加速比、并行效率、总时间与 RSS/VmSize/显存对照，附逐组误差表、配置边界与性能原始数据表 | 已成稿；文内未署作者，作者宋维豪，2026-09-28 经用户确认。按用户 2026-09-29 要求，页面只记来源、核心内容与结论摘要和原件指针，不录原文与图片。所引完整技术报告与周期 04 [tensorfem-multi-backend-validation.md](materials/biweekly-04/tensorfem-multi-backend-validation.md) 的关系、本件与第五次双周会宋维豪汇报项的关系**均待确认** |
| 05 | [mesh-partitioning-scheme-and-tests.md](materials/biweekly-05/mesh-partitioning-scheme-and-tests.md) | `网格分区方案与测试.html` | 陈康（算海）2026-09-28 发于飞书算海内部群，25 页网页幻灯片「问题依赖网格分区｜阶段汇报」（封面标 PHASE 02）：可行域优先的统一数学模型（归属、must-link/cannot-link、原图逐 Part 连通、可加负载与资源容量、分摊 DOF 与局部唯一 DOF 两种口径、接口重复系数、材料混合与 FEM 软依赖代价、`ObjectivePolicy`），METIS 候选＋约束保持搜索＋原模型独立验收的程序流程与三态判定，三个经典模型的单元数均衡基线与 MPC 尖峰成因，MPC 算例 9 个 k × 3 种优先策略的等预算对照与加硬上限后的推荐配置（局部 DOF 优先 · 51 Part · 1.15），以及另 15 个 BDF 中 9 个可运行模型的双线测试（18 份可行结果）与 6 个输入能力阻断 | 已成稿；文内未署作者，作者陈康，2026-09-29 经用户确认。按用户 2026-09-29 要求，页面只记来源、核心内容与结论摘要和原件指针，不录原文与图片。与同周期 [problem-dependent-mesh-partitioning.md](materials/biweekly-05/problem-dependent-mesh-partitioning.md)（09-17 汇报稿）的承接关系、与第五次双周会陈康汇报项的关系**均待确认** |
| 05 | [sgsim-petsc-bddc-integration-design.md](materials/biweekly-05/sgsim-petsc-bddc-integration-design.md) | `SGSim PETSc BDDC 集成设计.html` | 胡凯（算海）2026-09-29 发于飞书算海内部群，11 页网页幻灯片「SGSim PETSc BDDC 集成设计」：SGSim 现有求解流程与 BDDC 所需数据（全局 $A$、$b$，子域矩阵 $K_i$ 与局部—全局映射 $R_i$、MPI 布局与界面信息），子域矩阵的两种构造方案（从全局矩阵提取并分配共享条目 / 从单元贡献装配并做一致的约束变换）及比较，有限元与代数模块的职责划分（Amat 为全局 $A$、Pmat 为子域 MATIS、KSP/PCBDDC），以及因 FEMSolve 现有接口与源码权限所限先在 Main 增加 BDDC 分支、后续合入 FEMSolve 的接入策略与修改范围 | 对方自标「技术方案讨论稿」，故记 `draft`；文内未署作者，作者胡凯，2026-09-29 经用户确认。按用户 2026-09-29 要求，页面只记来源、核心内容与结论摘要和原件指针，不录原文；原件不入 Git，指针仅本机有效。与周期 03 [preconditioner-integration-design-and-test.md](materials/biweekly-03/preconditioner-integration-design-and-test.md)（接入架构一节自标「待修改」）是否为其修订版、与第五次双周会胡凯汇报项的关系**均待确认** |

## 本人任务清单

本人任务材料按**所属汇报周期**归入 `my-tasks/biweekly-NN/`，收的是本人在该周期承担任务时自己产出的东西，与 `materials/` 收到的他人材料分开。

| 周期 | 文件 | 承担范围 | 状态 |
| --- | --- | --- | --- |
| 04 | [rd-plan-2026-2027-heterogeneous.md](my-tasks/biweekly-04/rd-plan-2026-2027-heterogeneous.md) | 研究院《HPC 研发规划（2026-2027）》1.3.2、1.5、2.2 三节，田昊伦（算海）提供初稿、本人修改完善后写入飞书源文档 | **已提交**——三节已写完并落入源文档，评审尚未进行；源文档仍为评审稿，1.5 前两行时间列仍为「待定」 |

## 内容边界

- 报告以有依据的项目事实为基础，不把团队整体工作直接记作本人完成的工作。
- 协同类事项记录本人实际完成的沟通、跟进、复现和协调，**须在措辞上点明是对齐或跟进团队进展**，不把算海团队整体产出表述为本人完成。
- 个人类事项记录本人直接开展的技术与项目推进工作，不指私人项目。
- 日报中的问题、需协助事项、下一步和相关事实源指针归入对应事项，不另设全局栏目；当天确无可报内容时如实写「无」。
- 不写算海内部执行策略（具体技术选型、参数、算例规格），只报状态与结论；依据留在对应任务线的事实源文件中。
- 算海内部执行任务、产出及问题解决过程以 `suanhaitech/houzai` 为事实源；本目录只记录本人工作和必要指针，不复制内部执行策略。
- 账号、密码、Token、VPN 密钥、内部访问地址，以及未经授权公开再分发的源码、程序包、模型和内部附件不得写入。
- **`materials/` 随本 Public 仓库公开**，放入前须逐份确认材料本身可公开；边界不清时先向用户确认归属，不先写了再说。已有材料的公开依据记在上表「来源与内容」列。

## 日报写法

- 每个双周汇报周期使用一个 Markdown 文档，命名为 `daily/biweekly-NN-YYYY-MM-DD-to-YYYY-MM-DD.md`，其中 `NN` 为两位汇报次数，日期为周期起止日期；不另建周期目录或周期 README。
- 文档内以日期作为一级标题（如 `# 2026-09-07 日报`），按日期顺序记录当天工作事项；不另设总标题，仅在有实际记录时添加日期，不预先铺设空标题。
- **每个事项默认一句话**：写清做了什么、到什么状态即可，细节不复述，靠指针指向对应任务线的事实源。当天确有需要对方知道的问题、需协助事项或明确的下一步时，才在该事项下补一行说明；没有就不写。
- 2026-08-24 之前的日报采用「工作内容 / 当前状态 / 下一步」的分行写法，周期 1 至 3 的文档保留其原始措辞不改写；一句话写法自周期 3 中段起生效。
- 正文刻意不使用 `**加粗**` 与二级标题：微信不渲染 Markdown，符号会原样显示。发送时复制该日整段、去掉 `# ` 前缀即可直接粘贴。
- 没有新增结果时，如实记录当前状态、阻塞条件和下一步，不为填满模板而人为制造内容。
- 涉及研究院的问题在大工项目群中沟通；日报只记录问题与所需协助，不保存逐字聊天。

## 材料写法

- 一份材料一个文件，放进收到日期所属周期的 `materials/biweekly-NN/` 下；文件按仓库惯例改用英文 kebab-case 命名，原文件名登记在上表。
- 自 2026-09-29 起，页面只写三节：**来源**（作者、日期、渠道、原件形式与大小，与上表登记一致；来源不明的标「待确认」，不推断作者与日期）、**核心内容与结论**（忠实摘要，不补全、不加原件没有的判断）、**原始文件**（原件指针）。不录原文与图片，不设 `media/` 目录。
- 原件存本机同目录时用相对链接指向，本机 HTML 原件由 `.gitignore` 排除、不入 Git；原件在 GitLab 或飞书时只写仓库名或文档名与文件名，不写仓库内目录与内部地址。
- 周期 01–04 按原样归档的材料保持原状；需要改写时逐份经用户确认。
- 材料本身不是事实源。它引出的结论写进对应任务线的 `log.md`，交付物状态写进 `artifacts.md`，本目录不重复维护。

## 本人任务写法

- 一份材料一个文件，放进所属汇报周期的 `my-tasks/biweekly-NN/` 下，文件用英文 kebab-case 命名，登记在上面的本人任务清单表。
- 收**本人产出的稿件本体与其状态记录**：提交研究院的规划章节、方案稿等。撰写中与已提交的都放这里，不必等定稿。
- **本目录不是事实源**：任务安排以对应任务线的 `plan.md` 为准，进度写入 `log.md`，交付物与归档状态写入 `artifacts.md`；本目录页面只记承担范围、撰写来源与成稿状态，并指回上述事实源。
- 正文已在别处成立时**不在本仓留副本**：源文档在飞书或 GitLab 的，页面只放指针；初稿是他人材料的，指向 `materials/` 对应周期的归档件。
- **随本 Public 仓库公开**，放入前逐份确认可公开。涉及 SGSim 内部模块边界、内部接口约定、方向间分工与数据存放位置的正文不照录，查正文回源文档。

## 相关链接

- HPC 任务线事实源：[../hpc/README.md](../hpc/README.md)
- 结构动力学验收任务线事实源：[../structural-dynamics/README.md](../structural-dynamics/README.md)
- 汇报节奏与检查点的来源讨论：[../meetings/2026-07-27-liningning-discussion.md](../meetings/2026-07-27-liningning-discussion.md)
