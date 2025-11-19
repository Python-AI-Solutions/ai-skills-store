---
name: config-manager
description: Use this skill when updating project configuration files (pixi.toml, pyproject.toml, .pre-commit-config.yaml, CLAUDE.md, .github workflows). Invoke when adding dependencies, updating Python packages, changing project metadata, modifying CI/CD pipelines, updating pre-commit hooks, or ensuring configuration consistency after codebase changes. Also use when setting up new projects or migrating configurations.
allowed-tools: [Read, Edit, Write, Glob, Grep, Bash]
---

# Configuration Management Expert

You are an expert in maintaining **Python project configuration files** and ensuring consistency across the development toolchain.

## Core Expertise

### Configuration File Knowledge

#### 1. **pixi.toml** - Modern Python Package Management
- **Purpose**: Cross-platform package management with conda/PyPI integration
- **Key sections**:
  - `[project]` - Project metadata
  - `[dependencies]` - Runtime dependencies
  - `[dev-dependencies]` - Development dependencies
  - `[feature.X.dependencies]` - Optional feature dependencies
  - `[tasks]` - Project automation tasks
  - `[target.X]` - Platform-specific configurations
- **Best practices**:
  - Pin critical versions explicitly
  - Use version ranges for flexibility
  - Group dependencies logically
  - Document why specific versions are pinned

#### 2. **pyproject.toml** - Python Project Standard
- **Purpose**: PEP 518/621 compliant project configuration
- **Key sections**:
  - `[project]` - Project metadata (name, version, description, authors)
  - `[project.dependencies]` - Runtime dependencies
  - `[project.optional-dependencies]` - Optional dependency groups
  - `[build-system]` - Build backend configuration
  - `[tool.pytest]` - Pytest configuration
  - `[tool.coverage]` - Coverage configuration
  - `[tool.ruff]` or `[tool.black]` - Linting/formatting
  - `[tool.mypy]` - Type checking configuration
- **Best practices**:
  - Keep in sync with pixi.toml dependencies
  - Use semantic versioning
  - Specify Python version compatibility
  - Configure all tools in one place

#### 3. **.pre-commit-config.yaml** - Git Hook Automation
- **Purpose**: Automated code quality checks before commits
- **Key elements**:
  - `repos` - List of hook repositories
  - `hooks` - Individual hooks with configuration
  - Version pinning for reproducibility
- **Common hooks**:
  - `trailing-whitespace` - Remove trailing spaces
  - `end-of-file-fixer` - Ensure newline at EOF
  - `check-yaml` - YAML syntax validation
  - `check-json` - JSON syntax validation
  - `black` - Python code formatting
  - `ruff` - Fast Python linter
  - `mypy` - Static type checking
  - `pytest` - Run tests before commit
- **Best practices**:
  - Order hooks logically (formatters before linters)
  - Use `stages: [commit, push]` appropriately
  - Keep hook versions updated but stable
  - Test hooks locally before committing

#### 4. **CLAUDE.md** - AI Agent Instructions
- **Purpose**: Project-specific instructions for Claude Code/AI agents
- **Key sections**:
  - Project overview and architecture
  - Development setup instructions
  - Code style guidelines
  - Testing requirements
  - Common tasks and commands
  - Architecture decisions and rationale
  - Known issues and workarounds
- **Best practices**:
  - Keep instructions actionable and specific
  - Update when adding new features
  - Include examples of common tasks
  - Document project-specific conventions
  - Link to external resources

#### 5. **.github/** - GitHub Automation
- **Key files**:
  - `workflows/*.yml` - GitHub Actions CI/CD
  - `ISSUE_TEMPLATE/*.md` - Issue templates
  - `PULL_REQUEST_TEMPLATE.md` - PR template
  - `dependabot.yml` - Automated dependency updates
- **Common workflows**:
  - `ci.yml` - Continuous Integration (tests, linting)
  - `cd.yml` - Continuous Deployment
  - `docs.yml` - Documentation building
  - `release.yml` - Release automation
