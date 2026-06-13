---
name: spec-kit
description: Use this skill when working with GitHub Spec Kit for spec-driven development. Invoke when the user mentions spec-driven development, wants to create specifications, needs help with constitution.md, planning technical implementations, breaking down tasks, or using Spec Kit slash commands (/speckit.constitution, /speckit.specify, /speckit.plan, /speckit.tasks, /speckit.implement).
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash]
---

# GitHub Spec Kit Expert

You are an expert in **GitHub Spec Kit** and **Spec-Driven Development (SDD)** methodology.

## What is Spec-Driven Development?

Spec-Driven Development is a methodology where **specifications become executable**, directly generating working implementations rather than just guiding them. Instead of writing code first, you define what you're building through structured specifications, and AI agents transform those specs into production-ready code.

## The Spec Kit Workflow

### 4-Phase Development Process

```
Constitution → Specify → Plan → Tasks → Implementation
```

#### Phase 1: Constitution (Project Principles)
**Command**: `/speckit.constitution`

Establish non-negotiable principles and guidelines for your project:
- Core values and design principles
- Technology preferences and constraints
- Code quality standards
- Security requirements
- Performance criteria
- Documentation standards

**Best Practices**:
- Keep principles clear and actionable
- Focus on "what" and "why", not "how"
- Make principles testable/verifiable
- Include both technical and non-technical principles
- Think long-term (these guide all decisions)

**Example Constitution Elements**:
```markdown
# Project Constitution

## Core Principles
1. Data integrity is paramount - 100% extraction completeness required
2. Scientific accuracy over implementation speed
3. Transparent provenance for all data transformations
4. Dynamic extensibility - handle unknown data gracefully

## Technical Constraints
1. Use Python 3.11+ for all backend code
2. Neo4j as the primary graph database
3. No authentication in MVP (single-user focus)
4. Local-first deployment (no cloud dependencies)

## Quality Standards
1. >70% test coverage required
2. All parsing must validate against official schemas
3. Clear error messages for end users
4. Complete documentation for all public APIs
```

---

#### Phase 2: Specify (Requirements Definition)
**Command**: `/speckit.specify`

Define concrete requirements with:
- **User stories**: "As a [persona], I want [goal], so that [benefit]"
- **Functional requirements**: What the system must do
- **Acceptance criteria**: How to verify completion
- **Scope boundaries**: What's in and out of scope

**Best Practices**:
- Be explicit about what you're building and why
- Focus on requirements, NOT technology choices
- Include personas and stakeholders
- Define success metrics
- Specify constraints (time, resources, complexity)
- Break down into epics and stories

**Structure**:
```markdown
# Project Specification

## Executive Summary
[High-level overview in 3-5 sentences]

## Personas
### Primary User
- Role, goals, technical level, use cases

## Core Features
- Feature 1: Description and value
- Feature 2: Description and value

## User Stories by Epic
### Epic 1: [Name]
#### Story 1.1: [Title]
As a [persona]
I want [goal]
So that [benefit]

Acceptance Criteria:
- [ ] Criterion 1
- [ ] Criterion 2

## Scope Constraints
- What's included in MVP
- What's deferred post-MVP

## Success Metrics
- Functional success criteria
- Technical success criteria
- User success criteria
```

---

#### Phase 3: Plan (Technical Implementation)
**Command**: `/speckit.plan`

Create a detailed technical implementation plan:
- Technology stack selection
- Architecture design
- Component breakdown
- Data flow and structure
- Integration points
- Implementation phases

**Best Practices**:
- Now you choose HOW to build it
- Consider constitution principles
- Justify technology choices
- Document trade-offs
- Define clear interfaces
- Plan for testing and deployment
- Sequence work logically

**Structure**:
```markdown
# Implementation Plan

## Technology Stack
- Backend: Python 3.11+, FastAPI
- Database: Neo4j 5.x
- Libraries: pynwb, h5py, neo4j-driver
- Frontend: HTML/CSS/JS (vanilla, no frameworks)

Rationale: [Why these choices align with requirements and constitution]

## System Architecture
[Diagrams and component descriptions]

## Component Breakdown
### Component 1: [Name]
- Responsibility
- Dependencies
- Interfaces
- Implementation approach

## Data Flow
[Describe how data moves through the system]

## Implementation Phases
### Phase 1: [Name] (Timeline)
- Goals
- Deliverables
- Testing approach

## Testing Strategy
- Unit tests for [components]
- Integration tests for [flows]
- Manual testing for [UI]

## Deployment Plan
- Setup requirements
- Installation steps
- Configuration
```

---

#### Phase 4: Tasks (Actionable Breakdown)
**Command**: `/speckit.tasks`

Break specifications into implementable tasks:
- Concrete, actionable items
- Clear completion criteria
- Logical sequencing
- Estimated complexity
- Dependencies identified

**Best Practices**:
- Each task should be completable in one session
- Tasks should be testable
- Include setup and documentation tasks
- Consider dependencies between tasks
- Group related tasks together
- Start with foundational work

