# 🚀 Project Development Guide & Safety Rules

Welcome to the team! To maintain codebase stability, ensure a clean git history, and prevent accidental deployments, this organization strictly enforces branch safety rules. 

Follow the automated setup below to configure your local environment before writing code.

---

## 🛑 Core Workflow Rules

### Rule 1: No Direct Pushes
> **Never push code directly to `main` or `dev`. Always work out of a feature branch and raise a Pull Request (PR).**
* All core branch updates must be peer-reviewed via a GitHub PR.
* Direct pushes to `main` or `dev` bypass testing and risk breaking deployment environments.

### Rule 2: Enforce Local Push Protection
Every developer must block local pushes to `main` and `dev` so that terminal mistakes are caught immediately before hitting GitHub servers.

### Rule 3: Use a Pre-Push Script Hook
You must install a local pre-push validation script. Because the `.git/hooks/` directory does not sync over GitHub, this configuration must be activated locally on your machine.

---

## 🛠️ Automated Environment Setup (Run This First)

To make things easy, we have bundled the configuration requirements into a single shell script. Follow these steps to configure your environment automatically:

### 1. Run the Auto-Setup Command
Open your terminal in the root folder of this repository and run the following command:
```bash
bash setup.sh
```

### 2. Verify it worked
Try running a mock push to `main` or `dev`:
```bash
git push origin main
```
You should see a message stating that `pushRemote is set to no_push` or a terminal alert saying `❌ Direct push to 'main' is not allowed.`

---

## 📦 Reference: The Setup Script Source Code

If you prefer to set up manually or want to inspect what the `bash setup.sh` command executes, here is the complete code. You can also manually copy this code block into a file named `setup.sh` in your project root:

```bash
#!/bin/bash

# Define text colors for terminal output
GREEN='\033[0;32m'
BLUE='\033[0;34m'
RED='\033[0;31m'
NC='\033[0m' # No Color

echo -e "BLUE============ Git Local Environment Setup ============{NC}"

# 1. Verify this is being run inside a Git repository
if [ ! -d ".git" ]; then
    echo -e "\${RED}❌ Error: This script must be run from the root of a Git repository.\${NC}"
    exit 1
fi

# 2. Configure Local Push Protections
echo -e "\n\({BLUE}[1/3] Configuring local push blocks...\){NC}"
git config branch.main.pushRemote no_push
git config branch.dev.pushRemote no_push
echo -e "\${GREEN}✓ Local push block configured for 'main' and 'dev'.\${NC}"

# 3. Create Pre-Push Hook Directory
mkdir -p .git/hooks

# 4. Write the Pre-Push Hook Script
echo -e "\n\({BLUE}[2/3] Installing pre-push hook safety check...\){NC}"
cat << 'EOF' > .git/hooks/pre-push
#!/bin/bash

# Configuration: Define branches that cannot be pushed to directly
protected_branches=("main" "dev")

# Get the current branch name
current_branch=\$(git symbolic-ref HEAD | sed 's|refs/heads/||')

# Check if the current branch is in the protected list
for branch in "\${protected_branches[@]}"; do
  if [ "\(current_branch" == "\)branch" ]; then
    echo -e "\033[0;31m❌ Direct push to '\$branch' is not allowed.\033[0m"
    echo -e "   Please create a feature branch and raise a Pull Request (PR) instead."
    exit 1
  fi
done

exit 0
EOF

# 5. Make the Hook Executable
chmod +x .git/hooks/pre-push
echo -e "\${GREEN}✓ Pre-push hook successfully written and marked as executable.\${NC}"

# 6. Success Output
echo -e "\nGREEN================== Setup Complete! =================={NC}"
echo -e "Your local repository is now protected against accidental direct pushes."
echo -e "Remember: Always branch out using BLUEgit checkout -b feature/your-feature-name{NC}\n"
```

---

## 🔄 Standard Daily Git Routine

When working on this project, please follow this layout step-by-step:

1. **Pull changes daily** to stay up to date:
   ```bash
   git checkout dev && git pull origin dev
   ```
2. **Create your feature branch** off of `dev`:
   ```bash
   git checkout -b feature/your-feature-title
   ```
3. **Commit your local updates** with descriptive messages:
   ```bash
   git commit -m "feat: add user login validation logic"
   ```
4. **Push your branch** to GitHub:
   ```bash
   git push origin feature/your-feature-title
   ```
5. **Open a PR** on GitHub comparing your feature branch against `dev`.
