# Deploy Master
## Prepare Linux
- Update linux repository
```shell
apt update -y
apt upgrade -y
```
- Disable Swap
```shell
swapoff -a
```
## Install Dependency

> Dependency need to install: docker, kubeadm, kubelet

- Add GPG key for Docker repository
```shell
apt-get install ca-certificates curl gnupg lsb-release
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
- Add Docker repository
```shell
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
- Add GPG key for Kubernetes repository
```shell
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
```
- Add Kubernetes repository
```shell
cat << EOF | tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
```
- Update repository
```shell
apt update -y
```
- Install dependency components
```shell
apt install -y docker.io kubelet kubeadm kubectl
```
- Hold Kubernetes components version
```shell
apt-mark hold docker kubelet kubeadm
```
- Enable `kubelet` service
```shell
systemctl enable kubelet
```
## Bootstraping Master
- Bootstraping master using `kubeadm`
```shell
kubeadm init --pod-network-cidr=192.168.0.0/16
```
- Configure token
```shell
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
- Apply overlay networking
```shell
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/tigera-operator.yaml
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/custom-resources.yaml
```
## Optional command
- Get node information
```shell
kubectl get nodes
```
- Get node health
```shell
kubectl get componentstatuses
```
- Get system pods status
```shell
kubectl get pods --all-namespaces
```
- Show join command
```shell
kubeadm token create --print-join-command
```

--------------

# Deploy Node

## Prepare Linux

- Update linux repository
```shell
apt update -y
apt upgrade -y
```
- Disable Swap
```shell
swapoff -a
```

## Install Dependency

> Dependency need to install: docker, kubeadm, kubelet

- Add GPG key for Docker repository
```shell
apt-get install ca-certificates curl gnupg lsb-release
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
- Add Docker repository
```shell
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
- Add GPG key for Kubernetes repository
```shell
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
```
- Add Kubernetes repository
```shell
cat << EOF | tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
```
- Update repository
```shell
apt update -y
```
- Install dependency components
```shell
apt install -y docker.io kubelet kubeadm kubectl
```
- Hold Kubernetes components version
```shell
apt-mark hold docker kubelet kubeadm
```
- Enable `kubelet` service
```shell
systemctl enable kubelet
```
## Join Node to Master
> Run the join command. This command can be find by `kubeadm token create --print-join-command` command run from master.

