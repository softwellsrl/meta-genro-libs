# Docker Templates

**Version**: 1.0.0
**Last Updated**: 2025-11-05

---

## Overview

Standardized Dockerfile templates for We-Birds Python projects.

---

## Available Templates

### 1. `python-base.Dockerfile.gtext`

Base Python image setup with security best practices.

**Use for**: General Python applications

**Variables**:
- `{{python_version}}` - Python version (e.g., "3.12")
- `{{install_system_deps}}` - Boolean, install apt packages (default: false)
- `{{system_deps}}` - Space-separated apt packages (e.g., "postgresql-client git")

**Example**:
```bash
gtext render python-base.Dockerfile.gtext \
  --var python_version=3.12 \
  --var install_system_deps=true \
  --var system_deps='"postgresql-client"' \
  > Dockerfile
```

### 2. `python-cli.Dockerfile.gtext`

Python CLI tool with proper entrypoint.

**Use for**: Command-line tools (gtext, smartswitch, tryfly)

**Variables**:
- `{{python_version}}` - Python version (e.g., "3.12")
- `{{tool_name}}` - CLI command name (e.g., "gtext")
- `{{install_dev}}` - Boolean, install dev dependencies (default: false)

**Example**:
```bash
gtext render python-cli.Dockerfile.gtext \
  --var python_version=3.12 \
  --var tool_name=gtext \
  > Dockerfile
```

---

## Security Best Practices

All templates include:

### ✅ Non-root User

```dockerfile
# Create and use non-root user
RUN useradd -m -u 1000 appuser
USER appuser
```

### ✅ Minimal Base Image

```dockerfile
# Use slim variant, not full or latest
FROM python:3.12-slim
```

### ✅ Clean Up

```dockerfile
# Remove apt cache
RUN apt-get update && apt-get install -y ... && \
    rm -rf /var/lib/apt/lists/*
```

### ✅ Dependency Caching

```dockerfile
# Copy requirements first for Docker layer caching
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Then copy code
COPY . .
```

---

## Performance Optimizations

### Multi-stage Builds (Future)

For tools that need build-time dependencies:

```dockerfile
# Stage 1: Build
FROM python:3.12 AS builder
RUN pip install build
COPY . .
RUN python -m build

# Stage 2: Runtime
FROM python:3.12-slim
COPY --from=builder /dist/*.whl .
RUN pip install *.whl
```

### Layer Caching

```dockerfile
# Changes infrequently → Earlier layers
FROM python:3.12-slim
RUN apt-get update && ...

# Changes frequently → Later layers
COPY . .
```

---

## Usage Patterns

### Development

```dockerfile
# Dockerfile.dev
FROM python:3.12-slim

WORKDIR /app

# Install dev dependencies
COPY requirements.txt requirements-dev.txt ./
RUN pip install --no-cache-dir -r requirements-dev.txt

# Mount code as volume
CMD ["pytest", "--watch"]
```

### Production

```dockerfile
# Dockerfile (generated from template)
FROM python:3.12-slim

# Minimal runtime dependencies only
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .
CMD ["mytool"]
```

---

## Testing Dockerfiles

### Build Test

```bash
docker build -t my-tool:test .
```

### Size Check

```bash
docker images my-tool:test --format "{{.Size}}"
```

**Target sizes**:
- Base Python image: ~150MB
- With dependencies: ~200-300MB
- If larger: Review dependencies

### Security Scan

```bash
docker scan my-tool:test
```

### Run Test

```bash
docker run --rm my-tool:test --version
docker run --rm my-tool:test --help
```

---

## Updating Templates

### Process

1. **Modify `.gtext` file** in we-birds repository
2. **Update version** in template comments
3. **Test** in one project
4. **Regenerate** Dockerfiles in all projects
5. **Review and commit** changes

### Example Update

```bash
# 1. Edit template
vim we-birds/templates/docker/python-cli.Dockerfile.gtext

# 2. Test in one project
cd sub-projects/gtext
gtext render ../../we-birds/templates/docker/python-cli.Dockerfile.gtext \
  --var python_version=3.12 \
  --var tool_name=gtext \
  > Dockerfile

# 3. Build and test
docker build -t gtext:test .
docker run --rm gtext:test --version

# 4. If OK, sync to all projects
cd ../../we-birds
./scripts/sync-docker-templates.sh
```

---

## Common Issues

### Issue: Large Image Size

**Problem**: Docker image is >500MB

**Solutions**:
- Use `-slim` variant, not full Python image
- Use `--no-cache-dir` with pip
- Clean up apt cache
- Use multi-stage builds if needed
- Review dependencies (remove unnecessary ones)

### Issue: Permission Errors

**Problem**: File permission errors when running container

**Solution**: Ensure USER directive is after file operations:
```dockerfile
# ✅ Correct order
COPY . .
RUN chown -R appuser:appuser /app
USER appuser

# ❌ Wrong order
USER appuser
COPY . .  # Permission denied
```

### Issue: Slow Builds

**Problem**: Docker builds take too long

**Solutions**:
- Copy `requirements.txt` before code (caching)
- Use `.dockerignore` to exclude unnecessary files
- Use layer caching effectively
- Consider BuildKit for parallel builds

---

## .dockerignore

Include in all projects:

```
# .dockerignore
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
dist/
*.egg-info/
.git
.github
.vscode
.idea
temp/
*.md
!README.md
tests/
docs/
```

---

## Questions?

For Dockerfile template questions:
1. Check this README
2. Look at existing project Dockerfiles
3. Open issue in we-birds repository

---

**Remember**: Docker templates ensure consistent, secure, performant images across all We-Birds projects.
