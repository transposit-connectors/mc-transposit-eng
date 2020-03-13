# Debug connector tests

Connector tests run every day in [#connector-tests](https://app.slack.com/client/T2615V5UK/CC254A2E5). 
We expect the on-call engineer to be the first point of triage for when they fail.

## Auth error:

- "Unable to authorize" error: we have a transient bug where we fail to re-auth once every few months,
seemingly only for Google connectors. Add the auth back on the right environment 
([demo](https://console.demo.transposit.com/dev/t/transposit-connector-tests/test_runner/deploy/production-keys)
or [prod](https://console.transposit.com/dev/t/transposit-connector-tests/test_runner/deploy/production-keys))

## OAuth settings missing

This happens when we didn't set a client id and secret for the connector, or those are no longer valid.
Read the wiki [here](https://transposit.atlassian.net/wiki/spaces/DEV/pages/656506962/Data+Connectors+Test+Credentials)
on how to manage these settings.

