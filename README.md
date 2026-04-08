# mern-auth GitOps Repository

This repository contains Kubernetes manifests managed by **ArgoCD** for the `mern-auth` application.

## Structure

```
mern-auth-gitops/
├── base/                   # Shared base manifests
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   └── service.yaml
├── staging/                # Staging overlay — auto-updated by Jenkins CI
│   └── kustomization.yaml
└── production/             # Production overlay — promoted via PR merge
    └── kustomization.yaml
```

## How It Works

```
GitHub Push → Jenkins CI
     ↓
Kaniko Build → ECR Push → Trivy Scan → Cosign Sign
     ↓
Jenkins updates staging/kustomization.yaml with new image tag
     ↓
ArgoCD detects change → Deploys to staging EKS cluster
     ↓
Release manager opens PR: staging → production branch
     ↓
PR merged → ArgoCD deploys to production EKS cluster
```

## Deploying to Production

1. Review the [open PRs](../../pulls) for staging→production promotion
2. Check ArgoCD staging dashboard to verify the build is healthy
3. Merge the PR — ArgoCD handles the production rollout automatically

## Secrets Required (K8s Secrets — NOT stored here)

| Secret Name | Keys |
|-------------|------|
| `mern-auth-secrets` | `mongodb-uri`, `jwt-secret` |
