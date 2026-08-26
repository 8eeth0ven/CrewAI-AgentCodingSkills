# Agent Fullcycle Engineering

Reusable Codex/CrewAI-oriented skill for designing, building, debugging, testing, evaluating, observing, deploying, and improving LLM/AI agents — including CrewAI-style multi-agent flows, delegated engineering work, and adaptive scheduled optimization.

这是一组面向 AI Agent 全生命周期工程的开源 Skills,重点覆盖 Agent 项目从想法到上线的完整工程闭环:需求澄清、架构选型、工具调用、RAG、记忆、编排、编码实现、调试、评测、可观测性、安全、部署、交付和持续改进。

## 更新内容 / What's New

This release packages a single, comprehensive Agent Engineering skill as an end-to-end open-source engineering workflow:

- **Single self-contained skill** `agent-fullcycle-engineering` (one `SKILL.md` entry point) that routes each request to the right mode and the right reference.
- **12 task-specific references** covering architecture, development workflow, evaluation & testing, debugging, observability, CrewAI patterns, delivery checklists, and delegation / adaptive automation.
- **Scaffold generator** that produces a full set of planning artifacts (agent brief, feature brief, architecture decision matrix, tool contracts, RAG plan, CrewAI plan, eval plan, test plan, safety model, observability plan, runbook, deployment plan, delegation plan, adaptive automation state, senior readiness review, review checklist, and PR handoff).
- **Senior acceptance gates** for calling a design interview-ready or production-ready.
- **Full lifecycle workflow**: clarify the job, map the environment, choose the simplest architecture, design contracts, build a vertical slice, add tests/evals, add operations, and release in stages.
- **Adaptive scheduled optimization** support with leases, checkpoints, evidence-driven action selection, and safe no-ops.

## Agent 工程师要懂什么 / What Agent Engineers Need to Know

Agent engineering is not only prompt writing. A production-ready agent needs product thinking, software engineering, tool contracts, testing, safety, deployment, and operations.

从浅到深,Agent 工程师需要覆盖这些能力:

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

## Skill 与参考库 / Skill & Reference Library

The single skill `agent-fullcycle-engineering` under `crewskills/skills/` is self-contained. Its `SKILL.md` chooses a response mode from the user's intent and routes to the most relevant reference.

| Reference | Purpose |
| --- | --- |
| `references/beginner-agent-engineer-guide.md` | Beginner-friendly article with examples, terms, and use cases. |
| `references/agent-engineer-roadmap.md` | Shorter beginner-friendly learning path. |
| `references/senior-interviewer-acceptance.md` | Senior-interviewer acceptance review of the work and required depth. |
| `references/advanced-agent-engineer-practical-guide.md` | Advanced-but-readable interview prep: architecture tradeoffs, tools, RAG, evals, safety, observability, deployment, plus a full customer-support Agent case. |
| `references/architecture-patterns.md` | Architecture selection, state, memory, workflow, planner/executor, and multi-agent choices. |
| `references/development-workflow.md` | Coding process, task breakdown, tests, review, and PR handoff. |
| `references/evaluation-testing.md` | Tests, eval datasets, quality metrics, release gates, and regression strategy. |
| `references/debugging-playbook.md` | Runtime failures, bad answers, broken tools, loops, and root-cause isolation. |
| `references/observability-operations.md` | Trace fields, logs, metrics, costs, alerts, and production support. |
| `references/crewai-patterns.md` | CrewAI crews, flows, YAML, memory, tracing, deployment, and CLI verification. |
| `references/delivery-checklists.md` | Requirements, design review, QA, security, launch, git, PR, and post-release checklists. |
| `references/delegation-adaptive-automation.md` | Subagent delegation and scheduled tasks that automatically optimize a project. |

## 响应模式 / Response Modes

`SKILL.md` picks a mode from the user's intent:

- **Beginner learning**: explain from simple to deep, plain language, concrete examples.
- **Interview preparation**: senior-interviewer standards, tradeoffs, failure modes, metrics.
- **Project delivery**: drive the full lifecycle from brief to architecture, implementation, tests, observability, release, and handoff.
- **Debugging or review**: collect evidence first, isolate the failing layer, patch narrowly, add regression coverage.

## 推荐流程 / Recommended Workflow

Follow this order unless the task is narrow:

```text
Idea
  -> Clarify the job (user, outcome, non-goals, success criteria)
  -> Map the environment (data, tools/APIs, permissions, limits, approvals)
  -> Choose the simplest architecture (deterministic code -> LLM call -> tool calling -> RAG -> workflow -> planner/executor -> multi-agent)
  -> Design contracts (input/output/tool schemas, state, error model, audit trail)
  -> Build a vertical slice (one real request, one path, visible result)
  -> Add tests and evals before broadening scope
  -> Add operations (tracing, metrics, cost, retries, redaction, runbooks)
  -> Release in stages (demo -> shadow -> limited rollout -> measured feedback)
```

You do not need to follow every step for every task. For a small change, start directly at the matching stage.

## 仓库结构 / Repository Structure

```text
crewskills/
  skills/
    agent-fullcycle-engineering/
      SKILL.md
      agents/openai.yaml
      scripts/scaffold_agent_project.py
      references/*
        - beginner-agent-engineer-guide.md
        - agent-engineer-roadmap.md
        - senior-interviewer-acceptance.md
        - advanced-agent-engineer-practical-guide.md
        - architecture-patterns.md
        - development-workflow.md
        - evaluation-testing.md
        - debugging-playbook.md
        - observability-operations.md
        - crewai-patterns.md
        - delivery-checklists.md
        - delegation-adaptive-automation.md
LICENSE
README.md
```

The skill is self-contained:

- `SKILL.md` defines when to use the skill and how it should behave.
- `agents/openai.yaml` provides UI metadata such as display name, short description, and default prompt.
- `scripts/scaffold_agent_project.py` generates starter planning artifacts for a new agent project.
- `references/` contains the task-specific deep knowledge the skill routes to.

## 安装 / Installation

Copy the skill folder into your local Codex/CrewAI skills directory:

macOS / Linux / Git Bash:

```bash
cp -r crewskills/skills/agent-fullcycle-engineering ~/.codex/skills/
```

Windows PowerShell:

```powershell
Copy-Item -Recurse -Force .\crewskills\skills\agent-fullcycle-engineering "$env:USERPROFILE\.codex\skills\"
```

Then invoke the skill by name, for example:

```text
Use $agent-fullcycle-engineering to design, delegate, build, test, observe, deploy, and adaptively optimize an AI agent from requirements to release.
```

## 示例提示词 / Example Prompts

```text
Use $agent-fullcycle-engineering to turn my AI customer support idea into a feature brief, then design its architecture.
```

```text
Use $agent-fullcycle-engineering to design agents.yaml, tasks.yaml, and a Flow for this feature.
```

```text
Use $agent-fullcycle-engineering to diagnose why my CrewAI Flow hangs after the research task.
```

```text
Use $agent-fullcycle-engineering to review these agent changes before deploy.
```

Scaffold a new agent project:

```bash
python path/to/crewskills/skills/agent-fullcycle-engineering/scripts/scaffold_agent_project.py --name my-agent --output .
```

## 设计原则 / Design Principles

- **Smallest sufficient architecture**: deterministic code, single LLM call, RAG, tool calling, workflow, then planner/executor / multi-agent.
- **Evidence first**: inspect real files, logs, diffs, traces, and test output before changing code.
- **Tool inputs are untrusted**: validate and sanitize every tool input; make side-effecting tools idempotent; require confirmation for irreversible actions.
- **Testable outputs**: every plan, task, and implementation should have acceptance criteria or verification commands.
- **Production readiness**: include tracing, metrics, cost tracking, retries, redaction, rollback, and human approval paths.
- **Design for refusal and escalation**: a production agent must know when to stop, ask for clarification, or hand off to a human.
- **Clean handoff**: summarize changes, tests, risks, deployment notes, and PR context.

## 贡献 / Contributing

Contributions are welcome. Useful contributions include:

- New CrewAI debugging patterns.
- Better Flow and task planning templates.
- More observability integrations.
- Testing and eval examples.
- Additional references or documentation improvements.

When contributing, keep each skill concise and practical. A skill should contain procedures an agent can actually follow, not broad marketing language.

## GitHub About

Short description:

```text
Open-source Agent Engineering skill for the full lifecycle of AI agent development: design, build, test, observe, deploy, and improve — including CrewAI multi-agent flows.
```

Suggested topics:

```text
crewai, codex, ai-agents, agent-engineering, llm, agent-skills, vibe-coding, observability, evals, devtools, open-source
```

## 许可证 / License

This project is released under the MIT License. See [LICENSE](LICENSE).
