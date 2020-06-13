# Debugging our Website (www) and Docs

## Overview

Our website and docs are hosted in their own [repo](https://app.netlify.com/sites/transposit/overview). They are deployed by [Netlify](https://app.netlify.com/sites/transposit/overview). Members on the team with access to Netlify are Tina, Joseph, Yoko and Puya. The site is put together with [11ty](https://www.11ty.dev/) and [Tachyons](https://tachyons.io/) for CSS.

We auto-generated content for our connector, workflow, and integrator documentation.

## Connector docs

Nina is the best point of contact for our auto-generated connector docs. We have a [Static Docs Generator Lambda](https://github.com/transposit/staticDocsGenerator) that runs regularly to push changes to www using the autodocs generator [https://console.transposit.com/dev/t/transposit/autodocs_webhook/code/op/CreatePR]. **DAISIE/NINA, I don't see a scheduled task/know where this is run**

The main entry point within our codebase is `generateAllGlobalServiceMarkdown` within [ServiceResource.java](https://github.com/transposit/transposit/blob/master/java/server/web/src/main/java/web/api/v1/ServiceResource.java). This code calls into [MarkdownDocumentationGenerator.java](https://github.com/transposit/transposit/blob/master/java/server/web/src/main/java/web/MarkdownDocumentationGenerator.java) to do the heavy lifting.

## Workflow/Integrator docs

Daisie is the best point of contact for our workflow docs generator. We automatically generate README files for all workflows and integrators in [dynamic_configuration.yml](https://github.com/transposit/transposit/blob/master/java/server/common/src/main/resources/dynamic_configuration.yml). MC Helper has a [sync_documentation](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/sync_documentation) operation that regenerates a single README from a workflow's manifest. We have a weekly scheduled task [create_workflow_readmes_pr](https://console.demo.transposit.com/dev/t/transposit/mc_helper/code/op/create_workflow_readmes_pr) that generates a new PR in the [workflow-readmes](https://github.com/transposit/www/tree/workflow-readmes) branch with changes. We manually merge these PRs so that we can have a human sanity check for the website (which sadly lacks sufficient automated testing). Joseph is currently responsible to merge these changes.

Additional help:
* [Workflow/integrator docs aren't up to date](https://console.transposit.com/mc/t/transposit-eng/runbooks/public_docs_aren_t_up_to_date)
