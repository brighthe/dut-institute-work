# staging

存放**内容已定、但归属位置或整理形式尚未确定**的项目文档，作为进入正式目录前的暂存区。

与 [drafts/](../drafts/README.md) 的区别：`drafts/` 放**尚未定稿、待多方确认**的讨论稿，引用时须注明待确认状态；`staging/` 放**已成稿并已在项目内使用**、只是还没决定归入哪个任务线或以什么形式整理的材料。

## 规则

- 本目录纳入 Git 跟踪，随本 Public 仓库公开；放入前须确认材料本身可公开，凭据、内部路径与未经授权再分发的源码、程序包、模型一律不得进入。
- 文件按仓库惯例改用英文 kebab-case 命名，原文件名登记在下表，正文保持原样归档，不改写、不摘编。
- 每份材料在文首用引用块标注来源（作者、日期）与归档方式，与下表登记一致。
- 非 Markdown 原件（如 `.docx`）转为 Markdown 归档，只做格式转换、不改动文字；随文图片存入 `media/<同名目录>/`，按图号命名。
- 材料归位后迁往正式目录（本仓 `hpc/`、`structural-dynamics/` 等或相邻仓库），并在对应任务线的 log 记录去向；本表同步删除该行。

## 当前清单

| 文件 | 原文件名 | 来源与用途 | 状态 |
| --- | --- | --- | --- |
| [cpardiso-mumps-solver-comparison.md](cpardiso-mumps-solver-comparison.md) | `CPardiso与MUMPS解法器性能比较.md` | 胡凯（算海）2026-08-31 产出，直接解法器对比的精简主文档；2026-08-29 讨论确定的 8-31 节点材料，供 9-4 阶段进展沟通使用 | 已成稿，待确定归入 `hpc/` 的形式 |
| [cpardiso-mumps-full-data.md](cpardiso-mumps-full-data.md) | `完整数据对比.md` | 胡凯（算海）2026-08-31 产出，与上表主文档配套的完整支撑材料：算例规模、非求解阶段、求解时间、资源与残差、rank 级负载、BLAS 后端对照 | 已成稿，待确定归入 `hpc/` 的形式 |
| [preconditioner-integration-design-and-test.md](preconditioner-integration-design-and-test.md) | `预条件器接入设计与测试.md` | 胡凯（算海）2026-08-31 产出，BDDC 预条件器接入 SGSim/SGFem/Algebra 的架构设计、数学定义与 14 个算例的矩阵性质、MUMPS 直接法基线、BDDC 迭代法验证结果；供 9-4 阶段进展沟通使用 | 已成稿，接入架构一节自标「待修改」；待确定归入 `hpc/` 的形式 |
| [perf-test-analysis.md](perf-test-analysis.md) | `性能测试结果分析.docx` | 杜阳（研究院）2026-09-02 产出，`YUANTONG_10w`/`YUANTONG_185w` 两个模型在 1/2/4 进程下的 perf 函数调用、CPU 利用率与内存带宽分析 | 已成稿，经确认可公开；原 docx 转为 Markdown，6 张图存于 [media/perf-test-analysis/](media/perf-test-analysis/)；待确定归入 `hpc/` 的形式 |
