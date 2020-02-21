# Onboard New Engineer
When a new engineer starts at Transposit, there are several accounts and configurations that need to be updated.

## Create an Email Address
[Create Email Address](https://console.transposit.com/mc/t/transposit/actions/create_transposit_email)

Transposit uses [Google's G-Suite](https://gsuite.google.com/intl/en_ca/) for e-mail and shared documents. 
One of the most important things is to create the engineer's email address, but it doesn't look like this is something that can be automated yet.

The username chosen for the email address will be re-used as the new employee's account name for other services

Some things to keep in mind:

*Due to length restrictions, it is best if the main account is 8 characters or less if possible*

*At the current time, it's recommended to use the employee's first name*

_At some point in the future, this may change due to company size and availability_

## Add Email Aliases if Applicable
The new employee might ask to [create an alias](https://console.transposit.com/mc/t/transposit/actions/add_transposit_email_alias), such as first initial, last name - or a nickname

For example, `josh@transposit.com` could be the main account, with `jfraser@transposit.com` and `jfray@transposit.com` as aliases

## Google Groups
[Transposit](https://www.transposit.com) uses [Google Groups](https://support.google.com/a/users/answer/9304805?hl=en) for distribution lists, with the added benefit of searching archived threads

  [Invite](https://console.transposit.com/mc/t/transposit/actions/invite_user_to_google_groups) the new employee to the appropriate [Google Groups](https://support.google.com/a/users/answer/9304805?hl=en)

## HR Tasks
[Transposit](https://www.transposit.com) uses [Ease](https://www.ease.com) to manage benefits, discretionary time off, and other HR-related functions

  Using the email address previously created, [invite the employee to Ease](https://www.transposit.com)

The [HR representative](mailto:hr@transposit.com) will have the new employee fill out their employment and benefits paperwork

## Create AWS IAM User
[Transposit](https://www.transposit.com) is hosted by [Amazon Web Services / AWS](https://aws.amazon.com)

Each engineer is provided an AWS user, which is managed by their [Identity and Access Management / IAM](https://www.amazonaws.cn/en/iam/) service

  Using the username from the email step, [create an IAM user](https://console.transposit.com/mc/t/transposit/actions/create_iam_user)

Each engineer is provided an [AWS development sandbox](https://transposit.atlassian.net/wiki/spaces/DEV/pages/310706177/Sandbox+AWS+Accounts) to develop, test, and deploy code

  Provide the username to [SRE](mailto:sre@transposit.com) so that they can run the `sre-scripts/sandbox.py` management script

## GitHub
[Transposit](https://www.transposit.com) uses [GitHub](https://www.github.com) for `source code management / SCM`

  If the employee already has an active [GitHub](https://www.github.com) account they would like to use, [add that user](https://console.transposit.com/mc/t/transposit/actions/add_github_user_to_transposit) to the [Transposit](https://www.github.com/transposit) organization

    If the employee does not have an active [GitHub](https://www.github.com) account, [create one](https://console.transposit.com/mc/t/transposit/actions/create_github_user) for them

  [Add the employee to the appropriate groups](https://console.transposit.com/mc/t/transposit/actions/add_github_user_to_groups) for necessary permissions

## Slack
[Transposit](https://www.transposit.com) uses [Slack](https://www.slack.com) for team communication

  Using the email address previously created, [invite the employee](https://console.transposit.com/mc/t/transposit/actions/invite_user_to_slack) to [Slack](https://www.slack.com)

  [Add the employee](https://console.transposit.com/mc/t/transposit/actions/add_slack_user_to_channels) to appropriate [Slack](https://www.slack.com) channels

## Confluence
[Transposit](https://www.transposit.com) uses [Atlassian's hosted Confluence](https://www.atlassian.com/software/confluence) application for Documentation

  Using the email address previously created, [invite the employee](https://console.transposit.com/mc/t/transposit/actions/invite_user_to_confluence) to [Confluence](https://www.atlassian.com/software/confluence)

  [Provide links](https://console.transposit.com/mc/t/transposit/actions/send_confluence_links) to machine setup, company values, OpsGenie notification setup, Accessing AWS / Sandbox

## Jira Access
[Transposit](https://www.transposit.com) uses [Atlassian's hosted Jira](https://www.atlassian.com/software/jira) application for bug tracking, issue tracking, and project management.

  Using the email address previously created, [invite the employee](https://console.transposit.com/mc/t/transposit/actions/invite_user_to_jira) to [Jira](https://www.atlassian.com/software/jira)

  [Assign a ticket](https://console.transposit.com/mc/t/transposit/actions/assign_jira_ticket_for_onboarding) in [Jira](https://www.atlassian.com/software/jira) to the employee to track machine setup and other questions the employee might have

## OpsGenie
[Transposit](https://www.transposit.com) uses [Atlassian's hosted OpsGenie](https://www.atlassian.com/software/opsgenie) application to manage alert notification and on-call schedules

Using the email address prevoiusly created, [invite the employee](https://console.transposit.com/mc/t/transposit/actions/invite_user_to_opsgenie) to [OpsGenie](https://www.atlassian.com/software/opsgenie)

  [Send a link](https://console.transposit.com/mc/t/transposit/actions/send_opsgenie_download_links) to download the [OpsGenie](https://www.atlassian.com/software/opsgenie) mobile application

    [Android](https://play.google.com/store/apps/details?id=com.ifountain.opsgenie&hl=en_US)

    [iOS](https://apps.apple.com/us/app/opsgenie/id528590328)

  [Send a test notification](https://console.transposit.com/mc/t/transposit/actions/send_opsgenie_test_notification) to make sure the employee has configured their application correctly

