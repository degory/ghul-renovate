# ghul-renovate

The Renovate instance that keeps pinned versions current across the ghūl
repositories.

Everything is in two files:

- `.github/workflows/renovate.yml` — runs Renovate every six hours, and on
  demand with an optional dry run.
- `.github/renovate-global.json5` — which repositories it updates and how it
  behaves. Commented throughout with the reasoning, including why some
  repositories are deliberately absent.

Nothing is published from here and nothing builds against it, so a change to
the schedule or the repository list costs a workflow run and nothing else.

## Running it by hand

The Renovate workflow accepts a `workflow_dispatch` with two inputs: a dry run,
which logs what would change without opening pull requests, and a log level.
Use the dry run to check a configuration change before letting it open
anything.

## Adding a repository

Add it in two places — the `repositories` list in the configuration, and the
`repositories` the app token is scoped to in the workflow. The token grants
access to exactly the repositories named there, so one without the other
either does nothing or fails to authenticate.

Before adding one, check it cannot close a loop: a repository that ends up
building against something it publishes would hand updates back and forth
indefinitely. The configuration explains which ones this rules out and why.

## Credentials

It runs as the `ghul-coder` GitHub App, whose private key is held here as the
`GHUL_CODER_PRIVATE_KEY` secret. The app id is not a secret and is inline in
the workflow.

An app is used rather than `GITHUB_TOKEN` because a push made with
`GITHUB_TOKEN` does not start another workflow run — an update merged that way
would sit on `main` and never be released.
