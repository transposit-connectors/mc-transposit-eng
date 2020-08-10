# Internal tooling libraries

There are several connector-libraries that I've written over the years to make it more modular to perform various operations I use a lot. Right now, they are:

* connector_utilities: a grab-bag of miscellany
* parsing: a connector with no dependencies that is meant to provide a single source to manage various kinds of parsing, especially for Transposit URLs.
* dc_helper: originally based on a tool written by Sarah, this is the application/library that is meant to handle common data connector operations, hence the name "dc" helper.
* mc_helper: an analog to dc_helper that is meant to be for Mission Control-specific operations.
* mc_auditor: tool to audit workflows and integrators.

## connector_utilities

### syncTranspositApp operations:

#### sync_multiple_transposit_apps: 
Sync multiple apps at a time. This is the ur-operation that talks to the syncTranspositApp lambda.

Parameters:

  * apps_to_sync: an array of things to sync in the same lambda execution, so as not to waste resources setting up and tearing down the lambda a bunch of times if you're going to be syncing a lot of things. Each entry is in the format `{source_url, target_url, force_push, diff}`. URLs can be either Transposit or GitHub URLs. This operation will parse them and stuff in the git authentications for the lambda. `force_push` and `diff` set those git flags for the push. `diff` is false by default, but `force_push` doesn't have a default value because you should probably set that on purpose.
  * body: a fairly arbitrary object that contains information for callbacks. If the key `webhook` is specified, it should be an object of the format `{webhook_url, api_key, operation_name}` that will call a Transposit webhook. If no webhook is specified, sync_lambda_webhook is called. Everything else in the body object gets passed to the callback.
  * invoke_async: a boolean specifying if the lambda should be invoked synchronously, in which case it'll wait for completion, or asynchronously, which will return immediately and invoke the callback webhook on completion.
  
#### sync_lambda_webhook: 
This is the default callback if you call sync_multiple_transposit_apps with invoke_async: true. It posts the formatted lambda results in the #test-integrations slack channel in the Transposit workspace.

#### format_lambda_results:
Formats the returned results from the syncTranspositApp lambda into a pretty-print form for Slack.

#### get_git_credentials: 
A convenience operation that takes a Transposit or GitHub URL and inserts the appropriate git credentials into the correct format for the syncTranspositApp lambda.

#### diff_transposit_app: 
Uses the dry-run functionality of the syncTranspositApp lambda to diff two versions of a transposit app. Returns null if identical.

#### transfer_file: 
Convenience operation that uses syncTranspositApp to transfer any file from one GitHub repo to another.

#### sync_transposit_app: 
Convenience operation to sync an app from one place to another. It reduces the parameters needed to call sync_multiple_transposit_apps.

#### sync_transposit_app_to_all:
An early version of what ended up being the sync/publish system for MC workflows and integrators: if the app_url invoked is a non-GitHub (transposit-connectors) URL, it first syncs the app to the appropriate GitHub URL, then pushes from the GitHub URL to all of the envs.

### dynamic_config operations:

Convenience operations to access parts of dynamic_configuration.yml.

* get_dynamic_configuration: Get the entire dynamic_config object as JSON.
* list_connectors: List all publicly available connectors.
* list_dynamic_config_section: List all services in a dynamic_config section.

### internal_api operations:

A fairly hodgepodge group of convenience operations that call the internal API.

* run_operation_on_server: Run an internal Transposit API op on a given server without having to specifically add all of the internal_api connectors for all of the servers in every app you need.
* get_app_info: Get info for a single Transposit app, because `listAccessibleServices` returns all of them and you just want info for one.
* update_authenticator_settings: takes in an object of the schema returned by the `internal_api.view` operation and does all of the appropriate internal_api calls to set them all.
* create_app_stubs_everywhere: Create an app stub on all servers, as well as in GitHub transposit-connectors.
* delete_service_from_all_servers: Convenience operation to call deleteService on all the envs at once. It's really useful if you've accidentally made a stub connector by calling create_app_stubs_everywhere.

### stash:

