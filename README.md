
helm install prometheus oci://registry-1.docker.io/bitnamicharts/kube-prometheus -f .\prometheus-values.yaml -n mon --create-namespace
kubectl apply -f namespace.yaml
kubectl apply -f cm.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f servicemonitor.yaml
kubectl apply -f ingress.yaml
minikube tunnel

![hw8-1](https://github.com/user-attachments/assets/93e0a53c-36b8-4a51-a74c-f482b34300c9)

![hw8-2](https://github.com/user-attachments/assets/4e24ffd0-34dc-425f-9d6c-853f50b497c6)

![hw8-3](https://github.com/user-attachments/assets/e53467be-7d28-4634-8273-0d9406f0f805)

![hw8-4](https://github.com/user-attachments/assets/d4a31215-6f6d-48bf-a8ee-0b015ed74803)