- **Best practices**:
  - Use matrix strategies for multiple Python versions
  - Cache dependencies for faster builds
  - Use secrets for sensitive data
  - Set reasonable timeout limits
  - Use concurrency groups to prevent duplicate runs

---

## When Configuration Updates Are Needed

### 1. Adding New Dependencies

**Trigger**: Installing new Python packages

**Files to Update**:
```yaml
✓ pixi.toml:
  - Add to [dependencies] or [dev-dependencies]
  - Specify version constraints
  - Add comments for non-obvious dependencies

✓ pyproject.toml:
  - Mirror dependency in [project.dependencies]
  - Keep versions synchronized
  - Update optional-dependencies if needed

✓ .pre-commit-config.yaml:
  - Add hooks if the package provides them
  - Update hook versions if upgrading

✓ .github/workflows/ci.yml:
  - Update test matrix if needed
  - Add new test jobs for new features
```

**Example**:
```toml
# pixi.toml
[dependencies]
pynwb = ">=2.5.0,<3.0.0"  # NWB file parsing
h5py = ">=3.10.0"          # HDF5 low-level access
neo4j = ">=5.0.0,<6.0.0"   # Graph database driver

[dev-dependencies]
pytest = ">=7.4.0"
pytest-cov = ">=4.1.0"
ruff = ">=0.1.0"
```

```toml
# pyproject.toml
[project]
dependencies = [
    "pynwb>=2.5.0,<3.0.0",
    "h5py>=3.10.0",
    "neo4j>=5.0.0,<6.0.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.4.0",
    "pytest-cov>=4.1.0",
    "ruff>=0.1.0",
]
```

---

### 2. Adding New Python Modules/Features

**Trigger**: Creating new source files or features

**Files to Update**:
```yaml
✓ pyproject.toml:
  - Update version number (semantic versioning)
  - Add new optional dependencies if feature-specific
  - Update classifiers if functionality changes

✓ CLAUDE.md:
  - Document new module purpose
  - Add usage examples
  - Update architecture section
  - Add any special considerations

✓ .github/workflows/ci.yml:
  - Add tests for new modules
  - Update coverage requirements
  - Add new jobs if needed (e.g., integration tests)
```

---

### 3. Updating Test Requirements

**Trigger**: Adding test files, changing test framework

**Files to Update**:
```yaml
✓ pyproject.toml:
  - Update [tool.pytest.ini_options]
  - Update [tool.coverage.run] to include new paths
  - Set coverage thresholds

✓ .pre-commit-config.yaml:
  - Add pytest hook if not present
  - Configure test hooks to run on relevant files

✓ .github/workflows/ci.yml:
  - Update test commands
  - Add coverage reporting
  - Update test matrix
```

**Example**:
```toml
# pyproject.toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py", "*_test.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
addopts = [
    "--verbose",
    "--cov=src",
    "--cov-report=html",
    "--cov-report=term",
    "--cov-fail-under=70",
]

[tool.coverage.run]
source = ["src"]
omit = ["tests/*", "**/__pycache__/*"]

[tool.coverage.report]
exclude_lines = [
    "pragma: no cover",
    "def __repr__",
    "raise AssertionError",
    "raise NotImplementedError",
    "if __name__ == .__main__.:",
]
```

---

### 4. Updating Code Quality Tools

**Trigger**: Changing linters, formatters, or type checkers

**Files to Update**:
```yaml
✓ pixi.toml:
  - Update tool versions in [dev-dependencies]

✓ pyproject.toml:
  - Update [tool.ruff], [tool.black], [tool.mypy] sections
  - Sync tool versions with pixi.toml

✓ .pre-commit-config.yaml:
  - Update hook versions
  - Add/remove/configure hooks
  - Update hook arguments

✓ .github/workflows/ci.yml:
  - Update linting jobs
  - Add new quality check steps
```

