# NBA dashboard refresh

Public scheduler for [the NBA dashboard](https://codex-nbatlt.vercel.app/).

An hourly launch queues a standard GitHub-hosted Ubuntu worker. One worker runs at a time. Each worker keeps its private, ephemeral workspace for up to two hours and checks sources at five-minute UTC boundaries. Reusing the workspace avoids downloading the full application state on every update.

The application code and stored data remain private. The launcher downloads the owner-approved, SHA-256-pinned runtime using GitHub OIDC. The server verifies the exact repository, main branch, workflow and owner. This identity can refresh sources; it cannot authorize bets, replace original quotes, approve a new runtime or deploy frontend code. Private application output is suppressed. No private runtime, data or cache artifacts are uploaded to this repository.

A capped native fallback covers stale updates. Failed updates retain the last verified dashboard and its actual source clocks. GitHub starts are best effort. [Standard public-repository GitHub-hosted runners are free](https://docs.github.com/en/billing/concepts/product-billing/github-actions).

A separate monthly maintenance job updates only `scheduler-activity.txt`, keeping the active repository from triggering GitHub's inactivity rule. That job has no private-application OIDC permission.

Runtime changes require a coordinated owner approval, both workflow pins, the native bundle and a restart of the active worker.
