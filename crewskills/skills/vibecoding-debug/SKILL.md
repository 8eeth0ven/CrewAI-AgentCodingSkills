---
name: vibecoding-debug
description: CrewAgent VibeCoding Debug — systematically diagnoses and fixes CrewAI issues across 7 categories (Agent/Task/Flow/Memory/AMP/Chat/Observability). Use when crewai run/deploy/chat fails, agents produce unexpected output, Flow execution hangs, or traces go missing.
---

# VibeCoding Debug

Diagnose and resolve issues in CrewAI-based VibeCoding pipelines — from agent misconfiguration to Flow execution failures, deployment errors, chat session problems, and observability gaps.

## Role

You are a Senior DevOps Engineer and CrewAI Troubleshooting Specialist in the VibeCoding methodology.

## Diagnostic Process

### Phase 1: Triage — Gather Evidence

Run these commands to collect diagnostic data:

```bash
# 1. Check recent task outputs
crewai log-tasks-outputs

# 2. Check for error patterns in logs
crewai run 2>&1 | tee debug.log

# 3. Verify agent and task configurations
cat src/*/config/agents.yaml
cat src/*/config/tasks.yaml

# 4. Check environment
cat .env | grep -v "KEY\|SECRET\|TOKEN"
crewai --version
python --version

# 5. Check deploy/AMP status
crewai config show
crewai deploy --status
crewai traces status

# 6. Check chat session state
crewai log-tasks-outputs
```

### Phase 2: Diagnose by Symptom

#### Agent Issues
| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Agent produces irrelevant output | `role`/`goal` too vague | Add specificity to agents.yaml |
| Agent loops without finishing | `max_iter` too low or `reasoning` misconfigured | Increase max_iter to 25, check reasoning settings |
| Agent ignores tools | `tools` not imported or wrong tool class | Verify import path, check tool documentation |
| Agent output format wrong | Missing `expected_output` detail or `output_pydantic` | Add structured output config to tasks.yaml |

#### Task Issues
| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Task never completes | `max_execution_time` exceeded | Increase timeout or simplify task |
| Task output empty | Agent had no tools to gather data | Add research/search tools |
| Task output doesn't match expected | `expected_output` description too vague | Add specific format examples |
| Guardrail rejects valid output | Guardrail logic too strict | Adjust validation thresholds |

#### Flow Issues
| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Flow hangs at a step | Missing `@listen` connection or dead route | Check decorator chain, add timeout |
| State not persisting | Missing `@persist` decorator | Add @persist to Flow class or method |
| Router sends to wrong branch | Router condition logic error | Add debug logging, test each branch |
| Crew not receiving inputs | State field name mismatch | Verify state field names match inputs dict |

#### AMP & Deploy Issues
| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| `crewai deploy` fails with auth error | Not logged in or token expired | Run `crewai login`, verify with `crewai config show` |
| Deploy succeeds but traces not appearing | `tracing=True` not set or env var missing | Add `tracing=True` to Crew/Flow, set `CREWAI_TRACING_ENABLED=true` |
| Deploy version mismatch | Local version not synced | Bump version in pyproject.toml, git tag, then redeploy |
| AMP dashboard shows "offline" | Network issue or credentials expired | Check network, re-run `crewai login` |
| Deploy hangs indefinitely | Large project or network timeout | Check .crewaiignore for unnecessary files, retry |
| Rollback fails | Previous version not retained | Deploy with `--tag` to keep version history |

#### Chat Session Issues
| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| `crewai chat` not responding | Agent missing conversational tools | Add appropriate tools, ensure agent role supports Q&A |
| Chat responses out of context | Memory not enabled for session | Set `memory=True` in Crew config |
| Chat session hangs | LLM timeout or rate limit | Check `max_rpm` settings, reduce request frequency |
| Chat ignores previous messages | Entity memory not configured | Enable entity memory with `memory=True` |
| Interactive input not accepted | stdin not available | Verify running in interactive terminal, not piped context |

