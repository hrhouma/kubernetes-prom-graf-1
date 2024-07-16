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


# Troubleshooting 1


Pour établir un environnement Kubernetes complet avec Grafana, Prometheus, et le Kubernetes Dashboard dans leurs namespaces appropriés, voici un tutoriel complet étape par étape :

### Étape 1 : Préparation de l'Environnement

1. **Démarrage de Minikube** :
   Minikube est un outil qui permet de lancer un cluster Kubernetes localement. Lancez Minikube si ce n'est pas déjà fait :
   ```bash
   minikube start
   ```

### Étape 2 : Création des Namespaces

2. **Créer les Namespaces Nécessaires** :
   ```bash
   kubectl create namespace monitoring
   kubectl create namespace kubernetes-dashboard
   ```

### Étape 3 : Application des Fichiers de Configuration

3. **Appliquer les Fichiers YAML** :
   Appliquez tous les fichiers de configuration nécessaires en utilisant `kubectl apply`. Assurez-vous que chaque fichier est appliqué dans le bon namespace, soit en spécifiant le namespace directement dans le fichier YAML sous `metadata.namespace`, soit en utilisant l'option `-n [namespace]` dans la commande.
   
   ```bash
   # Ressources pour kube-state-metrics
   kubectl apply -f kube-state-metrics-service-account.yaml -n monitoring
   kubectl apply -f kube-state-metrics-cluster-role.yaml -n monitoring
   kubectl apply -f kube-state-metrics-cluster-role-binding.yaml -n monitoring
   kubectl apply -f kube-state-metrics-deployment.yaml -n monitoring
   kubectl apply -f kube-state-metrics-service.yaml -n monitoring
   
   # Ressources pour Grafana
   kubectl apply -f grafana-configmap.yaml -n monitoring
   kubectl apply -f grafana-deployment.yaml -n monitoring
   kubectl apply -f grafana-service.yaml -n monitoring
   
   # Ressources pour Prometheus
   kubectl apply -f metrics-server-prometheus.deployment.yml -n monitoring
   
   # Ressources pour le Kubernetes Dashboard
   kubectl apply -f Web-UI-dashboard-adminuser.yml -n kubernetes-dashboard
   kubectl apply -f Web-UI-newDeploy.yml -n kubernetes-dashboard
   ```

### Étape 4 : Vérification de l'État des Déploiements

4. **Vérifier les Pods et Services** :
   ```bash
   kubectl get pods --all-namespaces
   kubectl get services --all-namespaces
   ```

### Étape 5 : Configuration de Grafana

5. **Accès à Grafana** :
   Utilisez Minikube pour obtenir l'URL d'accès à Grafana :
   ```bash
   minikube service grafana --url -n monitoring
   ```

### Étape 6 : Configuration du Dashboard Kubernetes

6. **Configurer l'Accès au Kubernetes Dashboard** :
   Lancez le proxy Kubernetes pour accéder au Dashboard :
   ```bash
   kubectl proxy
   ```

   Puis, accédez à l'URL suivante dans votre navigateur :
   ```
   http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
   ```

### Étape 7 : Configuration de Prometheus comme Source de Données dans Grafana

7. **Configurer Prometheus dans Grafana** :
   Une fois dans Grafana, ajoutez Prometheus comme source de données :
   - Connectez-vous à Grafana.
   - Allez dans **Configuration** -> **Data Sources**.
   - Cliquez sur **Add data source**.
   - Sélectionnez **Prometheus**.
   - Configurez l'URL comme suit :
     ```
     http://prometheus:9090
     ```
   - Cliquez sur **Save & Test**.

### Conclusion

En suivant ces étapes, vous aurez configuré un environnement Kubernetes avec Grafana, Prometheus, et le Kubernetes Dashboard, chacun dans leur propre namespace, assurant une séparation et une gestion claire des ressources.

# Troubleshooting 2

### Étape 1 : Vérification du déploiement du Kubernetes Dashboard

1. **Vérifiez si le Dashboard est installé** :
   ```bash
   kubectl get all -n kubernetes-dashboard
   ```

2. **Si rien n'est trouvé**, vous devrez installer ou redéployer le Kubernetes Dashboard. Vous pouvez le faire en appliquant un fichier YAML officiel pour le déploiement du dashboard. Voici comment vous pouvez le déployer :

   ```bash
   kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.5.0/aio/deploy/recommended.yaml
   ```

