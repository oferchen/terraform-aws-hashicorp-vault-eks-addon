# HashiCorp Vault Add-on for AWS EKS

> Deploy HashiCorp Vault to AWS EKS Clusters with this add-on.

## Table of Contents

- [HashiCorp Vault Addon for AWS EKS](#hashicorp-vault-addon-for-aws-eks)
  - [Table of Contents](#table-of-contents)
  - [Usage](#usage)
  - [Author Information](#author-information)
  - [License](#license)

## Usage

If you would like to override any defaults with the chart, you can do so by passing the `helm_config` variable.

For a more extensive example, see [blueprints/getting-started](./blueprints/getting-started/).

### Unsealing Vault

Once the add-on has been deployed, you can unseal the Vault server using the following commands.

> You will need to be in the `vault` (Kubernetes) namespace while running these commands, by default

First, initialize the Vault server. This will return [unseal keys](https://www.vaultproject.io/docs/concepts/seal#seal-unseal) and [root token](https://www.vaultproject.io/docs/concepts/tokens#root-tokens). Store this in a safe place since you will use them to unseal the Vault server.

```sh
kubectl exec -it vault-0 -n vault -- vault operator init
```

Next, unseal the Vault server by providing at least _3_ of these keys to unseal Vault before servicing requests.

```sh
kubectl exec -it vault-0 -n vault -- vault operator unseal <key 1>
kubectl exec -it vault-0 -n vault -- vault operator unseal <key 2>
kubectl exec -it vault-0 -n vault -- vault operator unseal <key 3>
 ```

Confirm that the Vault server is unsealed by checking the status of the Vault server.

```sh
kubectl get pods -n vault | grep vault
```

This command will return a similar output to the following.

```sh

NAME                 | READY | STATUS  | RESTARTS | AGE
---------------------|-------|---------|----------|-----
vault-0              |  1/1  | Running | 0        | 28m
vault-agent-injector |  1/1  | Running | 0        | 1m
```

At this point, you can use Vault to store, access and deploy secrets to your application workloads.

Refer to [this guide](https://learn.hashicorp.com/tutorials/vault/getting-started-first-secret?in=vault/getting-started) for a detailed overview on how to get started.

<!-- BEGIN_TF_DOCS -->
### Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| helm_config | HashiCorp Vault Helm chart configuration | `any` | `{}` | no |
| manage_via_gitops | Determines if the add-on should be managed via GitOps | `bool` | `false` | no |

### Outputs

| Name | Description |
|------|-------------|
| argocd_gitops_config | Configuration used for managing the add-on with ArgoCD |
| merged_helm_config | (merged) Helm Config for HashiCorp Vault |
<!-- END_TF_DOCS -->

## Author Information

This repository is maintained by the contributors listed on [GitHub](https://github.com/hashicorp/terraform-aws-hashicorp-vault-eks-addon/graphs/contributors).

## License

Licensed under the Apache License, Version 2.0 (the "License").

You may obtain a copy of the License at [apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0).

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an _"AS IS"_ basis, without WARRANTIES or conditions of any kind, either express or implied.

See the License for the specific language governing permissions and limitations under the License.
