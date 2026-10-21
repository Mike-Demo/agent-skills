# Security audit triage — 2026-10-21

Parent: https://github.com/Mike-Demo/agent-skills/issues/1

Issue creation is unavailable in this session. The three issue titles and bodies
below are ready to file in this repository. This document is the triage deliverable,
not a remediation: no helper code was changed or executed, and no endpoint was
probed. Evidence links refer to the inspected source revision.

## Prioritized backlog

| Order | Issue title | Risk and fix rationale |
| --- | --- | --- |
| 1 | [security][medium] Keep Clera and Beautiful.ai OAuth tokens out of curl argv | A lower-trust local process observer can capture credentials during ordinary authenticated calls on hosts permitting command-line inspection. No upstream compromise is needed; Clera refresh tokens can extend the impact. Both helpers share the same root cause and belong in one issue. The fix is a bounded transport change. |
| 2 | [security][medium] Pin Aikido MCP execution and restrict its environment | Every valid invocation runs mutable upstream code with the owner's full environment. Exploitation requires compromise of a resolved package/dependency or distribution path, rather than merely a local observer. Pinning and environment filtering are small changes, but require a reviewed version and runtime compatibility checks. Treat these as one execution-boundary root-cause group. |

Both confirmed findings retain the parent's medium severity. Reachability decides
the ordering before fix size; this does not assert that any package is compromised
or that credentials have already been stolen. Shared hosts/runners make issue 1
especially urgent. On a host without lower-trust process observers, its exposure
is reduced; the Aikido supply-chain risk still applies.

The needs-validation issue is separate and has no severity. The rejected
`github-api-absolute-url-host-binding` candidate is excluded from the backlog;
there is no new evidence to reopen it.

---

## Issue 1

### Title

[security][medium] Keep Clera and Beautiful.ai OAuth tokens out of curl argv

### Body

Parent audit: https://github.com/Mike-Demo/agent-skills/issues/1

**Priority:** 1 of 2 confirmed remediation issues.
**Severity:** medium.
**Fingerprint:** `curl-argv-oauth-token-exposure`.
**Grouping:** Clera and Beautiful.ai share one root cause: OAuth secrets placed
in subprocess arguments.

#### Evidence

