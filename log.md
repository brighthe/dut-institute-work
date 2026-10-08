---
title: "知识库维护日志"
type: log
status: living
date_added: 2026-09-10
date_update: 2026-09-29
---

# 知识库维护日志

> **范围**：只记录知识库**自身**的维护动作——结构调整、规范变更、批量重组、目录新增或归档、Lint 结果。
> **项目进度不进这里**，归各任务线的 `log.md`（[hpc/log.md](hpc/log.md)、[structural-dynamics/log.md](structural-dynamics/log.md)）。
>
> Append-only：新条目加在最上面，格式 `## [YYYY-MM-DD] <动作> | <简述>`；只增不改历史条目。
> 动作取 `add`、`refactor`、`revise`、`archive`、`lint`。

## [2026-09-29] refactor | 周期材料写法改为摘要加原件指针，.gitignore 排除材料 HTML 原件

按用户要求，把周期 05 四份材料页的写法升为规范，同日下面几条 revise 记录的「与 §6.7 不一致」随之消除。

- **材料写法**：[schema/page-schemas.md](schema/page-schemas.md) §6.7 由「正文原样归档」改为三节：来源、核心内容与结论、原始文件。不录原文与图片，不设 `media/`。原件指针的写法是：本机同目录的原件用相对链接；GitLab 或飞书的原件只写仓库名或文档名与文件名。周期 01–04 已原样归档的材料保持原状，改写需逐份确认。
- **同步措辞**：同一规范的目录表与 `material` 类型说明、[reports/README.md](reports/README.md) 的定位段、目录表与「材料写法」、[schema/llm-wiki-methodology.md](schema/llm-wiki-methodology.md) 分层表、[index.md](index.md) 与 [README.md](README.md) 的目录说明，统一去掉「材料原件」的说法。
- **存储边界**：`.gitignore` 新增 `reports/materials/**/*.html`，§7 的忽略清单同步补上这一条。本机原件留在材料页同目录，但不入 Git。

## [2026-09-29] revise | 面向并行有限元的问题依赖网格分区材料页改为摘要加原件指针

按用户要求，[reports/materials/biweekly-05/problem-dependent-mesh-partitioning.md](reports/materials/biweekly-05/problem-dependent-mesh-partitioning.md) 由原文加 7 张插图改为三节：来源、核心内容与结论摘要、原始文件，写法与同日另三份材料页一致。原件不在本机，因此原件指针只写「算海 GitLab `meshx` 仓库 `work_report.md`（`develop` 分支）」，不写仓库内目录；[reports/README.md](reports/README.md) 材料清单表该行已同步删去内部目录路径，并改写说明栏。原文中的脚本路径与复现命令随正文一并移除。

`reports/materials/biweekly-05/media/problem-dependent-mesh-partitioning/`（7 张图，从未提交）经用户同意删除；删除后 `media/` 已空，整个目录一并删除。这些插图只能从 GitLab 源端 `report_assets/` 重新获取。

**与规范不一致**：周期 05 已有 4 份材料页不符合 [schema/page-schemas.md](schema/page-schemas.md) §6.7「正文原样归档，不摘编」的要求。是否修改规范尚未决定。

## [2026-09-29] revise | SGSim × TensorFem 异构计算进展材料页改为摘要加原件指针

按用户要求，把 [reports/materials/biweekly-05/sgsim-tensorfem-heterogeneous-progress.md](reports/materials/biweekly-05/sgsim-tensorfem-heterogeneous-progress.md) 从 12 页原文转写（含三个弹窗附表与 16 张图）改为来源、核心内容与结论摘要、原始文件三节，写法与同日另两份材料页一致。连带修改 [reports/README.md](reports/README.md) 材料清单表该行的说明栏。页面不再引用的 `reports/materials/biweekly-05/media/sgsim-tensorfem-progress/`（16 个文件，未曾提交）一并删除，可从同目录 HTML 原件重新提取。

## [2026-09-29] revise | 问题依赖网格分区阶段汇报材料页改为摘要加原件指针

按用户要求，把 [reports/materials/biweekly-05/mesh-partitioning-scheme-and-tests.md](reports/materials/biweekly-05/mesh-partitioning-scheme-and-tests.md) 从 25 页原文转写（含 23 张图）改为来源、核心内容与结论摘要、原始文件三节，写法与同日 BDDC 材料页一致。连带修改 [reports/README.md](reports/README.md) 材料清单表该行的说明栏。

