---
title: SGSim BDDC 求解接入进展
type: material
tags:
  - reports
  - hpc
status: final
cycle: 2026-09-19..2026-10-09
origin: SGSim_BDDC进展汇报.html
author: 胡凯（算海）
origin_date: 2026-10-08
date_added: 2026-09-29
date_update: 2026-10-09
---

# SGSim BDDC 求解接入进展

## 原始文件

- 原件：[SGSim_BDDC进展汇报.html](SGSim_BDDC进展汇报.html)

## 核心内容与结论

1. **接入定位与链路**（第 1–3 页）
   - 沿用 SGSim 原有有限元处理流程（SGFem），补充子域矩阵与局部到全局编号映射，为代数库（Algebra）提供 PETSc BDDC 所需数据。
   - 流程分工：SGFem 负责单元计算、保存区域刚度贡献、约束处理（SPC/MPC/AutoSPC）、载荷生成与最终解恢复/应力反力计算；Algebra 接收全局方程 $A, b$ 与子域数据 $K_i, R_i$，构造 MATIS 并设置 BDDC，完成 Krylov 迭代后返回独立方程解。
   - 接入方式：当前在 Main 增加临时分支衔接流程并已验证通过；正式接入位置预定在 FEMSolve。原有直接法等求解方式完整保留。
2. **子域数据构造机制**（第 4 页）
   - 采用单元贡献累加与约束变换方案：保存当前区域单元刚度累加结果 $\hat{K}_i$，利用 SGFem 原有约束处理形成的局部展开映射 $T_i$，构造子域刚度矩阵 $K_i = T_i^T \hat{K}_i T_i$。
   - 传入 Algebra 的数据包括约束处理后的子域刚度矩阵 $K_i$、局部行列到最终全局方程的编号映射 $R_i$（满足 $A = \sum_i R_i^T K_i R_i$）及界面坐标/分量元数据。Algebra 不直接读取物理数据库和原始约束。
3. **求解流程与多层组织**（第 5 页）
   - 算子接口配置：$A_{\text{mat}} = \text{全局 } A$（用于 Krylov 迭代计算算子作用与右端 $b$），$P_{\text{mat}} = \text{MATIS}$（利用 $K_i, R_i$ 构造 BDDC 预条件作用）。
   - 当前区域组织：1 个 MPI rank 对应 1 个物理子域，直接复用已有一级分区，不传 TSV 文件。
   - 局部分解与粗层：局部分解通过 PETSc 标准 LU 接口调用 MUMPS；粗问题通过多层 BDDC（`-pc_bddc_levels 1`）继续递归求解。
4. **参数取舍与分解后端**（第 6–7 页）
   - Krylov 方法选择：CG 要求矩阵与预条件严格对称正定；GMRES 用于固定线性预条件；FGMRES 支持变化/非线性预条件（本轮主测 CG 与 GMRES）。
   - BDDC 关键参数：控制角点（corner）、边约束（edges）、面约束（faces）进入粗空间；控制 Deluxe 界面缩放与 Schur 局部层数。增加粗空间约束会扩大粗问题成本，需结合算例平衡。
   - 分解后端：BDDC 局部与粗层分解本轮全部采用 MUMPS（标准 PCLU）；CPARDISO 本轮仅作为全局方程直接法对照，未测试作为 BDDC 分解后端。
5. **验证算例配置**（第 8 页）
   - 涵盖 4 组经典模型，均使用原始分区数据库，单线程（OMP/MKL/OpenBLAS=1），levels=1，MUMPS PCLU，收敛目标 $r_{\text{tol}} = 10^{-8}$：
     1. **10w-3d**（16 MPI）：CG，角点，无边/面约束，无 Deluxe。
     2. **100w-mix23d**（24 MPI）：CG，角点 + 边平均 + Deluxe，无面约束。
     3. **100w-mix23d-rbe**（24 MPI）：CG，角点 + 边平均 + Deluxe，无面约束。
     4. **17w 壳**（`Pshell_17w_cquad4_Pload2`，24 MPI）：GMRES（restart=100，右预条件），角点 + 边平均 + Deluxe。
6. **多算例时间、内存与残差表现**（第 9 页，2026-10-08 实测）
   - 4 组算例全部退出 0，KSP 均报告 `CONVERGED_RTOL`：

   | 算例 | 方法 / MPI | 全流程墙钟 / s | 总 RSS 峰值 / GiB | 迭代步数 | 真实相对残差 $\|b - Ax\| / \|b\|$ |
   | --- | --- | --- | --- | --- | --- |
   | **10w-3d** | CG / 16 | 11.74 | 8.79 | 54 步 | $7.62 \times 10^{-9}$ |
   | **100w-mix23d** | CG / 24 | 215.20 | 45.74 | 110 步 | $9.14 \times 10^{-9}$ |
   | **100w-mix23d-rbe** | CG / 24 | 218.79 | 44.09 | 109 步 | $9.90 \times 10^{-9}$ |
   | **17w 壳** | GMRES / 24 | 75.77 | 26.11 | 99 步 | $6.15 \times 10^{-6}$ (注) |

   - *注*：壳算例 KSP 递推残差范数达到 $5.04 \times 10^{-11}$ 且触发收敛，但真实相对残差未达到 $10^{-8}$ 目标，按已接受精度记录。
   - 关键事件最大耗时（PCSetUp / KSPSolve）：10w-3d 为 1.32s / 3.15s；100w-mix 为 80.1s / 108.6s；100w-mix-rbe 为 82.4s / 109.7s；17w 壳为 9.12s / 56.7s。
7. **与 CPARDISO 直接法对照**（第 10 页）
   - 相同算例与 MPI 规模下对照（CPARDISO 均正常退出 0 且成功导出）：
     - **全流程耗时**：10w-3d（BDDC 11.74s vs CPARDISO 10.64s，基本持平）；100w-mix（BDDC 215.20s vs CPARDISO 101.93s）；100w-mix-rbe（BDDC 218.79s vs CPARDISO 98.17s）；17w 壳（BDDC 75.77s vs CPARDISO 26.44s）。当前直接法在全流程用时上仍明显优于 BDDC。
     - **总 RSS 峰值内存**：BDDC 在全部算例中均展现出更低的内存占用。10w-3d（8.79 vs 9.88 GiB）；100w-mix（**45.74 vs 57.29 GiB**，节省约 11.5 GiB）；100w-mix-rbe（**44.09 vs 60.30 GiB**，节省约 16.2 GiB）；17w 壳（26.11 vs 30.70 GiB）。
     - *说明*：两组测试依赖库环境并非完全一致，直接法未独立复核真实相对残差，本对照主要作为运行成本参考。
8. **后续优化与正式接入计划**（第 11–12 页）
   - **rank 内物理子域细分**：当前每个 rank 承载一个物理子域，局部分解规模受限于该分区自由度；后续评估 1 个 rank 承载多个物理子域，减小单个局部分解规模，权衡粗空间增加的成本。
   - **正式合并**：将子域数据准备与求解流程从 Main 临时入口正式接入 FEMSolve 内部，完成后撤除临时分支。
