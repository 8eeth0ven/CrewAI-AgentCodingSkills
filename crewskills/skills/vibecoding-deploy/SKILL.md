---
name: vibecoding-deploy
description: CrewAgent VibeCoding Deploy — manages the complete deployment lifecycle of CrewAI applications to AMP platform, including CI/CD (GitHub Actions + GitLab CI), versioning, environment management, and rollback. Use when ready to ship a Crew or Flow to production.
---

# VibeCoding Deploy

Orchestrate the deployment of CrewAI applications from local development to the CrewAI AMP production platform, with proper environment management, version control, and CI/CD integration.

## Role

You are a Senior DevOps Engineer and Release Manager in the VibeCoding methodology.

## Process

### Phase 1: Pre-Deployment Checklist

Before deploying, verify:

```bash
# 1. All tests pass
crewai test

# 2. No hardcoded secrets in code
grep -r "api_key\|token\|secret\|password" src/ --include="*.py" | grep -v "os.environ\|os.getenv\|.env"

# 3. Dependencies are pinned
cat pyproject.toml | grep -E "crewai|langchain|openai"

# 4. Environment variables documented
cat .env.example

# 5. Memory reset (clean state for production)
crewai reset-memories --all

# 6. Git status clean
git status
```

### Phase 2: Authentication & Platform Setup

**Step 1: Create AMP account**
- Register at app.crewai.com (free tier available)
- Note your organization and project name

**Step 2: Authenticate CLI**

```bash
crewai login
# Opens browser for OAuth authentication
# Token stored in ~/.crewai/credentials
```

**Step 3: Verify authentication**

```bash
crewai config show
# Should display: logged in as <email>, organization: <org>
```

### Phase 3: Deployment Configuration

#### Environment Variables Strategy

```bash
# .env.production — production overrides (NEVER commit to git)
OPENAI_API_KEY=sk-prod-xxx
SERPER_API_KEY=prod-serper-key
CREWAI_TRACING_ENABLED=true
CREWAI_LOG_LEVEL=INFO

# .env.example — template for other developers (commit to git)
OPENAI_API_KEY=your_openai_api_key
SERPER_API_KEY=your_serper_api_key
CREWAI_TRACING_ENABLED=false
```

#### pyproject.toml Production Settings

```toml
[project]
name = "my-crew-project"
version = "1.2.0"
description = "Production CrewAI application for automated research"
requires-python = ">=3.10,<3.14"

[project.scripts]
my-crew = "src.my_crew.main:run"

[tool.crewai]
entry_point = "src/my_crew/main.py"
tracing_enabled = true
```

### Phase 4: Deploy to CrewAI AMP

#### Standard Deploy

```bash
# Deploy current project to AMP
crewai deploy

# Expected output:
# ✓ Building project...
# ✓ Uploading to CrewAI AMP...
# ✓ Deployment complete!
# Dashboard: https://app.crewai.com/projects/<project_id>
```

#### Deploy with Specific Configuration

```bash
# Deploy with environment
crewai deploy --env production

# Deploy specific version tag
crewai deploy --tag v1.2.0
```

### Phase 5: CI/CD Pipeline Integration

#### GitHub Actions Example

```yaml
# .github/workflows/deploy.yml
name: Deploy CrewAI to AMP

on:
  push:
    branches: [main]
    tags: ['v*']

jobs:
  test-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Install uv
        run: pip install uv

      - name: Install dependencies
        run: uv sync

      - name: Run tests
        run: crewai test
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
          SERPER_API_KEY: ${{ secrets.SERPER_API_KEY }}

      - name: Deploy to AMP
        if: success() && startsWith(github.ref, 'refs/tags/v')
        run: crewai deploy
        env:
          CREWAI_API_KEY: ${{ secrets.CREWAI_API_KEY }}
```

#### GitLab CI Example

```yaml
# .gitlab-ci.yml
deploy:
  stage: deploy
  only:
    - tags
  script:
    - pip install uv
    - uv sync
    - crewai test
    - crewai deploy
  variables:
    OPENAI_API_KEY: $OPENAI_API_KEY
    CREWAI_API_KEY: $CREWAI_API_KEY
```

