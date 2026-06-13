---
name: claude-md-expert
description: Use this skill when creating, updating, or optimizing CLAUDE.md files for Claude Code projects. Invoke when initializing new projects, documenting architecture changes, ensuring Claude has effective project context, or maintaining project documentation that helps Claude work efficiently.
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash]
---

# CLAUDE.md Expert

This skill provides expertise in creating, maintaining, and optimizing CLAUDE.md files that help Claude Code work most effectively with your project.

## What is CLAUDE.md?

CLAUDE.md is a project-level documentation file that provides Claude Code with essential context about your project. It's read automatically when Claude starts working in a directory, helping Claude understand:
- What the project does and why it exists
- How the codebase is structured
- Key conventions and patterns
- Development workflow and common tasks

A well-crafted CLAUDE.md significantly improves Claude's effectiveness by reducing unnecessary exploration and aligning responses with project standards.

## CLAUDE.md Structure

### Required Sections

#### 1. Project Overview
```markdown
# Project Name

Brief 2-3 sentence description of what the project does.

## Purpose
What problem does this solve? What is the project's goal?

## Scope
What is included and excluded from this project?
```

**Best Practices:**
- Be concise but complete
- Include the "why" not just the "what"
- Mention target users or use cases
- State current development phase (MVP, production, experimental)

#### 2. Architecture

```markdown
## Architecture

High-level architecture description.

### Key Components
- **Component 1**: Purpose and responsibility
- **Component 2**: Purpose and responsibility

### Data Flow
1. User uploads file
2. Parser extracts metadata
3. Graph builder creates nodes
4. Neo4j stores graph

### Directory Structure
```
project/
├── src/           # Application code
│   ├── api/       # FastAPI endpoints
│   ├── parser/    # NWB file parsing
│   └── graph/     # Neo4j graph builder
├── tests/         # Test suite
└── specs/         # Requirements and specifications
```
```

**Best Practices:**
- Show high-level components and their relationships
- Include a visual directory structure
- Explain data flow for key operations
- Mention external dependencies (databases, APIs)
- Link to architecture diagrams if available

#### 3. Tech Stack

```markdown
## Tech Stack

- **Language**: Python 3.11+
- **Framework**: FastAPI 0.104+
- **Database**: Neo4j 5.x
- **Key Libraries**:
  - pynwb: NWB file parsing
  - neo4j: Graph database driver
  - h5py: HDF5 file validation
- **Testing**: pytest, pytest-cov
- **Package Manager**: pixi (conda-based)
```

**Best Practices:**
- List versions for critical dependencies
- Group by category (framework, database, testing, etc.)
- Explain non-obvious library choices
- Mention development vs. production dependencies

#### 4. Setup Instructions

```markdown
## Setup

### Prerequisites
- Python 3.11+
- Neo4j 5.x running locally or accessible remotely

### Installation
```bash
# Install dependencies with pixi
pixi install

# Activate environment
pixi shell
```

### Configuration
Copy `.env.example` to `.env` and configure:
- `NEO4J_URI`: Neo4j connection string
- `NEO4J_USER`: Database username
- `NEO4J_PASSWORD`: Database password
```

**Best Practices:**
- Include actual commands, not just descriptions
- Mention common setup issues and solutions
- Link to external setup guides if lengthy
- Include environment variable requirements

#### 5. Development Workflow

```markdown
## Development Workflow

### Running Tests
```bash
pytest tests/ -v --cov=src
```

### Running the Application
```bash
uvicorn src.api.main:app --reload
```

### Code Quality
```bash
# Run pre-commit hooks
pre-commit run --all-files

# Type checking
mypy src/
```

### Common Tasks

#### Adding a New API Endpoint
1. Define Pydantic models in `src/api/models.py`
2. Create endpoint in `src/api/routes/`
3. Add tests in `tests/api/`
4. Update OpenAPI documentation

#### Adding a New NWB Type
1. Define node schema in `src/graph/schemas.py`
2. Add parser logic in `src/parser/extractors.py`
3. Create graph builder in `src/graph/builders.py`
4. Add integration test in `tests/integration/`
```

**Best Practices:**
- Include actual commands with expected output
- Document common tasks step-by-step
- Show examples of frequent operations
- Link to relevant code sections

### Optional Sections

#### 6. Codebase Conventions

