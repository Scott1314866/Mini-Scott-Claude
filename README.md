# ScottClaude

本地 AI Agent 系统。`scott-core` 作为常驻守护进程处理所有任务，`scott`（CLI）和 `scott-tui`（TUI）通过 TCP loopback 与之通信。

## 界面展示

![ScottClaude TUI 界面](docs/assets/scottclaude-tui.png)

## 环境要求

| 依赖 | 版本 |
|------|------|
| 操作系统 | macOS / Linux |
| Python | 3.12.x |
| [uv](https://docs.astral.sh/uv/) | ≥ 0.4 |

安装 uv（若尚未安装）：

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Python 3.12 由 uv 自动管理，无需手动安装。

## 快速开始

```bash
git clone <repo> && cd ScottClaude
uv sync
cp .env.example .env        # 按需修改

uv run scott-core &          # 启动守护进程（后台）
uv run scott ping            # 验证连通：应返回 pong
uv run scott --version       # 应输出 0.0.1
```

## 文档

- **[RUNBOOK.md](./RUNBOOK.md)** — 完整操作参考：配置、开发命令、故障排查
- **[WIRE_PROTOCOL.md](./WIRE_PROTOCOL.md)** — IPC 协议定义（由代码生成，勿手动编辑）

## S8：creator 创造模式

`creator` 是内置的元 Agent 预设：能在会话里**创作、修改、校验、导出其他预设**（Agent Profile 与 Skill），形成 agent 创作 agent 的闭环。

会话内工具：`preset_list` / `preset_show`（只读）、`preset_validate`（干跑校验）、`preset_write` / `preset_import`（写盘，默认 ASK 审批，仅 `creator` 预设自带）、`preset_export`（导出可分享的 Markdown 预设包，仅写 `./workspace/`）。

CLI 调试命令（本地文件操作，无需 daemon）：

```
scott preset list [--kind agent|skill|all]
scott preset show <kind> <name>
scott preset validate <kind> <file>
scott preset new <name> --kind agent|skill
scott preset export <kind> <name> [-o out.md]
scott preset import <file.md> [--global]
```

**信任边界**：`creator` 的会话视为「能改变未来会话行为」——写盘需审批、写入前强制校验、永不写入内建目录（builtin）。
