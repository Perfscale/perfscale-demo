# perfscale-demo

Public playground for the [**perfscale** GitHub
Action](https://github.com/Perfscale/github-action): every push runs real
load tests in GitHub Actions and publishes the results where you can see
them — the **job summary** (metric table rendered into the run page) and the
**artifacts** (a zip with the raw summary + machine-readable JSON).

Nothing here hits an external service: each job starts a local
`python3 -m http.server` on the runner and load-tests that.

## What runs where

| Job | Engine | What to look at |
|---|---|---|
| `native` | perfscale native step engine (`-f test.yaml -c config.yaml`) | job summary table, `perfscale-output-native` artifact |
| `jmeter` | JMeter (`--jmeter plan.jmx`, JMeter 5.6.3 installed in the job) | job summary table, `perfscale-output-jmeter` artifact |

Open the [Actions tab](../../actions), click the latest run: each job's
summary carries the rendered metric table; the artifacts are at the bottom
of the run page.

## The workflow in one look

```yaml
- uses: Perfscale/github-action@v1
  id: loadtest
  with:
    file: test.yaml        # or: k6 / locust / jmeter inputs
    config: config.yaml
- uses: actions/upload-artifact@v4
  with:
    name: perfscale-output
    path: ${{ steps.loadtest.outputs.output-file }}
```

The action installs the pinned perfscale release (checksum-verified), runs
the test, renders the metric table into `$GITHUB_STEP_SUMMARY`, and packs
`summary.txt` + `summary.json` into a zip. Engine binaries (k6, locust,
jmeter) are the workflow's responsibility — see the `jmeter` job for the
JMeter install step.

## Links

- [perfscale](https://github.com/Perfscale/perfscale) — the CLI itself
- [github-action](https://github.com/Perfscale/github-action) — the action
  this repo exercises
- [docs](https://perfscale.su/docs/oss) — OSS documentation
