# Infrastructure-kube-argocd

A GitOps-based infrastructure repository for bootstrapping a fully production-ready Kubernetes cluster with a single command. Once ArgoCD is installed, this repo takes care of deploying the entire platform layer automatically.

## Description

This repository contains all the core services needed to turn a fresh Kubernetes cluster into a production-ready platform. Everything is declarative, managed by ArgoCD, and version-controlled through Git.

### Components Deployed

* **Rook-Ceph** — Distributed storage
* **Infisical** — Secret management
* **Cert-Manager** — Automated TLS certificates
* **Ingress-Nginx** — HTTP/HTTPS ingress controller
* **MetalLB** — Bare-metal load balancer
* **Kargo** — Multi-stage GitOps promotions
* **Prometheus + Grafana** — Observability stack
* **Platform Ingresses** — Pre-configured ingress routes for all services

> ⚠️ Some YAML files require adjustment to match your local environment. See the configuration section below.

## Configuration

Before deploying, review and adjust the following files to match your environment.

### Rook-Ceph

```bash
addons/rook-ceph/cluster-release.yaml
```

Configure the Helm values under `storage`. You can either:

* Let Rook-Ceph **auto-detect** available disks, or
* **Manually specify** the nodes and disks to use for the Ceph cluster

### MetalLB Address Pool

```bash
addons/metallb-pool/address-pool.yaml
```

Set `spec.addresses` to match the IP range you want MetalLB to assign to `LoadBalancer` services.

### Infisical

```bash
addons/infisical/infisical-secrets.yaml
```

Update the following values:

* `AUTH_SECRET`
* `ENCRYPTION_KEY`

Refer to the [Infisical documentation](https://infisical.com/docs) for guidance on generating these securely.

### Kargo

```bash
infrastructure/11-kargo.yaml
```

Update the following values:

* `passwordHash`
* `tokenSigningKey`

Refer to the [Kargo documentation](https://docs.kargo.io/) for instructions on generating both values correctly.

### Platform Ingresses

```bash
ingress/*.yaml
```

Update `spec.rules.host` in each ingress manifest to match the hostnames used in your environment.

## Quick Start

Once all configuration values are set:

```bash
kubectl apply -f bootstrap/root-app.yaml
```

ArgoCD will pick up the root application and recursively sync all platform components.

## Contributing

Issues and pull requests are welcome. If you run into problems or have suggestions, feel free to open an issue.