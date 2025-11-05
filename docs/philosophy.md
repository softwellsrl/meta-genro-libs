# We-Birds Philosophy

**Version**: 1.0.0
**Last Updated**: 2025-11-05
**Status**: 🔴 DA REVISIONARE

---

## Core Principles

We-Birds is guided by a set of core principles that shape every tool in the ecosystem.

---

## 1. "Eat Your Own Dog Food"

**We use what we build.**

We-Birds tools are used **within We-Birds tools**:

- `smartswitch` dispatches logic in `tryfly`
- `gtext` generates templates for all tools
- Real usage drives improvements

**Why this matters**:
- ✅ **Real testing** - We discover limitations through actual use
- ✅ **Better design** - APIs are designed for real problems, not theoretical ones
- ✅ **Faster feedback** - Issues surface immediately during development
- ✅ **Credibility** - We trust our tools enough to depend on them

**Example**:
```python
# tryfly uses smartswitch for step dispatching
from smartswitch import Switcher

step_handlers = Switcher()

@step_handlers(typerule={'step': RunStep})
def handle_run_step(step: RunStep, context: Context):
    # Execute shell command in Docker
    ...
```

If smartswitch has issues, tryfly development finds them immediately.

---

## 2. General Purpose > Specific Purpose

**Tools solve common problems, not niche ones.**

We-Birds tools are **general-purpose**:
- Useful across many projects
- Not tied to specific frameworks or domains
- Solve universal developer problems

**What belongs in We-Birds**:
- ✅ Text transformation (gtext)
- ✅ Function dispatch (smartswitch)
- ✅ Local CI testing (tryfly)
- ✅ File manipulation, data processing, developer workflows

**What doesn't belong**:
- ❌ Web framework-specific plugins
- ❌ Domain-specific business logic
- ❌ Single-project utilities

**Test**: "Would developers in different ecosystems find this useful?"

---

## 3. Simple by Default, Powerful When Needed

**Start simple, grow gradually.**

Every tool has:
- **Simple default behavior** - Works out of the box
- **Clear configuration** - Override defaults when needed
- **Extension points** - Add custom behavior without forking

**Example - gtext**:
```python
# Simple: Just include a file
"""
{{include "template.txt"}}
"""

# Powerful: Custom extensions
from gtext import Extension

class MyExtension(Extension):
    def process(self, text, context):
        # Custom logic
        return text
```

**Example - smartswitch**:
```python
# Simple: Type-based dispatch
@handlers(typerule={'obj': MyType})
def handle_my_type(obj): ...

# Powerful: Complex rules
@handlers(valrule=lambda kw: condition1(kw) and condition2(kw))
def handle_complex_case(**kwargs): ...
```

**Guideline**: 80% of users should need only 20% of features.

---

## 4. Minimal Dependencies

**Depend on standard library first, external packages second.**

Dependency philosophy:
- ✅ **Standard library preferred** - Always available
- ✅ **We-Birds internal dependencies** - Same maintainer, high quality
- ⚠️ **External dependencies** - Only when necessary, well-maintained only

**Why minimal dependencies**:
- Faster installation
- Fewer security concerns
- Easier to maintain
- Less breaking changes from upstream

**Exception**: We-Birds tools can **freely depend on each other**:
- Same ecosystem
- Same quality standards
- Same maintainer

**Example**:
```toml
# tryfly dependencies
dependencies = [
    "pyyaml",           # External (necessary for YAML parsing)
    "docker",           # External (necessary for Docker API)
    "smartswitch>=0.1.0",  # Internal (We-Birds tool)
]
```

---

## 5. Documentation is Code

**Documentation is a first-class deliverable.**

For every tool:
- **README** - Clear overview, quick start, examples
- **API docs** - Docstrings for all public APIs
- **Examples** - Real-world use cases
- **Architecture docs** - Design decisions (for complex tools)

**Documentation standards**:
- Write for beginners, not experts
- Show code examples, not just descriptions
- Keep up-to-date (tests should validate examples)
- Explain **why**, not just **how**

**Example**:
```python
def transform(text: str, extension: Extension) -> str:
    """
    Transform text using the given extension.

    Args:
        text: The input text to transform
        extension: Extension instance to apply

    Returns:
        Transformed text

    Example:
        >>> from gtext import transform, UppercaseExtension
        >>> transform("hello", UppercaseExtension())
        'HELLO'

    Why: Extensions provide pluggable transformation logic,
         allowing users to add custom behavior without modifying gtext.
    """
```

---

## 6. Test Everything

**If it's not tested, it's broken.**

Testing standards:
- **Minimum 80% coverage** - Aim higher for critical paths
- **Unit tests** - Test individual functions in isolation
- **Integration tests** - Test real-world workflows
- **Edge cases** - Test error handling and unusual inputs

**CI/CD**:
- Run tests on every commit
- Test on Python 3.10, 3.11, 3.12
- Block merges if tests fail

