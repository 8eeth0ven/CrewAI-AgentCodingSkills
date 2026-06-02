---
name: vibecoding-observability
description: CrewAgent VibeCoding Observability — configures CrewAI tracing and monitoring across 15+ platforms (AMP, Braintrust, Langtrace, OpenLIT, Arize Phoenix, Datadog, Langfuse, MLflow, Portkey, Weave, TrueFoundry). Use when setting up production monitoring, debugging trace gaps, or integrating external observability platforms.
---

# VibeCoding Observability

Configure comprehensive observability for CrewAI applications — from built-in AMP tracing to external platform integrations — ensuring full visibility into agent decisions, task execution, LLM calls, and cost tracking.

## Role

You are a Senior SRE and Observability Architect in the VibeCoding methodology.

## Process

### Phase 1: Assess Observability Needs

Ask the user to identify their monitoring priorities:

| Priority | What It Tracks | Recommended Platform |
|----------|---------------|---------------------|
| Execution timeline | Agent decisions, task sequence, tool usage | CrewAI AMP (built-in) |
| Cost analysis | Token usage, model cost per run | OpenLIT, Langfuse |
| Evaluation & experiments | Output quality, prompt comparison | Braintrust |
| Security & compliance | PII detection, prompt injection, threat detection | OpenLIT |
| APM integration | Existing Datadog/Grafana dashboards | OpenTelemetry (vendor-neutral) |
| Prompt versioning | Prompt history, A/B testing | Langtrace, OpenLIT PromptHub |
| Dataset curation | Training data from task outputs | Langtrace |
| GPU monitoring | GPU utilization, memory | OpenLIT |

### Phase 2: Configure Built-in Tracing (CrewAI AMP)

**Step 1: Enable tracing in Crew**

```python
from crewai import Agent, Crew, Process, Task

crew = Crew(
    agents=[researcher, writer],
    tasks=[research_task, writing_task],
    process=Process.sequential,
    tracing=True,  # Enable built-in tracing
    verbose=True
)
```

**Step 2: Enable tracing in Flow**

```python
from crewai.flow.flow import Flow, listen, start

class MyFlow(Flow[MyState]):
    def __init__(self):
        super().__init__(tracing=True)  # Enable in constructor

# Or at kickoff
flow = MyFlow(tracing=True)
```

**Step 3: Environment variable (global override)**

```bash
# .env file
CREWAI_TRACING_ENABLED=true
```

**Tracing priority (highest to lowest):**
1. Code-level `tracing=True` / `tracing=False` (always honored)
2. Environment variable `CREWAI_TRACING_ENABLED`
3. CLI preference `crewai traces enable`

**Step 4: Authenticate and view traces**

```bash
crewai login
# Then visit app.crewai.com → Project Dashboard → Traces tab
```

### Phase 3: Configure External Platform Integrations

#### Braintrust (Evaluation + Experiments)

```bash
uv add braintrust[otel] crewai crewai-tools opentelemetry-instrumentation-openai opentelemetry-instrumentation-crewai python-dotenv
```

```python
import os
from braintrust.otel import BraintrustSpanProcessor
from opentelemetry import trace
from opentelemetry.instrumentation.crewai import CrewAIInstrumentor
from opentelemetry.instrumentation.openai import OpenAIInstrumentor
from opentelemetry.sdk.trace import TracerProvider

os.environ["BRAINTRUST_API_KEY"] = "your_api_key"
os.environ["BRAINTRUST_PARENT"] = "project_name:crewai-demo"

def setup_braintrust_tracing():
    provider = TracerProvider()
    trace.set_tracer_provider(provider)
    provider.add_span_processor(BraintrustSpanProcessor())
    CrewAIInstrumentor().instrument(tracer_provider=provider)
    OpenAIInstrumentor().instrument(tracer_provider=provider)

setup_braintrust_tracing()
```

#### Langtrace (Token Tracking + Prompt Versioning)

```bash
pip install langtrace-python-sdk
```

```python
from langtrace_python_sdk import langtrace

# MUST initialize before importing CrewAI
langtrace.init(api_key='<LANGTRACE_API_KEY>')

# Now import CrewAI
from crewai import Agent, Task, Crew
```

**Langtrace features:**
- LLM token & cost tracking per agent interaction
- Execution step trace graph with latency and logs
- Dataset curation from task outputs (manual annotation)
- Prompt version control and management
- Prompt playground with model comparison
- Automated testing and evaluation

#### OpenLIT (Cost + Security + Compliance)

```bash
pip install openlit
```

```python
import openlit

openlit.init(
    otlp_endpoint="http://127.0.0.1:4318",  # Or your OTLP collector
    environment="production",
    application_name="my-crewai-app"
)
```

