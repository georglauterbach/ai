## Reference

### General Instructions

- you MUST first report findings in chat before doing anything else
- you MUST NOT post, merge, implement, restart, approve, deny, comment or push anything unless the user explicitly asks

### `git` Platform Adapters

|Platform|URI shape|Identity|Adapter File|MCP Name Matches|
|:-|:-|:-|:-|:-|
|GitLab|`https://<host>/<project_path>/-/merge_requests/<mr_iid>`|`project_path`, `mr_iid`|[`gitlab.md`](adapters/gitlab.md)|`*gitlab*`|
|GitHub|`https://<host>/<owner>/<repo>/pull/<number>`|`owner/repo`, `number`|[`github.md`](adapters/github.md)|`*github*`|
|Gitea|`https://<host>/<owner>/<repo>/pulls/<number>`|`owner/repo`, `number`|[`gitea.md`](adapters/gitea.md)|`*gitea*`|

`project_path` is everything between host and `/-/merge_requests/` (nested groups kept). `mr_iid` is the merge request IID, not the global id.

When you encounter an unknown platform, you MUST tell the user and stop. Match a ready MCP server using the table's `MCP Name Matches` column. You SHOULD prefer MCP; you MUST use the CLI only when MCP is absent:

1. MCP Server matching `MCP Name Matches`
2. Else: Platform CLI from the adapter
3. Else: you MUST stop and ask whether to proceed with basic tools (WebFetch, `curl`); you MUST NOT scrape unprompted.

If the forge denies access, you MUST stop and tell the user. You MUST NOT work around allowlists.

### MCP

You MUST discover MCP tool schemas with `GetDynamicTools` before the first `CallDynamicTool` call.

### Data Load Sequence

After discovering schemas, you SHOULD load in parallel where the tools allow, following the load list in SKILL.md. Paginate until a short page. You MUST fetch full hunks; for GitLab MCP, pass `compact: false` (the default truncates).

For surrounding code not in the hunk, fetch the file at the head ref. You SHOULD prefer the local workspace when that remote and branch are already checked out.
