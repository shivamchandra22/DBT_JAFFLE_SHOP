## PR Description
<!-- Describe the changes in this PR -->

### Type of Change
- [ ] New feature (non-breaking change that adds functionality)
- [ ] Refactoring (non-breaking change that improves code structure)
- [ ] Bug fix (non-breaking change that fixes an issue)
- [ ] Breaking change
- [ ] Update to documentation

### Models Changed
<!-- List affected models -->

### Related Issues
<!-- Link to related issues, e.g., Closes #123 -->

---

## Definition of Done Checklist

### Code Quality
- [ ] All models follow naming conventions (stg_*, int_*, fct_*, dim_*)
- [ ] Staging models contain only minimal transforms (no joins or business logic)
- [ ] SQL follows house style guide (see CONVENTIONS.md)
- [ ] No credentials or PII in code/configs

### Documentation
- [ ] All models have descriptions in schema.yml
- [ ] All columns have descriptions in schema.yml
- [ ] Tests are documented where non-standard

### Testing
- [ ] `dbt deps` runs successfully
- [ ] `dbt parse` runs without errors
- [ ] `dbt compile -s <new_models>` compiles successfully
- [ ] `dbt test -s <new_models>` passes all tests
- [ ] No orphaned columns or hallucinated fields

### Validation Evidence (REQUIRED)
<!-- Paste output as code blocks -->

**dbt compile output:**
```
<paste output here>
```

**dbt test output:**
```
<paste output here>
```

### Backward Compatibility
- [ ] No breaking changes to existing models
- [ ] Downstream dependencies checked
- [ ] If refactoring: proof of semantic equivalence provided

### Security
- [ ] No secrets committed (check .gitignore for profiles.yml)
- [ ] No PII in example data
- [ ] Redaction rules applied where necessary

---

## Reviewer Notes
<!-- Any additional context for reviewers -->

---

## References
- [CONVENTIONS.md](../CONVENTIONS.md) — House style guide
- [.github/copilot_context.md](./copilot_context.md) — Developer context
