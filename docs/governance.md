# We-Birds Governance

**Version**: 1.0.0
**Last Updated**: 2025-11-05
**Status**: 🔴 DA REVISIONARE

---

## Overview

This document describes how decisions are made in the We-Birds ecosystem.

---

## Core Team

The **Softwell team** maintains the We-Birds ecosystem:

**Responsibilities**:
- Approve new tools
- Coordinate releases
- Maintain standards
- Review major changes
- Update templates

**Decision authority**:
- Architecture decisions
- Ecosystem standards
- Tool inclusion/exclusion
- Breaking changes

---

## Decision Process

### 1. Proposals

Anyone can propose:
- New tools
- Major features
- Breaking changes
- Standard updates

**How to propose**:
1. Open issue in `softwell/we-birds` repository
2. Use template (if applicable)
3. Explain problem and solution
4. Include examples and use cases

**What makes a good proposal**:
- Clear problem statement
- Real-world use cases
- Consideration of alternatives
- Implementation sketch

### 2. Discussion

**Public discussion** (for tool-specific issues):
- Open issue in tool repository (e.g., `genropy/gtext`)
- Community can comment
- Maintainers guide discussion

**Private discussion** (for cross-project coordination):
- Discussion in `softwell/we-birds` issues
- Core team members participate
- Decisions documented publicly

**Duration**:
- Minor changes: 1-3 days
- Major changes: 1-2 weeks
- Breaking changes: 2-4 weeks

### 3. Decision

**Decision makers**:
- **Core team** - For ecosystem-wide decisions
- **Tool maintainers** - For tool-specific decisions

**Decision types**:

| Decision Type | Authority | Examples |
|---------------|-----------|----------|
| Tool addition | Core team | Adding new tool to We-Birds |
| Major feature | Tool maintainer + core team | Adding GitHub Actions marketplace support |
| Minor feature | Tool maintainer | Adding new gtext extension |
| Bug fix | Tool maintainer | Fixing parsing error |
| Standard update | Core team | Updating template structure |

### 4. Implementation

After approval:
1. Create implementation issue/PR
2. Follow coding standards
3. Add tests
4. Update documentation
5. Get code review
6. Merge

### 5. Communication

Decisions are communicated via:
- **GitHub issue resolution** - Close with summary
- **Changelog updates** - Document user-facing changes
- **Release notes** - Highlight important changes

---

## Tool Lifecycle

### Adding a New Tool

**Process**:
1. **Proposal** - Open issue in we-birds with:
   - Tool purpose
   - Why it belongs in We-Birds
   - Initial design
   - Maintenance plan

2. **Evaluation** - Core team considers:
   - Is it general-purpose?
   - Does it fit We-Birds philosophy?
   - Can we maintain it long-term?
   - Does it duplicate existing tools?

3. **Approval** - If approved:
   - Create repository in `genropy` org
   - Apply templates
   - Add to ORGANIZATION.md
   - Announce in discussions

**Rejection criteria**:
- Too specific (not general-purpose)
- Overlaps with existing tool
- Maintenance concerns
- Doesn't align with philosophy

### Deprecating a Tool

**Process**:
1. **Announce intention** - Open issue explaining why
2. **Gather feedback** - Community discussion
3. **Deprecation period** - Minimum 6 months warning
4. **Archive repository** - Mark as archived
5. **Remove from ORGANIZATION.md** - Keep in "Archived" section

**Reasons for deprecation**:
- Maintenance burden too high
- Better alternatives exist
- No longer relevant
- Security concerns unfixable

---

## Release Management

### Version Numbering

All tools follow **Semantic Versioning**:
- **0.x.y** - Development phase (breaking changes allowed)
- **1.0.0** - First stable release
- **x.y.z** - MAJOR.MINOR.PATCH

### Release Process

**For tool maintainers**:

1. **Prepare release**:
   - Update version in `pyproject.toml`
   - Update `CHANGELOG.md`
   - Run full test suite
   - Build documentation

