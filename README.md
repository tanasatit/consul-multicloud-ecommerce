# Multi-Cloud E-Commerce with Consul Service Mesh

## Project Overview
This project deploys Google Cloud Online Boutique (microservices e-commerce app)
across two Kubernetes clusters on different cloud providers using HashiCorp Consul
as the service mesh for service discovery, mTLS, and failover.

- Cluster 1: AWS EKS (Amazon Web Services)
- Cluster 2: Linode LKE (Akamai Cloud)

## Original Authors (Acknowledged)
- GCP Online Boutique: https://github.com/GoogleCloudPlatform/microservices-demo
  Authors: Google Cloud Platform Team
- Consul Crash Course: https://gitlab.com/twn-youtube/consul-crash-course
  Author: TechWorld with Nana (Nana Janashia)
  Video: https://www.youtube.com/watch?v=s3I1kKKfjtQ

## Architecture
- AWS EKS = Primary cluster (Consul servers + full Online Boutique app)
- Linode LKE = Secondary cluster (Consul clients + failover)
- Cluster Peering between EKS and LKE via Consul mesh gateways
- mTLS enforced between all services

## Tools Used
- AWS EKS: Kubernetes cluster on Amazon cloud
- Linode LKE: Kubernetes cluster on Linode cloud
- Terraform: Provision EKS cluster as code
- HashiCorp Consul: Service mesh (discovery, mTLS, peering, failover)
- Helm: Deploy Consul onto clusters
- kubectl: Manage and inspect clusters

## Repository Structure
- terraform/         Terraform files to provision AWS EKS cluster
- k8s-manifests/     Kubernetes manifests (Online Boutique + Consul configs)
- docs/screenshots/  Evidence screenshots of working system

## Deployment Steps

### 1. Create EKS Cluster with Terraform
cd terraform
terraform init
terraform apply --auto-approve

### 2. Configure kubectl for EKS
aws eks update-kubeconfig --name myapp-eks-cluster --region eu-west-3

### 3. Deploy Online Boutique on EKS
kubectl apply -f k8s-manifests/kubernetes-manifests.yaml

### 4. Deploy Consul on EKS
helm install consul hashicorp/consul \
  --values k8s-manifests/consul-values.yaml \
  --namespace consul --create-namespace

### 5. Create Linode LKE cluster via Linode Console
Download kubeconfig and merge with local kubectl config

### 6. Deploy Consul on LKE
kubectl config use-context lke
helm install consul hashicorp/consul \
  --values k8s-manifests/consul-values.yaml \
  --namespace consul --create-namespace

### 7. Configure Cluster Peering
kubectl apply -f k8s-manifests/config-consul.yaml
kubectl apply -f k8s-manifests/exported-service.yaml

### 8. Configure Failover
kubectl apply -f k8s-manifests/service-resolver.yaml

### 9. Configure Service Intentions
kubectl apply -f k8s-manifests/intentions.yaml

## Evidence
See docs/screenshots/ folder for all screenshots showing the working system.
