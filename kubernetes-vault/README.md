Так как в задании установка с чартов из гитхаба
mkdir temp
git clone https://github.com/hashicorp/consul-k8s.git ./temp/consul/
git clone https://github.com/hashicorp/vault-helm.git ./temp/vault/

![hw11-1](https://github.com/user-attachments/assets/ab54a584-89cb-4ebb-94c1-e83345a11daf)

Редактируем values, устанавливаем чарты
helm install consul -n consul --create-namespace ./temp/consul -f consul-values.yaml
helm install vault -n vault --create-namespace ./temp/vault -f vault-values.yaml
И проверяем, что всё запустилось

![hw11-2](https://github.com/user-attachments/assets/7f09a640-2450-4bf3-871f-e29ea0a6c5fb)

![hw11-3](https://github.com/user-attachments/assets/20db9e6c-d773-422b-86ac-97ead163ac71)

Инициализируем vault
kubectl exec -ti vault-0 -n vault -- vault operator init

![hw11-4](https://github.com/user-attachments/assets/43e71285-f3a2-4602-bb57-36da8aff8fe5)

Распечатываем хранилище
kubectl exec -ti vault-0 -n vault -- vault operator unseal ska/SeJaP/89Wk81bLvxITvZ2bFWpvquENmWdhOyHs+6
kubectl exec -ti vault-0 -n vault -- vault operator unseal bahIuLcu/jRqP1mGtc53/uawkBqH0cDj3BNXYTHIhddD
kubectl exec -ti vault-0 -n vault -- vault operator unseal M7XbcAxGoGavmr9TmPe/5sVLDlEUqhWZwunX8opUPDOV

kubectl exec -ti vault-1 -n vault -- vault operator unseal ska/SeJaP/89Wk81bLvxITvZ2bFWpvquENmWdhOyHs+6
kubectl exec -ti vault-1 -n vault -- vault operator unseal bahIuLcu/jRqP1mGtc53/uawkBqH0cDj3BNXYTHIhddD
kubectl exec -ti vault-1 -n vault -- vault operator unseal M7XbcAxGoGavmr9TmPe/5sVLDlEUqhWZwunX8opUPDOV

kubectl exec -ti vault-2 -n vault -- vault operator unseal ska/SeJaP/89Wk81bLvxITvZ2bFWpvquENmWdhOyHs+6
kubectl exec -ti vault-2 -n vault -- vault operator unseal bahIuLcu/jRqP1mGtc53/uawkBqH0cDj3BNXYTHIhddD
kubectl exec -ti vault-2 -n vault -- vault operator unseal M7XbcAxGoGavmr9TmPe/5sVLDlEUqhWZwunX8opUPDOV

![hw11-5](https://github.com/user-attachments/assets/dfb741b8-e9c3-4ee4-9c52-71912d986773)

![hw11-6](https://github.com/user-attachments/assets/875f2481-b977-4b0d-b3b4-d8b6be2a7ac0)

![hw11-7](https://github.com/user-attachments/assets/1ed9234b-9625-4d55-9825-e70ec8211d80)

Проверяем, что поды теперь ready

![hw11-8](https://github.com/user-attachments/assets/b0952e41-ceed-41f4-b310-3157ce2298c0)
Добавляем SA, берем его токен и CA
kubectl apply -f sa.yaml
kubectl get secret -n vault vault-k8s-auth-secret -o yaml

Пробрасываем UI Vault, заходис, создаём секрет, авторизацию, роль, политику
kubectl port-forward -n vault service/vault-ui 30002:8200

![hw11-9](https://github.com/user-attachments/assets/874f97d7-9035-49df-a794-66d0a9770d50)

![hw11-12](https://github.com/user-attachments/assets/f4af5b86-a237-4d46-8f12-9c065edc716a)

![hw11-13](https://github.com/user-attachments/assets/f77f74c7-b7dc-4ae9-a78e-60c5c3ec4ad3)

Устанавливаем External secrets
helm repo add external-secrets https://charts.external-secrets.io
helm show values external-secrets/external-secrets > extsec-values.yaml
helm install external-secrets external-secrets/external-secrets -n vault -f extsec-values.yaml

Применяем Secret Store, проверяем статус Secret Store
kubectl apply -f ss.yaml
kubectl get ss -n vault

![hw11-14](https://github.com/user-attachments/assets/41820de2-bc53-44eb-bcf9-226614daffcd)

Вытягиваем External Secret и проверяем, что добавились данные в secret
kubectl apply -f es.yaml
kubectl get secret -n vault otus-cred -o yaml

![hw11-15](https://github.com/user-attachments/assets/7dc822f6-601d-41f6-9ca8-6e699ccb25fe)