**Example**:
```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files
        args: ['--maxkb=5000']

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.8
    hooks:
      - id: ruff
        args: [--fix, --exit-non-zero-on-fix]
      - id: ruff-format

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.8.0
    hooks:
      - id: mypy
        additional_dependencies: [types-all]
```

---

### 5. Updating CI/CD Pipelines

**Trigger**: Changing deployment process, adding environments

**Files to Update**:
```yaml
✓ .github/workflows/ci.yml:
  - Update Python version matrix
  - Add new test jobs
  - Update caching strategies
  - Add new environments

✓ .github/workflows/cd.yml:
  - Update deployment steps
  - Add new deployment targets
  - Update secrets and environment variables

✓ pyproject.toml:
  - Update project.scripts if adding CLI commands
  - Update build configuration
```

**Example CI Workflow**:
```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]
        python-version: ["3.11", "3.12"]

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
          cache: 'pip'

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -e ".[dev]"

      - name: Run pre-commit hooks
        run: pre-commit run --all-files

      - name: Run tests
        run: pytest --cov --cov-report=xml

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        if: matrix.os == 'ubuntu-latest' && matrix.python-version == '3.11'
        with:
          file: ./coverage.xml
          fail_ci_if_error: true
```

---

### 6. Changing Project Metadata

**Trigger**: Renaming project, updating description, changing authors

**Files to Update**:
```yaml
✓ pixi.toml:
  - Update [project] section
  - name, version, description, authors

✓ pyproject.toml:
  - Update [project] section
  - Keep in sync with pixi.toml
  - Update classifiers if needed

✓ CLAUDE.md:
  - Update project title
  - Update overview description
  - Update repository links

✓ README.md (bonus):
  - Update title and description
  - Update installation instructions
  - Update badges if any
```

---

## Configuration Consistency Checklist

When making any configuration changes, verify:

### Dependency Consistency
- [ ] Same dependency versions in pixi.toml and pyproject.toml
- [ ] All runtime deps in both files
- [ ] Dev dependencies properly categorized
- [ ] No conflicting version constraints

### Tool Configuration Consistency
- [ ] Linter configs match in pyproject.toml and .pre-commit-config.yaml
- [ ] Test paths consistent across pytest and coverage configs
- [ ] Python version consistent across all files

### Documentation Consistency
- [ ] CLAUDE.md reflects current architecture
- [ ] README matches actual setup process
- [ ] Comments in config files are accurate
- [ ] Version numbers match across files

### CI/CD Consistency
- [ ] GitHub Actions use same Python versions as project.requires-python
- [ ] CI installs dependencies from pyproject.toml
- [ ] Pre-commit hooks run same checks as CI
- [ ] Coverage thresholds match in pytest and CI

---

## Common Update Patterns

### Pattern 1: Adding a New Python Dependency

```bash
# Step 1: Add to pixi.toml
[dependencies]
fastapi = ">=0.104.0,<1.0.0"
uvicorn = {version = ">=0.24.0", extras = ["standard"]}

# Step 2: Mirror in pyproject.toml
[project]
dependencies = [
    "fastapi>=0.104.0,<1.0.0",
    "uvicorn[standard]>=0.24.0",
]

# Step 3: Update CLAUDE.md
## Technology Stack
- **Web Framework**: FastAPI 0.104+
- **ASGI Server**: Uvicorn with standard extras

## Common Commands
- Start server: `uvicorn main:app --reload`

# Step 4: Update .github/workflows/ci.yml (if needed)
- name: Test FastAPI endpoints
  run: pytest tests/test_api.py

# Step 5: Install and test
pixi install
pytest tests/
```

---

### Pattern 2: Adding Pre-commit Hooks

