---
name: openclaw-agent-bridge
description: Use OpenClaw's multi-agent system as sub-agents for Hermes — delegate tasks to 30+ specialized agents via CLI bridge.
version: 1.0.0
author: Hermes Agent
---

# OpenClaw Agent Bridge

Call OpenClaw's specialized agents from Hermes using the `openclaw agent` CLI.

## How it works

OpenClaw has a running gateway on port 18789 (node.exe). Hermes can invoke any OpenClaw agent via:

```bash
openclaw agent --agent <agent_id> --message "<task>" --json --timeout <seconds>
```

Returns structured JSON with text results in `result.payloads[].text`.

## Bridge Script

Located at: `C:\Users\Administrator\workspace\openclaw_bridge.py`

Usage:
```bash
python C:\Users\Administrator\workspace\openclaw_bridge.py <agent_id> "任务描述" [--timeout 60]
```

## Available Agents (30+)

### Core
- `main` - Main assistant agent

### Design
- `design-brand-guardian`, `design-image-prompt-engineer`, `design-ui-designer`
- `design-ux-architect`, `design-ux-researcher`

### Engineering
- `engineering-ai-engineer`, `engineering-backend-architect`, `engineering-code-reviewer`
- `engineering-data-engineer`, `engineering-database-optimizer`, `engineering-devops-automator`
- `engineering-dingtalk-integration-developer`, `engineering-feishu-integration-developer`
- `engineering-frontend-developer`, `engineering-git-workflow-master`
- `engineering-mobile-app-builder`, `engineering-rapid-prototyper`
- `engineering-security-engineer`, `engineering-senior-developer`
- `engineering-software-architect`, `engineering-technical-writer`
- `engineering-wechat-mini-program-developer`

### Finance
- `finance-financial-analyst`

### Marketing
- `marketing-baidu-seo-specialist`, `marketing-bilibili-strategist`
- `marketing-china-ecommerce-operator`, `marketing-cross-border-ecommerce`
- `marketing-douyin-strategist`, `marketing-private-domain-operator`
- `marketing-wechat-operator`, `marketing-xiaohongshu-operator`

## Usage from Hermes

When a complex task comes in that would benefit from parallel work:
1. Decompose the task into subtasks
2. Assign each subtask to the relevant OpenClaw agent
3. Collect results and synthesize

Example (via terminal):
```bash
cmd.exe //c "chcp 65001 >nul && C:\Users\Administrator\AppData\Roaming\npm\openclaw.cmd agent --agent engineering-code-reviewer --message \"Review this code...\" --json --timeout 60"
```

## Requirements
- OpenClaw gateway running on port 18789 (node.exe)
- `openclaw.cmd` in PATH via npm
- DeepSeek API key configured in OpenClaw

## Troubleshooting
- Gateway not running: `openclaw gateway start`
- Agent not found: `openclaw agents list`
- Session expired: You may need the user to re-scan the QR code
