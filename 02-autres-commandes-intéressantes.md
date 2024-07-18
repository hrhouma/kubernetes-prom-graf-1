
```bash
su
apt install openssh-server
ssh eleve@172.28.128.4
su
apt install git -y
cd install-docker/
git clone https://github.com/hrhouma/install-docker.git
chmod +x install-docker.sh
./install-docker.sh
sudo apt update
sudo apt install -y curl wget apt-transport-https
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version
wget https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version -o yaml
minikube start --driver=docker
exit
docker ps
echo "eleve ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/eleve
sudo usermod -aG docker $USER
newgrp docker
sudo apt update
docker ps
minikube start --driver=docker
sudo apt update
sudo apt install -y curl wget apt-transport-https
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
wget https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/amd64/kubectl
sudo wget https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/amd64/kubectl
sudo chmod +x kubectl
sudo mv kubectl /usr/local/bin/
sudo minikube start --driver=docker
minikube start --driver=docker
kubectl cluster-info
minikube status
cd ..
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
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
kubectl get secrets
kubectl get namespaces
kubectl get secrets -n kube-system
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
kubectl proxy
kubectl apply -f Web-UI-dashboard-adminuser.yml
cat Web-UI-dashboard-adminuser.yml
cat Web-UI-newDeploy.yml
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
kubectl get secrets -n kube-system
kubectl get services -n kubernetes-dashboard
cat Web-UI-dashboard-adminuser.yml -n  kubernetes-dashboard
kubectl apply -f Web-UI-dashboard-adminuser.yml -n  kubernetes-dashboard
kubectl get services -n kubernetes-dashboard
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
nano admin-user-token.yaml
kubectl apply -f admin-user-token.yaml
kubectl get secrets -n kubernetes-dashboard
kubectl describe secret admin-user-token -n kubernetes-dashboard
echo '[base64-encoded-token]' | base64 --decode
echo '[base64-encoded-token]' | base64 --decode
echo 'eyJhbGciOiJSUzI1NiIsImtpZCI6IjBnRV9RUGRTejdCTHF6WDlEMUh4NEJQSWJLY0F2T0xGVjBSUWRQSzlLdGcifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIyNmE2MjAxYS1iMjM2LTQ3NTgtYTE1Ni00ZGJkZjIxM3ViY2QiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.RyewRhVAHPC8CIO4pdAAbi0D50hYh_sOA8WbtxW03K_gtqjwcuAoDSg9emaPGA5FlmYqmna1Q0AzCSvNak0_MM0zBA2TmmQtMcF3C_IRHIKgl8umu6WY3XH5z9Nm9BnktJ48gHMibgjFqWdICJ2FqR1_nUrwCggdicjjaGY_4-wrrRope-df4HzXLRjvQ5TRGKctAO29XyKlRcqWjlJbunFgf8VNgWaJDBL2lhaMQJW83I_1hEQt2dZX1IuR-SwtN-mJXEyVYxbudKGYPtsPw6U7dJ9B3p03daN7xqwMC65xvD7Pz_dJR3PPYRPdrjxe7BAu3pJ7w11YRpKekxS2gw' | base64 --decode
kubectl proxy --address='0.0.0.0' --accept-hosts='^*$'
minikube service grafana --url
minikube service grafana --url -n monitoring
history
```

---

```bash
ls
cd Desktop/
cd kubernetes-prom-graf-1/
kubectl get services -n kubernetes-dashboard
kubectl get all -n kubernetes-dashboard
kubectl describe service kubernetes-dashboard -n kubernetes-dashboard
kubectl get pods -n kubernetes-dashboard
kubectl describe pod <pod-name> -n kubernetes-dashboard
kubectl logs <pod-name> -n kubernetes-dashboard
kubectl exec -it <pod-name> -- /bin/bash -n kubernetes-dashboard
kubectl apply -f some-service.yaml
kubectl delete service <service-name>
kubectl create -f some-deployment.yaml
kubectl delete deployment <deployment-name>
kubectl scale deployment <deployment-name> --replicas=3 -n kubernetes-dashboard
kubectl get deployments -n kubernetes-dashboard
kubectl get replicasets -n kubernetes-dashboard
kubectl rollout status deployment/<deployment-name> -n kubernetes-dashboard
kubectl rollout undo deployment/<deployment-name> -n kubernetes-dashboard
kubectl set image deployment/<deployment-name> <container-name>=<new-image> -n kubernetes-dashboard
kubectl autoscale deployment <deployment-name> --min=2 --max=5 --cpu-percent=80 -n kubernetes-dashboard
kubectl get hpa -n kubernetes-dashboard
kubectl describe hpa <hpa-name> -n kubernetes-dashboard
kubectl get events -n kubernetes-dashboard
kubectl get secret <secret-name> -o yaml -n kubernetes-dashboard
kubectl apply -f some-configmap.yaml -n kubernetes-dashboard
kubectl get configmaps -n kubernetes-dashboard
kubectl describe configmap <configmap-name> -n kubernetes-dashboard
kubectl get nodes
kubectl describe node <node-name>
kubectl top node
kubectl top pod -n kubernetes-dashboard
kubectl cordon <node-name>
kubectl drain <node-name> --ignore-daemonsets
kubectl uncordon <node-name>
kubectl taint nodes <node-name> key=value:Effect
kubectl label nodes <node-name> key=value
minikube start
minikube dashboard
minikube stop
minikube delete
minikube ssh
minikube addons list
minikube addons enable metrics-server
minikube addons disable metrics-server
minikube ip
minikube logs
minikube update-context
minikube profile list
exit
```


