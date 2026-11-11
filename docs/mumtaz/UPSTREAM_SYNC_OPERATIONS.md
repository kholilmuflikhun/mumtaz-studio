# Upstream synchronization operations

## Detection cadence

The upstream sync workflow polls `https://github.com/tracedraw/tracedraw` every five minutes. GitHub scheduled workflows are best-effort and may start later than the requested minute, so this is near-real-time detection, not a guaranteed instant webhook.

A true push-triggered integration requires a webhook delivered by the upstream repository owner (or an installed GitHub App subscribed to upstream push events). Mumtaz Studio cannot configure a webhook on the TraceDraw repository because it does not own that repository.

## Required repository secret

Add an Actions secret named `UPSTREAM_SYNC_TOKEN` in Mumtaz Studio repository settings.

Prefer a fine-grained token owned by a dedicated automation/bot account, not the human reviewer. That makes the bot the PR author and allows the repository owner to submit the required approval. If a personal token belonging to the owner creates the PR, GitHub will not allow that same owner to approve their own PR. Scope the token only to `kholilmuflikhun/mumtaz-studio` with:
- Contents: Read and write
- Pull requests: Read and write
- Metadata: Read (automatically granted)

The token is used to publish the automation branch and open/update the upstream review PR. It is deliberately not stored in the repository. Never paste the token into chat, commit it, or place it in workflow YAML.

## Required merge protection

Configure a repository ruleset or branch protection rule targeting `main`:

- Require a pull request before merging.
- Require at least 1 approving review from someone other than the PR author (the repository owner can approve when the bot account created the PR).
- Dismiss stale approvals when new commits are pushed.
- Require conversation resolution.
- Require all applicable status checks:
  - `merged-work`
  - `test`
  - `app-test`
  - `build (windows-x86_64)`
  - `build (macos-arm64)`
  - `build (macos-x86_64)`
  - `build (linux-x86_64)`
  - `build (linux-arm64)`
  - WebAssembly/PWA and Android jobs when present in the workflow.
- Block force pushes and branch deletion.
- Do not enable bypass for the upstream sync bot/token.

The exact check-run names must be selected from the checks shown on a successful pull request in GitHub Settings; GitHub may expose matrix job names differently.

## Merge policy

Upstream sync PRs are opened as regular (non-draft) PRs so they can receive review. CI runs on the PR branch. A human reviewer must approve; merge is blocked until required checks and approval are satisfied. Do not configure unconditional auto-merge while the repository has no required-review ruleset.

## Conflict policy

If upstream changes conflict with downstream changes, the sync workflow aborts and publishes no branch update. Resolve conflicts in a dedicated integration branch. Preserve Mumtaz Studio product branding, technical compatibility identifiers, the Rust command-based document mutation architecture, license notices, and upstream attribution.
