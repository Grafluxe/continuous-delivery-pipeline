# Continuous Delivery Pipeline

This document was written to work with my
[Git Continuous Integration Workflow](https://github.com/Grafluxe/git-continuous-integration-workflow#readme),
but can be tweaked to fit other flows as needed.

## Contents

- [Git Branch Definitions](#git-branch-definitions)
- [Deployment Environments](#deployment-environments)
- [Development Flow](#development-flow)
- [Additional Resources](#additional-resources)

## Git Branch Definitions

`master` - Represents production ready code which should always be deployable.

`integration` - Used for testing and preventing conflicts when integrating multiple feature/hotfix branches.

## Deployment Environments

**TEST**

- Environment where QA testing occurs (via manual and automated techniques).
- Continuous Delivery tool *auto builds* off HEAD of the `integration` branch.
  - If desired, builds can also be made off any commit via *manual trigger*.

**STAG**

- Environment where prerelease and performance testing occurs. This environment mirrors **PROD** in regards to hardware
  and configurations.
- Continuous Delivery tool *auto builds* off HEAD of the `master` branch.

**PROD**

- Environment where the public-facing application lives. The production/live server.
- Continuous Delivery tool builds off latest tag of the `master` branch (*manual trigger* required).

**EXPR**

- Environment where experimental logic can be tested.
- Continuous Delivery tool can build off any commit (*manual trigger* required).

## Development Flow

In this example, we assume Jira is used for project management and GitLab is used for Continuous Delivery. Detailed
testing practices (i.e. unit testing, load testing) are excluded from this document.

![dev-flow](dev-flow.svg)

**Example Where Tests Pass**

1. Developer creates/is assigned a ticket via Jira.
1. Developer checks out new local branch from `master` to work from (e.g. `feat/cors-support`).
1. Developer completes relevant work, merges branch to `integration` and pushes changes to the remote server.
1. GitLab *auto builds* off the HEAD of `integration` and deploys to **TEST**.
1. Developer assigns ticket to QA team via Jira.
1. QA team reviews changes on **TEST**.
1. QA team sets ticket status to "done" in Jira.
1. Developer merges local branch to `master`, deletes the no longer needed local branch and pushes changes to the
   remote server.
1. GitLab *auto builds* off the HEAD of `master` and deploys to **STAG**.
1. Repeat steps 1–9 for all needed features/hotfixes.
1. Once next release is scheduled, developer creates a release commit (along with an annotated tag) on `master` and
   pushes to the remote server.
1. GitLab *auto builds* off the HEAD of `master` and deploys to **STAG**.
1. QA team does a final prerelease review on **STAG**.
1. QA team green lights release.
1. Application is deployed to **PROD** via *manual trigger* off the latest tag in GitLab.

## Additional Resources

- [Deployment Environment Wiki](https://en.wikipedia.org/wiki/Deployment_environment)
- [Software Development Practice by Peter Murray](https://dltj.org/article/software-development-practice/) (ignore
  "Moving Between Tiers" section)
- [Continuous Delivery by Jez Humble](https://www.youtube.com/watch?v=skLJuksCRTw)
- [Continuous Delivery by Martin Fowler](https://www.youtube.com/watch?v=aoMfbgF2D_4)
- [CI vs CD](https://www.atlassian.com/continuous-delivery/ci-vs-ci-vs-cd)
