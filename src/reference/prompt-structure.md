# Benchmark Prompt Structure Reference

This document defines the structure and rules for generating Forge instructor benchmark prompts. The structure follows Anthropic's published prompt engineering best practices and context engineering principles.

## Design Principles

These principles come directly from Anthropic's guidance and should govern every benchmark prompt generated.

**1. Right-altitude instructions.** Not too brittle (no hardcoded if-else logic), not too vague (no "help me with this"). The prompt should give the AI concrete signals for desired output while remaining flexible enough for genuine reasoning.

**2. Minimal high-signal tokens.** Every sentence must earn its place. Cut redundancy. If a constraint is obvious from context, don't restate it. The goal is the smallest set of tokens that maximizes the likelihood of the desired outcome.

**3. Clear section boundaries.** Use XML tags to delineate distinct sections. Anthropic recommends XML tagging or Markdown headers for complex prompts; for benchmark prompts, XML tags provide cleaner parsing for Forge's scoring pipeline.

**4. Examples over rules.** Where a behavioral expectation is hard to articulate in rules, provide a canonical example instead. Anthropic's guidance: "examples are the pictures worth a thousand words."

**5. Verification as a first-class requirement.** Engineering workflows demand validation. The prompt must explicitly instruct the AI to verify, not just generate.

**6. Support for iteration.** The prompt should create room for follow-up, decomposition, and refinement — not demand a single monolithic answer.

---

## Template Structure

The benchmark prompt uses the following XML-tagged sections. Each section has a clear purpose aligned to a Forge scoring dimension.

```
<context>
[Domain background, course level, relevant technical context]
[What the student is working on and why]
[Any prerequisite knowledge or references]
</context>

<task>
[Clear, unambiguous statement of what needs to be accomplished]
[Use precise action verbs: analyze, design, compare, evaluate, derive, optimize]
[State the scope — what is in bounds and what is not]
</task>

<constraints>
[Material, method, or tool constraints]
[Formatting requirements]
[Assumptions the student should make explicit]
[Boundaries: what approaches are acceptable vs. prohibited]
</constraints>

<verification>
[What the AI and student should validate before finalizing]
[Specific checks: edge cases, unit consistency, assumption sensitivity]
[Cross-referencing expectations: compare against known values, alternative methods]
[Error identification: what common mistakes to watch for]
</verification>

<output_format>
[Exact structure of the expected response]
[Whether to include intermediate reasoning or just final results]
[Required sections: e.g., assumptions list, solution steps, final answer, limitations]
[Any specific formatting: tables, equations, diagrams, code blocks]
</output_format>

<reasoning_guidance>
[How to approach the problem — staged thinking, not just answer generation]
[Decomposition strategy: break into sub-problems if complex]
[Tradeoff analysis: when to weigh alternatives]
[Justification expectations: explain why, not just what]
</reasoning_guidance>
```

---

## Section-by-Section Rules

### `<context>`

**Purpose:** Provide the AI with domain grounding so it can reason appropriately.

**Forge alignment:** Drives PDS (context completeness) and ES (no redundant context).

Rules:
- State the domain, course level, and relevant technical background in 2-4 sentences
- Include only information the AI needs to reason correctly — omit general knowledge it already has
- If referencing specific data, equations, or standards, include them here or instruct the AI where to find them
- Do NOT pad with motivational framing ("this is an important problem because...") — that wastes tokens

**Example:**
```
<context>
This is a third-year Mechanical Engineering thermodynamics assignment. The student is analyzing a Rankine power cycle with reheat. Assume working fluid is water/steam. Reference steam tables are available; use standard NIST values.
</context>
```

### `<task>`

**Purpose:** State exactly what needs to be done.

**Forge alignment:** Drives PDS (clarity of objective, ambiguity reduction).

Rules:
- Lead with a strong action verb
- State the deliverable, not the process ("Determine the thermal efficiency" not "Work through the thermal efficiency calculation")
- Scope the task: what's included, what's excluded
- One task block per prompt — if the assignment has multiple parts, list them as numbered sub-tasks within this block

**Example:**
```
<task>
Determine the thermal efficiency of a Rankine cycle with reheat, given the following operating conditions:
1. Calculate the enthalpy at each state point
2. Determine the net work output and heat input
3. Calculate the thermal efficiency
4. Compare the efficiency with and without reheat
</task>
```

### `<constraints>`

**Purpose:** Define boundaries that shape the solution space.

**Forge alignment:** Drives PDS (constraints, ambiguity reduction) and ES (scoped context).

