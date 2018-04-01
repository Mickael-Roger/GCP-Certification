# Deploying a cluster
## K8s Master
* On master : Create a pod Network on master
```
kubeadm init --pod-network-cidr=10.10.0.0/16
```
* On a Node : Join the cluster
```
kubeadm join IP_MASTER:PORT --token TOKEN_ID --discovery-token-ca-cert-hash sha256:CERT_VAL
```
* On the Master : Tu use the cluster
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
* On the master : To install pod networking
```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```





# Commands
## Pods
kubectl get pods  --all-namespaces

## Nodes
kubtctl get nodes