I implemented a very rudimentary stash that can be accessed from anywhere and will maintain consistency. It's implemented with DynamoDB on Yoko's AWS sandbox, so you'll need to authenticate with those credentials if you want to use it. It uses the same paradigm as our Transposit dev console's stash: store any arbitrary value under a key. Store null for a key to delete the key.

* stash_get: get an arbitrary key's value. If it's possible to JSON.parse the value, it'll return the JSON object. If JSON.parse fails, it just returns the raw value.
* stash_put: put an arbitrary value in the stash under a key. The value will be JSON.stringified, so you can pass in arbitrary JSON objects. If you store null for a key, the key is deleted from the stash.

### manifest operations:

Convenience operations for getting common things out of a manifest.json.

* list_operations_for_app: Get all operations for an app
* get_app_data_connections: Get all dataConnections for an app

### transposit-connectors operations:

Operations that interrogate the transposit-connectors GitHub repo.

* list_sample_apps: List all sample apps. This is probably not used for much anymore, but it was used in dc_helper, so it's still here.
* list_custom_paginators: I wrote this when I was trying to keep tabs on which connectors were still using custom paginators.

### miscellaneous:

* slack_slash_command_webhook: runs both the `/sync-transposit-app` and the `/add-new-employee` slash commands. This is a pretty old implementation, so it might need some cleanup. There are better versions for `/sync-transposit-app`, like the one in mc_helper, but this one still works, I think. The `/add-new-employee` slash command does some stuff for Esbie that makes it easier to onboard new people.
* add_user_to_github_orgs: invoked by calling `/add-new-employee` with the `github` flag. Adds the specified GitHub user to a bunch of internal orgs.
* create_new_user_in_all_envs: invoked by calling `/add-new-employee` with the `transposit` flag. Adds the specified user to a bunch of internal workspaces.

## parsing

* parse_connector_url: Parse a Transposit URL (or a GitHub URL for a Transposit app) into its parts
* parse_github_url: Parse a GitHub URL into its parts
* parse_frontmatter: Parse a content string into a frontmatter attributes object and other content
* get_host_for_server: Given a short name like "demo," return the domain "console.demo.transposit.com."
* is_valid_url: stolen from the internet: is a string a valid URL?
* get_server_for_host: Given something like "console.demo.transposit.com", return the short name for the server.
* parse_mc_url: Parse a Mission Control URL into its parts
* parse_dependency: Parse a dependency line in manifest.json into {org, name, version}
* create_mc_url: Create an MC url out of an object of the form returned by parse_mc_url

## dc_helper

* create_connector_test: Create a stub test for a new connector.
* create_aws_connector: Create an AWS connector. Find service name and version at https://github.com/boto/botocore/tree/develop/botocore/data
* create_connector: Create a connector (and a raw connector, if is_smart is true) on all envs and at GitHub. Also create a stub test on demo.
* create_connector_webhook: webhook for workflows to run create_connector.
* list_tests: List all tests on this env by checking for all apps in the org "transposit-connector-tests."
* update_tests_on_prod: Run from a scheduled task to update all of the tests on prod.
* sync_myself_to_staging: Run this after changes have been made, so that the tests get updated on prod (the version on staging updates the tests on prod).
* update_internal_api: Run from a scheduled task to update the internal API every week.

### Syncing public connectors from dynamic_config:

Because some of the connectors have dependencies on raw connectors, we need to make sure we sequence the ones without dependencies before the ones with dependencies. Therefore, the scheduled task consists of a few operations, triggered by start_updating_public_connectors.

* start_updating_public_connectors: Run from a scheduled task to start updating all the connectors in dynamic_config.
* update_public_connectors: Loops over the list of connectors in dynamic_config, sorting them by dependency.
* update_services: Actually updates all of the services after we've sorted them by dependency.

## mc_helper

### Webhooks

