# Debugging automated sync processes

syncTranspositApp is an AWS Lambda that performs many of our automated syncs, both in actions and in scheduled tasks. This runbook attempts to lay out the parts where this could go wrong.

## Overview of syncTranspositApp

[syncTranspositApp](https://us-west-2.console.aws.amazon.com/lambda/home?region=us-west-2#/functions/syncTranspositApp?tab=monitoring) lives in the development-admin role in us-west-2. It runs code that is synced manually by Daisie with a [GitHub repo](https://github.com/transposit-connectors/syncTranspositApp) and the alias `deployed` points to the specific version used by other Transposit apps, so $LATEST may not correspond to the version that is actually being run.

The lambda can take several versions of inputs, many of which have examples in the test events. Fundamentally, the idea is that you give it one or many pairs of things to sync, each of which have a source URL and target URL. The URLs are fully-qualified git paths, including credentials, of the format `https://<gituser>:<gittoken>@<transposit-server-repo>:<branch, if not master>`. If either the target or source is a GitHub repo, the token is not necessary, as Daisie's git credential is saved in the lambda as an environment variable. In that case, the format is `https://@github.com/<github-repo>:<branch, if not master>`. The @ is still present in the URL. There is a boolean, `force_push`, that can be specified as well.

If something fails in a push, go to the [cloudwatch logs](https://us-west-2.console.aws.amazon.com/cloudwatch/home?region=us-west-2#logsV2:log-groups/log-group/$252Faws$252Flambda$252FsyncTranspositApp/log-events) to see what went wrong. If something went wrong with a push, try reproducing the push on your local machine.

The lambda can also be invoked asynchronously, with a separate `body` and `sync` key. The `sync` key corresponds to the array of apps to sync, as explained before. The `body` key points to an object that can contain a `webhook` object for a Transposit webhook, containing the api_key, hostname, and operation to be invoked. The whole body object is passed to the webhook in the request body, so context can be maintained.

The lambda is invoked through the [sync_multiple_transposit_apps](https://console.demo.transposit.com/dev/t/transposit/connector_utilities/code/op/sync_multiple_transposit_apps). The tag `deployed` is hard-coded to be the invoked qualifier in that operation. It can be invoked either synchronously or asynchronously. If invoked asynchronously, it can accept a body object, containing context and the webhook to be called back on completion. If it is invoked and there is a body passed in, but no webhook is specified, the default callback is [sync_lambda_webhook](https://console.demo.transposit.com/dev/t/transposit/connector_utilities/code/op/sync_lambda_webhook) in connector_utilities.

The sync_lambda_webhook callback formats the lambda results for slack and posts them to whatever slack channel was specified or test-integrations, if none was specified.

There are other helper ops in connector_utilities to make commonly-used sync patterns easier to access, but under the hood, they all call sync_multiple_transposit_apps.

## Highest-level ops: actions, workflows, slash commands, scheduled tasks

The `/sync-transposit-app` slash command was the first one of these to be written. It invokes the [slack_slash_command](https://console.demo.transposit.com/dev/t/transposit/connector_utilities/code/op/slack_slash_command_webhook) webhook in connector_utilities. Because I wrote it so long ago, it might be using some deprecated authentication and execution patterns from before the MC pivot. I may clean this up someday. It passes the whole parsed_body received in the slash command invocation in the body for the lambda, so any slack parameters can be picked up by the callback. The most common error seen for this slash command is forgetting the force_push true/false tag at the end of the invocation.

[Sync Transposit app](https://console.transposit.com/mc/t/transposit-eng/actions/sync_transposit_app)

The sync_transposit_app action/workflow calls a similar webhook, [sync_transposit_command](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/sync_transposit_command), in mc_helper. This webhook is meant to be generalizable to different sorts of sync cases, so, for example, the sync_runbook action also calls this webhook but with runbook URLs. It chooses the sync type based on the structure of the source URL, so if you ran sync_transposit_app with runbook URLs, you'd actually run sync_runbook instead of the generic sync_transposit_app.

Most of the derived sync actions, like sync_workflow, sync_runbook, publish_workflow, etc., use syncTranspositApp via connector_utilities to do the basic repo syncing, and then use GitHub and internal API calls to manipulate any extra configurations or files that need tweaking.

