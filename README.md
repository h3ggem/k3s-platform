# K3s Platform Lab

## Overview

This repository contains a personal exploration project focused on building and operating a small, production-inspired Kubernetes platform with K3s.

The project is running in a Proxmox homelab and is designed as a practical learning environment for Kubernetes operations, GitOps, observability, and failure recovery. The goal is not to reproduce a large enterprise platform, but to understand the core concepts by building, testing, breaking, and repairing a complete system.

## Platform

The current environment consists of:

- One K3s server node
- Two K3s agent nodes
- Ubuntu Server virtual machines hosted on Proxmox VE
- Traefik for ingress and HTTP routing
- Argo CD for GitOps-based delivery
- Prometheus and Grafana for observability
- A containerized Nginx demo application with three replicas

The demo application includes:

- Kubernetes Deployment, Service, and Ingress resources
- Readiness and liveness probes
- CPU and memory requests and limits
- Replica distribution across the cluster nodes
- Host-based routing through Traefik

## GitOps Workflow

After the initial bootstrap, Kubernetes resources are managed through Git wherever practical:

```text
GitHub change
      ↓
Argo CD detects the change
      ↓
Kubernetes desired state is updated
      ↓
Prometheus and Grafana verify platform health
```

Argo CD is configured to:

- Synchronize applications automatically
- Remove resources deleted from Git
- Correct manual configuration drift
- Retry failed synchronization attempts with exponential backoff

The initial K3s and Argo CD installation were bootstrap activities. Application and platform configuration are subsequently represented as version-controlled manifests and deployed through Argo CD.

## Repository Structure

```text
apps/
└── demo/
    ├── deployment.yaml
    ├── ingress.yaml
    ├── namespace.yaml
    └── service.yaml

clusters/
└── k3s-platform/
    ├── demo-application.yaml
    ├── observability-application.yaml
    ├── platform-access-application.yaml
    └── platform-access/
        ├── argocd-cmd-params.yaml
        ├── argocd-ingress.yaml
        └── grafana-ingress.yaml

docs/
└── Project documentation and test notes
```

## Observability

The observability stack is deployed with the Prometheus Community `kube-prometheus-stack` Helm chart through Argo CD.

Prometheus and Grafana are used to investigate questions such as:

- Are all cluster nodes available?
- Are the expected application replicas running?
- Are workloads passing their health checks?
- Is the platform recovering after a failure?
- Does a deployment remain available during a rolling update?

The project also documents K3s-specific monitoring considerations, including unsupported or unavailable control-plane metrics endpoints.

## Failure Testing

The platform is intentionally tested under failure conditions to validate Kubernetes recovery and GitOps behavior. Planned tests include:

- Pod deletion and automatic replica recovery
- Agent-node failure and workload behavior
- Invalid image release and `ImagePullBackOff` diagnosis
- Manual configuration drift and Argo CD self-healing
- Rolling updates while the application remains available
- Blind sabotage rounds where the fault must be diagnosed from symptoms

Each test is recorded with its symptom, investigation, hypothesis, root cause, remediation, and recovery time.

## Learning Approach

AI tools and official documentation are used as learning assistance during the project. AI helps with explanations, troubleshooting suggestions, YAML review, and identifying areas to investigate. All configuration is reviewed, tested, and explained manually before it is considered part of the project.

The emphasis is on understanding why the platform works, not simply producing a working deployment.

## Scope and Limitations

This is a focused homelab project. It intentionally does not include:

- Distributed storage
- Databases
- Service mesh
- Private container registry
- CI pipeline implementation
- Full control-plane high availability

These exclusions keep the project small enough to understand while still covering meaningful platform engineering practices.

## Project Goals

The project is intended to demonstrate practical experience with:

- Kubernetes resource design
- K3s cluster administration
- GitOps workflows with Argo CD
- Helm-based platform installation
- Ingress and service routing
- Prometheus and Grafana observability
- Failure analysis and recovery
- Configuration drift detection
- Technical documentation and operational discipline

## Status

This repository is an active learning project. Configuration, test results, and documentation will evolve as the platform is extended and challenged.
