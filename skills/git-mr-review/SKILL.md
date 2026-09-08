---
name: git-mr-review
description: Reviews a merge request.
disable-model-invocation: true
license: MIT
---

# Review a Merge Request

## Input

You MUST require a merge request URI. If none is given, you MUST stop and tell the user. An optional description after the URI provides context on the review's intent. You MUST read [`REFERENCE.md`](REFERENCE.md) first, but do not parse all linked files yet.

You MUST parse platform and identity from the URI. [`REFERENCE.md`](REFERENCE.md) provides information about platform adapters - read the suitable adapter file. You SHOULD load (in parallel where possible):

1. Metadata (title, description, branch name)
2. File diffs
3. Discussions and notes

## Review

If a review intent is provided by the user, you MUST judge the diff against this intent. You MUST look for the following (and in this order from most important to least important):

1. **Security**: injection, secrets, unsafe shell, authz gaps, etc.
2. **Bugs**: broken edge cases, regressions
3. **Tests**: Missing tests for new behavior
4. **Compliance**: Public registries or tooling that org rules forbid
5. **Ponytail**: unrequested deps, YAGNI, wrong-layer fixes
6. **Style**: Style violations

You MUST skip the following, unless asked:

1. Style that already matches the project
2. Drive-by refactors and extra abstractions
3. Issues already raised in an unresolved discussion

You SHOULD skip the following, unless you deem the information useful for this specific request:

1. Pipeline status
2. Approval status

## Report

You MUST use the template given below. Verdict MUST be either `APPROVE` (no blocking findings), `COMMENT` (suggestions only, or the review is incomplete (empty diff, failing fetch)), or `REQUEST CHANGES` (at least one blocking finding). After the front matter, a list of findings gives details. You MUST sort findings by highest severity first and omit empty sections. Severity MUST be either `BLOCKING`, `SUGGESTION`, or `NOTE`.

```md
# Merge Request Review

**Title**: <title>
**Link**: [<id>](<uri>)
**Verdict**: <VERDICT>
**Why**: <EXPLANATION OF VERDICT>

<ID>. **<SEVERITY>** (<LOCATION as `path:line` or "UNKNOWN">): <EXPLANATION>
```

If the user asked to post feedback, you MUST NOT post items with severity `NOTE`. Post `BLOCKING` or `SUGGESTION` notes as inline comments when a line is known, otherwise one request-level note. You MUST NOT approve or merge as part of posting unless the user asks for it.
