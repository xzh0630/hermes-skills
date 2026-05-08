---
name: github-auto-didact
description: Self-learning system - autonomously search GitHub for useful tools/frameworks and create skills from them.
version: 1.0.0
author: Hermes Agent
tags: [github, learning, skills, autonomous, auto-didact]
---

# GitHub Auto-Didact

Autonomous skill learning from GitHub. Hermes can search, analyze, and create skills from GitHub repositories.

## How It Works

Two modes:

### 1. During conversation (on-demand)
When user asks me to learn something or I encounter a gap:
- Search GitHub using C:\Users\Administrator\workspace\gh_search.py
- Fetch README via curl/terminal
- Create skills with skill_manage(action='create')

### 2. Scheduled weekly learning
Cron job `github-auto-learn` runs every 7 days:
- Searches trending repos in agent/developer/ML categories
- Creates skills from discovered tools
- Reports what was learned

## Search Script

`C:\Users\Administrator\workspace\gh_search.py` - searches GitHub repos by keyword

Usage:
```bash
python C:\Users\Administrator\workspace\gh_search.py <search query>
```

Example:
```bash
python C:\Users\Administrator\workspace\gh_search.py "agent-framework python"
```

## Cron Job

- Name: github-auto-learn
- Schedule: every 7 days
- Job ID: f16d5bf3c643
- Creates skills under ~/.hermes/skills/auto-learned/

## Manual Learning

During conversation, when user needs something new:
1. Search GitHub for relevant repos
2. Read README via web_fetch or curl
3. Create skill with skill_manage

## Trigger Conditions

- User says "learn X" or "find me a tool for X"
- You encounter a task you're not well-equipped for
- Weekly cron job runs autonomously