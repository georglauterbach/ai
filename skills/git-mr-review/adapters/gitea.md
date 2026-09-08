# Gitea Pull Request Operations

## CLI Fallback

When no `*gitea*` MCP is available and the Gitea CLI `tea` is installed. Pass `--repo <owner>/<repo>`. Pass `--login <name>` for the login that matches this host (`tea logins`). You MUST NOT infer the repo from `$PWD`.

```bash
tea pr <number> --repo <owner>/<repo> --login <login> --comments --output json \
  --fields index,title,body,state,url,base,head,base-commit,diff,patch,comments
```

`diff` / `patch` on that command are the PR diff. For files and comments (no first-class `tea` subcommand), use the Gitea API:

```
GET /api/v1/repos/<owner>/<repo>/pulls/<number>/files
GET /api/v1/repos/<owner>/<repo>/pulls/<number>/reviews
GET /api/v1/repos/<owner>/<repo>/pulls/<number>/reviews/<id>/comments
GET /api/v1/repos/<owner>/<repo>/issues/<number>/comments
```

If `tea` exposes an `api` subcommand, you SHOULD use it for those paths. Otherwise you MUST stop and ask before `curl`.

## Inline Comments

You MUST post only when the user explicitly asks. You MUST NOT call `tea pr approve` / `tea pr reject` as part of posting.

- Known line: POST `/api/v1/repos/<owner>/<repo>/pulls/<number>/reviews` with `event` `COMMENT` (not approve/reject):

```json
{
  "event": "COMMENT",
  "commit_id": "<head_sha>",
  "comments": [
    {
      "path": "<file>",
      "body": "...",
      "new_position": 42
    }
  ]
}
```

- Added or modified line in the new file: `new_position` (1-based).
- Deleted line only: `old_position` instead of `new_position`.
- Unknown line: `tea comment <number> --repo <owner>/<repo> --login <login>` with the body as the argument if `tea` accepts it; otherwise POST `/api/v1/repos/<owner>/<repo>/issues/<number>/comments` with `{"body":"..."}`. You MUST NOT guess a position.

You MUST keep each comment to one finding. Quote the behavior, not a lecture.
