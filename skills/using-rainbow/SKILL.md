---
name: using-rainbow
description: Use when starting Spec-Driven Development with Hanoi Rainbow framework. ALWAYS use this skill when the user mentions /rainbow commands, wants to build features with structured workflow, mentions "spec-driven", "design first", "task breakdown", "TDD workflow", or needs any phase of the Rainbow development cycle (specify, clarify, design, taskify, analyze, implement, test). Even if the user doesn't explicitly mention Rainbow, use this skill when they want structured feature development with clear phases and agent team orchestration.
---

# Using Rainbow

## Overview

Hanoi Rainbow is a Spec-Driven Development (SDD) framework that transforms ideas into production-ready applications through clear specifications. This skill orchestrates a comprehensive agent team with **isolated workspaces** to execute the workflow efficiently and safely.

**Core Principle**: Define **WHAT** and **WHY** before **HOW**.

**Key Architecture**: Each agent works in its own git worktree, preventing conflicts and enabling parallel work. Main agent tracks progress for crash recovery.

## When to Use This Skill

- Starting a new feature with Rainbow workflow
- User mentions `/rainbow.*` commands
- User wants structured development with specification-first approach
- Building features that need design → task breakdown → implementation flow
- User mentions "spec-driven", "design first", or needs team coordination

## Agent Team Configuration

The Rainbow workflow uses a team of specialized agents with isolated workspaces:

| Agent | Commands | When to Use | Workspace Branch |
|-------|----------|-------------|------------------|
| initializer | regulate, architect, standardize, checklist | Product initialization (once per product) | `rainbow/init/<product>` |
| assessor | assess-context | Brownfield projects only | `rainbow/assess/<feature>` |
| specifier | specify | Every new feature | `rainbow/spec/<feature>` |
| clarifier | clarify | When spec needs refinement | `rainbow/clarify/<feature>` |
| designer | design | After spec complete | `rainbow/design/<feature>` |
| taskifier | taskify | After design complete | `rainbow/tasks/<feature>` |
| analyzer | analyze | Before implementation | `rainbow/analyze/<feature>` |
| implementer | implement | After tasks generated | `rainbow/impl/<feature>` |
| e2e-tester | design-e2e-test, perform-e2e-test | After implementation | `rainbow/e2e/<feature>` |
| issue-manager | tasks-to-issues, tasks-to-ado | When tracking needed | `rainbow/issues/<feature>` |

**Full agent configuration JSON**: See [team-patterns.md](references/team-patterns.md) for complete team structure.

## Workflow Phases

```dot
digraph complete_workflow {
    rankdir=TB;
    node [shape=box, style=filled];

    "Start" [shape=ellipse];

    subgraph cluster_init {
        label="Product Initialization (Once)";
        style=dashed;
        "initializer" [fillcolor="#E1BEE7"];
        "assessor?" [fillcolor="#E1BEE7", label="assessor\n(brownfield only)"];
    }

    subgraph cluster_feature {
        label="Feature Development";
        style=dashed;
        "specifier" [fillcolor="#C8E6C9"];
        "clarifier" [fillcolor="#DCEDC8"];
        "designer" [fillcolor="#E8F5E9"];
        "taskifier" [fillcolor="#E3F2FD"];
        "analyzer" [fillcolor="#FFF9C4"];
        "implementer" [fillcolor="#FFF3E0"];
        "e2e-tester" [fillcolor="#FFCCBC"];
    }

    "issue-manager" [fillcolor="#CFD8DC", label="issue-manager\n(optional)"];

    "Complete" [shape=ellipse];

    "Start" -> "initializer";
    "initializer" -> "assessor?" [label="brownfield"];
    "initializer" -> "specifier" [label="greenfield"];
    "assessor?" -> "specifier";
    "specifier" -> "clarifier" [label="needs refinement"];
    "specifier" -> "designer" [label="spec complete"];
    "clarifier" -> "designer";
    "designer" -> "taskifier";
    "taskifier" -> "analyzer";
    "analyzer" -> "implementer";
    "implementer" -> "e2e-tester";
    "e2e-tester" -> "Complete";
    "taskifier" -> "issue-manager" [label="optional", style=dashed];
}
```

**For detailed phase specifications, commands, and outputs**: See [workflow-details.md](references/workflow-details.md).

## Workspace Isolation Architecture

### Git Worktree Strategy

Each agent works in an isolated git worktree to prevent conflicts:

```
main-workspace/                    # Main agent workspace
├── .claude/
│   └── teams/
│       └── rainbow-workflow/
│           ├── config.json        # Team configuration
│           └── progress.json      # Workflow progress tracking
└── .rainbow/
    └── ...                        # Project files

.claude/worktrees/
├── rainbow-initializer/           # initializer agent worktree
├── rainbow-specifier/             # specifier agent worktree
├── rainbow-designer/              # designer agent worktree
... (one per agent)
```

