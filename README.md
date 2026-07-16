# CrewAI Agent Coding Skills

Reusable Codex/CrewAI-oriented skills for designing, planning, coding, debugging, reviewing, observing, deploying, and handing off AI agent projects.

这是一组面向 AI Agent 工程开发的开源 Skills，重点覆盖 CrewAI 项目从想法到上线的完整工程闭环：需求澄清、方案设计、代码实现、调试、可观测性、代码审查、部署和 PR/Commit 交付。

## 更新内容 / What's New

This release packages the CrewAI skills as an end-to-end open-source agent engineering workflow:

- Reworked all 8 VibeCoding skills for a clearer full-cycle CrewAI development process.
- Added `agents/openai.yaml` metadata for every skill so tools can display names, descriptions, and default prompts.
- Strengthened the workflow from idea to release: brainstorm, plan, codegen, debug, observability, review, deploy, and commit.
- Added evidence-first debugging guidance for CrewAI agents, tasks, flows, memory, deploy, chat, and tracing failures.
- Added production gates for tests, evals, secrets, observability, smoke tests, rollback, and PR handoff.
- Added this GitHub README for open-source usage, installation, examples, and contribution guidance.

## Agent 工程师要懂什么 / What Agent Engineers Need to Know

Agent engineering is not only prompt writing. A production-ready agent needs product thinking, software engineering, tool contracts, testing, safety, deployment, and operations.

从浅到深，Agent 工程师需要覆盖这些能力：

- **基础能力**: Python or TypeScript, HTTP APIs, JSON schemas, Git, tests, environment variables, and secret management.
- **Prompt and context engineering**: instructions, structured outputs, context windows, tool descriptions, prompt versioning, and failure cases.
- **Tool calling**: strict input schemas, permissions, retries, timeouts, idempotency, side-effect controls, and human confirmation.
- **RAG and knowledge**: document parsing, chunking, embeddings, retrieval, reranking, citations, access control, and groundedness evaluation.
- **Workflow orchestration**: deterministic code for fixed steps, LLM judgment for fuzzy steps, state models, branching, recovery, and stopping rules.
- **CrewAI and multi-agent design**: agents, tasks, crews, flows, `agents.yaml`, `tasks.yaml`, `@start`, `@listen`, `@router`, memory, planning, and delegation.
- **Testing and evals**: unit tests, tool contract tests, integration tests, offline evals, safety probes, regression suites, and release gates.
- **Debugging**: reproduce failures, inspect traces/logs/tool outputs, isolate the failing layer, patch narrowly, and add a regression case.
- **Observability and operations**: traces, logs, metrics, token/cost tracking, alerts, runbooks, smoke tests, rollback, and incident review.
- **Delivery discipline**: code review, clean commits, PR descriptions, changelogs, deployment notes, and open-source documentation.

## Skills

| Skill | Purpose |
| --- | --- |
| `vibecoding-brainstorm` | Convert rough product or agent ideas into structured feature briefs. |
| `vibecoding-plan` | Create CrewAI implementation plans with agents, tasks, flows, tests, and release gates. |
| `vibecoding-codegen` | Implement CrewAI tasks as production-ready code with tests and verification. |
| `vibecoding-debug` | Diagnose and fix CrewAI agent, task, flow, memory, deploy, chat, and tracing failures. |
| `vibecoding-observability` | Add traces, logs, metrics, cost tracking, and production runbooks. |
| `vibecoding-review` | Review CrewAI and agent code for bugs, security, permissions, tests, and operability. |
| `vibecoding-deploy` | Prepare, verify, deploy, monitor, and roll back CrewAI applications. |
| `vibecoding-commit` | Create Conventional Commits, PR descriptions, changelog entries, and handoff notes. |

## Recommended Workflow

```text
Idea
  -> vibecoding-brainstorm
  -> vibecoding-plan
  -> vibecoding-codegen
  -> vibecoding-debug
  -> vibecoding-observability
  -> vibecoding-review
  -> vibecoding-deploy
  -> vibecoding-commit
  -> Pull Request / Release
```

You do not need to use every skill for every task. For small changes, start directly with the skill that matches the current stage.

## Repository Structure

```text
crewskills/
  skills/
    vibecoding-brainstorm/
      SKILL.md
      agents/openai.yaml
    vibecoding-plan/
      SKILL.md
      agents/openai.yaml
    ...
LICENSE
README.md
```

Each skill is self-contained:

- `SKILL.md` defines when to use the skill and how it should behave.
- `agents/openai.yaml` provides UI metadata such as display name, short description, and default prompt.

## Installation

Copy the skill folders into your local Codex skills directory:

```bash
cp -r crewskills/skills/* ~/.codex/skills/
```

On Windows PowerShell:

```powershell
Copy-Item -Recurse -Force .\crewskills\skills\* "$env:USERPROFILE\.codex\skills\"
```

Then invoke a skill by name, for example:

```text
Use $vibecoding-plan to convert this feature brief into a CrewAI implementation plan.
```

## Example Prompts

```text
Use $vibecoding-brainstorm to turn my AI customer support idea into a feature brief.
```

```text
Use $vibecoding-plan to design agents.yaml, tasks.yaml, and a Flow for this feature.
```

```text
Use $vibecoding-debug to diagnose why my CrewAI Flow hangs after the research task.
```

```text
Use $vibecoding-review to review these CrewAI changes before deploy.
```

## Design Principles

- Evidence first: inspect real files, logs, diffs, traces, and test output before changing code.
- Smallest sufficient architecture: avoid multi-agent complexity unless it has a clear purpose.
- Testable outputs: every plan, task, and implementation should have acceptance criteria or verification commands.
- Production readiness: include observability, rollback, secret safety, and human approval paths.
- Clean handoff: summarize changes, tests, risks, deployment notes, and PR context.

## Contributing

Contributions are welcome. Useful contributions include:

- New CrewAI debugging patterns.
- Better Flow and task planning templates.
- More observability integrations.
- Testing and eval examples.
- Documentation improvements.

When contributing, keep each skill concise and practical. A skill should contain procedures an agent can actually follow, not broad marketing language.

## GitHub About

Short description:

```text
Open-source Codex/CrewAI skills for full-cycle AI agent development, debugging, review, observability, deployment, and PR handoff.
```

Suggested topics:

```text
crewai, codex, ai-agents, agent-engineering, llm, agent-skills, vibe-coding, observability, evals, devtools, open-source
```

## License

This project is released under the MIT License. See [LICENSE](LICENSE).
