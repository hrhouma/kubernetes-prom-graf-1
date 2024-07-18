🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# Partie 0 : Installation de l'environnement
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

### 1. Mettre la première carte de notre machine Ubuntu 2204 en NAT ou Bridge
### 2. Ajouter une seconde carte réseau Host-Only Adapter

Pour ajouter une deuxième carte réseau en mode Host-Only dans VirtualBox, tout en conservant la première en mode NAT :

1. Assurez-vous que votre machine virtuelle est éteinte complétement.
2. Ouvrez **VirtualBox** et sélectionnez la machine virtuelle concernée.
3. Allez dans **Paramètres > Réseau**.
4. Vous verrez que l'**Adaptateur 1** est déjà configuré en NAT.
5. Cliquez sur l'onglet **Adaptateur 2**.
6. Cochez la case **Activer l'adaptateur réseau**.
7. Dans le menu déroulant **Attaché à**, choisissez **Adaptateur hôte uniquement**.
8. Sélectionnez le réseau hôte approprié dans le menu **Nom** si plusieurs réseaux hôtes sont disponibles.
9. Cliquez sur **OK** pour sauvegarder vos modifications.

### 2. Installer et configurer SSH

Une fois que votre machine virtuelle est configurée avec les deux cartes réseau, suivez les étapes ci-dessous pour installer et configurer SSH :

#### Installer OpenSSH Server
```bash
su
apt update
apt install openssh-server
```


#### Vérifier le statut du service SSH

Vérifiez si le service SSH fonctionne correctement avec :

```bash
sudo systemctl status ssh
```

Ou, pour les systèmes utilisant encore SysVinit :

```bash
sudo service ssh status
```

### 3. Connexion SSH

Vous pouvez maintenant vous connecter à votre machine virtuelle via SSH. Utilisez l'adresse IP de la deuxième carte (Host-Only) pour établir la connexion :

```bash
ssh votre_utilisateur@adresse_ip_host_only
```

Trouvez l'adresse IP avec `ip a` et cherchez l'interface correspondant à l'adaptateur Host-Only.

🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# Partie 1: Installer docker et mini-kube
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

- Il est nécessaire de configurer l'utilisateur `eleve` avec les privilèges sudo avant de procéder à l'installation de Docker et de le rajouter au groupe Docker.
  
1. **Ajouter l'utilisateur 'eleve' aux sudoers sans demande de mot de passe**
   ```bash
   su
   echo "eleve ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/eleve
   ```

2. **Se connecter en tant qu'utilisateur 'eleve'**
   ```bash
   su - eleve
   ```

3. **Installer Docker et ajouter l'utilisateur 'eleve' au groupe Docker**
   ```bash
   # Mettre à jour les paquets et installer les prérequis
   sudo apt-get update
   sudo apt-get install -y ca-certificates curl gnupg lsb-release

   # Ajouter la clé GPG officielle de Docker
   sudo mkdir -p /etc/apt/keyrings
   sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

   # Ajouter le dépôt Docker aux sources APT
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

   # Installer Docker Engine, CLI et Containerd
   sudo apt-get update
   sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

   # Ajouter l'utilisateur 'eleve' au groupe Docker
   sudo usermod -aG docker eleve
   ```

4. **Installer et démarrer Minikube**
   ```bash
   sudo apt update
   sudo apt install -y curl wget apt-transport-https

   # Télécharger et installer Minikube
   sudo curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
   sudo install minikube-linux-amd64 /usr/local/bin/minikube

   # Télécharger et installer kubectl
   sudo wget https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/amd64/kubectl
   sudo chmod +x kubectl
   sudo mv kubectl /usr/local/bin/

   sudo usermod -aG docker $USER && newgrp docker

   # Démarrer Minikube en tant qu'utilisateur 'eleve' (sans sudo)
   minikube start --driver=docker
   ```

5. **Vérifier l'installation**
   ```bash
   kubectl cluster-info
   minikube status
   ```
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# Partie 2: Cloner et installer le projet
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

```bash
minikube status
git clone https://github.com/hrhouma/kubernetes-prom-graf-1.git
cd kubernetes-prom-graf-1/
kubectl create namespace monitoring
kubectl create namespace kubernetes-dashboard
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
kubectl apply -f Web-UI-newDeploy.yml
```

🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
## Partie 3: Chercher les secrets et le dashboard ( Troubleshooting)
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