### Branch Naming Convention

| Agent | Branch Pattern | Example |
|-------|----------------|---------|
| initializer | `rainbow/init/<product-name>` | `rainbow/init/taskify-app` |
| specifier | `rainbow/spec/<feature-id>` | `rainbow/spec/001-user-auth` |
| designer | `rainbow/design/<feature-id>` | `rainbow/design/001-user-auth` |
| implementer | `rainbow/impl/<feature-id>` | `rainbow/impl/001-user-auth` |

**For complete worktree setup and management**: See [workspace-setup.md](references/workspace-setup.md).

## Progress Tracking for Recovery

### Main Agent Responsibilities

Track workflow progress in `.claude/teams/rainbow-workflow/progress.json`:

```json
{
  "workflow_id": "wf-2024-03-13-001",
  "current_phase": "design",
  "phases": {
    "specify": {
      "status": "completed",
      "agent": "specifier",
      "outputs": ["specs/001-user-auth/spec.md"]
    },
    "design": {
      "status": "in_progress",
      "agent": "designer",
      "outputs": []
    }
  },
  "active_agent": "designer"
}
```

### Recovery Protocol

When resuming after a crash:

1. **Read progress.json** - Find `current_phase` with `in_progress` status
2. **Discard incomplete changes** - Old worktree changes are lost by design
3. **Create fresh worktree** - Pull latest from remote
4. **Spawn agent** - Resume from last completed checkpoint

**For detailed recovery procedures**: See [recovery-guide.md](references/recovery-guide.md).

## Model Selection for Teammates

**IMPORTANT**: Before spawning agents, ask the user to confirm the model for each teammate.

### Recommended Models

| Agent | Recommended Model | Reason |
|-------|-------------------|--------|
| initializer | sonnet | Balanced for documentation tasks |
| assessor | sonnet/opus | Code analysis requires understanding |
| specifier | sonnet | Requirement gathering and organization |
| clarifier | sonnet | Interactive Q&A and refinement |
| designer | sonnet/opus | Complex research and decisions |
| taskifier | sonnet | Analysis and organization |
| analyzer | sonnet | Consistency checking |
| implementer | sonnet/opus | Coding and TDD |
| e2e-tester | sonnet | Test design and execution |
| issue-manager | haiku | Simple conversion tasks |

Use AskUserQuestion for model confirmation before creating the team.

## Team Creation Workflow

### Step 1: Confirm Model Selection

Use AskUserQuestion to confirm models for all agents.

### Step 2: Create Team and Progress Tracking

```
TeamCreate with team_name: "rainbow-workflow"

Create .claude/teams/rainbow-workflow/progress.json with initial state
```

### Step 3: Spawn Agent with Worktree

For each agent that will work:

```bash
# Create worktree
git worktree add -b <branch-name> .claude/worktrees/<agent-name>

# Spawn agent
Agent with:
- name: <agent-name>
- subagent_type: "general-purpose"
- team_name: "rainbow-workflow"
- model: <confirmed-model>
- isolation: "worktree"
```

### Step 4: Monitor and Handoff

1. Wait for agent completion message
2. Agent pushes changes to remote
3. Update progress.json
4. Spawn next agent in sequence

### Step 5: Cleanup on Completion

1. Send shutdown_request to all agents
2. Merge final changes to main branch
3. Remove worktrees
4. TeamDelete

**For detailed agent coordination patterns**: See [team-patterns.md](references/team-patterns.md).

## Key Rules

1. **One agent per workspace**: Each agent has its own git worktree
2. **Sync via remote**: Agents push/pull to share changes
3. **Track progress**: Main agent updates progress.json after each phase
4. **Discard incomplete changes on recovery**: Fresh worktree on restart
5. **Sequential phases**: Most phases depend on previous outputs
6. **Parallel when possible**: issue-manager, e2e-tester can run in parallel

## Red Flags

| Thought | Reality |
|---------|---------|
| "I'll skip the worktree isolation" | Conflicts will occur. Always use worktrees. |
| "I don't need progress tracking" | Cannot recover from crashes. Always track progress. |
| "Agents can share a workspace" | Race conditions and conflicts. Isolate workspaces. |
| "I'll just implement directly" | Skip phases = technical debt. Follow the workflow. |

## Reference Files

Load reference files based on specific needs:

| File | When to Read |
|------|--------------|
| [workflow-details.md](references/workflow-details.md) | Need complete phase specifications, commands, outputs, or quality gates |
| [workspace-setup.md](references/workspace-setup.md) | Creating or managing git worktrees for agent isolation |
| [team-patterns.md](references/team-patterns.md) | Spawning agents, coordinating handoffs, or handling errors |
| [recovery-guide.md](references/recovery-guide.md) | Recovering from crashes or interruptions |
| [command-reference.md](references/command-reference.md) | Need details about specific Rainbow commands |