### Étape 2 : Accéder au Kubernetes Dashboard

Après avoir assuré que le Dashboard est déployé, réessayez d'accéder via le proxy :

1. **Lancer le proxy** :
   ```bash
   kubectl proxy
   ```

2. **Accéder au Dashboard** :
   Utilisez l'URL correcte pour le Dashboard, en vous assurant que le service est disponible :

   ```
   http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
   ```

### Étape 3 : Vérification des permissions

Assurez-vous également que le compte de service ou les rôles associés ont les permissions nécessaires pour accéder aux ressources dans le cluster. Si des problèmes de permissions sont présents, ils doivent être résolus pour permettre un accès complet aux fonctionnalités du Dashboard.

Si le problème persiste après ces vérifications, cela peut indiquer un problème plus profond avec la configuration de votre cluster ou avec les fichiers YAML spécifiques que vous utilisez.

# Troubleshooting 3

- Pour cette commande
```bash
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```

L'information affichée montre des détails sur un secret nommé `bootstrap-token-3crfdr` et non sur un token d'accès pour un utilisateur de dashboard. 

Pour accéder au Dashboard Kubernetes, vous aurez besoin d'un token d'authentification associé à un compte de service ayant les droits nécessaires. Voici comment vous pouvez procéder pour créer un compte de service et obtenir un token pour le Dashboard :

### Création d'un Compte de Service et d'un Role Binding

1. **Créer un compte de service dans le namespace `kubernetes-dashboard`** :
   ```bash
   kubectl create serviceaccount dashboard-admin -n kubernetes-dashboard
   ```

2. **Créer un ClusterRoleBinding pour donner des droits administratifs à ce compte** :
   ```bash
   kubectl create clusterrolebinding dashboard-admin-binding --clusterrole=cluster-admin --serviceaccount=kubernetes-dashboard:dashboard-admin
   ```

### Récupération du Token d'Authentification

3. **Récupérer le token d'accès** :
   ```bash
   kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep dashboard-admin | awk '{print $1}')
   ```

Ce token est ce que vous utiliserez pour vous connecter au Dashboard Kubernetes. Veillez à copier le token qui apparaîtra dans la sortie de la commande `describe secret` sous la clé `token`.

# Troubleshooting 4

- Erreur due à la version de l'API utilisée par votre commande `kubectl` ou par votre cluster.
- Pour résoudre ce problème, vérifions la disponibilité de l'API pour créer un ClusterRoleBinding, puis essayons à nouveau la commande avec la bonne API.

### Vérifier les versions disponibles de l'API RBAC
Pour commencer, vérifions les versions de l'API disponibles pour le RBAC :

```bash
kubectl api-versions | grep rbac
```

Vous devriez voir une liste des versions disponibles comme `rbac.authorization.k8s.io/v1`.

### Créer le ClusterRoleBinding avec la bonne version de l'API
Si la version `rbac.authorization.k8s.io/v1` est disponible, utilisons cette version spécifiquement pour créer le ClusterRoleBinding. Voici comment vous pouvez le faire :

```bash
kubectl create clusterrolebinding dashboard-admin-binding --clusterrole=cluster-admin --serviceaccount=kubernetes-dashboard:dashboard-admin
```

Cette commande ne spécifie pas explicitement la version de l'API dans la commande elle-même, car `kubectl` devrait automatiquement utiliser la bonne version basée sur les versions disponibles de l'API.

Si cette commande réussit, vous devriez pouvoir continuer et récupérer le token comme suit :

```bash
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get serviceaccount dashboard-admin -o=jsonpath='{.secrets[0].name}')
```

Cela devrait vous donner le token nécessaire pour vous connecter au Dashboard Kubernetes. Assurez-vous de copier le token depuis la section `token` affichée dans les détails du secret.

### Lancer le Proxy Kubernetes

4. **Lancer le proxy** :
   ```bash
   kubectl proxy
   ```

Après avoir lancé le proxy, vous pouvez accéder au Dashboard à l'adresse suivante :
```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

Utilisez le token que vous avez récupéré pour vous connecter au Dashboard. Assurez-vous de choisir le mode d'authentification "Token" et de coller le token dans le champ prévu.