页面已不再引用的 `reports/materials/biweekly-05/media/mesh-partitioning-scheme-and-tests/`（23 个文件，未曾提交）经用户同意删除；同级 `media/` 下其他材料的图片目录仍在使用，保留。

## [2026-09-29] revise | SGSim PETSc BDDC 集成设计材料页改为摘要加原件指针

按用户要求，把 [reports/materials/biweekly-05/sgsim-petsc-bddc-integration-design.md](reports/materials/biweekly-05/sgsim-petsc-bddc-integration-design.md) 从 11 页原文转写改为三节：来源、核心内容与结论（摘要，注明非原文）、原始文件（同目录 HTML，原件不入 Git，指针仅本机有效）。连带修改 [reports/README.md](reports/README.md) 材料清单表该行的说明栏。

**与规范不一致**：本页不再符合 [schema/page-schemas.md](schema/page-schemas.md) §6.7「正文原样归档，不摘编」。规范是否随之修改、其他材料页是否照此处理，均未决定。

## [2026-09-29] archive | 归档陈康 2026-09-28 发出的问题依赖网格分区阶段汇报幻灯片

陈康（算海）2026-09-28 在飞书算海内部群发出 25 页单文件网页幻灯片《网格分区方案与测试.html》（36 MB），按 [reports/README.md](reports/README.md) 材料写法归入周期 05 → [reports/materials/biweekly-05/mesh-partitioning-scheme-and-tests.md](reports/materials/biweekly-05/mesh-partitioning-scheme-and-tests.md)。文内未署作者，作者、收到日期与公开边界均经用户确认。

转换方式：每页按原页码成节、页脚照录，文字未改；卡片与指标块转为段落、列表或表格，HTML 上下标转写为 LaTeX。原件约 17 MB 是内嵌中文字体，属版式资源未入仓；15 张 SVG 公式图与 9 张 PNG 提取至 `reports/materials/biweekly-05/media/mesh-partitioning-scheme-and-tests/`，第 5、6 页各一张公式图逐字节相同只存一份，共 23 个文件约 9.8 MB，其中第 20、21 页 6 张扩散视图各约 1.6 MB，经用户确认按原图入仓。

连带更新 [reports/README.md](reports/README.md)：材料清单表增一行，周期表周期 05 收到材料计 7 份。

**尚未回填**：本件结论未写入 [hpc/log.md](hpc/log.md)；与同周期 [problem-dependent-mesh-partitioning.md](reports/materials/biweekly-05/problem-dependent-mesh-partitioning.md)（09-17 汇报稿）的承接关系、与第五次双周会陈康汇报项的关系均待确认。

## [2026-09-29] archive | 归档胡凯 2026-09-29 发出的 SGSim PETSc BDDC 集成设计幻灯片

胡凯（算海）当日在飞书算海内部群发出 11 页单文件网页幻灯片《SGSim PETSc BDDC 集成设计.html》（36 KB），按 [reports/README.md](reports/README.md) 材料写法归入周期 05 → [reports/materials/biweekly-05/sgsim-petsc-bddc-integration-design.md](reports/materials/biweekly-05/sgsim-petsc-bddc-integration-design.md)。文内未署作者，作者与公开边界均经用户确认；原件页脚自标「技术方案讨论稿」，`status` 记 `draft`。

转换方式：每页按原页码成节、页脚照录，文字未改；流程框图按从左到右顺序转为表格，HTML 上下标公式转写为 LaTeX。原件无图片，本件不设 `media/` 目录。

连带更新 [reports/README.md](reports/README.md)：材料清单表增一行，周期表周期 05 收到材料计 6 份。

**尚未回填**：本件结论未写入 [hpc/log.md](hpc/log.md)；本件是否为周期 03 [preconditioner-integration-design-and-test.md](reports/materials/biweekly-03/preconditioner-integration-design-and-test.md) 接入架构一节的修订版、与第五次双周会胡凯汇报项的关系均待确认。

## [2026-09-28] archive | 归档宋维豪 2026-09-28 发出的 SGSim × TensorFem 进展幻灯片

宋维豪（算海）当日在飞书算海内部群发出 12 页单文件网页幻灯片《SGSim_TensorFem进展汇报.html》（3.2 MB），按 [reports/README.md](reports/README.md) 材料写法归入周期 05 → [reports/materials/biweekly-05/sgsim-tensorfem-heterogeneous-progress.md](reports/materials/biweekly-05/sgsim-tensorfem-heterogeneous-progress.md)。文内未署作者，作者与公开边界均经用户确认。

