# Workflow/integrator docs aren't up to date

## Overview

For a high level overview on how our website is put together, visit [this runbook](https://console.transposit.com/mc/t/transposit-eng/runbooks/debugging_our_website_www_and_docs).

## Unmerged docs changes

We run a syncer weekly that creates a PR for new workflow docs changes. If we have stale docs, the first thing to check is if we have an open PR that hasn't yet been merged.

Check to see if there are differences in the working branch: [workflow-readmes](https://github.com/transposit/www/compare/workflow-readmes). If there are changes, there is probably a pending pull request that hasn't been merged. [Go look and see](https://github.com/transposit/www/pulls). If not, use the comparison to manually generate a pull request.

(If there wasn't an open pull request, but there were changes in the working-readmes branch, something might have gone wrong in the mc_helper scheduled task. Let Daisie know if this happened to you.)

## Version is out of date

The process we use for releasing workflows/integrators to the public is:
1. The developer creates/updates a working copy of the service in some working org. For workflow engineers, this is generally the transposit-workflows org on the staging server.
2. Once the working copy is fully functioning in an action (tested using that working copy's latest version), the developer *syncs the workflow* so that it is available in the transposit org on all servers and in the transposit-connectors repository on GitHub. The GitHub version is the source of truth for all public services.
3. The developer then makes a new release for the public on the GitHub repository. This release then needs to be *deployed on all servers*.
4. Finally, the new version's documentation changes need to be updated in the public-facing docs. This happens as part of the *publishing workflows* process.

Assume that you're looking at some version of the workflow or integrator's README that looks the way you want it to look. That version is your working copy.

* Does the README version that is at [GitHub](https://github.com/transposit-connectors) match your working copy? If so, make sure that that version is published on all the servers:

[Publish from GitHub](https://console.staging.transposit.com/mc/t/transposit-workflows/actions/publish_to_github)

If not, your working copy is out of sync with the GitHub version. Sync it back to GitHub.

(add sync workflow button here)

Then publish from GitHub back to all the servers. This will update the docs on the workflow-readmes branch.

(add publish workflow button here)

Go back to the top of this page and troubleshoot again!