Rules:
- List concrete constraints, not aspirational goals
- Include assumptions the student must state explicitly
- Specify allowed methods or tools (e.g., "Use the ideal gas assumption" or "Do not use pre-built solvers")
- If there are no meaningful constraints beyond standard practice, this section can be brief

**Example:**
```
<constraints>
- Assume steady-state operation and negligible kinetic/potential energy changes
- Use ideal pump and turbine assumptions unless otherwise noted
- Express all temperatures in Kelvin and pressures in kPa
- Show unit conversions explicitly
- Do not use pre-computed cycle efficiency tables
</constraints>
```

### `<verification>`

**Purpose:** Instruct the AI to validate its reasoning, not just produce an answer.

**Forge alignment:** Directly drives VS (Verification Score). This is the most important section for Forge evaluation.

Rules:
- Request at least two distinct verification actions
- Include a cross-check (compare result against a known value, alternative method, or limiting case)
- Include an assumption sensitivity check (what happens if a key assumption changes?)
- Request identification of potential errors or failure modes
- Avoid generic "double-check your work" — be specific about what to check

**Example:**
```
<verification>
- Verify that enthalpy values are consistent with steam table data at each state point
- Check that the thermal efficiency falls within the expected range for reheat Rankine cycles (typically 35-45%)
- Test sensitivity: how does efficiency change if turbine isentropic efficiency drops to 85%?
- Identify at least one common error in Rankine cycle analysis and explain how to avoid it
</verification>
```

### `<output_format>`

**Purpose:** Define what a successful response looks like.

**Forge alignment:** Drives PDS (output specification) and ES (no wasted tokens on unwanted content).

Rules:
- Specify the structure of the response (sections, tables, equations, etc.)
- State whether intermediate reasoning should be shown or only final results
- If the assignment requires a specific deliverable format (report, table, code), say so
- Keep format requirements proportional to the assignment complexity

**Example:**
```
<output_format>
Structure your response as:
1. Assumptions — list all assumptions made
2. State point analysis — table with temperature, pressure, enthalpy, and entropy at each point
3. Calculations — show work for net work, heat input, and efficiency
4. Comparison — side-by-side table of with/without reheat
5. Limitations — note any simplifications that affect accuracy
</output_format>
```

### `<reasoning_guidance>`

**Purpose:** Guide the AI toward analytical thinking, not just answer retrieval.

**Forge alignment:** Drives IIS (Iteration Intelligence Score) and supports VS (reasoning quality).

Rules:
- Suggest a decomposition strategy if the problem is multi-step
- Ask for justification of key decisions ("explain why you chose..." not just "choose...")
- Encourage tradeoff analysis where multiple approaches exist
- This section should feel like a thinking coach, not a recipe

**Example:**
```
<reasoning_guidance>
Approach this problem in stages:
1. First establish all state points before calculating any cycle quantities
2. For each state point, explain which thermodynamic relationship you are using and why
3. When comparing reheat vs. no-reheat, explain the physical mechanism that drives the efficiency difference — do not just report numbers
4. If you encounter ambiguity in the problem statement, state your interpretation and justify it
</reasoning_guidance>
```

---

## Anti-Patterns to Avoid

These patterns reduce prompt quality and Forge scores. Do not include them in generated benchmarks.

**Edge-case stuffing.** Do not enumerate every possible edge case. Anthropic's guidance: curate canonical examples, not exhaustive rule lists. If the prompt has more than 5-6 constraints, it's likely over-specified.

**Vague verification.** "Check your work" is not verification. Every verification instruction must name a specific action and a specific criterion.

**Motivational padding.** "This is a challenging and important problem" adds zero signal. Cut it.

**Redundant context.** If the task says "analyze a Rankine cycle," don't repeat "you are analyzing a Rankine cycle" in the context section.

**Over-prescriptive reasoning.** Don't script every thought step. Give a strategy, not a script. The point is to evaluate whether the student (or AI) can reason, not whether they follow a recipe.

**Missing iteration support.** If the prompt demands a perfect single-shot answer with no room for follow-up or decomposition, it works against IIS. Leave space for "if you need clarification, state what's missing."

---

## Assembly Checklist

Before finalizing a generated benchmark prompt, verify:

- [ ] Every section uses the correct XML tag
- [ ] `<task>` starts with a clear action verb
- [ ] `<constraints>` lists only concrete, necessary constraints
- [ ] `<verification>` includes at least 2 specific validation actions
- [ ] `<output_format>` defines the response structure
- [ ] `<reasoning_guidance>` encourages staged thinking, not rote steps
- [ ] No redundancy between sections
- [ ] Total prompt length is proportional to assignment complexity (a simple problem should not produce a 500-word benchmark)
- [ ] A competent student in the target domain would understand every instruction without additional context
