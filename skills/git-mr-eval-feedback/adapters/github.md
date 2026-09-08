# GitHub Pull Request Operations

## CLI Fallback

When no `*github*` MCP is available and `gh` is installed. `gh` accepts the PR URI; otherwise pass `--repo <owner>/<repo>`. For GitHub Enterprise, `GH_HOST=<host>` or `gh --hostname <host>`.

```bash
gh pr view <uri> --json title,body,baseRefName,headRefName,headRefOid,isDraft,url,comments
gh pr diff <uri>
gh api repos/<owner>/<repo>/pulls/<number>/comments
```

## Inline Comments

You MUST post only when the user explicitly asks.

- Known line: create a pending or single review comment on `path` + `line` (1-based, new file) at `commit_id` = head SHA. Deleted-line-only: use the old side / `original_line` equivalent. You MUST NOT guess a position.
- Unknown line: `gh pr comment <uri> --body "..."` (request-level).

CLI inline example:

```bash
gh api repos/<owner>/<repo>/pulls/<number>/comments \
  -f body="..." -f path="<file>" -F line=42 -f side=RIGHT -f commit_id="<head_sha>"
```

You MUST keep each comment to one finding. Quote the behavior, not a lecture.
