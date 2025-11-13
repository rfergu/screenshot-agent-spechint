# Hand-off README: Building from Spec

**Project:** Screenshot Agent MCP
**Purpose:** Instructions for rebuilding this project from specification documents
**Audience:** Developers, AI agents, or collaborators implementing from spec
**Last Updated:** 2025-11-12

---

## Quick Start

This project uses **SpecKit** workflow for specification-driven development. All implementation decisions are driven by:
1. **Constitution** (non-negotiable principles)
2. **Specification** (what & why)
3. **Plan** (how & architecture)
4. **Tasks** (actionable implementation steps)

---

## Prerequisites

Before starting, ensure you have:

### Required Tools
- Python 3.11 or higher
- Git
- pip (Python package manager)
- Tesseract OCR (`brew install tesseract` on macOS)

### Required Accounts/Keys
- Azure OpenAI or Azure AI Foundry account
- API credentials (AZURE_AI_CHAT_ENDPOINT, AZURE_AI_CHAT_KEY, AZURE_AI_MODEL_DEPLOYMENT)

### Optional (for local testing mode)
- Azure AI Foundry CLI (`brew install azure/ai-foundry/foundry`)
- Phi-4-mini model (downloaded via `foundry model get phi-4-mini`)

---

## Step 1: Clone and Setup

```bash
# Clone the repository
git clone https://github.com/rfergu/Screenshot_Agent_MCP.git
cd Screenshot_Agent_MCP

# Switch to main branch (or feature branch if applicable)
git checkout main

# Create virtual environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
pip install -e .
```

---

## Step 2: Review the Constitution

**File:** `.specify/memory/constitution.md`

**Purpose:** Understand non-negotiable principles

**Action:**
```bash
# Read the constitution
cat .specify/memory/constitution.md
```

**Key takeaways:**
- Article X: **Required Technology Stack** - You MUST use:
  - Python 3.11+
  - Microsoft Agent Framework
  - Model Context Protocol (MCP)
  - Azure OpenAI / AI Foundry
- Articles I-IX: Development principles (testing, UX, performance, etc.)
- Article XI: Governance and amendment process

**Questions to ask yourself:**
- ✅ Do I agree with these constraints?
- ✅ Can I work within these principles?
- ⚠️  If no → propose constitutional amendment before proceeding

---

## Step 3: Read the Specification

**File:** `specs/001-screenshot-organizer/spec.md`

**Purpose:** Understand WHAT to build and WHY

**Action:**
```bash
# Read the spec
cat specs/001-screenshot-organizer/spec.md
```

**Focus areas:**
- **User Stories (US-001 through US-011):** What users want to accomplish
- **Functional Requirements (FR-001 through FR-016):** Specific features needed
- **Non-Functional Requirements:** Performance, security, UX expectations
- **Acceptance Criteria:** How to verify each feature works

**Verification checklist:**
- [ ] Spec is clear about WHAT to build
- [ ] Spec explains WHY (business value, user needs)
- [ ] Spec does NOT dictate HOW (no implementation details like "use React")
- [ ] Acceptance criteria are testable
- [ ] Edge cases are documented

**If spec is unclear:**
- Document questions
- Seek clarification from maintainer (see MAINTAINERS.md)
- Consider running spec clarification process

---

## Step 4: Review the Technical Plan

**File:** `specs/001-screenshot-organizer/plan.md`

**Purpose:** Understand HOW to build (architecture, stack, components)

**Action:**
```bash
# Read the plan
cat specs/001-screenshot-organizer/plan.md
```

**Focus areas:**
- **Architecture Overview:** High-level system design
- **Constitution Check Table:** How this plan enforces constitutional principles
- **Component Breakdown:** Major parts of the system
- **Technology Stack:** Specific libraries, frameworks, versions
- **Data Contracts:** Interfaces between components

**Verification checklist:**
- [ ] Plan references constitution principles
- [ ] Stack is explicitly defined
- [ ] Architecture diagrams are clear
- [ ] Component responsibilities are defined
- [ ] Dependencies and integration points are documented

**If plan conflicts with constitution:**
- This is a critical error
- Do NOT proceed with implementation
- Escalate to maintainer

---

## Step 5: Review the Task List

**File:** `specs/001-screenshot-organizer/tasks.md`

**Purpose:** Understand implementation order and acceptance criteria

**Action:**
```bash
# Read the tasks
cat specs/001-screenshot-organizer/tasks.md
```

**Focus areas:**
- **Task phases:** Logical grouping of work
- **Task dependencies:** What must be done first
- **Acceptance criteria:** How to know a task is complete
- **Status tracking:** What's done, what's pending

**Verification checklist:**
- [ ] Tasks are small enough (1-2 days max each)
- [ ] Each task has clear acceptance criteria
- [ ] Dependencies are explicit
- [ ] Tasks trace back to spec (see TRACEABILITY.md)

---

## Step 6: Understand Traceability

**File:** `TRACEABILITY.md`

**Purpose:** Map spec → plan → tasks → code

**Action:**
```bash
# Read traceability matrix
cat TRACEABILITY.md
```

**Use this to:**
- Verify every spec requirement has tasks
- Find which code implements which feature
- Ensure no orphan tasks or code
- Guide your implementation priorities

---

## Step 7: Implementation Options

You have several options for implementing from spec:

### Option A: Follow Existing Implementation

```bash
# The code already exists - study it
ls -R src/

# Key files to review:
src/agent/client.py              # Agent Framework integration
src/agent/prompts.py             # 7-phase conversational workflow
src/screenshot_mcp/tools/        # MCP tool implementations
```

**Use case:** Learning how SpecKit led to this implementation

### Option B: Rebuild from Scratch

