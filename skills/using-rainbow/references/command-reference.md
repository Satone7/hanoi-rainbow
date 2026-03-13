# Rainbow Command Reference

## Core Workflow Commands

### `/rainbow.regulate`

**Description**: Create or update project governing principles

**Output**: `memory/ground-rules.md`

**Usage**:
```
/rainbow.regulate Create principles focused on code quality, testing standards, user experience consistency, and performance requirements.
```

**Commit Prefix**: `docs:`

---

### `/rainbow.assess-context`

**Description**: Analyze existing codebase for brownfield projects

**Output**: `memory/ground-rules.md`, context documentation

**Usage**:
```
/rainbow.assess-context Analyze the existing codebase structure, patterns, and conventions.
```

**Commit Prefix**: `docs:`

---

### `/rainbow.specify`

**Description**: Define feature requirements and user stories

**Output**: `specs/<feature-id>/spec.md`

**Usage**:
```
/rainbow.specify Build a photo organizer with albums grouped by date. Users can drag-and-drop albums to reorganize them.
```

**Commit Prefix**: `docs:`

**Key Rules**:
- Focus on WHAT and WHY
- No implementation details
- Maximum 3 [NEEDS CLARIFICATION] markers

---

### `/rainbow.clarify`

**Description**: Refine specification through structured questioning

**Output**: Updated `spec.md` with clarifications section

**Usage**:
```
/rainbow.clarify
```

**When to Use**: Before `/rainbow.design`

**Commit Prefix**: `docs:`

---

### `/rainbow.design`

**Description**: Create technical implementation plan

**Output**: `design.md`, `research.md`, `data-model.md`, `contracts/`, `quickstart.md`

**Usage**:
```
/rainbow.design Use Vite with vanilla HTML, CSS, and JavaScript. Keep libraries minimal. Store metadata in local SQLite.
```

**Commit Prefix**: `docs:`

**Phases**:
1. Phase 0: Outline & Research
2. Phase 1: Design & Contracts

---

### `/rainbow.taskify`

**Description**: Generate actionable, dependency-ordered task list

**Output**: `tasks.md`

**Usage**:
```
/rainbow.taskify
```

**Commit Prefix**: `docs:`

**Task Format**:
```markdown
- [ ] T001 Create project structure per implementation plan
- [ ] T005 [P] Implement authentication middleware in src/middleware/auth.py
- [ ] T012 [P] [US1] Create User model in src/models/user.py
```

---

### `/rainbow.implement`

**Description**: Execute all tasks to build the feature

**Output**: Working implementation

**Usage**:
```
/rainbow.implement
```

**Commit Prefix**: Context-dependent (`feat:`, `fix:`, `test:`)

**Execution Rules**:
- Phase-by-phase
- TDD: Tests before code
- Mark completed tasks with `[X]`
- Commit after each logical unit

---

## Product-Level Commands

### `/rainbow.architect`

**Description**: Create system architecture documentation (run once per product)

**Output**: `docs/architecture.md`

**Usage**:
```
/rainbow.architect Create C4 diagrams, document tech stack decisions and architecture patterns.
```

**Commit Prefix**: `docs:`

---

### `/rainbow.standardize`

**Description**: Create coding standards (run once per product)

**Output**: `docs/standards.md`

**Usage**:
```
/rainbow.standardize Define naming conventions, file organization, and best practices.
```

**Commit Prefix**: `docs:`

---

### `/rainbow.design-e2e-test`

**Description**: Design end-to-end test specifications

**Output**: E2E test documentation

**Usage**:
```
/rainbow.design-e2e-test Create comprehensive E2E test specifications for the product.
```

**Commit Prefix**: `test:`

---

### `/rainbow.perform-e2e-test`

**Description**: Execute E2E tests and generate reports

**Output**: Test results and reports

**Usage**:
```
/rainbow.perform-e2e-test Execute the E2E test suite.
```

**Commit Prefix**: `test:`

---

## Quality & Enhancement Commands

### `/rainbow.analyze`

**Description**: Cross-artifact consistency and coverage analysis

**Usage**:
```
/rainbow.analyze
```

**When to Use**: After `/rainbow.taskify`, before `/rainbow.implement`

**Commit Prefix**: `docs:`

---

### `/rainbow.checklist`

**Description**: Generate custom quality checklists

**Output**: `checklists/*.md`

**Usage**:
```
/rainbow.checklist Create a checklist for security requirements.
```

**Commit Prefix**: `docs:`

---

### `/rainbow.tasks-to-issues`

**Description**: Convert tasks to GitHub issues

**Usage**:
```
/rainbow.tasks-to-issues
```

**Commit Prefix**: `chore:`

---

### `/rainbow.tasks-to-ado`

**Description**: Convert tasks to Azure DevOps work items

**Usage**:
```
/rainbow.tasks-to-ado
```

**Commit Prefix**: `chore:`

---

## Command Sequences

### Greenfield (New Project)

```
1. /rainbow.regulate     # Set project principles
2. /rainbow.specify      # Define feature
3. /rainbow.clarify      # Refine spec (optional)
4. /rainbow.architect    # System design (once per product)
5. /rainbow.standardize  # Coding standards (once per product)
6. /rainbow.design       # Technical plan
7. /rainbow.taskify      # Generate tasks
8. /rainbow.implement    # Build it
```

### Brownfield (Existing Project)

```
1. /rainbow.assess-context  # Analyze existing code
2. /rainbow.regulate        # Update principles
3. /rainbow.specify         # Define new feature
4. /rainbow.clarify         # Refine spec (optional)
5. /rainbow.design          # Integration plan
6. /rainbow.taskify         # Generate tasks
7. /rainbow.implement       # Add feature
```

## Handoff Points

Commands have built-in handoff suggestions:

| Command | Handoff Options |
|---------|-----------------|
| `specify` | `clarify`, `architect`, `design` |
| `clarify` | `architect`, `design` |
| `design` | `taskify`, `checklist`, `design-e2e-test` |
| `taskify` | `analyze`, `implement` |
| `implement` | `design-e2e-test`, `perform-e2e-test` |