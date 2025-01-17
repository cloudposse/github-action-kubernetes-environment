

<!-- markdownlint-disable -->
<a href="https://cpco.io/homepage"><img src="https://github.com/cloudposse/github-action-kubernetes-environment/blob/main/.github/banner.png?raw=true" alt="Project Banner"/></a><br/>
    <p align="right">
<a href="https://github.com/cloudposse/github-action-kubernetes-environment/releases/latest"><img src="https://img.shields.io/github/release/cloudposse/github-action-kubernetes-environment.svg" alt="Latest Release"/></a><a href="https://slack.cloudposse.com"><img src="https://slack.cloudposse.com/badge.svg" alt="Slack Community"/></a></p>
<!-- markdownlint-restore -->

<!--




  ** DO NOT EDIT THIS FILE
  **
  ** This file was automatically generated by the `cloudposse/build-harness`.
  ** 1) Make all changes to `README.yaml`
  ** 2) Run `make init` (you only need to do this once)
  ** 3) Run`make readme` to rebuild this file.
  **
  ** (We maintain HUNDREDS of open source projects. This is how we maintain our sanity.)
  **





-->

This repository wraps the environment information action, allowing it to be used as a replacement in support of various string functions and namespace standardization.




## Introduction

We often find when deploying with various environments that we need to standardize the namespace names. This repository wraps the environment information action, allowing it to be used as a replacement in support of various string functions and namespace standardization.

With this action, you can use pipe functions to standardize the namespace names, for example, to lowercase, or to replace a dash with an underscore you can use ` | kebabcase` or `| toLower`




## Usage


To use this action, you'll want to create a workflow and argocd repository.
This action is intended to replace cloudposse/github-action-yaml-config-query by wrapping it with helper actions.

With this action your `config` input can have several helper functions.

* `reformat` this replaces the namespace with a flavor of your choice. this is a key added to an environments configuration. See snipped below for example
  * `branch-name` will use the branch name as the namespace
  * `pr-number` will use the PR number as the namespace
* `| functions`: you can now perform simple string operations on keys in your environment configuration. This can help prevent dns invalid characters from becoming your namespace based on the branch name.
  * `| kebabcase` will convert the string to kebabcase (alternatively you can use `| toKebab` or `| kebab`)
  * `| lowercase` will convert the string to lowercase (alternatively you can use `| toLower` or `| lower`)
  * `| uppercase` will convert the string to uppercase (alternatively you can use `| toUpper` or `| upper`) Though this is perhaps less helpful as it is not valid in kubernetes nor dns.

```yaml
- name: Environment info
  # We recommend pinning this action to a specific release or version range to ensure stability
  uses: cloudposse/github-action-kubernetes-environment@main
  id: result
  with:
    environment: ${{ inputs.environment }}
    namespace: ${{ inputs.namespace }}
    application: ${{ inputs.application }}
    config: |
      preview:
        cluster: https://github.com/cloudposse/argocd-repo/blob/main/plat/ue2-dev/apps
        cluster-role: arn:aws:iam::123456789012:role/my-gha-cluster-role
        namespace: ${{ inputs.namespace }}
        reformat: branch-name # reformats namespace to be branch name as kebabcase, alternatively use `pr-number` here for `pr-123` as your namespace
        ssm-path: platform/dev-cluster
      qa1:
        cluster: https://github.com/cloudposse/argocd-repo/blob/main/plat/ue2-staging/apps
        cluster-role: arn:aws:iam::123456789012:role/my-gha-cluster-role
        namespace: QA1/MY-APP | kebabcase
        # output namespace will become qa1-my-app
        ssm-path: platform/staging-cluster
  ```

* To get custom key value pairs you can query the selected environment with a follow up step:
      
```yaml
- name: Environment info
uses: cloudposse/github-action-yaml-config-query@v1.0.0
id: environment-info
with:
  query: .
  config: ${{ steps.result.outputs.environment-config }}
```

<details><summary>  Full Workflow example:</summary>