```bash
kubectl get secrets
kubectl get namespaces
kubectl get secrets -n kube-system
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
kubectl get services -n kubernetes-dashboard
kubectl apply -f Web-UI-dashboard-adminuser.yml -n  kubernetes-dashboard
kubectl get services -n kubernetes-dashboard
kubectl proxy
CTL + C
```
## ==> Problème 1 : Pas de dashboard
## ==> Problème 2 : Pas de secrets

🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
## Partie 4: Régler le problème 1 (Installer un dashboard)
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
kubectl get services -n kubernetes-dashboard
kubectl proxy
kubectl get secrets -n kubernetes-dashboard
kubectl describe secret kubernetes-dashboard-certs -n kubernetes-dashboard
kubectl describe secret kubernetes-dashboard -n kubernetes-dashboard
kubectl get secrets -n kubernetes-dashboard -o jsonpath="{.items[?(@.metadata.annotations['kubernetes\.io/service-account\.name']=='admin-user')].metadata.name}"
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
kubectl get secrets -n kubernetes-dashboard
kubectl get serviceaccounts -n kubernetes-dashboard
```

🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
## Partie 5: Régler le problème 2 (Générez un secret)
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
kubectl get services -n kubernetes-dashboard
kubectl get secrets -n kubernetes-dashboard -o jsonpath="{.items[?(@.metadata.annotations['kubernetes\.io/service-account\.name']=='admin-user')].metadata.name}"
kubectl describe secret kubernetes-dashboard-certs -n kubernetes-dashboard
kubectl describe secret kubernetes-dashboard -n kubernetes-dashboard
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
kubectl get secrets -n kubernetes-dashboard
kubectl get serviceaccounts -n kubernetes-dashboard
```
###  En cas ou vous avez une sortie de 0 affichée : 

La sortie indique que le compte de service `admin-user` a été créé dans l'espace de noms `kubernetes-dashboard`, mais s'il affiche "0" sous la colonne SECRETS, ce qui indique qu'aucun secret (et donc aucun token) n'est associé à ce compte. Cela est inhabituel car Kubernetes crée normalement automatiquement un secret contenant un token lorsqu'un nouveau compte de service est créé.

```bash
nano admin-user-token.yaml
```

### Étapes pour créer manuellement un token pour le compte de service `admin-user` :

Puisque le système n'a pas généré de token automatiquement, vous devrez en créer un manuellement.

1. **Créer un Token Manuellement** :
   Vous pouvez explicitement créer un token pour `admin-user` en appliquant une configuration YAML pour un secret de type `kubernetes.io/service-account-token`. Voici comment vous pouvez le faire :

   Créez un fichier YAML nommé `admin-user-token.yaml` avec le contenu suivant :
   ```yaml
   apiVersion: v1
   kind: Secret
   metadata:
     name: admin-user-token
     annotations:
       kubernetes.io/service-account.name: "admin-user"
     namespace: kubernetes-dashboard
   type: kubernetes.io/service-account-token
   ```

2. **Appliquer le YAML** :
   ```bash
   kubectl apply -f admin-user-token.yaml
   kubectl get secrets -n kubernetes-dashboard
   kubectl describe secret admin-user-token -n kubernetes-dashboard
   ```

Cherchez l'entrée `token` sous la section `Data`. Vous aurez besoin de décoder ce token encodé en base64 pour l'utiliser pour la connexion au tableau de bord.

### Décoder le Token

```bash
echo '[token-encodé-en-base64]' | base64 --decode
```

- Remplacez `[token-encodé-en-base64]` par le token récupéré.
- Ce token décodé pourra être utilisé pour se connecter au Dashboard de Kubernetes.

### Résumé

```bash
nano admin-user-token.yaml
kubectl apply -f admin-user-token.yaml
kubectl get secrets -n kubernetes-dashboard
kubectl describe secret admin-user-token -n kubernetes-dashboard
echo '[base64-encoded-token]' | base64 --decode
```

🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
## Partie 6: Afficher les tableaux de bord
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

## Récupérez le token
```bash
echo '[base64-encoded-token]' | base64 --decode
echo 'token' | cut -d"." -f2 | base64 -d
echo 'token' | base64 --decode
```

## Affichez le premier tableau de bord
```bash
kubectl proxy
kubectl proxy --address='0.0.0.0' --accept-hosts='^*$'
```


🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
## Partie 7: Étapes à suivre
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

### Vérification des déploiements et services

Une fois les fichiers appliqués, vérifiez que tous les déploiements et services sont correctement configurés et en cours d'exécution :

