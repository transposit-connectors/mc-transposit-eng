# Action-specific credentials and configuration

## Action-specific credentials and configuration

You can now add action-specific credentials and configuration.

**What does this mean?**


Imagine you have a workflow that uses `transposit/aws_cloudwatch_logs`. You want to use it on both staging and prod. But the [integrations page](https://console.transposit.com/mc/t/transposit-eng/settings/integrations) only allows you to add one auth to be used for all of the workflows that use `transposit/aws_cloudwatch_logs`.

With action-specific credentials, you can add auth that will _only_ be used for that action. For example, we have these three actions:
[Request ID Help (Demo)](https://console.transposit.com/mc/t/transposit-eng/actions/request_id_help_demo)
[Request ID Help (Staging)](https://console.transposit.com/mc/t/transposit-eng/actions/request_id_help_staging)
[Request ID Help (Prod)](https://console.transposit.com/mc/t/transposit-eng/actions/request_id_help_prod)

Which all use the same workflow, but have their own environment-specific credentials tied to them (which you can see on the respective action pages).

** The hierarchy of applied credentials and configuration **


If the action you're running has specific credentials and configuration (applied on the action's page), those are used. If those do not exist, we fallback to any credentials provided via the integrations page.

** Why did we build this? **

We want to dogfood our product more and debug Transposit using runbooks and actions, but a big limitation was the "only one credential for all of the workflows that use a connector" issue, since we have three AWS environments with different credentials for each.

Note: that this is currently an internal feature only.
