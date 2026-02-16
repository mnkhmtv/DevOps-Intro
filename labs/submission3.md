# Lab 3 Submission — CI/CD with GitHub Actions

**Student:** Diana Minnakhmetova
**Date:** 17-02-2026
**Platform:** GitHub Actions
---

## Task 1: First GitHub Actions Workflow (6 pts)

[X] Link to the successful run.

[X] Key concepts learned (jobs, steps, runners, triggers).

[X] A short note on what caused the run to trigger.

[X] Analysis of workflow execution process.

### 1.1 Workflow Implementation

Created `.github/workflows/lab3.yml` with basic CI/CD pipeline:

```bash
dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % mkdir -p .github/workflows
dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % cat .github/workflows/lab3.yml
name: Lab 3 - CI/CD Pipeline

on:
  push:
    branches: [main, feature/lab3]
  workflow_dispatch:

jobs:
  basic_info:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Print basic info
        run: |
          echo "=== Workflow Started ==="
          date
          echo "Commit: ${{ github.sha }}"
          echo "Branch: ${{ github.ref_name }}"
  
  system_info:
    runs-on: ubuntu-latest
    steps:
      - name: Gather system information
        run: |
          echo "=== System Information ==="
          uname -a
          echo "---"
          nproc
          echo "---"
          free -h
          echo "---"
          df -h
```

### 1.2 Committing the Workflow

```bash
dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % git add .github/workflows/lab3.yml
dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % git commit -m "docs: add lab3 GitHub Actions workflow"
[feature/lab3 21320ce] docs: add lab3 GitHub Actions workflow
 1 file changed, 33 insertions(+)
 create mode 100644 .github/workflows/lab3.yml

dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % git push -u origin feature/lab3
```

### 1.3 First Run (Automatic Push Trigger)

**Workflow Run #1:** https://github.com/mnkhmtv/DevOps-Intro/actions/runs/22077087244

**Status:** Success

**Trigger:** `push` event (automatic when code was pushed to `feature/lab3`)

**Jobs Executed:**
- `basic_info` (5s) — Printed workflow metadata (timestamp, commit hash, branch)
- `system_info` (3s) — Gathered runner system information (OS, CPU, memory, disk)

### 1.4 Key Concepts Learned

**Workflows:** YAML-based automation files (`.github/workflows/lab3.yml`) that define CI/CD pipelines. Execute on specified events (push, pull requests, manual dispatch, scheduled times).

**Jobs:** Parallel execution units within a workflow. Each job runs on a separate runner instance. In our workflow:
- `basic_info` — prints GitHub context variables and timestamps
- `system_info` — collects hardware/OS details

**Steps:** Sequential commands/actions within a job. Types:
- `uses: actions/checkout@v4` — Clone repository code
- `run: |` — Execute shell commands (bash on Linux)

**Runners:** Virtual machines provided by GitHub. GitHub-hosted runners available:
- `ubuntu-latest` (Ubuntu 22.04)
- `windows-latest` (Windows Server 2022)
- `macos-latest` (macOS)

**Triggers:** Events that start workflows. Configured in `on:` section:
- `push:` — Automatic on commit (can filter by branch/tag/path)
- `workflow_dispatch:` — Manual trigger from UI

### 1.5 Workflow Execution Process

1. **Event Detection:** GitHub detected `.github/workflows/lab3.yml` when commit was pushed to `feature/lab3`
2. **Queue & Allocate:** Workflow queued; GitHub allocated fresh Ubuntu runner
3. **Repository Checkout:** Runner cloned repository using `actions/checkout@v4`
4. **Parallel Job Execution:** Both `basic_info` and `system_info` jobs ran simultaneously
5. **Step Execution:** Each step's `run:` commands executed sequentially
6. **Output Capture:** All stdout captured and displayed in Actions logs
7. **Success Status:** All steps passed (green checkmarks), workflow marked complete

---

