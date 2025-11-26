# ArgoCD Demo - Nginx

Démonstration simple d'ArgoCD pour Orange Sonatel.

## 🎯 Objectif

Démontrer le déploiement continu avec ArgoCD:
1. Créer une application ArgoCD liée à ce repo GitHub
2. ArgoCD surveille automatiquement les changements
3. Toute modification dans Git déclenche un redéploiement automatique
4. Visible immédiatement sur Kubernetes

## 📁 Structure

```
argocd-demo-nginx/
├── deployment/
│   ├── deployment.yaml    # Déploiement nginx + ConfigMap HTML
│   └── service.yaml        # Service NodePort (port 30081)
└── README.md
```

## 🚀 Déploiement

### Étape 1: Créer l'application dans ArgoCD

Via l'interface ArgoCD (http://13.39.144.79:30908):

1. Cliquer sur "+ NEW APP"
2. Remplir les informations:
   - **Application Name**: `nginx-demo`
   - **Project**: `default`
   - **Sync Policy**: `Automatic`
   - **Repository URL**: `https://github.com/magatte365/argocd-demo-nginx.git`
   - **Revision**: `main`
   - **Path**: `deployment`
   - **Cluster URL**: `https://kubernetes.default.svc`
   - **Namespace**: `default`
3. Cliquer sur "CREATE"

### Étape 2: Vérifier le déploiement

```bash
# Voir l'application ArgoCD
kubectl get application nginx-demo -n argocd

# Voir les pods
kubectl get pods -l app=nginx-demo

# Tester l'accès
curl http://13.39.144.79:30081
```

L'application sera accessible sur: **http://13.39.144.79:30081**

## 🎬 Démonstration du CD (Continuous Deployment)

### Test 1: Changer la version

1. Modifier `deployment/deployment.yaml`:
   ```yaml
   data:
     index.html: |
       ...
       <div class="version">Version 2.0</div>  # Changer ici
   ```

2. Commiter et pusher:
   ```bash
   git add .
   git commit -m "Update to version 2.0"
   git push origin main
   ```

3. **ArgoCD détecte automatiquement le changement** (max 3 minutes)
4. Rafraîchir http://13.39.144.79:30081 → Version 2.0 visible!

### Test 2: Changer le nombre de replicas

1. Modifier `deployment/deployment.yaml`:
   ```yaml
   spec:
     replicas: 3  # Passer de 2 à 3
   ```

2. Pusher vers GitHub
3. ArgoCD crée automatiquement le 3ème pod
4. Vérifier: `kubectl get pods -l app=nginx-demo`

### Test 3: Changer le design

1. Modifier le CSS dans le ConfigMap (couleurs, texte, etc.)
2. Pusher vers GitHub
3. ArgoCD redémarre les pods avec la nouvelle configuration
4. Le nouveau design apparaît automatiquement

## 📝 Points clés à montrer

1. **GitOps**: Git est la source de vérité unique
2. **Automatique**: Aucune intervention manuelle nécessaire
3. **Rapide**: Détection et déploiement en quelques minutes
4. **Traçabilité**: Chaque changement lié à un commit Git
5. **Rollback facile**: Revenir à n'importe quelle version Git

## 👥 Auteur

Magatte Seck - Stage Orange Sonatel
