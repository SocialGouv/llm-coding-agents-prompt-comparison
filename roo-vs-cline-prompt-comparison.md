# Roo vs Cline prompt-engineering comparison (based on extracted prompt texts)

## Scope and sources

This report compares prompt-engineering techniques visible in the extracted instruction texts from:

- [`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md)
- [`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md)

It focuses on *how each agent is instructed to behave* (structure, constraints, workflows), not on product UI or runtime implementation.

## Versus table (prompt-engineering techniques)

| Dimension | Roo | Cline |
|---|---|---|
| Agent identity and positioning | Defines multiple roles via **modes** (architect/code/ask/debug/orchestrator/etc.), each with a different role definition; overall identity is still a software engineer baseline ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L1782)). | Defines a single agent identity baseline (Cline as skilled software engineer) ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L10)). Variants can extend role description significantly ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L119)). |
| Prompt modularity and assembly | System prompt is explicitly described as **sections** (capabilities, rules, tool use, modes, etc.), implying composable assembly ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L10)). | System prompt is explicitly composed from **components** (agent_role, rules, tool guidelines, act_vs_plan) and then overridden by variants ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L8)). |
| Tool-gating and stepwise execution constraints | Enforces **exactly one tool call per assistant response** and “every assistant message must include a tool call” in the system instructions ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L245)). | Enforces **one tool per message** in global rules/tool-use guidance ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L70)). Variant text can additionally instruct “always respond using tools” ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L122)). |
| Confirmation loop / iterative control | Strong emphasis on “wait for user response after each tool use” and do not assume results ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L169)). | Same emphasis on waiting for confirmation and step-by-step tool use ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L74)). |
| Plan vs act separation | Achieved primarily via **mode switching** ([`switch_mode()`](ROO-EXTRACTED_PROMPTS.md#L768)) and explicit mode definitions (e.g., architect vs code) rather than a dedicated plan-mode responder channel in the extracted text ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L109)). | Has an explicit **ACT MODE vs PLAN MODE** section describing different available tools and behaviors ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L93)). Some variants add additional plan/act tools like [`act_mode_respond()`](CLINE-EXTRACTED_PROMPTS.md#L165) / [`plan_mode_respond()`](CLINE-EXTRACTED_PROMPTS.md#L169). |
| Environment anchoring (workspace, CWD constraints) | Repeats strict workspace anchoring: project base dir, relative paths, cannot change directories, no `~`/`$HOME` ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L161)). | Similar: current working directory, cannot change directories, no `~`/`$HOME` ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L27)). |
| Tool-choice heuristics | Encodes “choose best tool; prefer list/search/read over asking; think before [`execute_command()`](ROO-EXTRACTED_PROMPTS.md#L479)” and describes when to use each tool ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L205)). Also introduces semantic-first exploration ([`codebase_search()`](ROO-EXTRACTED_PROMPTS.md#L446)) as a hard rule in its tool docs ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L445)). | Encodes similar heuristics (prefer [`list_files()`](CLINE-EXTRACTED_PROMPTS.md#L31) vs shell listing, craft regex carefully, combine search/read) ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L31)). Does not show a semantic-search-first constraint in the extracted subset. |
| Output formatting constraints as alignment technique | Adds a very strong formatting constraint: all filename + language construct references must be clickable markdown links ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L69)). | No equivalent universal clickable-link constraint shown. Some variants encourage rich markdown in non-tool responses ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L133)). |
| Customization and “policy layering” | Explicitly supports **user custom instructions** from multiple sources and standard rule sets, suggesting hierarchical injection/merging ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L28)). | Supports **variant overrides** (native-gpt-5-1, microwave, xs, etc.), and also includes conditional toggles like a “yoloModeToggled” branch in rules ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L37)). |
| Safety / confidentiality controls | Has a base **Vendor Confidentiality** block (never reveal vendor; constrained responses) ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L140)). | Includes a vendor confidentiality block in the “Microwave variant” override ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L244)). |
| Extended workflows and sub-prompts (specialized calls) | Provides “support prompt” templates (enhance/condense/explain/fix/improve) and a “slash command” execution tool concept ([`run_slash_command()`](ROO-EXTRACTED_PROMPTS.md#L697)) ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L693)). | Provides specialized prompts for “explain code changes” and “commit message generation” ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L338)). |
| Ecosystem extensibility via tools | Strong emphasis on MCP servers and instructions for creating/adding them ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L81)). | Mentions browser tooling snippets ([`BROWSER_RULES`](CLINE-EXTRACTED_PROMPTS.md#L55)) and generic use cases, but not MCP-server creation in the extracted content shown ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L53)). |
| Tone and “anti-chattiness” constraints | Enforces directness (e.g., forbidden to start with certain phrases) and discourages back-and-forth beyond completing the task ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L170)). | Same pattern: forbids specific openings, avoid pointless back and forth, do not end completion with a question ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L40)). |