```bash
kubectl get all -A
```

### Accès au tableau de bord Kubernetes

Pour accéder au tableau de bord Kubernetes :

1. Obtenez le token d'accès :
   ```bash
   kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
   ```

2. Lancez le proxy Kubernetes :
   ```bash
   kubectl proxy
   kubectl proxy --address='0.0.0.0' --accept-hosts='^*$' 
   ```

3. Accédez au tableau de bord à partir de votre navigateur :
   ```
   http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
   ```

### Étape 5 : Accès à Grafana

Pour accéder à Grafana :

```bash
minikube service grafana --url -n monitoring
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
   ## Correction de 5. :
![image](https://github.com/user-attachments/assets/2a294419-f724-47ff-a4f6-3ad97f44a3f3)
   
7. Cliquez sur **Save & Test**.

Cette approche vous permet de mettre en œuvre un système de surveillance de votre cluster Kubernetes en utilisant les fichiers spécifiques que vous avez fournis, plutôt que de dépendre de clones de dépôts externes.



---
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# ANNEXE 1
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

```bash
# Installation de OpenSSH Server
su
apt update
apt install openssh-server

# Vérification du statut du service SSH
sudo systemctl status ssh
# Ou pour les systèmes utilisant encore SysVinit
sudo service ssh status

# Connexion SSH
ssh votre_utilisateur@adresse_ip_host_only

# Ajouter l'utilisateur 'eleve' aux sudoers sans demande de mot de passe
su
echo "eleve ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/eleve

# Se connecter en tant qu'utilisateur 'eleve'
su - eleve

# Installation de Docker et configuration
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg lsb-release

sudo mkdir -p /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo usermod -aG docker eleve

# Installation et démarrage de Minikube
sudo apt update
sudo apt install -y curl wget apt-transport-https

sudo curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

sudo wget https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/amd64/kubectl
sudo chmod +x kubectl
sudo mv kubectl /usr/local/bin/

sudo usermod -aG docker $USER && newgrp docker

minikube start --driver=docker

# Vérification de l'installation de Minikube
kubectl cluster-info
minikube status

# Gestion des déploiements Kubernetes et accès au Dashboard
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
kubectl proxy

# Création et gestion de secrets
nano admin-user-token.yaml
kubectl apply -f admin-user-token.yaml
kubectl get secrets -n kubernetes-dashboard
kubectl describe secret admin-user-token -n kubernetes-dashboard
echo '[base64-encoded-token]' | base64 --decode

# Accès à Grafana
minikube service grafana --url -n monitoring

# Lancement du proxy Kubernetes pour accéder aux tableaux de bord
kubectl proxy --address='0.0.0.0' --accept-hosts='^*$'

# Vérification des déploiements et services dans Kubernetes
kubectl get all -A
```



---
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# ANNEXE 2
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

```bash
su
apt update
apt install openssh-server
sudo systemctl status ssh
sudo service ssh status
ssh votre_utilisateur@adresse_ip_host_only
su
echo "eleve ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/eleve
su - eleve
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg lsb-release
sudo mkdir -p /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo usermod -aG docker eleve
sudo apt update
sudo apt install -y curl wget apt-transport-https
sudo curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
sudo wget https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/amd64/kubectl
sudo chmod +x kubectl
sudo mv kubectl /usr/local/bin/
sudo usermod -aG docker $USER && newgrp docker
minikube start --driver=docker
kubectl cluster-info
minikube status
git clone https://github.com/hrhouma/kubernetes-prom-graf-1.git
cd kubernetes-prom-graf-1/
kubectl create namespace monitoring
kubectl create namespace kubernetes-dashboard
```

# Méthode 1
- Pour appliquer tous les fichiers YAML en une seule commande, si cela est approprié, vous pouvez utiliser : kubectl apply -f *.yaml
- Si vous souhaitez appliquer tous les fichiers YAML dans le dossier (en supposant qu'ils sont tous compatibles et destinés à être appliqués ensemble), vous pouvez utiliser un joker `*` avec `kubectl apply -f` comme ceci :


```bash
kubectl apply -f *.yml
kubectl apply -f *.yaml
```
ou
```bash
for file in *.yaml; do
    kubectl apply -f "$file"
