# Debugging our Website (www) and Docs

## Overview

Our website and docs are hosted in their own [repo](https://app.netlify.com/sites/transposit/overview). They are deployed by [Netlify](https://app.netlify.com/sites/transposit/overview). Members on the team with access to Netlify are Tina, Joseph, Yoko and Puya. The site is put together with [11ty](https://www.11ty.dev/) and [Tachyons](https://tachyons.io/) for CSS.

We auto-generated content for our connector, workflow, and integrator documentation.

## Connector docs

Nina is the best point of contact for our auto-generated connector docs. We have a [Static Docs Generator Lambda](https://github.com/transposit/staticDocsGenerator) that runs regularly to push changes to www using the autodocs generator [https://console.transposit.com/dev/t/transposit/autodocs_webhook/code/op/CreatePR]. **DAISIE/NINA, I don't see a scheduled task/know where this is run**

The main entry point within our codebase is `generateAllGlobalServiceMarkdown` within [ServiceResource.java](https://github.com/transposit/transposit/blob/master/java/server/web/src/main/java/web/api/v1/ServiceResource.java). This code calls into [MarkdownDocumentationGenerator.java](https://github.com/transposit/transposit/blob/master/java/server/web/src/main/java/web/MarkdownDocumentationGenerator.java) to do the heavy lifting.

## Workflow/Integrator docs


