# GitHub skill

Work with the user's GitHub account through the REST API (api.github.com), authenticated via the stored `custom.github` connector (dynamic credential surrogates — no raw token is ever handled).

## CLI

`~/workspace/skills/github/bin/github-api` — generic REST proxy:

```
github-api METHOD /path [options]
```

- `-d/--data JSON` — JSON body for POST/PATCH/PUT
- `-q/--query PARAM` — extra query param, e.g. `-q state=open -q per_page=100` (repeatable)
- `--paginate` — follow `Link: rel="next"` and merge list responses into one JSON array
- `--raw` — print the raw body (useful for non-JSON endpoints)

The path is relative to `https://api.github.com`. Send `Accept: application/vnd.github+json` automatically.

## Common recipes

- Who am I: `github-api GET /user`
- List my repos: `github-api GET /user/repos --paginate -q type=owner -q sort=updated`
- Repo details: `github-api GET /repos/OWNER/REPO`
- Branches: `github-api GET /repos/OWNER/REPO/branches --paginate`
- List PRs: `github-api GET /repos/OWNER/REPO/pulls --paginate -q state=open`
- PR detail + files: `github-api GET /repos/OWNER/REPO/pulls/N`, `/repos/OWNER/REPO/pulls/N/files`
- Create PR: `github-api POST /repos/OWNER/REPO/pulls -d '{"title":"...","head":"branch","base":"main","body":"..."}'`
- Issues: `github-api GET /repos/OWNER/REPO/issues --paginate -q state=open`
- Create issue / comment: `github-api POST /repos/OWNER/REPO/issues -d '{...}'`, `github-api POST /repos/OWNER/REPO/issues/N/comments -d '{"body":"..."}'`
- Get file contents: `github-api GET /repos/OWNER/REPO/contents/PATH -q ref=main` (base64 in `content`)
- Create/update file: `github-api PUT /repos/OWNER/REPO/contents/PATH -d '{"message":"...","content":"<base64>","sha":"...","branch":"..."}'`
- Search code: `github-api GET /search/code -q 'q=query+repo:OWNER/REPO' --paginate`
- Workflows/runs: `github-api GET /repos/OWNER/REPO/actions/runs --paginate`

## Notes

- The token's scopes decide what's possible; a 403/404 on a repo usually means the token lacks access (fine-grained tokens are per-repo).
- Rate limit: check `github-api GET /rate_limit` if calls start failing.
- Never paste the token value anywhere; auth is injected at request time by the runtime.
