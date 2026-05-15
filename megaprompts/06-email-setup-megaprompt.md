# Mega Prompt: Email-Setup Onboarding Skill

## Role

You are a **Skill Architect** specializing in interview-driven setup workflows. Generate a production-grade, distributable Claude skill that interviews a user about their email patterns and produces a complete personalized knowledge base that powers a separate `email-triage` skill.

## Output Target

Single file: `${SKILLS_DIR}/email-setup/SKILL.md`

Word budget: 2,000–2,400 words. Hard ceiling: 2,500.

## Critical Pairing Note

This skill is **paired with `email-triage`**. The knowledge base it produces is consumed by `email-triage` on every run. The file contracts (names, sections, fields) MUST match between the two skills exactly. Generate this skill with that contract awareness.

## Skill Purpose

Run once (or re-run when business/priorities change). Interview the user about their email patterns, business context, reply style, and priorities. Generate a structured knowledge base — a set of markdown files in `${WORKSPACE}/Email/` — that captures everything `email-triage` needs to process the inbox effectively.

## Knowledge Base Contract (Files To Produce)

The skill must produce exactly these files at `${WORKSPACE}/Email/`:

|File                     |Purpose                                     |Required?                                  |
|-------------------------|--------------------------------------------|-------------------------------------------|
|`email-taxonomy.md`      |Classification system + report preferences  |Yes                                        |
|`email-patterns.md`      |Reply voice, tone, templates, hard rules    |Yes                                        |
|`evaluation-framework.md`|Decision tree for opportunity emails        |Only if user receives pitches/opportunities|
|`rate-card.md`           |Pricing, terms, negotiation posture         |Only if user has pricing                   |
|`blocklist.md`           |Auto-skip senders + learned decline patterns|Yes (seeded, grows over time)              |
|`tracker.md`             |Active follow-ups, overdue items, deadlines |Yes (starts mostly empty)                  |
|`triage-log/`            |Directory for per-run logs                  |Yes (created empty)                        |

## Workflow Structure

The generated skill must follow this structure:

```
1. Introduction (what this produces, when to re-run)
2. Conduct discipline (do NOT generate all files at once; walk through sections)
3. Section 1: The Big Picture (context-gathering interview)
4. Section 2: Email Categories (propose, confirm, generate taxonomy)
5. Section 3: Reply Style & Voice (interview + sample analysis)
6. Section 4: Evaluation Framework (only if opportunities exist)
7. Section 5: Blocklist & Patterns (initial seeding)
8. Section 6: Current State (active follow-ups)
9. Section 7: Report Preferences (delivery format)
10. Section 8: Confirmation & Handoff (final list + handoff to triage)
11. Privacy and ambiguity rules
```

## Critical Improvements Over Naive Implementation

The skill MUST address these concerns:

1. **Email-provider agnostic** — Don’t hardcode Gmail. Reference “email provider” generically; document common ones (Gmail, Outlook, Fastmail, etc.). The companion triage skill will handle provider-specific tooling.
1. **Modular knowledge base** — Skip sections that don’t apply (e.g., no evaluation framework if user doesn’t receive pitches). Document this skip-logic explicitly.
1. **Sample-based voice extraction** — Ask user to paste 3–5 real sent emails as the highest-quality input for voice patterns. Self-description is unreliable; demonstrated voice is reliable.
1. **Conversational pacing** — Strict rule: do NOT batch all questions at once. Walk through sections incrementally. Wait for answers before proceeding.
1. **Why-questions** — Explain *why* each question matters as you ask it. This helps users give better answers.
1. **Privacy boundary** — Document: never persist passwords, full account numbers, SSNs, or other sensitive credentials in knowledge base files.
1. **Re-run-safe** — Document that this skill can be re-run; existing files should be confirmed before overwriting, with the user choosing per-file behavior (replace, merge, skip).

## Section Specifications (Must Be Fully Documented)

### Section 1: The Big Picture

Questions (asked conversationally, not as a numbered list):

- What do you do? (Role/business — enough for context)
- What dominates your inbox? (Sales pitches, client work, internal team, newsletters, etc.)
- Rough volume split? (e.g., “60% business inquiries, 20% ops, 20% noise”)
- Which email address(es) should triage cover?
- Run frequency preference? (Once daily, 2x, 3x, on-demand)
- Anyone helping manage email (assistant, VA, team), or solo?

**Action**: Build mental model. Do NOT write files yet.

### Section 2: Email Categories

Propose 5–7 categories based on Section 1. Use this template set as a starting menu:

- New Opportunities
- Active Conversations
- Action Required
- Financial
- Important/Personal
- Informational
- Ignore/Low Priority

Ask the user:

- Does this map to your inbox reality?
- Missing categories?
- Which category takes the most time?

**Action**: Generate `email-taxonomy.md` with categories, signals, default actions.

### Section 3: Reply Style & Voice

Questions:

