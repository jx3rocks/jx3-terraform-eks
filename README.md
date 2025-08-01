# EKS Terraform Quickstart template

Use this template to easily create a new Git Repository for managing Jenkins X cloud infrastructure needs.

We recommend using Terraform to manage the infrastructure needed to run Jenkins X. 
There can be a number of cloud resources which need to be created such as:
- Kubernetes cluster
- Storage buckets for long term storage of logs
- IAM Bindings to manage permissions for applications using cloud resources

Jenkins X likes to use GitOps to manage the lifecycle of both infrastructure and cluster resources.  
This requires two git Repositories to achieve this:
- the first, infrastructure resources will be managed by Terraform and will keep resourecs in sync.
- the second, the Kubernetes specific cluster resources will be managed by Jenkins X and keep resources in sync.

# Prerequisites

- A Git organisation that will be used to create the GitOps repositories used for Jenkins X below.
  e.g. https://github.com/organizations/plan.
- Create a git bot user (different from your own personal user)
  e.g. https://github.com/join
  and generate a a personal access token, this will be used by Jenkins X to interact with git repositories.
  e.g. https://github.com/settings/tokens/new?scopes=repo,read:user,read:org,user:email,write:repo_hook,delete_repo,admin:repo_hook

- __This bot user needs to have write permission to write to any git repository used by Jenkins X.  This can be done by adding the bot user to the git organisation level or individual repositories as a collaborator__
  Add the new `bot` user to your Git Organisation, for now give it Owner permissions, we will reduce this to member permissions soon.
