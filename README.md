# GPT-OSS GitOps PoC

Example repository of a Gitops GPT-OSS Deployment using ArgoCD, Kustomize, and vLLM on OpenShift.

## Overview

This repository deploys the `openai/gpt-oss-20b` model using vLLM as an OpenAI-compatible API server. The deployment is managed through GitOps practices using ArgoCD and Kustomize for environment-specific configurations.

> **IMPORTANT**: This is a Proof of Concept (PoC) and is intended for demonstration and learning purposes only. It should **NOT** be used in production environments without proper security hardening, monitoring, and operational considerations.

## Prerequisites

- OpenShift 4.x or Kubernetes 1.23+ cluster
- NVIDIA GPU nodes with the GPU operator installed
- ArgoCD installed in the cluster (namespace: `openshift-gitops`)
- kubectl or oc CLI tool
- Access to a Hugging Face account with gpt-oss model access

## Setup Instructions

### Create Hugging Face Token Secret

Since the GPT-OSS model requires authentication, you need to create a Kubernetes secret with your Hugging Face token. This secret is **not** stored in Git for security reasons.

```bash
# Create the secret in the gpt-oss namespace
kubectl create namespace gpt-oss

kubectl create secret generic huggingface-token \
  --from-literal=token=YOUR_HF_TOKEN_HERE \
  -n gpt-oss
```

Alternatively, if the namespace doesn't exist yet (ArgoCD will create it):

```bash
# Wait for ArgoCD to create the namespace, then run:
kubectl create secret generic huggingface-token \
  --from-literal=token=YOUR_HF_TOKEN_HERE \
  -n gpt-oss
```

### Deploy with ArgoCD

Apply the ArgoCD Application manifest:

```bash
kubectl apply -f clusters/prod/apps/gpt-oss-20b.yaml
```

This will create an ArgoCD Application that:

- Monitors the `manifests/overlays/prod` path
- Automatically syncs changes from Git
- Deploys to the `gpt-oss` namespace
- Enables auto-pruning and self-healing

## Configuration

### Resource Requirements

Production overlay configures:

- **GPU**: 1x NVIDIA GPU
- **CPU**: 4 cores (requests) / 8 cores (limits)
- **Memory**: 24Gi (requests) / 40Gi (limits)

Modify `manifests/overlays/prod/patch-resources.yaml` to adjust these values.

## Contributing

Contributions are welcome! If you have suggestions, improvements, or bug fixes, please feel free to:

- Open an issue to discuss your ideas or report problems
- Submit a pull request with your proposed changes

Please ensure your contributions align with the PoC nature of this repository.
