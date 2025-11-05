# Contributing to We-Birds

**Thank you for your interest in contributing to We-Birds!**

This document explains how to contribute to the We-Birds ecosystem.

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
- [Repository Structure](#repository-structure)
- [Development Setup](#development-setup)
- [Contribution Workflow](#contribution-workflow)
- [Coding Standards](#coding-standards)
- [Testing](#testing)
- [Documentation](#documentation)
- [Commit Messages](#commit-messages)
- [Pull Request Process](#pull-request-process)

---

## Code of Conduct

### Our Pledge

We are committed to providing a friendly, safe, and welcoming environment for all contributors.

### Expected Behavior

- ✅ Be respectful and inclusive
- ✅ Focus on constructive feedback
- ✅ Accept criticism gracefully
- ✅ Prioritize the community's best interest

### Unacceptable Behavior

- ❌ Harassment or discriminatory language
- ❌ Personal attacks
- ❌ Trolling or inflammatory comments
- ❌ Spam or off-topic discussions

---

## How Can I Contribute?

### Reporting Bugs

**Before submitting a bug report**:
1. Check existing issues to avoid duplicates
2. Verify the bug with the latest version
3. Collect relevant information (Python version, OS, error messages)

**How to submit a good bug report**:
1. **Use a clear title** - Describe the issue concisely
2. **Provide steps to reproduce** - How can we see the bug?
3. **Expected vs actual behavior** - What should happen vs what happens?
4. **Environment details** - Python version, OS, package versions
5. **Logs and error messages** - Include full error output

**Example**:
```markdown
**Title**: gtext fails with UnicodeDecodeError on Windows

**Steps to reproduce**:
1. Install gtext 0.3.0 on Windows 11
2. Run: `gtext render template.txt`
3. Error occurs

**Expected**: File renders successfully

**Actual**: UnicodeDecodeError: 'charmap' codec can't decode byte...

**Environment**:
- OS: Windows 11
- Python: 3.12.0
- gtext: 0.3.0
```

### Suggesting Features

**Before suggesting a feature**:
1. Check if it already exists or is planned
2. Consider if it fits We-Birds philosophy (see `docs/philosophy.md`)
3. Think about maintenance and complexity

**How to suggest a feature**:
1. **Open a discussion** (not an issue) - GitHub Discussions
2. **Explain the problem** - What are you trying to solve?
3. **Describe the solution** - How would it work?
4. **Consider alternatives** - What other approaches exist?
5. **Show use cases** - Real-world examples

### Contributing Code

See [Contribution Workflow](#contribution-workflow) below.

### Improving Documentation

Documentation improvements are always welcome:
- Fix typos or unclear explanations
- Add examples
- Improve API documentation
- Update outdated information

---

## Repository Structure

### Meta-Repository (This Repo)

**`softwell/we-birds`** - Private governance repository:
- Documentation and standards
- Shared templates
- Cross-project coordination

**Do NOT** submit code implementations here. This is for governance only.

### Tool Repositories (Public)

**`genropy/*`** - Public tool repositories:
- `genropy/gtext`
- `genropy/smartswitch`
- `genropy/tryfly`

**Submit code contributions** to the appropriate tool repository.

---

## Development Setup

### Prerequisites

- Python 3.10 or higher
- Git
- GitHub account

### Local Setup

```bash
# 1. Fork the repository on GitHub
# (Click "Fork" button on the tool's repository page)

# 2. Clone your fork
git clone git@github.com:YOUR-USERNAME/TOOL-NAME.git
cd TOOL-NAME

# 3. Add upstream remote
git remote add upstream git@github.com:genropy/TOOL-NAME.git

# 4. Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# 5. Install in development mode
pip install -e .[dev]

# 6. Verify installation
pytest
```

### Keeping Your Fork Updated

```bash
# Fetch upstream changes
git fetch upstream

# Merge into your local main
git checkout main
git merge upstream/main

# Push to your fork
git push origin main
```

---

## Contribution Workflow

### 1. Create a Branch

```bash
# Start from main
git checkout main
git pull upstream main

# Create feature branch
git checkout -b feature/your-feature-name
```

**Branch naming**:
- `feature/add-something` - New feature
- `fix/issue-123` - Bug fix
- `docs/improve-readme` - Documentation
- `refactor/cleanup-parser` - Refactoring

### 2. Make Changes

- Write clean, readable code
- Follow coding standards (see below)
- Add tests for new functionality
- Update documentation

### 3. Test Your Changes

```bash
# Run tests
pytest

# Run specific test
pytest tests/test_specific.py::test_function

# Check coverage
pytest --cov=src --cov-report=term-missing

# Ensure coverage is ≥80%
```

### 4. Commit Your Changes

```bash
# Stage changes
git add .

# Commit with descriptive message
git commit -m "Add feature X to improve Y"

# See "Commit Messages" section for guidelines
```

### 5. Push to Your Fork

```bash
git push origin feature/your-feature-name
```

### 6. Create Pull Request

1. Go to your fork on GitHub
2. Click "Compare & pull request"
3. Fill in PR template (see below)
4. Submit PR

---

## Coding Standards

### Python Style

**Follow PEP 8** with these guidelines:

- Use **snake_case** for functions and variables
- Use **PascalCase** for classes
- Maximum line length: **100 characters**
- Use **type hints** for function signatures

### Code Quality Tools

We recommend:

```bash
# Install tools
pip install ruff mypy

# Run linter
ruff check src/

# Run type checker
mypy src/
```

### Example

```python
from typing import Optional

class TextProcessor:
    """Process text with extensions."""

    def __init__(self, encoding: str = "utf-8") -> None:
        self.encoding = encoding

    def process(self, text: str, extension: Optional[Extension] = None) -> str:
        """
        Process text using the given extension.

        Args:
            text: Input text to process
            extension: Optional extension to apply

        Returns:
            Processed text

        Example:
            >>> processor = TextProcessor()
            >>> processor.process("hello")
            'hello'
        """
        if extension:
            return extension.apply(text)
        return text
```

---

## Testing

### Test Requirements

- **Minimum 80% coverage** for new code
- **All tests must pass** before PR is merged
- **Test edge cases** - Empty inputs, invalid data, etc.

### Test Structure

```
tests/
├── __init__.py
├── unit/              # Fast, isolated tests
│   ├── test_parser.py
│   └── test_utils.py
├── integration/       # Real workflow tests
│   └── test_workflows.py
└── fixtures/          # Test data
    └── sample.txt
```

### Writing Tests

```python
import pytest
from mypackage import MyClass

def test_basic_functionality():
    """Test that basic functionality works"""
    obj = MyClass()
    result = obj.process("input")
    assert result == "expected"

def test_error_handling():
    """Test that errors are handled correctly"""
    obj = MyClass()
    with pytest.raises(ValueError, match="Invalid input"):
        obj.process(None)

@pytest.mark.parametrize("input,expected", [
    ("hello", "HELLO"),
    ("world", "WORLD"),
])
def test_multiple_inputs(input, expected):
    """Test with multiple inputs"""
    obj = MyClass()
    assert obj.process(input) == expected
```

---

## Documentation

### Code Documentation

**Docstrings required** for:
- All public classes
- All public methods/functions
- All modules

**Format**: Google-style docstrings

```python
def function(arg1: str, arg2: int) -> bool:
    """
    Brief description of what the function does.

    More detailed explanation if needed. Can span multiple lines.

    Args:
        arg1: Description of arg1
        arg2: Description of arg2

    Returns:
        Description of return value

    Raises:
        ValueError: When invalid input is provided

    Example:
        >>> function("test", 42)
        True
    """
```

### README Updates

When adding features, update the README:
- Add to feature list
- Include usage examples
- Update API documentation

---

## Commit Messages

### Format

```
<type>: <subject>

<body>

<footer>
```

### Types

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

### Examples

**Good commits**:
```
feat: Add support for custom delimiters in gtext

Allow users to specify custom delimiters for template variables
instead of always using {{...}}. Useful for templates that need
to include literal {{ characters.

Closes #42
```

```
fix: Handle Unicode errors on Windows

Use UTF-8 encoding explicitly when reading files to avoid
UnicodeDecodeError on Windows systems.

Fixes #123
```

**Bad commits**:
```
Update stuff          # Too vague
Fixed bug            # What bug?
WIP                  # Don't commit WIP
```

### Rules

- ✅ Write in present tense ("Add feature" not "Added feature")
- ✅ Keep subject line under 72 characters
- ✅ Reference issues/PRs when relevant
- ✅ Explain **why**, not just **what**
- ❌ Don't include Claude/AI attribution (per project policy)

---

## Pull Request Process

### Before Submitting

- [ ] Tests pass locally
- [ ] Code follows style guidelines
- [ ] Documentation is updated
- [ ] Commit messages are clear
- [ ] Branch is up to date with main

### PR Template

```markdown
## Description
Brief description of what this PR does.

## Related Issue
Closes #123

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Refactoring

## Testing
How was this tested?
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] Manual testing performed

## Checklist
- [ ] Code follows project style
- [ ] Tests pass
- [ ] Documentation updated
- [ ] Changelog updated (if applicable)
```

### Review Process

1. **Automated checks** - CI must pass
2. **Code review** - Maintainer reviews code
3. **Feedback** - Address review comments
4. **Approval** - At least one approval required
5. **Merge** - Maintainer merges PR

### After Merge

- Delete your feature branch (GitHub offers this)
- Update your fork: `git pull upstream main`

---

## Questions?

**For tool-specific questions**:
- Open issue in the tool's repository
- Check tool's documentation

**For ecosystem questions**:
- Open discussion in `softwell/we-birds`
- Check `docs/` directory

---

## License

By contributing, you agree that your contributions will be licensed under the same MIT License that covers the project.

---

**Thank you for contributing to We-Birds!** 🐦