## Similarities (similitudes)

Both prompt stacks strongly converge on a few high-leverage techniques:

1. **Tool-gated interaction**: actions happen through tools, not freeform execution ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L245), [`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L70)).
2. **Stepwise, confirmation-driven workflow**: perform one operation, wait for the user/tool result, then continue ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L169), [`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L81)).
3. **Strong environment anchoring**: fixed workspace/CWD, explicit path handling, no `~`/`$HOME`, no `cd` roaming ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L161), [`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L27)).
4. **Heuristic tool selection guidance**: prefer discoverable facts via list/search/read tools over asking; be careful with regex; investigate before editing ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L205), [`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L31)).
5. **Tone steering**: direct, technical, avoid certain conversational openers, and do not end final responses with questions ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L175), [`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L41)).

## Formulation and wording patterns (how the instructions are written)

This section focuses on *the instruction language itself* (imperatives, modal verbs, prohibitions, and emphasis patterns). The goal is to help agent builders reuse these high-signal formulations.

### 1) Imperatives and force markers (MUST / NEVER / DO NOT)

- Roo uses high-force imperatives frequently, including literal emphasis like **NON-NEGOTIABLE** and **STRICTLY FORBIDDEN**, to reduce ambiguity and constrain the assistant’s output style.
  - Examples: “ALL responses MUST…” ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L77)), “You MUST use exactly one tool per message” ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L248)), “This is NON-NEGOTIABLE” ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L908)), “You are STRICTLY FORBIDDEN…” ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L175)).
- Cline also uses high-force imperatives (MUST/NEVER/DO NOT), but tends to frame them as “RULES” bullets and execution constraints.
  - Examples: “You cannot `cd`…” ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L27)), “NEVER end attempt_completion…” ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L41)), “STRICTLY FORBIDDEN…” ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L42)).

Builder takeaway: reserve **MUST/NEVER** for invariants that you want the agent to treat as correctness constraints; place them near tool-use and output formatting rules to maximize compliance.

### 2) Prohibition phrasing patterns (negative constraints)

Both systems repeat prohibitions in multiple places to make them “sticky”:

- “Do not ask for more information than necessary” appears as an explicit constraint in both prompt stacks ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L170), [`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L36)).
- “Do not assume tool outcomes” + “wait for confirmation” is repeated as a workflow law ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L169), [`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L81)).

Builder takeaway: repeat prohibitions at least twice (once in rules, once in tool-use workflow) because they are easy for models to drift on.

### 3) Modal verbs and hedging control (should / may / prefer)

- Roo uses **prefer** and “When X, do Y” patterns heavily to encode heuristics without making them absolute; then uses **MUST** for hard constraints.
  - Examples: “Prefer to execute complex CLI commands…” ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L24)), “You should prefer using other editing tools…” ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L1192)).
- Cline similarly uses heuristic language like “Be sure to consider…” and “When relevant…”, reserving MUST/NEVER for critical constraints.
  - Examples: “Be sure to consider the type of project…” ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L33)), “When relevant, briefly explain…” in the explain-changes system prompt ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L344)).

Builder takeaway: distinguish **policy** (MUST/NEVER) from **heuristics** (prefer/when relevant) to keep agents both reliable and adaptable.

### 4) Self-referential workflow cues (meta-instructions)

- Roo encodes meta-workflow directly in the objective: “Analyze the user’s task… Work through goals sequentially…” ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L128)).
- Cline’s variants go further by prescribing “Deliverables and Success Criteria” checklists and “Implementation Workflow” steps ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L176)).

Builder takeaway: meta-instructions that describe *how to think and sequence work* (not just what to do) reduce mode collapse on multi-step tasks.

### 5) Tone markers and style guardrails

- Both use explicit disallow-lists (e.g., do not start with certain words) as a precise style constraint ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L175), [`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L42)).
- Cline sometimes mixes in “friendly, conversational” when the task is *explain changes*, showing task-specific tone overrides ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L344)).