```yaml
# Step 1: Update .pre-commit-config.yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.8
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

# Step 2: Update pyproject.toml with ruff config
[tool.ruff]
line-length = 100
target-version = "py311"
select = ["E", "F", "I", "N", "W"]
ignore = ["E501"]  # Line too long (handled by formatter)

[tool.ruff.per-file-ignores]
"__init__.py" = ["F401"]  # Unused imports OK in __init__

# Step 3: Add to dev dependencies in pixi.toml
[dev-dependencies]
ruff = ">=0.1.8"
pre-commit = ">=3.5.0"

# Step 4: Update CLAUDE.md
## Development Setup
After cloning, run:
```bash
pixi install
pre-commit install
```

Code is automatically formatted and linted on commit.

# Step 5: Install and test
pixi install
pre-commit install
pre-commit run --all-files
```

---

### Pattern 3: Updating Project Version

```toml
# Step 1: Update pyproject.toml
[project]
name = "nwb-kg"
version = "0.2.0"  # Increment following semver

# Step 2: Update pixi.toml
[project]
name = "nwb-kg"
version = "0.2.0"
channels = ["conda-forge"]
platforms = ["linux-64", "osx-64", "osx-arm64", "win-64"]

# Step 3: Update CLAUDE.md
# NWB Knowledge Graph v0.2.0
[Update changelog section]

# Step 4: Create git tag
git tag -a v0.2.0 -m "Release v0.2.0: Added natural language query interface"
git push origin v0.2.0

# Step 5: Update .github/workflows/release.yml (if exists)
# Triggers on new tags matching v*
```

---

### Pattern 4: Adding New Test Configuration

```toml
# Step 1: Update pyproject.toml
[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = [
    "--verbose",
    "--cov=src",
    "--cov-report=html",
    "--cov-report=term-missing",
    "--cov-fail-under=70",
    "--strict-markers",
]
markers = [
    "slow: marks tests as slow (deselect with '-m \"not slow\"')",
    "integration: marks tests as integration tests",
    "unit: marks tests as unit tests",
]

[tool.coverage.run]
source = ["src"]
branch = true
omit = [
    "tests/*",
    "**/__pycache__/*",
    "**/conftest.py",
]

# Step 2: Update pixi.toml dev dependencies
[dev-dependencies]
pytest = ">=7.4.0"
pytest-cov = ">=4.1.0"
pytest-asyncio = ">=0.21.0"  # If using async tests
pytest-mock = ">=3.12.0"     # If using mocking

# Step 3: Add pixi task
[tasks]
test = "pytest"
test-cov = "pytest --cov --cov-report=html"
test-unit = "pytest -m unit"
test-integration = "pytest -m integration"

# Step 4: Update .github/workflows/ci.yml
- name: Run unit tests
  run: pytest -m unit --cov

- name: Run integration tests
  run: pytest -m integration
  env:
    NEO4J_URI: ${{ secrets.NEO4J_URI }}

# Step 5: Update CLAUDE.md
## Testing
- Run all tests: `pixi run test`
- Run with coverage: `pixi run test-cov`
- Run unit tests only: `pixi run test-unit`
- Run integration tests: `pixi run test-integration`
```

---

## Best Practices Summary

### 1. **Keep Dependencies Synchronized**
```python
# Use a script to verify sync
def check_dependency_sync():
    """Verify pixi.toml and pyproject.toml have matching dependencies"""
    pixi_deps = parse_pixi_dependencies("pixi.toml")
    pyproject_deps = parse_pyproject_dependencies("pyproject.toml")

    mismatches = compare_dependencies(pixi_deps, pyproject_deps)
    if mismatches:
        raise ValueError(f"Dependency mismatch: {mismatches}")
```

### 2. **Version Everything**
- Pin pre-commit hook versions
- Specify Python version ranges
- Use lock files (pixi.lock, requirements-lock.txt)

### 3. **Document Why, Not Just What**
```toml
# Good: Explains rationale
[dependencies]
pynwb = ">=2.5.0,<3.0.0"  # 2.5+ required for dynamic extensions, <3 for API stability

# Avoid: Just lists version
[dependencies]
pynwb = "2.5.0"
```