* sync_workflow_slash_command: Webhook to sync a workflow to its repo in transposit-connectors at GitHub.
* sync_workflow_webhook: Webhook that is called back after the lambda portion of syncing workflows.
* publish_workflow_slash_command: Webhook that publishes a workflow from GitHub to the transposit workspace on all servers.
* fork_template_command: Webhook that forks a new workflow  or integrator, based on the template.
* validate_readme_slash_command: Webhook to validate the autogenerated readme created from a service's metadata.
* deploy_release_command: Webhook that takes a git tag for a service and deploys that tag on MC.
* sync_transposit_command: Webhook to sync any Transposit app from one place to another.
* fork_working_copy_slash_command: Webhook that forks a working copy of a global workflow/integrator into transposit-workflows on staging.
* make_dynamic_config_slash_command: Webhook that makes a pull request for dynamic_configuration (or at least starts a branch for one) that adds a new workflow or integrator to the global list.
* run_op_for_service_command: Webhook to generically run an mc_helper op on a service.

### Operations executed by webhooks

* sync_workflow: Push a workflow to GitHub, creating the repo if necessary.\n
* publish_workflow: Publish a workflow (or workflows) from GitHub to an org on every server.
* generate_frontmatter_for_service: Generates frontmatter from a workflow's metadata.
* fork_working_copy: Fork a copy of a workflow at GitHub to a workspace on an env. Defaults to transposit-workflows on staging.
* make_dynamic_config_pr: Make a pull request for dynamic_configuration (or at least starts a branch for one) that adds a new workflow or integrator to the global list.
* create_workflow_readmes_pr: Scheduled task to sweep up the workflow-readmes branch into a weekly pull request.
* sync_runbook: Sync a runbook from one workspace/env to another.

### Helper operations

* save_workflow_configs_to_github: Save MC configs to config.yml at GitHub.
* publish_github_workflow_configs: Publish MC configs stored at GitHub to the specified workflow on all servers.
* sync_public_services: Scheduled task to sync all public services from GitHub to all envs.
* create_readme: Given some frontmatter, create a README.md string.
* sync_documentation: Sync documentation from service metadata to both the README.md file and to the www repo, if the service is publicly listed.
* get_dynamic_config: Gets dynamic_config JSON object. Not completely sure why this is a separate version from the one in connector_utilities.
* sync_runbook_to_github: Sync a runbook to a GitHub repo named after the workspace at the branch named by the runbook's server.
* batch_operation_for_services: Perform an operation on an array of services. For now, only performs publish_github_workflow_configs.
* post_results_to_slack: Post results of an operation to Slack (by default, #test-integrations).

## mc_auditor

### Webhooks

* complete_audit_on_service: Runs audits on a service. Returns an object with keys for "message" and "success."
* run_audit_on_services: Takes in an array of services and runs an audit on them. Returns an object with keys for "message" and "success."
* list_audits: List all audits available on https://console.demo.transposit.com/dev/t/transposit/mc_auditor.
* list_results_for_audit: Return the results for a particular audit.

### Internal operations
* _collect_subaudits: Task that collects subtasks into one set of audit results.
* _start_run_op_for_all: Start an async run of an operation over all public workflows (or an array of workflows/integrators)
* _loop_run_op_for_all: Run an operation over all public workflows (or an array of workflows/integrators)
* _get_run_op_for_all: Get the results of an audit. If it's complete, delete the stash entry as well.
* _sync_run_op_for_all: Create a loop that runs an operation over a set of all services. Default set of services is all global workflows.

### Audits

* audit_template: template/test audit
* integrator_curl_error: Does the integrator leave a friendly slack message if it's curled with no body?
* is_blockkit_v2: Validates whether or not a service uses transposit/block_kit_lib:v2.*.*
* is_latest_version_listed: Is the latest version deployed listed in dynamic_config?
* sync_documentation: Sync documentation. This is a roundabout webhook to mc_helper's sync_documentation: more of an example than something to necessarily use.
* uses_new_bindings: Does this service use deprecated bindings?
* uses_slack_5: Validates whether or not a service uses current Slack connector (v5)
* uses_wf_log_done: Does this service use workflow.log.done?
* aws_override_checked: If an AWS connector is present, make sure the allow overrides box is checked.