- [`clera/bin/clera-mcp:26–32`](https://github.com/Mike-Demo/agent-skills/blob/e2e006d4e99c97eb0321366e30014d32a797fe4c/clera/bin/clera-mcp#L26-L32):
  `refresh()` constructs a curl argument containing
  `'refresh_token=' + t['refresh_token']`.
- [`clera/bin/clera-mcp:58–64`](https://github.com/Mike-Demo/agent-skills/blob/e2e006d4e99c97eb0321366e30014d32a797fe4c/clera/bin/clera-mcp#L58-L64):
  `mcp()` puts `'Authorization: Bearer ' + tok` in curl argv.
- [`beautiful-ai/bin/beautiful-mcp:65–72`](https://github.com/Mike-Demo/agent-skills/blob/e2e006d4e99c97eb0321366e30014d32a797fe4c/beautiful-ai/bin/beautiful-mcp#L65-L72):
  `post()` likewise puts the access token in a curl header argument.
- Calls have 30–60 second timeouts; secrets remain in argv for the lifetime of
  the curl process, not necessarily the entire timeout.

#### Risk and reachability

On a default Linux `/proc` mount, process command lines can be read through
`/proc/<pid>/cmdline` by other local users. A lower-trust local user or process
with that visibility can observe and replay access tokens and, for Clera refresh
calls, refresh tokens. This is not a remote-only exploit. Process visibility
restrictions such as `hidepid` or isolation can limit reachability.

#### Root cause

The helpers build curl subprocess argument lists containing access/refresh
tokens. Private token-file permissions do not protect copies exposed in argv.

#### Smallest fix

Keep secrets out of argv in all three paths above. Feed sensitive headers and
body through a curl configuration on stdin (`curl -K -`), with correct escaping,
or use Python's existing standard-library `urllib` transport with headers/body
set in-process, as
[`indeed/bin/indeed-mcp:60–65`](https://github.com/Mike-Demo/agent-skills/blob/e2e006d4e99c97eb0321366e30014d32a797fe4c/indeed/bin/indeed-mcp#L60-L65)
already does. Preserve authentication,
refresh, response parsing, error behavior, and timeouts. Do not substitute
another secret-bearing command-line argument or a world-readable temporary file.

#### Acceptance criteria

- [ ] Clera refresh and MCP calls and Beautiful.ai POST calls contain no OAuth
  access/refresh token in subprocess argv.
- [ ] Synthetic-token checks cover all three paths without live credentials;
  no secrets appear in logs or temporary files.
- [ ] Existing request encoding, headers, refresh behavior, response handling,
  and timeouts are preserved.

---

## Issue 2

### Title

[security][medium] Pin Aikido MCP execution and restrict its environment

### Body

Parent audit: https://github.com/Mike-Demo/agent-skills/issues/1

**Priority:** 2 of 2 confirmed remediation issues.
**Severity:** medium.
**Fingerprint:** `aikido-mcp-unpinned-npx-full-env`.
**Grouping:** Mutable package execution and wholesale environment inheritance
form the supplied execution-boundary finding and should be remediated together.

#### Evidence

- [`aikido/bin/aikido-mcp:24–39`](https://github.com/Mike-Demo/agent-skills/blob/e2e006d4e99c97eb0321366e30014d32a797fe4c/aikido/bin/aikido-mcp#L24-L39):
  both `SERVER_CMD` (line 24) and the actual spawn (line 39) use
  `npx -y @aikidosec/mcp`, without an
  exact version, repository lockfile, or integrity check.
- [`aikido/bin/aikido-mcp:36–45`](https://github.com/Mike-Demo/agent-skills/blob/e2e006d4e99c97eb0321366e30014d32a797fe4c/aikido/bin/aikido-mcp#L36-L45):
  `spawn_server()` copies the complete environment with `dict(os.environ)` and
  passes it to the downloaded server.
- [`aikido/bin/aikido-mcp:92`](https://github.com/Mike-Demo/agent-skills/blob/e2e006d4e99c97eb0321366e30014d32a797fe4c/aikido/bin/aikido-mcp#L92):
  valid invocations reach server spawning.
- [`aikido/SKILL.md:24–43`](https://github.com/Mike-Demo/agent-skills/blob/e2e006d4e99c97eb0321366e30014d32a797fe4c/aikido/SKILL.md#L24-L43):
  the documented server execution uses the transient `AIKIDO_API_KEY`.

#### Risk and reachability

Normal helper use resolves a mutable package spec. A compromised resolved release,
dependency, or distribution path can execute with the owner's privileges and
read unrelated credentials inherited through the environment. No lower-trust
remote caller is shown to control the command directly, and no actual package
compromise was demonstrated. The confirmed issue is the unbounded code/environment
trust boundary, not evidence of an ongoing compromise.

#### Root cause

Neither the executed package version nor the environment supplied to it is
bounded by an in-repository control.

#### Smallest fix

Pin an exact reviewed `@aikidosec/mcp` version in both `SERVER_CMD` and the actual
spawn command. Replace `dict(os.environ)` with an explicit environment allowlist
containing only `PATH`, `HOME`, and `AIKIDO_API_KEY` when present. Verify the pinned
version and runtime needs before selecting it; do not invent a version or broadly
inherit the environment for compatibility.

An exact top-level pin does not itself lock transitive dependencies or provide
artifact integrity, and an environment allowlist is not a sandbox. A locked,
integrity-verified installation is further hardening, not a reason to defer this
smallest fix.

#### Acceptance criteria

- [ ] Both package-spec locations use the same exact reviewed version.
- [ ] Spawn environment contains only the allowed variables; a synthetic
  unrelated environment value is absent.
- [ ] Authentication, executable discovery, and MCP initialization still work
  with the restricted environment, including when optional variables are absent.

---

## Issue 3

### Title

[security][needs-validation] Audit leads to validate

### Body

Parent audit: https://github.com/Mike-Demo/agent-skills/issues/1

This is a validation checklist, not a confirmed vulnerability or remediation
issue. Do not assign severity until the missing host facts establish exposure.
Static inspection alone does not establish the owner's first-save permissions.

- [ ] **Validate token-file creation permissions in Clera and Indeed**
  (`oauth-token-file-created-before-chmod`).
  - **Evidence:**
    [`clera/bin/clera-mcp:19–22`](https://github.com/Mike-Demo/agent-skills/blob/e2e006d4e99c97eb0321366e30014d32a797fe4c/clera/bin/clera-mcp#L19-L22)
    and
    [`indeed/bin/indeed-mcp:19–22`](https://github.com/Mike-Demo/agent-skills/blob/e2e006d4e99c97eb0321366e30014d32a797fe4c/indeed/bin/indeed-mcp#L19-L22)
    write JSON and close the file before `os.chmod(..., 0o600)`.
  - **Claimed root cause:** Newly created files use umask-derived permissions
    while token bytes are written, before the later chmod restricts access.
  - **Exact missing fact supplied by the parent (including its truncation):**

    > The owner's host facts at first token save are not in the repository: the process umask under which clera-mcp/indeed-mcp first creates .clera-tokens.json/.indeed-tokens.json, and the resulting mode of a freshly created token file (0666 & ~umask — e.g. umask 0022 yields 0644 world-readable, while the verification environment's umask 0007 would yiel…

  - **Validation required:** With owner authorization, establish the actual
    first-save process umask for each helper and the resulting newly created
    file mode **before chmod**, using dummy data rather than real tokens.
    Record whether a lower-trust local principal can traverse the containing
    directory and read the file during that interval. Do not infer host facts
    from this session's umask or from the final post-chmod mode.
  - **Interpretation:** `0666 & ~0022` is `0644`; `0666 & ~0007` is `0660`
    (group access, not world access). Truncating an existing `0600` file preserves
    its mode, so ordinary refresh of an already-private file does not prove the
    first-creation exposure. Record creation/recreation circumstances and relevant
    group/directory access before deciding whether to promote or reject this lead.