转换方式：每页按原页码成节、页脚照录，三个弹窗（逐组误差、配置边界、性能原始数据）附于文末，文字未改。原件内嵌 14 张 base64 图，其中第 7 页三张位移云图逐字节相同只存一份；另从第 3、5、6 页内联 SVG 提取 4 张示意图，共 16 个文件存于 `reports/materials/biweekly-05/media/sgsim-tensorfem-progress/`。指向作者本机归档目录（`comparison.json` / `model.h5`）与「完整技术报告」的链接本仓无对应文件，改为纯文字，在文首来源块说明。

连带更新 [reports/README.md](reports/README.md)：材料清单表增一行，周期表周期 05 收到材料计 5 份。

**尚未回填**：本件结论未写入 [hpc/log.md](hpc/log.md)；所引完整技术报告与周期 04 [tensorfem-multi-backend-validation.md](reports/materials/biweekly-04/tensorfem-multi-backend-validation.md) 的关系、本件与第五次双周会宋维豪汇报项的关系均待确认。

## [2026-09-22] revise | 两份网页材料的作者与状态经确认落实

同日归档的两份算海网页材料（见下一条）此前按「文内未署作者」记 `author: 待确认`、按对方明示待组内核对记 `status: draft`。经用户确认：两份均为田昊伦（算海）制作，内容当日已与算海讨论确认。两份 frontmatter 相应改为 `author: 田昊伦`、`status: final`，文首来源引用块与 [reports/README.md](reports/README.md) 材料清单表同步；周期表汇报日由「据算海材料，待对外确认」改记「10-09（预定，对外通知未发）」。

## [2026-09-22] archive | 归档算海 2026-09-22 发出的两份对外网页材料

田昊伦（算海）当日在飞书算海内部群发出两份 HTML 网页材料，标注为「后续要发在外部群里的网页材料」并请组内确认。按 [reports/README.md](reports/README.md) 材料写法归入周期 05（2026-09-19 至 2026-10-02），单页 HTML 转 Markdown，只做格式转换、文字未改；两份原件各内嵌两张 base64 页眉 logo，属版面标识、不含正文信息，未提取入仓，故本周期 `media/` 下无对应目录。

- 《第五次双周会议预计产出.html》→ [reports/materials/biweekly-05/suanhai-fifth-biweekly-expected-outputs.md](reports/materials/biweekly-05/suanhai-fifth-biweekly-expected-outputs.md)。会议时间 2026.10.9 与三项预期产出及汇报人（陈康／胡凯／宋维豪）。

- 《2027_hpc研发计划.html》→ [reports/materials/biweekly-05/hpc-rd-plan-2027-annual.md](reports/materials/biweekly-05/hpc-rd-plan-2027-annual.md)。2027 年度异构并行研发的五个季度节点、里程碑与两条边界原则。

两份文内均未署作者，只能确认发出者，`author` 记「待确认」；对方明示待组内核对，`status` 记 `draft`。正文含三人实名分工、SGSim 模块名与 CPU/MPI、CUDA 路线表述，无凭据、内部地址、源码与算例数据，经用户确认后入仓。

连带更新 [reports/README.md](reports/README.md)：材料清单表增两行，周期表周期 05 收到材料计 4 份；汇报日由「待确认」改记 10-09，依据即第一份材料，并注明该材料尚待算海确认、以对外通知为准。

两处**尚未回填**，留待确认：第二份与周期 04 [rd-plan-heterogeneous-sections-final.md](reports/materials/biweekly-04/rd-plan-heterogeneous-sections-final.md) 1.5 节属同一主题（该落稿版 1.5 无季度表），两者关系及本件能否作为 1.5 待定时间列的落定依据均无依据可判，[reports/my-tasks/biweekly-04/rd-plan-2026-2027-heterogeneous.md](reports/my-tasks/biweekly-04/rd-plan-2026-2027-heterogeneous.md) 未动；两份材料引出的结论亦未写入 [hpc/log.md](hpc/log.md)。

## [2026-09-16] refactor | 删除 staging/，reports/ 下新建 my-tasks/ 按周期收本人任务材料

`staging/` 整体取消，本仓不再保留任何暂存区。原先压在它身上的两类用途分别归位：

- **本人承担撰写的对外交付稿**（2026-09-14 扩出来的第二类）进 [reports/my-tasks/](reports/my-tasks/)，按**所属汇报周期**分 `biweekly-NN/` 子目录，与按**收到日期**编排的 `reports/materials/` 并列——一边收本人产出的，一边收他人发出的。
- **收到日期或归属未定的外部材料**（原第一类）不再中转，直接归入最接近周期的 `reports/materials/biweekly-NN/`，在材料清单表状态栏标「待确认」。

