# GitHub Issue Fixer

Claude Code plugin for autonomous GitHub issue fixing. Includes a single-issue TDD agent and a parallel multi-issue fixer with conflict-aware batching.

## Skills

### `github-issue-fixer:fix`

Autonomous loop: triage → claim → worktree → TDD (RED/GREEN) → browser verify → merge → close. Picks the top-priority open issue automatically; no confirmation needed.

### `github-issue-fixer:fix-all`

Spawns up to 6 isolated subagents in a single message, each fixing one issue independently and opening a PR. Auto-selects the lowest-conflict batch — no user input required.

## Requirements

- `gh` CLI authenticated to your GitHub repo
- Issues use `status:in-progress` label (created automatically on first use)
- Repo issue titles follow [Conventional Commits](https://www.conventionalcommits.org/) for best conflict detection: `feat(scope): verb …`

## Installation

```bash
# From a local directory
claude plugin install /path/to/github-issue-fixer

# Test without installing
claude --plugin-dir /path/to/github-issue-fixer
```

## Usage

```
/github-issue-fixer:fix              # Fix top-priority open issue
/github-issue-fixer:fix-all          # Fix up to 6 in parallel (auto-selected)
/github-issue-fixer:fix-all 42 57 103 # Fix specific issues in parallel
```

## Conflict detection

`fix-all` derives a conflict zone from each issue title using the conventional-commit scope and first verb:

| Title | Zone |
|-------|------|
| `feat(mobile): owner screens` | `mobile/owner` |
| `feat(mobile): tenant calendar` | `mobile/tenant` |
| `fix(auth): token expiry` | `auth/token` |
| `refactor: remove unused import` | `refactor/remove` |

Issues in the same zone are assumed to touch the same files and will not be batched together. Only one issue per zone is selected per run.

## Plugin structure

```
github-issue-fixer/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   ├── fix/
│   │   └── SKILL.md
│   └── fix-all/
│       └── SKILL.md
└── README.md
```