done
```

# Méthode 2
- Pour appliquer ces fichiers à l'aide de `kubectl`, vous pouvez spécifier chaque fichier individuellement comme suit :

```bash
kubectl apply -f Deployement-service.yml
kubectl apply -f Deployment-deployment.yml
kubectl apply -f Web-UI-dashboard-adminuser.yml
kubectl apply -f Web-UI-newDeploy.yml
kubectl apply -f admin-user-token.yaml
kubectl apply -f grafana-configmap.yaml
kubectl apply -f grafana-deployment.yaml
kubectl apply -f grafana-service.yaml
kubectl apply -f kube-state-metrics-cluster-role-binding.yaml
kubectl apply -f kube-state-metrics-cluster-role.yaml
kubectl apply -f kube-state-metrics-deployment.yaml
kubectl apply -f kube-state-metrics-service-account.yaml
kubectl apply -f kube-state-metrics-service.yaml
kubectl apply -f metrics-server-prometheus.deployment.yml
```

- Assurez-vous d'être dans le répertoire correct où ces fichiers YAML sont situés lorsque vous exécutez ces commandes.

---



```bash
kubectl get secrets
kubectl get namespaces
kubectl get secrets -n kube-system
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
kubectl get services -n kubernetes-dashboard
kubectl apply -f Web-UI-dashboard-adminuser.yml -n  kubernetes-dashboard
kubectl get services -n kubernetes-dashboard
kubectl proxy
CTL + C
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
kubectl get services -n kubernetes-dashboard
kubectl proxy
kubectl get secrets -n kubernetes-dashboard
kubectl describe secret kubernetes-dashboard-certs -n kubernetes-dashboard
kubectl describe secret kubernetes-dashboard -n kubernetes-dashboard
kubectl get secrets -n kubernetes-dashboard -o jsonpath="{.items[?(@.metadata.annotations['kubernetes\.io/service-account\.name']=='admin-user')].metadata.name}"
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
kubectl get secrets -n kubernetes-dashboard
kubectl get serviceaccounts -n kubernetes-dashboard
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
kubectl get services -n kubernetes-dashboard
kubectl get secrets -n kubernetes-dashboard -o jsonpath="{.items[?(@.metadata.annotations['kubernetes\.io/service-account\.name']=='admin-user')].metadata.name}"
kubectl describe secret kubernetes-dashboard-certs -n kubernetes-dashboard
kubectl describe secret kubernetes-dashboard -n kubernetes-dashboard
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
kubectl get secrets -n kubernetes-dashboard
kubectl get serviceaccounts -n kubernetes-dashboard
nano admin-user-token.yaml
 ```
# Créez un fichier YAML nommé `admin-user-token.yaml` avec le contenu suivant :
   ```yaml
   apiVersion: v1
   kind: Secret
   metadata:
     name: admin-user-token
     annotations:
       kubernetes.io/service-account.name: "admin-user"
     namespace: kubernetes-dashboard
   type: kubernetes.io/service-account-token
   ```

# Appliquer le YAML :
   ```bash
   kubectl apply -f admin-user-token.yaml
   kubectl get secrets -n kubernetes-dashboard
   kubectl describe secret admin-user-token -n kubernetes-dashboard
   ```

# Décoder le Token

```bash
echo '[token-encodé-en-base64]' | base64 --decode
```

- Remplacez `[token-encodé-en-base64]` par le token récupéré.
- Ce token décodé pourra être utilisé pour se connecter au Dashboard de Kubernetes.

# Résumé

```bash
nano admin-user-token.yaml
kubectl apply -f admin-user-token.yaml
kubectl get secrets -n kubernetes-dashboard
kubectl describe secret admin-user-token -n kubernetes-dashboard
echo '[base64-encoded-token]' | base64 --decode
```

# Récupérez le token
```bash
echo '[base64-encoded-token]' | base64 --decode
echo 'token' | cut -d"." -f2 | base64 -d
echo 'token' | base64 --decode
```

## Affichez le premier tableau de bord
```bash
kubectl proxy
kubectl proxy --address='0.0.0.0' --accept-hosts='^*$'
kubectl get all -A
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
kubectl proxy
kubectl proxy --address='0.0.0.0' --accept-hosts='^*$' 
   ```

# 3. Accédez au tableau de bord à partir de votre navigateur :
   ```
http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
   ```
ou
   ```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
   ```
# Étape 5 : Accès à Grafana
```bash
minikube service grafana --url -n monitoring
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
## Correction de 5. :
![image](https://github.com/user-attachments/assets/2a294419-f724-47ff-a4f6-3ad97f44a3f3)



