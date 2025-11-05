# We-Birds Templates

**Version**: 1.0.0
**Last Updated**: 2025-11-05

---

## Overview

This directory contains shared templates used across all We-Birds projects to ensure consistency and best practices.

---

## Directory Structure

```
templates/
├── README.md              # This file
├── docker/               # Dockerfile templates
│   ├── python-base.Dockerfile.gtext
│   ├── python-cli.Dockerfile.gtext
│   └── README.md
├── workflows/            # GitHub Actions templates
│   ├── tests.yml
│   ├── publish.yml
│   ├── docs.yml
│   └── README.md
└── project/             # New project templates
    ├── README.md.template
    ├── pyproject.toml.template
    ├── CLAUDE.md.template
    └── README.md
```

---

## Template Categories

### 1. Docker Templates

Standardized Dockerfiles for Python projects.

**Location**: `templates/docker/`

**Available templates**:
- `python-base.Dockerfile.gtext` - Base Python image setup
- `python-cli.Dockerfile.gtext` - CLI application with entrypoint

**Usage**:
```bash
# Generate Dockerfile from template
gtext render templates/docker/python-cli.Dockerfile.gtext \
  --var python_version=3.12 \
  --var tool_name=mytool \
  > Dockerfile
```

**Why templates?**:
- ✅ Consistent security practices
- ✅ Best practices baked in
- ✅ Easy to update all projects
- ✅ Preprocessor approach (commit both source and generated)

See `docker/README.md` for details.

### 2. GitHub Actions Workflows

Standardized CI/CD workflows.

**Location**: `templates/workflows/`

**Available templates**:
- `tests.yml` - Run test suite on push/PR
- `publish.yml` - Publish to PyPI on release
- `docs.yml` - Build and publish documentation

**Usage**:
```bash
# Copy to new project
cp templates/workflows/tests.yml my-project/.github/workflows/
cp templates/workflows/publish.yml my-project/.github/workflows/
```

**Features**:
- Matrix testing (Python 3.10, 3.11, 3.12)
- Trusted Publishing (no API tokens)
- Caching for faster builds
- Test coverage reporting

See `workflows/README.md` for details.

### 3. Project Templates

Boilerplate for new We-Birds projects.

**Location**: `templates/project/`

**Available templates**:
- `README.md.template` - Project README structure
- `pyproject.toml.template` - Python project configuration
- `CLAUDE.md.template` - AI assistant instructions

**Usage**:
```bash
# Create new project structure
./scripts/create-project.sh my-new-tool

# Manually copy and customize
cp templates/project/README.md.template my-project/README.md
# Edit placeholders: {{TOOL_NAME}}, {{DESCRIPTION}}, etc.
```

See `project/README.md` for details.

---

## Template Philosophy

### Preprocessor Pattern

Templates use **gtext as preprocessor**:

1. **Write template** - `.gtext` source with variables
2. **Generate file** - Concrete file with variables filled in
3. **Commit both** - Users don't need gtext to use the tool

**Example**:
```dockerfile
# python-cli.Dockerfile.gtext (source)
FROM python:{{python_version}}-slim
COPY . .
CMD ["{{tool_name}}"]

# Dockerfile (generated)
FROM python:3.12-slim
COPY . .
CMD ["mytool"]
```

**Benefits**:
- Templates are version-controlled
- Generated files work without gtext
- Easy to update and regenerate

### Best Practices Baked In

Templates include:
- ✅ Security best practices
- ✅ Performance optimizations
- ✅ Industry standards
- ✅ We-Birds conventions

**Example - Docker security**:
```dockerfile
# Run as non-root user
RUN useradd -m -u 1000 appuser
USER appuser

# Minimal attack surface
FROM python:3.12-slim  # Not 'latest'

# Clean up
RUN rm -rf /var/lib/apt/lists/*
```

---

## Using Templates

### For New Projects

```bash
# 1. Create project directory
mkdir my-new-tool
cd my-new-tool

# 2. Copy templates
cp ../we-birds/templates/project/README.md.template README.md
cp ../we-birds/templates/project/pyproject.toml.template pyproject.toml
cp ../we-birds/templates/project/CLAUDE.md.template CLAUDE.md

# 3. Customize placeholders
# Replace {{TOOL_NAME}}, {{DESCRIPTION}}, etc.

# 4. Copy workflows
mkdir -p .github/workflows
cp ../we-birds/templates/workflows/*.yml .github/workflows/

# 5. Generate Dockerfile (if needed)
gtext render ../we-birds/templates/docker/python-cli.Dockerfile.gtext \
  --var python_version=3.12 \
  --var tool_name=my-new-tool \
  > Dockerfile
```

### For Existing Projects

```bash
# 1. Update specific template
cp we-birds/templates/workflows/publish.yml .github/workflows/

# 2. Regenerate Dockerfile
gtext render we-birds/templates/docker/python-cli.Dockerfile.gtext \
  --var python_version=3.12 \
  --var tool_name=mytool \
  > Dockerfile

# 3. Review changes
git diff

# 4. Commit if appropriate
git add Dockerfile .github/workflows/publish.yml
git commit -m "Update from we-birds templates"
```

---

## Updating Templates

### Process

1. **Modify template** in we-birds repository
2. **Test in one project** - Verify it works
3. **Run sync script** - Apply to all projects
4. **Create PRs** - Review changes per project
5. **Merge** after review

### Sync Script

```bash
# From we-birds root
./scripts/sync-templates.sh

# What it does:
# - Scans sub-projects/
# - Updates templates
# - Regenerates gtext files
# - Reports what changed
```

---

## Template Variables

### Common Variables

Most templates use these variables:

| Variable | Example | Used In |
|----------|---------|---------|
| `{{TOOL_NAME}}` | gtext | README, pyproject.toml |
| `{{DESCRIPTION}}` | Text transformation tool | README, pyproject.toml |
| `{{PYTHON_VERSION}}` | 3.12 | Dockerfile, workflows |
| `{{AUTHOR}}` | Genropy Team | pyproject.toml |
| `{{LICENSE}}` | MIT | README, pyproject.toml |

### Template-Specific Variables

See individual template README files for specific variables.

---

## Contributing Templates

### Adding New Templates

1. **Create template file** - In appropriate subdirectory
2. **Document variables** - In template comments
3. **Test generation** - Verify it works
4. **Update this README** - Add to list
5. **Create PR** - In we-birds repository

### Template Standards

Templates must:
- ✅ Use clear variable names (`{{tool_name}}`, not `{{t}}`)
- ✅ Include comments explaining sections
- ✅ Follow We-Birds best practices
- ✅ Be tested on at least one project
- ✅ Document all variables

---

## Template Versioning

Templates are versioned with the we-birds meta-repo:

```
# At top of template
# We-Birds Template - Docker Python CLI
# Version: 1.0.0
# Last Updated: 2025-11-05
```

**Update when**:
- Security fix → Bump version, update all projects
- Best practice change → Bump version, update all projects
- Optional enhancement → Document, projects can opt-in

---

## Questions?

If you have questions about templates:
1. Check template-specific README
2. Look at existing projects using the template
3. Open discussion in we-birds repository

---

**Remember**: Templates ensure consistency. When you improve a template, all projects benefit.