```markdown
## Codebase Conventions

### File Organization
- One class per file (except small related classes)
- Test files mirror source structure: `src/foo/bar.py` → `tests/foo/test_bar.py`
- Constants in `UPPER_CASE`
- Private functions prefixed with `_`

### Naming Patterns
- API models: `{Entity}Request`, `{Entity}Response`
- Graph nodes: `{NWBType}Node` classes
- Tests: `test_{function_name}_{scenario}`

### Error Handling
- Use custom exceptions in `src/exceptions.py`
- Always include context in exception messages
- Log errors before raising

### Import Order
1. Standard library
2. Third-party packages
3. Local imports
(Alphabetical within each group)
```

**Best Practices:**
- Document patterns that aren't obvious from code
- Explain "why" for non-standard conventions
- Include examples of good vs. bad patterns
- Keep it focused on what's actually enforced

#### 7. Domain Knowledge

```markdown
## Domain Knowledge

### NWB File Format
- Hierarchical HDF5 format for neuroscience data
- Core types: NWBFile, Subject, Device, Electrode, etc.
- Metadata vs. data distinction important for graph building

### Graph Schema Design
- One node per metadata entity (not per file)
- Relationships represent semantic connections
- Provenance tracked via `file_path` property on all nodes

### Key Constraints
- MVP limited to 15 core NWB types (fixed schema)
- No dynamic schema expansion
- Focus on metadata, not raw scientific data
```

**Best Practices:**
- Explain domain-specific terminology
- Document business rules and constraints
- Include glossary for specialized terms
- Link to external domain resources

#### 8. Known Issues & Gotchas

```markdown
## Known Issues & Gotchas

### NWB Parsing
- Some NWB files have incomplete metadata - validate before processing
- TimeSeries data can be huge - stream, don't load into memory
- Device names not always unique - use combination of name + metadata

### Neo4j
- Batch imports significantly faster than individual creates
- Index creation required before large imports
- Transaction size limited - batch in chunks of 1000

### Testing
- Neo4j test fixtures need cleanup between tests
- Mock file uploads with `io.BytesIO` for FastAPI tests
- Integration tests require running Neo4j instance
```

**Best Practices:**
- Document non-obvious issues that caused problems
- Include workarounds and solutions
- Explain why certain patterns must be followed
- Update as new issues are discovered

## Creating CLAUDE.md from Scratch

### Step-by-Step Process

1. **Analyze Project Structure**
   ```bash
   # Check project structure
   tree -L 3 -I '__pycache__|*.pyc|.git'

   # Identify dependencies
   cat pyproject.toml | grep -A 20 "\[project.dependencies\]"

   # Find main entry points
   find . -name "main.py" -o -name "__init__.py" | head -10
   ```

2. **Extract Key Information**
   - Read `README.md` for project overview
   - Check `pyproject.toml` for tech stack
   - Review `specs/` or `docs/` for architecture
   - Examine `tests/` for common patterns

3. **Create Initial Structure**
   ```markdown
   # [Project Name from pyproject.toml]

   [Brief description from README or pyproject.toml description]

   ## Purpose
   [What problem it solves]

   ## Architecture
   [High-level components]

   ## Tech Stack
   [Extracted from pyproject.toml]

   ## Setup
   [From README or infer from project structure]

   ## Development Workflow
   [Common commands from Makefile, package.json scripts, or README]
   ```

4. **Add Project-Specific Details**
   - Codebase conventions from actual code patterns
   - Domain knowledge from specs or documentation
   - Common tasks from commit history or issues

5. **Optimize for Claude**
   - Add context that reduces tool usage
   - Include concrete examples
   - Highlight critical patterns
   - Document project-specific terminology

## Maintaining CLAUDE.md

### Update Triggers

Update CLAUDE.md when:
- **Architecture changes**: New components, refactoring, data flow changes
- **Dependencies change**: Major version upgrades, new libraries added
- **Conventions evolve**: New patterns adopted, coding standards updated
- **Common tasks change**: New workflows, updated procedures
- **Domain knowledge grows**: New business rules, constraints discovered

### Maintenance Checklist

```markdown
- [ ] Project overview still accurate?
- [ ] Architecture diagram matches current state?
- [ ] Tech stack versions current?
- [ ] Setup instructions work on fresh clone?
- [ ] Development workflow commands still valid?
- [ ] Common tasks reflect current practices?
- [ ] Known issues list up to date?
```

### Detecting Outdated Information

