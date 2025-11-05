# We-Birds Ecosystem Organization

**Version**: 1.0.0
**Last Updated**: 2025-11-05
**Status**: 🔴 DA REVISIONARE

---

## Overview

We-Birds is a collection of general-purpose Python developer tools that work beautifully together. This document describes the organizational structure and standards for the entire ecosystem.

---

## Repositories

### Meta-Repository (Private)

**Location**: `softwell/we-birds` (this repository)

**Purpose**:
- Governance and coordination
- Documentation and standards
- Shared templates
- Cross-project decisions

**Not Included**:
- Implementation code
- Published packages

---

### Tool Repositories (Public)

All We-Birds tools are **public repositories** in the `genropy` organization:

| Tool | Repository | PyPI | Status | Description |
|------|-----------|------|--------|-------------|
| gtext | [genropy/gtext](https://github.com/genropy/gtext) | [gtext](https://pypi.org/project/gtext/) | ✅ Active | Text transformation with pluggable extensions |
| smartswitch | [genropy/smartswitch](https://github.com/genropy/smartswitch) | [smartswitch](https://pypi.org/project/smartswitch/) | ✅ Active | Rule-based function dispatch |
| tryfly | [genropy/tryfly](https://github.com/genropy/tryfly) | - | ⏳ Pre-Alpha | Run GitHub Actions locally |

---

## Local Development Structure

```
genro_ng/
├── we-birds/                      # Meta-repository (softwell/we-birds)
│   ├── CLAUDE.md                  # Central AI instructions
│   ├── ORGANIZATION.md            # This file
│   ├── README.md                  # Meta-repo overview
│   ├── CONTRIBUTING.md            # Contribution guidelines
│   │
│   ├── docs/                      # Documentation
│   │   ├── philosophy.md          # We-Birds principles
│   │   ├── governance.md          # Decision process
│   │   └── integration.md         # How tools integrate
│   │
│   ├── templates/                 # Shared templates
│   │   ├── docker/                # Dockerfile templates
│   │   ├── workflows/             # GitHub Actions
│   │   └── project/               # New project structure
│   │
│   ├── scripts/                   # Automation
│   │   ├── sync-templates.sh      # Update templates
│   │   └── check-consistency.py   # Verify standards
│   │
│   └── sub-projects/              # Git-ignored local clones
│       ├── gtext/                 # genropy/gtext clone
│       ├── smartswitch/           # genropy/smartswitch clone
│       └── tryfly/                # genropy/tryfly clone
│
└── ... (other projects)
```

---

## Project Standards

All We-Birds tools follow these standards:

### Repository Structure

```
tool-name/
├── README.md                  # Project overview
├── LICENSE                    # MIT License
├── pyproject.toml            # Project configuration
├── CLAUDE.md                 # References we-birds/CLAUDE.md
│
├── src/
│   └── tool_name/            # Source code
│       ├── __init__.py
│       └── ...
│
├── tests/                    # Test suite
│   ├── __init__.py
│   └── ...
│
├── docs/                     # Documentation
│   └── ...
│
└── .github/
    └── workflows/            # CI/CD
        ├── tests.yml
        └── publish.yml
```

### Required Files

- ✅ `README.md` - Clear overview with We-Birds badge
- ✅ `LICENSE` - MIT License
- ✅ `pyproject.toml` - Standard Python project config
- ✅ `CLAUDE.md` - References central we-birds/CLAUDE.md
- ✅ `.gitignore` - Standard Python ignores
- ✅ `tests/` directory - Test suite with pytest

### pyproject.toml Standards

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "tool-name"
version = "0.x.y"
description = "Brief description"
authors = [{name = "Genropy Team", email = "info@genropy.org"}]
license = {text = "MIT"}
readme = "README.md"
requires-python = ">=3.10"
keywords = ["we-birds", "developer-tools", ...]

classifiers = [
    "Development Status :: 3 - Alpha",
    "Intended Audience :: Developers",
    "License :: OSI Approved :: MIT License",
    "Programming Language :: Python :: 3.10",
    "Programming Language :: Python :: 3.11",
    "Programming Language :: Python :: 3.12",
]

dependencies = [
    # Runtime dependencies
]

[project.optional-dependencies]
dev = [
    "pytest>=8.0.0",
    "pytest-cov>=4.1.0",
]

[project.urls]
Homepage = "https://github.com/genropy/tool-name"
Repository = "https://github.com/genropy/tool-name"
Issues = "https://github.com/genropy/tool-name/issues"
"We-Birds" = "https://github.com/softwell/we-birds"

[tool.hatch.build.targets.wheel]
packages = ["src/tool_name"]
```

### README Standards

All tool READMEs include:

1. **We-Birds Badge** (at top):
   ```markdown
   [![We-Birds](https://img.shields.io/badge/We--Birds-Family-blue)](https://github.com/softwell/we-birds)
   ```

2. **Quick Start** - Installation and basic usage
3. **Features** - What the tool does
4. **Documentation** - Links to detailed docs
5. **Development** - How to contribute
6. **License** - MIT

### Naming Conventions

**Tool Names**:
- ✅ Clean names: `gtext`, `smartswitch`, `tryfly`
- ❌ No prefixes: `we-birds-gtext`, `wb-smartswitch`

**Python Packages**:
- Match tool name: `import gtext`, `import smartswitch`
- Use snake_case for modules: `from gtext.extensions import *`

**GitHub Topics**:
- `we-birds` (required)
- `developer-tools` (required)
- Tool-specific tags

---

## Dependencies

### Internal Dependencies (We-Birds → We-Birds)

**We-Birds tools can freely depend on each other**:

- ✅ Same ecosystem
- ✅ Same maintainer
- ✅ Same quality standards
- ✅ No concerns about "too many dependencies"

**Example**:
```toml
# tryfly/pyproject.toml
dependencies = [
    "smartswitch>=0.1.0",  # ✅ Internal We-Birds dependency
]
```

**Benefits**:
- Code reuse
- Consistent patterns
- Battle-tested through internal usage
- Faster development

### External Dependencies

Keep external dependencies minimal:
- Prefer standard library when possible
- Choose well-maintained packages
- Pin minimum versions, not exact versions
- Document why each dependency is needed

---

## Development Workflow

### Adding a New Tool

1. **Discuss in we-birds issues** - Gather feedback
2. **Create repository** - In `genropy` organization (public)
3. **Use templates** - From `we-birds/templates/project/`
4. **Add to ORGANIZATION.md** - Update this table
5. **Link to meta-repo** - Add We-Birds badge and URL

### Updating Shared Templates

1. **Modify in we-birds/templates/**
2. **Update version/date in template**
3. **Run sync script** - `scripts/sync-templates.sh`
4. **Test in one tool** - Verify changes work
5. **Roll out to all tools** - Create PRs

### Coordinated Releases

When a change affects multiple tools:

1. **Document in we-birds issues** - Create tracking issue
2. **Plan order** - Dependencies first, dependents second
3. **Test together** - Clone all affected tools to sub-projects/
4. **Release sequentially** - Wait for PyPI propagation
5. **Update documentation** - Cross-link changes

---

## CI/CD

All tools use GitHub Actions for:

### Testing (`tests.yml`)

```yaml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12"]

    steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-python@v5
      with:
        python-version: ${{ matrix.python-version }}
    - name: Install dependencies
      run: |
        pip install -e .[dev]
    - name: Run tests
      run: |
        pytest --cov=src --cov-report=term-missing
```

### Publishing (`publish.yml`)

```yaml
name: Publish to PyPI

on:
  release:
    types: [published]

jobs:
  publish:
    runs-on: ubuntu-latest
    permissions:
      id-token: write  # Trusted Publishing

    steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-python@v5
      with:
        python-version: "3.12"
    - name: Build package
      run: |
        pip install build
        python -m build
    - name: Publish to PyPI
      uses: pypa/gh-action-pypi-publish@release/v1
```

**Note**: All tools use **Trusted Publishing** (no API tokens needed).

---

## Quality Standards

### Code Quality

- Python 3.10+ syntax
- Type hints where beneficial
- Docstrings for public APIs
- PEP 8 compliant (with black formatting)

### Testing

- Minimum 80% code coverage
- Unit tests for all public APIs
- Integration tests for complex features
- Test edge cases and error handling

### Documentation

- README with quick start
- API documentation
- Examples and tutorials
- Changelog (keep updated)

---

## Communication Channels

### GitHub Issues

- **we-birds issues**: Cross-project discussions, proposals, standards
- **Tool issues**: Tool-specific bugs, features, questions

### GitHub Discussions

- Ideas and brainstorming
- Community feedback
- Best practices sharing

### Private Communication

- Softwell team uses we-birds repository for internal coordination
- Public discussions happen in tool repositories

---

## Tool Status Definitions

| Status | Meaning | Criteria |
|--------|---------|----------|
| ⏳ Pre-Alpha | Planning phase | Only documentation, no implementation |
| 🔨 Alpha | Early development | Basic implementation, unstable API |
| ✅ Active | Production-ready | Stable API, published to PyPI |
| 🔧 Maintenance | Bug fixes only | No new features planned |
| 🗄️ Archived | No longer maintained | Use at your own risk |

---

## Version Numbering

All tools follow **Semantic Versioning** (SemVer):

- **0.x.y** - Development phase (breaking changes allowed)
- **1.0.0** - First stable release
- **x.y.z** - MAJOR.MINOR.PATCH

**Breaking changes**:
- Before 1.0.0: Can happen in any release
- After 1.0.0: Only in MAJOR version bumps

---

## License

All We-Birds tools use the **MIT License**:

- Permissive
- Commercial use allowed
- Clear and simple
- Industry standard

---

## Adding Tools to We-Birds

Not every tool belongs in We-Birds. Consider:

1. **General purpose** - Useful beyond one specific project
2. **High quality** - Well-tested, documented, maintained
3. **Aligned philosophy** - Fits We-Birds principles
4. **Team capacity** - Can we maintain it long-term?

**Process**:
1. Open issue in `we-birds` repository
2. Discuss proposal with community
3. Get approval from maintainers
4. Follow standardization checklist
5. Add to this document

---

## Maintenance Responsibilities

### Core Team (Softwell)

- Coordinate releases
- Maintain templates
- Review proposals
- Ensure quality standards

### Contributors

- Report bugs
- Suggest features
- Submit pull requests
- Help with documentation

---

## Future Plans

See [GitHub Issues](https://github.com/softwell/we-birds/issues) for:
- Proposed new tools
- Cross-project improvements
- Template updates
- Process refinements

---

**This document is living documentation. Update it as the ecosystem evolves.**