**Structure**:
```markdown
# Implementation Tasks

## Phase 1: Foundation
### Task 1.1: Setup Development Environment
- [ ] Install Neo4j locally
- [ ] Create Python virtual environment
- [ ] Install dependencies from requirements.txt
- [ ] Verify Neo4j connection

Estimated Time: 30 minutes
Dependencies: None

### Task 1.2: Create Project Structure
- [ ] Create directory structure
- [ ] Initialize Git repository
- [ ] Setup configuration files
- [ ] Create README skeleton

Estimated Time: 20 minutes
Dependencies: Task 1.1

## Phase 2: Core Parsing
### Task 2.1: Implement PyNWB Parser
- [ ] Create parser module
- [ ] Implement recursive traversal
- [ ] Add error handling
- [ ] Write unit tests

Estimated Time: 2 hours
Dependencies: Task 1.2
```

---

#### Phase 5: Implementation
**Command**: `/speckit.implement`

Execute tasks to build the actual system:
- Write code following the plan
- Test each component
- Document as you build
- Validate against acceptance criteria
- Iterate based on testing

**Best Practices**:
- Refer back to constitution for decisions
- Check specifications for requirements
- Follow the technical plan
- Complete tasks in order
- Test continuously
- Update documentation
- Track progress

---

## Spec Kit CLI Commands

### Initialize a New Project
```bash
specify init <project-name>
```

### Check System Requirements
```bash
specify check
```

### Available Slash Commands (in AI Agent)
- `/speckit.constitution` - Create/update constitution.md
- `/speckit.specify` - Create/update specifications
- `/speckit.plan` - Create/update technical plan
- `/speckit.tasks` - Generate task breakdown
- `/speckit.implement` - Execute implementation

## Key Files in Spec Kit Projects

```
project/
├── constitution.md          # Project principles and constraints
├── specs/
│   ├── requirements.md      # Full specification
│   ├── user-stories.md      # User stories (optional)
│   └── success-metrics.md   # Success criteria (optional)
├── docs/
│   ├── plan.md             # Technical implementation plan
│   ├── architecture.md     # Architecture design
│   └── tasks.md            # Task breakdown
├── src/                    # Source code
└── tests/                  # Test files
```

## Best Practices for Spec-Driven Development

### 1. Start with Why
- Clearly articulate the problem you're solving
- Define who will use this and how
- Establish success criteria upfront

### 2. Separate Concerns
- **Constitution**: Principles (unchanging)
- **Specification**: What and Why (requirements)
- **Plan**: How (technology and architecture)
- **Tasks**: Steps (implementation breakdown)

### 3. Iterate Within Phases
- Don't jump phases prematurely
- Refine specifications before planning
- Solidify plans before task breakdown
- Complete tasks before moving to next phase

### 4. Keep Specifications Technology-Agnostic
- Focus on capabilities, not implementations
- Describe user needs, not technical solutions
- Let the plan phase handle technology choices

### 5. Make Everything Testable
- Specifications have acceptance criteria
- Plans have deliverables
- Tasks have completion criteria
- Implementation has tests

### 6. Maintain Traceability
- Tasks trace back to plan
- Plan traces back to specifications
- Specifications trace back to constitution
- Every feature has a "why"

## Common Pitfalls to Avoid

❌ **Jumping to implementation without specs**
✅ Complete specify → plan → tasks first

❌ **Including technology in specifications**
✅ Keep specs technology-agnostic

❌ **Vague acceptance criteria**
✅ Make criteria specific and testable

❌ **Tasks too large**
✅ Break into completable chunks

❌ **Ignoring constitution during implementation**
✅ Refer back to principles for decisions

❌ **Skipping the plan phase**
✅ Always create technical plan before coding

## Integration with Existing Projects

If you already have a project but want to adopt Spec Kit:

1. **Start with Constitution**: Document existing principles
2. **Reverse-Engineer Specs**: Document what the system does/should do
3. **Document Current Plan**: Capture architecture and tech stack
4. **Create Forward Tasks**: Plan future work using Spec Kit
5. **Use for New Features**: Apply full workflow to additions

## Spec Kit with Claude Code

When using Spec Kit with Claude Code:
- Use slash commands naturally in conversation
- Claude will help generate specification documents
- Claude can validate specs against constitution
- Claude can help break down specifications into tasks
- Claude can implement tasks following the plan

## Example Workflow

```
User: "I want to build an NWB file parser with knowledge graph"

1. User: "/speckit.constitution"
   Claude: Creates constitution.md with principles

2. User: "/speckit.specify - NWB parser with Neo4j graph"
   Claude: Creates detailed requirements.md

3. User: "/speckit.plan"
   Claude: Creates technical plan with Python/Neo4j stack

4. User: "/speckit.tasks"
   Claude: Breaks down into actionable tasks

5. User: "/speckit.implement - Task 1.1"
   Claude: Implements first task with code
```

---

## When to Use This Skill

Claude will automatically invoke this skill when:
- User mentions "Spec Kit" or "spec-driven development"
- User wants to create specifications or requirements
- User needs help structuring a project
- User asks about constitution, planning, or task breakdown
- User wants to use Spec Kit slash commands
- User needs to align implementation with specifications

## Resources

- **GitHub**: https://github.com/github/spec-kit
- **Spec-Driven Development Guide**: https://github.com/github/spec-kit/blob/main/spec-driven.md
- **Latest Release**: v0.0.85 (November 2025)

---

When this skill is invoked, help users follow the Spec-Driven Development methodology with GitHub Spec Kit, ensuring they create comprehensive specifications before implementation and maintain alignment with their constitution throughout development.