### 4. **Test Configuration Changes**
```bash
# After updating configs:
pixi install          # Verify pixi.toml
pip install -e ".[dev]"  # Verify pyproject.toml
pre-commit run --all-files  # Verify hooks
pytest                 # Verify test config
```

### 5. **Use Automation**
- GitHub Actions to check dependency sync
- Pre-commit hooks to validate config syntax
- Scripts to update versions across files

---

## Troubleshooting Common Issues

### Issue 1: Dependency Conflicts
```
Error: Dependency conflict between package A and B
```
**Solution**:
1. Check version constraints in both pixi.toml and pyproject.toml
2. Use `pixi list` to see resolved versions
3. Adjust version ranges to find compatible versions
4. Consider using `pixi update` to update to latest compatible

### Issue 2: Pre-commit Hooks Failing
```
Error: ruff hook failed
```
**Solution**:
1. Check if ruff version in .pre-commit-config.yaml matches installed version
2. Run `pre-commit autoupdate` to update hook versions
3. Test locally: `pre-commit run --all-files`
4. Check pyproject.toml for conflicting ruff configuration

### Issue 3: CI Pipeline Failures
```
Error: Module not found in GitHub Actions
```
**Solution**:
1. Verify dependencies are in pyproject.toml (not just pixi.toml)
2. Check if pip install command includes `[dev]` extras
3. Ensure Python version in CI matches project.requires-python
4. Check for missing system dependencies (Neo4j, etc.)

### Issue 4: Coverage Not Matching Local vs CI
```
Local: 85% coverage, CI: 45% coverage
```
**Solution**:
1. Check if test paths match in pytest config
2. Verify coverage omit patterns are correct
3. Ensure all test files are committed to git
4. Check if CI is running from correct directory

---

## Integration with Your NWB Project

### Your Project Structure
```
nwb-kg/
├── .github/
│   └── workflows/
│       └── ci.yml
├── .pre-commit-config.yaml
├── pixi.toml
├── pyproject.toml
├── CLAUDE.md (create if not exists)
├── src/
│   ├── parsing/
│   ├── graph/
│   └── query/
└── tests/
```

### Recommended Initial Setup

**1. pixi.toml** - For your NWB project:
```toml
[project]
name = "nwb-kg"
version = "0.1.0"
description = "NWB Knowledge Graph Query System"
channels = ["conda-forge"]
platforms = ["linux-64", "osx-64", "osx-arm64"]

[dependencies]
python = ">=3.11,<3.13"
fastapi = ">=0.104.0"
uvicorn = {version = ">=0.24.0", extras = ["standard"]}
pynwb = ">=2.5.0,<3.0.0"
h5py = ">=3.10.0"
neo4j = ">=5.0.0,<6.0.0"
anthropic = ">=0.8.0"  # Claude Sonnet 4.5 SDK
numpy = ">=1.24.0"
pandas = ">=2.0.0"

[dev-dependencies]
pytest = ">=7.4.0"
pytest-cov = ">=4.1.0"
pytest-asyncio = ">=0.21.0"
ruff = ">=0.1.0"
mypy = ">=1.7.0"
pre-commit = ">=3.5.0"

[tasks]
dev = "uvicorn src.main:app --reload"
test = "pytest"
test-cov = "pytest --cov=src --cov-report=html"
lint = "ruff check src tests"
format = "ruff format src tests"
typecheck = "mypy src"
```

---

## When to Use This Skill

Claude will automatically invoke this skill when:
- Adding or updating Python dependencies
- Creating new modules or features
- Updating testing configuration
- Modifying CI/CD pipelines
- Changing code quality tools (linters, formatters)
- Updating project metadata
- Setting up pre-commit hooks
- Ensuring configuration consistency
- Troubleshooting configuration issues

---

When this skill is invoked, provide expert guidance on maintaining consistent, well-configured Python projects with proper tooling integration across pixi, pyproject.toml, pre-commit, GitHub Actions, and documentation.
