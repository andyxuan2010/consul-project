# Consul project on Amazon EKS

This repository provisions an Amazon VPC and EKS cluster with Terraform, then
provides Kubernetes manifests and Consul Helm values for service-mesh testing.
The examples use the Google microservices demo workloads and Consul Connect
injection, peering, and mesh gateways.

## Pipeline used

The GitHub Pages workflow publishes the repository's static documentation on
pushes to `main`. There is no automated AWS or Kubernetes deployment workflow;
Terraform and `kubectl` operations are intentionally operator-controlled.

## Usage

Prerequisites are AWS CLI credentials, Terraform, `kubectl`, and Helm. From the
`terraform/` directory, configure the region, cluster, and subnet variables,
then review and apply the infrastructure:

```bash
terraform init
terraform fmt -check
terraform validate
terraform plan
terraform apply
```

Configure `kubectl` for the new cluster and install Consul with the checked-in
values:

```bash
aws eks update-kubeconfig --region <region> --name <cluster-name>
helm repo add hashicorp https://helm.releases.hashicorp.com
helm repo update
helm install consul hashicorp/consul --namespace consul --create-namespace \
  --values ../kubernetes/consul-values.yaml
kubectl apply -f ../kubernetes/config.yaml
kubectl apply -f ../kubernetes/config-consul.yaml
kubectl apply -f ../kubernetes/consul-mesh-gateway.yaml
```

Review the public EKS endpoint, security-group rules, and AWS costs before
applying. Do not commit access keys; prefer an AWS profile or role.