```yaml
name: 'Environments - ArgoCD'
description: 'Get information about environment'
inputs:
  environment:
    description: "Environment name"
    required: true
  application:
    description: "The application name"
    required: false
  namespace:
    description: "Namespace name"
    required: true
outputs:
  name:
    description: "Environment name"
    value: ${{ inputs.environment }}
  region:
    description: "Default AWS Region"
    value: us-east-2
  role:
    description: "Environments that need to be deployed"
    value: ${{ steps.result.outputs.role }}
  cluster:
    description: "Environments that need to be destroyed"
    value: ${{ steps.result.outputs.cluster }}
  namespace:
    description: "Namespace"
    value: ${{ steps.result.outputs.namespace }}
  ssm-path:
    description: "Path to ssm secrets"
    value: ${{ steps.result.outputs.ssm-path }}

runs:
  using: "composite"
  steps:
    - name: Environment info
      # We recommend pinning this action to a specific release or version range to ensure stability
      uses: cloudposse/github-action-kubernetes-environment@main
      id: result
      with:
        environment: ${{ inputs.environment }}
        namespace: ${{ inputs.namespace }}
        application: ${{ inputs.application }}
        config: |
          preview:
            cluster: https://github.com/cloudposse/argocd-repo/blob/main/plat/ue2-dev/apps
            cluster-role: arn:aws:iam::123456789012:role/my-gha-cluster-role
            namespace: ${{ inputs.namespace }}
            ssm-path: platform/dev-cluster
            reformat: branch-name
          qa1:
            cluster: https://github.com/cloudposse/argocd-repo/blob/main/plat/ue2-staging/apps
            cluster-role: arn:aws:iam::123456789012:role/my-gha-cluster-role
            namespace: qa1
            ssm-path: platform/staging-cluster
          qa2:
            cluster: https://github.com/cloudposse/argocd-repo/blob/main/plat/ue2-staging/apps
            cluster-role: arn:aws:iam::123456789012:role/my-gha-cluster-role
            namespace: qa2
            ssm-path: platform/staging-cluster
          qa3:
            cluster: https://github.com/cloudposse/argocd-repo/blob/main/plat/ue2-staging/apps
            cluster-role: arn:aws:iam::123456789012:role/my-gha-cluster-role
            namespace: qa3
            ssm-path: platform/staging-cluster
          qa4:
            cluster: https://github.com/cloudposse/argocd-repo/blob/main/plat/ue2-staging/apps
            cluster-role: arn:aws:iam::123456789012:role/my-gha-cluster-role
            namespace: qa4
            ssm-path: platform/staging-cluster
          
          production:
            cluster: https://github.com/athoteldev/argocd-deploy-prod/blob/main/plat/ue2-prod/apps
            cluster-role: arn:aws:iam::123456789012:role/my-gha-cluster-role
            namespace: production
            ssm-path: platform/prod-cluster
          
          staging:
            cluster: https://github.com/cloudposse/argocd-repo/blob/main/plat/ue2-staging/apps
            cluster-role: arn:aws:iam::123456789012:role/my-gha-cluster-role
            namespace: staging
            ssm-path: platform/staging-cluster
          
          sandbox:
            cluster: https://github.com/cloudposse/argocd-repo/blob/main/plat/ue2-sandbox/apps
            cluster-role: arn:aws:iam::123456789012:role/my-gha-cluster-role
            namespace: sandbox
            ssm-path: platform/sandbox-cluster

          dev:
            cluster: https://github.com/cloudposse/argocd-repo/blob/main/plat/ue2-dev/apps
            cluster-role: arn:aws:iam::123456789012:role/my-gha-cluster-role
            namespace: dev  
            ssm-path: platform/dev-cluster
```

</details>






<!-- markdownlint-disable -->

## Inputs

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| application | The application name | N/A | false |
| config | configuration | N/A | true |
| environment | Environment | N/A | true |
| namespace | Kubernetes namespace | N/A | true |
| namespace-deny-list | Kubernetes namespace deny list, generated names cannot contain this comma separated list. | kube-system,kube-public,default | false |
| namespace-prefix | Kubernetes namespace prefix |  | false |
| namespace-suffix | Kubernetes namespace suffix |  | false |


## Outputs

