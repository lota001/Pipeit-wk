# PipeIt Weekend: Building My First CI/CD Pipeline

![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-CI%2FCD-2088FF?logo=githubactions&logoColor=white)
![Bash](https://img.shields.io/badge/Bash-Automated_Testing-4EAA25?logo=gnubash&logoColor=white)
![GitHub Pages](https://img.shields.io/badge/GitHub_Pages-Deployed-222222?logo=githubpages&logoColor=white)

**Live Website:** [View My Deployed Website](https://lota001.github.io/Pipeit-wk/)

## Project Overview

This project demonstrates a complete CI/CD (Continuous Integration and Continuous Deployment) pipeline I built using GitHub Actions during **PipeIt Weekend by SKILL.SCH**.

The goal was to automate the process of testing, building, and deploying a simple website whenever changes are pushed to the repository.

Instead of manually running tests, generating website files, and deploying updates, I configured GitHub Actions to handle these tasks automatically.

I also developed a **custom composite GitHub Action** to generate the website's HTML content.

The pipeline can be triggered by a code push, a scheduled run, or manual execution.

## How the CI/CD Pipeline Works

The pipeline follows three main stages:

**Test → Build → Deploy**

```text
          Push to Main Branch
                   |
          Daily Scheduled Run
                   |
          Manual Workflow Run
                   |
                   v
          +------------------+
          |       TEST       |
          |  tests/check.sh  |
          +------------------+
                   |
             Test Passed
                   |
                   v
          +------------------+
          |      BUILD       |
          |  Custom GitHub   |
          |      Action      |
          +------------------+
                   |
          Generates Website
          (site/index.html)
                   |
                   v
          +------------------+
          |      DEPLOY      |
          |   GitHub Pages   |
          +------------------+
                   |
                   v
             Live Website
```

Each stage depends on the successful completion of the previous stage. If the automated test fails, the build and deployment stages will not execute.

This ensures that changes failing validation are not deployed.

## Pipeline Stages

### 1. Test — Automated Validation

The first stage executes a Bash script located at:

`tests/check.sh`

The script checks whether the `README.md` file contains a valid Markdown heading beginning with `# `.

If the heading exists, the test passes and the pipeline continues.

If the heading is missing, the script exits with a non-zero status code, causing the test job to fail.

**Example Bash Test:**

```bash
#!/usr/bin/env bash

# Check that README.md contains a Markdown title.

set -e

if grep -q "^# " README.md; then
    echo "✅ README has a title"
else
    echo "❌ README.md needs a title line that starts with # "
    exit 1
fi
```

This demonstrates how basic automated validation can be integrated into a CI/CD workflow to identify issues before deployment.

### 2. Build — Custom GitHub Action

Once the test passes, the build stage runs my custom composite GitHub Action:

`.github/actions/build-card`

The action automatically generates a static HTML website at:

`site/index.html`

The generated page includes information about the pipeline execution, such as:

- GitHub Actions workflow run number.
- Git commit information.
- Build date and timestamp.
- Custom name and message.
- Build status information.

By creating a reusable GitHub Action, I separated the website generation logic from the main workflow configuration.

### 3. Deploy — GitHub Pages

After the build completes successfully, the deployment stage publishes the generated website to GitHub Pages.

This makes the latest successfully built version accessible through a public URL.

**Live Website:** https://lota001.github.io/pipeit-weekend/

The deployment process is automated, eliminating the need to manually upload website files after each change.

## Repository Structure

The repository contains the following key files:

| File | Description |
|---|---|
| `.github/workflows/hello.yml` | My first GitHub Actions workflow, which prints a message when code is pushed. |
| `.github/workflows/ci.yml` | Runs automated tests on pushes and pull requests. |
| `.github/workflows/pipeline.yml` | Executes the complete test, build, and deployment pipeline. |
| `.github/actions/build-card/action.yml` | Defines my custom composite GitHub Action for generating the website. |
| `tests/check.sh` | Bash script that validates the README heading. |
| `README.md` | Project documentation and implementation overview. |
| `site/index.html` | HTML website generated during the build process. |

## My Custom GitHub Action

One of the key achievements of this project was creating my own **composite GitHub Action**, named `build-card`.

Instead of placing all the build commands directly inside the workflow YAML file, I created a reusable action that
