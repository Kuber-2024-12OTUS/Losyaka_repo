# Создаём ns и добавляем в него deployment, service и ingress

kubectl create ns homework

kubectl apply -f .\deployment.yaml

kubectl apply -f .\service.yaml

kubectl apply -f .\ingress.yaml

kubectl get all -n homework

![hw13-3](https://github.com/user-attachments/assets/279d6821-d4dd-4d5c-be07-952d100d03a5)

# Проверяем, что страница открывается

![hw13-4](https://github.com/user-attachments/assets/fd8c29f4-4dc0-48ec-b7c3-28764c47f8a8)

# Подключаем debug-контейнер к поду, проверяем доступ к процессам и файловой системе

kubectl debug pod/web-deployment-7767556c99-6d4wg -n homework -it --image=ubuntu --target=nginx --share-processes

ps aux

ls –la /proc/1/root/etc/nginx/

![hw13-5](https://github.com/user-attachments/assets/aad48641-7148-45d5-bd96-f17d6f45f6df)

# Устанавливаем tcpdump и запускаем, открываем страничку, чтобы сгенерировать нагрузку

apt update && apt install tcpdump

tcpdump -nn -i any -e port 80

![hw13-6](https://github.com/user-attachments/assets/a4aaa47c-2d8e-42f6-a475-41f37c3be9d6)

# Находим ноду, на которой работает под, подключаем к ней дебаг-контейнер, проверяем доступ к файловой системе и находим логи подов

kubectl get all -n homework -o wide

kubectl debug node/minikube -it --image=busybox

cd /host/

ls -al

cd cd /host/var/log/pods/

ls -al

cd  homework_web-deployment-547bf749b7-m6dt7_30235e34-9ae1-462c-8b8a-28b750dbe478/nginx/

ls -al

![hw13-7](https://github.com/user-attachments/assets/d9635b52-cae3-47ad-9d36-7443a031f610)

![hw13-8](https://github.com/user-attachments/assets/641d931d-57b1-4598-af46-8b49ebae147f)

# Читаем окончание лога

tail /host/var/lib/docker/containers/18cead6f06e66a13a74ba2753fc244cf0b25a8d9f56db0f8d6e8c2b22ef71efb/18cead6f06e66a13a74ba2753fc244cf0b25a8d9f56db0f8d6e8c2b22ef71efb-json.log

![hw13-9](https://github.com/user-attachments/assets/b97e464f-0169-4f43-9985-ca114ee6b593)

# Задание со *
# Подключаем к поду дебаг-контейнер, устанивливаем strace

kubectl debug pod/web-deployment-547bf749b7-m6dt7 -n homework -it --image=ubuntu --target=nginx --share-processes

apt update && apt install strace -y

![hw13-10](https://github.com/user-attachments/assets/6e887522-4117-47a4-bea6-79a487ebc1c2)

# Находи pid корневого процесса nginx и подключаем к нему strace

ps aux

strace -p 1

![hw13-11](https://github.com/user-attachments/assets/975081f4-c54c-473c-9272-b5f76d792847)

