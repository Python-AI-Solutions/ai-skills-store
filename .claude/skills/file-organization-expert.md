---
name: file-organization-expert
description: Use this skill when organizing project files, moving files to correct locations, validating directory structure, or ensuring the project follows the canonical directory layout. Invoke when files are created in wrong locations, the project structure needs validation, or when setting up new modules/components.
allowed-tools: [Bash, Read, Write, Edit, Glob, Grep]
---

# File Organization Expert

This skill ensures the dandi-kg project follows a clean, standardized directory structure that keeps code organized and maintainable.

## Canonical Directory Structure

### dandi-kg Project Structure

```
dandi-kg/                             # Repository root
├── .github/
│   └── workflows/                    # CI/CD: tests, linting, type checking, deployment
│       ├── ci.yml
│       └── deploy.yml
│
├── .claude/                          # Claude Code integration
│   ├── commands/                     # Custom slash commands
│   │   └── example.md
│   └── skills/                       # Claude Code skills
│       ├── nwb-expert.md
│       ├── neo4j-expert.md
│       └── ...
│
├── .specify/                         # Optional: Specify AI constitution
│   └── memory/
│       └── constitution.md           # Project-specific AI rules
│
├── dandi_kg/                         # Main package (note: underscore for Python)
│   ├── __init__.py
│   ├── __main__.py                   # Entry point for `python -m dandi_kg`
│   │
│   ├── core/                         # Core business logic
│   │   ├── __init__.py
│   │   └── domain.py                 # Domain models and core concepts
│   │
│   ├── api/                          # FastAPI application
│   │   ├── __init__.py
│   │   ├── app.py                    # FastAPI app creation
│   │   ├── routes/
│   │   │   ├── __init__.py
│   │   │   ├── upload.py             # File upload endpoints
│   │   │   ├── query.py              # Query endpoints
│   │   │   └── health.py             # Health check
│   │   ├── middleware/
│   │   │   ├── __init__.py
│   │   │   ├── error_handler.py
│   │   │   └── logging.py
│   │   └── dependencies.py           # Dependency injection
│   │
│   ├── cli/                          # CLI interface (if applicable)
│   │   ├── __init__.py
│   │   ├── commands/
│   │   │   ├── __init__.py
│   │   │   ├── parse.py
│   │   │   └── upload.py
│   │   └── __main__.py
│   │
│   ├── parser/                       # NWB file parsing
│   │   ├── __init__.py
│   │   ├── nwb_parser.py             # Main NWB parser
│   │   ├── extractors/               # Metadata extractors
│   │   │   ├── __init__.py
│   │   │   ├── device.py
│   │   │   ├── electrode.py
│   │   │   └── timeseries.py
│   │   ├── validators/               # File validators
│   │   │   ├── __init__.py
│   │   │   ├── h5py_validator.py
│   │   │   └── schema_validator.py
│   │   └── utils.py
│   │
│   ├── graph/                        # Neo4j graph building
│   │   ├── __init__.py
│   │   ├── builders/                 # Node builders
│   │   │   ├── __init__.py
│   │   │   ├── base.py
│   │   │   ├── nwb_file.py
│   │   │   ├── device.py
│   │   │   └── electrode.py
│   │   ├── schemas/                  # Graph schemas
│   │   │   ├── __init__.py
│   │   │   └── nwb_schema.py
│   │   ├── relationships.py          # Relationship definitions
│   │   └── bulk_import.py
│   │
│   ├── models/                       # Data models & schemas
│   │   ├── __init__.py
│   │   ├── nwb_entities.py           # NWB domain entities
│   │   ├── api_schemas.py            # Pydantic schemas for API
│   │   └── graph_nodes.py            # Graph node models
│   │
│   ├── services/                     # Business logic & integrations
│   │   ├── __init__.py
│   │   ├── file_processor.py         # File processing service
│   │   ├── graph_service.py          # Graph operations
│   │   ├── query_service.py          # Query processing
│   │   └── nl_to_cypher.py           # NL to Cypher conversion
│   │
│   ├── database/                     # Database layer
│   │   ├── __init__.py
│   │   ├── connection.py             # Neo4j connection management
│   │   ├── repositories/             # Data access layer
│   │   │   ├── __init__.py
│   │   │   ├── nwb_repository.py
│   │   │   └── query_repository.py
│   │   └── migrations/               # Database migrations/indexes
│   │       ├── __init__.py
│   │       └── 001_create_indexes.py
│   │
│   ├── utils/                        # Utilities & helpers
│   │   ├── __init__.py
│   │   ├── logger.py                 # Logging configuration
│   │   ├── exceptions.py             # Custom exceptions
│   │   └── helpers.py                # General helpers
│   │
│   └── config/                       # Configuration management
│       ├── __init__.py
│       └── settings.py               # Environment-based config
│
├── tests/                            # Test suite
│   ├── __init__.py
│   ├── conftest.py                   # Shared pytest fixtures
│   │
│   ├── unit/                         # Fast, isolated tests
│   │   ├── __init__.py
│   │   ├── test_parser/
│   │   │   ├── __init__.py
│   │   │   ├── test_nwb_parser.py
│   │   │   └── test_extractors/
│   │   ├── test_graph/
│   │   │   ├── __init__.py
│   │   │   └── test_builders/
│   │   ├── test_services/
│   │   │   ├── __init__.py
│   │   │   └── test_query_service.py
│   │   └── test_utils/
│   │       ├── __init__.py
│   │       └── test_helpers.py
│   │
│   ├── integration/                  # Component interaction tests
│   │   ├── __init__.py
│   │   ├── test_api/
│   │   │   ├── __init__.py
│   │   │   ├── test_upload.py
│   │   │   └── test_query.py
│   │   ├── test_database/
│   │   │   ├── __init__.py
│   │   │   └── test_neo4j.py
│   │   └── test_pipeline/
│   │       ├── __init__.py
│   │       └── test_end_to_end.py
│   │
│   └── e2e/                          # End-to-end workflow tests
│       ├── __init__.py
│       └── test_workflows.py
│
├── docs/                             # Documentation (committed to git)
│   ├── architecture.md               # System design
│   ├── api.md                        # API documentation
│   ├── deployment.md                 # Deployment guide
│   ├── development.md                # Development guide
│   └── temp/                         # AI-generated docs (NOT in git)
│       └── .gitkeep
│
├── scripts/                          # Utility & automation scripts
│   ├── setup.sh                      # Environment setup
│   ├── deploy.sh                     # Deployment automation
│   ├── seed_data.py                  # Data seeding for Neo4j
│   ├── create_indexes.py             # Index creation script
│   └── temp/                         # Temporary AI scripts (gitignored)
│       └── .gitkeep
│
├── specs/                            # Requirements & specifications
│   ├── requirements.md               # Full requirements
│   ├── architecture.md               # Architecture decisions
│   └── api_spec.md                   # API specification
│
├── data/                             # Sample/test data (large files gitignored)
│   ├── .gitkeep
│   ├── sample.nwb                    # Small sample file (committed)
│   └── large/                        # Large files (gitignored)
│
├── logs/                             # Application logs (gitignored)
├── uploads/                          # Uploaded files (gitignored)
├── outputs/                          # Generated outputs (gitignored)
│
├── .env.example                      # Environment variable template
├── .env                              # Actual env vars (gitignored)
├── .gitignore
├── .gitattributes                    # Git LFS, line endings
├── .pre-commit-config.yaml           # Pre-commit hooks
│
├── pixi.toml                         # Pixi package manager config
├── pixi.lock                         # Locked dependencies
├── pyproject.toml                    # Python project & tool config
│
├── README.md                         # Project overview
├── CLAUDE.md                         # AI assistance guide
├── CHANGELOG.md                      # Version history (optional)
└── LICENSE                           # Project license
```

