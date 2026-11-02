# job-agent

面向真实招聘岗位数据的 Agent 实验工程。首个案例采用 **DeepSeek Harness Python SDK**，从一个可观察、可隔离的 Agent 执行开始，逐步接入 job-rag 的岗位数据、工具、验证和评测。

**职责分工**：本仓库只维护运行代码、部署配置、测试和运行说明；通用机制、学习路线及实验原理文档统一维护在 [HarnessLab / agent-runtime](https://github.com/leecolin2023/HarnessLab/tree/main/agent-runtime)。

## 阶段 0：隔离运行 SDK 与观察 Session

参照 [HarnessLab｜阶段 0 中文教程](https://github.com/leecolin2023/HarnessLab/blob/main/agent-runtime/00-deepseek-harness-sdk-learning.md)。

代码入口：

| 文件 | 说明 |
| --- | --- |
| `docker/Dockerfile` | Python 3.12 与 DeepSeek Harness Python SDK |
| `scripts/run-docker.sh` | Docker 环境检查、构建、真实执行与 Session 统计 |
| `src/run_phase0.py` | SDK 最小执行、记录运行元数据 |
| `src/session_report.py` | 只展示 JSONL 事件类型及数量，不打印正文 |
| `config/disable-session-upload.patch.yml` | 关闭向官方模型 API 附加 Session 事件的可选机制 |
| `tests/test_phase0.py` | 不依赖真实 SDK/模型 Key 的离线测试 |
| `.github/workflows/phase0-offline.yml` | 代码静态与离线 CI 校验 |

### Linux Docker 快速开始

```bash
git clone https://github.com/leecolin2023/job-agent.git
cd job-agent

bash scripts/run-docker.sh check
bash scripts/run-docker.sh test
bash scripts/run-docker.sh build

read -rs -p 'DeepSeek test API Key: ' DEEPSEEK_API_KEY
echo
export DEEPSEEK_API_KEY

# 根据实际 API 端点设置模型，可选：
# export DSH_MODEL=deepseek-v4-flash
# export DEEPSEEK_BASE_URL=https://your-compatible-endpoint/v1

bash scripts/run-docker.sh run
bash scripts/run-docker.sh inspect
```

**运行状态**：截至迁移时，原先的 HarnessLab 离线 CI 已通过；真实 Docker / DeepSeek API / Session 日志测试**尚未执行**。后续以此仓库自己的 CI、真实运行记录为准。

### 安全边界

- 使用有用量上限、可撤销的专用测试 API Key。请勿将凭据或真实银行数据写入仓库。
- DSH `sdk-minimal` 默认 `danger-full-access`；仅指定 `cwd` **不是沙箱**。本项目使用独立容器、只读根文件系统、收紧进程权限、独立挂载工作区和日志 Home。
- Docker 默认 bridge 网络仍可能访问其他地址；有严格出站要求时应在宿主防火墙或模型网关限制。
- Key 作为环境变量进入容器，Agent Shell 理论上也能读取它；生产方案应采用容器外模型网关。
- `.local/`、密钥和原始 Session 日志不可提交公开仓库。

## 后续计划

先完成阶段 0 的实际 Docker 运行，再使用固定、脱敏完整 JD 形成首个可回查的 `report.md`；其后按 [HarnessLab 完整学习路线](https://github.com/leecolin2023/HarnessLab/blob/main/agent-runtime/LEARNING_PATH.md) 逐步演进工具调用、Workflow、MCP、Context、State、恢复与评测。

本仓库独立承担工程实现，不再在 HarnessLab 里维护重复代码。