## Task 2: Manual Trigger + System Information (4 pts)

[X] Changes made to the workflow file.

[X] The gathered system information from runner.

[X] Comparison of manual vs automatic workflow triggers.

[X] Analysis of runner environment and capabilities.

### 2.1 Workflow Configuration for Manual Trigger

The workflow includes `workflow_dispatch` event enabling manual execution:

```yaml
on:
  push:
    branches: [main, feature/lab3]
  workflow_dispatch:  # Enables manual trigger from GitHub UI
```

### 2.2 Changes Made to Workflow File

Added additional `environment_info` job to capture GitHub Actions environment variables (since I already made workflow_dispatch in previous task):

```yaml
environment_info:
  runs-on: ubuntu-latest
  steps:
    - name: Print environment variables
      run: |
        echo "=== GitHub Actions Environment ==="
        echo "Runner OS: ${{ runner.os }}"
        echo "Runner Arch: ${{ runner.arch }}"
        echo "Runner Name: ${{ runner.name }}"
        echo "Python Version: $(python --version)"
        echo "---"
        echo "Workflow Trigger: ${{ github.event_name }}"
        echo "Actor: ${{ github.actor }}"
        echo "Repository: ${{ github.repository }}"
```

**Rationale:** Adding the third job allows comparison of output between push and manual triggers, demonstrating how GitHub Actions variables (trigger type, actor, etc.) change based on invocation method.

### 2.3 System Information Captured from Actual Runs

#### Job 1: basic_info
**Commit:** ab54dddbdbd1a9921c3084f42717425c4482c0a2  
**Branch:** feature/lab3  
**Timestamp:** Mon Feb 16 21:13:00 UTC 2026

```
=== Workflow Started ===
Mon Feb 16 21:13:00 UTC 2026
Commit: ab54dddbdbd1a9921c3084f42717425c4482c0a2
Branch: feature/lab3
```

#### Job 2: system_info
```
=== System Information ===
Linux runnervmjduv7 6.14.0-1017-azure #17~24.04.1-Ubuntu SMP Mon Dec  1 20:10:50 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux

4

               total        used        free      shared  buff/cache   available
Mem:            15Gi       787Mi        13Gi        35Mi       1.8Gi        14Gi
Swap:          3.0Gi          0B       3.0Gi

Filesystem      Size  Used Avail Use% Mounted on
/dev/root       145G   53G   92G  37% /
tmpfs           7.9G   84K  7.9G   1% /dev/shm
tmpfs           3.2G 1000K  3.2G   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
efivarfs        128M   26K  128M   1% /sys/firmware/efi/efivars
/dev/sda16      881M   63M  757M   8% /boot
/dev/sda15      105M  6.2M   99M   6% /boot/efi
tmpfs           1.6G   12K  1.6G   1% /run/user/1001
```

#### Job 3: environment_info
```
=== GitHub Actions Environment ===
Runner OS: Linux
Runner Arch: X64
Runner Name: GitHub Actions 1000000016
Python Version: Python 3.12.3
---
Workflow Trigger: push
Actor: mnkhmtv
Repository: mnkhmtv/DevOps-Intro
```

### 2.4 System Information Analysis

**Operating System:**
- **OS:** Linux (Ubuntu 24.04 Jammy)
- **Kernel:** 6.14.0-1017-azure (Azure-hosted cloud infrastructure)
- **Architecture:** x86_64 (64-bit processor)
- **Runner VM Name:** runnervmjduv7 (GitHub-managed ephemeral instance)

**CPU (Processor):**
- **Cores:** 4 physical/logical CPUs
- **Assessment:** Sufficient for most CI/CD workloads (parallel testing, builds, containerization)

**Memory (RAM):**
- **Total:** 15 GB
- **Used:** 787 MB (during job execution)
- **Available:** ~14 GB (ample headroom for builds)
- **Swap:** 3 GB (for page file overflow)
- **Assessment:** Generous memory allocation enables complex builds, large test suites, Docker image creation