```bash
# Check if dependencies changed
git diff HEAD~10 pyproject.toml pixi.toml

# Find new directories since last update
find src/ -type d -newer .claude/CLAUDE.md

# Check for new conventions in recent commits
git log --since="3 months ago" --grep="convention\|pattern\|standard"
```

## Optimizing for Claude Code

### What Makes CLAUDE.md Effective?

1. **Reduces Unnecessary Exploration**
   - Document directory structure clearly
   - Explain what each component does
   - Show where specific functionality lives

   ❌ **Ineffective**: "Code is in src/"
   ✅ **Effective**: "API endpoints in `src/api/routes/`, business logic in `src/services/`, data access in `src/repositories/`"

2. **Provides Concrete Examples**
   - Show actual commands with expected output
   - Include code snippets for common patterns
   - Demonstrate typical workflows

   ❌ **Ineffective**: "Run tests before committing"
   ✅ **Effective**:
   ```markdown
   Run tests before committing:
   ```bash
   pytest tests/ -v
   # Expected: 45 passed, 2 skipped in 3.2s
   ```
   ```

3. **Highlights Critical Patterns**
   - Point out non-obvious conventions
   - Explain "why" for important decisions
   - Document gotchas and edge cases

   ❌ **Ineffective**: "Use async/await"
   ✅ **Effective**: "Use async/await for all API endpoints to prevent blocking I/O during file uploads (files can be 10GB+)"

4. **Uses Project Terminology**
   - Define domain-specific terms
   - Create a shared vocabulary
   - Reduce ambiguity

   ❌ **Ineffective**: "Process the files"
   ✅ **Effective**: "Parse NWB files (HDF5 format) to extract metadata containers, then transform to graph nodes"

5. **Links to Relevant Resources**
   - Specs and requirements documents
   - External documentation
   - Related code sections

   ```markdown
   ## Related Documentation
   - [Requirements](specs/requirements.md): Full project specification
   - [NWB Format](https://nwb-schema.readthedocs.io/): NWB schema documentation
   - [Neo4j Cypher](https://neo4j.com/docs/cypher-manual/): Query language reference
   ```

### Claude Code-Specific Tips

1. **Include File Paths as Links**
   ```markdown
   API endpoints are defined in [src/api/routes/upload.py](src/api/routes/upload.py)
   ```

2. **Show Expected File Locations**
   ```markdown
   When adding a new graph node type:
   - Schema: `src/graph/schemas/{type}_schema.py`
   - Builder: `src/graph/builders/{type}_builder.py`
   - Tests: `tests/graph/test_{type}_builder.py`
   ```

3. **Document Test Patterns**
   ```markdown
   ## Testing Patterns

   ### FastAPI Endpoints
   ```python
   from fastapi.testclient import TestClient

   def test_upload_endpoint(client: TestClient):
       files = {"file": ("test.nwb", b"content", "application/octet-stream")}
       response = client.post("/api/v1/upload", files=files)
       assert response.status_code == 200
   ```
   ```

4. **Include Common Errors**
   ```markdown
   ## Common Errors

   ### "Neo4j connection refused"
   **Cause**: Neo4j not running or wrong connection string
   **Fix**:
   ```bash
   # Check Neo4j is running
   docker ps | grep neo4j

   # Verify connection in .env
   NEO4J_URI=bolt://localhost:7687
   ```
   ```

## Integration with Other Documentation

### CLAUDE.md vs. README.md

**README.md** (for humans):
- Project introduction and motivation
- Installation for end users
- Usage examples for end users
- Contributing guidelines
- License and credits

**CLAUDE.md** (for Claude):
- Architecture and technical design
- Development environment setup
- Codebase conventions and patterns
- Common development tasks
- Project-specific context that helps Claude code

**Overlap**: Basic setup instructions, tech stack

**Rule of thumb**: README is marketing + user guide, CLAUDE.md is developer guide + AI context

### CLAUDE.md vs. CONTRIBUTING.md

**CONTRIBUTING.md**:
- How to contribute (fork, PR process)
- Code of conduct
- Communication channels
- Review process

**CLAUDE.md**:
- How to develop (setup, workflow, testing)
- What the code does (architecture, patterns)
- Where things are (directory structure)

**Integration**: CLAUDE.md can link to CONTRIBUTING.md for contribution process

### CLAUDE.md vs. specs/ or docs/

