---
name: Provision a project and analysis token
description: Create a SonarQube Cloud project, mint an analysis token, and register an analysis webhook.
api: openapi/sonarsource-web-api-openapi.yml
operations: [projectsCreate, userTokensGenerate, webhooksCreate]
---

# Provision a project and analysis token

Bootstrap a new project for CI-based analysis on SonarQube Cloud.

## Auth
User token with administer-organization permission, as `Authorization: Bearer <token>` or
HTTP Basic username (empty password).

## Steps
1. **Create the project** — `POST /api/projects/create` (`projectsCreate`) with
   `organization=<orgKey>`, `project=<projectKey>`, and `name=<display name>`.
2. **Mint an analysis token** — `POST /api/user_tokens/generate` (`userTokensGenerate`)
   with `name=<token name>` (and `type`/`projectKey` for a project-scoped analysis token).
   The plaintext token is returned once — store it as the scanner's `sonar.token`.
3. **Register a webhook** (optional) — `POST /api/webhooks/create` (`webhooksCreate`) with
   `name`, `project=<projectKey>`, and `url=<callback>` to be notified when analyses
   complete.

## Notes
- Feed the token to `sonar-scanner` / the CI plugin as `-Dsonar.token` (see
  `cli/sonarsource-cli.yml`).
- Errors return `{"errors":[{"msg":"..."}]}` with an HTTP status (403 insufficient
  permissions, 400 duplicate project key). See `errors/sonarsource-problem-types.yml`.
