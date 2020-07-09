# Debugging workflow commands

## Forking a workflow

[Fork a global workflow](https://console.transposit.com/mc/t/transposit-eng/actions/fork_global_workflow)

The fork_global_workflow and fork_global_integrator actions both call the fork_working_copy workflow. This workflow presents a list of all repositories at transposit-connectors at Github that contain both the topic "workflow" or "integrator" (depending on the action configuration) and "global." The "global" topic is added to repositories as part of the syncing operation that syncs dynamic_config's list of public services to all environments. The workflow then calls the webhook in mc_helper that executes the [fork_working_copy operation](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/fork_working_copy) in mc_helper.

The fork_working_copy operation can accept parameters to specify the working org and the working environment to fork to, but by default, these are set in mc_helper's environment variables.

I'm not sure if it's absolutely necessary, but in fact fork_working_copy both forks the template *and* syncs the workflow repo from GitHub, making sure that the configs deployed in the new copy match the GitHub config.yml file. 

## Creating a new workflow from a template

[Create a new workflow](https://console.transposit.com/mc/t/transposit-eng/actions/fork_template_workflow)

Both fork_template_workflow and fork_template_integrator call the fork_service_template workflow. This workflow calls the [fork_template_command](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/fork_template_command) webhook in mc_helper, which runs the fork command on the appropriate template on the server.

## Forking block_kit_lib

You can also fork block_kit_lib if you need to make changes to it:

[Fork block_kit_lib](https://console.transposit.com/mc/t/transposit-eng/actions/fork_block_kit_lib)

This action also calls the fork_working_copy workflow. The workflow then calls the webhook in mc_helper that executes the [fork_working_copy operation](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/fork_working_copy) in mc_helper on block_kit_lib.

## Syncing a workflow to GitHub:

[Sync workflow](https://console.transposit.com/mc/t/transposit-eng/actions/sync_workflow)

Syncing a workflow is the process of pushing a working copy of a workflow from a Transposit workspace to its GitHub source-of-truth repository in transposit-connectors. The underlying operation won't let you sync a copy from the transposit workspace, because that workspace is meant for the currently-deployed versions and will get rewritten from the GitHub source-of-truth every day.

This action calls the sync_workflow workflow (and works on both workflows and integrators, name notwithstanding), which executes a webhook that calls the [sync_workflow](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/sync_workflow) operation in mc_helper. It accepts a URL from the Transposit dev console, which it can parse into the relevant workflow/maintainer/environment.

Sync_workflow uses the syncTranspositApp lambda (via connector_utilities.sync_multiple_transposit_apps) to perform git push on the specified workflow/integrator to its GitHub repo. By default, the lambda does a force push, so as not to deal with merge conflicts, etc. If something fails here, though, it's worth checking the logs (https://us-west-2.console.aws.amazon.com/cloudwatch/home?region=us-west-2#logsV2:log-groups/log-group/$252Faws$252Flambda$252FsyncTranspositApp) to see the details of what went wrong.

After syncTranspositApp successfully completes its sync, it will call back to [sync_workflow_webhook](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/sync_workflow_webhook). This webhook completes the sync process: 

    - it saves out the deployed MC configurations to config.yml in the GitHub repo 
    - it regenerates the README and public-facing documentation for the workflow/integrator
    - it posts the results of everything to Slack.

## Validating a README:

[Validate README](https://console.transposit.com/mc/t/transposit-eng/actions/validate_readme)

When one is publicly releasing a workflow/integrator for the first time, it's a good idea to check to make sure that the automatically-generated README has valid information for all of its fields. The validate_readme action runs the validate_readme_for_service workflow, which calls a webhook that basically does the autogenerating-readme part of sync_workflow. It uses data from the manifest.json file to create a basic README file with the information a user would need to have, and provides a nice summary of the specific errors relating to data it couldn't find, like descriptions for environment variables, etc. If specific information pertaining to this particular workflow is needed, it should be provided in a setup.md file in the workflow repo; the generator will append it to the end of README.md.

After validating the README, one should re-sync the workflow to GitHub with the last action.

(Eventually we'll have an action here to validate the workflow) 

## Deploy latest release

[Deploy latest release](https://console.transposit.com/mc/t/transposit-eng/actions/deploy_release)

While it is possible to deploy configurations based only on git tags, I think it is best practice for "real" deployments (i.e. final versions meant for customer use) to correspond to releases in the GitHub source-of-truth repo. This way we can make sure that we've written a bit of description about what important changes happened.

This action calls the deploy_release_to_mc workflow, which finds the latest tag saved in the GitHub version and calls the [deploy_release_command](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/deploy_release_command) webhook. This operation uses the fork_working_copy operation to create a temporary working copy on staging in the transposit-workflows workspace. This working copy has that latest release as a git tag, so the operation can publish this tag as a configuration. The operation then runs sync_workflow again to sync this back to GitHub, completing the cycle.

## Publish from GitHub to servers

[Publish from GitHub](https://console.transposit.com/mc/t/transposit-eng/actions/publish_to_github)

At this point, the GitHub version of the workflow/integrator should be absolutely the way you want it. If this service is already listed in dynamic_config, it'll be published from GitHub to the transposit workspace on all servers by the daily syncer. You can also manually trigger publication by running this action.

The publish_workflow workflow calls a webhook that calls [publish_workflow](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/publish_workflow) in mc_helper. Publish_workflow has two execution paths: one for an array of names (used by the scheduled syncer) and one for a single name, which is what this workflow uses. It makes sure that there are at least stub versions of the service in the transposit org on all envs, then syncs the GitHub version to each of those, using sync_multiple_transposit_apps. If the sync was successful, it then publishes the configurations listed in config.yml to each of the transposit versions on the envs.

## Create PR for dynamic_config

[Make dynamic_config PR](https://console.transposit.com/mc/t/transposit-eng/actions/make_dynamic_config_pr)

This action only works for adding a new entry into dynamic_configuration. It executes the create_dynamic_config_pr workflow, which calls a webhook that runs the [make_dynamic_config_pr](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/make_dynamic_config_pr) operation in mc_helper. Because YAML parsers can't be guaranteed to write out ingested YAML exactly as it was, comments and all, and I didn't want to make a change that affected any more lines than necessary, this operation can only insert a new entry into the relevant global workflow or integrator array.

## Sync a Transposit app

[Sync Transposit app](https://console.transposit.com/mc/t/transposit-eng/actions/sync_transposit_app)

## Create a connector

[Create a connector](https://console.transposit.com/mc/t/transposit-eng/actions/create_a_connector)

## Generate a README

[Generate README](https://console.transposit.com/mc/t/transposit-eng/actions/generate_readme)
