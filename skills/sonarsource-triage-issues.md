---
name: Search and triage SonarQube issues
description: Find open issues on a SonarQube Cloud project and triage them — assign, comment, and transition their status.
api: openapi/sonarsource-web-api-openapi.yml
operations: [issuesSearch, issuesAssign, issuesDoTransition, issuesAddComment]
---

# Search and triage SonarQube issues

Use the SonarQube Cloud Web API (`https://sonarcloud.io/api`) to find and resolve code
quality / security issues on a project.

## Auth
Send a user token on every request, either as `Authorization: Bearer <token>` or as the
HTTP Basic username with an empty password. See `authentication/sonarsource-authentication.yml`.

## Steps
1. **Find issues** — `GET /api/issues/search` (`issuesSearch`). Filter with
   `componentKeys=<projectKey>`, `resolved=false`, `severities`, `types`, and page with
   `p` / `ps` (page-number pagination; see `conventions/sonarsource-conventions.yml`).
   Read the `paging` object to iterate.
2. **Assign** — `POST /api/issues/assign` (`issuesAssign`) with `issue=<key>` and
   `assignee=<login>` (omit `assignee` to unassign).
3. **Comment** — `POST /api/issues/add_comment` (`issuesAddComment`) with `issue=<key>`
   and `text=<markdown>`.
4. **Transition** — `POST /api/issues/do_transition` (`issuesDoTransition`) with
   `issue=<key>` and `transition` (e.g. `confirm`, `resolve`, `falsepositive`, `wontfix`,
   `reopen`). Available transitions depend on the issue's current state.

## Errors
Failures return `{"errors":[{"msg":"..."}]}` with an HTTP status (400 bad params, 401
unauthenticated, 403 missing Browse/Administer Issues permission, 404 unknown issue). See
`errors/sonarsource-problem-types.yml`.