- Formal, casual, or in between?
- Communication pet peeves? (Phrases you hate, openings you avoid)
- Phrases or sign-offs you always use?
- Different persona for different contexts? (e.g., assistant replies as you)
- Typical reply length?
- Hard rules? (Never emojis, always reply within 24h, never take calls)

If user runs a business: ask about media kits, rate sheets, standard pitches, repeated replies.

**Best input**: Ask user to paste 3–5 real sent emails. Analyze those for voice patterns rather than relying solely on self-description.

**Action**: Generate `email-patterns.md` with tone description (with do/don’t examples), persona rules, templates, signatures, hard rules.

### Section 4: Evaluation Framework (Conditional)

Only run this section if user receives opportunity emails.

Questions:

- First thing you check when pitched something?
- Instant deal-breakers?
- Things that make you immediately interested?
- Standard pricing/terms?
- Negotiation posture (firm, flexible, depends)?
- VIP senders/orgs that always get engagement?

**Action**: Generate `evaluation-framework.md` (decision tree + recommendation categories + VIP list) AND `rate-card.md` if pricing exists.

### Section 5: Blocklist & Patterns

Questions:

- Senders/domains to always skip?
- Patterns in emails always deleted?
- Specific companies/recruiters/newsletters wasting time?

**Action**: Generate `blocklist.md` (auto-maintained by triage thereafter).

### Section 6: Current State

Questions:

- Active threads you’re tracking?
- Overdue replies?
- Time-sensitive items?

**Action**: Generate `tracker.md` with active follow-ups table, overdue section, resolved section (empty), update log (empty). Also create empty `triage-log/` directory.

### Section 7: Report Preferences

Questions:

- Delivery format: email draft to self / file / chat summary?
- Detail level: 30-second scan / detailed breakdown / both?
- Anything always shown first? (e.g., overdue payments)

**Action**: Save these preferences into `email-taxonomy.md` under a “Report Preferences” section.

### Section 8: Confirmation & Handoff

- List every file created with one-sentence summary
- Tell user: “Your triage system is ready. Run the **email-triage** skill to process your inbox. First runs need oversight — system learns from your edits and overrides.”
- Remind: re-run this setup anytime business/pricing/priorities change

## Trigger Phrases (for frontmatter description)

- “set up my email system”
- “configure email triage”
- “build my email knowledge base”
- “initialize email management”
- “set up inbox triage”
- “onboard email triage”

## Error Handling Requirements

|Situation                          |Behavior                                                                       |
|-----------------------------------|-------------------------------------------------------------------------------|
|Workspace inaccessible             |Stop. Tell user where files would go and ask for permission/path               |
|User refuses to share samples      |Use self-description; flag in patterns file that calibration may need iteration|
|User says “skip this” mid-interview|Honor it; flag the gap in the file as `[needs follow-up]`                      |
|Sensitive info volunteered         |Acknowledge but don’t persist; note in file as `[stored separately by user]`   |
|Re-run on existing setup           |Detect existing files; ask user per-file: replace, merge, skip                 |
|User has no pricing / opportunities|Skip Section 4 entirely; don’t create empty files                              |

## Portability Requirements

- **Claude Code CLI**: Native — writes markdown files directly to filesystem.
- **Claude.ai web**: Works with project files / artifacts. Document the alternate path: generate files as artifacts, instruct user to save to their workspace, or use connected file system if available.

## Frontmatter Spec

```yaml
---
name: email-setup
description: "One-time setup skill that builds a personalized email triage knowledge base via interactive interview. Interviews the user about their email patterns, business context, reply style, and priorities, then generates the knowledge base files that power the companion 'email-triage' skill. Run this once before using email-triage for the first time. Re-run when business, pricing, or priorities change significantly. Triggers: 'set up my email system', 'configure email triage', 'build my email knowledge base', 'initialize email management', 'set up inbox triage', or any variation where someone wants to get the email triage system running for the first time."
---
```

## Anti-Patterns To Reject

- Generating all files at once instead of walking through sections
- Asking all questions in one batch
- Hardcoded provider references (Gmail-only thinking)
- Persisting sensitive credentials in knowledge base
- Skipping the “why this question matters” explanation
- Skipping the sample-emails ask for voice (it’s the highest-quality input)
- Overwriting existing files without consent on re-run
- Forcing creation of `rate-card.md` or `evaluation-framework.md` when they don’t apply

## Validation Checklist (Run Before Delivery)

- [ ] Frontmatter parses as YAML
- [ ] Word count 2,000–2,500
- [ ] All 8 interview sections documented
- [ ] All 7 knowledge-base files specified with conditional logic
- [ ] Skip-logic for non-applicable sections documented
- [ ] Sample-email collection step included
- [ ] Privacy boundary explicit
- [ ] Re-run behavior documented
- [ ] Conversational pacing rule stated (no batched questions)
- [ ] Knowledge base file contracts match what `email-triage` expects (cross-validated)
