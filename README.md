# job-agent · JD Research Agent

本仓库定位为**面向招聘岗位数据的业务 Agent 工程**，目标是让 Agent 使用真实岗位库执行检索、比较、统计和生成附证据的研究报告，而不是只输出缺乏依据的答案。

## 与 HarnessLab 的分工

**通用 SDK / Agent Runtime 实验已经移走**。原 `src/run_phase0.py` 的功能只是验证 DeepSeek Harness SDK、Bash 工具与 Session 日志，未涉及岗位检索，所以它及其运行依赖统一迁入：

- [HarnessLab · SDK Minimal Phase 0 可运行实验](https://github.com/leecolin2023/HarnessLab/tree/main/agent-runtime/labs/sdk-minimal-phase0)
- [Phase 0 项目架构与逐行代码解读](https://github.com/leecolin2023/HarnessLab/blob/main/agent-runtime/labs/sdk-minimal-phase0/01-code-walkthrough.md)
- [Agent Loop 运行原理](https://github.com/leecolin2023/HarnessLab/blob/main/agent-runtime/01-agent-loop-execution-mechanism.md)

**本仓库只维护 JD Research Agent 的业务实现。** 此次清理后，原来的 `bash scripts/run-docker.sh run` 不再是本仓库的命令；请在 HarnessLab 对应实验目录运行它。

## 本项目希望解决什么问题？

以一组经过授权、固定版本的完整 JD 为例，用户提出：

> 统计岗位职责或任职要求涉及 RAG 的职位中，各项技术要求的出现情况，并区分必备条件与优先条件。

合格结果不能只是一段模型自行概括的总结，至少要能回答：统计的样本范围是什么、按什么字段判断 RAG 相关、如何去重、某项技能出现了多少次、每一项统计能追溯到哪些职位 ID 与原文。

## 下一步业务架构（规划，尚未实现）

| 模块 | 计划职责 |
| --- | --- |
| `job-rag` 数据访问适配器 | 按职位 ID 读取固定快照中的完整 JD |
| 检索工具 | 根据职位文本、职责和任职要求召回相关岗位，按职位 ID 去重 |
| JD 语义判断 | 判断职位是否真实涉及 RAG，区分主责、必备和加分项 |
| 证据与报告 | 生成统计分母、来源职位 ID、可回查原文与研究报告 |
| 任务验证与评测 | 检查覆盖范围、去重结果、引用正确性及统计口径 |

上述只是目标拆分，不代表已存在这些源码、CLI 或模型服务。

## 当前状态

- **已完成的历史实验**：Phase 0 的 SDK 最小调用及离线测试，现归档到 HarnessLab 继续维护。
- **本仓库业务实现**：尚未接入 JD 查询工具、业务 Agent 执行入口或报告校验。
- **文档入口**：见 [docs/01-project-architecture-and-code.md](./docs/01-project-architecture-and-code.md) 的历史文档迁移说明。

原则：需要解释某项 Agent Runtime 原理时在 HarnessLab 中做可复现实验；需要实现实际招聘岗位研究任务时才在本仓库编写业务代码。