迁移：`staging/rd-plan-2026-2027-heterogeneous.md` → [reports/my-tasks/biweekly-04/rd-plan-2026-2027-heterogeneous.md](reports/my-tasks/biweekly-04/rd-plan-2026-2027-heterogeneous.md)，frontmatter 由 `type: plan` + `status: staged` 改为 `type: my-task` + `status: submitted` 并补 `cycle`；`staging/README.md` 删除。

**规范变更**：[schema/page-schemas.md](schema/page-schemas.md) 的 `type` 取值以 `my-task` 取代 `staged`，两者的区别由「归属是否已定」改为「谁产出」；新增 `in-progress | submitted | done` 状态取值与 `task_line` / `cycle` 字段；§6.5 由「staging 暂存」改写为「`reports/my-tasks` 本人任务材料」，§6.7 补入「收到日期未定按最接近周期归入并标待确认」；§6.6 与 §6.7 两块调回数字顺序，编号未变。[schema/llm-wiki-methodology.md](schema/llm-wiki-methodology.md) 的派生层三档、Ingest 产物与 Lint 检查项同步。

这同时补上了 2026-09-14 条目自记的两处规范缺口：`type` 没有「我方承担撰写的对外文档」这一类，以及 §6.5 的归档纪律只按外部来源材料写。

**入链同步**：[README.md](README.md)（内容去向表新增本人交付稿一行、目录树、README 指针列表、Ingest 与 Lint 表述）、[index.md](index.md)（删去「材料暂存」行）、[reports/README.md](reports/README.md)（定位段、目录结构表、周期表新增「本人任务材料」列、新增本人任务清单表与本人任务写法、相关链接）、[hpc/README.md](hpc/README.md) 与 [hpc/plan.md](hpc/plan.md) 的指针。顺带修好 `reports/README.md` 材料清单里 `rd-plan-heterogeneous-sections-draft.md` 缺 `materials/biweekly-04/` 前缀的断链。

本文件与 [hpc/log.md](hpc/log.md) 的历史条目按 append-only 不回改，其中指向 `staging/` 的链接自此失效，去向以本条为准。

## [2026-09-14] revise | staging/ 定位扩到「本人在写、未定稿的对外交付稿」

原定位是「**内容已定**、但归属位置或整理形式尚未确定」，只覆盖外部来源的成稿。本次新增 [staging/rd-plan-2026-2027-heterogeneous.md](staging/rd-plan-2026-2027-heterogeneous.md)（《HPC 研发规划（2026-2027）》中本人承担撰写的三节）时暴露出缺口：它**内容未定而归属明确**，与原定位正好相反，既不属 `reports/materials/`（那里归的是收到的材料，按收到日期编排），也不该进任务线目录。

该稿一度建在 `hpc/` 下，由用户指出后迁入本目录。**判据**：任务线目录只放已经成立的内容——安排事实源、进度、交付物状态与已验证的手册；内容归属在某条任务线，不等于该放进那条任务线的目录。此前把这两件事当成了一回事。

[staging/README.md](staging/README.md) 定位段改为两类并列，新增第二类；「文首引用块」一条补上本人撰写稿的标注方式（无外部作者，标承担范围与当前状态，清单表「原文件名」填 `—`）；当前清单登记一行。定稿并回填飞书源文档后，交付事实写入 [hpc/log.md](hpc/log.md) 与 [hpc/artifacts.md](hpc/artifacts.md)，清单删行。

**未改**：[schema/page-schemas.md](schema/page-schemas.md) §6.5 的 staging 归档纪律仍是按外部来源材料写的（原文件名、格式转换、随文图片），本轮未动；§3 的 `type` 取值也仍没有「我方承担撰写、尚未提交的对外文档」这一类，该稿暂用 `plan` + `status: draft`，与 §3 状态表不符。这两处规范缺口待定。

## [2026-09-14] refactor | reports/ 按双周汇报周期重组，staging/ 七份材料迁入

用户确认第一次双周汇报为 2026-07-31、第四次为 2026-09-18，四次汇报（07-31、08-14、09-04、09-18）均为周五，**周期以汇报日收尾**。据此重排周期边界并完成 [reports/README.md](reports/README.md) 一直挂着的「待周期起止确认后再迁移」。

