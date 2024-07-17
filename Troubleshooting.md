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

- https://stackoverflow.com/questions/46664104/how-to-sign-in-kubernetes-dashboard
- https://janrock.medium.com/kubernetes-dashboard-token-issue-solved-6df37893bef6
- https://github.com/kubernetes/dashboard/issues/8767
