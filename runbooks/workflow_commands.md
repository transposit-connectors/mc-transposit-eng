# Debugging workflow commands

## Introduction

This runbook enumerates all of the actions available in the transposit-workflows [Workflow Commands](https://console.staging.transposit.com/mc/t/transposit-workflows/runbooks/workflow_slash_commands) runbook. That version is aimed at walking a user through the processes involved in creating, modifying, and publishing workflows, while this version explains each of the actions in more detail to aid in debugging.

These actions all follow a similar pattern: they invoke webhooks in either [mc_helper](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/) or [dc_helper](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/), which then call on specific operations. I've tried to explain the basic execution flow to the important operations, but if something is not clear from tracing through the execution, please let me (Daisie) know. 

Note: because we don't pass the bodies of HTTP responses up to the calling workflows, the error messages you get in MC are often just "HostException: Response status not ok. Status code: 500". There may very well be an informative message in that HTTP response, though! Go look in the Monitor tab of the calling workflow.

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

## Validating a service

(https://console.transposit.com/mc/t/transposit-eng/actions/validate_service)

Validating a service runs all of the available audits in [mc_auditor](https://console.demo.transposit.com/dev/t/transposit/mc_auditor/). It executes the [complete_audit_on_service webhook](https://console.demo.transposit.com/dev/t/transposit/mc_auditor/code/op/complete_audit_on_service) and then loops, calling the [list_results_for_audit webhook](https://console.demo.transposit.com/dev/t/transposit/mc_auditor/code/op/list_results_for_audit) until the status is "completed." The complete_audit_on_service webhook sets up a master task, to be used to collect the sub-audits, then spins off separate tasks for each audit. The [_collect_subaudits](https://console.demo.transposit.com/dev/t/transposit/mc_auditor/code/op/_collect_subaudits) operation then picks up all of the subaudits as they're completed and adds them to the master audit results.

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


This action is not actually workflow-specific, but I didn't know where else to put this. It runs the sync_transposit_app workflow, which calls the [sync_transposit_command](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/sync_transposit_command) webhook in mc_helper, which calls sync_transposit_app in connector_utilities, which calls sync_multiple_transposit_apps, which calls the syncTranspositApp lambda to git push the apps from one place to the other. By default, this is a force-push, to make it smoother for automated processes. If something fails in a push, go to the [cloudwatch logs](https://us-west-2.console.aws.amazon.com/cloudwatch/home?region=us-west-2#logsV2:log-groups/log-group/$252Faws$252Flambda$252FsyncTranspositApp/log-events) to see what went wrong.

## Create a connector

[Create a connector](https://console.transposit.com/mc/t/transposit-eng/actions/create_a_connector)

This action is not actually workflow-specific, but I didn't know where else to put this. It runs the create_connector workflow, which calls the [create_connector](https://console.demo.transposit.com/dev/t/transposit/dc_helper/code/op/create_connector_webhook) webhook in *dc_helper* (not mc_helper). Connectors can be either smart connectors or swagger-only. 

The operation that is called from the webhook, [create_connector](https://console.demo.transposit.com/dev/t/transposit/dc_helper/code/op/create_connector), creates the necessary stubs for a new data connector and test. If the is_smart flag is on, it will create a raw and smart stub on all the servers and in GitHub transposit-connectors. The `raw_whatever` repo will contain the swagger part and the `whatever` repo will contain the smart part. If it's not on, the raw swagger connector stub will be created as `whatever`. 

A stub `test_whatever` app will be created on demo, which you can fill in to make a test you can add to the test_runner app.

## Generate a README

[Generate README](https://console.transposit.com/mc/t/transposit-eng/actions/generate_readme)

This action runs the generate_readme_for_service workflow, which actually calls a generic webhook at mc_helper, [run_op_for_service_command](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/run_op_for_service_command). In this case, the operation that is called is [sync_documentation](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/sync_documentation) (also called in sync_workflow). This operation generates the metadata used to create the readme from the manifest.json for the service and syncs the readme to GitHub. It also writes this metadata out as frontmatter to the public documentation in www, as part of an ongoing branch called `workflow-readmes`. Every week, `workflow-readmes` gets scooped up into a pull request by a scheduled task.

## Sync a runbook

[Sync runbook](https://console.transposit.com/mc/t/transposit-eng/actions/sync_runbook)

This action is most akin to the sync_transposit_app action: it calls the sync_runbook action, which calls a webhook that calls the [sync_runbook](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/sync_runbook) operation in mc_helper. 

The sync_runbook operation uses a GitHub repository named `mc-workspace_name` to perform the synchronization. If this repo doesn't exist, it first creates it. From there, it pushes the source url's workspace and the target url's workspace to the appropriate env-named branches at GitHub, to make sure those sources of truth are as up-to-date as possible. 

Then it takes the runbook md file at the GitHub source branch, updates the action links to point to the target workspace, and pushes that runbook file to the GitHub target branch. 

It also updates all of the linked action yaml files from the GitHub source branch and copies them to the GitHub target branch.

Finally, it uses the syncTranspositApp lambda, via connector_utilities.sync_transposit_app, to push the GitHub target branch to the targeted workspace.

The result is a link to the diff between the GitHub target branch before and after the sync, so you can easily see what changes were made.