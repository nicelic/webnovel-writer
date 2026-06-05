---
name: webnovel-doctor
description: This skill should be used when the user asks to "/webnovel-doctor", "检查项目环境", "体检网文项目", "排查 RAG 配置", "检查缺失文件", "项目状态不对", or needs a read-only diagnosis of webnovel-writer project files, databases, dependencies, and runtime configuration.
version: 0.1.0
allowed-tools: Read Bash
argument-hint: "[--chapter N] [--deep]"
---

# Webnovel Doctor

## 目标

运行只读项目体检，确认当前书项目在所处阶段应该具备的目录、文件、JSON、SQLite、RAG 配置、Python 依赖和 Dashboard 构建产物是否完整。

## 原则

1. 只读诊断，不写入项目文件，不自动修复，不安装依赖，不启动 Dashboard。
2. 先运行 `project-status` 获取短状态，再运行 `doctor` 获取详细检查。
3. 使用 `python -X utf8`，避免 Windows 中文路径和中文文件名编码问题。
4. 保留旧 `status` 命令语义；需要短状态时使用 `project-status`，需要宏观创作健康报告时才使用 `status`。
5. 根据 doctor 输出说明影响和修复建议；缺失项不要自行猜测为终态要求，阶段由 runtime 推导。

## 执行

准备路径：

```bash
export WORKSPACE_ROOT="${CLAUDE_PROJECT_DIR:-$PWD}"
export SCRIPTS_DIR="${CLAUDE_PLUGIN_ROOT:?}/scripts"
```

短状态：

```bash
python -X utf8 "${SCRIPTS_DIR}/webnovel.py" --project-root "${WORKSPACE_ROOT}" project-status --format summary
```

标准体检：

```bash
python -X utf8 "${SCRIPTS_DIR}/webnovel.py" --project-root "${WORKSPACE_ROOT}" doctor --format text
```

指定章节：

```bash
python -X utf8 "${SCRIPTS_DIR}/webnovel.py" --project-root "${WORKSPACE_ROOT}" doctor --chapter {chapter_num} --format text
```

深度体检：

```bash
python -X utf8 "${SCRIPTS_DIR}/webnovel.py" --project-root "${WORKSPACE_ROOT}" doctor --deep --format text
```

## 输出方式

汇报时包含：

- 当前 `phase` 和 `target_chapter`。
- 是否有 blocker。
- 缺失或异常文件的路径。
- RAG / Python / Dashboard 配置是否缺失。
- 每个问题的影响和建议修复动作。

避免输出：

- 不执行真实修复。
- 不展示或要求用户粘贴 API key。
- 不把 init 刚结束的项目按已写多章项目检查。
