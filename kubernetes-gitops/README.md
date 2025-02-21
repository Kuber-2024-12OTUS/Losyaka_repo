# Добавляем репозиторий, забираем и редактируем под наши нужды values

# Для nodeSelector делаем метку node-role=infra, taint добавлен в YC при создании

helm repo add argo https://argoproj.github.io/argo-helm

helm show values argo/argo-cd > argocd-values.yaml

kubectl label node cl1ml5hat1m8vgvsf64j-utyn node-role=infra

kubectl get nodes --show-labels

kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints


![hw10-1](https://github.com/user-attachments/assets/c68c1e7c-db26-4607-851c-71fe8189b2ce)

# Устанавливаем argocd и проверяем, чтобы он установился на ноду infra

helm install argocd argo/argo-cd -n argo --create-namespace -f argocd-values.yaml

kubectl get all -n argo -o wide


![hw10-2](https://github.com/user-attachments/assets/8b82bf32-a5ba-42f6-a78f-cc809cef22e4)

# На worker-ноде делаем метку homework=true, чтобы манифесты из kubernetes-networks могли на неё установиться

# Проверяем, что нет неймспейсов homework и homeworkhelm, применяем project и apps

# Проверяем, что всё запустилось корректно в нужных неймспейсах

kubectl label node cl1a293i8os28juv6kl3-omej homework=true

kubectl get ns

kubectl apply -f .\argocd-project.yaml

kubectl apply -f .\argocd-app-manifest.yaml

kubectl get all -n homework

kubectl apply -f .\argocd-app-helm.yaml

get all -n homeworkhelm

![hw10-3](https://github.com/user-attachments/assets/15fedcca-bf44-4c65-8d3e-91b14fcb87ca)
