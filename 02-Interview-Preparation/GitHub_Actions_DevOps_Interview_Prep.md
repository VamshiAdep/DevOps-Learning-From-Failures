# 🚀 GitHub Actions — Complete Interview Preparation
### For DevOps Freshers & Intern Roles

> **Covers:** Core Concepts · YAML Syntax · Practical Workflows · Real Interview Questions · Hands-on Examples

---

## 📋 Table of Contents

1. [What is GitHub Actions?](#1-what-is-github-actions)
2. [Core Concepts & Terminology](#2-core-concepts--terminology)
3. [Workflow YAML — Full Anatomy](#3-workflow-yaml--full-anatomy)
4. [Triggers (Events)](#4-triggers-events)
5. [Jobs, Steps & Runners](#5-jobs-steps--runners)
6. [Environment Variables & Secrets](#6-environment-variables--secrets)
7. [Practical Workflow Examples](#7-practical-workflow-examples)
8. [Caching & Artifacts](#8-caching--artifacts)
9. [Matrix Builds](#9-matrix-builds)
10. [Reusable Workflows & Composite Actions](#10-reusable-workflows--composite-actions)
11. [GitHub Actions vs Other CI/CD Tools](#11-github-actions-vs-other-cicd-tools)
12. [Most Expected Interview Questions](#12-most-expected-interview-questions)
13. [Scenario-Based Questions](#13-scenario-based-questions)
14. [Quick Revision Cheatsheet](#14-quick-revision-cheatsheet)

---

## 1. What is GitHub Actions?

GitHub Actions is a **CI/CD (Continuous Integration / Continuous Deployment) platform** built directly into GitHub. It allows you to automate software workflows — like building, testing, and deploying code — whenever something happens in your repository.

### Why GitHub Actions?
- **Free for public repos** (2,000 minutes/month for private on free tier)
- **No external CI server needed** — everything lives inside GitHub
- **YAML-based configuration** — easy to version control
- **Huge marketplace** — 20,000+ pre-built actions available
- **Matrix builds** — test across multiple OS/language versions simultaneously

### Real-World Analogy
Think of GitHub Actions like an **automated assembly line in a factory**:
- A worker arrives (code is pushed) → the assembly line starts
- Each station (job) does a specific task (test, build, deploy)
- If any station fails, the line stops and you get notified

---

## 2. Core Concepts & Terminology

| Term | Definition | Example |
|------|-----------|---------|
| **Workflow** | An automated process defined in a YAML file | `ci.yml`, `deploy.yml` |
| **Event/Trigger** | What starts the workflow | `push`, `pull_request`, `schedule` |
| **Job** | A set of steps that run on the same runner | `build`, `test`, `deploy` |
| **Step** | An individual task inside a job | Run a command, use an action |
| **Action** | A reusable unit of code (from marketplace or custom) | `actions/checkout@v4` |
| **Runner** | The server/machine that executes jobs | `ubuntu-latest`, `windows-latest` |
| **Artifact** | Files saved from a workflow run | Build output, test reports |
| **Secret** | Encrypted environment variable | API keys, passwords |
| **Context** | Objects containing workflow information | `github`, `env`, `secrets` |
| **Expression** | Dynamic values evaluated at runtime | `${{ github.actor }}` |

---

## 3. Workflow YAML — Full Anatomy

All workflow files go inside `.github/workflows/` directory.

```yaml
# .github/workflows/ci.yml

name: CI Pipeline                     # Display name in GitHub UI

on:                                   # TRIGGER — when to run
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

env:                                  # GLOBAL environment variables
  NODE_VERSION: '18'
  APP_NAME: 'my-app'

jobs:                                 # One or more jobs

  build:                              # Job ID (your custom name)
    name: Build Application           # Display name
    runs-on: ubuntu-latest            # Runner OS

    steps:
      - name: Checkout code           # Step name
        uses: actions/checkout@v4     # Use a pre-built action

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:                         # Inputs to the action
          node-version: ${{ env.NODE_VERSION }}

      - name: Install dependencies
        run: npm install              # Run shell command

      - name: Run tests
        run: npm test

      - name: Build project
        run: npm run build

  deploy:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: build                      # Wait for 'build' job to pass
    if: github.ref == 'refs/heads/main'   # Only on main branch

    steps:
      - name: Deploy
        run: echo "Deploying..."
        env:                          # Step-level env variable
          API_KEY: ${{ secrets.API_KEY }}
```

### File Location Rules
```
your-repo/
├── .github/
│   └── workflows/
│       ├── ci.yml          ✅ Picked up automatically
│       ├── deploy.yml      ✅ Picked up automatically
│       └── pr-checks.yml   ✅ Picked up automatically
├── src/
└── README.md
```

---

## 4. Triggers (Events)

### Most Common Triggers

```yaml
on:
  # Trigger on push to specific branches
  push:
    branches:
      - main
      - 'release/*'     # Wildcard pattern
    paths:
      - 'src/**'        # Only if files in src/ changed
    tags:
      - 'v*'            # Only on version tags like v1.0.0

  # Trigger on pull requests
  pull_request:
    branches: [ main ]
    types: [ opened, synchronize, reopened ]

  # Scheduled trigger (cron syntax)
  schedule:
    - cron: '0 9 * * 1'    # Every Monday at 9 AM UTC

  # Manual trigger from GitHub UI
  workflow_dispatch:
    inputs:
      environment:
        description: 'Deployment environment'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production

  # Trigger from another workflow
  workflow_call:

  # When a release is created
  release:
    types: [ published ]

  # When an issue is opened
  issues:
    types: [ opened ]
```

### Cron Syntax (Important for Interviews!)

```
┌───────────── minute (0-59)
│ ┌───────────── hour (0-23)
│ │ ┌───────────── day of month (1-31)
│ │ │ ┌───────────── month (1-12)
│ │ │ │ ┌───────────── day of week (0-6, Sun=0)
│ │ │ │ │
* * * * *

'0 0 * * *'     → Every day at midnight
'0 9 * * 1-5'   → Every weekday at 9 AM
'*/15 * * * *'  → Every 15 minutes
'0 0 1 * *'     → First day of every month
```

---

## 5. Jobs, Steps & Runners

### Job Dependencies

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Linting code..."

  test:
    runs-on: ubuntu-latest
    needs: lint          # Runs after lint
    steps:
      - run: echo "Running tests..."

  build:
    runs-on: ubuntu-latest
    needs: [lint, test]  # Runs after BOTH lint and test
    steps:
      - run: echo "Building..."

  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - run: echo "Deploying..."
```

### Parallel vs Sequential Jobs

```
Parallel (no needs):          Sequential (with needs):
  lint ──┐                      lint → test → build → deploy
  test ──┤─→ build → deploy
  scan ──┘
```

### Runners Available

```yaml
runs-on: ubuntu-latest      # Ubuntu Linux (most common, cheapest)
runs-on: ubuntu-22.04       # Specific Ubuntu version
runs-on: windows-latest     # Windows Server
runs-on: macos-latest       # macOS
runs-on: self-hosted        # Your own server/machine
```

### Conditional Steps

```yaml
steps:
  - name: Only on main branch
    if: github.ref == 'refs/heads/main'
    run: echo "This is main!"

  - name: Only on failure
    if: failure()
    run: echo "Something went wrong"

  - name: Always run (even if previous step failed)
    if: always()
    run: echo "Cleanup step"

  - name: Only on success
    if: success()
    run: echo "All good!"

  - name: Check env variable
    if: env.MY_VAR == 'true'
    run: echo "Variable is set"
```

---

## 6. Environment Variables & Secrets

### Variable Hierarchy (Priority Order)

```
Step-level env > Job-level env > Workflow-level env > GitHub defaults
```

```yaml
env:
  GLOBAL_VAR: "workflow level"      # Available to all jobs

jobs:
  example:
    env:
      JOB_VAR: "job level"          # Available to all steps in this job

    steps:
      - name: Step with its own env
        env:
          STEP_VAR: "step level"    # Only in this step
        run: |
          echo $GLOBAL_VAR
          echo $JOB_VAR
          echo $STEP_VAR
```

### Default GitHub Variables (Must Know!)

```yaml
${{ github.sha }}           # Full commit hash
${{ github.ref }}           # Branch/tag ref (refs/heads/main)
${{ github.actor }}         # User who triggered the workflow
${{ github.repository }}    # owner/repo-name
${{ github.event_name }}    # Event that triggered (push, pull_request)
${{ github.run_number }}    # Workflow run number
${{ github.workspace }}     # Path to the repo on runner
```

### Secrets — Storing Sensitive Data

```yaml
# In your workflow, reference like this:
steps:
  - name: Deploy
    env:
      AWS_ACCESS_KEY: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    run: aws s3 sync ./dist s3://my-bucket

# NEVER do this — secrets will be exposed in logs!
# run: echo ${{ secrets.MY_SECRET }}   ❌ BAD
```

**Where to add secrets:** Repository → Settings → Secrets and Variables → Actions → New repository secret

### Levels of Secrets

| Level | Scope | Use Case |
|-------|-------|----------|
| Repository Secret | Single repo only | Project-specific keys |
| Environment Secret | Specific environments (prod/staging) | Deployment credentials |
| Organization Secret | All repos in org | Shared team credentials |

---

## 7. Practical Workflow Examples

### Example 1: Node.js CI Pipeline

```yaml
name: Node.js CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Use Node.js 18
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'              # Cache npm dependencies

      - name: Install dependencies
        run: npm ci                 # Cleaner than npm install for CI

      - name: Run linter
        run: npm run lint

      - name: Run tests
        run: npm test -- --coverage

      - name: Upload coverage report
        uses: actions/upload-artifact@v4
        with:
          name: coverage-report
          path: coverage/
```

### Example 2: Python CI + Docker Build

```yaml
name: Python App CI/CD

on:
  push:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run tests
        run: pytest tests/ -v

  docker-build:
    runs-on: ubuntu-latest
    needs: test                     # Only build if tests pass

    steps:
      - uses: actions/checkout@v4

      - name: Log in to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          push: true
          tags: myuser/myapp:${{ github.sha }}
```

### Example 3: Deploy to AWS S3 (Static Site)

```yaml
name: Deploy to S3

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Build site
        run: |
          npm install
          npm run build

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1

      - name: Deploy to S3
        run: aws s3 sync ./dist s3://${{ secrets.S3_BUCKET }} --delete

      - name: Invalidate CloudFront cache
        run: |
          aws cloudfront create-invalidation \
            --distribution-id ${{ secrets.CF_DIST_ID }} \
            --paths "/*"
```

### Example 4: PR Validation Workflow

```yaml
name: PR Checks

on:
  pull_request:
    branches: [ main ]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Check PR title format
        run: |
          PR_TITLE="${{ github.event.pull_request.title }}"
          if [[ ! $PR_TITLE =~ ^(feat|fix|docs|chore|refactor): ]]; then
            echo "PR title must start with: feat|fix|docs|chore|refactor"
            exit 1
          fi

      - name: Run tests
        run: npm test

      - name: Post comment on failure
        if: failure()
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '❌ CI checks failed. Please fix the issues.'
            })
```

---

## 8. Caching & Artifacts

### Caching Dependencies (Speed Up Builds)

```yaml
- name: Cache Node modules
  uses: actions/cache@v4
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-

# The key concept:
# - If key matches exactly → Cache HIT, restore instantly
# - If key doesn't match → Check restore-keys (partial match)
# - If nothing matches → Cache MISS, build from scratch, then save cache
```

### Common Cache Paths

```yaml
# Node.js
path: ~/.npm
key: node-${{ hashFiles('package-lock.json') }}

# Python (pip)
path: ~/.cache/pip
key: python-${{ hashFiles('requirements.txt') }}

# Maven (Java)
path: ~/.m2
key: maven-${{ hashFiles('pom.xml') }}

# Gradle
path: ~/.gradle/caches
key: gradle-${{ hashFiles('build.gradle') }}
```

### Artifacts — Sharing Files Between Jobs

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Build
        run: npm run build

      - name: Upload build output
        uses: actions/upload-artifact@v4
        with:
          name: build-files          # Artifact name
          path: dist/                # What to upload
          retention-days: 5         # Auto-delete after 5 days

  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Download build output
        uses: actions/download-artifact@v4
        with:
          name: build-files
          path: dist/

      - name: Deploy
        run: ./deploy.sh
```

**Cache vs Artifact — Key Difference:**

| Feature | Cache | Artifact |
|---------|-------|----------|
| Purpose | Speed up builds | Share files between jobs |
| Scope | Across workflow runs | Within same workflow run |
| Example | `node_modules`, pip packages | Build output, test reports |
| Retention | Up to 7 days (default) | Up to 90 days |

---

## 9. Matrix Builds

Test your code across multiple versions/platforms simultaneously.

```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node: [16, 18, 20]
        # This creates 3 × 3 = 9 parallel jobs!

      fail-fast: false    # Don't cancel other matrix jobs if one fails
      max-parallel: 4     # Limit concurrent jobs

    steps:
      - uses: actions/checkout@v4

      - name: Use Node ${{ matrix.node }} on ${{ matrix.os }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}

      - run: npm test

  # Exclude specific combinations
  test-custom:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        python: ['3.9', '3.10', '3.11']
        exclude:
          - os: windows-latest
            python: '3.9'         # Skip Python 3.9 on Windows
        include:
          - os: ubuntu-latest
            python: '3.12'        # Add extra combination
```

---

## 10. Reusable Workflows & Composite Actions

### Reusable Workflow (Call one workflow from another)

```yaml
# .github/workflows/reusable-deploy.yml
on:
  workflow_call:
    inputs:
      environment:
        required: true
        type: string
    secrets:
      API_KEY:
        required: true

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to ${{ inputs.environment }}
        run: echo "Deploying to ${{ inputs.environment }}"
        env:
          API_KEY: ${{ secrets.API_KEY }}

---

# .github/workflows/production.yml  (caller)
jobs:
  call-deploy:
    uses: ./.github/workflows/reusable-deploy.yml
    with:
      environment: production
    secrets:
      API_KEY: ${{ secrets.PROD_API_KEY }}
```

### Composite Action (Custom reusable action)

```yaml
# .github/actions/setup-env/action.yml
name: 'Setup Environment'
description: 'Sets up Node.js with caching'
inputs:
  node-version:
    description: 'Node version'
    default: '18'

runs:
  using: 'composite'
  steps:
    - uses: actions/setup-node@v4
      with:
        node-version: ${{ inputs.node-version }}
        cache: 'npm'
    - run: npm ci
      shell: bash

---

# Using it in a workflow:
steps:
  - uses: ./.github/actions/setup-env
    with:
      node-version: '20'
```

---

## 11. GitHub Actions vs Other CI/CD Tools

| Feature | GitHub Actions | Jenkins | GitLab CI | CircleCI |
|---------|---------------|---------|-----------|----------|
| **Hosting** | Cloud (GitHub) | Self-hosted | Cloud/Self | Cloud |
| **Config file** | YAML | Jenkinsfile (Groovy) | YAML | YAML |
| **Setup effort** | Zero | High | Medium | Low |
| **Marketplace** | 20,000+ actions | Plugins | Templates | Orbs |
| **Free tier** | 2000 min/month | Free (self-hosted) | 400 min/month | 6000 min/month |
| **Best for** | GitHub repos | Enterprise/complex | GitLab users | Fast builds |
| **Learning curve** | Low | High | Medium | Medium |

---

## 12. Most Expected Interview Questions

### 🟢 Basic Level (Freshers / Interns)

**Q1: What is GitHub Actions and what problem does it solve?**

> GitHub Actions is a CI/CD automation platform built into GitHub. It solves the problem of manual, repetitive tasks in software development — like running tests every time someone pushes code, building Docker images, or deploying to production. Before tools like this, developers had to do these steps manually or set up separate CI servers like Jenkins.

---

**Q2: What is a workflow in GitHub Actions?**

> A workflow is an automated process defined in a YAML file stored in `.github/workflows/`. It contains one or more jobs that run in response to events. For example, a workflow can run unit tests whenever a pull request is opened.

---

**Q3: What is the difference between a Job and a Step?**

> A **Job** is a collection of Steps that run on the same runner machine. Jobs run in parallel by default (unless you use `needs`). A **Step** is an individual unit of work inside a job — it's either a shell command (`run`) or a pre-built action (`uses`). Steps within a job always run sequentially.

---

**Q4: What are GitHub Actions Runners? Name the types.**

> A Runner is a server/machine that executes the jobs in a workflow. Types:
> - **GitHub-hosted runners**: Managed by GitHub. Options: `ubuntu-latest`, `windows-latest`, `macos-latest`. Free for public repos.
> - **Self-hosted runners**: Your own machines (on-prem or cloud VM) registered with GitHub. Used for custom hardware, private networks, or cost savings.

---

**Q5: What is `actions/checkout` and why is it always the first step?**

> `actions/checkout@v4` is a pre-built action from the GitHub marketplace that clones your repository onto the runner. Without it, the runner has a blank machine with no code. It's always the first step because all subsequent steps (install, build, test) need the code to work with.

---

**Q6: How do you store sensitive data like API keys in GitHub Actions?**

> Using **GitHub Secrets**. You add secrets via Repository → Settings → Secrets and Variables → Actions. In the workflow, you reference them using `${{ secrets.SECRET_NAME }}`. Secrets are masked in logs and never exposed in plain text.

---

**Q7: What is `needs` in GitHub Actions?**

> `needs` creates a dependency between jobs. A job with `needs: [build, test]` will only start after both `build` and `test` jobs complete successfully. Without `needs`, all jobs run in parallel.

---

**Q8: What is the difference between `run` and `uses` in a step?**

> - `run` executes a shell command (bash, powershell, etc.) directly: `run: npm install`
> - `uses` calls a pre-built action from the marketplace or a local path: `uses: actions/checkout@v4`

---

**Q9: What triggers can start a GitHub Actions workflow?**

> Common triggers include:
> - `push` — when code is pushed to a branch
> - `pull_request` — when a PR is opened/updated
> - `schedule` — on a cron schedule (e.g., nightly builds)
> - `workflow_dispatch` — manually triggered from GitHub UI
> - `release` — when a GitHub release is published
> - `workflow_call` — triggered by another workflow

---

**Q10: What is `workflow_dispatch`?**

> `workflow_dispatch` allows you to manually trigger a workflow from the GitHub UI (or API). You can also define input parameters so the person running it can pass values like environment name, version number, etc.

---

### 🟡 Intermediate Level

**Q11: What is caching in GitHub Actions and how does it work?**

> Caching saves dependencies between workflow runs to avoid re-downloading them. The `actions/cache` action stores a directory with a key. On the next run:
> - If the key matches → cache is restored (cache hit)
> - If not → dependencies are downloaded fresh, then saved with the new key
>
> Example: caching `node_modules` using `hashFiles('package-lock.json')` as the key ensures cache is invalidated when dependencies change.

---

**Q12: What is the difference between Artifacts and Cache?**

> - **Cache** persists data *across* workflow runs to speed up builds (e.g., npm packages). It's shared across runs.
> - **Artifacts** share files *within* a single workflow run between jobs (e.g., passing build output from build job to deploy job). They're also available for download from the GitHub UI.

---

**Q13: What is a Matrix Strategy? When would you use it?**

> Matrix strategy runs a job multiple times with different configurations in parallel. You'd use it to:
> - Test across multiple OS (Linux, Mac, Windows)
> - Test with multiple language versions (Node 16, 18, 20)
> - Test across multiple environments
>
> It automatically creates a combination of all values — 3 OS × 3 versions = 9 parallel jobs.

---

**Q14: How do you pass data between steps in the same job?**

> Using `GITHUB_OUTPUT`. Steps can write key-value pairs to this file, and later steps can read them via `${{ steps.step-id.outputs.key }}`.
> ```yaml
> - name: Set output
>   id: my-step
>   run: echo "result=hello" >> $GITHUB_OUTPUT
>
> - name: Read output
>   run: echo ${{ steps.my-step.outputs.result }}
> ```

---

**Q15: What is `GITHUB_ENV` and `GITHUB_OUTPUT`?**

> - `GITHUB_ENV`: Appending `KEY=value` to this file sets an environment variable available to all subsequent steps in the job.
> - `GITHUB_OUTPUT`: Appending `KEY=value` sets a step output that can be referenced by later steps using `${{ steps.id.outputs.KEY }}`.

---

**Q16: What is `fail-fast` in a matrix strategy?**

> `fail-fast: true` (default) means if any job in the matrix fails, GitHub cancels all remaining matrix jobs. Setting `fail-fast: false` lets all matrix jobs run to completion regardless of failures — useful when you want full test results across all platforms.

---

**Q17: How do you run a step only on a specific branch?**

> Using the `if` conditional with `github.ref`:
> ```yaml
> - name: Deploy to production
>   if: github.ref == 'refs/heads/main'
>   run: ./deploy.sh
> ```

---

**Q18: What are Environments in GitHub Actions?**

> Environments represent deployment targets (staging, production). They let you:
> - Add protection rules (require reviewers before deploying)
> - Set environment-specific secrets
> - Add wait timers before deployment
>
> ```yaml
> jobs:
>   deploy:
>     environment: production   # Uses 'production' environment secrets
> ```

---

**Q19: How do you make a workflow reusable?**

> Using `workflow_call` as the trigger. This lets other workflows call it like a function and pass inputs and secrets. The reusable workflow lives in `.github/workflows/` and is called with `uses: ./.github/workflows/deploy.yml`.

---

**Q20: What is a Composite Action?**

> A composite action groups multiple steps into a single reusable action defined in `action.yml`. Unlike reusable workflows (which are full workflow files), composite actions are more granular and can be shared across different workflows or even different repositories.

---

### 🔴 Advanced / Scenario Questions

**Q21: How do you prevent a workflow from running on draft PRs?**

```yaml
on:
  pull_request:
    types: [opened, synchronize, reopened, ready_for_review]

jobs:
  test:
    if: github.event.pull_request.draft == false
    runs-on: ubuntu-latest
```

---

**Q22: How would you deploy only if all tests pass across a matrix?**

```yaml
jobs:
  test:
    strategy:
      matrix:
        node: [16, 18, 20]
    # ... test steps

  deploy:
    needs: test     # Waits for ALL matrix combinations to pass
    runs-on: ubuntu-latest
    steps:
      - run: ./deploy.sh
```

---

**Q23: How do you limit concurrent workflow runs?**

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true   # Cancel old run when new one starts
```

This prevents two deployments from running simultaneously on the same branch.

---

## 13. Scenario-Based Questions

**Scenario 1:** *"Your team pushes code 20 times a day and tests take 10 minutes. How would you optimize the GitHub Actions pipeline?"*

> - Add **dependency caching** to avoid re-installing packages
> - Use **matrix builds** only where needed
> - Run **only affected tests** using path filters
> - Use `concurrency` with `cancel-in-progress: true` to cancel outdated runs
> - Split tests into **parallel jobs**
> - Use **self-hosted runners** for faster hardware

---

**Scenario 2:** *"How would you set up a workflow that deploys to staging on every push, but requires manual approval for production?"*

```yaml
jobs:
  deploy-staging:
    environment: staging
    runs-on: ubuntu-latest
    steps:
      - run: ./deploy.sh staging

  deploy-production:
    environment: production    # Set up "required reviewers" in GitHub settings
    needs: deploy-staging
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - run: ./deploy.sh production
```

> In GitHub Settings → Environments → production → add "Required reviewers". The workflow will pause and wait for approval before the production job runs.

---

**Scenario 3:** *"A secret leaked in the workflow logs. What do you do?"*

> 1. **Immediately revoke** the leaked credential from the provider (AWS, etc.)
> 2. **Rotate** the secret and add the new value to GitHub Secrets
> 3. **Audit** which workflow runs may have exposed it
> 4. Check if GitHub already **masked** it (secrets matching `${{ secrets.X }}` are auto-masked)
> 5. Review the workflow — never use `echo ${{ secrets.X }}` or pass secrets as command arguments

---

**Scenario 4:** *"How do you share build artifacts between a build job and a deploy job?"*

```yaml
jobs:
  build:
    steps:
      - run: npm run build
      - uses: actions/upload-artifact@v4
        with:
          name: dist-files
          path: dist/

  deploy:
    needs: build
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: dist-files
          path: dist/
      - run: ./deploy.sh
```

---

## 14. Quick Revision Cheatsheet

```
📁 File location:  .github/workflows/<name>.yml

🔑 KEY SECTIONS:
   name:           → Workflow display name
   on:             → When to trigger
   env:            → Global variables
   jobs:           → Job definitions
     runs-on:      → Runner OS
     needs:        → Job dependencies
     steps:        → List of tasks
       uses:       → Pre-built action
       run:        → Shell command
       with:       → Action inputs
       env:        → Step variables
       if:         → Condition

🔧 COMMON ACTIONS:
   actions/checkout@v4            → Clone repo
   actions/setup-node@v4          → Setup Node.js
   actions/setup-python@v5        → Setup Python
   actions/cache@v4               → Cache dependencies
   actions/upload-artifact@v4     → Save files
   actions/download-artifact@v4   → Get saved files
   docker/login-action@v3         → Docker Hub login
   docker/build-push-action@v5    → Build & push image

🌐 COMMON TRIGGERS:
   push            → Code pushed
   pull_request    → PR opened/updated
   schedule        → Cron job
   workflow_dispatch → Manual trigger
   release         → GitHub release created

🖥️ RUNNERS:
   ubuntu-latest   → Linux (cheapest, most common)
   windows-latest  → Windows
   macos-latest    → macOS
   self-hosted     → Your own machine

💡 CONTEXT VARIABLES:
   ${{ github.sha }}          → Commit hash
   ${{ github.ref }}          → Branch ref
   ${{ github.actor }}        → Who triggered
   ${{ github.repository }}   → owner/repo
   ${{ secrets.NAME }}        → Secret value
   ${{ env.NAME }}            → Env variable

⚡ TIPS FOR INTERVIEWS:
   ✅ Always checkout first
   ✅ Use npm ci instead of npm install in CI
   ✅ Cache dependencies for speed
   ✅ Never print secrets in run commands
   ✅ Use needs: for job ordering
   ✅ Use if: for conditional steps
   ✅ Environments for deployment protection
   ✅ concurrency to prevent duplicate runs
```

---

## 📚 Resources to Practice

- **GitHub Docs:** https://docs.github.com/en/actions
- **Marketplace:** https://github.com/marketplace?type=actions
- **Starter Workflows:** https://github.com/actions/starter-workflows
- **Practice:** Create a free GitHub repo and try building a Node.js or Python CI pipeline

---

> **Pro Tip for Interviews:** Always explain GitHub Actions with a real example you've built or practiced. Even a simple "I created a workflow that runs tests on every pull request" shows hands-on experience and stands out from candidates who only know theory.

---

*Last Updated: May 2026 | Covers GitHub Actions v2+ syntax*
