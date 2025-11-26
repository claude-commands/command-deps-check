# command-deps-check

A Claude Code slash command for comprehensive dependency analysis.

## Installation

```bash
# Clone to your preferred location
git clone git@github.com:claude-commands/command-deps-check.git <clone-path>/command-deps-check

# Symlink (use full path to cloned repo)
ln -s <clone-path>/command-deps-check/deps-check.md ~/.claude/commands/deps-check.md
```

## Usage

```text
/deps-check              # Full analysis
/deps-check --security   # Security vulnerabilities only
/deps-check --outdated   # Outdated packages only
/deps-check --unused     # Unused dependencies only
```

## What it does

1. Detects package manager (npm, go mod, pip, cargo)
2. Checks for security vulnerabilities with severity levels
3. Finds outdated packages (major/minor/patch)
4. Detects unused dependencies
5. Generates upgrade recommendations

## Output Format

```markdown
# Dependency Analysis

## Security Summary
| Severity | Count | Action |
|----------|-------|--------|
| Critical | 1 | Immediate fix |
| High | 3 | Fix this sprint |

### Critical: lodash < 4.17.21
- **CVE**: CVE-2021-23337
- **Fix**: `npm install lodash@4.17.21`

## Outdated Packages
| Package | Current | Latest |
|---------|---------|--------|
| react | 17.0.2 | 18.2.0 |

## Unused Dependencies
| Package | Size |
|---------|------|
| moment | 290KB |
```

## Package Manager Support

| Manager | Security | Outdated | Unused |
|---------|----------|----------|--------|
| npm/yarn | npm audit | npm outdated | Import search |
| Go | govulncheck | go list -m -u | go mod tidy |
| pip | pip-audit | pip list --outdated | Import search |
| Cargo | cargo-audit | cargo outdated | Import search |

## Requirements

- Git repository with dependencies
- Claude Code with Opus 4.5 model access

## Updates

```bash
cd <clone-path>/command-deps-check && git pull
```
