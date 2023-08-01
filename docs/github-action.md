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
