# Public docs aren't up to date

## Workflow/integrator isn't in the public web site

Check to see if there are differences in the working branch: [workflow-readmes](https://github.com/transposit/www/compare/workflow-readmes). If there are changes, there is probably a pending pull request that hasn't been merged. [Go look and see](https://github.com/transposit/www/pulls). If not, use the comparison to manually generate a pull request.

(If there wasn't an open pull request, but there were changes in the working-readmes branch, something might have gone wrong in the mc_helper scheduled task. Let Daisie know if this happened to you.)

Make sure that the version that is at GitHub looks the way you expect. If so, make sure that that version is published on all the servers:

[Publish from GitHub](https://console.staging.transposit.com/mc/t/transposit-workflows/actions/publish_to_github)

If not, your working copy may need to be synced to GitHub.

(add sync workflow button here)

Then publish from GitHub back to all the servers. This will update the docs on the workflow-readmes branch.

(add publish workflow button here)

Go back to the top of this page and troubleshoot again!