- Install `terraform` CLI - [see here](https://learn.hashicorp.com/tutorials/terraform/install-cli#install-terraform)
- Install `jx` CLI - [see here](https://github.com/jenkins-x/jx-cli/releases)
- AWS account and credentials

# Git repositories

We use 2 git repositories:

* **Infrastructure** git repository for the Terraform configuration to setup/upgrade/modify your cloud infrastructure (kubernetes cluster, IAM accounts, IAM roles, buckets etc)
* **Cluster** git repository to contain the `helmfile.yaml` file to define the helm charts to deploy in your cluster

We use separate git repositories since the infrastructure tends to change rarely; whereas the cluster git repository changes alot (every time you add a new quickstart, import a project, release a project etc).

Often different teams look after infrastructure; or you may use tools like Terraform Cloud to process changes to infrastructure & review changes to infrastructure more closely than promotion of applications.

# Getting started

__Note: remember to create the Git repositories below in your Git Organisation rather than your personal Git account else this will lead to issues with ChatOps and automated registering of webhooks__

1. Create and clone your **Infrastructure** git repository from this GitHub Template https://github.com/jx3-gitops-repositories/jx3-terraform-eks/generate
2. Create a **Cluster** git repository from this template https://github.com/jx3-gitops-repositories/jx3-eks-vault/generate
3. Override the variable defaults in the **Infrastructure** repository. (E.g, edit `variables.tf`, set `TF_VAR_` environment variables, or pass the values on the terraform command line.)
 * `cluster_version`: Kubernetes version for the EKS cluster.
 * `region`: AWS region code for the AWS region to create the cluster in.
 * `jx_git_url`: URL of the **Cluster** repository.
 * `jx_bot_username`: The username of the git bot user
4. (Optional) Depending on how you want to authenticate with AWS you might want to adjust the AWS provider block in main.tf according to [this documentation](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#authentication-and-configuration).
5. commit and push any changes to your **Infrastructure** git repository:

```sh
git commit -a -m "fix: configure cluster repository and project"
git push
```

6. Define an environment variable to pass the bot token into Terraform:

```sh
export TF_VAR_jx_bot_token=my-bot-token
```

7. Now, initialise, plan and apply Terraform:

```sh
terraform init
```

```sh
terraform plan
```

```sh
terraform apply
```
Tail the Jenkins X installation logs
```
$(terraform output follow_install_logs)
```
Once finished you can now move into the Jenkins X Developer namespace

```sh
jx ns jx
```

and create or import your applications

```sh
jx project
```

If your application is not yet in a git repository, you are asked for a github.com user and token to push the application to git. This user needs administrative permissions to create repository and hooks. It is likely not the same user as the bot user mentioned above.

## Terraform

For the full list of terraform inputs in the eks-jx module se [see the documentation for jenkins-x/terraform-aws-eks-jx](https://github.com/jenkins-x/terraform-aws-eks-jx#inputs)

<!-- BEGIN_TF_DOCS # Autogenerated do not edit! -->
### Providers

| Name | Version |
|------|---------|
| <a name="provider_aws"></a> [aws](#provider\_aws) | 5.73.0 |
| <a name="provider_random"></a> [random](#provider\_random) | 3.6.3 |
### Modules

| Name | Source | Version |
|------|--------|---------|
| <a name="module_eks"></a> [eks](#module\_eks) | terraform-aws-modules/eks/aws | 20.37.2 |
| <a name="module_eks-jx"></a> [eks-jx](#module\_eks-jx) | github.com/jenkins-x/terraform-aws-eks-jx | v4.0.0 |
| <a name="module_iam-assumable-role-ebs-csi"></a> [iam-assumable-role-ebs-csi](#module\_iam-assumable-role-ebs-csi) | terraform-aws-modules/iam/aws//modules/iam-assumable-role-with-oidc | ~> v3.8.0 |
| <a name="module_vpc"></a> [vpc](#module\_vpc) | terraform-aws-modules/vpc/aws | 5.9.0 |
### Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_ami_type"></a> [ami\_type](#input\_ami\_type) | ami type for the node group worker intances | `string` | `"AL2023_x86_64_STANDARD"` | no |
| <a name="input_cluster_endpoint_private_access"></a> [cluster\_endpoint\_private\_access](#input\_cluster\_endpoint\_private\_access) | Indicates whether or not the Amazon EKS private API server endpoint is enabled. | `bool` | `false` | no |
| <a name="input_cluster_endpoint_public_access"></a> [cluster\_endpoint\_public\_access](#input\_cluster\_endpoint\_public\_access) | Indicates whether or not the Amazon EKS public API server endpoint is enabled. | `bool` | `true` | no |
| <a name="input_cluster_in_private_subnet"></a> [cluster\_in\_private\_subnet](#input\_cluster\_in\_private\_subnet) | Flag to enable installation of cluster on private subnets | `bool` | `true` | no |
| <a name="input_cluster_name"></a> [cluster\_name](#input\_cluster\_name) | Name of the Kubernetes cluster to create | `string` | `""` | no |
| <a name="input_cluster_version"></a> [cluster\_version](#input\_cluster\_version) | Kubernetes version to use for the EKS cluster. | `string` | n/a | yes |
| <a name="input_enable_nat_gateway"></a> [enable\_nat\_gateway](#input\_enable\_nat\_gateway) | n/a | `bool` | `true` | no |
| <a name="input_force_destroy"></a> [force\_destroy](#input\_force\_destroy) | Flag to determine whether storage buckets get forcefully destroyed. If set to false, empty the bucket first in the aws s3 console, else terraform destroy will fail with BucketNotEmpty error | `bool` | `false` | no |
| <a name="input_jx_bot_token"></a> [jx\_bot\_token](#input\_jx\_bot\_token) | Bot token used to interact with the Jenkins X cluster git repository | `string` | n/a | yes |
| <a name="input_jx_bot_username"></a> [jx\_bot\_username](#input\_jx\_bot\_username) | Bot username used to interact with the Jenkins X cluster git repository | `string` | n/a | yes |
| <a name="input_jx_git_url"></a> [jx\_git\_url](#input\_jx\_git\_url) | URL for the Jenins X cluster git repository | `string` | n/a | yes |
| <a name="input_nginx_chart_version"></a> [nginx\_chart\_version](#input\_nginx\_chart\_version) | nginx chart version | `string` | `"3.12.0"` | no |
| <a name="input_node_machine_type"></a> [node\_machine\_type](#input\_node\_machine\_type) | n/a | `string` | `"m6i.large"` | no |
| <a name="input_private_subnets"></a> [private\_subnets](#input\_private\_subnets) | n/a | `list(string)` | <pre>[<br>  "10.0.4.0/24",<br>  "10.0.5.0/24",<br>  "10.0.6.0/24"<br>]</pre> | no |
| <a name="input_public_subnets"></a> [public\_subnets](#input\_public\_subnets) | n/a | `list(string)` | <pre>[<br>  "10.0.1.0/24",<br>  "10.0.2.0/24",<br>  "10.0.3.0/24"<br>]</pre> | no |
| <a name="input_region"></a> [region](#input\_region) | AWS region code for creating resources. | `string` | n/a | yes |
| <a name="input_single_nat_gateway"></a> [single\_nat\_gateway](#input\_single\_nat\_gateway) | n/a | `bool` | `true` | no |
| <a name="input_use_asm"></a> [use\_asm](#input\_use\_asm) | Flag to specify if resources for AWS Secret MAanger should be created instead of vault resources | `bool` | `false` | no |
| <a name="input_vpc_cidr_block"></a> [vpc\_cidr\_block](#input\_vpc\_cidr\_block) | n/a | `string` | `"10.0.0.0/16"` | no |
| <a name="input_vpc_name"></a> [vpc\_name](#input\_vpc\_name) | VPC | `string` | `"tf-vpc-eks"` | no |
### Outputs

| Name | Description |
|------|-------------|
| <a name="output_cert_manager_iam_role"></a> [cert\_manager\_iam\_role](#output\_cert\_manager\_iam\_role) | The IAM Role that the Cert Manager pod will assume to authenticate |
| <a name="output_cluster_autoscaler_iam_role"></a> [cluster\_autoscaler\_iam\_role](#output\_cluster\_autoscaler\_iam\_role) | The IAM Role that the Jenkins X UI pod will assume to authenticate |
| <a name="output_cluster_name"></a> [cluster\_name](#output\_cluster\_name) | The name of the created cluster |
| <a name="output_cluster_oidc_issuer_url"></a> [cluster\_oidc\_issuer\_url](#output\_cluster\_oidc\_issuer\_url) | The Cluster OIDC Issuer URL |
| <a name="output_cm_cainjector_iam_role"></a> [cm\_cainjector\_iam\_role](#output\_cm\_cainjector\_iam\_role) | The IAM Role that the CM CA Injector pod will assume to authenticate |
| <a name="output_controllerbuild_iam_role"></a> [controllerbuild\_iam\_role](#output\_controllerbuild\_iam\_role) | The IAM Role that the ControllerBuild pod will assume to authenticate |
| <a name="output_docs"></a> [docs](#output\_docs) | Follow Jenkins X 3.x alpha docs for more information |
| <a name="output_external_dns_iam_role"></a> [external\_dns\_iam\_role](#output\_external\_dns\_iam\_role) | The IAM Role that the External DNS pod will assume to authenticate |
| <a name="output_follow_install_logs"></a> [follow\_install\_logs](#output\_follow\_install\_logs) | Follow Jenkins X install logs |
| <a name="output_lts_logs_bucket"></a> [lts\_logs\_bucket](#output\_lts\_logs\_bucket) | The bucket where logs from builds will be stored |
| <a name="output_lts_reports_bucket"></a> [lts\_reports\_bucket](#output\_lts\_reports\_bucket) | The bucket where test reports will be stored |
| <a name="output_lts_repository_bucket"></a> [lts\_repository\_bucket](#output\_lts\_repository\_bucket) | The bucket that will serve as artifacts repository |
| <a name="output_tekton_bot_iam_role"></a> [tekton\_bot\_iam\_role](#output\_tekton\_bot\_iam\_role) | The IAM Role that the build pods will assume to authenticate |
<!-- BEGIN_TF_DOCS -->

# Cleanup

To remove any cloud resources created here:

* Manually remove the generated load balancer, for example, through the AWS EC2 console "Load Balancers" tab. The load balancer is currently not cleaned up automatically and may cause the following destroy step to hang and finally fail.
* Run:
```sh
terraform destroy
```

# Contributing

When adding new variables please regenerate the markdown table
```sh
terraform-docs markdown table .
```
and replace the Inputs section above

## Formatting

When developing please remember to format codebase before raising a pull request
```sh
terraform fmt -check -diff -recursive
```
