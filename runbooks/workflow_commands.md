# Debugging workflow commands

## Forking a workflow

[Fork a global workflow](https://console.transposit.com/mc/t/transposit-eng/actions/fork_global_workflow)

## Creating a new workflow from a template

If you want to start a new workflow, you can create a new workflow based on the template workflow:

[Create a new workflow](https://console.transposit.com/mc/t/transposit-eng/actions/fork_template_workflow)

## Forking block_kit_lib

You can also fork block_kit_lib if you need to make changes to it:

[Fork block_kit_lib](https://console.transposit.com/mc/t/transposit-eng/actions/fork_block_kit_lib)

## Syncing a workflow to GitHub:

Sync your workflow to GitHub:

[Sync workflow](https://console.transposit.com/mc/t/transposit-eng/actions/sync_workflow)

## Validating a README:

[Validate README](https://console.transposit.com/mc/t/transposit-eng/actions/validate_readme)

Make any necessary changes to your working copy and then sync the workflow again.

(Eventually we'll have an action here to validate the workflow) 

## Deploy latest release

If all looks to be in order, make a release at GitHub, either from an existing tag or by creating a new tag. The tag must conform to semver conventions. Then deploy that release in MC:

[Deploy latest release](https://console.transposit.com/mc/t/transposit-eng/actions/deploy_release)

## Publish from GitHub to servers

Clean up behind yourself: delete your working copy (as well as the copy in transposit-workflows, if that's not the org you used).
Then you can publish the GitHub version to the transposit org on all servers:

[Publish from GitHub](https://console.transposit.com/mc/t/transposit-eng/actions/publish_to_github)

## Create PR for dynamic_config

If you want to include this workflow or integrator in the global list, you'll have to add it to dynamic_config:

[Make dynamic_config PR](https://console.transposit.com/mc/t/transposit-eng/actions/make_dynamic_config_pr)

Note: You'll have to wait for all the CI tests to run before you'll be allowed to squash and merge the PR, but you can do that whenever the tests are done. Delete the branch when you've merged.

## Sync a Transposit app

[Sync Transposit app](https://console.transposit.com/mc/t/transposit-eng/actions/sync_transposit_app)

## Create a connector

[Create a connector](https://console.transposit.com/mc/t/transposit-eng/actions/create_a_connector)

## Generate a README

[Generate README](https://console.transposit.com/mc/t/transposit-eng/actions/generate_readme)
