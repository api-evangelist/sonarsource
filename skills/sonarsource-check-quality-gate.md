---
name: Check a project quality gate in CI
description: Read a SonarQube Cloud project's quality gate status and key measures to gate a CI/CD pipeline.
api: openapi/sonarsource-web-api-openapi.yml
operations: [projectsSearch, qualitygatesProjectStatus, measuresComponent]
---

# Check a project quality gate in CI

Gate a deployment on the SonarQube Cloud analysis result for a project.

## Auth
User token as `Authorization: Bearer <token>` or HTTP Basic username (empty password).

## Steps
1. **Resolve the project** (optional) — `GET /api/projects/search` (`projectsSearch`) with
   `organization` and `q` to confirm the `projectKey`.
2. **Read the quality gate status** — `GET /api/qualitygates/project_status`
   (`qualitygatesProjectStatus`) with `projectKey=<key>` (or `analysisId` /
   `pullRequest`). The response `projectStatus.status` is `OK`, `WARN`, or `ERROR`; fail
   the pipeline on `ERROR`. Each condition lists `metricKey`, `actualValue`, and
   `comparator`.
3. **Read supporting measures** (optional) — `GET /api/measures/component`
   (`measuresComponent`) with `component=<key>` and
   `metricKeys=bugs,vulnerabilities,code_smells,coverage,duplicated_lines_density` to
   report numbers alongside the gate result.

## Errors
`{"errors":[{"msg":"..."}]}` with HTTP status (401 unauthenticated, 403 missing Browse
permission, 404 unknown project). See `errors/sonarsource-problem-types.yml`.