## File Placement Rules

### By File Type

#### Python Source Files (.py)

| File Type | Correct Location | Example |
|-----------|-----------------|---------|
| API endpoints | `dandi_kg/api/routes/` | `upload.py`, `query.py` |
| NWB parsers | `dandi_kg/parser/` | `nwb_parser.py` |
| Metadata extractors | `dandi_kg/parser/extractors/` | `device.py`, `electrode.py` |
| Graph builders | `dandi_kg/graph/builders/` | `nwb_file.py`, `device.py` |
| Data models | `dandi_kg/models/` | `nwb_entities.py`, `api_schemas.py` |
| Business logic | `dandi_kg/services/` | `file_processor.py`, `query_service.py` |
| Database access | `dandi_kg/database/repositories/` | `nwb_repository.py` |
| Utilities | `dandi_kg/utils/` | `logger.py`, `helpers.py` |
| Configuration | `dandi_kg/config/` | `settings.py` |
| CLI commands | `dandi_kg/cli/commands/` | `parse.py`, `upload.py` |

#### Test Files (test_*.py)

Tests should **mirror the source structure**:

| Source File | Test Location |
|-------------|---------------|
| `dandi_kg/parser/nwb_parser.py` | `tests/unit/test_parser/test_nwb_parser.py` |
| `dandi_kg/api/routes/upload.py` | `tests/integration/test_api/test_upload.py` |
| `dandi_kg/services/query_service.py` | `tests/unit/test_services/test_query_service.py` |
| `dandi_kg/graph/builders/device.py` | `tests/unit/test_graph/test_builders/test_device.py` |

