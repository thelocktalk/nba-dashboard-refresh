# NBA dashboard refresh

Public scheduler for [the NBA dashboard](https://codex-nbatlt.vercel.app/).

One standard GitHub-hosted Ubuntu worker runs at a time. Each worker keeps its private, ephemeral workspace for up to two hours and checks sources at five-minute UTC boundaries. After a successful worker completes, a separate handoff job automatically queues its successor. Hourly cron remains a backup launch path. Reusing the workspace avoids downloading the full application state on every update.

The application code and stored data remain private. The launcher downloads the owner-approved, SHA-256-pinned runtime using GitHub OIDC. The server verifies the exact repository, main branch, workflow and owner. This identity can refresh sources; it cannot authorize bets, replace original quotes, approve a new runtime or deploy frontend code. Private application output is suppressed. No private runtime, data or cache artifacts are uploaded to this repository.

A capped native fallback covers stale updates. Failed updates retain the last verified dashboard and its actual source clocks. GitHub starts are best effort. [Standard public-repository GitHub-hosted runners are free](https://docs.github.com/en/billing/concepts/product-billing/github-actions).

A separate monthly maintenance job updates only `scheduler-activity.txt`, keeping the active repository from triggering GitHub's inactivity rule. That job has no private-application OIDC permission.

The handoff job has only `actions: write` in this scheduler repository. Its automatically issued GitHub token expires when that job ends. It has no OIDC or repository-content permission and never downloads the private application. It makes at most three dispatch attempts for transient network/server failures; authentication errors remain explicit. Successor runs retain their public predecessor run ID for verification. Failed or cancelled workers do not create a rapid retry loop; the last verified dashboard, hourly backup and capped native fallback remain available.

A reviewed push containing `[verify-continuation]` exercises the real worker and handoff with two source ticks. Its automatically queued successor uses the normal two-hour configuration.

Runtime changes require a coordinated owner approval, both workflow pins, the native bundle and a restart of the active worker.