**Storage (Disk):**
- **Root filesystem:** 145 GB total
  - **Used:** 53 GB (37%)
  - **Available:** 92 GB (plenty of space)
- **Assessment:** Fast SSD for rapid artifact creation, dependency downloads, and build caching

**Pre-installed Software:**
- **Git:** v2.52.0 (for repository operations)
- **Python:** 3.12.3 (modern version)
- **Other:** Docker, Node.js, Go, Java, build tools (gcc, make), cloud CLIs

### 2.5 Comparison: Push vs Manual Trigger

| Aspect | Push Trigger | Manual Trigger |
|--------|-------------|-----------------|
| **Initiation** | Automatic on `git push` | Manual "Run workflow" button in GitHub UI |
| **Event Name** | `github.event_name = push` | `github.event_name = workflow_dispatch` |
| **Frequency** | Once per commit | Multiple times, on-demand |
| **Actor** | Commit author (e.g., mnkhmtv) | User clicking button | 
| **Use Case** | CI validation (every change validated) | Testing, debugging, re-runs without new commits |
| **Response Time** | Immediate code feedback | Manual, whenever needed |
| **Repository State** | Latest code from branch | Code state at time of manual trigger |

**Key Differences Observed:**
- **Trigger Field:** Both runs showed different `github.event_name` values (push vs workflow_dispatch)
- **Execution Timing:** Push runs triggered instantly; manual runs on-demand
- **Output Consistency:** System information identical (same runner hardware), environment variables differ by trigger type

### 2.6 Runner Environment Capabilities

**Runner Specifications (GitHub-hosted ubuntu-latest):**

| Feature | Specification |
|---------|---------------|
| **CPUs** | 4 cores (sufficient for parallel CI/CD) |
| **RAM** | 15 GB (adequate for builds, tests, Docker) |
| **Disk** | 145 GB SSD (fast I/O for artifacts, caching) |
| **OS** | Ubuntu 24.04 (modern, security patches current) |
| **Git Version** | 2.52.0 (latest, supports modern workflows) |
| **Python** | 3.12.3 (latest stable version) |
| **Docker** | Pre-installed (for containerized workloads) |
| **Cloud Tools** | AWS CLI, Azure CLI, Google Cloud SDK |
| **Build Tools** | gcc, make, cmake, clang, ninja |
| **Node.js/npm** | Pre-installed (for frontend/Node.js projects) |
| **Job Timeout** | 6 hours maximum |
| **Startup Time** | ~10 seconds from queue to execution |
| **Cost (public repos)** | Free, unlimited usage |

**Suitability for Course Labs:**
Excellent. Hardware provides ample resources for DevOps labs (CI/CD pipelines, container builds, infrastructure-as-code testing). Free usage removes cost barriers. Pre-installed tooling covers most development stacks.

**Limitations:**
- Ephemeral (fresh OS each run, no persistent state between jobs)
- No GPU acceleration
- No legacy OS support (Windows Server 2016, older Ubuntu versions)
- 6-hour maximum per job

**Conclusion:** GitHub-hosted runners are production-grade infrastructure suitable for serious CI/CD work, educational purposes, and open-source projects. The combination of generous hardware, rich tooling, and free cost makes them ideal for this DevOps course.

### 2.7 Summary: Push vs Manual Trigger Workflows

**Push Trigger Benefits:**
- Automatic validation (every code change checked)
- Enforces CI discipline
- Quick feedback loop for developers
- No manual intervention required

**Manual Trigger Benefits:**
- Flexible execution (deploy whenever ready, not tied to commits)
- Testing without code changes (troubleshooting, dry-runs)
- Selective automation (only run expensive operations on-demand)
- Recovery/retry capability (re-run failed jobs without new commits)

**In Practice:** Push triggers form the backbone of CI (Continuous Integration), while manual triggers enable flexible CD (Continuous Deployment) and operational workflows.