Builder takeaway: allow **task-scoped tone overrides** for explain/teach tasks, but keep a strict baseline tone for execution reliability.

## Common prompt-engineering practices (distilled guide for agent builders)

These are practices that appear in both systems (sometimes via different wording), and are broadly reusable for building capable agents.

1. **Tool-gate everything important**
   - Require tools for actions; forbid “silent” execution. This makes the agent auditable and reduces hallucinated changes ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L248), [`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L70)).

2. **One-step-at-a-time execution loop**
   - Explicitly encode: pick tool → run → wait for result → adapt → repeat ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L209), [`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L74)).

3. **Separate invariants from heuristics**
   - Put hard invariants in MUST/NEVER lists (e.g., cannot `cd`, do not end with questions).
   - Put heuristics in “prefer” / “when relevant” guidance (e.g., prefer list_files over `ls`) ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L31), [`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L24)).

4. **Anchor the agent to the user’s environment**
   - Repeat workspace constraints, path requirements, and environment_details usage so the agent does not drift into generic advice ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L161), [`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L27)).

5. **Minimize user burden while avoiding assumption**
   - “Don’t ask for more than necessary” + “prefer tools to discover facts” is a strong combined pattern ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L170), [`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L36)).

6. **Use task-specific micro-prompts for high-frequency workflows**
   - Explain-changes, commit messages, summarize/condense, fix/improve are examples of specialized prompts that raise quality by narrowing intent ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L338), [`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L1578)).

7. **Include explicit completion criteria**
   - Both systems include “when done, use [`attempt_completion()`](ROO-EXTRACTED_PROMPTS.md#L330)” style constraints ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L135), [`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L36)). This prevents infinite loops and makes “done” observable.

## Mini-glossary of recurring instruction patterns

- **Invariant rule**: Non-negotiable constraint stated with MUST/NEVER/DO NOT (e.g., cannot change directories) ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L27)).
- **Heuristic rule**: Preferred behavior stated with “prefer”, “when relevant”, “be sure to” ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L24)).
- **Confirmation loop**: Run one tool, then wait for the user’s response before continuing ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L169)).
- **Tool gating**: Forcing all actions through tools; forbidding non-tool replies (as an interaction model) ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L248)).
- **Environment anchoring**: Explicitly binding actions to workspace/CWD and environment_details context ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L193)).
- **Tone guardrail**: Style constraints enforced via prohibited phrases and “don’t be conversational” rules ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L42)).

## Notable differences (practical implications)

- **Roo emphasizes output-format compliance as a control mechanism** (clickable link rule), which forces structured references and reduces ambiguity in collaborative code review contexts ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L69)).
- **Cline emphasizes a more explicit plan/act dichotomy** as a first-class concept in its system prompt, and some variants formalize that with dedicated responder tools ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L93)).
- **Roo’s prompt stack is designed to be extended via MCP servers** (tools/resources) and includes detailed guidance for adding new capabilities ([`ROO-EXTRACTED_PROMPTS.md`](ROO-EXTRACTED_PROMPTS.md#L1207)).
- **Cline includes productized micro-prompts for specific developer workflows** like multi-file diff explanations and commit message generation ([`CLINE-EXTRACTED_PROMPTS.md`](CLINE-EXTRACTED_PROMPTS.md#L338)).
