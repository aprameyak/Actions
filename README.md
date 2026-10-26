# Actions

A GitHub Actions workflow that pings a Render-hosted service every 15 minutes so the free-tier instance does not spin down and hit a cold start on the next real request. You can copy the workflow into your own repo and point it at your own services.

## Features

- Runs on a cron schedule, every 15 minutes (`*/15 * * * *`).
- Can also be started by hand from the Actions tab (`workflow_dispatch`), and runs on pushes and pull requests to `main`.
- Sends a single `curl -I` (HEAD request) to each service URL.
- Ends each request with `|| true`, so a service that is down or slow does not fail the workflow.

## What it pings

There is one workflow, `.github/workflows/blank.yml`, with one job that currently pings one service, the MusIQ backend at its public `onrender.com` URL. No secrets or tokens are used. The job looks like this:

```yaml
jobs:
  ping-render:
    runs-on: ubuntu-latest
    steps:
      - name: Ping MusIQ
        run: curl -I https://musiq-sc2d.onrender.com || true
```

## Using it for your own services

1. Copy `.github/workflows/blank.yml` into your repository's `.github/workflows/` folder.
2. Replace the ping step with one step per service:

   ```yaml
   steps:
     - name: Ping my-service
       run: curl -I https://my-service.onrender.com || true
   ```

3. Change the cron expression if you want a different interval, then commit to your default branch. Scheduled workflows only run from the default branch.

Keep in mind that GitHub does not guarantee exact timing for scheduled runs; they can start late under load. GitHub also disables scheduled workflows in public repos after 60 days with no repository activity, so the pings will stop until you re-enable the workflow.

## Contributing

This is a small personal utility, but issues and pull requests are welcome.

## License

MIT, see [LICENSE](LICENSE).
