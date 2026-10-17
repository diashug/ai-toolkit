# AI Toolkit

A collection of specialized agents and skills for AI-assisted software development.

## Agents

| Agent | Purpose |
|-------|---------|
| **code-editor** | Implements precise code changes — new features, bug fixes, refactoring. Use when you know exactly what needs to change. |
| **code-reviewer** | Reviews written code for correctness, quality, security, and adherence to project standards. Invoked after code is written, before committing. |
| **code-searcher** | Locates code patterns, function calls, class usages, or text strings across the entire codebase. Returns file paths and line numbers. |
| **context-gatherer** | Explores a codebase to gather understanding before implementation or debugging. The first agent to call when working on something unfamiliar. |
| **deep-reasoner** | Analyzes complex problems thoroughly — breaks down multi-faceted issues, considers trade-offs, and produces structured plans with actionable recommendations. |
| **directory-mapper** | Maps project directory layout and code organization. Useful for onboarding, finding file types, or documenting structure. |
| **file-fetcher** | Discovers which files in a codebase are relevant to a given topic, feature, or bug. Returns ranked file lists with explanations. |
| **technical-researcher** | Finds technical information from the web — API documentation, best practices, troubleshooting steps, and latest updates on technologies. |

## Skills

| Skill | Purpose |
|-------|---------|
| **code-optimization** | Optimizes code for speed, reliability under load, lower latency, higher throughput, or resource efficiency. Establishes evidence, targets the real bottleneck, and makes the smallest maintainable change without weakening correctness. |
