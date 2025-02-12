По мануалу яндекса созданы сеть, кластер, ноды и s3-bucket

![hw9-0](https://github.com/user-attachments/assets/45d9b26b-f801-43b7-b7d3-619ccbf7f190)

Настроен taint и label node-role=infra

![hw9-1](https://github.com/user-attachments/assets/1f844c48-d6ff-48ec-aa7c-b08b4ffab60b)

Добавлен рпозиторий и скачаны default values

helm repo add grafana https://grafana.github.io/helm-charts

helm show values grafana/loki > loki-values.yaml

helm show values grafana/promtail > promtail-values.yaml

helm show values grafana/grafana > grafana-values.yaml


Values приведены к нужному виду, расположены в репозитории

Установлены loki, promtail, grafana
helm install loki grafana/loki -f .\loki-values.yaml -n loki --create-namespace
![hw9-2](https://github.com/user-attachments/assets/b8c35930-9fb8-4868-90d9-76ed16249267)

helm install promtail grafana/promtail -f .\promtail-values.yaml -n loki --create-namespace
![hw9-3](https://github.com/user-attachments/assets/73772f74-d8e9-4235-96f7-8110d320af62)

helm install grafana grafana/grafana -f .\grafana-values.yaml -n loki --create-namespace
![hw9-4](https://github.com/user-attachments/assets/547b2c98-b720-44be-a5ed-2c046a7287d7)

Изменен сервис графаны на NodePort для доступа извне, изменена политика безопасности в YC для доступа к 30000+ портам
![hw9-5 5](https://github.com/user-attachments/assets/651ddfef-3693-4f79-b3a9-0188b1cf2d17)

![hw9-5 6](https://github.com/user-attachments/assets/7391e7b9-8891-4076-92ef-bbdc81859171)

Добавлен источник логов, проверено, что логи в графане появились

![hw9-5](https://github.com/user-attachments/assets/1dc64725-58a2-4c58-9fd5-5cb5c02617b4)

![hw9-6](https://github.com/user-attachments/assets/7d688fb4-78fd-440b-aff1-f1befd944aa8)
