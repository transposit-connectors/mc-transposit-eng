# Debugging workflow commands

## Start working with a workflow

If you want to make changes to a working copy of a globally-listed workflow or integrator, fork it into transposit-workflows and make your changes:

[Fork a global workflow](https://console.transposit.com/mc/t/transposit-eng/actions/fork_global_workflow)
[Fork a global integrator](https://console.transposit.com/mc/t/transposit-eng/actions/fork_global_integrator)

If you want to start a new workflow, you can create a new workflow based on the template workflow:

[Create a new workflow](https://console.transposit.com/mc/t/transposit-eng/actions/fork_template_workflow)
[Create a new integrator](https://console.transposit.com/mc/t/transposit-eng/actions/create_a_new_integrator)

You can also fork block_kit_lib if you need to make changes to it:

[Fork block_kit_lib](https://console.transposit.com/mc/t/transposit-eng/actions/fork_block_kit_lib)

## Publish your workflow

Sync your workflow to GitHub:

[Sync workflow](https://console.transposit.com/mc/t/transposit-eng/actions/sync_workflow)

Verify that the README at GitHub looks correct:

[Validate README](https://console.transposit.com/mc/t/transposit-eng/actions/validate_readme)

Make any necessary changes to your working copy and then sync the workflow again.

(Eventually we'll have an action here to validate the workflow) 

If all looks to be in order, make a release at GitHub, either from an existing tag or by creating a new tag. The tag must conform to semver conventions. Then deploy that release in MC:

[Deploy latest release](https://console.transposit.com/mc/t/transposit-eng/actions/deploy_release)

Clean up behind yourself: delete your working copy (as well as the copy in transposit-workflows, if that's not the org you used).
Then you can publish the GitHub version to the transposit org on all servers:

[Publish from GitHub](https://console.transposit.com/mc/t/transposit-eng/actions/publish_to_github)

If you want to include this workflow or integrator in the global list, you'll have to add it to dynamic_config:

[Make dynamic_config PR](https://console.transposit.com/mc/t/transposit-eng/actions/make_dynamic_config_pr)

Note: You'll have to wait for all the CI tests to run before you'll be allowed to squash and merge the PR, but you can do that whenever the tests are done. Delete the branch when you've merged.

Once the workflow is public, make sure that you've added details to the [workflow_debugging runbook](https://console.transposit.com/mc/t/transposit-eng/runbooks/workflow_debugging)

## A global workflow/integrator isn't showing up somewhere!

First, make sure that the version that is at GitHub looks the way you expect. If so, make sure that that version is published on all the servers:

[Publish from GitHub](https://console.transposit.com/mc/t/transposit-eng/actions/publish_to_github)



## Miscellaneous commands

[Sync Transposit app](https://console.transposit.com/mc/t/transposit-eng/actions/sync_transposit_app)

[Create a connector](https://console.transposit.com/mc/t/transposit-eng/actions/create_a_connector)

[Generate README](https://console.transposit.com/mc/t/transposit-eng/actions/generate_readme)
