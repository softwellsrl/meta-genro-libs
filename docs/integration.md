# We-Birds Tool Integration

**Version**: 1.0.0
**Last Updated**: 2025-11-05
**Status**: 🔴 DA REVISIONARE

---

## Overview

We-Birds tools are designed to work together. This document explains how they integrate and common usage patterns.

---

## Philosophy: "Eat Your Own Dog Food"

We-Birds tools **use other We-Birds tools** internally:

**Benefits**:
- ✅ Real-world testing
- ✅ Validation of APIs
- ✅ Consistent patterns
- ✅ Faster development

**Example**: `tryfly` uses `smartswitch` for dispatching step handlers.

---

## Integration Patterns

### 1. Direct Dependencies

We-Birds tools can depend on each other as regular Python packages.

**Example - tryfly uses smartswitch**:

```toml
# tryfly/pyproject.toml
[project]
name = "tryfly"
dependencies = [
    "smartswitch>=0.1.0",  # We-Birds internal dependency
]
```

```python
# tryfly/src/tryfly/executor.py
from smartswitch import Switcher

step_handlers = Switcher()

@step_handlers(typerule={'step': RunStep})
def handle_run_step(step: RunStep, context: Context):
    """Execute shell command"""
    ...
```

**No concerns**:
- Same maintainer (Genropy Team)
- Same quality standards
- Same release cadence

### 2. Template Generation

Tools can use `gtext` to generate configuration files and boilerplate.

**Example - Dockerfile generation**:

```dockerfile
# templates/docker/python.Dockerfile.gtext

FROM python:{{python_version}}-slim

# Common setup for all We-Birds projects
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

{{#if dev_mode}}
# Development dependencies
RUN pip install --no-cache-dir pytest pytest-cov
{{/if}}

COPY . .

CMD [{{command}}]
```

**Generate**:
```bash
gtext render python.Dockerfile.gtext \
  --var python_version=3.12 \
  --var dev_mode=true \
  --var command='["python", "app.py"]'
```

**Commit both**:
- `python.Dockerfile.gtext` (source)
- `Dockerfile` (generated)

Users don't need gtext to use the tool.

### 3. Shared Configuration

Tools follow consistent configuration patterns.

**Example - pyproject.toml structure**:

All We-Birds tools use:
- Same build system (hatchling)
- Same test framework (pytest)
- Same author metadata
- Same license (MIT)

This makes it easy to:
- Switch between projects
- Apply common scripts
- Maintain consistency

### 4. Documentation Cross-References

Tools reference each other in documentation.

**Example - tryfly README**:

```markdown
## How It Works

tryfly uses several We-Birds tools:

- **smartswitch** - Dispatches different step types (run, uses, if)
- **gtext** (optional) - Generates Dockerfile templates

Learn more about the [We-Birds ecosystem](https://github.com/softwell/we-birds).
```

---

## Common Integration Scenarios

### Scenario 1: CLI Tool Using Multiple We-Birds Tools

**Use case**: Build a CLI that needs dispatching and text generation.

```python
# my_cli.py
from smartswitch import Switcher
from gtext import render

# Use smartswitch for command dispatch
commands = Switcher()

@commands(valrule=lambda cmd, **kw: cmd == 'init')
def command_init(cmd: str, template: str):
    """Initialize new project from template"""
    # Use gtext to generate files
    output = render(f'templates/{template}.gtext')
    with open('output.txt', 'w') as f:
        f.write(output)

@commands(valrule=lambda cmd, **kw: cmd == 'build')
def command_build(cmd: str, config: dict):
    """Build project"""
    ...

# Dispatch
commands()(cmd=args.command, template=args.template)
```

**Benefits**:
- Clean command dispatch with smartswitch
- Template generation with gtext
- Familiar patterns from We-Birds

### Scenario 2: Test Suite Using tryfly

**Use case**: Test your GitHub Actions locally before pushing.

```bash
# Run specific workflow
tryfly .github/workflows/tests.yml

# Run specific job
tryfly .github/workflows/tests.yml --job test

# Run with specific matrix
tryfly .github/workflows/tests.yml --matrix python-version=3.12
```

**Integration**:
- Works with any Python project
- No code changes needed
- Just run before pushing

### Scenario 3: Dockerfile Template Management

**Use case**: Maintain consistent Dockerfiles across multiple projects.

**Create shared template**:
```dockerfile
# templates/docker/python-app.Dockerfile.gtext

FROM python:{{python_version}}-slim

{{#if install_system_deps}}
RUN apt-get update && apt-get install -y {{system_deps}} && rm -rf /var/lib/apt/lists/*
{{/if}}

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

{{#if entrypoint}}
ENTRYPOINT [{{entrypoint}}]
{{else}}
CMD [{{command}}]
{{/if}}
```

**Generate for each project**:
```bash
# Project A - web app
gtext render python-app.Dockerfile.gtext \
  --var python_version=3.12 \
  --var install_system_deps=true \
  --var system_deps='"postgresql-client"' \
  --var command='["gunicorn", "app:app"]' \
  > Dockerfile

# Project B - CLI tool
gtext render python-app.Dockerfile.gtext \
  --var python_version=3.11 \
  --var command='["python", "cli.py"]' \
  > Dockerfile
```

**Benefits**:
- Single source of truth
- Consistent best practices
- Easy updates (regenerate all)

