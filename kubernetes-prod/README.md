# Созданы ноды для задания в YC
![hw14-1](https://github.com/user-attachments/assets/2f172da4-bbab-41cc-888c-8513f813b745)

# Обновлены пакеты
```
sudo apt update && sudo apt upgrade -y
sudo reboot now
```
![hw14-2](https://github.com/user-attachments/assets/463edeeb-ad0b-4ffc-a52c-644bd5bd6bfd)

# Внесены необходимые правки в систему, отключен firewall, добавлен keyring и репозиторий версии 1.31
```
sudo su
swapoff -a
cat <<EOF | tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

modprobe overlay
modprobe br_netfilter
lsmod | egrep "br_netfilter|overlay"
cat <<EOF | tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sysctl --system
systemctl stop ufw && systemctl disable ufw

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
![hw14-3](https://github.com/user-attachments/assets/67afd38f-baff-48c1-9ebb-b4dd053f3f39)

# Установлены containerd kubelet kubeadm kubectl, внесены иизменения в конфиг containerd, чтобы работал flannel
```
apt update && apt -y install containerd kubelet kubeadm kubectl
systemctl enable containerd --now

mkdir /etc/containerd/
containerd config default > /etc/containerd/config.toml
nano /etc/containerd/config.toml

#  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
#    SystemdCgroup = true
systemctl restart containerd
```
![hw14-4](https://github.com/user-attachments/assets/89f4d16c-a30f-4aa4-ad70-db40403f7b51)

# Инициализируем кластер (CIDR 10.244.0.0/16, чтобы не менять конфиг flannel)
```
kubeadm init --pod-network-cidr=10.244.0.0/16
exit
```
![hw14-5](https://github.com/user-attachments/assets/1a944dde-1a1d-4660-a723-73e2a250ee35)

# Копируем конфиг для kubectl, устанавливаем flannel, проверяем, что всё завелось
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
kubectl get all -A
```
![hw14-6](https://github.com/user-attachments/assets/66c2cd49-371b-4a07-baea-53feb7f59cb7)

# Добавляем все worker-ноды и проверяем, что они добавились
```
kubeadm join 172.18.0.16:6443 --token 0gsazo.9u1b01sjiknmt2fm --discovery-token-ca-cert-hash sha256:19418eb3d2847aa3e886d37658f650a102150ee1086de80f443a6c0fd888c606
kubectl get nodes -o wide
```
![hw14-7](https://github.com/user-attachments/assets/555e2fb1-d7cc-4bb6-849e-dda339a5363c)

# На мастер ноде меняем репозиторий на версию 1.32, обновляем kubeadm
```
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update && sudo apt install kubeadm
```
![hw14-8](https://github.com/user-attachments/assets/607c32ac-e9e8-4282-932d-ff47293f012e)

# Приверяем корректность для обновления, обновляем кластер, kubelet, проверяем версию ноды
```
sudo kubeadm upgrade plan
sudo kubeadm upgrade apply v1.32.3
sudo apt install kubelet
sudo systemctl daemon-reload
sudo systemctl restart kubelet
kubectl get nodes
```
![hw14-9](https://github.com/user-attachments/assets/871c8ccd-66ec-49b8-87cf-cbef7896f93c)
![hw14-10](https://github.com/user-attachments/assets/0c377dd0-e4ae-4d9d-bc81-1dc83b5c033a)
![hw14-11](https://github.com/user-attachments/assets/5cc5a2d6-7221-481d-99a3-a67b16e2a674)

# Дрейним ноды по очереди и обновляем, после чего проверяем версии всех нод
```
kubectl drain kubeadm-worker1 --ignore-daemonsets
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update && sudo apt install kubeadm kubelet
sudo systemctl daemon-reload && sudo systemctl restart kubelet
kubectl uncordon kubeadm-worker1

kubectl drain kubeadm-worker2 --ignore-daemonsets
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update && sudo apt install kubeadm kubelet
sudo systemctl daemon-reload && sudo systemctl restart kubelet
kubectl uncordon kubeadm-worker2

kubectl drain kubeadm-worker3 --ignore-daemonsets
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update && sudo apt install kubeadm kubelet
sudo systemctl daemon-reload && sudo systemctl restart kubelet
kubectl uncordon kubeadm-worker3

kubectl get nodes
```
![hw14-12](https://github.com/user-attachments/assets/0519855f-a585-489f-abf1-d510831ec796)

# Окончательная проверка
```
kubectl get nodes -o wide
```
![hw14-13](https://github.com/user-attachments/assets/55db4335-16ff-45fe-a014-9172449e8170)

# Задание со *
# Создаём необходимые ВМ в YC
![hw14-14](https://github.com/user-attachments/assets/5b2fa1ff-9707-4381-b383-a79757283dc0)

# Запускаем kubespray (inventory прилагается)
![hw14-14-1](https://github.com/user-attachments/assets/f89f5c7e-140d-4fb7-b391-5ef5b01dfb94)

# Копируем конфиг для kubectl, проверяем ноды
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
kubectl get nodes -o wide
```
![hw14-15](https://github.com/user-attachments/assets/38021856-ce87-49b8-84a8-d11706e09cb2)
