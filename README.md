# github-core-clone-03

A standalone GitHub-compatible forge in a single Python file (stdlib only,
Python 3.13). One process answers the whole surface the contest scores:

- **git smart HTTP** — `git-upload-pack` v2 (ls-refs, fetch, shallow and
  deepen, thin-pack-free plain packs) and v0 advertisements, plus
  `git-receive-pack` v0 with fast-forward-only ref updates and sideband
  `unpack ok` / per-ref `ok` / `ng` reporting.
- **REST v3** — repositories, branches, commits, contents (create/update/
  delete with blob-sha checks), labels, issues, comments, pull requests,
  files, squash merges with three-way conflict detection, pagination via
  `Link: rel="next"`, and a non-empty `X-GitHub-Request-Id` on every reply.
- **GraphQL v4** — viewer/repository/issue/pullRequest/node, `__type`
  introspection, batched operations, and HTTP-200 errors that name the
  invalid field.
- **MCP** over streamable HTTP — `server/discover` for the 2026-07-28 era
  with a legacy `initialize` fallback, and eight tools (`create_repo`,
  `get_repo`, `list_repos`, `create_issue`, `get_issue`, `list_issues`,
  `create_pull_request`, `merge_pull_request`) on the same store as REST.

Determinism: every timestamp comes from `MAKEFUN_TRIAL_NOW` (or a fixed
base) plus a monotonic counter, request ids are counter-based, and the
viewer login is derived from the contest's fixed scenario seed with the
same FNV-1a / mix64 / xorshift64* stream the kit's wire contract uses —
no wall clock, no randomness in any response.

## Run

```sh
python3 main.py   # needs MAKEFUN_ENV_URL (and MAKEFUN_SHOP_TOKEN)
```

Lifecycle: bind an ephemeral port, `POST $MAKEFUN_ENV_URL/register`
with `{"port": n}`, poll `GET $MAKEFUN_ENV_URL/done` until `{"done": true}`,
exit 0.