---

## Versioning and Compatibility

### Internal Dependencies

We-Birds tools specify **minimum versions**:

```toml
dependencies = [
    "smartswitch>=0.1.0",  # Minimum version
]
```

**Not pinned exactly**:
- Allows users to get updates
- Avoids dependency conflicts
- Trusts semantic versioning

### Breaking Changes

**Before 1.0.0**:
- Breaking changes can happen anytime
- Document in changelog
- Coordinate releases if needed

**After 1.0.0**:
- Breaking changes only in MAJOR versions
- Deprecation period (at least 1 minor version)
- Migration guides provided

### Coordinated Releases

When a breaking change affects multiple tools:

1. **Plan**: Create tracking issue in we-birds
2. **Test**: Clone all tools to sub-projects/, test together
3. **Release order**: Dependencies first, dependents second
4. **Announce**: Document in all affected changelogs

**Example**:
```
smartswitch 2.0.0 (breaking change)
  ↓
Wait for PyPI propagation
  ↓
tryfly 1.0.0 (updated to use smartswitch 2.0)
```

---

## Testing Integration

### Unit Tests

Test your code with We-Birds tools as normal dependencies:

```python
# tests/test_handlers.py
from smartswitch import Switcher
from myapp.handlers import step_handlers

def test_run_step_handler():
    """Test that RunStep is dispatched correctly"""
    from myapp.models import RunStep, Context

    step = RunStep(run="echo hello")
    context = Context(...)

    result = step_handlers()(step=step, context=context)

    assert result.success
```

### Integration Tests

Test real workflows with multiple We-Birds tools:

```python
# tests/integration/test_full_workflow.py
import subprocess

def test_tryfly_with_real_workflow():
    """Test that tryfly can run our actual CI workflow"""
    result = subprocess.run(
        ['tryfly', '.github/workflows/tests.yml'],
        capture_output=True,
        text=True
    )

    assert result.returncode == 0
    assert 'All steps passed' in result.stdout
```

---

## Development Workflow

### Local Development with Multiple Tools

**Setup**:
```bash
cd we-birds/sub-projects

# Clone tools you're working on
git clone git@github.com:genropy/smartswitch.git
git clone git@github.com:genropy/tryfly.git

# Install in development mode
cd smartswitch && pip install -e .[dev]
cd ../tryfly && pip install -e .[dev]
```

**Benefits**:
- Changes in smartswitch immediately available in tryfly
- Test integration without publishing
- Fast iteration

### Testing Changes Before Release

1. **Modify dependency**: Make changes to smartswitch
2. **Test dependent**: Run tryfly tests with modified smartswitch
3. **Fix issues**: Iterate until tests pass
4. **Release**: Publish smartswitch, then update tryfly

---

## Documentation Integration

### Cross-Linking

Tools reference each other in documentation:

```markdown
## Dependencies

tryfly uses the following We-Birds tools:

- [smartswitch](https://github.com/genropy/smartswitch) - Rule-based function dispatch
  - Used for: Step type dispatching, image selection, error handling
  - See: [WE_BIRDS_USAGE.md](docs/WE_BIRDS_USAGE.md)
```

### Example Sharing

Tools share examples and patterns:

```markdown
## Pattern: Type-Based Dispatch

This pattern is used in several We-Birds tools:

**smartswitch** (where it's defined):
```python
@handlers(typerule={'obj': MyType})
def handle_my_type(obj: MyType): ...
```

**tryfly** (how it's used):
```python
@step_handlers(typerule={'step': RunStep})
def handle_run_step(step: RunStep, context: Context): ...
```

See [smartswitch documentation](https://github.com/genropy/smartswitch) for more.
```

---

## Best Practices

### DO ✅

- **Use We-Birds tools liberally** - They're meant to be used together
- **Document integration** - Explain how tools work together
- **Test integration** - Ensure tools work well together
- **Share patterns** - Document reusable integration patterns
- **Keep versions compatible** - Use semantic versioning properly

### DON'T ❌

- **Don't duplicate functionality** - If a We-Birds tool does it, use it
- **Don't tightly couple** - Tools should work independently too
- **Don't hide integration** - Document dependencies clearly
- **Don't create circular dependencies** - A → B → A is bad
- **Don't lock versions** - Use minimum versions, not exact pins

---

## Integration Examples in the Wild

### tryfly → smartswitch

**What**: Step dispatching, image selection, error handling

**Why**: Complex dispatch logic with multiple conditions

**Pattern**: Type-based and value-based rules

**Code**: See `tryfly/temp/WE_BIRDS_USAGE.md`

### Future: tryfly → gtext

**What**: Dockerfile generation, workflow templates

**Why**: Consistent configuration across test environments

**Pattern**: Template preprocessing

**Status**: Planned for Phase 2

---

## Adding New Integrations

When adding integration between We-Birds tools:

1. **Document the use case** - What problem does it solve?
2. **Show examples** - Clear code examples
3. **Update both tools' docs** - Cross-reference
4. **Test thoroughly** - Unit and integration tests
5. **Track patterns** - Add to pattern library

---

## Questions?

If you have questions about tool integration:
1. Check this document
2. Look at existing integrations (e.g., tryfly → smartswitch)
3. Open discussion in we-birds repository

---

**Remember**: Integration is a feature, not a bug. We-Birds tools are designed to work together.