**Test Type Guidelines:**
- **Unit tests** (`tests/unit/`): Fast, isolated, mocked dependencies
- **Integration tests** (`tests/integration/`): Test component interactions (API + DB, Parser + Graph)
- **E2E tests** (`tests/e2e/`): Full workflow tests (upload → parse → graph → query)

#### Configuration Files

| File | Location | Description |
|------|----------|-------------|
| `pyproject.toml` | Root | Python project config, dependencies, tool settings |
| `pixi.toml` | Root | Pixi package manager config |
| `.pre-commit-config.yaml` | Root | Pre-commit hooks |
| `.env.example` | Root | Environment variable template |
| `.env` | Root (gitignored) | Actual secrets |
| `.gitignore` | Root | Git ignore rules |
| `.gitattributes` | Root | Git LFS, line endings |

#### Documentation Files (.md)

| File Type | Location | Gitignored |
|-----------|----------|------------|
| Project docs | `docs/` | No |
| Requirements | `specs/` | No |
| AI-generated temp docs | `docs/temp/` | **Yes** |
| README | Root | No |
| CLAUDE.md | Root | No |
| CHANGELOG | Root | No |

#### Scripts

| Script Type | Location | Gitignored |
|-------------|----------|------------|
| Setup/deploy scripts | `scripts/` | No |
| Permanent utilities | `scripts/` | No |
| Temporary AI scripts | `scripts/temp/` | **Yes** |

#### Data Files

| Data Type | Location | Gitignored |
|-----------|----------|------------|
| Small samples | `data/` | No |
| Large NWB files | `data/large/` | **Yes** |
| Uploaded files | `uploads/` | **Yes** |
| Generated outputs | `outputs/` | **Yes** |
| Logs | `logs/` | **Yes** |

## Detecting Misplaced Files

### Check for Files in Wrong Locations

```bash
# Find Python files in root (should be in dandi_kg/)
find . -maxdepth 1 -name "*.py" -type f

# Find test files in wrong locations
find . -name "test_*.py" -not -path "./tests/*"

# Find markdown files in root (check if they belong in docs/)
find . -maxdepth 1 -name "*.md" -type f | grep -v "README\|CLAUDE\|CHANGELOG\|LICENSE"

# Find scripts in wrong locations
find . -name "*.sh" -not -path "./scripts/*" -not -path "./.git/*"

# Find config files in wrong locations
find . -name "*.toml" -o -name "*.yaml" | grep -v "^\./\."
```

### Identify File Purpose

To determine correct location, analyze:

1. **File name patterns:**
   - `test_*.py` → `tests/`
   - `*_parser.py` → `dandi_kg/parser/`
   - `*_builder.py` → `dandi_kg/graph/builders/`
   - `*_repository.py` → `dandi_kg/database/repositories/`
   - `*.sh` → `scripts/`

2. **File contents:**
   ```python
   # If contains FastAPI routes
   @app.get("/...")
   # → dandi_kg/api/routes/

   # If contains pytest fixtures
   @pytest.fixture
   # → tests/conftest.py or tests/{unit|integration}/

   # If imports pynwb
   from pynwb import NWBHDF5IO
   # → dandi_kg/parser/

   # If imports neo4j
   from neo4j import GraphDatabase
   # → dandi_kg/database/ or dandi_kg/graph/
   ```

3. **Import statements:**
   ```python
   # If imported by API routes
   from dandi_kg.services.query_service import QueryService
   # → dandi_kg/services/query_service.py
   ```

## Moving Files Correctly

### Moving Files

**NOTE: This skill does NOT execute git commands. It only suggests file movements.**

```bash
# Move files to correct locations
mv old_location.py new_location.py

# Example: Move misplaced parser to correct location
mv parser.py dandi_kg/parser/nwb_parser.py

# Example: Move test to correct location
mv test_parser.py tests/unit/test_parser/test_nwb_parser.py
```