**周期边界**。周期 1 = 07-20 至 07-31、2 = 08-03 至 08-14、3 = 08-17 至 09-04、4 = 09-07 至 09-18。周期 1 起始日按双周制自 07-31 倒推，周期 3 因第二、三次汇报间隔三周而为三周，两处均由用户拍板，不是推断。此前会话中按「日报连续段」切出的 07-27~08-07 / 08-10~08-21 / 08-24~09-04 是错的，**该推断在当时也不成立**——它把周六日报 2026-08-08 排在所有周期之外，本身已是反证，只是未被追究。新边界下 08-08 落在周期 2 内。

**日报合并**。`reports/daily/` 下 22 篇单日日报合入三份周期文档：[biweekly-01](reports/daily/biweekly-01-2026-07-20-to-2026-07-31.md)（5 篇）、[biweekly-02](reports/daily/biweekly-02-2026-08-03-to-2026-08-14.md)（7 篇）、[biweekly-03](reports/daily/biweekly-03-2026-08-17-to-2026-09-04.md)（10 篇）。**正文一字未改**：仅把首行统一成 `# YYYY-MM-DD 日报`（原先 07-27 至 07-31 带 `# ` 前缀、其余不带），去首行后逐字节比对 22 天全部一致。2026-08-24 之前的分行写法（工作内容／当前状态／下一步）按 append-only 原样保留，未改写成一句话记法；该写法分叉的待确认项仍挂在下方 2026-09-10 条。顺带去掉 `2026-08-31.md` 的 UTF-8 BOM，它是全库唯一一处。

**材料迁入**。`staging/` 七份算海材料按**收到日期**迁入 `reports/materials/biweekly-NN/`：周期 3 收 `cpardiso-mumps-solver-comparison`、`cpardiso-mumps-full-data`、`preconditioner-integration-design-and-test`（均 08-31）与 `mpi-solver-performance-comparison`（09-04）；周期 4 收 `environment-checklist`（09-09）、`reissner-mindlin-shell-linear-static`、`bdf-to-db-partition-dataflow`（均 09-12）。正文未动，frontmatter 改 `type: material`、`tags` 的 `staging` 换 `reports`、`status` 由 `staged` 改 `final`（`environment-checklist` 保留 `draft`）、补 `cycle`。

**归入周期不等于归属已定**。材料按收到日期编排，与内容归属无关；`reissner-mindlin-shell-linear-static` 的 `dut-postdoc` 归位候选与其用途待确认两项仍然有效，只是登记位置从 `staging/README.md` 移到 `reports/README.md` 的材料清单表。

**reports/ 定位随之改写**。原表述「报告是派生层……本目录只做对外汇报的组织与措辞」放不下材料原件，改为日报与汇报仍是派生层、`materials/` 是本目录内唯一的非派生内容，并明确材料不承担事实源角色。新增「材料写法」一节。

**规范扩充**。[schema/page-schemas.md](schema/page-schemas.md) 新增 `material` 类型（与 `staged` 只差归属是否已定）、其 `final | draft` 状态取值与 `cycle` 字段、§6.7 周期材料结构、`reports/materials/biweekly-NN/` 的命名与「不单独设索引」；删去「2026-09-07 之前的历史单日日报不加 frontmatter」这条例外——文件已不存在。[schema/llm-wiki-methodology.md](schema/llm-wiki-methodology.md) 的派生层定义、Ingest 产物与 Lint 检查项同步。

**失效链接未回改**。[hpc/log.md](hpc/log.md) 2026-09-10 条指向 `staging/environment-checklist.md`，本文件 2026-09-13 条指向 `staging/reissner-mindlin-shell-linear-static.md` 与 `staging/bdf-to-db-partition-dataflow.md`，三处均为历史条目，按 `llm-wiki-methodology.md` 的 Lint 例外「append-only 不回改，去向由后续条目说明」保持原样，去向以本条为准。

**本轮未做**：周期 1 至 3 三份文档的 `status` 直接置 `done`，未逐日核对周期内是否还有未归档的日报；`staging/README.md` 的「待处理」中 `perf-test-analysis.md` 缺失一项未动。

## [2026-09-14] refactor | 精简 AGENTS.md，CLAUDE.md 改为纯导入桩

用户精简 [AGENTS.md](AGENTS.md)，删去「内容纪律」「内部系统的访问」「跨仓库边界」「Git」四节，保留定位与边界／交流与写作／修改授权／导航与记录维护四节，与 `dut-postdoc` 的 `AGENTS.md` 同构。据此调整全库指针与工具入口。

