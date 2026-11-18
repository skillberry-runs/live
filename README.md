# live

**Temporary** "Watch live" data for cap-evolve benchmark runs that are still in progress.
Nothing here is permanent: when a run finishes, its permanent record and "Open UI" data are written
to [`dashboard`](https://github.com/skillberry-runs/dashboard) and the benchmark's repo, and its
branch here is deleted.

## How it works

- **One branch per running CI job**, named `<run_id>__<tier>-<bench>` (the same slug the dashboard
  uses), holding `data/`: the run's CapEvolve dashboard export.
- **Every 5 minutes** cap-evolve's `ci/benchmarks/lib/live_push.sh` builds a fresh one-commit snapshot
  and force-pushes it to that branch, so a branch never has more than one commit and nothing
  accumulates.
- **When the job ends** (any outcome), the same script deletes the branch.
- **The dashboard's "Running now" panel** links each running job to the shared viewer, reading
  `https://raw.githubusercontent.com/skillberry-runs/live/<branch>/data/`.
- **Leftovers** (a job killed before its cleanup step) are deleted by
  [`sweep-stale-branches.yml`](.github/workflows/sweep-stale-branches.yml): any run branch untouched
  for 26 hours (longer than a CI job's 24-hour limit).

`main` holds only this README and the sweep workflow. CI writes with a token that can reach this
repo only (`SKILLBERRY_RUNS_LIVE_TOKEN` in cap-evolve), so a bug in the live poller cannot touch
the dashboard's records or any benchmark's data. The repo can be wiped or recreated at any time.
