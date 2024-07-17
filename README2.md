🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# Partie 0
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

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

🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# Partie 1:
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

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

   # Démarrer Minikube en tant qu'utilisateur 'eleve' (sans sudo)
   minikube start --driver=docker
   ```

5. **Vérifier l'installation**
   ```bash
   kubectl cluster-info
   minikube status
   ```
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# Partie 2:
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

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

🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# Partie 3:
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

```bash
kubectl get secrets
kubectl get namespaces
kubectl get secrets -n kube-system
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
kubectl get services -n kubernetes-dashboard
kubectl apply -f Web-UI-dashboard-adminuser.yml -n  kubernetes-dashboard
kubectl get services -n kubernetes-dashboard
```

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

🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
# Partie 3:
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

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
```









kubectl apply -f admin-user-token.yaml


 
nano admin-user-token.yaml
kubectl apply -f admin-user-token.yaml
kubectl get secrets -n kubernetes-dashboard
kubectl describe secret admin-user-token -n kubernetes-dashboard
   
   76  echo '[base64-encoded-token]' | base64 --decode
   77  echo '[eyJhbGciOiJSUzI1NiIsImtpZCI6IjBnRV9RUGRTejdCTHF6WDlEMUh4NEJQSWJLY0F2T0xGVjBSUWRQSzlLdGcifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIyNmE2MjAxYS1iMjM2LTQ3NTgtYTE1Ni00ZGJkZjIxM2ViY2QiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.RyewRhVAHPC8CIO4pdAAbi0D50hYh_sOA8WbtxW03K_gtqjwcuAoDSg9emaPGA5FlmYqmna1Q0AzCSvNak0_MM0zBA2TmmQtMcF3C_IRHIKgl8umu6WY3XH5z9Nm9BnktJ48gHMibgjFqWdICJ2FqR1_nUrwCggdicjjaGY_4-wrrRope-df4HzXLRjvQ5TRGKctAO29XyKlRcqWjlJbunFgf8VNgWaJDBL2lhaMQJW83I_1hEQt2dZX1IuR-SwtN-mJXEyVYxbudKGYPtsPw6U7dJ9B3p03daN7xqwMC65xvD7Pz_dJR3PPYRPdrjxe7BAu3pJ7w11YRpKekxS2gw]' | base64 --decode
   78  echo 'eyJhbGciOiJSUzI1NiIsImtpZCI6IjBnRV9RUGRTejdCTHF6WDlEMUh4NEJQSWJLY0F2T0xGVjBSUWRQSzlLdGcifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIyNmE2MjAxYS1iMjM2LTQ3NTgtYTE1Ni00ZGJkZjIxM2ViY2QiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.RyewRhVAHPC8CIO4pdAAbi0D50hYh_sOA8WbtxW03K_gtqjwcuAoDSg9emaPGA5FlmYqmna1Q0AzCSvNak0_MM0zBA2TmmQtMcF3C_IRHIKgl8umu6WY3XH5z9Nm9BnktJ48gHMibgjFqWdICJ2FqR1_nUrwCggdicjjaGY_4-wrrRope-df4HzXLRjvQ5TRGKctAO29XyKlRcqWjlJbunFgf8VNgWaJDBL2lhaMQJW83I_1hEQt2dZX1IuR-SwtN-mJXEyVYxbudKGYPtsPw6U7dJ9B3p03daN7xqwMC65xvD7Pz_dJR3PPYRPdrjxe7BAu3pJ7w11YRpKekxS2gw' | base64 --decode
   79  echo 'eyJhbGciOiJSUzI1NiIsImtpZCI6IjBnRV9RUGRTejdCTHF6WDlEMUh4NEJQSWJLY0F2T0xGVjBSUWRQSzlLdGcifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIyNmE2MjAxYS1iMjM2LTQ3NTgtYTE1Ni00ZGJkZjIxM2ViY2QiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.RyewRhVAHPC8CIO4pdAAbi0D50hYh_sOA8WbtxW03K_gtqjwcuAoDSg9emaPGA5FlmYqmna1Q0AzCSvNak0_MM0zBA2TmmQtMcF3C_IRHIKgl8umu6WY3XH5z9Nm9BnktJ48gHMibgjFqWdICJ2FqR1_nUrwCggdicjjaGY_4-wrrRope-df4HzXLRjvQ5TRGKctAO29XyKlRcqWjlJbunFgf8VNgWaJDBL2lhaMQJW83I_1hEQt2dZX1IuR-SwtN-mJXEyVYxbudKGYPtsPw6U7dJ9B3p03daN7xqwMC65xvD7Pz_dJR3PPYRPdrjxe7BAu3pJ7w11YRpKekxS2gw' | cut -d"." -f2 | base64 -d
   80  echo 'eyJhbGciOiJSUzI1NiIsImtpZCI6IjBnRV9RUGRTejdCTHF6WDlEMUh4NEJQSWJLY0F2T0xGVjBSUWRQSzlLdGcifQ' | base64 --decode
   81  echo 'eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIyNmE2MjAxYS1iMjM2LTQ3NTgtYTE1Ni00ZGJkZjIxM2ViY2QiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9' | base64 --decode
   82  kubectl proxy
   83  kubectl proxy --address='0.0.0.0' --accept-hosts='^*$'
   84  minikube service grafana --url
   85  minikube service grafana --url -n monitoring
   86  history
