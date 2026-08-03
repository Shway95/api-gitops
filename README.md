# Employee API GitOps Repository

Kubernetes deployment configuration for Employee Management API, managed by Argo CD.

## Structure

```
employee-api-gitops/
├── helm/
│   └── employee-api/          # Helm chart
│       ├── Chart.yaml
│       ├── values.yaml        # Default values
│       └── templates/
│           ├── deployment.yaml
│           ├── service.yaml
│           ├── ingress.yaml
│           ├── configmap.yaml
│           └── secret.yaml
├── environments/
│   ├── dev/values.yaml        # Dev overrides
│   ├── qa/values.yaml         # QA overrides
│   └── prod/values.yaml       # Prod overrides
├── argocd/
│   ├── application.yaml       # Argo CD app (dev)
│   └── application-prod.yaml  # Argo CD app (prod)
└── README.md
```

## GitOps Workflow

1. CI pipeline in `employee-api` repo updates `image.tag` in `values.yaml`
2. Argo CD detects the change in this repository
3. Argo CD syncs the new image to the Kubernetes cluster
4. Self-healing ensures desired state is maintained

## Argo CD Setup

```bash
# Install Argo CD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Apply application manifest
kubectl apply -f argocd/application.yaml
```

## Helm Deployment (Manual)

```bash
# Dev
helm upgrade --install employee-api helm/employee-api \
  -f environments/dev/values.yaml \
  -n employee-api-dev --create-namespace

# Prod
helm upgrade --install employee-api helm/employee-api \
  -f environments/prod/values.yaml \
  -n employee-api-prod --create-namespace
```

## Kubernetes Resources Created

- Namespace
- Deployment (with liveness/readiness probes)
- Service (ClusterIP)
- Ingress (nginx)
- ConfigMap (environment variables)
- Secret (database credentials)
- ServiceAccount
