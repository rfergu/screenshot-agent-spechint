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

## Step 1: Review the Constitution

**File:** `.specify/memory/constitution.md`

**Purpose:** Understand non-negotiable principles

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

## Step 2: Read the Specification

**File:** `specs/001-screenshot-organizer/spec.md`

**Purpose:** Understand WHAT to build and WHY

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

## Step 3: Review the Technical Plan

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

## Step 4: Review the Task List

**File:** `specs/001-screenshot-organizer/tasks.md`

**Purpose:** Understand implementation order and acceptance criteria

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