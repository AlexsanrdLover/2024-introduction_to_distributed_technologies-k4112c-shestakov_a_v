University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2024
Group: K4112c
Author: Shestakov Albert Victorovich
Lab: Lab2
Date of create: 24.12.2024
Date of finished: ...

**1. Развернуть minikube cluster**
```
$ minikube start
```
**2. Манифест файла для развертывания frontend-deployment.yaml**

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
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
          value: "Shestakov"
        - name: REACT_APP_COMPANY_NAME
          value: "ITMO"
```

**replicas:** 
Количество экземпляров приложения, которые должны быть запущены одновременно

**selector:** 
Определяет, какие поды будут управляться этим деплойментом

**3. Манифест файла для frontend-service.yaml**
```
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  type: NodePort
  selector:
    app: frontend
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 30001
```
**4. Привязывание манифеста**
```
$ kubectl apply -f frontend-service.yaml
```
**5. Перенаправление запрос с Service на pods**
```
$ minikube kubectl -- port-forward service/frontend-service 3000:3000
```

**6. Написать в браузере url**
```
http://localhost:3000
```
**7. Создать новый терминал и найти логи**
```
NAME                                READY   STATUS    RESTARTS      AGE
deployment-react-5858d5b9db-jntdt   1/1     Running   0             44m
deployment-react-5858d5b9db-l6z25   1/1     Running   0             44m
vault                               1/1     Running   1 (26m ago)   88m

kubectl -- logs deployment-react-5858d5b9db-l6z25
Builing frontend
Browserslist: caniuse-lite is outdated. Please run:
  npx update-browserslist-db@latest
  Why you should do it regularly: https://github.com/browserslist/update-db#readme
Browserslist: caniuse-lite is outdated. Please run:
  npx update-browserslist-db@latest
  Why you should do it regularly: https://github.com/browserslist/update-db#readme
build finished
Server started on port 30001

kubectl -- logs deployment-react-5858d5b9db-jntdt
Builing frontend
build finished
Browserslist: caniuse-lite is outdated. Please run:
  npx update-browserslist-db@latest
  Why you should do it regularly: https://github.com/browserslist/update-db#readme
Browserslist: caniuse-lite is outdated. Please run:
  npx update-browserslist-db@latest
  Why you should do it regularly: https://github.com/browserslist/update-db#readme
Server started on port 30001
```
![image_2024-12-24_22-44-05](https://github.com/AlexsanrdLover/2024-introduction_to_distributed_technologies-k4112c-shestakov_a_v/blob/main/lab2/Лаб2_ход_работы/image.jpg)
![image_2024-12-24_22-47-56](https://github.com/AlexsanrdLover/2024-introduction_to_distributed_technologies-k4112c-shestakov_a_v/blob/main/lab2/Лаб2_ход_работы/image%202.jpg)
