# 🐦 We-Birds - Meta Repository

**Private coordination repository for the We-Birds toolkit ecosystem**

---

## What is We-Birds?

**We-Birds** is a collection of high-quality, general-purpose Python developer tools. Each tool solves a specific problem elegantly, and they work beautifully together.

**Philosophy**: "Developer tools that fly together" 🐦

---

## This Repository

This is the **private meta-repository** for We-Birds governance and coordination. It contains:

- 📋 Documentation and governance
- 🎨 Shared templates (Dockerfile, workflows, etc.)
- 🔧 Cross-project scripts and automation
- 📝 Design decisions and roadmaps

**This is NOT a code repository.** Individual tools are in their own public repositories.

---

## Public Repositories

All We-Birds tools are **open source** and published on **PyPI**:

### Active Tools

- **[gtext](https://github.com/genropy/gtext)** - Text transformation with pluggable extensions
  - `pip install gtext`
  - PyPI: https://pypi.org/project/gtext/

- **[smartswitch](https://github.com/genropy/smartswitch)** - Rule-based function dispatch
  - `pip install smartswitch`
  - PyPI: https://pypi.org/project/smartswitch/

- **[tryfly](https://github.com/genropy/tryfly)** - Run GitHub Actions locally
  - `pip install tryfly`
  - Status: Pre-Alpha (in development)

---

## Repository Structure

```
we-birds/
├── README.md              # This file
├── CLAUDE.md             # AI assistant instructions
├── ORGANIZATION.md       # Ecosystem structure
├── CONTRIBUTING.md       # Contribution guidelines
│
├── docs/                 # Documentation
│   ├── philosophy.md     # We-Birds philosophy
│   ├── governance.md     # Decision-making process
│   └── integration.md    # How tools integrate
│
├── templates/            # Shared templates
│   ├── docker/          # Dockerfile templates
│   ├── workflows/       # GitHub Actions templates
│   └── project/         # New project templates
│
├── scripts/             # Automation
│   ├── sync-templates.sh
│   └── check-consistency.py
│
└── sub-projects/        # Git-ignored, local clones
    ├── gtext/          # Clone of genropy/gtext
    ├── smartswitch/    # Clone of genropy/smartswitch
    └── tryfly/         # Clone of genropy/tryfly
```

---

## Working with Sub-Projects

The `sub-projects/` directory is **git-ignored**. Clone public repositories there for local development:

```bash
cd sub-projects

# Clone We-Birds tools
git clone git@github.com:genropy/gtext.git
git clone git@github.com:genropy/smartswitch.git
git clone git@github.com:genropy/tryfly.git
```

**Note**: Changes to sub-projects are committed to their own repositories, not to this meta-repo.

---

## Templates

Shared templates ensure consistency across all We-Birds projects:

- **Docker templates**: Standard Dockerfile patterns with security best practices
- **Workflow templates**: GitHub Actions for testing, publishing, documentation
- **Project templates**: README, pyproject.toml, CONTRIBUTING.md structures

See [templates/README.md](templates/README.md) for usage.

---

## Governance

Key decisions about the We-Birds ecosystem are documented in:

- [docs/philosophy.md](docs/philosophy.md) - Core principles
- [docs/governance.md](docs/governance.md) - Decision process
- [GitHub Issues](https://github.com/softwell/we-birds/issues) - Proposals and discussions

---

## Who Uses This Repo?

**Softwell team members** use this private repo for:
- Coordinating releases
- Sharing templates
- Documenting decisions
- Planning roadmap

**External contributors** work directly with public tool repositories (genropy/*).

---

## Links

### Meta Repositories
- This repo (we-birds): Private governance
- [genro-next-generation](https://github.com/softwell/genro-next-generation): Genro Blocks meta-repo

### Tool Repositories (Public)
- [genropy/gtext](https://github.com/genropy/gtext)
- [genropy/smartswitch](https://github.com/genropy/smartswitch)
- [genropy/tryfly](https://github.com/genropy/tryfly)

### Package Registries
- [PyPI: gtext](https://pypi.org/project/gtext/)
- [PyPI: smartswitch](https://pypi.org/project/smartswitch/)
- [PyPI: tryfly](https://pypi.org/project/tryfly/) (coming soon)

---

**Maintained by [Softwell](https://softwell.it)**