**指针改为各目录自治**。此前七处写着「完整口径见 AGENTS.md 的『内容纪律』」，章节删除后指针悬空，改为各目录 `README.md` 自身即事实源，禁写项就地写成自足表述（措辞统一照 [reports/README.md](reports/README.md) 的现成写法）：[hpc/README.md](hpc/README.md)、[meetings/README.md](meetings/README.md)、[structural-dynamics/README.md](structural-dynamics/README.md)、[schema/page-schemas.md](schema/page-schemas.md)、[schema/llm-wiki-methodology.md](schema/llm-wiki-methodology.md) 与 `CLAUDE.md` 两处。`chats/`、`reports/`、`staging/` 三个 `README.md` 本就自足，未动。「跨仓库边界」确认冗余——[README.md](README.md) 的内容去向表已完整覆盖，`staging/README.md` 中 `dut-postdoc` 的归位依据改指该表。

**CLAUDE.md 改为纯导入桩**，仿 `dut-postdoc` 写法，只剩 `@AGENTS.md` 一行加一句说明。原有的 Claude Code 运行时差异按内容归属拆走，不留在工具入口：内网 GitLab 各工具可用的读取通道进 [hpc/environment.md](hpc/environment.md) 3.4（新增，编号取末位以免影响既有小节引用），飞书聊天读取通道进 [chats/README.md](chats/README.md) 新增的「读取通道」节。两处都保留了「AI 不持有、不索取、不代填凭据，不代为登录」的口径。通道差异从自动加载降为按需查阅，代价是可能多试几次失败的通道，不涉及公开发布风险。

**「Git」一节经用户明确决定不放回**。原三条为提交前逐条看已暂存 `git diff` 做公开发布检查、附件停下核对权利方授权、`main` 直接提交不开分支，删除后全库无第二份正文，[README.md](README.md) 与 [schema/README.md](schema/README.md) 中论证「安全硬约束必须放在自动加载文件里」的两处表述随之移除。**本仓 Public 且内容涉及单位内部工作，此后没有任何自动加载的提交前检查约束，公开发布检查依赖每次提交时人工在场判断。** 本条记录该状态，不作为已解决事项。

## [2026-09-14] refactor | 删除 staging/ 三份非算海材料

经用户确认，`staging/` 只保留算海同事产出的材料，三份非算海文件删除：

- `controlled-ai-use-plan.md`——项目组《大连理工项目受控使用 AI 方案》对外讨论稿 v0.2，归档经过见 [hpc/log.md](hpc/log.md) 2026-08-24 条。
- `solver-and-environment-review.md`——求解配置确认（复数版 PETSc/Hypre 必要性与切实数版本的兼容影响），文首无来源标注、作者与日期始终未确认。[2026-09-10] lint 条登记的「来源待确认」项随文件删除关闭。
- `hpc_研发规划_2026-2027_1.3.2_多后端异构并行计算_配图说明.md`——《HPC 研发规划（2026-2027）》1.3.2 节配图说明，未登记进 [staging/README.md](staging/README.md) 清单，且引用的 `images/` 图片与 2.2 节草稿在全库均不存在。该文件此前未纳入 Git 跟踪，删除后仓库内无副本。

连带修改：[staging/README.md](staging/README.md) 清单表删去前两份对应行，「待处理」中关于未定稿讨论稿与已成稿材料混放的一条随之删除。历史条目按 append-only 不改写正文，仅把指向已删文件的失效链接摘成纯文本文件名并标注「（已删除）」，共三处——[hpc/log.md](hpc/log.md) 的 2026-09-10、2026-08-24 两条，本文件 2026-09-14 archive 条一处。

受影响的判断：本文件 2026-09-14 archive 条中，`bdf-to-db-partition-dataflow.md` 的入仓判断原援引受控使用 AI 方案的数据分档，方案文件删除后该分档依据不再在本仓留存，入仓依据以「用户确认已取得算海公开授权」为准。

## [2026-09-14] archive | 归档 2026-09-12 收到的两份算海文档

按 [staging/README.md](staging/README.md) 处理，均改 kebab-case 命名、补 frontmatter 与来源引用块，正文原文照录未改。

- 胡凯《壳结构线性静力分析.md》→ [staging/reissner-mindlin-shell-linear-static.md](staging/reissner-mindlin-shell-linear-static.md)。全文为壳单元公式的数学推导，引用公开文献，扫查无源码、内部路径、算例数据与凭据，属 `staging/controlled-ai-use-plan.md`（已删除）第一档「已审查过的抽象数学表达」。