**specs/** or **docs/**:
- Detailed requirements
- API specifications
- Architecture decision records (ADRs)
- In-depth technical documentation

**CLAUDE.md**:
- High-level summary of architecture
- Quick reference for common tasks
- Links to detailed specs

**Integration**: CLAUDE.md should reference specs/docs for details:
```markdown
## Architecture

[Brief 2-3 paragraph overview]

For detailed architecture, see [specs/architecture.md](specs/architecture.md)
```

## Example CLAUDE.md Template

```markdown
# Project Name

Brief description of what this project does.

## Purpose

What problem does this solve and why does it exist?

## Architecture

High-level architecture overview.

### Key Components
- **Component 1**: Purpose
- **Component 2**: Purpose

### Directory Structure
```
project/
├── src/           # Application code
├── tests/         # Test suite
└── specs/         # Requirements
```

## Tech Stack

- **Language**: Python 3.11+
- **Framework**: FastAPI
- **Database**: PostgreSQL
- **Testing**: pytest

## Setup

### Prerequisites
- Python 3.11+
- PostgreSQL 14+

### Installation
```bash
pip install -e .
```

### Configuration
Create `.env`:
```
DATABASE_URL=postgresql://localhost/dbname
```

## Development Workflow

### Running Tests
```bash
pytest
```

### Running the Application
```bash
uvicorn src.main:app --reload
```

### Common Tasks

#### Adding a New Feature
1. Create feature branch
2. Implement with tests
3. Run linters
4. Submit PR

## Codebase Conventions

- File organization: One class per file
- Naming: `snake_case` for functions, `PascalCase` for classes
- Imports: Standard lib, third-party, local

## Related Documentation

- [Requirements](specs/requirements.md)
- [API Docs](docs/api.md)
- [Contributing](CONTRIBUTING.md)
```

## CLAUDE.md for Different Project Types

### Web Application (FastAPI/Flask/Django)

Focus on:
- API endpoint organization
- Request/response patterns
- Database models and migrations
- Authentication/authorization
- Frontend integration (if applicable)

### Library/Package

Focus on:
- Public API surface
- Usage examples
- Extension points
- Testing strategies for library code
- Documentation generation

### Data Pipeline/ETL

Focus on:
- Data sources and sinks
- Transformation logic
- Scheduling and orchestration
- Error handling and retries
- Data validation

### CLI Tool

Focus on:
- Command structure
- Configuration options
- Common use cases
- Output formats
- Error handling

## Best Practices Summary

✅ **DO**:
- Keep it concise (aim for readable in 5-10 minutes)
- Use concrete examples and actual commands
- Update when architecture or conventions change
- Include "why" for non-obvious decisions
- Link to detailed documentation
- Show directory structure visually
- Document common tasks step-by-step

❌ **DON'T**:
- Duplicate everything from README
- Include implementation details better suited for code comments
- Let it become outdated
- Use vague descriptions without examples
- Forget to explain domain-specific terminology
- Make it a dumping ground for all documentation

## Measuring Effectiveness

A good CLAUDE.md is effective if:
- Claude rarely asks clarifying questions about project structure
- Claude follows project conventions without reminders
- Claude knows where to find/create specific types of files
- Claude suggests solutions aligned with project architecture
- Claude uses project-specific terminology correctly

Track these signals and update CLAUDE.md when you notice gaps.

## Tools and Automation

### Generating Initial CLAUDE.md

```bash
# Extract project name and description
cat pyproject.toml | grep -E "^name|^description"

# Generate directory tree
tree -L 3 -I '__pycache__|*.pyc|.git|node_modules' > structure.txt

# List dependencies
cat pyproject.toml | grep -A 100 "dependencies"

# Find main entry points
find . -name "main.py" -o -name "__main__.py"
```

### Checking for Updates Needed

```bash
# Files changed since last CLAUDE.md update
find . -newer CLAUDE.md -type f -name "*.py" | grep -v __pycache__

# New dependencies added
git diff $(git log -1 --format=%H CLAUDE.md) pyproject.toml

# New directories created
find src/ -type d -newer CLAUDE.md
```

### Validation Checklist

```markdown
- [ ] All commands run successfully
- [ ] File paths exist and are correct
- [ ] Dependencies versions match actual
- [ ] Examples use current API
- [ ] Links resolve correctly
- [ ] Directory structure matches actual
- [ ] Common tasks list is current
```

## Conclusion

CLAUDE.md is a powerful tool for improving Claude Code's effectiveness. Invest time in creating a thorough, well-structured CLAUDE.md, and keep it updated as your project evolves. The time saved in reduced back-and-forth and better-aligned suggestions will far outweigh the maintenance cost.