```bash
# Create new branch
git checkout -b rebuild-from-spec

# Remove existing implementation (keep spec/plan/tasks)
rm -rf src/*

# Implement following tasks.md sequentially
# Refer to plan.md for architecture
# Refer to spec.md for requirements
```

**Use case:** Validating that spec is complete enough for rebuild

### Option C: Iterative Development (Recommended)

```bash
# Pick specific tasks from tasks.md
# Implement one at a time
# Test against acceptance criteria
# Commit frequently with task references
```

**Use case:** Normal feature development

---

## Step 8: Running the Implementation

### Remote Mode (Production)

```bash
# Set environment variables
export AZURE_AI_CHAT_ENDPOINT="https://your-project.services.ai.azure.com/api/projects/your-project"
export AZURE_AI_MODEL_DEPLOYMENT="gpt-4o"
export AZURE_AI_CHAT_KEY="your_api_key"

# Run
python -m src.cli_interface
```

### Local Mode (Testing)

```bash
# Start Foundry service
foundry service start
foundry model load phi-4-mini

# Run in local mode
python -m src.cli_interface --local
```

---

## Step 9: Testing

```bash
# Run all tests
pytest -v

# Run specific test suites
pytest tests/test_local_mode.py -v
pytest tests/test_smoke_local.py -v
pytest tests/test_integration.py -v
pytest tests/test_performance.py -v
```

**Test coverage requirements:**
- Minimum 80% coverage (per constitution Article III)
- All acceptance criteria from tasks.md must have tests
- Performance tests must validate FR-008 requirements

---

## Step 10: Spec Compliance Review

Before marking implementation complete:

### Run Constitution Check

```bash
# Verify adherence to constitutional principles
# Compare implementation against Article X (Stack Requirements)

# Example checks:
python --version  # Should be 3.11+
grep "agent-framework" requirements.txt  # Must be present
grep "mcp" requirements.txt  # Must be present
```

### Verify Traceability

```bash
# For each implemented feature, confirm:
# 1. It maps to a spec requirement (US-XXX or FR-XXX)
# 2. It follows the plan architecture
# 3. It completes tasks with acceptance criteria met
# 4. It has test coverage

# Use TRACEABILITY.md as your checklist
```

### Document Deviations

If you deviated from spec/plan:
1. Document WHY in implementation notes
2. Update spec/plan with learnings
3. Create amendment proposal if constitution was violated

---

## Step 11: Update Spec with Learnings

After implementation:

```bash
# Update spec.md with:
# - Features that were harder than expected
# - Edge cases discovered during implementation
# - Performance characteristics observed
# - User feedback incorporated

# Update plan.md with:
# - Architectural decisions that changed
# - Alternative approaches tried
# - Lessons learned

# Update tasks.md with:
# - Task estimates (if they were wrong)
# - Additional tasks discovered
# - Dependencies that weren't obvious
```

---

## Common Pitfalls

### 1. Ignoring Constitution
**Symptom:** Implementation uses different stack
**Fix:** Review Article X, use required technologies
**Prevention:** Read constitution FIRST

### 2. Weak Traceability
**Symptom:** Can't explain why code exists
**Fix:** Use TRACEABILITY.md to map features
**Prevention:** Reference spec IDs in commits

### 3. Skipping Tests
**Symptom:** No test coverage, acceptance criteria not verified
**Fix:** Write tests for each acceptance criterion
**Prevention:** Test-driven development (Article V)

### 4. Incomplete Tasks
**Symptom:** Task marked done but acceptance criteria not met
**Fix:** Re-review acceptance criteria, complete gaps
**Prevention:** Use acceptance criteria as checklist

---

## Getting Help

### Clarification Needed?

1. Check **TRACEABILITY.md** - might already be documented
2. Check **SPECKIT_AUDIT.md** - known gaps documented
3. Review **MAINTAINERS.md** - contact project maintainer
4. File issue on GitHub with `[spec-question]` tag

### Found a Bug in Spec?

1. Document the issue clearly
2. Propose fix in spec.md
3. Update TRACEABILITY.md if it affects traceability
4. Submit PR with `[spec-fix]` tag

### Need Constitutional Amendment?

1. Review Article XI (Governance)
2. Propose amendment with justification
3. Follow amendment procedure (PR + maintainer approval)
4. Update version number per semver policy

---

## Success Criteria

Implementation is complete when:

- [x] All user stories (US-001 through US-011) implemented
- [x] All functional requirements (FR-001 through FR-016) implemented
- [x] All tasks in tasks.md marked complete
- [x] All acceptance criteria verified
- [x] Minimum 80% test coverage achieved
- [x] Constitution Article X requirements met
- [x] TRACEABILITY.md reflects actual implementation
- [x] Spec updated with implementation learnings

---

## Next Steps After Completion

1. **Merge to main** - after code review and testing
2. **Tag release** - semantic versioning
3. **Update README** - if public-facing changes
4. **Archive specs** - move completed spec to archive if needed
5. **Celebrate** - You've completed a SpecKit-driven project! 🎉

---

## Additional Resources

- **Constitution:** `.specify/memory/constitution.md`
- **Spec:** `specs/001-screenshot-organizer/spec.md`
- **Plan:** `specs/001-screenshot-organizer/plan.md`
- **Tasks:** `specs/001-screenshot-organizer/tasks.md`
- **Traceability:** `TRACEABILITY.md`
- **Audit Report:** `SPECKIT_AUDIT.md`
- **Maintainers:** `MAINTAINERS.md`

---

**Questions or Issues?**
Contact: See MAINTAINERS.md for project maintainer contact information

---

*This handoff document is version controlled with the project. Keep it updated as processes evolve.*
