---
name: forge-benchmark-prompt
description: Generate instructor benchmark prompts for Forge, the workflow analytics platform that evaluates human-AI collaboration quality. Use this skill whenever an instructor wants to create a benchmark prompt, reference prompt, or model prompt for a Forge assignment. Also trigger when someone mentions "Forge benchmark", "benchmark prompt", "instructor prompt template", "workflow evaluation prompt", or asks to generate a high-quality AI collaboration prompt for educational assessment. This skill produces structured prompts following Anthropic's context engineering and prompt engineering best practices.
---

# Forge Benchmark Prompt Generator

This skill generates high-quality instructor benchmark prompts for the Forge workflow analytics platform. Benchmark prompts serve as the reference standard against which student AI collaboration workflows are evaluated.

## What This Skill Does

An instructor provides an assignment question or problem description. This skill transforms it into a structured benchmark prompt that:

- follows Anthropic's prompt engineering best practices (clear structure, XML tags, examples, explicit constraints)
- follows Anthropic's context engineering principles (minimal high-signal tokens, right-altitude instructions, progressive disclosure)
- satisfies all Forge scoring dimensions (PDS, VS, ES, IIS)
- serves as the pedagogical reference for evaluating student workflows

## When to Use

- Instructor says "generate a benchmark prompt for this assignment"
- Instructor provides an assignment question and wants a Forge-ready prompt
- Someone asks to create a model/reference prompt for Forge evaluation
- Someone wants to build an assignment-aware scoring benchmark

## Workflow

1. Collect the assignment question and domain context from the instructor
2. Read `references/prompt-structure.md` for the template and structural rules
3. Generate the benchmark prompt following the template
4. Output the prompt as a copyable artifact (markdown file)

## Step 1 — Gather Input

Ask the instructor for:

- **The assignment question or problem statement** (required)
- **Course and domain context** (e.g., "Mechanical Engineering, 3rd year, Thermodynamics")
- **Any specific constraints** (tools allowed, methods required, output format expectations)
- **Verification expectations** (what should students check or validate?)

If the instructor only provides the question, infer reasonable defaults for the rest based on the domain. State assumptions explicitly in the output.

## Step 2 — Read the Template

Before generating, read `references/prompt-structure.md` for the full template structure and Anthropic-aligned formatting rules. This file defines the exact sections, XML tag conventions, and design principles the output must follow.

## Step 3 — Generate the Benchmark Prompt

Transform the instructor's input into a structured benchmark prompt. The prompt must:

1. Use XML tags to delineate sections (following Anthropic's recommendation for complex prompts)
2. Be at the "right altitude" — specific enough to guide behavior, flexible enough to allow reasoning
3. Provide minimal but sufficient context (no bloat, no redundancy)
4. Explicitly request verification and reasoning (not just answers)
5. Specify output format expectations
6. Support iteration (leave room for follow-up and refinement)

The generated prompt should score highly on all four Forge dimensions:
- **PDS (Prompt Design Score):** Clear objective, structured, constrained, output-specified
- **VS (Verification Score):** Explicitly requests validation, cross-checking, assumption testing
- **ES (Efficiency Score):** Concise, no redundant instructions, well-scoped context
- **IIS (Iteration Intelligence Score):** Supports decomposition, staged reasoning, refinement

## Step 4 — Deliver

Output the benchmark prompt as a markdown file artifact. Include a brief note at the top explaining what it is and how Forge uses it.

## Quality Criteria

A well-generated benchmark prompt:

- Can be understood by a competent student without additional explanation
- Would produce meaningfully different Forge scores when compared against vague or unstructured prompts
- Follows Anthropic's prompting hierarchy: clarity first, then examples, then structured reasoning, then XML organization
- Avoids over-engineering (no "edge-case stuffing" — use canonical expectations instead)
- Is domain-appropriate in language and depth
