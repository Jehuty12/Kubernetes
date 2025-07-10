# 📚 Documentation - Projet T-CLO-902

Documentation organisée pour le déploiement d'une application Kubernetes 3-tiers avec GitOps.

## 🚀 Guides rapides

### Infrastructure
- [Configuration Vagrant](./infrastructure/vagrant-setup.md) - VM et cluster Kubernetes
- [Configuration NFS](./infrastructure/nfs-setup.md) - Stockage partagé

### Déploiement
- [Installation ArgoCD](./deployment/argocd-setup.md) - GitOps et déploiement continu
- [Déploiement applications](./deployment/app-deployment.md) - Backend, Frontend, MySQL

### Monitoring
- [Configuration Prometheus](./monitoring/prometheus-setup.md) - Métriques

### Sécurité
- [Configuration TLS](./security/tls-setup.md) - HTTPS et certificats

## � Structure originale

```text
docs/
├── infrastructure/     # Configuration VM et stockage
├── deployment/        # ArgoCD et applications
├── monitoring/        # Prometheus
└── security/         # TLS et certificats
```

## 🔧 Ordre d'installation

1. **Infrastructure** → 2. **ArgoCD** → 3. **Applications** → 4. **TLS** → 5. **Monitoring**
