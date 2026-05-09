# `pome-sh/run-scenarios-action`

Run [Pome](https://github.com/pome-sh/pome) scenarios in GitHub Actions CI.
Drop one step into a workflow and your agent gets evaluated against a
deterministic GitHub-shaped twin on every PR.

[![License: AGPL-3.0](https://img.shields.io/badge/License-AGPL--3.0-blue.svg)](./LICENSE)

## Usage

```yaml
- uses: pome-sh/run-scenarios-action@v1
  with:
    scenario-path: scenarios/triage-agent.md
```

By default the action installs the latest `pome-sh` from npm, boots a local
twin in-process via `pome run`, executes the scenario, and uploads the trace
directory as a workflow artifact. Exit code is non-zero on scenario failure;
escape with `continue-on-error: true` if you want the rest of the job to run
regardless.

A worked workflow lives in [`examples/.github/workflows/example.yml`](./examples/.github/workflows/example.yml).

## Inputs

| Name | Required | Default | Description |
| --- | --- | --- | --- |
| `scenario-path` | yes | — | Path to a scenario `.md` file (or directory of them) in the consumer repo. |
| `pome-version` | no | `latest` | npm dist-tag or version of `pome-sh` to install. Pin in production. |
| `hosted-api-key` | no | (empty) | If non-empty, runs against the Pome hosted control plane (`POME_API_KEY` + `--hosted`). If empty, the CLI boots a local twin in-process. Source from a repo secret. |
| `agent-command` | no | (empty) | Shell command the CLI spawns to run your agent against the twin (forwarded as `pome run --agent <command>`). The CLI's built-in default is not useful in a consumer workflow — most users should set this. |

## Outputs

| Name | Description |
| --- | --- |
| `result` | `pass` or `fail`. |
| `trace-artifact-id` | GitHub Actions artifact ID for the uploaded trace directory. |

## Hosted vs local

- **Local (default).** `pome run` boots a fresh twin per scenario, seeds it
  from the scenario file, runs the agent against it, scores deterministically,
  and writes the trace under `runs/<scenario-slug>/<run-id>/`.
- **Hosted.** When `hosted-api-key` is non-empty the CLI talks to the hosted
  control plane instead; the local artifact path is the same and is uploaded
  alongside the cloud-side run.

## Versioning

- `v1.0.0` and `v1` (sliding) are published on every patch on the `v1` line.
- Pin to `@v1` for sliding patch updates, or to `@v1.0.0` for an immutable SHA.

## Troubleshooting

- **`E404 Not Found - GET https://registry.npmjs.org/pome-sh`** — `pome-sh`
  is published to npm at the OSS Launch Stage 1 public flip. Until then the
  action will not resolve the install. The smoke workflow in this repo is
  gated on `workflow_dispatch` only for the same reason; re-enable on
  `pull_request` after Stage 1.
- **`pome run` fails with a credentials error in hosted mode** — confirm
  `hosted-api-key` is being read from a secret and that the key is valid for
  the target hosted control plane.

## Documentation

- Quickstart, CLI reference, and scenario format: <https://docs.pome.sh>
- Pome OSS repo: <https://github.com/pome-sh/pome>
- Recording / replay event format: [`pome/docs/recording-spec.md`](https://github.com/pome-sh/pome/blob/main/docs/recording-spec.md)

## License

[AGPL-3.0](./LICENSE).
