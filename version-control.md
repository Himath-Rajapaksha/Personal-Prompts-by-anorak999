# GitCore Four-Tier Branching Strategy

> **IRONCLAD ENFORCEMENT** — This is the permanent, non-negotiable version control standard for this project. Any deviation requires explicit architectural review and written exception.

---

## The Rule

**NEVER push directly to `main`.** This rule is absolute — solo projects, hotfixes, documentation changes, every scenario. All code reaches `main` exclusively through the four-tier pipeline below.

---

## Four-Tier Branching Strategy

| Tier | Branch | Purpose | Source | Lifespan | Protection |
|------|--------|---------|--------|----------|------------|
| **1** | `feature/*` | One branch per feature, task, or bugfix | `dev` | Ephemeral (deleted after merge) | None |
| **2** | `dev` | Integration environment — all features merge here first | `feature/*` | Persistent | Require PR, require CI pass |
| **3** | `staging` | Pre-production mirror — release candidate validation | `dev` | Persistent | Require PR, require CI pass, require approval |
| **4** | `main` | Sacred production branch — only merged from `staging` | `staging` | Persistent | Require PR, require CI pass, require approval, require up-to-date, no direct pushes, linear history |

### Branch Naming Convention

```
feature/<issue-or-task-id>-<short-description>
```

Examples:
- `feature/42-add-login-endpoint`
- `feature/87-fix-null-pointer-checkout`
- `feature/123-update-readme-toc`

---

## Full Workflow

```
[Start] → feature/42-add-login-endpoint (from dev)
              ↓
         PR → dev (squash merge, delete feature branch)
              ↓
         PR → staging (merge commit, release candidate)
              ↓
         PR → main (merge commit, production release)
              ↓
         Tag: vX.Y.Z
```

### Step-by-Step

1. **Branch**: `git checkout dev && git pull && git checkout -b feature/<id>-<desc>`
2. **Develop**: Commit freely on the feature branch
3. **PR to Dev**: Open pull request into `dev`. CI must pass. Squash-merge.
4. **Delete Feature Branch**: Clean up after merge.
5. **PR to Staging**: From `dev` into `staging`. CI must pass. Requires code review approval. Merge commit.
6. **PR to Main**: From `staging` into `main`. CI must pass. Requires senior review approval. Must be up-to-date with `staging`. Merge commit.
7. **Tag Release**: `git tag vX.Y.Z` on `main`.

### Hotfix Exception

For production-critical fixes that cannot wait for the full pipeline:

```
[Production Bug] → feature/hotfix-<id>-<desc> (from main)
                        ↓
                   PR → staging (expedited review)
                        ↓
                   PR → main (expedited review)
                        ↓
                   Cherry-pick → dev
```

Hotfixes still bypass `dev` initially but **must** be cherry-picked back to `dev` post-deployment to prevent regression gaps.

---

## Key Takeaways

1. **`main` is sacred** — never commit directly, never force-push, never delete
2. **`dev` is the single source of truth** for ongoing work
3. **`staging` catches environment-specific issues** before production
4. **Feature branches are disposable** — squash-merge keeps history clean
5. **Every merge is reviewed** — no silent deployments
6. **Tags create auditable releases** — every production deploy maps to a tag

---

## Consequences of Violation

| Violation | Consequence |
|-----------|-------------|
| Direct push to `main` | Immediate revert + mandatory team retro |
| Merge without passing CI | Rollback + fix CI + re-merge |
| Skip tier (e.g., feature → main) | Blocked by branch protection + written explanation required |
| Delete `dev`, `staging`, or `main` | Restore from reflog + branch lock review |

---

## Required Branch Protection Rules (GitHub)

Configure these in Settings → Branches → Add rule for each branch:

### `main`
- [x] Require pull request before merging
- [x] Require approvals (2 for production)
- [x] Dismiss stale reviews
- [x] Require status checks to pass (CI)
- [x] Require branches to be up-to-date
- [x] Require linear history
- [x] Include administrators
- [x] Restrict push access (only CI/CD bots)

### `staging`
- [x] Require pull request before merging
- [x] Require approvals (1 for staging)
- [x] Require status checks to pass (CI)
- [x] Include administrators

### `dev`
- [x] Require pull request before merging
- [x] Require status checks to pass (CI)
- [x] Include administrators

---

## CI/CD Integration

Every PR to `dev`, `staging`, and `main` must run:

1. **Lint** — code style enforcement
2. **Type check** — static type validation
3. **Unit tests** — `npm test` / `pytest` / equivalent
4. **Build** — verify artifact compiles
5. **Security scan** — dependency audit + SAST

Add these as required status checks in branch protection rules.

---

## Migration Plan

### Current State Analysis

| Aspect | Current | Target |
|--------|---------|--------|
| Default branch | `Home` | `main` |
| Active branches | `Home` only | `main`, `staging`, `dev` |
| Protection rules | None | Full protection per tier |
| Tags | None | Semantic versioning |

### Migration Steps

1. **Rename default branch**: `Home` → `main`
   ```bash
   git branch -m Home main
   git push origin -u main
   ```

2. **Create `staging` and `dev` from `main`**
   ```bash
   git checkout main
   git checkout -b staging
   git push origin staging
   git checkout main
   git checkout -b dev
   git push origin dev
   ```

3. **Update default branch on remote** (GitHub)
   - Settings → Branches → Default branch → Change from `Home` to `main`

4. **Delete old `Home` branch on remote**
   ```bash
   git push origin --delete Home
   ```

5. **Configure branch protection rules** (see table above)

6. **Add `.gitignore`** if missing
   ```bash
   # Standard Node.js/Python gitignore as applicable
   ```

7. **Tag current state as initial release**
   ```bash
   git tag v0.1.0
   git push origin --tags
   ```

---

## Ready-to-Run Command List

Execute in order:

```bash
# 1. Rename default branch
git branch -m Home main

# 2. Create tier branches
git checkout -b staging
git push origin staging
git checkout main
git checkout -b dev
git push origin dev

# 3. Push renamed main
git push origin -u main

# 4. Delete old remote branch
git push origin --delete Home

# 5. Tag initial release
git tag v0.1.0
git push origin --tags

# 6. Set upstream tracking
git branch -u origin/main main
git branch -u origin/dev dev
git branch -u origin/staging staging
```

---

## Compliance Verification

Run this periodically to audit branch hygiene:

```bash
#!/bin/bash
echo "=== GitCore Compliance Check ==="
echo ""

# Check for direct pushes to main (via reflog)
if git reflog show main | grep -q "commit.*main"; then
  echo "❌ WARNING: Direct commits detected on main"
else
  echo "✅ No direct commits on main"
fi

# Check feature branch cleanup
STALE=$(git branch --merged dev | grep "feature/" | head -5)
if [ -n "$STALE" ]; then
  echo "⚠️  Stale feature branches (merged but not deleted):"
  echo "$STALE"
else
  echo "✅ No stale feature branches"
fi

# Verify branch existence
for branch in main staging dev; do
  if git show-ref --verify --quiet "refs/heads/$branch"; then
    echo "✅ $branch exists"
  else
    echo "❌ $branch missing"
  fi
done

echo ""
echo "=== Check Complete ==="
```

---

*GitCore Four-Tier Branching Strategy v1.0 — Enforced from 2026-07-12*
