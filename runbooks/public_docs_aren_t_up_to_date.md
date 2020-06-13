# Public docs aren't up to date

## Workflow/integrator isn't in the public web site

Check to see if there are differences in the working branch: [workflow-readmes](https://github.com/transposit/www/compare/workflow-readmes)

First, make sure that the version that is at GitHub looks the way you expect. If so, make sure that that version is published on all the servers:

[Publish from GitHub](https://console.staging.transposit.com/mc/t/transposit-workflows/actions/publish_to_github)

If something still seems off, it might be that a scheduled task that sweeps up changes hasn't run yet. Check in [mc_helper](https://console.demo.transposit.com/dev/t/transposit/mc_helper/deploy/scheduled) to see if one of those tasks might do the thing you're looking for.