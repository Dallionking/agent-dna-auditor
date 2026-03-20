---
name: agent-dna-auditor
description: |
  Scan AI coding agents, classify behaviors as DNA (methodology/ideology) vs Tech Stack Skills (framework knowledge), and optionally rewrite definitions with DNA embedded.
  Triggers: "audit my agents", "classify agent skills", "embed DNA", "separate DNA from skills", "agent DNA audit", "DNA vs skills", "audit agent definitions"
allowed-tools: [Read, Write, Edit, Bash, Glob, Grep, mcp__exa__web_search_exa, mcp__exa__get_code_context_exa]
---

# Agent DNA Auditor

**Purpose:** Analyze agent definitions, classify each skill/behavior as permanent DNA (methodology, ideology, best practices) or project-specific Tech Stack Skill (framework knowledge), then optionally rewrite agents with DNA embedded and tech skills mapped as invokable references.

## Core Concept

An agent has two layers:

| Layer | What it is | Changes per project? | Example |
|-------|-----------|---------------------|---------|
| **DNA** | HOW the agent thinks. Methodology, ideology, universal best practices. | No. Embedded permanently. | Clean Architecture, anti-slop writing, WCAG 2.2 AA |
| **Tech Stack Skill** | WHAT TOOLS the agent uses for this project. Framework docs, vendor APIs. | Yes. Invoked per project. | $supabase, $langchain, $shadcn, $stripe |

**One sentence:** DNA is the ideology that makes the agent good at its role. Skills are the framework knowledge that makes it useful for this specific project.

---

## Workflow

### Phase 1: Scan

Find all agent definitions in both locations:

```
Scan order:
1. {cwd}/.claude/agents/*.md          — project agents
2. ~/.claude/agents/*.md              — global agents
```

For each agent file found:
- Read the full contents
- Extract: name, description, role/persona, referenced skills, inline instructions
- Note any `$skill-name` references or skill file paths

Also scan for skill definitions:
```
1. {cwd}/.claude/skills/*/SKILL.md    — project skills
2. ~/.claude/skills/*/SKILL.md        — global skills
```

### Phase 2: Analyze

For each agent, build an inventory of every distinct behavior, rule, or knowledge block in its system prompt. Categorize the raw content:

**Extract these signal types:**
- Architecture patterns (dependency direction, layering, bounded contexts)
- Design methodology (Design Thinking, typography rules, spatial composition)
- Writing rules (anti-slop lists, formatting standards, tone requirements)
- Security practices (trust boundaries, auth patterns, rate limiting)
- Accessibility rules (WCAG references, keyboard nav, contrast ratios)
- Testing methodology (TDD workflow, coverage expectations, test structure)
- Quality gates (thresholds, verification steps, review requirements)
- Framework-specific knowledge (API references, config patterns, vendor docs)
- Library-specific patterns (hooks, components, utility functions)
- Vendor integrations (auth providers, databases, payment processors)

### Phase 3: Classify

Apply these heuristics to each extracted behavior:

**DNA (Embed) if:**
- Teaches HOW to think about a category (architecture, design, writing quality)
- Contains anti-patterns or "never do this" rules that apply regardless of framework
- Defines quality thresholds or methodology steps that are framework-agnostic
- Establishes trust boundaries, security posture, or accessibility standards
- Sets writing tone, formatting rules, or communication standards
- Would still apply if the project switched from React to Vue, or Python to Rust

**Tech Stack Skill (Invoke) if:**
- Teaches HOW to use a specific tool, library, or framework
- Contains API references, config patterns, or vendor-specific documentation
- References specific package names, import paths, or version numbers
- Would NOT apply if the project switched tech stacks
- Is large enough that embedding it would bloat the agent definition

**When classification is unclear:**
- Ask the user: "This behavior could go either way. Is [X] something this agent should always do (DNA), or only when working with [Y framework] (Tech Skill)?"
- Default to DNA for methodology, default to Tech Skill for implementation details

### Phase 4: Research (Optional Enhancement)

Use EXA MCP to find the latest best practices for each agent's role. This fills gaps in the agent's DNA — things a senior practitioner would know but that aren't in the current definition.

```
For a "backend engineer" agent, search:
- "backend engineering best practices 2026"
- "clean architecture principles senior engineer"
- "API design guidelines production systems"
- "backend security trust boundaries 2026"

For a "frontend engineer" agent, search:
- "frontend architecture best practices 2026"
- "React component design principles senior"
- "web accessibility WCAG 2.2 AA checklist"
- "design system implementation patterns"
```

Synthesize findings into DNA recommendations. Do NOT blindly inject search results — distill them into concise, opinionated rules that match the agent's existing voice.

### Phase 5: Report

Generate a classification report for each agent:

```markdown
## Agent: {agent-name}
**Role:** {role description}
**File:** {file path}

### DNA (Embed — always active)
| # | Category | Behavior | Source |
|---|----------|----------|--------|
| 1 | Architecture | Clean Architecture: dependencies point inward | Existing |
| 2 | Security | Never trust client-side validation alone | Existing |
| 3 | Writing | No hedging language ("might", "could perhaps") | Research |

### Tech Stack Skills (Invoke — per project)
| # | Skill | What it covers | Recommendation |
|---|-------|---------------|----------------|
| 1 | $supabase | RLS policies, Edge Functions, realtime | Invoke: $supabase |
| 2 | $langchain | Chain composition, tool calling, memory | Invoke: $langchain |

### Gaps Found (from research)
| # | Category | Missing DNA | Suggested Addition |
|---|----------|------------|-------------------|
| 1 | Testing | No TDD methodology defined | Add: red-green-refactor cycle |
| 2 | Accessibility | No WCAG requirements | Add: WCAG 2.2 AA minimum |

### Summary
- **DNA items:** {count} ({existing} existing + {new} from research)
- **Tech skills:** {count} (recommend invoking as $skill references)
- **Gaps found:** {count} (missing DNA for this role)
```

After showing all agents, show a summary table:

```markdown
## Overall Summary

| Agent | DNA Items | Tech Skills | Gaps | Status |
|-------|-----------|-------------|------|--------|
| backend-engineer | 12 | 3 | 2 | Needs rewrite |
| frontend-engineer | 8 | 5 | 4 | Needs rewrite |
| qa-engineer | 6 | 1 | 1 | Minor gaps |
```

### Phase 6: Apply (User Approval Required)

**STOP and ask the user before rewriting any files.**

Show the proposed changes and ask:
> "I've classified your agents. Want me to rewrite them with DNA embedded and tech skills mapped as $references? I'll create backups first."

If approved:

1. **Backup** each agent file to `{agent-name}.md.bak`
2. **Rewrite** the agent definition:
   - DNA goes into the system prompt body as permanent instructions
   - Tech Stack Skills get replaced with `$skill-name` invocation references
   - Research-sourced DNA gets added under a `## DNA (auto-enhanced)` section
3. **Verify** the rewritten agent by reading it back and confirming structure

**Rewritten agent structure:**

```markdown
---
name: {agent-name}
description: {description}
---

# {Agent Role}

## DNA — Core Methodology

### Architecture
{architecture DNA rules}

### Security
{security DNA rules}

### Writing Quality
{anti-slop and formatting DNA}

### Testing
{TDD methodology DNA}

### Accessibility
{WCAG and inclusive design DNA}

## DNA (auto-enhanced)
{Rules added from EXA research — marked so user can review}

## Tech Stack Skills (invoke per project)
- When working with Supabase: invoke $supabase
- When working with LangChain: invoke $langchain
- When working with shadcn/ui: invoke $shadcn

## Role-Specific Instructions
{The original role-specific instructions that aren't DNA or tech skills}
```

---

## DNA Categories Reference

Use this checklist when auditing. A well-defined agent should have DNA for every category relevant to its role.

### Architecture DNA
- Dependency direction (always inward)
- Layer separation (domain, application, infrastructure)
- Bounded contexts and module boundaries
- Interface-first design (contracts before implementation)
- ADR (Architecture Decision Record) discipline

### Frontend Design DNA
- Design Thinking process (Purpose, Tone, Constraints, Differentiation)
- Typography rules (font pairing, hierarchy, never default to Inter/Roboto without reason)
- Spatial composition (whitespace, grid rhythm, visual weight)
- Motion philosophy (purposeful animation, not decoration)
- Color theory (contrast ratios, semantic color usage)

### Writing Quality DNA
- Anti-slop word list (never: "dive in", "game-changing", "seamless", "robust", "delve", "leverage")
- No hedging ("might", "could perhaps", "it seems like")
- AI-native formatting (structured output, scannable, no walls of text)
- Active voice preference
- Precision over politeness

### Security DNA
- Trust boundaries (never trust client input)
- Auth-first development (auth before features)
- Rate limiting by default
- Secrets management (never hardcode, never log)
- Principle of least privilege

### Accessibility DNA
- WCAG 2.2 AA as minimum standard
- Keyboard navigation for all interactive elements
- Touch targets (minimum 44x44px)
- Contrast ratios (4.5:1 normal text, 3:1 large text)
- Screen reader compatibility (semantic HTML, ARIA when needed)

### Testing DNA
- TDD cycle: red (write failing test) -> green (minimal code to pass) -> refactor
- Test pyramid (unit > integration > e2e)
- Coverage expectations (not 100%, but critical paths covered)
- Test naming conventions (describe what, not how)

### Quality DNA
- Verification gates before merge
- Code review expectations
- Performance budgets
- Error handling standards (never swallow errors)
- Logging and observability requirements

---

## Error Handling

- **No agents found:** Report "No agent definitions found in {cwd}/.claude/agents/ or ~/.claude/agents/. Create agent .md files there first."
- **No skills found:** That's fine — report that no tech stack skills were detected to separate.
- **EXA unavailable:** Skip Phase 4 (Research). Note in report: "Research enhancement skipped — EXA MCP not available."
- **Agent file unreadable:** Skip that agent, note it in the report, continue with others.
