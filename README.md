"# 🚀 Projet Kubernetes - Application 3-tiers avec GitOps

Ce projet déploie une application web 3-tiers complète sur Kubernetes avec une approche GitOps utilisant ArgoCD. L'infrastructure est automatisée avec Vagrant et inclut monitoring, logging et sécurité TLS.

## 🏗️ Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│    Frontend     │    │     Backend     │    │    Database     │
│   (React/HTML)  │◄──►│   (Flask API)   │◄──►│     (MySQL)     │
│     Nginx       │    │     Python      │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
         ┌───────────────────────▼───────────────────────┐
         │            Kubernetes Cluster                 │
         │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐│
         │  │   Master    │ │  Worker 1   │ │  Worker 2   ││
         │  │ 192.168.56.10│ │192.168.56.11│ │192.168.56.12││
         │  └─────────────┘ └─────────────┘ └─────────────┘│
         └───────────────────────────────────────────────┘
                                 │
         ┌───────────────────────▼───────────────────────┐
         │              Observabilité                    │
         │  Prometheus │ Grafana │ Loki │ Tempo │ ArgoCD │
         └───────────────────────────────────────────────┘
```

## 🎯 Fonctionnalités

- **Application 3-tiers** : Frontend (HTML/JS) + Backend (Flask) + Base de données (MySQL)
- **GitOps** : Déploiement automatisé avec ArgoCD
- **Monitoring** : Prometheus + Grafana pour les métriques
- **Logging** : Loki + Promtail pour la centralisation des logs
- **Tracing** : Tempo pour le tracing distribué
- **Sécurité** : TLS/HTTPS avec certificats auto-signés
- **Load Balancing** : Traefik comme ingress controller
- **Stockage** : NFS provisioner pour les volumes persistants
- **Haute disponibilité** : Déploiement multi-nœuds

## 📋 Prérequis

### Logiciels requis
- **Vagrant** 2.4.3+
- **VirtualBox** 7.1.6+
- **Git**

### Ressources système recommandées
- **RAM** : 8 Go minimum (12 Go recommandé)
- **CPU** : 4 cœurs minimum
- **Disque** : 50 Go d'espace libre

## 🚀 Installation rapide

### 1. Cloner le projet
```bash
git clone <votre-repo>
cd Kubernetes
```

### 2. Configuration Vagrant
```bash
cd vagrant/
cp Vagrantfile.sample Vagrantfile
```

**Personnalisez votre configuration** dans `Vagrantfile` selon vos ressources :
- Ajustez `MEMORY` et `CPUS` 
- Modifiez les IPs si nécessaire (192.168.56.x)

### 3. Démarrer l'infrastructure
```bash
# Démarrer toutes les VMs (peut prendre 10-15 minutes)
vagrant up

# Se connecter au master
vagrant ssh master
```

### 4. Configuration du cluster (sur le master)
```bash
# Configuration kubectl
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Installation des outils
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash

# Alias utiles
echo "alias k='kubectl'" >> ~/.bashrc
echo "alias h='helm'" >> ~/.bashrc
source ~/.bashrc

# Vérifier le cluster
kubectl get nodes
```

### 5. Déployer l'application
```bash
# Cloner le repo dans la VM master
git clone <votre-repo> /tmp/kube-project
cd /tmp/kube-project

# Déployer dans l'ordre
kubectl apply -f kube/apps/rbac.yaml
kubectl apply -f kube/apps/root-app.yaml

# Attendre qu'ArgoCD se déploie (5-10 minutes)
kubectl get pods -n argocd -w
```

## 🔗 Accès aux services

Une fois déployé, vous pouvez accéder aux services via :

- **Application principale** : http://localhost (port 80)
- **ArgoCD** : https://localhost:8443
  - Login : `admin` / Mot de passe à récupérer avec :
    ```bash
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
    ```
- **Prometheus** : http://prometheus.local (ajoutez l'entrée dans `/etc/hosts`)
- **Grafana** : http://grafana.local

## 📁 Structure du projet

```
├── README.md                    # Ce fichier
├── docs/                       # Documentation détaillée
│   ├── infrastructure/         # Configuration VM et stockage
│   ├── deployment/            # ArgoCD et applications
│   ├── monitoring/            # Prometheus & Grafana
│   └── security/              # Configuration TLS
├── kube/                      # Manifests Kubernetes
│   ├── apps/                  # Applications ArgoCD
│   └── charts/                # Charts Helm
├── pooc/                      # Code source applications
│   ├── backend/               # API Flask
│   └── frontend/              # Interface web
└── vagrant/                   # Configuration VMs
    ├── Vagrantfile.sample     # Template Vagrant
    └── scripts/               # Scripts d'installation
```

## 🛠️ Commandes utiles

### Gestion Vagrant
```bash
# Démarrer toutes les VMs
vagrant up

# Arrêter toutes les VMs
vagrant halt

# Redémarrer une VM
vagrant reload master

# Supprimer toutes les VMs
vagrant destroy -f

# État des VMs
vagrant status
```

### Gestion Kubernetes
```bash
# Voir tous les pods
kubectl get pods --all-namespaces

# Voir les applications ArgoCD
kubectl get applications -n argocd

# Logs d'un pod
kubectl logs -f <pod-name> -n <namespace>

# Redéployer une application
kubectl rollout restart deployment/<deployment-name> -n <namespace>
```

### Debugging
```bash
# Vérifier les nœuds
kubectl get nodes -o wide

# Vérifier les événements
kubectl get events --sort-by=.metadata.creationTimestamp

# Diagnostiquer un pod
kubectl describe pod <pod-name> -n <namespace>
```

## 🔧 Personnalisation

### Modifier l'application
1. Modifiez le code dans `pooc/backend/` ou `pooc/frontend/`
2. Commitez et pushez vos changements
3. ArgoCD synchronisera automatiquement (ou forcez avec l'UI ArgoCD)

### Ajouter des services
1. Créez un nouveau chart Helm dans `kube/charts/`
2. Ajoutez une entrée dans `kube/apps/`
3. ArgoCD déploiera automatiquement

### Modifier la configuration
- **Vagrant** : Éditez `vagrant/Vagrantfile`
- **Kubernetes** : Modifiez les values dans `kube/charts/*/values.yaml`
- **Monitoring** : Configurez Prometheus/Grafana dans leurs charts respectifs

## 🆘 Dépannage

### Problèmes courants

**VMs ne démarrent pas**
```bash
# Vérifiez VirtualBox
vboxmanage list vms

# Relancez avec debug
vagrant up --debug
```

**Pods en erreur**
```bash
# Vérifiez les événements
kubectl get events -n <namespace>

# Vérifiez les logs
kubectl logs <pod-name> -n <namespace>
```

**ArgoCD ne synchronise pas**
- Vérifiez la connectivité Git
- Regardez les logs ArgoCD : `kubectl logs -n argocd deployment/argocd-server`

### Réinitialisation complète
```bash
# Détruire toutes les VMs et recommencer
vagrant destroy -f
vagrant up
```

## 📚 Documentation complète

Pour plus de détails, consultez la [documentation complète](./docs/README.md) qui inclut :
- Configuration détaillée de chaque composant
- Guides de troubleshooting avancés
- Procédures de mise à jour
- Configuration de la sécurité

## 📄 Licence

Ce projet est sous licence MIT. Voir le fichier [LICENSE](LICENSE) pour plus de détails." 