- 宋维豪《BDF到DBManager与Partition计算数据流.md》→ [staging/bdf-to-db-partition-dataflow.md](staging/bdf-to-db-partition-dataflow.md)。**入仓前提出了公开发布异议**：文中含 SGSim 未公开接口与 C++ 源码片段、`/home/peter/...` 内部绝对路径、`50w-2d` 算例统计与运行日志计时，按受控使用 AI 方案的分档落在「只能在受控环境里处理」与「禁止上传」范围。**用户确认已取得算海对该文档的公开授权**，据此原样入库，授权依据在清单表与文首引用块注明。未发现账号、口令、密钥类凭据。

两份的用途与归位均未在本仓留下依据，清单表标「待确认」，未推断。

## [2026-09-10] refactor | 删除 schema/git-workflow.md，提交纪律并入 AGENTS.md

**理由是加载机制，不是内容冗余。** `CLAUDE.md` 里的 `@AGENTS.md` 是真正的 import，会话启动即注入；而 `AGENTS.md` 指向 `schema/git-workflow.md` 的是普通 Markdown 链接，**不会被自动加载**，只能靠「用户说提交时去读一次」这个懒加载指针生效。指针够明确，多数情况会触发，但漏读是可能的——而漏读的代价在三条规则之间严重不对称：漏读「在 `main` 直接提交」只是多建一个分支，漏读「提交前 `git diff` 做公开发布检查」和「附件停下核对授权」就是 Public 仓库泄漏，push 出去即进入 GitHub 缓存与索引。安全硬约束不能挂在懒加载上。

- 三条本仓特有纪律搬进 [AGENTS.md](AGENTS.md) 的「Git」一节：公开发布检查、附件授权核对、`main` 直接提交不开分支。补上 workstation 声明所有仓库共同遵守的「push 前先同步整合远程最新提交」。
- 四条与全局规则重复的内容随文件一并删除（仅用户要求时提交、只 `add` 相关文件、中文提交信息、用原生 Git），避免第二套事实源。
- 同时修好一个失效的跨仓指针：`workstation` 的 Git 模块已从 `git/` 移到 `machine/git/`，原文件里的 blob 与 raw 链接都已失效。上一条 lint 没扫到它——本地断链检查看不见跨仓库链接，Lint 检查项已补充说明这类指针只能人工核对。
- `schema/` 因此收窄为纯粹的页面结构层：方法论、页面规范、模板，不再放操作规程。规范 §1、[schema/README.md](schema/README.md)、[README.md](README.md)、[index.md](index.md) 同步改写。

历史条目「[2026-09-10] refactor | 按 LLM Wiki 模式重构全库」中指向 `schema/git-workflow.md` 的链接因此失效，append-only 不回改，去向以本条为准。原文件内容仍可在 git 历史中按 `ai/git-workflow.md` 取回。

## [2026-09-10] revise | 日报写法统一为一句话记法

上一条 lint 登记的「日报写法分叉」已定：**采用一句话记法**，即 2026-08-24 起的实际写法，四项展开不再作为默认要求。

- [reports/README.md](reports/README.md)「日报写法」改为「每个事项默认一句话，写清做了什么、到什么状态；细节靠指针指向任务线事实源。确有问题、需协助事项或明确下一步时才补一行」，并删除待确认标注。
- [schema/templates/daily-report.md](schema/templates/daily-report.md) 骨架同步改写，示例段由四字段块改为一句话条目。
- 历史日报不回改：2026-08-24 之前按四项展开写的那些是当时的实际记录，改了就不是原件了。

## [2026-09-10] lint | 重构后全库体检

重构完成后按 [schema/llm-wiki-methodology.md](schema/llm-wiki-methodology.md) 的 Lint 检查项走了一遍，结果如下。

修复：

- 补建 [schema/README.md](schema/README.md)。`schema/` 是一级内容目录却没有索引，违反规范 §4；同步登记进 [index.md](index.md) 与 [README.md](README.md) 的目录树。
- 为 [reports/daily/biweekly-04-2026-09-07-to-2026-09-18.md](reports/daily/biweekly-04-2026-09-07-to-2026-09-18.md) 补 frontmatter。它是周期文档，不在「历史单日日报暂不加」的例外范围内。
- 规范 §3 删除 `template` 类型：`templates/` 下的骨架直接写目标页面的 `type`，复制出去即正确，`template` 是没有实例的死词表项。
- 规范 §3 例外补全：根目录三个门面文件与 `.gitignore` 排除的本机文件不加 frontmatter，此前只写了历史日报一条。
- 规范 §3 `staging/` 状态取值扩为 `staged | draft | relocated`，容纳未定稿讨论稿。
- Lint 检查项补两条：相对链接是否解析（`schema/templates/` 除外，模板里的链接相对于复制后的位置写）、规范与最近实际写法是否分叉。
- 日报骨架的一级标题改为 `# YYYY-MM-DD 日报`，与实际日报一致；`reports/README.md` 的示例同步。

