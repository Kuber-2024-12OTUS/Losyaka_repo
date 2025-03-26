Запускаем кластер с 2 узлами, создаём бакет, сервис-аккаунт и ключ к нему

![hw12-1](https://github.com/user-attachments/assets/55f2efa7-faa3-43bd-81ed-e11845485d2a)

![hw12-2](https://github.com/user-attachments/assets/cedbf6e1-795d-43d7-8446-ddfc2cd6dc1b)

Проверяем, что узлы запустились, добавляем подготовленные манифесты секрета и StorageClass, устанавливаем драйвер, добавляем pvc. Проверяем, что SC добавился

kubectl get node

kubectl create -f .\yc-sc.yaml

kubectl create -f .\s3-secret.yaml

kubectl create -f .\temp\deploy\kubernetes\provisioner.yaml

kubectl create -f .\temp\deploy\kubernetes\driver.yaml

kubectl create -f .\temp\deploy\kubernetes\csi-s3.yaml

![hw12-3](https://github.com/user-attachments/assets/7f89cf02-203f-4b16-8117-1a149f3718e2)

Запускаем под

kubectl apply -f .\pod.yaml

kubectl get pod

![hw12-4](https://github.com/user-attachments/assets/4d5425cb-e77e-48ec-86e7-c8fd0f8b5501)

Проверяем, что файлы создаются в s3

![hw12-5](https://github.com/user-attachments/assets/f5d9cb56-30a4-46fb-b695-f9cbdad30e90)