**To preserve git history:** If you want to preserve git history, manually run `git mv` instead of `mv` after reviewing the suggestions above.

### Update Imports After Moving

When moving Python files, update imports in:

1. **Files that import the moved module:**
   ```python
   # Before move
   from parser import parse_nwb

   # After moving parser.py → dandi_kg/parser/nwb_parser.py
   from dandi_kg.parser.nwb_parser import parse_nwb
   ```

2. **The moved file's internal imports:**
   ```python
   # If the moved file imports other project modules
   # Update relative imports to absolute imports

   # Before (in root/parser.py)
   from utils import logger

   # After (in dandi_kg/parser/nwb_parser.py)
   from dandi_kg.utils import logger
   ```

3. **Test files:**
   ```python
   # Update test imports to match new location
   from dandi_kg.parser.nwb_parser import parse_nwb
   ```

### Find All Import References

```bash
# Find all files that import a module
grep -r "from parser import" --include="*.py"
grep -r "import parser" --include="*.py"

# After identifying files, update each one
```

### Create Missing Directories

When moving files to new locations, ensure directories exist:

```bash
# Create directory structure with __init__.py files
mkdir -p dandi_kg/parser/extractors
touch dandi_kg/parser/__init__.py
touch dandi_kg/parser/extractors/__init__.py

# Then move file
mv extractor.py dandi_kg/parser/extractors/device.py
```

## Validation & Structure Checks

### Validate Project Structure

```bash
# Check that main package exists
test -d dandi_kg && echo "✓ Main package exists" || echo "✗ Missing dandi_kg/"

# Check for __init__.py in all packages
find dandi_kg -type d -exec test -f {}/__init__.py \; -print || echo "Missing __init__.py"

# Check tests mirror source structure
diff <(find dandi_kg -type d | sed 's/dandi_kg/tests\/unit\/test_dandi_kg/') \
     <(find tests/unit -type d)

# Check for files in root that shouldn't be there
find . -maxdepth 1 -type f -name "*.py" | grep -v "__" | grep -v "setup.py"
```

### Ensure __init__.py Files Exist

Every Python package directory needs `__init__.py`:

```bash
# Find directories without __init__.py
find dandi_kg -type d ! -path "*/\.*" -exec sh -c '
  test -f "$1/__init__.py" || echo "Missing: $1/__init__.py"
' sh {} \;

# Create missing __init__.py files
find dandi_kg -type d ! -path "*/\.*" -exec sh -c '
  test -f "$1/__init__.py" || touch "$1/__init__.py"
' sh {} \;
```

### Check Test Structure Mirrors Source

```bash
# Ensure each source module has corresponding test
for src in $(find dandi_kg -name "*.py" ! -name "__*"); do
  # Convert dandi_kg/parser/nwb_parser.py → tests/unit/test_parser/test_nwb_parser.py
  test_file=$(echo $src | sed 's/dandi_kg/tests\/unit\/test/' | sed 's/\.py$//' | sed 's|/\([^/]*\)$|/test_\1.py|')
  test -f "$test_file" || echo "Missing test: $test_file for $src"
done
```

## Auto-Organization Workflows

### Before Creating New Files

When creating a new module, determine correct location:

1. **API endpoint?** → `dandi_kg/api/routes/{name}.py`
2. **NWB parser?** → `dandi_kg/parser/{name}.py`
3. **Graph builder?** → `dandi_kg/graph/builders/{name}.py`
4. **Data model?** → `dandi_kg/models/{name}.py`
5. **Business logic?** → `dandi_kg/services/{name}.py`
6. **Utility?** → `dandi_kg/utils/{name}.py`
7. **Test?** → Mirror source location in `tests/unit/` or `tests/integration/`

### After Creating New Files

If files were created in wrong location:

1. **Identify misplaced files** (see Detection section)
2. **Determine correct location** (see Placement Rules)
3. **Create destination directories** if needed
4. **Move files** using `mv` (or manually use `git mv` to preserve history)
5. **Update imports** in all affected files
6. **Run tests** to ensure nothing broke

### Cleanup Root Directory

The root directory should only contain:
- Configuration files (`.toml`, `.yaml`, `.env.example`)
- Documentation (`README.md`, `CLAUDE.md`, `CHANGELOG.md`, `LICENSE`)
- Git files (`.git/`, `.gitignore`, `.gitattributes`)
- CI files (`.github/`)
- Claude files (`.claude/`)
- Top-level directories (`dandi_kg/`, `tests/`, `docs/`, etc.)