登记待确认，未自行改动：

- **日报写法分叉**：规范要求每事项展开四项，但 2026-08-24 起实际发出的 11 篇以上日报都改成一事项一句话。已在 [reports/README.md](reports/README.md) 记为待确认，两套写法择一后统一改齐。
- **`chats/weixin-dut-institute-hpc.md` 归属**：与 [README.md](README.md) 内容去向表「微信原文归 `heliangos/wechat`」冲突，见 [chats/README.md](chats/README.md)。
- **`staging/perf-test-analysis.md` 转换状态**：清单曾登记，文件与图片目录均不在仓库内，见 [staging/README.md](staging/README.md)。
- **`staging/solver-and-environment-review.md` 来源**：文首无来源标注，作者与日期未推断。

通过项：全库相对链接无断链；frontmatter 必填字段无缺失；`type` / `status` 取值均在词表内；各目录 `README.md` 文件清单与实际文件一致；全库 UTF-8 无 Mojibake；未发现凭据、内部访问地址或未授权再分发内容混入。

## [2026-09-10] refactor | 按 LLM Wiki 模式重构全库

参照 `dut-postdoc` 的实现，把本仓从「按目录堆文档」改为显式的三层结构：原始源层（GitLab、飞书、相邻仓库，只读不入 Git）／ Wiki 层（任务线三件套、纪要、群聊原文、报告、暂存）／ Schema 层（`schema/`）。

新建：

- [index.md](index.md) 全库总导航，[log.md](log.md) 知识库维护日志（本文件）。
- `schema/`：[llm-wiki-methodology.md](schema/llm-wiki-methodology.md)（三层分工与 Ingest / Query / Lint 的本仓定义）、[page-schemas.md](schema/page-schemas.md)（页面归属、命名、属性、索引、存储）、`templates/` 四份骨架。
- [meetings/README.md](meetings/README.md)：此前唯一没有索引的内容目录。

改写：

- `ai/context.md` 拆成两份并删除原文件：常驻协作纪律进 [AGENTS.md](AGENTS.md)，页面结构规范进 [schema/page-schemas.md](schema/page-schemas.md)。`ai/git-workflow.md` 经 `git mv` 移到 [schema/git-workflow.md](schema/git-workflow.md)，`ai/` 目录移除。
- [AGENTS.md](AGENTS.md) 成为工具无关常驻规则的唯一来源，[CLAUDE.md](CLAUDE.md) 改为 `@AGENTS.md` 导入桩，只保留 Claude Code 的运行时差异（GitLab 走 `claude-in-chrome`、飞书走 `preview_start`）。导入桩不复制规则正文，杜绝规则漂移。
- 各目录 `README.md` 统一为「frontmatter → 收录范围 → 文件清单表 → 内容纪律 → 相关链接」。保留 `README.md` 文件名而不改叫 `_index.md`：本仓是 Public 仓库，GitHub 只自动渲染 `README.md`。
- 既有 37 个内容页补 YAML frontmatter（`title` / `type` / `status` / `date_added` / `date_update`），日期取自 git 首末提交记录，不倒填。
- 链接保持相对 Markdown 形式，不改 Obsidian 双链——Public 仓库需要在 GitHub 上可点击。

与 `dut-postdoc` 的有意差异：本仓**不建概念页与文献层**。可复用的技术知识归 `dut-postdoc`，本仓只写项目事实。`status` 词表按项目档案重新定义（`active` / `living` / `staged` 等），不沿用论文库的取值。任务线进度仍留在各自 `log.md`，本文件只记知识库自身的演化。

顺带修正：

- 删除对不存在的 `drafts/` 的引用（[README.md](README.md) 目录树、[staging/README.md](staging/README.md)）。`hpc/log.md` 中的提及属历史条目，append-only，不改。
- `staging/` 两个 PascalCase 文件名经 `git mv` 改为 kebab-case（`MPI_Solver_Performance_Comparison.md`、`Solver_and_Environment_Review.md`），确认无入链后再改。
- [staging/README.md](staging/README.md) 清单表与实际文件对齐：补登三份漏登材料，登记 `perf-test-analysis.md` 缺失。
