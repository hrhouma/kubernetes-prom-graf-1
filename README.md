# kubernetes-prom-graf-1

### Étape 1 : Démarrage de Minikube

Démarrons par créer un cluster Kubernetes avec Minikube, si ce n'est pas déjà fait.

```bash
minikube start
```

### Étape 2 : Application des fichiers YAML de configuration et de déploiement

Nous allons maintenant appliquer les fichiers YAML que vous avez fournis. Voici les commandes pour chaque fichier :

```bash
kubectl apply -f kube-state-metrics-service-account.yaml
kubectl apply -f kube-state-metrics-cluster-role.yaml
kubectl apply -f kube-state-metrics-cluster-role-binding.yaml
kubectl apply -f kube-state-metrics-deployment.yaml
kubectl apply -f kube-state-metrics-service.yaml
kubectl apply -f grafana-configmap.yaml
kubectl apply -f grafana-deployment.yaml
kubectl apply -f grafana-service.yaml
kubectl apply -f metrics-server-prometheus.deployment.yml
kubectl apply -f Web-UI-dashboard-adminuser.yml
kubectl apply -f Web-UI-newDeploy.yml
```

### Étape 3 : Vérification des déploiements et services

Une fois les fichiers appliqués, vérifiez que tous les déploiements et services sont correctement configurés et en cours d'exécution :

```bash
kubectl get all -A
```

### Étape 4 : Accès au tableau de bord Kubernetes

Pour accéder au tableau de bord Kubernetes :

1. Obtenez le token d'accès :
   ```bash
   kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
   ```

2. Lancez le proxy Kubernetes :
   ```bash
   kubectl proxy
   ```

3. Accédez au tableau de bord à partir de votre navigateur :
   ```
   http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
   ```

### Étape 5 : Accès à Grafana

Pour accéder à Grafana :

```bash
minikube service grafana --url
```

Utilisez l'URL fournie pour accéder à Grafana. Les identifiants par défaut sont généralement "admin" pour le nom d'utilisateur et "admin" pour le mot de passe.

### Étape 6 : Configuration de Prometheus comme source de données dans Grafana

Suivez ces étapes dans l'interface utilisateur de Grafana pour configurer Prometheus comme source de données :

1. Connectez-vous à Grafana.
2. Allez dans **Configuration** -> **Data Sources**.
3. Cliquez sur **Add data source**.
4. Sélectionnez **Prometheus**.
5. Configurez l'URL comme suit :
   ```
   http://prometheus:9090
   ```
6. Cliquez sur **Save & Test**.

Cette approche vous permet de mettre en œuvre un système de surveillance de votre cluster Kubernetes en utilisant les fichiers spécifiques que vous avez fournis, plutôt que de dépendre de clones de dépôts externes.