**Move everything else** to appropriate locations:
```bash
# Find files that don't belong in root
find . -maxdepth 1 -type f \
  ! -name "README.md" \
  ! -name "CLAUDE.md" \
  ! -name "CHANGELOG.md" \
  ! -name "LICENSE" \
  ! -name "*.toml" \
  ! -name "*.yaml" \
  ! -name "*.yml" \
  ! -name ".env.example" \
  ! -name ".gitignore" \
  ! -name ".gitattributes"
```

## Common Scenarios

### Scenario 1: API Endpoint Created in Root

**Problem:**
```
dandi-kg/
├── upload_endpoint.py          # ✗ Wrong location
└── dandi_kg/
    └── api/
        └── routes/
```

**Solution:**
```bash
# Move to correct location
mv upload_endpoint.py dandi_kg/api/routes/upload.py

# Update imports in other files
# If app.py imported it:
# Before: from upload_endpoint import router
# After:  from dandi_kg.api.routes.upload import router

# Create test file
touch tests/integration/test_api/test_upload.py
```

### Scenario 2: Test File in Source Directory

**Problem:**
```
dandi_kg/
└── parser/
    ├── nwb_parser.py
    └── test_nwb_parser.py      # ✗ Wrong location
```

**Solution:**
```bash
# Create test directory structure
mkdir -p tests/unit/test_parser

# Move test file
mv dandi_kg/parser/test_nwb_parser.py tests/unit/test_parser/test_nwb_parser.py

# Update imports in test file
# Before: from nwb_parser import parse_nwb
# After:  from dandi_kg.parser.nwb_parser import parse_nwb
```

### Scenario 3: Utility Script in Root

**Problem:**
```
dandi-kg/
├── seed_database.py            # ✗ Wrong location
└── scripts/
```

**Solution:**
```bash
# Move to scripts
git mv seed_database.py scripts/seed_data.py

# Make executable if needed
chmod +x scripts/seed_data.py

# Add shebang if needed
echo '#!/usr/bin/env python3' | cat - scripts/seed_data.py > temp && mv temp scripts/seed_data.py
```

### Scenario 4: Multiple Files Need Organization

**Problem:**
```
dandi-kg/
├── parser.py                   # → dandi_kg/parser/nwb_parser.py
├── graph_builder.py            # → dandi_kg/graph/builders/base.py
├── neo4j_conn.py              # → dandi_kg/database/connection.py
├── test_parser.py             # → tests/unit/test_parser/test_nwb_parser.py
└── utils.py                   # → dandi_kg/utils/helpers.py
```

**Solution:**
```bash
# Create directory structure
mkdir -p dandi_kg/parser
mkdir -p dandi_kg/graph/builders
mkdir -p dandi_kg/database
mkdir -p dandi_kg/utils
mkdir -p tests/unit/test_parser

# Move files
git mv parser.py dandi_kg/parser/nwb_parser.py
git mv graph_builder.py dandi_kg/graph/builders/base.py
git mv neo4j_conn.py dandi_kg/database/connection.py
git mv utils.py dandi_kg/utils/helpers.py
git mv test_parser.py tests/unit/test_parser/test_nwb_parser.py

# Create __init__.py files
touch dandi_kg/parser/__init__.py
touch dandi_kg/graph/__init__.py
touch dandi_kg/graph/builders/__init__.py
touch dandi_kg/database/__init__.py
touch dandi_kg/utils/__init__.py
touch tests/unit/test_parser/__init__.py

# Update all imports (scan codebase for references)
grep -r "from parser import" --include="*.py"
# Update each file found...
```

## Best Practices

### Package Naming

✅ **Use underscores for Python packages:**
```
dandi_kg/          # ✓ Correct (importable as: import dandi_kg)
```

❌ **Avoid hyphens:**
```
dandi-kg/          # ✗ Wrong (can't import dandi-kg)
```

### Import Paths

Always use **absolute imports** from package root:

```python
# ✓ Good: Absolute imports
from dandi_kg.parser.nwb_parser import parse_nwb
from dandi_kg.utils.logger import get_logger

# ✗ Bad: Relative imports from parent
from ..utils.logger import get_logger
```

### Test Structure

Mirror source structure exactly:

```
Source:
dandi_kg/parser/extractors/device.py

Test:
tests/unit/test_parser/test_extractors/test_device.py
```

### Temporary Files

Use temp/ directories for AI-generated or temporary files:

```
docs/temp/          # Temporary AI-generated docs (gitignored)
scripts/temp/       # Temporary AI scripts (gitignored)
data/large/         # Large data files (gitignored)
```

**Add to .gitignore:**
```
docs/temp/
scripts/temp/
data/large/
*.pyc
__pycache__/
.env
logs/
uploads/
outputs/
```

### When to Use Each Directory

**dandi_kg/core/** - Core domain logic that doesn't depend on infrastructure
- Domain models
- Business rules
- Pure functions

**dandi_kg/services/** - Orchestration and business logic
- Coordinate multiple components
- Implement use cases
- External API integrations

**dandi_kg/database/repositories/** - Data access layer
- Database queries
- Data persistence
- Repository pattern

**dandi_kg/utils/** - Generic utilities
- Logging
- Helpers
- Decorators
- Constants

**scripts/** - One-off or maintenance scripts
- Setup scripts
- Deployment automation
- Data migration
- Database seeding

## Validation Commands

### Quick Structure Check

```bash
# Run all validation checks
echo "=== Checking main package ==="
test -d dandi_kg && echo "✓ dandi_kg/ exists" || echo "✗ Missing dandi_kg/"

echo "=== Checking for files in root ==="
find . -maxdepth 1 -type f -name "*.py" | grep -v setup.py || echo "✓ No stray .py files in root"

echo "=== Checking __init__.py files ==="
find dandi_kg -type d -exec sh -c 'test -f "$1/__init__.py" && echo "✓ $1" || echo "✗ Missing: $1/__init__.py"' sh {} \;

echo "=== Checking test structure ==="
test -d tests/unit && echo "✓ tests/unit/ exists" || echo "✗ Missing tests/unit/"
test -d tests/integration && echo "✓ tests/integration/ exists" || echo "✗ Missing tests/integration/"
test -f tests/conftest.py && echo "✓ tests/conftest.py exists" || echo "✗ Missing tests/conftest.py"

echo "=== Checking documentation ==="
test -f README.md && echo "✓ README.md exists" || echo "✗ Missing README.md"
test -f CLAUDE.md && echo "✓ CLAUDE.md exists" || echo "✗ Missing CLAUDE.md"
test -d docs && echo "✓ docs/ exists" || echo "✗ Missing docs/"

echo "=== Checking configuration ==="
test -f pyproject.toml && echo "✓ pyproject.toml exists" || echo "✗ Missing pyproject.toml"
test -f .pre-commit-config.yaml && echo "✓ .pre-commit-config.yaml exists" || echo "✗ Missing .pre-commit-config.yaml"
```

### Complete Structure Validation

```bash
# Create structure validation script
cat > scripts/validate_structure.sh << 'EOF'
#!/bin/bash
set -e

echo "Validating dandi-kg project structure..."

# Check required directories
required_dirs=(
  "dandi_kg"
  "tests/unit"
  "tests/integration"
  "docs"
  "scripts"
  "specs"
)

for dir in "${required_dirs[@]}"; do
  if [ -d "$dir" ]; then
    echo "✓ $dir"
  else
    echo "✗ Missing: $dir"
    exit 1
  fi
done

# Check no .py files in root (except setup.py)
if find . -maxdepth 1 -name "*.py" -not -name "setup.py" | grep -q .; then
  echo "✗ Found .py files in root:"
  find . -maxdepth 1 -name "*.py" -not -name "setup.py"
  exit 1
else
  echo "✓ No stray .py files in root"
fi

# Check __init__.py in all package directories
find dandi_kg -type d | while read dir; do
  if [ ! -f "$dir/__init__.py" ]; then
    echo "✗ Missing __init__.py in $dir"
    exit 1
  fi
done
echo "✓ All packages have __init__.py"

echo "✓ Structure validation passed!"
EOF

chmod +x scripts/validate_structure.sh
```

## Summary

The dandi-kg project follows a clean, standardized directory structure:

- **Main package**: `dandi_kg/` (with underscores)
- **Tests mirror source**: `tests/unit/test_{module}/`
- **Config in root**: `pyproject.toml`, `pixi.toml`, etc.
- **Docs committed**: `docs/`, `specs/`
- **AI temp files gitignored**: `docs/temp/`, `scripts/temp/`
- **Always use `git mv`** to preserve history
- **Update imports** after moving files
- **Validate structure** regularly

This structure keeps the codebase organized, maintainable, and easy to navigate for both humans and AI assistants.