### Phase 6: Post-Deployment Verification

```bash
# 1. Verify deployment status
crewai deploy --status

# 2. Check traces are flowing
# Visit app.crewai.com → Traces → verify recent executions appear

# 3. Test with a sample input
crewai run --inputs '{"topic": "test deployment"}'

# 4. Monitor for errors
crewai log-tasks-outputs
```

### Phase 7: Rollback Strategy

```bash
# If deployment has issues:

# 1. Identify last stable version
crewai deploy --history

# 2. Rollback to previous version
crewai deploy --rollback v1.1.0

# 3. Verify rollback
crewai deploy --status
```

## Deployment Best Practices

### Version Management
- Use semantic versioning: MAJOR.MINOR.PATCH
- Tag releases in git: `git tag v1.2.0 && git push --tags`
- Deploy only from tagged releases, never from feature branches
- Keep a CHANGELOG.md tracking what changed per version

### Environment Management
- NEVER commit `.env` files to git (add to `.gitignore`)
- Always maintain `.env.example` as a template
- Use different API keys for dev/staging/production
- Rotate API keys regularly (every 90 days minimum)
- Store production secrets in GitHub Secrets / GitLab Variables / HashiCorp Vault

### Safety Gates
- All tests must pass before deploy (`crewai test`)
- Code review must be approved (vibecoding-review)
- No hardcoded secrets in codebase
- Dependencies must be pinned to exact versions
- Deployment window: prefer off-peak hours for production

### Monitoring Post-Deploy
- Watch AMP dashboard for error spikes (first 30 minutes)
- Set up alerts for task failure rate > 5%
- Monitor token usage to catch cost anomalies
- Review traces for unexpected agent behavior
- Keep previous version ready for rollback (minimum 1 version retained)

## Deployment Checklist

```markdown
## Pre-Deploy
- [ ] All tests passing (`crewai test`)
- [ ] Code review approved
- [ ] No hardcoded secrets
- [ ] .env.example updated
- [ ] CHANGELOG.md updated
- [ ] Version bumped in pyproject.toml
- [ ] Git tag created

## Deploy
- [ ] Authenticated (`crewai login`)
- [ ] Deploy command succeeded
- [ ] AMP dashboard shows new version

## Post-Deploy
- [ ] Traces appearing in AMP
- [ ] Test execution succeeded
- [ ] No error spikes in dashboard
- [ ] Rollback plan documented
```

## Output Format

```markdown
## Deployment Plan

### Application: [crew/flow name]
### Version: [semver]
### Target: CrewAI AMP

### Pre-Deploy Verification
[Checklist results]

### Deployment Command
```bash
[exact deploy command]
```

### CI/CD Configuration
[GitHub Actions / GitLab CI YAML]

### Post-Deploy Verification
[Steps + expected results]

### Rollback Plan
[Rollback command + trigger conditions]
```

## Examples

**Input:** Deploy my research crew to production for the first time
**Output:** Plan: 1) crewai login → authenticate, 2) crewai test → verify all pass, 3) check no secrets in code, 4) crewai deploy → deploy to AMP, 5) verify traces at app.crewai.com. Added GitHub Actions CI/CD for automated deploy on git tags.

**Input:** Set up CI/CD pipeline that auto-deploys on merge to main
**Output:** GitHub Actions workflow: on push to main → install uv + deps → crewai test → crewai deploy. Secrets stored in GitHub Secrets (OPENAI_API_KEY, CREWAI_API_KEY). Includes rollback job triggered manually via workflow_dispatch.

**Input:** Current deployment is broken, need to rollback to v1.1.0
**Output:** Rollback plan: 1) crewai deploy --history → confirm v1.1.0 is available, 2) crewai deploy --rollback v1.1.0, 3) crewai deploy --status → verify rollback, 4) run smoke test with crewai run, 5) investigate root cause in v1.2.0 before re-deploying.
