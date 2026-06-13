# Claude Code Skills Creation Guide

A comprehensive guide to creating well-architected, conflict-free Claude Code skills based on real-world experience building a 10-skill system for the dandi-kg project.

## Table of Contents

1. [What Are Claude Code Skills?](#what-are-claude-code-skills)
2. [When to Create a Skill](#when-to-create-a-skill)
3. [Skill Anatomy](#skill-anatomy)
4. [Design Principles](#design-principles)
5. [Step-by-Step Creation Process](#step-by-step-creation-process)
6. [Scope and Boundaries](#scope-and-boundaries)
7. [Best Practices](#best-practices)
8. [Common Pitfalls](#common-pitfalls)
9. [Skill Categories](#skill-categories)
10. [Validation and Testing](#validation-and-testing)
11. [Real-World Examples](#real-world-examples)

---

## What Are Claude Code Skills?

Claude Code skills are **specialized capabilities** that Claude can invoke to handle specific domains or tasks. Think of them as expert modes that provide:

- **Domain expertise** (e.g., NWB file formats, Neo4j databases)
- **Methodological guidance** (e.g., Spec-Driven Development, git commit messages)
- **Structural enforcement** (e.g., file organization, configuration management)

### Key Characteristics

- **Model-invoked**: Claude decides when to use them based on the skill description
- **Scoped expertise**: Each skill has a clear, bounded domain
- **Tool access**: Skills can use specific tools (Read, Write, Edit, Bash, etc.)
- **Stateless**: Each invocation is independent

### File Structure

```
.claude/
└── skills/
    ├── domain-expert.md
    ├── methodology-skill.md
    └── structural-skill.md
```

---

## When to Create a Skill

### ✅ Create a Skill When:

1. **Specialized domain knowledge** is needed repeatedly
   - Example: NWB file format expertise, Neo4j Cypher queries

2. **Complex methodology** needs to be followed consistently
   - Example: Spec-Driven Development workflow, git commit message conventions

3. **Structural patterns** need enforcement across the project
   - Example: Python file organization, configuration management

4. **Repeated tasks** have specific workflows
   - Example: Adding FastAPI endpoints, creating Neo4j schemas

5. **Domain-specific best practices** need to be maintained
   - Example: Testing strategies, documentation standards

### ❌ Don't Create a Skill When:

1. **One-off tasks** that won't repeat
2. **Simple operations** that don't need guidance
3. **Tasks already covered** by existing skills
4. **Overlapping domains** with existing skills (causes conflicts!)

---

## Skill Anatomy

### YAML Frontmatter

```yaml
---
name: skill-name
description: Clear, concise description of WHEN to invoke this skill. Be specific about triggers and scope.
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash]
---
```

**Critical**: The `description` is how Claude decides to invoke your skill. Make it:
- Action-oriented ("Use this skill when...")
- Specific about triggers ("Invoke when the user mentions X, wants to Y, or needs Z")
- Clear about what it does NOT handle

### Skill Structure Template

```markdown
---
name: example-skill
description: Use this skill when [specific triggers]. Does NOT handle [exclusions]. Invoke when [use cases].
allowed-tools: [Read, Write, Edit, Grep]
---

# Skill Title

Brief introduction explaining what this skill does and why it exists.

## Scope and Boundaries

### This Skill Manages:
- ✅ **Thing 1** - Specific responsibility
- ✅ **Thing 2** - Specific responsibility
- ✅ **Thing 3** - Specific responsibility

### This Skill Does NOT Manage:
- ❌ **Thing A** → **Delegated to other-skill** (reason)
- ❌ **Thing B** → **Delegated to another-skill** (reason)
- ❌ **Thing C** → **User-defined** (reason)

### Key Distinction:
- **This skill** = [Clear statement of primary responsibility]
- **other-skill** = [What other skills handle]
- **another-skill** = [What other skills handle]

**Complementary relationship with X:**
- This skill: [What this skill does]
- other-skill: [What other skill does]

---

## [Main Content Sections]

### Section 1: Core Knowledge
[Domain knowledge, patterns, best practices]

### Section 2: Common Workflows
[Step-by-step workflows for common tasks]

### Section 3: Examples
[Concrete examples with code]

### Section 4: Troubleshooting
[Common issues and solutions]

## Best Practices Summary

✅ **DO**:
- [Best practice 1]
- [Best practice 2]

❌ **DON'T**:
- [Anti-pattern 1]
- [Anti-pattern 2]
```

---

## Design Principles

### 1. One Responsibility, One Skill

**❌ Bad**: `web-development-expert` (too broad)
**✅ Good**: `fastapi-backend-expert` (specific framework and layer)

**Why**: Narrow scope prevents conflicts and makes invocation clear.

### 2. Avoid Overlapping Domains

Skills should have **complementary** responsibilities, not competing ones.

**Example of conflict**:
```
❌ file-organization-expert: "Manages .specify/ directory"
❌ spec-kit: "Manages .specify/ directory"
→ Result: Both skills fight over the same files!
```

**Example of complementary**:
```
✅ config-manager: "Reminds to update CLAUDE.md when configs change"
✅ claude-md-expert: "Explains HOW to write effective CLAUDE.md content"
→ Result: One triggers, the other explains!
```

### 3. Clear Hierarchical Skills

Distinguish between **meta skills** and **tactical skills**:

**Meta skills** (methodology, guidance):
- `spec-kit` - Guides the SDD process
- `git-commit-expert` - Generates commit messages (READ-ONLY)
- `claude-md-expert` - Explains how to write documentation

**Tactical skills** (execution, structure):
- `file-organization-expert` - Enforces Python file structure
- `config-manager` - Syncs configuration files
- `fastapi-backend-expert` - Implements FastAPI patterns

### 4. Clear Invocation Triggers

Make it obvious when Claude should invoke your skill:

**❌ Vague**: "Use this skill for documentation"
**✅ Specific**: "Use this skill when creating, updating, or optimizing CLAUDE.md files for Claude Code projects. Invoke when initializing new projects, documenting architecture changes, ensuring Claude has effective project context, or maintaining project documentation that helps Claude work efficiently."

---

## Step-by-Step Creation Process

### Step 1: Define the Need

**Questions to ask**:
1. What problem does this skill solve?
2. What domain knowledge or methodology does it provide?
3. Who are the "users" of this skill? (other developers, Claude, both?)
4. What are the common tasks or workflows?

**Example**:
```
Problem: Need expertise in NWB file format
Domain: Neuroscience data standards, HDF5 structure
Users: Developers parsing NWB files, Claude understanding NWB concepts
Tasks: Parsing metadata, validating files, extracting entities
```

### Step 2: Check for Conflicts

**Before creating**, scan existing skills:

```bash
# List all skill descriptions
grep "^description:" .claude/skills/*.md

# Search for overlapping domains
grep -r "manages.*directory" .claude/skills/
grep -r "Python.*organization" .claude/skills/
```

**Conflict resolution**:
1. If overlap exists, **refine boundaries** instead of creating new skill
2. Use **delegation pattern**: "X manages Y, but delegates Z to skill-name"
3. Add **Scope and Boundaries** section to both skills

### Step 3: Define Scope with ✅/❌ Lists

**Template**:
```markdown
## Scope and Boundaries

### This Skill Manages:
- ✅ **Specific thing 1** - What exactly
- ✅ **Specific thing 2** - What exactly

### This Skill Does NOT Manage:
- ❌ **Thing A** → **Delegated to skill-x** (reason)
- ❌ **Thing B** → **User responsibility** (reason)

### Key Distinction:
[Crystal clear statement of what makes this skill unique]
```

### Step 4: Write the Content

**Recommended structure**:

1. **Core Knowledge** (20%)
   - Concepts, terminology, fundamentals

2. **Workflows** (40%)
   - Step-by-step processes for common tasks
   - Concrete commands with examples

3. **Examples** (30%)
   - Real-world scenarios
   - Before/after comparisons
   - Code snippets

4. **Best Practices** (10%)
   - Do's and don'ts
   - Common pitfalls
   - Quality checklist

### Step 5: Define Tool Access

**Tools available**:
- `Read` - Read files (always safe)
- `Write` - Create new files
- `Edit` - Modify existing files
- `Glob` - Find files by pattern
- `Grep` - Search file contents
- `Bash` - Execute shell commands (⚠️ use carefully)

**Guidelines**:
- **Read-only skills**: `[Read, Glob, Grep, Bash]` - for analysis/generation skills
- **Structure skills**: `[Read, Write, Edit, Glob, Grep, Bash]` - for organization/config skills
- **Avoid git write operations**: Skills should NEVER execute `git commit`, `git push`, `git mv`

### Step 6: Test Invocation

**Create test scenarios**:

```markdown
## Test Scenarios

### Should Invoke:
1. User says: "Help me parse an NWB file"
   → nwb-expert should activate

2. User says: "Add a new FastAPI endpoint"
   → fastapi-backend-expert should activate

### Should NOT Invoke:
1. User says: "Read this Python file"
   → No skill needed (basic operation)

2. User says: "Organize my Python files"
   → file-organization-expert (not nwb-expert)
```

---

## Scope and Boundaries

### Why Scope Boundaries Matter

Without clear boundaries, you get:
- **Skill conflicts**: Multiple skills trying to handle the same request
- **Confusion**: Claude doesn't know which skill to use
- **Broken workflows**: Skills interfering with each other
- **Maintenance hell**: Overlapping responsibilities

### Anatomy of Good Boundaries

```markdown
## Scope and Boundaries

### This Skill Manages:
- ✅ **[Specific domain/directory/task]** - [Precise scope definition]
  - Example: ✅ **CLAUDE.md file** - Content, structure, and optimization
  - Not: ✅ Documentation (too vague)

### This Skill Does NOT Manage:
- ❌ **[Thing]** → **Delegated to [skill-name]** ([reason])
  - Example: ❌ **Python source code** → **Delegated to file-organization-expert skill** (different domain)
  - Shows delegation pattern clearly

### Key Distinction:
[One-line statement that captures the essence]
  - Example: "This skill = CLAUDE.md CONTENT and HOW to write it effectively"

**Complementary relationship with [related-skill]:**
- This skill: [What this does]
- related-skill: [What related skill does]
  - Shows how skills work TOGETHER, not compete
```

### Delegation Pattern

When multiple skills could handle something, use **delegation**:

```markdown
### This Skill Does NOT Manage:
- ❌ `.specify/` directory → **Delegated to spec-kit skill** (SDD methodology)
- ❌ `.claude/` directory → **Delegated to claude-md-expert skill** (Claude Code configuration)
- ❌ Python source code → **Delegated to file-organization-expert skill** (code structure)
```

This makes it **explicit** who owns what.

### Complementary vs. Competing

**❌ Competing** (causes conflicts):
```
skill-a: "Manages CLAUDE.md file"
skill-b: "Manages CLAUDE.md file"
→ Both fight over the same file!
```

**✅ Complementary** (work together):
```
config-manager: "Reminds to update CLAUDE.md when dependencies change"
claude-md-expert: "Explains HOW to document dependencies in CLAUDE.md"
→ One triggers the need, the other provides the solution!
```

---

## Best Practices

### 1. Write Clear Descriptions

**❌ Vague**:
```yaml
description: Expert in Python code
```

**✅ Specific**:
```yaml
description: Use this skill when organizing Python SOURCE CODE (dandi_kg/, tests/) and root config files. Does NOT handle methodology directories (.specify/, .claude/). Invoke when Python files are in wrong locations, source code structure needs validation, or when setting up new Python modules/components.
```

### 2. Use Action-Oriented Language

**In skill content**:
- ✅ "Run this command to..."
- ✅ "Update the file by..."
- ✅ "Validate using..."
- ❌ "You might want to..."
- ❌ "Consider doing..."

### 3. Provide Concrete Examples

**❌ Abstract**:
```markdown
Parse NWB files to extract metadata.
```

**✅ Concrete**:
```markdown
Parse NWB files to extract metadata:

```python
from pynwb import NWBHDF5IO

def extract_metadata(file_path: str) -> dict:
    with NWBHDF5IO(file_path, 'r') as io:
        nwbfile = io.read()
        return {
            'identifier': nwbfile.identifier,
            'session_description': nwbfile.session_description,
            'experimenter': nwbfile.experimenter
        }
```

Expected output:
```python
{
    'identifier': '12345',
    'session_description': 'Mouse behavioral session',
    'experimenter': ['Dr. Smith']
}
```
```

### 4. Include Validation Commands

Always provide ways to verify the work:

```markdown
## Validation

```bash
# Check file exists
test -f dandi_kg/parser/nwb_parser.py && echo "✓ File exists"

# Verify imports work
python -c "from dandi_kg.parser.nwb_parser import parse_nwb; print('✓ Import successful')"

# Run tests
pytest tests/unit/test_parser/ -v
```
```

### 5. Document "Why", Not Just "What"

**❌ What**:
```markdown
Use async/await for API endpoints.
```

**✅ Why**:
```markdown
Use async/await for all API endpoints to prevent blocking I/O during file uploads. NWB files can be 10GB+, so synchronous handling would block the entire server.
```

### 6. Keep Skills Focused

**Skill size guidelines**:
- **Small skill** (5-10 KB): Single concept (git-commit-expert)
- **Medium skill** (15-25 KB): Domain expertise (nwb-expert, fastapi-backend)
- **Large skill** (25-35 KB): Structural enforcement (file-organization-expert, config-manager)

**If a skill exceeds 40 KB**, consider splitting into:
- Core skill (concepts, workflows)
- Reference skill (complete API, detailed examples)

### 7. Git Safety

**IMPORTANT**: Skills should NEVER execute git write operations.

**❌ Dangerous**:
```bash
git commit -m "message"
git push origin main
git mv old.py new.py
git tag v1.0.0
```

**✅ Safe**:
```bash
# Analysis only
git status
git diff
git log

# Suggest, don't execute
# After reviewing changes, you can manually run:
# git commit -m "your message here"
```

**For file movements**:
```bash
# Use mv, not git mv
mv old.py new.py

# Let user preserve history if they want:
# To preserve git history, manually run: git mv old.py new.py
```

---

## Common Pitfalls

### ❌ Pitfall 1: Skills That Are Too Broad

**Problem**:
```yaml
name: python-expert
description: Expert in all things Python
```

**Why it fails**: Too vague, Claude won't know when to invoke it.

**Solution**: Create specific skills:
- `fastapi-backend-expert` - FastAPI patterns
- `testing-expert` - pytest and testing strategies
- `file-organization-expert` - Python project structure

### ❌ Pitfall 2: Overlapping Responsibilities

**Problem**:
```
file-organization-expert: "Manages .specify/ directory"
spec-kit: "Manages .specify/ directory"
```

**Result**: Both skills activate, causing confusion and conflicts.

**Solution**: Add Scope and Boundaries to both:
```
file-organization-expert:
  ❌ .specify/ → Delegated to spec-kit skill

spec-kit:
  ✅ .specify/ directory - SDD methodology
```

### ❌ Pitfall 3: Missing Invocation Triggers

**Problem**:
```yaml
description: Helps with NWB files
```

**Why it fails**: Not specific enough for Claude to know WHEN to invoke.

**Solution**:
```yaml
description: Use this skill when parsing NWB files, understanding NWB schemas, extracting NWB metadata, validating NWB file structure, or working with PyNWB library. Invoke when user mentions NWB, neurodata, or HDF5 neuroscience data.
```

### ❌ Pitfall 4: No Examples

**Problem**: Theoretical explanations without concrete examples.

**Why it fails**: Users (and Claude) can't apply the knowledge.

**Solution**: Include:
- Code snippets with expected output
- Before/after comparisons
- Step-by-step workflows with actual commands
- Common error scenarios and fixes

### ❌ Pitfall 5: Forgetting Tool Access

**Problem**: Skill needs to edit files but only has `[Read]` access.

**Why it fails**: Skill can analyze but can't implement.

**Solution**: Match tools to skill purpose:
- Analysis skills: `[Read, Grep, Glob, Bash]`
- Implementation skills: `[Read, Write, Edit, Grep, Glob, Bash]`
- Read-only skills: `[Read, Grep, Bash]` (e.g., git-commit-expert)

### ❌ Pitfall 6: Git Write Operations

**Problem**: Skill executes `git commit`, `git push`, `git mv`

**Why it fails**: User loses control, unexpected git history changes

**Solution**: Skills are READ-ONLY for git:
```markdown
**IMPORTANT: This skill is READ-ONLY for git operations.**
- Analyzes git changes using `git diff`, `git log`, `git status`
- Generates suggestions for you to execute
- Does NOT commit, push, or modify git in any way
- All git write operations remain under your control
```

---

## Skill Categories

### 1. Domain Expert Skills

**Purpose**: Provide specialized domain knowledge

**Examples**:
- `nwb-expert` - NWB file format, PyNWB library
- `neo4j-expert` - Graph databases, Cypher queries
- `nl-to-cypher` - Natural language to Cypher translation

**Characteristics**:
- Deep knowledge in specific domain
- Terminology and concepts
- Best practices and patterns
- Common operations and workflows

**Tools**: Usually `[Read, Grep, Bash]` (mostly read-only)

### 2. Methodology Skills

**Purpose**: Guide processes and methodologies

**Examples**:
- `spec-kit` - Spec-Driven Development workflow
- `git-commit-expert` - Conventional Commits format
- `testing-expert` - Test-driven development, pytest patterns

**Characteristics**:
- Process-oriented
- Step-by-step workflows
- Quality checklists
- Best practices and anti-patterns

**Tools**: Usually `[Read, Grep, Bash]` (analysis and guidance)

### 3. Structural Skills

**Purpose**: Enforce project structure and organization

**Examples**:
- `file-organization-expert` - Python file structure
- `config-manager` - Configuration file synchronization
- `claude-md-expert` - CLAUDE.md structure and content

**Characteristics**:
- Directory and file organization
- Structural patterns
- Validation commands
- Auto-organization workflows

**Tools**: Usually `[Read, Write, Edit, Glob, Grep, Bash]` (full access)

### 4. Technical Implementation Skills

**Purpose**: Implement specific technologies or frameworks

**Examples**:
- `fastapi-backend` - FastAPI patterns, endpoints, middleware
- `testing-expert` - pytest fixtures, test organization

**Characteristics**:
- Framework-specific patterns
- Code generation templates
- Integration patterns
- Common configurations

**Tools**: Usually `[Read, Write, Edit, Grep, Bash]` (implementation)

---

## Validation and Testing

### Pre-Release Checklist

Before considering a skill complete, verify:

- [ ] **YAML frontmatter** is valid (name, description, allowed-tools)
- [ ] **Description** is specific with clear invocation triggers
- [ ] **Scope and Boundaries** section exists with ✅/❌ lists
- [ ] **No git write operations** (commit, push, git mv)
- [ ] **Concrete examples** with code and expected output
- [ ] **Validation commands** provided for key operations
- [ ] **Best practices section** with ✅ DO and ❌ DON'T lists
- [ ] **No conflicts** with existing skills (check overlaps)
- [ ] **Complementary relationships** documented if applicable
- [ ] **Tool access** matches skill purpose

### Testing Invocation

**Create test cases**:

```markdown
## Skill Invocation Tests

### Should Invoke This Skill:
- [ ] User mentions [specific keyword]
- [ ] User asks to [specific task]
- [ ] User wants to [specific goal]

### Should NOT Invoke This Skill:
- [ ] User mentions [overlapping but different domain]
- [ ] User asks for [task handled by different skill]
- [ ] Basic operation that doesn't need expertise
```

**Test in practice**:
1. Ask Claude questions that should trigger the skill
2. Verify the skill is invoked (check for skill loading message)
3. Check if skill provides helpful guidance
4. Verify no conflicts with other skills

### Boundary Testing

**Check for conflicts**:

```bash
# Find overlapping "manages" statements
grep -A 5 "This Skill Manages:" .claude/skills/*.md | grep "✅"

# Find delegation conflicts
grep "Delegated to" .claude/skills/*.md | sort

# Check for competing responsibilities
grep -r "\.specify/" .claude/skills/  # Should only be in spec-kit
grep -r "CLAUDE\.md" .claude/skills/  # Check who manages what aspect
```

### Validation Script

```bash
#!/bin/bash
# validate_skills.sh - Verify skill integrity

echo "=== Checking skill files ==="
for skill in .claude/skills/*.md; do
    echo "Checking $skill..."

    # Check YAML frontmatter
    grep -q "^name:" "$skill" || echo "  ✗ Missing name"
    grep -q "^description:" "$skill" || echo "  ✗ Missing description"
    grep -q "^allowed-tools:" "$skill" || echo "  ✗ Missing allowed-tools"

    # Check Scope and Boundaries
    grep -q "## Scope and Boundaries" "$skill" || echo "  ⚠ Missing Scope and Boundaries section"

    # Check for git write operations (should not exist)
    if grep -q "git commit\|git push\|git tag.*-a" "$skill"; then
        echo "  ✗ DANGER: Contains git write operations!"
    fi

    # Check for examples
    grep -q "```" "$skill" || echo "  ⚠ No code examples found"

    echo "  ✓ $skill validated"
done

echo "=== Checking for conflicts ==="
echo "Multiple skills managing same directory:"
for dir in ".specify" ".claude" "dandi_kg" "tests" "specs"; do
    count=$(grep -l "$dir" .claude/skills/*.md | wc -l)
    if [ $count -gt 1 ]; then
        echo "  ⚠ $dir managed by $count skills:"
        grep -l "$dir" .claude/skills/*.md
    fi
done

echo "✓ Validation complete"
```

---

## Real-World Examples

### Example 1: Domain Expert Skill

**File**: `.claude/skills/nwb-expert.md`

**Purpose**: Provide NWB file format expertise

**Key sections**:
```markdown
## Core Concepts
- NWB file structure (HDF5-based)
- Core NWB types (NWBFile, Subject, Device, etc.)
- PyNWB library usage

## Common Operations
- Opening NWB files
- Extracting metadata
- Navigating hierarchical structure

## Best Practices
- Lazy loading for large files
- Validation before parsing
- Error handling for incomplete metadata
```

**Tools**: `[Read, Grep, Bash]` (read-only)

**Invocation**: "Help me parse an NWB file", "What's the NWB schema for devices?"

### Example 2: Methodology Skill

**File**: `.claude/skills/spec-kit.md`

**Purpose**: Guide Spec-Driven Development workflow

**Key sections**:
```markdown
## Scope and Boundaries
- ✅ .specify/ directory - SDD artifacts
- ✅ SDD Workflow - Constitution → Specify → Plan → Tasks
- ❌ Python source code → Delegated to file-organization-expert

## The Spec Kit Workflow
1. Constitution (principles)
2. Specify (requirements)
3. Plan (technical design)
4. Tasks (actionable breakdown)
5. Implementation (execution)

## Best Practices
- Separate concerns (principles vs requirements vs design)
- Keep specifications technology-agnostic
- Make everything testable
```

**Tools**: `[Read, Write, Edit, Bash]` (creates .specify/ files)

**Invocation**: "Help me with Spec-Driven Development", "Create a constitution.md"

### Example 3: Structural Skill

**File**: `.claude/skills/file-organization-expert.md`

**Purpose**: Enforce Python project structure

**Key sections**:
```markdown
## Scope and Boundaries
- ✅ dandi_kg/ - Python source code
- ✅ tests/ - Test code
- ✅ Root config file STRUCTURE (location only)
- ❌ .specify/ → Delegated to spec-kit
- ❌ Config file CONTENT → Delegated to config-manager

## Canonical Structure
[Visual directory tree]

## File Placement Rules
[Table mapping file types to locations]

## Auto-Organization Workflows
[Step-by-step file movement procedures]
```

**Tools**: `[Read, Write, Edit, Glob, Grep, Bash]` (full structure management)

**Invocation**: "Organize my Python files", "Where should this test file go?"

### Example 4: Complementary Skills

**Scenario**: Configuration management and documentation

**config-manager.md**:
```markdown
## Scope and Boundaries
- ✅ Config file SYNCHRONIZATION (pixi.toml ↔ pyproject.toml)
- ✅ CLAUDE.md UPDATE REMINDERS - Remind when configs change
- ❌ CLAUDE.md CONTENT → Delegated to claude-md-expert

## Complementary Relationship
- This skill: "You added FastAPI → remind to update CLAUDE.md"
- claude-md-expert: "Here's HOW to document FastAPI effectively"
```

**claude-md-expert.md**:
```markdown
## Scope and Boundaries
- ✅ CLAUDE.md CONTENT and STRUCTURE
- ✅ How to write effective documentation
- ❌ Update reminders → Delegated to config-manager

## Complementary Relationship
- config-manager: Triggers the need to update CLAUDE.md
- This skill: Explains HOW to write the update
```

**Result**: No conflict - one triggers, the other explains!

---

## Skill Architecture Patterns

### Pattern 1: Hierarchical Delegation

**Use when**: Multiple skills could handle related domains

```
config-manager (orchestrator)
  ├─→ Syncs config files
  ├─→ Reminds to update CLAUDE.md
  └─→ Delegates CLAUDE.md content to claude-md-expert

claude-md-expert (implementer)
  ├─→ Explains HOW to write CLAUDE.md
  ├─→ Provides structure and best practices
  └─→ Receives delegation from config-manager
```

### Pattern 2: Domain Specialization

**Use when**: Complex domain needs multiple perspectives

```
neo4j-expert (database)
  ├─→ Cypher syntax
  ├─→ Index optimization
  └─→ Connection management

nl-to-cypher (translation)
  ├─→ Natural language parsing
  ├─→ Query generation
  └─→ Uses neo4j-expert knowledge
```

### Pattern 3: Layer Separation

**Use when**: Skills operate at different abstraction layers

```
file-organization-expert (structure)
  └─→ WHERE files should be (location)

config-manager (content)
  └─→ WHAT files should contain (sync)

spec-kit (methodology)
  └─→ WHY we organize files (process)
```

---

## Advanced Topics

### Skill Composition

Skills can reference each other:

```markdown
## Related Skills

This skill works well with:
- **neo4j-expert**: For Cypher query optimization
- **testing-expert**: For testing graph operations
- **file-organization-expert**: For organizing graph builder files

When creating a new graph builder:
1. Use **file-organization-expert** to determine file location
2. Use this skill for Neo4j connection patterns
3. Use **testing-expert** for test structure
```

### Skill Evolution

As your project grows, skills should evolve:

**Version 1** (MVP):
```yaml
name: api-expert
description: FastAPI expertise
# Basic CRUD operations
```

**Version 2** (Growth):
```yaml
name: fastapi-backend-expert
description: FastAPI patterns, middleware, authentication
# + Middleware patterns
# + Auth strategies
# + Error handling
```

**Version 3** (Mature):
```yaml
name: fastapi-backend-expert
description: Comprehensive FastAPI backend patterns
# + WebSocket support
# + Background tasks
# + Dependency injection patterns
```

**Version 4** (Split):
```yaml
# Split into specialized skills:
name: fastapi-api-expert      # REST API patterns
name: fastapi-websocket-expert # Real-time patterns
name: fastapi-auth-expert     # Authentication/authorization
```

### Skill Metrics

Track skill effectiveness:

```markdown
## Skill Metrics (Internal)

- **Invocation rate**: How often is this skill invoked?
- **Conflict rate**: How often does it conflict with other skills?
- **Completion rate**: Does it successfully help users?
- **Refinement needs**: What questions does it fail to answer?
```

---

## Quick Reference

### Skill Creation Checklist

```markdown
- [ ] Identified clear, non-overlapping domain
- [ ] Checked for conflicts with existing skills
- [ ] Written specific invocation triggers in description
- [ ] Added Scope and Boundaries section
- [ ] Included concrete examples with code
- [ ] Provided validation commands
- [ ] Added best practices (✅ DO / ❌ DON'T)
- [ ] Verified no git write operations
- [ ] Tested invocation with sample queries
- [ ] Documented complementary relationships
```

### Common Commands

```bash
# List all skills
ls -1 .claude/skills/

# Check skill descriptions
grep "^description:" .claude/skills/*.md

# Find conflicts
grep -r "manages.*directory" .claude/skills/

# Check for git safety
grep -r "git commit\|git push" .claude/skills/

# Validate YAML frontmatter
for f in .claude/skills/*.md; do
    head -5 "$f" | grep -q "^---" && echo "✓ $f" || echo "✗ $f";
done
```

---

## Conclusion

Building a well-architected skill system requires:

1. **Clear boundaries** - Each skill owns a specific domain
2. **Explicit delegation** - Skills know what they DON'T handle
3. **Complementary design** - Skills work together, not compete
4. **Concrete guidance** - Examples, commands, and validation
5. **Git safety** - Never execute git write operations
6. **Continuous refinement** - Update as conflicts emerge

**The result**: A harmonious skill ecosystem where Claude can expertly navigate your project's domains without confusion or conflicts.

---

## Resources

- **This project's skills**: `.claude/skills/` directory
- **Claude Code documentation**: https://github.com/anthropics/claude-code
- **Skill examples**:
  - Domain: `nwb-expert.md`, `neo4j-expert.md`
  - Methodology: `spec-kit.md`, `git-commit-expert.md`
  - Structural: `file-organization-expert.md`, `config-manager.md`
  - Technical: `fastapi-backend.md`, `testing-expert.md`

**Created**: Based on real-world experience building a 10-skill system for dandi-kg project
**Last Updated**: After resolving skill conflicts and establishing clear boundaries
