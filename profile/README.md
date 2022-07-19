## Overview

This org exists to provide easy-to-use [Kustomize-based](https://kustomize.io/) GitOps deployment tools for repositories containing multiple deploy environments. This is meant to be accomplished via the techniques of promoting built container image shas from development repos and using "rendered YAML branches" for GitOps deployment.

To get started, look at the [guestbook-deploy](https://github.com/kustomize-everything/guestbook-deploy) repository which deploys the [guestbook](https://github.com/kustomize-everything/guestbook) app.

### Github Actions

- [action-env-build-and-deploy](https://github.com/kustomize-everything/action-env-build-and-deploy)
- [action-promote](https://github.com/kustomize-everything/action-promote)
- [action-kustomize](https://github.com/kustomize-everything/action-kustomize)

## What are "rendered YAML branches"?

The technique of using "rendered YAML branches" removes the responsibility of config templating from your deployment tool and gives it to the CI/CD pipeline. For example, the [action-env-build-and-deploy GitHub Action](https://github.com/kustomize-everything/guestbook-deploy/blob/main/.github/workflows/render-manifests.yml) automates the config management templating (e.g. `kustomize build`) such that fully rendered Kubernetes manifests are outputted to an environment specific branch (e.g. `env/stage`, `env/prod`). The deployment tool is then configured to deploy the manifests from the **environment branch**, as opposed to a directory in the `main` branch.

An example application source repository is located at https://github.com/kustomize-everything/guestbook and has a [CI/CD Pipeline](https://github.com/kustomize-everything/guestbook/blob/main/.github/workflows/ci-cd.yml) which builds new container images and automatically commits the new image tags to the [kustomize environments](https://github.com/kustomize-everything/guestbook-deploy/tree/main/env) contained in the [guestbook-deploy](https://github.com/kustomize-everything/guestbook-deploy) repository.

## Why this approach?

### Advantages

* Easily understandable change history/diff - change is not obfuscated by config tooling
* Use different policies per environment - e.g. automated commit/deployment to dev/stage, PR approval process and protected branch for prod
* Upgrading your deployment tool and baked-in toolchain (kustomize) is no longer a risk - templating done in CI, not by the deployment tool
* Better security - No longer at risk from vulnerabilities in tooling (helm, kustomize)
* Safer change management - Change to a kustomize base will not immediately affect all environments
* Improved deployment tool performance - expensive templating process (kustomize build) is no longer performed by the deployment tool

### Disadvantages

* Additional CI automation requirements (e.g. GitHub action)
* Does not support tools which render plain-text secrets (e.g. Kustomize + SOPS)
