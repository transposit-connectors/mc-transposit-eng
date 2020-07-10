# Debugging automated sync processes

syncTranspositApp is an AWS Lambda that performs many of our automated syncs, both in actions and in scheduled tasks. This runbook attempts to lay out the parts where this could go wrong.

## Overview of syncTranspositApp

[syncTranspositApp](https://us-west-2.console.aws.amazon.com/lambda/home?region=us-west-2#/functions/syncTranspositApp?tab=monitoring) lives in the development-admin role in us-west-2. It runs code that is synced manually by Daisie with a [GitHub repo](https://github.com/transposit-connectors/syncTranspositApp) and the alias `deployed` points to the specific version used by other Transposit apps, so $LATEST may not correspond to the version that is actually being run.

The lambda can take several versions of inputs, many of which have examples in the test events. Fundamentally, the idea is that you give it one or many pairs of things to sync, each of which have a source URL and target URL. The URLs are fully-qualified git paths, including credentials, of the format `https://<gituser>:<gittoken>@<transposit-server-repo>:<branch, if not master>`. If either the target or source is a GitHub repo, the token is not necessary, as Daisie's git credential is saved in the lambda as an environment variable. In that case, the format is `https://@github.com/<github-repo>:<branch, if not master>`. The @ is still present in the URL. There is a boolean, `force_push`, that can be specified as well.

If something fails in a push, go to the [cloudwatch logs](https://us-west-2.console.aws.amazon.com/cloudwatch/home?region=us-west-2#logsV2:log-groups/log-group/$252Faws$252Flambda$252FsyncTranspositApp/log-events) to see what went wrong. If something went wrong with a push, try reproducing the push on your local machine.

The lambda can also be invoked asynchronously, with a separate `body` and `sync` key. The `sync` key corresponds to the array of apps to sync, as explained before. The `body` key points to an object that can contain a `webhook` object for a Transposit webhook, containing the api_key, hostname, and operation to be invoked. The whole body object is passed to the webhook in the request body, so context can be maintained.

The lambda is invoked through the [sync_multiple_transposit_apps](https://console.demo.transposit.com/dev/t/transposit/connector_utilities/code/op/sync_multiple_transposit_apps). The tag `deployed` is hard-coded to be the invoked qualifier in that operation. It can be invoked either synchronously or asynchronously. If invoked asynchronously, it can accept a body object, containing context and the webhook to be called back on completion.

There are other helper ops in connector_utilities to make commonly-used sync patterns easier to access, but under the hood, they all call sync_multiple_transposit_apps.

## Highest-level ops: actions, workflows, slash commands, scheduled tasks

The `/sync-transposit-app` slash command was the first one of these to be written. 

## Step 2



### Third-level headings like this are treated as headings only

To divide information within a step, you can use a third-level heading.

## Step 3

And that's how runbooks are made. Happy runbooking!