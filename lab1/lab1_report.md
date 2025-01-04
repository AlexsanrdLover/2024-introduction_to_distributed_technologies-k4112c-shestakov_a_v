University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2024
Group: K4112c
Author: Stestakov Albert Victorovich
Lab: Lab1
Date of create: 12.12.2024
Date of finished: ...

**1. Развернуть minikube cluster**
```
$ minikube start
```
![photo_2024-12-12_21-37-07](https://github.com/AlexsanrdLover/2024-introduction_to_distributed_technologies-k4112c-shestakov_a_v/blob/main/lab1/Лаб1_ход_работы/image.jpg)
![photo_2024-12-12_21-37-07](https://github.com/AlexsanrdLover/2024-introduction_to_distributed_technologies-k4112c-shestakov_a_v/blob/main/lab1/Лаб1_ход_работы/image%202.jpg)
**2. Манифест файла для развертывания vault**

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vault
spec:
  replicas: 1
  selector:
    matchLabels:
      app: vault
  template:
    metadata:
      labels:
        app: vault
    spec:
      containers:
      - name: vault
        image: hashicorp/vault:latest
        ports:
        - containerPort: 8200
        env:
        - name: VAULT_DEV_LISTEN_ADDRESS
          value: "0.0.0.0:8200"
```

**3. Создание или обновление объекта в Pod**
```
$ kubectl apply -f vault.yaml
```
**4. Создаёт объект Service, который открывает доступ к Pod vault через порт 8200**
```
$ minikube kubectl -- expose pod vault --type=NodePort --port=8200
```
**5. Перенаправление локального порта 8200 на порт 8200 сервиса vault, чтобы получить доступ к сервису**
```
$ minikube kubectl -- port-forward service/vault 8200:8200
```
**6. Написать в браузере url**
```
http://localhost:8200/ui/vault/dashboard
```
![photo_2024-12-12_21-37-07](https://github.com/AlexsanrdLover/2024-introduction_to_distributed_technologies-k4112c-shestakov_a_v/blob/main/lab1/Лаб1_ход_работы/image%203.jpg)
**7. Создать новый терминал и найти Root Token**
```
$ minikube kubectl logs vault
```
![photo_2024-12-12_21-37-07](https://github.com/AlexsanrdLover/2024-introduction_to_distributed_technologies-k4112c-shestakov_a_v/blob/main/lab1/Лаб1_ход_работы/image%204.jpg)
