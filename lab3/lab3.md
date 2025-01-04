University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2024
Group: K4112c
Author: Shestakov Albert Victorovich
Lab: Lab3
Date of create: 24.12.2024
Date of finished: ...

**1. Развернуть minikube cluster**
```
$ minikube start
```
**2. Манифест файла для развертывания deployment-react**

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: frontend-config
data:
  REACT_APP_USERNAME: "Shestakov"
  REACT_APP_COMPANY_NAME: "ITMO"

---

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend-replicas
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image: ifilyaninitmo/itdt-contained-frontend:master
        ports:
        - containerPort: 3000
        env:
        - name: REACT_APP_USERNAME
          valueFrom:
            configMapKeyRef:
              name: frontend-config
              key: REACT_APP_USERNAME
        - name: REACT_APP_COMPANY_NAME
          valueFrom:
            configMapKeyRef:
              name: frontend-config
              key: REACT_APP_COMPANY_NAME

---

apiVersion: v1
kind: Service
metadata:
  name: react-service
spec:
  selector:
    app: react
  type: ClusterIP
  ports:
    - port: 3010
      name: react-port
      targetPort: react-port
      protocol: TCP

---

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: frontend-ingress
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
  - hosts:
    - your-fqdn.com
    secretName: tls-secret
  rules:
  - host: your-fqdn.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-service
            port:
              number: 3000
```
**3. Включение Ingress**
```
$ minikube addons enable ingress
```
**4. Создание сертификата с помощью OpenSSL**

![photo_2024-12-24_23-28-39](https://github.com/AlexsanrdLover/2024-introduction_to_distributed_technologies-k4112c-shestakov_a_v/blob/main/lab3/Лаб3_ход_работы/image.jpg)

**5. Вывести ключи**
```
$ cat tls.crt | base64
$ cat tls.key | base64
```
**6. Обновить манифест**
```
$ kubectl apply -f replicaset.yaml
```
**7. Запуск tunnel**
```
$ minikube tunnel
```
![photo_2024-12-24_23-28-39](https://github.com/AlexsanrdLover/2024-introduction_to_distributed_technologies-k4112c-shestakov_a_v/blob/main/lab3/Лаб3_ход_работы/image%202.jpg)
![photo_2024-12-24_23-28-39](https://github.com/AlexsanrdLover/2024-introduction_to_distributed_technologies-k4112c-shestakov_a_v/blob/main/lab3/Лаб3_ход_работы/image%203.jpg)
