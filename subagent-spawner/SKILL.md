---
name: subagent-spawner
description: "Spawn, manage, and dispatch tasks to specialized CoPaw agents. Enables parallel execution with multiple agents working simultaneously."
metadata:
  builtin_skill_version: "1.0"
  copaw:
    emoji: "🚀"
    requires: {}
---

# Subagent Spawner Skill

Delegate tasks to specialized agents while you focus on orchestration.

## Core Concept

You are the **Team Lead (Orchestrator)**. Spawn specialized agents to work in parallel:
- SEO Specialist → Multi-language SEO implementation
- Bug Hunter → Bug detection and fixing
- Content Writer → Blog content
- Translator → Translation updates
- DevOps Engineer → Infrastructure

## Agent Registry

| Agent | ID | Specialty | Workspace |
|-------|-----|-----------|-----------|
| Default (Jenny) | default | Team Lead, Orchestration | default |
| SEO Specialist | 7aYH9b | SEO, hreflang, sitemaps | seo-specialist |
| Bug Hunter | yfHF7d | Bug detection, fixing | bug-hunter |
| DevOps Engineer | kC6AHy | Cloudflare, infrastructure | devops-engineer |
| UX/UI Designer | AXKut5 | UI improvements | ux-designer |
| Content Writer | J4LZMU | Blog, copywriting | content-writer |
| Translator | pp9cNQ | Localization | translator |

## How to Dispatch a Task

### Step 1: Write Task File

Write a clear task to the agent's workspace:

```
File: C:\Users\kevin\.copaw\workspaces\{agent-workspace}\TASK.md

# Task: {Task Name}
**Priority:** 🔴 URGENT / 🟡 HIGH / 🟢 NORMAL
**From:** Jenny (Team Lead)
**Date:** {current_date}

## Objective
[Clear, specific goal]

## Requirements
1. [Requirement 1]
2. [Requirement 2]

## Files to Work On
- Path: C:\Users\kevin\Desktop\influqa-platform\public

## When Done
1. Commit and push to GitHub
2. Update this file with ✅ COMPLETED
3. Write results to REPORT.md
```

### Step 2: Create Chat Session

```bash
copaw chats create --agent-id {agent_id} --session-id "task:{task_name}" --user-id orchestrator --name "{Task Name}"
```

### Step 3: Monitor Progress

```bash
# Check agent's memory/report
read_file: C:\Users\kevin\.copaw\workspaces\{agent-workspace}\MEMORY.md
read_file: C:\Users\kevin\.copaw\workspaces\{agent-workspace}\REPORT.md

# Check chat sessions
copaw chats list --agent-id {agent_id}
```

## Parallel Execution Pattern

```
Jenny (Team Lead)
├── My Task: i18n data-i18n attributes
├── SEO Agent: hreflang + sitemaps
├── Translator: language file updates
└── Bug Hunter: bug fixes

All running in parallel!
```

## Checking Agent Completion

1. Read `MEMORY.md` in agent's workspace
2. Read `REPORT.md` if exists
3. Check `TASK.md` for status updates
4. Review commits in GitHub

## Quick Commands

```bash
# List all agents
curl -s http://127.0.0.1:8088/api/agents

# DISPATCH TASK TO AGENT (THE KEY METHOD!)
curl -X POST "http://127.0.0.1:8088/api/console/chat" \
  -H "Content-Type: application/json" \
  -H "X-Agent-Id: {agent_id}" \
  -d '{"input": [{"content": [{"type": "text", "text": "YOUR TASK INSTRUCTIONS"}]}], "session_id": "task-{name}", "user_id": "jenny", "channel": "console"}'

# Check agent sessions
copaw chats list --agent-id {id}

# Read agent's memory
# Use read_file tool on: C:\Users\kevin\.copaw\workspaces\{workspace}\MEMORY.md
```

## Example: Dispatch SEO Task

```bash
curl -X POST "http://127.0.0.1:8088/api/console/chat" \
  -H "Content-Type: application/json" \
  -H "X-Agent-Id: 7aYH9b" \
  -d '{"input": [{"content": [{"type": "text", "text": "Implement multi-language SEO: 1) Add hreflang tags, 2) Create language sitemaps, 3) Update robots.txt"}]}], "session_id": "seo-task-001", "user_id": "jenny", "channel": "console"}'
```

## Important Notes

1. **Agents have isolated workspaces** - They don't see your files by default
2. **Provide full paths** - Always give absolute paths to project files
3. **GitHub access** - Agents need to push commits
4. **Check daily notes** - Agents log work to `memory/YYYY-MM-DD.md`