2. **Create GitHub release**:
   - Tag: `vX.Y.Z`
   - Title: `Release X.Y.Z`
   - Description: Changelog excerpt

3. **Publish to PyPI**:
   - Automated via GitHub Actions
   - Uses Trusted Publishing (no manual tokens)

4. **Announce**:
   - GitHub Discussions
   - Update we-birds meta-repo if needed

### Coordinated Releases

When multiple tools need coordinated releases:

1. **Create tracking issue** - In we-birds repository
2. **Plan order** - Dependencies first
3. **Test together** - Clone to sub-projects/, run integration tests
4. **Release sequentially** - Wait for PyPI propagation between releases
5. **Update documentation** - Cross-reference changes

---

## Standards and Templates

### Updating Standards

**Process**:
1. Propose change in we-birds issue
2. Discuss impact on existing tools
3. Update templates if needed
4. Document migration path
5. Roll out to all tools

**Example**:
```
Proposal: Switch from setup.py to pyproject.toml

Impact: All tools need migration
Migration: Create migration guide
Rollout: Update one tool first, then others
```

### Template Updates

Templates in `we-birds/templates/` are shared across all tools.

**Update process**:
1. Modify template in meta-repo
2. Test in one tool
3. Run `scripts/sync-templates.sh`
4. Create PRs for all affected tools
5. Merge after review

---

## Contribution Guidelines

### For Tool Contributors

**Bug fixes**:
1. Open issue (if not exists)
2. Fork repository
3. Create feature branch
4. Add tests
5. Submit PR
6. Respond to review

**New features**:
1. Open issue first - discuss before implementing
2. Get approval from maintainer
3. Follow process above

### For Core Team

**Code review checklist**:
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] Follows coding standards
- [ ] No breaking changes (or properly versioned)
- [ ] Changelog updated

**Merge criteria**:
- All tests pass
- At least one approval
- No unresolved comments
- Follows standards

---

## Conflict Resolution

### Technical Disagreements

**Process**:
1. Discuss in GitHub issue
2. Present technical arguments
3. Consider user impact
4. Core team makes final call

**Principles**:
- Technical merit over seniority
- User needs over developer preferences
- Data over opinions
- Simplicity over cleverness

### Policy Disagreements

**Process**:
1. Discuss in we-birds issue
2. Consider ecosystem impact
3. Review against philosophy
4. Core team consensus

**If no consensus**:
- Table decision
- Gather more data
- Revisit in 1-2 weeks

---

## Communication Channels

### GitHub Issues

**Tool repositories** (genropy/*):
- Bug reports
- Feature requests
- Questions
- Public discussions

**Meta-repository** (softwell/we-birds):
- Cross-project coordination
- Proposals for new tools
- Standard updates
- Ecosystem-wide discussions

### GitHub Discussions

**For**:
- Ideas and brainstorming
- Community feedback
- Best practices
- General questions

**Not for**:
- Bug reports (use issues)
- Urgent matters (use issues)

### Private Communication

Core team uses:
- GitHub issues in private meta-repo
- Email for sensitive matters

---

## Transparency

### Public Information

Available to everyone:
- All tool source code
- All tool issues and PRs
- Release notes
- Documentation
- Test results

### Private Information

Internal to core team:
- Strategic planning
- Unreleased roadmaps
- Security issues (until patched)

**Principle**: Default to public unless there's a good reason not to.

---

## Evolution of Governance

This governance model will evolve as the ecosystem grows:

**Review annually**:
- Is decision process working?
- Are there bottlenecks?
- Does it scale?

**Update as needed**:
- Document changes
- Announce to community
- Maintain transparency

---

## Questions?

If you have questions about governance:
1. Check this document
2. Search existing issues
3. Open new issue in we-birds repository

---

**Remember**: Governance exists to serve the ecosystem. It should enable work, not block it.
