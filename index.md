# 🚀 Branch Protection & Workflow Guide

To maintain a stable codebase, this repository enforces a strict branch protection policy. Please follow these steps to configure your local environment before pushing any code.

---

## 🛑 Rule 1: No Direct Pushes

> **Never push directly to `main` or `dev`. Always push to your own feature branch and raise a Pull Request (PR).**

All changes must go through the standard code review process. Direct pushes to core branches are strictly forbidden.

---

## 💻 Rule 2: Local Push Protection

To avoid accidental mistakes, every developer must configure local blocks for the protected branches. Run the following commands inside your local repository terminal:

```bash
# Prevent accidental push to main
git config branch.main.pushRemote no_push

# Prevent accidental push to dev
git config branch.dev.pushRemote no_push
```

### How it works:
If you accidentally attempt to push directly to a protected branch, Git will immediately block the operation locally:
```bash
git push origin main
# Error: pushRemote is set to no_push — blocked locally!
```

---

## 🪝 Rule 3: Enforce with Pre-Push Hooks

Because Git does not automatically sync the `.git/hooks/` directory across team machines, you must manually install this safety script locally.

### 1. Create the hook file
Create a file on your local machine at `.git/hooks/pre-push` and paste the following script:

```bash
#!/bin/bash

protected_branches=("main" "dev")
current_branch=\$(git symbolic-ref HEAD | sed 's|refs/heads/||')

for branch in "\${protected_branches[@]}"; do
  if [ "\(current_branch" == "\)branch" ]; then
    echo "❌ Direct push to '\$branch' is not allowed."
    echo "   Create a feature branch and raise a PR instead."
    exit 1
  fi
done

exit 0
```

### 2. Make it executable
Run this command in your terminal to ensure the script has permission to execute:

```bash
chmod +x .git/hooks/pre-push
```

---

## 🔄 Recommended Workflow Reminder

1. **Pull** latest changes: `git checkout dev && git pull origin dev`
2. **Branch** out: `git checkout -b feature/your-feature-name`
3. **Commit** changes: `git commit -m "Add short description"`
4. **Push** safely: `git push origin feature/your-feature-name`
5. **Open a PR** against the target branch on GitHub.
