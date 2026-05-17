---
name: task-manager
description: "Create and manage task lists for subagents. Agents work through tasks sequentially and don't stop until all tasks are complete."
metadata:
  builtin_skill_version: "1.0"
  copaw:
    emoji: "📋"
    requires: {}
---

# Task Manager Skill

Manage task lists that agents work through until completion.

## How It Works

1. **Create a TASKS.md file** in the agent's workspace with all tasks
2. **Agent reads tasks** and works through them one by one
3. **Agent marks tasks complete** with ✅ as they finish
4. **Agent doesn't stop** until all tasks are marked complete
5. **Agent reports back** to Jenny when done

## TASKS.md Format

```markdown
# SEO Implementation Tasks

**Agent:** SEO Specialist
**Priority:** 🔴 URGENT
**Started:** 2026-03-22
**Status:** 🔄 IN PROGRESS

## Instructions
- Work through tasks in order
- Mark each task ✅ when complete
- Do NOT stop until ALL tasks are done
- Push to GitHub after completing related tasks
- Report to Jenny when finished

---

## Tasks

### Section 1: Hreflang Implementation
- [ ] Task 1: Add hreflang tags to all HTML pages
- [ ] Task 2: Test hreflang tags are correct
- [ ] Task 3: Commit and push

### Section 2: Sitemaps
- [ ] Task 4: Create language-specific sitemaps
- [ ] Task 5: Update robots.txt
- [ ] Task 6: Commit and push

...

---

## Progress
- Started: {date}
- Completed: 0/{total} tasks
- Last Update: {date}
```

## Task Status Indicators

| Symbol | Status |
|--------|--------|
| ⬜ | Not started |
| 🔄 | In progress |
| ✅ | Complete |
| ❌ | Blocked/Failed |
| ⚠️ | Needs review |

## Dispatching Tasks to Agent

```bash
# Write TASKS.md to agent's workspace
# Then trigger the agent:

curl -X POST "http://127.0.0.1:8088/api/console/chat" \
  -H "Content-Type: application/json" \
  -H "X-Agent-Id: {agent_id}" \
  -d '{"input": [{"content": [{"type": "text", "text": "Read your TASKS.md file and complete ALL tasks. Do NOT stop until all tasks are marked ✅ complete. Report back when finished."}]}], "session_id": "task-{name}", "user_id": "jenny", "channel": "console"}'
```

## Monitoring Progress

```bash
# Read agent's TASKS.md to see progress
# Use read_file on: C:\Users\kevin\.copaw\workspaces\{workspace}\TASKS.md

# Check agent's MEMORY.md for updates
# Use read_file on: C:\Users\kevin\.copaw\workspaces\{workspace}\MEMORY.md
```

## Agent Workspaces

| Agent | Workspace |
|-------|-----------|
| SEO Specialist | seo-specialist |
| Translator | translator |
| Bug Hunter | bug-hunter |
| DevOps Engineer | devops-engineer |
| Content Writer | content-writer |
| UX/UI Designer | ux-designer |