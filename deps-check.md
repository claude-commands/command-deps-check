---
argument-hint: "[--security|--outdated|--unused]"
description: "Comprehensive dependency analysis"
model: claude-opus-4-5-20251101
allowed-tools: ["Bash", "Read", "Glob", "Grep", "WebSearch", "AskUserQuestion"]
---

**If `$ARGUMENTS` is empty or not provided:**

Run a comprehensive dependency analysis.

**Usage:** `/deps-check [options]`

**Examples:**

- `/deps-check` - Full analysis (security, outdated, unused)
- `/deps-check --security` - Security vulnerabilities only
- `/deps-check --outdated` - Outdated packages only
- `/deps-check --unused` - Unused dependencies only

**Workflow:**

1. Identify package manager (npm, go mod, pip, cargo)
2. Check for security vulnerabilities
3. Find outdated packages
4. Detect unused dependencies
5. Generate upgrade recommendations

Proceed with full analysis.

---

**If `$ARGUMENTS` is provided:**

Analyze dependencies based on specified focus area.

## Configuration

- **Focus**: `$ARGUMENTS`
  - `--security`: Security vulnerabilities only
  - `--outdated`: Outdated packages only
  - `--unused`: Unused dependencies only
  - (none): All checks

## Steps

1. **Detect Package Manager**

   Check for:
   - `package.json` → npm/yarn/pnpm
   - `go.mod` → Go modules
   - `requirements.txt` / `pyproject.toml` → pip/poetry
   - `Cargo.toml` → Cargo (Rust)
   - `Gemfile` → Bundler (Ruby)

2. **Security Vulnerability Check**

   **Node.js**:

   ```bash
   npm audit --json 2>/dev/null || yarn audit --json 2>/dev/null
   ```text

   **Go**:

   ```bash
   go list -m -json all | go run golang.org/x/vuln/cmd/govulncheck@latest
   # Or check: govulncheck ./...
   ```text

   **Python**:

   ```bash
   pip-audit --format=json 2>/dev/null
   # Or: safety check --json
   ```text

   **Rust**:

   ```bash
   cargo audit --json 2>/dev/null
   ```text

   Parse results and categorize by severity:
   - **Critical**: Remote code execution, data breach
   - **High**: Privilege escalation, DoS
   - **Medium**: Information disclosure
   - **Low**: Minor issues

3. **Outdated Package Check**

   **Node.js**:

   ```bash
   npm outdated --json 2>/dev/null
   ```text

   **Go**:

   ```bash
   go list -m -u -json all 2>/dev/null
   ```text

   **Python**:

   ```bash
   pip list --outdated --format=json 2>/dev/null
   ```text

   Categorize updates:
   - **Major**: Breaking changes likely (1.x → 2.x)
   - **Minor**: New features (1.1 → 1.2)
   - **Patch**: Bug fixes (1.1.1 → 1.1.2)

4. **Unused Dependency Check**

   **Node.js**:

   ```bash
   # Check each dependency in package.json
   # Search for imports in codebase
   rg "from ['\"]package-name" --type ts --type js
   rg "require\(['\"]package-name" --type js
   ```text

   **Go**:

   ```bash
   # go mod tidy will report unused
   go mod tidy -v 2>&1 | grep "unused"
   ```text

   **Python**:

   ```bash
   # Check imports vs requirements
   rg "^import pkg|from pkg import" --type py
   ```text

5. **Analyze Dependencies**

   For each dependency, gather:
   - Current version installed
   - Latest version available
   - Last publish date
   - Known vulnerabilities
   - Usage in codebase (import count)
   - Bundle size impact (for JS)

6. **Generate Report**

   ```markdown
   # Dependency Analysis

   **Project**: my-app
   **Package Manager**: npm
   **Total Dependencies**: 45 (12 direct, 33 transitive)

   ## Security Summary

   | Severity | Count | Action |
   |----------|-------|--------|
   | Critical | 1 | Immediate fix required |
   | High | 3 | Fix this sprint |
   | Medium | 5 | Plan to fix |
   | Low | 2 | Optional |

   ### Critical Vulnerabilities

   #### lodash < 4.17.21 (CVE-2021-23337)
   - **Severity**: Critical (9.8)
   - **Type**: Prototype Pollution
   - **Fix**: Upgrade to 4.17.21+
   - **Impact**: Remote code execution possible
   ```bash
   npm install lodash@4.17.21
   ```text

   ## Outdated Packages

   ### Major Updates (Breaking Changes Likely)

   | Package | Current | Latest | Age |
   |---------|---------|--------|-----|
   | react | 17.0.2 | 18.2.0 | 1 year |
   | webpack | 4.46.0 | 5.88.0 | 2 years |

   ### Minor Updates (New Features)

   | Package | Current | Latest |
   |---------|---------|--------|
   | axios | 1.4.0 | 1.6.0 |
   | typescript | 5.0.0 | 5.3.0 |

   ### Patch Updates (Bug Fixes)

   | Package | Current | Latest |
   |---------|---------|--------|
   | eslint | 8.50.0 | 8.52.0 |

   ## Unused Dependencies

   These packages appear unused:

   | Package | Size | Last Import |
   |---------|------|-------------|
   | moment | 290KB | None found |
   | lodash | 70KB | None found |
   | uuid | 12KB | None found |

   **Potential savings**: ~372KB

   ## Recommendations

   ### Immediate Actions

   1. Fix critical vulnerability in lodash
   2. Remove unused dependencies
   3. Update patch-level dependencies

   ### Planned Updates

   1. Plan React 18 migration (breaking changes)
   2. Update webpack when ready for v5

   ### Commands

   ```bash
   # Fix critical vulnerabilities
   npm audit fix

   # Remove unused
   npm uninstall moment lodash uuid

   # Update safe packages
   npm update

   # Update specific package
   npm install axios@latest
   ```text

   ```text

7. **Provide Upgrade Path**

   For major updates, outline:
   - Breaking changes to expect
   - Migration guide links
   - Estimated effort

## Package Manager Support

| Manager | Security | Outdated | Unused |
|---------|----------|----------|--------|
| npm/yarn | `npm audit` | `npm outdated` | Import search |
| Go | govulncheck | `go list -m -u` | `go mod tidy` |
| pip | pip-audit | `pip list --outdated` | Import search |
| Cargo | cargo-audit | `cargo outdated` | Import search |

## Notes

- Security checks require network access
- Some tools may need to be installed first
- Transitive dependencies matter too
- Consider lock file implications
- Test thoroughly after updates
