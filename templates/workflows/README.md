# GitHub Actions Workflow Templates

**Version**: 1.0.0
**Last Updated**: 2025-11-05

---

## Overview

Standardized GitHub Actions workflows for We-Birds Python projects.

---

## Available Templates

### 1. `tests.yml`

Run test suite on push and pull requests.

**Features**:
- Matrix testing (Python 3.10, 3.11, 3.12)
- Code coverage reporting
- Dependency caching for faster builds
- Runs on ubuntu-latest

**Usage**:
```bash
cp templates/workflows/tests.yml .github/workflows/
```

**Triggers**: `push`, `pull_request`

### 2. `publish.yml`

Publish package to PyPI on GitHub release.

**Features**:
- Trusted Publishing (no API tokens needed)
- Automatic version from git tag
- Build verification before publish
- Only runs on `release` event

**Usage**:
```bash
cp templates/workflows/publish.yml .github/workflows/
```

**Requires**:
- PyPI Trusted Publisher configured (see below)

**Triggers**: `release` (type: `published`)

### 3. `docs.yml`

Build and publish documentation (future).

**Status**: Placeholder for Phase 2

---

## Setup Instructions

### 1. Setting Up Tests Workflow

```bash
# 1. Copy template
mkdir -p .github/workflows
cp we-birds/templates/workflows/tests.yml .github/workflows/

# 2. Commit
git add .github/workflows/tests.yml
git commit -m "Add tests workflow"
git push

# 3. Verify
# Go to GitHub → Actions tab → Tests workflow should appear
```

### 2. Setting Up Trusted Publishing

**One-time setup per tool**:

1. **Go to PyPI**:
   - Visit https://pypi.org/manage/account/publishing/
   - Login with your PyPI account

2. **Add Trusted Publisher**:
   - Click "Add a new publisher"
   - Fill in:
     - **PyPI Project Name**: `your-tool-name` (e.g., `gtext`)
     - **Owner**: `genropy`
     - **Repository name**: `your-tool-name`
     - **Workflow name**: `publish.yml`
     - **Environment name**: (leave blank)
   - Click "Add"

3. **Copy workflow**:
   ```bash
   cp we-birds/templates/workflows/publish.yml .github/workflows/
   git add .github/workflows/publish.yml
   git commit -m "Add publish workflow with Trusted Publishing"
   git push
   ```

4. **Test with release**:
   ```bash
   # Create a release on GitHub
   gh release create v0.1.0 --title "Release 0.1.0" --notes "First release"

   # Workflow will automatically publish to PyPI
   ```

**No API tokens needed!** GitHub generates short-lived tokens via OIDC.

---

## Workflow Details

### tests.yml

```yaml
name: Tests

on:
  push:
    branches: [main]
  pull_request:

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
        cache: 'pip'

    - name: Install dependencies
      run: |
        pip install -e .[dev]

    - name: Run tests
      run: |
        pytest --cov=src --cov-report=term-missing --cov-report=xml

    - name: Upload coverage
      uses: codecov/codecov-action@v4
      if: matrix.python-version == '3.12'
```

**Key features**:
- Caches pip dependencies (faster builds)
- Uploads coverage to Codecov (only for Python 3.12)
- Tests all supported Python versions

### publish.yml

```yaml
name: Publish to PyPI

on:
  release:
    types: [published]

jobs:
  publish:
    runs-on: ubuntu-latest
    permissions:
      id-token: write  # Required for Trusted Publishing

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

**Key features**:
- No API tokens (uses OIDC Trusted Publishing)
- Automatic package building
- Only runs on releases

---

## Customization

### Adding Environment Variables

```yaml
# tests.yml
env:
  PYTHONUNBUFFERED: 1
  MY_CUSTOM_VAR: value

jobs:
  test:
    env:
      JOB_SPECIFIC_VAR: value
```

### Adding Steps

```yaml
# tests.yml
- name: Lint code
  run: |
    pip install ruff
    ruff check src/

- name: Type check
  run: |
    pip install mypy
    mypy src/
```

### Matrix Variations

```yaml
# Test with different dependency versions
strategy:
  matrix:
    python-version: ["3.10", "3.11", "3.12"]
    dependency-version: ["min", "max"]

steps:
- name: Install dependencies
  run: |
    if [ "${{ matrix.dependency-version }}" == "min" ]; then
      pip install -e .[dev] --constraint=constraints-min.txt
    else
      pip install -e .[dev]
    fi
```

---

## Secrets and Tokens

### No PyPI Token Needed

✅ Trusted Publishing eliminates the need for PyPI API tokens.

### GitHub Token

The `GITHUB_TOKEN` is **automatically provided** by GitHub Actions:

```yaml
- name: Create comment
  uses: actions/github-script@v7
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

**No setup required** - works out of the box.

---

## Troubleshooting

### Issue: Tests Fail on CI but Pass Locally

**Possible causes**:
- Missing dependencies in `requirements.txt` or `[dev]`
- Environment differences
- File paths (use pathlib, not hardcoded paths)

**Solution**:
```bash
# Test in clean environment locally
python -m venv test-env
source test-env/bin/activate
pip install -e .[dev]
pytest
```

### Issue: Publish Workflow Fails

**Error**: "OIDC token verification failed"

**Cause**: Trusted Publisher not configured on PyPI

**Solution**:
1. Go to https://pypi.org/manage/account/publishing/
2. Add publisher for your repository
3. Ensure workflow name matches exactly: `publish.yml`

### Issue: Slow Workflow Runs

**Cause**: Not using dependency caching

**Solution**:
```yaml
- uses: actions/setup-python@v5
  with:
    python-version: "3.12"
    cache: 'pip'  # Add this line
```

---

## Best Practices

### ✅ DO

- **Use caching** - Speeds up subsequent runs
- **Test on multiple Python versions** - Catch compatibility issues
- **Use Trusted Publishing** - More secure than API tokens
- **Keep workflows DRY** - Use shared templates
- **Run tests on PRs** - Catch issues before merge

### ❌ DON'T

- **Don't store secrets in code** - Use GitHub Secrets
- **Don't skip tests** - They catch bugs
- **Don't use `latest` tags** - Pin action versions
- **Don't commit API tokens** - Use Trusted Publishing

---

## Future Enhancements

### Planned for Phase 2

- **docs.yml** - Build and publish documentation
- **release-drafter.yml** - Auto-generate release notes
- **dependabot.yml** - Automated dependency updates
- **codeql.yml** - Security scanning

---

## Questions?

For workflow questions:
1. Check this README
2. Look at existing tool workflows
3. Check GitHub Actions documentation
4. Open issue in we-birds repository

---

**Remember**: Consistent workflows across all We-Birds projects ensure reliability and ease of maintenance.
