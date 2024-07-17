# Partie 1:

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

# Partie 2:

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
```
