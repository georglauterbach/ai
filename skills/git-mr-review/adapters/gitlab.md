# GitLab Merge Request Operations

## CLI Fallback

When no `*gitlab*` MCP is available and `glab` is installed. Pass `--hostname <host>` when the default instance is not this host. `--repo` is `<host>/<project_path>` or `project_path` depending on glab version. GitLab names `mr_iid` as `iid`.

```bash
glab mr view <mr_iid> --repo <project_path> --comments
glab mr diff <mr_iid> --repo <project_path>
glab api "projects/<url-encoded-project_path>/merge_requests/<mr_iid>/discussions"
```

Some `glab` versions accept the MR URI in place of `<mr_iid> --repo ...`; you MAY use that when it works.

## Inline Threads

You MUST post only when the user explicitly asks.

1. Take `base_sha`, `start_sha`, `head_sha` from the latest entry of `list_merge_request_versions` (`diff_refs`). With CLI, GET `projects/.../merge_requests/<mr_iid>/versions` and use the latest `diff_refs`.
2. `create_merge_request_thread` with `position` (CLI: POST `projects/.../merge_requests/<mr_iid>/discussions` with the same fields):

```json
{
  "position_type": "text",
  "base_sha": "<diff_refs.base_sha>",
  "start_sha": "<diff_refs.start_sha>",
  "head_sha": "<diff_refs.head_sha>",
  "old_path": "<path in the old file>",
  "new_path": "<path in the new file>",
  "new_line": 42
}
```

- Added or modified line in the new file: set `new_line` (1-based, new file).
- Deleted line only: set `old_line` instead of `new_line`.
- Unknown line: `create_merge_request_note` (MR-level), or `glab mr note <mr_iid> --repo <project_path> --message "..."`. You MUST NOT guess a position.

You MUST keep each thread to one finding. Quote the behavior, not a lecture.