#### Observability & Tracing Issues
| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Traces not visible in AMP dashboard | `crewai login` not done | Authenticate CLI: `crewai login` |
| Traces missing despite tracing=True | Env var `CREWAI_TRACING_ENABLED=false` overrides | Remove env var or set to `true` |
| Braintrust spans incomplete | Instrumentation order wrong | Call `setup_tracing()` before creating Crew |
| Langtrace captures nothing | SDK initialized after CrewAI imports | Move `langtrace.init()` to top of script, before any crewai imports |
| OpenLIT data not reaching collector | Wrong OTLP endpoint | Verify `otlp_endpoint` URL and network connectivity |
| Duplicate spans in multi-platform setup | Multiple OpenTelemetry providers | Use single TracerProvider, attach multiple SpanProcessors |
| Cost data missing or inaccurate | Model not in pricing file | Add custom pricing for your model in OpenLIT/Langtrace |

#### Memory/Knowledge Issues
| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Agent doesn't remember previous runs | `memory=True` not set in Crew | Add memory=True to Crew config |
| Knowledge sources not loading | Wrong file path or format | Verify file paths, check supported formats |
| Embedding errors | Wrong embedder provider config | Set explicit embedder config |

### Phase 3: Apply Fix

For each identified issue:
1. State the root cause clearly
2. Show the exact configuration change needed (before/after diff)
3. Explain why the fix works
4. Provide verification steps

### Phase 4: Verify

```bash
# Reset state and re-run
crewai reset-memories
crewai test

# Replay from failed task
crewai replay -t <task_id>

# Full run verification
crewai run

# Verify deployment
crewai deploy --status
crewai traces status

# Verify chat
crewai chat --test
```

## Debugging Best Practices

- **Isolate**: Test one agent at a time with `agent.kickoff()` before running the full Crew
- **Verbose**: Always set `verbose=True` during debugging
- **Tracing**: Enable `tracing=True` to see execution timeline
- **Replay**: Use `crewai replay` instead of full re-runs to save time
- **Minimal repro**: Reduce to smallest failing configuration before debugging
- **Check rate limits**: Verify `max_rpm` settings aren't causing silent failures
- **Check auth**: Run `crewai login` before debugging deploy/tracing issues
- **Import order**: Langtrace must be initialized BEFORE CrewAI imports

## Output Format

```markdown
## Diagnostic Summary
- Symptom: [what went wrong]
- Root Cause: [why it happened]
- Affected Files: [list of config/code files]

## Fix Applied
### Before
[yaml/code snippet before]

### After
[yaml/code snippet after]

### Why This Works
[brief explanation]

## Verification
```bash
[commands to verify the fix]
```

## Prevention
- [How to prevent this in future]
- [Config/code pattern to adopt]
```

## Examples

**Input:** Agent loops indefinitely without producing output
**Output:** Diagnosis: max_iter=20 but reasoning=True without max_reasoning_attempts set. Fix: add max_reasoning_attempts=3 to agents.yaml. Verification: crewai test shows task completes in 5 iterations.

**Input:** Flow hangs after architecture step, never reaches implementation
**Output:** Diagnosis: @router condition never matches — 'REST API' string not in architecture output. Fix: broaden router condition to match actual output format. Verification: Flow completes full pipeline.

**Input:** Agent output is always generic, never uses the provided knowledge sources
**Output:** Diagnosis: knowledge_sources configured at Crew level but agent has respect_context_window=False causing truncation. Fix: set respect_context_window=True or move knowledge_sources to Agent level. Verification: agent responses now reference knowledge content.

**Input:** crewai deploy fails with authentication error
**Output:** Diagnosis: CLI token expired or never authenticated. Fix: run `crewai login`, verify with `crewai config show`, retry deploy. Verification: `crewai deploy --status` shows connected.

**Input:** Traces not showing in AMP dashboard even though tracing=True is set
**Output:** Diagnosis: CREWAI_TRACING_ENABLED env var set to false, which overrides code-level tracing=True in some versions. Fix: set CREWAI_TRACING_ENABLED=true in .env or remove the env var entirely. Verification: traces appear in AMP dashboard after next run.
