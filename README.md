# forge-skills

A collection of Claude skills for the **Forge** workflow analytics and educational research platform. These skills extend Claude's capabilities to support instructors in designing high-quality AI collaboration benchmarks aligned with Forge's scoring framework.

---

## What is Forge?

Forge is a browser-based platform that evaluates the quality of human-AI collaboration workflows in engineering education. Rather than grading final answers alone, Forge measures *how* students interact with AI systems — focusing on reasoning quality, verification behavior, prompt design, and workflow efficiency.

Forge scores student workflows across four dimensions:

| Score | Name | Weight | What It Measures |
|---|---|---|---|
| PDS | Prompt Design Score | 40% | Clarity, structure, constraints, output specification |
| VS | Verification Score | 25% | Fact-checking, assumption validation, cross-checking |
| ES | Efficiency Score | 20% | Token efficiency, context hygiene, redundancy |
| IIS | Iteration Intelligence Score | 15% | Prompt refinement quality over time |

---

## Available Skills

### `forge-benchmark-prompt`

Generates instructor benchmark prompts — the reference prompts that Forge uses to evaluate student AI workflows for a specific assignment.

An instructor provides an assignment question. The skill produces a fully structured benchmark prompt following Anthropic's prompt engineering and context engineering best practices, with all six required sections that Forge's scoring pipeline expects.

**Install:** download `forge-benchmark-prompt.skill` and install it in Claude.

---

## Installation

1. Download the `.skill` file for the skill you want
2. In Claude, go to **Settings → Skills**
3. Click **Install Skill** and select the `.skill` file
4. The skill is now available in any conversation

---

## Using the Benchmark Prompt Skill

Once installed, describe your assignment to Claude in plain language:

```
Generate a Forge benchmark prompt for this assignment: [paste your question].
Course: 3rd year Software Engineering — Data Structures and Algorithms.
```

Claude will ask for any missing details (course context, constraints, verification expectations) and produce a structured benchmark prompt as a downloadable markdown file.

### What you need to provide

| Input | Required | Example |
|---|---|---|
| Assignment question or problem | Yes | "Design a hash table with open addressing" |
| Course and domain | Recommended | "2nd year Computer Science, Algorithms" |
| Specific constraints | Optional | "No built-in hash libraries allowed" |
| Verification expectations | Optional | "Students should test collision handling" |

If you only provide the question, Claude infers reasonable defaults from the domain and states them explicitly in the output.

---

## Benchmark Prompt Structure

Every generated benchmark prompt uses six XML-tagged sections, each mapped to a Forge scoring dimension:

```xml
<context>
  Domain background, course level, relevant technical context
</context>

<task>
  Clear, unambiguous statement of what needs to be accomplished
</task>

<constraints>
  Method, tool, and formatting boundaries
</constraints>

<verification>
  Specific validation actions the AI must perform before finalizing
</verification>

<output_format>
  Exact structure and format of the expected response
</output_format>

<reasoning_guidance>
  Staged thinking strategy — not a recipe, but a reasoning approach
</reasoning_guidance>
```

This structure follows Anthropic's recommendations for complex prompts: XML tags for clear section boundaries, right-altitude instructions (specific but not brittle), and minimal high-signal tokens with no redundancy.

---

## Repo Structure

```
forge-skills/
├── README.md
├── forge-benchmark-prompt.skill        ← install this
└── src/
    └── forge-benchmark-prompt/
        ├── SKILL.md                    ← skill logic and workflow
        └── references/
            ├── prompt-structure.md     ← template rules and section definitions
            └── sample-output.md        ← complete worked example
```

The `src/` folder contains the raw source files. Edit these and repackage if you want to customize the skill. The `.skill` file in the root is what end users install.

---

## Design Philosophy

The benchmark prompt structure is grounded in two Anthropic publications:

- [Prompt Engineering Best Practices](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview) — clarity, examples, XML structuring, chain-of-thought
- [Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) — minimal high-signal tokens, right-altitude instructions, avoiding context rot

The core principle: a benchmark prompt should be the smallest set of tokens that fully defines high-quality AI collaboration for a given assignment — no bloat, no edge-case stuffing, and explicit verification as a first-class requirement.

---

## Contributing

To add a new skill or modify an existing one:

1. Edit the source files in `src/<skill-name>/`
2. Repackage: `python package_skill.py src/<skill-name>`
3. Replace the `.skill` file in the repo root
4. Update this README with any new skills or changed behavior

---

## Related

- [Forge Platform](https://github.com/your-org/forge) — the main Forge codebase
- [Anthropic Prompt Engineering Docs](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview)