| Name | Description |
|------|-------------|
| cluster | Environments that need to be destroyed |
| environment-config | Environment configuration |
| name | Environment name |
| namespace | Namespace |
| role | Environments that need to be deployed |
| ssm-path | Path to ssm secrets |
<!-- markdownlint-restore -->


## Related Projects

Check out these related projects.



## References

For additional context, refer to some of these links.

- [github-actions-workflows](https://github.com/cloudposse/github-actions-workflows) - Reusable workflows for different types of projects
- [example-github-action-release-workflow](https://github.com/cloudposse/example-github-action-release-workflow) - Example application with complicated release workflow




## ✨ Contributing

This project is under active development, and we encourage contributions from our community.



Many thanks to our outstanding contributors:

<a href="https://github.com/cloudposse/github-action-kubernetes-environment/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=cloudposse/github-action-kubernetes-environment&max=24" />
</a>

For 🐛 bug reports & feature requests, please use the [issue tracker](https://github.com/cloudposse/github-action-kubernetes-environment/issues).

In general, PRs are welcome. We follow the typical "fork-and-pull" Git workflow.
 1. Review our [Code of Conduct](https://github.com/cloudposse/github-action-kubernetes-environment/?tab=coc-ov-file#code-of-conduct) and [Contributor Guidelines](https://github.com/cloudposse/.github/blob/main/CONTRIBUTING.md).
 2. **Fork** the repo on GitHub
 3. **Clone** the project to your own machine
 4. **Commit** changes to your own branch
 5. **Push** your work back up to your fork
 6. Submit a **Pull Request** so that we can review your changes

**NOTE:** Be sure to merge the latest changes from "upstream" before making a pull request!

### 🌎 Slack Community

Join our [Open Source Community](https://cpco.io/slack?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/github-action-kubernetes-environment&utm_content=slack) on Slack. It's **FREE** for everyone! Our "SweetOps" community is where you get to talk with others who share a similar vision for how to rollout and manage infrastructure. This is the best place to talk shop, ask questions, solicit feedback, and work together as a community to build totally *sweet* infrastructure.

### 📰 Newsletter

Sign up for [our newsletter](https://cpco.io/newsletter?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/github-action-kubernetes-environment&utm_content=newsletter) and join 3,000+ DevOps engineers, CTOs, and founders who get insider access to the latest DevOps trends, so you can always stay in the know.
Dropped straight into your Inbox every week — and usually a 5-minute read.

### 📆 Office Hours <a href="https://cloudposse.com/office-hours?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/github-action-kubernetes-environment&utm_content=office_hours"><img src="https://img.cloudposse.com/fit-in/200x200/https://cloudposse.com/wp-content/uploads/2019/08/Powered-by-Zoom.png" align="right" /></a>

[Join us every Wednesday via Zoom](https://cloudposse.com/office-hours?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/github-action-kubernetes-environment&utm_content=office_hours) for your weekly dose of insider DevOps trends, AWS news and Terraform insights, all sourced from our SweetOps community, plus a _live Q&A_ that you can’t find anywhere else.
It's **FREE** for everyone!
## License

<a href="https://opensource.org/licenses/Apache-2.0"><img src="https://img.shields.io/badge/License-Apache%202.0-blue.svg?style=for-the-badge" alt="License"></a>

<details>
<summary>Preamble to the Apache License, Version 2.0</summary>
<br/>
<br/>

Complete license is available in the [`LICENSE`](LICENSE) file.

```text
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

  https://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
```
</details>

## Trademarks

All other trademarks referenced herein are the property of their respective owners.


---
Copyright © 2017-2025 [Cloud Posse, LLC](https://cpco.io/copyright)


<a href="https://cloudposse.com/readme/footer/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/github-action-kubernetes-environment&utm_content=readme_footer_link"><img alt="README footer" src="https://cloudposse.com/readme/footer/img"/></a>

<img alt="Beacon" width="0" src="https://ga-beacon.cloudposse.com/UA-76589703-4/cloudposse/github-action-kubernetes-environment?pixel&cs=github&cm=readme&an=github-action-kubernetes-environment"/>