**OpenLIT features:**
- Analytics dashboard for agent health and performance
- OpenTelemetry-native SDK (send to Grafana, Datadog, etc.)
- Custom cost tracking with custom pricing files
- Anomaly monitoring dashboard for common exceptions
- Compliance & security: PII detection, prompt injection detection
- API key management (centralized, secure)
- PromptHub for prompt management and versioning
- Model playground for testing and comparing models

### Phase 4: Platform Selection Decision Tree

```
Need observability?
├── Just need basic execution traces?
│   └── CrewAI AMP (built-in, tracing=True)
├── Need evaluation + experiments?
│   └── Braintrust
├── Need cost tracking + prompt versioning?
│   └── Langtrace
├── Need security/compliance + cost + GPU monitoring?
│   └── OpenLIT
├── Already using APM (Datadog/Grafana)?
│   └── OpenTelemetry SDK → your existing collector
└── Multi-vendor strategy?
    └── OpenTelemetry-native (OpenLIT) + export to multiple backends
```

### Phase 5: Common Observability Configurations

#### Configuration 1: Production Minimum

```python
crew = Crew(
    agents=[...],
    tasks=[...],
    tracing=True,      # Built-in execution traces
    verbose=True,       # Console logging
)
```

#### Configuration 2: Production with Cost Tracking

```python
# .env
CREWAI_TRACING_ENABLED=true
OPENLIT_OTLP_ENDPOINT=http://collector:4318

# Python
import openlit
openlit.init(environment="production")
```

#### Configuration 3: Full Enterprise

```python
# Built-in + Braintrust (evaluation) + OpenLIT (security)
crew = Crew(
    agents=[...],
    tasks=[...],
    tracing=True,
    verbose=True
)
# Plus Braintrust for experiment tracking
# Plus OpenLIT for PII/compliance scanning
```

### Phase 6: Troubleshooting Observability

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Traces not showing in AMP | Not authenticated | Run `crewai login` |
| Traces not showing in AMP | tracing not enabled | Check `tracing=True` in Crew/Flow |
| Traces not showing in AMP | Env var not set | Verify `CREWAI_TRACING_ENABLED=true` |
| Braintrust spans missing | Initialization order | Call setup before creating Crew |
| Langtrace not capturing | Import order wrong | `langtrace.init()` BEFORE `from crewai import ...` |
| OpenLIT data missing | OTLP endpoint wrong | Verify collector endpoint is reachable |
| Duplicate traces | Multiple platforms enabled | Check for conflicting OpenTelemetry configs |
| Cost data inaccurate | Model pricing missing | Add custom pricing file in OpenLIT |

## Output Format

```markdown
## Observability Configuration

### Platform Selected: [AMP / Braintrust / Langtrace / OpenLIT / Multi]

### Setup Code
[Complete, copy-paste-ready setup code]

### Environment Variables
```bash
[All required env vars]
```

### Verification Steps
1. Run `crewai run` with tracing enabled
2. Check platform dashboard for traces
3. Verify all expected spans appear (agent → task → tool → LLM)

### Monitoring Checklist
- [ ] Execution traces visible
- [ ] Token usage tracked
- [ ] Cost estimates accurate
- [ ] Error traces captured
- [ ] Alert thresholds configured
```

## Guidelines
- Start with built-in AMP tracing — it's zero-config and sufficient for most teams
- Add external platforms only when you need specific features (evaluation, security, cost)
- Use OpenTelemetry-native SDKs when integrating with existing APM infrastructure
- Always verify `crewai login` before troubleshooting trace gaps
- Langtrace requires initialization BEFORE CrewAI imports — this is the #1 setup error

## Examples

**Input:** Set up basic production monitoring for my CrewAI application
**Output:** Configuration: CrewAI AMP built-in tracing. Code: tracing=True in Crew constructor + CREWAI_TRACING_ENABLED=true in .env. Verification: crewai login → run → app.crewai.com dashboard. Zero additional dependencies.

**Input:** We need cost tracking per agent + prompt injection detection for compliance
**Output:** Configuration: OpenLIT (cost tracking + security + compliance). Setup: pip install openlit → openlit.init(environment='production') → custom pricing file for non-OpenAI models. Features: PII detection, prompt injection scanning, per-model cost breakdown.

**Input:** Our team needs A/B experiment tracking across different Crew configurations
**Output:** Configuration: Braintrust (evaluation + experiments). Setup: uv add braintrust[otel] → OpenTelemetry instrumentation for CrewAI + OpenAI → BraintrustSpanProcessor. Features: experiment comparison, automated evaluation metrics, cost analysis per experiment run.
