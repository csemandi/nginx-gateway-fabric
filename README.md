# NGINX Gateway Fabric v2.4.2 - AKS & ArgoCD Setup

This repository contains the configuration for deploying **NGINX Gateway Fabric (NGF) v2.4.2** on **Azure Kubernetes Service (AKS)** with **ArgoCD** for continuous deployment and version management.

## Repository Structure

```
nginx-gateway-fabric/
├── argocd/
│   └── nginx-gateway-fabric-app.yaml       # ArgoCD Application manifest
├── helm-values/
│   └── ngf-values.yaml                     # Custom Helm values for NGF
├── deployment/
│   └── namespace.yaml                      # Kubernetes namespace
└── README.md                               # This file
```

## Prerequisites

- **AKS Cluster** up and running
- **kubectl** configured for your AKS cluster
- **Helm** installed (v3 or later)
- **ArgoCD** installed on your AKS cluster
- Sufficient permissions to create namespaces and deploy applications

## Quick Start

### 1. Add NGINX Helm Repository

```bash
helm repo add nginx-stable https://helm.nginx.com/stable
helm repo update
```

### 2. Deploy via ArgoCD

Apply the ArgoCD Application manifest:

```bash
kubectl apply -f argocd/nginx-gateway-fabric-app.yaml
```

### 3. Verify Deployment

```bash
# Check ArgoCD application status
kubectl get application -n argocd
kubectl describe application nginx-gateway-fabric -n argocd

# Check NGF pods
kubectl get pods -n nginx-gateway

# Check the LoadBalancer service
kubectl get svc -n nginx-gateway

# View logs
kubectl logs -n nginx-gateway -l app.kubernetes.io/name=nginx-gateway-fabric -f
```

## Configuration

- **NGINX Gateway Fabric Version**: v2.4.2
- **Service Type**: LoadBalancer (can be customized in `helm-values/ngf-values.yaml`)
- **Replicas**: 2 (for high availability)
- **Resource Limits**: 
  - CPU: 100m (request) / 500m (limit)
  - Memory: 128Mi (request) / 512Mi (limit)

## Updating NGF Version

To upgrade to a newer version of NGINX Gateway Fabric:

1. Update the `targetRevision` in `argocd/nginx-gateway-fabric-app.yaml`:
   ```yaml
   targetRevision: "X.Y.Z"
   ```

2. Update the image tag in the same file or in `helm-values/ngf-values.yaml`:
   ```yaml
   image:
     tag: "X.Y.Z"
   ```

3. Commit and push your changes:
   ```bash
git add .
   git commit -m "chore: upgrade NGF to vX.Y.Z"
   git push origin main
   ```

4. ArgoCD will automatically detect the changes and sync the deployment.

## ArgoCD Auto-Sync Policy

This configuration uses ArgoCD's automated sync policy with:
- **Automated Pruning**: Removes resources deleted from Git
- **Self-Heal**: Continuously syncs the cluster state with the Git repository

## Troubleshooting

### Pods not starting
```bash
kubectl logs <pod-name> -n nginx-gateway
kubectl describe pod <pod-name> -n nginx-gateway
```

### ArgoCD not syncing
```bash
kubectl get application -n argocd
kubectl describe application nginx-gateway-fabric -n argocd
```

### Check service endpoints
```bash
kubectl get endpoints -n nginx-gateway
```

## Next Steps

1. Configure DNS for the LoadBalancer IP address
2. Set up monitoring and logging (Prometheus, Grafana, ELK)
3. Configure ingress/routing rules
4. Implement network policies for security

## Documentation

- [NGINX Gateway Fabric Documentation](https://docs.nginx.com/nginx-gateway-fabric/)
- [NGINX Gateway Fabric Helm Chart](https://helm.nginx.com/stable)
- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)

## Support

For issues or questions:
- Check the logs: `kubectl logs -n nginx-gateway`
- Review ArgoCD application status: `kubectl describe application nginx-gateway-fabric -n argocd`
- Consult official [NGINX documentation](https://docs.nginx.com/nginx-gateway-fabric/)