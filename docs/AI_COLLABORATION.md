# AI Collaboration & Handoff Guide

## Overview

All atnplex repositories use a `.ai/` directory structure to enable **seamless handoffs between AI systems**: Antigravity, Jules.Google, Perplexity Pro, and GitHub Copilot.

This solves the problem where individual AI systems maintain internal, conversation-specific artifacts that are invisible to other AIs.

## Directory Structure

Every atnplex repository should have:

```
<repo-root>/.ai/
├── context/
│   ├── current-task.md         # Live task tracking
│   ├── implementation-plan.md  # Active technical plan
│   └── summary.md              # Summary of completed work
├── history/
│   ├── 2026-01-28-auth-refactor.md
│   └── 2026-01-27-docker-fix.md
├── handoff/
│   └── next-actions.md         # What needs to happen next
└── README.md                    # Usage instructions
```

**Templates**: See [`templates/ai/`](../templates/ai/) for starter templates.

## AI Usage Guidelines

### Before Starting Work

1. **Read active context**:

   ```bash
   cat .ai/context/current-task.md
   cat .ai/handoff/next-actions.md
   ```

2. **If work is in progress**: Continue from where the previous AI left off

3. **If starting new work**: Update `current-task.md` to mark your task as in-progress

### During Work

1. **Keep `current-task.md` updated** with progress (use checklist format):

   ```markdown
   ## Active Task: Fix Authentication Bug

   - [x] Identified issue in auth-functions.php
   - [/] Implementing LDAP helper functions
   - [ ] Update test suite
   - [ ] Open PR
   ```

2. **Document decisions** in `implementation-plan.md`:

   ```markdown
   ## Technical Approach

   Using centralized error logging function `logAuthError()` to standardize
   error handling across all auth plugins...
   ```

3. **Follow atnplex workflow standards**:
   - No direct commits to `main`
   - All changes via branch → PR → merge → delete branch
   - Reference SSOT workflows from `atnplex/actions`

### After Completing Work

1. **Archive the plan** to history:

   ```bash
   mv .ai/context/implementation-plan.md \
      .ai/history/$(date +%Y-%m-%d)-<task-name>.md
   ```

2. **Update summary**:

   ```markdown
   # Summary of Completed Work

   ## 2026-01-28: Authentication Refactoring

   - Restored missing LDAP helper functions
   - Implemented centralized error logging
   - Updated 4 auth plugins to use new functions
   - PR: #123 (merged)
   ```

3. **Update handoff** for next AI:

   ```markdown
   # Next Actions

   ## Priority 1: Test Coverage

   - Add unit tests for new LDAP helpers
   - Verify error logging across all plugins

   ## Priority 2: Documentation

   - Update API_DOCS.md with new functions
   ```

4. **Clear current task** if all work complete:
   ```bash
   echo "# No active tasks" > .ai/context/current-task.md
   ```

## File Templates

Templates are available in [`templates/ai/`](../templates/ai/):

- `current-task.md` - Active task tracking template
- `implementation-plan.md` - Technical planning template
- `next-actions.md` - Handoff priorities template
- `summary.md` - Completed work summary template

## Integration with Specific AIs

### Antigravity

Antigravity maintains internal artifacts in `/root/.gemini/antigravity/brain/<conversation-id>/` but **also syncs to `.ai/` directories**:

- Internal `task.md` → `.ai/context/current-task.md`
- Internal `implementation_plan.md` → `.ai/context/implementation-plan.md`
- Internal `walkthrough.md` → `.ai/context/summary.md` (when complete)

**Configuration**: When working in atnplex repositories:

1. Read `.ai/context/current-task.md` before starting work
2. Keep `.ai/context/` files updated as you work
3. After completion, archive to `.ai/history/` and update `.ai/handoff/next-actions.md`

### Jules.Google

Knowledge block configuration:

```
Before starting work in atnplex repos:
- Read .ai/context/current-task.md for active work
- Read .ai/handoff/next-actions.md for pending tasks
- Update .ai/context/ files as you make changes
- Follow atnplex workflow standard (branch → PR → merge)
```

### Perplexity Pro

Space README configuration:

```
## AI Collaboration
All atnplex repos have .ai/ directories:
- Read .ai/context/current-task.md before analysis
- Write findings to .ai/handoff/next-actions.md
- Document architecture recommendations in .ai/context/
```

### GitHub Copilot

Add to repository instructions or `.github/copilot-instructions.md`:

```
Check .ai/context/current-task.md to understand active work.
Update .ai/context/ files when making significant changes.
Follow patterns documented in .ai/history/ for consistency.
```

## Benefits

1. **Continuity**: Any AI can pick up where another left off
2. **Visibility**: All AIs see the same state of work
3. **History**: Past work is documented in `.ai/history/`
4. **Standards**: Consistent workflow across all AIs
5. **Handoffs**: Clear next actions for any AI

## Example Handoff Flow

1. **Antigravity** creates implementation plan in `.ai/context/implementation-plan.md`
2. **Antigravity** starts work, updates `.ai/context/current-task.md`
3. **User** switches to Jules.Google for dependency updates
4. **Jules.Google** reads `.ai/context/current-task.md`, sees Antigravity's work
5. **Jules.Google** completes dependency updates, updates `.ai/handoff/next-actions.md`
6. **User** returns to Antigravity
7. **Antigravity** reads `.ai/handoff/next-actions.md` and continues

No context is lost between AI systems!

## Standards Compliance

- All `.ai/` files should be in **Markdown** format
- Use **GitHub Flavored Markdown** (checklists, tables, code blocks)
- Keep files **concise and scannable**
- Use **relative file links** where possible: `[auth.php](../../api/auth.php)`
- **Commit `.ai/` directory** to the repository (it's source of truth)

## Setting Up .ai/ Directory

To initialize the `.ai/` directory structure in a repository:

1. Copy templates:

   ```bash
   mkdir -p .ai/{context,history,handoff}
   cp atnplex/actions/templates/ai/* .ai/
   ```

2. Customize `README.md` for your repository

3. Commit to version control:
   ```bash
   git add .ai/
   git commit -m "Initialize AI collaboration directory"
   ```

---

For questions or improvements to this system, open an issue in `atnplex/actions`.
