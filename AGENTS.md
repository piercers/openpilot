# Openpilot Development Guide for Agents

This repository contains the source code for openpilot, an open source driver assistance system. 
Follow these instructions strictly to ensure code quality, safety, and consistency.

## 1. Build & Test Commands

The project uses `scons` for building and `pytest` for testing.

### Build
- **Full Build:** `scons -j$(nproc)`
- **Minimal Build (Faster, no tests/tools):** `scons --minimal -j$(nproc)`
- **Clean:** `scons -c`
- **Sanitizers:** `scons --asan` (AddressSanitizer) or `scons --ubsan` (UndefinedBehaviorSanitizer)

### Testing
- **Run All Tests:** `pytest` (Note: Takes a long time)
- **Run Single Test File:** `pytest <path/to/test_file.py>`
- **Run Single Test Case:** `pytest <path/to/test_file.py>::<test_function_name>`
- **Important Notes:**
  - Tests run in parallel by default (`-n auto`).
  - Many directories are excluded by default in `pyproject.toml`.
  - Use `pytest -v` for verbose output.

### Linting & Formatting
- **Lint (Python):** `ruff check .`
- **Format (Python):** `ruff format .`
- **Type Check:** `ty` (or rely on `scons` for C++ types)
- **Spell Check:** `codespell`

## 2. Code Style & Conventions

### Python
- **Indentation:** **2 spaces** (Strictly enforced).
- **Line Length:** 160 characters.
- **Imports:**
  - Use absolute imports: `import openpilot.selfdrive.car...` instead of `import selfdrive.car...`.
  - **Banned:** `time.time` (use `time.monotonic`), `unittest` (use `pytest`).
  - **Organization:** Imports should be sorted (handled by `ruff`).
- **Typing:** Use type hints extensively. The project aims for high type coverage.
- **Naming:** Snake_case for functions/variables, PascalCase for classes.
- **Error Handling:** Prefer explicit exceptions. Avoid bare `except:`.

### C++
- **Standard:** C++17 (`-std=c++1z`) and GNU11 (`-std=gnu11`).
- **Compiler:** Clang/Clang++ with `-Werror` (warnings are errors).
- **Style:** Follow the style of existing files. No official `.clang-format` found, but consistency is key.

### Project Structure
- `common/`: Shared utilities (mathematics, OS abstractions).
- `selfdrive/`: Core driving logic (controls, planning, car interfaces).
- `system/`: System services (logging, hardware interaction).
- `cereal/`: Cap'n Proto messaging definitions (IPC).
- `tools/`: Development and replay tools.

## 3. General Rules & Philosophy

- **Safety First:** This is safety-critical software. Changes must be safe and robust.
- **Simplicity:** "The best part is no part." Remove unused code. Simple implementations are preferred over complex ones.
- **No Large PRs:** Keep changes small and focused. Avoid 500+ line changes.
- **Dependencies:** Do not add new dependencies without explicit permission and verification.
- **Testing:** Verify changes with tests. If a bug is fixed, add a regression test.

## 4. Cursor/Copilot Rules

*No specific `.cursorrules` or `copilot-instructions.md` found. Adhere to the guidelines above.*

## 5. Fork Specifics & Customizations

This fork is based on upstream's `master` branch and deviates in the following ways:

- **Modifications (as of commit e35e528):**
  - **Engagement/Disengagement:** Events for engaging and disengaging have been disabled (`selfdrive/selfdrived/events.py`, `selfdrive/selfdrived/selfdrived.py`).
  - **Audio:** Maximum warning volume has been lowered.

## 6. Workflow for This Fork (piercers/openpilot)

**This repo is a personal fork of `commaai/openpilot` used exclusively for small, targeted code changes. The following rules override any general guidance above.**

### Tests
- **Do NOT run tests.** Tests are assumed to pass and will be verified manually.
- Do not run `pytest`, `scons`, or any build/test commands as part of your workflow.
- Do not suggest or add test files.

### GitHub Actions / CI
- **Do NOT trigger or rely on GitHub Actions.** No CI runs are expected or required.
- Do not modify `.github/workflows/` files.

### Remotes & PRs
- **All pushes and PRs target this fork only** (`piercers/openpilot` on `origin`).
- **Never push to or PR against the upstream** (`commaai/openpilot`).
- The upstream remote (if configured as `upstream`) is read-only — used only for syncing the `master` branch.

### Branch strategy
- Work branches are cut from `master` (the fork's default branch, kept in sync with upstream).
- PRs go: `feat/<branch>` → `master` on `piercers/openpilot`.
- Keep changes small and focused — this repo exists for targeted modifications only.

### Upstream sync
- To pull in upstream changes: `git fetch upstream && git merge upstream/master`
- Do not rebase or force-push `master`.
