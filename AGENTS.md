# Agent Guide: skills-communicate-using-markdown

## Project Overview

This repository is a **GitHub Skills interactive exercise** titled "Communicate using Markdown". It is an official GitHub tutorial repository (licensed under MIT) that teaches learners how to use Markdown syntax on GitHub through a hands-on, step-by-step workflow.

Unlike a traditional software project, this repo contains **no application code, build system, or runtime server**. The entire learning experience is orchestrated by GitHub Actions workflows that guide the learner through creating and editing a `day-1.md` file.

## Technology Stack

- **GitHub Actions**: The core engine of the project. All logic lives in `.github/workflows/`.
- **Markdown**: The content format for instructions (`.github/steps/`) and the learner's exercise file (`day-1.md`).
- **External Reusable Actions/Workflows**:
  - `skills/exercise-toolkit@v0.6.0` — Provides shared workflows (`start-exercise.yml`, `find-exercise-issue.yml`, `finish-exercise.yml`) and feedback templates.
  - `skills/action-text-variables@v2` — Injects dynamic variables into Markdown templates.
  - `skills/action-keyphrase-checker@v1` — Validates learner submissions by scanning `day-1.md` for required Markdown syntax.
  - `skills/exercise-toolkit/actions/file-exists@v0.6.0` — Checks for file existence.

## Repository Structure

```
.
├── .github/
│   ├── steps/                    # Step-by-step instruction content
│   │   ├── 1-add-headings.md
│   │   ├── 2-make-a-task-list.md
│   │   ├── 3-add-a-code-example.md
│   │   ├── 4-add-an-image.md
│   │   ├── 5-merge-your-pull-request.md
│   │   └── x-review.md           # Final review content shown on completion
│   └── workflows/                # GitHub Actions workflows
│       ├── 0-start-exercise.yml  # Kicks off the exercise on push to main
│       ├── 1-add-headings.yml    # Validates headings in day-1.md
│       ├── 2-make-a-task-list.yml
│       ├── 3-add-a-code-example.yml
│       ├── 4-add-an-image.yml
│       └── 5-merge-your-pull-request.yml
├── .gitignore                    # Standard ignore file (OS artifacts, archives, logs)
├── LICENSE                       # MIT License (GitHub, Inc.)
└── README.md                     # Landing page for the repo
```

**Note**: There is no `package.json`, `pyproject.toml`, `Cargo.toml`, or similar build configuration. The `.venv/` directory present locally is **not part of the repository** and should be ignored.

## How the Exercise Works

1. **Step 0 — Start**: When a user pushes to `main` (e.g., after creating the repo from the template), `0-start-exercise.yml` creates an exercise issue and posts the instructions for Step 1.
2. **Steps 1–4 — Practice**: The learner works on the `start-blog` branch, editing `day-1.md`. Each push to `start-blog` that changes `day-1.md` triggers the corresponding workflow:
   - **Step 1**: Checks that `day-1.md` exists, contains one `# ` (H1), and at least two `## ` (H2).
   - **Step 2**: Checks for a task list (` [ ] `).
   - **Step 3**: Checks for a fenced code block (` ``` `).
   - **Step 4**: Checks for an HTML image tag (`<img`).
   
   If checks pass, the workflow posts a success message and enables the next step. If checks fail, it posts a failure table and leaves the current step enabled.
3. **Step 5 — Merge**: When the learner opens a pull request from `start-blog` to `main` and merges it, `5-merge-your-pull-request.yml` posts the final review (`x-review.md`) and finishes the exercise.

## Key Conventions

- **Workflow naming**: Each workflow file corresponds to a step number (`0-start-exercise.yml` through `5-merge-your-pull-request.yml`). The `name:` field inside each YAML file is `Step 0` through `Step 5`.
- **Step content location**: Instruction Markdown for step *N* is stored in `.github/steps/N-*.md`.
- **Environment variables in workflows**: Each workflow defines an env variable pointing to the next step's content file (e.g., `STEP_2_FILE: ".github/steps/2-make-a-task-list.md"`).
- **Permissions**: All workflows require `contents: write`, `actions: write`, and `issues: write` so they can manage issues, post comments, and enable/disable workflows.
- **Branching model**:
  - `main` — The default branch; exercise starts here.
  - `start-blog` — The learner's working branch where `day-1.md` is created and edited.

## Testing and Validation

There are **no unit tests or linting scripts** in this repository. Validation is performed live by the GitHub Actions workflows using the `skills/action-keyphrase-checker` action. If you modify a workflow or a step instruction, you should verify:

1. **YAML syntax** is valid (use a YAML linter or the GitHub Actions editor).
2. **Step content Markdown** renders correctly on GitHub.
3. **Workflow triggers** (`on.push.branches` and `on.pull_request`) align with the intended learner flow.
4. **Keyphrase checks** match the syntax you are asking the learner to use in `day-1.md`.

## Deployment

There is **no deployment process**. The repository is meant to be used as a GitHub template or directly cloned. The GitHub Actions workflows run automatically on GitHub's infrastructure.

## Security Considerations

- Workflows use `secrets.GITHUB_TOKEN` with elevated permissions (`contents: write`, `actions: write`, `issues: write`). Keep these permissions minimal to what is strictly required.
- The workflows checkout the external `skills/exercise-toolkit` repository at a pinned tag (`v0.6.0`). If updating this dependency, verify the new tag is trusted.
- Do not commit learner data or personal information; the repository is designed to be a reusable template.