**Example test structure**:
```
tests/
├── __init__.py
├── unit/                 # Fast, isolated tests
│   ├── test_parser.py
│   └── test_utils.py
├── integration/          # Real workflows
│   ├── test_workflows.py
│   └── test_docker.py
└── fixtures/             # Test data
    └── sample_workflow.yml
```

---

## 7. AI-Assisted Development is Real

**Modern estimates account for AI assistance.**

We acknowledge the reality of AI-assisted coding:
- **Traditional estimates** - Weeks of manual work
- **AI-assisted estimates** - Hours to days (10-30x faster)

**What this means**:
- Focus on architecture, not boilerplate
- Iterate faster
- Spend more time on design and testing

**Example** (from tryfly ARCHITECTURE.md):
```
Phase 1: MVP
- Traditional: 1.5-2 weeks
- AI-assisted: 3-4 hours
```

**This is not aspirational - it's observed reality.**

---

## 8. "Any Color as Long as It's Black"

**One well-done solution beats multiple mediocre options.**

Inspired by Ford's Model T philosophy:
- ✅ **Docker-only execution** (tryfly) - One path, well-optimized
- ✅ **Single template format** (gtext) - Master it, don't choose between alternatives

**Why**:
- Simpler codebase (easier maintenance)
- Better quality (focus on one solution)
- Faster development (no time spent on alternatives)
- Clear documentation (one way to do things)

**Counter-example** (what we avoid):
```python
# ❌ Don't do this
class Executor:
    def execute(self, mode='docker'):
        if mode == 'docker':
            # Docker logic
        elif mode == 'native':
            # Native logic
        elif mode == 'hybrid':
            # Hybrid logic
```

Instead:
```python
# ✅ Do this
class Executor:
    """Execute workflows in Docker containers."""

    def execute(self):
        # One implementation, done right
```

**Guideline**: If a feature requires "choose your flavor", rethink the design.

---

## 9. Public Tools, Private Governance

**Tools are open source. Coordination is internal.**

Structure:
- **Public repositories** (genropy org) - Tool implementations
- **Private meta-repo** (softwell org) - Coordination, governance, templates

**Why**:
- ✅ Users get full transparency (tool repos are public)
- ✅ Team has space to discuss and plan (meta-repo is private)
- ✅ Clear separation (governance vs implementation)

**What's public**:
- All tool source code
- All tool issues and PRs
- All tool documentation

**What's private**:
- Cross-project coordination
- Template development
- Roadmap planning
- Team discussions

---

## 10. Critical Thinking Over Enthusiasm

**Challenge ideas. Don't validate blindly.**

When evaluating proposals:
- ❌ "Great idea! Let's do it!"
- ✅ "What would frustrated users say?"
- ✅ "What will they complain about in GitHub issues?"
- ✅ "What's the HackerNews feedback?"

**Reality check questions**:
1. What's the real-world problem?
2. How will this break?
3. What's the maintenance cost?
4. Will users actually use this?
5. What's the simpler solution?

**Example**:
```
Proposal: "Add native execution mode to tryfly as fallback"

Critical analysis:
- Doubles the codebase
- Doubles the test surface
- Maintenance burden for 2 code paths
- What % of users actually need it? (probably <5%)
- Docker is already widely available

Conclusion: Not worth the complexity
```

**Guideline**: Be the devil's advocate. Ideas that survive criticism are worth implementing.

---

## Anti-Patterns to Avoid

### ❌ Feature Creep

**Don't add features just because we can.**

- Every feature has maintenance cost
- Complexity is the enemy
- Focus on core use cases

### ❌ Premature Abstraction

**Don't abstract until patterns emerge.**

- Wait for 2-3 real use cases
- Abstractions should simplify, not complicate
- Inline code is better than bad abstraction

### ❌ Over-Engineering

**Don't solve problems we don't have.**

- Build for current needs, not hypothetical futures
- Simple solutions > clever solutions
- Refactor when needed, not preemptively

### ❌ Validation Without Criticism

**Don't agree just to please.**

- Challenge assumptions
- Think about real users
- Prioritize technical accuracy over validation

---

## When to Break the Rules

These principles are guidelines, not laws. Break them when:

1. **User feedback** - Real users need something different
2. **Technical constraints** - Better solution emerges
3. **Cost/benefit** - Rule costs more than it's worth

**But**: Document why you're breaking the rule and what you learned.

---

## Living Philosophy

This philosophy evolves with the ecosystem:

- **Learn from mistakes** - Update principles based on experience
- **Listen to users** - Adapt to real-world needs
- **Stay pragmatic** - Principles serve the tools, not vice versa

---

## Questions to Ask

Before adding a feature or tool:

1. Is it general-purpose?
2. Does it solve a real problem?
3. Can we maintain it long-term?
4. Would we use it ourselves?
5. What's the simplest solution?
6. What will users complain about?

If you can't answer these clearly, it's not ready.

---

**Remember**: We-Birds is about **quality over quantity**. Better to have 3 excellent tools than 20 mediocre ones.
